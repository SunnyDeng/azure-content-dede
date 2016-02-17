<properties
   pageTitle="Konfigurieren von ExpressRoute- und Standort-zu-Standort-VPN-Verbindungen, die gleichzeitig bestehen können | Microsoft Azure"
   description="Dieser Artikel führt Sie durch die Konfiguration von ExpressRoute- und Site-to-Site-VPN-Verbindungen, die gleichzeitig bestehen können."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Konfigurieren von gleichzeitig bestehenden ExpressRoute- und Standort-zu-Standort-VPN-Verbindungen für ein VNet

Die Möglichkeit zum Konfigurieren von Standort-zu-Standort-VPN und ExpressRoute bietet mehrere Vorteile. Sie können Standort-zu-Standort-VPN als sicheren Failoverpfad für ExressRoute konfigurieren oder Standort-zu-Standort-VPNs für die Verbindung mit Websites nutzen, die nicht Teil Ihres Netzwerks, aber über ExpressRoute verbunden sind. Wir werden die Schritte zum Konfigurieren von beiden Szenarien in diesem Artikel behandeln. Derzeit können Sie diese Konfiguration nur mit dem klassischen Bereitstellungsmodell erstellen. Sobald Dokumentation für das Ressourcen-Manager-Bereitstellungsmodell verfügbar ist, stellen wir hier einen Link bereit.

>[AZURE.IMPORTANT] Sie sollten wissen, dass Azure derzeit mit zwei Bereitstellungsmodellen arbeitet: der Bereitstellung mit dem Ressourcen-Manager und der klassischen Bereitstellung. Bevor Sie Ihre Konfiguration beginnen, sollten Sie sicherstellen, dass Sie die Bereitstellungsmodelle und -tools verstehen. Informationen zu den Bereitstellungsmodellen finden Sie unter [Azure-Bereitstellungsmodelle](../azure-classic-rm.md).


ExpressRoute-Verbindungen müssen bereits konfiguriert sein, bevor Sie die folgenden Schritte ausführen. Stellen Sie sicher, dass Sie die Handbücher zu [ExpressRoute-Verbindung erstellen](expressroute-howto-circuit-classic.md) und [Konfigurieren des Routings](expressroute-howto-routing-classic.md) gelesen haben, bevor Sie die folgenden Schritte unternehmen.

## Grenzwerte und Einschränkungen

