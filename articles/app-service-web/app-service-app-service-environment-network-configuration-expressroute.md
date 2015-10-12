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
	ms.date="09/11/2015" 
	ms.author="stefsch"/>

# Details zur Netzwerkkonfiguration für App Service-Umgebungen mit ExpressRoute 

## Übersicht ##
Kunden können eine [Azure ExpressRoute][ExpressRoute]-Verbindung mit ihrer virtuellen Netzwerkinfrastruktur verbinden und so ihr lokales Netzwerks auf Azure ausdehnen. Eine App Service-Umgebung kann in einem Subnetz dieser [virtuellen Netzwerkinfrastruktur][virtualnetwork] erstellt werden. In der App Service-Umgebung ausgeführte Apps können dann sichere Verbindungen mit Back-End-Ressourcen herstellen, auf die nur über die ExpressRoute-Verbindung ein Zugriff besteht.

**Hinweis:** Eine App Service-Umgebung kann nicht in einem „v2“ virtuellen Netzwerk erstellt werden. App Service-Umgebungen werden derzeit nur in klassischen „v1“ virtuellen Netzwerken unterstützt.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Erforderliche Netzwerkverbindung ##
Es gibt Netzwerkverbindungsanforderungen für App Service-Umgebungen, die ursprünglich nicht von einem virtuellen Netzwerk erfüllt werden konnten, das mit einer ExpressRoute verbunden war.

App Service-Umgebungen erfordern für einen ordnungsgemäßen Betrieb Folgendes:


-  Ausgehende Netzwerkverbindungen mit Azure Storage weltweit und Verbindungen mit SQL-Datenbankressourcen, die sich in derselben Region wie die App Service-Umgebung befinden. Dieser Netzwerkpfad kann nicht durch interne Unternehmensproxys laufen, da sich dabei wahrscheinlich die tatsächliche NAT-Adresse des ausgehenden Netzwerkdatenverkehrs ändert. Durch Ändern der NAT-Adresse des ausgehendem Netzwerkdatenverkehrs einer App Service-Umgebung zu Azure Storage- und SQL-Datenbankendpunkten werden Verbindungsfehler verursacht.
-  Die DNS-Konfiguration für das virtuelle Netzwerk muss Endpunkte in den folgenden von Azure gesteuerten Domänen auflösen können: **. file.core.windows.net*, **. blob.core.windows.net*, **. database.windows.net*.
-  Die DNS-Konfiguration für das virtuelle Netzwerk muss stabil bleiben, wenn App Service-Umgebungen erstellt werden. Das gilt auch für Neukonfigurationen und Skalierungsänderungen an App Service-Umgebungen.   
-  Ist ein benutzerdefinierter DNS-Server am anderen Ende eines VPN-Gateways vorhanden, muss der DNS-Server erreichbar und verfügbar sein. 
-  Eingehender Netzwerkzugriff auf die erforderlichen Ports für App Service-Umgebungen muss entsprechend diesem [Artikel][requiredports] zugelassen werden.

Die DNS-Anforderungen können erfüllt werden, indem eine gültige DNS-Konfiguration für das virtuelle Netzwerk gewährleistet wird.

Die eingehenden Netzwerkzugriffsanforderungen können erfüllt werden, indem im Subnetz der App Service-Umgebung eine [Netzwerksicherheitsgruppe][NetworkSecurityGroups] konfiguriert wird, um, wie in diesem [Artikel][requiredports] beschrieben, den erforderlichen Zugriff zu ermöglichen.

## Aktivieren ausgehender Netzwerkverbindungen für eine App Service-Umgebung##
In der Standardeinstellung kündigt eine neu erstellte ExpressRoute-Verbindung eine Standardroute an, die ausgehende Internetverbindungen zulässt. Bei dieser Konfiguration kann sich eine App Service-Umgebung mit anderen Azure-Endpunkten verbinden.

Eine gängige Kundenkonfiguration sieht hingegen das Definieren einer eigenen Standardroute vor, die ausgehenden Internetdatenverkehr stattdessen zwingt, die lokale Proxy-/Firewallinfrastruktur eines Kunden zu durchlaufen. Bei diesem Datenfluss funktionieren App Service-Umgebungen nicht mehr, da ausgehender Datenverkehr entweder lokal blockiert oder mittels NAT in eine nicht mehr nachvollziehbare Gruppe von Adressen übersetzt wird, die nicht mehr mit verschiedenen Azure-Endpunkten funktionieren.

Die Lösung besteht darin, eine (oder mehrere) benutzerdefinierten Routen im Subnetz zu definieren, das die App Service-Umgebung enthält. Eine benutzerdefinierte Route definiert subnetzspezifische Routen, die anstelle der Standardroute berücksichtigt werden.

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

Sie müssen der Routentabelle eine oder mehrere Routen hinzufügen, um den ausgehenden Zugriff auf das Internet zu aktivieren. Das folgende Beispiel fügt genügend Routen hinzu, um alle möglichen in der Region USA, Westen verwendeten Azure-Adressen abzudecken.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Eine umfassende und aktualisierte Liste der von Azure verwendeten CIDR-Adressbereiche erhalten Sie durch Herunterladen einer XML-Datei mit allen Bereichen aus dem [Microsoft Download Center][DownloadCenterAddressRanges].

**Hinweis:** An einem bestimmten Punkt wird das CIDR-Kurzformat 0.0.0.0/0 für die Verwendung im *AddressPrefix*-Parameter zur Verfügung stehen. Dieses Kurzformat entspricht "allen Internetadressen". Derzeit müssen Entwickler stattdessen einen umfangreichen Satz von CIDR-Bereichen nutzen, der ausreicht, um alle möglichen Azure-Adressbereiche abzudecken.

**Schritt 3: Zuordnen der Routentabelle zum Subnetz mit der App Service-Umgebung**

Der letzte Konfigurationsschritt ist das Zuordnen der Routentabelle zum Subnetz, in dem die App Service-Umgebung bereitgestellt wird. Der folgende Befehl ordnet die Tabelle "DirectInternetRouteTable" dem Subnetz "ASESubnet" zu, das schließlich eine App Service-Umgebung enthalten wird.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Schritt 4: Abschließende Schritte**

Sobald die Routentabelle an das Subnetz gebunden ist, wird empfohlen, die gewünschte Auswirkung zunächst zu testen und zu bestätigen. Stellen Sie beispielsweise einen virtuellen Computer im Subnetz bereit, und bestätigen Sie Folgendes:


- Ausgehender Datenverkehr an Azure-Endpunkte wird nicht über die ExpressRoute-Verbindung übertragen.
- DNS-Lookups für Azure-Endpunkte werden ordnungsgemäß aufgelöst. 

Nachdem die oben genannten Schritte bestätigt wurden, können Sie den virtuellen Computer löschen und dann mit dem Erstellen einer App Service-Umgebung fortfahren.

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
 

<!-- IMAGES -->

<!---HONumber=Oct15_HO1-->