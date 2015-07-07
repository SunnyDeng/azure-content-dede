<properties 
	pageTitle="Konfigurieren des Application Insights-SDK mit &quot;ApplicationInsights.config&quot; oder XML" 
	description="Aktivieren oder deaktivieren Sie Datensammlungsmodule, und fügen Sie Leistungsindikatoren und andere Parameter hinzu" 
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

# Konfigurieren des Application Insights-SDK mit "ApplicationInsights.config" oder XML

Das Application Insights-SDK besteht aus einer Reihe von Modulen. Das Kernmodul stellt die grundlegende API bereit, die Telemetrie an das Application Insights-Portal sendet. Zusätzliche Module sammeln Daten von Ihrer Anwendung und deren Kontext. Durch Anpassen der Konfigurationsdatei können Sie Module aktivieren oder deaktivieren sowie Parameter für einige von ihnen festlegen.

Die Konfigurationsdatei heißt `ApplicationInsights.config` oder `ApplicationInsights.xml`, je nach dem Typ der Anwendung. Sie wird dem Projekt automatisch hinzugefügt, sobald Sie das [SDK installieren][start]. Über den [Statusmonitor auf einem IIS-Server][redfield] oder durch [Auswahl der Application Insights-Erweiterung für eine Azure-Website oder einen virtuellen Computer][azure] wird sie außerdem einer Web-App hinzugefügt.

Es gibt keine gleichwertige Datei zum Steuern des [SDK in einer Webseite][client].


## Telemetriemodule

Für jedes Modul gibt es in der Konfigurationsdatei einen Knoten. Um ein Modul zu deaktivieren, löschen Sie den Knoten, oder kommentieren Sie ihn aus.

#### Implementation.Tracing.DiagnosticsTelemetryModule

Meldet Fehler im SDK. Dies geschieht beispielsweise, wenn das SDK nicht auf Leistungsindikatoren zugreifen kann oder ein benutzerdefinierter TelemetryInitializer eine Ausnahme auslöst.

Daten werden in der [Diagnosesuche][diagnostic] angezeigt.

#### RuntimeTelemetry.RemoteDependencyModule

Sammelt Daten zur Reaktionsfähigkeit externer Komponenten, die von der Anwendung verwendet werden. Damit dieses Modul auf einem IIS-Server funktioniert, müssen Sie den [Statusmonitor installieren][redfield]. Zur Verwendung in Azure-Web-Apps oder auf virtuellen Computern [wählen Sie die Application Insights-Erweiterung][azure].

#### Web.WebApplicationLifecycleModule

Versucht, die Telemetriedaten aus allen speicherinternen Puffern zu leeren, damit diese beim Beenden des Prozesses nicht verloren gehen.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Zählt die Anforderungen an Ihre Web-App und misst die Reaktionszeiten.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Zählt nicht behandelte Ausnahmen in Ihrer Web-App. Informationen hierzu finden Sie unter [Fehler und Ausnahmen][exceptions].



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## Leistungserfassungsmodul

#### PerfCollector.PerformanceCollectorModule

Standardmäßig sammelt dieses Modul eine Vielzahl von Windows-Leistungsindikatoren. Diese Indikatoren werden angezeigt, wenn Sie das Blatt "Filter" im Metrik-Explorer öffnen.

Sie können weitere Leistungsindikatoren überwachen – sowohl standardmäßige Windows-Leistungsindikatoren als auch andere, die Sie hinzugefügt haben.
      
Verwenden Sie zum Sammeln weiterer Leistungsindikatoren die folgende Syntax:
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
`PerformanceCounter` muss entweder `\CategoryName(InstanceName)\CounterName` oder `\CategoryName\CounterName` sein.
      
Wenn Sie ein `ReportAs`-Attribut angeben, wird dieses als Anzeigename in Application Insights verwendet.

Für die Berichterstellung an Application Insights dürfen die Indikatornamen nur folgende Zeichen enthalten: Buchstaben, runde Klammern, Schrägstriche, Bindestriche, Unterstriche, Leerzeichen und Punkte.

Verwenden Sie "ReportAs", wenn der zu überwachende Leistungsindikator ungültige Zeichen wie "#" oder Ziffern enthält.
      
Die folgenden Platzhalter werden für `InstanceName` unterstützt:

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Kanalparameter (Java)

Diese Parameter beeinflussen, wie das Java-SDK die gesammelten Telemetriedaten speichert und leert.

#### MaxTelemetryBufferCapacity

Die Anzahl der Telemetrieelemente, die im In-Memory-Speicher des SDK gespeichert werden können. Wenn diese Zahl erreicht ist, wird der Telemetriepuffer geleert – d. h. die Telemetrieelemente werden an den Application Insights-Server gesendet.

-	Min.: 1
-	Max.: 1000
-	Standard: 500

    <ApplicationInsights> ... <Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

Bestimmt, wie oft der In-Memory-Speicher geleert (die Daten an Application Insights gesendet) werden sollen.

