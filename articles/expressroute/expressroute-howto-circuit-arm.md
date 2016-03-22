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

```

**Schritt 7. Erstellen Sie die Routingkonfiguration.**

Eine Schritt-für-Schritt-Anleitung finden Sie unter [Routingkonfiguration für ExpressRoute-Verbindung (Erstellen und Ändern von Verbindungspeerings)](expressroute-howto-routing-arm.md).

**Schritt 8. Verknüpfen Sie ein virtuelles Netzwerk mit einer ExpressRoute-Verbindung.**

Verknüpfen Sie anschließend ein virtuelles Netzwerk mit Ihrer ExpressRoute-Verbindung. Sie können für die Arbeit mit dem Resource Manager-Bereitstellungsmodell [diese Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) verwenden. Wir arbeiten derzeit an Schritten, um diese Aufgabe mithilfe von PowerShell durchzuführen.

## Abrufen des Status einer ExpressRoute-Verbindung

Sie können diese Informationen jederzeit mithilfe des Cmdlets *Get-AzureRmExpressRouteCircuit* abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet.

```
Get-AzureRmExpressRouteCircuit
```

Die Antwort ähnelt dem folgenden Beispiel:

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
```

Sie können Informationen zu einer bestimmten ExpressRoute-Verbindung erhalten, indem Sie den Ressourcengruppennamen und den Verbindungsnamen als Parameter an den Aufruf übergeben:

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
```

Ausführliche Beschreibungen aller Parameter erhalten Sie, indem Sie Folgendes ausführen:

```
get-help get-azurededicatedcircuit -detailed
```

## Ändern einer ExpressRoute-Verbindung

Sie können bestimmte Eigenschaften einer ExpressRoute-Verbindung ändern, ohne die Konnektivität zu beeinträchtigen.

Sie können ohne Ausfallzeiten folgendermaßen vorgehen:

- Aktivieren oder deaktivieren Sie ein ExpressRoute Premium-Add-On für Ihre ExpressRoute-Verbindung.
- Erhöhen Sie die Bandbreite Ihrer ExpressRoute-Verbindung.

Weitere Informationen zu Beschränkungen und Grenzwerten finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md).

### Aktivieren des ExpressRoute Premium-Add-Ons

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Codeausschnitt aktivieren:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Für die Verbindung sind nun die Features des ExpressRoute Premium-Add-Ons aktiviert. Beachten Sie, dass Microsoft die Gebühren für das Premium-Add-On berechnet, sobald der Befehl erfolgreich ausgeführt wurde.

### Deaktivieren des ExpressRoute Premium-Add-Ons

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet deaktivieren:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Das Premium-Add-On ist jetzt für Ihre Verbindung deaktiviert.

Beachten Sie, dass dieser Vorgang fehlschlagen kann, wenn Sie Ressourcen verwenden, die die zulässige Menge für die Standardverbindung überschreiten.

- Bevor Sie ein Downgrade von Premium auf Standard durchführen, müssen Sie sicherstellen, dass weniger als zehn virtuelle Netzwerke mit der Verbindung verknüpft sind. Andernfalls verursacht Ihre Updateanforderung einen Fehler, und Microsoft berechnet Ihnen Premium-Gebühren.
- Sie müssen die Verknüpfung für alle virtuellen Netzwerke in anderen geopolitischen Regionen aufheben. Andernfalls verursacht Ihre Updateanforderung einen Fehler, und Microsoft berechnet Ihnen Premium-Gebühren.
- Ihre Routentabelle muss für das private Peering weniger als 4.000 Routen aufweisen. Wenn Ihre Routentabelle mehr als 4.000 Routen umfasst, wird die BGP-Sitzung verworfen. Eine erneute Aktivierung ist erst dann wieder möglich, wenn die Anzahl der angekündigten Präfixe unter 4.000 fällt.

### Aktualisieren der Bandbreite für die ExpressRoute-Verbindung

Informationen zu unterstützten Bandbreitenoptionen für Ihren Anbieter finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md). Sie können jede Größe auswählen, die die Größe der vorhandenen Verbindung übersteigt. Sobald Sie sich für die benötigte Größe entschieden haben, verwenden Sie den folgenden Befehl, um die Größe der Verbindung anzupassen:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Die Größe Ihrer Verbindung wird auf der Microsoft-Seite angepasst. Anschließend müssen Sie sich an Ihren Konnektivitätsanbieter wenden, um die entsprechende Aktualisierung der Konfigurationen auf Anbieterseite vornehmen zu lassen. Nachdem Sie diese Benachrichtigung gesendet haben, beginnt Microsoft, die Gebühren für die aktualisierte Bandbreitenoption abzurechnen.

**Wichtig**: Es ist nicht möglich, die Bandbreite einer ExpressRoute-Verbindung ohne Unterbrechung zu reduzieren. Ein Downgrade der Bandbreite erfordert, dass Sie die Bereitstellung der ExpressRoute-Verbindung aufheben und dann eine neue ExpressRoute-Verbindung bereitstellen.

## Löschen und Aufheben der Bereitstellung einer ExpressRoute-Verbindung

Sie können die ExpressRoute-Verbindung löschen, indem Sie den folgenden Befehl ausführen:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

Beachten Sie, dass Sie die Verknüpfung aller virtuellen Netzwerke für die ExpressRoute-Verbindung aufheben müssen, damit dieser Vorgang erfolgreich ist. Falls dieser Vorgang nicht erfolgreich ist, überprüfen Sie, ob noch virtuelle Netzwerke mit der Verbindung verknüpft sind.

Wenn der Bereitstellungszustand des Dienstanbieters für die ExpressRoute-Verbindung „enabled“ lautet, wechselt der Status vom aktivierten Zustand in *disabling*. Arbeiten Sie mit Ihrem Service Provider zusammen, um die Bereitstellung der Verbindung auf Anbieterseite aufzuheben. Microsoft reserviert weiterhin Ressourcen für Sie und stellt Ihnen dies in Rechnung, bis der Dienstanbieter die Aufhebung der Verbindungsbereitstellung abgeschlossen hat und uns benachrichtigt.

Wenn der Dienstanbieter die Bereitstellung der Verbindung aufgehoben hat (Bereitstellungszustand des Dienstanbieters lautet *not provisioned*), bevor Sie das obige Cmdlet ausführen, hebt Microsoft die Verbindungsbereitstellung auf und stellt Ihnen keine Gebühren mehr in Rechnung.

## Nächste Schritte

Führen Sie nach dem Erstellen Ihrer Verbindung folgende Vorgänge aus:

- [Erstellen und Ändern des Routings für Ihre ExpressRoute-Verbindung](expressroute-howto-routing-arm.md)
- [Verknüpfen Ihres virtuelles Netzwerks mit Ihrer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0309_2016-->