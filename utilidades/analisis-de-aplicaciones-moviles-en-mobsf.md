# Análisis de Aplicaciones Móviles en MobSF

## Instalación&#x20;

* **MacOS**
  * Instalar [Git](https://www.atlassian.com/git/tutorials/install-git).
  * Instalar [Python **3.8 o 3.9**](https://www.python.org/).
  * Después de instalar Python 3.8+: Se debe de ir a `/Applications/Python 3.8/` y ejecutar `Update Shell Profile.command`, después ejecutar `Install Certificates.command`.
  * Instalar el [JDK 8+](https://www3.ntu.edu.sg/home/ehchua/programming/howto/JDK\_Howto.html).
  * Instalar las command line tools con el comando `xcode-select --install`.
  * Descargar e instalar [wkhtmltopdf](https://wkhtmltopdf.org/downloads.html) como se indica en las [instrucciónes](https://github.com/JazzCore/python-pdfkit/wiki/Installing-wkhtmltopdf).
  * Para realizar un análisis estático de una aplicación de Windows se necesita una computadora con Windows o una máquina virtual en Mac o Linux. [Más información](https://github.com/MobSF/Mobile-Security-Framework-MobSF/blob/master/mobsf/install/windows/readme.md)

```
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
./setup.sh./run.sh
```

* **Distribuciones de Linux basadas en Ubuntu/Debian**:
  * Instalar Git `sudo apt-get install git`.
  * Instalar Python **3.8 o 3.9** `sudo apt-get install python3.8`.
  * Instalar el JDK 8+ `sudo apt-get install openjdk-8-jdk`.
  * Instalar las siguientes dependencias:

```
sudo apt install python3-dev python3-venv python3-pip build-essential libffi-dev libssl-dev libxml2-dev libxslt1-dev libjpeg8-dev zlib1g-dev wkhtmltopdf
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
./setup.sh./run.sh
```

* Para realizar un análisis estático de una aplicación de Windows se necesita una computadora con Windows o una máquina virtual en Mac o Linux. [Más información](https://github.com/MobSF/Mobile-Security-Framework-MobSF/blob/master/mobsf/install/windows/readme.md).\
  \

* **Windows**
  * Instalar [Git](https://git-scm.com/download/win).
  * Instalar [Python **3.8 o 3.9**](https://www.python.org/).
  * Instalar [JDK 8+](https://www3.ntu.edu.sg/home/ehchua/programming/howto/JDK\_Howto.html).
  * Instalar [Microsoft Visual C++ Build Tools](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools\&rel=16).
  * Instalar [OpenSSL (non-light)](https://slproweb.com/products/Win32OpenSSL.html).
  * Descargar e instalar [wkhtmltopdf](https://wkhtmltopdf.org/downloads.html) como se indica en las [instrucciónes](https://github.com/JazzCore/python-pdfkit/wiki/Installing-wkhtmltopdf).
  * Añadir el directorio que contiene el binario de `wkhtmltopdf` a la variable de ambiente `PATH`

```
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git

cd Mobile-Security-Framework-MobSF
setup.batrun.bat
```



* **Docker Sin Persistencia de datos**

> docker pull opensecurity/mobile-security-framework-mobsf\
> docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest



* **Docker con Persistencia**

> mkdir NuevoDirectorio\
> chown 9901:9901 NuevoDirectorio\
> docker run -it --rm --name mobsf -p 8000:8000 -v NuevoDirectorio:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest
