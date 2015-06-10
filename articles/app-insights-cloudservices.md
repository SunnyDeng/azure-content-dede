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
   ms.date="05/21/2015"
   ms.author="sdash"/>

# Application Insights für Azure Cloud Services


*Application Insights befindet sich in der Vorschau.*

[Microsoft Azure Cloud Service-Apps](http://azure.microsoft.com/services/cloud-services/) können mit [Visual Studio Application Insights][start] auf Verfügbarkeit, Leistung, Fehler und Auslastung überwacht werden.

<!-- For illustration purposes, we have added Application Insights to this [sample Azure cloud service](sample link). This code is available [here](git link), for you to follow along with the steps below. -->

## Erstellen einer Application Insights-Ressource für die einzelnen Rollen

Ihre Telemetriedaten werden in einer Application Insights-Ressource analysiert und angezeigt.

1.  Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie als Anwendungstyp "ASP.NET-App" aus. 

    ![Klicken Sie auf "Neu > Application Insights"](./media/app-insights-cloudservices/01-new.png)

2.  Erstellen Sie eine Kopie des Instrumentationsschlüssels. Sie benötigen diese in Kürze, um das SDK zu konfigurieren.

    ![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-cloudservices/02-props.png)


Es empfiehlt sich in der Regel, eine separate Ressource für die Daten aus den einzelnen Web- und Workerrollen zu erstellen.

Als Alternative könnten Sie Daten aus allen Rollen an nur eine Ressource senden, jedoch eine [Standardeigenschaft][apidefaults] festlegen, damit Sie die Ergebnisse aus den einzelnen Rollen filtern oder gruppieren können.

## <a name="sdk"></a>Installieren des SDK in den einzelnen Projekten


1. Bearbeiten Sie die NuGet-Pakete Ihres Desktop-App-Projekts in Visual Studio.![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten".](./media/app-insights-cloudservices/03-nuget.png)

2. Installieren Sie das Application Insights-SDK für Web-Apps.

    ![Wählen Sie **Online**, **Vorabversion einschließen** aus, und suchen Sie nach "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)

    (Alternativ können Sie das Application Insights SDK für Webanwendungen auswählen. Dieses bietet einige integrierte Leistungsindikatoren für Telemetrie.)

3. Konfigurieren Sie das SDK zum Senden von Daten an die Application Insights-Ressource.

    Öffnen Sie `ApplicationInsights.config`, und fügen Sie diese Zeile ein:

    `<InstrumentationKey>` *der kopierte Instrumentationsschlüssel* `</InstrumentationKey>`

    Verwenden Sie den Instrumentationsschlüssel, den Sie aus Ihrer Application Insights-Ressource kopiert haben.

    Als Alternative können Sie [den Schlüssel zur Laufzeit festlegen][apidynamicikey]. Dadurch können Sie Telemetriedaten aus verschiedenen Umgebungen (Entwicklung, Test, Produktion) an verschiedene Ressourcen leiten.

## Führen Sie Ihre App aus.

Führen Sie das Projekt im Debugmodus auf dem Entwicklungscomputer aus, oder veröffentlichen Sie es in Azure. Verwenden Sie es für ein paar Minuten, um einige Daten zu generieren.

## <a name="monitor"></a> Anzeigen der Telemetrie

Kehren Sie zum [Azure-Portal][portal] zurück, und navigieren Sie zu Ihren Application Insights-Ressourcen.


Suchen Sie nach Daten in der Übersichtsdiagrammen. Zuerst sehen Sie lediglich einen oder zwei Punkte. Zum Beispiel:

![Zu mehr Daten durchklicken](./media/app-insights-asp-net/12-first-perf.png)

Klicken Sie sich durch ein beliebiges Diagramm, um ausführlichere Metriken anzuzeigen. [Weitere Informationen zu Metriken.][perf]

Veröffentlichen Sie jetzt Ihre Anwendung, und sehen Sie zu, wie Daten gesammelt werden.


#### Sie sehen keine Daten?

* Öffnen Sie die Kachel [Search][diagnostic], um einzelne Ereignisse anzuzeigen.
* Verwenden Sie die Anwendung, und öffnen Sie verschiedene Seiten, damit einige Telemetriedaten generiert werden.
* Warten Sie einige Sekunden, und klicken Sie auf "Aktualisieren".
* Informationen hierzu finden Sie unter [Problembehandlung][qna].


## Abschließen der Installation

Um die vollständige 360-Grad-Ansicht Ihrer Anwendung zu erhalten, sollten Sie einige weitere Schritte durchführen:


* [Fügen Sie Ihren Webseiten das JavaScript-SDK hinzu][client], um browserbasierte Telemetriedaten wie Anzahl der Seitenaufrufe, Seitenladezeiten oder Skriptausnahmen zu erhalten und benutzerdefinierte Telemetrie in Ihre Seitenskripts schreiben zu können.
* Fügen Sie die Abhängigkeitsnachverfolgung hinzu, um Probleme zu diagnostizieren, die durch die von der App verwendeten Datenbanken oder anderen Komponenten verursacht werden:
 * [in Ihrer Azure Web-App oder VM][azure]
 * [auf Ihrem lokalen IIS-Server][redfield]
* [Erfassen Sie Protokoll-Ablaufverfolgungen][netlogs] aus Ihrem bevorzugten Protokollierungsframework
* [Verfolgen Sie benutzerdefinierte Ereignisse und Metriken][api] im Client oder im Server oder beides, um weitere Informationen zur Nutzung Ihrer Anwendung zu erhalten.
* [Richten Sie Webtests ein][availability], um sicherzustellen, dass die Anwendung online und reaktionsfähig bleibt.

#### Nachverfolgen von Anforderungen für Workerrollen

Sie können die Leistung der Aufrufe von Workerrollen erfassen, indem Sie diese auf die gleiche Weise wie HTTP-Anforderungen nachverfolgen. In Application Insights misst der Request-Telemetrietyp eine Arbeitseinheit aufseiten des benannten Servers, die zeitlich bestimmt werden und unabhängig erfolgreich sein oder einen Fehler verursachen kann. Während HTTP-Anforderungen durch das SDK automatisch erfasst werden, können Sie Ihren eigenen Code zum Nachverfolgen von Anforderungen an Workerrollen einfügen.

Hier sehen Sie eine typische Ausführungsschleife für eine Workerrolle:

```C#

    while (true)
    {
      Stopwatch s1 = Stopwatch.StartNew();
      var startTime = DateTimeOffset.UtcNow;
      try
      {
        // ... get and process messages ...

        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable",
            startTime, s1.Elapsed, SUCCESS_CODE, true);
      }
      catch (Exception ex)
      {
        string err = ex.Message;
        if (ex.InnerException != null)
        {
           err += " Inner Exception: " + ex.InnerException.Message;
        }
        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable", 
            startTime, s1.Elapsed, FAILURE_CODE, false);
        telemetryClient.TrackException(ex);

        // Don't flood if we have a bug in queue process loop.
        System.Threading.Thread.Sleep(60 * 1000);
      }
    }
```
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

<!---HONumber=58-->