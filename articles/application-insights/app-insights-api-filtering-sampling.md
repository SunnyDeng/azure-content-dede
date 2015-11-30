<properties 
	pageTitle="Stichprobenerstellung, Filterung und Vorverarbeitung im Application Insights-SDK" 
	description="Schreiben Sie Plug-Ins für das SDK, um die Telemetriedaten vor dem Senden der an das Application Insights-Portal zu filtern, aus diesen Stichproben zu erstellen oder ihnen Eigenschaften hinzuzufügen." 
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
	ms.date="11/04/2015" 
	ms.author="awills"/>

# Stichprobenerstellung, Filterung und Vorverarbeitung von Telemetriedaten im Application Insights-SDK

*Application Insights befindet sich in der Vorschau.*

Sie können Plug-Ins für das Application Insights SDK schreiben und konfigurieren, um anzupassen, wie Telemetriedaten erfasst und verarbeitet werden, bevor sie an den Application Insights-Dienst gesendet werden.

Derzeit sind diese Features für das ASP.NET-SDK verfügbar.

* Durch das [Erstellen von Stichproben](#sampling) verringert sich der Umfang der Telemetriedaten, ohne Statistiken zu verfälschen. Zusammengehörige Datenpunkte werden dabei zusammengehalten, sodass Sie bei der Diagnose von Problemen zwischen diesen navigieren können. Im Portal wird die Gesamtanzahl multipliziert, um eine Kompensation der Stichproben zu erreichen.
* Durch das [Filtern](#filtering) können Sie Telemetriedaten im SDK auswählen oder ändern, bevor sie an den Server gesendet werden. Sie können beispielsweise den Umfang der Telemetriedaten verringern, indem Sie Anforderungen von Robots ausschließen. Dies stellt ein grundlegenderes Verfahren zur Senkung des Datenverkehr dar als das Erstellen von Stichproben. Sie können so besser steuern, was übertragen wird. Jedoch müssen Sie beachten, dass dies Auswirkungen auf die Statistik hat – wenn Sie z. B. alle erfolgreichen Anforderungen herausfiltern.
* Auch das [Hinzufügen von Eigenschaften](#add-properties) zu beliebigen von der App gesendeten Telemetriedaten, einschließlich Telemetriedaten von Standardmodulen, ist möglich. Sie könnten z. B. berechnete Werte hinzufügen oder Versionsnummern, nach denen Sie die Daten im Portal filtern.
* Die [SDK-API](app-insights-api-custom-events-metrics.md) wird zum Senden benutzerdefinierter Ereignisse und Metriken verwendet.

Vorbereitung:

* Installieren Sie das [Application Insights SDK](app-insights-start-monitoring-app-health-usage.md) in Ihrer App. Installieren Sie die NuGet-Pakete manuell, und wählen Sie die neueste *Vorabversion* aus.
* Probieren Sie die [Application Insights-API](app-insights-api-custom-events-metrics.md) aus. 


## Stichproben

*Diese Funktion steht als Betaversion zur Verfügung.*

Hierbei handelt es sich um die empfohlene Methode zum Reduzieren des Datenverkehrs bei gleichzeitig präzisen Statistiken. Der Filter wählt verwandte Elemente, damit Sie zwischen den Elementen in der Diagnose navigieren können. Ereigniszähler werden im metrischen Explorer angepasst, um die gefilterten Elemente zu kompensieren.

1. Aktualisieren Sie die NuGet-Pakete Ihres Projekts auf die neueste *Vorabversion* von Application Insights. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, wählen Sie „Manage NuGet Packages“ aus, aktivieren Sie **Include prerelease**, und suchen Sie nach „Microsoft.ApplicationInsights.Web“. 

2. Fügen Sie in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) diesen Codeausschnitt hinzu:

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 10, 1 (=100/100), 0.1 (=100/1000) ... -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```


Fügen Sie zur Stichprobenerstellung für Webseitendaten eine zusätzliche Zeile in den eingefügten [Application Insights-Codeausschnitt](app-insights-javascript.md) (üblicherweise auf einer Masterseite wie etwa „\_Layout.cshtml“) ein:

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Legen Sie einen Prozentsatz fest (in diesen Beispielen „10“), der „100/N“ entspricht. Dabei steht N für eine Ganzzahl. Beispiele: 50 (=100/2), 33,33 (=100/3), 25 (=100/4) oder 10 (=100/10). 
* Bei großen Datenmengen können Sie sehr niedrige Stichprobenraten wählen, z. B. 0,1.
* Beim Festlegen des Samplings für die Webseite und den Server müssen Sie für beide Seiten denselben Prozentwert für das Sampling angeben.
* Server- und Clientseite werden zur Auswahl der entsprechenden Elemente koordiniert.

[Erfahren Sie mehr über Sampling](app-insights-sampling.md).

## Filterung

Diese Technik bietet Ihnen eine bessere Kontrolle über die Daten aus dem Telemetriedatenstrom, die ein- oder ausgeschlossen werden. Sie können sie zusammen mit der Stichprobenerstellung oder einzeln verwenden.

Zum Filtern von Telemetriedaten schreiben Sie einen Telemetrie-Prozessor und registrieren ihn beim SDK. Alle Telemetriedaten durchlaufen den Prozessor. Sie können dann auswählen, diese aus dem Datenstrom zu verwerfen, oder Eigenschaften hinzufügen. Dies schließt Telemetriedaten aus Standardmodulen wie etwa dem HTTP-Anforderungssammler und der Abhängigkeitserfassung sowie von Ihnen erstellte Telemetriedaten ein. Sie können z. B. Telemetriedaten zu Anforderungen von Robots oder erfolgreiche Abhängigkeitsaufrufe herausfiltern.

> [AZURE.WARNING]Die Filterung der vom SDK gesendeten Telemetriedaten mithilfe von Prozessoren kann die im Portal angezeigten Statistiken verfälschen und die Nachverfolgung verwandter Elemente erschweren.
> 
> Verwenden Sie stattdessen [Sampling](#sampling).

### Erstellen eines Telemetrieprozessors

1. Aktualisieren Sie das Application Insights-SDK auf die neueste Version (2.0.0-beta2 oder höher). Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „NuGet-Pakete verwalten“ aus. Wählen Sie im NuGet-Paket-Manager **Vorabversion einschließen** aus, und suchen Sie nach „Microsoft.ApplicationInsights.Web“.

1. Implementieren Sie zum Erstellen eines Filters „ITelemetryProcessor“. Hierbei handelt es sich um einen weiteren Erweiterungspunkt wie Telemetriemodul, Telemetrieinitiliasierer und Telemetriekanal.

    Beachten Sie, dass Telemetrieprozessoren eine Verarbeitungskette erstellen. Beim Instanziieren eines Telemetrieprozessors übergeben Sie einen Link an den nächsten Prozessor in der Kette. Wenn ein Telemetriedatenpunkt an die Verarbeitungsmethode übergeben wird, führt er seine Aufgaben aus und ruft dann den nächsten Telemetrieprozessor in der Kette auf.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
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
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Fügen Sie Folgendes in „ApplicationInsights.config“ ein: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Beachten Sie, dass dies der gleiche Abschnitt ist, in dem Sie einen Filter für die Stichprobe initialisieren).

Sie können durch die Bereitstellung von öffentlich benannten Eigenschaften in Ihrer Klasse Zeichenfolgenwerte aus der .config-Datei übergeben.

> [AZURE.WARNING]Achten Sie darauf, dass die Typnamen und sonstige Eigenschaftennamen in der .config-Datei mit den Klassen- und Eigenschaftennamen im Code übereinstimmen. Wenn die .config-Datei auf einen nicht vorhandenen Typ oder eine nicht vorhandene Eigenschaft verweist, könnte das SDK möglicherweise automatisch keine Telemetriedaten mehr senden.

 
**Alternativ** können Sie den Filter im Code initialisieren. Fügen Sie in einer geeigneten Initialisierungsklasse, z. B. AppStart in „Global.asax.cs“ Ihren Prozessor in die Kette ein:

    ```C#

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

    ```

Nach diesem Punkt erstellte TelemetryClients-Elemente verwenden Ihre Prozessoren.

### Beispielfilter

#### Synthetische Anforderungen

Filtern Sie Bots und Webtests heraus. Mit dem Metrik-Explorer haben Sie die Option, synthetische Quellen herauszufiltern. Dadurch wird der Datenverkehr verringert, da die Quellen im SDK gefiltert werden.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### Fehler bei der Authentifizierung

Filtern Sie Abfragen mit der Antwort „401“ heraus.

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


## Eigenschaften hinzufügen

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


## Referenz

* [API-Übersicht](app-insights-api-custom-events-metrics.md)

* [ASP.NET-Referenz](https://msdn.microsoft.com/library/dn817570.aspx)


## SDK-Code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


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

 

<!---HONumber=Nov15_HO4-->