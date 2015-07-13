<properties 
	pageTitle="Was ist Application Insights?" 
	description="Verfolgen Sie die Nutzung und Leistung Ihrer Live-Web- oder Geräteanwendung. Erkennen, Selektieren und Diagnostizieren Sie Probleme. Überwachen und verbessern Sie fortlaufend die Beliebtheit bei den Benutzern." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# Was ist Application Insights?

Mit Visual Studio Application Insights können Sie die Leistung und die Nutzung Ihrer Live-Web- oder Geräteanwendung nachverfolgen.

* [Erkennen, Selektieren und Diagnostizieren][detect] Sie Leistungsprobleme, und korrigieren Sie sie, bevor die meisten Benutzer sie bemerken.
 *  Warnungen bei Leistungsänderungen oder Abstürzen.
 *  Metriken zur Diagnose von Leistungsproblemen, z. B. Reaktionszeiten, CPU-Auslastung, Nachverfolgung von Abhängigkeiten.
 *  Verfügbarkeitstests für Web-Apps.
 *  Berichte und Warnungen zu Abstürzen und Ausnahmen.
 *  Leistungsstarke Suche für Diagnoseprotokolle (einschließlich Protokollablaufverfolgungen aus Ihren bevorzugten Protokollierungsframeworks).
* [Verbessern Sie Ihre Anwendung kontinuierlich][knowUsers], indem Sie verstehen, wie sie von den Benutzern eingesetzt wird. 
 * Zähler für Seitenaufrufe, neue und wiederkehrende Benutzer und andere zentrale Nutzungsanalysen.
 * Verfolgen Sie Ihre eigenen Ereignisse, um Nutzungsmuster und den Erfolg der einzelnen Features auszuwerten.

## Wie funktioniert Application Insights?

Sie installieren ein kleines SDK in Ihrer Anwendung und richten ein Konto im Application Insights-Portal ein. Das SDK überwacht Ihre App und sendet Telemetriedaten an das Portal. Das Portal zeigt Ihnen statistische Diagramme und bietet leistungsfähige Suchtools, mit denen Sie Probleme diagnostizieren können.

![Das Application Insights-SDK in Ihrer App sendet Telemetriedaten an Ihre Application Insights-Ressource im Azure-Portal.](./media/app-insights-overview/01-scheme.png)

Das SDK umfasst mehrere Module, die Telemetriedaten sammeln, um z. B. Benutzer und Sitzungen zu zählen und Leistungsdaten zu erfassen. Sie können auch Ihren eigenen benutzerdefinierten Code zum Senden von Telemetriedaten an das Portal schreiben. Benutzerdefinierte Telemetrie ist besonders nützlich für die Ablaufverfolgung von User Storys: Sie können Ereignisse zählen wie z. B. Schaltflächenklicks, das Erreichen bestimmter Ziele oder Benutzerfehler.

Für ASP.NET-Server und Azure Web-Apps können Sie auch [Statusmonitor][redfield] installieren, der zwei Verwendungszwecke erfüllt. Sie können damit folgende Aktionen durchführen:

* Überwachen einer Web-App, ohne sie neu zu erstellen oder neu zu installieren.
* Verfolgen von Aufrufen abhängiger Module.

## Mit welchen Arten von Apps kann er eingesetzt werden?

Derzeit gibt es SDKs für:

* Web-Apps
 * [ASP.NET][greenbrown] auf Azure oder dem IIS-Server
 * [Java][java] auf JRE 
 * [Webseiten][client]: HTML und JavaScript
* Geräte-Apps
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [Andere Plattformen][platforms]


## Wozu sollte ich ihn verwenden?

* [Erkennen, Selektieren und Diagnose von Problemen][detect]
* [Analysieren der Nutzung Ihrer App][knowUsers]


## Was brauche ich, um ihn einzusetzen?

* Ein Abonnement für Microsoft Azure. Application Insights ist einer der vielen Clouddienste von Azure, darunter auch Websites, Datenbanken, virtuelle Computer und vieles mehr. (Sie können mit Application Insights jedoch jede Anwendung überwachen – Ihre App muss nicht in Azure ausgeführt werden.) 

 * Ihre Organisation verfügt möglicherweise über ein Konto.


## Wie fange ich an?

Wählen Sie Ihre Plattform im Menü "Erste Schritte" auf der linken Seite.

In allen Fällen gilt das folgende grundlegende Verfahren:

1. Erstellen Sie eine Application Insights-Ressource in [Azure][portal] (und rufen Sie deren Instrumentationsschlüssel ab).
2. Instrumentieren Sie Ihre Anwendung mit dem entsprechenden SDK (und konfigurieren Sie sie mit dem Instrumentationsschlüssel).
3. Führen Sie die Anwendung entweder im Debugmodus oder live aus.
4. Sehen Sie sich die Ergebnisse in der Ressource in [Azure][portal] an.

In einigen Fällen steht für die IDE (z. B. für Visual Studio oder Eclipse) ein Plug-In zur Verfügung, das die ersten beiden Schritte für Sie ausführt. Sie können das Verfahren jedoch immer manuell durchführen.

Wenn es sich bei Ihrer App um eine Website oder einen Dienst handelt, gibt es für das grundlegende Verfahren einige optionale Erweiterungen und Variationen:

* Fügen Sie sowohl der serverseitigen Anwendung als auch dem [Clientgerät][windows] oder der [Webseite][client] ein SDK hinzu. Die Telemetriedaten werden im Portal zusammengeführt, damit Sie Ereignisse auf beiden Seiten zueinander in Beziehung setzen können.
* Richten Sie Webtests zur Überwachung der Verfügbarkeit Ihrer Website an Orten auf der ganzen Welt ein.
* Instrumentieren Sie eine bereits live geschaltete serverseitige Anwendung, ohne sie neu zu erstellen oder erneut bereitzustellen. Diese Option steht für [IIS-Server][redfield] und [Azure Web-Apps][azure] zur Verfügung.
* Überwachen Sie die Abhängigkeitsaufrufe, die Ihre App an andere Komponenten wie z. B. Datenbanken oder über REST-APIs vornimmt. Diese Option steht für [IIS-Server][redfield] und [Azure Web-Apps][azure] zur Verfügung.


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->