<properties 
	pageTitle="Erkennen und Diagnostizieren von Fehlern und Ausnahmen in Webanwendungen" 
	description="Erkennen und Diagnostizieren von Fehlern und Ausnahmen in Webanwendungen" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="awills"/>
 
# Diagnostizieren von Fehlern und Ausnahmen in Webanwendungen mit Application Insights

[Visual Studio Application Insights][start] ist ein leistungsfähiges Tool zum Erkennen und Diagnostizieren von Fehlern in Anwendungen. Hier konzentrieren wir uns auf Webanwendungen (Sie können jedoch auch Application Insights eine [Vielzahl anderer Plattformen][platforms]).

## Richten Sie Ihre Anwendung mit Application Insights

Fügen Sie das SDK zum Anwendungsprojekt hinzu. Wenn Ihre app freigegeben und wird ausgeführt, wird das SDK Telemetriedaten über die Leistung der app an Application Insights-Portal senden.

* [Hinzufügen von Application Insights zu ASP.NET-Anwendungen][greenbrown]
* [Hinzufügen von Application Insights für Java-Anwendungen][java]

Wenn Ihre Webseiten umfangreiche Skripts haben Sie wahrscheinlich möchten: * [Application Insights zu Webseiten hinzufügen][track]


## Diagnosesuche



Öffnen Sie diagnostische suchen, um die Telemetrie angezeigt, die das SDK automatisch gesendet.

![](./media/app-insights-web-failures-exceptions/appinsights-45diagnostic.png)

![](./media/app-insights-web-failures-exceptions/appinsights-31search.png)

Die Details, aus einem Typ in einen anderen unterschiedlich. Sie können über jedes einzelne Ereignis, erhalten Sie weitere Informationen klicken.

##<a name="events"></a>Benutzerdefinierte Ereignisse

Benutzerdefinierte Ereignisse angezeigt werden, werden beide in [diagnostische Suche][diagnostic] und [Metrik Explorer][metrics]. Sie können diese von Geräten, Webseiten und Serveranwendungen senden. Sie können verwendet werden, sowohl für Diagnosezwecke und [Verstehen von Verwendungsmustern][track].

Ein benutzerdefiniertes Ereignis kann hat einen Namen, und auch Eigenschaften, die Sie zusammen mit numerischen, Filtern können.

JavaScript auf Client

    appInsights.trackEvent("WinGame",
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
    telemetry.TrackEvent("WinGame", properties, measurements);


VB auf Server

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### Ihre Anwendung ausführen und die Ergebnisse anzuzeigen.

Öffnen Sie die Diagnose suchen.

Wählen Sie benutzerdefiniertes Ereignis aus, und wählen Sie einen bestimmten Ereignisnamen.

![](./media/app-insights-web-failures-exceptions/appinsights-332filterCustom.png)


Filtern der Daten erhöhen, indem Sie einen Suchbegriff eingeben, auf einen Eigenschaftswert ab.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-5.png)

Einen Drilldown in ein einzelnes Ereignis aus, um detaillierten Eigenschaften anzuzeigen.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-4.png)



##<a name="trace"></a> Ablaufverfolgung Telemetrie

Ablaufverfolgung Telemetrie ist Code, den Sie einfügen, insbesondere um die Diagnoseprotokolle erstellen.

Beispielsweise könnten Sie beispielsweise Folgendes aufrufen einfügen:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  Installieren eines Adapters für Ihr Protokollierungsframework

Sie können auch ein Framework für die Protokollierung - generierten Protokolle suchen log4Net, NLog oder System.Diagnostics.Trace.

1. Wenn Sie log4Net oder NLog verwenden möchten, installieren Sie es in Ihrem Projekt. 
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.
3. Klicken Sie auf "Online > Alle", wählen Sie **Vorabversion einschließen** aus, und suchen Sie nach "Microsoft.ApplicationInsights".

    ![Vorabversion des entsprechenden Adapters auswählen](./media/app-insights-web-failures-exceptions/appinsights-36nuget.png)

4. Wählen Sie das entsprechende Paket aus:
  + Microsoft.ApplicationInsights.TraceListener (zum Erfassen von System.Diagnostics.Trace-Aufrufen)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Das NuGet-Paket installiert die erforderlichen Assemblys und ändert auch die Datei "web.config" oder "app.config".

#### <a name="pepper"></a>Aufrufe des Diagnoseprotokolls einfügen

Wenn Sie System.Diagnostics.Trace verwenden, wäre ein typischer Aufruf:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Wenn Sie log4net oder NLog bevorzugen:

    logger.Warn("Slow response - database01");

Ihre app im Debugmodus ausgeführt, oder es bereitstellen.

