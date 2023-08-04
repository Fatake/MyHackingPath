# Cyber Kill Chain

Basada en el concepto militar de igual nombre, esta metodología de cadena es un componente de defensa y ataque ofensivo que conta de 7 pasos (pasos que otras metodologías como la de [MITRE ATT\&CK](mitre-att-and-ck.md) expanden o implementan). El propósito del modelo es comprender mejor las etapas por las que debe pasar un ataque para realizar un ataque, y ayudar a los equipos de seguridad a detener un ataque en cada etapa.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Fases

### 1- Reconocimiento

Un adversario lleva a cabo un reconocimiento para recopilar tanta información sobre el objetivo como sea posible, con el fin de sondear los puntos débiles antes de atacar realmente. Buscan información como la disponible públicamente en Internet, información de la red, información del sistema e información organizativa del objetivo. Llevando a cabo reconocimientos a través de diferentes niveles de red, el adversario puede obtener información como bloques de red, direcciones IP específicas y detalles de los empleados. El adversario puede utilizar herramientas automatizadas para obtener información como puertos y servicios abiertos, vulnerabilidades en aplicaciones y credenciales de inicio de sesión. Dicha información puede ayudar al adversario a obtener acceso de puerta trasera a la red objetivo.&#x20;

Las actividades del adversario incluyen las siguientes:

* Recopilar información sobre la organización objetivo mediante búsquedas en Internet o mediante ingeniería social.
* Análisis de diversas actividades en línea e información pública disponible.
* Recopilación de información de sitios de redes sociales y servicios web.
* Obtención de información sobre sitios web visitados. o Supervisión y análisis del sitio web de la organización objetivo.
* Análisis de Whois, DNS y footprinting.
* Escaneo para identificar puertos y servicios abiertos.

### 2 - Weaponization

El adversario analiza los datos recopilados en la etapa anterior para identificar las vulnerabilidades y técnicas que puede explotar y obtener acceso no autorizado a la organización objetivo. Basándose en las vulnerabilidades identificadas durante el análisis, el adversario selecciona o crea una carga útil maliciosa entregable a medida (arma maliciosa de acceso remoto) utilizando un exploit y una puerta trasera para enviarla a la víctima. Un adversario puede dirigirse a dispositivos de red específicos, sistemas operativos, dispositivos endpoint o incluso individuos dentro de la organización para llevar a cabo su ataque. Por ejemplo, el adversario puede enviar un correo electrónico de phishing a un empleado de la organización objetivo, que puede incluir un archivo adjunto malicioso como un virus o un gusano que, al descargarse, instala una puerta trasera en el sistema que permite el acceso remoto al adversario.&#x20;

Las siguientes son las actividades del adversario:

* Identificar la carga útil de malware apropiada basándose en el análisis
* Crear una nueva carga útil de malware o seleccionar, reutilizar, modificar las cargas útiles de malware disponibles basándose en la vulnerabilidad identificada.
* Creación de una campaña de phishing por correo electrónico.
* Aprovechamiento de exploit kits y botnets.

### 3 - Entrega

La etapa anterior incluye la creación de un arma. Su carga útil se transmite a la(s) víctima(s) prevista(s) como un archivo adjunto de correo electrónico, a través de un enlace malicioso en sitios web, o a través de una aplicación web vulnerable o una unidad USB. La entrega es una etapa clave que mide la eficacia de las estrategias de defensa implementadas por la organización objetivo en función de si el intento de intrusión del adversario es bloqueado o no.&#x20;

Las siguientes son las actividades del adversario:

* Envío de correos electrónicos de phishing a los empleados de la organización objetivo.
* Distribución de unidades USB con carga maliciosa a los empleados de la organización objetivo.
* Realizar ataques como watering hole en el sitio web comprometido.
* Implementación de varias herramientas de pirateo contra los sistemas operativos, aplicaciones y servidores de la organización objetivo.

### 4- Explotación

Después de que el arma se transmite a la víctima prevista, la explotación desencadena el código malicioso del adversario para explotar una vulnerabilidad en el sistema operativo, aplicación o servidor en un sistema de destino. En esta fase, la organización puede enfrentarse a amenazas como ataques de autenticación y autorización, ejecución arbitraria de código, amenazas a la seguridad física y desconfiguración de la seguridad.

Las siguientes son las actividades del adversario:

* Aprovechamiento de vulnerabilidades de software o hardware para obtener acceso remoto al sistema objetivo.

### 5 - Instalación

El adversario descarga e instala más software malicioso en el sistema objetivo para mantener el acceso a la red objetivo durante un periodo prolongado. Pueden utilizar el arma para instalar una puerta trasera y obtener acceso remoto. Tras la inyección del código malicioso en un sistema objetivo, el adversario adquiere la capacidad de propagar la infección a otros sistemas finales de la red. Además, el adversario intenta ocultar la presencia de actividades maliciosas a los controles de seguridad, como los cortafuegos, utilizando diversas técnicas como el cifrado.&#x20;

Las siguientes son las actividades del adversario:

* Descarga e instalación de software malicioso como puertas traseras.
* Obtención de acceso remoto al sistema objetivo.
* Aprovechamiento de diversos métodos para mantener la puerta trasera oculta y en funcionamiento.
* Mantenimiento del acceso al sistema objetivo

### 6 - Comando y Control C2

El adversario crea un canal de mando y control, que establece una comunicación bidireccional entre el sistema de la víctima y el servidor controlado por el adversario para comunicarse y pasar datos de un lado a otro. Los adversarios implementan técnicas como el cifrado para ocultar la presencia de dichos canales. Utilizando este canal, el adversario realiza una explotación remota en el sistema o red objetivo.&#x20;

Las actividades del adversario son las siguientes:

* Establecer un canal de comunicación bidireccional entre el sistema de la víctima y el servidor controlado por el adversario.&#x20;
* Aprovechar canales como el tráfico web, la comunicación por correo electrónico y los mensajes DNS.
* Aplicar técnicas de escalada de privilegios.
* Ocultar cualquier evidencia de compromiso utilizando técnicas como el cifrado.

### 7 - Acciones sobre los objetivos

El adversario controla el sistema de la víctima desde una ubicación remota y finalmente logra sus objetivos previstos. El adversario obtiene acceso a datos confidenciales, interrumpe los servicios o la red, o destruye la capacidad operativa del objetivo accediendo a su red y comprometiendo más sistemas. Además, el adversario puede utilizarlo como punto de lanzamiento para realizar otros ataques.
