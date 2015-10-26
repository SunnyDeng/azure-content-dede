<properties 
	pageTitle="Geografisch verteilte Skalierung mit App Service-Umgebungen" 
	description="Erfahren Sie, wie Apps mit geografischer Verteilung mit Traffic Manager und App Service-Umgebungen horizontal hochskaliert werden." 
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
	ms.date="10/08/2015" 
	ms.author="stefsch"/>

# Geografisch verteilte Skalierung mit App Service-Umgebungen

## Übersicht ##
Anwendungsszenarien, die eine sehr große Skalierung erfordern, können die Computeressourcenkapazität überschreiten, die für eine einzelne Bereitstellung einer App verfügbar ist. Anwendungen mit Abstimmfunktion, Sportveranstaltungen und im Fernsehen übertragene Unterhaltungssendungen sind Beispiele für Szenarien, die eine extrem hohe Skalierbarkeit erfordern. Anforderungen für eine hohe Skalierbarkeit können durch das horizontale Hochskalieren von Apps erfüllt werden, wobei mehrere App-Bereitstellungen innerhalb einer Region sowie regionsübergreifend erfolgen, um extreme Lastanforderungen zu verarbeiten.

App Service-Umgebungen sind eine ideale Plattform für das horizontale Hochskalieren. Wenn eine App Service-Umgebungskonfiguration ausgewählt wurde, die eine bekannte Anforderungsrate unterstützen kann, können Entwickler nach Bedarf zusätzliche App Service-Umgebungen bereitstellen, um eine gewünschte Höchstlastkapazität zu erreichen.

Nehmen Sie beispielsweise an, eine App, die auf einer App Service-Umgebungskonfiguration ausgeführt wird, wurde für die Verarbeitung von 20.000 Anforderungen pro Sekunde (RPS) getestet. Ist die gewünschte Höchstlastkapazität 100.000 RPS, können fünf (5) App Service-Umgebungen erstellt und konfiguriert werden, um sicherzustellen, dass die Anwendung die voraussichtliche maximale Last verarbeiten kann.

Da Kunden in der Regel auf Apps mit einer benutzerdefinierten Domäne (oder einer Vanity-Domäne) zugreifen, benötigen Entwickler eine Möglichkeit zur Verteilung von App-Anforderungen auf alle Instanzen der App Service-Umgebung. Eine hervorragende Möglichkeit dies zu erreichen, ist das Auflösen der benutzerdefinierten Domäne mit einem [Azure Traffic Manager-Profil][AzureTrafficManagerProfile]. Das Traffic Manager-Profil kann so konfiguriert werden, dass es auf alle einzelnen App Service-Umgebungen verweist. Traffic Manager verteilt automatisch die Kunden auf alle App Service-Umgebungen. Dabei werden die Einstellungen für den Lastenausgleich im Traffic Manager-Profil verwendet. Dieser Ansatz funktioniert unabhängig davon, ob alle App Service-Umgebungen in einer einzelnen Azure-Region oder in mehreren Azure-Regionen weltweit bereitgestellt werden.

Da Kunden zudem auf Apps über die Vanity-Domäne zugreifen, kennen Kunden die Anzahl der App Service-Umgebungen nicht, die eine App ausführen. Daher können Entwickler schnell und einfach App Service-Umgebungen basierend auf der beobachteten Datenverkehrslast hinzufügen und entfernen.

Das folgende konzeptionelle Diagramm zeigt eine Anwendung, die über drei App Service-Umgebungen in einer einzelnen Region horizontal hockskaliert ist.

![Konzeptionelle Architektur][ConceptualArchitecture]

Im weiteren Verlauf dieses Themas werden die Schritte zum Einrichten einer verteilten Topologie für die Beispiel-App mit mehreren App Service-Umgebungen erläutert.

## Planen der Topologie ##
Vor der Erstellung einer verteilten App ist es hilfreich, über einige Informationen zu verfügen.

