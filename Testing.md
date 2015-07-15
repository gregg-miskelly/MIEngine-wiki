# MIEngine Testing

MIEngine includes a suite of automated tests that can be run against an Android emulator or device. The tests can be found in [test\Android](https://github.com/Microsoft/MIEngine/tree/glass/test/Android).

## Running Glass Tests

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