Most of the MIEngine assemblies depend on Microsoft.DebugEngineHost. Microsoft.DebugEngineHost provides a number of utility services to the MIEngine. Some examples include configuration, interface marshalling, and wait dialogs.

The basic design of this assembly is that we will actually have multiple versions of this assembly with the same public API, and assembly identity. Thus we can compile all of our other code once and have it work in both IDEs.

Contract version: The first version of Microsoft.DebugEngineHost is in [MIEngine\src\DebugEngineHost.Stub\DebugEngineHost.ref.cs](https://github.com/Microsoft/MIEngine/blob/master/src/DebugEngineHost.Stub/DebugEngineHost.ref.cs). This defines the contract that the reset of the code is compiled against.

Visual Studio version: Under MIEngine\src\DebugEngineHost you can find the version of DebugEngineHost used when running in Visual Studio.

VS Code version: In a project that we expect to open source in the future (most likely early to mid 2016) you can find the VS Code version of DebugEngineHost.