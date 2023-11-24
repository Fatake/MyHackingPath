# Instalación

## Introducción

En esta entrada se cubrirá la instalación desde diferentes enfoques:

* Instalación de binarios.
* Compilar el codigo por uno mismo.

Como se mencionó en el post anterior [<mark style="color:green;">**Sliver C2**</mark>](./) cuenta con una arquitectura de cliente servidor, donde múltiples clientes son llamados "operadores" o "players" conectado al mismo servidor para colaborar en diversas actividades . En consecuencia, se tiene que que configurar el servidor y, a continuación, distribuir la aplicación cliente junto con las credenciales a todos los operadores. Para facilitar las cosas, utilizaré una sola máquina virtual Kali. Ejecutar el servidor Sliver como root y cada jugador puede ser configurado como un usuario dedicado del sistema Linux.

Recomiendo instalar Sliver dentro de una máquina virtual (VM). Esto facilitará la posterior adición de otras máquinas a la configuración, como una segunda VM que sirva de objetivo para los ataques. Todas tus máquinas virtuales pueden funcionar dentro de una red virtual para simular varios entornos.

### Preparación

Antes de instalar sliver, si se quiere que la herramienta realice compilaciones cross-compile de ejecutables y DLLs se requieren los siguientes paquetes:

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>apt-get install -y build-essential mingw-w64 binutils-mingw-w64 g++-mingw-w64
</strong></code></pre>

## Instalación desde script

