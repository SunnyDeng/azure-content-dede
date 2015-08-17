<properties 
	pageTitle="Verwendungsanalyse für Webanwendungen mit Application Insights" 
	description="Übersicht über die Verwendungsanalyse mit Application Insights" 
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
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# Verwendungsanalyse für Webanwendungen mit Application Insights

Wenn Sie wissen, wie Benutzer Ihre Anwendung verwenden, können Sie Ihre Entwicklungsarbeit auf die Szenarien konzentrieren, die für die Benutzer am wichtigsten sind. Außerdem verschaffen Sie sich Einblick in die Ziele, die für Benutzer einfacher oder schwieriger zu erreichen sind.

Visual Studio Application Insights bietet Nutzungsverfolgung auf zwei Ebenen:

* **Daten zu Benutzern, Sitzungen und Seitenaufrufen** – direkt bereitgestellt.  
* **Benutzerdefinierte Telemetrie** – Sie [schreiben Code][api], um die Nutzung Ihrer App zu verfolgen. 

## Einrichten

Nutzungsdaten aus einer Webanwendung werden vom Clientbrowser gesendet.

#### Einrichten einer Application Insights-Ressource 

Mithilfe einer Application Insights-Ressource werden in Microsoft Azure Telemetriedaten aus Ihrer App analysiert und angezeigt. Möglicherweise haben Sie bereits eine solche Ressource eingerichtet, um serverseitige Daten Ihrer App in [ASP.NET][greenbrown] oder [J2EE][java] anzuzeigen. Andernfalls wäre jetzt ein guter Zeitpunkt dafür.

Es empfiehlt sich üblicherweise, die Nutzungsdaten vom Webclient in der gleichen Ressource anzuzeigen wie die Daten vom Server. Auf diese Weise können Sie Diagnosedaten und Metriken beider Seiten korrelieren. Wenn Sie bereits eine Ressource eingerichtet haben, fahren Sie mit dem nächsten Schritt fort.

#### Einfügen von Code in Webseiten

Öffnen Sie "Schnellstart" in Ihrer Ressource im [Azure-Portal][portal], und rufen Sie den Codeausschnitt zur Überwachung von Webseiten ab.

![](./media/app-insights-web-track-usage/02-monitor-web-page.png)

Fügen Sie den Code in eine Masterseite wie (in .NET) \_Layout.cshtml oder in eine Includedatei ein, um sicherzustellen, dass er in all Ihren Seiten eingefügt wird.

Der Codeausschnitt enthält den Instrumentationsschlüssel (iKey), der Ihre Ressource identifiziert. Um Daten an eine andere Ressource zu senden – beispielsweise zum Testen –, müssen Sie nur den iKey ersetzen.

Veröffentlichen Sie Ihre Webseiten, oder verwenden Sie sie im Debugmodus, um Telemetriedaten zu generieren.


## Wie beliebt ist meine Webanwendung?

Melden Sie sich beim [Azure-Portal][portal] an, navigieren Sie zur Anwendungsressource, und klicken Sie auf "Verwendung".

![](./media/app-insights-web-track-usage/14-usage.png)

* **Benutzer:** Die Anzahl der unterschiedlichen aktiven Benutzer über den Zeitbereich des Diagramms. 
* **Sitzungen:** Die Anzahl der aktiven Sitzungen
* **Seitenaufrufe** Zählt die Anzahl der Aufrufe von trackPageView(), wird in der Regel einmal auf jeder Webseite aufgerufen.

Klicken Sie auf ein Diagramm, um weitere Details anzuzeigen. Beachten Sie, dass Sie den Zeitraum der Diagramme ändern können.

### Wo wohnen meine Benutzer?

Klicken Sie auf das Blatt "Verwendung", um das Benutzerdiagramm und weitere Details anzuzeigen:

![Klicken Sie auf dem Blatt „Verwendung“ auf das Diagramm „Benutzer“.](./media/app-insights-web-track-usage/02-sessions.png)
 
### Welche Browser oder Betriebssysteme verwenden sie?

Gruppieren (Segmentieren) Sie Daten anhand einer Eigenschaft, z. B. Browser, Betriebssystem oder Ort:

