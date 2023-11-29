# Enumeración manual

La enumeración es el proceso de extraer información de Active Directory, como enumerar los usuarios, grupos, algunos campos y recursos interesantes.

Active Directory (AD) es una base de datos y un conjunto de servicios que conectan usuarios con los recursos de red que necesitan para hacer su trabajo. Aquí están algunas de las funcionalidades más importantes del Active Directory y cómo acceder a ellas a través del comando "Ejecutar" en Windows:

1. <mark style="color:green;">**Usuarios y Equipos de Active Directory (ADUC)**</mark>** - `dsa.msc`**
   * **Acceso:** Escribe `dsa.msc` en el comando "Ejecutar".
   * **Función:** Permite administrar cuentas de usuarios, grupos, y equipos. Puedes crear y administrar objetos de usuario, resetear contraseñas, y organizar estos objetos en unidades organizativas (OUs).
2. <mark style="color:green;">**Política de Grupo (GPMC)**</mark>** - `gpmc.msc`**
   * **Acceso:** Escribe `gpmc.msc` en el comando "Ejecutar".
   * **Función:** Administra y configura las políticas de grupo (GPOs) para usuarios y computadoras dentro de un dominio. Permite centralizar la gestión de configuraciones de seguridad y comportamientos de usuario/equipo.
3. <mark style="color:green;">**Sitios y Servicios de Active Directory**</mark>** - `dssite.msc`**
   * **Acceso:** Escribe `dssite.msc` en el comando "Ejecutar".
   * **Función:** Administra los sitios de AD y los servicios asociados a ellos, incluyendo la replicación de AD y la topología de la red.
4. <mark style="color:green;">**Dominios y Confianzas de Active Directory**</mark>** - `domain.msc`**
   * **Acceso:** Escribe `domain.msc` en el comando "Ejecutar".
   * **Función:** Gestiona los dominios de AD y las relaciones de confianza entre dominios y bosques.
5. <mark style="color:green;">**Centro de administración de Active Directory**</mark>** - `dsac.exe`**
   * **Acceso:** Escribe `dsac.exe` en el comando "Ejecutar".
   * **Función:** Es una interfaz gráfica moderna para administrar AD que combina las funcionalidades de varias otras herramientas de MMC.
6. <mark style="color:green;">**DNS**</mark>** - `dnsmgmt.msc`**
   * **Acceso:** Escribe `dnsmgmt.msc` en el comando "Ejecutar".
   * **Función:** Administra el servidor de DNS del dominio, lo cual es crucial para la funcionalidad de AD, ya que AD depende en gran medida de una configuración de DNS correcta para resolver nombres y localizar servicios y servidores.

### **Otras funcionalidades esenciales de Active Directory incluyen:**

7. <mark style="color:green;">**Control de Servicios**</mark>** - `services.msc`**
   * **Acceso:** Escribe `services.msc` en el comando "Ejecutar".
   * **Función:** Gestiona los servicios en ejecución en un servidor, lo cual incluye los servicios de Active Directory como el servicio de Directorio de Active Directory (NTDS) o el servicio de replicación de File Replication Service (FRS) o Distributed File System (DFS).
8. <mark style="color:green;">**Visor de Eventos**</mark> **- `eventvwr.msc`**
   * **Acceso:** Escribe `eventvwr.msc` en el comando "Ejecutar".
   * **Función:** Permite a los administradores ver eventos y registros de seguridad, lo cual es esencial para el monitoreo del rendimiento y la seguridad del AD.
9. <mark style="color:green;">**Administrador de DHCP**</mark>** - `dhcpmgmt.msc`**
   * **Acceso:** Escribe `dhcpmgmt.msc` en el comando "Ejecutar".
   * **Función:** Administra el protocolo de configuración dinámica de host (DHCP) que asigna direcciones IP a dispositivos en la red.
10. <mark style="color:green;">**Administrador de Certificados**</mark>** - `certmgr.msc`**
    * **Acceso:** Escribe `certmgr.msc` en el comando "Ejecutar".
    * **Función:** Gestiona certificados para garantizar la seguridad en la comunicación entre servidores y clientes dentro del dominio.
11. <mark style="color:green;">**Conector de Almacenamiento de Información de Internet (IIS)**</mark> **- `inetmgr`**
    * **Acceso:** Escribe `inetmgr` en el comando "Ejecutar".
    * **Función:** Gestiona las configuraciones de servidores web que son importantes si estás utilizando servicios web en tu infraestructura de AD.
12. <mark style="color:green;">**Servicios de Escritorio Remoto**</mark> **- `tsadmin.msc`**
    * **Acceso:** Escribe `tsadmin.msc` en el comando "Ejecutar" (solo disponible en versiones anteriores de Windows Server
