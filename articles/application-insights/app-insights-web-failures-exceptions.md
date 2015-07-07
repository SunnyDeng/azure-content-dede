<properties 
	pageTitle="Erkennen und Diagnostizieren von Fehlern und Ausnahmen in Web-Apps" 
	description="Erkennen und Diagnostizieren von Fehlern und Ausnahmen in Web-Apps" 
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
 
# Diagnostizieren von Fehlern und Ausnahmen in Web-Apps mit Application Insights

[Visual Studio Application Insights][start] ist ein leistungsstarkes Tool zum Erkennen und Diagnostizieren von Fehlern in Anwendungen. An dieser Stelle geht es um Webanwendungen (Sie können Application Insights jedoch auch auf eine [Vielzahl anderer Plattformen][platforms] anwenden.)

## Einrichten Ihrer App mit Application Insights

Fügen Sie das SDK zu Ihrem Anwendungsprojekt hinzu. Wenn Ihre App veröffentlicht wurde und ausgeführt wird, sendet das SDK Telemetriedaten zur Leistung der App an das Application Insights-Portal.

* [Hinzufügen von Application Insights zu ASP.NET-Apps][greenbrown]
* [Hinzufügen von Application Insights zu Java-Apps][java]

Wenn Ihre Webseiten umfangreiche Skripts enthalten, sollten Sie auch dieses Thema lesen: * [Hinzufügen von Application Insights zu Webseiten][track].


## Diagnosesuche



Öffnen Sie die Diagnosesuche, um die Telemetriedaten anzuzeigen, die das SDK automatisch sendet.

![](./media/app-insights-web-failures-exceptions/appinsights-45diagnostic.png)

![](./media/app-insights-web-failures-exceptions/appinsights-31search.png)

Die Details unterscheiden sich je nach Anwendungstyp. Sie können auf jedes einzelne Ereignis klicken, um weitere Informationen zu erhalten.

##<a name="events"></a>Benutzerdefinierte Ereignisse

Benutzerdefinierte Ereignisse werden sowohl in der [Diagnosesuche][diagnostic] als auch im [Metrik-Explorer][metrics] angezeigt. Diese Ereignisse können von Geräten, Webseiten und Serveranwendungen gesendet werden. Sie können für Diagnosezwecke und zum [Verstehen von Nutzungsmustern][track] verwendet werden.

Ein benutzerdefiniertes Ereignis hat einen Namen und kann auch Eigenschaften besitzen, nach denen Sie filtern können, sowie über numerische Messwerte verfügen.

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

### Führen Sie Ihre App aus, und zeigen Sie die Ergebnisse an.

Öffnen Sie die Diagnosesuche.

Wählen Sie ein benutzerdefiniertes Ereignis und den Namen eines bestimmten Ereignisses aus.

![](./media/app-insights-web-failures-exceptions/appinsights-332filterCustom.png)


Filtern Sie die Daten genauer, indem Sie einen Suchbegriff für einen Eigenschaftswert eingeben.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-5.png)

Zeigen Sie Detailinformationen zu den Eigenschaften eines einzelnen Ereignisses an.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-4.png)



##<a name="trace"></a> Verfolgen von Telemetriedaten

Bei der Ablaufverfolgung von Telemetriedaten handelt es sich um Code, den Sie speziell zum Erstellen von Diagnoseprotokollen einfügen.

Sie können beispielsweise Aufrufe wie die folgenden einfügen:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  Installieren eines Adapters für Ihr Protokollierungsframework

Sie können auch Protokolle durchsuchen, die mit einem Protokollierungsframework wie z. B. log4Net, NLog oder System.Diagnostics.Trace generiert wurden.

1. Wenn Sie log4Net oder NLog verwenden möchten, installieren Sie es in Ihrem Projekt. 
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.
3. Klicken Sie auf "Online > Alle", wählen Sie **Vorabversion einschließen** aus, und suchen Sie nach "Microsoft.ApplicationInsights".

    ![Vorabversion des entsprechenden Adapters auswählen](./media/app-insights-web-failures-exceptions/appinsights-36nuget.png)

