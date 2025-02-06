# Mimikats

## Qué es Mimikatz

Mimikatz es una herramienta de seguridad de código abierto creada por Benjamin Delpy (gentilkiwi). Es ampliamente conocida y utilizada en la comunidad de seguridad, especialmente en las actividades de Red Teams y en evaluaciones de seguridad para sistemas Windows. Su propósito principal es investigar y explotar varias vulnerabilidades de seguridad en Windows, aunque es más famosa por su capacidad para extraer contraseñas de texto plano, hashes NTLM, y kerberos tickets de la memoria de un sistema. También puede realizar passthe-hash, passthe-ticket, y crear tickets de golden/silver kerberos, lo cual es particularmente útil para movimientos laterales en entornos de Active Directory (AD).



{% embed url="https://github.com/gentilkiwi/mimikatz" %}

{% embed url="https://gitlab.com/kalilinux/packages/mimikatz/-/tree/kali/master/x64?ref_type=heads" %}

### Uso en Red Team contra AD <a href="#uso-en-red-team-contra-a-d" id="uso-en-red-team-contra-a-d"></a>

En operaciones de Red Team, especialmente en entornos que usan Active Directory, Mimikatz se ha convertido en una herramienta esencial debido a su habilidad para:

* Extraer credenciales de usuarios que han iniciado sesión en una máquina.
* Manipular el proceso de autenticación y autorización de Windows para obtener acceso a recursos adicionales.
* Facilitar la movilidad lateral y la elevación de privilegios dentro de una red.

### Pros y Contras de ejecutar Mimikatz con mimikatz.exe <a href="#pros-y-contras-de-ejecutar-mimikatz-con-mimikatz.exe" id="pros-y-contras-de-ejecutar-mimikatz-con-mimikatz.exe"></a>

#### <mark style="color:green;">**Pros**</mark> <a href="#pros" id="pros"></a>

* **Funcionalidad Completa**: La versión ejecutable suele tener todas las funciones de Mimikatz disponibles y está actualizada con las últimas características.
* **Rapidez**: Al ejecutar el binario directamente, se suele tener una ejecución más rápida y directa de las funciones deseadas.

#### <mark style="color:green;">**Contras**</mark> <a href="#contras" id="contras"></a>

* **Detección**: Es más probable que los antivirus y las herramientas de seguridad modernas detecten la versión ejecutable debido a sus firmas conocidas.
* **Necesidad de Escritura en el Disco**: A menos que se cargue en la memoria de manera especial, es probable que requiera ser escrito en el disco para su ejecución, lo que aumenta el riesgo de detección y deja evidencia forense.
* **Bloqueo por Políticas de Seguridad**: Políticas de grupo y configuraciones de seguridad de Windows pueden impedir la ejecución de binarios desconocidos o no firmados.

## LSASS

El servicio <mark style="color:green;">Local Security Authority Subsystem Service (LSASS)</mark> en sistemas operativos Windows es crucial para la administración de la política de seguridad y maneja la autenticación de usuarios en el sistema local. Para un pentester, el entendimiento profundo de LSASS y cómo interactúa con los componentes de seguridad de Windows es fundamental para la evaluación de la postura de seguridad de un sistema y la ejecución de ciertas formas de ataques. Aquí hay varios aspectos de LSASS que son de especial interés:

<figure><img src="https://books.spartan-cybersec.com/~gitbook/image?url=https%3A%2F%2F1580805812-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FzDuZE3U19tNNqSAmcpyE%252Fuploads%252FAsVdOj2PJB7dm5TAObgZ%252Fimage.png%3Falt%3Dmedia%26token%3D9a8d2e33-e3b0-4748-9930-9290bc658d22&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=1bbd7dbe&#x26;sv=2" alt=""><figcaption></figcaption></figure>

### Función y Rol de LSASS <a href="#funcion-y-rol-de-lsass" id="funcion-y-rol-de-lsass"></a>

* **Autenticación**: LSASS es responsable de la autenticación de usuarios para las sesiones de inicio de sesión en una computadora.
* **Creación de Tokens**: Cuando se autentica a un usuario, LSASS genera un "token de acceso" que contiene los derechos y privilegios del usuario.
* **Almacenamiento de Credenciales**: En el proceso de LSASS, las credenciales, como hashes de contraseñas y tickets Kerberos, son almacenados en la memoria.

### Ataques y Tácticas Asociadas con LSASS <a href="#ataques-y-tacticas-asociadas-con-lsass" id="ataques-y-tacticas-asociadas-con-lsass"></a>

* **Dumping de Credenciales**: Las herramientas de extracción de credenciales como Mimikatz explotan el acceso a LSASS para obtener hashes de contraseñas y tickets de autenticación.
* **Pass-the-Hash / Pass-the-Ticket**: Las credenciales extraídas pueden ser usadas para realizar estos ataques, permitiendo a los atacantes autenticarse como otro usuario sin conocer la contraseña en texto claro.
* **Inyección de Código**: Los atacantes pueden intentar inyectar código malicioso en el proceso LSASS para interceptar credenciales o elevar privilegios.

### Técnicas de Protección y Evasión <a href="#tecnicas-de-proteccion-y-evasion" id="tecnicas-de-proteccion-y-evasion"></a>

* **Protección de Memoria**: Los pentesters deben estar al tanto de las características como Credential Guard que protegen la memoria de LSASS.
* **Detección de Anomalías**: La manipulación de LSASS puede disparar alertas en soluciones de detección de intrusiones y en los sistemas de protección contra malware.
* **Técnicas de Evasión**: Para evitar la detección, los pentesters pueden necesitar usar técnicas de evasión o desarrollar nuevas herramientas que no estén firmadas por firmas de AV.
