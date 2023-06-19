---
description: SSH (Secure Shell)
---

# 22 - SSH

## Info

SSH es un protocolo de administración remota que permite a los usuarios **controlar** y **modificar** sus servidores remotos a través de Internet mediante un mecanismo de **autenticación seguro**. Como una alternativa más segura al protocolo **Telnet**, que transmite información sin cifrar, SSH utiliza **técnicas criptográficas** para garantizar que todas las comunicaciones hacia y desde el servidor remoto estén cifradas.

SSH proporciona un mecanismo para autenticar un usuario remoto, transferir entradas desde el cliente al host y retransmitir la salida de vuelta al cliente. Esto es especialmente útil para administrar sistemas remotos de manera segura y eficiente, sin tener que estar físicamente presentes en el sitio.

## Enumeración

{% code overflow="wrap" %}
```bash
# Banner Grabbing
nc -vn <IP> 22


nmap -p22 <ip> -sC # Send default nmap scripts for SSH
nmap -p22 <ip> -sV # Retrieve version
nmap -p22 <ip> --script ssh2-enum-algos # Retrieve supported algorythms 
nmap -p22 <ip> --script ssh-hostkey --script-args ssh_hostkey=full # Retrieve weak keys
nmap -p22 <ip> --script ssh-auth-methods --script-args="ssh.user=root" # Check authentication methods
```
{% endcode %}

## Laboratorios

A continuación, se proporciona el enlace directo a la web donde copiamos todo el comando de ‘docker’ para desplegar nuestro contenedor:

* **Docker Hub OpenSSH-Server**: [https://hub.docker.com/r/linuxserver/openssh-server](https://hub.docker.com/r/linuxserver/openssh-server)

Cabe destacar que a través de la versión de SSH, también podemos identificar el codename de la distribución que se está ejecutando en el sistema.

Por ejemplo, si la versión del servidor SSH es “**OpenSSH 8.2p1 Ubuntu 4ubuntu0.5**“, podemos determinar que el sistema está ejecutando una distribución de Ubuntu. El número de versión “**4ubuntu0.5**” se refiere a la revisión específica del paquete de SSH en esa distribución de Ubuntu. A partir de esto, podemos identificar el **codename** de la distribución de Ubuntu, que en este caso sería “**Focal**” para Ubuntu 20.04.

Todas estas búsquedas las aplicamos sobre el siguiente dominio:

* **Launchpad**: [https://launchpad.net/ubuntu](https://launchpad.net/ubuntu)

## Tools

{% embed url="https://github.com/jtesta/ssh-audit" %}
