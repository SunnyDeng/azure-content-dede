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
	ms.date="10/11/2015" 
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

### E-Mail für Ausnahmen

1. [Einrichten der Ausnahmeüberwachung](app-insights-asp-net-exceptions.md)
2. [Einrichten einer Warnung](app-insights-alert.md) für die Metrik der Anzahl von Ausnahmen


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

### Automatisches Einrichten von Warnungen

[Erstellen neuer Warnungen mithilfe von PowerShell](app-insights-alerts/#set-alerts-by-using-powershell)

## Verwalten von Application Insights mithilfe von PowerShell

* [Erstellen neuer Ressourcen](app-insights-powershell-script-create-resource.md)
* [Erstellen neuer Warnungen](app-insights-alerts/#set-alerts-by-using-powershell)

## Anwendungsversionen und Zeitstempel

### Trennen der Ergebnisse für Entwicklung, Test und Produktion

* Richten Sie verschiedene iKeys für unterschiedliche Umgebungen ein.
* Kennzeichnen Sie die Telemetrie für unterschiedliche Zeitstempel (Entwicklung, Test, Produktion) mit verschiedenen Eigenschaftswerten.

[Weitere Informationen](app-insights-separate-resources.md)
 

### Filtern nach Buildnummer

Wenn Sie eine neue Version Ihrer App veröffentlichen, sollten Sie die Telemetrie aus verschiedenen Builds trennen können.

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

    Hierdurch wird eine Datei mit dem Namen "*yourProjectName*.BuildInfo.config" generiert. Der Veröffentlichungsprozess benennt diese Datei in "BuildInfo.config" um.

    Die Buildbezeichnung enthält einen Platzhalter (AutoGen\_...), wenn Sie für die Erstellung Visual Studio verwenden. Bei der Erstellung mit MSBuild wird der Name jedoch mit der richtigen Versionsnummer aufgefüllt.

    Um das Generieren von Versionsnummern in MSBuild zu ermöglichen, legen Sie in "AssemblyReference.cs" die Version fest, z. B. `1.0.*`.

## Überwachen von Back-End-Servern

[Verwenden der grundlegenden API](app-insights-windows-desktop.md)


## Visualisieren von Daten

#### Dashboard mit Metriken aus mehreren Apps

* Passen Sie Ihr Diagramm im [Metrik-Explorer](app-insights-metrics-explorer.md) an, und speichern Sie es als Favorit. Heften Sie es an das Azure-Dashboard an.
* 

#### Dashboard mit Daten aus anderen Quellen und Application Insights

* [Exportieren Sie die Telemetrie in Power BI](app-insights-export-power-bi.md). 

Oder

* Verwenden Sie SharePoint als Dashboard, und zeigen Sie Daten in SharePoint-Webparts an. [Nutzen Sie fortlaufenden Export und Stream Analytics für den Export in SQL](app-insights-code-sample-export-sql-stream-analytics.md). Überprüfen Sie die Datenbank mithilfe von PowerView, und erstellen Sie ein SharePoint-Webpart für PowerView.


### Komplexe Filterungen, Segmentierungen und Verknüpfungen

* [Nutzen Sie fortlaufenden Export und Stream Analytics für den Export in SQL](app-insights-code-sample-export-sql-stream-analytics.md). Verwenden Sie PowerView, um die Datenbank zu überprüfen.

<a name="search-specific-users"></a>
### Herausfiltern anonymer oder authentifizierter Benutzer

Wenn die Benutzer sich anmelden, können Sie die [ID für authentifizierte Benutzer](app-insights-api-custom-events-metrics.md#authenticated-users) festlegen. (Dies erfolgt nicht automatisch.)

Anschließend können Sie folgende Aktionen ausführen:

* Suchen nach bestimmten Benutzer-IDs

![](./media/app-insights-how-do-i/110-search.png)

* Filtern von Metriken für anonyme oder authentifizierte Benutzer

![](./media/app-insights-how-do-i/115-metrics.png)

## Auflisten bestimmter Benutzer und deren Nutzung

Wenn Sie nur [nach bestimmten Benutzern suchen möchten](#search-specific-users), können Sie die [ID für authentifizierte Benutzer](app-insights-api-custom-events-metrics/#authenticated-users) festlegen.

Wenn Sie eine Liste von Benutzern mit bestimmten Daten erstellen möchten, z. B., welche Seiten sich die Benutzer ansehen oder wie oft sie sich anmelden, haben Sie zwei Möglichkeiten:

* [Legen Sie die ID für authentifizierte Benutzer fest](app-insights-api-custom-events-metrics/#authenticated-users), [exportieren Sie die Daten in eine Datenbank](app-insights-code-sample-export-sql-stream-analytics.md), und analysieren Sie dort die Benutzerdaten mit den entsprechenden Tools.
* Wenn Sie nur über eine kleine Anzahl von Benutzern verfügen, senden Sie benutzerdefinierte Ereignisse oder Metriken, verwenden Sie dabei die relevanten Daten als Metrikwert oder Ereignisname, und legen Sie die Benutzer-ID als Eigenschaft fest. Um Seitenansichten zu analysieren, ersetzen Sie den standardmäßigen JavaScript-trackPageView-Aufruf. Um die serverseitige Telemetrie zu analysieren, fügen Sie die Benutzer-ID mithilfe eines Telemetrieinitialisierers allen Servertelemetriedaten hinzu. Sie können dann Metriken und Suchvorgänge nach der Benutzer-ID filtern und segmentieren.


## Reduzierung des Datenverkehrs von einer App an Application Insights

* Deaktivieren Sie in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) alle nicht benötigten Module, z. B. Leistungsindikatoren.
* Berechnen Sie bei Verwendung von [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) das Aggregat der Batches von Metrikwerten vor dem Senden des Ergebnisses. Eine Überladung von TrackMetric() ist dafür vorgesehen.

Erfahren Sie mehr über [Preise und Kontingente](app-insights-pricing.md).

## Anzeigen von Systemleistungsindikatoren

Zu den Metriken, die Sie im Metrik-Explorer anzeigen können, zählt u. a. eine Gruppe von Systemleistungsindikatoren. Mehrere dieser Leistungsindikatoren werden auf dem vordefinierten Blatt **Server** angezeigt.

![Öffnen Sie die Application Insights-Ressource, und klicken Sie auf "Server".](./media/app-insights-how-do-i/121-servers.png)

### Wenn keine Leistungsindikatordaten angezeigt werden

* **IIS-Server** auf Ihrem eigenen oder auf einem virtuellen Computer. [Installieren Sie den Statusmonitor](app-insights-monitor-performance-live-website-now.md). 
* **Azure-Website** – Leistungsindikatoren werden noch nicht unterstützt. Sie können jedoch mehrere Metriken über die Systemsteuerung der Azure-Website standardmäßig abrufen.
* **Unix-Server** – [Installieren Sie collectd](app-insights-java-collectd.md).

### Anzeigen weiterer Leistungsindikatoren

* Fügen Sie zunächst [ein neues Diagramm hinzu](app-insights-metrics-explorer.md), und prüfen Sie dann, ob der gewünschte Leistungsindikator im angebotenen grundlegenden Satz enthalten ist.
* Wenn dies nicht der Fall ist, [fügen Sie den Leistungsindikator dem über das Leistungsindikatormodul erfassten Satz hinzu](app-insights-web-monitor-performance.md#system-performance-counters).


 

### Azure-Webrollen

Zum gegenwärtigen Zeitpunkt werden Leistungsindikatoren nicht überwacht.

<!---HONumber=Oct15_HO3-->