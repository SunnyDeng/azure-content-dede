<properties
   pageTitle="Erstellen und Ändern einer ExpressRoute-Verbindung mit dem Azure-Ressourcen-Manager und mit PowerShell | Microsoft Azure"
   description="In diesem Artikel werden Sie durch die Schritte zum Erstellen und Bereitstellen einer ExpressRoute-Verbindung geführt. Außerdem wird veranschaulicht, wie Sie Status überprüfen, Updates durchführen oder die Verbindung löschen oder deren Bereitstellung aufheben."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/12/2016"
   ms.author="cherylmc"/>

# Erstellen und Ändern einer ExpressRoute-Verbindung mit dem Azure-Ressourcen-Manager und mit PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

In diesem Artikel werden Sie durch die Schritte zum Erstellen einer ExpressRoute-Verbindung mithilfe von PowerShell-Cmdlets und des Azure-Ressourcen-Manager-Bereitstellungsmodells geführt. In den Schritten unten wird auch veranschaulicht, wie Sie den Status prüfen, ein Update durchführen oder eine ExpressRoute-Verbindung löschen oder deren Bereitstellung aufheben.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Konfigurationsvoraussetzungen

- Sie benötigen die neueste Version der Azure PowerShell-Module (also mindestens Version 1.0). Befolgen Sie die Anleitung auf der Seite [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Sie erhalten dort eine Schritt-für-Schritt-Anleitung zum Konfigurieren des Computers für die Verwendung der Azure PowerShell-Module. 
- Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md) gelesen haben.

## So können Sie eine ExpressRoute-Verbindung erstellen und bereitstellen

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
			


2. **Rufen Sie die Liste der unterstützten Anbieter, Standorte und Bandbreiten ab.**

	Vor dem Erstellen einer ExpressRoute-Verbindung benötigen Sie die Liste der Konnektivitätsanbieter, unterstützten Standorte und Bandbreitenoptionen. Das PowerShell-Cmdlet *Get-AzureRmExpressRouteServiceProvider* gibt diese Informationen zurück, die Sie in späteren Schritten noch verwenden werden.

		Get-AzureRmExpressRouteServiceProvider

	Überprüfen Sie, ob Ihr Konnektivitätsanbieter hier aufgeführt ist. Notieren Sie die folgenden Angaben, da Sie sie zum Erstellen der Verbindungen benötigen.
	
	- Name
	- PeeringLocations
	- BandwidthsOffered

	Sie können nun eine ExpressRoute-Verbindung erstellen.

		
3. **Erstellen Sie eine ExpressRoute-Verbindung.**

	Sie müssen zuerst eine Ressourcengruppe erstellen, sofern noch keine vorhanden ist, bevor Sie Ihre ExpressRoute-Verbindung erstellen. Dazu können Sie den folgenden Befehl ausführen.

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	Das folgende Beispiel zeigt, wie Sie eine ExpressRoute-Verbindung mit 200 MBit/s über Equinix im Silicon Valley herstellen können. Wenn Sie einen anderen Anbieter und andere Einstellungen verwenden, ersetzen Sie bei Ihrer Anforderung die entsprechenden Informationen.

	Nachfolgend sehen Sie eine Beispielanforderung für einen neuen Dienstschlüssel:

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	Stellen Sie sicher, dass Sie die richtige SKU-Ebene und die richtige SKU-Familie angeben.
 
	 - Die SKU-Ebene bestimmt, ob ExpressRoute Standard oder das ExpressRoute Premium-Add-On aktiviert wird. Sie können *Standard* für das Standard-SKU oder *Premium* für das Premium-Add-On angeben.
	 - Die SKU-Familie bestimmt den Abrechnungstyp. Sie können *MeteredData* für einen Volumentarif und „UnlimitedData“ für einen Plan mit Datenflatrate auswählen. **Hinweis:** Sie können den Abrechnungstyp nicht mehr ändern, nachdem eine Verbindung erstellt wurde.

	Die Antwort enthält den Dienstschlüssel. Ausführliche Beschreibungen aller Parameter erhalten Sie, wenn Sie Folgendes ausführen:

		Get-Help New-AzureRmExpressRouteCircuit -detailed 

