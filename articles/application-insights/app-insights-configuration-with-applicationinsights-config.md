<properties 
	pageTitle="Konfigurieren von Application Insights SDK mit ApplicationInsights.config oder .xml" 
	description="Aktivieren Sie oder deaktivieren Sie Data Collection-Module und fügen Leistungsindikatoren und andere Parameter hinzu" 
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
	ms.date="06/04/2015" 
	ms.author="awills"/>

# Konfigurieren die Application Insights SDK mit ApplicationInsights.config oder .xml

Das Application Insights SDK besteht aus einer Reihe von Modulen. Das Core-Modul stellt die grundlegende API, die Telemetrie an Application Insights-Portal sendet. Zusätzliche Module sammeln Daten von der Anwendung und der Kontext. Durch das Anpassen der Konfigurationsdatei, können Sie aktivieren oder Deaktivieren von Modulen und Festlegen von Parametern für einige von ihnen.

Die Konfigurationsdatei ist mit dem Namen `ApplicationInsights.config` oder `ApplicationInsights.xml`, je nach dem Typ der Anwendung. Es wird automatisch dem Projekt hinzugefügt Wenn Sie [SDK installieren][start]. Auch durch eine Web-app hinzugefügt [Statusmonitor auf einem IIS-Server][redfield], oder wenn Sie [Wählen Sie die Erweiterung beliebigen Anwendung Einblicke für eine Azure-Website oder VM][azure].

Gibt es keine entsprechende Datei steuern die [SDK auf einer Webseite][client].


## Telemetrie-Module

Es ist ein Knoten in der Konfigurationsdatei für jedes Modul. Um ein Modul zu deaktivieren, löschen Sie den Knoten, oder kommentieren Sie es aus.

#### Implementation.Tracing.DiagnosticsTelemetryModule

Meldet Fehler im SDK. Wenn das SDK nicht auf Leistungsindikatoren zugreifen kann oder eine benutzerdefinierte TelemetryInitializer löst beispielsweise eine Ausnahme aus.

Daten angezeigt werden, [diagnostische Suche][diagnostic].

#### RuntimeTelemetry.RemoteDependencyModule

Sammelt Daten, auf die Reaktionsfähigkeit von externen Komponenten, die von der Anwendung verwendet. Damit um dieses Modul in einem IIS-Server arbeiten zu können, müssen Sie [Installieren Sie die Status-Monitor][redfield]. Die Verwendung in Azure Web-apps oder virtuelle Computer, [Wählen Sie die Application Insights-Erweiterung][azure].

#### Web.WebApplicationLifecycleModule

Versucht, alle speicherinternen Puffer Telemetriedaten zu leeren, damit es nicht auf das Beenden des Prozesses verloren geht.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Zählt die Anforderungen an Ihre Webanwendung und misst die Reaktionszeit.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Zählt nicht behandelte Ausnahmen in Ihrer Webanwendung. Finden Sie unter [Fehler und Ausnahmen][exceptions].



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## Modul-Sammlung

#### PerfCollector.PerformanceCollectorModule

Standardmäßig sammelt dieses Modul eine Vielzahl von Windows-Leistungsindikatoren. Diese Indikatoren können angezeigt werden, wenn das Filter-Fenster im Metrik-Explorer zu öffnen.

Sie können weitere Leistungsindikatoren - überwachen sowohl standardmäßige Windows-Leistungsindikatoren und andere, die Sie hinzugefügt haben.
      
Verwenden Sie die folgende Syntax, um weitere Leistungsindikatoren zu sammeln:
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
`PerformanceCounter` muss entweder `\CategoryName(InstanceName)\CounterName` oder `\CategoryName\CounterName`
      
Wenn Sie angeben, einen `ReportAs` -Attribut, wird dies als in Application Insights angezeigte Name verwendet.

Für die Berichterstellung an Application Insights müssen die Indikatornamen nur enthalten: Buchstaben, runde Klammern, Schrägstriche, Bindestriche, Unterstriche, Leerzeichen und Punkte.

Verwenden Sie ReportAs, wenn es sich bei der zu überwachenden Leistungsindikator alle ungültigen Zeichen wie '\#' oder Ziffern enthält.
      
Die folgenden Platzhalter werden unterstützt, als `InstanceName`:

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Channel-Parameter (Java)

Diese Parameter beeinflussen die Java-SDK sollte speichern und die gesammelten Telemetriedaten zu leeren.

#### MaxTelemetryBufferCapacity

Die Anzahl der Telemetrie-Elemente, die in das SDK im Arbeitsspeicher gespeichert werden können. Wenn diese Zahl erreicht ist, der Telemetrie-Puffer geleert wird – d. h. die Telemetrie Elemente an den Application Insights-Server gesendet werden.

-	Min: 1
-	Max: 1000
-	Standardwert: 500

    <ApplicationInsights> ... <Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

