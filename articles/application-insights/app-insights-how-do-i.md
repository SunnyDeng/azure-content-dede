<properties 
	pageTitle="Gewusst wie – in Application Insights" 
	description="Häufig gestellte Fragen in Application Insights" 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Gewusst wie – in Application Insights

## Wie erhalte ich eine E-Mail-Nachricht, wenn...

### E-Mail, wenn die Website nicht mehr funktioniert

Richten Sie einen [Webtest zur Verfügbarkeit](app-insights-monitor-web-app-availability.md) ein.

### E-Mail, wenn meine Website überlastet ist

Richten Sie eine [Warnung](app-insights-alerts.md) zur **Serverantwortzeit** ein. Geeignet ist in der Regel ein Schwellenwert zwischen 1 und 2 Sekunden.

![](./media/app-insights-how-do-i/030-server.png)

Auch die Rückgabe von Fehlercodes kann ein Hinweis auf die Belastung Ihrer App sein. Richten Sie eine Warnung über **Anforderungsfehler** ein.

Wenn Sie eine Warnung für **Serverausnahmen** einrichten möchten, müssen Sie möglicherweise [einige zusätzliche Konfigurationsschritte](app-insights-asp-net-exceptions.md) vornehmen, damit die Daten einsehbar sind.


### E-Mail über ein Ereignis in meiner App

Angenommen, Sie möchten eine E-Mail erhalten, wenn ein bestimmtes Ereignis eintritt. Diese Funktion ist in Application Insights nicht in dieser Form verfügbar, aber es kann [eine Warnung gesendet werden, wenn eine Metrik einen Schwellenwert überschreitet](app-insights-alerts.md).

Warnungen können für [benutzerdefinierte Metriken](app-insights-api-custom-events-metrics.md#track-metric), jedoch nicht für benutzerdefinierte Ereignisse eingerichtet werden. Mithilfe von Code können Sie eine Metrik erhöhen, wenn das Ereignis eintritt:

    telemetry.TrackMetric("Alarm", 10);

oder:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Da Warnungen zwei Zustände aufweisen, müssen Sie einen niedrigen Wert senden, wenn das Ende der Warnung erreicht ist:

    telemetry.TrackMetric("Alarm", 0.5);

Erstellen Sie ein Diagramm im [Metrik-Explorer](app-insights-metric-explorer.md), um Ihre Warnung anzuzeigen:

![](./media/app-insights-how-do-i/010-alarm.png)

Richten Sie jetzt eine Warnung ein, die ausgelöst wird, wenn die Metrik für kurze Zeit einen mittleren Wert überschreitet:


![](./media/app-insights-how-do-i/020-threshold.png)

Legen Sie den Durchschnittszeitraum auf den Mindestwert fest.

Sie erhalten E-Mails, wenn die Metrik den Schwellenwert überschreitet und auch unterschreitet.

Zu berücksichtigende Punkte:

* Eine Warnung verfügt über zwei Zustände "Warnung" und "Fehlerfrei". Der Status wird nur ausgewertet, wenn eine Metrik empfangen wird.
* Nur bei einer Änderung des Status wird eine E-Mail gesendet. Aus diesem Grund müssen Sie Metriken mit sowohl hohen als auch niedrigen Werten senden. 
* Zur Auswertung der Warnung wird der Durchschnitt der empfangenen Werte über den vorherigen Zeitraum zugrunde gelegt. Dies erfolgt immer dann, wenn eine Metrik empfangen wird, sodass E-Mails öfter gesendet werden können, als durch den angegebenen Zeitraum festgelegt.
* Da sowohl für den Zustand "Warnung" als auch für den Zustand "Fehlerfrei" E-Mails gesendet werden, sollten Sie das einmalige Ereignis als Vorfall mit zwei Zuständen betrachten. Beispiel: Statt eines Ereignisses mit dem Status "Abgeschlossen" verwenden Sie den Zustand "In Bearbeitung" und erhalten E-Mails am Anfang und Ende des betreffenden Auftrags.
 
## Filter für die Anwendungsversion

Wenn Sie eine neue Version Ihrer App veröffentlichen, sollten Sie die Telemetrie aus verschiedenen Versionen trennen können.

Sie können die Eigenschaft "Anwendungsversion" festlegen, sodass Sie die Ergebnisse in der [Suche](app-insights-diagnostic-search.md) und im [Metrik-Explorer](app-insights-metrics-explorer.md) filtern können.


![](./media/app-insights-how-do-i/050-filter.png)

Es gibt verschiedene Methoden, um die Eigenschaft "Anwendungsversion" festzulegen.

* Direktes Festlegen:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* Umschließen Sie diese Zeile in einem [Telemetrieinitialisierer](app-insights-api-custom-events-metrics.md#telemetry-initializers), um sicherzustellen, dass alle TelemetryClient-Instanzen einheitlich festgelegt werden.

* [ASP.NET] Legen Sie die Version in `BuildInfo.config` fest. Das Webmodul übernimmt die Version aus dem Knoten "BuildLabel". Schließen Sie diese Datei in Ihr Projekt ein, und denken Sie daran, die Eigenschaft "Immer kopieren" im Projektmappen-Explorer festzulegen.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generieren Sie "BuildInfo.config" automatisch in MSBuild. Fügen Sie der CSPROJ-Datei zu diesem Zweck einige Zeilen hinzu:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    Hierdurch wird eine Datei namens "*YourProjectName*.BuildInfo.config" generiert. Der Veröffentlichungsprozess benennt diese Datei in "BuildInfo.config" um.

    Die Buildbezeichnung enthält einen Platzhalter (AutoGen\_...), wenn Sie für die Erstellung Visual Studio verwenden. Bei der Erstellung mit MSBuild wird der Name jedoch mit der richtigen Versionsnummer aufgefüllt.

    Um MSBuild das Generieren von Versionsnummern zu ermöglichen, legen Sie in "AssemblyReference.cs" die Version ähnlich wie `1.0.*` fest

<!---HONumber=Oct15_HO1-->