4. **Listen Sie alle ExpressRoute-Verbindungen auf.**

	Sie können den Befehl *Get-AzureRmExpressRouteCircuit* ausführen, um eine Liste mit allen von Ihnen erstellten ExpressRoute-Verbindungen abzurufen.

		
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
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


	Sie können diese Informationen jederzeit mithilfe des Cmdlets *Get-AzureRmExpressRouteCircuit* abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet. Der Dienstschlüssel wird im Feld *ServiceKey* aufgeführt.

		Get-AzureRmExpressRouteCircuit

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
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []



	Ausführliche Beschreibungen aller Parameter erhalten Sie, wenn Sie Folgendes ausführen:

		Get-Help Get-AzureRmExpressRouteCircuit -detailed 

5. **Senden Sie den Dienstschlüssel zur Bereitstellung an Ihren Konnektivitätsanbieter.**

	Wenn Sie eine neue ExpressRoute-Verbindung erstellen, hat die Verbindung folgenden Zustand:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	*ServiceProviderProvisioningState* enthält Informationen zum aktuellen Status der Bereitstellung auf Service Provider-Seite, und mit "Status" wird der Status auf Microsoft-Seite angegeben. Eine ExpressRoute-Verbindung muss sich im folgenden Zustand befinden, damit Sie sie verwenden können.

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	Die Verbindung wird in den folgenden Zustand versetzt, wenn sie vom Konnektivitätsanbieter für Sie aktiviert wird.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Überprüfen Sie regelmäßig den Status und Zustand des Verbindungsschlüssels.**

	So sind Sie informiert, wenn Ihr Anbieter die Verbindung aktiviert hat. Sobald die Verbindung konfiguriert wurde, zeigt *ServiceProviderProvisioningState* wie im folgenden Beispiel den Status *Provisioned* an.

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

6. **Erstellen Sie die Routingkonfiguration.**
	
	Lesen Sie die Schritt-für-Schritt-Anweisungen unter [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung](expressroute-howto-routing-arm.md).

>[AZURE.IMPORTANT]Diese Anweisungen gelten nur für Verbindungen, die über Service Provider erstellt wurden, von denen Layer 2-Konnektivitätsdienste angeboten werden. Wenn Sie einen Service Provider nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IPVPN, z. B. MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie. In diesem Fall können Sie keine Peerings erstellen oder verwalten.


7. **Verknüpfen Sie ein VNet mit einer ExpressRoute-Verbindung.** 

	Verknüpfen Sie als Nächstes ein VNet mit der ExpressRoute-Verbindung. Lesen Sie die Schritt-für-Schritt-Anweisungen unter [Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen](expressroute-howto-linkvnet-arm.md).

##  So rufen Sie den Status einer ExpressRoute-Verbindung ab

Sie können diese Informationen jederzeit mithilfe des Cmdlets *Get-AzureRmExpressRouteCircuit* abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet.

		Get-AzureRmExpressRouteCircuit

Die Antwort lautet etwa wie im folgendem Beispiel:

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

Sie können Informationen zu einer bestimmten ExpressRoute-Verbindung erhalten, indem Sie den Ressourcengruppennamen und den Verbindungsnamen als Parameter an den Aufruf übergeben.

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

Ausführliche Beschreibungen aller Parameter erhalten Sie, wenn Sie Folgendes ausführen:

		Get-Help Get-azurededicatedcircuit -detailed 

## So ändern Sie eine ExpressRoute-Verbindung

Sie können bestimmte Eigenschaften einer ExpressRoute-Verbindung ändern, ohne die Konnektivität zu beeinträchtigen.

Sie können Folgendes durchführen:

- Aktivieren/Deaktivieren eines ExpressRoute Premium-Add-Ons für Ihre ExpressRoute-Verbindung ohne Ausfallzeit
- Erhöhen der Bandbreite Ihrer ExpressRoute-Verbindung ohne Ausfallzeit

Weitere Informationen zu Beschränkungen und Grenzwerten finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md).

### Aktivieren des ExpressRoute Premium-Add-Ons

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Codeausschnitt aktivieren:

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Name = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
Für Ihre Verbindung sind nun die Features des ExpressRoute Premium-Add-Ons aktiviert. Beachten Sie, dass die Gebühren für das Premium-Add-On ab der erfolgreichen Ausführung des Befehls erhoben werden.

