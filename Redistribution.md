# Redistributing MIEngine

The license of the MIEngine project allows for redistribution, but certain changes must be made in order to ensure different distributions do not conflict with Microsoft's. The VSIX package produced by the build is named MIDebugEngine.DoNotInstall.vsix because it will corrupt the MIEngine that ships with Visual Studio if installed directly. With the correct changes, you can produce a VSIX that is installable side by side with the official MIEngine distributed by Microsoft.

## Changes Required for Redistribution

### GUIDs
Multiple GUIDs need to be replaced in order to redistribute.


1. The COM Registration GUID must be replaced in [AD7Engine.cs](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugEngine/AD7.Impl/AD7Engine.cs#L34) and two places in [Microsoft.MIDebugEngine.pkgdef](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugEngine/Microsoft.MIDebugEngine.pkgdef#L17).
2. The EngineId GUID must be replaced in [EngineConstants.cs](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugEngine/AD7.Impl/EngineConstants.cs) and [Microsoft.MIDebugEngine.pkgdef](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugEngine/Microsoft.MIDebugEngine.pkgdef#L1).
3. If redistributing the iOS or Android Launcher binaries, their COM Registration GUIDs must be changed on both their Launcher classes and their pkgdef files.
4. All GUIDs in the MIDebugPackage project must change. GUIDs are located in [Guids.cs](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugPackage/Guids.cs), [MIDebugPackage.vsct](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugPackage/MIDebugPackage.vsct), and  [source.extension.vsixmanifext](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugPackage/source.extension.vsixmanifest). 

### Strong Name Signing Key
You will need to strong name sign your redistributable binaries with your own private key. The development key is checked in the [Keys](https://github.com/Microsoft/MIEngine/blob/master/Keys/) directory as ExternalKey.snk. This key is referenced by [miengine.settings.targets](https://github.com/Microsoft/MIEngine/blob/master/build/miengine.settings.targets). For more information on signing binaries with your own key, see [MSDN](https://msdn.microsoft.com/en-us/library/xwb8f617(v=vs.110).aspx).

### VSIX Manifest
In addition to updating the GUIDs in [source.extension.vsixmanifext](https://github.com/Microsoft/MIEngine/blob/master/src/MIDebugPackage/source.extension.vsixmanifest), you will need to update all other pertinent information in the manifest to reflect your distribution.

### Renaming
It is fine to use the MIEngine name in redistributions, we expect that 'Microsoft' would be removed from all binaries being redistributed. For example Microsoft.MIEngine.dll -> YourNameHere.MIEngine.dll. 

### Testing
After completing these steps, please test your VSIX on a copy of Visual Studio that has the Microsoft MIEngine installed (in Visual Studio setup, 'Cross Platform Mobile Development -> Visual C++ Mobile Development' is checked). Make sure that your version of the MIEngine isn't being used for Android scenarios and that the Android debugger still works.

### Additional Information
If you would like to redistribute MIEngine and have additional questions, please contact us at vsmiengine@microsoft.com. 