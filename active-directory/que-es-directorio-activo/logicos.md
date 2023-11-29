# Componentes Lógicos

## AD DS Schema

El AD DS Schema es un componente crítico de Active Directory Domain Services. Define la estructura y el conjunto de atributos que pueden ser utilizados para describir y representar objetos en un dominio de Active Directory. (Como un libro de relas y definiciones para cada objeto creado en AD).

<figure><img src="../../.gitbook/assets/image (59) (2).png" alt=""><figcaption></figcaption></figure>

El esquema de AD DS actúa como una plantilla o plan maestro que define los tipos de objetos que pueden existir en Active Directory, así como los atributos y propiedades asociados a cada tipo de objeto. Estos objetos pueden incluir usuarios, grupos, equipos, contactos y otros elementos que forman parte de la infraestructura de Active Directory.

El esquema de AD DS determina qué atributos y propiedades pueden tener los objetos de Active Directory, y cómo se pueden utilizar y modificar esos atributos. Por ejemplo, el esquema define atributos como el nombre de usuario, la contraseña, el nombre completo, la dirección de correo electrónico, entre otros, que se pueden asociar a un objeto de usuario en Active Directory.

El esquema de AD DS es una entidad única y está presente en todos los Controladores de Dominio de un dominio de Active Directory. Cualquier modificación realizada en el esquema se replica en todos los Controladores de Dominio del dominio para mantener la consistencia y coherencia de los datos.

## Domains

Los AD Domains son <mark style="color:green;">**unidades lógicas de organización**</mark> dentro de la infraestructura de Active Directory. Un dominio es una <mark style="color:green;">**agrupación de objetos relacionados**</mark>, como usuarios, grupos, equipos y recursos, que comparten una base de datos centralizada y un conjunto común de políticas de seguridad y administración.

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

Cada dominio de Active Directory tiene su propia base de datos y se gestiona de manera independiente de otros dominios (Sí solo existe un dominio, este se puede comportar como el Domain Controller). Los dominios permiten una administración y seguridad eficientes al proporcionar límites lógicos y jerárquicos en una red de Windows.

## Árboles

Grupos de domínios, una estructura jerárquica de dominios en Active Directory. Un árbol de AD se forma cuando varios dominios están conectados en una relación de confianza y comparten un espacio de nombres de dominio común

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

En un árbol de AD, un dominio raíz es el dominio primario que se establece inicialmente. A partir de ahí, se pueden agregar dominios secundarios que están vinculados jerárquicamente al dominio raíz. Cada dominio secundario se convierte en un hijo del dominio raíz y forma una relación de confianza con él.

Cada dominio en un árbol de AD tiene su propia base de datos y se administra de manera independiente, pero comparten una estructura común y una base de datos de Active Directory. Esto significa que los objetos y las políticas de seguridad pueden ser compartidos y heredados a través de los dominios en el árbol.

La ventaja de usar árboles de Active Directory es que permiten una administración centralizada y una estructura lógica más organizada.

## Bosques

Un AD Forest es una estructura de alto nivel en Active Directory que consiste en uno o más árboles de dominio. En términos simples, un bosque de AD es una colección de árboles de AD interconectados.

<figure><img src="../../.gitbook/assets/image (7) (4).png" alt=""><figcaption></figcaption></figure>

Un bosque de Active Directory se forma cuando varios árboles de dominio se conectan mediante relaciones de confianza. Cada árbol de dominio en el bosque es independiente y tiene su propio espacio de nombres de dominio único, pero comparten una estructura común y una base de datos de Active Directory.

## Organizational Units (OUs)

Las Organizational Units (Unidades Organizativas), también conocidas como OUs, son contenedores lógicos dentro de un dominio de Active Directory (AD) que se utilizan para organizar y administrar los objetos del directorio, como usuarios, grupos y equipos. Las OUs proporcionan una forma estructurada de gestionar y aplicar políticas específicas a grupos de objetos relacionados en una jerarquía lógica.

<figure><img src="../../.gitbook/assets/image (26) (3).png" alt=""><figcaption></figcaption></figure>

## Trusts / Relaciones de confianza

<figure><img src="../../.gitbook/assets/image (22) (3).png" alt=""><figcaption></figcaption></figure>

## Objetos

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

## Referencias

{% embed url="https://learn.microsoft.com/en-us/shows/introtoad/01" %}
