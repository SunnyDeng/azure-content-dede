<properties 
	pageTitle="Ablaufverfolgung für Verwendung und Ereignisse in Ihrer Webanwendung mit der Application Insights-API" 
	description="Fügen Sie einige Codezeilen ein, um die Verwendung nachzuverfolgen und Probleme zu diagnostizieren." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-06" 
	ms.author="awills"/>

# Nachverfolgen von benutzerdefinierten Nutzungsereignissen und Metriken in Ihrer Webanwendung

*Application Insights befindet sich in der Vorschauphase.*

Fügen Sie ein paar Zeilen Code in die Webanwendung ein, um herauszufinden, wofür Benutzer sie verwenden. Sie können Ereignisse, Metriken und Seitenaufrufe nachverfolgen. Sie sehen Diagramme und Tabellen der Daten, die für alle Benutzer zusammengefasst werden. 

> [AZURE.NOTE] Derzeit ist nicht die vollständige Benutzerfreundlichkeit vorhanden. Sie können benutzerdefinierte Ereignisse und Metriken an Application Insights senden und die unformatierten Telemetriedaten in der [Diagnosesuche][diagnostic] suchen. Sie können aber noch nicht die verarbeiteten statistischen Diagramme anzeigen - das wird bald möglich sein.

<!-- Sample pic -->

