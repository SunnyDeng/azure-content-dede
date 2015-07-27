<properties
	pageTitle="Ich verwende Mobile Services – welche Vorteile bietet App Service?"
	description="Erfahren Sie, welche Vorteile App Service für Ihre vorhandenen Mobile Services-Projekte bietet."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>Ich verwende Mobile Services. Welche Vorteile bietet App Service?

##Übersicht
Ihr vorhandener Mobile Service ist sicher und wird weiterhin unterstützt. Die *Azure App Service*-Plattform bietet für Ihre mobile App jedoch eine Reihe von Vorteilen, die mit Mobile Services derzeit nicht verfügbar sind:

- Einfacheres und kostengünstigeres Angebot für Apps, die sowohl Web- als auch mobile Clients enthalten
- Neue Hostfunktionen, einschließlich Webaufträgen, benutzerdefinierter CNames, besserer Überwachung
- Gebrauchsfertige Integration in Office 365, Dynamics CRM, Salesforce und andere wichtige SaaS-APIs
- Unterstützung für Java- und PHP-Back-End-Code zusätzlich zu Node.js und .NET
- Gebrauchsfertige Integration in Traffic Manager
- Konnektivität mit lokalen Ressourcen und VPNs mit VNet zusätzlich zu Hybridverbindungen
- Überwachung und Problembehandlung für Ihre App mit NewRelic oder AppInsights sowie Warnungen
- Umfangreicheres Spektrum an zugrunde liegenden Serverressourcen, z. B. VM-Größen
- Integrierte automatische Skalierung, Lastenausgleich und Leistungsüberwachung
- Integrierte Funktionen für Staging, Sicherung, Rollback und Produktionstests

## Neue Hostingfunktionen
In *Azure App Service* wird der *Mobile App*-Back-End-Code in demselben Container ausgeführt wie Web-App und API-App. So können Sie alle Funktionen in diesem Container nutzen, einschließlich einiger, die derzeit nicht in Mobile Services vorhanden sind:

- Hinzufügen ständig ausgeführter Back-End-Logik über Webaufträge
- Sicherstellen der ständigen Ausführen des Back-End-Codes
- Verwenden benutzerdefinierter CNames zum Bereitstellen benutzerfreundlicher und stabiler Namen für Ihre mobilen Back-End-Endpunkte
- Geoskalierung Ihrer App mit Traffic Manager


##Verbinden Ihrer *Mobile App* mit SaaS-API
*Azure App Service* erleichtert das Verbinden Ihrer Mobile App mit SaaS-APIs wie Office 365, Dynamics, Salesforce, SAP und vielen mehr. *Azure App Service* bietet eine sofort einsetzbare Authentifizierung im Namen des Benutzers und ermöglicht Ihnen eine einmalige Anmeldung über alle SaaS-APIs hinweg, indem Token für einzelne SaaS-APIs Ihrer primären Identität zugeordnet werden.

##Zugriff auf lokale Daten über VNet
Mit Mobile Services können Sie schon heute Hybridverbindungen für den Zugriff auf lokale Ressourcen verwenden. Es gibt jedoch Situationen, in denen eine VPN-Lösung bevorzugt wird. Mit *Azure App Service* können Sie Azure-VNet für Mobile App-Back-End-Code verwenden.

##Verwenden Ihrer bevorzugten Back-End-Sprache
*Azure App Service* bietet eine breitere und umfassendere Unterstützung für Entwicklerplattformen, einschließlich Java, PHP und Python zusätzlich zu .NET und Node.js, die in Mobile Services unterstützt werden.

##Einrichten der automatischen Skalierung
Mit Mobile Services wurden alle Instanzen des Back-End-Codes auf kleinen virtuellen Computern ausgeführt. Mit *Azure App Service* können Sie die Größe der virtuellen Computer aus einem umfassenderen Satz von Optionen auswählen. Sie können zudem schnell zentral oder horizontal hochskalieren, um die gesamte von den Kunden eingehende Workload basierend auf verschiedenen Leistungsmetriken zu bewältigen.

##Auf dem Laufenden bleiben
Reagieren Sie auf Probleme in Echtzeit mithilfe von Überwachung und Warnungen, durch die Sie und Ihr Team automatisch benachrichtigt werden. Integrieren Sie die erweiterten App-Analyse- und Überwachungsfunktionen von New Relic und AppInsights, und erhalten Sie noch umfangreichere Informationen zur Leistung Ihrer mobilen App. Mit *Azure App Service* können Sie jetzt programmgesteuert und über das Portal Warnungen basiert auf verschiedenen Leistungsmetriken einrichten.

##Schützen Ihrer Ressourcen
Sichern Sie automatisch Back-End und Datenbank. Ihr Code und Ihre Daten werden in Notfällen geschützt und können problemlos wiederhergestellt werden, sodass Sie sich keine Sorgen um Ihr Unternehmen machen müssen.

##Auf die Plätze, Staging, los!
Mit *Azure App Service* können Sie jetzt mehrere private Test- und Stagingumgebungen für Ihre mobilen Apps erstellen. Verwenden Sie sie zum Durchführen von Tests vor der Bereitstellung. Wechseln Sie ohne Ausfallzeiten in die Produktion. Web-Apps werden vorab geladen, um höchste Kundenzufriedenheit zu gewährleisten.



Sobald die Funktion *Mobile Azure App Service-App* allgemein verfügbar wird, stellen wir eine nahtlose Migrationsmöglichkeit für Ihre vorhandenen Mobile Services auf App Service bereit, falls Sie die Migration durchführen möchten. In der Zwischenzeit können Sie sich mithilfe dieses [Lernprogramms](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md) mit *App Service* vertraut machen und die Vorteile von *App Service* für Ihre vorhandenen Mobile Service-Projekte nutzen.
 

<!---HONumber=July15_HO3-->