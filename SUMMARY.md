# Table of contents

## 👓 Wellcome

* [My Personal Hacking Path](README.md)
  * [Whoami](wellcome/readme/whoami.md)

## 📚 Mi Metodología de Pentesting <a href="#myMethod" id="myMethod"></a>

* [Tipos de evaluaciones](myMethod/tipos-de-evaluaciones/README.md)
  * [El día a día en pentesting](myMethod/tipos-de-evaluaciones/el-dia-a-dia-en-pentesting.md)
* [Metodología general](myMethod/metodologia-general.md)
* [Fase 1 - Reconocimiento](myMethod/fase-1-reconocimiento/README.md)
  * [Web / Host](myMethod/fase-1-reconocimiento/web-host.md)
  * [SubDominios](myMethod/fase-1-reconocimiento/subdominios.md)
  * [Descubrimiento de correos electrónicos](myMethod/fase-1-reconocimiento/descubrimiento-de-correos-electronicos.md)
  * [Escaneo de Puertos](myMethod/fase-1-reconocimiento/escaneo-de-puertos.md)
  * [¿Sin Ideas?](myMethod/fase-1-reconocimiento/sin-ideas.md)
* [Fase 2 - Explotación](myMethod/fase-2-explotacion/README.md)
  * [Shells](myMethod/fase-2-explotacion/shells.md)
  * [Network Penetration Services](myMethod/fase-2-explotacion/network-penetration-services/README.md)
    * [20/21 - FTP](myMethod/fase-2-explotacion/network-penetration-services/20-21-ftp.md)
    * [443 - SMB](myMethod/fase-2-explotacion/network-penetration-services/443-smb.md)

## 👾 Malware

* [Malware DevOps](malware/malware-devops.md)
* [Arquitectura de Windows](malware/arquitectura-de-windows/README.md)
  * [Windows API](malware/arquitectura-de-windows/windows-api.md)
  * [Procesos de Windows](malware/arquitectura-de-windows/procesos-de-windows.md)
  * [Estructuras no documentadas](malware/arquitectura-de-windows/estructuras-no-documentadas.md)
* [PE](malware/pe/README.md)
  * [DLL](malware/pe/dll.md)
* [Almacenamiento del Payload](malware/almacenamiento-del-payload/README.md)
  * [.data & .rdata](malware/almacenamiento-del-payload/.data-and-.rdata.md)
  * [.text](malware/almacenamiento-del-payload/.text.md)
  * [.rsrc](malware/almacenamiento-del-payload/.rsrc.md)
* [Cifrado de Payload](malware/cifrado-de-payload/README.md)
  * [XOR](malware/cifrado-de-payload/xor.md)
  * [RC4](malware/cifrado-de-payload/rc4.md)
  * [AES](malware/cifrado-de-payload/aes.md)
* [Ofuscación de Payload](malware/ofuscacion-de-payload/README.md)
  * [IPv4/IPv6Fuscation](malware/ofuscacion-de-payload/ipv4-ipv6fuscation.md)
  * [MACFucscation](malware/ofuscacion-de-payload/macfucscation.md)
  * [UUIDFuscation](malware/ofuscacion-de-payload/uuidfuscation.md)
* [Process Injectión](malware/process-injection/README.md)
  * [DLL Injection](malware/process-injection/dll-injection.md)
  * [Shellcode Injection](malware/process-injection/shellcode-injection.md)
  * [APC Injection](malware/process-injection/apc-injection.md)

## ☁ AWS Pentesting

* [Introducción a Amazon Web Services AWS](aws-pentesting/introduccion-a-amazon-web-services-aws.md)
* [Acceso Inicial](aws-pentesting/acceso-inicial.md)
* [IAM](aws-pentesting/iam/README.md)
  * [Políticas de IAM](aws-pentesting/iam/politicas-de-iam.md)
  * [Enumeraciones](aws-pentesting/iam/enumeraciones.md)
  * [Privesc-Paths](aws-pentesting/iam/privesc-paths/README.md)
    * [Permisos de IAM](aws-pentesting/iam/privesc-paths/permisos-de-iam.md)
    * [Permisos sobre políticas](aws-pentesting/iam/privesc-paths/permisos-sobre-politicas.md)
    * [AssumeRolePolicy Update](aws-pentesting/iam/privesc-paths/assumerolepolicy-update.md)
    * [IAM:PassRole\*](aws-pentesting/iam/privesc-paths/iam-passrole.md)
      * [PassExistingRoleToCloudFormation](aws-pentesting/iam/privesc-paths/iam-passrole/passexistingroletocloudformation.md)
* [S3](aws-pentesting/s3/README.md)
  * [Enumeración](aws-pentesting/s3/enumeracion.md)
  * [S3\_public](aws-pentesting/s3/s3\_public.md)
  * [OSINT](aws-pentesting/s3/osint.md)
* [Lambda](aws-pentesting/lambda/README.md)
  * [Enum Lambda](aws-pentesting/lambda/enum-lambda.md)
  * [Enum API Gateway](aws-pentesting/lambda/enum-api-gateway.md)
  * [Privesc-Paths](aws-pentesting/lambda/privesc-paths/README.md)
    * [RCE sobre Lambda](aws-pentesting/lambda/privesc-paths/rce-sobre-lambda.md)
    * [PassExistingRoleToNewLambdaThenInvoke](aws-pentesting/lambda/privesc-paths/passexistingroletonewlambdatheninvoke.md)
    * [PassRoleToNewLambdaThenTrigger](aws-pentesting/lambda/privesc-paths/passroletonewlambdathentrigger.md)
    * [EditExistingLambdaFunctionWithRole](aws-pentesting/lambda/privesc-paths/editexistinglambdafunctionwithrole.md)