-	Min.: 1
-	Max.: 300
-	Standard: 5

    <ApplicationInsights> ... <Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

Bestimmt die maximale Größe in MB, die dem beständigen Speicher auf dem lokalen Datenträger zugewiesen wird. Dieser Speicher wird zur dauerhaften Speicherung von Telemetrieelementen verwendet, die nicht an den Application Insights-Endpunkt übertragen werden konnten. Wenn die Speichergröße erreicht ist, werden neue Telemetrieelemente verworfen.

-	Min.: 1
-	Max.: 100
-	Standard: 10

    <ApplicationInsights> ... <Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## Kontextinitialisierer

Diese Komponenten sammeln Daten von der Plattform. Die Daten werden im [TelemetryContext-Objekt][api] gesammelt.

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## Telemetrieinitialisierer

Diese Komponenten fügen jedem an Application Insights gesendeten Telemetrieereignis Daten hinzu.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

Diese Komponente identifiziert die HTTP-Anforderungen, die von Robots wie Suchmaschinen und Webtests zu stammen scheinen. Legt "TelemetryClient.Context.Operation.SyntheticSource" fest.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

Fügt jedem Element der Telemetrie einen Vorgangsnamen hinzu. Für Web-Apps bezieht "Vorgang" sich auf eine HTTP-Anforderung. Legt "TelemetryClient.Context.Operation.Name" fest.

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

Dies aktiviert das Feature zum Suchen von Ereignissen in derselben Anforderung in der [Diagnosesuche][diagnostic]. Legt "TelemetryClient.Context.Operation.Id" fest.

Fügt jedem an Application Insights gesendeten Datenelement eine Vorgangs-ID hinzu. Für Web-Apps bezieht "Vorgang" sich auf eine HTTP-Anforderung. Daher besitzen z. B. die Anforderung und alle benutzerdefinierten Ereignisse und Ablaufverfolgungen, die an der Verarbeitung der Anforderung beteiligt sind, alle dieselbe Vorgangs-ID.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

Fügt jedem Telemetrieelement eine anonyme Benutzer-ID hinzu. Dadurch können Sie nur die Ereignisse, die sich auf die Aktivitäten eines bestimmten Benutzers beziehen, in der Diagnosesuche herausfiltern. Wenn beispielsweise eine Ausnahme gemeldet wird, können Sie verfolgen, was dieser Benutzer tat.

Legt "telemetryClient.Context.User" fest.

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

Fügt jedem Ereignis eine Sitzungs-ID hinzu. Legt "telemetryClient.Context.Session" fest.

## Benutzerdefinierte Initialisierer


Wenn die Standardinitialisierer für Ihre Anwendung nicht geeignet sind, können Sie Ihre eigenen erstellen.

Mit Kontextinitialisierern können Sie Werte festlegen, die zum Initialisieren der einzelnen Telemetrieclients verwendet werden. Wenn Sie zum Beispiel mehrere Versionen Ihrer App veröffentlicht haben, können Sie sicherstellen, dass die Daten durch Filtern einer benutzerdefinierten Eigenschaft getrennt werden können:

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Verwenden Sie Telemetrieinitialisierer, um jedem Ereignis eine Verarbeitung hinzuzufügen. Beispielsweise kennzeichnet das Web-SDK jede Anforderung mit einem Antwortcode >= 400 als fehlerhaft. Sie können dieses Verhalten außer Kraft setzen:

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
 
Fügen Sie zum Installieren der Initialisierer folgende Zeilen in "ApplicationInsights.config" hinzu:

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


Alternativ können Sie Code zum Installieren des Initialisierers zu einem frühen Zeitpunkt in der Ausführung der Anwendung schreiben. Zum Beispiel:


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

Hierdurch wird die Application Insights-Ressource bestimmt, in der die Daten angezeigt werden. In der Regel erstellen Sie für jede Ihrer Anwendungen eine separate Ressource mit einem separaten Schlüssel.

Wenn Sie den Schlüssel dynamisch festlegen möchten, um z. B. Ergebnisse aus Ihrer Anwendung an andere Ressourcen zu senden, können Sie den Schlüssel aus der Konfigurationsdatei entfernen und stattdessen im Code festlegen.

Um den Schlüssel für alle Instanzen von TelemetryClient festzulegen, einschließlich der Standard-Telemetriemodule, legen Sie den Schlüssel in "TelemetryConfiguration.Active" fest. Verwenden Sie hierzu eine Initialisierungsmethode wie "global.aspx.cs" in einem ASP.NET-Dienst:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Wenn Sie nur einen bestimmten Satz von Ereignissen an eine andere Ressource senden möchten, können Sie den Schlüssel für einen bestimmten TelemetryClient festlegen:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Weitere Informationen zur API][api]

Um einen neuen Schlüssel abzurufen, [erstellen Sie eine neue Ressource im Application Insights-Portal][new].

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

 

<!---HONumber=62-->