![Wählen Sie ein Diagramm mit einer einzelnen Metrik, schalten Sie "Gruppierung" ein, und wählen Sie eine Eigenschaft](./media/app-insights-web-track-usage/03-browsers.png)


## Sitzungen

Sitzungen sind ein grundlegendes Konzept in Application Insights, um jedes Telemetrieereignis – wie z. B. Anforderungen, Seitenaufrufe, Ausnahmen oder benutzerdefinierte Ereignisse, die Sie selbst codieren – einer bestimmten Benutzersitzung zuzuordnen.

Für jede Sitzung werden umfangreiche Kontextinformationen gesammelt, wie etwa Geräteeigenschaften, geografischer Standort, Betriebssystem usw.

Wenn Sie sowohl Client als auch Server ([ASP.NET][greenbrown] oder [J2EE][java]) instrumentieren, geben die SDKs die Sitzungs-ID zwischen Client und Server weiter, sodass Ereignisse auf beiden Seiten korreliert werden können.

Bei der [Problemdiagnose][diagnostic] erhalten Sie alle Telemetriedaten zu der Sitzung, bei der ein Problem aufgetreten ist, einschließlich sämtlicher Anforderungen sowie protokollierter Ereignisse, Ausnahmen oder Ablaufverfolgungen.

Sitzungen ermöglichen eine ziemlich genaue Messung der Beliebtheit von Kontexten wie z. B. Gerät, Betriebssystem oder Standort. Indem Sie beispielsweise die Anzahl der Sitzungen für jedes Gerät anzeigen, erhalten Sie eine genauere Messung, wie häufig Ihre App auf diesem Gerät verwendet wird, als durch Zählung der Seitenansichten. Dies sind nützliche Informationen, um gerätespezifische Probleme zu untersuchen.


#### Was ist eine Sitzung?

Eine Sitzung repräsentiert eine einmalige Verwendung der App durch einen Benutzer. In der einfachsten Form beginnt eine Sitzung, wenn ein Benutzer die App startet, und endet, wenn er sie verlässt. Web-App-Sitzungen werden standardmäßig nach 30 Minuten Inaktivität bzw. nach 24 Stunden Aktivität beendet.

