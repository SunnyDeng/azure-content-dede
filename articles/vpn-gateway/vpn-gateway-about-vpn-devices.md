<properties 
   pageTitle="Informationen zu VPN-Geräten für Standort-zu-Standort-Verbindungen in Azure Virtual Network | Microsoft Azure"
   description="Erfahren Sie mehr über VPN-Geräte und IPsec-Parameter für Standort-zu-Standort-VPN-Gatewayverbindungen in Azure Virtual Network."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2015"
   ms.author="cherylmc" />

# Informationen zu VPN-Geräten für virtuelle Standort-zu-Standort-Netzwerkverbindungen

Ein VPN-Gerät ist erforderlich, um eine Standort- zu-Standort-VPN-Verbindung zu konfigurieren. Standort-zu-Standort-Verbindungen können zum Erstellen einer Hybrid-Cloud-Lösung verwendet werden. Sie können sie auch zum Herstellen einer sicheren Verbindung zwischen Ihrem lokalen Netzwerk und dem virtuellen Netzwerk verwenden. Dieser Artikel beschreibt kompatible VPN-Geräte und Konfigurationsparameter.

Dabei muss beachtet werden, dass Standort-zu-Standort-Verbindungen zusätzlich zu einem kompatiblen VPN-Gerät auch eine öffentliche IPv4-IP-Adresse benötigen. Darüber hinaus sollten Sie den Gatewaytyp auswählen, der Ihre Lösung unterstützt. Nicht alle VPN-Geräte unterstützen alle Gatewaytypen. Unter [VPN-Gateways](vpn-gateway-about-vpngateways.md) können Sie überprüfen, welcher Gatewaytyp benötigt wird, um Ihre Lösung zu erstellen.



## VPN-Geräte

