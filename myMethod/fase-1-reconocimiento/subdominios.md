# SubDominios

### DNSRECON Para Dominios

```bash
dnsrecon -d --disable_check_bindversion $target-t std -c ${target}_dnsrecon_stdrecon_.csv
```

```bash
dnsrecon -d $target-t axfr -c ${target}_dnsrecon_axfr_.csv 
```

```bash
dnsrecon -d $target-t zonewalk -c ${target}_dnsrecon_crt_.csv 
```



## AMASS <a href="#amass" id="amass"></a>

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

```
!subfinder -d $TARGET >> domains; 
```

```
!assetfinder -subs-only $TARGET >> domains;
```

```
!amass enum -norecursive -noalts -d $TARGET >> domains;
```

```
!dnsrecon.py -n ns1.insecuredns.com -d insecuredns.com -D subdomains-top1mil-5000.txt -t brt
```

```
!ldns-walk @ns1.insecuredns.com insecuredns.comFinally:
```

```
altdns.py -i icann.domains -o data_output -w icann.words -r -s results_output.txt
```

### Tomar el Subdominio <a href="#tomar-el-subdominio" id="tomar-el-subdominio"></a>

```
subjack -w domains -t 100 -timeout 30 -ssl -c ~/HAHWUL/tool/subjack/fingerprints.json -v 3 >> takeover ;
```













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
