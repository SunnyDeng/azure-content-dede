<properties
   pageTitle="Azure-Ressourcen-Manager-Unterstützung für Traffic Manager – Vorschau | Microsoft Azure"
   description="Verwenden von PowerShell für Traffic Manager mit Azure-Ressourcen-Manager (ARM) in der Vorschau"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />





# Azure-Ressourcen-Manager-Unterstützung für Azure Traffic Manager – Vorschau
Der Azure-Ressourcen-Manager (ARM) ist das neue Verwaltungsframework für Dienste in Azure. Azure Traffic Manager kann jetzt mithilfe von APIs und Tools auf Basis von Azure-Ressourcen-Manager verwaltet werden. Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-preview-portal-using-resource-groups.md).

>[AZURE.NOTE]ARM-Unterstützung für Traffic Manager steht derzeit als Vorschau zur Verfügung, einschließlich REST-API, Azure PowerShell, Azure-CLI und .NET SDK.



## Ressourcenmodell

Azure Traffic Manager wird mithilfe einer Reihe von Einstellungen, dem sogenannten Traffic Manager-Profil, konfiguriert. Dieses Profil enthält DNS-Einstellungen, Einstellungen für das Routing von Datenverkehr, Endpunktüberwachungseinstellungen und die Liste der Dienstendpunkte, an die der Datenverkehr weitergeleitet wird.

In ARM wird jedes Traffic Manager-Profil durch eine ARM-Ressource vom Typ „TrafficManagerProfiles“ dargestellt und vom Ressourcenanbieter „Microsoft.Network“ verwaltet. Auf der REST-API-Ebene lautet der URI für jedes Profil wie folgt:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Vergleich mit Azure Traffic Manager-Dienstverwaltungs-API

Durch die Verwendung von ARM zum Konfigurieren von Traffic Manager-Profilen erhalten Sie Zugriff auf die gleichen Traffic Manager-Features wie mit der Nicht-ARM-Dienstverwaltungs-API. Eine Ausnahme stellen die unten aufgeführten Einschränkungen der Vorschau dar.

Die Features sind zwar die gleichen, die Terminologie hat sich jedoch geändert:

- Die Lastenausgleichsmethode (beeinflusst, wie Traffic Manager entscheidet, an welchen Endpunkt Datenverkehr bei DNS-Anforderungen weitergeleitet wird), wurde in „Datenverkehr-Routingmethode“ umbenannt.

- Die Datenverkehr-Routingmethode „Roundrobin“ heißt nun „Gewichtung“.

- Die Datenverkehr-Routingmethode „Failover“ wurde in „Priorität“ umbenannt.

## Einschränkungen der Vorschau
Da die Azure-Ressourcen-Manager-Unterstützung für Traffic Manager ein Vorschaudienst ist, liegen derzeit einige Einschränkungen vor:

- Traffic Manager-Profile, die mit der vorhandenen Nicht-ARM-Dienstverwaltungs-API, den vorhandenen Tools und dem vorhandenen Portal erstellt wurden, sind nicht über ARM verfügbar. Dies gilt auch im umgekehrten Fall. Die Migration von Profilen von Nicht-ARM-APIS zu ARM-APIs wird derzeit nicht unterstützt.

- 	Die REST-API unterstützt das Patching von Traffic Manager-Profilen nicht. Zum Aktualisieren einer Profileigenschaft müssen Sie das Profil abrufen (GET) und das geänderte Profil speichern (PUT).
- 	Nur externe Endpunkte werden unterstützt. Diese können immer noch zur Verwendung von Traffic Manager mit Azure-basierten Diensten genutzt werden. In diesem Fall werden die Endpunkte zum Tarif interner Endpunkte abgerechnet. (Die einzige Auswirkung der Verwendung externer Endpunkte ist, dass sie nicht automatisch deaktiviert oder gelöscht werden, wenn der zugrunde liegende Azure-Dienst deaktiviert oder gelöscht wird. Stattdessen müssen Sie den Endpunkt manuell deaktivieren oder löschen).
-	Azure Traffic Manager ist noch nicht im Azure-Portal, sondern nur im klassischen Portal verfügbar.

## Einrichten von Azure PowerShell

Diese Anweisungen verwenden Microsoft Azure PowerShell, die mithilfe der folgenden Schritte konfiguriert werden muss.

Benutzer, die nicht PowerShell verwenden, können die gleichen Vorgänge auch über andere Schnittstellen ausführen.

### Schritt 1
Installieren Sie die neueste Azure PowerShell, die auf der Azure-Downloadseite zur Verfügung steht.
### Schritt 2
Wechseln Sie den PowerShell-Modus, um die ARM-Cmdlets zu verwenden. Weitere Informationen finden Sie unter „Verwenden von Windows PowerShell mit dem Ressourcen-Manager“.

	PS C:\> Switch-AzureMode -Name AzureResourceManager