- **Benutzerdefinierte Domäne für die App:** Wie lautet der Name der benutzerdefinierten Domäne, mit dem Kunden auf die App zugreifen? Für die Beispiel-App lautet der Name der benutzerdefinierten Domäne *www.scalableasedemo.com*
- **Traffic Manager-Domäne:** Ein Domänenname muss ausgewählt werden, wenn Sie ein [Azure Traffic Manager-Profil][AzureTrafficManagerProfile] erstellen. Dieser Name wird mit dem Suffix *trafficmanager.net* kombiniert, um einen Domäneneintrag zu registrieren, der von Traffic Manager verwaltet wird. Für die Beispiel-App ist der ausgewählte Name *scalable-ase-demo*. Der vollständige Domänenname, der von Traffic Manager verwaltet wird, lautet also *scalable-ase-demo.trafficmanager.net*.
- **Strategie für die Skalierung der App:** Wird die Anwendung über mehrere App Service-Umgebungen in einer Region verteilt? Über mehrere Regionen? Eine Kombination beider Ansätze? Die Entscheidung sollte darauf basieren, woher der Kundendatenverkehr erwartet wird, aber auch darauf, wie gut der Rest der Back-End-Infrastruktur zur Unterstützung einer App skaliert werden kann. Bei einer zu 100 % statusfreien Anwendung kann eine App beispielsweise hochgradig skaliert werden, indem eine Kombination von mehreren App Service-Umgebungen pro Azure-Region verwendet und dies mit App Service-Umgebungen, die in mehreren Azure-Regionen bereitgestellt sind, multipliziert wird. Da Kunden aus mehr als 15 öffentlichen Azure-Regionen auswählen können, ist tatsächlich die Erstellung einer weltweiten, enorm skalierbaren Anwendung möglich. Für die Beispiel-App in diesem Artikel wurden drei App Service-Umgebungen in einer einzelnen Azure-Region (USA (Mitte/Süden)) erstellt.
- **Benennungskonvention für die App Service-Umgebungen:** Jede App Service-Umgebung erfordert einen eindeutigen Namen. Mit mehr als einer oder zwei App Service-Umgebungen ist es hilfreich, über eine Benennungskonvention zu verfügen, um die Identifizierung der einzelnen App Service-Umgebungen zu vereinfachen. Für die Beispiel-App wurde eine einfache Benennungskonvention verwendet. Die Namen der drei App Service-Umgebungen sind *fe1ase*, *fe2ase* und *fe3ase*.
- **Benennungskonvention für die Apps:** Da mehrere Instanzen der App bereitgestellt werden, ist ein Name für jede Instanz der bereitgestellten App erforderlich. Eine sehr praktische, aber wenig bekannte Funktion von App Service-Umgebungen ist, dass der gleiche App-Name in mehreren App Service-Umgebungen verwendet werden kann. Da jede App Service-Umgebung ein eindeutiges Domänensuffix aufweist, können Entwickler den gleichen App-Namen in jeder Umgebung verwenden. Ein Entwickler kann Apps beispielsweise wie folgt benennen: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net* usw... Bei der Beispiel-App hat jedoch jede Instanz der App auch einen eindeutigen Namen. Die verwendeten Namen für die App-Instanzen sind *webfrontend1*, *webfrontend2*, und *webfrontend3*.


## Einrichten des Traffic Manager-Profils ##
Wenn mehrere Instanzen einer App in mehreren App Service-Umgebungen bereitgestellt werden, können die einzelnen App-Instanzen bei Traffic Manager registriert werden. Für die Beispiel-App ist ein Traffic Manager-Profil für *scalable-ase-demo.trafficmanager.net* erforderlich, das die Kunden an die folgenden bereitgestellten App-Instanzen weiterleiten kann:

- **webfrontend1.fe1ase.p.azurewebsites.net:** Eine Instanz der Beispiel-App, die in der ersten App Service-Umgebung bereitgestellt ist.
- **webfrontend2.fe2ase.p.azurewebsites.net:** Eine Instanz der Beispiel-App, die in der zweiten App Service-Umgebung bereitgestellt ist.
- **webfrontend3.fe3ase.p.azurewebsites.net:** Eine Instanz der Beispiel-App, die in der dritten App Service-Umgebung bereitgestellt ist.

Die einfachste Möglichkeit, mehrere Azure App Service-Endpunkte zu registrieren, die alle in der **gleichen** Azure-Region ausgeführt werden, ist die Nutzung der [Azure-Ressourcen-Manager-Unterstützung (ARM) für Azure Traffic Manager][ARMTrafficManager] der Vorschau-PowerShell.

Der erste Schritt ist die Erstellung eines Azure Traffic Manager-Profils. Der folgende Code zeigt, wie das Profil für die Beispiel-App erstellt wurde:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Beachten Sie, dass der Parameter *RelativeDnsName* auf *scalable-ase-demo* festgelegt wurde. Auf diese Weise wird der Domänenname *scalable-ase-demo.trafficmanager.net* erstellt und einem Traffic Manager-Profil zugeordnet.

Der Parameter *TrafficRoutingMethod* definiert die Lastenausgleichsrichtlinie, die Traffic Manager verwendet, um zu bestimmen, wie Kundenlasten über alle verfügbaren Endpunkte verteilt werden. In diesem Beispiel wurde die Methode *Weighted* ausgewählt. Dies führt dazu, dass Kundenanforderungen basierend auf der relativen Gewichtung, die den Endpunkten zugeordnet ist, über alle registrierten Anwendungsendpunkte verteilt werden.

Wenn das Profil erstellt ist, werden alle App-Instanzen dem Profil als *externe Endpunkte* hinzugefügt. Der folgende Code zeigt die URLs für alle drei App-Instanzen, die dem Profil hinzugefügt werden.

    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend1.fe1ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend2.fe2ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend3.fe3ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Beachten Sie, dass ein Aufruf von *Add-AzureTrafficManagerEndpointConfig* für jede einzelne App-Instanz vorhanden ist. Die Parameter *Target* in den einzelnen PowerShell-Befehlen verweisen auf den vollqualifizierten Domänennamen (FQDN) der drei bereitgestellten App-Instanzen. Die unterschiedlichen FQDNs sind die Werte, die verwendet werden, um die DNS CNAME-Kette für *scalable-ase-demo.trafficmanager.net* zu durchlaufen, damit die Datenverkehrslast auf alle Endpunkte verteilt wird, die im Traffic Manager-Profil registriert sind.

