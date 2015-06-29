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

## Version 0.17
- Abhängigkeit von EventSource NuGet für die Framework 4.5-Anwendungen wurde entfernt.
- Anonyme Benutzer und Sitzungscookies werden serverseitig nicht generiert. Die Telemetriemodule ```WebSessionTrackingTelemetryModule``` und ```WebUserTrackingTelemetryModule``` werden nicht mehr unterstützt und wurden aus der Datei "ApplicationInsights.config" entfernt. Cookies des JavaScript-SDK werden berücksichtigt.
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

 

<!---HONumber=58_postMigration-->