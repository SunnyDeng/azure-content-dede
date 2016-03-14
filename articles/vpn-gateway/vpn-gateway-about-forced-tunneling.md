<properties pageTitle="Konfigurieren der Tunnelerzwingung für VPN-Gateways mit PowerShell | Microsoft Azure" description="Sie können in einem virtuellen Netzwerk im klassischen Bereitstellungsmodell mit einem standortübergreifenden VPN-Gateway die Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort „erzwingen“." services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carmonm" editor="" tags="azure-service-management"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="cherylmc" />

# Konfigurieren der Tunnelerzwingung für VPN-Gateways mit dem klassischen Bereitstellungsmodell

> [AZURE.SELECTOR]
- [PowerShell – Dienstverwaltung](vpn-gateway-about-forced-tunneling.md)
- [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)

Über die Tunnelerzwingung können Sie die Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort "erzwingen". Sie verwenden dazu einen Standort-zu-Standort-VPN-Tunnel für die Kontrolle und Überwachung. Dies ist eine wichtige Sicherheitsvoraussetzung der IT-Richtlinien für die meisten Unternehmen. Ohne die Tunnelerzwingung wird der Internetdatenverkehr Ihrer virtuellen Computer in Azure immer direkt von der Azure-Netzwerkinfrastruktur an das Internet geleitet, ohne dass Sie die Möglichkeit haben, diesen zu überprüfen oder zu überwachen. Nicht autorisierter Zugriff auf das Internet kann potenziell zur Offenlegung von Informationen oder anderen Arten von Sicherheitsverletzungen führen.


[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]


**Informationen zu Azure-Bereitstellungsmodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Anforderungen und Überlegungen

Das erzwungene Tunneln in Azure wird über benutzerdefinierte Routen in Virtual Network konfiguriert. Das Umleiten von Datenverkehr an einen lokalen Standort wird als eine Standardroute zum Azure-VPN-Gateway umgesetzt. Im folgende Abschnitt werden die aktuellen Einschränkung für die Routingtabelle und die Routen in Azure Virtual Network aufgeführt:


-  Jedes Subnetz des virtuellen Netzwerks verfügt über eine integrierte Systemroutingtabelle. Die Systemroutingtabelle verfügt über die folgenden drei Gruppen von Routen:

	- **Lokale VNET-Routen:** direkt zu den virtuelle Zielcomputern im gleichen virtuellen Netzwerk
	
	- **Lokale Routen:** zum Azure-VPN-Gateway
	
	- **Standardroute:** direkt zum Internet. Beachten Sie, dass Pakete an private IP-Adressen, die nicht durch die vorherigen beiden Routen abgedeckt sind, verworfen werden.


-  Sie können mit der Veröffentlichung von benutzerdefinierten Routen eine Routingtabelle erstellen, um eine Standardroute hinzufügen. Anschließend verknüpfen Sie dann die Routingtabelle mit den VNet-Subnetzen, um die Tunnelerzwingung in diesen Subnetzen zu aktivieren.

- Sie müssen einen "Standardstandort" unter den standortübergreifenden lokalen Standorten auswählen, der mit dem virtuellen Netzwerk verbunden ist.

- Die Tunnelerzwingung muss einem VNet zugeordnet werden, das über ein VPN-Gateway für dynamisches Routing (kein statisches Gateway) verfügt.
 
