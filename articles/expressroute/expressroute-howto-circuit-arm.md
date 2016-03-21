<properties
   pageTitle="Erstellen und Ändern einer ExpressRoute-Verbindung mit Resource Manager und PowerShell | Microsoft Azure"
   description="Dieser Artikel beschreibt die Erstellung und Bereitstellung einer ExpressRoute-Verbindung. Außerdem wird veranschaulicht, wie Sie die Verbindung prüfen, aktualisieren, löschen oder deren Bereitstellung aufheben."
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
   ms.date="03/03/2016"
   ms.author="cherylmc"/>

# Erstellen und Ändern einer ExpressRoute-Verbindung mit Resource Manager und PowerShell

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

In diesem Artikel wird beschrieben, wie Sie eine Azure ExpressRoute-Verbindung mithilfe von Windows PowerShell-Cmdlets und des Azure Resource Manager-Bereitstellungsmodells erstellen. In den folgenden Schritten wird auch veranschaulicht, wie Sie den Status der Verbindung prüfen, aktualisieren, löschen oder deren Bereitstellung aufheben.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Konfigurationsvoraussetzungen

Für das Erstellen einer ExpressRoute-Verbindung gelten folgende Voraussetzungen:

- Sie benötigen die neueste Version der Azure PowerShell-Module (Version 1.0 oder höher). Um eine Schritt-für-Schritt-Anleitung zum Konfigurieren des Computers für die Verwendung der Azure PowerShell-Module zu erhalten, befolgen Sie die Anweisungen auf der Seite [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).
- Lesen Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).

## Erstellen und Bereitstellen einer ExpressRoute-Verbindung

**Schritt 1. Importieren Sie das PowerShell-Modul für ExpressRoute.**

