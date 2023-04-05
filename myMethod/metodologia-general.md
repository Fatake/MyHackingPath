# Metodología general

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

## Fase 1 - Reconocimiento

### 1 Footprinting&#x20;

Proceso de acumulación de información del target específico. El atacante crea un perfil en el objetivo y obtiene información útil como IP, rango de IP, namespace, nombres, usuarios, contraseñas, transferencias de DNS.

### 2 Escaneo

Proceso de identificación de host activos, puertos abiertos, servicios innecesariamente expuestos en los host. los atacantes usan diferentes tipos de escaneos para el host discovery, host port and servicies scaning, conocer la versión de sistemas operativos, evasión de firewalls, ids, dz para llegar al target objetivo. Un buen escaneo permite a un atacante identificar los posibles puntos vulnerables, si bien el escaneo de puestos, barrido de IP recaen en obtención de información, se separa este paso de footprinting ya que se considera una fase más activa durante las pruebas de intrusión.

### 3 Enumeración

Forma intrusiva, en esta parte se usan aun mas paquetes de intrusión para saber por ejemplo banner grabbing de los servicios descubiertos durante la sub fase 2 escaneo; en esta fase también se obtienen:

* listas de usuarios en la red
* tablas de routeo
* banderas de seguridad
* versiones específicas de servicios
* Simple Network Management Protol (SNMP)

La enumeración incluye conexiones activas a puertos que pueden general logs de registros, o realizar queries directas.



## Fase 2 - Explotación

Dentro de esta fase existen sub fases importantes que son las que mas se suelen repetir dentro de las pruebas de pentesting.

### Sub fase 1 - Ganando acceso

* [ ] cracking passwords
  * [ ] Con diccionarios
  * [ ] Con hash craking
  * [ ] Defauld passwords
* [ ] Explotación de vulnerabilidades.

### Sub fase 2 - Escalación de privilegios

Solo si es en entornos don escalación horizontal. Importante, si sale del alcance del acuerdo previo con el cliente, se tiene que informar sobre este tipo de ataques.

### Sub fase 3 - Manteniendo el acceso

Al igual que la sub fase 2, esta fase también se debe revisar si no se sale del alcance.

* [ ] Ejecución de comandos remotos
* [ ] Backdoors
* [ ] Escondiendo archivos

### Sub Fase  4 - Eliminación de logs

Casi nunca como un ejercicio de pentesting o red teaming se llega a esta fase, esta fase normalmente es actuar realmente como los "Malos"



## Fase 3 - Reporte

La fase mas tediosa de todas, generar un reporte con todas las vulnerabilidades y clasificarlas de acuerdo a su CVSS. ¿**Tomaste capturas de todo verdad?**

