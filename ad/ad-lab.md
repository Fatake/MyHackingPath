# AD Lab

## Componentes

* 1 Windows Server 2019 - 2gb ram - 60gb rom
* 2 Windows 10 Enterprise - 2gb ram - 60gb rom
* 1 Kali - 4gb ram - 60gb rom



## Instalación AD DC

### Instalación Sistema Operativo

Descargar <mark style="color:green;">**Window Server 2019 Evaluation**</mark> de su página oficial.

{% embed url="https://www.microsoft.com/es-mx/evalcenter/download-windows-server-2019" %}

En VMware crear una máquina virtual e in iniciar el proceso de instalación

<figure><img src="../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

Dar click en instalar y elegir <mark style="color:green;">**Windows Server 2019 Evaluation Desktop Experience**</mark>

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Aceptar los términos y condiciones, después elegir <mark style="color:green;">**Custom: Install Windows only (advanced)**</mark>

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Seleccionar el disco de 60gb y dar clic en <mark style="color:green;">**New**</mark> -> <mark style="color:green;">**Apply**</mark> y luego en <mark style="color:green;">**Next**</mark> y finalmente esperar a la instalación de sistema operativo

<figure><img src="../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

### Configuración del DC

Al finalizar la instalación del sistema operativo, se te pedirá colocar una contraseña

<figure><img src="../.gitbook/assets/image (7).png" alt="" width="541"><figcaption></figcaption></figure>

En VMware dar click en VM -> Install VMware tools y ejecutar en Windows Server el Setup64

<figure><img src="../.gitbook/assets/image (6) (3).png" alt=""><figcaption></figcaption></figure>

En el buscador, escribir <mark style="color:green;">**computer**</mark> y dar click en <mark style="color:green;">**View Your PC name**</mark>

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Luego renombrar la computadora, para este ejemplo se usó el nombre ECorp-DC (puede ser el nombre que sea) después, se reiniciará el servidor.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Después del reinicio, se abrirá la consola **Server Manager**, Dar Click en <mark style="color:green;">**configure this local Server**</mark>, luego en <mark style="color:green;">**Add roles and features**</mark> y luego en <mark style="color:green;">**Next**</mark> 3 veces

<figure><img src="../.gitbook/assets/image (13) (5).png" alt=""><figcaption></figcaption></figure>

Después en Server Roles, seleccionar <mark style="color:green;">**Active Directory Domain Services**</mark> y luego en <mark style="color:green;">**Add Features**</mark> y luego en <mark style="color:green;">**Next**</mark> 3 veces y finalmente en Install

<figure><img src="../.gitbook/assets/image (11) (3).png" alt=""><figcaption></figcaption></figure>

Al finalizar la Intalación, dar click en Close

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Finalmente, en **Server Manager Dashboard** se encuenta un icono de bandera con un símbolo de Warning, dar click en <mark style="color:green;">**Promote this server to a domain controler**</mark>.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Seleccionar la opción <mark style="color:green;">**Add a New Forest**</mark> y escribir un nombre de dominio, para este ejemplo se usará ECORP.local y después dar click en <mark style="color:green;">**Next**</mark>

<figure><img src="../.gitbook/assets/image (12) (5).png" alt=""><figcaption></figcaption></figure>

Después, se tiene que configurar una contraseña para el DSRM y dar click en Next 5 veces y finalmente dar click en Install.

<figure><img src="../.gitbook/assets/image (5) (2).png" alt=""><figcaption></figcaption></figure>

Al finalizar la instalación, el servidor se reiniciará e iniciará ya con el AD configurado.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