Um die ExpressRoute-Cmdlets verwenden zu können, müssen Sie den neuesten PowerShell-Installer aus dem [PowerShell-Katalog](http://www.powershellgallery.com/) installieren und die Resource Manager-Module in die PowerShell-Sitzung importieren. Sie führen PowerShell als Administrator aus.

```
Install-Module AzureRM

Install-AzureRM
```

Importieren Sie alle AzureRM-Module im bekannten semantischen Versionsbereich:

```
Import-AzureRM
```

Sie können auch ein bestimmtes Modul im bekannten semantischen Versionsbereich importieren:

```
Import-Module AzureRM.Network
```

Melden Sie sich bei Ihrem Konto an:

```
Login-AzureRmAccount
```

Wählen Sie das Abonnement aus, mit dem eine ExpressRoute-Verbindung erstellt werden soll:

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**Schritt 2. Rufen Sie die Liste der unterstützten Anbieter, Standorte und Bandbreiten ab.**

Vor dem Erstellen einer ExpressRoute-Verbindung benötigen Sie die Liste der Konnektivitätsanbieter, unterstützten Standorte und Bandbreitenoptionen. Das PowerShell-Cmdlet *Get-AzureRmExpressRouteServiceProvider* gibt diese Informationen zurück, die Sie in späteren Schritten noch verwenden werden.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

Überprüfen Sie, ob Ihr Konnektivitätsanbieter hier aufgeführt ist. Notieren Sie sich die folgenden Informationen, da Sie diese später beim Erstellen der Verbindung benötigen:

- Name
- PeeringLocations
- BandwidthsOffered

Sie können nun eine ExpressRoute-Verbindung erstellen.

**Schritt 3. Erstellen Sie eine ExpressRoute-Verbindung.**

Wenn Sie noch keine Ressourcengruppe besitzen, müssen Sie zuerst eine erstellen, bevor Sie Ihre ExpressRoute-Verbindung erstellen. Dazu können Sie den folgenden Befehl ausführen:

```
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Das folgende Beispiel zeigt, wie Sie eine ExpressRoute-Verbindung mit 200 MBit/s über Equinix im Silicon Valley herstellen können. Wenn Sie einen anderen Anbieter und andere Einstellungen verwenden, ersetzen Sie bei Ihrer Anforderung die entsprechenden Informationen. Nachfolgend sehen Sie eine Beispielanforderung für einen neuen Dienstschlüssel:

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Stellen Sie sicher, dass Sie die richtige SKU-Ebene und die richtige SKU-Familie angeben.

- Die SKU-Ebene bestimmt, ob ein ExpressRoute Standard- oder ein ExpressRoute Premium-Add-On aktiviert wird. Sie können *standard* für das Standard-SKU bzw. *premium* für das Premium-Add-On angeben.
- Die SKU-Familie bestimmt den Abrechnungstyp. Sie können *MeteredData* für einen Volumentarif und *UnlimitedData* für einen Plan mit Datenflatrate auswählen. **Hinweis:** Sie können den Abrechnungstyp nicht mehr ändern, nachdem eine Verbindung erstellt wurde.

Die Antwort enthält den Dienstschlüssel. Ausführliche Beschreibungen aller Parameter erhalten Sie, indem Sie Folgendes ausführen:

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**Schritt 4. Listen Sie alle ExpressRoute-Verbindungen auf.**

Um eine Liste mit allen von Ihnen erstellten ExpressRoute-Verbindungen abzurufen, können Sie den Befehl *Get-AzureRmExpressRouteCircuit* ausführen.

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Die Antwort entspricht etwa dem folgenden Beispiel:

```
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
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                      "ServiceProviderName": "Equinix",
                                      "PeeringLocation": "Silicon Valley",
                                      "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Sie können diese Informationen jederzeit mithilfe des Cmdlets *Get-AzureRmExpressRouteCircuit* abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet. Der Dienstschlüssel wird im Feld *ServiceKey* aufgeführt.

```
Get-AzureRmExpressRouteCircuit
```

Die Antwort entspricht etwa dem folgenden Beispiel:

```
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
```

Ausführliche Beschreibungen aller Parameter erhalten Sie, indem Sie Folgendes ausführen:

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**Schritt 5. Senden Sie den Dienstschlüssel zur Bereitstellung an Ihren Konnektivitätsanbieter.**

Wenn Sie eine neue ExpressRoute-Verbindung erstellen, weist die Verbindung folgenden Zustand auf:

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

*ServiceProviderProvisioningState* enthält Informationen zum aktuellen Zustand der Bereitstellung auf Dienstanbieterseite, und mit „Status“ wird der Zustand auf Microsoft-Seite angegeben. Damit Sie eine ExpressRoute-Verbindung verwenden können, muss sie sich im folgenden Zustand befinden:

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

Die Verbindung wechselt in den folgenden Zustand, wenn sie vom Konnektivitätsanbieter für Sie aktiviert wird:

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**Schritt 6. Überprüfen Sie regelmäßig den Status und Zustand des Verbindungsschlüssels.**

Durch das Überprüfen des Status und des Zustands des Verbindungsschlüssels werden Sie informiert, wenn Ihr Anbieter Ihre Verbindung aktiviert hat. Sobald die Verbindung konfiguriert wurde, wird *ServiceProviderProvisioningState* wie im folgenden Beispiel als *Provisioned* angezeigt:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Die Antwort entspricht etwa dem folgenden Beispiel:

```
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

Beachten Sie, dass Sie die Verknüpfung aller virtuellen Netzwerke für die ExpressRoute-Verbindung aufheben müssen, damit dieser Vorgang erfolgreich ist. Falls dieser Vorgang nicht erfolgreich ist, überprüfen Sie, ob noch virtuelle Netzwerke mit der Verbindung verknüpft sind.

Wenn der Bereitstellungszustand des Dienstanbieters für die ExpressRoute-Verbindung „enabled“ lautet, wechselt der Status vom aktivierten Zustand in *disabling*. Arbeiten Sie mit Ihrem Service Provider zusammen, um die Bereitstellung der Verbindung auf Anbieterseite aufzuheben. Microsoft reserviert weiterhin Ressourcen für Sie und stellt Ihnen dies in Rechnung, bis der Dienstanbieter die Aufhebung der Verbindungsbereitstellung abgeschlossen hat und uns benachrichtigt.

Wenn der Dienstanbieter die Bereitstellung der Verbindung aufgehoben hat (Bereitstellungszustand des Dienstanbieters lautet *not provisioned*), bevor Sie das obige Cmdlet ausführen, hebt Microsoft die Verbindungsbereitstellung auf und stellt Ihnen keine Gebühren mehr in Rechnung.

## Nächste Schritte

Führen Sie nach dem Erstellen Ihrer Verbindung folgende Vorgänge aus:

- [Erstellen und Ändern des Routings für Ihre ExpressRoute-Verbindung](expressroute-howto-routing-arm.md)
- [Verknüpfen Ihres virtuelles Netzwerks mit Ihrer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0309_2016-->