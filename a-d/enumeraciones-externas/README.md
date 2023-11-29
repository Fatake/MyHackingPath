# Enumeraciones Externas

## Credenciales expuestas

Las credenciales expuestas en internet y en la deep web representan uno de los vectores de ataque más críticos y prevalentes que enfrentan las organizaciones hoy en día. Cuando se realizan operaciones de Red Team contra infraestructuras de Active Directory (AD), la búsqueda y el análisis de credenciales comprometidas es un aspecto crucial de la auditoría de seguridad, ya que proporciona una imagen clara de la postura de seguridad actual de la organización y destaca las posibles vías de acceso que un atacante podría explotar.

### **Importancia en el Contexto de Red Team Contra AD:**

1. **Evaluación de Riesgo Realista:** El Red Team necesita comprender cómo un atacante real podría obtener acceso a los sistemas de la organización. Las credenciales expuestas en línea son una mina de oro para los atacantes porque les permiten acceder a los sistemas sin necesidad de técnicas avanzadas de hacking.
2. **Simulación de Ataques Avanzados:** Parte de las pruebas de Red Team es simular ataques avanzados persistentes (APT). Estos ataques a menudo comienzan con el uso de credenciales filtradas encontradas en foros de la dark web o bases de datos expuestas en la internet superficial.
3. **Impacto en el AD:** Si un atacante obtiene credenciales de un usuario de AD, especialmente de un usuario con privilegios, pueden comprometer no solo la cuenta individual sino también potencialmente todo el dominio o bosque de AD, dependiendo de los privilegios asociados con esa cuenta.
4. **Prevención de Brechas de Datos:** Al identificar credenciales expuestas, las organizaciones pueden tomar medidas proactivas para prevenir brechas de datos. Esto podría incluir restablecer contraseñas, implementar autenticación multifactor (MFA) y educar a los usuarios sobre prácticas seguras de creación y manejo de contraseñas.

### **Búsqueda de Credenciales en la Deep Web:**

El Red Team debe considerar la posibilidad de que las credenciales de la organización se hayan filtrado en la deep web, un segmento de internet que no es indexado por motores de búsqueda convencionales y que es conocido por ser un mercado para el comercio de información ilegal, incluyendo credenciales robadas.

1. **Análisis Profundo:** La búsqueda de credenciales en la deep web requiere herramientas especializadas y conocimientos de cómo operan los mercados ilegales y los foros de hacking.
2. **Operaciones Encubiertas:** A menudo, esta búsqueda implica operaciones encubiertas y técnicas de inteligencia de fuentes abiertas (OSINT) para rastrear y atribuir las fugas de datos a posibles infracciones de seguridad.

### <mark style="color:red;">**Soluciones de Pago para la Búsqueda de Credenciales:**</mark>

Hay varias soluciones de pago que las organizaciones pueden utilizar para monitorear y buscar credenciales expuestas:

1. **CrowdStrike Falcon X Recon** Esta fuente de inteligencia escanea la Dark Web en busca de menciones de su marca e identificadores corporativos, como direcciones de correo electrónico en su dominio. Disponible en dos niveles de plan y entregado desde una plataforma en la nube.
2. **Echosec Beacon** comprueba la Dark Web en busca de credenciales de cuenta comprometidas e información personal y datos financieros robados.
3. **DarkOwl Vision** Un servicio de inteligencia de amenazas que incluye un escáner web oscuro como fuente de información.
4. **SpyCloud ATO Prevention Prevención** de adquisición de cuentas con una base de datos de inteligencia de amenazas derivada de escaneos de Dark Web.
5. **Digital Shadows SearchLight** Un servicio de protección de marca corporativa.
6. **¿Have I Been Pwned?** Un escaneo gratuito de la Dark Web relacionado con la dirección de correo electrónico.
