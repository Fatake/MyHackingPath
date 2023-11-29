# Sliver C2

En estas entradas del blog se describirán el uso y utilidades del framework de comando y control [<mark style="color:green;">**Sliver**</mark> ](https://github.com/BishopFox/sliver)creado por la gente [<mark style="color:green;">**BishopFox**</mark>](https://bishopfox.com/tools/sliver).

## ¿Que es un C2 Comando y control?

Se recomienda la lectura de C2 Comando y Control en las entradas de este blog:

{% content-ref url="../" %}
[..](../)
{% endcontent-ref %}

## ¿Qué es Sliver?

Como lo describe su wiki en github&#x20;

> Sliver is a Command and Control (C2) system made for penetration testers, red teams, and blue teams. It generates implants that can run on virtually every architecture out there, and securely manage these connections through a central server. Sliver supports multiple callback protocols including DNS, Mutual TLS (mTLS), WireGuard, and HTTP(S) to make egress simple, even when those pesky blue teams block your domains. You can even have multiple operators (players) simultaneously commanding your sliver army.

Sliver framework es un sistema de Comando y Control (C2) creado para pentesters, personal de red team o blue team. Genera implantes que pueden ejecutarse en prácticamente cualquier arquitectura existente, y gestiona de forma segura estas conexiones a través de un servidor central. Sliver soporta múltiples protocolos de callback incluyendo: DNS, Mutual TLS (mTLS), WireGuard, HTTP(S) los cuales permiten la salida de comunicación ante ambientes controlados, incluso cuando esos molestos blue teams bloquean sus dominios. Permite tener múltiples operadores (jugadores) comandando simultáneamente dentro de sliver server.

<figure><img src="https://user-images.githubusercontent.com/43555923/59543161-56a38c00-8ebe-11e9-8eb2-ecea457976d1.png" alt=""><figcaption></figcaption></figure>

Los frameworks como Sliver no dan acceso a sistemas que no se tenga ya el acceso, es una herramienta de post explotación. Existen muchas tecnicas para obtener el acceso inicial a los sistemas, las mas comunes son explotando vulnerabilidades de software utilizar otras herramientas como [Metasploit](https://www.metasploit.com/), otro método puede ser por ingeniería social para pedirle al usuario finar que ejecuta inadvertidamente un payload de C2 y ganar acceso su equipo.

Independientemente del mpetodo de acceso inicial, los pasos posteriores dados por los atacantes se suelen denominar "post-explotación". Sliver es una herramienta que facilita magenar las sesiones remotas y seguir con las actividades de post explotación

En esta guía y en el código base de Sliver se utilizan los siguientes términos. Estas definiciones pueden variar ligeramente según el sector:

* <mark style="color:green;">**Implant - Implante**</mark> Término genérico para una pieza de software utilizada para mantener el acceso a un entorno o sistema, generalmente mediante el uso de mando y control (C\&C, C2, etc.), es el código que el ataque ejecuta en la máquina objetivo además de mantener el acceso. El término "implante" es a menudo intercambiable con "<mark style="color:green;">agente</mark>".
* <mark style="color:green;">**Beacon**</mark> - Puede referirse a dos cosas:
  * (1) un patrón de comunicación en el que un implante se conecta <mark style="color:green;">periódicamente</mark> al servidor C2 en lugar de utilizar una conexión de estado/tiempo real&#x20;
  * (2) el implante principal de [<mark style="color:green;">Cobalt Strike</mark>](https://www.cobaltstrike.com/), más a menudo llamado "CS Beacon".
  * En el contexto de Sliver, "Beacon" se refiere específicamente a un implante Sliver que implementa la definición (1) estilo de comunicación.
* <mark style="color:green;">**Stage - Cargador**</mark> - Un "stage" o "staged payload" es un método de cargar un fragmento de código, normalmente a través de una red, en una máquina remota. Tradicionalmente, el staging (Lanzador / cargador) se utilizaba junto con exploits que tenían limitaciones de tamaño. Normalmente, el objetivo es ejecutar un pequeño fragmento de código, es decir, el "stager", que a su vez carga un fragmento de código mayor. Los cargadores de payload o Staged payloads por etapas se siguen utilizando hoy en día de esta forma, aunque también suelen ser útiles para <mark style="color:green;">eludir la detección antivirus</mark>, ya que la mayor parte del código malicioso se carga directamente en la memoria. Las etapas a veces están numeradas, por ejemplo, una "stage 0" cargará una "stage 1" que carga una carga útil de "stage 2".

