<properties 
	pageTitle="Einführung in die App Service-Umgebung" 
	description="Lernen Sie die App Service-Umgebung kennen, die sichere, mit dem VNet verknüpfte, dedizierte Skalierungseinheiten für die Ausführung all Ihrer Apps bietet." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="stefsch"/>

# Einführung in die App Service-Umgebung

## Übersicht ##
Eine App Service-Umgebung ist eine Option des [Premium][PremiumTier]-Tarifs von Azure App Service, die eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung all Ihrer Apps bereitstellt. Dazu gehören [Web-Apps][WebApps], [mobile Apps][MobileApps], [API-Apps][APIApps] und [Logik-Apps][LogicApps] mit erweiterten Skalierungsoptionen.

Computeressourcen für eine App Service-Umgebung werden ausschließlich zur Ausführung Ihrer Apps eingesetzt. Eine App Service-Umgebung wird immer in einem regionalen, klassischen virtuellen „v1“-Netzwerk erstellt. Dadurch ergeben sich für Ihre Apps neue Optionen für die Netzwerkisolation. Eine App Service-Umgebung unterstützt außerdem zusätzliche Skalierungsoptionen mit bis zu fünfzig (50) Computeressourcen, die zur Ausführung Ihrer Apps zur Verfügung stehen. Außerhalb einer App Service-Umgebung gilt ein Limit von 20 Computeressourcen für das Hosten Ihrer Apps.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Unterstützung für virtuelle Netzwerke ##
Eine App Service-Umgebung kann entweder in einem bereits vorhandenen oder in einem neuen regionalen, klassischen virtuellen „v1“-Netzwerk erstellt werden. ([Hier finden Sie weitere Informationen zu virtuellen Netzwerken.][MoreInfoOnVirtualNetworks]) Da eine App Service-Umgebung sich immer in einem regionalen virtuellen Netzwerk, genauer gesagt in einem Subnetz eines regionalen virtuellen Netzwerks befindet, können Sie die Sicherheitsfunktionen virtueller Netzwerke zum Steuern sowohl der eingehenden als auch der ausgehenden Netzwerkkommunikation nutzen.

**Hinweis:** Eine App Service-Umgebung kann nicht in einem „v2“ virtuellen Netzwerk erstellt werden.

Mithilfe von [Netzwerksicherheitsgruppen][NetworkSecurityGroups] können Sie die eingehende Netzwerkkommunikation mit dem Subnetz einschränken, das eine App Service-Umgebung enthält. Dadurch können Sie Apps hinter Upstreamgeräten und -diensten ausführen wie z. B. Web Application Firewalls und Netzwerk-SaaS-Anbietern.

Apps müssen häufig auch auf Unternehmensressourcen wie interne Datenbanken und Webdienste zugreifen. Ein gängiger Ansatz besteht darin, diese Endpunkte nur für internen Netzwerkdatenverkehr verfügbar zu machen, der innerhalb eines virtuellen Azure-Netzwerks fließt. Sobald eine App Service-Umgebung demselben virtuellen Netzwerk angeschlossen wird wie die internen Dienste, können die in der Umgebung ausgeführten Apps auf diese zugreifen. Dies gilt auch für Endpunkte, die über [Site-to-Site-][SiteToSite] und [Azure ExpressRoute][ExpressRoute]-Verbindungen erreichbar sind.

## Dedizierte Computeressourcen ##
Alle Computeressourcen in einer App Service-Umgebung sind ausschließlich für ein einzelnes Abonnement vorgesehen. Eine App Service-Umgebung besteht aus einem einzelnen Front-End-Computeressourcenpool sowie ein bis drei Worker-Computeressourcenpools.

Der Front-End-Pool enthält Computeressourcen, die für die SSL-Beendigung sowie für den automatischen Lastenausgleich von App-Anforderungen in einer App Service-Umgebung zuständig sind.

Jeder Workerpool enthält Computeressourcen, die [App Service-Plänen][AppServicePlan] zugeordnet sind, die wiederum eine oder mehrere Azure App Service-Apps enthalten. Da in einer App Service-Umgebung bis zu drei verschiedenen Workerpools vorhanden sein können, können Sie flexibel verschiedene Computeressourcen für jeden Workerpool auswählen.

Beispielsweise können Sie einen Workerpool mit weniger leistungsfähigen Computeressourcen für App Service-Pläne erstellen, die für Entwicklungs- oder Test-Apps vorgesehen sind. Ein zweiter (oder sogar dritter) Workerpool kann leistungsfähigere Computeressourcen für App Service-Pläne nutzen, die für Produktions-Apps ausgeführt werden.

Eine App Service-Umgebung kann mit bis zu fünfzig (50) Computeressourcen in einem einzelnen Workerpool konfiguriert werden. Ausführliche Informationen über die Menge von Computeressourcen, die den Front-End- und Workerpools zur Verfügung stehen, finden Sie unter [Konfigurieren einer App Service-Umgebung][HowToConfigureanAppServiceEnvironment].

Weitere Informationen zu den verfügbaren Computeressourcengrößen, die in einer App Service-Umgebung unterstützt werden, finden Sie auf der Seite [App Service-Preise][AppServicePricing]. Sehen Sie sich die verfügbaren Optionen für App Service-Umgebungen im Premium-Tarif an.


## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung][HowToCreateAnAppServiceEnvironment]

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

Eine Übersicht über die Netzwerkarchitektur der App Service-Umgebung finden Sie im Artikel [Übersicht über die Netzwerkarchitektur][NetworkArchitectureOverview].

Informationen zur Verwendung einer App Service-Umgebung mit ExpressRoute finden Sie im folgenden Artikel zu [ExpressRoute und App Service-Umgebungen][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=Sept15_HO3-->