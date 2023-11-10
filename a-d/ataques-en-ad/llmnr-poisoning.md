# LLMNR Poisoning



## ¿Qué es LLMNR?

<mark style="color:green;">**LLMNR (Link-Local Multicast Name Resolution)**</mark>, es un protocolo de resolución de nombres de multidifusión de vínculo local que resuelve los nombres de los sistemas informáticos cercanos, si la red no tiene un servidor de Sistema de nombres de dominio (DNS). A veces también se hace referencia a este protocolo como **NBT-NS**.

Normalmente entra en acción cuando el DNS falla..

Vector de ataque: Cuandos los servicios utilizan el nombre de usuario y el hash NTLMv2 de un usuario cuando responden adecuadamente podemos obtener el hash de usuarios y utilizar la técnica de pass the hash para robar la sesión de los usuarios.

