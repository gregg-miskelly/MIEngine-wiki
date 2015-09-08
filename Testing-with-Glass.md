## What is Glass?

The MIEngine has a number of Glass-based tests. Glass is a console tool for testing Visual Studio debugger backend extensions (ex: debug engines, expression evaluators, and Concord components) outside of Visual Studio. It calls the debugger API's in a way that is very similar to what the UI would do, but in a way that is meant to be fast and easy to test.

## How do Glass tests work?

The tests run glass2.exe against an XML input script. This input script (TestScript.xml) tells Glass what commands to run, and what should happen as a result of running the commands. Glass then produces two different output files - an error log (ErrorLog.xml) which contains differences between the expected values and the observed values, and a session log (SessionLog.xml) which is a log of everything that happened. One useful thing is that session logs have the same format as an input script, so you can also take a session log, tweak it a little bit, and now you have a test script.

Let's look at [MIEngine\test\Android\Sanity\TestScript.xml](https://github.com/Microsoft/MIEngine/blob/master/test/Android/Sanity/TestScript.xml) as an example of how test scripts work. You can see that the script executes a number of commands. In response to the 'launch' command, you can see that many events are generated. The last of these events is IDebugBreakpointEvent2. This is a stopping event, so when Glass receives this event, it will determine where it stopped and output the functionName/function/file/line/col attributes indicating where it stopped.

Now that you have a basic feel for the test script, let's cover the less obvious concepts --

### 'expected' attribute on events
In Glass, every event gets an 'expected' attribute which is set to 'True' or 'False'.

In an input script, 'expected="True"' tells Glass that it shouldn't move on to the next command until this expected event arrives. Conversely non-expected events are ignored by Glass if they are in an input script. They are only there because input scripts are just a session log.

In a session log, 'expected="True"' means that an event arrived that is in the set of events that Glass has been told to treat as an expected event. By default, an event is marked as expected if it is a stopping event (ex: breakpoint hit, set complete, etc), session destroy (which means debugging stopped) or an error event (minor note: IDebugTimeContextChangeEvent100 is also expected, but the MIEngine will never send this). It's also possible to make other events expected using the setAsExpectedEvent/ignore Glass commands. If the session log was created because Glass is running a test (as opposed to running Glass interactively to develop a new test), and an expected event arrives that wasn't expected by the test, Glass will fail the test.

### Volatile/RegEx/IgnoreCase/Optional attributes

In addition to validating that the right set of expected events arrive, Glass determines if a test passes or fails by comparing the child XML elements of 'Command' or 'Event' elements (ex: the functionName/function/file/line/col elements from the sanity test). However, sometimes we want Glass to do some sort of fuzzy match rather than requiring the text to exactly match. Glass supports the following attributes for doing a fuzzy match:

* Volatile: This is for content that will change every run of the test, as long as this element shows up, consider it a match.
* RegEx: Test the specified regular expression against the text. Note that this is considered a match as long as it matches anywhere in the text. So if you want to require the full text to match be sure to use '^' (start of the string) and '$' (end of the string). Ex, you could match to an unsigned base-10 integer with: ^[0-9]+$.
* IgnoreCase: Use a case-insensitive comparison
* Optional: It is okay if the element is missing

## Debugging Android Glass tests

To debug Glass, you can set the 'GLASS_DEBUG=1' environment variable. Then you can attach a debugger to the glass2.exe process. You can also pass the '/v' (Verbose) option to [androidtest.cmd](https://github.com/Microsoft/MIEngine/blob/master/test/Android/androidtest.cmd) which will list the command line arguments being passed to Glass. You can then copy/paste these into the debug settings in a project. You can also File->Open Project in Visual Studio and point to glass2.exe to create an exe project for Glass, and then set the working directory/command line arguments.

When debugging Glass it can be useful to make a few tweaks to the command line:
* '-diag' will output extra diagnostics. You can open up the output window in your debugger to see what commands were executed. If you double click on these lines, Visual Studio will navigate to that line in the TestScript/target app source code.
* Use '-s CON' instead of '-s SessionLog.xml' to write the session log output to the console.

## Developing new Glass tests

There are two useful techniques for developing new Glass-based tests:

\#1: Use a SessionLog.xml as a TestScript.xml -- Here you create a very minimal TestScript.xml (one that will not pass), run that to generate a SessionLog, and use the session log as your test script.

Steps:
* Create a minimal TestScript.xml -- list the commands you want to run, and fill in the name of the expected events but don't bother getting any of the details correct. You can usually start with an existing TestScript.xml and then modify what you want to change.
* Run the test. This will fail but generate a SessionLog.xml. 
* Look at the SessionLog.xml. Make sure that the output is what you expect, and remove anything from the session log that you don't want to test. The most common thing that you need to tweak is to remove 'OutputString' elements.
* Copy SessionLog.xml to TestScript.xml.

-or-

\#2: Use Glass in interactive mode -- Glass can function as a basic command line debugger, and you can use that to develop tests.

Steps:
* Start glass2.exe. You can start up glass2.exe without a test script at all, or with a minimal test script that uses the 'interact' command when you want to switch to interactive mode. 
* Issue commands to Glass like it is a regular debugger (ex: type 'step' to issue the 'step' command).
* Use the 'help' command to list commands and get their syntax. The information here isn't perfect, but its a start. You can search through other TestScript.xml's to see how other tests are using the command.
* At the end, edit and save the session log just like technique #1.

## Events that require special care

When you are developing a glass test, there are a few events that you want to be a little careful about --

### IDebugBreakpointBoundEvent2
By default IDebugBreakpointBoundEvent2 is not considered an expected event and, in some tests, this can cause problems. The problem happens because glass will output a 'BoundBreakpoint' XML element when the event comes in. Example:

    <Event name="IDebugBreakpointBoundEvent2" expected="False">  
        <BoundBreakpoint>24,0 to 24,0 in [Unknown]!break_test_1()</BoundBreakpoint>  
    </Event>

If the IDebugBreakpointBoundEvent2 is not declared as expected and associated with the command that causes the bound breakpoint event than this can cause problems because glass is being told to expect information, but isn't being told to block for that information, so it is non deterministic as to when this information shows up. See [commit 3a4b643](https://github.com/Microsoft/MIEngine/commit/3a4b64354eec43a3c55d62b83ab78e7f84834f18) for an example of this problem.

If the breakpoint is set before the launch command, generally this is not a problem as somewhere in the launch command the breakpoint will bind, and it will always bind as part of the launch command, so glass will always get the right output. 

If the breakpoint is set in the middle of the test, one of the following should be done:

1. Remove the 'BoundBreakpoint' element from the test script. Now glass will not complain no matter when the breakpoint bind shows up.

    -or-
2. Declare the IDebugBreakpointBoundEvent2 as expected, and make sure it in associated with the command that causes the breakpoint to bind.


### IDebugOutputStringEvent2

IDebugOutputStringEvent2 is a similar problem to IDebugBreakpointBoundEvent2 in that when an output string event shows up, glass will output an XML node with data from the event (OutputString is this case). The other problem without output string events is that the core debugger can combine multiple output string events together depending on timing. So if the target is sending multiple output string events, it may be non deterministic how many events make it to glass.

Usually, the best solution for OutputString events is just to remove the OutputString from the test script.
