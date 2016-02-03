<properties 
	pageTitle="Details zur Netzwerkkonfiguration für das Arbeiten mit Express Route" 
	description="Details zur Netzwerkkonfiguration zum Ausführen von App Service-Umgebungen in einem an eine ExpressRoute-Verbindung angeschlossenen virtuellen Netzwerk" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="stefsch"/>

# Details zur Netzwerkkonfiguration für App Service-Umgebungen mit ExpressRoute 

## Übersicht ##
Kunden können eine [Azure ExpressRoute][ExpressRoute]-Verbindung mit ihrer virtuellen Netzwerkinfrastruktur verbinden und so ihr lokales Netzwerks auf Azure ausdehnen. Eine App Service-Umgebung kann in einem Subnetz dieser [virtuellen Netzwerkinfrastruktur][virtualnetwork] erstellt werden. In der App Service-Umgebung ausgeführte Apps können dann sichere Verbindungen mit Back-End-Ressourcen herstellen, auf die nur über die ExpressRoute-Verbindung ein Zugriff besteht.

**Hinweis:** Eine App Service-Umgebung kann nicht in einem „v2“ virtuellen Netzwerk erstellt werden. App Service-Umgebungen werden derzeit nur in klassischen „v1“ virtuellen Netzwerken unterstützt.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Erforderliche Netzwerkverbindung ##
Es gibt Netzwerkverbindungsanforderungen für App Service-Umgebungen, die ursprünglich nicht von einem virtuellen Netzwerk erfüllt werden konnten, das mit einer ExpressRoute verbunden war. App Service-Umgebungen erfordern für einen ordnungsgemäßen Betrieb Folgendes:


-  Ausgehende Netzwerkverbindungen mit Azure-Speicherendpunkten in der ganzen Welt. Dies beinhaltet sowohl Endpunkte, die sich in der gleichen Region wie die App Service-Umgebung befinden, als auch Speicherendpunkte in **anderen** Azure-Regionen. Azure Storage-Endpunkte werden unter den folgenden DNS-Domänen aufgelöst: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* und *file.core.windows.net*.  
-  Ausgehende Netzwerkverbindungen mit SQL-Datenbankendpunkten, die sich in der gleichen Region wie die App Service-Umgebung befinden. SQL-Datenbankendpunkte werden unter der folgenden Domäne aufgelöst: *database.windows.net*.
-  Ausgehende Netzwerkverbindungen mit den Endpunkten auf der Azure-Verwaltungsebene (ASM- und ARM-Endpunkte). Dies beinhaltet ausgehende Verbindungen mit *management.core.windows.net* und *management.azure.com*. 
-  Ausgehende Netzwerkkonnektivität mit *ocsp.msocsp.com*. Dies ist zur Unterstützung von SSL-Funktionen erforderlich.
-  Die DNS-Konfiguration für das virtuelle Netzwerk muss alle der zuvor genannten Endpunkte und Domänen auflösen können. Können diese Endpunkte nicht aufgelöst werden, schlägt die Erstellung von App Service-Umgebungen fehl, und vorhandene App Service-Umgebungen werden als fehlerhaft gekennzeichnet.
-  Falls ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden ist, muss der DNS-Server über das Subnetz mit der App Service-Umgebung erreichbar sein. 
-  Der ausgehende Netzwerkpfad kann weder durch interne Unternehmensproxys laufen noch zwangsweise zur lokalen Infrastruktur getunnelt werden. Andernfalls wird die tatsächliche NAT-Adresse des ausgehenden Netzwerkdatenverkehrs der App Service-Umgebung geändert. Das Ändern der NAT-Adresse des ausgehenden Netzwerkdatenverkehrs einer App Service-Umgebung verursacht bei vielen der oben genannten Endpunkte Verbindungsfehler. Dies führt zu Fehlern bei der Erstellung von App Service-Umgebungen und dazu, dass zuvor fehlerfreie App Service-Umgebungen als fehlerhaft gekennzeichnet werden.  
-  Eingehender Netzwerkzugriff auf die erforderlichen Ports für App Service-Umgebungen muss entsprechend diesem [Artikel][requiredports] zugelassen werden.

Die DNS-Anforderungen können erfüllt werden, indem Sie sicherstellen, dass eine gültige DNS-Infrastruktur für das virtuelle Netzwerk konfiguriert und beibehalten wird. Falls die DNS-Konfiguration nach der Erstellung einer App Service-Umgebung geändert wird, können Entwickler erzwingen, dass eine App Service-Umgebung die neue DNS-Konfiguration übernimmt. Wird im [Azure-Portal][NewPortal] über das Symbol „Neu starten“ oben auf dem Verwaltungsblatt der App Service-Umgebung ein paralleler Neustart der Umgebung ausgelöst, übernimmt diese die neue DNS-Konfiguration.

