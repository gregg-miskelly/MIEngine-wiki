The MIEngine has logging in it to make it easy to see what commands we are sending to GDB/LLDB, what output GDB/LLDB is returning, and how long each command took.

### Using debug builds
If you have the MIEngine solution open and you are hitting F5 using the debug configuration, the logging is automatically on. Just open the output window (Debug->Windows->Output) and you can see the interactions that MIEngine is having with the target debugger.

### Using shipping bits
Logging can also be enabled in a Visual Studio instance without needing to somehow patch the version of the MIEngine used by Visual Studio. To do so:
* Clone the sources: `git clone https://github.com/Microsoft/MIEngine.git`
* Enable logging: `src\MICore\SetMIDebugLogging.cmd on`
* Restart Visual Studio
* Try the scenario
* To turn logging back off, run `src\MICore\SetMIDebugLogging.cmd off`
* Open %TMP%\Microsoft.MIDebug.log

### Visual Studio 2017 RC.2 and after
Starting with RC2 MIEngine logging is much simpler. There is a new command in the command window:

	Debug.MIDebugLog  (/On[:<filename>] | /Off) [/OutputWindow]

Options:
* /On[:<filename>]	– Turn on MIEngine logging. Optionally specify a file to contain the log. Either the file must be supplied, or the “/OutputWindow” option must appear.
* /Off 		    	-- Turn off MIEngine logging. If logging to a file the file is closed.
* /OutputWindow	-- Log to the “Debug” pane in the output Window.

The command may be issued at any point. Logging can be turned on and off as often as desired. If you have enabled logging via the previous mechanism (SetMIDebugLogging.cmd) this command will override those settings. Unlike the previous mechanism, the MIDebugLog state is not remembered between VS invocations.

### Additional notes:
* [SetMIDebugLogging.cmd](https://github.com/Microsoft/MIEngine/blob/master/src/MICore/SetMIDebugLogging.cmd) has no dependencies, so you can copy it to another computer if you don't want to enlist on the target box.
* If you are looking at Android scenarios, there is a '/serverlogging' argument to SetMIDebugLogging.cmd which can be used to turn on gdbserver logging
