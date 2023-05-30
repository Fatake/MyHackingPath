# Introducción a Red Team

## ¿Qué es un Red Team?

<mark style="color:green;">**Red Teaming**</mark> es un término muy utilizado en el ámbito de la ciberseguridad. Su significado y propósito se han ido deformando con el tiempo (o al menos no están estandarizados), debido a varios factores, entre ellos el mal uso del nombre en el marketing de los proveedores y un malentendido de los requisitos de cumplimiento. Se intentará ofrecer aquí una definición precisa que sentará las bases: debemos entender qué son los equipos Red Team, qué hacen, por qué lo hacen, cómo lo hacen y, quizás igual de importante, **para qué no sirven.**

Joe Vest y James Tubberville describen a los red team como:

> Red Teaming es el proceso de utilizar tácticas, técnicas y procedimientos (TTPs) para emular una amenaza del mundo real, con el objetivo de medir la eficacia de las personas, procesos y tecnologías utilizadas para defender un entorno.

Los equipos de Red Team aportan una perspectiva de confrontación al cuestionar las suposiciones de una organización y sus defensores. Suposiciones como "estamos seguros porque parcheamos", "sólo X personas pueden acceder a ese sistema" o "la tecnología Y lo impediría" son peligrosas y a menudo no resisten el escrutinio. Al cuestionar estos supuestos, un equipo rojo puede identificar áreas de mejora en la defensa operativa de una organización.

Aunque existe cierta similitud con las pruebas de penetración, hay algunas diferencias clave que conviene destacar.

Una prueba de penetración típica se centrará en una única pila tecnológica, ya sea porque forma parte del ciclo de vida de un proyecto o de un requisito de cumplimiento (por ejemplo, evaluaciones mensuales o anuales). Los objetivos son identificar tantas vulnerabilidades como sea posible, demostrar cómo pueden ser explotadas y proporcionar algunas calificaciones de riesgo contextuales. El resultado suele ser un informe que contiene cada vulnerabilidad y las medidas correctoras, como instalar un parche o reconfigurar algún software. No hay un enfoque explícito en la detección o respuesta, no se evalúan las personas o los procesos y no hay ningún objetivo específico aparte de "explotar el sistema o sistemas".

En cambio, los equipos rojos tienen un objetivo claro definido por la organización, ya sea acceder a un sistema concreto, una cuenta de correo electrónico, una base de datos o un archivo compartido. Porque, al fin y al cabo, las organizaciones defienden "algo" y comprometer la confidencialidad, integridad y/o disponibilidad de ese "algo" representa un riesgo tangible, ya sea financiero o de reputación. Un equipo rojo también emulará una amenaza real para la organización. Por ejemplo, una empresa financiera puede estar en peligro por grupos FIN conocidos. En el caso de una prueba de penetración, un probador simplemente utilizará sus TTP preferidos personalmente, mientras que un equipo rojo estudiará y reutilizará (cuando proceda) los TTP de la amenaza que están emulando. Esto permite a la organización crear detecciones y procesos diseñados para combatir la amenaza a la que esperan enfrentarse. Los equipos rojos también analizarán de forma holística la postura global de seguridad de una organización y no se centrarán en un área específica, lo que incluye tanto a las personas y los procesos como a la tecnología. Por último, los equipos rojos hacen mucho hincapié en el sigilo y en el "principio del menor privilegio". Para desafiar las capacidades de detección y respuesta, necesitan alcanzar el objetivo sin ser descubiertos; parte de esto es no ir tras cuentas con privilegios elevados (como la de administrador de dominio) innecesariamente. Si "Bob de Contabilidad" puede acceder al objetivo, entonces eso es todo lo que harán.




