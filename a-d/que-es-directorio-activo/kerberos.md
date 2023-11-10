# Kerberos

## ¿Qúe es Kerberos?

Kerberos es un protocolo de autenticación, no de autorización. En otras palabras, permite identificar a cada usuario, que proporciona una contraseña secreta, pero **no valida a qué recursos o servicios puede acceder dicho usuario**.

### Elementos de kerberos

#### **Capa de transporte**

Kerberos utiliza UDP o TCP como protocolo de transporte, que envía datos en texto claro. Debido a esto Kerberos es responsable de proporcionar encriptación. Los puertos utilizados por Kerberos son UDP/88 y TCP/88, que deben escucharse en el KDC (se explica en la siguiente sección).

#### **Agentes**

Varios agentes trabajan juntos para proporcionar autenticación en Kerberos. Estos son los siguientes:

* **Client or user** who wants to access to the service.
* **AP** (Application Server) which offers the service required by the user.
* **KDC** (Key Distribution Center), the main service of Kerberos, responsible of issuing the tickets, installed on the DC (Domain Controller). It is supported by the **AS** (Authentication Service), which issues the TGTs.

#### **Encryption keys**

There are several structures handled by Kerberos, as tickets. Many of those structures are encrypted or signed in order to prevent being tampered by third parties. These keys are the following:

* **KDC or krbtgt key** which is derivate from krbtgt account NTLM hash.
* **User key** which is derivate from user NTLM hash.
* **Service key** which is derivate from the NTLM hash of service owner, which can be a user or computer account.
* **Session key** which is negotiated between the user and KDC.
* **Service session key** to be use between user and service.

#### **Tickets**

The main structures handled by Kerberos are the tickets. These tickets are delivered to the users in order to be used by them to perform several actions in the Kerberos realm. There are 2 types:

* The **TGS** (Ticket Granting Service) is the ticket which user can use to authenticate against a service. It is encrypted with the service key.
* The **TGT** (Ticket Granting Ticket) is the ticket presented to the KDC to request for TGSs. It is encrypted with the KDC key.

#### **PAC**

The **PAC** (Privilege Attribute Certificate) is a structure included in almost every ticket. This structure contains the privileges of the user and it is signed with the KDC key.

It is possible to services to verify the PAC by communicating with the KDC, although this does not happen often. Nevertheless, the PAC verification consists of checking only its signature, without inspecting if privileges inside of PAC are correct.

Furthermore, a client can avoid the inclusion of the PAC inside the ticket by specifying it in _KERB-PA-PAC-REQUEST_ field of ticket request.

#### **Messages**

Kerberos uses differents kinds of messages. The most interesting are the following:

* **KRB\_AS\_REQ**: Used to request the TGT to KDC.
* **KRB\_AS\_REP**: Used to deliver the TGT by KDC.
* **KRB\_TGS\_REQ**: Used to request the TGS to KDC, using the TGT.
* **KRB\_TGS\_REP**: Used to deliver the TGS by KDC.
* **KRB\_AP\_REQ**: Used to authenticate a user against a service, using the TGS.
* **KRB\_AP\_REP**: (Optional) Used by service to identify itself against the user.
* **KRB\_ERROR**: Message to communicate error conditions.

Additionally, even if it is not part of Kerberos, but NRPC, the AP optionally could use the **KERB\_VERIFY\_PAC\_REQUEST** message to send to KDC the signature of PAC, and verify if it is correct.



