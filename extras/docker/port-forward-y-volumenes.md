# Port Forward y volúmenes

## Port Forwarding

El **port forwarding**, también conocido como reenvío de puertos, nos permite **redirigir el tráfico de red** desde un puerto específico en el host a un puerto específico en el contenedor. Esto nos permitirá acceder a los servicios que se ejecutan dentro del contenedor desde el exterior.

Para utilizar el port forwarding, se utiliza la opción “**-p**” o “**–publish**” en el comando “**docker run**“. Esta opción se utiliza para especificar la redirección de puertos y se puede utilizar de varias maneras. Por ejemplo, si se desea redirigir el puerto 80 del host al puerto 8080 del contenedor, se puede utilizar la siguiente sintaxis:

```bash
docker run -p 80:8080 mi_imagen
```

Esto redirigirá cualquier tráfico entrante en el puerto 80 del host al puerto 8080 del contenedor. Si se desea especificar un protocolo diferente al protocolo TCP predeterminado, se puede utilizar la opción “**-p**” con un formato diferente. Por ejemplo, si se desea redirigir el puerto 53 del host al puerto 53 del contenedor utilizando el protocolo UDP, se puede utilizar la siguiente sintaxis:

```bash
docker run -p 53:53/udp mi_imagen
```

## Volúmenes

Los voúlemes, por otro lado, nos permiten compartir un directorio o archivo entre el sistema host y el contenedor. Esto nos permitirá persistir la información entre ejecuciones de contenedores y compartir datos entre diferentes contenedores.

Para utilizar los volúmenes, se utiliza la opción “**-v**” o “**–volume**” en el comando “**docker run**“. Esta opción se utiliza para especificar el volumen y se puede utilizar de varias maneras. Por ejemplo, si se desea montar el directorio “**/home/usuario/datos**” del host en el directorio “**/datos**” del contenedor, se puede utilizar la siguiente sintaxis:

```bash
docker run -v /home/usuario/datos:/datos mi_imagen
```

Esto montará el directorio “**/home/usuario/datos**” del host en el directorio “**/datos**” del contenedor. Si se desea especificar una opción adicional, como la de montar el directorio en modo de solo lectura, se puede utilizar la opción “**-v**” con un formato diferente. Por ejemplo, si se desea montar el directorio en modo de solo lectura, se puede utilizar la siguiente sintaxis:

```bash
docker run -v /home/usuario/datos:/datos:ro mi_imagen
```

