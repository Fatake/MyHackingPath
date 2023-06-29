# ¿Qué es MITRE ATT\&CK?

## ¿Qué es ATT\&CK?

La organización estadounidense sin ánimo de lucro llamada MITRE creo el marco de trabajo llamado ATT\&CK (**A**dversarial **T**actics, **T**echniques, and **C**ommon **K**nowledge por sus siglas en inglés o Tácticas, Técnicas y Conocimientos Comunes del Adversario en español), que es una base de conocimientos gratuita y abierta de información sobre ciberseguridad y un modelo para el comportamiento de los ciberadversarios que refleja las distinas fases del “ciclo de vida” de un atacante y las plataformas que se sabe que se tiene como objetivo ATT\&CK está diseñado para ayudar a los analistas de ciberseguridad y otras partes interesadas a obtener conocimientos de inteligencia sobre amenazas cibernéticas (Cyber Threat Intelligence CTI) para planificar y diseñar programas de ciberseguridad para facilitar la comunicación proporcionando un vocabulario común de referencia de ciberseguridad.

La metodología ATT\&CK hace uso de la abstracción de Tácticas, Técnicas y Procedimientos (Tactics, Techniques, and Procedures TTP) que proporcionan una taxonomía común de las acciones individuales de los adversarios o atacantes entendida por los lados ofensivos y defensivos de la ciberseguridad. También proporciona un nivel adecuado de categorización para las acciones de los adversarios y formas específicas de defenderse contra ellas organizando los conocimientos de ciberseguridad en un marco jerárquico.

Cada Táctica contiene múltiples Técnicas, cada una de las cuales define un método estratégico para lograr el objetivo táctico. El nivel jerárquico más bajo del marco ATT\&CK incluye procedimientos detallados para cada técnica como: las herramientas, protocolos y piezas de malware observadas en ciberataques del mundo real.

El modelo de comportamiento presentado por ATT\&CK contiene los siguientes componentes básicos:

·         Tácticas que denotan los objetivos tácticos a corto plazo del adversario durante un ataque (las columnas);

·         Técnicas que describen los medios por los que los adversarios logran los objetivos tácticos (las celdas individuales); y

·         Uso de Matrices de las técnicas por parte del adversario y otros metadatos.

Tipos de Matrices

·         Enterprise: La matriz contiene información para las siguientes plataformas: Windows, macOS, Linux, PRE, Azure AD, Office 365, Google Workspace, SaaS, IaaS, Network, Containers.

·         Mobile: Matriz enfocada en las plataformas móviles donde se cubren técnicas que implican el acceso al dispositivo y efectos basados en la red que pueden ser utilizados por los adversarios sin acceso al dispositivo. La Matriz contiene información para las siguientes plataformas: Android, iOS.

·         ICS: Matriz diseñada para sistemas de control industrial (ICS), parecida a la enterprise, pero con la diferencia de que está dirigida específicamente a los sistemas de control industrial, como redes eléctricas, fábricas, factorías y otras organizaciones que dependen de maquinaria, dispositivos, sensores y redes interconectados.

Cada una de estas matrices está basada en la Metodología de Martin Lockhedd (2011) llamada Cyber Kill Chain, que fue uno s de los primeros intentos para explicar el flujo de un ataque. Este framewors contiene 7 niveles de objetivos y técnicas que un adversario hace durante un ataque:}

1\.       Reconnaissance: Reconocimiento

2\.       Weaponization: Armamento

3\.       Delivery: Entrega

4\.       Exploitation: Explotación

5\.       Installation: Instalación

6\.       Command and Control (C2): Mando y control

7\.       Actions on Objectives: Acciones sobre los objetivos

Con base a estas 7 etapas MITRE desarrolla sus Tácticas para cada matriz las cuales son:

* Reconnaissance (Enterprise, ICS): Reconocimiento (Empresa, ICS)
* Resource Development (Enterprise, ICS): Desarrollo de recursos (empresa, ICS)
* Initial Access: Acceso inicial
* Execution: Ejecución
* Persistence: Persistencia
* Privilege Escalation: Escalada de privilegios
* Defense Evasion: Evasión de la defensa
* Credential Access (Enterprise, Mobile): Acceso a credenciales (empresa, móvil)
* Discovery: Descubrimiento
* Lateral Movement: Movimiento lateral
* Collection: Recogida
* Command and Control: Mando y control
* Exfiltration (Enterprise, Mobile): Exfiltración (empresa, móvil)
* Impact : Impacto
* Network Effects (Mobile-only): Efectos en la red (sólo móvil)
* Network Service Effects (Mobile-only): Efectos del servicio de red (sólo móvil)
* Inhibit Response Function (ICS-only): Inhibir la función de respuesta (sólo ICS)
* Impair Process Control (ICS-only): Deterioro del control de procesos (sólo ICS)

&#x20;

### Referencias

1\.       “Cyber Kill Chain®,” Lockheed Martin, Jun. 29, 2022. https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html (accessed Dec. 01, 2022).

2\.       “MITRE ATT\&CK vs Cyber Kill Chain,” Blackberry.com, 2022. https://www.blackberry.com/us/en/solutions/endpoint-security/mitre-attack/mitre-attack-vs-cyber-kill-chain (accessed Dec. 01, 2022).

3\.       “Trellix,” Trellix.com, 2022. https://www.trellix.com/en-us/security-awareness/cybersecurity/what-is-mitre-attack-framework.html (accessed Dec. 01, 2022).

4\.       “What Is MITRE ATT CK - Definition | VMware Glossary,” VMware, Nov. 25, 2022. https://www.vmware.com/es/topics/glossary/content/mitre-attack.html (accessed Dec. 01, 2022).

5\.       “Matrix - Enterprise | MITRE ATT\&CK®,” Mitre.org, 2022. https://attack.mitre.org/matrices/enterprise/ (accessed Dec. 01, 2022).

6\.       “¿Qué es ATT\&CK de MITRE y cuál es su utilidad?,” Anomali.com, 2013. https://www.anomali.com/es/resources/what-mitre-attck-is-and-how-it-is-useful (accessed Dec. 01, 2022).

7\.       “Getting Started | MITRE ATT\&CK®,” Mitre.org, 2018. https://attack.mitre.org/resources/getting-started/ (accessed Dec. 01, 2022).
