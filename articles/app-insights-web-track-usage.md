<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Nutzung nachverfolgen

## <a name="webclient"></a>Einrichten der Webnutzungsanalysen

Falls Sie es noch nicht getan haben, [fügen Sie Ihrem Webprojekt Application Insights hinzu][start].

## <a name="usage"></a>Nutzungsanalyse

Im Anwendungsübersichtsfenster werden Ihnen folgende Nutzungs-Tiles angezeigt:

![](./media/appinsights/appinsights-47usage.png)

### Sessions per browser

Eine *Sitzung* ist ein Zeitraum, der mit dem Öffnen einer Seite Ihrer Website durch den Benutzer beginnt. Dieser Zeitraum endet, nachdem der Benutzer für die Dauer von 30 Minuten keinerlei Webanforderungen mehr gesendet hat.

Klicken Sie, um das Diagramm zu vergrößern.

### Top page views

Zeigt die Gesamtzählerstände der letzten 24 Stunden an.

Klicken Sie auf das Seitenaufrufe-Tile, um einen detaillierteren Verlauf anzuzeigen.

![](./media/appinsights/appinsights-49usage.png)

Klicken Sie auf den Zeitraum, um den bis zu sieben Tage zurückliegenden Verlauf anzuzeigen.

Klicken Sie auf einen Graphen, um andere mögliche Metriken anzuzeigen.

![](./media/appinsights/appinsights-63usermetrics.png)

## Custom page counts

Standardmäßig wird jedes Mal ein Seitenzähler angezeigt, wenn eine neue Seite in den Client-Browser geladen wird. Sie möchten jedoch ggf. zusätzliche Seitenaufrufe zählen. Beispielsweise könnte eine Seite in Registerkarten gegliedert sein, und Sie möchten, dass jeder Wechsel von Registerkarten als Aufruf gezählt wird. Auf der Seite könnte auch neuer Inhalt durch JavaScript-Code geladen werden, ohne dass sich die Browser-URL ändert.

Fügen Sie an geeigneter Stelle in Ihren Client-Code einen JavaScript-Aufruf wie diesen ein:

    appInsights.trackPageView(myPageName);

Der Seitenname kann die gleichen Zeichen wie eine URL enthalten, allerdings wird alles nach den Zeichen „\#“ oder „?“ ignoriert.

## Inspecting individual page view events

Normalerweise wird die Seitenaufruf-Telemetrie von Application Insights analysiert, und Sie erhalten nur kumulative Berichte, gemittelt über alle Benutzer. Sie können jedoch zu Debugging-Zwecken auch einzelne Seitenaufrufereignisse anzeigen.

Legen Sie im Fenster "Diagnosesuche" als Filter die Einstellung "Seitenansicht" fest.

![](./media/appinsights/appinsights-51searchpageviews.png)

Wählen Sie ein Ereignis, um weitere Details anzuzeigen.

> [WACOM.NOTE] Falls Sie [Suche][diagnostic] verwenden, beachten Sie, dass nur ganze Wörter verglichen werden: „Abou“ und „bout“ stimmen nicht mit „About“ überein, „Abou\*“ jedoch stimmt überein. Ein Suchbegriff darf nicht mit einem Platzhalterzeichen beginnen. Beispielsweise ergibt die Suche nach „\*bou“ nicht „About“.

> [Erfahren Sie mehr über Diagnosesuche][diagnostic]

## Nachverfolgen der Nutzung

> [AZURE.VIDEO Nachverfolgung-Nutzung-mit-Application-Insights]

## Weitere Informationen

-   [Application Insights – Erste Schritte][start]
-   [Live-Webserver jetzt überwachen][redfield]
-   [Leistung in Webanwendungen überwachen][perf]
-   [Diagnoseprotokolle durchsuchen][diagnostic]
-   [Nachverfolgung der Verfügbarkeit mit Webtests][availability]
-   [Nutzung nachverfolgen][usage]
-   [Fragen und Antworten, Problembehandlung][qna]

<!--Link references-->

  [start]: ../app-insights-start-monitoring-app-health-usage/
  [diagnostic]: ../app-insights-search-diagnostic-logs/
  [redfield]: ../app-insights-monitor-performance-live-website-now/
  [perf]: ../app-insights-web-monitor-performance/
  [availability]: ../app-insights-monitor-web-app-availability/
  [usage]: ../app-insights-web-track-usage/
  [qna]: ../app-insights-troubleshoot-faq/
