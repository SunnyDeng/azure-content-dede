<properties
    pageTitle="Analysen für iOS-Apps | Microsoft Azure"
    description="Analysieren Sie die Nutzung und Leistung Ihrer iOS-App."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# Analysen für iOS-Apps

Mit Visual Studio Application Insights können Sie Ihre mobile Anwendung auf Nutzung, Ereignisse und Abstürze überwachen.


> [AZURE.NOTE]Wir empfehlen [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios), um Absturzberichte, Analysen, Verteilung und Feedback zu verwalten.

## Anforderungen

Sie benötigen Folgendes:

* Ein Abonnement für [Microsoft Azure](http://azure.com). Sie melden sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, XBox Live oder andere Microsoft-Clouddienste verwenden.
* Xcode 6 oder höher.
* Das SDK wird auf Geräten unter iOS 6.0 oder höher ausgeführt.

## Erstellen einer Application Insights-Ressource

Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie die iOS-Option.

![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights".](./media/app-insights-ios/11-new.png)

Auf dem nun geöffneten Blatt werden die Leistungs- und Nutzungsdaten über Ihre App angezeigt. Um bei der nächsten Anmeldung bei Azure dorthin zu gelangen, sollten Sie eine Kachel auf dem Startbildschirm anlegen. Klicken Sie alternativ auf "Durchsuchen", um das Blatt zu finden.

## Einrichtung

Befolgen Sie den [Einrichtungsleitfaden](https://github.com/Microsoft/ApplicationInsights-iOS#setup), um das SDK in Ihrer App zu installieren.

## Anzeigen Ihrer Daten in Application Insights

Führen Sie Ihre App aus, um Telemetriedaten zu generieren.

Kehren Sie zu http://portal.azure.com zurück, und navigieren Sie zur Application Insights-Ressource.

Klicken Sie auf **Suchen**, um [Diagnosesuche][diagnostic] zu öffnen. Dort werden die ersten Ereignisse angezeigt. Wenn keine Ereignisse angezeigt werden, warten Sie eine oder zwei Minuten, und klicken Sie dann auf **Aktualisieren**.

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
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->