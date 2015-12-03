Here are the requirements to build the MIEngine:

1. You need to be on Windows. The MIEngine cannot currently be compiled on Unix/MacOS.

2. You need to install Visual Studio 2015

3. Not an absolute requirement, but strongly recommended: install [VS 2015 Update 1](http://go.microsoft.com/fwlink/?LinkId=691129) or newer.

4. The Windows 10 tools must be installed (these add support for the portable class libraries that the MIEngine uses). It is also recommended to install the Android tools so that you can test the Android C++ scenario. If you didn't do this or you aren't sure --

    * Open Windows Control Panel.
    * Open the Programs and Features applet.
    * Find Microsoft Visual Studio 2015 in the list.
    * Right click and invoke 'Change'.
    * Click the 'Modify' button.
    * Make sure these are checked
        * Windows and Web Development -> Universal Windows App Development Tools
            * Tools and Windows 10 SDK
            * Windows 10 SDK
        * [Recommended] Programming Languages -> Visual C++ -> Common Tools for Visual C++ 2015
        * Cross Platform Mobile Development
            * [Recommended] Visual C++ Mobile Development
            * [Recommended] Microsoft Visual Studio Emulator for Android
