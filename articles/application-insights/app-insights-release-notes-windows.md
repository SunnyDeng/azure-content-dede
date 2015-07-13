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
 
# Versionshinweise für das Application Insights-SDK für Windows Phone und Store

Das [Application Insights-SDK](app-insights-windows-get-started.md) sendet Telemetriedaten über Ihre Live-App an [Application Insights](http://azure.microsoft.com/services/application-insights/), wo Sie die Nutzung und Leistung analysieren können.


#### So installieren Sie das SDK in Ihrer Anwendung

Weitere Informationen finden Sie unter [Erste Schritte mit Application Insights für Windows Phone- und Windows Store-Apps](app-insights-windows-get-started.md).

#### So aktualisieren Sie auf das neueste SDK 

* Speichern Sie eine Kopie der Datei "ApplicationInsights.config", um Ihre Anpassungen beizubehalten.
* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.
* Legen Sie einen Filter fest, um nur die installierten Pakete anzuzeigen. 
* Wählen Sie die installierten Application Insights-Pakete und dann "Upgrade" aus.
* Vergleichen Sie die alte und neue Version der Datei "ApplicationInsights.config". Übernehmen Sie wieder alle Anpassungen, die Sie an der alten Version vorgenommen haben.
* Erstellen Sie die Projektmappe neu.


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

<!---HONumber=62-->