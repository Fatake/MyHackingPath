# GPO VS ACL

Las GPO (Políticas de Grupo) y las ACL (Listas de Control de Acceso) son conceptos fundamentales en la administración de la seguridad de redes Windows y Active Directory, pero sirven a propósitos diferentes y ambos necesitan ser gestionados con un alto nivel de seguridad debido a su potencial impacto en el entorno de TI.

## **GPO (Group Policy Objects):**

* Las GPOs son utilizadas para controlar el entorno de trabajo de usuarios y computadoras en una red. Permiten la configuración en masa de políticas que afectan a aspectos como la configuración de seguridad, asignaciones de software, scripts de inicio/cierre de sesión y configuraciones de usuario, entre otros.
* Las GPOs se aplican a gran escala en el dominio, las OUs o a nivel de sitio en Active Directory.
* Un cambio en una GPO puede afectar a muchas cuentas y dispositivos simultáneamente.
* Los abusos de las GPO pueden llevar a la propagación de malware, configuraciones de seguridad debilitadas, escalada de privilegios, etc.

## **ACL (Access Control Lists):**

* Las ACLs son listas que definen los permisos individuales de acceso a objetos específicos como archivos, directorios, objetos de AD, etc.
* Cada objeto en AD y en el sistema de archivos de Windows tiene una ACL que define quién puede interactuar con el objeto y de qué manera (leer, escribir, ejecutar, modificar, etc.).
* Los cambios en las ACLs generalmente afectan a un solo objeto o a un grupo pequeño de objetos relacionados.
* Las ACLs mal configuradas o comprometidas pueden permitir accesos no autorizados a datos sensibles, escalada de privilegios y control sobre recursos críticos.