Sehen Sie die Nachrichten in der Diagnose Suche bei der Auswahl des Ablaufverfolgungsfilters.

### <a name="exceptions"></a>Ausnahmen

Abrufen von Berichten für die Ausnahme in Application Insights bietet die leistungsstarke besonders da können Sie fehlgeschlagenen Anforderungen sowie die Ausnahmen zu navigieren und den Ausnahmestapel lesen.

Sie können den Code zum Senden der Ausnahme Telemetrie schreiben:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Die Eigenschaften und Messparameter sind optional, sind aber hilfreich zum Filtern und Hinzufügen von zusätzlichen Informationen. Wenn Sie z. B. eine Anwendung haben, die mehrere Spiele ausführen kann, können Sie alle im Zusammenhang mit einem bestimmten Spiel stehenden Ausnahmeberichte ermitteln. Sie können jedem Wörterbuch beliebig viele Elemente hinzufügen.

#### Anzeigen von Ausnahmen

Sehen Sie eine Zusammenfassung der Ausnahmen, die auf das Fenster "Übersicht", und Sie können durch Klicken, um weitere Details anzuzeigen. Beispiel:


![](./media/app-insights-web-failures-exceptions/appinsights-039-1exceptions.png)[]

Klicken Sie auf jeden Ausnahmetyp an bestimmte Instanzen finden Sie unter:

![](./media/app-insights-web-failures-exceptions/appinsights-333facets.png)[]

Sie können auch diagnostische Suche direkt öffnen, auf Ausnahmen filtern, und wählen Sie den Typ der Ausnahme, den Sie anzeigen möchten.

### Nicht behandelte Ausnahmen

Application Insights-Berichten nicht behandelte Ausnahmen, wo er kann, von Geräten, [Webbrowser][usage], oder Webserver, ob instrumentiert durch [Statusmonitor][redfield] oder [Application Insights-SDK][greenbrown].

> [AZURE.NOTE]In einem Webbrowser an. Wenn Sie die Skriptdateien aus CDNs oder andere Domänen angeben, stellen Sie sicher Script-Tag das Attribut hat ```crossorigin="anonymous"``` und der Server sendet die CORS-Header, um eine Stapelüberwachung und Details für nicht behandelte Javascript-Execptions von diesen Ressourcen zu erhalten.

Allerdings ist es nicht immer in einigen Fällen möglich, weil .NET Framework die Ausnahmen abgefangen werden. Stellen Sie sicher, dass Sie alle Ausnahmen angezeigt, müssen Sie daher einen kleinen Ausnahmehandler zu schreiben. Das beste Verfahren variiert mit der Technologie. Finden Sie in [diesen Blog](http://blogs.msdn.com/b/visualstudioalm/archive/2014/12/12/application-insights-exception-telemetry.aspx) Details.

### Korrelieren von einem Build

Wenn Sie Diagnoseprotokolle lesen, ist es wahrscheinlich, dass der Quellcode geändert werden, da der live-Code bereitgestellt wurde.

Daher ist es hilfreich, die Buildinformationen, z. B. die URL der aktuellen Version, einer Eigenschaft und jede Ausnahme oder Ablaufverfolgung abgelegt.

Anstatt die Eigenschaft wird in jedem Aufruf der Ausnahme separat hinzuzufügen, können Sie die Informationen in den Standardkontext festlegen.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

Gehen Sie im App-Initialisierer wie z. B. "Global.asax.cs" so vor:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a> Web-Serveranforderungen

Anfordern der Telemetrie wird automatisch gesendet, wenn Sie [Statusmonitor auf Ihrem Webserver installieren][redfield], oder wenn Sie [Application Insights zum Webprojekt hinzufügen][greenbrown]. Diese feeds auch in den Diagrammen zu Anforderungs- und Antwortnachrichten Zeit im Metrik-Explorer, und klicken Sie auf der Seite "Übersicht".

Wenn Sie zusätzliche Ereignisse senden möchten, können Sie die TrackRequest()-API.

## <a name="questions"></a>FRAGEN UND ANTWORTEN

### <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.

Anscheinend haben Sie das Protokollierungsadapter-Nuget-Paket installiert, ohne Application Insights zu installieren.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt. Damit sollte das Problem behoben sein.

### <a name="limits"></a>Wie viele Daten werden beibehalten?

Bis zu 500 Ereignisse pro Sekunde für jede Anwendung. Ereignisse werden sieben Tage lang aufbewahrt.

## <a name="add"></a>Nächste Schritte

* [Einrichten von Tests der Verfügbarkeit und Reaktionsfähigkeit][availability]
* [Problembehandlung][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[platforms]: app-insights-platforms.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->