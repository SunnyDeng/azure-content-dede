<properties
   pageTitle="Azure-Ressourcen-Manager-Unterstützung für Traffic Manager – Vorschau | Microsoft Azure"
   description="Verwenden von PowerShell für Traffic Manager mit Azure-Ressourcen-Manager (ARM) in der Vorschau"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="joaoma" />

# Azure-Ressourcen-Manager-Unterstützung für Azure Traffic Manager – Vorschau
Der Azure-Ressourcen-Manager (ARM) ist das neue Verwaltungsframework für Dienste in Azure. Azure Traffic Manager kann jetzt mithilfe von APIs und Tools auf Basis von Azure-Ressourcen-Manager verwaltet werden. Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-preview-portal-using-resource-groups.md).

## Ressourcenmodell

Azure Traffic Manager wird mithilfe einer Reihe von Einstellungen, dem sogenannten Traffic Manager-Profil, konfiguriert. Dieses Profil enthält DNS-Einstellungen, Einstellungen für das Routing von Datenverkehr, Endpunktüberwachungseinstellungen und die Liste der Dienstendpunkte, an die der Datenverkehr weitergeleitet wird.

In ARM wird jedes Traffic Manager-Profil durch eine ARM-Ressource vom Typ „TrafficManagerProfiles“ dargestellt und vom Ressourcenanbieter „Microsoft.Network“ verwaltet. Auf der REST-API-Ebene lautet der URI für jedes Profil wie folgt:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Vergleich mit Azure Traffic Manager-Dienstverwaltungs-API

Durch die Verwendung von ARM zum Konfigurieren von Traffic Manager-Profilen erhalten Sie Zugriff auf die gleichen Traffic Manager-Features wie mit der Azure-Service-Verwaltungs-API. Eine Ausnahme stellen die unten aufgeführten Einschränkungen der Vorschau dar.

Die Features sind zwar die gleichen, die Terminologie hat sich jedoch geändert:

- Die Lastenausgleichsmethode (beeinflusst, wie Traffic Manager entscheidet, an welchen Endpunkt Datenverkehr bei DNS-Anforderungen weitergeleitet wird), wurde in „Datenverkehr-Routingmethode“ umbenannt.

- Die Datenverkehr-Routingmethode „Roundrobin“ heißt nun „Gewichtung“.

- Die Datenverkehr-Routingmethode „Failover“ wurde in „Priorität“ umbenannt.

## Einschränkungen
Es gibt zurzeit einige wenige Einschränkungen in der ARM-Unterstützung für den Azure Traffic Manager.

- Traffic Manager-Profile, die mit der vorhandenen Nicht-ARM-Azure-Service-Verwaltungs-API, den vorhandenen Tools und dem klassischen Portal erstellt wurden, sind nicht über ARM verfügbar. Dies gilt auch im umgekehrten Fall. Die Migration von Profilen von der Azure-Service-Verwaltung zu ARM-APIs wird derzeit nicht unterstützt. Die einzige Möglichkeit ist das Löschen und Neuerstellen des Profils.


- „Geschachtelte“ Traffic Manager Endpunkte werde durch den ARM-API, ARM PowerShell und ARM-Modus Azure CLI unterstützt. Sie werden gegenwärtig im Azure-Portal (das ebenfalls ARM API verwendet) nicht unterstützt.


## Einrichten von Azure PowerShell

Diese Anweisungen verwenden Microsoft Azure PowerShell, die mithilfe der folgenden Schritte konfiguriert werden muss.

Bei Benutzern, die nicht PowerShell oder Windows benutzen, können analoge Vorgänge über die Azure-Befehlszeilenschnittstelle ausgeführt werden. Alle Operationen, mit Ausnahme der Verwaltung von „geschachtelten“ Traffic Manager-Profilen, sind auch über das Azure-Portal verfügbar.

### Schritt 1
Installieren Sie die neueste Azure PowerShell, die auf der Azure-Downloadseite zur Verfügung steht.

### Schritt 2
Melden Sie sich beim Azure-Konto an.

	PS C:\> Login-AzureRmAccount

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.

### Schritt 3
Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

	PS C:\> Set-AzureRmContext -SubscriptionName "MySubscription"

