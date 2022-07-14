# ¿Cómo interceptar tráfico de una aplicación Flutter Android con Burp?

## ¿Cómo interceptar tráfico de una aplicación Flutter Android con Burp?

Dado que las aplicaciones construidas con el Frameworks de Google Flutter no utilizan la configuración por defecto de proxys del teléfono. Dart genera y compila su propio Keystore usando la biblioteca NSS de Mozilla. Esto significa que no podemos eludir la validación SSL agregando nuestra CA proxy al almacén de CA del sistema. Entonces se requiere hacer una serie de modificaciones para poder interceptar todo el tráfico HTTP que la aplicación genera, para ello se necesita primeramente:

* Herramienta Ghidra.
* Aplicación ProxyDroid Instalada en Android.
* Dispositivo rooteado Android.
* Archivo **libflutter.so** extraído del archivo .apk
* Herramienta Frida.

&#x20;

### Extracción del archivo **libflutter.so**

Este archivo se puede encontrar en muchas partes dentro del archivo .apk.

\
Paso 1: De descomprimir todo el apk.

Paso 2: Grep -u “libflutter.so” en la carpeta extraída del .apk para encontrar el archivo libflutter.so

&#x20;

### Importar el archivo **libflutter.so en Ghidra**

Genere un proyecto nuevo en Ghidra (Se recomienda mínimamente 4gb de ram para el descompilado), después agregue el archivo libfflutter.so.

Dado que Dart usa la biblioteca BoringSSL de Google para manejar todo lo relacionado a certificados SSL y tanto Dart como BoringSSL son código abierto se puede realizar un parte con ingeniería inversa.

En la función Handshake.cc de la biblioteca de BoringSSL se contiene la lógica para realizar la validación del certificado.

Este código es fragmento de la función “ssl\_verify\_peer\_cert” que devuelve el código de si la verificación fue exitosa o no.

&#x20;

Por lo tanto si se cambia el valor de retorno de la función “ssl\_verify\_peer\_cert”  a ssl\_verify\_ok (= 0) entonces podemos evitar la validación del certificado y capturar el tráfico. Sin embargo, suceden muchas cosas en este método  y la herramienta Frida debería poder cambiar con facilidad este valor de retorno, pero aún si se cambia el valor de retorno, todavía existe una función llamada “ssl\_send\_alert” que es la encargada de confiar y validar este valor.

Un mejor candidato es modificar ell valor de la función “session\_verify\_cert\_chain”

&#x20;

Si esta función falla, solo modifica la función “OPENSSL\_PUT\_ERROR” y no la de envió de alertas., entonces se procede a modificar la función “OPENSSL\_PUT\_ERROR”

A continuación, se tiene que buscar el valor numérico corresponde a la dirección de memoria de la función macro “OPENSSL\_PUT\_ERROR”.

En Ghidra use el buscador -> Buscar cadenas

Podría intentar primeramente buscando donse hace la llamada a la biblioteca ssl\_x509.cc de ffluter buscando el string **x509.ccx**

&#x20;

Dado que existen solo 3 referencias, puede buscar uno a uno hasta que encuentre la función “session\_verify\_cert\_chain” que es donde esta el llamado a “OPENSSL\_PUT\_ERROR” que queremos modificar.\
\
Una de las dos funciones toma como parámetros 2 enteros

Normalmente este valor numérico se encuentra en forma de la siguiente instrucción en ensamblador:

* Mov w3, #0x186

Es conveniente buscar por los valores **390 o 0x186**.

&#x20;

&#x20;

_Ilustración 4 - Dirección de memoria de OPENSSL\_PUT\_ERROR_

&#x20;

Normalmente el nombre de la función “OPENSSL\_PUT\_ERROR”, se puede también encontrar en su descompilado como “FUN\_0034B330”.

Una vez encontrada la dirección de esta función proceda a buscar donde se encuentra en memoria la ubicación de su llamada, a veces Ghidra usa como dirección inicial del programa el valor 0x100000, por lo tanto solo tiene que restar la dirección de memoria de la función “OPENSSL\_PUT\_ERROR” menos la dirección base 0x100000, lo que da un desplazamiento:

* Por ejemplo: 006874e4 – 0x100000 = 0x5873d4.

&#x20;

### Frida

Haciendo disassembly correctamente ahora podemos simplificar los cálculos con el siguiente script js y ejecutarlo en frida:

script.js

> &#x20;
>
> function hook\_ssl\_verify\_result(address) {​​​​​​  Interceptor.attach(address, {​​​​​​\
> &#x20;     onEnter: function(args) {​​​​​​​​​​​​​​​​​​​​\
> &#x20;     console.log("\[i] Deshabilitando la validacion SSL")\
> &#x20;   }​​​​​​​​​​​​​​​​​​​​,\
> &#x20;   onLeave: function(retval) {​​​​​​​​​​​​​​​​​​​​\
> &#x20;     console.log("\[+] Retval: " + retval)\
> &#x20;     retval.replace(0x1);\
> &#x20;   }​​​​​​​​​​​​​​​​​​​​\
> &#x20; }​​​​​​​​​​​​​​​​​​​​);\
> }​​​​​​​​​​​​​​​​​​​​\
> function disablePinning() {​​​​​​​​​​​​​​​​​​​​\
> &#x20; var m = Process.findModuleByName("libflutter.so");\
> &#x20; var patronHex = "2d e9 f0 4f a3 b0 82 46 50 20 10 70"\
> &#x20; var res = Memory.scan(m.base, m.size, patronHex, {​​​​​​​​​​​​​​​​​​​​\
> &#x20;   onMatch: function(address, size){​​​​​​​​​​​​​​​​​​​​\
> &#x20;     console.log('\[+] ssl\_verify\_result encontrado en la direccion: ' + address.toString());\
> &#x20;     hook\_ssl\_verify\_result(address.add(0x01));\
> &#x20;   }​​​​​​​​​​​​​​​​​​​​,\
> &#x20;   onError: function(reason) {​​​​​​​​​​​​​​​​​​​​\
> &#x20;     console.log('\[!] Error al analizar el archivo');\
> &#x20;   }​​​​​​​​​​​​​​​​​​​​,\
> &#x20;   onComplete: function() {​​​​​​​​​​​​​​​​​​​​\
> &#x20;     console.log("\[i] Finalizado")\
> &#x20;   }​​​​​​​​​​​​​​​​​​​​\
> &#x20; }​​​​​​​​​​​​​​​​​​​​);\
> }​​​​​​​​​​​​​​​​​​​​\
> setTimeout(disablePinning, 1000)

\
&#x20;

Se ejecuta de la siguiente forma:

> $ frida -U -f aplicación.apk -l script.js --no-pause

\
