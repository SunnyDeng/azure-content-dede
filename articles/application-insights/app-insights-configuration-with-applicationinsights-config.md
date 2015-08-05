<properties 
	pageTitle="Konfigurieren des Application Insights-SDK mit ";ApplicationInsights.config"; oder XML" 
	description="Aktivieren oder deaktivieren Sie Datensammlungsmodule, und fügen Sie Leistungsindikatoren und andere Parameter hinzu" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# Konfigurieren des Application Insights-SDK mit "ApplicationInsights.config" oder XML

Das Application Insights .NET-SDK umfasst eine Reihe von NuGet-Paketen. Das [Kernpaket](http://www.nuget.org/packages/Microsoft.ApplicationInsights) stellt die API für das Senden von Telemetriedaten an Application Insights bereit. [Zusätzliche Pakete](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) bieten _Telemetriemodule_ und _-initialisierer_ für die automatische Nachverfolgung von Telemetriedaten von Ihrer Anwendung und deren Kontext. Durch Anpassen der Konfigurationsdatei können Sie Telemetriemodule und -initialisierer aktivieren oder deaktivieren sowie Parameter für einige von ihnen festlegen.

Die Konfigurationsdatei heißt `ApplicationInsights.config` oder `ApplicationInsights.xml`, je nach dem Typ der Anwendung. Sie wird dem Projekt automatisch hinzugefügt, wenn Sie [einige Versionen des SDK installieren][start]. Über den [Statusmonitor auf einem IIS-Server][redfield] oder durch Auswahl der [Application Insights-Erweiterung für eine Azure-Website oder einen virtuellen Computer][azure] wird sie außerdem einer Web-App hinzugefügt.

Es gibt keine gleichwertige Datei zum Steuern des [SDK in einer Webseite][client].

Für jedes Modul gibt es in der Konfigurationsdatei einen Knoten. Um ein Modul zu deaktivieren, löschen Sie den Knoten, oder kommentieren Sie ihn aus.

## [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights)-NuGet-Paket

Das `Microsoft.ApplicationInsights`-NuGet-Paket enthält die folgenden Telemetriemodule in `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
`DiagnosticsTelemetryModule` gibt Fehler im Application Insights-Instrumentationscode selbst an. Dies geschieht beispielsweise, wenn der Code nicht auf Leistungsindikatoren zugreifen kann oder wenn `ITelemetryInitializer` eine Ausnahme auslöst wird. Telemetriedaten der Ablaufverfolgung, die in diesem Modul nachverfolgt werden, werden in der [Diagnosesuche][diagnostic] angezeigt.

## [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)-NuGet-Paket

Das `Microsoft.ApplicationInsights.DependencyCollector`-NuGet-Paket enthält die folgenden Telemetriemodule in `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
`DependencyTrackingTelemetryModule` verfolgt Telemetriedaten zu Aufrufen der externen Abhängigkeiten, die von der Anwendung vorgenommen werden, wie z. B. HTTP-Anforderungen und SQL-Abfragen. Damit dieses Modul auf einem IIS-Server funktioniert, müssen Sie den [Statusmonitor installieren][redfield]. Zur Verwendung in Azure-Web-Apps oder auf virtuellen Computern [wählen Sie die Application Insights-Erweiterung][azure].

Sie können auch eigenen Code zur Abhängigkeitsnachverfolgung mithilfe der [TrackDependency-API](app-insights-api-custom-events-metrics.md#track-dependency) schreiben.

## [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-NuGet-Paket

Das `Microsoft.ApplicationInsights.Web`-NuGet-Paket enthält die folgenden Telemetrieinitialisierer und -module in `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**Initialisierer**

* `SyntheticTelemetryInitializer` aktualisiert die Kontexteigenschaften `User`, `Session` und `Operation` aller Telemetrieelemente, die bei der Verarbeitung einer Anforderung von einer synthetischen Quelle wie einem Verfügbarkeitstest nachverfolgt werden. Im Application Insights-Portal werden synthetische Telemetriedaten nicht standardmäßig angezeigt.
* `ClientIpHeaderTelemetryInitializer` aktualisiert die `Ip`-Eigenschaft des `Location`-Kontexts aller Telemetrieelemente basierend auf dem `X-Forwarded-For`-HTTP-Header der Anforderung.
* `UserAgentTelemetryInitializer` aktualisiert die `UserAgent`-Eigenschaft des `User`-Kontexts aller Telemetrieelemente basierend auf dem `User-Agent`-HTTP-Header der Anforderung.
* `OperationNameTelemetryInitializer` aktualisiert die `Name`-Eigenschaft von `RequestTelemetry` und die `Name`-Eigenschaft des `Operation`-Kontexts aller Telemetrieelemente basierend auf der HTTP-Methode sowie die Namen von ASP.NET-MVC-Controllern und Aktionen, die aufgerufen werden, um die Anforderung zu verarbeiten.
* `OperationNameTelemetryInitializer` aktualisiert "Operation.Id` context property of all telemetry items tracked while 
handling a request with the automatically generated `RequestTelemetry.Id".
* `UserTelemetryInitializer` aktualisiert die `Id`- und `AcquisitionDate`-Eigenschaften des `User`-Kontexts für alle Telemetrieelemente mit Werten, die aus dem `ai_user`-Cookie extrahiert werden, der vom Application Insights-JavaScript-Instrumentationscode generiert wird, der im Browser des Benutzers ausgeführt wird.
* `SessionTelemetryInitializer` aktualisiert die `Id`-Eigenschaft des `Session`-Kontexts für alle Telemetrieelemente mit Werten, die aus dem `ai_session`-Cookie extrahiert werden, der vom Application Insights-JavaScript-Instrumentationscode generiert wird, der im Browser des Benutzers ausgeführt wird. 
* `AzureRoleEnvironmentTelemetryInitializer` aktualisiert die `RoleName`- und `RoleInstance`-Eigenschaften des `Device`-Kontexts für alle Telemetrieelemente mit Informationen, die aus der Azure-Laufzeitumgebung extrahiert wurden.
* `DomainNameRoleInstanceTelemetryInitializer` aktualisiert die `RoleInstance`-Eigenschaft des `Device`-Kontexts für alle Telemetrieelemente mit dem Domänennamen des Computers, auf dem die Webanwendung ausgeführt wird.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualisiert die `Version`-Eigenschaft des `Component`-Kontexts für alle Telemetrieelemente mit dem Wert, der aus der Datei `BuildInfo.config` extrahiert wurde, die vom TFS-Build erzeugt wurde.
* `DeviceTelemetryInitializer` aktualisiert die folgenden Eigenschaften des `Device`-Kontexts für alle Telemetrieelemente.
 - `Type` wird auf "PC" festgelegt.
 - `Id` wird auf den Domänennamen des Computers festgelegt, auf dem die Webanwendung ausgeführt wird.
 - `OemName` wird auf den Wert festgelegt, der mithilfe von WMI aus dem Feld `Win32_ComputerSystem.Manufacturer` extrahiert wurde.
 - `Model` wird auf den Wert festgelegt, der mithilfe von WMI aus dem Feld `Win32_ComputerSystem.Model` extrahiert wurde.
 - `NetworkType` wird auf den Wert festgelegt, der aus dem Feld `NetworkInterface` extrahiert wurde.
 - `Language` wird auf den Namen von `CurrentCulture` festgelegt.

**Module**

* `RequestTrackingTelemetryModule` verfolgt von Ihrer Web-App empfangene Anforderungen und misst die Reaktionszeiten.
* `ExceptionTrackingTelemetryModule` verfolgt nicht behandelte Ausnahmen in Ihrer Web-App. Informationen hierzu finden Sie unter [Fehler und Ausnahmen][exceptions].
* `DeveloperModeWithDebuggerAttachedTelemetryModule` erzwingt, dass `TelemetryChannel` von Application Insights Daten sofort sendet (jeweils nur ein Telemetrieelement), wenn ein Debugger an den Anwendungsprozess angefügt ist. Dies reduziert die Zeitspanne zwischen dem Zeitpunkt, zu dem Ihre Anwendung Telemetriedaten verfolgt, und der Anzeige im Application Insights-Portal auf Kosten einer signifikanten Auslastung von CPU und Netzwerkbandbreite.

## [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)-NuGet-Paket

Das `Microsoft.ApplicationInsights.PerfCounterCollector`-NuGet-Paket fügt standardmäßig die folgenden Telemetriemodule in `ApplicationInsights.config` ein.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` verfolgt eine Reihe von Windows-Leistungsindikatoren. Diese Indikatoren können angezeigt werden, wenn Sie im Metrik-Explorer auf ein Diagramm klicken, um dessen Detailblatt zu öffnen.

Sie können weitere Leistungsindikatoren überwachen – sowohl standardmäßige Windows-Leistungsindikatoren als auch andere, die Sie hinzugefügt haben.
      
Verwenden Sie zum Sammeln weiterer Leistungsindikatoren die folgende Syntax:

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
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

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

Bestimmt, wie oft der In-Memory-Speicher geleert (die Daten an Application Insights gesendet) werden sollen.

-	Min.: 1
-	Max.: 300
-	Standard: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

Bestimmt die maximale Größe in MB, die dem beständigen Speicher auf dem lokalen Datenträger zugewiesen wird. Dieser Speicher wird zur dauerhaften Speicherung von Telemetrieelementen verwendet, die nicht an den Application Insights-Endpunkt übertragen werden konnten. Wenn die Speichergröße erreicht ist, werden neue Telemetrieelemente verworfen.

-	Min.: 1
-	Max.: 100
-	Standard: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

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

<!---HONumber=July15_HO4-->