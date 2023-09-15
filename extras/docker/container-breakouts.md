# Container Breakouts

## Salirte del contenedor

El término <mark style="color:green;">**Container Breakout**</mark> se refiere al evento en el que un usuario malicioso o legítimo es capaz de escapar del aislamiento del contenedor y acceder a recursos (por ejemplo, sistema de archivos, procesos, interfaces de red) en la máquina anfitriona. En esta entrada se cubren los diferentes errores de configuración y los privilegios excesivos que pueden utilizarse para escapar de los contenedores. El container Breackout siempre se hace bajo un enfoque de violación asumida, lo que significa que el atacante ya ha obtenido un shell de comandos en el contenedor.



### Atáques comunes

* Aprovechamiento de configuraciones erróneas para la fuga de Docker
* Aprovechamiento de privilegios excesivos para acceder al host Docker
* Identificar y aprovechar las capacidades adicionales de Linux asignadas al contenedor
* Atacar espacios de nombres compartidos para violar el aislamiento del contenedor



Por defecto, los contenedores Docker se ejecutan con capacidades limitadas. Por lo tanto, para realizar una operación privilegiada (como actualizar el tiempo, depurar el proceso, etc.) el contenedor requiere capacidades adicionales. Y, la mayoría de las veces en lugar de definir las capacidades específicas, la gente simplemente ejecuta el contenedor en el modo privilegiado que puede conducir a comprometer la máquina host subyacente.



### Referencias

Vulnerabilidad de fuga de contenedor RunC&#x20;

{% embed url="https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-5736" %}

Abusando de la capacidad SYS\_MODULE para realizar la fuga del contenedor Docker&#x20;

{% embed url="https://blog.pentesteracademy.com/abusing-sys-module-capability-to-perform-docker-container-breakout-cf5c29956edd" %}

Charla Blackhat sobre escapes de contenedores&#x20;

{% embed url="https://i.blackhat.com/USA-19/Thursday/us-19-Edwards-Compendium-Of-Container-Escapes-up.pdf" %}
