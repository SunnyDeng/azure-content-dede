<properties 
    pageTitle="Application Insights für iOS-Apps" 
    description="Analysieren Sie die Auslastung und Leistung von der iOS-app mit Application Insights." 
    services="application-insights" 
    documentationCenter="ios"
    authors="alancameronwills" 
    manager="ronmart"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2015" 
    ms.author="awills"/>

# Application Insights für iOS-Apps

Mit Visual Studio Application Insights können Sie Ihre mobile Anwendung auf Nutzung, Ereignisse und Abstürze überwachen.

## Anforderungen

Sie benötigen Folgendes:

* Ein Abonnement für [Microsoft Azure](http://azure.com). Sie melden sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.
* Xcode 6 oder höher.
* Das SDK wird auf Geräten unter iOS 6.0 oder höher ausgeführt.

## Erstellen einer Application Insights-Ressource

Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie die iOS-Option.

![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights".](./media/app-insights-ios/11-new.png)

Auf dem nun geöffneten Blatt werden die Leistungs- und Nutzungsdaten über Ihre App angezeigt. Um bei der nächsten Anmeldung bei Azure dorthin zu gelangen, sollten Sie eine Kachel auf dem Startbildschirm anlegen. Klicken Sie alternativ auf "Durchsuchen", um das Blatt zu finden.

## Herunterladen von Application Insights für Mac

(Falls Sie dies noch nicht durchgeführt haben.)

1. Herunterladen von [Application Insights für Mac](http://go.microsoft.com/fwlink/?LinkID=533209)

2. Extrahieren Sie die ZIP-Datei.

3. Klicken Sie auf das App-Symbol, um Application Insights für Mac zu starten.

## <a name="signin"></a>Anmelden bei Azure

1. Klicken Sie auf "Anmelden".

2. Melden Sie sich mit Ihrem Azure-Konto an.

## Installieren des SDK in Ihrer Anwendung

1. Klicken Sie auf "Integrieren", um die SDK-Integration zu starten.

2. Wählen Sie Ihr Xcode-Projekt aus der Liste aus, oder klicken Sie auf "Anderes öffnen", um Ihr Projekt zu suchen, und klicken Sie dann auf "Integrieren".

3. Wählen Sie den Ordner für das Application Insights-SDK aus, und klicken Sie auf "Installieren".

4. Fügen Sie Ihren Buildphasen die gezeigte Skriptausführung hinzu.

    [Hinzufügen einer "Skript ausführen"-Phase](http://hockeyapp.net/help/runscriptbuildphase/)

5. Fügen Sie dem Xcode-Projekt die fehlenden Frameworks hinzu.

6. Ziehen Sie das Application Insights-Framework in Ihr Xcode-Projekt, und klicken Sie auf "Weiter".

7. Wählen Sie für Ihr Ziel "SDK in Ziel integrieren" aus.

8. Klicken Sie auf "Neue Komponente erstellen", um Ihre App im Application Insights-Portal zu erstellen.

9. Wählen Sie Ihr Abonnement und die Ressourcengruppe aus, und geben Sie einen Komponentennamen ein. In den meisten Fällen sollte dieser dem Namen Ihrer App entsprechen. Bestätigen Sie den Vorgang mit der Schaltfläche "Ressource erstellen".

10. Stellen Sie sicher, dass die richtige Komponente ausgewählt ist, und klicken Sie auf "Weiter".

11. Ändern Sie den Quellcode, wie im Assistenten gezeigt, und klicken Sie dann auf "Fertig stellen".

12. Starten Sie Ihre App im iOS-Simulator mit "Erstellen und ausführen".

## Einfügen von Telemetrieaufrufen

Sobald `[MSAIApplicationInsights start]` aufgerufen wird, beginnt das SDK mit dem Nachverfolgen von Sitzungen, Seitenaufrufen sowie unbehandelten Ausnahmen oder Abstürzen.

Sie können zusätzliche Ereignisse wie folgt hinzufügen:

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric" 
                                        value:42.2];

## Anzeigen Ihrer Daten in Application Insights

Kehren Sie zu http://portal.azure.com zurück, und navigieren Sie zur Application Insights-Ressource.

Klicken Sie auf "Suchen", um [Diagnosesuche][diagnostic] zu öffnen. Dort werden die ersten Ereignisse angezeigt. Wenn nichts angezeigt wird, warten Sie eine oder zwei Minuten, und klicken Sie auf "Aktualisieren".

![Klicken Sie auf "Diagnosesuche".](./media/app-insights-ios/21-search.png)

Während Ihre App verwendet wird, werden die Daten auf dem Blatt "Übersicht" angezeigt.

![Blatt "Übersicht"](./media/app-insights-ios/22-oview.png)

Klicken Sie auf ein beliebiges Diagramm, um weitere Details zu erhalten. Zum Beispiel zu Abstürzen:

![Klicken Sie auf das Absturzdiagramm.](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>Nächste Schritte

[Nachverfolgen der Nutzung Ihrer App][track]

[Diagnosesuche][diagnostic]

[Metrik-Explorer][metrics]

[Problembehandlung][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->