# Funcionamiento de Kerberos

El protocolo Kerberos es un sistema de autenticación de red que utiliza criptografía de clave simétrica para proporcionar autenticación fuerte para las comunicaciones cliente-servidor. La versión más comúnmente utilizada es Kerberos V5, que está diseñada para funcionar en redes inseguras y proporcionar autenticación mutua entre el cliente y el servidor. Veamos los detalles del flujo de autenticación y los términos asociados:

## **Componentes Principales:**

1. **Key Distribution Center (KDC):** Es una instancia de confianza centralizada que provee los servicios AS y TGS.
2. **Authentication Service (AS):** Es el servicio dentro del KDC que autentica a los usuarios y servicios y emite TGTs.
3. **Ticket Granting Service (TGS):** Es otro servicio dentro del KDC que emite tickets de servicio para acceder a recursos después de que el cliente ha sido autenticado inicialmente por el AS.
4. **Ticket Granting Ticket (TGT):** Es un "boleto" que el AS emite a los clientes cuando se autentican con éxito, que a su vez es utilizado para solicitar tickets de servicio del TGS.

### <mark style="color:green;">**Términos Adicionales:**</mark>

* **Principal:** En Kerberos, un principal es una entidad única (usuario o servicio) identificada por su nombre completo en el dominio.
* **Session Key:** Es una clave criptográfica simétrica utilizada para cifrar y descifrar comunicaciones durante una sesión específica.
* **Pre-authentication:** Es un proceso donde el cliente debe demostrar conocimiento de la contraseña antes de que el AS emita un TGT, para prevenir ataques de repetición.
* **Service Ticket:** Es un ticket emitido por el TGS que permite al cliente acceder a un servicio en particular.
* **Realm:** Es un dominio administrativo para Kerberos, generalmente corresponde a un dominio en una red.

## **Flujo de Autenticación:**



1. <mark style="color:green;">**Pre-Autenticación:**</mark>
   * El usuario inicia sesión en su cliente Kerberos proporcionando su identificador de usuario y contraseña.
   * El cliente Kerberos genera una clave de cifrado a partir de la contraseña del usuario y la utiliza para cifrar un timestamp, enviando este paquete al AS como parte de su AS\_REQ.
2. <mark style="color:green;">**AS\_REQ / AS\_REP:**</mark>
   * El AS valida la pre-autenticación y luego crea el TGT y una clave de sesión para el usuario.
   * El TGT está cifrado con la clave secreta del servicio TGS, y solo el TGS puede descifrarlo.
   * El AS envía de vuelta al cliente el TGT y la clave de sesión (AS\_REP), cifrada con la clave generada a partir de la contraseña del usuario.
3. <mark style="color:green;">**TGS\_REQ / TGS\_REP:**</mark>
   * Cuando el cliente necesita acceder a un servicio, cifra una solicitud con la clave de sesión y envía esta solicitud junto con el TGT al TGS.
   * El TGS descifra el TGT, verifica la validez y autenticidad de la solicitud y genera un ticket de servicio para el recurso solicitado, cifrado con la clave del servicio en cuestión.
4. <mark style="color:green;">**Acceso al Servicio:**</mark>
   * El cliente recibe el ticket de servicio y la nueva clave de sesión asociada, y se conecta al servidor del recurso solicitado, enviando el ticket de servicio y una autenticación cifrada con la clave de sesión.
   * El servidor de recursos descifra el ticket de servicio, verifica la autenticidad de la autenticación del cliente y, si todo es correcto, permite el acceso.

## **Ejemplo práctico:**

Supongamos que <mark style="color:red;">**Bop**</mark> quiere acceder a un servidor de archivos en su empresa:

1. <mark style="color:red;">**Bop**</mark> ingresa su nombre de usuario y contraseña en su cliente Kerberos.
2. El cliente solicita un TGT al AS pre-autenticándose con un timestamp cifrado.
3. El AS valida la solicitud, crea un TGT y una clave de sesión, y los envía a <mark style="color:red;">**Bop**</mark> cifrados con su clave de contraseña.
4. <mark style="color:red;">**Bop**</mark> desea acceder al servidor de archivos y envía un TGS\_REQ al TGS, incluyendo el TGT y una solicitud cifrada con su clave de sesión.
5. El TGS valida el TGT, crea un ticket de servicio para el servidor de archivos, y se lo envía a <mark style="color:red;">**Bop**</mark>.
6. <mark style="color:red;">**Bop**</mark> accede al servidor de archivos presentando el ticket de servicio y su autenticación cifrada.
7. El servidor valida el ticket y la autenticación, y permite a <mark style="color:red;">**Bop**</mark> acceder a los archivos.

Este proceso garantiza que solo usuarios autenticados y con los permisos correctos puedan acceder a los recursos protegidos en la red. Además, la autenticación mutua asegura tanto al cliente como al servidor que están comunicándose con las partes correctas.

## S4U (Service for User)

S4U (Service for User) es un conjunto de extensiones Kerberos que incluye S4U2self y S4U2proxy, utilizadas en Active Directory para la delegación de credenciales:

* <mark style="color:red;">**S4U2self**</mark>: Permite a un servicio obtener un ticket Kerberos para un usuario en específico, incluso si el usuario no ha iniciado sesión. Esto es útil para escenarios donde un servicio necesita actuar en nombre de un usuario.
* <mark style="color:yellow;">**S4U2proxy**</mark>: Extiende la funcionalidad de S4U2self, permitiendo a un servicio usar un ticket obtenido a través de S4U2self para solicitar tickets adicionales para acceder a otros servicios en nombre del usuario.

En ataques de Active Directory, estas extensiones pueden ser explotadas para escalar privilegios o moverse lateralmente en una red. Por ejemplo, un atacante con control sobre una cuenta que puede utilizar S4U2proxy podría obtener acceso a servicios adicionales que están fuera de su alcance directo. Estos ataques requieren comprensión detallada de Kerberos y la configuración del Active Directory.
