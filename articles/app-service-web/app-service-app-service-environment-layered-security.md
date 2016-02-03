<properties 
	pageTitle="Mehrstufige Sicherheitsarchitektur mit App Service-Umgebungen" 
	description="Implementieren einer mehrstufigen Sicherheitsarchitektur mit App Service-Umgebungen." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="stefsch"/>

# Implementieren einer mehrstufigen Sicherheitsarchitektur mit App Service-Umgebungen

## Übersicht ##
 
Da App Service-Umgebungen eine in einem virtuellen Netzwerk bereitgestellte isolierte Laufzeitumgebung bieten, können Entwickler eine mehrstufige Sicherheitsarchitektur erstellen, in der sie abgestuften Netzwerkzugriff für jede physische Anwendungsschicht gewähren können.

Üblicherweise sollten API-Back-Ends nicht dem allgemeinen Internetzugriff preisgegeben werden, und APIs sollten nur von Upstream-Web-Apps aufgerufen werden können. Um den öffentlichen Zugriff auf API-Anwendungen zu beschränken, können [Netzwerksicherheitsgruppen (Network Security Groups, NSGs)][NetworkSecurityGroups] in Subnetzen mit App Service-Umgebungen verwendet werden.

Das nachfolgende Diagramm zeigt eine Beispielarchitektur mit einer WebAPI-basierten App, die in einer App Service-Umgebung bereitgestellt wurde. Drei getrennte Web-App-Instanzen, die in drei getrennten App Service-Umgebungen bereitgestellt wurden, führen Back-End-Aufrufe an die gleiche WebAPI-App aus.

![Konzeptionelle Architektur][ConceptualArchitecture]

Die grünen Pluszeichen weisen darauf hin, dass die Netzwerksicherheitsgruppe im Subnetz, das „apiase“ enthält, eingehende Aufrufe von den Upstream-Web-Apps sowie Aufrufe der App an sich selbst zulässt. Die gleiche Netzwerksicherheitsgruppe verweigert jedoch explizit den Zugriff durch allgemeinen eingehenden Datenverkehr aus dem Internet.

Im weiteren Verlauf dieses Themas werden die Schritte erläutert, die zum Konfigurieren der Netzwerksicherheitsgruppe in dem Subnetz erforderlich sind, das „apiase“ enthält.

## Festlegen des Netzwerkverhaltens ##
Um zu ermitteln, welche Netzwerksicherheitsregeln erforderlich sind, müssen Sie festlegen, welche Netzwerkclients auf die App Service-Umgebung mit der API-App zugreifen können und welche Clients blockiert werden.

Da [Netzwerksicherheitsgruppen][NetworkSecurityGroups] auf Subnetze angewendet und App Service-Umgebungen in Subnetzen bereitgestellt werden, gelten die Regeln einer Netzwerksicherheitsgruppe für **alle** Apps, die in der jeweiligen App Service-Umgebung ausgeführt werden. Betrachten Sie die Beispielarchitektur für diesen Artikel: Sobald eine Netzwerksicherheitsgruppe auf das Subnetz angewendet wird, das „apiase“ enthält, werden alle Apps, die in der App Service-Umgebung „apiase“ ausgeführt werden, durch den gleichen Satz Sicherheitsregeln geschützt.

