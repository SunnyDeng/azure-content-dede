<properties title="Search diagnostic logs with Application Insights" pageTitle="Durchsuchen der Diagnoseprotokolle" description="Suchen Sie nach mit der Ablaufverfolgung, NLog oder Log4Net generierten Protokollen." metaKeywords="analytics web test" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Diagnosesuche in Application Insights

Eine der bewährtesten Debuggingmethoden besteht im Einfügen von Codezeilen, die ein Ablaufverfolgungsprotokoll ausgegeben. [Application Insights][start] kann Webserverprotokolle erfassen und Sie beim Suchen und Filtern unterstützen. Wenn Sie log4Net, NLog oder System.Diagnostics.Trace bereits verwenden, können Sie diese Protokolle mit unserem Adapter erfassen. Oder Sie können die TrackTrace- und TrackException-Methoden verwenden, die in das Application Insights-SDK integriert sind.

Die Suchergebnisse können auch die reguläre Seitenansicht und Anforderungsereignisse enthalten, mit denen die [Nutzungs-][usage] und [Leistungsberichte][perf] zusammen mit geschriebenen [benutzerdefinierten TrackEvent-Aufrufen][track] erstellt werden.


2. [Installieren Sie einen Adapter für das Protokollierungsframework?](#capture)
+ [Einfügen von Diagnoseprotokollaufrufen](#pepper)
+ [Ausnahmen](#exceptions)
+ [Anzeigen von Protokolldaten](#view)
+ [Durchsuchen von Protokolldaten](#search)
+ [Problembehandlung](#questions)
+ [Nächste Schritte](#next)



## <a name="capture"></a> Installieren Sie einen Adapter für das Protokollierungsframework?

Wenn Sie [Application Insights noch nicht in Ihrem Projekt installiert haben][start], holen Sie das jetzt nach.

Wenn Sie die integrierten Application Insights SDK Track*()-Aufrufe verwenden, brauchen Sie keinen Adapter - [fahren Sie mit dem nächsten Abschnitt fort](#pepper).

Installieren Sie den entsprechenden Adapter, um Protokolle zu durchsuchen, die mit log4Net, NLog oder System.Diagnostics.Trace generiert wurden:

1. Wenn Sie log4Net oder NLog verwenden möchten, installieren Sie es in Ihrem Projekt. 
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Manage NuGet Packages** aus.
3. Wählen Sie "Online > All" aus, wählen Sie **Include Prerelease** aus, und suchen Sie nach "Microsoft.ApplicationInsights".

    ![Get the prerelease version of the appropriate adapter](./media/appinsights/appinsights-36nuget.png)

4. Wählen Sie das entsprechende Paket aus:
  + Microsoft.ApplicationInsights.TraceListener (zum Erfassen von System.Diagnostics.Trace-Aufrufen)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Das NuGet-Paket installiert die erforderlichen Assemblys und ändert auch die Datei "web.config" oder "app.config".

## <a name="pepper"></a>3. Einfügen von Diagnoseprotokollaufrufen

Fügen Sie Ereignisprotokollierungsaufrufe mithilfe Ihres bevorzugten Protokollframeworks ein. 

Wenn Sie z. B. das Application Insights-SDK verwenden, können Sie Folgendes einfügen:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Oder wenn Sie System.Diagnostics.Trace verwenden:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Wenn Sie log4net oder NLog bevorzugen:

    logger.Warn("Slow response - database01");

Führen Sie Ihre App im Debugmodus aus oder stellen Sie sie auf dem Webserver bereit.

### <a name="exceptions"></a>Ausnahmen

So senden Sie Ausnahmen an das Protokoll

JavaScript auf Client

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C# auf Server

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

VB auf Server

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

## <a name="view"></a>4. Anzeigen von Protokolldaten


1. Öffnen Sie in Application Insights die Diagnosesuche.

    ![Open diagnostic search](./media/appinsights/appinsights-30openDiagnostics.png)
   
2. Legen Sie den Filter für die Ereignistypen fest, die Sie anzeigen möchten.

    ![Open diagnostic search](./media/appinsights/appinsights-331filterTrace.png)


Die Ereignistypen sind:

* **Ablaufverfolgung** - Durchsuchen Sie Diagnoseprotokolle, die Sie von Ihrem Webserver aufgezeichnet haben. Dazu gehören log4Net-, NLog-, System.Diagnostic.Trace- und ApplicationInsights TrackTrace-Aufrufe.
* **Anforderung** - Suchen Sie HTTP-Anforderungen, die durch die Serverkomponente der Webapp empfangen wurden, einschließlich Seitenanforderungen, Datenanforderungen, Bilder usw. Die Ereignisse, die Sie sehen, entsprechen der Telemetrie, die vom Application Insights-Server-SDK gesendet wurde, und werden verwendet, um den Bericht zur Anforderungsanzahl zu erstellen.
* **Seitenansicht** - Durchsuchen Sie Seitenansichtsereignisse. Diese Ereignisse werden vom Webvlient gesendet und werden verwendet, um Seitenansichtsberichte zu erstellen. (Falls hier nichts angezeigt wird, richten Sie die [Webclientüberwachung][usage] ein.)
* **Benutzerdefiniertes Ereignis** - Wenn Sie Aufrufe von TrackEvent() und TrackMetric() in die [Nutzungsüberwachung][track] eingefügt haben, können Sie diese hier durchsuchen.

Wählen Sie ein Protokollereignis aus, um Details dazu anzuzeigen. 

![Open diagnostic search](./media/appinsights/appinsights-32detail.png)

Sie können Klartext-Zeichenfolgen (ohne Platzhalter) verwenden, um die Felddaten in einem Objekt zu filtern.

Die verfügbaren Felder hängen vom Protokollframework und den im Aufruf verwendeten Parametern ab.


## <a name="search"></a>5. Durchsuchen der Daten

Legen Sie einen Zeitbereich fest und suchen Sie nach Begriffen. Das Suchen innerhalb eines kürzeren Zeitbereichs geht schneller. 

![Open diagnostic search](./media/appinsights/appinsights-311search.png)

Beachten Sie, dass Sie nach Begriffen suchen, nicht nach Teilzeichenfolgen. Begriffe sind alphanumerische Zeichenfolgen, die Zeichen wie '.' und '_' enthalten können. Beispiel:

<table>
  <tr><th>Begriff</th><th>entspricht NICHT</th><th>, aber diese entsprechen</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

Dies sind einige Suchausdrücke, die Sie verwenden können:

<table>
                    <tr>
                      <th>
                        <p>Beispielabfrage</p>
                      </th>
                      <th>
                        <p>Effekt</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">langsam</span>
                        </p>
                      </td>
                      <td>
                        <p>Findet alle Ereignisse im Datumsbereich, deren Felder den Begriff "langsam" enthalten</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">Datenbank??</span>
                        </p>
                      </td>
                      <td>
                        <p>Findet Datenbank01, DatenbankAB, ...</p>
                        <p>? ist zu Beginn eines Suchbegriffs nicht zulässig.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">Datenbank*</span>
                        </p>
                      </td>
                      <td>
                        <p>Findet Datenbank, Datenbank01, DatenbankNNNN</p>
                        <p>* ist zu Beginn eines Suchbegriffs nicht zulässig.</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">Apfel UND Banane</span>
                        </p>
                      </td>
                      <td>
                        <p>Findet Ereignisse, die beide Begriffe enthalten. Verwenden Sie "AND" in Großbuchstaben, nicht "and".</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">Apfel OR Banane</span>
                        </p>
                        <p>
                          <span class="code">Apfel Banane</span>
                        </p>
                      </td>
                      <td>
                        <p>Findet Ereignisse, die einen der beiden Begriffe enthalten. Verwenden Sie "OR" in Großbuchstaben, nicht "or".</p>
                        <p>Kurzform.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">Apfel NOT Banane</span>
                        </p>
                        <p>
                          <span class="code">Apfel -Banane</span>
                        </p>
                      </td>
                      <td>
                        <p>Findet Ereignisse, die den einen Begriff enthalten, aber nicht den anderen.</p>
                        <p>Kurzform.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>Apf* AND Banane NOT (Traube OR Birne)</p>
                        <p>
                          <span class="code">Apf* AND Banane -(Traube Birne)</span>
                        </p>
                      </td>
                      <td>
                        <p>Logische Operatoren und Klammern.</p>
                        <p>Verkürzte Form.</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:langsam</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>Übereinstimmung mit dem angegebenen Feld ist. Standardmäßig werden alle Felder durchsucht. Um zu sehen, welche Felder verfügbar sind, rufen Sie die Details für ein Ereignis auf.</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Fragen und Antworten

### <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.

Anscheinend haben Sie das Protokollierungsadapter-Nuget-Paket installiert, ohne Application Insights zu installieren.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder zur Wiederverwendung einer vorhandenen Ressource einlädt. Damit sollte das Problem behoben sein.

### <a name="limits"></a>Wie viele Daten werden beibehalten?

Bis zu 500 Ereignisse pro Sekunde für jede Anwendung. Ereignisse werden sieben Tage lang aufbewahrt.

### <a name="cani"></a>Kann ich ...?

- Warnungen für Ereignisse und Ausnahmen festlegen
- Protokolle zur weiteren Analyse exportieren
- Nach bestimmten Eigenschaften suchen

Noch nicht, aber alle diese Funktionen sind im Backlog.

## <a name="add"></a>Nächste Schritte

* [Einrichten von Tests zu Verfügbarkeit und Reaktionszeit][availability]
* [Problembehandlung][qna]





[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




