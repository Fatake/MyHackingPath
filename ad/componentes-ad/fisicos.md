# Físicos

## Domain Controller

Un Domain Controller es un servidor que ejecuta el sistema operativo Windows Server y tiene instalado el servicio de <mark style="color:green;">**Active Directory Domain Services (AD DS).**</mark> Contiene toda la información de: las computadoras de los usuarios, las impresoras, llaves de acceso.

La función principal de un Domain Controller es almacenar, autenticar y proporcionar acceso a los recursos de red dentro de un dominio específico. Un dominio en Active Directory es una agrupación lógica de objetos, como usuarios, equipos y recursos, que comparten una base de datos centralizada y un conjunto común de políticas de seguridad y administración.

<figure><img src="../../.gitbook/assets/image (17) (3).png" alt=""><figcaption></figcaption></figure>

Cuando se establece un dominio de Active Directory, al menos uno de los servidores en ese dominio se designa como Controlador de Dominio. El Controlador de Dominio contiene una réplica del directorio de Active Directory, que incluye información sobre todos los objetos y atributos en el dominio.

### Las funciones y responsabilidades de un DC

1. <mark style="color:green;">**Autenticación y autorización**</mark>: Un Domain Controller es responsable de autenticar las credenciales de los usuarios y garantizar que tengan acceso autorizado a los recursos dentro del dominio.
2. <mark style="color:green;">**Servicio de directorio**</mark>: El Controlador de Dominio almacena y administra la base de datos de Active Directory, que contiene información sobre los objetos del dominio, como usuarios, grupos, equipos y políticas.
3. <mark style="color:green;">**Replicación de directorio**</mark>: Los Controladores de Dominio se replican entre sí para mantener una copia actualizada de la base de datos de Active Directory en cada servidor. Esto garantiza la disponibilidad de los datos y la tolerancia a fallos.
4. <mark style="color:green;">**Políticas de seguridad y administración**</mark>: El Controlador de Dominio permite la aplicación de políticas de seguridad y administración a los objetos del dominio, lo que facilita la gestión centralizada de la red.
5. <mark style="color:green;">**Servicios de nombres**</mark>: Un Controlador de Dominio también puede actuar como servidor DNS (Domain Name System), proporcionando servicios de resolución de nombres para los clientes de la red.

Sí se puede comprometer un DC en una auditoria / pentesting/ redteaming, se puede potencialmente comprometer toda la red coorportativa. Todo depende del tamaño de la red, perp sí solo existe un solo DC y este es comprometido, se puede controlar todo.



## AD DS Data Store

AD DS Data Store (Almacén de datos de AD DS) es el componente de <mark style="color:green;">almacenamiento principal</mark> de Active Directory Domain Services (AD DS). Es una base de datos jerárquica y distribuida que almacena información sobre los objetos y atributos de un dominio en Active Directory.

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

El AD DS Data Store almacena datos críticos relacionados con la estructura y configuración de Active Directory, incluyendo:

1. <mark style="color:green;">**Objetos de directorio**</mark>: Usuarios, grupos, equipos, contactos y otros objetos que representan entidades en la red.
2. <mark style="color:green;">**Atributos de objetos**</mark>: Información asociada a los objetos de directorio, como nombres, contraseñas (en hashes), direcciones de correo electrónico, números de teléfono, entre otros.
3. <mark style="color:green;">**Relaciones entre objetos**</mark>: Información sobre las relaciones y asociaciones entre los diferentes objetos del directorio, como la membresía de grupos, la pertenencia a unidades organizativas (OUs) y otras relaciones jerárquicas.

El AD Data Store utiliza un formato de base de datos específico, conocido como Extensible Storage Engine (ESE) o JET Blue. Esta base de datos permite la indexación y búsqueda eficientes de los objetos y atributos almacenados, lo que facilita el acceso rápido a la información de Active Directory.

Normalmente, cuando se compromete el DC, un atacante va a buscar el archivo Ntsd.dit donde se almacena toda la información almacenada de AD.

La replicación de datos es un aspecto fundamental del AD DS Data Store. Los Controladores de Dominio, que alojan copias del almacén de datos, se sincronizan entre sí para garantizar la coherencia y disponibilidad de los datos en todo el dominio. La replicación asegura que cualquier cambio realizado en un Controlador de Dominio se propague a los demás, permitiendo que los datos estén actualizados y disponibles en diferentes ubicaciones de la red.

