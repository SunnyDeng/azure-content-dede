<properties
   pageTitle="Application Insights für Azure Cloud Services"
   description="Effektives Überwachen Ihrer Web- und Workerrollen mit Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="06/17/2015"
   ms.author="sdash"/>

# Application Insights für Azure Cloud Services


*Application Insights befindet sich in der Vorschau.*

[Microsoft Azure Cloud Service-Apps](http://azure.microsoft.com/services/cloud-services/) können mit [Visual Studio Application Insights][start] auf Verfügbarkeit, Leistung, Fehler und Auslastung überwacht werden. Mit dem Feedback zur Leistung und Effektivität der App in der Praxis können Sie in jedem Entwicklungslebenszyklus eine fundierte Entscheidung für die Richtung des Entwurfs treffen.

![Beispiel](./media/app-insights-cloudservices/sample.png)

Sie benötigen ein Abonnement für [Microsoft Azure](http://azure.com). Melden Sie sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.


#### Mit Application Insights instrumentierte Beispielanwendung

Sehen Sie sich diese [Beispielanwendung](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) an, in der Application Insights mit zwei in Azure gehosteten Workerrollen einem Clouddienst hinzugefügt wird.

Im Folgenden wird erläutert, wie Sie Ihr eigenes Clouddienstprojekt auf die gleiche Weise anpassen können.

## Erstellen einer Application Insights-Ressource für die einzelnen Rollen

Ihre Telemetriedaten werden in einer Application Insights-Ressource analysiert und angezeigt.

1.  Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie als Anwendungstyp "ASP.NET-App" aus. 

    ![Klicken Sie auf "Neu > Application Insights"](./media/app-insights-cloudservices/01-new.png)

2.  Erstellen Sie eine Kopie des Instrumentationsschlüssels. Sie benötigen diese in Kürze, um das SDK zu konfigurieren.

    ![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-cloudservices/02-props.png)


Es empfiehlt sich in der Regel, eine separate Ressource für die Daten aus den einzelnen Web- und Workerrollen zu erstellen.

Als Alternative könnten Sie Daten aus allen Rollen an nur eine Ressource senden, jedoch eine [Standardeigenschaft][apidefaults] festlegen, damit Sie die Ergebnisse aus den einzelnen Rollen filtern oder gruppieren können.

## <a name="sdk"></a>Installieren des SDK in den einzelnen Projekten


1. Bearbeiten Sie die NuGet-Pakete Ihres Cloud-App-Projekts in Visual Studio.

    ![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten".](./media/app-insights-cloudservices/03-nuget.png)

2. Fügen Sie das NuGet-Paket [Application Insights for Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) hinzu. Diese Version des SDK enthält Module, die Serverkontext wie z. B. Rolleninformationen hinzufügen.

    ![Suchen Sie nach "Application Insights".](./media/app-insights-cloudservices/04-ai-nuget.png)


3. Konfigurieren Sie das SDK zum Senden von Daten an die Application Insights-Ressource.

    Öffnen Sie `ApplicationInsights.config`, und fügen Sie diese Zeile ein:

    `<InstrumentationKey>` *der kopierte Instrumentationsschlüssel* `</InstrumentationKey>`

    Verwenden Sie den Instrumentationsschlüssel, den Sie aus Ihrer Application Insights-Ressource kopiert haben.

4. Legen Sie für die Datei "ApplicationInsights.config" fest, dass sie immer in das Ausgabeverzeichnis kopiert wird. Dies ist nur für Workerrollen erforderlich.


Alternativ können Sie den Instrumentationsschlüssel (iKey) im Code festlegen. Dies empfiehlt sich beispielsweise, wenn Sie mithilfe von Azure Service Configuration (CSCFG)-Einstellungen die Instrumentationsschlüssel für die jeweiligen Umgebungen verwalten möchten. In der [Beispielanwendung](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) wird gezeigt, wie Sie den iKey festlegen können:

* [Webrolle](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
* [Workerrolle](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
* [Für Webseiten](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)

## Verwenden des SDK zum Melden von Telemetriedaten
### Melden von Anforderungen
 * In Webrollen sammelt das Modul für Anforderungen automatisch Daten zu HTTP-Anforderungen. Beispiele zum Überschreiben des Standardsammelverhaltens finden Sie im [MVCWebRole-Beispiel](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 
 * Sie können die Leistung der Aufrufe von Workerrollen erfassen, indem Sie diese auf die gleiche Weise wie HTTP-Anforderungen nachverfolgen. In Application Insights misst der Request-Telemetrietyp eine Arbeitseinheit aufseiten des benannten Servers, die zeitlich bestimmt werden und unabhängig erfolgreich sein oder einen Fehler verursachen kann. Während HTTP-Anforderungen durch das SDK automatisch erfasst werden, können Sie Ihren eigenen Code zum Nachverfolgen von Anforderungen an Workerrollen einfügen.
 * Siehe dazu die zwei Beispielworkerrollen, die zum Melden von Anforderungen instrumentiert sind: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) und [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

### Melden von Abhängigkeiten
  * Das Application Insights-SDK kann Aufrufe melden, die Ihre App an externe Abhängigkeiten sendet, z. B. an REST-APIs und SQL-Server. Auf diese Weise können Sie feststellen, ob eine bestimmte Abhängigkeit langsame Antworten oder Fehler verursacht.
  * Um Abhängigkeiten nachzuverfolgen, müssen Sie die Web- oder Workerrolle mit dem [Application Insights-Agent](app-insights-monitor-performance-live-website-now.md) einrichten, der auch als "Statusmonitor" bezeichnet wird.
  * So verwenden Sie den Application Insights-Agent mit Ihren Web- oder Workerrollen
    * Fügen Sie den Ordner [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) und die beiden darin enthaltenen Dateien Ihren Web- oder Workerrollenprojekten hinzu. Achten Sie darauf, dass Sie ihre Buildeigenschaften so festlegen, dass sie immer in das Ausgabeverzeichnis kopiert werden. Mit diesen Dateien wird der Agent installiert.
    * Fügen Sie der CSDEF-Datei wie [hier](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18) gezeigt die Startaufgabe hinzu.
    * HINWEIS: Für *Workerrollen* sind wie [hier](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44) gezeigt drei Umgebungsvariablen erforderlich. Für Webrollen ist dies nicht erforderlich.

Hier ein Beispiel für die Ansicht im Application Insights-Portal:

* Umfassende Diagnose mit automatisch korrelierten Anforderungen und Abhängigkeiten:

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Leistung der Webrolle mit Abhängigkeitsinformationen:

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* Hier ein Screenshot zu den Anforderungen und Abhängigkeitsinformationen für eine Workerrolle:

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

### Melden von Ausnahmen

* Informationen zum Sammeln von Ausnahmefehlern von verschiedenen Webanwendungstypen finden Sie unter [Monitoring Exceptions in Application Insights](app-insights-asp-net-exceptions.md) (in englischer Sprache).
* Die Beispielwebrolle verfügt über MVC5- und Web-API 2-Controller. Die Ausnahmefehler von beiden werden wie folgt erfasst:
    * [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs), wird [hier](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) für MVC5-Controller eingerichtet
    * [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs), wird [hier](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) für Web-API 2-Controller eingerichtet
* Für Workerrollen: Es gibt zwei Möglichkeiten zum Nachverfolgen von Ausnahmen.
    * TrackException(ex)
    * Wenn Sie das NuGet-Paket für Application Insights-Ablaufverfolgungslistener hinzugefügt haben, können Sie Ausnahmen mithilfe von "System.Diagnostics.Trace" protokollieren. [Codebeispiel](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

### Leistungsindikatoren

Die folgenden Leistungsindikatoren werden standardmäßig erfasst:

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

Darüber hinaus werden die folgenden Leistungsindikatoren für Webrollen gesammelt:

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Sie können zusätzliche benutzerdefinierte oder andere Windows-Leistungsindikatoren angeben, wie [hier](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14) gezeigt.

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

### Korrelierte Telemetriedaten für Workerrollen

Durch die umfassende Diagnose können Sie die Ursachen für Anforderungen mit Fehlern oder hoher Latenz sehen. Bei Webrollen richtet das SDK automatisch Korrelationen zwischen verknüpften Telemetriedaten ein. Für Workerrollen können Sie zu diesem Zweck mithilfe eines benutzerdefinierten Telemetrieinitialisierers ein allgemeines Operation.Id-Kontextattribut für alle Telemetriedaten festlegen. Dadurch können Sie auf einen Blick sehen, ob die Latenz oder der Fehler durch eine Abhängigkeit oder durch den Code verursacht wurde.

Das geht so:

* Fügen Sie die Korrelations-ID wie [hier](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36) gezeigt in einen CallContext ein. In diesem Fall wird die Anforderungs-ID als Korrelations-ID verwendet.
* Fügen Sie eine benutzerdefinierte TelemetryInitializer-Implementierung, hinzu, die das Operation.Id-Attribut auf die oben festgelegte correlationId setzt. Siehe dazu: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Fügen Sie den benutzerdefinierten Telemetrieinitialisierer hinzu. Dies kann in der Datei "ApplicationInsights.config" oder im Code (wie [hier](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233) gezeigt) erfolgen.

Das ist alles! Die Portalfunktionalität ist bereits angeschlossen, damit Sie alle zugeordneten Telemetriedaten auf einen Blick sehen können:

![](./media/app-insights-cloudservices/bHxuUhd.png)

#### Sie sehen keine Daten?

* Öffnen Sie die Kachel [Suche][diagnostic], um einzelne Ereignisse anzuzeigen.
* Verwenden Sie die Anwendung, und öffnen Sie verschiedene Seiten, damit einige Telemetriedaten generiert werden.
* Warten Sie einige Sekunden, und klicken Sie auf "Aktualisieren".
* Informationen hierzu finden Sie unter [Problembehandlung][qna].


## Abschließen der Installation

Um die vollständige 360-Grad-Ansicht Ihrer Anwendung zu erhalten, sollten Sie einige weitere Schritte durchführen:


* [Fügen Sie Ihren Webseiten das JavaScript-SDK hinzu][client], um browserbasierte Telemetriedaten wie Anzahl der Seitenaufrufe, Seitenladezeiten oder Skriptausnahmen zu erhalten und benutzerdefinierte Telemetrie in Ihre Seitenskripts schreiben zu können.
* Fügen Sie die Abhängigkeitsnachverfolgung hinzu, um Probleme zu diagnostizieren, die durch die von der App verwendeten Datenbanken oder von anderen Komponenten verursacht werden:
 * [in Ihrer Azure-Web-App oder -VM][azure]
 * [auf Ihrem lokalen IIS-Server][redfield]
* [Erfassen Sie Protokoll-Ablaufverfolgungen][netlogs] aus Ihrem bevorzugten Protokollierungsframework
* [Verfolgen Sie benutzerdefinierte Ereignisse und Metriken][api] im Client oder im Server oder beides, um weitere Informationen zur Nutzung Ihrer Anwendung zu erhalten.
* [Richten Sie Webtests ein][availability], um sicherzustellen, dass die Anwendung online und reaktionsfähig bleibt.



## Beispiel

[In diesem Beispiel](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) wird ein Dienst mit einer Webrolle und zwei Workerrollen überwacht.



[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=August15_HO6-->