Sie können diese Standardeinstellungen ändern, indem Sie den Codeausschnitt bearbeiten.

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs` : Der Zeitraum in Millisekunden, nach dem die Sitzung aufgrund der Inaktivität des Benutzers beendet wird. Standardwert: 30 Minuten.
* `sessionExpirationMs` : Die maximale Sitzungslänge in Millisekunden. Wenn der Benutzer nach Ablauf dieses Zeitraums noch aktiv ist, zählt dies als weitere Sitzung. Standardwert: 24 Stunden.

**Sitzungsdauer** ist eine [Metrik][metrics], die die Zeitspanne zwischen den ersten und letzten Telemetrieelementen einer Sitzung aufzeichnet. (Der Timeoutzeitraum ist darin nicht enthalten.)

**Sitzungsanzahl** innerhalb eines bestimmten Intervalls ist als Anzahl eindeutiger Sitzungen mit Aktivität während dieses Intervalls definiert. Wenn Sie einen langen Zeitraum betrachten, wie z. B. die Anzahl der täglichen Sitzungen der vergangenen Woche, entspricht diese Anzahl üblicherweise der Gesamtanzahl von Sitzungen.

Wenn Sie jedoch kürzere Zeiträume untersuchen – beispielsweise auf Stundenbasis –, wird eine lange, mehrstündige Sitzung pro Stunde gezählt, in der die Sitzung aktiv ist.

## Benutzer und Benutzeranzahlen

Jeder Benutzersitzung ist eine eindeutige Benutzer-ID zugeordnet.

Standardmäßig werden Benutzer durch Platzieren eines Cookies identifiziert. In diesem Fall wird ein Benutzer, der mehrere Browser oder Geräte verwendet, mehr als einmal gezählt.

Die Metrik **Benutzeranzahl** innerhalb eines bestimmten Intervalls ist als Anzahl eindeutiger Benutzer mit aufgezeichneter Aktivität während dieses Intervalls definiert. Aus diesem Grund werden Benutzer mit langen Sitzungen möglicherweise mehrfach gezählt, wenn Sie einen Zeitraum von weniger als einer Stunde untersuchen.

**Neue Benutzer** zählt die Benutzer, deren erste Sitzungen mit der App während dieses Intervalls aufgezeichnet wurden. Wenn die Standardmethode der Benutzerzählung anhand von Cookies verwendet wird, werden hierbei auch Benutzer gezählt, die ihre Cookies gelöscht haben oder ein neues Gerät oder einen neuen Browser verwenden und damit zum ersten Mal auf Ihre App zugreifen. ![Klicken Sie auf dem Blatt „Verwendung“ auf das Diagramm „Benutzer“, um „Neue Benutzer“ zu überprüfen.](./media/app-insights-web-track-usage/031-dual.png)

## Synthetischer Datenverkehr

Synthetischer Datenverkehr umfasst Anforderungen aus Verfügbarkeits- und Auslastungstests, Suchmaschinencrawlern und anderen Agents.

Application Insights versucht, synthetischen Datenverkehr automatisch zu ermitteln, zu klassifizieren und entsprechend zu markieren. In den meisten Fällen ruft synthetischer Datenverkehr das JavaScript-SDK nicht auf, daher wird diese Aktivität in der Benutzer- und Sitzungszählung nicht eingeschlossen.

Für Application Insights-[Webtests][availability] hingegen wird die Benutzer-ID automatisch basierend auf dem POP-Standort und die Sitzungs-ID basierend auf der Testlauf-ID festgelegt. In Standardberichten wird synthetischer Datenverkehr standardmäßig herausgefiltert, sodass diese Benutzer und Sitzungen ausgeschlossen werden. Wenn jedoch synthetischer Datenverkehr eingeschlossen wird, kann dies zu einer geringfügigen Erhöhung der Gesamtanzahl von Benutzern und Sitzungen führen.
 
## Seitennutzung

Klicken Sie sich durch das Diagramm der Seitenaufrufe, um eine vergrößerte Version sowie eine Aufschlüsselung Ihrer beliebtesten Seiten zu erhalten:


![Klicken Sie auf dem Blatt "Übersicht" auf das Diagramm "Seitenaufrufe"](./media/app-insights-web-track-usage/05-games.png)
 
Das vorherige Beispiel gehört zu einer Spielewebsite. Daraus geht Folgendes unmittelbar hervor:

* Die Nutzung hat sich in der Vorwoche nicht erhöht. Vielleicht sollten wir über eine Suchmaschinenoptimierung nachdenken?
* Die Seite mit den Spielen wird von viel weniger Leuten als die Homepage aufgerufen. Warum regt unsere Homepage die Leute nicht zum Spielen an?
* "Crossword" ist das beliebteste Spiel. Wir sollten neue Ideen und Verbesserungen hier Priorität geben.

## Benutzerdefinierte Nachverfolgung

Lassen Sie uns einmal Folgendes annehmen. Anstatt jedes Spiel in einer eigenen Webseite zu implementieren, gestalten Sie den Code so um, dass alle in der App auf einer Seite angezeigt werden, wobei der Großteil der Funktionalität als JavaScript in die Website programmiert wird. Dies ermöglicht Benutzern ein schnelles Wechseln zwischen Spielen oder sogar die Anzeige mehrerer Spiele auf einer Seite.

Aber dennoch soll Application Insights weiter protokollieren, wie oft jedes Spiel geöffnet wird, und zwar auf dieselbe Weise wie bei den getrennten Webseiten. Das ist ganz einfach: Fügen Sie einfach in Ihr JavaScript einen Aufruf des Telemetriemoduls an der Stelle ein, an der aufgezeichnet werden soll, dass eine neue Seite geöffnet wurde:

	telemetryClient.trackPageView(game.Name);

## Benutzerdefinierte Ereignisse

Verwenden Sie auch benutzerdefinierte Ereignisse. Sie können diese von Geräte-Apps, Webseiten oder einem Webserver senden:

*JavaScript*

    telemetryClient.trackEvent("GameEnd");

*C#*

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

*VB*

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


Die häufigsten benutzerdefinierten Ereignisse werden auf dem Blatt "Übersicht" aufgeführt.

![Führen Sie auf dem Blatt "Übersicht" einen Bildlauf nach unten durch, und klicken Sie auf "Benutzerdefinierte Ereignisse".](./media/app-insights-web-track-usage/04-events.png)

Klicken Sie auf die Kopfzeile der Tabelle, um die Gesamtanzahl von Ereignissen anzuzeigen. Sie können das Diagramm anhand verschiedener Attribute wie z. B. des Ereignisnamens segmentieren:

![Wählen Sie ein Diagramm aus, das nur eine Metrik anzeigt. Schalten Sie "Gruppieren" ein. Wählen Sie eine Eigenschaft aus. Nicht alle Eigenschaften sind verfügbar.](./media/app-insights-web-track-usage/06-eventsSegment.png)

Das besonders Nützliche an Zeitachsen ist, dass Sie Änderungen mit anderen Metriken und Ereignissen in Beziehung setzen können. Zu Zeiten, in denen mehr Spiele gespielt werden, erwarten Sie z. B. auch einen Anstieg abgebrochener Spiele. Aber wenn der Anstieg abgebrochener Spiele unverhältnismäßig ist, möchten Sie herausfinden, ob die hohe Auslastung Probleme verursacht, die Benutzer nicht akzeptabel finden.

## Detailsuche bei bestimmten Ereignissen

Um besser zu verstehen, wie eine typische Sitzung abläuft, wechselt zu erhalten, wird empfohlen, sich auf eine bestimmte Benutzersitzung konzentrieren, die einen bestimmten Typ von Ereignis aufweist.

Bei diesem Beispiel haben wir das benutzerdefinierte Ereignis "NoGame" programmiert, das aufgerufen wird, wenn sich der Benutzer abmeldet, ohne tatsächlich ein Spiel gestartet zu haben. Warum würde ein Benutzer das tun? Wenn wir einige bestimmte Vorkommen analysieren, erhalten wir vielleicht einen Hinweis.

Die von der App empfangenen benutzerdefinierten Ereignisse sind anhand des Namens auf dem Blatt "Übersicht" aufgeführt:


![Klicken Sie auf dem Blatt "Übersicht" auf einen der benutzerdefinierten Ereignistypen.](./media/app-insights-web-track-usage/07-clickEvent.png)
 
Klicken Sie sich durch das Ereignis von Interesse, und wählen Sie ein aktuelles spezifisches Vorkommen aus:


![Klicken Sie in der Liste unter dem Übersichtsdiagramm auf ein Ereignis](./media/app-insights-web-track-usage/08-searchEvents.png)
 
Lassen Sie uns nun alle Telemetriedaten für die Sitzung anschauen, in der dieses bestimmte "NoGame"-Ereignis aufgetreten ist.


![Klicken Sie auf "Gesamte Telemetrie für Sitzung"](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
Es gab keine Ausnahmen, sodass der Benutzer nicht durch einen Fehler am Spielen gehindert wurde.
 
Wir können bis auf Seitenaufrufe alle Arten von Telemetriedaten für diese Sitzung herausfiltern:


![](./media/app-insights-web-track-usage/10-filter.png)
 
Jetzt können wir erkennen, dass sich dieser Benutzer nur angemeldet, um die neuesten Punktstände zu überprüfen. Vielleicht sollten wir eine User Story entwickeln, die dies vereinfacht. (Und wir sollten ein benutzerdefiniertes Ereignis implementieren, das meldet, wenn diese bestimmte User Story auftritt.)

## Filtern, Suchen und Segmentieren der Daten mit Eigenschaften
Sie können beliebige Tags und numerische Werte an Ereignisse anfügen.
 

*JavaScript auf Client*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*C# auf Server*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*VB auf Server*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

Fügen Sie Eigenschaften an Seitenaufrufe auf die gleiche Weise an:

*JavaScript auf Client*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

Zeigen Sie auf dem Blatt "Diagnosesuche" die Eigenschaften an, indem Sie sich durch ein einzelnes Vorkommen eines Ereignisses klicken.


![Öffnen Sie in der Liste der Ereignisse ein Ereignis, und klicken Sie dann auf "...", um weitere Eigenschaften anzuzeigen](./media/app-insights-web-track-usage/11-details.png)
 
Verwenden Sie das Suchfeld, um Ereignisvorkommen mit einem bestimmten Eigenschaftswert anzuzeigen.


![Geben Sie einen Wert in das Suchfeld ein](./media/app-insights-web-track-usage/12-searchEvents.png)


## A-B-Tests

Wenn Sie nicht wissen, welche Variante eines Features erfolgreicher sein wird, veröffentlichten Sie beide, und ermöglichen Sie verschiedenen Benutzern den Zugriff darauf. Messen Sie den jeweiligen Erfolg, und erstellen Sie anschließend eine vereinheitlichte Version.

Für dieses Verfahren fügen Sie unterschiedliche Tags an alle Telemetriedaten an, die von jeder Version Ihrer App gesendet wird. Dazu definieren Sie Eigenschaften im aktiven "TelemetryContext"-Element. Diese Standardeigenschaften werden jeder Telemetrienachricht hinzugefügt, die die Anwendung sendet, nicht nur Ihren benutzerdefinierten Nachrichten, sondern auch den Standardtelemetriedaten.

Im Application Insights-Portal können Sie anschließend Ihre Daten anhand der Tags filtern und gruppieren (segmentieren), um die verschiedenen Versionen zu vergleichen.

*C# auf Server*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*VB auf Server*

```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

