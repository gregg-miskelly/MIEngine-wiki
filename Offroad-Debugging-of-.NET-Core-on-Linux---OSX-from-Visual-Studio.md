Microsoft and the .NET community have created a new version of .NET, .NET Core, which is designed to be cross-platform, modular, and optimized for the cloud. Visual Studio has obviously had support for developing .NET Core apps on Windows throughout the project, but debugging support on non-Windows platforms is new for the RC2 release.

If you want to develop on the target device itself, you can use Visual Studio Code by following [these instructions](http://aka.ms/vscclrdebugger). But it is also possible to stay in Visual Studio and still debug to Linux or OSX. For day-to-day development, especially if you intend to deploy to a Linux Docker container, you should use use the [Visual Studio Tools for Docker](https://visualstudiogallery.msdn.microsoft.com/0f5b2caa-ea00-41c8-b8a2-058c7da0b3e4) as soon as they ship their update with debugging support.

In the future, more scenarios are likely to get a more 'on-road' experence from Visual Studio. But for now, for folks who want to play around with non-Docker scenarios, need to be able to attach to processes, or just want to get under the hood and see how things work, this wiki page should walk you through how to get things to work in an offroad manner. Pay attention as this will be a bit bumpy.

### Your feedback
File bugs and feature requests [here](https://github.com/Microsoft/MIEngine/issues). Many issues are likely to be in common with Visual Studio Code, so please also look in the [VS Code C# extension github](https://github.com/OmniSharp/omnisharp-vscode/labels/Debugger) to see if the issue is already filed.

### Machine setup

#### Visual Studio Computer
In Visual Studio you need the VS 2015 Update 2 version of the MIDebugEngine. The easiest way currently to acquire these bits is to -

1. If you haven't already done so, install [Visual Studio 2015 Update 2](http://go.microsoft.com/fwlink/?LinkId=691129).
2. Enable C++ iOS development, which will install the MIEngine:
   * Open Windows Control Panel.
   * Open the Programs and Features applet.
   * Find Microsoft Visual Studio 2015 in the list.
   * Right click and invoke 'Change'.
   * Click the 'Modify' button.
   * Enable 'Cross Platform Mobile Development->Visual C++ Mobile Development->Visual C++ iOS Development'

As an alternative, you can certainly also clone this project and [build your own copy](https://github.com/Microsoft/MIEngine/wiki/Building-the-MIEngine).

#### Linux Computer

1. Install the [.NET Core Command line tools (CLI)](https://github.com/gregg-miskelly/omnisharp-vscode/blob/daily-build-docs/debugger.md#2-install-net-command-line-tools).
2. Install CLRDBG by running the following command. Replace '~/clrdbg' with wherever you want clrdbg installed to.

    curl -sSL https://raw.githubusercontent.com/Microsoft/MIEngine/getclrdbg-release/scripts/GetClrDbg.sh | 
        bash /dev/stdin vs2015u2 ~/clrdbg

### Setting up a transport
Visual Studio relies on another executable to take care of remoting stdin/out between the Windows computer and the target computer. The nice side of this is that you can debug as long as you have some way to exchange messages between your two computers; The downside is that this will take a bit of work to setup. Then again, you _are_ reading the offroad instructions :). Here are some hints for getting things setup with Docker or SSH, but you can bring anything you want.

#### Docker
If your target is running in Docker, you want to install the [Docker Toolbox](https://www.docker.com/products/docker-toolbox), and configure the Docker client tools to connect to whatever host machine is running your container.

Test to make sure you have things setup correctly by running something like:

    REM List the containers so you know which one to target
    "C:\Program Files\Docker Toolbox\docker.exe" ps
    "C:\Program Files\Docker Toolbox\docker.exe" exec -i <container-id> /bin/bash -c "echo hello world"
    REM 'hello world' should print

#### SSH
For SSH support, you will first want to enable SSH in your Linux server. For example, on Ubuntu you can do that by running:

    sudo apt-get install openssh-server

Then, on Windows you need an SSH client designed to be used programmatically. Plink.exe from [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) fits the bill, though you can certainly use other tools too. 

Next, you need a scriptable way to authenticate. One option is to provide the password on the command line, but obviously there are some security concerns there. A more secure option is to use SSH keys --

1. Download puttygen.exe from [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
2. Run the tool and click 'Generate' and follow the instructions.
3. Save the generated private key to a file.
4. Copy the public key's text from the top part of the PuTTY Key Generator's Window.
5. Add this to the ~/.ssh/authorized_keys file on your server.
6. Test your connection from the command line. This will also allow you to accept the server's key on the client, which must be done the first time.

Example:

    c:\mytools\plink.exe -i c:\users\greggm\ssh-key.ppk greggm@mylinuxbox -batch -t echo "hello world"
    REM 'hello world' should print

### Sharing sources and compiled binaries

##### Building on Windows
If you are developing and compiling your app in Visual Studio, you need some way of getting the following on your Linux target machine:

* The PDB files for any module you want to debug. Currently, by default, projects built on Windows will **not** generate PDBs that are readable on Linux, so you need to change your project to use Portable PDBs ([instructions](https://github.com/OmniSharp/omnisharp-vscode/wiki/Portable-PDBs#how-to-generate-portable-pdbs)).
* The app itself and any runtime dependencies it might have
* The '\<proj-name\>.deps.json' file which is used by the 'dotnet' host executable to determine runtime dependencies

For simple projects, you can find these files in \<SolutionDir\>\artifacts\src\\<ProjectName\>\bin\Debug\netcoreapp1.0. For projects with dependencies, you can use 'dotnet publish' to assemble the files you are likely to need.

##### Building on Linux
If you are compiling your app on Linux, you need some way of sharing sources back to Windows so that Visual Studio can open them. Keep in mind that we don't yet support checks in the debugger to make sure the files match exactly, so make sure you are debugging with the right set of source files.

##### Transferring file
Obviously there any many options to transfer files between Windows and Linux. This document will not try and list all of them, but for those just trying to kick the tires, here are a few commands you might find useful:

Connect to a Windows share from Ubuntu:

    sudo apt-get install cifs-utils
    sudo mkdir /mnt/myshare
    sudo mount -t cifs //my-windows-computer/myshare /mnt/myshare -o domain=my-windows-domain,username=myalias,uid=$USER,gid=$USER
    # /mnt/myshare should now be mapped

To copy files using scp (SSH-based secure copy):

    # NOTE: greggm is an example account name
    c:\mytools\pscp.exe -i c:\users\greggm\my-ssh-key.ppk c:\MyProject\artifacts\src\MyProject\bin\Debug\netcoreapp1.0\* greggm@mylinuxbox:/home/greggm/myproject

### Create launch options file:
Next, you need to create an XML file that will tell Visual Studio how to debug. You may want to save https://github.com/Microsoft/MIEngine/blob/master/src/MICore/LaunchOptions.xsd to your project so that the XML editor will give you IntelliSense for the file. Here is an example launch option file which uses plink.exe to connect to the target over SSH and launch a project called 'clicon':

    <?xml version="1.0" encoding="utf-8" ?>
    <PipeLaunchOptions xmlns="http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014"
      PipePath="c:\mytools\plink.exe" PipeArguments="-i c:\users\greggm\ssh-key.ppk greggm@mylinuxbox -batch -t ~/clrdbg/clrdbg --interpreter=mi"
      TargetArchitecture="x64" MIMode="clrdbg" ExePath="dotnet" WorkingDirectory="~/clicon" ExeArguments="bin/Debug/netcoreapp1.0/MyExmapleApp.dll">
    </PipeLaunchOptions>

Let's look at how this works:
* PipePath: this is the path to the executable that MIEngine will launch that will connect to the target computer, launch clrdbg, and establish that stdin/out connection. If you are using Docker, you want this set to 'C:\Program Files\Docker Toolbox\docker.exe' (or wherever the Docker client tools are installed).
* PipeArguments: Any arguments that the executable specified in 'PipePath' takes. In my example, I am telling plink.exe the path to my private SSH key, telling it to connect to my SSH Linux box, and telling it to run executable clrdbg from the '~/clrdbg' directory that I installed it to. If I was using Docker, I might set this to 'exec -i <container-id> ~/clrdbg/clrdbg --interpreter=mi'.
* ExePath: the path, on the Linux computer, to the executable I want to run. .NET Core provides a generic host executable ('dotnet') which the installer adds to the path. So as long as your application uses the default 'dotnet' host executable, you can leave this as-is.
* ExeArguments: If you are using the 'dotnet' host executable, the first argument is the path to the dll you want to run. After that you can include any command line arguments that your executable accepts.

##### Attaching to a process
You can also use launch option files to attach to the target process. For example, here is how to attach to process id #13594 with a clrdbg installed to ~/clrdbg:

    <PipeLaunchOptions xmlns="http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014"
      PipePath="c:\mytools\plink.exe" PipeArguments="-i c:\users\greggm\my-ssh-key.ppk greggm@mylinuxbox -batch -t ~/clrdbg/clrdbg --interpreter=mi"
      TargetArchitecture="x64" MIMode="clrdbg">
      <CustomLaunchSetupCommands>
        <Command>-target-attach 13594</Command>
      </CustomLaunchSetupCommands>
     <LaunchCompleteCommand>None</LaunchCompleteCommand>
    </PipeLaunchOptions>

You can also attach to a process by name instead of by process id using '-n <process-name' instead of '13594', though this option is likely only useful if you have configured your project to have a host executable since there are probably too many 'dotnet' processes.
If you have a bash script that can find the right process, you can have the bash script provide the process id to clrdbg using the '--attach' command line argument. In this case, remove the 'Command' element from within 'CustomLaunchSetupCommands'.

### Turn of Just My Code if you are retail debugging
If you are attempting to debug retail code, you will want to turn off Just My Code through Tools->Options->Debugging in Visual Studio. MIEngine has some issues doing this on the fly still, so I would recommend doing it before start debugging.

### Start debugging
1. Start Visual Studio
2. View->Other Windows->Command Window
3. Debug.MIDebugLaunch /Executable:dotnet /OptionsFile:\<path-to-the-xml-file-you-saved\>
