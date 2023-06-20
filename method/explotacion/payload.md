# Payload

## ¿Qué es un payload?

Los <mark style="color:green;">**payload**</mark> o en español <mark style="color:green;">**carga útil**</mark> se refiere a una pieza de código malicioso o instrucciones específicas que se envían o ejecutan en un sistema o aplicación vulnerable con el fin de explotar una debilidad y lograr un objetivo malintencionado.

El término "payload" se utiliza comúnmente en el campo de la prueba de penetración (pentesting) y el red teaming, que son prácticas legítimas para evaluar la seguridad de los sistemas informáticos. Durante un pentest o una operación de red teaming, los expertos en seguridad pueden utilizar payloads para aprovechar vulnerabilidades conocidas o desconocidas en el software o la infraestructura de una organización con el fin de descubrir fallos de seguridad y ayudar a mejorar las defensas.

Los payloads pueden variar en forma y función dependiendo del objetivo deseado. Pueden incluir códigos de exploit diseñados para aprovechar vulnerabilidades conocidas en el software objetivo, secuencias de comandos maliciosos para obtener acceso remoto no autorizado, o incluso malware para el robo de información, entre otros fines.

## Tipos de Payload

### **Payload Staged**

Es un tipo de payload que se **divide en dos** **o más etapas**. La primera etapa es una pequeña parte del código que se envía al objetivo, cuyo propósito es establecer una conexión segura entre el atacante y la máquina objetivo. Una vez que se establece la conexión, el atacante envía la segunda etapa del payload, que es la carga útil real del ataque. Este enfoque permite a los atacantes sortear medidas de seguridad adicionales, ya que la carga útil real no se envía hasta que se establece una conexión segura.



### **Payload Non-Staged**

Es un tipo de payload que se envía como **una sola entidad** y **no se divide en múltiples etapas**. La carga útil completa se envía al objetivo en un solo paquete y se ejecuta inmediatamente después de ser recibida. Este enfoque es más simple que el Payload Staged, pero también es más fácil de detectar por los sistemas de seguridad, ya que se envía todo el código malicioso de una sola vez.

Es importante tener en cuenta que el tipo de payload utilizado en un ataque dependerá del objetivo y de las medidas de seguridad implementadas. En general, los payloads Staged son más difíciles de detectar y son preferidos por los atacantes, mientras que los payloads Non-Staged son más fáciles de implementar pero también son más fáciles de detectar.
