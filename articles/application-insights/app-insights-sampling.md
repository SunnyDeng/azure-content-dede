<properties 
	pageTitle="Stichprobenerstellung für Telemetriedaten in Application Insights" 
	description="So behalten Sie die Kontrolle über die Menge an erfassten Telemetriedaten." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>

#  Erstellen von Stichproben in Application Insights

*Application Insights befindet sich in der Vorschau.*


Das Erstellen von Stichproben ist ein Feature in Application Insights, mit der Sie einen eingeschränkten Satz an Telemetriedaten erfassen und speichern können, während gleichzeitig eine statistisch korrekte Analyse der Anwendungsdaten sichergestellt wird. Es verringert den Datenverkehr und hilft, [Drosselungen](app-insights-pricing.md#data-rate) zu vermeiden. Die Daten werden so gefiltert, dass verwandte Elemente übertragen werden und Sie beim Ausführen diagnostischer Untersuchungen zwischen Elementen navigieren können. Bei der Anzeige der Metrikergebnisse im Portal werden diese erneut normalisiert, um der Stichprobenerstellung Rechnung zu tragen und Auswirkungen auf die Statistiken zu minimieren.

Im Application Insights SDK für ASP.NET, Version 2.0.0-beta3 oder später, ist die adaptive Stichprobenerstellung standardmäßig aktiviert. Für das Erstellen von Stichproben ist gegenwärtig die Betaversion verfügbar. Für die Zukunft ist eine Überarbeitung dieser Version geplant.

Es gibt zwei alternative Module zur Stichprobenerstellung:

* Bei der adaptiven Stichprobenerstellung wird der Stichproben-Prozentsatz automatisch angepasst, um ein bestimmtes Volumen an Anforderungen zu erreichen. Derzeit nur für serverseitige Telemetrie bei ASP.NET verfügbar.  
* Eine Stichprobenerstellung mit festem Prozentsatz steht ebenfalls zur Verfügung. Dabei geben Sie den Stichproben-Prozentsatz an. Verfügbar für ASP.NET-Web-App-Code und JavaScript-Webseiten. Client und Server synchronisieren ihre Stichprobenerstellung, sodass Sie in der Suche zwischen den verwandten Seitenaufrufen und Anforderungen navigieren können.

## Aktivieren der adaptiven Stichprobenerstellung

**Aktualisieren Sie die NuGet-Pakete Ihres Projekts** auf die neueste *Vorabversion* von Application Insights: Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, wählen Sie „NuGet-Pakete verwalten“ aus, aktivieren Sie **Vorabversion einschließen**, und suchen Sie nach „Microsoft.ApplicationInsights.Web“.

In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) können Sie eine Reihe von Parametern im `AdaptiveSamplingTelemetryProcessor`-Knoten anpassen. Die folgenden Zahlen veranschaulichen die Standardwerte:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    Der Zielprozentsatz, den der Adaptionsalgorithmus **auf einem einzelnen Serverhost** zu erreichen versucht. Wenn Ihre Web-App auf mehreren Hosts ausgeführt wird, sollten Sie diesen Wert reduzieren, damit Sie innerhalb des Zielrate für den Datenverkehr im Application Insights-Portal bleiben.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    Das Intervall, mit dem die aktuelle Telemetrierate erneut ausgewertet wird. Die Auswertung wird als gleitender Durchschnitt ausgeführt. Wenn bei Ihrer Telemetrie plötzliche Spitzen auftreten, sollten Sie dieses Intervall verkürzen.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    Gibt an, nach welchem Zeitraum der Stichproben-Prozentsatz verringert werden darf, um weniger Daten zu erfassen, wenn sich der Wert für den Stichproben-Prozentsatz geändert hat.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageDecreaseTimeout>`

    Gibt an, nach welchem Zeitraum der Stichproben-Prozentsatz erhöht werden darf, um mehr Daten zu erfassen, wenn sich der Wert für den Stichproben-Prozentsatz geändert hat.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    Gibt den minimal zulässigen Wert an, der bei variierendem Stichproben-Prozentsatz festgelegt werden darf.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    Gibt den maximal zulässigen Wert an, der bei variierendem Stichproben-Prozentsatz festgelegt werden darf.

* `<MovingAverageRatio>0.25</MovingAverageRatio>`

    Die Gewichtung, die bei der Berechnung des gleitenden Durchschnitts dem jüngsten Wert beigemessen wird. Verwenden Sie einen Wert kleiner oder gleich 1. Bei einem kleineren Wert reagiert der Algorithmus langsamer auf plötzliche Veränderungen.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    Der Wert, der sofort nach dem Start der App zugewiesen wird. Reduzieren Sie ihn nicht, solange Sie debuggen.

### Alternative: Konfigurieren der adaptiven Stichprobenerstellung in Code

Anstatt die Stichproben in der config-Datei anzupassen, können Sie Code verwenden. Dadurch können Sie eine Rückruffunktion angeben, die aufgerufen wird, wenn die Abtastrate neu ausgewertet wird. Z. B. können Sie herausfinden, welche Samplingrate verwendet wird.

Entfernen Sie den `AdaptiveSamplingTelemetryProcessor`-Knoten aus der .config-Datei.



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Informieren Sie sich über Telemetrieprozessoren](app-insights-api-filtering-sampling.md#filtering).)


<a name="other-web-pages"></a>
## Erstellen von Stichproben für Webseiten mit JavaScript

Sie können einen beliebigen Server verwenden, um Webseiten für die Erstellung von Stichproben mit festem Prozentsatz zu konfigurieren.

Ändern Sie beim [Konfigurieren der Webseiten für Application Insights](app-insights-javascript.md) den Codeausschnitt, den Sie vom Application Insights-Portal erhalten. (In ASP.NET-Apps wird der Codeausschnitt normalerweise in \_Layout.cshtml eingefügt.) Fügen Sie vor dem Instrumentierungsschlüssel eine Zeile wie `samplingPercentage: 10,` ein:

    <script>
	var appInsights= ... 
	}({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist). Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.

Wenn Sie auch auf dem Server die Stichprobenerstellung mit festem Prozentsatz aktivieren, wird die Stichprobenerstellung zwischen Client und Server synchronisiert, sodass Sie in der Suche zwischen den verwandten Seitenaufrufen und Anforderungen navigieren können.


## Aktivieren der Stichprobenerstellung mit festem Prozentsatz auf dem Server

1. **Aktualisieren Sie die NuGet-Pakete Ihres Projekts** auf die neueste *Vorabversion* von Application Insights. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, wählen Sie „NuGet-Pakete verwalten“ aus, aktivieren Sie **Vorabversion einschließen**, und suchen Sie nach „Microsoft.ApplicationInsights.Web“. 

2. **Deaktivieren Sie die adaptive Stichprobenerstellung**: Entfernen Sie in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) den Knoten `AdaptiveSamplingTelemetryProcessor`, oder kommentieren Sie ihn aus.

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **Aktivieren Sie das Modul für die Stichprobenerstellung mit festem Prozentsatz.** Fügen Sie in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) diesen Codeausschnitt hinzu:

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE]Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist). Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.



### Alternative: Aktivieren der Stichprobenerstellung mit festem Prozentsatz im Servercode


Anstatt den Stichprobenerstellungsparameter in der config-Datei festzulegen, können Sie Code verwenden.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Informieren Sie sich über Telemetrieprozessoren](app-insights-api-filtering-sampling.md#filtering).)

## Wann sollten Sie die Stichprobenerstellung nutzen?

Die adaptive Stichprobenerstellung ist automatisch aktiviert, wenn Sie das ASP.NET-SDK ab Version 2.0.0-beta3 verwenden.

Für die meisten kleinen und mittelgroßen Anwendungen ist keine Stichprobenerstellung erforderlich. Die nützlichsten Diagnoseinformationen und präzisesten Statistiken werden abgerufen, indem Sie Daten zu sämtlichen Benutzeraktivitäten erfassen.

 
Die Hauptvorteile von Stichproben sind:

* Der Application Insights-Dienst senkt („drosselt“) Datenpunkte, wenn Ihre App in kurzen Abständen eine große Menge an Telemetriedaten sendet. 
* Sie können das [Kontingent](app-insights-pricing.md) an Datenpunkten besser einhalten, das für Ihren Tarif vorgesehen ist. 
* Der Netzwerk-Datenverkehr, der durch die Erfassung von Telemetriedaten verursacht wird, wird reduziert. 

### Stichprobenerstellung mit festem Prozentsatz oder adaptive Stichprobenerstellung?

Verwenden Sie die Stichprobenerstellung mit festem Prozentsatz unter folgenden Bedingungen:

* Sie möchten die Stichprobenerstellung zwischen Client und Server synchronisieren, sodass Sie beim Untersuchen von Ereignissen in der [Suche](app-insights-diagnostic-search.md) zwischen verwandten Ereignissen auf dem Client und dem Server navigieren können, z. B. zwischen Seitenaufrufen und HTTP-Anforderungen.
* Sie sind sich sicher, welcher Stichproben-Prozentsatz für Ihre App angemessen ist. Er sollte hoch genug sein, um genaue Metriken zu erhalten, aber so tief liegen, dass Sie Ihr Tarifkontingent und die Drosselungslimits einhalten. 
* Sie debuggen Ihre App nicht. Wenn Sie F5 drücken und einige Seiten Ihrer App testen, empfiehlt es sich, die gesamte Telemetrie einsehen zu können.

In allen anderen Fällen empfehlen wir die adaptive Stichprobenerstellung.

## Wie funktioniert die Stichprobenerstellung?

Aus Anwendungssicht ist die Erstellung von Stichproben ein Feature des Application Insights SDK. Sie geben an, welcher Prozentsatz aller Datenpunkte an den Application Insights-Dienst gesendet werden soll. Ab Version 2.0.0 des Application Insights SDK können Sie den Prozentsatz für die Stichprobenerstellung über Ihren Code steuern. (In zukünftigen Versionen des SDK wird es auch möglich sein, den Prozentsatz für die Stichprobenerstellung über die Datei „ApplicationInsights.config“ zu konfigurieren.)

Das SDK entscheidet, welche Telemetrieelemente verworfen und welche beibehalten werden. Diese Entscheidung im Hinblick auf die Stichprobenerstellung wird basierend auf verschiedenen Regeln getroffen, mit denen sichergestellt wird, dass alle zusammenhängenden Datenpunkte intakt bleiben. Dadurch sind die in Application Insights durchgeführten Diagnosen auch bei einer geringeren Datenmenge zuverlässig und ermöglichen es Ihnen, die erforderlichen Maßnahmen zu ergreifen. Wenn Ihre App beispielsweise zusätzliche Telemetrieelemente für eine Anforderung sendet, bei der ein Fehler aufgetreten ist (z. B. Ausnahmen und Ablaufverfolgungen, die für diese Anforderung protokolliert wurden), werden diese Anforderung und andere Telemetriedaten bei der Stichprobenerstellung nicht getrennt. Die Daten werden entweder vollständig beibehalten oder vollständig verworfen. Wenn Sie die Anforderungsdetails in Application Insights anzeigen, wird die Anforderung folglich immer mit den zugehörigen Telemetrieelementen angezeigt.

Bei Anwendungen, in denen Benutzer definiert sind (also die meisten typischen Webanwendungen), basiert die Entscheidung im Zusammenhang mit der Stichprobenerstellung auf dem Hash der Benutzer-ID. Das heißt, dass die Telemetriedaten für einen bestimmten Benutzer entweder vollständig beibehalten oder vollständig verworfen werden. Bei Anwendungen, in denen keine Benutzer definiert sind (z. B. Webdienste), basiert die Entscheidung bezüglich der Stichprobenerstellung auf der Vorgangs-ID der Anforderung. Für Telemetrieelemente, für die weder ein Benutzer noch eine Vorgangs-ID festgelegt wurde (z. B. Telemetrieelemente aus asynchronen Threads ohne HTTP-Kontext), wird bei der Stichprobenerstellung ein Prozentsatz von Telemetrieelementen der einzelnen Typen erfasst.

Wenn Ihnen die Telemetriedaten angezeigt werden, passt der Application Insights-Dienst die Metriken basierend auf dem Prozentsatz der Stichprobenerstellung an, der bei der Erfassung der Daten verwendet wurde. Auf diese Weise werden die fehlenden Datenpunkte kompensiert. Bei Betrachtung der Telemetriedaten in Application Insights werden folglich statistisch korrekte Annäherungen angezeigt, die den reellen Zahlen sehr nahe kommen.

Die Genauigkeit der Annäherung hängt weitgehend vom konfigurierten Prozentsatz für die Stichprobenerstellung ab. Zudem steigt die Genauigkeit bei Anwendungen, die eine große Anzahl von im Allgemeinen ähnlichen Anforderungen von einer großen Anzahl von Benutzern verarbeiten. Andererseits ist bei Anwendungen mit geringer Last keine Stichprobenerstellung erforderlich, da diese Anwendungen üblicherweise sämtliche Telemetriedaten senden können, ohne dass dabei das jeweilige Kontingent überschritten wird oder es durch die Drosselung zu Datenverlust kommt.

Beachten Sie, dass Application Insights keine Stichproben für die Telemetrietypen „Metriken“ und „Sitzungen“ erstellt, da bei diesen Typen eine Reduzierung der Genauigkeit nicht wünschenswert ist.

### Adaptive Stichprobenerstellung

Bei der adaptiven Stichprobenerstellung wird eine Komponente hinzugefügt, die die aktuelle Übertragungsrate vom SDK überwacht und den Stichproben-Prozentsatz anpasst, um den angestrebten maximalen Prozentsatz einzuhalten. Die Anpassung wird in regelmäßigen Intervallen neu berechnet und basiert auf dem gleitenden Durchschnitt der ausgehenden Übertragungsrate.

## Stichprobenerstellung und das JavaScript SDK

Das clientseitige (JavaScript) SDK führt die Stichprobenerstellung in Verbindung mit dem serverseitigen SDK aus. Die instrumentierten Seiten senden clientseitige Telemetriedaten lediglich von den Benutzern, für die serverseitig die Entscheidung zur Stichprobenerstellung getroffen wurde. Mithilfe dieser Logik wird die Integrität der Benutzersitzung auf Client- und Serverseite sichergestellt. So können Sie für ein bestimmtes Telemetrieelement in Application Insights nach allen anderen Telemetrieelementen für den jeweiligen Benutzer oder die jeweilige Sitzung suchen.

*Meine client- und serverseitigen Telemetriedaten weisen keine koordinierten Stichproben auf.*

* Überprüfen Sie, ob Sie die Stichprobenerstellung sowohl auf dem Server als auch auf dem Client aktiviert haben.
* Stellen Sie sicher, dass Sie die SDK-Version 2.0 oder höher verwenden.
* Stellen Sie sicher, dass Sie auf Client und Server denselben Prozentsatz für die Stichprobenerstellung festgelegt haben.


## Häufig gestellte Fragen 

*Weshalb erfolgt die Stichprobenerstellung nicht einfach nach dem Prinzip „X Prozent jedes Telemetrietyps erfassen“?*

 *  Wenngleich dieser Ansatz an die Stichprobenerstellung eine hohe Genauigkeit bei metrischen Annäherungen bietet, bestünde in diesem Fall keine Möglichkeit, Diagnosedaten für einzelne Benutzer, Sitzungen und Anforderungen in Zusammenhang zu bringen – ein äußerst wichtiger Aspekt bei der Durchführung von Diagnosen. Aus diesem Grund sind „alle Telemetrieelemente für X Prozent der App-Benutzer“ oder „alle Telemetriedaten für X Prozent der App-Anforderungen“ besser geeignete Ansätze an die Stichprobenerstellung. Für Telemetrieelemente, die nicht mit den Anforderungen verknüpft sind (z. B. die asynchrone Hintergrundverarbeitung), wird auf den Ansatz „X Prozent aller Elemente für jeden Telemetrietyp erfassen“ zurückgegriffen. 

*Kann der Prozentsatz für die Stichprobenerstellung im Verlauf der Zeit geändert werden?*

 * Ja, bei der adaptiven Stichprobenerstellung wird der Stichproben-Prozentsatz langsam auf Basis des aktuell beobachteten Telemetrievolumens verändert.

*Kann ich den Stichproben-Prozentsatz bei der adaptiven Stichprobenerstellung ermitteln?*

 * Ja – Verwenden Sie die Codemethode zum Konfigurieren der adaptiven Stichprobenerstellung, und Sie können einen Rückruf bereitstellen, der die Abtastrate abruft.

*Wie kann ich bei einer Stichprobenerstellung mit festem Prozentsatz für meine App den idealen Prozentsatz ermitteln?*

* Eine Möglichkeit ist, mit der adaptiven Stichprobenerstellung zu beginnen, herauszufinden, bei welchem Wert sie sich einstellt (siehe die oben gestellt Frage), und dann mit diesem Wert zur Stichprobenerstellung mit festem Prozentsatz zu wechseln. 

    Andernfalls müssen Sie raten. Analysieren Sie Ihre aktuelle Nutzung der Telemetriedaten in Application Insights, beobachten Sie die auftretende Drosselung, und schätzen Sie die Menge der erfassten Telemetriedaten. In Kombination mit Ihrem ausgewählten Tarif geben diese drei Faktoren Auskunft darüber, um wie viel Sie die Menge der erfassten Telemetriedaten gegebenenfalls reduzieren sollten. Allerdings kann eine Zunahme der Benutzeranzahl oder eine andere Verschiebung in der Menge der Telemetriedaten Ihre Schätzung ungültig werden lassen.

*Was geschieht, wenn ich einen zu geringen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Ein deutlich zu geringer Prozentsatz für die Stichprobenerstellung (übermäßige Stichprobenerstellung) wirkt sich negativ auf die Genauigkeit der Annäherungen aus, wenn Application Insights versucht, die Visualisierung der Daten für die reduzierte Datenmenge auszugleichen. Auch kann das Diagnoseergebnis beeinträchtigt sein, da einige der langsamen Anforderungen oder Anforderungen, bei denen es selten zu einem Fehler kommt, möglicherweise nicht erfasst werden.

*Was geschieht, wenn ich einen zu hohen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Wenn Sie einen zu hohen Prozentsatz für die Stichprobenerstellung konfigurieren (keine ausreichende Stichprobenerstellung), führt dies zu einer nicht ausreichenden Reduzierung der Menge an erfassten Telemetriedaten. Trotzdem kann es im Zusammenhang mit der Drosselung zum Verlust von Telemetriedaten kommen, und die Kosten für die Nutzung von Application Insights können die geplanten Kosten aufgrund von Überschreitungsgebühren übersteigen.

*Auf welchen Plattformen kann ich die Stichprobenerstellung verwenden?*

* Derzeit steht die adaptive Stichprobenerstellung für die Serverseite von ASP.NET-Web-Apps zur Verfügung, sofern diese in Azure oder auf Ihrem eigenen Server gehostet sind. Die Stichprobenerstellung mit festem Prozentsatz ist für beliebige Webseiten sowie für die Client- und die Serverseite von .NET-Webanwendungen verfügbar.

*Es gibt einige seltene Ereignisse, die ich immer untersuchen möchte. Wie bekomme ich sie durch das Stichprobenmodul?*

 * Initialisieren Sie eine separate Instanz von TelemetryClient mit einer neuen TelemetryConfiguration (nicht die standardmäßig aktivierte). Verwenden Sie diese zum Senden der seltenen Ereignisse.

<!---HONumber=AcomDC_1217_2015-->