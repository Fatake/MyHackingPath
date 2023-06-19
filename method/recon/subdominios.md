# SubDominios

La enumeración de **subdominios** es una de las fases cruciales en la seguridad informática para identificar los subdominios asociados a un dominio principal.

Los subdominios son parte de un dominio más grande y a menudo están configurados para apuntar a diferentes recursos de la red, como servidores web, servidores de correo electrónico, sistemas de bases de datos, sistemas de gestión de contenido, entre otros.

Al identificar los subdominios vinculados a un dominio principal, un atacante podría obtener información valiosa para cada uno de estos, lo que le podría llevar a encontrar **vectores de ataque potenciales**. Por ejemplo, si se identifica un subdominio que apunta a un servidor web vulnerable, el atacante podría utilizar esta información para intentar explotar la vulnerabilidad y acceder al servidor en cuestión.

Existen diferentes herramientas y técnicas para la enumeración de subdominios, tanto pasivas como activas. Las **herramientas** **pasivas** permiten obtener información sobre los subdominios sin enviar ninguna solicitud a los servidores identificados, mientras que las **herramientas activas** envían solicitudes a los servidores identificados para encontrar subdominios bajo el dominio principal.

Algunas de las **herramientas pasivas** más utilizadas para la enumeración de subdominios incluyen la búsqueda en motores de búsqueda como Google, Bing o Yahoo, y la búsqueda en registros DNS públicos como **PassiveTotal** o **Censys**. Estas herramientas permiten identificar subdominios asociados con un dominio, aunque no siempre son exhaustivas. Además, existen herramientas como **CTFR** que utilizan registros de certificados **SSL/TLS** para encontrar subdominios asociados a un dominio.

También se pueden utilizar páginas online como **Phonebook.cz** e **Intelx.io**, o herramientas como **sublist3r**, para buscar información relacionada con los dominios, incluyendo subdominios.

Por otro lado, las **herramientas activas** para la enumeración de subdominios incluyen herramientas de fuzzing como **wfuzz** o **gobuster**. Estas herramientas envían solicitudes a los servidores mediante ataques de fuerza bruta, con el objetivo de encontrar subdominios válidos bajo el dominio principal.



## Pasivo

* **Phonebook** (Herramienta pasiva): [https://phonebook.cz/](https://phonebook.cz/)
* **Intelx** (Herramienta pasiva): [https://intelx.io/](https://intelx.io/)
* **CTFR** (Herramienta pasiva): [https://github.com/UnaPibaGeek/ctfr](https://github.com/UnaPibaGeek/ctfr)
* **Gobuster** (Herramienta activa): [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)
* **Wfuzz** (Herramienta activa): [https://github.com/xmendez/wfuzz](https://github.com/xmendez/wfuzz)
* **Sublist3r** (Herramienta pasiva): [https://github.com/aboul3la/Sublist3r](https://github.com/aboul3la/Sublist3r)

## Activo

### DNSRECON Para Dominios

{% code overflow="wrap" %}
```bash
dnsrecon -d --disable_check_bindversion $target-t std -c ${target}_dnsrecon_stdrecon_.csv
```
{% endcode %}

{% code overflow="wrap" %}
```bash
dnsrecon -d $target-t axfr -c ${target}_dnsrecon_axfr_.csv 
```
{% endcode %}

{% code overflow="wrap" %}
```bash
dnsrecon -d $target-t zonewalk -c ${target}_dnsrecon_crt_.csv 
```
{% endcode %}

### AMASS <a href="#amass" id="amass"></a>

Cuidado con generar DNS Brute force

```bash
amass enum -norecursive -noalts -d $target
amass enum -norecursive -noalts -d $target >> pentest/foundsubdomains.txt
amass enum -brute -w AuditScrips/WorldList/DNS_plussFinancial.dic-d $target >> pentest/foundsubdomains.txt 
amass enum -brute -w custom_dictionary.lst -d $d >> foundsubdomains.txt
amass enum -brute -w /opt/rlyeh/dictionaries/DNS_allsubdomains.dic -d $d >> pentest/foundsubdomains.txt
```



### Dig <a href="#dig" id="dig"></a>

```
dig +multi AXFR @ns1.insecuredns.com insecuredns.com
```



### Más Herramientas <a href="#m-c3-a1s-herramientas" id="m-c3-a1s-herramientas"></a>

{% code overflow="wrap" %}
```bash
subfinder -d $TARGET >> domains; 
assetfinder -subs-only $TARGET >> domains;
amass enum -norecursive -noalts -d $TARGET >> domains;

dnsrecon.py -n ns1.insecuredns.com -d insecuredns.com -D subdomains-top1mil-5000.txt -t brt

ldns-walk @ns1.insecuredns.com insecuredns.comFinally:

altdns.py -i icann.domains -o data_output -w icann.words -r -s results_output.txt

altdns.py -i icann.domains -o data_output -w icann.words -r -s results_output.txt
```
{% endcode %}



#### Tomar el Subdominio <a href="#tomar-el-subdominio" id="tomar-el-subdominio"></a>

{% code overflow="wrap" %}
```
subjack -w domains -t 100 -timeout 30 -ssl -c ~/HAHWUL/tool/subjack/fingerprints.json -v 3 >> takeover ;
```
{% endcode %}



## Recursos Externos: <a href="#recursos" id="recursos"></a>

```
https://github.com/tomnomnom/httprobe
https://github.com/projectdiscovery/subfinder
https://github.com/tomnomnom/assetfinder
https://github.com/OWASP/Amass
https://github.com/darkoperator/dnsrecon
https://github.com/infosec-au/altdns
https://github.com/NLnetLabs/ldns 
https://github.com/blechschmidt/massdns
https://gist.github.com/jhaddix/f64c97d0863a78454e44c2f7119c2a6a
```



