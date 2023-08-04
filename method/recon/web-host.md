# Web / Host

## Host Discovery

### ARP Ping Scan

En el escaneo ARP ping, los paquetes ARP se envían para descubrir todos los dispositivos activos en el rango IPv4 aunque la presencia de tales dispositivos esté oculta por cortafuegos restrictivos.

```bash
nmap -n -sn -PR --packet-trace --send-eth 192.168.33.37
nmap -sP -PR 192.168.0.*
nmap -sn -PR 192.168.0.*
nmap -sn --send-ip 192.168.0.*
```

```bash
nping --arp-type ARP 192.168.0.1
```

### UDP Ping Scan&#x20;

Los atacantes envían paquetes UDP al host objetivo, y una respuesta UDP significa que el host objetivo está activo. Si el host objetivo está fuera de línea o inalcanzable, pueden aparecer varios mensajes de error como host/red inalcanzable o TTL excedido.

```bash
nmap -sn -PU 10.10.1.11
```

### ICMP Echo Ping Scan

Los atacantes utilizan el escaneo ICMP ping para enviar paquetes ICMP al sistema de destino para recopilar toda la información necesaria sobre él. Esto se debe a que ICMP no incluye abstracción de puertos, y es diferente del escaneo de puertos. Sin embargo, es útil para determinar qué hosts de una red se están ejecutando haciendo ping a todos ellos. El escaneo ping ICMP ECHO consiste en enviar peticiones ICMP ECHO a un host. Si el host está vivo, devolverá una respuesta ICMP ECHO. Este escaneo es útil para localizar dispositivos activos o determinar si ICMP está pasando a través de un cortafuegos. Normalmente esta técnica no funciona en redes basadas en Windows, ya que su implementación de pila TCP/IP no responde a las sondas ICMP dirigidas a la dirección de difusión.

```bash
nmap -sn -PE 10.10.1.1
```

### Barrido o ICMP Echo Ping Sweep

Un barrido ping (también conocido como ICMP sweep) es una técnica básica de escaneo de red que se adopta para determinar el rango de direcciones IP que mapean a hosts (ordenadores) vivos. Aunque un solo ping le dirá al usuario si un ordenador host especificado existe en la red, un barrido ping consiste en peticiones ICMP ECHO enviadas a múltiples hosts. Si un host especificado está activo, devolverá una respuesta ICMP ECHO. Los barridos ping están entre los métodos más antiguos y lentos utilizados para escanear una red. Esta utilidad se distribuye a través de casi todas las plataformas, y actúa como un pase de lista para los sistemas; un sistema que está activo en la red responde a la consulta ping que otro sistema envía.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

```bash
nmap -sn -PE 10.10.1.0/24
fping -g 192.168.0.1/24
```

### ICMP Ping Scan Basado en tiempo

Además del tradicional ping ICMP ECHO, existen otros tipos de técnicas de ping ICMP, como el ping ICMP timestamp y el ping ICMP address mask, que un atacante puede adoptar en condiciones específicas. El ping ICMP timestamp es un tipo opcional y adicional de ping ICMP mediante el cual los atacantes consultan un mensaje timestamp para adquirir la información relacionada con la hora actual de la máquina host objetivo. La máquina de destino responde con una respuesta de marca de tiempo a cada consulta de marca de tiempo que se recibe. Sin embargo, la respuesta del host de destino es condicional, y puede o no responder con el valor de la hora dependiendo de su configuración por el administrador en el extremo del objetivo. Este ping ICMP timestamp se utiliza generalmente para la sincronización horaria. Este método de ping es efectivo para identificar si la máquina host de destino está activa, específicamente en la condición en la que el administrador bloquea las peticiones de ping ICMP ECHO tradicionales.&#x20;

```bash
nmap -sn -PP 10.10.1.0/24
```

### ICMP Address Mask Ping Scan

El ping de máscara de dirección ICMP es otra alternativa al ping ICMP ECHO tradicional, en el que los atacantes envían una consulta de máscara de dirección ICMP al host de destino para obtener información relacionada con la máscara de subred. Sin embargo, la respuesta de máscara de dirección del host de destino es condicional, y puede o no responder con el valor de subred apropiado dependiendo de su configuración por el administrador en el extremo del objetivo. Este tipo de método de ping también es efectivo para identificar los hosts activos de forma similar al ping ICMP timestamp, específicamente cuando el administrador bloquea el ping ICMP Echo tradicional.&#x20;

