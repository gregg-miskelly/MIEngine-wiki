Attaching to a process on Linux with GDB may fail with "ptrace:Operation not permitted". By default Linux does not allow attaching to a process which wasn't launched by the debugger, see https://www.kernel.org/doc/Documentation/security/Yama.txt for more details. 

There are two ways to workaround that,

1. Run the following command as super user. This will set the ptrace level to 0, after this just with user permissions you can attach to processes which are not launched by the debugger. `echo "0" > /proc/sys/kernel/yama/ptrace_scope`

2. Launch GDB as super user and attach to processes. 