Die eingehenden Netzwerkzugriffsanforderungen können erfüllt werden, indem im Subnetz der App Service-Umgebung eine [Netzwerksicherheitsgruppe][NetworkSecurityGroups] konfiguriert wird, um, wie in diesem [Artikel][requiredports] beschrieben, den erforderlichen Zugriff zu ermöglichen.

## Aktivieren ausgehender Netzwerkverbindungen für eine App Service-Umgebung##
In der Standardeinstellung kündigt eine neu erstellte ExpressRoute-Verbindung eine Standardroute an, die ausgehende Internetverbindungen zulässt. Bei dieser Konfiguration kann sich eine App Service-Umgebung mit anderen Azure-Endpunkten verbinden.

Eine gängige Kundenkonfiguration sieht hingegen das Definieren einer eigenen Standardroute (0.0.0.0/0) vor, die ausgehenden Internetdatenverkehr stattdessen zwingt, die lokale Infrastruktur zu durchlaufen. Bei diesem Datenfluss funktionieren App Service-Umgebungen nicht mehr, da ausgehender Datenverkehr entweder lokal blockiert oder mittels NAT in eine nicht mehr nachvollziehbare Gruppe von Adressen übersetzt wird, die nicht mehr mit verschiedenen Azure-Endpunkten funktionieren.

Die Lösung besteht darin, eine (oder mehrere) benutzerdefinierten Routen im Subnetz zu definieren, das die App Service-Umgebung enthält. Eine benutzerdefinierte Route definiert subnetzspezifische Routen, die anstelle der Standardroute berücksichtigt werden.

Nach Möglichkeit sollte die folgende Konfiguration verwendet werden:

- Die ExpressRoute-Konfiguration kündigt 0.0.0.0/0 an und tunnelt standardmäßig allen ausgehenden Datenverkehr zwangsweise lokal.
- Die auf das Subnetz mit der App Service-Umgebung angewendete benutzerdefinierte Route (UDR) definiert 0.0.0.0/0 mit dem nächsten Hoptyp „Internet“ (ein Beispiel dazu finden Sie weiter unten in diesem Artikel).

Gemeinsam führen diese Schritte dazu, dass die UDR auf Subnetzebene Vorrang vor erzwungenem ExpressRoute-Tunneln hat. Dadurch wird ausgehender Internetzugriff aus der App Service-Umgebung sichergestellt.

**WICHTIG:** Die in einer UDR definierten Routen **müssen** ausreichend spezifisch sein, damit sie Vorrang vor allen von der ExpressRoute-Konfiguration angekündigten Routen erhalten. Im folgenden Beispiel wird der umfassende Adressbereich 0.0.0.0/0 verwendet. Dieser kann daher versehentlich durch Routenankündigungen mit spezifischeren Adressbereichen überschrieben werden.

**SEHR WICHTIG:** App Service-Umgebungen werden nicht mit ExpressRoute-Konfigurationen unterstützt, die **fälschlich Routen „über Kreuz“ vom öffentlichen Peeringpfad zum privaten Peeringpfad ankündigen**. ExpressRoute-Konfigurationen, für die öffentliches Peering konfiguriert ist, erhalten Routenankündigungen von Microsoft für zahlreiche Microsoft Azure-IP-Adressbereiche. Werden diese Adressbereiche falsch über Kreuz auf dem privaten Peeringpfad angekündigt, führt dies dazu, dass alle ausgehenden Netzwerkpakete aus dem Subnetz der App Service-Umgebung fälschlich zwangsweise zur lokalen Netzwerkinfrastruktur eines Kunden getunnelt werden. Dieser Netzwerkdatenfluss führt dazu, dass App Service-Umgebungen unterbrochen werden. Die Lösung für dieses Problem besteht darin, „Über-Kreuz-Ankündigungen“ von Routen vom öffentlichen Peeringpfad zum privaten Peeringpfad zu verhindern.

Hintergrundinformationen zu benutzerdefinierten Routen finden Sie in dieser [Übersicht][UDROverview].

Details zum Erstellen und Konfigurieren benutzerdefinierter Routen finden Sie in dieser [Anleitung][UDRHowTo].

## Beispielkonfiguration einer benutzerdefinierten Route für eine App Service-Umgebung ##

**Voraussetzungen**

