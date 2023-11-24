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

### Instalación desde script

Sliver provee un [<mark style="color:green;">**script de instalación**</mark>](https://github.com/BishopFox/sliver/blob/master/docs/install) el cual hacer que la configuración e instalación no tome mucho tiempo, solamente ejecute el siguiente comando:

{% code overflow="wrap" %}
```bash
curl https://sliver.sh/install | sudo bash
```
{% endcode %}

Sin embargo, es entendible no querer ejecutar código de 3ros con máximos privilegios, en lo personal por mas que se confié en los creadores, es recomendable hacer la instalación manual.

### Instalación de binarios release

La descarga de los binarios compilados se realiza desde su repositorio github en la sección de [<mark style="color:green;">**releases**</mark>](https://github.com/BishopFox/sliver/releases). Existen dos binarios, sliver-client y sliver-server, seleccione la arquitectura deseada para su descarga, o ejecute el siguiente comando para visualizar la versión mas resiente para su sistema:

{% code overflow="wrap" %}
```bash
curl -s https://api.github.com/repos/BishopFox/sliver/releases/latest \
    | jq -r '.assets | .[] | .browser_download_url' \
    | grep -E '(sliver-server_linux|sliver-client_linux)$'
```
{% endcode %}

Esto generará un output como el siguiente (tenga en cuenta que las versiones pueden ser diferentes a las de este blog):

{% code overflow="wrap" %}
```
https://github.com/BishopFox/sliver/releases/download/v1.5.41/sliver-client_linux
https://github.com/BishopFox/sliver/releases/download/v1.5.41/sliver-server_linux
```
{% endcode %}