- Die ExpressRoute-Tunnelerzwingung kann über diesen Mechanismus nicht konfiguriert werden. Sie wird stattdessen durch Anfordern einer Standardroute über die ExpressRoute-BGP-Peeringsitzungen aktiviert. Weitere Informationen finden Sie unter der [ExpressRoute-Dokumentation](https://azure.microsoft.com/documentation/services/expressroute/).



## Konfigurationsübersicht

Im Beispiel unten wird für das Front-End-Subnetz kein Tunneln erzwungen. Die Workloads im Frontend-Subnetz können weiterhin Kundenanfragen direkt aus dem Internet akzeptieren und darauf reagieren. Für die Subnetze "Midtier" und "Backend" wird das Tunneln erzwungen.

Bei allen ausgehenden Verbindungen mit dem Internet aus diesen zwei Subnetzen wird eine Umleitung an einen lokalen Standort über einen der S2S-VPN-Tunnel erzwungen. Dadurch können Sie den Internetzugriff aus Virtual Machines und Cloud Services in Azure einschränken und überprüfen, während die erforderliche mehrschichtige Dienstarchitektur weiterhin in Betrieb bleibt. Sie haben auch die Möglichkeit, das erzwungene Tunneln auf alle virtuellen Netzwerke anzuwenden, wenn in diesen keine Workloads mit Internetverbindung erforderlich sind.


![Tunnelerzwingung](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.

- Ein konfiguriertes virtuelles Netzwerk.

- Die neueste Version der Azure PowerShell-Cmdlets mit dem Webplattform-Installer. Sie können die neueste Version im Abschnitt **Windows PowerShell** der [Downloadseite](https://azure.microsoft.com/downloads/) herunterladen und installieren.

## Konfigurieren der Tunnelerzwingung

Das folgende Verfahren hilft Ihnen bei der Angabe der Tunnelerzwingung für ein virtuelles Netzwerk. Die Konfigurationsschritte entsprechen denen im folgenden Beispiel der Konfigurationsdatei (netcfg-Datei) des virtuellen Netzwerks.



	<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
	</VirtualNetworkSite>

In diesem Beispiel verfügt das virtuelle Netzwerk „MultiTier-VNet“ über drei Subnetze: *Frontend*, *Midtier* und *Backend*, die vier standortübergreifende Verbindungen aufweisen: *DefaultSiteHQ* und drei *Verzweigungen*. Mit den Verfahrensschritten wird festgelegt, dass *DefaultSiteHQ* die Standard-Standortverbindung für die Tunnelerzwingung ist und dass die Subnetze *Midtier* und *Backend* die Tunnelerzwingung verwenden müssen.


1. Erstellen Sie eine Routingtabelle. Verwenden Sie das folgende Cmdlet zum Erstellen der Routingtabelle.

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

1. Fügen Sie der Routingtabelle eine Standardroute hinzu.

	Das folgende Beispiel-Cmdlet fügt der in Schritt 1 erstellten Routingtabelle eine Standardroute hinzu. Beachten Sie, dass die einzige unterstützte Route das Zielpräfix von "0.0.0.0/0" zum nächsten Hop "VPNGateway" ist.
 
		Set-AzureRoute –RouteTableName "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

1. Ordnen Sie die Routingtabelle den Subnetzen zu.

	Nachdem eine Routingtabelle erstellt und eine Route hinzugefügt wurde, verwenden Sie das folgende Cmdlet, um die Routingtabelle einem VNet-Subnetz hinzuzufügen oder zuzuordnen. In den Beispielen unten wird die Routingtabelle "MyRouteTable" den Subnetzen "Midtier" und "Backend" von VNet MultiTier-VNet hinzugefügt.

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

1. Weisen Sie einen Standardstandort für die Tunnelerzwingung zu.

	Im vorherigen Schritt wurde mit dem Cmdlet-Beispielskript die Routingtabelle erstellt und zwei VNet-Subnetzen zugeordnet. Im letzten Schritt wird ein lokaler Standort aus den Verbindungen mit mehreren Standorten des virtuellen Netzwerks als Standardstandort oder -tunnel ausgewählt.

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## Weitere PowerShell-Cmdlets

Im folgenden finden Sie einige weitere PowerShell-Cmdlets, die bei der Verwendung von Konfigurationen mit Tunnelerzwingung hilfreich sein können.

**Zum Löschen von Routingtabellen:**

	Remove-AzureRouteTable -RouteTableName <routeTableName>

**Zum Auflisten von Routingtabellen:**

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

**Zum Löschen von Routen aus einer Routentabelle:**

	Remove-AzureRouteTable –Name <routeTableName>

**Zum Entfernen von Routen aus einem Subnetz:**

	Remove-AzureSubnetRouteTable –VNetName <virtualNetworkName> -SubnetName <subnetName>

**Zum Auflisten der einem Subnetz zugeordneten Routingtabelle:**
	
	Get-AzureSubnetRouteTable -VNetName <virtualNetworkName> -SubnetName <subnetName>

**Zum Entfernen eines Standardstandorts aus einem VNet VPN-Gateway:**

	Remove-AzureVnetGatewayDefaultSites -VNetName <virtualNetworkName>

<!---HONumber=AcomDC_0302_2016-->