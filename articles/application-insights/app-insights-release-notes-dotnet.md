<properties 
	pageTitle="Versionshinweise für Application Insights für .NET" 
	description="Die neuesten Updates für das .NET SDK." 
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
	ms.date="08/06/2015" 
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

## Version 2.0.0-beta2
- Zusätzliche Unterstützung für ITelemetryProcessor und die Möglichkeit, per Code oder Konfigurationsdatei zu konfigurieren. [Aktiviert das benutzerdefinierte Filtern im SDK](https://azure.microsoft.com/documentation/articles/app-insights-api-telemetry-processors/#telemetry-processors)
- Kontextinitialisierer wurden entfernt. Verwenden Sie stattdessen [Telemetrieinitialisierer](https://azure.microsoft.com/documentation/articles/app-insights-api-telemetry-processors/#telemetry-initializers).
- Aktualisierte Application Insights für .NET Framework 4.6. 
- Die Namen von benutzerdefinierten Ereignissen können jetzt bis zu 512 Zeichen enthalten.
- Eigenschaft ```OperationContext.Name``` wurde umbenannt in ```RootName```.
- Eigenschaft ```RequestTelemetry.Id``` wurde entfernt.
- Eigenschaft ```Id``` und ```Context.Operation.Id``` von „RequestTelemetry“ werden beim Erstellen von neuem „RequestTelemetry“ nicht initialisiert.
- ```RequestTelemetry.Name``` wird nicht mehr initialisiert. ```RequestTelemetry.Context.Operation.Name``` wird stattdessen verwendet.
- Bei der Anforderungsüberwachung ist Antwortcode 401 Teil des normalen Authentifizierungshandshakes und führt zu einer erfolgreichen Anforderung.
- Beheben der Benutzeroberflächenthread-Sperren beim Initialisieren von „InMemoryChannel“ (Standardkanal) vom Benutzeroberflächenthread aus. Dies behebt Probleme mit dem Einfrieren der Benutzeroberfläche bei WPF-Anwendungen.
 
## Version 2.0.0-beta1
- TrackDependency erzeugt gültiges JSON, wenn nicht alle erforderlichen Felder angegeben wurden.
- Die redundante Eigenschaft ```RequestTelemetry.ID``` ist jetzt nur ein Proxy für ```RequestTelemetry.Operation.Id```.
- Neue Schnittstelle ```ISupportSampling``` und deren explizite Implementierung durch die meisten Datenelementtypen.
- ```Count```-Eigenschaft für „DependencyTelemetry“ ist als veraltet markiert. Verwenden Sie stattdessen ```SamplingPercentage```.
- Neuer ```CloudContext``` wurde eingeführt, und die Eigenschaften ```RoleName``` und ```RoleInstance``` wurden daraus zu ```DeviceContext``` verschoben.
- Neue Eigenschaft ```AuthenticatedUserId``` in ```UserContext```, um die Identität des authentifizierten Benutzers anzugeben.
- `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer` und `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer` wurden hinzugefügt, die den Kontext des authentifizierten Benutzers wie durch JavaScript-SDK festgelegt initialisieren.
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` wurde hinzugefügt, und Unterstützung für Sampling mit fester Rate zu dessen Implementierung.
- `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder` wurde hinzugefügt, was das Erstellen von `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` mit einem `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`-Satz ermöglicht.

## Version 1.2

- Telemetrieinitialisierer ohne Abhängigkeiten von ASP.NET-Bibliotheken wurden von `Microsoft.ApplicationInsights.Web` zu dem neuen Abhängigkeits-NuGet `Microsoft.ApplicationInsights.WindowsServer` verschoben.
- `Microsoft.ApplicationInsights.Web.dll` wurde umbenannt in `Microsoft.AI.Web.dll`.
- NuGet `Microsoft.ApplicationInsights.Web.TelemetryChannel` wurde umbenannt in `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`. Assembly `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` wurde umbenannt in `Microsoft.AI.ServerTelemetryChannel.dll`. Klasse `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` wurde umbenannt in `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Alle Namespaces, die Teil des Web-SDK sind, wurden geändert, um Teil `Extensibility` auszuschließen. Dazu gehören alle Telemetrieinitialisierer in „ApplicationInsights.config“ und Modul `ApplicationInsightsWebTracking` in „web.config“.
- Abhängigkeiten, die mit dem Laufzeitinstrumentierungs-Agent (aktiviert über den Statusmonitor oder die Azure WebSite-Erweiterung) erfasst werden, werden nicht als asynchron markiert, wenn HttpContext.Current für den Thread nicht vorhanden ist.
- Eigenschaft `SamplingRatio` von `DependencyTrackingTelemetryModule` führt keine Aktion aus und ist als veraltet markiert.
- Assembly `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` wurde umbenannt in `Microsoft.AI.PerfCounterCollector`.
- Mehrere kleinere Fehlerbehebungen in Web- und Geräte-SDKs


## Version 1.1

- Der neue Telemetrietyp `DependencyTelemetry` wurde hinzugefügt. Er kann zum Senden von Informationen über Aufrufe von Abhängigkeiten durch die Anwendung verwendet werden (z. B. SQL, HTTP-Aufrufe usw.).
- Die neue Überladungsmethode `TelemetryClient.TrackDependency` wurde hinzugefügt, mit der Sie Informationen zu Aufrufen von Abhängigkeiten senden können.
- Die feste NullReferenceException wird vom Diagnosemodul ausgelöst, wenn TelemetryConfiguration.CreateDefault verwendet wird.

## Version 1.0

- Telemetrieinitialisierer und Telemetriemodule wurden aus den separaten untergeordneten Namespaces in den Stammnamespace `Microsoft.ApplicationInsights.Extensibility.Web` verschoben.
- Das Präfix „Web“ wurde aus Namen von Telemetrieinitialisierern und Telemetriemodulen entfernt, da es bereits im Namespacenamen `Microsoft.ApplicationInsights.Extensibility.Web` enthalten ist.
- `DeviceContextInitializer` wurde aus der Assembly `Microsoft.ApplicationInsights` in die Assembly `Microsoft.ApplicationInsights.Extensibility.Web` verschoben und in einen `ITelemetryInitializer` konvertiert.
- Namespace- und Assemblynamen wurden von `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` in `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` umbenannt, damit Konsistenz mit dem Namen des NuGet-Pakets gegeben ist.
- `RemoteDependencyModule` wurde in `DependencyTrackingTelemetryModule` umbenannt.
- `CustomPerformanceCounterCollectionRequest` wurde in `PerformanceCounterCollectionRequest` umbenannt.

## Version 0.17
- Abhängigkeit von EventSource NuGet für die Framework 4.5-Anwendungen wurde entfernt.
- Anonyme Benutzer und Sitzungscookies werden serverseitig nicht generiert. Für die Nachverfolgung von Benutzern und Sitzungen für Web-Apps ist jetzt die Instrumentation mit dem JS-SDK erforderlich – Cookies des JavaScript-SDK werden weiterhin beachtet. Die Telemetriemodule ```WebSessionTrackingTelemetryModule``` und ```WebUserTrackingTelemetryModule``` werden nicht mehr unterstützt und wurden aus der Datei "ApplicationInsights.config" entfernt. Beachten Sie, dass diese Änderung zu einer erheblichen Anpassung von Benutzer- und Sitzungszahlen führen kann, da nun nur von Benutzern initiierte Sitzungen gezählt werden.
- OSVersion wird nicht mehr standardmäßig vom SDK angegeben. Ohne eine Angabe werden OS und OSVersion von der Application Insights-Pipeline basierend auf dem Benutzer-Agent berechnet. 
- Der für Szenarios mit hoher Belastung optimierte Persistenz-Kanal wird für das Web SDK verwendet. Das "Spiral of death"-Problem wurde behoben. "Spiral of death" ist eine Bedingung, bei der die Spitze bei der Anzahl der Telemetrieelemente, die den Einschränkungsgrenzwert am Endgerät erheblich überschreitet, zu einem erneuten Versuch führt und beim erneuten Versuch wieder eingeschränkt wird.
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

 

<!---HONumber=Nov15_HO2-->