Wir haben in Zusammenarbeit mit Geräteherstellern eine Reihe von VPN-Geräten für standardmäßige S2S-Verbindungen (Standort-zu-Standort-Verbindungen) getestet. Eine Liste der VPN-Geräte, die bekanntermaßen mit Virtual Network kompatibel sind, finden Sie nachfolgend im Abschnitt [Kompatible VPN-Geräte](#compatible-vpn-devices). Alle Geräte der in dieser Liste aufgeführten Gerätefamilien sollten mit Azure-VPN-Gateways kompatibel sein. Hilfreiche Informationen zur Konfiguration des VPN-Geräts finden Sie in der entsprechenden Konfigurationsvorlage für die jeweilige Gerätefamilie.

Die Spezifikationen für HighPerformance-VPN-Gateways und VPN-Gateways mit dynamischem Routing sind identisch, sofern nicht anders angegeben. Beispielsweise sind die überprüften VPN-Geräte, die mit den Azure-VPN-Gateways mit dynamischem Routing kompatibel sind, auch mit dem neuen Azure-HighPerformance-VPN-Gateway kompatibel.


### Kompatible VPN-Geräte

Gemeinsam mit VPN-Geräteherstellern haben wir an der Qualifizierung von spezifischen VPN-Gerätefamilien gearbeitet. Der folgende Abschnitt enthält eine Liste aller Gerätefamilien, die bekanntermaßen unseren VPN-Gateway unterstützen. Alle Geräte, die Mitglieder der aufgelisteten Gerätefamilien sind, funktionieren bekanntermaßen, sofern keine entsprechenden Ausnahmen genannt werden. Wenn Ihr Gerät nicht in der Liste angezeigt wird, finden Sie weitere Informationen unter [Geräte, die nicht auf der Liste der kompatiblen Geräte stehen](#devices-not-on-the-compatible-list).



Wenden Sie sich für Unterstützung im Hinblick auf das VPN-Gerät an den Gerätehersteller.



| **Hersteller** | **Gerätefamilie** | **Betriebssystemversion (Min.)** | **Statisches Routing** | **Dynamisches Routing** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | VPN-Router der AR-Serie | 2\.9.2 | In Kürze verfügbar | Nicht kompatibel |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| Nicht kompatibel |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Nicht kompatibel |
| Brocade | 5400 Vyatta vRouter | Virtual Router 6.6R3 GA | [Konfigurationsanweisungen](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Nicht kompatibel |
| Check Point | Sicherheitsgateway | R75.40, R75.40VS | [Konfigurationsanweisungen](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Konfigurationsanweisungen](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Cisco-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) | Nicht kompatibel |
| Cisco | ASR | IOS 15.1 (statisch), IOS 15.2 (dynamisch) | [Cisco-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Cisco-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Cisco | ISR | IOS 15.0 (statisch), IOS 15.1 (dynamisch) | [Cisco-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Cisco-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Citrix | CloudBridge-MPX-Gerät oder virtuelles VPX-Gerät | N/V | [Integrationsanweisungen](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | Nicht kompatibel |
| Dell SonicWALL | TZ-Serie, NSA-Serie, SuperMassive-Serie, E-Class-NSA-Serie | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [Anleitung: SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Anleitung: SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [Anleitung: SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Anleitung: SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | BIG-IP-Serie | N/V | [Konfigurationsanweisungen](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | Nicht kompatibel |
| Fortinet | FortiGate | FortiOS 5.0.7 | [Konfigurationsanweisungen](http://docs.fortinet.com/fortigate/admin-guides) | [Konfigurationsanweisungen](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | SEIL-Serie | SEIL / X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Konfigurationsanweisungen](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Nicht kompatibel |
| Juniper | SRX | JunOS 10.2 (statisch), JunOS 11.4 (dynamisch) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | J-Serie | JunOS 10.4r9 (statisch), JunOS 11.4 (dynamisch) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | ISG | ScreenOS 6.3 (statisch und dynamisch) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Juniper | SSG | ScreenOS 6.2 (statisch und dynamisch) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) | [Juniper-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Microsoft | Routing- und RAS-Dienst | Windows Server 2012 | Nicht kompatibel | [Microsoft-Beispiele](http://go.microsoft.com/fwlink/p/?LinkId=691833) |
| Openswan | Openswan | 2\.6.32 | (In Kürze verfügbar) | Nicht kompatibel |
| Palo Alto Networks | Alle Geräte mit PAN-OS 5.0 oder höher | PAN-OS 5x oder höher | [Palo Alto Networks](https://support.paloaltonetworks.com/) | Nicht kompatibel |
| Watchguard | Alle | Fireware XTM v11.x | [Konfigurationsanweisungen](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Nicht kompatibel |


### Geräte, die nicht in der Liste der kompatiblen Geräte stehen


Wenn Ihr Gerät nicht in der Liste der bekanntermaßen kompatiblen VPN-Geräte aufgeführt wird, Sie das Gerät aber dennoch für eine VPN-Verbindung verwenden möchten, müssen Sie überprüfen, ob es die Mindestanforderungen gemäß der Tabelle [Gatewayanforderungen](vpn-gateway-about-vpngateways.md#gateway-requirements) erfüllt. Geräte, die die Mindestanforderungen erfüllen, sollten auch problemlos mit Virtual Network verwendet werden können. Zusätzliche Unterstützung und Konfigurationsanweisungen erhalten Sie vom Gerätehersteller.


## Bearbeiten der Gerätekonfigurationsvorlagen

Nachdem Sie die bereitgestellte Konfigurationsvorlage für das VPN-Gerät heruntergeladen haben, müssen Sie einige der Werte entsprechend den Einstellungen Ihrer Umgebung ersetzen.

**So bearbeiten Sie die Vorlage:**

1. Öffnen Sie die Vorlage im Editor. 
1. Suchen und ersetzen Sie alle <*text*>-Zeichenfolgen mit den Werten, die für Ihre Umgebung gelten. Schließen Sie dabei unbedingt < and > mit ein. Wenn ein Name angegeben ist, sollte der ausgewählte Name eindeutig sein. Wenn ein Befehl nicht funktioniert, lesen Sie zunächst die Dokumentation des Geräteherstellers.

| **Vorlagentext** | **Ändern in** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinLokalesNetzwerk |
| &lt;RP\_AzureNetwork&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinAzureNetzwerk |
| &lt;RP\_AccessList&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineAzureZugriffsliste |
| &lt;RP\_IPSecTransformSet&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinIPSecTransformSet |
| &lt;RP\_IPSecCryptoMap&gt; | Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineIPSecKryptoMap |
| &lt;SP\_AzureNetworkIpRange&gt; | Geben Sie den Bereich an. Beispiel: 192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | Geben Sie die Subnetzmaske an. Beispiel: 255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | Geben Sie den lokalen Bereich an. Beispiel: 10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | Geben Sie die lokale Subnetzmaske an. Beispiel: 255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter **Gateway-IP-Adresse**. |
| &lt;SP\_PresharedKey&gt; | Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter "Schlüssel verwalten". |



## IPsec-Parameter

### IKE Phase 1-Einrichtung

| **Eigenschaft** | **VPN-Gateway mit statischem Routing** | **VPN-Gateway mit dynamischem Routing und Standard- bzw. HighPerformance-VPN-Gateway** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE-Version | IKEv1 | IKEv2 |
| Diffie-Hellman-Gruppe | Gruppe 2 (1024 Bit) | Gruppe 2 (1024 Bit) |
| Authentifizierungsmethode | Vorab ausgetauschter Schlüssel | Vorab ausgetauschter Schlüssel |
| Verschlüsselungsalgorithmen | AES256 AES128 3DES | AES256 3DES |
| Hashalgorithmus | SHA1(SHA128) | SHA1(SHA128) |
| Phase 1 Sicherheitszuordnung (SA) Lebensdauer (Zeit) | 28\.800 Sekunden | 28\.800 Sekunden |


### IKE Phase 2-Einrichtung

| **Eigenschaft** | **VPN-Gateway mit statischem Routing** | **VPN-Gateway mit dynamischem Routing und Standard- bzw. HighPerformance-VPN-Gateway** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE-Version | IKEv1 | IKEv2 |
| Hashalgorithmus | SHA1(SHA128) | SHA1(SHA128) |
| Phase 2 Sicherheitszuordnung (SA) Lebensdauer (Zeit) | 3.600 Sekunden | - | | Phase 2 Sicherheitszuordnung (SA) Lebensdauer (Durchsatz) | 102.400.000 KB | - | | IPSec-SA-Verschlüsselung und Authentifizierungsangebote (Rangfolge) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/V | Siehe "IPsec-Sicherheitszuordnungsangebote (SA) für Gateways mit dynamischem Routing" || Perfect Forward Secrecy (PFS) | Nein | Ja (DH-Gruppe 1) || Erkennung inaktiver Peers | Nicht unterstützt | Unterstützt |

### IPsec-Sicherheitszuordnungsangebote (SA) für Gateways mit dynamischem Routing

Die folgende Tabelle listet die IPSec-SA-Verschlüsselungs und -Authentifizierungsangebote auf. Angebote werden in der Reihenfolge ihrer Priorität aufgeführt, in der das Angebot dargeboten oder akzeptiert wurde.

| **IPsec-SA-Verschlüsselungs- und -Authentifizierungsangebote** | **Azure-Gateway als Initiator** | Azure-Gateway als Antwortender |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 mit ESP AES\_128 mit Null HMAC |
| 5 | AH SHA1 mit ESP AES\_256 mit Null HMAC | AH SHA1 mit ESP 3\_DES mit Null HMAC |
| 6 | AH SHA1 mit ESP AES\_128 mit Null HMAC | AH MD5 mit ESP 3\_DES mit Null HMAC, keine vorgesehene Lebensdauer |
| 7 | AH SHA1 mit ESP 3\_DES mit Null HMAC | AH SHA1 mit ESP 3\_DES SHA1, keine Lebensdauer |
| 8 | AH MD5 mit ESP 3\_DES mit Null HMAC, keine vorgesehene Lebensdauer | AH MD5 mit ESP 3\_DES MD5, keine Lebensdauer |
| 9 | AH SHA1 mit ESP 3\_DES SHA1, keine Lebensdauer | ESP DES MD5 |
| 10 | AH MD5 mit ESP 3\_DES MD5, keine Lebensdauer | ESP DES SHA1, keine Lebensdauer |
| 11 | ESP DES MD5 | AH SHA1 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer |
| 12 | ESP DES SHA1, keine Lebensdauer | AH MD5 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer |
| 13 | AH SHA1 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer | AH SHA1 mit ESP DES SHA1, keine Lebensdauer |
| 14 | AH MD5 mit ESP DES Null HMAC, keine vorgesehene Lebensdauer | AH MD5 mit ESP DES MD5, keine Lebensdauer |
| 15 | AH SHA1 mit ESP DES SHA1, keine Lebensdauer | ESP SHA, keine Lebensdauer |
| 16 | AH MD5 mit ESP DES MD5, keine Lebensdauer | ESP MD5, keine Lebensdauer |
| 17 | - | AH SHA, keine Lebensdauer || 18 | - | AH MD5, keine Lebensdauer |




- Sie können eine IPSec ESP NULL-Verschlüsselung mit VPN-Gateway mit dynamischem Routing und HighPerformance-VPN-Gateway angeben, die für VNet-zu-VNet-Verbindungen in Azure-Netzwerken vorgesehen ist. 

- Verwenden Sie für standortübergreifende Konnektivität über das Internet die Standardeinstellungen für Azure-VPN-Gateways mit Verschlüsselung und Hashalgorithmen, die in der Tabelle oben aufgelistet werden, um die Sicherheit Ihrer kritischen Kommunikation zu gewährleisten.

## Nächste Schritte


Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN-Gateways](vpn-gateway-about-vpngateways.md).

Informationen zum Konfigurieren eines Standort-zu-Standort-VPN finden Sie unter [Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung](vpn-gateway-site-to-site-create.md).

<!---HONumber=AcomDC_1210_2015-->