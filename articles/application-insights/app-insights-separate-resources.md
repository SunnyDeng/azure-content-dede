<properties 
	pageTitle="Erstellen einer neuen Application Insights-Ressource" 
	description="Richten Sie eine neue Anwendung ein, und erhalten Sie einen neuen Instrumentationsschlüssel. Application Insights überwacht die Leistung und Verwendung von Live-Anwendungen." 
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
	ms.date="07/14/2015" 
	ms.author="awills"/>

# Erstellen einer neuen Application Insights-Ressource


Um das Vermischen von Telemetriedaten aus Debug-, Test- und Produktionsversionen Ihrer App zu vermeiden, erstellen Sie separate [Application Insights][start]-Ressourcen, die die Daten der verschiedenen Versionen erhalten.

Die von Ihrer Anwendung empfangenen Daten werden von Application Insights in einer Microsoft Azure-*Ressource* gespeichert und verarbeitet. Jede Ressource wird durch einen *Instrumentationsschlüssel* identifiziert. In Ihrer App wird der Schlüssel dem Application Insights-SDK bereitgestellt, sodass er die gesammelten Daten an die richtige Ressource senden kann. Der Schlüssel kann im Code oder in "ApplicationInsights.config" bereitgestellt werden. Durch Ändern des Schlüssels im SDK können Sie Daten an andere Ressourcen weiterleiten.


## Erstellen einer Application Insights-Ressource
  

Fügen Sie unter [portal.azure.com](https://portal.azure.com) eine neue Application Insights-Ressource hinzu:

![Klicken Sie auf "Neu > Application Insights"](./media/app-insights-create-new-resource/01-new.png)


* **Anwendungstyp** bestimmt den Inhalt des Blatts "Übersicht" und die im [Metrik-Explorer][metrics] verfügbaren Eigenschaften. Wenn Ihr App-Typ nicht angezeigt wird, wählen Sie einen der Webtypen für Webseiten und einen der Telefontypen für andere Geräte aus.
* Eine **Ressourcengruppe** ist eine benutzerfreundliche Möglichkeit zum Verwalten von Eigenschaften wie der [Zugriffssteuerung](app-insights-resources-roles-access-control.md). Sie können separate Ressourcengruppen für Entwicklungs-, Test- und Produktionsumgebungen verwenden.
* **Abonnement** ist Ihr Zahlungskonto in Azure.
* **Speicherort** ist der Ort, an dem Ihre Daten aufbewahrt werden. Dieser kann derzeit nicht geändert werden.
* **Zum Startmenü hinzufügen** legt für die Ressource eine Kachel für den Schnellzugriff auf Ihrer Azure-Startseite ab. 

Das Erstellen der Ressource dauert einige Sekunden. Wenn es abgeschlossen ist, sehen Sie eine Warnung.

(Sie können ein [PowerShell-Skript](app-insights-powershell-script-create-resource.md) schreiben, um eine Ressource automatisch zu erstellen.)


## Kopieren des Instrumentationsschlüssels

Der Instrumentationsschlüssel identifiziert die Ressource, die Sie erstellt haben.

![Klicken Sie auf "Essentials", klicken Sie auf "Instrumentationsschlüssel", STRG+C](./media/app-insights-create-new-resource/02-props.png)

Sie benötigen die Instrumentationsschlüssel aller Ressourcen, an die Ihre App Daten sendet.


## <a name="dynamic-ikey"></a>Dynamischer Instrumentationsschlüssel

In der Regel ruft das SDK den iKey aus der Datei "ApplicationInsights.config" ab. Sie können ihn aber leichter ändern, indem Sie ihn im Code festlegen.

Legen Sie den Schlüssel in einer Initialisierungsmethode fest, wie z. B. "global.aspx.cs" in einem ASP.NET-Dienst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

In diesem Beispiel werden die iKeys für die verschiedenen Ressourcen in verschiedenen Versionen der Webkonfigurationsdatei platziert. Durch Austauschen der Webkonfigurationsdatei wird die Zielressource ausgetauscht.

#### Webseiten

Der iKey wird auch in Webseiten Ihrer App in dem [Skript verwendet, das Sie auf dem Blatt "Schnellstart" erhalten haben](app-insights-javascript.md). Statt ihn direkt im Skript zu programmieren, generieren Sie ihn über den Serverzustand. Beispielsweise in einer ASP.NET-App:

*JavaScript in Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO4-->