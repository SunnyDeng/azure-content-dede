<properties 
	pageTitle="Application Insights für ASP.NET 5" 
	description="Überwachen Sie Webanwendungen auf Verfügbarkeit, Leistung und Auslastung." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# Application Insights für ASP.NET 5

Mit Visual Studio Application Insights können Sie Ihre Webanwendung auf Verfügbarkeit, Leistung und Nutzung überwachen. Mit dem Feedback zur Leistung und Effektivität der App in der Praxis können Sie in jedem Entwicklungslebenszyklus eine fundierte Entscheidung für die Richtung des Entwurfs treffen.

![Beispiel](./media/app-insights-asp-net-five/sample.png)

Sie benötigen ein Abonnement für [Microsoft Azure](http://azure.com). Melden Sie sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.

## Erstellen eines ASP.NET 5-Projekts

... falls Sie noch keines erstellt haben.

Verwenden Sie die standardmäßige ASP.NET 5-Projektvorlage in Visual Studio 2015.


## Erstellen einer Application Insights-Ressource

Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie die Option für ASP.NET.

![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights".](./media/app-insights-asp-net-five/01-new-asp.png)

Auf dem nun geöffneten Blatt [Ressource][roles] werden die Leistungs- und Nutzungsdaten über Ihre App angezeigt. Um bei der nächsten Anmeldung bei Azure dorthin zu gelangen, sollten Sie eine Kachel auf dem Startbildschirm anlegen. Klicken Sie alternativ auf "Durchsuchen", um das Blatt zu finden.

Durch Auswahl des Anwendungstyps wird der Standardinhalt der Ressourcenblätter und der im [Metrik-Explorer][metrics] angezeigten Eigenschaften festgelegt.

##  Konfigurieren Sie das Projekt mit dem Instrumentationsschlüssel.

Kopieren Sie den Schlüssel aus der Application Insights-Ressource:

![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-asp-net-five/02-props-asp.png)

Fügen Sie ihn in Ihrem Projekt ASP.NET 5 in `config.json` ein:

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

Wenn Sie eine dynamische Konfiguration bevorzugen, können Sie diesen Code der Startup-Klasse der Anwendung hinzufügen:

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## Hinzufügen von Application Insights zu Ihrem Projekt


#### Verweisen auf das NuGet-Paket

Suchen Sie die [aktuelle Versionsnummer](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases) des NuGet-Pakets.

Öffnen Sie `project.json`, und bearbeiten Sie den `dependencies`-Abschnitt:

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### Analysieren der Konfigurationsdatei

In `startup.cs`:

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

In der `Startup`-Methode:

    // Setup configuration sources.
    var configuration = new Configuration()
       .AddJsonFile("config.json")
       .AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    configuration.AddEnvironmentVariables();
    Configuration = configuration;

    if (env.IsEnvironment("Development"))
    {
      configuration.AddApplicationInsightsSettings(developerMode: true);
    }

In der `ConfigurationServices`-Methode:

    services.AddApplicationInsightsTelemetry(Configuration);

In der `Configure`-Methode:

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## Hinzufügen von JavaScript-Clientinstrumentation

Wenn Sie eine _Layout.cshtml-Datei verwenden, fügen Sie dort folgenden Code ein. Andernfalls fügen Sie diesen Code auf jeder Seite ein, die Sie nachverfolgen möchten.

Definieren Sie die Einfügung am Anfang der Datei:

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

Fügen Sie das Html-Hilfsobjekt vor dem `</head>`-Tag und vor allen anderen Skripts ein. Die gesamte benutzerdefinierte JavaScript-Telemetrie, die Sie von der Seite melden möchten, sollte nach diesem Ausschnitt eingefügt werden:

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## Führen Sie Ihre App aus.

Debuggen Sie die Anwendung in Visual Studio, oder veröffentlichen Sie sie auf Ihrem Webserver.

## Anzeigen von Daten über Ihre App

Kehren Sie zum [Azure-Portal][portal] zurück, und navigieren Sie zur Application Insights-Ressource. Wenn im Blatt "Übersicht" keine Daten enthalten sind, warten Sie eine oder zwei Minuten, und klicken Sie auf "Aktualisieren".

* [Nachverfolgen der Nutzung Ihrer App][usage]
* [Diagnostizieren von Leistungsproblemen][detect]
* [Einrichten von Webtests zum Überwachen der Verfügbarkeit][availability]



## Open Source

[Lesen und Hinzufügen von Code](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=62-->