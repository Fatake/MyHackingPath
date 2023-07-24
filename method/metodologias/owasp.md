---
description: >-
  OWASP, o Open Web Application Security Project, es un conjunto de estándares y
  pautas para la seguridad de las aplicaciones web
---

# OWASP

OWASP, o Open Web Application Security Project, es un conjunto de estándares y pautas para la seguridad de las aplicaciones web. OWASP proporciona varios recursos por sí solo para mejorar la postura de seguridad de las aplicaciones web internas y externas al proporcionar a las empresas una lista completa de categorías de vulnerabilidad para aplicaciones web, así como formas de mitigarlas o remediarlas.

{% embed url="https://owasp.org/www-project-web-security-testing-guide/" %}

1. Information Gathering: En esta fase, se recopila información sobre la aplicación web que será evaluada. Esto puede incluir identificar las URL, páginas, tecnologías utilizadas, posibles puntos de entrada y cualquier otra información relevante.
2. Threat Modeling: En esta etapa, se analiza la arquitectura y el diseño de la aplicación web para identificar posibles amenazas y vulnerabilidades. Se realiza un análisis de riesgos para priorizar los aspectos que deben ser evaluados con mayor detenimiento.
3. Configuration and Deployment Management Testing: En esta fase, se verifica la configuración del servidor web, la base de datos y otros componentes relacionados con la implementación de la aplicación web. Se busca configuraciones inseguras o malas prácticas que puedan ser explotadas.
4. Identity Management Testing: Aquí se evalúa cómo la aplicación maneja la autenticación, la autorización y la gestión de identidades. Se busca identificar vulnerabilidades que puedan permitir el acceso no autorizado o la usurpación de identidad.
5. Authentication Testing: En esta etapa, se evalúan los mecanismos de autenticación utilizados por la aplicación web. Se busca identificar vulnerabilidades como contraseñas débiles, problemas de sesión, etc.
6. Authorization Testing: Se centra en evaluar cómo la aplicación web controla el acceso a los recursos y funcionalidades. Se busca identificar posibles problemas de autorización que puedan permitir a usuarios no autorizados realizar acciones privilegiadas.
7. Session Management Testing: En esta fase, se examina cómo la aplicación maneja las sesiones de usuario. Se busca identificar problemas como sesiones sin expirar, tokens de sesión predecibles, entre otros.
8. Input Validation Testing: Se evalúa cómo la aplicación web valida y procesa la entrada del usuario. El objetivo es identificar vulnerabilidades de inyección, como SQL injection, XSS (Cross-Site Scripting), etc.
9. Error Handling: En esta etapa, se evalúa cómo la aplicación maneja los errores y las excepciones. Se busca identificar posibles fugas de información sensible o detalles técnicos que podrían ser útiles para los atacantes.
10. Cryptography: Aquí se evalúa el uso y la implementación de técnicas criptográficas en la aplicación web. Se busca identificar debilidades en el cifrado y la protección de datos confidenciales.
11. Business Logic Testing: Esta fase se enfoca en evaluar la lógica de negocio de la aplicación web. Se busca identificar posibles vulnerabilidades o manipulaciones que puedan afectar la integridad o la confidencialidad de los datos.
12. Client-Side Testing: En esta etapa, se evalúa el código y las funcionalidades que se ejecutan en el lado del cliente (navegador). Se busca identificar vulnerabilidades como XSS, CSRF (Cross-Site Request Forgery), etc.
13. API Testing: Aquí se evalúan las interfaces de programación de aplicaciones (API) utilizadas por la aplicación web. Se busca identificar posibles problemas de seguridad en la interacción con otras aplicaciones o servicios.
14. Reporting: Finalmente, se documentan todos los hallazgos y las recomendaciones para mejorar la seguridad de la aplicación web. Se proporciona un informe detallado que incluye información técnica y orientación para abordar los problemas identificados.