* [EC2](aws-pentesting/ec2/README.md)
  * [Enumeración](aws-pentesting/ec2/enumeracion.md)
  * [Privesc-Paths](aws-pentesting/ec2/privesc-paths/README.md)
    * [EC2\_SSRF](aws-pentesting/ec2/privesc-paths/ec2\_ssrf.md)
    * [CreateEC2WhithExistingIP](aws-pentesting/ec2/privesc-paths/createec2whithexistingip.md)
    * [PassExistingRoleToNewGlueDevEndpoint](aws-pentesting/ec2/privesc-paths/passexistingroletonewgluedevendpoint.md)
    * [ECS\_takeover](aws-pentesting/ec2/privesc-paths/ecs\_takeover.md)
* [VPC](aws-pentesting/vpc.md)
  * [Enumeración](aws-pentesting/vpc/enumeracion.md)
  * [PivotingInTheCloud](aws-pentesting/vpc/privesc-paths/pivotinginthecloud.md)
* [Bases de Datos](aws-pentesting/bases-de-datos/README.md)
  * [RDS](aws-pentesting/rds.md)
    * [Enumeración](aws-pentesting/bases-de-datos/rds/enumeracion.md)
  * [DynamoDB](aws-pentesting/dynamodb.md)
    * [Enumeración](aws-pentesting/dynamodb/enumeracion.md)
* [ECS](aws-pentesting/ecs.md)
  * [Enum ECR](aws-pentesting/ecs/enum-ecr.md)
  * [Enum ECS](aws-pentesting/ecs/enum-ecs.md)
  * [Enum EKS](aws-pentesting/ecs/enum-eks.md)
* [AWS Secrets Manager](aws-pentesting/aws-secrets-manager.md)
  * [Enumeración](aws-pentesting/aws-secrets-manager/enumeracion.md)
* [Análisis de vulnerabilidades Automatizado](aws-pentesting/analisis-de-vulnerabilidades-automatizado.md)
* [Blue Team AWS](aws-pentesting/blue-team-aws/README.md)
  * [CloudTrail](aws-pentesting/blue-team-aws/cloudtrail.md)
  * [CloudWatch](aws-pentesting/blue-team-aws/cloudwatch.md)
  * [GuardDuty](aws-pentesting/blue-team-aws/guardduty.md)
  * [AWS Inspector](aws-pentesting/blue-team-aws/aws-inspector.md)
  * [AWS Shield](aws-pentesting/blue-team-aws/aws-shield.md)
  * [Web Application Firewall](aws-pentesting/blue-team-aws/web-application-firewall.md)
* [Notas](aws-pentesting/notas/README.md)
  * [Terraform](aws-pentesting/notas/terraform.md)

## 🌩 Azure Coud Pentesting <a href="#azure-pentest" id="azure-pentest"></a>

* [Introducción a Azure y Office 365](azure-pentest/introduccion-a-azure-y-office-365.md)
* [Introducción a Microsoft Indentity Services](azure-pentest/introduccion-a-microsoft-indentity-services.md)
* [Enumeración](azure-pentest/enumeracion.md)
* [Azure Files](azure-pentest/azure-files/README.md)
  * [Encontrar Fuga de Datos](azure-pentest/azure-files/encontrar-fuga-de-datos.md)
* [Microsoft Graph](azure-pentest/microsoft-graph.md)
* [Notas](azure-pentest/notas.md)

## 🔫 Red\_Team

* [Introducción a Red Team](red\_team/introduccion-a-red-team.md)
* [¿Qué es MITRE ATT\&CK?](red\_team/que-es-mitre-att-and-ck/README.md)
  * [Guia de uso de Invoke-AtomicRedTeam](red\_team/que-es-att-and-ck/guia-de-uso-de-invoke-atomicredteam.md)

## 💾 Active Directory <a href="#ad" id="ad"></a>

* [¿Qué es Directorio Activo?](ad/que-es-directorio-activo.md)
* [Componentes AD](ad/componentes-ad/README.md)
  * [Físicos](ad/componentes-ad/fisicos.md)
  * [Lógicos](ad/componentes-ad/logicos.md)
* [AD Lab](ad/ad-lab.md)

## 📱 Mobile Pentesting <a href="#mob-pentest" id="mob-pentest"></a>

* [Análisis de Aplicaciones Móviles en MobSF](mobile/analisis-de-aplicaciones-moviles-en-mobsf.md)
* [¿Cómo interceptar tráfico de una aplicación Flutter Android con Burp?](mobile/como-interceptar-trafico-de-una-aplicacion-flutter-android-con-burp.md)

## 📶 Wireless

* [Terminología](wireless/terminologia.md)
* [Tipos de Wireless Networks](wireless/tipos-de-wireless-networks.md)
* [Formas de Autenticación](wireless/formas-de-autenticacion.md)
* [Cifrados](wireless/cifrados/README.md)
  * [WEP](wireless/cifrados/wep.md)
  * [WPA](wireless/cifrados/wpa.md)
  * [WPA2](wireless/cifrados/wpa2.md)
  * [WPA3](wireless/cifrados/wpa3.md)
* [Ataques](wireless/ataques.md)

## 😎 Extras

* [Simple python HTTP Server](extras/simple-python-http-server.md)
* [Simple NetCat file transfer](extras/simple-netcat-file-transfer.md)
* [Comandos no categorizados](extras/comandos-no-categorizados.md)
* [Bug Bounty Programs](extras/bug-bounty-programs.md)
