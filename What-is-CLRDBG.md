CLRDBG is mentioned a few places in the documentation and the code. This is our new cross-platform command line debugger used for debugging code running on [CoreCLR](https://github.com/dotnet/coreclr). It is a port of the backend for Visual Studio's normal .NET debugger to run on other platforms + a new command line debugger wrapper which speaks the MI protocol.

CLRDBG has not yet been released because isn't yet far enough along to be a usable debugger. 

CLRDBG + MI/Engine is what powered the CoreCLR Linux debugging demo that was shown in //Build/ conference day one keynote ([video link](https://channel9.msdn.com/Events/Build/2015/KEY01#time=25m51s)).