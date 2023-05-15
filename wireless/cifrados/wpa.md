# WPA

## ¿Qué es WPA?

Wi-Fi Protected Access (WPA) es un protocolo de seguridad definido por la norma 802.11i. En el pasado, el principal mecanismo de seguridad utilizado entre los puntos de acceso inalámbricos y los clientes inalámbricos era el cifrado WEP, que tiene el gran inconveniente de que utiliza una clave de cifrado estática. Un atacante puede aprovecharse de este punto débil utilizando herramientas disponibles gratuitamente en Internet. El IEEE define WPA como "una ampliación de los protocolos 802.11 que puede permitir una mayor seguridad". Casi todos los fabricantes de Wi-Fi ofrecen WPA.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

WPA ofrece mayor seguridad de cifrado de datos que WEP porque los mensajes pasan a través de una comprobación de integridad de mensajes (MIC) mediante el Protocolo de Integridad de Clave Temporal (TKIP), que utiliza el cifrado de flujo RC4 con claves de 128 bits y MIC de 64 bits para proporcionar un cifrado y una autenticación sólidos. WPA es un ejemplo de cómo 802.11i proporciona un cifrado más potente y permite la autenticación mediante clave precompartida (PSK) o EAP. WPA utiliza TKIP para el cifrado de datos, que elimina los puntos débiles de WEP al incluir funciones de mezcla por paquete, MICs, IVs extendidos y mecanismos de re-cifrado.
