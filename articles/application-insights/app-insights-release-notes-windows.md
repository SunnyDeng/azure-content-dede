<properties 
	pageTitle="Versionshinweise für Application Insights für Windows" 
	description="Die neuesten Updates für das Windows Store SDK." 
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
	ms.date="02/12/2016" 
	ms.author="joshweb"/>
 
# Versionshinweise für das Application Insights-SDK für Windows Phone und Store

Das [Application Insights-SDK](app-insights-windows-get-started.md) sendet Telemetriedaten über Ihre Live-App an [Application Insights](https://azure.microsoft.com/services/application-insights/), wo Sie die Nutzung und Leistung analysieren können.


#### So installieren Sie das SDK in Ihrer Anwendung

Weitere Informationen finden Sie unter [Erste Schritte mit Application Insights für Windows Phone- und Windows Store-Apps](app-insights-windows-get-started.md).

#### So aktualisieren Sie auf das neueste SDK 

* Speichern Sie eine Kopie der Datei "ApplicationInsights.config", um Ihre Anpassungen beizubehalten.
* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.
* Legen Sie einen Filter fest, um nur die installierten Pakete anzuzeigen. 
* Wählen Sie die installierten Application Insights-Pakete und dann "Upgrade" aus.
* Vergleichen Sie die alte und neue Version der Datei "ApplicationInsights.config". Übernehmen Sie wieder alle Anpassungen, die Sie an der alten Version vorgenommen haben.
* Erstellen Sie die Projektmappe neu.

## Version 1.1.1

### Windows SDK

- Beheben einer ausbleibenden Reaktion bei Abstürzen bei Nutzung des Silverlight SDK von Windows Phone. Nach dieser Änderung wird jeder Absturz, der später als etwa 2 Sekunden nach dem Aufruf von WindowsAppInitialier.InitializeAsync(...) auftritt, auf dem Datenträger gespeichert und bei der nächsten Ausführung der App gesendet. Wenn ein Absturz innerhalb von etwa 2 Sekunden nach dem Aufruf auftritt, wird er ignoriert.  
- Legen Sie die NuGet-Abhängigkeiten auf eine bestimmte Version von Core und „Microsoft.ApplicationInsights.PersistenceChannel“ (Version 1.2.3) fest.   

### Core SDK

- Core wird auf GitHub verwaltet. Zukünftige Versionsinformationen des Core SDK finden Sie [in Github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## Version 1.1

### Core SDK

- Das SDK stellt jetzt den neuen Telemetrie-Typ ```DependencyTelemetry``` vor, der Informationen zum Abhängigkeitsaufruf von der Anwendung enthält.
- Die neue ```TelemetryClient.TrackDependency```-Methode erlaubt das Senden von Informationen über Abhängigkeitsaufrufe von der Anwendung.

## Version 1.0.0

### Windows-App-SDK

- Neue Initialisierung für Windows-Apps. Neue `WindowsAppInitializer`-Klasse, deren `InitializeAsync()`-Methode die Bootstrappinginitialisierung der SDK-Sammlung ermöglicht. Diese Änderung erlaubt eine bessere Kontrolle und beträchtliche Leistungsverbesserungen bei der App-Initialisierung gegenüber der früheren Verwendung von "ApplicationInsights.config".
- DeveloperMode wird nicht mehr automatisch festgelegt. Um das DeveloperMode-Verhalten zu ändern, müssen Sie es im Code angeben.
- Das NuGet-Paket fügt "ApplicationInsights.config" nicht mehr ein. Sie sollten den neuen WindowsAppInitializer verwenden, wenn Sie das NuGet-Paket manuell hinzufügen.
- "ApplicationInsights.config" liest nur `<InstrumentationKey>`, alle anderen Einstellungen werden in der Voreinstellung für WindowsAppInitializer-Einstellungen ignoriert.
- Der Store-Markt wird automatisch vom SDK erfasst.
- Zahlreiche Fehlerkorrekturen, Stabilitäts- und Leistungsverbesserungen.

### Core SDK

- Die Datei "ApplicationInsights.config" ist nicht mehr erforderlich. Sie wird nicht vom NuGet-Paket hinzugefügt. Die Konfiguration kann vollständig im Code angegeben werden.
- Das NuGet-Paket fügt der Projektmappe keine Zieldatei mehr hinzu. Dadurch wird die automatische DeveloperMode-Einstellung im Debugbuild entfernt. DeveloperMode sollte manuell im Code festgelegt werden.

## Version 0.17

### Windows-App-SDK

- Das Windows-App-SDK unterstützt jetzt universelle Windows-Apps, die für Windows 10 Technical Preview und mit Visual Studio 2015 RC erstellt wurden.

### Core SDK

- TelemetryClient wird standardmäßig mit InMemoryChannel initialisiert.
- Neue API wurde hinzugefügt, `TelemetryClient.Flush()`. Diese Löschmethode löst ein unmittelbares Blockieren des Uploads aller Telemetriedaten aus, die auf dem Client protokolliert wurden. Dies ermöglicht das manuelle Auslösen des Uploads vor dem Beenden des Prozesses.
- Das NuGet-Paket hat ein .NET 4.5-Ziel hinzugefügt. Dieses Ziel verfügt über keine externen Abhängigkeiten (entfernte BCL- und EventSource-Abhängigkeiten).

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

<!---HONumber=AcomDC_0218_2016-->