### Schritt 3
Melden Sie sich beim Azure-Konto an.

	PS C:\> Add-AzureAccount

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.

### Schritt 4
Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

	PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Um eine Liste der verfügbaren Abonnements anzuzeigen, verwenden Sie das Cmdlet „Get-AzureSubscription“.

### Schritt 5

 Der Traffic Manager-Dienst wird vom Ressourcenanbieter „Microsoft.Network“ verwaltet. Ihr Azure-Abonnement muss für die Verwendung dieses Ressourcenanbieters registriert werden, bevor Sie Traffic Manager über ARM verwenden können. Dieser Schritt muss für jedes Abonnement einmal ausgeführt werden.

	PS C:\> Register-AzureProvider –ProviderNamespace Microsoft.Network

### Schritt 6
Erstellen Sie eine Ressourcengruppe (überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden).

	PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Da alle Traffic Manager-Profilressourcen global und nicht regional sind, hat die Auswahl des Speicherorts für die Ressourcengruppe jedoch keine Auswirkungen auf Azure Traffic Manager.

## Erstellen eines Traffic Manager-Profils

Verwenden Sie zum Erstellen eines Traffic Manager-Profils das Cmdlet „New-AzureTrafficManagerProfile“:

	PS C:\> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Die Parameter lauten wie folgt:

- Name: Der ARM-Ressourcenname für die Traffic Manager-Profilressource. Profile in der gleichen Ressourcengruppe müssen eindeutige Namen aufweisen. Dieser Name unterscheidet sich von den DNS-Namen, die für DNS-Abfragen verwendet werden.

-	ResourceGroupName: Der Name der ARM-Ressourcengruppe mit der Profilressource

-	TrafficRoutingMethod: Gibt die Routingmethode an. Hiermit wird ermittelt, welcher Endpunkt als Reaktion auf eingehende DNS-Abfragen zurückgegeben wird. Mögliche Werte sind „Leistung“, „Gewichtung“ und „Priorität“.

-	RelativeDnsName: Gibt den relativen DNS-Namen an, der von diesem Traffic Manager-Profil bereitgestellt wird. Dieser Wert wird mit dem DNS-Domänennamen kombiniert, der von Azure Traffic Manager zum Erstellen des vollqualifizierten Domänennamens (FQDN) des Profils verwendet wird. Beispielsweise ergibt der Wert „contoso“ ein Traffic Manager-Profil mit dem vollqualifizierten Namen „contoso.trafficmanager.net“.

-	TTL: Gibt die DNS-Gültigkeitsdauer (Time-To-Live, TTL) in Sekunden an. Dadurch werden die lokale DNS-Auflösung und DNS-Clients informiert, wie lange die von diesem Traffic Manager-Profil bereitgestellten DNS-Antworten zwischengespeichert werden sollen.

-	MonitorProtocol: Gibt das Protokoll an, mit dem die Endpunktintegrität überwacht werden soll. Mögliche Werte sind „HTTP“ und „HTTPS“.

-	MonitorPort: Gibt den TCP-Port an, mit dem die Endpunktintegrität überwacht wird.

-	MonitorPath: Gibt den Pfad relativ zum Endpunktdomänennamen an, mit dem die Endpunktintegrität getestet wird.

