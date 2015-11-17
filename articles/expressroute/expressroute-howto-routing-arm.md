<properties
   pageTitle="Vorgehensweise: Konfigurieren des Routings für eine ExpressRoute-Verbindung | Microsoft Azure"
   description="In diesem Artikel werden Sie durch die Schritte zum Erstellen und Bereitstellen des privaten, öffentlichen und Microsoft-Peerings einer ExpressRoute-Verbindung geführt. Außerdem wird veranschaulicht, wie Sie den Status überprüfen, Updates durchführen oder Peerings für die Verbindung löschen."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/04/2015"
   ms.author="ganesr"/>

# Erstellen und Ändern der ExpressRoute-Routingkonfiguration

> [AZURE.SELECTOR]
[PowerShell Classic](expressroute-howto-routing-classic.md)
[PowerShell Resource Manager](expressroute-howto-routing-arm.md)

In diesem Artikel werden Sie durch die Schritte zum Erstellen und Verwalten der Routingkonfiguration einer ExpressRoute-Verbindung mithilfe von PowerShell-Cmdlets und des ARM-Bereitstellungsmodells geführt. In den Schritten unten wird auch veranschaulicht, wie Sie den Status prüfen, ein Update durchführen oder Peerings für eine ExpressRoute-Verbindung löschen oder deren Bereitstellung aufheben.

>[AZURE.IMPORTANT]Sie sollten wissen, dass Azure derzeit mit zwei Bereitstellungsmodellen arbeitet: der Bereitstellung mit dem Ressourcen-Manager und der klassischen Bereitstellung. Bevor Sie Ihre Konfiguration beginnen, sollten Sie sicherstellen, dass Sie die Bereitstellungsmodelle und -tools verstehen. Informationen zu den Bereitstellungsmodellen finden Sie unter [Azure-Bereitstellungsmodelle](../azure-classic-rm.md).


## Konfigurationsvoraussetzungen

