---
description: Command and control
---

# C2 Comando y control

## ¿Qué es un Command and Control?

Un Command and Control, también conocido por las siglas C\&C o C2, es un tipo de infraestructura utilizado para controlar un cliente desde un servidor central.

Desde este servidor es posible comunicarse con los distintos clientes para actuar sobre los mismos, ya sea de forma manual o mediante el uso de scripts o funcionalidades que permitan la automatización de tareas.

Estos servidores de C2 pueden utilizarse para crear poderosas redes de dispositivos infectados capaces de llevar a cabo ataques distribuidos de denegación de servicio (DDOS), robo de datos, cifrado, etc.

El Comando y control se define como una técnica utilizada tanto como red teamers y blue teams como agentes de amenaza para comunicarse con dispositivos comprometidos a través de una red.

El C2 suele implicar uno o más canales encubiertos, pero dependiendo del ataque, los mecanismos específicos pueden variar enormemente. Los atacantes utilizan estos canales de comunicación para entregar instrucciones al dispositivo comprometido para descargar malware adicional, crear botnets o exfiltrar datos.

Según el marco ATT\&CK de MITRE, existen más de 16 tácticas diferentes de mando y control utilizadas por los adversarios, incluidas numerosas subtécnicas:

1. Application Layer Protocol
2. Communication Through Removable Media
3. Data Encoding
4. Data Obfuscation
5. Dynamic Resolution
6. Encrypted Channel
7. Fallback Channels
8. Ingress Tool Transfer
9. Multi-Stage Channels
10. Non-Application Layer Protocol
11. Non-Standard Port
12. Protocol Tunneling
13. Proxy
14. Remote Access Software
15. Traffic Signaling
16. Web Service

### C2 Matrix

{% embed url="https://www.thec2matrix.com/" %}

## Referencias

{% embed url="https://www.paloaltonetworks.com/cyberpedia/command-and-control-explained" %}

{% embed url="https://attack.mitre.org/tactics/TA0011/" %}