Bestimmt, wie oft die Daten, die in den Speicher im Arbeitsspeicher gespeichert ist (zum Application Insights gesendet) entleert werden soll.

-	Min: 1
-	Max: 300
-	Standardwert: 5

    <ApplicationInsights> ... <Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

Bestimmt die maximale Größe in MB, der in den permanenten Speicher auf dem lokalen Datenträger zugewiesen wird. Dieser Speicher wird für persistente Telemetrie-Elemente verwendet, die nicht an den Application Insights-Endpunkt übertragen werden. Wenn die Größe des Speichers erreicht wurde, werden neue Telemetrie Elemente verworfen.

-	Min: 1
-	Max: 100
-	Standard: 10

    <ApplicationInsights> ... <Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## Kontext-Initialisierer

Diese Komponenten sammeln Daten von der Plattform. Die Daten werden der [TelemetryContext Objekt][api].

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## Telemetrie Initialisierer

Diese Komponenten hinzufügen von Daten zu jedem Telemetrie-Ereignis an Application Insights gesendet.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

Diese Komponente identifiziert die HTTP-Anforderungen, die scheinbar von Robotern z. B. Suchmaschinen und Webtests stammen. TelemetryClient.Context.Operation.SyntheticSource festgelegt.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

Fügt einen Vorgangsnamen für jedes Element der Telemetrie. Für Webanwendungen bedeutet "Vorgang" eine HTTP-Anforderung. Legt die TelemetryClient.Context.Operation.Name fest

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

Dies ermöglicht die Funktion "Suchen Sie Ereignisse in der gleichen Anforderung" im [diagnostische Suche][diagnostic]. Legt die TelemetryClient.Context.Operation.Id fest

Fügt eine Vorgangs-ID jedes Datenelement an Application Insights gesendet. Für Web-apps ist eine "Operation" eine HTTP-Anforderung. Daher führen z. B. die Anforderung und alle benutzerdefinierten Ereignissen und Ablaufverfolgungen, die Teil der Verarbeitung der Anforderung, die alle dieselbe Operation.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

Fügt eine anonyme Benutzer-Id für jedes Element der Telemetrie. Dies ermöglicht Ihnen, nur die Ereignisse, die über eine bestimmte Tätigkeiten diagnostische Suche zu filtern. Z. B. wenn eine Ausnahme gemeldet wird, können Sie verfolgen was dieser Benutzer machte.

Legt die telemetryClient.Context.User fest

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

Fügt eine Sitzungs-Id für jedes Ereignis. Legt die telemetryClient.Context.Session fest

## Benutzerdefinierte Initialisierer


Wenn die standard-Initialisierer für Ihre Anwendung geeignet sind, können Sie Ihre eigenen erstellen.

Verwenden Sie Kontext Initialisierer Werte festlegen, die zum Initialisieren von jedem Client Telemetrie verwendet werden. Z. B. Wenn Sie mehr als eine Version der Anwendung veröffentlicht haben, können Sie sicherstellen, dass die Daten durch eine benutzerdefinierte Eigenschaft filtern können zu trennen:

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Verwenden Sie Telemetrie Initialisierer, um die Verarbeitung jedes Ereignis hinzufügen. Fehler bei jeder Anforderung den Antwortcode z. B. im Internet SDK flags > = 400. Sie können dieses Verhalten überschreiben:

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
Fügen Sie zum Installieren der Initialisierer in ApplicationInsights.config Zeilen hinzu:

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


Alternativ können Sie Code zum Installieren des Initialisierers zu einem frühen Zeitpunkt in der Ausführung der Anwendung schreiben. Beispiel:


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

Dies bestimmt die Application Insights-Ressource, in der die Daten angezeigt, werden. Erstellen Sie in der Regel eine separate Ressource mit einem separaten Schlüssel, für jede Ihrer Anwendungen.

Sie ggf. den Schlüssel dynamisch – z. B. festlegen, wenn Sie Ergebnisse aus Ihrer Anwendung auf verschiedene Ressourcen - senden möchten können Sie den Schlüssel aus der Konfigurationsdatei auslassen und stattdessen im Code festlegen.

Legen Sie den Schlüssel für alle Instanzen des TelemetryClient legen einschließlich der standardmäßigen Telemetrie-Module, Sie den Schlüssel in TelemetryConfiguration.Active fest. Hierzu eine Initialisierungsmethode, wie z. B. global.aspx.cs in einem ASP.NET-Dienst:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Wenn Sie eine bestimmte Gruppe von Ereignissen zu einer anderen Ressource senden möchten, können Sie den Schlüssel für eine bestimmte TelemetryClient festlegen:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Erfahren Sie mehr über die API][api].

Um einen neuen Schlüssel abrufen [erstellt eine neue Ressource in Application Insights-Portal][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->