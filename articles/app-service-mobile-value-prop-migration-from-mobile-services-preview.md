<properties
	pageTitle="Ich verwende Mobile Services - welche Vorteile bietet App Service?"
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
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>Ich verwende Mobile Services - welche Vorteile bietet App Service?

##Übersicht
Ihre vorhandenen Mobile Service-Projekte sind sicher und werden weiterhin unterstützt. Die *Azure App Service*-Plattform bietet jedoch zahlreiche Vorteile für Ihre mobilen Apps, die mit Mobile Services aktuell nicht genutzt werden können:  

- Einfachere und kostengünstigere Angebote für Apps, die sowohl Webclients als auch mobile Clients einschließen
- Neue Hostingfunktionen, darunter Webaufträge, benutzerdefinierte CName-Werte, bessere Überwachung
- Sofortige Integration in Office 365, Dynamics CRM, Salesforce und viele weitere wichtige Saas-APIs
- Unterstützung für Java- und PHP-Back-End-Code (zusätzlich zu Node.js und .NET) 
- Sofortige Integration in Traffic Manager
- Konnektivität mit Ihren lokalen Ressourcen und VPNs über VNet (zusätzlich zu Hybridverbindungen)
- Überwachung und Problembehandlung für Ihre App mit NewRelic oder AppInsights sowie Warnungen
- Umfangreichere Möglichkeiten bei den zugrunde liegenden Computingressourcen, z. B. VM-Größen 
- Integrierte automatische Skalierung, Lastenausgleich und Leistungsüberwachung
- Integrierte Funktionen für Staging, Sicherung, Rollback und Testen 

## Neue Hostingfunktionen
In *Azure App Service* wird der *Mobile App*-Back-End-Code im selben Container ausgeführt wie Web-App und API-App. Auf diese Weise können Sie die Vorteile aller Features in diesem Container nutzen, darunter einige, die derzeit in Mobile Services nicht zur Verfügung stehen: 

- Kontinuierliche Ausführung von Back-End-Logik über Webaufträge 
- Sicherstellen, dass Ihr Back-End-Code immer ausgeführt wird
- Verwenden von benutzerdefinierten CName-Werten zur Bereitstellung benutzerfreundlicher und dauerhafter Namen für die Endpunkte Ihres mobilen Back-Ends
- Geoskalierung Ihrer App mit Traffic Manager


##Verbinden Ihrer *Mobile App* mit einer SaaS-API
*Azure App Service* macht es einfacher, Ihre mobile App mit SaaS-APIs zu verbinden, z. B. mit Office 365, Dynamics, Salesforce, SAP und vielen weiteren. *Azure App Service* stellt eine sofort einsatzfähige Authentifizierung im Namen des Benutzers bereit und bietet für einzelne SaaS-APIs echte SSO-Funktionalität (Single Sign-On, einmaliges Anmelden) über Ihre primäre Identität.

##Zugriff auf lokale Daten mit VNet
Mit Mobile Services können Sie bereits Hybridverbindungen für den Zugriff auf lokale Ressourcen nutzen. Es gibt jedoch Situationen, in denen eine VPN-Lösung besser geeignet ist. Mit *Azure App Service* können Sie Azure VNet für Ihren Mobile App-Back-End-Code verwenden.

##Verwenden Sie Ihre bevorzugte Back-End-Sprache
*Azure App Service* bietet umfangreichere Unterstützung für Entwicklerplattformen, darunter Java, PHP und Python - zusätzlich zu .NET und Node.js, die bereits in Mobile Services unterstützt wurden.

##Einrichten einer automatischen Skalierung
Mit Mobile Services wurden alle Instanzen Ihres Back-End-Codes in kleinen VMs ausgeführt. *Azure App Service* ermöglicht es Ihnen, die Größe der VMs über eine umfangreichere Auswahl an Optionen festzulegen. Sie können darüber hinaus basierend auf verschiedenen Leistungsmetriken eine schnelle vertikale oder horizontale Skalierung durchführen, um beliebige eingehende Datenlasten zu verarbeiten. 

##Jederzeit alles im Blick
Dank einer verbesserten Überwachung und durch Warnungen, die Sie und Ihr Team automatisch benachrichtigen, können Sie in Echtzeit auf mögliche Probleme reagieren. Integrieren Sie eine erweiterte App-Analyse und Überwachungsfunktionen aus New Relic und AppInsights, um noch besseren Einblick in die Leistung Ihrer mobilen App zu erhalten. Mit *Azure App Service* können Sie jetzt programmatisch und über das Portal Warnungen einrichten, die auf einer Vielzahl von Leistungsmetriken basieren.

##Schützen Sie Ihre Ressourcen
Sichern Sie Back-End und Datenbank automatisch. Ihr Code und Ihre Daten sind vor Katastrophen geschützt und können auf einfache Weise wiederhergestellt werden. So können Sie sich ganz auf Ihre geschäftlichen Aufgaben konzentrieren.

##Auf die Plätze, Staging, Los!
Mit *Azure App Service* können Sie jetzt verschiedene private Test- und Stagingumgebungen für Ihre mobilen Apps erstellen. Verwenden Sie diese für Tests vor der Bereitstellung. Wechseln Sie ohne Ausfallzeiten in die Produktionsumgebung. Web Apps werden vorab geladen, um ein bestmögliches Kundenerlebnis sicherzustellen.



Bei allgemeiner Verfügbarkeit der *Azure App Service Mobile App*-Funktion wird eine nahtlose Migration vorhandener Mobile Services-Projekte auf App Service ermöglicht (sofern Sie sich für eine Migration entscheiden). In der Zwischenzeit können Sie sich mit *App Service* vertraut machen und die Vorteile von *App Service* für Ihre vorhandenen Mobile Service-Projekte nutzen. [tutorial](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md).



<!--HONumber=49-->