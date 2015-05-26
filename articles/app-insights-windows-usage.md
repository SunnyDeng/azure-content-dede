<properties 
	pageTitle="Überwachen der Nutzung in Windows Store- und Windows Phone-Apps mit Application Insights" 
	description="Analysieren Sie die Nutzung von Windows-Geräteanwendungen mit Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="awills"/>

#  Überwachen der Nutzung in Windows Store- und Windows Phone-Apps mit Application Insights

*Application Insights befindet sich in der Vorschau.*

Erfahren Sie, wie viele Benutzer Ihre App hat und welche Seiten sie in Ihrer App anzeigen. Application Insights bietet Ihnen diese Daten ohne Konfigurationsaufwand. Durch Einfügen einiger Zeilen Code in Ihre App erfahren Sie mehr über die Pfade der Benutzer durch Ihre App und was sie mit ihr erreichen möchten.

Falls noch nicht geschehen, fügen Sie [Application Insights Ihrem App-Projekt hinzu][windows] und veröffentlichen es erneut.


## <a name="usage"></a>Nachverfolgung der Nutzung

Klicken Sie sich auf der Zeitachse "Übersicht" durch die Diagramme "Benutzer und Sitzungen", um ausführlichere Analysen einzublenden.


![](./media/appinsights/appinsights-d018-oview.png)

* **Benutzer** werden anonym nachverfolgt, weshalb der gleiche Benutzer auf verschiedenen Geräten zweimal gezählt wird.
* Eine **Sitzung** wird gezählt, wenn die App angehalten wird \(und zwar für mehr als einen kurzen Zeitraum, um das Zählen unbeabsichtigter Unterbrechungen zu vermeiden\).

#### Segmentierung

Segmentieren Sie ein Diagramm, um eine Aufschlüsselung nach verschiedenen Kriterien zu erhalten. Um z. B. zu prüfen, wie viele Benutzer jede Version Ihrer App verwenden, öffnen Sie das Diagramm "Benutzer" und segmentieren nach Anwendungsversion:

![Aktivieren Sie im Diagramm "Benutzer" die Segmentierung, und wählen Sie die Anwendungsversion](./media/appinsights/appinsights-d25-usage.png)


#### Seitenaufrufe

Um die Pfade der Benutzer durch Ihre App zu ermitteln, fügen Sie Ihrem Code [Telemetrie für Seitenaufrufe][api] hinzu:

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

Sehen Sie sich die Ergebnisse im Diagramm "Seitenaufrufe" und die Details an:

![](./media/appinsights/appinsights-d27-pages.png)

Klicken Sie sich durch eine beliebige Seite, um die Details der einzelnen Vorkommen zu untersuchen.

#### Benutzerdefinierte Ereignisse

Durch Einfügen von Code, um benutzerdefinierte Ereignisse aus Ihrer App zu senden, können Sie das Verhalten Ihre Benutzer und die Verwendung bestimmter Features und Szenarien nachverfolgen.

Zum Beispiel:

    telemetry.TrackEvent("GameOver");

Die Daten werden im Raster "Benutzerdefinierte Ereignisse" angezeigt. Sie können eine aggregierte Ansicht im Metrik-Explorer anzeigen oder sich durch ein Ereignis klicken, um bestimmte Vorkommen zu prüfen.

![](./media/appinsights/appinsights-d28-events.png)


Sie können zu jedem Ereignis Zeichenfolgen- und numerische Eigenschaften hinzufügen.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


Klicken Sie auf ein Vorkommen, um seine detaillierten Eigenschaften einschließlich derjenigen anzuzeigen, die Sie definiert haben.


![](./media/appinsights/appinsights-d29-eventProps.png)

In der [API-Referenz][api] finden Sie weitere Informationen zu benutzerdefinierten Ereignissen.



## <a name="debug"></a>Debug- im Vergleich zum Freigabemodus

#### Debuggen

Wenn Sie den Build im Debug-Modus erstellen, werden Ereignisse gesendet, sobald sie generiert werden. Wenn Sie die Internetverbindung verlieren und dann die Anwendung beenden, ehe die Verbindung wiederhergestellt ist, werden Offlinetelemetriedaten verworfen.

#### Freigabe

Wenn Sie den Build im Freigabemodus erstellen, werden Ereignisse auf dem Gerät gespeichert und gesendet, wenn die Anwendung fortgesetzt wird. Daten werden auch bei der ersten Verwendung der Anwendung gesendet. Wenn es beim Start keine Internetverbindung gibt, werden die vorherigen Telemetriedaten und auch die Telemetrie für den aktuellen Lebenszyklus gespeichert und beim nächsten Fortsetzen der Anwendung gesendet.

## <a name="next"></a>Nächste Schritte

[Kennenlernen Ihrer Benutzer][knowUsers]

[Weitere Informationen zum Metrik-Explorer][metrics]


[Problembehandlung][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


<!--HONumber=54-->