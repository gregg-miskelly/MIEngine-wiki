Visual Studio ships with support for debugging on Android or iOS, which is powered by this project. But even though we don't have official support for other platforms yet, this project is a very good start towards hooking Visual Studio to GDB/LLDB for debugging on other platforms as well. On this page we will walk though how to do this.

### Launch options XML

The MIEngine accepts a set of options at startup to tell it what to debug. Here is an example:

    <PipeLaunchOptions xmlns="http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014" 
        PipePath="plink.exe" PipeArguments="-i private.ppk user@server -batch -t gdb --interpreter=mi" 
        ExePath="/home/user/YourDir/exe" ExeArguments="" 
        TargetArchitecture="X64" WorkingDirectory="/home/user/YourDir" AdditionalSOLibSearchPath=""> 
    </PipeLaunchOptions>

The MIEngine supports several different types of XML elements, here is a list:
* PipeLaunchOptions - use some program to connect to a target device, and run gdb/lldb on that target device, passing back stdin/out. An example of such a program is [plink.exe](http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe) from the PuTTY project which can be used to connect to the target device over SSH. The Docker client executable can also be used as a pipe to run commands inside the target container.
* LocalLaunchOptions - runs gdb/lldb locally (on the same Windows computer where Visual Studio is running) and either connect to some remote gdbserver end point, or debug a local windows process.
* TcpLaunchOptions - connect to a TCP/IP port that GDB input/output has been piped through.
* SerialPortLaunchOptions - connect to a serial port that GDB input/output has been piped through.

More details on all the options can be found in MICore\LaunchOptions.xsd. If you open this file in Visual Studio before you start to author your own launch options file, the XML language service will validate your XML against the schema, provide auto-complete and give you access to the documentation.

In the master branch, the launch options XML also support replacing or adding custom launch commands. This is with the SetupCommands/CustomLaunchSetupCommands/LaunchCompleteCommand elements which are nested inside the top level options xml. Note that this support didn't ship with VS 2015 RTM, but will be included in future releases of Visual Studio.

### Start debugging

Now that you have your launch options all setup, it is time to try them out.
* In Visual Studio, go to View->Other Windows->Command Window to open up the Visual Studio command window
* Run the following command. Note that friendly name can be anything you want as long as the real path to the target is in the options file (or the default launch command are overridden so that the real path to the executable doesn't matter anyway).
    Debug.MIDebugLaunch /Executable:<friendly_name> /OptionsFile:<path-to-file>

Note that once you have everything working, its possible to make a Visual Studio extension to start debugging without needing to write the launch options to disk or use the command window. Debug.MIDebugLaunch just invokes the normal Visual Studio start debugging API (LaunchDebugTargets4).

To see the source code for how Debug.MIDebugLaunch works, expand the MIDebugPackage project, and open MIDebugPackagePackage.cs. Look at the LaunchDebugTarget function. To create projects which will start debugging using your extension, you want to create a [Visual C++ 2015 Debugger Launch Extension](https://visualstudiogallery.msdn.microsoft.com/7fe7f19f-ceb9-47e3-b440-c62df2b85281). If you want something simpler, you could certainly also just add a Visual Studio menu item.

### Trouble shooting

TODO

### Known issues

TODO
