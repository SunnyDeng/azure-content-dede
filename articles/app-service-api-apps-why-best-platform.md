<properties 
	pageTitle="Was sind API-Apps?" 
	description="Erfahren Sie, warum Azure App Service die beste Plattform zum Entwickeln, Veröffentlichen und Hosten von RESTful-APIs ist." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tdykstra"/>

# Was sind API-Apps?

## Übersicht

Azure App Service ist eine vollständig verwaltete Computingplattform für professionelle Entwickler, die einen umfangreichen Satz von Funktionen für Web-, mobile und Integrationsszenarien bereitstellt. API-Apps sind Bestandteil des Azure App Service und bieten technischen Benutzern und Entwicklern die Möglichkeit, APIs und SaaS-Connectors auf einer modernen, funktionsreichen, skalierbaren und global verfügbaren Cloudplattform zu ermitteln, zu hosten, zu verwalten und gewinnbringend zu vermarkten.

## Warum API-Apps?

Mit API-Apps in Azure App Service können Sie APIs auf einfache Weise entwickeln, veröffentlichen, verwalten und gewinnbringend vermarkten.

- **Nutzen Sie eigene APIs in unveränderter Form** - Verwenden Sie ASP.NET, Java, PHP, Node.js oder Python für Ihre APIs. Ihre APIs können die Vorteile von Azure App Service nutzen, ohne geändert werden zu müssen.

- **Verbinden von Apps mit gängigen SaaS-Plattformen** - Azure App Service macht es einfach, eine Verbindung mit gängigen SaaS-Plattformen herzustellen, einschließlich Salesforce, Office 365, Twitter, Facebook, Dropbox und vielen weiteren.

- **Einfache Zugriffssteuerung** - APIs können nur für andere Apps in Azure App Service oder für die Öffentlichkeit offengelegt werden, und Sie können eine Authentifizierung über Azure Active Directory oder Drittanbieterdienste hinzufügen, ohne Ihren Code ändern zu müssen.

- **Öffentliche und organisationseigene API-Galerie** - Geben Sie APIs durch eine eigene, private Organisationsgalerie mit APIs auf einfache Weise für andere Teams in Ihrer Organisation frei. APIs können auch öffentlich freigegeben werden, um Drittanbieterentwicklern eine Nutzung zu ermöglichen.

- **Automatisches Generieren von SDKs** - Azure App Service kann automatisch SDKs für verschiedene Sprachen erstellen, darunter C#, Java und Javascript, damit Ihre APIs auf einer Vielzahl von Plattformen zur Verfügung stehen.

- **Nutzen der besten IDE auf dem Markt** - Die Integration in [Visual
Studio](/campaigns/visual-studio-2013/) vereinfacht das Erstellen, Debuggen, Paketieren und Veröffentlichen von APIs und ermöglicht eine vollständige Lebenszyklusverwaltung für die App.

<!--removing per directive to cut back on size of this section
- **Keine Wartung** - Führen Sie Ihre Apps in einer hochverfügbaren Umgebung mit automatischem Patching aus. Mit Azure App Service bereitgestellte API-Apps sind isoliert und werden auf virtuellen Computern gehostet, die dediziert für Ihre Anwendungen eingesetzt werden. So ist die Leistung vorhersagbar und die Sicherheit aufgrund der Isolation immer gegeben.

- **Automatische Skalierung** - App Service ermöglicht Ihnen eine schnelle vertikale oder horizontale Skalierung, um beliebige eingehende Datenlasten zu verarbeiten. Wählen Sie Anzahl und Größe der virtuellen Computer manuell aus, oder richten Sie die
[automatische Skalierung](/documentation/videos/auto-scaling-azure-web-sites/)
ein, um Ihre Server auf Grundlage von Datenlast oder nach einem Zeitplan zu skalieren.
-->

- **Zugriff auf lokale Daten** - API-Apps können über [Hybridverbindungen](integration-hybrid-connection-overview.md) und [VNET](web-sites-integrate-with-vnet.md) Daten aus Ihren eigenen Datencentern nutzen.

- **Reibungslose Bereitstellung** - Richten Sie die fortlaufende Integration und Bereitstellungsworkflows mit Visual Studio Online, GitHub, TeamCity, Hudson oder BitBucket ein. Dadurch können Sie bei jedem erfolgreichen Einchecken von Code oder Integrationstest Ihre API-App erstellen, testen und bereitstellen.

## API-Apps - Konzepte ##

- **API-Apps-Galerie** - Wählen Sie aus einer ständig größer werdenden Auswahl an vorhandenen API-App-Vorlagen.
- **Connectors** - Eine Art von API-App, welche die Verbindung mit SaaS-Plattformen wie z. B. Salesforce und Office 365 vereinfacht.
- **Gateway** - Eine Web-App, die API-Verwaltungsfunktionen wie z. B. die Authentifizierung für alle API-Apps in einer Ressourcengruppe übernimmt. 
- **Automatische Skalierung** - API-Apps können automatisch vertikal oder horizontal skaliert werden, um eingehende Datenlasten von Kunden zu verarbeiten. Wählen Sie Anzahl und Größe der virtuellen Computer manuell aus, oder richten Sie die automatische Skalierung ein, um Ihre Server basierend auf Last oder Zeitplan zu skalieren.
- **Fortlaufende Integration** - Richten Sie die fortlaufende Integration und Bereitstellungsworkflows mit Visual Studio Online, GitHub, TeamCity, Hudson oder BitBucket ein. Dadurch können Sie bei jedem erfolgreichen Einchecken von Code oder Integrationstest Ihre Webanwendung erstellen, testen und bereitstellen.

## Erste Schritte

Befolgen Sie zum Einstieg in API-Apps das Lernprogramm [Erstellen einer API-App ](app-service-dotnet-create-api-app.md).

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](app-service-value-prop-what-is.md).

<!--HONumber=49-->