### Deaktivieren des ExpressRoute Premium-Add-Ons

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet deaktivieren:
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Das Premium-Add-On ist jetzt für Ihre Verbindung deaktiviert.

Beachten Sie, dass dieser Vorgang fehlschlagen kann, wenn Sie Ressourcen verwenden, die die zulässige Menge für die Standardverbindung überschreiten.

- Sie müssen sicherstellen, dass die Anzahl von virtuellen Netzwerken, die mit der Verbindung verknüpft sind, kleiner als zehn ist, bevor Sie ein Downgrade von Premium auf Standard durchführen. Wenn Sie dies nicht beachten, tritt für die Updateanforderung ein Fehler auf, und Ihnen werden die Premium-Gebühren berechnet.
- Sie müssen die Verknüpfung für alle virtuellen Netzwerke in anderen geopolitischen Regionen aufheben. Wenn Sie dies nicht beachten, tritt für die Updateanforderung ein Fehler auf, und Ihnen werden die Premium-Gebühren berechnet.
- Ihre Routentabelle muss für das private Peering weniger als 4.000 Routen aufweisen. Wenn Ihre Routentabelle mehr als 4.000 Routen umfasst, wird die BGP-Sitzung verworfen. Eine erneute Aktivierung ist dann erst wieder möglich, wenn die Anzahl der angekündigten Präfixe unter 4.000 fällt.


### Aktualisieren der Bandbreite für die ExpressRoute-Verbindung

Informationen zu unterstützten Bandbreitenoptionen für Ihren Anbieter finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md). Sie können jede Größe auswählen, die die Größe der vorhandenen Verbindung übersteigt. Sobald Sie sich für die benötigte Größe entschieden haben, können Sie den folgenden Befehl verwenden, um die Größe der Verbindung anzupassen.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Die Größe Ihrer Verbindung wurde bereits auf der Microsoft-Seite angepasst. Sie müssen sich an Ihren Konnektivitätsanbieter wenden, um die entsprechende Aktualisierung der Konfigurationen auf Anbieterseite vornehmen zu lassen. Beachten Sie, dass wir Ihnen ab diesem Zeitpunkt die aktualisierte Bandbreitenoption in Rechnung stellen.

>[AZURE.IMPORTANT]Es ist nicht möglich, die Bandbreite einer ExpressRoute-Verbindung ohne Störungen zu reduzieren. Ein Downgrade der Bandbreite erfordert, dass Sie die Bereitstellung der ExpressRoute-Verbindung aufheben und dann eine neue ExpressRoute-Verbindung bereitstellen.

## So löschen Sie eine ExpressRoute-Verbindung und heben die Bereitstellung auf

Sie können die ExpressRoute-Verbindung löschen, indem Sie den folgenden Befehl ausführen:

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

Beachten Sie, dass Sie die Verknüpfung aller virtuellen Netzwerke für die ExpressRoute aufheben müssen, damit dieser Vorgang erfolgreich ist. Überprüfen Sie, ob noch virtuelle Netzwerke mit der Verbindung verknüpft sind, falls dieser Vorgang nicht erfolgreich ist.

Wenn der Service Provider-Bereitstellungsstatus für die ExpressRoute-Verbindung aktiviert ist, wechselt der Status von "enabled" in *disabling*. Arbeiten Sie mit Ihrem Service Provider zusammen, um die Bereitstellung der Verbindung auf Anbieterseite aufzuheben. Wir reservieren weiterhin Ressourcen für Sie und stellen Ihnen dies in Rechnung, bis der Service Provider die Aufhebung der Verbindungsbereitstellung abgeschlossen hat und uns eine Benachrichtigung sendet.

Wenn der Service Provider die Bereitstellung der Verbindung aufgehoben hat (Bereitstellungsstatus des Service Providers lautet *not provisioned*), bevor Sie das obige Cmdlet ausführen, führen wir die Aufhebung der Verbindungsbereitstellung durch und stellen Ihnen keine Gebühren mehr in Rechnung.

## Nächste Schritte

- [Konfigurieren des Routings](expressroute-howto-routing-arm.md)

<!---HONumber=AcomDC_0114_2016-->