<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Überwachen der Integrität und Nutzung Ihrer Anwendung

*Application Insights befindet sich in der Vorschauphase.*

Stellen Sie sicher, dass Ihre Anwendung korrekt funktioniert und Ihre Benutzer zufriedenstellt. Application Insights warnt Sie über eventuelle Leistungsprobleme und unterstützt Sie bei der Suche und Diagnose der zugrunde liegenden Probleme. Außerdem können Sie die Aktivität von Benutzern überwachen, um Ihre Anwendung nach deren Bedürfnissen anzupassen.

Application Insights kann Windows Phone-Apps, Windows Store-Apps, Webanwendungen auf lokalen Servern oder auf virtuellen Computern sowie Microsoft Azure-Websites überwachen.

Sie benötigen [Visual Studio Update 3][Visual Studio Update 3] und ein Konto in [Microsoft Azure][Microsoft Azure] (probieren Sie die kostenlose Testversion aus).

1. [Hinzufügen von Application Insights][Hinzufügen von Application Insights]
+ [Ausführen des Projekts][Ausführen des Projekts]
+ [Anzeigen der Überwachungsdaten][Anzeigen der Überwachungsdaten]
+ [Bereitstellen der Anwendung][Bereitstellen der Anwendung]
+ [Nächste Schritte][Nächste Schritte]

*Falls Sie einen vorhandenen Webdienst ohne erneute Bereitstellung oder über Visual Studio überwachen möchten, können Sie [einen Agenten auf dem Server installieren][einen Agenten auf dem Server installieren].*

## <a name="add"></a>1. Hinzufügen von Application Insights

### Für ein neues Projekt...

Stellen Sie bei der Erstellung des Projekts in Visual Studio 2013 sicher, dass Application Insights ausgewählt ist.

![Erstellen eines ASP.NET-Projekts][Erstellen eines ASP.NET-Projekts]

Beim ersten Mal werden Sie aufgefordert, Ihre Anmeldedaten für die Microsoft Azure-Vorschau einzugeben oder sich zu registrieren. (Dies ist nicht Ihr Visual Studio Online-Konto.)

> Keine Application Insights-Option? Vergewissern Sie sich, dass Sie Visual Studio 2013 Update 3 verwenden und dass Sie ein Web-, Windows Store- oder Windows Phone-Projekt erstellen.

### ... oder für ein vorhandenes Projekt

Um Application Insights zu einem neuen Projekt hinzuzufügen, klicken Sie mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer und klicken Sie auf Application Insights hinzufügen.

![Application Insights hinzufügen][Application Insights hinzufügen]

## <a name="run"></a>2. Ausführen des Projekts

Führen Sie Ihre Anwendung mit F5 aus und öffnen Sie einige Seiten.

In Visual Studio sehen Sie eine Anzahl der empfangenen Ereignisse.

![][]

## <a name="monitor"></a>3. Anzeigen der Überwachungsdaten

Öffnen Sie Application Insights für Ihr Projekt.

![Klicken Sie mit der rechten Maustaste auf Ihr Projekt und öffnen Sie das Azure-Portal][Klicken Sie mit der rechten Maustaste auf Ihr Projekt und öffnen Sie das Azure-Portal]

Suchen Sie in den Kacheln Anwendungsintegrität und Nutzungsanalyse nach den Daten. Beispiel:

![Klicken Sie, um weitere Daten anzuzeigen][Klicken Sie, um weitere Daten anzuzeigen]

Klicken Sie auf eine Kachel, um mehr Details anzuzeigen. [Weitere Informationen zu Kacheln und Berichten.][Weitere Informationen zu Kacheln und Berichten.]

> [WACOM.NOTE] Viele der Kacheln zeigen in dieser Vorschauversion eingeschränkte Details an.

## <a name="deploy"></a>4. Bereitstellen der Anwendung

Stellen Sie Ihre Anwendung bereit und sehen Sie zu, wie Daten gesammelt werden.

## <a name="next"></a>Nächste Schritte

[Weitere Informationen zu Kacheln und Berichten][Weitere Informationen zu Kacheln und Berichten.]

[Webtests][Webtests]

[Erfassen und Durchsuchen von Diagnoseprotokollen][Erfassen und Durchsuchen von Diagnoseprotokollen]

[Problembehandlung][Problembehandlung]

## Weitere Informationen

-   [Application Insights][Application Insights]
-   [Hinzufügen von Application Insights zu Ihrem Projekt][Hinzufügen von Application Insights zu Ihrem Projekt]
-   [Überwachen eines Live-Webservers][einen Agenten auf dem Server installieren]
-   [Erkunden von Metriken in Application Insights][Weitere Informationen zu Kacheln und Berichten.]
-   [Durchsuchen der Diagnoseprotokolle][Erfassen und Durchsuchen von Diagnoseprotokollen]
-   [Verfügbarkeitsüberwachung mit Webtests][Webtests]
-   [Nutzungsverfolgung mit Ereignissen und Metriken][Nutzungsverfolgung mit Ereignissen und Metriken]
-   [Fragen und Antworten sowie Problembehandlung][Problembehandlung]

<!--Link references-->

  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [Hinzufügen von Application Insights]: #add
  [Ausführen des Projekts]: #run
  [Anzeigen der Überwachungsdaten]: #monitor
  [Bereitstellen der Anwendung]: #deploy
  [Nächste Schritte]: #next
  [einen Agenten auf dem Server installieren]: ../app-insights-monitor-performance-live-website-now/
  [Erstellen eines ASP.NET-Projekts]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Application Insights hinzufügen]: ./media/appinsights/appinsights-03-addExisting.png
  []: ./media/appinsights/appinsights-09eventcount.png
  [Klicken Sie mit der rechten Maustaste auf Ihr Projekt und öffnen Sie das Azure-Portal]: ./media/appinsights/appinsights-04-openPortal.png
  [Klicken Sie, um weitere Daten anzuzeigen]: ./media/appinsights/appinsights-05-usageTiles.png
  [Weitere Informationen zu Kacheln und Berichten.]: ../app-insights-explore-metrics/
  [Webtests]: ../app-insights-monitor-web-app-availability/
  [Erfassen und Durchsuchen von Diagnoseprotokollen]: ../app-insights-search-diagnostic-logs/
  [Problembehandlung]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Hinzufügen von Application Insights zu Ihrem Projekt]: ../app-insights-monitor-application-health-usage/
  [Nutzungsverfolgung mit Ereignissen und Metriken]: ../app-insights-track-usage-custom-events-metrics/
