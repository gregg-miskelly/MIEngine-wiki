Attaching to a process on Linux with GDB as a normal user may fail with "ptrace:Operation not permitted". By default Linux does not allow attaching to a process which wasn't launched by the debugger (see the [Yama security documentation](https://www.kernel.org/doc/Documentation/security/Yama.txt) for more details). 

There are two ways to workaround this:

* Run the following command as super user: `echo "0" | sudo tee /proc/sys/kernel/yama/ptrace_scope`

    This will set the ptrace level to 0, after this just with user permissions you can attach to processes which are not launched by the debugger. 

* Alternatively, launch GDB as super user and attach to processes. Use root@machine to login with a password or certificate. Note, many Linux distros are configured to disallow root login through SSH for security reasons. You may have to configure /etc/ssh/sshd_config to allow root login with certificate or password. You can verify SSH connection via a client like putty.exe in windows. 