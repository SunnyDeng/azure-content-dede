<properties 
	pageTitle="Application Insights-API für benutzerdefinierte Ereignisse und Metriken" 
	description="Fügen Sie einige Codezeilen in Ihrer Geräte- oder Desktop-App, Webseite oder dem Webdienst ein, um Nutzungs- und Diagnoseprobleme nachzuverfolgen." 
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
	ms.date="04/28/2015" 
	ms.author="awills"/>

# Application Insights-API für benutzerdefinierte Ereignisse und Metriken 

*Application Insights befindet sich in der Vorschau.*

Fügen Sie einige Codezeilen in Ihre Anwendung ein, um herauszufinden, wie sie von Benutzern eingesetzt wird, oder um Probleme zu diagnostizieren. Sie können Telemetriedaten von Geräte- und Desktop-Apps, Webclients und Webservern senden.

Application Insights-Datensammler verwenden diese API, um Standardtelemetriedaten wie Seitenaufrufe und Ausnahmeberichte zu senden. Sie können sie jedoch auch verwenden, um eigene benutzerdefinierte Telemetriedaten zu senden.

## API-Zusammenfassung

Die API ist, abgesehen von ein paar kleinen Variationen, auf allen Plattformen einheitlich.

Methode | Verwendung
---|---
[`TrackPageView`](#page-views) | Seiten, Bildschirme, Blätter oder Formulare.
[`TrackEvent`](#track-event) | Benutzeraktionen und andere Ereignisse. Wird zum Verfolgen des Benutzerverhaltens oder zur Leistungsüberwachung eingesetzt.
[`TrackMetric`](#track-metric) | Leistungsmessungen wie Warteschlangenlängen, die nicht im Zusammenhang mit bestimmten Ereignissen stehen.
[`TrackException`](#track-exception)|Protokollieren von Ausnahmen für die Diagnose. Verfolgen Sie, wo diese in Bezug auf andere Ereignisse auftreten, und untersuchen Sie die Stapelüberwachung.
[`TrackRequest`](#track-request)| Protokollieren Sie die Häufigkeit und Dauer der Serveranforderungen für die Leistungsanalyse.
[`TrackTrace`](#track-trace)|Diagnoseprotokollnachrichten. Sie können auch Drittanbieterprotokolle erfassen.

Sie können den meisten dieser Telemetrieaufrufe [Eigenschaften und Metriken anfügen](#properties).


## <a name="prep"></a>Vorbereitung

Falls noch nicht geschehen:

* Fügen Sie Ihrem Projekt das Application Insights-SDK hinzu:
 * [ASP.NET-Projekt][greenbrown]
 * [Windows-Projekt][windows]
 * [Java-Projekt][java] 
 * [JavaScript auf jeder Webseite][client]   

* Schließen Sie Folgendes in den Geräte- oder Webservercode ein:

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## Erstellen eines TelemetryClient

Erstellen Sie eine Instanz von TelemetryClient (außer in JavaScript auf Webseiten):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

Es wird empfohlen, für jede Anforderung in einer Web-App oder für jede Sitzung in anderen Apps eine Instanz von `TelemetryClient` zu verwenden. Sie können Eigenschaften wie z. B. `TelemetryClient.Context.User.Id` festlegen, um Benutzer und Sitzungen zu verfolgen. Diese Informationen werden allen Ereignissen angefügt, die von der Instanz gesendet werden.

TelemetryClient ist threadsicher.



## Nachverfolgen von Ereignissen

Ereignisse können im [Metrik-Explorer][metrics] als aggregierte Anzahl angezeigt werden, und Sie können auch einzelne Vorkommen in der [Diagnosesuche][diagnostic] anzeigen.

Fügen Sie Ereignisse in Ihren Code ein, um zu zählen, wie oft sie ein bestimmtes Feature nutzen, wie oft sie bestimmte Ziele erreichen oder bestimmte Auswahlen treffen.

Senden Sie z. B. in einer Spiele-App ein Ereignis, sobald ein Benutzer das Spiel gewinnt:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


Klicken Sie auf die Kachel "Benutzerdefinierte Ereignisse" auf dem Blatt "Übersicht":

![Navigieren Sie zu Ihrer Anwendungsressource in portal.azure.com.](./media/app-insights-api-custom-events-metrics/01-custom.png)

Klicken Sie weiter, um ein Übersichtsdiagramm und eine vollständige Liste anzuzeigen.

Wählen Sie das Diagramm aus, und unterteilen Sie es nach Ereignisnamen, um die relativen Beiträge der wichtigsten Ereignisse anzuzeigen.

![Wählen Sie das Diagramm aus, und legen Sie die Gruppierung fest.](./media/app-insights-api-custom-events-metrics/02-segment.png)

Wählen Sie in der Liste unterhalb des Diagramms einen Ereignisnamen aus. Klicken Sie, um weitere einzelne Vorkommen eines Ereignisses anzuzeigen.

![Führen Sie ein Drillthrough für die Ereignisse aus.](./media/app-insights-api-custom-events-metrics/03-instances.png)

Klicken Sie auf ein beliebiges Vorkommen, um weitere Details anzuzeigen.

## <a name="properties"></a>Filtern, Suchen und Segmentieren der Daten mit Eigenschaften

Sie können Ihren Ereignissen (und auch Metriken, Seitenaufrufen und anderen Telemetriedaten) Eigenschaften und Messungen anfügen.

**Eigenschaften** sind die Zeichenfolgenwerte, die Sie zum Filtern der Telemetriedaten in den Nutzungsberichten verwenden können. Wenn zum Beispiel die Anwendung mehrere Spiele bereitstellt, sollten Sie den Namen des Spiels an jedes Ereignis anfügen, damit Sie sehen können, welche Spiele immer populärer werden.

Es gibt eine Beschränkung von ca. 1 KB für die Länge der Zeichenfolge. (Wenn Sie große Datenblöcke senden möchten, verwenden Sie den message-Parameter von [TrackTrace](#track-trace).)

**Metriken** sind numerische Werte, die grafisch dargestellt werden können. Beispiel: Sie möchten überprüfen, ob die von den Spielern erreichten Punktzahlen stetig zunehmen. Die Diagramme können anhand der mit dem Ereignis gesendeten Eigenschaften unterteilt werden, sodass Sie für verschiedene Spiele separate oder gestapelte Diagramme erhalten.

Metrikwerte müssen >=0 sein, um richtig angezeigt zu werden.

*JavaScript*

    appInsights.trackEvent // or trackPageView, trackMetric, ...
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*
    
    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());
    
    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());
    
    telemetry.trackEvent("WinGame", properties, metrics2/7/2015 12:05:25 AM );


> [AZURE.NOTE]Achten Sie darauf, keine persönlich identifizierbaren Informationen in den Eigenschaften zu protokollieren.

**Wenn Sie Metriken verwenden**, öffnen Sie den Metrik-Explorer, und wählen Sie die Metrik aus der benutzerdefinierten Gruppe aus:

![Öffnen Sie den Metrik-Explorer, wählen Sie das Diagramm aus, und wählen Sie die Metrik aus.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*Wenn die Metrik nicht angezeigt wird, schließen Sie das Blatt "Auswahl", warten Sie einen Moment, und klicken Sie auf "Aktualisieren".*

**Wenn Sie Eigenschaften und Metriken verwendet haben**, unterteilen Sie die Metrik nach der Eigenschaft:


![Legen Sie die Gruppierung fest, und wählen Sie dann unter "Gruppieren nach" die Eigenschaft aus.](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**In der Diagnosesuche** können Sie die Eigenschaften und Metriken einzelner Vorkommen eines Ereignisses anzeigen.


![Wählen Sie eine Instanz und anschließend "...".](./media/appinsights/appinsights-23-customevents-4.png)


Verwenden Sie das Suchfeld, um Ereignisvorkommen mit einem bestimmten Eigenschaftswert anzuzeigen.


![Geben Sie einen Begriff in das Suchfeld ein.](./media/appinsights/appinsights-23-customevents-5.png)

[Erfahren Sie mehr über Suchzeichenfolgen][diagnostic]

#### Alternative Methode zum Festlegen von Eigenschaften und Metriken

Wenn es für Sie praktischer ist, können Sie die Parameter eines Ereignisses in einem separaten Objekt sammeln:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);


## <a name="timed"></a> Zeitmessung bei Ereignissen

Manchmal möchten Sie im Diagramm darstellen, wie lange es dauert, eine Aktion auszuführen. Beispielsweise möchten Sie wissen, wie lange Benutzer brauchen, um die Auswahl in einem Spiel zu erwägen.

Sie können Ereignissen Zeitdaten anfügen. Verwenden Sie im Webclient diese Aufrufe, statt trackEvent aufzurufen:

*JavaScript auf Webclient*

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

Verwenden Sie die gleiche Zeichenfolge als ersten Parameter in den Start- und Stoppaufrufen.

Dieses Feature ist nicht in den anderen SDKs integriert. Sie können jedoch Ihren eigenen Code wie zum Beispiel folgenden schreiben:

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## TrackMetric

Verwenden Sie TrackMetric zum Senden von Metriken, die keinen bestimmten Ereignissen zugeordnet sind. Beispielsweise könnten Sie die Länge einer Warteschlange in regelmäßigen Abständen überwachen.

Metriken werden als statistische Diagramme im Metrik-Explorer angezeigt. Im Gegensatz zu Ereignissen können Sie jedoch nicht nach einzelnen Vorkommen in der Diagnosesuche suchen.

Metrikwerte müssen >=0 sein, um richtig angezeigt zu werden.


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

Tatsächlich können Sie dies in einem Hintergrundthread ausführen:

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Um die Ergebnisse anzuzeigen, öffnen Sie den Metrik-Explorer, und fügen Sie ein neues Diagramm hinzu. Legen Sie es für die Anzeige Ihrer Metrik fest.

![Fügen Sie ein neues Diagramm hinzu, oder wählen Sie ein Diagramm aus, und wählen Sie Ihre Metrik unter "Benutzerdefiniert" aus.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)


## Seitenaufrufe

In einer Geräte- oder Webseiten-App werden die Telemetriedaten zu den Seitenaufrufen standardmäßig gesendet, wenn die einzelnen Bildschirme oder Seiten geladen werden. Sie können dies jedoch so ändern, dass Seitenaufrufe zu zusätzlichen oder anderen verfolgt werden. Angenommen, Sie möchten in einer App mit Registerkarten oder Blättern eine "Seite" nachverfolgen, sobald der Benutzer ein neues Blatt öffnet.

![Lupe für Nutzung auf dem Blatt "Übersicht"](./media/appinsights/appinsights-47usage-2.png)

Benutzer- und Sitzungsdaten werden als Eigenschaften zusammen mit Seitenaufrufen gesendet, damit die Benutzer- und Sitzungsdiagramme aktiv werden, sobald Telemetriedaten zu den Seitenaufrufen vorliegen.

#### Benutzerdefinierte Seitenaufrufe

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Wenn Sie mehrere Registerkarten in anderen HTML-Seiten haben, können Sie ebenfalls die URL angeben:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### Zeitlich gemessene Seitenaufrufe

Durch Verwendung dieser beiden Methodenaufrufe anstelle von trackPageView können Sie analysieren, wie lange Benutzer auf Ihren Seiten verweilen.

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Verwenden Sie die gleiche Zeichenfolge als ersten Parameter in den Start- und Stoppaufrufen.

Sehen Sie sich die Metrik "Seitendauer" im [Metrik-Explorer][metrics] an.


## TrackRequest

Wird vom Server-SDK zum Protokollieren von HTTP-Anforderungen verwendet.

Sie können diese Methode auch selbst aufrufen, wenn Sie Anforderungen in einem Kontext simulieren möchten, in dem das Webdienstmodul nicht ausgeführt wird.

*C#*

    // At start of processing this request:

    // Operation Id is attached to all telemetry and helps you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetryClient.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success

## TrackException

Senden Sie Ausnahmen an Application Insights, um sie als Hinweis auf die Häufigkeit eines Problems [zu zählen][metrics] und um [einzelne Vorkommen zu untersuchen][diagnostic].

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

In mobilen Windows-Apps fängt das SDK nicht behandelte Ausnahmen ab, sodass Sie sie nicht protokollieren müssen.



## TrackTrace 

Verwenden Sie diese Methode zur Diagnose von Problemen, indem Sie eine "Brotkrümelnavigation" an Application Insights senden. Sie können Blöcke von Diagnosedaten senden und sie in der [Diagnosesuche][diagnostic] überprüfen.

 

[Protokolladapter][trace] verwenden diese API zum Senden von Drittanbieterprotokollen an das Portal.


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

Die Größenbeschränkung für `message` liegt wesentlich höher als der Grenzwert für Eigenschaften. Sie können nach Nachrichteninhalt suchen, aber (anders als bei Eigenschaftswerten) nicht danach filtern.


## Festlegen von Standardeigenschaften für alle Telemetriedaten

Sie können einen universellen Initialisierer einrichten, damit alle neuen "TelemetryClient"-Elemente automatisch Ihren Kontext verwenden.

Dies schließt die standardmäßigen Telemetriedaten ein, die von plattformspezifischen Telemetriemodulen gesendet werden, wie z. B. die Nachverfolgung von Webserveranforderungen.

*C#*

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

*Java*

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyTelemetryInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.getProperties().put("AppVersion", "2.1");
      }
    }

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyTelemetryInitializer());



## Festlegen des Instrumentationsschlüssels im Code für die gesamte Telemetrie

Statt den Instrumentationsschlüssel aus der Konfigurationsdatei abzurufen, können Sie ihn im Code festlegen. Dies bietet sich beispielsweise an, um Telemetriedaten von Testinstallationen zu einer anderen Application Insights-Ressource zu senden als Telemetriedaten von der Live-Anwendung.

Legen Sie den Schlüssel in einer Initialisierungsmethode fest, wie z. B. "global.aspx.cs" in einem ASP.NET-Dienst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 



Auf Webseiten empfiehlt es sich, ihn über den Zustand des Webservers festzulegen, anstatt ihn im Skript zu codieren. Hier zum Beispiel auf einer Webseite, die von einer ASP.NET-App generiert wird:

*JavaScript in Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="defaults"></a>Festlegen von Standardeinstellungen für ausgewählte benutzerdefinierte Telemetriedaten

Wenn Sie nur die Standardeigenschaftswerte für einige von Ihnen benutzerdefinierte Ereignisse festlegen möchten, können Sie diese in einem TelemetryClient einstellen. Sie werden jedem Telemetrieelement zugeordnet, das von diesem Client gesendet wird..

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");
    
*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);
    
    gameTelemetry.TrackEvent("WinGame");
    
Einzelne Telemetrieaufrufe können die Standardwerte in ihren Eigenschaftenwörterbüchern überschreiben.



## <a name="ikey"></a> Festlegen des Instrumentationsschlüssels für ausgewählte benutzerdefinierte Telemetriedaten

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...


## Deaktivieren der Standardtelemetrie

Sie können [ausgewählte Teile der Standardtelemetrie deaktivieren][config], indem Sie `ApplicationInsights.config` bearbeiten. Diese Vorgehensweise bietet sich z. B. an, wenn Sie Ihre eigenen TrackRequest-Daten senden möchten.

[Weitere Informationen][config].


## <a name="debug"></a>Entwicklermodus

Während des Debuggens ist es sinnvoll, die Telemetriedaten beschleunigt über die Pipeline zu senden, damit die Ergebnisse sofort angezeigt werden. Sie erhalten außerdem zusätzliche Meldungen, mit denen Sie alle Probleme mit der Telemetrie verfolgen können. Schalten Sie ihn in der Produktion aus, da er Ihre App beeinträchtigen kann.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True



## Referenz

* [ASP.NET-Referenz](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java-Referenz](http://dl.windowsazure.com/applicationinsights/javadoc/)


* *F: Gibt es eine REST-API?*

    Ja, aber sie wird noch nicht veröffentlicht.

## <a name="next"></a>Nächste Schritte


[Durchsuchen von Ereignissen und Protokollen][diagnostic]

[Problembehandlung][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58-->