- **Transitrouting wird nicht unterstützt:** Ein Routing (über Azure) zwischen Ihrem lokalen Netzwerk mit Standort-zu-Standort-VPN-Verbindung und Ihrem lokalen Netzwerk mit ExpressRoute-Verbindung ist nicht möglich.
- **Punkt-zu-Standort-Verbindungen werden nicht unterstützt:** Sie können keine Punkt-zu-Standort-VPN-Verbindungen mit einem VNET herstellen, für das eine Verbindung mit ExpressRoute besteht. Punkt-zu-Standort-VPN- und ExpressRoute-Verbindungen können für ein VNET nicht gleichzeitig vorhanden sein.
- **Erzwungenes Tunneln kann nicht auf dem Site-to-Site-VPN-Gateway aktiviert werden:** Sie können nur "erzwingen", dass sämtlicher Internetdatenverkehr über ExpressRoute an Ihr lokales Netzwerk zurückgeleitet wird. 
- **Nur Standard- oder Hochleistungsgateways:** Sie müssen einen Standard- oder einen Hochleistungsgateway sowohl für den ExpressRoute-Gateway als auch den Standort-zu-Standort-VPN-Gateway verwenden. Informationen zu Gateway-SKUs finden Sie unter [Gateway-SKUs](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Voraussetzungen für eine statische Route:** Wenn Ihr lokales Netzwerk mit ExpressRoute und einem Standort-zu-Standort-VPN verbunden ist, müssen Sie eine statische Route in Ihrem lokalen Netzwerk konfiguriert haben, um die Standort-zu-Standort-VPN-Verbindung an das öffentliche Internet weiterzuleiten.
- **ExpressRoute-Gateway muss zuerst konfiguriert werden:** Sie müssen zuerst das ExpressRoute-Gateway erstellen, bevor Sie das Standort-zu-Standort-VPN-Gateway hinzufügen.

## Konfigurieren einer Standort-zu-Standort-VPN als Failoverpfad für ExpressRoute

Sie können eine Standort-zu-Standort-VPN-Verbindung als Sicherung für ExpressRoute konfigurieren. Dies gilt nur für virtuelle Netzwerke, die mit dem privaten Azure-Peeringpfad verknüpft sind. Es gibt keine VPN-basierte Failoverlösung für Dienste, auf die über öffentliche Azure- und Microsoft-Peerings zugegriffen werden. Die ExpressRoute-Verbindung ist immer der primäre Link. Durch den Pfad der Standort-zu-Standort-VPN fließen die Daten nur, wenn bei der ExpressRoute-Verbindung ein Fehler auftritt.

![Koexistenz](media/expressroute-howto-coexist-classic/scenario1.jpg)

## Konfigurieren einer Standort-zu-Standort-VPN zum Herstellen einer Verbindung mit Websites, die nicht über ExpressRoute verbunden sind

Sie können Ihr Netzwerk so konfigurieren, dass einige Seiten direkt mit Azure über eine Standort-zu-Standort-VPN und andere über ExpressRoute verbunden sind.

![Koexistenz](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] Sie können ein virtuelles Netzwerk nicht als Transitrouter konfigurieren.

## Erstellen und konfigurieren

Es stehen zwei unterschiedliche Verfahren für die Konfiguration von Verbindungen zur Auswahl, die gleichzeitig bestehen können. Welches Konfigurationsverfahren Sie wählen, hängt davon ab, ob Sie über ein vorhandenes virtuelles Netzwerk verfügen, mit dem Sie eine Verbindung aufbauen möchten, oder ob Sie ein neues virtuelles Netzwerk erstellen möchten.

- **Erstellen ein neues virtuellen Netzwerks mit gleichzeitig bestehenden Verbindungen:**
	
	Wenn Sie noch nicht über ein virtuelles Netzwerk verfügen, wird Sie dieses Verfahren durch die Schritte zum Erstellen eines neuen virtuellen Netzwerks und neuer ExpressRoute- sowie Standort-zu-Standort-VPN-Verbindungen führen. Zum Konfigurieren führen Sie die folgenden Schritte im Artikelabschnitt **Erstellen eines neuen virtuellen Netzwerks mit ExpressRoute und Standort-zu-Standort-Konnektivität**.

- **Konfigurieren Ihrer vorhandenen virtuellen Netzwerke für gleichzeitig bestehende Verbindungen:**

	Möglicherweise haben Sie bereits ein virtuelles Netzwerk mit einer vorhandenen Standort-zu-Standort-VPN-Verbindung oder einer ExpressRoute-Verbindung. Der Abschnitt **Konfigurieren von gleichzeitig bestehenden Verbindungen für Ihr vorhandenes virtuelles Netzwerk** führt Sie durch die Schritte zum Löschen des Gateways und anschließendem Erstellen neuer ExpressRoute- und Standort-zu-Standort-VPN-Verbindungen. Beachten Sie, dass die Schritte beim Erstellen von neuen Verbindungen in einer ganz bestimmten Reihenfolge ausgeführt werden müssen. Verwenden Sie die Anweisungen nicht in anderen Artikeln, um Ihre Gateways und Verbindungen zu erstellen.

	In diesem Verfahren erfordert das Erstellen von gleichzeitig bestehenden Verbindungen das Löschen Ihres Gateway löschen und das anschließende Konfigurieren neuer Gateways. Das bedeutet, dass Sie für Ihre standortübergreifenden Verbindungen Ausfallzeiten haben werden, während Sie Ihr Gateway und Ihre Verbindungen löschen und neu erstellen, aber Sie müssen keine Ihrer VMs oder Dienste auf ein neues virtuelles Netzwerk migrieren. Die VMs und Dienste werden immer noch in der Lage sein, über den Load Balancer zu kommunizieren, während Sie Ihr Gateway konfigurieren, wenn sie zu diesem Zweck konfiguriert sind.


## Erstellen eines neuen virtuellen Netzwerks sowohl mit ExpressRoute- als auch Standort-zu-Standort-Konnektivität

Dieses Verfahren führt Sie durch das Erstellen eines VNets sowie das Erstellen von gleichzeitig bestehenden Standort-zu-Standort- und ExpressRoute-Verbindungen.

1. Stellen Sie sicher, dass Sie über die neueste Version von PowerShell-Cmdlets verfügen. Sie können die neuesten PowerShell-Cmdlets aus dem PowerShell-Abschnitt der [Downloadseite](https://azure.microsoft.com/downloads/) herunterladen und installieren.

2. Geben Sie Ihrem Virtual Network einen Namen. Weitere Informationen zum Arbeiten mit der Netzwerk-Konfigurationsdatei finden Sie unter [Konfigurieren eines Virtual Network mithilfe einer Netzwerk-Konfigurationsdatei](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Weitere Informationen zum Konfigurationsschema finden Sie unter [Konfigurationsschema für Azure Virtual Network](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Wenn Sie Ihr Schema erstellen, stellen Sie sicher, dass Sie die folgenden Werte verwenden:

	- Das Gateway-Subnetz für das virtuelle Virtual Network muss /27 sein oder ein kürzeres Präfix (wie z. B. /26 oder /25).
	- Der Gateway-Verbindungstyp ist "dediziert".

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>

3. Laden Sie nach dem Erstellen und Konfigurieren Ihrer XML-Schemadatei die Datei hoch. Dadurch wird Ihr Virtual Network erstellt.

	Verwenden Sie das folgende Cmdlet zum Hochladen Ihrer Datei und um den Wert mit Ihrem eigenen zu ersetzen.

		Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. Erstellen eines ExpressRoute-Gateway. Achten Sie darauf, dass Sie die GatewaySKU als *Standard* oder *HighPerformance* angeben, und den GatewayType als *DynamicRouting*.

	Verwenden Sie das folgende Beispiel, um die Werte durch Ihre eigenen zu ersetzen.

		New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. Verknüpfen Sie das ExpressRoute-Gateway mit dem ExpressRoute-Schaltkreis. Nachdem dieser Schritt abgeschlossen ist, wird die Verbindung zwischen dem lokalen Netzwerk und Azure durch ExpressRoute eingerichtet.

		New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. Erstellen Sie als Nächstes Ihr Standort-zu-Standort-VPN-Gateway. Die GatewaySKU muss *Standard* oder *HighPerformance* sein, und der GatewayType *DynamicRouting*.

		New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

	Verwenden Sie zum Abrufen der Einstellungen des Virtual Network-Gateway, einschließlich der Gateway-ID und der öffentlichen IP, das`Get-AzureVirtualNetworkGateway` Cmdlet.

		Get-AzureVirtualNetworkGateway

		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. Erstellen Sie eine VPN-Gateway-Entität für einen lokalen Standort. Mit diesem Befehl wird das lokale VPN-Gateway nicht konfiguriert. Stattdessen können Sie mit ihm die Einstellungen des lokalen Gateway bereitstellen, wie z. B. die öffentliche IP-Adresse und der lokale Adressraum, sodass eine Verbindung mit dem Azure-VPN-Gateway hergestellt werden kann.

	> [AZURE.IMPORTANT] Der lokale Standort für das Standort-zu-Standort-VPN ist nicht in der Netcfg-Datei definiert. Stattdessen müssen Sie dieses Cmdlet verwenden, um die lokalen Standort-Parameter anzugeben. Sie können sie nicht über das klassische Azure-Portal oder die Netcfg-Datei definieren.

	Ersetzen Sie anhand des folgenden Beispiels die Werte mit Ihren eigenen.

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>`

	> [AZURE.NOTE] Wenn Ihr lokales Netzwerk mehrere Routen hat, können Sie diese als ein Array übergeben. $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")


	Verwenden Sie zum Abrufen der Einstellungen des Virtual Network-Gateway, einschließlich der Gateway-ID und der öffentlichen IP, das`Get-AzureVirtualNetworkGateway` Cmdlet. Siehe folgendes Beispiel.

		Get-AzureLocalNetworkGateway

		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded


8. Konfigurieren Sie Ihr lokales VPN-Gerät für die Verbindung zum neuen Gateway. Verwenden Sie die Informationen, die Sie in Schritt 6 abgerufen haben, wenn Sie Ihr VPN-Gerät konfigurieren. Weitere Informationen zu VPN-Gerätekonfiguration finden Sie unter [VPN-Gerätekonfiguration](http://go.microsoft.com/fwlink/p/?linkid=615099).

9. Verknüpfen Sie das Standort-zu-Standort-VPN-Gateway in Azure mit dem lokalen Gateway.

	In diesem Beispiel ist ConnectedEntityId die lokale Gateway-ID, die sich beim Ausführen finden können`Get-AzureLocalNetworkGateway`. Die VirtualNetworkGatewayId können Sie unter Verwendung des `Get-AzureVirtualNetworkGateway` Cmdlet finden. Nach diesem Schritt wird die Verbindung zwischen Ihrem lokalen Netzwerk und Azure über die Standort-zu-Standort-VPN-Verbindung hergestellt.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`

## Konfigurieren von gleichzeitig bestehenden Verbindungen für Ihr vorhandenes virtuelles Netzwerk

Wenn Sie ein vorhandenes virtuelles Netzwerk über eine ExpressRoute- oder Standort-zu-Standort-VPN-Verbindung verbunden haben, um eine Verbindung beider Verbindungen mit dem vorhandenen Virtual Network zu ermöglichen, müssen Sie das vorhandene Gateway zunächst löschen. Dies bedeutet, dass lokal Ihre Verbindung mit Ihrem Virtual Network über das Gateway verloren geht, während Sie an dieser Konfiguration arbeiten.

**Bevor Sie mit der Konfiguration beginnen:** Überprüfen Sie, dass Sie in Ihrem Virtual Network noch ausreichend IP-Adressen vorhanden sind, sodass Sie die Größe des Gateway-Subnetzes erhöhen können.

1. Laden Sie die aktuelle Version der PowerShell-Cmdlets herunter. Sie können die neuesten PowerShell-Cmdlets aus dem PowerShell-Abschnitt der [Downloadseite](https://azure.microsoft.com/downloads/) herunterladen und installieren.

2. Löschen Sie das vorhandene Standort-zu-Standort-VPN-Gateway. Nutzen Sie das folgende Cmdlet, um die Werte mit Ihren eigenen auszutauschen.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. Exportieren Sie das Virtual Network-Schema. Nutzen Sie das folgende PowerShell-Cmdlet, um die Werte mit Ihren eigenen auszutauschen.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`

3. Bearbeiten Sie die Netzwerk-Konfigurationsdateischema, sodass das Gateway-Subnetz /27 ist oder ein kürzeres Präfix (wie z. B. /26 oder /25). Siehe folgendes Beispiel. Weitere Informationen zum Arbeiten mit der Netzwerk-Konfigurationsdatei finden Sie unter [Konfigurieren eines Virtual Network mithilfe einer Netzwerk-Konfigurationsdatei](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Weitere Informationen zum Konfigurationsschema finden Sie unter [Konfigurationsschema für Azure Virtual Network](https://msdn.microsoft.com/library/azure/jj157100.aspx).

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

4. Wenn Ihr vorheriges Gateway ein Standort-zu-Standort-VPN war, müssen Sie auch den Verbindungstyp auf **Dediziert** ändern.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>

5. An diesem Punkt verfügen Sie über ein VNet ohne Gateways. Sie können mit **Schritt 4** im Abschnitt [Erstellen eines neuen virtuellen Netzwerks sowohl mit ExpressRoute- als auch Standort-zu-Standort-Konnektivität](#create-a-new-virtual-network-with-both-expressroute-and-site-to-site-connectivity) fortfahren, um neue Gateways zu erstellen und Ihre Verbindungen zu vervollständigen.

## Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!---HONumber=AcomDC_0204_2016-->