- **Ermitteln der ausgehenden IP-Adressen von Aufruffunktionen für den Upstream:** Wie lauten die IP-Adressen der Aufruffunktionen für den Upstream? Diesen Adressen muss in der Netzwerksicherheitsgruppe explizit Zugriff gewährt werden. Da Aufrufe zwischen App Service-Umgebungen als „Internetaufrufe“ betrachtet werden, muss der ausgehenden IP-Adresse, die jeder der App Service-Upstreamumgebungen zugewiesen ist, in der Netzwerksicherheitsgruppe Zugriff auf das Subnetz „apiase“ gewährt werden. Weitere Informationen zum Ermitteln der ausgehenden IP-Adresse für Apps, die in einer App Service-Umgebung ausgeführt werden, finden Sie im Artikel [Übersicht über die Netzwerkarchitektur von App Service-Umgebungen][NetworkArchitecture].
- **Muss die Back-End-API-App sich selbst aufrufen?** Ein Aspekt, der nicht selten übersehen wird, ist ein Szenario, in dem die Back-End-Anwendung sich selbst aufrufen muss. Wenn sich die Back-End-API-Anwendung in einer App Service-Umgebung selbst aufrufen muss, wird auch dies als „Internetaufruf“ behandelt. In der Beispielarchitektur muss zu diesem Zweck auch der Zugriff von der ausgehenden IP-Adresse der App Service-Umgebung „apiase“ gewährt werden.

## Einrichten der Netzwerksicherheitsgruppe ##
Sobald die ausgehenden IP-Adressen bekannt sind, besteht der nächste Schritt darin, eine Netzwerksicherheitsgruppe zu erstellen und einzurichten. Da App Service-Umgebungen zurzeit nur in virtuellen „v1“-Netzwerken unterstützt werden, erfolgt die [Konfiguration der Netzwerksicherheitsgruppe][NetworkSecurityGroupsClassic] mithilfe der klassischen Einrichtung von Netzwerksicherheitsgruppen in PowerShell.

In der Beispielarchitektur befinden sich die Umgebungen in der Region „USA, Süden-Mitte“, daher wird in dieser Region eine leere Netzwerksicherheitsgruppe erstellt:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Zuerst wird eine explizite Zulassungsregel für die Azure-Verwaltungsinfrastruktur hinzugefügt, wie im Artikel zu [eingehendem Datenverkehr][InboundTraffic] für App Service-Umgebungen erläutert.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
Als Nächstes werden zwei Regeln hinzugefügt, die HTTP- und HTTPS-Aufrufe aus der ersten App Service-Upstreamumgebung („fe1ase“) zulassen.

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Wiederholen Sie diesen Schritt für die zweite und dritte App Service-Upstreamumgebung („fe2ase“ und „fe3ase“).

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Zum Schluss wird der ausgehenden IP-Adresse der App Service-Umgebung der Back-End-API Zugriff gewährt, sodass diese API sich selbst aufrufen kann.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Es müssen keine weiteren Netzwerksicherheitsregeln konfiguriert werden, da jede Netzwerksicherheitsgruppe über einen Satz Standardregeln verfügt, die Zugriffsversuche aus dem Internet standardmäßig blockieren.

Unten sehen Sie die vollständige Liste der Regeln in der Netzwerksicherheitsgruppe. Beachten Sie, dass die letzte, hervorgehobene Regel den Zugriff aller aufrufenden Funktionen blockiert, mit Ausnahme derjenigen, denen explizit Zugriff gewährt wurde.

![Konfiguration von Netzwerksicherheitsgruppen][NSGConfiguration]

Der letzte Schritt besteht darin, die Netzwerksicherheitsgruppe auf das Subnetz anzuwenden, in dem sich die App Service-Umgebung „apiase“ befindet.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Nach Anwendung der Netzwerksicherheitsgruppe auf das Subnetz sind Aufrufe an die „apiase“-Umgebung nur noch für die drei App Service-Upstreamumgebungen sowie die App Service-Umgebung, die das API-Back-End umfasst, zulässig.


## Zusätzliche Links und Informationen ##
Konfigurieren von [Netzwerksicherheitsgruppen][NetworkSecurityGroupsClassic] in klassischen virtuellen Netzwerken.

Informationen zu [ausgehenden IP-Adressen][NetworkArchitecture] und App Service-Umgebungen.

In einer App Service-Umgebung verwendete [Netzwerkports][InboundTraffic].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkSecurityGroupsClassic]: https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-ps/
[InboundTraffic]: https://azure.microsoft.com/de-DE/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]: ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png

<!---HONumber=AcomDC_0107_2016-->