Das Cmdlet erstellt ein Traffic Manager-Profil in Azure Traffic Manager und gibt ein entsprechendes Profilobjekt zurück. Das Profil enthält zu diesem Zeitpunkt keine Endpunkte. Ausführliche Informationen zum Hinzufügen von Endpunkten zu einem Traffic Manager-Profil finden Sie unter [Aktualisieren eines Traffic Manager-Profils](#update-a-traffic-manager-profile) .

## Abrufen eines Traffic Manager-Profils

Rufen Sie ein vorhandenes Traffic Manager-Profilobjekt mit dem Cmdlet „Get-AzureTrafficManagerProfle“ ab:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

Dieses Cmdlet gibt ein Traffic Manager-Profilobjekt zurück.

## Aktualisieren eines Traffic Manager-Profils [](#update-traffic-manager-profile)

Traffic Manager-Profile werden mithilfe von 3 Schritten geändert. Mit diesen Schritten können Sie beispielsweise Endpunkte hinzufügen oder löschen oder Profileinstellungen ändern:

1.	Rufen Sie das Profil mithilfe von „Get-AzureTrafficManagerProfile“ ab (oder verwenden Sie das von „New-AzureTrafficManagerProfile“ zurückgegebene Profil).

2.	Ändern Sie das Profil, indem Sie Endpunkte hinzufügen, Endpunkte entfernen oder die Endpunktparameter oder Profilparameter ändern. Diese Änderungen sind offline – nur das lokale Objekt, das das Profil darstellt, wird geändert.

3.	Übernehmen Sie Ihre Änderungen mithilfe des Cmdlets „Set-AzureTrafficManagerProfile“. Dadurch wird das vorhandene Profil in Azure Traffic Manager durch das bereitgestellte Profil ersetzt.

Dies wird anhand der folgenden Beispiele ausführlicher erläutert:

### Hinzufügen von Endpunkten zu einem Profil

Endpunkte können einem Traffic Manager-Profil mithilfe des Cmdlets „Add-AzureTrafficManagerEndpointConfig“ hinzugefügt werden:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Die Parameter von „Add-AzureTrafficManagerEndpointConfig“ lauten folgendermaßen:

- EndpointName: Der Name des Endpunkts. Endpunkte im selben Profil müssen unterschiedliche Namen haben. Dadurch wird bei Dienstverwaltungsvorgängen auf den Endpunkt verwiesen. Es handelt sich dabei nicht um den DNS-Namen des Endpunkts.

-	TrafficManagerProfile: Das Traffic Manager Profilobjekt, dem der Endpunkt hinzugefügt wird.

-	Type: Der Typ des Traffic Manager-Endpunkts. Derzeit wird über die ARM-API nur der Typ „ExternalEndpoint“ unterstützt (siehe [Einschränkungen der Vorschau](#preview-limitations)).

-	Target: Der vollqualifizierte DNS-Name des Endpunkts. Traffic Manager gibt diesen Wert in DNS-Antworten zurück, um Datenverkehr an diesen Endpunkt weiterzuleiten.

-	EndpointStatus: Gibt den Status des Endpunkts an. Ist der Endpunkt aktiviert, wird die Endpunktintegrität getestet und in die Datenverkehr-Routingmethode aufgenommen. Mögliche Werte sind „Aktiviert“ oder „Deaktiviert“.

-	Weight: Gibt die dem Endpunkt zugeordnete Gewichtung an. Dieser Parameter wird nur verwendet, wenn das Traffic Manager-Profil für die Verwendung der gewichteten Datenverkehr-Routingmethode konfiguriert ist. Mögliche Werte: 1 bis 1.000

-	Priority: Gibt die Priorität dieses Endpunkts an, wenn die Datenverkehr-Routingmethode „Priorität“ verwendet wird. Die Priorität muss im Bereich von 1 bis 1.000 liegen. Niedrigere Werte stellen eine höhere Priorität dar.

-	EndpointLocation: Gibt den Speicherort des externen Endpunkts an. Wird für die Datenverkehr-Routingmethode „Leistung“ verwendet. Eine Liste der möglichen Speicherorte finden Sie unter „Get-AzureLocation“.

Endpunktstatus, Gewichtung und Priorität sind optionale Parameter. Werden sie nicht angegeben, werden sie nicht an PowerShell übergeben. In diesem Fall gelten die serverseitigen Standardwerte.

### Entfernen von Endpunkten aus einem Profil

Um einen Endpunkt aus einem Profil zu entfernen, verwenden Sie „Remove-AzureTrafficmanagerEndpointConfig“, und geben Sie dabei den Namen des Endpunkts an, der entfernt werden soll:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Die Reihenfolge der Vorgänge zum Hinzufügen oder Entfernen von Endpunkten kann auch weitergeleitet werden. Das Profilobjekt wird über die Pipe und nicht als Parameter übergeben. Beispiel:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### Ändern von Profil- oder Endpunkteinstellungen

Profil- und Endpunktparameter können offline geändert werden. Die Änderungen werden mithilfe von „Set-AzureTrafficManagerProfile“ übernommen. Einzige Ausnahme: Das RelativeDnsName-Element des Profils kann nicht geändert werden, nachdem das Profil erstellt wurde (wenn Sie diesen Wert ändern möchten, müssen Sie das Profil löschen und erneut erstellen). So ändern Sie beispielsweise die Profilgültigkeitsdauer und den Status des ersten Endpunkts:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> $profile.Ttl = 300
	PS C:\> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### Löschen eines Traffic Manager-Profils
Verwenden Sie zum Löschen eines Traffic Manager-Profils das Cmdlet „Remove-AzureTrafficManagerProfile“. Geben Sie dabei den Profilnamen und die Ressourcengruppennamen an:

	PS C:\> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

Das Cmdlet fordert Sie zur Bestätigung auf. Mit dem optionalen Schalter „-Force“ kann diese Aufforderung unterdrückt werden. Das zu löschende Profil kann auch mithilfe eines Profilobjekts angegeben werden:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Diese Sequenz kann auch weitergeleitet werden:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## Siehe auch

[Was ist Traffic Manager?](traffic-manager-overview.md)

[Erste Schritte mit Azure-Cmdlets](https://msdn.microsoft.com/library/jj554332.aspx)
 

<!---HONumber=August15_HO8-->