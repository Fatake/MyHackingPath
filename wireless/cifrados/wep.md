---
description: Wired Equivalent Privacy == WEP
---

# WEP

## ¿Que es WEP?

Es un protocolo de seguridad definido en el estandar 802.11b. Fupe diseñado para proveer a las redes inalambrias LAN Wireless un nivel de seguridad y privacidad comparadas con las redes planas LAN

WEP ocupa 24 bits del vecto de inicialización IV para formar un steam cifrado RC4 y una un checksum CRC-32 para integridad de la transmisión de los datos

<figure><img src="../../.gitbook/assets/image (2) (5) (2).png" alt=""><figcaption></figcaption></figure>

Tiene una vulnerabilidad significante y su diseño tiene defectos que lka hacen facilmente crackeable.

### Defectos

A continuación se da un listado de los defectos de WEP

* No hay un método definido para la distribución de claves de cifrado
  * Las claves pre-compartidas (Pre-shared keys PSK) se establecen una vez en la instalación y rara vez (o nunca) se modifican.
  * es fácil recuperar el número de mensajes en texto plano cifrados con la misma clave
* RC4 se diseñó para utilizarse en un entorno más aleatorio que el utilizado por WEP:&#x20;
  * Como el PSK rara vez se cambia, la misma clave se utiliza repetidamente.&#x20;
  * Un atacante vigila el tráfico y encuentra diferentes formas de trabajar con el mensaje en texto plano.&#x20;
  * Conociendo el texto cifrado y el texto sin formato, un atacante puede calcular la clave.
* Los atacantes analizan el tráfico de las capturas de datos pasivas y descifran las claves WEP con la ayuda de herramientas como AirSnort y WEPCrack.&#x20;
* Los algoritmos de programación de claves también son vulnerables a los ataques.