1. Installieren Sie die aktuellste Azure PowerShell über die [Seite mit den Azure-Downloads][AzureDownloads] (vom Juni 2015 oder später). Unter "Befehlszeilentools" befindet sich unter "Windows PowerShell" der Link "Installieren", über den die aktuellen PowerShell-Cmdlets installiert werden.

2. Es wird empfohlen, ein eindeutiges Subnetz für die ausschließliche Verwendung durch eine App Service-Umgebung zu erstellen. Dadurch wird sichergestellt, dass die im Subnetz eingerichteten benutzerdefinierten Routen nur für ausgehenden Datenverkehr für die App Service-Umgebung geöffnet werden.
3. **Wichtig**: Stellen Sie die App Service-Umgebung erst bereit, **nachdem** die folgenden Konfigurationsschritte erfolgt sind. Dadurch wird sichergestellt, dass ausgehende Netzwerkverbindungen verfügbar sind, bevor Sie versuchen, eine App Service-Umgebung bereitzustellen.

**Schritt 1: Erstellen einer benannten Routentabelle**

Im folgenden Codeausschnitt wird eine Routentabelle namens "DirectInternetRouteTable" in der Azure-Region USA, Westen erstellt:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Schritt 2: Erstellen einer oder mehrerer Routen in der Routentabelle**

Sie müssen der Routentabelle eine oder mehrere Routen hinzufügen, um den ausgehenden Zugriff auf das Internet zu aktivieren.

Für die Konfiguration des ausgehenden Zugriffs auf das Internet wird empfohlen, wie unten dargestellt eine Route für 0.0.0.0/0 zu definieren.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Denken Sie daran, dass der Adressbereich 0.0.0.0/0 durch spezifischere Adressbereiche überschrieben wird, die von ExpressRoute angekündigt werden. Entsprechend der obigen Empfehlung sollte zudem eine UDR mit einer 0.0.0.0/0-Route in Verbindung mit einer ExressRoute-Konfiguration verwendet werden, die nur 0.0.0.0/0 ankündigt.

Alternativ können Sie eine umfassende und aktualisierte Liste der von Azure verwendeten CIDR-Adressbereiche herunterladen. Die XML-Datei mit sämtlichen Azure-IP-Adressbereichen steht im [Microsoft Download Center][DownloadCenterAddressRanges] zur Verfügung.

Beachten Sie, dass sich diese Bereiche im Laufe der Zeit ändern und eine UDR daher regelmäßig manuell aktualisiert werden muss, um sie auf dem neuesten Stand zu halten. Da eine einzelne UDR zudem maximal 100 Routen enthalten kann, müssen Sie die Azure-IP-Adressbereiche zudem entsprechend „zusammenfassen“. Denken Sie dabei daran, dass die in einer UDR definierten Routen spezifischer sein müssen als die von Ihrer ExpressRoute-Konfiguration angekündigten Routen.


**Schritt 3: Zuordnen der Routentabelle zum Subnetz mit der App Service-Umgebung**

Der letzte Konfigurationsschritt ist das Zuordnen der Routentabelle zum Subnetz, in dem die App Service-Umgebung bereitgestellt wird. Der folgende Befehl ordnet die Tabelle "DirectInternetRouteTable" dem Subnetz "ASESubnet" zu, das schließlich eine App Service-Umgebung enthalten wird.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Schritt 4: Abschließende Schritte**

Sobald die Routentabelle an das Subnetz gebunden ist, wird empfohlen, die gewünschte Auswirkung zunächst zu testen und zu bestätigen. Stellen Sie beispielsweise einen virtuellen Computer im Subnetz bereit, und bestätigen Sie Folgendes:


- Ausgehender Datenverkehr zu den weiter oben genannten Azure-Endpunkten und Nicht-Azure-Endpunkten wird **nicht** über die ExpressRoute-Verbindung übertragen. Dieses Verhalten muss unbedingt überprüft werden, da die Erstellung von App Service-Umgebungen fehlschlägt, wenn ausgehender Datenverkehr aus dem Subnetz weiterhin zwangsweise lokal getunnelt wird. 
- DNS-Suchen nach den zuvor genannten Endpunkten werden alle korrekt aufgelöst. 

Nachdem Sie die obigen Schritte ausgeführt und überprüft haben, müssen Sie den virtuellen Computer löschen, da das Subnetz bei der Erstellung der App Service-Umgebung „leer“ sein muss.
 
Fahren Sie anschließend mit dem Erstellen einer App Service-Umgebung fort.

## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in die App Service-Umgebung][IntroToAppServiceEnvironment]

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]: https://portal.azure.com
 

<!-- IMAGES -->

<!---HONumber=AcomDC_0107_2016-->