- Sie benötigen die neueste Version der Azure PowerShell-Module. Sie können das neueste PowerShell-Modul aus dem PowerShell-Abschnitt der [Azure-Downloadseite](http://azure.microsoft.com/downloads) herunterladen. Befolgen Sie die Anleitung auf der Seite [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Sie erhalten dort eine Schritt-für-Schritt-Anleitung zum Konfigurieren des Computers für die Verwendung der Azure PowerShell-Module. 
- Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.
- Sie benötigen eine aktive ExpressRoute-Verbindung. Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie die Verbindung vom Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Verbindung muss sich im Zustand „provisioned“ und „enabled“ befinden, damit Sie die unten beschriebenen Cmdlets ausführen können.

>[AZURE.IMPORTANT]Diese Anweisungen gelten nur für Verbindungen, die über Service Provider erstellt wurden, von denen Layer 2-Konnektivitätsdienste angeboten werden. Wenn Sie einen Service Provider nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IPVPN, z. B. MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie. In diesem Fall können Sie keine Peerings erstellen oder verwalten.

Sie können eine, zwei oder alle drei Peerings (Azure privat, Azure öffentlich und Microsoft) für eine ExpressRoute-Verbindung konfigurieren. Sie können Peerings in beliebiger Reihenfolge konfigurieren. Sie müssen jedoch sicherstellen, dass Sie die Konfiguration jedes Peerings einzeln nacheinander durchführen.

## Privates Azure-Peering

Dieser Abschnitt enthält Anweisungen zum Erstellen, Abrufen, Aktualisieren und Löschen der privaten Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung.

### Erstellen eines privaten Azure-Peerings

1. **Importieren Sie das PowerShell-Modul für ExpressRoute.**
	
 	Sie müssen den neuesten PowerShell-Installer aus dem [PowerShell-Katalog](http://www.powershellgallery.com/) installieren und die Azure-Ressourcen-Manager-Module in die PowerShell-Sitzung importieren, um die ExpressRoute-Cmdlets verwenden zu können. Sie müssen PowerShell als Administrator ausführen.

	    Install-Module AzureRM

		Install-AzureRM

	Importieren Sie alle AzureRM.*-Module im bekannten semantischen Versionsbereich.

		Import-AzureRM

	Sie können auch einfach ein ausgewähltes Modul im bekannten semantischen Versionsbereich importieren.
		
		Import-Module AzureRM.Network 

	Melden Sie sich bei Ihrem Konto an.

		Login-AzureRmAccount

	Wählen Sie das Abonnement aus, mit dem die ExpressRoute-Verbindung erstellt werden soll.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Erstellen Sie eine ExpressRoute-Verbindung.**
	
	Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie die Verbindung vom Konnektivitätsanbieter bereitstellen. **Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie anfordern, dass der Anbieter für Sie das private Azure-Peering aktiviert. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen.** Falls Ihr Konnektivitätsanbieter das Routing für Sie nicht verwaltet, müssen Sie nach dem Erstellen der Verbindung die unten angegebenen Anweisungen befolgen.

3. **Überprüfen Sie die ExpressRoute-Verbindung, um sicherzustellen, dass sie bereitgestellt wurde.**

	Sie müssen zuerst überprüfen, ob die ExpressRoute-Verbindung bereitgestellt (Provisioned) und aktiviert (Enabled) wurde. Betrachten Sie das folgende Beispiel.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	Die Antwort wird in etwa wie im folgendem Beispiel gezeigt lauten:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


4. **Konfigurieren Sie das private Azure-Peering für die Verbindung.**

	Stellen Sie sicher, dass Sie über die folgenden Elemente verfügen, bevor Sie mit den nächsten Schritten fortfahren:

	- Ein /30-Subnetz für die primäre Verknüpfung. Dieses darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist.
	- Ein /30-Subnetz für die sekundäre Verknüpfung. Dieses darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist.
	- Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
	- AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden. Sie können eine private AS-Nummer für dieses Peering verwenden. Achten Sie darauf, dass Sie nicht 65515 verwenden.
	- MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden. **Dies ist optional**.
	
	Sie können das folgende Cmdlet ausführen, um das private Azure-Peering für Ihre Verbindung zu konfigurieren.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	Sie können das unten angegebene Cmdlet verwenden, wenn Sie sich für den Einsatz eines MD5-Hash entscheiden.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	>[AZURE.IMPORTANT]Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.

### Abrufen von Details zum privaten Azure-Peering

Sie können die Konfigurationsdetails mit dem folgenden Cmdlet abrufen.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt	


### Aktualisieren der Konfiguration für privates Azure-Peering

Sie können einen beliebigen Teil der Konfiguration mit dem folgenden Cmdlet aktualisieren. Im folgenden Beispiel wird die VLAN-ID der Verbindung von 100 in 500 geändert.

	Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Löschen eines privaten Azure-Peerings

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Cmdlet ausführen.

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

>[AZURE.IMPORTANT]Sie müssen sicherstellen, dass die Verknüpfungen aller virtuellen Netzwerke mit der ExpressRoute-Verbindung aufgehoben werden, bevor Sie dieses Cmdlet ausführen.

## Öffentliches Azure-Peering

Dieser Abschnitt enthält Anweisungen zum Erstellen, Abrufen, Aktualisieren und Löschen der öffentlichen Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung.

### Erstellen von öffentlichem Azure-Peering

1. **Importieren Sie das PowerShell-Modul für ExpressRoute.**
	
 	Sie müssen den neuesten PowerShell-Installer aus dem [PowerShell-Katalog](http://www.powershellgallery.com/) installieren und die Azure-Ressourcen-Manager-Module in die PowerShell-Sitzung importieren, um die ExpressRoute-Cmdlets verwenden zu können. Sie müssen PowerShell als Administrator ausführen.

	    Install-Module AzureRM

		Install-AzureRM

	Importieren Sie alle AzureRM.*-Module im bekannten semantischen Versionsbereich.

		Import-AzureRM

	Sie können auch einfach ein ausgewähltes Modul im bekannten semantischen Versionsbereich importieren.
		
		Import-Module AzureRM.Network 

	Melden Sie sich bei Ihrem Konto an.

		Login-AzureRmAccount

	Wählen Sie das Abonnement aus, mit dem die ExpressRoute-Verbindung erstellt werden soll.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Erstellen einer ExpressRoute-Verbindung**
	
	Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie die Verbindung vom Konnektivitätsanbieter bereitstellen. **Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie anfordern, dass der Anbieter für Sie das private Azure-Peering aktiviert. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen.** Falls Ihr Konnektivitätsanbieter das Routing für Sie nicht verwaltet, müssen Sie nach dem Erstellen der Verbindung die unten angegebenen Anweisungen befolgen.

3. **Überprüfen Sie die ExpressRoute-Verbindung, um sicherzustellen, dass sie bereitgestellt wurde.**

	Sie müssen zuerst überprüfen, ob die ExpressRoute-Verbindung bereitgestellt (Provisioned) und aktiviert (Enabled) wurde. Betrachten Sie das folgende Beispiel.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	Die Antwort wird in etwa wie im folgendem Beispiel gezeigt lauten:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	

4. **Konfigurieren Sie das öffentliche Azure-Peering für die Verbindung.**

	Stellen Sie sicher, dass die folgenden Informationen vorliegen, bevor Sie fortfahren:

	- Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
	- Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
	- Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
	- AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden. Sie müssen eine öffentliche AS-Nummer für dieses Peering verwenden.
	- MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden. **Dies ist optional**.
	
	Sie können das folgende Cmdlet ausführen, um das öffentliche Azure-Peering für Ihre Verbindung zu konfigurieren.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	Sie können das unten angegebene Cmdlet verwenden, wenn Sie sich für die Verwendung eines MD5-Hash entscheiden.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


	>[AZURE.IMPORTANT]Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.

### Abrufen der Details für das öffentliche Azure-Peering

Sie können die Konfigurationsdetails mit dem folgenden Cmdlet abrufen.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### Aktualisieren der Konfiguration für öffentliches Azure-Peering

Sie können einen beliebigen Teil der Konfiguration mit dem folgenden Cmdlet aktualisieren.

	Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Die VLAN-ID der Verbindung wird im obigen Beispiel von 200 in 600 geändert.

### Löschen von öffentlichem Azure-Peering

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Cmdlet ausführen.

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Microsoft-Peering

Dieser Abschnitt enthält Anweisungen zum Erstellen, Abrufen, Aktualisieren und Löschen der Microsoft-Peeringkonfiguration für eine ExpressRoute-Verbindung.

### Erstellen des Microsoft-Peerings

1. **Importieren Sie das PowerShell-Modul für ExpressRoute.**
	
 	Sie müssen den neuesten PowerShell-Installer aus dem [PowerShell-Katalog](http://www.powershellgallery.com/) installieren und die Azure-Ressourcen-Manager-Module in die PowerShell-Sitzung importieren, um die ExpressRoute-Cmdlets verwenden zu können. Sie müssen PowerShell als Administrator ausführen.

	    Install-Module AzureRM

		Install-AzureRM

	Importieren Sie alle AzureRM.*-Module im bekannten semantischen Versionsbereich.

		Import-AzureRM

	Sie können auch einfach ein ausgewähltes Modul im bekannten semantischen Versionsbereich importieren.
		
		Import-Module AzureRM.Network 

	Melden Sie sich bei Ihrem Konto an.

		Login-AzureRmAccount

	Wählen Sie das Abonnement aus, mit dem die ExpressRoute-Verbindung erstellt werden soll.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **Erstellen einer ExpressRoute-Verbindung**
	
	Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie die Verbindung vom Konnektivitätsanbieter bereitstellen. **Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie anfordern, dass der Anbieter für Sie das private Azure-Peering aktiviert. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen.** Falls Ihr Konnektivitätsanbieter das Routing für Sie nicht verwaltet, müssen Sie nach dem Erstellen der Verbindung die unten angegebenen Anweisungen befolgen.

3. **Überprüfen Sie die ExpressRoute-Verbindung, um sicherzustellen, dass sie bereitgestellt wurde.**

	Sie müssen zuerst überprüfen, ob die ExpressRoute-Verbindung bereitgestellt (Provisioned) und aktiviert (Enabled) wurde. Betrachten Sie das folgende Beispiel.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	Die Antwort wird in etwa wie im folgendem Beispiel gezeigt lauten:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	
4. **Konfigurieren Sie das Microsoft-Peering für die Verbindung.**

	Stellen Sie vorab sicher, dass die folgenden Informationen vorliegen:

	- Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt.
	- Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt.
	- Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
	- AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden. Sie dürfen hierbei nur öffentliche AS-Nummern verwenden. Die AS-Nummer muss sich in Ihrem Besitz befinden.
	- Angekündigte Präfixe: Sie müssen eine Liste mit allen Präfixen bereitstellen, die Sie über die BGP-Sitzung ankündigen möchten. Nur öffentliche IP-Adresspräfixe werden akzeptiert. Sie können eine kommagetrennte Liste senden, wenn Sie planen, einen Satz mit Präfixen zu senden. Diese Präfixe müssen über eine RIR/IRR-Registrierung für Sie verfügen.
	- Kunden-ASN: Wenn Sie Präfixe ankündigen, die nicht für die Peering-AS-Nummer registriert sind, können Sie die AS-Nummer angeben, unter der sie registriert sind. **Dies ist optional**.
	- Routing-Registrierungsname: Sie können den RIR/IRR-Wert angeben, unter dem die AS-Nummer und die Präfixe registriert sind.
	- Ein MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden. **Dies ist optional**.
	
	Sie können das folgende Cmdlet ausführen, um das Microsoft-Peering für Ihre Verbindung zu konfigurieren.

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Abrufen von Details zum Microsoft-Peering

Sie können die Konfigurationsdetails mit dem folgenden Cmdlet abrufen.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt


### Aktualisieren der Konfiguration für das Microsoft-Peering

Sie können einen beliebigen Teil der Konfiguration mit dem folgenden Cmdlet aktualisieren.

		Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
		

### Löschen des Microsoft-Peerings

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Cmdlet ausführen.

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Nächste Schritte

-  [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md).
-  Weitere Informationen zu Workflows finden Sie unter [ExpressRoute-Workflows](expressroute-workflows.md).
-  Weitere Informationen zum Verbindungspeering finden Sie unter [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md).

<!---HONumber=Nov15_HO2-->