The MIEngine allows sending commands directly to the underlying debugger (gdb/lldb).

To do so:
* View -> Other Windows -> Command Window
* Run: `Debug.MIDebugExe example-gdb-command-goes-here`

Notes:
* This is only supported when the target process is stopped
* Don't run commands that cause the target process to run again

![Example screenshot](MIDebugExec.png)