<properties 
	pageTitle="Versionshinweise für Application Insights" 
	description="Die neuesten Updates." 
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
	ms.date="04/28/2015" 
	ms.author="sergkanz"/>
 
# Versionshinweise für das Application Insights-SDK für .NET

[Verwenden des SDK für .NET](app-insights-start-monitoring-app-health-usage.md)

## Version 0,17 Dollar
- Entfernte Abhängigkeit EventSource NuGet für das Framework 4.5-Anwendungen.
- Anonyme Benutzer und Sitzungscookies werden nicht auf Serverseite generiert. Telemetrie-Module ```WebSessionTrackingTelemetryModule``` und ```WebUserTrackingTelemetryModule``` werden nicht mehr unterstützt und wurden aus der ApplicationInsights.config-Datei entfernt. Cookies von JavaScript-SDK werden eingehalten werden.
- Optimiert für Szenarien mit hoher Belastung Persistenz-Kanal wird für das Web SDK verwendet. "Spirale of Death"-Problem behoben. Spirale Death ist eine Bedingung, wenn die Spitze in der Anzahl der Telemetrie-Elemente, die am Endpunkt Einschränkungsgrenzwert erheblich überschreitet, führen um zu wiederholen Sie nach bestimmten Zeit und werden während der wieder wiederholen eingeschränkt.
- Entwicklermodus ist für die Produktion optimiert. Wenn Sie versehentlich verursacht nicht als großer Aufwand als dies vor dem Versuch, zusätzliche Informationen ausgegeben.
- Entwicklermodus in der Standardeinstellung wird nur aktiviert werden, wenn die Anwendung im Debugger ist. Überschreiben Sie es mit ```DeveloperMode``` Eigenschaft ```ITelemetryChannel``` Schnittstelle.

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

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->