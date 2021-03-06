Visual Studio ships with support for debugging on Android or iOS, which is powered by this project. But even though MIEngine doesn't have official support for other platforms yet, MIEngine is a very good start towards hooking Visual Studio to GDB/LLDB for debugging on other platforms as well. On this page we will walk though how to do this.

### Launch options XML

The MIEngine accepts a set of options when we start debugging to tell it what to debug. Here is an example:

    <PipeLaunchOptions xmlns="http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014" 
        PipePath="plink.exe" 
        PipeArguments="-i private.ppk user@server -batch -t gdb --interpreter=mi" 
        ExePath="/home/user/YourDir/exe" ExeArguments="" 
        TargetArchitecture="X64" 
        WorkingDirectory="/home/user/YourDir" 
        AdditionalSOLibSearchPath=""> 
    </PipeLaunchOptions>

The MIEngine supports several different types of XML elements, here is a list:
* PipeLaunchOptions - use some program to connect to a target device, and run gdb/lldb on that target device, passing back stdin/out. An example of such a program is [plink.exe](http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe) from the [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) project which can be used to connect to the target device over SSH. For another example, the Docker client executable can also be used as a pipe to run commands inside the target container using 'docker exec'.
* LocalLaunchOptions - runs gdb/lldb locally (on the same Windows computer where Visual Studio is running) and either connect to some gdbserver end point, or debug a local windows process.
* TcpLaunchOptions - connect to a TCP/IP port that GDB input/output has been piped through.
* SerialPortLaunchOptions - connect to a serial port that GDB input/output has been piped through.

More details on all the options can be found in [MICore\LaunchOptions.xsd](https://github.com/Microsoft/MIEngine/blob/master/src/MICore/LaunchOptions.xsd). If you open this file in Visual Studio before you start to author your own launch options file, the XML language service will validate your XML against the schema, provide auto-complete and give you access to the documentation.

In the master branch, the launch options XML also supports replacing or adding custom launch commands. This is with the SetupCommands / CustomLaunchSetupCommands / LaunchCompleteCommand elements which are nested inside the top level option xml element. Note that this support didn't ship with VS 2015 RTM, but will be included in future releases of Visual Studio.

### Start debugging

To start debugging, we need to call the Visual Studio start debugging API (IVsDebugger[2/3/4].LaunchDebugTargets[2/3/4]) passing in those XML launch options in the 'bstrOptions' field, and setting the 'guidLaunchDebugEngine' to the engine Guid of the MIEngine. 

Visual Studio ships with some basic support for calling this API without needing to write code so that you can easily experiment with launch options --
* In Visual Studio, go to View->Other Windows->Command Window to open up the Visual Studio command window
* Run the following command. Note that friendly name can be anything you want as long as the real path to the target is in the options file (or the default launch command are overridden so that the real path to the executable doesn't matter anyway).
    Debug.MIDebugLaunch /Executable:\<friendly_name\> /OptionsFile:\<path-to-file\>

Once you have everything working, its possible to make a Visual Studio extension to start debugging without needing to write the launch options to disk or use the command window. To see the source code for how Debug.MIDebugLaunch works so you can do this in your own extension open [MIDebugPackagePackage.cs](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugPackage/MIDebugPackagePackage.cs) and look at the LaunchDebugTarget function. To create projects which will start debugging using your extension, you want to create a [Visual C++ 2015 Debugger Launch Extension](https://visualstudiogallery.msdn.microsoft.com/7fe7f19f-ceb9-47e3-b440-c62df2b85281). If you want something simpler, you could certainly also just add a Visual Studio menu item.

### Troubleshooting
If you are still using VS 2015 RC, I would recommend upgrading to something more recent (ex: pull down sources from master), as we have made improvements to our diagnostics since RC.

Beyond that, I would recommend starting by looking at logs. See [Logging](logging).

### Known issues

We have a number of known issues with supporting other platforms that might be relevant to what you are trying to do. These are issues that we intend to fix, though if you are interested in working on these contributions are welcome.

[Issues list](https://github.com/Microsoft/MIEngine/labels/general-linux-support)

### Changing the MIEngine

If you find you need to make changes to the MIEngine to support your platform, we are happy to accept high quality changes (see [instructions](https://github.com/Microsoft/MIEngine/wiki/Contributing-Code)).

If you then want to distribute your changes, you have a few options:

1. If you just are distributing changes to a couple of machines in your office, you can use Install.cmd (see [instructions](https://github.com/Microsoft/MIEngine/wiki/Patching-Visual-Studio)).
2. Visual Studio ships frequent updates, so you can wait for Microsoft to distribute your changes in the next update. We generally recommend this approach.
3. You can [redistribute](https://github.com/Microsoft/MIEngine/wiki/Redistribution) your own copy.