```bash
nmap -sn -PM 10.10.1.0/24
```

### TCP SYN Ping Scan

TCP SYN ping es una técnica de descubrimiento de host para sondear diferentes puertos con el fin de determinar si el puerto está en línea y comprobar si encuentra algún conjunto de reglas de cortafuegos. En este tipo de técnica de descubrimiento de host, un atacante utiliza la herramienta Nmap para iniciar el handshake de tres vías enviando la bandera vacía TCP SYN al host objetivo. Tras recibir SYN, el host objetivo acusa recibo con una bandera ACK. Tras la recepción de la bandera ACK, el atacante confirma que el host de destino está activo y termina la conexión enviando una bandera RST a la máquina host de destino (ya que su objetivo de descubrimiento de host está cumplido). El puerto 80 se utiliza como puerto de destino por defecto. También se puede especificar un rango de puertos en este tipo de formato de ping sin insertar un espacio entre -PS y el número de puerto (por ejemplo, PS22-25,80,113,1050,35000), donde la sonda se realizará contra cada puerto paralelamente.&#x20;

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Ventajas de este tipo de escaneos:

* Como las máquinas pueden ser escaneadas paralelamente, el escaneo nunca obtiene el error de tiempo de espera mientras espera la respuesta.
* El ping TCP SYN puede utilizarse para determinar si el host está activo sin crear ninguna conexión. Por lo tanto, los registros no se registran a nivel de sistema o de red, lo que permite al atacante no dejar rastros para la detección

{% code overflow="wrap" %}
```bash
nmap -sn -PS 80,443,22,3389,1723,8080,3306,135,53,143,139,445,110,25,21,23,5432,27017,1521 10.10.1.0/25
```
{% endcode %}

### TCP ACK Ping Scan

El ping TCP ACK es similar al ping TCP SYN, aunque con pequeñas variaciones. En la técnica de ping TCP ACK, los atacantes envían directamente un paquete TCP ACK vacío al host objetivo. Dado que no existe una conexión previa entre el atacante y el host de destino, tras recibir el paquete ACK, el host de destino responde con una bandera RST para finalizar la solicitud. La recepción de este paquete RST en el extremo del atacante indica que el host está activo.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Tanto el paquete SYN como el ACK pueden utilizarse para maximizar las posibilidades de eludir el cortafuegos. Sin embargo, los cortafuegos suelen estar configurados para bloquear los paquetes de ping SYN, ya que son la técnica de ping más común. En tales casos, la sonda ACK puede utilizarse eficazmente para eludir fácilmente estos conjuntos de reglas de cortafuegos.

{% code overflow="wrap" %}
```bash
nmap -sn -PA 80,443,22,3389,1723,8080,3306,135,53,143,139,445,110,25,21,23,5432,27017,1521 10.10.1.0/25
```
{% endcode %}

### IP Protocol Ping Scan

El ping de protocolo IP es la última opción de descubrimiento de host que envía paquetes ping IP con la cabecera IP de cualquier número de protocolo especificado. Tiene el mismo formato que el ping TCP y UDP. Esta técnica intenta enviar diferentes paquetes utilizando diferentes protocolos IP, esperando obtener una respuesta que indique que un host está en línea.

En pocas palabras, los atacantes envían diferentes paquetes de sonda de diferentes protocolos IP al host objetivo; cualquier respuesta de cualquier sonda indica que el host está en línea.

```bash
nmap -sn -PO 10.10.1.1
nmap -sn -PO <list,protocol> <target>
```



## Validar el Objetivo

Tools:

* WHOIS
* nslookup
* dnsrecon
* dig

## Recolección de información variable

Tools

* Google dorks
* Shodan
* censys
* zoomeye

## Buscar Subdominios

Tools

* Google FU
* dig
* nmap
* sublist3r
* bluto
* amass
* crt.sh

## Fingerprinting

tools

* nmap
* wappalyzar
* WhatWeb
* BuiltWith
* Netcat

## Data Breaches

tools

* HaveIBeenPwned (para correos encontrados)
* Breach-Parse
* WeLeakInfo
* hunter.io (encontrar correos)
