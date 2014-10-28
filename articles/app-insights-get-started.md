<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Application Insights – Erste Schritte

*Application Insights befindet sich in der Vorschauphase.*

Mit Application Insights können Sie Verfügbarkeit, Leistung und Nutzung Ihrer Live-Anwendung überwachen. (Dies gilt nicht nur für Microsoft Azure-Anwendungen.) Die Konfiguration ist kinderleicht, und Sie erhalten Ergebnisse innerhalb weniger Minuten.

-   **Verfügbarkeit** - Stellen Sie sicher, dass Ihre Web-App verfügbar und reaktionsfähig ist. Wir testen Ihre URLs in regelmäßigen Abständen von Standorten weltweit und warnen Sie, wenn ein Problem auftritt.
-   **Leistung** - Diagnostizieren Sie Leistungsprobleme oder Ausnahmen in Ihrem Webdienst. Analysieren Sie Ihre Antwortzeiten im Vergleich zu der Anzahl der Anfragen, finden Sie heraus, ob Prozessor oder andere Ressourcen am Limit sind, rufen Sie Stapelüberwachungen von Ausnahmen ab und suchen Sie schnell und einfach in Ablaufverfolgungsprotokollen.
-   **Nutzung** - Erfahren Sie, wer mit Ihrer Anwendung was tut, um sich auf die wichtigsten Entwicklungsarbeiten konzentrieren zu können. Momentan können Sie Web Apps, Windows Store- und Windows Phone-Apps überwachen.

## Erste Schritte

Sie können auf zweierlei Arten anfangen:

-   [Application Insights zu Ihrem Projekt in Visual Studio hinzufügen][Application Insights zu Ihrem Projekt in Visual Studio hinzufügen]

    Application Insights zu Ihren Projekten hinzufügen, um Nutzung, Leistung und Verfügbarkeit zu überwachen und Diagnoseprotokolle zu analysieren. Sie erhalten innerhalb weniger Minuten Daten im Debugmodus und können Ihr Projekt anschließend bereitstellen, um Live-Daten zu erhalten.

    Verwenden Sie diese Option, wenn Sie ein Projekt aktualisieren oder erstellen.

    [Fügen Sie zunächst Application Insights zu Ihrem Projekt hinzu.][Application Insights zu Ihrem Projekt in Visual Studio hinzufügen]

-   [Diagnose von Problemen in einem Live-Webdienst][Diagnose von Problemen in einem Live-Webdienst]

    Installieren Sie den Application Insights-Agenten auf Ihrem IIS-Server, um innerhalb weniger Minuten Leistungsdaten zu erhalten. Überwachen Sie Anzahl der Anfragen, Antwortzeiten, Ressourcenauslastung und rufen Sie Ausnahme-Ablaufverfolgungen ab.

    Verwenden Sie diese Option, wenn Sie herausfinden müssen, was jetzt gerade in Ihrem Webserver geschieht. Sie müssen Ihren Code nicht erneut bereitstellen. Sie benötigen jedoch Administratorzugriff auf Ihren Server und ein Microsoft Azure-Konto.

    Sie können die Überwachung der Verfügbarkeit jederzeit hinzufügen.

    Später können Sie die andere Option verwenden, um Application Insights zu Ihrem Projekt hinzuzufügen, um Diagnoseprotokolle zu analysieren und die Nutzung zu überwachen.

    [Installieren Sie zunächst Application Insights auf Ihrem Webserver.][Diagnose von Problemen in einem Live-Webdienst]

> [WACOM.NOTE] Visual Studio Online enthält eine [ältere Version von Application Insights][ältere Version von Application Insights]. Wir entwickeln Application Insights als Teil von Microsoft Azure von Grund auf neu, und dieser Artikel befasst sich mit der neuen Version.

![Beispiel-Anwendungsüberwachung in Application Insights][Beispiel-Anwendungsüberwachung in Application Insights]

## Weitere Informationen

-   [Application Insights][Application Insights]
-   [Hinzufügen von Application Insights zu Ihrem Projekt][Application Insights zu Ihrem Projekt in Visual Studio hinzufügen]
-   [Überwachen eines Live-Webservers][Diagnose von Problemen in einem Live-Webdienst]
-   [Erkunden von Metriken in Application Insights][Erkunden von Metriken in Application Insights]
-   [Durchsuchen der Diagnoseprotokolle][Durchsuchen der Diagnoseprotokolle]
-   [Verfügbarkeitsüberwachung mit Webtests][Verfügbarkeitsüberwachung mit Webtests]
-   [Nutzungsverfolgung mit Ereignissen und Metriken][Nutzungsverfolgung mit Ereignissen und Metriken]
-   [Fragen und Antworten sowie Problembehandlung][Fragen und Antworten sowie Problembehandlung]

<!--Link references-->

  [Application Insights zu Ihrem Projekt in Visual Studio hinzufügen]: ../app-insights-monitor-application-health-usage/
  [Diagnose von Problemen in einem Live-Webdienst]: ../app-insights-monitor-performance-live-website-now/
  [ältere Version von Application Insights]: http://msdn.microsoft.com/de-de/library/dn481095.aspx
  [Beispiel-Anwendungsüberwachung in Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Application Insights]: ../app-insights-get-started/
  [Erkunden von Metriken in Application Insights]: ../app-insights-explore-metrics/
  [Durchsuchen der Diagnoseprotokolle]: ../app-insights-search-diagnostic-logs/
  [Verfügbarkeitsüberwachung mit Webtests]: ../app-insights-monitor-web-app-availability/
  [Nutzungsverfolgung mit Ereignissen und Metriken]: ../app-insights-web-track-usage-custom-events-metrics/
  [Fragen und Antworten sowie Problembehandlung]: ../app-insights-troubleshoot-faq/