Sliver provee un [<mark style="color:green;">**script de instalación**</mark>](https://github.com/BishopFox/sliver/blob/master/docs/install) el cual hace que la instalación y configuración no tome mucho tiempo, solamente ejecute el siguiente comando:

{% code overflow="wrap" %}
```bash
curl https://sliver.sh/install | sudo bash
```
{% endcode %}

Sin embargo, es entendible no querer ejecutar código de 3ros con máximos privilegios, en lo personal por mas que se confié en los creadores, es recomendable hacer la instalación manual.

## Instalación de binarios release

La descarga de los binarios compilados se realiza desde su repositorio github en la sección de [<mark style="color:green;">**releases**</mark>](https://github.com/BishopFox/sliver/releases). Existen dos binarios, sliver-client y sliver-server, seleccione la arquitectura deseada para su descarga, o ejecute el siguiente comando para visualizar la versión mas resiente para su sistema:

{% code overflow="wrap" %}
```bash
curl -s https://api.github.com/repos/BishopFox/sliver/releases/latest \
    | jq -r '.assets | .[] | .browser_download_url' \
    | grep -E '(sliver-server_linux|sliver-client_linux)$'
```
{% endcode %}

Esto generará un output como el siguiente (tenga en cuenta que las versiones pueden ser diferentes a las de este blog):

<pre data-overflow="wrap"><code>https://github.com/BishopFox/sliver/releases/download/v1.5.41/sliver-client_linux
<strong>https://github.com/BishopFox/sliver/releases/download/v1.5.41/sliver-server_linux
</strong></code></pre>

Después, para descargar los binarios, se puede mediante la interfaz web o con los siguientes comandos:

{% code title="sliver-server" overflow="wrap" %}
```bash
wget -O /usr/local/bin/sliver-server \
https://github.com/BishopFox/sliver/releases/download/v1.5.41/sliver-server_linux && \
chmod 755 /usr/local/bin/sliver-server
```
{% endcode %}

{% code title="sliver-client" overflow="wrap" %}
```bash
wget -O /usr/local/bin/sliver \
https://github.com/BishopFox/sliver/releases/download/v1.5.41/sliver-client_linux && \
chmod 755 /usr/local/bin/sliver
```
{% endcode %}

Estos comandos asumen que se tiene la ubicación `/usr/local/bin/` en el path. A continuación se tiene que ejecutar el siguiente comando con el servidor para extraer todos los assets necesarios, este comando creará una carpeta con archivo en el archivo `~/.sliver` y lo más importante es que generará una versión personalizada de go dedicada a Sliver en la carpeta `~/.sliver/go`.

{% code overflow="wrap" %}
```bash
sliver-server unpack --force
```
{% endcode %}

Finalmente, yo recomiendo la creación de un servicio en systemd para sliver server.

{% code overflow="wrap" %}
```bash
sudo touch /etc/systemd/system/sliver.service && sudo chmod 600 /etc/systemd/system/sliver.service
```
{% endcode %}

{% code title="/etc/systemd/system/sliver.service" overflow="wrap" %}
```bash
[Unit]
Description=Sliver
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=on-failure
RestartSec=3
User=root
ExecStart=/usr/local/bin/sliver-server daemon

[Install]
WantedBy=multi-user.target
```
{% endcode %}

Este archivo permite iniciar el servicio `systemctl start sliver` el cual inicia sliver-server en modio demonio con escucha en el puerto 31337 para la conexión a los operadores.&#x20;

{% code overflow="wrap" %}
```
┌──(root㉿kali)-[~]
└─# netstat -antop | grep 31337
tcp6    0   0 :::31337    :::*    LISTEN    4706/sliver-server   off (0.00/0/0)
```
{% endcode %}

### Creacción Operadores

Los operadores se autentican en el servidor mediante TLS mutuo (mTLS). Los certificados de cliente pueden ser emitidos por el servidor utilizando el comando `operador`. A continuación, se creará un nuevo operador llamado `hacker1`, que se conecta al servidor directamente desde localhost:

{% code overflow="wrap" %}
```bash
sudo sliver-server operator --name kali --lhost localhost --save /tmp
```
{% endcode %}

Esto creará un archivo de configuración json con los certificados necesarios para la conección:

{% code overflow="wrap" %}
```bash
┌──(root㉿kali)-[~]
└─# cat /tmp/kali_localhost.cfg  | jq
{
  "operator": "kali",
  "token": "4700ca860f06ed4e47ad50fc5a58ff22280272ccac437614667520ee31142c28",
  "lhost": "localhost",
  "lport": 31337,
  "ca_certificate": "-----BEGIN CERTIFICATE-----\n...\n-----END CERTIFICATE-----\n",
  "private_key": "-----BEGIN EC PRIVATE KEY-----\n...\n-----END EC PRIVATE KEY-----\n",
  "certificate": "-----BEGIN CERTIFICATE-----\n...-----END CERTIFICATE-----\n"
}
```
{% endcode %}

Este archivo debe esta en la carpeta `~/.sliver-client/configs`:

{% code overflow="wrap" %}
```bash
mkdir -p /home/kali/.sliver-client/configs && mv /tmp/kali_localhost.cfg /home/kali/.sliver-client/configs/ && chown -R kali:kali /home/kali/.sliver-client/ && chmod 600 /home/kali/.sliver-client/configs/kali_localhost.cfg
```
{% endcode %}

Si todo se configuró correctamente solo se tiene que ejecutar el comando `sliver` o `sliver-client` y se tendrá una shell como esta:

{% code overflow="wrap" %}
```bash
┌──(kali㉿kali)-[~]
└─$ sliver
Connecting to localhost:31337 ...

    ███████╗██╗     ██╗██╗   ██╗███████╗██████╗
    ██╔════╝██║     ██║██║   ██║██╔════╝██╔══██╗
    ███████╗██║     ██║██║   ██║█████╗  ██████╔╝
    ╚════██║██║     ██║╚██╗ ██╔╝██╔══╝  ██╔══██╗
    ███████║███████╗██║ ╚████╔╝ ███████╗██║  ██║
    ╚══════╝╚══════╝╚═╝  ╚═══╝  ╚══════╝╚═╝  ╚═╝

All hackers gain indestructible
[*] Server v1.5.41 - kali
[*] Welcome to the sliver shell, please type 'help' for options

[server] sliver > version

[*] Client 1.5.41 - kali - linux/amd64
    Compiled at 2023-08-29 04:38:25 -0600 CST
    Compiled with go version go1.21.0 linux/amd64


[*] Server v1.5.41 - kali - linux/amd64
    Compiled at 2023-08-29 04:38:25 -0600 CST

```
{% endcode %}

## Compilar el código fuente

Compilar sliver desde el código fuente es fácil. Sólo tienes que seguir los pasos descritos en su [<mark style="color:green;">**wiki**</mark>](https://github.com/BishopFox/sliver/wiki/Compile-From-Source).&#x20;

Para compilar desde el código fuente, necesitas tener Go instalado. Instale los paquetes con `apt-get install -y golang`. A continuación, obtener el código fuente de GitHub. Clone el repositorio en cualquier lugar de tu máquina con:

```bash
git clone https://github.com/BishopFox/sliver && cd sliver 
```

Ahora, se tiene que ejecutar el script `go-assets.sh` para descargar todas las utilidades que ocupa sliver, como es el ofuscador de binarios de golang [<mark style="color:green;">**garble**</mark>](https://github.com/burrowers/garble).&#x20;

{% code overflow="wrap" %}
```bash
sudo bash ./go-assets.sh
```
{% endcode %}

Finalmente, se ejecuta el comando `make`, el cual empezará a compilar los binarios y los dejará en la carpeta raíz de proyecto de git, antes de compilar se puede realiza modificaciones al código fuente. Tome en cuenta que puede compilar los binarios para diferentes plataformas (Windows, Linux, Max).

{% code overflow="wrap" %}
```bash
┌──(root㉿kali)-[~/github/sliver]
└─# make        
...

┌──(root㉿kali)-[~/github/sliver]
└─# file sliver-server 
sliver-server: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=3c42b0bbc6d3077929e87f3c1c170d680eb6f8b2, for GNU/Linux 3.2.0, stripped
                                                                                                                                                                                                                    
┌──(root㉿kali)-[~/github/sliver]
└─# file sliver-client 
sliver-client: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=7add139c2d119c72b533778f08897ed575b3f15e, for GNU/Linux 3.2.0, stripped
```
{% endcode %}

{% hint style="info" %}
Asegúrese de seguir después de la compilación los pasos descrito en [<mark style="color:green;">**Instalación de binarios**</mark>](instalacion.md#instalacion-de-binarios-release) descrito previamente en este blog.
{% endhint %}

