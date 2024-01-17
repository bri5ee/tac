# Description
`PsExec` is a utility often used by system administrators for executing programs on remote Windows machines. It's a standalone tool that allows interactive access to programs running remotely. While similar features are provided by PowerShell Remoting in newer Windows versions, `PsExec` remains popular for its simplicity and effectiveness, making it attractive to attackers as well. Exploit kits like Cobalt Strike and Metasploit offer `PsExec`-like capabilities, and it's known that various threat actors use either Microsoft's official, signed version of PsExec or a custom variant.

In scenarios of lateral movement by attackers, Sysinternals `PsExec` is commonly utilized. For example, if an attacker has established a presence in a network and gained compromised credentials with Local Administrator rights on a host, they can use PsExec on that host to execute commands on another host.

# Activity
The typical pattern of `PsExec` activity involves the following steps:

1. Authentication to the target host over SMB, using either the existing logon session or provided credentials.
2. Transfer of the `PSEXECSVC.EXE` service executable file to the target host's `ADMIN$` (`System32`) directory.
3. Connection to the service control manager of the target host to set up and initiate `PSEXESVC`.
4. Enabling input/output through the named pipe `.pipepsexesvc`.
5. Upon completion, the removal of the service and deletion of the executable file.

# Artifacts
When attempting to detect `PsExec` activity, there are a handful of indicators that might be of use by utilizing host-based telemetry tools such as Sysmon, CrowdStrike Falcon, Windows System / Security logs, etc.
## Source
1. [[Event ID 4648 - A logon was attempted using explicit credentials]]
	1. This event is logged when a logon is attempted with explicit user credentials. In the context of `PsExec`, this precedes the actual `PsExec` process creation, as it represents the moment the attacker (or user) inputs credentials for remote access. This could be for authenticating to the target host over SMB.
2. [[Event ID 4688 - A new process has been created]]
	1. This event is recorded when a new process, in this case, `PsExec`, is created. It indicates that `PsExec` has been initiated on the source host to execute commands on a remote host.
3. [[Event ID 4689 - A process has exited]]
	1. This event is logged when a process, such as `PsExec`, exits or is terminated. It marks the completion of the `PsExec` operation initiated by the attacker or user.
## Target
1. [[Event ID 4624 - An account was successfully logged on 1]]
	1. User successfully logs on. In the context of` PsExec`, this logon could be the attacker authenticating to the target host over SMB.
2. [[Event ID 4672 - Special privileges assigned to new logon 1]]
	1. Event occurs immediately following a successful logon (Event ID: 4624), indicating that special privileges are assigned to the new logon session.
3. [[Event ID 5140 - A network share object was accessed 1]]
	1. This event is logged when a shared object (like a network share) is accessed. In the PsExec process, this could represent accessing the `ADMIN$` share on the target host to copy the `PSEXECSVC.EXE` file.
4. [[Event ID 5145 - A Network Share Object Was Checked To See Whether Client Can Be Granted Desired Access]]
	1. Indicates a check to see if the client (in this case, the `PsExec` process) can be granted the desired access to a network share object.
5. [[Event ID 7045 - A new service was installed in the system]]
	1. Logs the installation of the `PSEXESVC` service on the target host, which happens after the `PSEXECSVC.EXE` file is copied over.
6. [[Event ID 4656 - A handle to an object was requested 1]]
	1. Indicates that a handle to an object (like a file or service) was requested, which is part of the process of starting the `PSEXESVC` service.
7. [[Event ID 7036 - Service state has changed]]
	1. State of a service has changed. The first occurrence indicates that the `PSEXESVC` service has started (moved to the Executing state). After the following event (Event ID 4663), the service state will change again to a stopped state.
8. [[Event ID 4663 - An attempt was made to access an object 1]]
	1. An attempt to access an object, which could be related to the activities performed by the `PSEXESVC` service.