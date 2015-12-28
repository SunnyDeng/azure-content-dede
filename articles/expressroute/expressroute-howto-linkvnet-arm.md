<properties 
   pageTitle="Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen | Microsoft Azure"
   description="In diesem Dokument erhalten Sie einen Überblick über das Verknüpfen von virtuellen Netzwerken (VNets) mit ExpressRoute-Verbindungen."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/09/2015"
   ms.author="ganesr" />

# Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)
- [Template - Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

In diesem Artikel erhalten Sie eine Übersicht über das Verknüpfen von virtuellen Netzwerken (VNETs) mit ExpressRoute-Verbindungen. Virtuelle Netzwerke können Teil desselben Abonnements sein oder zu einem anderen Abonnement gehören. Dieser Artikel gilt für virtuelle Netzwerke, für die das klassische Bereitstellungsmodell verwendet wird. Wenn Sie ein virtuelles Netzwerk verknüpfen möchten, das mit der Azure-Ressourcen-Manager-Bereitstellungsmethode bereitgestellt wurde, können Sie dazu eine Vorlage verwenden. Über die Registerkarte oben können Sie zur Vorlage navigieren.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Konfigurationsvoraussetzungen

- Sie benötigen die neueste Version der Azure PowerShell-Module (also mindestens Version 1.0). 
- Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.
- Sie benötigen eine aktive ExpressRoute-Verbindung. Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Verbindung muss sich im Zustand „provisioned“ und „enabled“ befinden, damit Sie die unten beschriebenen Cmdlets ausführen können.
- Sie benötigen eine aktive ExpressRoute-Verbindung. 
	- Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren. 
	- Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](expressroute-howto-routing-arm.md). 
	- Das private Azure-Peering muss konfiguriert sein, und das BGP-Peering zwischen Ihrem Netzwerk und Microsoft muss aktiv sein, damit End-to-End-Konnektivität bereitgestellt werden kann.
	- Sie müssen ein virtuelles Netzwerk und ein virtuelles Netzwerkgateway erstellt und vollständig bereitgestellt haben. Durchführen der Schritte zum Erstellen eines [VPN-Gateways](../articles/vpn-gateway-create-site-to-site-rm-powershell.md)

Sie können bis zu 10 virtuelle Netzwerke mit einer ExpressRoute-Verbindung verknüpfen. Alle ExpressRoute-Verbindungen müssen sich in derselben geopolitischen Region befinden. Wenn Sie das ExpressRoute Premium-Add-On aktiviert haben, können Sie eine größere Anzahl von virtuellen Netzwerken mit der ExpressRoute-Verbindung verknüpfen. Weitere Informationen zum Premium-Add-On finden Sie in den [häufig gestellten Fragen](expressroute-faqs.md).

## Verbinden eines VNETs im gleichen Azure-Abonnement mit einer ExpressRoute-Verbindung

Sie können das folgende Cmdlet verwenden, um ein virtuelles Netzwerkgateway mit einer ExpressRoute-Verbindung zu verbinden. Stellen Sie sicher, dass das Gateway für das virtuelle Netzwerk erstellt wurde und für das Erstellen von Verknüpfungen bereit ist, bevor Sie das Cmdlet ausführen.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## Verbinden eines virtuellen Netzwerks in einem anderen Azure-Abonnement mit einer ExpressRoute-Verbindung

Eine ExpressRoute-Verbindung kann für mehrere Abonnements freigegeben werden. Die folgende Abbildung zeigt eine einfache schematische Darstellung der Freigabe von ExpressRoute-Verbindungen für mehrere Abonnements. Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jede der Abteilungen innerhalb der Organisation kann ihr eigenes Abonnement zum Bereitstellen von Diensten verwenden, für die Verbindung mit dem lokalen Netzwerk kann jedoch eine einzelne gemeinsam genutzte ExpressRoute-Verbindung verwendet werden. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die ExpressRoute-Verbindung besitzen. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Verbindung nutzen.

>[AZURE.NOTE]Konnektivitäts- und Bandbreitengebühren für die dedizierte Verbindung werden dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt. Alle virtuellen Netzwerke verwenden gemeinsam dieselbe Bandbreite.

![Abonnementübergreifende Konnektivität](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Verwaltung

Der *Verbindungsbesitzer* ist autorisierter Hauptbenutzer der ExpressRoute-Verbindungsressource. Der Verbindungsbesitzer kann Autorisierungen erstellen, die durch *Verbindungsbenutzer* eingelöst werden können. *Verbindungsbenutzer* sind Besitzer virtueller Netzwerkgateways (die sich nicht in demselben Abonnement wie die ExpressRoute-Verbindung befinden). *Verbindungsbenutzer* können Autorisierungen einlösen (eine Autorisierung für jedes virtuelle Netzwerk.

Der *Verbindungsbesitzer* hat die Möglichkeit, Autorisierungen jederzeit zu ändern und aufzuheben. Das Aufheben einer Autorisierung führt dazu, dass alle Verbindungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

### Aktionen als Verbindungsbesitzer 

#### Erstellen einer Autorisierung
	
Der Verbindungsbesitzer erstellt eine Autorisierung. Dies führt zur Erstellung eines Autorisierungsschlüssels, der von einem Verbindungsbenutzer verwendet werden kann, um dessen virtuelle Netzwerkgateways mit der ExpressRoute-Verbindung zu verbinden. Eine Autorisierung gilt nur für jeweils eine Verbindung.

Der nachstehende Cmdlet-Ausschnitt veranschaulicht das Erstellen einer Autorisierung.

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

Die Antwort auf diese enthält Schlüssel und Zustand für die Autorisierung.

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### Überprüfen von Autorisierungen

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung alle für eine bestimmte Verbindung ausgestellten Autorisierungen überprüfen.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### Hinzufügen von Autorisierungen

Mit dem folgenden Cmdlet kann der Verbindungsbesitzer Autorisierungen hinzufügen.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### Löschen von Autorisierungen

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung Autorisierungen für einen Benutzer widerrufen oder löschen.

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### Aktionen als Verbindungsbenutzer

Der Verbindungsbenutzer benötigt die Peer-ID und einen Autorisierungsschlüssel vom Verbindungsbesitzer. Der Verbindungsschlüssel entspricht etwa dem unten gezeigten Format:


Der Autorisierungsschlüssel ist eine GUID.

#### Einlösen von Verbindungsautorisierungen

Mit dem folgenden Cmdlet können Benutzer einer Verbindung eine Verknüpfungsautorisierung abrufen.

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### Freigeben von Verbindungsautorisierungen

Sie können eine Autorisierung durch das Löschen der Verbindung freigeben, die die ExpressRoute-Verbindung mit dem virtuellen Netzwerk verknüpft.

## Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!---HONumber=AcomDC_1217_2015-->