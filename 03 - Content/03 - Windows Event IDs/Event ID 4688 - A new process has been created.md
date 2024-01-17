_**Required Server Roles:**_ None.

_**Minimum OS Version:**_ Windows Server 2008, Windows Vista.

_**Event Versions:**_

- 0 - Windows Server 2008, Windows Vista.
- 1 - Windows Server 2012 R2, Windows 8.1.
    - Added "Process Command Line" field.
- 2 - Windows 10.
    - **Subject** renamed to **Creator Subject**.
    - Added "**Target Subject**" section.
    - Added "**Mandatory Label**" field.
    - Added "**Creator Process Name**" field.

_**Field Descriptions:**_

**Creator Subject** [Value for versions 0 and 1 – **Subject**]**:**

- **Security ID** [Type = SID]**:** SID of account that requested the "create process" operation. Event Viewer automatically tries to resolve SIDs and show the account name. If the SID cannot be resolved, you will see the source data in the event.

> A **security identifier (SID)** is a unique value of variable length used to identify a trustee (security principal). Each account has a unique SID that is issued by an authority, such as an Active Directory domain controller, and stored in a security database. Each time a user logs on, the system retrieves the SID for that user from the database and places it in the access token for that user. The system uses the SID in the access token to identify the user in all subsequent interactions with Windows security. When a SID has been used as the unique identifier for a user or group, it cannot ever be used again to identify another user or group. For more information about SIDs, see [Security identifiers](https://learn.microsoft.com/en-us/windows/access-protection/access-control/security-identifiers).

- **Account Name** [Type = UnicodeString]**:** the name of the account that requested the "create process" operation.
    
- **Account Domain** [Type = UnicodeString]**:** subject's domain or computer name. Formats vary, and include the following:
    
    - Domain NETBIOS name example: CONTOSO
        
    - Lowercase full domain name: contoso.local
        
    - Uppercase full domain name: CONTOSO.LOCAL
        
    - For some [well-known security principals](https://learn.microsoft.com/en-us/windows/security/identity-protection/access-control/security-identifiers), such as LOCAL SERVICE or ANONYMOUS LOGON, the value of this field is "NT AUTHORITY".
        
    - For local user accounts, this field will contain the name of the computer or device that this account belongs to, for example: "Win81".
        
- **Logon ID** [Type = HexInt64]**:** hexadecimal value that can help you correlate this event with recent events that might contain the same Logon ID, for example, "[4624](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624): An account was successfully logged on."
    

**Target Subject** [Version 2]**:**

> [Note] This event includes the principal of the process creator, but this is not always sufficient if the target context is different from the creator context. In that situation, the subject specified in the process termination event does not match the subject in the process creation event even though both events refer to the same process ID. Therefore, in addition to including the creator of the process, we will also include the target principal when the creator and target do not share the same logon.

- **Security ID** [Type = SID] [Version 2]**:** SID of target account. Event Viewer automatically tries to resolve SIDs and show the account name. If the SID cannot be resolved, you will see the source data in the event.

> [Note] A **security identifier (SID)** is a unique value of variable length used to identify a trustee (security principal). Each account has a unique SID that is issued by an authority, such as an Active Directory domain controller, and stored in a security database. Each time a user logs on, the system retrieves the SID for that user from the database and places it in the access token for that user. The system uses the SID in the access token to identify the user in all subsequent interactions with Windows security. When a SID has been used as the unique identifier for a user or group, it cannot ever be used again to identify another user or group. For more information about SIDs, see [Security identifiers](https://learn.microsoft.com/en-us/windows/access-protection/access-control/security-identifiers).

- **Account Name** [Type = UnicodeString] [Version 2]**:** the name of the target account.
    
- **Account Domain** [Type = UnicodeString] [Version 2]**:** target account's domain or computer name. Formats vary, and include the following:
    
    - Domain NETBIOS name example: CONTOSO
        
    - Lowercase full domain name: contoso.local
        
    - Uppercase full domain name: CONTOSO.LOCAL
        
    - For some [well-known security principals](https://learn.microsoft.com/en-us/windows/security/identity-protection/access-control/security-identifiers), such as LOCAL SERVICE or ANONYMOUS LOGON, the value of this field is "NT AUTHORITY".
        
    - For local user accounts, this field will contain the name of the computer or device that this account belongs to, for example: "Win81".
        
- **Logon ID** [Type = HexInt64] [Version 2]**:** hexadecimal value that can help you correlate this event with recent events that might contain the same Logon ID, for example, "[4624](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624): An account was successfully logged on."
    

**Process Information:**

- **New Process ID** [Type = Pointer]: hexadecimal Process ID of the new process. Process ID (PID) is a number used by the operating system to uniquely identify an active process. To see the PID for a specific process you can, for example, use Task Manager (Details tab, PID column):
    

> If you convert the hexadecimal value to decimal, you can compare it to the values in Task Manager.

- **New Process Name** [Type = UnicodeString]**:** full path and the name of the executable for the new process.
    
- **Token Elevation Type** [Type = UnicodeString]**:**
    
    - **%%1936:** Type 1 is a full token with no privileges removed or groups disabled. A full token is only used if User Account Control is disabled or if the user is the built-in Administrator account (for which UAC is disabled by default), service account, or local system account.
        
    - **%%1937:** Type 2 is an elevated token with no privileges removed or groups disabled. An elevated token is used when User Account Control is enabled and the user chooses to start the program using Run as administrator. An elevated token is also used when an application is configured to always require administrative privilege or to always require maximum privilege, and the user is a member of the Administrators group.
        
    - **%%1938:** Type 3 is a limited token with administrative privileges removed and administrative groups disabled. The limited token is used when User Account Control is enabled, the application does not require administrative privilege, and the user does not choose to start the program using Run as administrator.
        
- **Mandatory Label** [Version 2] [Type = SID]**:** SID of [integrity label](https://learn.microsoft.com/en-us/windows/win32/secauthz/mandatory-integrity-control) which was assigned to the new process. Can have one of the following values:
    

Expand table

|SID|RID|RID label|Meaning|
|---|---|---|---|
|S-1-16-0|0x00000000|SECURITY_MANDATORY_UNTRUSTED_RID|Untrusted.|
|S-1-16-4096|0x00001000|SECURITY_MANDATORY_LOW_RID|Low integrity.|
|S-1-16-8192|0x00002000|SECURITY_MANDATORY_MEDIUM_RID|Medium integrity.|
|S-1-16-8448|0x00002100|SECURITY_MANDATORY_MEDIUM_PLUS_RID|Medium high integrity.|
|S-1-16-12288|0X00003000|SECURITY_MANDATORY_HIGH_RID|High integrity.|
|S-1-16-16384|0x00004000|SECURITY_MANDATORY_SYSTEM_RID|System integrity.|
|S-1-16-20480|0x00005000|SECURITY_MANDATORY_PROTECTED_PROCESS_RID|Protected process.|

- **Creator Process ID** [Type = Pointer]**:** hexadecimal Process ID of the process which ran the new process. If you convert the hexadecimal value to decimal, you can compare it to the values in Task Manager.

> You can also correlate this process ID with a process ID in other events, for example, "[4688](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4688): A new process has been created" **Process Information\New Process ID**.

- **Creator Process Name** [Version 2] [Type = UnicodeString]**:** full path and the name of the executable for the process.
    
- **Process Command Line** [Version 1, 2] [Type = UnicodeString]**:** contains the name of executable and arguments which were passed to it. You must enable "Administrative Templates\System\Audit Process Creation\Include command line in process creation events" group policy to include command line in process creation events:
    
    
    By default **Process Command Line** field is empty.