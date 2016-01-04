<properties 
	pageTitle="Application Insights für ASP.NET 5" 
	description="Überwachen Sie Webanwendungen auf Verfügbarkeit, Leistung und Auslastung." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Application Insights für ASP.NET 5

Mit Visual Studio Application Insights können Sie Ihre Webanwendung auf Verfügbarkeit, Leistung und Nutzung überwachen. Mit dem Feedback zur Leistung und Effektivität der App in der Praxis können Sie in jedem Entwicklungslebenszyklus eine fundierte Entscheidung für die Richtung des Entwurfs treffen.

![Beispiel](./media/app-insights-asp-net-five/sample.png)

Sie benötigen ein Abonnement für [Microsoft Azure](http://azure.com). Melden Sie sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.


## Erste Schritte

Wenn Sie das Projekt in Visual Studio 2015 erstellt haben, sollten Sie bereits über Application Insights verfügen. Befolgen Sie andernfalls die Anweisungen unter [Getting Started guide](https://github.com/Microsoft/ApplicationInsights-aspnet5/wiki/Getting-Started) (in englischer Sprache).

## Verwenden von Application Insights

Melden Sie sich beim [Microsoft Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu der Ressource, die Sie zum Überwachen Ihrer App erstellt haben.

Nutzen Sie die App eine Zeit lang in einem separaten Browserfenster. Nach und nach werden Daten in den Application Insights-Diagrammen angezeigt. (Möglicherweise müssen Sie auf „Aktualisieren“ klicken.) Während der Entwicklung werden nur wenige Daten angezeigt. Wenn Sie die App veröffentlichen und sie von vielen Benutzern verwendet wird, werden diese Diagramme jedoch mit zahlreichen Daten gefüllt.

Auf der Übersichtsseite werden die Leistungsdiagramme angezeigt, für die Sie sich wahrscheinlich am meisten interessieren: Serverantwortzeiten, Seitenladezeiten und Anzahl der fehlgeschlagenen Anforderungen. Klicken Sie auf ein beliebiges Diagramm, um weitere Diagramme und Daten anzuzeigen.

Die Ansichten im Portal lassen sich in zwei Hauptkategorien unterteilen:

* Im [Metrik-Explorer](app-insights-metrics-explorer.md) werden Diagramme und Graphen von Metriken und Zahlen angezeigt, z. B. Antwortzeiten, Fehlerraten oder Metriken, die Sie selbst über die [API](app-insights-api-custom-events-metrics.md) erstellt haben. Filtern und segmentieren Sie die Daten nach Eigenschaftswerten, um sich einen besseren Überblick über Ihre App und ihre Benutzer zu verschaffen.
* Im [Suchexplorer](app-insights-diagnostic-search.md) sind einzelne Ereignisse aufgeführt, z. B. bestimmte Anforderungen, Ausnahmen, Ablaufprotokolle oder Ereignisse, die Sie selbst über die [API](app-insights-api-custom-events-metrics.md) erstellt haben. Filtern und durchsuchen Sie die Ereignisse, und navigieren Sie zwischen zugehörigen Ereignissen, um Probleme zu untersuchen.

## Warnungen

* Richten Sie [Verfügbarkeitstests](app-insights-monitor-web-app-availability.md) ein, um Ihre Website fortwährend an Standorten auf der ganzen Welt zu testen und um E-Mails zu erhalten, sobald bei einem Test Fehler auftreten.
* Richten Sie [Metrikwarnungen](app-insights-monitor-web-app-availability.md) ein, um informiert zu werden, wenn Metriken wie z. B. Antwortzeiten oder Ausnahmeraten außerhalb zulässiger Grenzwerte liegen.


## Erweitern der Telemetriedaten

* [Überwachen Sie Abhängigkeiten](app-insights-dependencies.md), um zu überprüfen, ob REST, SQL oder andere externe Ressourcen die Leistung beeinträchtigen.
* [Verwenden Sie die API](app-insights-api-custom-events-metrics.md), um Ihre eigenen Ereignisse und Metriken für eine detailliertere Ansicht der Leistung und Nutzung Ihrer App zu senden.
* Mithilfe von [Verfügbarkeitstests](app-insights-monitor-web-app-availability.md) wird Ihre App fortwährend an Standorten auf der ganzen Welt überprüft. 


## Open Source

[Lesen und Hinzufügen von Code](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Nov15_HO4-->