# Metodología Propia

Basada en todas las metodologías listadas en este sitio y con el paso del tiempo y mi experiencia en las evaluaciones de ciberseguridad como consultor de pentesting, en este apartado se describe la metodología personal.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

## Fase 1 - Reconocimiento

### Reconocimiento pasivo

#### Footprinting

Proceso de acumulación de información del target específico. El atacante crea un perfil en el objetivo y obtiene información útil como IP, rango de IP, namespace, nombres, usuarios, contraseñas, transferencias de DNS.

### Reconocimiento Activo

#### Escaneo

Proceso de identificación de host activos, puertos abiertos, servicios innecesariamente expuestos en los host. los atacantes usan diferentes tipos de escaneos para el host discovery, host port and servicies scaning, conocer la versión de sistemas operativos, evasión de firewalls, ids, dz para llegar al target objetivo. Un buen escaneo permite a un atacante identificar los posibles puntos vulnerables, si bien el escaneo de puestos, barrido de IP recaen en obtención de información, se separa este paso de footprinting ya que se considera una fase más activa durante las pruebas de intrusión.

#### Enumeración

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

Escalación horizontal. Importante, si sale del alcance del acuerdo previo con el cliente, se tiene que informar sobre este tipo de ataques.

### Sub fase 3 - Manteniendo el acceso

Al igual que la sub fase 2, esta fase también se debe revisar si no se sale del alcance.

* [ ] Ejecución de comandos remotos
* [ ] Backdoors
* [ ] Escondiendo archivos

### Sub Fase 4 - Eliminación de logs

Casi nunca como un ejercicio de pentesting o red teaming se llega a esta fase, esta fase normalmente es actuar realmente como los "Malos"

## Fase 3 - Post explotación

Pivoting, habiendo obtenido escalación de privilegios sean horizontales o verticales se vuelve a empezar desde la fase 1.

Backdoor, persistencia

## Fase 4 - Reporte

La fase mas tediosa de todas, generar un reporte con todas las vulnerabilidades y clasificarlas de acuerdo a su CVSS. <mark style="color:green;">**¿Tomaste capturas de todo verdad?**</mark>

Por lo general, se entrega un informe dentro de una semana después de la la finalización del proyecto. El informe debe destacar tanto los hallazgos no técnicos (ejecutivos) como los técnicos. Las recomendaciones para la remediación deben ser claras tanto para los ejecutivos como para el personal técnico. A veces se requiere el uso de una presentación ejecutiva, donde sean presentaciones de diapositivas para explicar de forma resumida los hallazgos, el donde está la empresa auditada y como solucionar los problemas, estas diapositivas deben estar enfocadas para personas no tan técnicas.

### Debrief

El proceso de briefing repasa las conclusiones del informe con el cliente. Puede contar con la presencia de personal técnico y no técnico. El cliente tiene así la oportunidad de plantear preguntas y resolver dudas antes de que se publique el informe final.
