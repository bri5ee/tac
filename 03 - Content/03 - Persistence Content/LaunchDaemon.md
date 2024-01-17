
# Description
`Launch Daemons` are property list ([[plist]]) files that are executed on a MacOS system for each user prior to login. They are a type of launch item responsible for starting background processes that run without the need for user interaction. As a result, they are commonly used by malware for establishing persistence. Launch daemons can be found in `/Library/LaunchDaemons` (system Launch Daemons are now protected by System Integrity Protection as of OS X 10.11 El Capitan) and are typically used to manage automated tasks such as software updates. 
In the context of Mac malware, `Launch Daemons` are commonly used in parallel with [[Launch Agents]] to provide multiple vectors of persistence. 
`Launch Daemons` usually run with root permissions unless otherwise specified with the `UserName` key. Likewise, they require administrator-level privileges to be added to a system. However, this can be easily achieved by prompting an administrative user to provide their credentials. 
# Activity
Activity associated with `Launch Daemons` typically involve the following steps:
1. Malware checks whether it is being run with administrative privileges
	1. If not, invokes `AuthorizationExecuteWithPrivileges` API to prompt for user credentials so that the program can be run with elevated privileges
2. Write a property list (plist) to `/Library/LaunchDaemons/com.example.plist`
	1. If `RunAtLoad` key is set to `True`, the malware will run each time the system is rebooted
	2. `Program`/`Program Arguments` key denotes the path to the script/binary to be executed
3. Launch the plist with `launchctl load /Library/LaunchDaemons/com.example.plist`
 > NOTE: Launch Daemons only exist at the system level and computer level. For adversaries, this means that writing a daemon to `/Library/LaunchDaemons/*` requires administrative privileges.  System Launch Daemons are also protected by SIP so the primary location to monitor for changes by utilizing capabilities like FIM is `/Library/LaunchDaemons/*`. 
# Artifacts
1. [[plist]]
	1. Check for extraneous plists: `/Library/LaunchDaemons/<com.example.plist>`
	2. It is not uncommon to see anomalous plist files used for persistence from adversaries with arbitrary naming conventions. Having a baseline of what daemons are running and what daemons were recently written using file integrity monitor events from tools such as [osquery](https://osquery.readthedocs.io/en/stable/deployment/file-integrity-monitoring/) can be beneficial during investigations.
2. [[AuthorizationExecuteWithPrivileges]]
	1. Check logs for evidence of the API being called:  `strings /private/var/log/DiagnosticMessages/* | grep -A 1 AuthorizationExecuteWithPrivileges!`
	2. Monitor for the execution of `security_authtrampoline` process
	3. Ensure the executable that is being referenced by `security_authtrampoline` is owned by the `root` user, and the executable being referenced by `security_authtrampoline` is an absolute path and not a relative path
3. [[Plist Program and Program Arguments]]
	1. Identify the values defined by the `Program`/`Program Arguments` key in the plist file. These contain the path to the launch item's executable binary and the arguments that are passed alongside the executable. As the launch item's executable may be malicious, analysis on the executable and determining its legitimacy is important in identifying if the plist is legitimate or not.
## MITRE Guide
| ID      | Data Source | Data Component                   | Detects                                                                                                                                                                                                                                                              |
|---------|-------------|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [DS0017](https://attack.mitre.org/datasources/DS0017/) | Command | [Command Execution](https://attack.mitre.org/datasources/DS0017/#Command%20Execution) | Some legitimate LaunchDaemons point to unsigned code that could be exploited. For Launch Daemons with the `RunAtLoad` parameter set to true, ensure the `Program` parameter points to signed code or executables are in alignment with enterprise policy. Some parameters are interchangeable with others, such as `Program` and `ProgramArguments` parameters but one must be present. [^3] |
| [DS0022](https://attack.mitre.org/datasources/DS0022) | File | [File Creation](https://attack.mitre.org/datasources/DS0022/#File%20Creation) | Monitor for new files added to the `/Library/LaunchDaemons/` folder. The System LaunchDaemons are protected by SIP.                                                                                                                                                   |
| | | [File Modification](https://attack.mitre.org/datasources/DS0022/#File%20Modification) | Monitor files for changes that may create or modify Launch Daemons to execute malicious payloads as part of persistence.                                                                                                                                              |
| [DS0009](https://attack.mitre.org/datasources/DS0009) | Process | [Process Creation](https://attack.mitre.org/datasources/DS0009/#Process%20Creation) | Monitor for newly executed processes that may create or modify Launch Daemons to execute malicious payloads as part of persistence.                                                                                                                           |
| [DS0019](https://attack.mitre.org/datasources/DS0019) | Service | [Service Creation](https://attack.mitre.org/datasources/DS0019/#Service%20Creation) | Monitor for newly constructed services may create or modify Launch Daemons to execute malicious payloads as part of persistence.                                                                                                                                  |
| | | [Service Modification](https://attack.mitre.org/datasources/DS0019/#Service%20Modification) | Monitor services for changes made to Launch Daemons to execute malicious payloads as part of persistence.                                                                                                                                                          |
Source: https://attack.mitre.org/techniques/T1543/004/#:~:text=Launch%20Daemons%20are%20plist%20files,the%20need%20for%20user%20interaction.
# Additional Info
`Launch Daemons` are commonly used in conjunction with [[Launch Agents]] for redundancy. Therefore, it is crucial to check for the presence of both of these persistence methods. 
Additionally, legitimate `Launch Daemons` can be leveraged for malicious purposes by altering the unsigned code that it points to, replacing it with something malicious. 