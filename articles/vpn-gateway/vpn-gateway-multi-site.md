<properties 
   pageTitle="Verbinden mehrerer lokaler Standorte mit einem virtuellen Netzwerk mithilfe eines VPN-Gateways"
   description="In diesem Artikel werden die Schritte zum Verbinden mehrerer lokaler Standorte mit einem virtuellen Netzwerk erläutert. Dabei wird ein VPN-Gateway im klassischen Bereitstellungsmodell verwendet."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="cherylmc" />

# Verbinden mehrerer lokaler Standorte mit einem virtuellen Netzwerk

Dieser Artikel behandelt das Verbinden mehrerer lokaler Standorte mit einem VNet, das mithilfe des klassischen Bereitstellungsmodells erstellt wurde (auch als Dienstverwaltung bezeichnet).

**Informationen zu Azure-Bereitstellungsmodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]Wenn der Artikel mit den Schritten für VNets fertig ist, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden, wird ein Link darauf hier eingefügt.

## Über das Herstellen einer Verbindung

Sie können mehrere lokale Standorte mit einem einzigen virtuellen Netzwerk verbinden. Dies ist besonders beim Erstellen von Hybrid Cloud-Lösungen interessant. Das Erstellen einer Mehrfachstandort- bzw. Multisite-Verbindung mit Ihrem virtuellen Azure-Netzwerkgateway ähnelt dem Erstellen anderer Standort-zu-Standort-Verbindungen. Sie können dafür ein vorhandenes Azure-VPN-Gateway verwenden, sofern das Gateway dynamisch (routenbasiert) ist.

Wenn bereits ein statisches Gateway mit Ihrem virtuellen Netzwerk verbunden ist, können Sie den Gatewaytyp in „dynamisch“ ändern, um mehrere Standorte zu einzubinden. Sie brauchen das virtuelle Netzwerk dafür nicht neu zu erstellen. Stellen Sie vor dem Ändern des Routingtyps sicher, dass Ihr lokales VPN-Gateway routenbasierte VPN-Konfigurationen unterstützt.

![VPN mit mehreren Standorten](./media/vpn-gateway-multi-site/IC727363.png)

## Zu berücksichtigende Punkte

**Es ist nicht möglich, Änderungen an diesem virtuellen Netzwerk über das klassische Azure-Portal vorzunehmen.** In dieser Version müssen Sie die Netzwerkkonfigurationsdatei ändern, anstatt das klassische Azure-Portal zu verwenden. Wenn Sie Änderungen im klassischen Azure-Portal vornehmen, überschreiben diese Ihre Multisite-Verweiseinstellungen für das virtuelle Netzwerk. Sie werden sich während der Einrichtung der Multisite-Verbindung schnell mit der Verwendung der Netzwerkkonfigurationsdatei vertraut machen. Wenn jedoch mehrere Personen an Ihrer Netzwerkkonfiguration arbeiten, müssen Sie sicherstellen, dass jeder über diese Einschränkung informiert ist. Dies bedeutet nicht, dass Sie das klassische Azure-Portal überhaupt nicht verwenden können. Sie können es für alle anderen Aufgaben verwenden, nur nicht zum Ändern der Konfiguration dieses speziellen virtuellen Netzwerks.

## Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.

