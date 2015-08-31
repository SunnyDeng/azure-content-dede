<properties 
	pageTitle="Untersuchen von .NET-Ablaufverfolgungsprotokollen in Application Insights" 
	description="Suchen Sie nach mit der Ablaufverfolgung, NLog oder Log4Net generierten Protokollen." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/19/2015" 
	ms.author="awills"/>
 
# Untersuchen von .NET-Ablaufverfolgungsprotokollen in Application Insights  

Wenn Sie NLog, log4Net oder System.Diagnostics.Trace für die Diagnoseablaufverfolgung in Ihrer ASP.NET-Anwendung verwenden, können Sie Ihre Protokolle an [Visual Studio Application Insights][start] senden, um sie dort zu untersuchen und zu durchsuchen. Die Protokolle werden mit den anderen Telemetriedaten aus Ihrer Anwendung zusammengeführt, damit Sie die Ablaufverfolgungen für die Verarbeitung der einzelnen Benutzeranforderungen identifizieren und mit anderen Ereignissen und Ausnahmeberichten in Beziehung setzen können.

> [AZURE.NOTE]Benötigen Sie das Protokollerfassungsmodul? Dabei handelt es sich um einen nützlichen Adapter für die Protokollierung von Drittanbietern. Falls Sie nicht bereits NLog, log4Net oder „System.Diagnostics.Trace“ verwenden, können Sie auch einfach direkt [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) aufrufen.

Wenn Sie [Application Insights noch nicht in Ihrem Projekt installiert haben][start], holen Sie das jetzt nach. Ihr Projekt muss die Datei `ApplicationInsights.config` und das NuGet-Paket `Microsoft.ApplicationInsights.Web` enthalten.


##  Installieren eines Adapters für Ihr Protokollierungsframework

Wenn Sie ein Protokollierungsframework wie log4Net, NLog oder System.Diagnostics.Trace verwenden, können Sie einen Adapter installieren, der diese Protokolle zusammen mit anderen Telemetriedaten an Application Insights sendet.

1. Wenn Sie log4Net oder NLog verwenden möchten, installieren Sie es in Ihrem Projekt. 
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.
3. Suchen Sie nach "Application Insights".

    ![Vorabversion des entsprechenden Adapters auswählen](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. Wählen Sie das entsprechende Paket aus:
  + Microsoft.ApplicationInsights.TraceListener (zum Erfassen von System.Diagnostics.Trace-Aufrufen)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Das NuGet-Paket installiert die erforderlichen Assemblys und ändert auch die Datei "web.config" oder "app.config".

#### Einfügen von Diagnoseprotokollaufrufen

Wenn Sie System.Diagnostics.Trace verwenden, wäre ein typischer Aufruf:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Wenn Sie log4net oder NLog bevorzugen:

    logger.Warn("Slow response - database01");


## Direktes Verwenden der Ablaufverfolgungs-API

Sie können die API zur Application Insights-Ablaufverfolgung direkt aufrufen. Die Protokollierungsadapter verwenden diese API.

Beispiel:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Ein Vorteil von TrackTrace ist, dass relativ lange Daten in die Nachricht eingefügt werden können. Sie können dort z. B. POST-Daten codieren.


## Untersuchen Ihrer Protokolle

Wählen Sie im Übersichtsblatt im [Application Insights-Portal][portal] die Option [Suchen][diagnostic].

![Wählen Sie in Application Insights die Option "Suchen".](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Diagnosesuche](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Sie haben beispielsweise folgende Möglichkeiten:

* Filtern nach Protokollablaufverfolgungen oder nach Elementen mit bestimmten Eigenschaften
* Untersuchen eines bestimmten Elements im Detail
* Finden weiterer Telemetriedaten im Zusammenhang mit derselben Benutzeranforderung (d. h. mit derselben OperationId) 
* Speichern der Konfiguration dieser Seite als Favorit


## Nächste Schritte

[Diagnostizieren von Fehlern und Ausnahmen in ASP.NET][exceptions]

[Erfahren Sie mehr über die Diagnosesuche][diagnostic].



## Problembehandlung

### <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.

Anscheinend haben Sie das Protokollierungsadapter-Nuget-Paket installiert, ohne Application Insights zu installieren.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt. Damit sollte das Problem behoben sein.

### Ablaufverfolgungen werden in der Diagnosesuche angezeigt, aber nicht die anderen Ereignisse.

Manchmal kann es eine Weile dauern, bis alle Ereignisse und Anforderungen über die Pipeline abgerufen werden.

### <a name="limits"></a>Wie viele Daten werden beibehalten?

Bis zu 500 Ereignisse pro Sekunde für jede Anwendung. Ereignisse werden sieben Tage lang aufbewahrt.

## <a name="add"></a>Nächste Schritte

* [Einrichten von Tests der Verfügbarkeit und Reaktionsfähigkeit][availability]
* [Problembehandlung][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

 

<!---HONumber=August15_HO8-->