Einzelne Telemetriedaten können die Standardwerte außer Kraft setzen.

Sie können einen universellen Initialisierer einrichten, damit alle neuen "TelemetryClient"-Elemente automatisch Ihren Kontext verwenden.

```C#

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }
```

Gehen Sie im App-Initialisierer wie z. B. "Global.asax.cs" so vor:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## Erstellen – Messen – Lernen

Wenn Sie mit Analysen arbeiten, werden diese zu einem integrierten Bestandteil Ihres Entwicklungszyklus und nicht bloß zu etwas, an das Sie denken, wenn es gilt, Probleme zu lösen. Hier einige Tipps:

* Bestimmen Sie die wichtigste Metrik Ihrer Anwendung. Wünschen Sie sich so viele Benutzer wie möglich oder lieber eine kleine Gruppe sehr zufriedener Benutzer? Möchten Sie Websitebesuche oder den Umsatz maximieren?
* Planen Sie eine Messung für jede User Story. Wenn Sie eine neue User Story oder Funktion entwerfen oder eine vorhandene aktualisieren wollen, denken Sie stets daran, wie Sie den Erfolg der Änderung messen möchten. Fragen Sie sich vor Beginn der Programmierung "Welche Auswirkungen hat dies auf unsere Metriken, wenn es funktioniert? Sollten wir neue Ereignisse nachverfolgen?" Und wenn das Feature dann online ist, sehen Sie sich die Analyse an, und reagieren Sie auf die Ergebnisse. 
* Setzen Sie die anderen Metriken in Beziehung zur Hauptmetrik. Wenn Sie z. B. das Feature "Favoriten" hinzufügen, möchten Sie bestimmt wissen, wie oft Benutzer Favoriten hinzufügen. Aber vielleicht ist es noch interessanter zu wissen, wie oft sie zu ihren Favoriten zurückkehren. Und am wichtigsten ist, ob Benutzer, die Favoriten nutzen, letztendlich mehr von Ihrem Produkt kaufen.
* Testen Sie mit begrenzten Benutzergruppen. Richten Sie einen Feature-Umschalter ein, der es Ihnen ermöglicht, ein neues Feature nur für einige Benutzer sichtbar zu machen. Verwenden Sie Application Insights, um festzustellen, ob das neue Feature so wie vorgesehen genutzt wird. Nehmen Sie Anpassungen vor, und veröffentlichen Sie es für ein breiteres Publikum.
* Sprechen Sie Ihre Benutzer an! Analysen allein genügen nicht, sondern dienen zur Aufrechterhaltung einer guten Kundenbeziehung.


## Referenzen

* [Verwenden der API – Übersicht][api]
* [JavaScript-API-Referenz](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## Video

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=August15_HO6-->