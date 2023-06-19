# 20|21 - FTP

## Info

El <mark style="color:green;">**Protocolo de Transferencia de Archivos (FTP)**</mark> es un protocolo de red estándar utilizado para la transferencia de archivos informáticos entre un cliente y un servidor en una red informática. Es un protocolo de texto plano que utiliza como carácter de nueva línea 0x0d 0x0a por lo que a veces es necesario conectarse utilizando&#x20;

* `telnet`
* `ncat -C`

## Banner grab

```
nc -vn <IP> 21
openssl s_client -connect crossfit.htb:21 -starttls ftp #Get certificate if any
```

## Scripts de Nmap

{% code overflow="wrap" %}
```bash
nmap --script ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 192.168.6.66
```
{% endcode %}

## Conecciones

### Anonimo | anonymous

```bash
## Inicio de sesión anónimo, anonymous:anonymous
ftp <IP>

# Conección web
ftp://anonymous:anonymous@<IP>

lftp
lftp :~> set ftp:ssl-force true
lftp :~> set ssl:verify-certificate no
lftp :~> connect 10.10.10.208
lftp 10.10.10.208:~> login                       
Usage: login <user|URL> [<pass>]
lftp 10.10.10.208:~> login username Password
```

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### Fuerza bruta de usuarios

#### Fuerza bruta con hydra

{% code overflow="wrap" %}
```bash
hydra <IP> ftp -l username -P /usr/share/wordlists/rockyou.txt -e ns -vV
```
{% endcode %}

#### Fuerza bruta con MSF

{% code overflow="wrap" %}
```bash
use auxiliary/scanner/ftp/ftp_login
set rhosts <IP>
set user_file /root/Desktop/user.txt
set pass_file /root/Desktop/pass.txt
set stop_on_success true
run
```
{% endcode %}



### Archivos de configuración relacionados a FTP

• ftpusers

• ftp.conf

• proftpd.conf



## Laboratorios

FTP es un protocolo ampliamente utilizado para la **transferencia de archivos** en redes. La enumeración del servicio FTP implica recopilar información relevante, como la versión del servidor FTP, la configuración de permisos de archivos, los usuarios y las contraseñas (mediante ataques de fuerza bruta o guessing), entre otros.

* **Docker-FTP-Server**: [https://github.com/garethflowers/docker-ftp-server](https://github.com/garethflowers/docker-ftp-server)

Una de las herramientas que usamos en esta clase para el primer proyecto que nos descargamos es ‘**Hydra**‘. Hydra es una herramienta de pruebas de penetración de código abierto que se utiliza para realizar ataques de fuerza bruta contra sistemas y servicios protegidos por contraseña. La herramienta es altamente personalizable y admite una amplia gama de protocolos de red, como HTTP, FTP, SSH, Telnet, SMTP, entre otros.

El siguiente de los proyectos que utilizamos para desplegar el contenedor que permite la autenticación de usuarios invitados para FTP, es el proyecto ‘**docker-anon-ftp**‘ de ‘**metabrainz**‘. A continuación, se os proporciona el enlace al proyecto:

* **Docker-ANON-FTP**: [https://github.com/metabrainz/docker-anon-ftp](https://github.com/metabrainz/docker-anon-ftp)





## Referencias

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp" %}

