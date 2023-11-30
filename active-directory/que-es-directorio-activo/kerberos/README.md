# Kerberos

## ¿Qúe es Kerberos?

Kerberos es un protocolo de autenticación, no de autorización. En otras palabras, permite identificar a cada usuario, que proporciona una contraseña secreta, pero **no valida a qué recursos o servicios puede acceder dicho usuario**.

### Elementos de kerberos

#### **Capa de transporte**

Kerberos utiliza UDP o TCP como protocolo de transporte, que envía datos en texto claro. Debido a esto Kerberos es responsable de proporcionar encriptación. Los puertos utilizados por Kerberos son UDP/88 y TCP/88, que deben escucharse en el KDC (se explica en la siguiente sección).

#### **Agentes**

Varios agentes trabajan juntos para proporcionar autenticación en Kerberos. Estos son los siguientes:

* **Client or user** who wants to access to the service.
* **AP** (Application Server) which offers the service required by the user.
* **KDC** (Key Distribution Center), the main service of Kerberos, responsible of issuing the tickets, installed on the DC (Domain Controller). It is supported by the **AS** (Authentication Service), which issues the TGTs.

#### **Encryption keys**

Varias estructuras manejadas por Kerberos, como los tickets, se transmiten cifradas o firmadas. Esto evita que sean manipuladas por terceros. Las claves de cifrado utilizados por Kerberos, en Active Directory, son las siguientes:

* **Clave del KDC o krbtgt**: clave derivada del hash NTLM de la cuenta [krbtgt](https://www.tarlogic.com/es/glosario-ciberseguridad/krbtgt/).
* **Clave de usuario**: clave derivada del hash NTLM del propio usuario.
* **Clave de servicio**: clave derivada del hash NTLM del propietario del servicio, que puede ser una cuenta de usuario o del servidor.
* **Clave de sesión**: clave negociada por el cliente y el KDC.
* **Clave de sesión de servicio**: clave negociada para utilizar entre el cliente y el AP.

#### **Tickets**

Kerberos maneja unas estructuras llamadas “Tickets”, que son entregados a los usuarios autenticados para que estos puedan realizar ciertas acciones dentro del dominio de Kerberos. Se distinguen 2 tipos:

* El **TGS** (Ticket Granting Service) es el ticket que se presenta ante un servicio para poder acceder a sus recursos. Se cifra con la clave del servicio correspondiente.
* El **TGT** (Ticket Granting Ticket) es el ticket que se presenta ante el KDC para obtener los TGS. Se cifra con la clave del KDC.

#### **PAC**

El **PAC** (Privilege Attribute Certificate) es una estructura incluida en la mayoría los tickets. Esta estructura contiene los privilegios del usuario y está firmada con la clave del KDC.

Es posible para los servicios verificar el PAC comunicándose con el KDC, aunque esto no es común. No obstante, la verificación del PAC solo consiste en comprobar su firma, sin comprobar si los privilegios son correctos.

Por otra parte, un cliente puede evitar que se incluya el PAC especificándolo en el campo KERB-PA-PAC-REQUEST de la petición del ticket.

#### **Mensajes**

El protocolo Kerberos permite la comunicación de los diferentes agentes a través de diferentes tipos de mensajes. A continuación se enumeran los más interesantes:

* **KRB\_AS\_REQ**: Utilizado por el usuario para solicitar el TGT al KDC.
* **KRB\_AS\_REP**: Respuesta del KDC para enviar el TGT al usuario.
* **KRB\_TGS\_REQ**: Utilizado por el usuario para solicitar el TGS al KDC, utilizando el TGT.
* **KRB\_TGS\_REP**: Respuesta del KDC para enviar el TGS solicitado al usuario.
* **KRB\_AP\_REQ**: Utilizado por el usuario para identificarse contra el servicio deseado, utilizando el TGS del propio servicio.
* **KRB\_AP\_REP**: (Opcional) Utilizado por el servicio para autenticarse frente al usuario.
* **KRB\_ERROR**: Utilizado por los diferentes agentes para notificar situaciones de error.

Además, aunque no es parte de Kerberos, sino de NRPC, el AP puede opcionalmente utilizar el mensaje **KERB\_VERIFY\_PAC\_REQUEST** para enviar la firma del PAC al KDC, y verificar si ésta es correcta.

{% embed url="https://www.tarlogic.com/es/blog/como-funciona-kerberos/" %}

{% embed url="https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/kerberos-authentication" %}

