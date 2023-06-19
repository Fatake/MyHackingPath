# Escaneo de Puertos

### Descubrimiento de Host <a href="#descubrimiento-de-host" id="descubrimiento-de-host"></a>

{% code overflow="wrap" %}
```bash
nmap $TARGET -sn -PE -PP -PM -PS80,443,22,3389,1723,8080,3306,135,53,143,139,445,110,25,21,23,5432,27017,1521 -PU139,53,67,135,445,1434,138,123,137,161,631  -oA ResultadosNmap/nmap_HostDiscovery

# Tambien es importante checar si estas en una sub net /25 por ejemplo
# Buscar otras rede /16 validado si existe segmentación o no

# arp-scan solo funciona si estás en físico o una máquina pivoting
arp-scan -I <Interface> --ignoredups
netdiscover # Esta herramienta hace mucho ruido
ping -c 1 <target>

## masscan genera mucho ruido y cuanto mas rápido se pierden paquetes
masscan -p21,22,139,445 -Pn HostRange/16 --rate=10000


# Por archivos descriptores TCP linux
echo '' > /dev/tcp/ip/puerto y checar si el el código de error echo $? es diferente de 0
```
{% endcode %}

### Lista para Escaneo Avanzado <a href="#lista-para-escaneo-avanzado" id="lista-para-escaneo-avanzado"></a>

{% code overflow="wrap" %}
```
!cat ResultadosNmap/nmap_HostDiscovery.gnmap | grep "Status: Up" | cut -d " " -f 2 > ResultadosNmap/iL_Alive.lst;
```
{% endcode %}

### Carpetas por IP encontrada <a href="#carpetas-por-ip-encontrada" id="carpetas-por-ip-encontrada"></a>

```
!mkdir `cat ResultadosNmap/iL_Alive.lst`
```

### HTTP & HTTPS <a href="#http-and-https" id="http-and-https"></a>

{% code overflow="wrap" %}
```
!cat ResultadosNmap/iL_Alive.lst | ~/go/bin/httprobe | tee -a ResultadosNmap/alive.txt
```
{% endcode %}

### Abre URL's encontradas <a href="#abre-urls-encontradas" id="abre-urls-encontradas"></a>

{% code overflow="wrap" %}
```
!for URL in `cat ResultadosNmap/alive.txt`; do firefox $URL; done
```
{% endcode %}

### TCP <a href="#tcp" id="tcp"></a>

{% code overflow="wrap" %}
```bash
!nmap -Pn -sS -p T:1-65535 --max-retries 2 --min-rtt-timeout 500ms --max-rtt-timeout 2000ms --initial-rtt-timeout 750ms --defeat-rst-ratelimit --min-rate 800 --max-rate 5000 --disable-arp-ping -oA ResultadosNmap/nmap_SYNScan -iL ResultadosNmap/iL_Alive.lst -v -T4
```
{% endcode %}

### UDP <a href="#udp" id="udp"></a>

{% code overflow="wrap" %}
```bash
!nmap -Pn -sU -p U:7,9,11,13,17,19,37,49,53,67-69,80,88,111,120,123,135-139,158,161-162,177,213,259-260,427,443,445,464,497,500,514-515,518,520,523,593,623,626,631,749-751,996-999,1022-1023,1025-1030,1194,1433-1434,1645-1646,1701,1718-1719,1812-1813,1900,2000,2048-2049,2222-2223,2746,3230-3235,3283,3401,3456,3703,4045,4444,4500,4665-4666,4672,5000,5059-5061,5351,5353,5632,6429,7777,8888,9100-9102,9200,10000,17185,18233,20031,23945,26000-26004,26198,27015-27030,27444,27960-27964,30718,30720-30724,31337,32768-32769,32771,32815,33281,34555,44400,47545,49152-49154,49156,49181-49182,49186,49190-49194,49200-49201,49211,54321,65024 --max-retries 1 --min-rtt-timeout 500ms --max-rtt-timeout 2000ms --initial-rtt-timeout 750ms  --min-rate 800 --max-rate 5000 --disable-arp-ping -oA ResultadosNmap/nmap_UDPScan -iL ResultadosNmap/iL_Alive.lst -v -T4
```
{% endcode %}

### Puertos Abiertos <a href="#puertos-abiertos" id="puertos-abiertos"></a>

{% code overflow="wrap" %}
```
TCPPORTS=!(cat ResultadosNmap/nmap_SYNScan.gnmap | awk -F " " '{ s = ""; for (i = 4; i <= NF; i++) s = s $i " "; print s }' | tr ", " "\n" | grep open | grep tcp | cut -d "/" -f 1 | sort -nu | paste -s -d, - );
```
{% endcode %}

{% code overflow="wrap" %}
```
UDPPORTS=!(cat ResultadosNmap/nmap_UDPScan.gnmap | awk -F " " '{ s = ""; for (i = 4; i <= NF; i++) s = s $i " "; print s }' | tr ", " "\n" | grep open | grep udp | cut -d "/" -f 1 | sort -nu | paste -s -d, - );
```
{% endcode %}

### Escaneo Completo <a href="#escaneo-completo" id="escaneo-completo"></a>

{% code overflow="wrap" %}
```
!nmap $TARGET -v -sT -sCV -O -A -p T:22,80,443,$TCPPORTS,U:137,161,$UDPPORTS -oA ResultadosNmap/nmap_FULLScan
```
{% endcode %}

## Herramienta autiomatizada

{% embed url="https://github.com/Fatake/autonmap" %}
autonmap script
{% endembed %}

![](../../.gitbook/assets/20220616\_095155.png)
