# MIEngine Testing

MIEngine includes a suite of automated tests that can be run against an Android emulator or device. The tests can be found in [test\Android](https://github.com/Microsoft/MIEngine/tree/glass/test/Android).


These tests are called 'Glass Tests' because they are automated against a scriptable, command line debugger frontend called Glass (glass2.exe) that loads and uses the MIEngine binaries the same way that Visual Studio does. More information about Glass can be found [here](https://github.com/Microsoft/MIEngine/wiki/Testing-with-Glass).

## Running Android Glass Tests

To run all tests against an android emulator:

- Make sure the project has been built, either using build.cmd or building from within Visual Studio.
- Start an Android emulator using the Visual Studio Emulator for Android application that comes with Visual Studio.
- From an administrator Visual Studio Developer Command Prompt, cd to the test\Android\ directory. 
- Starting the tests requires the device ID of the emulator. Running androidtest.cmd with no arguments will use adb.exe to list connected android devices:
```
c:\MIEngine\test\Android>androidtest.cmd
--- MIEngine Android Test Script ---
Usage: androidtest.cmd /DeviceId <id> /Platform <platform> [/SdkRoot <path>] [/NdkRoot <path>] [/v] [<test 1> [<test 2> [...]]]

List of devices attached
169.254.138.177:5555   device product:VS Emulator 7" KitKat (4.4) XHDPI Tablet model:7__KitKat__4_4__XHDPI_Tablet device:donatello
```
- Run the androidtest.cmd script using the appropriate device ID and platform:
```
c:\MIEngine\test\Android>androidtest.cmd /DeviceId 169.254.138.177:5555 /Platform x86
```
- To run a single test, use the /Tests flag. This flag must be the last flag specified on the command line.
```
c:\MIEngine\test\Android>androidtest.cmd /DeviceId 169.254.138.177:5555 /Platform x86 /Tests Sanity
```


## Authoring Glass Tests
A new glass tests requires at least three items:
- A test 'debuggee', which is an Android application that MIEngine will debug in order to exercise it's code.
- A LaunchOptions.xml.template file, which contains information needed to launch the debguggee, such as it's package name.
- A TestScript.xml, which contains the commands for Glass and the expected output. 

Here is an example of authoring a new test for setting a breakpoint, running to it, and evaluating a local variable. 

- The existing tests are all based on the Native Activity Project template for Android provided in Visual Studio 2015. The name of the project will define the name of the test. It should be saved to the test\Android directory.
For this test we will name the project Eval.
![New Test Project](images/new-test-project.PNG)

- We will strip out all of the graphics code from the template project and insert our test code into the android_main function.
```C++
void android_main(struct android_app* state) {
	int i = 0xDEADBEEF;

	i += 0; // bp here, line 29
}
```

- Next, copy the LaunchOptions.xml.template file from the Sanity test into the Eval test folder and make changes for the Eval project. Items such as _$SdkRoot$_ are filled in by the androidtest.cmd script. 
```XML
  <AndroidLaunchOptions xmlns="http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014"
    Package="com.Eval"
    LaunchActivity="android.app.NativeActivity"
    SDKRoot="$SdkRoot$"
    NDKRoot="$NdkRoot$"
    TargetArchitecture="$TargetArchitecture$"
    IntermediateDirectory="$IntermediateDirectory$"
    AdditionalSOLibSearchPath="$AdditionalSOLibSearchPath$"
    DeviceId="$DeviceId$"/>
```

- Author a TestScript.xml. This will contain the debug commands to glass as well as the expected output. The TestScript.xml for our simple eval test will look like this:
```XML
<?xml version="1.0" encoding="utf-8"?>
<GlassEventLog>
  <Command name="setmode ShowParameterValues off" />
  <Command name="SetLaunchOptionsString @LaunchOptions.xml" />
  <Command name="bp main.cpp 29" />
  <Command name="launch MIEngine app_process">
    <Event name="IDebugSessionCreateEvent2" expected="False" />
    <Event name="IDebugProcessCreateEvent2" expected="False" />
    <Event name="IDebugProgramCreateEvent2" expected="False" />
    <Event name="IDebugBreakpointErrorEvent2" expected="False">
      <WarningMessage>module containing this breakpoint has not yet loaded or the breakpoint address could not be obtained.</WarningMessage>
    </Event>
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugOutputStringEvent2" expected="False" />
    <Event name="IDebugBreakpointBoundEvent2" expected="False">
      <BoundBreakpoint>28,0 to 28,0 in [Unknown]!android_main(android_app*)</BoundBreakpoint>
    </Event>
    <Event name="IDebugModuleLoadEvent2" expected="False" />
    <Event name="IDebugOutputStringEvent2" expected="False" />
    <Event name="IDebugOutputStringEvent2" expected="False" />
    <Event name="IDebugOutputStringEvent2" expected="False" />
    <Event name="IDebugThreadCreateEvent2" expected="False" />
    <Event name="IDebugBreakpointEvent2" expected="True">
      <functionName>android_main</functionName>
      <function>android_main(android_app * state)</function>
      <file>main.cpp</file>
      <line>29</line>
      <col>0</col>
    </Event>
  </Command>
  <Command name="eval i">
    <EvalResult>-559038737</EvalResult>
    <EvalType>int</EvalType>
    <EvalName>i</EvalName>
    <EvalFullName>i</EvalFullName>
    <EvalIsExpandable>False</EvalIsExpandable>
  </Command>
  <Command name="eval i,x">
    <EvalResult>0xdeadbeef</EvalResult>
    <EvalType>int</EvalType>
    <EvalName>i,x</EvalName>
    <EvalFullName>i</EvalFullName>
    <EvalIsExpandable>False</EvalIsExpandable>
  </Command>
  <Command name="eval i,o">
    <EvalResult>033653337357</EvalResult>
    <EvalType>int</EvalType>
    <EvalName>i,o</EvalName>
    <EvalFullName>i</EvalFullName>
    <EvalIsExpandable>False</EvalIsExpandable>
  </Command>
  <Command name="go">
    <Event name="IDebugProcessContinueEvent100" expected="False" />
  </Command>
  <Command name="stopdebugging">
    <Event name="IDebugOutputStringEvent2" expected="False" />
    <Event name="IDebugProgramDestroyEvent2" expected="False" />
    <Event name="IDebugProcessDestroyEvent2" expected="False" />
    <Event name="IDebugSessionDestroyEvent2" expected="True" />
  </Command>
  <Command name="quit" />
</GlassEventLog>
```
Tips for authoring a TestScript.xml can be found [here](https://github.com/Microsoft/MIEngine/wiki/Testing-with-Glass#developing-new-glass-tests)

- Run our new test using the androidtest.cmd script
```
c:\MIEngine\test\Android>androidtest.cmd /DeviceId 169.254.138.177:5555 /Platform x86 /Tests Eval
```