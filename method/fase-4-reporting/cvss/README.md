---
description: Sistema Compun de puntuación de Vulnerabilidades CVSS
---

# CVSS

## ¿Cómo evaluar tus hallazgos?

Al momento de realizar la evaluación de criticidad de las vulnerabilidades encontradas durante las pruebas de intrusión / pentesting, el estándar es clasificarlas bajo el marco de trabajo **CVSS** de la empresa [first.org](https://www.first.org/). En esta entrada se pretende explicar el funcionamiento y formas de puntuar las vulnerabilidades con CVSS en su versión 3.1 (este versionado puede cambiar durante el paso del tiempo donde se agregan y modifican parámetros de evaluación, a fecha de edición de esta entrada se está desarrollando la versión 4).

## ¿Qué es el CVSS?

El <mark style="color:green;">**Sistema Común de Puntuación de Vulnerabilidades (CVSS)**</mark> es un marco abierto para comunicar las características y la gravedad de las vulnerabilidades del software. Este sistema recoge las principales características técnicas de las vulnerabilidades de software, hardware y firmware. Sus resultados incluyen puntuaciones numéricas que indican la gravedad de una vulnerabilidad en relación a otras vulnerabilidades.

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

CVSS consta de tres grupos de métricas en su versión 3.1:

* Base
* Temporal
* Ambiental.

El grupo Base representa las cualidades intrínsecas de una vulnerabilidad que son constantes a lo largo del tiempo y en todos los entornos de usuario, el grupo Temporal refleja las características de una vulnerabilidad que cambian con el tiempo, y el grupo Ambiental representa las características de una vulnerabilidad que son exclusivas del entorno de un usuario. Las métricas Base producen una puntuación que va de 0 a 10, que puede modificarse puntuando las métricas Temporal y Entorno. Una puntuación CVSS también se representa como una cadena vectorial, una representación textual comprimida de los valores utilizados para derivar la puntuación.

<figure><img src="../../../.gitbook/assets/image (2) (8).png" alt=""><figcaption></figcaption></figure>

Las ventajas del CVSS incluyen la provisión de una metodología estandarizada de puntuación de vulnerabilidades independiente del proveedor y de la plataforma. Se trata de un marco abierto, que proporciona transparencia a las características individuales y a la metodología utilizada para obtener una puntuación.