Alle drei Endpunkte verwenden den gleichen Wert (10) für den Parameter *Weight*. Dadurch werden Kundenanforderungen von Traffic Manager relativ gleichmäßig auf alle drei App-Instanzen verteilt.

*Hinweis:* Da ARM-Unterstützung für Traffic Manager derzeit in der Vorschau ist, müssen Azure App Service-Endpunkte den Parameter *Type* auf *ExternalEndpoints* festlegen. In der Zukunft gibt es eine systemeigene Unterstützung der Azure App Service-Endpunkte als Endpunkttyp durch die ARM-Variante von Traffic Manager.

## Verweisen der benutzerdefinierten Domäne der App auf die Traffic Manager-Domäne ##
Der letzte erforderliche Schritt ist, die benutzerdefinierte Domäne der App auf die Traffic Manager-Domäne zu verweisen. Für die Beispiel-App bedeutet dies, dass *www.scalableasedemo.com* auf *scalable-ase-demo.trafficmanager.net* verwiesen wird. Dieser Schritt muss bei der Domänenregistrierungsstelle abgeschlossen werden, die die benutzerdefinierte Domäne verwaltet.

Mit den Domänenverwaltungstools Ihrer Registrierungsstelle muss ein CNAME-Eintrag erstellt werden, der die benutzerdefinierte Domäne an die Traffic Manager-Domäne verweist. Die folgende Abbildung zeigt ein Beispiel für eine CNAME-Konfiguration:

![CNAME für die benutzerdefinierte Domäne][CNAMEforCustomDomain]

Auch wenn es in diesem Thema nicht behandelt wird, denken Sie daran, dass für jede einzelne App-Instanz auch die benutzerdefinierte Domäne registriert werden muss. Andernfalls schlägt eine Anforderung fehl, wenn eine Anforderung eine App-Instanz erreicht und die Anwendung die benutzerdefinierte Domäne nicht bei der App registriert hat.

In diesem Beispiel ist die benutzerdefinierte Domäne *www.scalableasedemo.com*, und jeder Anwendungsinstanz wurde die benutzerdefinierte Domäne zugeordnet.

![Benutzerdefinierte Domäne][CustomDomain]

Eine Zusammenfassung der Registrierung einer benutzerdefinierten Domäne bei Azure App Service-Apps finden Sie im folgenden Artikel über das [Registrieren benutzerdefinierter Domänen][RegisterCustomDomain].

## Testen der verteilten Topologie ##
Das Endergebnis der Traffic Manager- und der DNS-Konfiguration ist, das Anforderungen für *www.scalableasedemo.com* in der folgenden Reihenfolge verarbeitet werden:

1. Ein Browser oder Gerät führt eine DNS-Suche für *www.scalableasedemo.com* aus.
2. Der CNAME-Eintrag bei der Domänenregistrierungsstelle bewirkt, dass die DNS-Suche an Azure Traffic Manager umgeleitet wird.
3. Eine DNS-Suche wird für *scalable-ase-demo.trafficmanager.net* über einen der Azure Traffic Manager-DNS-Server ausgeführt.
4. Basierend auf der Lastenausgleichsrichtlinie (dem Parameter *TrafficRoutingMethod*, der zuvor beim Erstellen des Traffic Manager-Profils verwendet wurde) wählt Traffic Manager einen der konfigurierten Endpunkte aus und gibt den FQDN des betreffenden Endpunkts an den Browser oder das Gerät zurück.
5.  Da der vollqualifizierte Domänenname des Endpunkts die URL einer App-Instanz ist, die in einer App Service-Umgebung ausgeführt wird, fordert der Browser oder das Gerät einen Microsoft Azure-DNS-Server auf, den FQDN in eine IP-Adresse aufzulösen. 
6. Der Browser oder das Gerät sendet die HTTP/S-Anforderung an die IP-Adresse.  
7. Die Anforderung geht bei einer App-Instanz ein, die in einer der App Service-Umgebungen ausgeführt wird.

Die Konsolenabbildung unten zeigt eine DNS-Suche nach der benutzerdefinierten Domäne der Beispiel-App mit einer erfolgreichen Auflösung in eine App-Instanz, die in einer der drei App Service-Umgebungen des Beispiels ausgeführt wird (in diesem Fall die zweite der drei App Service-Umgebungen):

![DNS-Suche][DNSLookup]

## Zusätzliche Links und Informationen ##
Dokumentation zur [Azure-Ressourcen-Manager-Unterstützung (ARM) für Azure Traffic Manager][ARMTrafficManager] der Vorschau-PowerShell.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]: https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]: https://azure.microsoft.com/de-DE/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]: ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png

<!---HONumber=Oct15_HO3-->