Um eine Liste der verfügbaren Abonnements anzuzeigen, verwenden Sie das Cmdlet „Get-AzureRmSubscription“.

### Schritt 4

Der Traffic Manager-Dienst wird vom Ressourcenanbieter „Microsoft.Network“ verwaltet. Ihr Azure-Abonnement muss für die Verwendung dieses Ressourcenanbieters registriert werden, bevor Sie Traffic Manager über ARM verwenden können. Dieser Schritt muss für jedes Abonnement einmal ausgeführt werden.

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### Schritt 5
Erstellen Sie eine Ressourcengruppe (überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden).

	PS C:\> New-AzureRmResourceGroup -Name MyRG -Location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Da alle Traffic Manager-Profilressourcen global und nicht regional sind, hat die Auswahl des Speicherorts für die Ressourcengruppe jedoch keine Auswirkungen auf Azure Traffic Manager.

## Erstellen eines Traffic Manager-Profils

Verwenden Sie zum Erstellen eines Traffic Manager-Profils das Cmdlet „New-AzureRmTrafficManagerProfile“:

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Die Parameter lauten wie folgt:

- Name: Der ARM-Ressourcenname für die Traffic Manager-Profilressource. Profile in der gleichen Ressourcengruppe müssen eindeutige Namen aufweisen. Dieser Name unterscheidet sich von den DNS-Namen, die für DNS-Abfragen verwendet werden.

- ResourceGroupName: Der Name der ARM-Ressourcengruppe mit der Profilressource

- TrafficRoutingMethod: Gibt die Routingmethode an. Hiermit wird ermittelt, welcher Endpunkt als Reaktion auf eingehende DNS-Abfragen zurückgegeben wird. Mögliche Werte sind „Leistung“, „Gewichtung“ und „Priorität“.

- RelativeDnsName: Gibt den relativen DNS-Namen an, der von diesem Traffic Manager-Profil bereitgestellt wird. Dieser Wert wird mit dem DNS-Domänennamen kombiniert, der von Azure Traffic Manager zum Erstellen des vollqualifizierten Domänennamens (FQDN) des Profils verwendet wird. Beispielsweise ergibt der Wert „contoso“ ein Traffic Manager-Profil mit dem vollqualifizierten Namen „contoso.trafficmanager.net“.

- TTL: Gibt die DNS-Gültigkeitsdauer (Time-To-Live, TTL) in Sekunden an. Dadurch werden die lokale DNS-Auflösung und DNS-Clients informiert, wie lange die von diesem Traffic Manager-Profil bereitgestellten DNS-Antworten zwischengespeichert werden sollen.

- MonitorProtocol: Gibt das Protokoll an, mit dem die Endpunktintegrität überwacht werden soll. Mögliche Werte sind „HTTP“ und „HTTPS“.

- MonitorPort: Gibt den TCP-Port an, mit dem die Endpunktintegrität überwacht wird.

- MonitorPath: Gibt den Pfad relativ zum Endpunktdomänennamen an, mit dem die Endpunktintegrität getestet wird.

