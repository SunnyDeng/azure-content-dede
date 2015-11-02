<properties 
	pageTitle="Application Insights-API für benutzerdefinierte Ereignisse und Metriken" 
	description="Fügen Sie einige Codezeilen in Ihrer Geräte- oder Desktop-App, Webseite oder dem Webdienst ein, um Nutzungs- und Diagnoseprobleme nachzuverfolgen." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Verarbeiten von Telemetriedaten im Application Insights SDK

*Application Insights befindet sich in der Vorschau.*

Sie können Plug-Ins für das Application Insights SDK schreiben und konfigurieren, um anzupassen, wie Telemetriedaten erfasst und verarbeitet werden, bevor sie an den Application Insights-Dienst gesendet werden.

* [Sampling](#sampling) verringert den Umfang der Telemetriedaten, ohne Statistiken zu verfälschen. Zur Vereinfachung der Suche werden verwandte Datenpunkte nicht getrennt.
* [Telemetrieprozessoren](#telemetry-processors) initialisieren, filtern und sampeln Daten im SDK, bevor sie an den Server gesendet werden. Sie können einigen Telemetriedaten beispielsweise berechnete Eigenschaften hinzufügen oder den Umfang der Telemetriedaten verringern, indem Sie Anforderungen von Robots ausschließen. 
* [Telemetrieinitialisierer](#telemetry-initializers) fügen Standardeigenschaften zu allen gesendeten Telemetriedaten hinzu. Sie können zudem einige Verhaltensweisen der Standardtelemetriemodule außer Kraft setzen. Die Eigenschaften werden für jeden Tracking-Aufruf ausgewertet, sodass Sie diese Werte variieren können.
* [Kontextinitialisierer](#context-initializers) legen auch globale Eigenschaften fest. Die Eigenschaften werden einmal beim Systemstart festgelegt und können später nicht geändert werden. Dadurch wird (im Gegensatz zu Telemetrieinitialisierern) zwar CPU-Zeit eingespart, diese Initialisierer sind jedoch auch sehr unflexibel.
* Die [SDK-API](app-insights-api-custom-events-metrics.md) wird zum Senden benutzerdefinierter Ereignisse und Metriken verwendet.

Da sich diese Mechanismen teilweise überschneiden, werden die Initialisierer wahrscheinlich irgendwann geändert.

Vorbereitung:

* Installieren Sie das [Application Insights SDK](app-insights-asp-net.md) in Ihrer App. Telemetrieprozessoren benötigen 
* Probieren Sie die [Application Insights-API](app-insights-api-custom-events-metrics.md) aus. 


## Stichproben

*Diese Funktion steht als Betaversion zur Verfügung.*

Hierbei handelt es sich um die empfohlene Methode zum Reduzieren des Datenverkehrs bei gleichzeitig präzisen Statistiken. Der Filter wählt verwandte Elemente, damit Sie zwischen den Elementen in der Diagnose navigieren können. Ereigniszähler werden im metrischen Explorer angepasst, um die gefilterten Elemente zu kompensieren.

Fügen Sie bei ASP.NET-Servern diesen Code an geeigneter Stelle für die Initialisierung ein: Beispiel: Application\_Start:

*C#*

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```

Fügen Sie bei Webseiten eine zusätzliche Zeile in den eingefügten [Application Insights-Codeausschnitt](app-insights-javascript.md) ein (in der Regel auf einer Masterseite wie etwa „\_Layout.cshtml“):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Legen Sie einen Prozentsatz fest (in diesen Beispielen „10“), der „100/N“ entspricht. Dabei steht N für eine Ganzzahl. Beispiel: 50 (=1/2), 33,33 (=1/3), 25 (=1/4), 10 (=1/5)
* Beim Festlegen des Samplings für die Webseite und den Server müssen Sie für beide Seiten denselben Prozentwert für das Sampling angeben.
* Server- und Clientseite werden zur Auswahl der entsprechenden Elemente koordiniert.

[Erfahren Sie mehr über Sampling](app-insights-sampling.md).

## Telemetrieprozessoren

Schreiben Sie Telemetrieprozessoren zum Filtern und Vorverarbeiten der vom Application Insights SDK generierten Telemetriedaten, bevor sie an das Back-End-Portal gesendet werden. Dies schließt Telemetriedaten aus Standardmodulen wie etwa dem HTTP-Anforderungssammler und der Abhängigkeitserfassung ein. Sie können z. B. Telemetriedaten zu Anforderungen von Robots oder erfolgreiche Abhängigkeitsaufrufe herausfiltern.

> [AZURE.WARNING]Die Filterung der vom SDK gesendeten Telemetriedaten mithilfe von Prozessoren kann die im Portal angezeigten Statistiken verfälschen und die Nachverfolgung verwandter Elemente erschweren.
> 
> Verwenden Sie stattdessen [Sampling](#sampling).

### Erstellen eines Telemetrieprozessors

1. Implementieren Sie zum Erstellen eines Filters „ITelemetryProcessor“. Hierbei handelt es sich um einen weiteren Erweiterungspunkt wie Telemetriemodul, Telemetrieinitiliasierer und Telemetriekanal. 

    Beachten Sie, dass Telemetrieprozessoren eine Verarbeitungskette erstellen. Beim Instanziieren eines Telemetrieprozessors übergeben Sie einen Link an den nächsten Prozessor in der Kette. Wenn ein Telemetriedatenpunkt an die Verarbeitungsmethode übergeben wird, führt er seine Aufgaben aus und ruft dann den nächsten Telemetrieprozessor in der Kette auf.

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. Fügen Sie in einer geeigneten Initialisierungsklasse, z. B. AppStart in „Global.asax.cs“ Ihren Prozessor in die Kette ein:

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    Nach diesem Punkt erstellte TelemetryClients-Elemente verwenden Ihre Prozessoren.

### Beispielfilter

#### Synthetische Anforderungen

Filtern Sie Bots und Webtests heraus. Mit dem Metrik-Explorer haben Sie die Option, synthetische Quellen herauszufiltern. Dadurch wird der Datenverkehr verringert, da die Quellen im SDK gefiltert werden.

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

    this.Next.Process(item);
}

```

#### Fehler bei der Authentifizierung

Filtern Sie Abfragen mit der Antwort „401“ heraus:

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### Herausfiltern schneller Remoteabhängigkeitsaufrufe

Wenn Sie langsame Aufrufe diagnostizieren möchten, filtern Sie die schnellen Aufrufe heraus.

> [AZURE.NOTE]Dadurch wird die im Portal angezeigte Statistik verfälscht. Das Abhängigkeitsdiagramm sieht aus, als ob alle Abhängigkeitsaufrufe fehlgeschlagen wären.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```


## Telemetrieinitialisierer

Mithilfe von Telemetrieinitialisierern definieren Sie globale Eigenschaften, die mit allen Telemetriedaten gesendet werden, und setzen das ausgewählte Verhalten der Standardtelemetriemodule außer Kraft.

Das Application Insights for Web-Paket erfasst beispielsweise Telemetriedaten über HTTP-Anforderungen. Standardmäßig kennzeichnet es jede Anforderung mit einem Antwortcode >= 400 als fehlerhaft. Wenn 400 jedoch als erfolgreich behandelt werden soll, können Sie einen Telemetrieinitialisierer angeben, der die Success-Eigenschaft festlegt.

Wenn Sie einen Telemetrieinitialisierer angeben, wird dieser immer aufgerufen, wenn eine der Track*()-Methoden aufgerufen wird. Dies umfasst auch Methoden, die von den Standardtelemetriemodulen aufgerufen werden. Nach Abmachung legen diese Module keine Eigenschaft fest, die bereits durch einen Initialisierer festgelegt wurde.

**Definieren des Initialisierers**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Laden des Initialisierers**

In "ApplicationInsights.config":

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Alternativ* können Sie den Initialisierer im Code instanziieren, z. B. in "Global.aspx.cs":


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Weitere Informationen zu diesem Beispiel anzeigen.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### JavaScript-Telemetrieinitialisierer

*JavaScript*

Fügen Sie einen Telemetrieinitialisierer unmittelbar nach dem Initialisierungscode ein, den Sie vom Portal abgerufen haben:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Eine Übersicht der für „telemetryItem“ verfügbaren nicht benutzerdefinierten Eigenschaften finden Sie im [Datenmodell](app-insights-export-data-model.md/#lttelemetrytypegt).

Sie können beliebig viele Initialisierer hinzufügen.






## <a name="default-properties"></a>Kontextinitialisierer – Festlegen von Standardeigenschaften für alle Telemetriedaten

Sie können einen universellen Initialisierer einrichten, damit alle neuen "TelemetryClient"-Elemente automatisch Ihren Kontext verwenden. Dies schließt die standardmäßigen Telemetriedaten ein, die von plattformspezifischen Telemetriemodulen gesendet werden, wie z. B. die Nachverfolgung von Webserveranforderungen.

Eine typische Verwendung besteht in der Identifikation von Telemetriedaten aus verschiedenen Versionen oder Komponenten Ihrer App. Im Portal können Sie Ergebnisse nach der Eigenschaft "Application Version" filtern oder gruppieren.

Im Allgemeinen [empfehlen wir die Verwendung von Telemetrieinitialisierern anstelle von Kontextinitialisierern](http://apmtips.com/blog/2015/06/09/do-not-use-context-initializers/).

#### Definieren eines Kontextinitialisierers


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

#### Laden eines Kontextinitialisierers

In "ApplicationInsights.config":

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*Alternativ* können Sie den Initialisierer im Code instanziieren:

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```



## Referenz

* [API-Übersicht](app-insights-api-custom-events-metrics.md)

* [ASP.NET-Referenz](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java-Referenz](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript-Referenz](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android-SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## SDK-Code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Android-SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)
* [Alle Plattformen](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## Fragen

* *Welche Ausnahmen werden möglicherweise von Aufrufen vom Typ „Track\_()“ ausgelöst?*
    
    Keine. Sie müssen sie nicht mit try/catch-Klauseln umschließen. Wenn beim SDK Probleme auftreten, werden Meldungen protokolliert, die in der Debugkonsolenausgabe und – sofern die Meldungen ankommen – in der Diagnosesuche angezeigt werden.



* *Gibt es eine REST-API?*

    Ja, aber sie wird noch nicht veröffentlicht.

## <a name="next"></a>Nächste Schritte


[Durchsuchen von Ereignissen und Protokollen][diagnostic]

[Beispiele und exemplarische Vorgehensweisen](app-insights-code-samples.md)

[Problembehandlung][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->