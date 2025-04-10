# Kali Set Up

Comandos iniciales para una instalación limpia de Kali Linux

## Actualizar

{% code overflow="wrap" %}
```bash
apt update && apt upgrade -y && apt full-upgrade -y && apt autoremove -y
```
{% endcode %}

### Instalar Paquetes





### One Liner

{% code overflow="wrap" %}
```bash
apt update && apt upgrade -y && apt full-upgrade -y && apt autoremove -y; sudo git clone --recursive https://github.com/Fatake/autonmap.git /opt/autonmap; sudo chown -R kali:kali /opt/autonmap; sudo git clone https://github.com/Fatake/autoffuf.git /opt/autoffuf; sudo chown -R kali:kali /opt/autoffuf; sudo git clone https://github.com/Fatake/autoWebPentest.git /opt/autoWebPentest; sudo sudo chown -R kali:kali /opt/autoWebPentest; sudo git clone --recursive https://github.com/testssl/testssl.sh.git /opt/testssl; sudo chown -R kali:kali /opt/testssl; sudo wget -O /usr/share/wordlists/fuzz.txt https://raw.githubusercontent.com/Bo0oM/fuzz.txt/master/fuzz.txt; sudo apt install seclists ffuf nuclei whatweb nxc golang-go peass sliver chisel neo4j bloodhound nishang backdoor-factory pipx enum4linux-ng exiftool openvpn -y; nuclei -update-templates
```
{% endcode %}

