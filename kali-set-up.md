# Kali Set Up

Comandos iniciales para una instalación limpia de Kali Linux

## Actualizar

{% code overflow="wrap" %}
```bash
apt update && apt upgrade -y && apt full-upgrade -y && apt autoremove -y
```
{% endcode %}

El siguiente script instala todos los paquetes que normalmente ocupo durante un assessment de pentesting en una instalación limpia de kali linux.

### One Liner

{% code overflow="wrap" %}
```bash
sudo git clone --recursive https://github.com/Fatake/autonmap.git /opt/autonmap; sudo chown -R kali:kali /opt/autonmap; sudo git clone https://github.com/Fatake/autoffuf.git /opt/autoffuf; sudo chown -R kali:kali /opt/autoffuf; sudo git clone https://github.com/Fatake/autoWebPentest.git /opt/autoWebPentest; sudo sudo chown -R kali:kali /opt/autoWebPentest; sudo git clone --recursive https://github.com/testssl/testssl.sh.git /opt/testssl; sudo chown -R kali:kali /opt/testssl; sudo wget -O /usr/share/wordlists/fuzz.txt https://raw.githubusercontent.com/Bo0oM/fuzz.txt/master/fuzz.txt; wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg; sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg; echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" |sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null; rm -f packages.microsoft.gpg; sudo apt install apt-transport-https -y; sudo apt update -y ; sudo apt install code -y; sudo apt install seclists ffuf nuclei whatweb netexec golang-go peass sliver chisel neo4j bloodhound nishang backdoor-factory pipx mimikatz gpg powercat windows-binaries enum4linux-ng exiftool kali-tools-post-exploitation kali-tools-exploitation openvpn -y; nuclei -update-templates; searchsploit --update; sudo msfdb init && sudo msfdb stop
```
{% endcode %}

