# Diferencia entre una WebShell y Reverse Shell

## <mark style="color:green;">Webshell</mark>

1. **Definición:** Una webshell es un script malicioso que se carga en un servidor web y que proporciona una interfaz que permite ejecutar comandos en el servidor. Generalmente está escrita en lenguajes de scripting web como PHP, ASP, o JSP.
2. **Acceso:** La webshell permite el acceso y control remoto del servidor web a través de una interfaz web, típicamente a través de un navegador.
3. **Uso:** Es utilizada para mantener el acceso persistente a un servidor web comprometido y para realizar diversas actividades maliciosas como la exfiltración de datos, la creación de cuentas de usuario, etc.
4. **Comunicación:** La webshell generalmente se comunica directamente con el atacante mediante el uso del protocolo HTTP o HTTPS.
5. **Ejemplo:** Un atacante podría utilizar una webshell para explorar los archivos en un servidor web, descargar datos sensibles, o utilizar el servidor para lanzar otros ataques.

## <mark style="color:green;">Reverse Shell</mark>

1. **Definición:** Un reverse shell ocurre cuando una máquina comprometida inicia una conexión hacia un atacante (en lugar de que el atacante conecte directamente a la máquina comprometida), y luego recibe y ejecuta comandos de este.
2. **Acceso:** La shell (interfaz de línea de comandos) del sistema comprometido es redirigida a través de la red hacia el atacante, quien puede emitir comandos remotos.
3. **Uso:** Los reverse shells son utilizados para evadir mecanismos de seguridad que bloquean las conexiones entrantes, permitiendo al atacante control remoto sobre el sistema comprometido.
4. **Comunicación:** La máquina comprometida se conecta al atacante (posiblemente utilizando una variedad de protocolos), invirtiendo la dirección de la conexión en comparación con una shell remota tradicional.
5. **Ejemplo:** Si un atacante logra ejecutar un script en una máquina objetivo que inicia una conexión reverse shell, podría usar esa conexión para ejecutar comandos arbitrarios en la máquina objetivo.

## En Resumen:

* **Webshell:** Se ejecuta en un servidor web y es accedida a través de un navegador web, permitiendo ejecutar comandos en el servidor.
* **Reverse Shell:** La máquina comprometida inicia una conexión hacia el atacante y le permite emitir comandos en la máquina comprometida, comúnmente utilizada para evadir defensas que bloquean conexiones entrantes.

Ambas técnicas son comunes en ciberataques y a menudo se usan para mantener el acceso a sistemas comprometidos, permitir la movilidad lateral en una red, y facilitar otras actividades maliciosas. Las defensas contra estas tácticas incluyen una seguridad robusta de la red, auditorías regulares del sistema y del código, y el uso de soluciones de seguridad avanzadas, tales como sistemas de detección y prevención de intrusiones.
