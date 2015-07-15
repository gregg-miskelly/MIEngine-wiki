# MIEngine Testing

MIEngine includes a suite of automated tests that can be run against an Android emulator or device. The tests can be found in [test\Android](https://github.com/Microsoft/MIEngine/tree/glass/test/Android).


These tests are called 'Glass Tests' because they are automated against a scriptable, command line debugger frontend called Glass (glass2.exe) that loads and uses the MIEngine binaries the same way that Visual Studio does. More information about Glass can be found [here](TODO: link to glass wiki page).

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
![New Test Project](images/new-test-project.png)