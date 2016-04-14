CLRDBG is mentioned a few places in the documentation and the code. This is our new cross-platform command line debugger used for debugging code running on [CoreCLR](https://github.com/dotnet/coreclr). It is a port of the backend for Visual Studio's normal .NET debugger to run on other platforms + a new command line debugger wrapper which speaks the MI protocol.

CLRDBG will release for the first time with .NET Core RC2. On Linux, you can obtain the latest version designed to run against VS 2015 Update 2 with the following command. Replace '~/clrdbg' if you want to install it to a different directory.

    curl -sSL https://raw.githubusercontent.com/Microsoft/MIEngine/getclrdbg-release/scripts/GetClrDbg.sh | bash /dev/stdin vs2015u2 ~/clrdbg

CLRDBG + MI/Engine is what powered the CoreCLR Linux debugging demo that was shown in 2015 //Build/ conference day one keynote ([video link](https://channel9.msdn.com/Events/Build/2015/KEY01#time=25m51s)).

It is also what powered the Visual Studio Code debugging on MacOS demo in the 2015 Connect() day one keynote ([video link](https://channel9.msdn.com/Events/Visual-Studio/Connect-event-2015/010#time=34m53s)).