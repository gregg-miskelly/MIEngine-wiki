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

### Additional notes:
* [SetMIDebugLogging.cmd](https://github.com/Microsoft/MIEngine/blob/master/src/MICore/SetMIDebugLogging.cmd) has no dependencies, so you can copy it to another computer if you don't want to enlist on the target box.
* If you are looking at Android scenarios, there is a '/serverlogging' argument to SetMIDebugLogging.cmd which can be used to turn on gdbserver logging
