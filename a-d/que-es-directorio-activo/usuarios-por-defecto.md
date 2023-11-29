# Usuarios por defecto

En un entorno de Active Directory (AD), hay varios grupos y usuarios predeterminados que son creados durante la instalación. Estos grupos y usuarios tienen diferentes niveles de privilegios que permiten realizar ciertas tareas dentro del dominio.

## **Grupos predeterminados de Active Directory:**

1. **Domain Admins:** Este grupo tiene control total sobre todos los dominios en el bosque. Cualquier usuario que sea miembro de este grupo puede realizar cualquier cambio en el dominio, como crear o eliminar otros usuarios, grupos y modificar políticas de seguridad.
2. **Enterprise Admins:** Los miembros de este grupo tienen permisos para realizar cambios a nivel de toda la empresa, incluidos todos los dominios y bosques.
3. **Schema Admins:** Los usuarios en este grupo pueden hacer cambios en el esquema de AD, que es la definición de objetos y atributos que pueden ser creados y almacenados en AD.
4. **Account Operators:** Pueden administrar las cuentas de usuario y grupos dentro de un dominio, pero no pueden modificar los miembros de los grupos de administradores ni cambiar la configuración de los servidores de dominio.
5. **Backup Operators:** Pueden realizar copias de seguridad y restaurar archivos en un servidor, independientemente de los permisos de acceso a esos archivos.
6. **Server Operators:** Pueden realizar tareas de mantenimiento en servidores de dominio.
7. **Guests:** Es un grupo que por defecto tiene privilegios mínimos, y generalmente se utiliza para proporcionar acceso temporal o limitado a la red.

## **Usuarios predeterminados de Active Directory:**

1. <mark style="color:green;">**Administrator**</mark>**:** La cuenta de administrador del dominio que tiene acceso total a todos los servidores y estaciones de trabajo del dominio.
2. <mark style="color:green;">**Guest**</mark>**:** Una cuenta de usuario que tiene privilegios limitados, generalmente deshabilitada por defecto.

## **Impacto de la Compromisión de Cuentas Privilegiadas:**

Si un atacante compromete una cuenta perteneciente a uno de los grupos privilegiados, como Domain Admins o Enterprise Admins, podría tener impactos severos en la seguridad de la red. Por ejemplo:

* **Compromiso de Domain Admins:** El atacante podría crear cuentas de usuario adicionales para acceso persistente, cambiar políticas de seguridad, acceder a datos confidenciales, implementar malware en toda la red, tomar control de otros servidores y estaciones de trabajo, y más.
* **Compromiso de Enterprise Admins:** Este nivel de acceso podría permitir al atacante afectar múltiples dominios dentro del bosque de AD, potencialmente dándoles el control sobre toda la infraestructura de IT de la empresa.
* **Compromiso de Schema Admins:** El atacante podría modificar el esquema de AD, lo que puede tener un impacto duradero y posiblemente irreversible en la estructura del directorio y la interoperabilidad de los servicios basados en AD.
* **Compromiso de Account Operators:** Aunque no pueden cambiar cuentas de administradores, podrían alterar otras cuentas y grupos, posiblemente creando puertas traseras para ellos mismos.
* **Compromiso de Backup Operators:** Podrían utilizar sus privilegios para copiar datos confidenciales o alterar/eliminar respaldos para impedir la recuperación después de un incidente.

En todos estos casos, la detección y respuesta rápida son esenciales para mitigar el daño. La implementación de la menor cantidad de privilegios necesarios, la monitorización de las cuentas privilegiadas y la utilización de medidas de seguridad adicionales como la autenticación multifactor y el análisis de comportamiento son prácticas recomendadas para reducir el riesgo de compromiso.
