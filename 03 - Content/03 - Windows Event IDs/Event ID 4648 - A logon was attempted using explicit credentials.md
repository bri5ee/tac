# Description
This event is generated when a process attempts an account logon by explicitly specifying that account’s credentials. This most commonly occurs in batch-type configurations such as scheduled tasks, or when using the “RUNAS” command. It is also a routine event which periodically occurs during normal operating system activity.
_**Required Server Roles:**_ None.
_**Minimum OS Version:**_ Windows Server 2008, Windows Vista.
_**Event Versions:**_ 0.
_**Field Descriptions:**_

**Subject:**

- **Security ID** [Type = SID]**:** SID of account that requested the new logon session with explicit credentials. Event Viewer automatically tries to resolve SIDs and show the account name. If the SID cannot be resolved, you will see the source data in the event.

> **Note**  A **security identifier (SID)** is a unique value of variable length used to identify a trustee (security principal). Each account has a unique SID that is issued by an authority, such as an Active Directory domain controller, and stored in a security database. Each time a user logs on, the system retrieves the SID for that user from the database and places it in the access token for that user. The system uses the SID in the access token to identify the user in all subsequent interactions with Windows security. When a SID has been used as the unique identifier for a user or group, it cannot ever be used again to identify another user or group. For more information about SIDs, see [Security identifiers](https://learn.microsoft.com/en-us/windows/access-protection/access-control/security-identifiers).

- **Account Name** [Type = UnicodeString]**:** the name of the account that requested the new logon session with explicit credentials.
    
- **Account Domain** [Type = UnicodeString]**:** subject’s domain or computer name. Formats vary, and include the following:
    
    - Domain NETBIOS name example: CONTOSO
        
    - Lowercase full domain name: contoso.local
        
    - Uppercase full domain name: CONTOSO.LOCAL
        
    - For some [well-known security principals](https://learn.microsoft.com/en-us/windows/security/identity-protection/access-control/security-identifiers), such as LOCAL SERVICE or ANONYMOUS LOGON, the value of this field is “NT AUTHORITY”.
        
    - For local user accounts, this field will contain the name of the computer or device that this account belongs to, for example: “Win81”.
        
- **Logon ID** [Type = HexInt64]**:** hexadecimal value that can help you correlate this event with recent events that might contain the same Logon ID, for example, “[4624](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624): An account was successfully logged on.”
    
- **Logon GUID** [Type = GUID]: a GUID that can help you correlate this event with another event that can contain the same **Logon GUID**, “[4769](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4769)(S, F): A Kerberos service ticket was requested event on a domain controller.
    
    It also can be used for correlation between a 4648 event and several other events (on the same computer) that can contain the same **Logon GUID**, “[4624](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624)(S): An account was successfully logged on” and “[4964](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4964)(S): Special groups have been assigned to a new logon.”
    
    This parameter might not be captured in the event, and in that case appears as “{00000000-0000-0000-0000-000000000000}”.
    

> **Note**  **GUID** is an acronym for 'Globally Unique Identifier'. It is a 128-bit integer number used to identify resources, activities or instances.

**Account Whose Credentials Were Used:**

- **Account Name** [Type = UnicodeString]**:** the name of the account whose credentials were used.
    
- **Account Domain** [Type = UnicodeString]**:** subject’s domain or computer name. Formats vary, and include the following:
    
    - Domain NETBIOS name example: CONTOSO
        
    - Lowercase full domain name: contoso.local
        
    - Uppercase full domain name: CONTOSO.LOCAL
        
    - For some [well-known security principals](https://learn.microsoft.com/en-us/windows/security/identity-protection/access-control/security-identifiers), such as LOCAL SERVICE or ANONYMOUS LOGON, the value of this field is “NT AUTHORITY”.
        
    - For local user accounts, this field will contain the name of the computer or device that this account belongs to, for example: “Win81”.
        
- **Logon GUID** [Type = GUID]: a GUID that can help you correlate this event with another event that can contain the same **Logon GUID**, “[4769](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4769)(S, F): A Kerberos service ticket was requested event on a domain controller.
    
    It also can be used for correlation between a 4648 event and several other events (on the same computer) that can contain the same **Logon GUID**, “[4624](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624)(S): An account was successfully logged on” and “[4964](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4964)(S): Special groups have been assigned to a new logon.”
    
    This parameter might not be captured in the event, and in that case appears as “{00000000-0000-0000-0000-000000000000}”.
    

> **Note**  **GUID** is an acronym for 'Globally Unique Identifier'. It is a 128-bit integer number used to identify resources, activities or instances.

**Target Server:**

- **Target Server Name** [Type = UnicodeString]**:** the name of the server on which the new process was run. Has “**localhost**” value if the process was run locally.
    
- **Additional Information** [Type = UnicodeString]**:** there is no detailed information about this field in this document.
    

**Process Information:**

- **Process ID** [Type = Pointer]: hexadecimal Process ID of the process which was run using explicit credentials. Process ID (PID) is a number used by the operating system to uniquely identify an active process. To see the PID for a specific process you can, for example, use Task Manager (Details tab, PID column):
    
    
    If you convert the hexadecimal value to decimal, you can compare it to the values in Task Manager.
    
    You can also correlate this process ID with a process ID in other events, for example, “[4688](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4688): A new process has been created” **Process Information\New Process ID**.
    
- **Process Name** [Type = UnicodeString]**:** full path and the name of the executable for the process.
    

**Network Information:**

- **Network Address** [Type = UnicodeString]**:** IP address of machine from which logon attempt was performed.
    
    - IPv6 address or ::ffff:IPv4 address of a client.
        
    - ::1 or 127.0.0.1 means localhost.
        
- **Port** [Type = UnicodeString]: source port which was used for logon attempt from remote machine.
    
    - 0 for interactive logons.