4. Wählen Sie das entsprechende Paket aus:
  + Microsoft.ApplicationInsights.TraceListener (zum Erfassen von System.Diagnostics.Trace-Aufrufen)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Das NuGet-Paket installiert die erforderlichen Assemblys und ändert auch die Datei "web.config" oder "app.config".

#### <a name="pepper"></a>Einfügen von Diagnoseprotokollaufrufen

Wenn Sie System.Diagnostics.Trace verwenden, wäre ein typischer Aufruf:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Wenn Sie log4net oder NLog bevorzugen:

    logger.Warn("Slow response - database01");

Führen Sie Ihre App im Debugmodus aus, oder stellen Sie sie bereit.

Sie sehen die Nachrichten in der Diagnosesuche, wenn Sie den Ablaufverfolgungsfilter auswählen.

### <a name="exceptions"></a>Ausnahmen

Das Abrufen von Ausnahmeberichten in Application Insights bietet eine Reihe von Vorteilen, insbesondere, da Sie zwischen den Anforderungen mit Fehlern und den Ausnahmen navigieren und den Ausnahmestapel lesen können.

Sie können auch Code zum Senden von Telemetriedaten für Ausnahmen schreiben:

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

Auf dem Blatt "Übersicht" sehen Sie eine Zusammenfassung der gemeldeten Ausnahmen, und Sie können auf diese Ausnahmen klicken, um weitere Details anzuzeigen. Zum Beispiel:


![](./media/app-insights-web-failures-exceptions/appinsights-039-1exceptions.png)

Klicken Sie auf einen Ausnahmetyp, um die jeweiligen Vorkommen anzuzeigen:

![](./media/app-insights-web-failures-exceptions/appinsights-333facets.png)

Sie können auch die Diagnosesuche direkt öffnen, nach Ausnahmen filtern und den Ausnahmetyp auswählen, den Sie anzeigen möchten.

### Berichterstellung für Ausnahmefehler

Application Insights meldet Ausnahmefehler – wann immer es möglich ist – von Geräten, [Webbrowsern][usage] oder Webservern, unabhängig davon, ob diese vom [Statusmonitor][redfield] oder dem [Application Insights SDK][greenbrown] ermittelt wurden.

> [AZURE.NOTE]Für Webbrowser gilt: Wenn Sie Skriptdateien aus CDNs oder anderen Domänen einfügen, stellen Sie sicher, dass das Skripttag das Attribut ```crossorigin="anonymous"``` umfasst und der Server CORS-Header sendet, um Stapelüberwachungsinformationen sowie Details zu JavaScript-Ausnahmefehlern aus diesen Ressourcen abzurufen.

In einigen Fällen ist dies jedoch nicht möglich, da die Ausnahmen vom .NET Framework aufgefangen werden. Um sicherzustellen, dass Ihnen alle Ausnahmen angezeigt werden, müssen Sie daher einen kleinen Ausnahmehandler schreiben. Welches Verfahren sich am besten eignet, variiert je nach Technologie. Informationen hierzu finden Sie in [diesem Blog](http://blogs.msdn.com/b/visualstudioalm/archive/2014/12/12/application-insights-exception-telemetry.aspx).

### Korrelation mit einem Build

Wenn Sie Diagnoseprotokolle lesen, ist es wahrscheinlich, dass der Quellcode seit Bereitstellung des Livecodes geändert wurde.

Daher ist es sinnvoll, Buildinformationen wie beispielsweise die URL der aktuellen Version sowie alle zu verfolgenden Ausnahmen in eine Eigenschaft einzufügen.

Anstatt diese Eigenschaft separat zu jedem Ausnahmeaufruf hinzuzufügen, können Sie die Informationen im Standardkontext festlegen.

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

###<a name="requests"></a> Webanforderungen des Servers

Telemetriedaten zu Anforderungen werden automatisch gesendet, wenn Sie [den Statusmonitor auf Ihrem Webserver installieren][redfield] oder [Application Insights zu Ihrem Webprojekt hinzufügen][greenbrown]. Diese Daten werden ebenfalls in die Diagramme zu Anforderungs- und Antwortzeiten im Metrik-Explorer und auf der Übersichtsseite übertragen.

Wenn Sie zusätzliche Ereignisse senden möchten, können Sie die TrackRequest()-API verwenden.

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

 

<!---HONumber=62-->