<properties 
	pageTitle="Versionshinweise für Application Insights" 
	description="Die neuesten Updates." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>

 
# Versionshinweise für das Application Insights-SDK für .NET

Das [Application Insights-SDK für .NET](app-insights-start-monitoring-app-health-usage.md) sendet Telemetriedaten über Ihre Live-App an [Application Insights](http://azure.microsoft.com/services/application-insights/), wo Sie die Nutzung und Leistung analysieren können.


#### So installieren Sie das SDK in Ihrer Anwendung

Informationen dazu finden Sie unter [Application Insights – Beginnen Sie damit, Integrität und Nutzung Ihrer Anwendung zu überwachen](app-insights-start-monitoring-app-health-usage.md).

#### So aktualisieren Sie auf das neueste SDK 

* Nach dem Upgrade müssen Sie alle an "ApplicationInsights.config" vorgenommenen Anpassungen wieder zusammenführen. Wenn Sie nicht sicher sind, ob Sie Anpassungen vorgenommen haben, erstellen Sie ein neues Projekt, fügen Sie ihm Application Insights hinzu, und vergleichen Sie die CONFIG-Datei mit der Datei im neuen Projekt. Notieren Sie sich alle Unterschiede.
* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.
* Legen Sie einen Filter fest, um nur die installierten Pakete anzuzeigen. 
* Wählen Sie **Microsoft.ApplicationInsights.Web** und dann **Upgrade** aus. (Damit werden auch alle abhängigen Pakete aktualisiert.)
* Vergleichen Sie "ApplicationInsights.config" mit der alten Kopie. Die meisten Änderungen sind darauf zurückzuführen, dass einige Module entfernt und andere parametrisierbar gemacht wurden. Reaktivieren Sie alle Anpassungen, die Sie an der alten Datei vorgenommen haben.
* Erstellen Sie die Projektmappe neu.

## Version 1.2

- Telemetrieinitialisierer, die keine Abhängigkeiten von ASP.NET-Bibliotheken haben, wurden von `Microsoft.ApplicationInsights.Web` zu dem neuen Abhängigkeit-NuGet `Microsoft.ApplicationInsights.WindowsServer` verschoben
- `Microsoft.ApplicationInsights.Web.dll` wurde umbenannt in `Microsoft.AI.Web.dll`
- NuGet `Microsoft.Web.TelemetryChannel` wurde umbenannt in `Microsoft.WindowsServer.TelemetryChannel`. Assembly `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` wurde umbenannt in `Microsoft.AI.ServerTelemetryChannel.dll`. Klasse `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` wurde umbenannt in `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Alle Namespaces, die Teil des Web-SDK sind, wurden geändert, um Teil `Extensibility` auszuschließen. Dazu gehören alle Telemetrieinitialisierer in ApplicationInsights.config und Modul `ApplicationInsightsWebTracking` in web.config.
- Abhängigkeiten, die mit dem Laufzeitinstrumentierungs-Agent (aktiviert über den Statusmonitor oder die Azure WebSite-Erweiterung) erfasst werden, werden nicht als asynchron markiert, wenn HttpContext.Current für den Thread nicht vorhanden ist.
- Eigenschaft `SamplingRatio` von `DependencyTrackingTelemetryModule` führt keine Aktion aus und ist als veraltet markiert.
- Assembly `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` wurde umbenannt in `Microsoft.AI.PerfCounterCollector`
- Mehrere kleinere Fehlerbehebungen in Web- und Geräte-SDKs


## Version 1.1

- Der neue Telemetrietyp `DependencyTelemetry` wurde hinzugefügt. Er kann zum Senden von Informationen über Aufrufe von Abhängigkeiten durch die Anwendung verwendet werden (z. B. SQL, HTTP-Aufrufe usw.).
- Die neue Überladungsmethode `TelemetryClient.TrackDependency` wurde hinzugefügt, mit der Sie Informationen zu Aufrufen von Abhängigkeiten senden können.
- Die feste NullReferenceException wird vom Diagnosemodul ausgelöst, wenn TelemetryConfiguration.CreateDefault verwendet wird.

## Version 1.0

- Telemetrieinitialisierer und Telemetriemodule wurden aus den separaten untergeordneten Namespaces in den Stammnamespace `Microsoft.ApplicationInsights.Extensibility.Web` verschoben.
- Das Präfix "Web" wurde aus Namen von Telemetrieinitialisierern und Telemetriemodulen entfernt, da es bereits im Namespacenamen `Microsoft.ApplicationInsights.Extensibility.Web` enthalten ist.
- `DeviceContextInitializer` wurde aus der Assembly `Microsoft.ApplicationInsights` in die Assembly `Microsoft.ApplicationInsights.Extensibility.Web` verschoben und in einen `ITelemetryInitializer` konvertiert.
- Ändern Sie Namespace- und Assemblynamen von `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` in `Microsoft.ApplicationInsights.Extensibility.DependencyCollector`, damit Konsistenz mit dem Namen des NuGet-Pakets gegeben ist.
- Benennen Sie `RemoteDependencyModule` in `DependencyTrackingTelemetryModule` um.
- Benennen Sie `CustomPerformanceCounterCollectionRequest` in `PerformanceCounterCollectionRequest` um.

## Version 0.17
- Abhängigkeit von EventSource NuGet für die Framework 4.5-Anwendungen wurde entfernt.
- Anonyme Benutzer und Sitzungscookies werden serverseitig nicht generiert. Für die Nachverfolgung von Benutzern und Sitzungen für Web-Apps ist jetzt die Instrumentation mit dem JS-SDK erforderlich – Cookies des JavaScript-SDK werden weiterhin beachtet. Die Telemetriemodule ```WebSessionTrackingTelemetryModule``` und ```WebUserTrackingTelemetryModule``` werden nicht mehr unterstützt und wurden aus der Datei "ApplicationInsights.config" entfernt. Beachten Sie, dass diese Änderung zu einer erheblichen Anpassung von Benutzer- und Sitzungszahlen führen kann, da nun nur von Benutzern initiierte Sitzungen gezählt werden.
- OSVersion wird nicht mehr standardmäßig vom SDK angegeben. Ohne eine Angabe werden OS und OSVersion von der Application Insights-Pipeline basierend auf dem Benutzer-Agent berechnet. 
- Der für Szenarios mit hoher Belastung optimierte Persistenz-Kanal wird für das Web SDK verwendet. Das "Spiral of death"-Problem wurde behoben. "Spiral of death" ist eine Bedingung, bei der die Spitze bei der Anzahl der Telemetrieelemente, die den Einschränkungsgrenzwert am Endpunkt erheblich überschreitet, zu einem erneuten Versuch führt und beim erneuten Versuch wieder eingeschränkt wird.
- Der Entwicklermodus wurde für die Produktion optimiert. Wenn dieser Modus versehentlich aktiviert bleibt, verursacht der Versuch, zusätzliche Informationen auszugeben, keinen so großen Overhead wie zuvor.
- Der Entwicklermodus wird standardmäßig nur aktiviert, wenn für die Anwendung der Debugger aktiviert ist. Sie können ihn durch Verwenden der ```DeveloperMode```-Eigenschaft der ```ITelemetryChannel```-Schnittstelle überschreiben.

## Version 0.16 

Vorschau vom 28.04.2015

- Das SDK unterstützt jetzt die DNX-Zielplattform, um die Überwachung von Anwendungen des [.NET Core Frameworks](http://www.dotnetfoundation.org/NETCore5) zu unterstützen.
- Von ```TelemetryClient```-Instanzen wird der Instrumentationsschlüssel nicht mehr zwischengespeichert. Wenn der Instrumentationsschlüssel nicht explizit in ```TelemetryClient``` festgelegt wurde, gibt ```InstrumentationKey``` null zurück. Dadurch wird folgendes Problem behoben: Wenn Sie ```TelemetryConfiguration.Active.InstrumentationKey``` festgelegt haben, nachdem einige Telemetriedaten bereits erfasst wurden, verwenden Telemetriemodule wie die Abhängigkeitserfassung, die Erfassung von Webanforderungsdaten und die Erfassung von Leistungskennzahlen den neuen Instrumentationsschlüssel.

## Version 0.15

- Neue Eigenschaft ```Operation.SyntheticSource``` jetzt verfügbar für ```TelemetryContext```. Jetzt können Sie Ihre Telemetrieelemente mit "kein echter Benutzerdatenverkehr" markieren und angeben, wie dieser Datenverkehr generiert wurde. Beispiel: Durch Festlegen dieser Eigenschaft können Sie den Datenverkehr der Testautomatisierung von dem Datenverkehr unterscheiden, der durch Auslastungstests verursacht wird.
- Kanallogik wurde in das separate NuGet-Paket namens "Microsoft.ApplicationInsights.PersistenceChannel" verschoben. Der Standardkanal heißt jetzt "InMemoryChannel".
- Mit der neuen Methode ```TelemetryClient.Flush``` können Sie Telemetrieelemente synchron aus dem Puffer leeren.

## Version 0.13

Für ältere Versionen sind keine Versionshinweise verfügbar.

 

<!---HONumber=August15_HO6-->