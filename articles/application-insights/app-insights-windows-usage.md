<properties 
	pageTitle="Überwachen der Nutzung in Windows Store- und Windows Phone-Apps mit Application Insights" 
	description="Analysieren Sie die Nutzung von Windows-Geräteanwendungen mit Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="awills"/>

#  Überwachen der Nutzung in Windows Store- und Windows Phone-Apps mit Application Insights

*Application Insights befindet sich in der Vorschau.*

Erfahren Sie, wie viele Benutzer Ihre App hat und welche Seiten sie in Ihrer App anzeigen. Application Insights bietet Ihnen diese Daten ohne Konfigurationsaufwand. Durch Einfügen einiger Zeilen Code in Ihre App erfahren Sie mehr über die Pfade der Benutzer durch Ihre App und was sie mit ihr erreichen möchten.

Falls noch nicht geschehen, fügen Sie [Application Insights Ihrem App-Projekt hinzu][windows] und veröffentlichen es erneut.


## <a name="usage"></a>Nachverfolgung der Nutzung

Klicken Sie sich auf der Zeitachse "Übersicht" durch die Diagramme "Benutzer und Sitzungen", um ausführlichere Analysen einzublenden.


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* **Benutzer** werden anonym nachverfolgt, weshalb der gleiche Benutzer auf verschiedenen Geräten zweimal gezählt wird.
* Eine **Sitzung** wird gezählt, wenn die App angehalten wird (und zwar für mehr als einen kurzen Zeitraum, um das Zählen unbeabsichtigter Unterbrechungen zu vermeiden).

#### Segmentierung

Segmentieren Sie ein Diagramm, um eine Aufschlüsselung nach verschiedenen Kriterien zu erhalten. Um z. B. zu prüfen, wie viele Benutzer jede Version Ihrer App verwenden, öffnen Sie das Diagramm "Benutzer" und segmentieren nach Anwendungsversion:

![Aktivieren Sie im Diagramm "Benutzer" die Segmentierung, und wählen Sie die Anwendungsversion](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### Seitenaufrufe

Um die Pfade der Benutzer durch Ihre App zu ermitteln, fügen Sie Ihrem Code [Telemetrie für Seitenaufrufe][api] hinzu:

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

Sehen Sie sich die Ergebnisse im Diagramm "Seitenaufrufe" und die Details an:

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

Klicken Sie sich durch eine beliebige Seite, um die Details der einzelnen Vorkommen zu untersuchen.

#### Benutzerdefinierte Ereignisse

Durch Einfügen von Code, um benutzerdefinierte Ereignisse aus Ihrer App zu senden, können Sie das Verhalten Ihre Benutzer und die Verwendung bestimmter Features und Szenarien nachverfolgen.

Zum Beispiel:

    telemetry.TrackEvent("GameOver");

Die Daten werden im Raster "Benutzerdefinierte Ereignisse" angezeigt. Sie können eine aggregierte Ansicht im Metrik-Explorer anzeigen oder sich durch ein Ereignis klicken, um bestimmte Vorkommen zu prüfen.

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


Sie können zu jedem Ereignis Zeichenfolgen- und numerische Eigenschaften hinzufügen.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


Klicken Sie auf ein Vorkommen, um seine detaillierten Eigenschaften einschließlich derjenigen anzuzeigen, die Sie definiert haben.


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

In der [API-Referenz][api] finden Sie weitere Informationen zu benutzerdefinierten Ereignissen.

## Sitzungen

Sitzungen sind ein grundlegendes Konzept in Application Insights, um jedes Telemetrieereignis – wie z. B. Abstürze oder benutzerdefinierte Ereignisse, die Sie selbst codieren – einer bestimmten Benutzersitzung zuzuordnen.

Für jede Sitzung werden umfangreiche Kontextinformationen gesammelt, wie etwa Geräteeigenschaften, geografischer Standort, Betriebssystem usw.

Bei der [Problemdiagnose][diagnostic] erhalten Sie alle Telemetriedaten zu der Sitzung, bei der ein Problem aufgetreten ist, einschließlich sämtlicher Anforderungen sowie protokollierter Ereignisse, Ausnahmen oder Ablaufverfolgungen.

Sitzungen ermöglichen eine ziemlich genaue Messung der Beliebtheit von Kontexten wie z. B. Gerät, Betriebssystem oder Standort. Indem Sie beispielsweise die Anzahl der Sitzungen für jedes Gerät anzeigen, erhalten Sie eine genauere Messung, wie häufig Ihre App auf diesem Gerät verwendet wird, als durch Zählung der Seitenansichten. Dies sind nützliche Informationen, um gerätespezifische Probleme zu untersuchen.


#### Was ist eine Sitzung?

Eine Sitzung repräsentiert eine einmalige Verwendung der App durch einen Benutzer. In der einfachsten Form beginnt eine Sitzung, wenn ein Benutzer die App startet, und endet, wenn er sie verlässt. Bei mobilen Apps wird eine Sitzung beendet, wenn die App-Verwendung länger als 20 Sekunden angehalten (in den Hintergrund verschoben) wird. Wenn die Anwendung fortgesetzt wird, wird eine neue Sitzung gestartet. Naturgemäß kann ein Benutzer mehrere Sitzungen pro Tag oder sogar innerhalb einer Stunde starten.

**Sitzungsdauer** ist eine Metrik, die die Zeitspanne zwischen den ersten und letzten Telemetrieelementen einer Sitzung darstellt. (Der Timeoutzeitraum ist darin nicht enthalten.)


**Sitzungsanzahl** innerhalb eines bestimmten Intervalls ist als Anzahl eindeutiger Sitzungen mit Aktivität während dieses Intervalls definiert. Wenn Sie einen langen Zeitraum betrachten, wie z. B. die Anzahl der täglichen Sitzungen der vergangenen Woche, entspricht diese Anzahl üblicherweise der Gesamtanzahl von Sitzungen.

Wenn Sie jedoch kürzere Zeiträume untersuchen – beispielsweise auf Stundenbasis –, wird eine lange, mehrstündige Sitzung pro Stunde gezählt, in der die Sitzung aktiv ist.

## Benutzer und Benutzeranzahlen

Jede Benutzersitzung wird mit einer eindeutigen Benutzer-ID verknüpft, anhand der App-Verwendung generiert und im lokalen Gerätespeicher gespeichert. Ein Benutzer, der mehrere Browser oder Geräte verwendet, wird mehr als einmal gezählt.

Die Metrik **Benutzeranzahl** innerhalb eines bestimmten Intervalls ist als Anzahl eindeutiger Benutzer mit aufgezeichneter Aktivität während dieses Intervalls definiert. Aus diesem Grund werden Benutzer mit langen Sitzungen möglicherweise mehrfach gezählt, wenn Sie einen Zeitraum von weniger als einer Stunde untersuchen.

**Neue Benutzer** zählt die Benutzer, deren erste Sitzungen mit der App während dieses Intervalls aufgezeichnet wurden.


## <a name="debug"></a>Debugmodus im Vergleich zum Freigabemodus

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
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


 

<!---HONumber=Oct15_HO3-->