<properties 
   pageTitle="Informationen zu VPN-Gateways für virtuelle Netzwerke | Microsoft Azure"
   description="Sie erhalten Informationen zu VPN-Gateway-SKUs vom Typ Basic, Standard und Leistung, zur gemeinsamen Verwendung von VPN-Gateways und ExpressRoute, zu Gatewaytypen mit statischem und dynamischem Routing und zu den Gatewayanforderungen für die Konnektivität virtueller Netzwerke."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2015"
   ms.author="cherylmc" />

# Informationen zu VPN-Gateways

VPN-Gateways werden zum Senden von Netzwerkdatenverkehr zwischen virtuellen Netzwerken und lokalen Standorten oder zwischen mehreren virtuellen Netzwerken (VNet-zu-VNet) verwendet. Beim Erstellen eines Gateways müssen Sie einige Faktoren berücksichtigen. Sie müssen folgende Informationen besitzen: Welche Gateway-SKU Sie nutzen möchten, den für die Konfiguration erforderlichen Routingtyp (dynamisch oder statisch) und das VPN-Gerät, das Sie verwenden möchten, wenn ein VPN-Gerät für Ihre Konfiguration benötigt wird.

## Gateway-SKUs
Es gibt drei VPN-Gateway-SKUs: Basic, Standard und Leistung. In der folgenden Tabelle sind die Gatewaytypen und der geschätzte zusammengefasste Durchsatz angegeben. Für die einzelnen Gateway-SKUs gelten unterschiedliche Preise. Informationen zu den Preisen finden Sie unter [VPN-Gateway – Preise](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Gemeinsame Verwendung von VPN-Gateway und ExpressRoute | ExpressRoute-Gateway-Durchsatz | VPN-Gateway-Durchsatz | Max. IPsec-Tunnel für VPN-Gateway |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Basic | Nein | 500 MBit/s | 100 MBit/s | 10 |
| Standard | Ja | 1.000 MBit/s | 100 MBit/s | 10 |
| Leistung | Ja | 2.000 MBit/s | 200 MBit/s | 30 |

**Hinweis:** Der VPN-Durchsatz ist eine grobe Schätzung, die auf Messungen zwischen den VNets einer Azure-Region basiert. Dies ist keine Garantie für den Wert, den Sie für standortübergreifende Verbindungen über das Internet erzielen können. Die Angabe sollte stattdessen als maximal möglicher Wert angesehen werden.

## Gatewaytypen

Es gibt zwei Gatewaytypen: *statisches Routing* (auch als richtlinienbasiertes VPN bezeichnet) und *dynamisches Routing* (auch als weiterleitungsbasiertes VPN bezeichnet). Einige Konfigurationen funktionieren nur mit einem speziellen Routingtyp, und auch einige VPN-Geräte funktionieren nur mit einem bestimmten Routingtyp. Beim Erstellen eines VPN-Gateways wählen Sie den Gatewaytyp aus, der für Ihre Konfiguration erforderlich ist. Dabei stellen Sie sicher, dass das gewählte VPN-Gerät diesen Routingtyp auch unterstützt.

Wenn Sie beispielsweise die Verwendung einer Site-to-Site-Konfiguration parallel zu einer Punkt-zu-Site-Konfiguration planen, müssen Sie ein VPN-Gateway mit dynamischem Routing konfigurieren. Es ist zwar richtig, dass Site-to-Site-Konfigurationen mit Gateways mit statischem Routing funktionieren, aber für Punkt-zu-Site-Konfigurationen ist ein Gateway mit dynamischem Routing erforderlich. Da beide Verbindungen über das gleiche Gateway führen, müssen Sie das Gateway auswählen, von dem beide Konfigurationen unterstützt werden.

Außerdem sollten Sie überprüfen, ob Ihr VPN-Gerät den Gatewaytyp sowie die IPsec/IKE-Parameter und die benötigte Konfiguration unterstützt. Wenn Sie beispielsweise ein dynamisches Gateway erstellen möchten und Ihr VPN-Gerät keine weiterleitungsbasierten VPNs unterstützt, müssen Sie Ihre Planung überdenken. Sie können entweder ein anderes VPN-Gerät kaufen, das dynamische Gateways unterstützt, oder eine VPN-Gatewayverbindung erstellen, die ein Gateway mit statischem Routing unterstützt. Wenn Sie später ein VPN-Gerät mit Unterstützung eines Gateways mit dynamischem Routing erwerben, können Sie das Gateway immer noch als dynamisches Gateway neu erstellen, um das Gerät zu verwenden. Sie müssen dann lediglich das Gateway neu erstellen. Es ist nicht erforderlich, das virtuelle Netzwerk neu zu erstellen.

Unten sind die beiden Gatewaytypen angegeben:

- **Statisches Routing:** Gateways mit statischem Routing unterstützen **richtlinienbasierte VPNs**. Bei richtlinienbasierten VPNs werden Pakete mit Datenverkehrselektoren durch IPsec-Tunnel geleitet. Dies basiert auf den Kombinationen der Adresspräfixe zwischen Ihrem lokalen Netzwerk und Ihrem Azure VNet. Die Datenverkehrselektoren bzw. Richtlinien werden normalerweise als Zugriffslisten in Ihren VPN-Konfigurationen festgelegt.

	>[AZURE.NOTE]Nicht alle Konfigurationen sind mit VPN-Gateways mit statischem Routing kompatibel. Für Konfigurationen für mehrere Standorte, VNet-zu-VNet-Konfigurationen und Punkt-zu-Site-Verbindungen sind beispielsweise jeweils Gateways mit dynamischem Routing erforderlich. Die Gatewayanforderungen sind in den Artikeln für jede Konfiguration angegeben.

- **Dynamisches Routing:** Mit Gateways mit dynamischem Routing werden **weiterleitungsbasierte VPNs** implementiert. Bei weiterleitungsbasierten VPNs werden die „Routen“ der IP-Weiterleitungs- oder -Routingtabelle verwendet, um Pakete an die entsprechenden VPN-Tunnelschnittstellen zu leiten. Über die Tunnelschnittstellen werden die Pakete für die Tunnel dann ver- oder entschlüsselt (ein- und ausgehend). Die Richtlinie bzw. der Datenverkehrselektor für weiterleitungsbasierte VPNs werden im Any-to-Any-Format (bzw. als Platzhalter) konfiguriert.

## Gatewayanforderungen

In der folgenden Tabelle sind die Anforderungen für statische und dynamische VPN-Gateways aufgeführt.


| **Eigenschaft** | **VPN-Gateway mit statischem Routing** | **VPN-Gateway mit dynamischem Routing** | **Standard-VPN-Gateway** | **VPN-Hochleistungsgateway** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Site-to-Site-Konnektivität (S2S) | Richtlinienbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration |
| Punkt-zu-Site-Konnektivität (P2S) | Nicht unterstützt | Unterstützt (gemeinsame Verwendung mit S2S möglich) | Unterstützt (gemeinsame Verwendung mit S2S möglich) | Unterstützt (gemeinsame Verwendung mit S2S möglich) |
| Authentifizierungsmethode | Vorinstallierter Schlüssel | - Vorinstallierter Schlüssel für S2S-Konnektivität - Zertifikate für P2S-Konnektivität | - Vorinstallierter Schlüssel für S2S-Konnektivität - Zertifikate für P2S-Konnektivität | - Vorinstallierter Schlüssel für S2S-Konnektivität - Zertifikate für P2S-Konnektivität |
| Maximale Anzahl von S2S-Verbindungen | 1 | 10 | 10 | 30 |
| Maximale Anzahl von P2S-Verbindungen | Nicht unterstützt | 128 | 128 | 128 |
| Aktive Routingunterstützung (BGP) | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt |


## Nächste Schritte

Wählen Sie das VPN-Gerät für Ihre Konfiguration aus. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](http://go.microsoft.com/fwlink/p/?LinkID=615934).

Konfigurieren Sie Ihr virtuelles Netzwerk. Informationen zu standortübergreifenden Verbindungen finden Sie in den folgenden Artikeln:

- [Konfigurieren einer standortübergreifenden Site-to-Site-Verbindung mit einem Azure Virtual Network](vpn-gateway-site-to-site-create.md)
- [Konfigurieren einer Punkt-zu-Site-VPN-Verbindung mit einem Azure Virtual Network](vpn-gateway-point-to-site-create.md)
- [Konfigurieren eines Site-to-Site-VPN mit Windows Server 2012 RRAS (Routing- und RAS-Dienst)](https://msdn.microsoft.com/library/dn636917.aspx)

Informationen zum Konfigurieren eines VPN-Gateways finden Sie unter [Konfigurieren eines VPN-Gateways](http://go.microsoft.com/fwlink/p/?LinkId=615106).

Informationen zum Ändern des VPN-Gatewaytyps finden Sie unter [Ändern des Routingtyps von Gateways eines virtuellen Netzwerks](http://go.microsoft.com/fwlink/p/?LinkId=615109).

Informationen zum Verbinden mehrerer Standorte zu einem virtuellen Netzwerk finden Sie unter [Verbinden mehrerer lokaler Standorte mit einem virtuellen Netzwerk](http://go.microsoft.com/fwlink/p/?LinkID=615106).

 

<!---HONumber=62-->