* [Client- und Servernachverfolgung](#clientServer)
* [Vorbereitung](#prep)
* [Nachverfolgen von Metriken](#metrics)
* [Nachverfolgen von Ereignissen](#events)
* [Nachverfolgen von Seitenaufrufen](#pageViews)
* [Filtern, Suchen und Segmentieren der Daten mit Eigenschaften](#properties)
* [Kombinieren von Metriken und Ereignissen](#measurements)
* [Festlegen der Standardwerte für Eigenschaften](#defaults)
* [Definieren mehrerer Kontexte](#contexts)
* [Ein- und Ausschalten der Telemetrie](#disable)
* [Nächste Schritte](#next)



## <a name="clientServer"></a> Client- und Servernachverfolgung

Sie können die Telemetriedaten vom Client (Webseite) oder vom Server Ihrer App oder von beiden aus senden.

Die Client- und Server-APIs sind sehr ähnlich. Sie können die gleichen Arten von Telemetriedaten von den Webbrowsern Ihrer Benutzer und von Ihrem Webserver aus senden. Der Unterschied liegt im Umfang der Daten, die Sie senden können.

* Die Nachverfolgung auf dem Webclient ist besonders nützlich, wenn Sie sehr aktive Webseiten mit viel JavaScript haben. Beispielsweise könnten Sie überwachen, wie oft Benutzer auf eine bestimmte Schaltfläche klicken oder wie oft sie auf Validierungsfehler stoßen.
* Die Nachverfolgung auf dem Webserver eignet sich besser für die Überwachung von Geschäftsmetriken und -ereignissen, wie z. B. der Wert des Warenkorbs eines Kunden oder die Anzahl der abgebrochenen Bestellungen.

In einer typischen ASP.NET-Webanwendung haben Sie den Standard-JavaScript-Aufruf von trackPageView() in der Hauptwebseite, und Sie fügen einige Aufrufe zum Nachverfolgen von Ereignissen und Metriken im Servercode hinzu. Ist der clientseitigen Code sehr umfangreich, können Sie auch einige Aufrufe zum Nachverfolgen von Ereignissen und Metriken auf dem Client hinzufügen.


## <a name="prep"></a>Vorbereitung

Falls noch nicht geschehen:

* So rufen Sie Telemetriedaten aus einer ASP.NET-Webanwendung ab:
    [Hinzufügen von Application Insights zu Ihrem Projekt][greenbrown]
    Schließen Sie Folgendes in den Webservercode ein:
    (C#) `mit Microsoft.ApplicationInsights;`
	(VB) `importiert Microsoft.ApplicationInsights`
* [Einrichten der Webnutzungsanalysen][usage]. Der JavaScript-Initialisierungscode sollte in jeder Webseite, in der Sie Überwachungscode schreiben möchten, oder in einer Hauptseite eingeschlossen werden. 
    Funktioniert es, sollten Sie Daten im Übersichtsfenster unter der Nutzungsanalyse sehen.

Wenn Sie Ihre Anwendung auf dem Entwicklungscomputer im Debugmodus ausführen, werden die Ergebnisse in Application Insights innerhalb von Sekunden angezeigt. Wenn Sie die Anwendung bereitstellen, dauert es länger, bis Daten durch die Pipeline von Ihrem Server und Ihren Clients übertragen wurden.

<!--
## <a name="metrics"></a> Nachverfolgen von Metriken

Sie müssen nicht mehr tun, um grundlegende Nutzungsdaten, wie z. B. Seitenaufrufe, zu erhalten. Jedoch können Sie ein paar Codezeilen schreiben, um mehr über die Verwendung Ihrer Anwendung durch die Benutzer zu erfahren.

Wenn Ihre Anwendung z. B. ein Spiel ist, können Sie die durchschnittliche Punktzahl ermitteln, die Benutzer erreichen, und überprüfen, ob sie das Spiel nach dem Veröffentlichen einer neuen Version schwieriger oder einfacher finden.

Fügen Sie eine Zeile des Skripts wie folgt an geeigneter Stelle in Ihre Anwendung ein, um eine Metrik - d. h. einen numerischen Wert wie eine Punktzahl - nachzuverfolgen:

JavaScript auf Client

    appInsights.trackMetric("Alerts", notifications.Count);

C# auf Server

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

VB auf Server

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

Testen Sie die Anwendung und verwenden Sie sie, um den trackMetric()-Aufruf auszuführen.


Wechseln Sie zu Ihrer Anwendung in Application Insights und klicken Sie durch die [Metriken][metrics]-Kachel. Wählen Sie die Metrik aus, um die ersten Ergebnisse anzuzeigen.


Das Diagramm zeigt die aktuellen durchschnittlichen Gesamtwerte aller Benutzer. 


(Übrigens: Metriken werden nicht für die Diagnose von Problemen optimiert. Wenn Sie diese Funktion benötigen, finden Sie Informationen unter [Diagnoseprotokollierung][diagnostic].) -->


## <a name="events"></a>Nachverfolgen von Ereignissen

Ereignisse zeigen die Häufigkeit des Auftretens, gemittelt über Ihre Benutzer. Angenommen, Sie möchten wissen, wie oft Benutzer Ihr Spiel abschließen. Fügen Sie in den Code, der das Spiel beendet, eine Zeile wie folgt ein:

JavaScript auf Client

    appInsights.trackEvent("EndOfGame");

C# auf Server
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

VB auf Server


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

Wenn Sie Telemetriedaten von Client und Server senden, müssen Sie die Ereignisse unterschiedlich benennen.


## <a name="pageViews"></a>Seitenaufrufe (nur Client)

Standardmäßig protokolliert das Initialisierungsskript im Kopf der Webseite einen Seitenaufruf und benennt das Ereignis mit der relativen URL der Seite. Diese Aufrufe bieten die grundlegenden Seitennutzungsstatistiken. 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### Benutzerdefinierte Seitendaten

Wenn Sie möchten, können Sie den Aufruf zum Ändern des Namens ändern oder zusätzliche Aufrufe einfügen. Wenn Ihre Einzelseiten-Webanwendungen z. B. mehrere Registerkarten anzeigt, sollten Sie einen Seitenaufruf erfassen, wenn der Benutzer zu einer anderen Registerkarte wechselt. Beispiel:

JavaScript auf Client:

    appInsights.trackPageView("tab1");

Wenn Sie mehrere Registerkarten in anderen HTML-Seiten haben, können Sie ebefalls die URL angeben:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>Filtern, Suchen und Segmentieren der Daten mit Eigenschaften

Sie können Eigenschaften und Messungen Ihren Ereignisses, Seitenaufrufen und anderen Telemetriedaten anfügen. 

**Eigenschaften** sind die Zeichenfolgenwerte, die Sie zum Filtern der Telemetriedaten in den Nutzungsberichten verwenden können. Wenn zum Beispiel die Anwendung mehrere Spiele bereitstellt, sollten Sie den Namen des Spiels an jedes Ereignis anfügen, damit Sie sehen können, welche Spiele immer populärer werden.

**Maßeinheiten** sind numerische Werte, aus denen Sie Statistiken in den Nutzungsberichten abrufen können.


JavaScript auf Client

    appInsights.trackEvent("EndOfGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# auf Server

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements);


VB auf Server

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


Fügen Sie Eigenschaften an Seitenaufrufe auf die gleiche Weise an:

JavaScript auf Client

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] Achten Sie darauf, keine persönlich identifizierbaren Informationen in den Eigenschaften zu protokollieren.


## Zeitgesteuerte Seitenaufrufe und Ereignisse

Sie können Ereignissen und Seitenaufrufen Zeitsteuerungsdaten anfügen. Verwenden Sie diese Aufrufe, anstatt trackEvent oder trackPageView aufzurufen:

JavaScript auf Client

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Verwenden Sie die gleiche Zeichenfolge als ersten Parameter in den Start- und Stoppaufrufen.

## <a name="defaults"></a>Festlegen von Standardwerten für Eigenschaften (nicht auf Webclient)

Sie können die Standardwerte in einem TelemetryContext-Aufruf festlegen. Sie werden an jede Metrik und jedes Ereignis angefügt, das aus dem Kontext gesendet wird. 
    

C# auf Server

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
VB auf Server

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
Einzelne Telemetriedaten können die Standardwerte außer Kraft setzen.

Wenn Sie zwischen Gruppen von Standardeigenschaftswerten wechseln möchten, richten Sie mehrere Kontexte ein.



## <a name="next"></a>Nächste Schritte


[Suchen von Ereignissen und Protokollen][diagnostic]

[Problembehandlung][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=46--> 
