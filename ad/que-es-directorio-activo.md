---
description: What Is Active Directory
---

# ¿Qué es Directorio Activo?

<mark style="color:green;">**Active Directory (AD)**</mark> es un servicio de directorio desarrollado por Microsoft que se utiliza para administrar y organizar los recursos de red en un entorno empresarial. Es una parte integral del sistema operativo Windows Server y se implementa principalmente en entornos de red de Microsoft.

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

Active Directory actúa como un servicio de directorio centralizado que almacena información sobre objetos en una red, como usuarios, grupos, equipos y recursos compartidos. Proporciona un mecanismo para autenticar y autorizar el acceso a estos objetos, lo que permite a los administradores de red controlar y gestionar de manera eficiente los recursos de red.

Algunas de las características y funcionalidades principales de Active Directory incluyen:

1. <mark style="color:green;">**Autenticación centralizada (Kerberos tickets)**</mark>: Los usuarios pueden iniciar sesión en su cuenta de Windows y acceder a los recursos de red utilizando las mismas credenciales en diferentes equipos.
   1. Los dispositivos no Windows como Linux, firewalls, entre otros pueden autenticarse al AD via RADIUS o LDAP
2. <mark style="color:green;">**Administración de políticas de grupo**</mark>: Permite a los administradores establecer políticas y configuraciones de seguridad para usuarios y equipos en la red.
3. <mark style="color:green;">**Organización jerárquica (Dominios)**</mark>: Active Directory utiliza una estructura jerárquica de dominios y árboles de dominios para organizar los objetos y facilitar su administración.
4. <mark style="color:green;">**Servicios de directorio distribuido**</mark>: Active Directory replica y sincroniza automáticamente los datos del directorio en múltiples controladores de dominio para garantizar la disponibilidad y la redundancia.
5. <mark style="color:green;">**Acceso a recursos compartidos**</mark>: Los usuarios pueden acceder a recursos compartidos, como impresoras, archivos y carpetas, de forma segura y controlada.



Active Directory es el servicio de gestión de identidades más utilizado en el mundo. El 95% de las empresas de la lista Fortune 1000 utilizan este servicio en sus redes.

{% embed url="https://techcommunity.microsoft.com/t5/security-compliance-and-identity/success-with-enterprise-mobility-identity/ba-p/248613" %}

Puede ser explotado sin necesidad de atacar exploits parcheables. En su lugar, se suele abusar de características, configuraciones de confianzas, componentes y más





