<properties 
	pageTitle="Was ist Application Insights?" 
	description="Verfolgen Sie die Nutzung und Leistung Ihrer Live-Web- oder Geräteanwendung. Erkennen, Selektieren und Diagnostizieren Sie Probleme. Überwachen und verbessern Sie fortlaufend die Beliebtheit bei den Benutzern." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# Was ist Application Insights?

Application Insights ist ein erweiterbarer Analysedienst, der Ihnen dabei hilft, die Leistung und die Verwendung der Liveanwendung zu verstehen. Der Dienst ist für Entwickler konzipiert und dient dazu, die Leistung und Benutzerfreundlichkeit Ihrer App kontinuierlich zu verbessern.

Er funktioniert mit Web-Apps und eigenständigen Apps auf einer Vielzahl von Plattformen: .NET oder J2EE, lokal gehostet oder in der Cloud, Geräteapps für Windows, iOS, Android, OS X und andere Plattformen.

Er ist für das Entwicklungsteam vorgesehen. Damit können Sie folgende Aktionen ausführen:

* [Analysieren von Verwendungsmustern][knowUsers], um Ihre Benutzer besser zu verstehen und Ihre App kontinuierlich zu verbessern. 
 * Zähler für Seitenansichten, neue und wiederkehrende Benutzer, Geolocation, Plattformen und andere zentrale Nutzungsanalysen.
 * Ablaufverfolgung für Nutzungspfade, um den Erfolg der einzelnen Features zu bewerten.
* [Erkennen, Selektieren und Diagnostizieren][detect] Sie Leistungsprobleme, und korrigieren Sie sie, bevor die meisten Benutzer sie bemerken.
 *  Warnungen bei Leistungsänderungen oder Abstürzen.
 *  Metriken zur Diagnose von Leistungsproblemen, z. B. Reaktionszeiten, CPU-Auslastung, Nachverfolgung von Abhängigkeiten.
 *  Verfügbarkeitstests für Web-Apps.
 *  Berichte und Warnungen zu Abstürzen und Ausnahmen.
 *  Leistungsstarke Suche für Diagnoseprotokolle (einschließlich Protokollablaufverfolgungen aus Ihren bevorzugten Protokollierungsframeworks).

Das SDK für jede Plattform umfasst eine Reihe von Modulen, die die Anwendung ohne Konfigurationsaufwand überwachen. Darüber hinaus können Sie Ihre eigene Telemetrie für detailliertere und besser abgestimmte Analysen codieren.

Von der Anwendung gesammelte Telemetriedaten werden im Azure-Portal gespeichert und analysiert, wo intuitive Ansichten und leistungsstarke Tools für eine schnelle Diagnose und Analyse zur Verfügung stehen.

![Erstellen Sie ein Diagramm mit der Statistik zur Benutzeraktivität, oder führen Sie eine Detailsuche bei bestimmten Ereignissen aus.](./media/app-insights-overview/00-sample.png)


## Plattformen und Sprachen

Es gibt SDKs für eine wachsende Palette an Plattformen. Derzeit enthält die Liste:

 * [ASP.NET-Server][greenbrown] auf Azure oder dem IIS-Server
 * [Azure Cloud Services](app-insights-cloudservices.md)
 * [J2EE-Server][java]
 * [Webseiten][client]: HTML und JavaScript
 * [Windows Phone, Windows Store, universelle Windows-10-Apps und direkte Integration in das Windows-10-Entwicklerportal][windows]
 * [Windows Desktop][desktop]
 * [iOS][ios]
 * [Android][android]
 * [Weitere Plattformen][platforms] – Node.js, PHP, Python, Ruby, Joomla, SharePoint, WordPress

Application Insights kann Telemetriedaten auch aus vorhandenen ASP.NET-Web-Apps in IIS abrufen, ohne diese neu zu erstellen.

Wenn Ihre App Clients, Server und andere Komponenten aufweist, können Sie sie alle instrumentieren. Die Daten werden in das Application Insights-Portal integriert, sodass Sie beispielsweise Ereignisse auf dem Client mit Ereignissen auf dem Server vergleichen können.


## So funktioniert's

Sie installieren ein kleines SDK in Ihrer Anwendung und richten ein Konto im Application Insights-Portal ein. Das SDK überwacht Ihre App und sendet Telemetriedaten an das Portal. Das Portal zeigt Ihnen statistische Diagramme und bietet leistungsfähige Suchtools, mit denen Sie Probleme diagnostizieren können.

![Das Application Insights-SDK in Ihrer App sendet Telemetriedaten an Ihre Application Insights-Ressource im Azure-Portal.](./media/app-insights-overview/01-scheme.png)

Das SDK umfasst mehrere Module, die Telemetriedaten sammeln, um z. B. Benutzer und Sitzungen zu zählen und Leistungsdaten zu erfassen. Sie können auch Ihren eigenen benutzerdefinierten Code zum Senden von Telemetriedaten an das Portal schreiben. Benutzerdefinierte Telemetrie ist besonders nützlich für die Ablaufverfolgung von User Storys: Sie können Ereignisse zählen wie z. B. Schaltflächenklicks, das Erreichen bestimmter Ziele oder Benutzerfehler.

Für ASP.NET-Server und Azure Web-Apps können Sie auch [Statusmonitor][redfield] installieren, der zwei Verwendungszwecke erfüllt. Sie können damit folgende Aktionen durchführen:

* Überwachen einer Web-App, ohne sie neu zu erstellen oder neu zu installieren.
* Verfolgen von Aufrufen abhängiger Module.

### Wie sieht der Aufwand aus?

Die Auswirkungen auf die Leistung sind sehr gering. Aufrufe werden nicht blockierend nachverfolgt, zusammengefasst und in einem separaten Thread gesendet.


## Erste Schritte

1. Sie benötigen ein [Microsoft Azure](http://azure.com)-Abonnement. Die Registrierung ist kostenlos, und Sie können den kostenlosen [Tarif](https://azure.microsoft.com/pricing/details/application-insights/)von Application Insights auswählen.

2. Melden Sie sich am [Azure-Vorschauportal](http://portal.azure.com) an.
3. Erstellen Sie eine Application Insights-Ressource. Hier werden die Daten aus Ihrer App angezeigt.

    ![Hinzufügen, Entwicklerdienste, Application Insights.](./media/app-insights-overview/11-new.png)

    Wählen Sie Ihren Anwendungstyp aus.

4. Öffnen Sie die neue Ressource, und öffnen Sie die Kurzanleitung.
    
    ![Durchsuchen,](./media/app-insights-overview/quickstart.png)

    Sie erklärt, wie das SDK in Ihrer App installiert wird. Wenn es sich um eine Web-App handelt, finden Sie auch heraus, wie das SDK Webseiten hinzugefügt wird und Verfügbarkeitstests eingerichtet werden.


Wählen Sie für weitere Details Ihren App-Typ unter "Erste Schritte" in der Navigationsleiste links auf der Seite aus.


## Support und Feedback

* Fragen und Probleme:
 * [Problembehandlung][qna]
 * [MSDN-Forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Fehler:
 * [Kontakt](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* Vorschläge:
 * [Aussagen von Benutzern](http://visualstudio.uservoice.com/forums/121579-visual-studio/category/77108-application-insights)


## Videos


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=July15_HO3-->