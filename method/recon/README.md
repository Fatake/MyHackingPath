# Fase 1 - Reconocimiento

Dependiendo del tipo de proyecto, existen diferentes técnicas de reconocimiento. Existen proyectos de hacking a infra estructura, donde el reconocimiento inicia tenido las IP o los rango IP a auditar. Sí me dan un proyecto de pentesting web, normalmente se inicia con reconocimiento de dominios y subdominios.



## Footpriting o Reconocimiento

La fase de reconocimiento o footprinting es **identificar** el nivel de riesgo asociado a la información de acceso público de la organización<mark style="color:green;">**. El footprinting, el primer paso en el hacking ético, se refiere al proceso de recopilación de información sobre una red objetivo y su entorno.**</mark>&#x20;

Desde esta fase, se puede encontrar oportunidades de vulnerar y evaluar el entorno de la organización objetivo. Después de completar el proceso de footprinting de forma metodológica, obtendrás el plano del perfil de seguridad de la organización objetivo. Aquí, el término "blueprint" se refiere al perfil de sistema único de la organización objetivo adquirido mediante el footprinting. No existe una metodología única para el footprinting, ya que la información puede rastrearse de varias maneras. Sin embargo, la actividad es importante, ya que es necesario recopilar toda la información crucial sobre la organización objetivo antes de comenzar la fase explotación.&#x20;

## Escaneo

El <mark style="color:green;">**scanning**</mark> o <mark style="color:green;">**escaneo**</mark> es el proceso de **recopilación de información detallada adicional** sobre el objetivo mediante técnicas de reconocimiento muy complejas y agresivas. Podría decirse la fase activa del reconocimiento.

El escaneado de red se refiere a un **conjunto de procedimientos** utilizados para **identificar hosts**, **puertos** y **servicios** en una red. El escaneo de red también se utiliza para descubrir máquinas activas en una red e identificar el sistema operativo que se ejecuta en la máquina objetivo. Es una de las fases más importantes de la recopilación de inteligencia para un atacante, que le permite crear un perfil de la organización objetivo. En el proceso de exploración, el atacante intenta recopilar información, incluidas las direcciones IP específicas a las que se puede acceder a través de la red, el sistema operativo y la arquitectura del sistema del objetivo, y los puertos junto con sus respectivos servicios que se ejecutan en cada equipo.

## Enumeración

La enumeración es el proceso de extracción de nombres de usuario, nombres de máquinas, recursos de red, recursos compartidos y servicios de un sistema o red. En la fase de enumeración, un atacante crea conexiones activas con el sistema y envía consultas dirigidas para obtener más información sobre el objetivo. El atacante utiliza la información recopilada mediante la enumeración para identificar vulnerabilidades en la seguridad del sistema, lo que le ayuda a explotar el sistema objetivo. A su vez, la enumeración permite al atacante realizar ataques con contraseña para obtener acceso no autorizado a los recursos del sistema de información. Las técnicas de enumeración funcionan en un entorno de intranet.

En concreto, la enumeración permite al atacante recopilar la siguiente información:&#x20;

* Recursos de red
* Recursos compartidos de red
* Tablas de enrutamiento
* Auditoría y configuración de servicios
* Detalles de SNMP y nombres de dominio totalmente cualificados (FQDN)
* Nombres de máquinas
* Usuarios y grupos
* Aplicaciones y banners.