Das Cmdlet erstellt ein Traffic Manager-Profil in Azure Traffic Manager und gibt ein entsprechendes Profilobjekt zurück. Das Profil enthält zu diesem Zeitpunkt keine Endpunkte. Ausführliche Informationen zum Hinzufügen von Endpunkten zu einem Traffic Manager-Profil finden Sie unter [Hinzufügen von Traffic Manager-Endpunkten](#adding-traffic-manager-endpoints).

## Abrufen eines Traffic Manager-Profils

Rufen Sie ein vorhandenes Traffic Manager-Profilobjekt mit dem Cmdlet „Get-AzureRmTrafficManagerProfle“ ab:

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG

Dieses Cmdlet gibt ein Traffic Manager-Profilobjekt zurück.

## Aktualisieren eines Traffic Manager-Profils [](#update-traffic-manager-profile)

Traffic Manager-Profile werden mithilfe von 3 Schritten geändert. Mit diesen Schritten können Sie beispielsweise Endpunkte hinzufügen oder löschen oder Profileinstellungen ändern:

1.	Rufen Sie das Profil mithilfe von „Get-AzureRmTrafficManagerProfile“ ab (oder verwenden Sie das von „New-AzureRmTrafficManagerProfile“ zurückgegebene Profil).

2.	Ändern Sie das Profil, indem Sie Endpunkte hinzufügen, Endpunkte entfernen oder die Endpunktparameter oder Profilparameter ändern. Bei diesen Änderungen handelt es sich um Offlinevorgänge – nur das lokale Objekt, das das Profil darstellt, wird geändert.

3.	Übernehmen Sie Ihre Änderungen mithilfe des Cmdlets „Set-AzureRmTrafficManagerProfile“. Dadurch wird das vorhandene Profil in Azure Traffic Manager durch das bereitgestellte Profil ersetzt.

Alle Profileigenschaften können geändert werden. Ausnahme: Das RelativeDnsName-Element des Profils kann nicht geändert werden, nachdem das Profil erstellt wurde (wenn Sie diesen Wert ändern möchten, müssen Sie das Profil löschen und erneut erstellen).

So ändern Sie beispielsweise die Profilgültigkeitsdauer:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## Hinzufügen von Traffic Manager-Endpunkten
Es gibt drei Arten von Traffic Manager-Endpunkten: 1. Azure-Endpunkte: Diese Endpunkte stellen in Azure gehostete Dienste dar. 2. Externe Endpunkte: Diese Endpunkte stellen außerhalb von Azure gehostete Dienste dar. 3. Geschachtelte Endpunkte: Diese Endpunkte werden verwendet, um geschachtelte Hierarchien von Traffic Manager-Profilen zu erstellen und dadurch erweiterte Konfigurationen für Datenverkehrrouting für komplexere Anwendungen zu ermöglichen. Sie werden über die ARM-API noch nicht unterstützt.

In allen drei Fällen können Endpunkte auf zwei Arten hinzugefügt werden: 1. Mithilfe von drei Schritten, die dem unter [Aktualisieren eines Traffic Manager-Profils](#update-traffic-manager-profile) beschriebenen Vorgang ähneln: Abrufen des Profilobjekts mithilfe von „Get-AzureRmTrafficManagerProfile“, Aktualisieren im Offlinemodus zum Hinzufügen eines Endpunkts mithilfe von „Add-AzureRmTrafficManagerEndpointConfig“ und Hochladen von Änderungen in Azure Traffic Manager mithilfe von „Set-AzureRmTrafficManagerProfile“. Der Vorteil dieser Methode besteht darin, dass mehrere Änderungen am Endpunkt mit nur einer Aktualisierung vorgenommen werden können. 2. Mithilfe des Cmdlets „New-AzureRmTrafficManagerEndpoint“. Mit diesem Cmdlet wird einem vorhandenen Traffic Manager-Profil in einem Schritt ein Endpunkt hinzugefügt.

### Hinzufügen von Azure-Endpunkten

Azure-Endpunkte verweisen auf andere in Azure gehostete Dienste. Derzeit werden 3 Arten von Azure-Endpunkten unterstützt: 1. Azure-Web-Apps 2. Klassische Clouddienste (die entweder einen PaaS-Dienst oder virtuelle IaaS-Computer enthalten können) 3. ARM Microsoft.Network/publicIpAddress-Ressourcen (die an einen Load Balancer oder an die Netzwerkkarte eines virtuellen Computers angefügt werden können). Beachten Sie, dass der publicIpAddress-Ressource ein DNS-Name zugewiesen werden muss, damit sie in Traffic Manager verwendet werden kann.

In jedem Fall gilt: - Der Dienst wird mithilfe des Parameters „targetResourceId“ von „Add-AzureRmTrafficManagerEndpointConfig“ oder „New-AzureRmTrafficManagerEndpoint“ angegeben. - Die Parameter „Target“ und „EndpointLocation“ müssen nicht angegeben werden. Sie werden von dem oben angegebenen TargetResourceId-Element impliziert. - Die Angabe von „Weight“ ist optional. „Weights“ wird nur verwendet, wenn das Profil für die Verwendung der Datenverkehr-Routingmethode „Weighted“ konfiguriert ist, andernfalls wird der Parameter ignoriert. Wird der Parameter angegeben, muss er im Bereich 1 bis 1.000 liegen. Der Standardwert lautet „1“. - Die Angabe der Priorität ist optional. Prioritäten werden nur verwendet, wenn das Profil für die Verwendung der Datenverkehr-Routingmethode „Priority“ konfiguriert ist, andernfalls wird der Parameter ignoriert. Gültige Werte liegen zwischen 1 und 1.000 (niedrigere Werte stehen für eine höhere Priorität). Wenn Sie für einen Endpunkt Prioritäten angeben, müssen Sie für alle Endpunkte Prioritäten angeben. Wenn keine Prioritäten angegeben werden, werden Standardwerte beginnend mit 1, 2, 3 usw. in der Reihenfolge angewendet, in der Endpunkte bereitgestellt werden.

#### Beispiel 1: Hinzufügen von Web-App-Endpunkten mithilfe von „Add-AzureRmTrafficManagerEndpointConfig“
In diesem Beispiel erstellen wir ein neues Traffic Manager-Profil und fügen zwei Web-App-Endpunkte mithilfe des Cmdlets „Add-AzureRmTrafficManagerEndpointConfig“ hinzu. Anschließend übergeben wir das aktualisierte Profil mithilfe von „Set-AzureRmTrafficManagerProfile“ an Azure Traffic Manager.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Beispiel 2: Hinzufügen eines klassischen Clouddienst-Endpunkts mithilfe von „New-AzureRmTrafficManagerEndpoint“
In diesem Beispiel wird einem Traffic Manager-Profil ein „klassischer“ Clouddienst-Endpunkt hinzugefügt. Beachten Sie, dass wir in diesem Fall das Profil mithilfe des Profilnamens und des Ressourcengruppennamens angegeben haben, anstatt ein Profilobjekt zu übergeben (beide Ansätze werden unterstützt).

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### Beispiel 3: Hinzufügen eines publicIpAddress-Endpunkts mithilfe von „New-AzureRmTrafficManagerEndpoint“
In diesem Beispiel wird eine ARM-Ressource für eine öffentliche IP-Adresse zum Traffic Manager-Profil hinzugefügt. Für die öffentliche IP-Adresse muss ein DNS-Name konfiguriert sein. Sie kann an die Netzwerkkarte eines virtuellen Computers oder an einen Load Balancer gebunden sein.

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### Hinzufügen externer Endpunkte
Traffic Manager verwendet externe Endpunkte zum Weiterleiten von Datenverkehr an außerhalb von Azure gehostete Dienste. Wie bei Azure-Endpunkten können externe Endpunkte entweder mithilfe von „Add-AzureRmTrafficManagerEndpointConfig“ gefolgt von „Set-AzureRmTrafficManagerProfile“ oder mithilfe von „New-AzureRMTrafficManagerEndpoint“ hinzugefügt werden.

Beim Angeben externer Endpunkte: - Der Domänenname des Endpunkts muss mithilfe des Parameters „Target“ angegeben werden. - „EndpointLocation“ ist erforderlich, wenn die Datenverkehr-Routingmethode „Performance“ verwendet wird. Andernfalls ist der Parameter optional. Der Wert muss ein [gültiger Azure-Regionsname](https://azure.microsoft.com/regions/) sein. - Die Parameter „Weight“ und „Priority“ sind wie bei Azure-Endpunkten optional.

#### Beispiel 1: Hinzufügen externer Endpunkte mithilfe von „Add-AzureRmTrafficManagerEndpointConfig“ und „Set-AzureRmTrafficManagerProfile“
In diesem Beispiel erstellen wir ein neues Traffic Manager-Profil, fügen zwei externe Endpunkte hinzu und übernehmen die Änderungen.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Beispiel 2: Hinzufügen externer Endpunkte mithilfe von „New-AzureRmTrafficManagerEndpoint“
In diesem Beispiel fügen wir einem vorhandenen Profil, das durch den Profil- und den Ressourcengruppennamen angegeben wird, einen externen Endpunkt hinzu.

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

### „Geschachtelte“ Endpunkte hinzufügen

Der Traffic Manager erlaubt Ihnen, ein Traffic Manager-Profil (hier als „untergeordnetes“ Profil bezeichnet) als einen Endpunkt innerhalb eines anderen Traffic Manager-Profils (hier als „übergeordnetes“ Profil bezeichnet) zu erstellen.

Geschachtelte Traffic Manager erlauben Ihnen flexiblere und stärkere Datenverkehrs-Routing und Failoverschemata zu erstellen und die Bedürfnisse größerer, komplexerer Bereitstellungen zu unterstützen. [Dieser Blogeintrag](https://azure.microsoft.com/blog/new-azure-traffic-manager-nested-profiles/) enthält mehrere Beispiele.

Geschachtelte Endpunkte sind beim übergeordneten Profil mittels eines speziellen Endpunkttyps „NestedEndpoints“ konfiguriert. Beim Angeben geschachtelter Endpunkte: - Der Endpunkt (z. B. das untergeordnete Profil) muss mithilfe des Parameters „targetResourceId“ angegeben werden. -Die „EndpointLocation“ ist erforderlich, wenn die Datenverkehr-Routingmethode „Performance“ verwendet wird. Andernfalls ist der Parameter optional. Der Wert muss ein [gültiger Azure-Regionsname](http://azure.microsoft.com/regions/) sein. - Die Parameter „Weight“ und „Priority“ sind wie bei Azure-Endpunkten optional. Der Parameter „MinChildEndpoints“ ist optional. Der Standardwert ist „1“. Falls die Anzahl der verfügbaren Endpunkte im untergeordneten Profil unter diesen Schwellenwert fällt, betrachtet das übergeordnete Profil das untergeordnete als „heruntergestuft“ und leitet den Verkehr auf die anderen Endpunkte des übergeordneten Profils um.


#### Beispiel 1: Hinzufügen geschachtelter Endpunkte mithilfe von „Add-AzureRmTrafficManagerEndpointConfig“ und „Set-AzureRmTrafficManagerProfile“

In diesem Beispiel erstellen wir neue unter- und übergeordnete Traffic Manager-Profile, fügen die untergeordneten Profile den übergeordneten als geschachtelte Endpunkte hinzu und übermitteln diese Änderungen. (Kurz gesagt werden wir weder dem über- noch dem untergeordneten Profil anderen Endpunkte hinzufügen, obwohl dies normalerweise erforderlich wäre.)

	PS C:\> $child = New-AzureRmTrafficManagerProfile –Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $parent = New-AzureRmTrafficManagerProfile –Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName child-endpoint –TrafficManagerProfile $parent –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Beispiel 2: Hinzufügen geschachtelter Endpunkte mithilfe von „New-AzureRmTrafficManagerEndpoint“

In diesem Beispiel fügen wir ein vorhandenes untergeordnetes Profil als einen geschachtelten Endpunkt zu einem vorhandenen übergeordneten Profil hinzu. Dieses wird durch den Profilnamen und den Ressourcengruppennamen angegeben.

	PS C:\> $child = Get-AzureRmTrafficManagerEndpoint –Name child -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name child-endpoint –ProfileName parent -ResourceGroupName MyRG –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2


## Aktualisieren eines Traffic Manager-Endpunkts
Es gibt zwei Möglichkeiten zum Aktualisieren eines vorhandenen Traffic Manager-Endpunkts: 1. Rufen Sie das Traffic Manager-Profil mithilfe von „Get-AzureRmTrafficManagerProfile“ ab, aktualisieren Sie die Endpunkteigenschaften im Profil, und übernehmen Sie anschließend die Änderungen mit „Set-AzureRmTrafficManagerProfile“. Diese Methode hat den Vorteil, dass mehrere Endpunkte in einem Schritt aktualisiert werden können. 2. Rufen Sie den Traffic Manager-Endpunkt mithilfe von „Get-AzureRmTrafficManagerEndpoint“ ab, aktualisieren Sie die Endpunkteigenschaften, und übernehmen Sie die Änderungen mit „Set-AzureRmTrafficManagerEndpoint“. Diese Methode ist einfacher, da keine Indizierung im Endpunktarray im Profil erforderlich ist.

#### Beispiel 1: Aktualisieren von Endpunkten mithilfe von „Get-AzureRmTrafficManagerProfile“ und „Set-AzureRmTrafficManagerProfile“
In diesem Beispiel ändern wir die Priorität für zwei Endpunkte innerhalb eines vorhandenen Profils.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Beispiel 2: Aktualisieren eines Endpunkts mithilfe von „Get-AzureRmTrafficManagerEndpoint“ und „Set-AzureRmTrafficManagerEndpoint“
In diesem Beispiel ändern wir die Gewichtung für einen Endpunkt innerhalb eines vorhandenen Profils.

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## Aktivieren und Deaktivieren von Endpunkten und Profilen
Mit Traffic Manager können einzelne Endpunkte und sogar ganze Profile aktiviert und deaktiviert werden. Diese Änderungen können durch Abrufen, Aktualisieren und Festlegen des Endpunkts oder der Profilressourcen vorgenommen werden. Um diese allgemeinen Vorgänge zu vereinfachen, werden sie auch über dedizierte Cmdlets unterstützt.

#### Beispiel 1: Aktivieren und Deaktivieren eines Traffic Manager-Profils
Verwenden Sie zum Aktivieren eines Traffic Manager-Profils „Enable-AzureRmTrafficManagerProfile“. Das Profil kann mithilfe eines Profilobjekts angegeben werden. Dieses wird entweder über die Pipeline oder mithilfe des Parameters „-TrafficManagerProfile“ übergeben. Alternativ können Sie auch wie in diesem Beispiel direkt den Profil- und den Ressourcengruppennamen angeben.

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Beim Deaktivieren des Traffic Manager-Profils gehen Sie ähnlich vor:

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Das Cmdlet „Disable-AzureRmTrafficManagerProfile“ fordert Sie zur Bestätigung auf. Diese Aufforderung kann mithilfe des Parameters „-Force“ unterdrückt werden.

#### Beispiel 2: Aktivieren und Deaktivieren eines Traffic Manager-Endpunkts
Verwenden Sie zum Aktivieren eines Traffic Manager-Endpunkts „Enable-AzureRmTrafficManagerEndpoint“. Der Endpunkt kann mithilfe eines TrafficManagerEndpoint-Objekts angegeben werden. Dieses wird entweder über die Pipeline oder mithilfe des Parameters „-TrafficManagerEndpoint“ übergeben. Alternativ können Sie auch den Endpunktnamen, Endpunkttyp, Profilnamen und Ressourcengruppennamen verwenden:

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG

Beim Deaktivieren eines Traffic Manager-Endpunkts gehen Sie ähnlich vor:

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force

Wie „Disable-AzureRmTrafficManagerProfile“ enthält auch das Cmdlet „Disable-AzureRmTrafficManagerEndpoint“ eine Bestätigungsaufforderung, die mithilfe des Parameters „-Force“ unterdrückt werden kann.

## Löschen eines Traffic Manager-Endpunkts
Ein Traffic Manager-Endpunkt kann beispielsweise durch Abrufen des Profilobjekts (mithilfe von „Get-AzureRmTrafficManagerProfile“), Aktualisieren der Endpunktliste im lokalen Profilobjekt und Übernehmen der Änderungen (mithilfe von „Set-AzureRmTrafficManagerProfile“) gelöscht werden. Diese Methode ermöglicht das Übernehmen mehrerer Endpunktänderungen in einem Schritt.

Einzelne Endpunkte können auch mithilfe des Cmdlets „Remove-AzureRmTrafficManagerEndpoint“ entfernt werden:

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
	
Dieses Cmdlet fordert Sie zur Bestätigung auf, es sei denn, die Aufforderung wird mit dem Parameter „-Force“ unterdrückt.

## Löschen eines Traffic Manager-Profils
Verwenden Sie zum Löschen eines Traffic Manager-Profils das Cmdlet „Remove-AzureRmTrafficManagerProfile“. Geben Sie dabei den Profilnamen und den Ressourcengruppennamen an:

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG [-Force]

Das Cmdlet fordert Sie zur Bestätigung auf. Mit dem optionalen Schalter „-Force“ kann diese Aufforderung unterdrückt werden. Das zu löschende Profil kann auch mithilfe eines Profilobjekts angegeben werden:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Diese Sequenz kann auch weitergeleitet werden:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG | Remove-AzureTrafficManagerProfile [-Force]

## Nächste Schritte

[Traffic Manager-Überwachung](traffic-manager-monitoring.md)

[Leistungsüberlegungen zu Traffic Manager](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_0128_2016-->