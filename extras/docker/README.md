# Docker

## ¿Qúe es docker?

Docker es una plataforma de software que le permite crear, probar e implementar aplicaciones rápidamente. Docker empaqueta software en unidades estandarizadas llamadas [<mark style="color:green;">**contenedores**</mark>](https://hub.docker.com/search?q=\&type=image) que incluyen todo lo necesario para que el software se ejecute, incluidas bibliotecas, herramientas de sistema, código y tiempo de ejecución. Con Docker, puede implementar y ajustar la escala de aplicaciones rápidamente en cualquier entorno con la certeza de saber que su código se ejecutará.

<figure><img src="../../.gitbook/assets/image (7) (6).png" alt=""><figcaption><p>how docker works</p></figcaption></figure>

## Dockerfile

Un archivo **Dockerfile** se compone de varias secciones, cada una de las cuales comienza con una **palabra clave** en **mayúsculas**, seguida de uno o más argumentos.

Algunas de las secciones más comunes en un archivo Dockerfile son:

* **FROM**: se utiliza para especificar la imagen base desde la cual se construirá la nueva imagen.
* **RUN**: se utiliza para ejecutar comandos en el interior del contenedor, como la instalación de paquetes o la configuración del entorno.
* **COPY/ADD**: se utiliza para copiar archivos desde el sistema host al interior del contenedor.
* **CMD**: se utiliza para especificar el comando que se ejecutará cuando se arranque el contenedor.



Además de estas secciones, también se pueden incluir otras instrucciones para configurar el entorno, instalar paquetes adicionales, exponer puertos de red y más.

{% code title="dockerfile" overflow="wrap" %}
```docker
# syntax=docker/dockerfile:1
   
FROM node:18-alpine
MAINTAINER Pruiz "fatake@fatake.com"
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
ENTRYPOINT Comando de inicio && /bin/bash
```
{% endcode %}

Para crear una imagen de Docker, es necesario tener un archivo **Dockerfile** que defina la configuración de la imagen. Una vez que se tiene el Dockerfile, se puede utilizar el comando “**docker build**” para construir la imagen. Este comando buscará el archivo ‘Dockerfile’ en el directorio actual y utilizará las instrucciones definidas en el mismo para construir la imagen.

{% embed url="https://docs.docker.com/engine/reference/builder/" %}

## Comandos más usados

### docker build

**docker build**: es el comando que se utiliza para construir una imagen de Docker a partir de un Dockerfile.

La sintaxis básica es la siguiente:

```bash
docker build [opciones] ruta_al_Dockerfile
```

El parámetro “**-t**” se utiliza para etiquetar la imagen con un nombre y una etiqueta. Por ejemplo, si se desea etiquetar la imagen con el nombre “**mi\_imagen**” y la etiqueta “**v1**“, se puede usar la siguiente sintaxis:

```bash
 docker build -t mi_imagen:v1 ruta_al_Dockerfile
```

El punto (“**.**“) al final de la ruta al Dockerfile se utiliza para indicar al comando que busque el Dockerfile en el directorio actual. Si el Dockerfile no se encuentra en el directorio actual, se puede especificar la ruta completa al Dockerfile en su lugar. Por ejemplo, si el Dockerfile se encuentra en “**/home/usuario/proyecto/**“, se puede usar la siguiente sintaxis:

{% code overflow="wrap" %}
```bash
docker build -t mi_imagen:v1 /home/usuario/proyecto/
```
{% endcode %}

### docker pull

**docker pull**: es el comando que se utiliza para descargar una imagen de Docker desde un registro de imágenes (Docker hub u otros).

La sintaxis básica es la siguiente:

{% code overflow="wrap" %}
```bash
docker pull nombre_de_la_imagen:etiqueta
```
{% endcode %}

Por ejemplo, si se desea descargar la imagen “ubuntu” con la etiqueta “latest”, se puede usar la siguiente sintaxis:

{% code overflow="wrap" %}
```bash
docker pull ubuntu:latest
```
{% endcode %}

### docker images

**docker images**: es el comando que se utiliza para listar las imágenes de Docker que están disponibles en el sistema.

La sintaxis básica es la siguiente:

{% code overflow="wrap" %}
```bash
docker images [opciones]
```
{% endcode %}

Durante la construcción de la imagen, Docker descargará y almacenará en caché las capas de la imagen que se han construido previamente, lo que hace que las compilaciones posteriores sean más rápidas.

### docker run

El comando **docker run** se utiliza para crear y arrancar un contenedor a partir de una imagen. Algunas de las opciones más comunes para el comando “docker run” son:

* “**-d**” o “**–detach**“: se utiliza para arrancar el contenedor en segundo plano, en lugar de en primer plano.
* “**-i**” o “**–interactive**“: se utiliza para permitir la entrada interactiva al contenedor.
* “**-t**” o “**–tty**“: se utiliza para asignar un seudoterminal al contenedor.
* “**–name**“: se utiliza para asignar un nombre al contenedor.

Para arrancar un contenedor a partir de una imagen, se utiliza el siguiente comando:

```bash
docker run [opciones] nombre_de_la_imagen
```

Por ejemplo, si se desea arrancar un contenedor a partir de la imagen **mi\_imagen**, en segundo plano y con un seudoterminal asignado, se puede utilizar la siguiente sintaxis:

```bash
docker run -dit mi_imagen
```

### docker ps

Una vez que el contenedor está en ejecución, se puede utilizar el comando **docker ps**”para listar los contenedores que están en ejecución en el sistema. Algunas de las opciones más comunes son:

* “**-a**” o “**–all**“: se utiliza para listar todos los contenedores, incluyendo los contenedores detenidos.
* “**-q**” o “**–quiet**“: se utiliza para mostrar sólo los identificadores numéricos de los contenedores.

Por ejemplo, si se desea listar todos los contenedores que están en ejecución en el sistema, se puede utilizar la siguiente sintaxis:

```bash
docker ps -a
```

### docker exec

Para ejecutar comandos en un contenedor que ya está en ejecución, se utiliza el comando **docker exec** con diferentes opciones. Algunas de las opciones más comunes son:

* “**-i**” o “**-interactive**“: se utiliza para permitir la entrada interactiva al contenedor.
* “**-t**” o “**–tty**“: se utiliza para asignar un seudoterminal al contenedor.

Por ejemplo, si se desea ejecutar el comando “**bash**” en el contenedor con el identificador “**123456789**“, se puede utilizar la siguiente sintaxis:

```bash
docker exec -it 123456789 bash
```

### Borrar todos los contenedores

**docker rm $(docker ps -a -q) –force**: este comando se utiliza para eliminar todos los contenedores en el sistema, incluyendo los contenedores detenidos. La opción “**-q**” se utiliza para mostrar sólo los identificadores numéricos de los contenedores, y la opción “**–force**” se utiliza para forzar la eliminación de los contenedores que están en ejecución. Es importante tener en cuenta que la eliminación de todos los contenedores en el sistema puede ser peligrosa, ya que puede borrar accidentalmente contenedores importantes o datos importantes. Por lo tanto, se recomienda tener precaución al utilizar este comando.

### docker rm

**docker rm id\_contenedor**: este comando se utiliza para eliminar un contenedor específico a partir de su identificador. Es importante tener en cuenta que la eliminación de un contenedor eliminará también cualquier cambio que se haya realizado dentro del contenedor, como la instalación de paquetes o la modificación de archivos.

### docker rmi

**docker rmi $(docker images -q)**: este comando se utiliza para eliminar todas las imágenes de Docker en el sistema. La opción “**-q**” se utiliza para mostrar sólo los identificadores numéricos de las imágenes. Es importante tener en cuenta que la eliminación de todas las imágenes de Docker en el sistema puede ser peligrosa, ya que puede borrar accidentalmente imágenes importantes o datos importantes. Por lo tanto, se recomienda tener precaución al utilizar este comando.

**docker rmi id\_imagen**: este comando se utiliza para eliminar una imagen específica a partir de su identificador. Es importante tener en cuenta que la eliminación de una imagen eliminará también cualquier contenedor que se haya creado a partir de esa imagen. Si se desea eliminar una imagen que tiene contenedores en ejecución, se deben detener primero los contenedores y luego eliminar la imagen.

### otros

```bash
# Borra todas las imagenes y todos los contenedores
docker system prune -a 
```

## Referencias

{% embed url="https://docs.docker.com/get-started/02_our_app/" %}

{% embed url="https://www.redhat.com/es/topics/containers/what-is-docker" %}