- Kompatible VPN-Hardware für jeden lokalen Standort. Lesen Sie unter [Informationen zu VPN-Geräten und Gateways für virtuelle Netzwerkverbindungen](http://go.microsoft.com/fwlink/p/?linkid=615099) nach, ob das Gerät, das Sie verwenden möchten, bekanntermaßen kompatibel ist.

- Eine externe öffentliche IPv4-Adresse für jedes VPN-Gerät. Die IP-Adresse darf sich nicht hinter einer NAT befinden. Dies ist eine Voraussetzung

- Die neueste Version von Azure PowerShell-Cmdlets. Sie können die neueste Version aus dem Abschnitt „Windows PowerShell“ der [Downloadseite](https://azure.microsoft.com/downloads/) herunterladen und installieren.

- Eine Person, die sich mit der Konfiguration Ihrer VPN-Hardware auskennt. Es ist nicht möglich, automatisch generierte VPN-Skripts aus dem klassischen Azure-Portal zum Konfigurieren der VPN-Geräte zu verwenden. Daher müssen Sie über profunde Kenntnisse in der Konfiguration des VPN-Geräts verfügen oder jemanden mit entsprechenden Fachkenntnissen hinzuziehen.

- Die IP-Adressbereiche, die Sie für Ihr virtuelles Netzwerk verwenden möchten (sofern Sie sie noch nicht erstellt haben).

- Die IP-Adressbereiche für alle lokalen Netzwerkstandorte, mit denen Sie eine Verbindung herstellen. Sie müssen sicherstellen, dass sich die IP-Adressbereiche der lokalen Netzwerkstandorte nicht überlappen. Andernfalls wird das Hochladen der Konfiguration vom klassischen Azure-Portal oder von der REST-API abgelehnt. Wenn Sie z. B. zwei lokale Netzwerkstandorte haben, die beide den IP-Adressbereich 10.2.3.0/24 enthalten, und ein Paket die Zieladresse 10.2.3.3 aufweist, würde Azure nicht wissen, an welchen Standort Sie das Paket senden möchten, weil sich die Adressbereiche überlappen. Zur Vermeidung von Routingproblemen lässt Azure das Hochladen von Konfigurationsdateien mit überlappenden Bereichen nicht zu.

## Erstellen des virtuellen Netzwerks und Gateways

1. **Erstellen Sie ein Standort-zu-Standort-VPN mit einem Gateway mit dynamischem (routenbasiertem) Routing.** Wenn Sie bereits über ein solches VPN verfügen – großartig! In diesem Fall können Sie direkt mit dem [Exportieren der Konfigurationseinstellungen für das virtuelle Netzwerk](#export) fortfahren. Andernfalls führen Sie die folgenden Schritte aus:

	**Wenn Sie bereits über ein virtuelles Standort-zu-Standort-Netzwerk verfügen, das aber ein statisches (richtlinienbasiertes) Gateway enthält:** Schritt 1: Ändern Sie den Gatewaytyp in dynamisches Routing. Für ein VPN mit mehreren Standorten ist ein Gateway mit dynamischem Routing erforderlich. Um den Gatewaytyp zu ändern, müssen Sie zuerst das vorhandene Gateway löschen und dann ein neues erstellen. Anweisungen dazu finden Sie unter [Ändern des Routingtyps für ein VPN-Gateway](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type). Schritt 2: Konfigurieren Sie das neue Gateway, und erstellen Sie den VPN-Tunnel. Eine Anleitung hierzu finden Sie unter [Konfigurieren eines VPN-Gateways im klassischen Azure-Portal](vpn-gateway-configure-vpn-gateway-mp.md). Ändern Sie zunächst den Gatewaytyp in dynamisches Routing.

	**Wenn Sie nicht über ein virtuelles Standort-zu-Standort-Netzwerk verfügen:** Schritt 1: Erstellen Sie anhand der folgenden Anweisungen das virtuelle Standort-zu-Standort-Netzwerk: [Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung im klassischen Azure-Portal](vpn-gateway-site-to-site-create.md). Schritt 2: Konfigurieren Sie anhand der folgenden Anweisungen ein Gateway mit dynamischem Routing: [Konfigurieren eines VPN-Gateways](vpn-gateway-configure-vpn-gateway-mp.md). Denken Sie daran, **dynamisches Routing** als Gatewaytyp auszuwählen.

2. **<a name="export"></a>Exportieren Sie die Konfigurationseinstellungen für das virtuelle Netzwerk.** Informationen zum Exportieren der Netzwerkkonfigurationsdatei finden Sie unter [So exportieren Sie die Netzwerkeinstellungen](../virtual-network/virtual-networks-using-network-configuration-file.md). Die exportierte Datei wird zum Konfigurieren der neuen Multisite-Einstellungen verwendet.

3. **Öffnen Sie die Netzwerkkonfigurationsdatei.** Öffnen Sie die Netzwerkkonfigurationsdatei, die Sie im letzten Schritt heruntergeladen haben. Verwenden Sie dazu einen beliebigen XML-Editor. Die Datei sollte in etwa wie folgt aussehen:

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

4. **Fügen Sie der Netzwerkkonfigurationsdatei mehrere Standortverweise hinzu**. Beim Hinzufügen oder Entfernen der Informationen für Standortverweise nehmen Sie Konfigurationsänderungen an „ConnectionsToLocalNetwork/LocalNetworkSiteRef“ vor. Wenn Sie einen neuen Verweis für einen lokalen Standort hinzufügen, erstellt Azure einen neuen Tunnel. Das folgende Beispiel zeigt die Netzwerkkonfiguration für eine Einzelstandortverbindung.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	Um weitere Standortverweise hinzuzufügen (d. h. eine Multisite-Konfiguration zu erstellen), fügen Sie einfach wie im folgenden Beispiel gezeigt zusätzliche Zeilen „LocalNetworkSiteRef“ hinzu:

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

5. **Speichern Sie die Netzwerkkonfigurationsdatei, und importieren Sie sie.** Informationen zum Importieren der Netzwerkkonfigurationsdatei finden Sie unter [So importieren Sie die Netzwerkeinstellungen](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). Wenn Sie diese Datei mit den Änderungen importieren, werden die neuen Tunnel hinzugefügt. Die Tunnel verwenden das dynamische Gateway, das Sie zuvor erstellt haben.

6. **Laden Sie die vorinstallierten Schlüssel für die VPN-Tunnel herunter.** Nachdem die neuen Tunnel hinzugefügt wurden, rufen Sie mit dem PowerShell-Cmdlet „Get-AzureVNetGatewayKey“ die vorinstallierten IPSec-/IKE-Schlüssel für jeden Tunnel ab.

	Beispiel:

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	Sie können auch die REST-API *Get Virtual Network Gateway Shared Key* zum Abrufen der vorinstallierten Schlüssel verwenden.

## Überprüfen der Verbindungen

**Überprüfen Sie den Multisite-Tunnelstatus.** Nachdem Sie die Schlüssel für jeden Tunnel heruntergeladen haben, sollten Sie die Verbindungen überprüfen. Verwenden Sie wie im folgenden Beispiel gezeigt *Get-AzureVnetConnection*, um eine Liste der virtuellen Netzwerktunnel abzurufen. „VNet1“ ist der Name des VNets.

		Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## Nächste Schritte

Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN-Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=AcomDC_0128_2016-->