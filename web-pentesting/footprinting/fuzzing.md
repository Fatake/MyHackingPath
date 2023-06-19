# Fuzzing

## ¿Qué es fuzzing o descrubimiento de directorios?

Después de realizar un escaneo de puertos en un sistema, como Nmap, es posible obtener más información. El pobjetivo de hacer un "web fuzzer" es buscar qué direcciones URL están activas en un dominio determinado.

Un web fuzzer es una herramienta que permite probar qué rutas están accesibles y cuáles no en un sitio web. Por ejemplo, si se encuentra una dirección que contiene un archivo vulnerable, se ha descubierto una falla de seguridad.

Entonces, podemos definir un web fuzzer o app fuzzing como una herramienta utilizada para detectar vulnerabilidades. Un sistema bien configurado no debería tener acceso público a archivos confidenciales. Sin embargo, es común encontrar estas vulnerabilidades en Internet y siempre es importante verificar su existencia durante una auditoría de seguridad.

Mediante el uso de un web fuzzer o app fuzzing, se puede identificar una configuración deficiente en la red. Algunas rutas pueden contener archivos sensibles, como el código fuente de una página. En ocasiones, la configuración de los sitios web se realiza de manera descuidada, lo que permite que estén expuestos a posibles ataques de hackers malintencionados.

## Tools

### wfuzz

**Wfuzz** es una herramienta de descubrimiento de contenido y una herramienta de inyección de datos. Básicamente, se utiliza para automatizar los procesos de prueba de vulnerabilidades en aplicaciones web.

Permite realizar ataques de fuerza bruta en parámetros y directorios de una aplicación web para identificar recursos existentes. Una de las ventajas de Wfuzz es que es altamente personalizable y se puede ajustar a diferentes necesidades de pruebas. Algunas de las desventajas de Wfuzz incluyen la necesidad de comprender la sintaxis de sus comandos y que puede ser más lenta en comparación con otras herramientas de descubrimiento de contenido.

{% embed url="https://github.com/xmendez/wfuzz" %}

### Gobuster

**Gbuster** es una herramienta de descubrimiento de contenido que también se utiliza para buscar archivos y directorios ocultos en una aplicación web. Al igual que Wfuzz, Gobuster se basa en ataques de fuerza bruta para encontrar archivos y directorios ocultos. Una de las principales ventajas de Gobuster es su velocidad, ya que es conocida por ser una de las herramientas de descubrimiento de contenido más rápidas. También es fácil de usar y su sintaxis es simple. Sin embargo, una desventaja de Gobuster es que puede no ser tan personalizable como Wfuzz.

{% embed url="https://github.com/OJ/gobuster.git" %}

{% code overflow="wrap" %}
```bash
gobuster dir -u <url> -w <PathToWorldList> -t 10 -b 403,404 -x html,php,py,back

## Con coockies
gobuster dir -u https://mysite.com/path/to/folder -c 'session=123456' -t 50 -w common-files.txt -x .php,.html
```
{% endcode %}

### _**FFUF**_

FFUF es otra de las herramientas de seguridad más utilizadas para fuzz testing en aplicaciones web. El sistema en el que opera es similar al de Dirb, pero con la diferencia de que su código fuente está escrito en Go

{% embed url="https://github.com/ffuf/ffuf.git" %}

{% embed url="https://github.com/Fatake/autoffuf.git" %}
