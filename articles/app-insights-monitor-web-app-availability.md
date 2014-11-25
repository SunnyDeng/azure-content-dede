<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Überwachen der Verfügbarkeit und Reaktionszeit von Webanwendungen

Nachdem Sie die Webanwendung bereitgestellt haben, können Sie Webtests einrichten, um die Verfügbarkeit und Reaktionszeit zu überwachen. Application Insights sendet regelmäßig Webanforderungen von verschiedenen Punkten rund um die Welt und benachrichtigt Sie, wenn Ihre Anwendung langsam oder gar nicht reagiert.

Sie können für jeden HTTP-Endpunkt, der über das öffentliche Internet zugänglich ist, Webtests einrichten.

1. [Erstellen einer neuen Anwendung][Erstellen einer neuen Anwendung]
+ [Einrichten eines Webtests][Einrichten eines Webtests]
+ [Anzeigen von Überwachungsberichten][Anzeigen von Überwachungsberichten]
+ [Bearbeiten oder Deaktivieren von Tests][Bearbeiten oder Deaktivieren von Tests]
+ [Nächste Schritte][Nächste Schritte]

## <a name="create"></a>1. Erstellen einer neuen Anwendung

Überspringen Sie diesen Schritt, wenn Sie bereits [Nutzung und Integrität der Webanwendung überwachen][Nutzung und Integrität der Webanwendung überwachen] und nun die Verfügbarkeitsdaten an derselben Stelle anzeigen möchten.

Falls Sie jedoch die Ergebnisse separat erhalten möchten, melden Sie sich bei der Microsoft Azure-Vorschau an, und erstellen Sie eine neue Anwendung in Application Insights.

![Neu \> Application Insights][Neu \> Application Insights]

## <a name="setup"></a>2. Einrichten eines Webtests

Klicken Sie im Startfenster für Ihre Anwendung in Application Insights auf eine leere Webtest-Kachel.

![Auf leeren Verfügbarkeitstest klicken][Auf leeren Verfügbarkeitstest klicken]

> *Sie haben bereits Webtests? Klicken Sie durch die Webtest-Kacheln, und wählen Sie „Add Webtest“ aus.*

Geben Sie einen Namen für den Test und die zu testende URL ein. Die URL muss über das öffentliche Internet zugänglich sein.

![Mindestens die URL der Website eintragen][Mindestens die URL der Website eintragen]

-   Teststandorte sind die Orte, von denen unsere Server Webanforderungen an Ihre URL senden. Wählen Sie zwei oder drei aus, sodass Sie Probleme mit der Website von Netzwerkproblemen unterscheiden können. Sie können maximal drei auswählen.

-   Erfolgskriterien - Geben Sie die akzeptablen HTTP-Rückgabecodes an - normalerweise 200.

    Sie können auch eine Zeichenfolge angeben, die in jeder richtigen Antwort vorhanden sein sollte. Dies muss eine Zeichenfolge in Klartext, ohne Platzhalter sein. Vergessen Sie nicht, diese zu aktualisieren, wenn sich der Inhalt ändert.

-   Warnungen - Standardmäßig werden Warnungen an Sie gesendet, wenn über 15 Minuten mehrere Fehlmeldungen auftreten. Sie können dies auf engere Überwachung einstellen oder die E-Mail-Adresse für die Benachrichtigung ändern.

### Testen weiterer URLs

Sie können weitere Tests für beliebig viele URLs hinzufügen. Neben dem Testen der Startseite können Sie sich auch vergewissern, dass die Datenbank läuft, indem Sie eine Such-URL testen.

![Im Webtest-Fensterbereich auf „Add“ klicken][Im Webtest-Fensterbereich auf „Add“ klicken]

## <a name="monitor"></a>3. Anzeigen von Überwachungsberichten

Nach 1 bis 2 Minuten können Sie das Anwendungsfenster schließen und erneut öffnen. (In dieser Version wird es nicht automatisch aktualisiert.)

![Ergebnisübersicht im Startfenster][Ergebnisübersicht im Startfenster]

Dies ist eine Zusammenfassung. Wenn Sie mehrere Webtests für die Anwendung definiert haben, sind diese alle hier kombiniert.

Klicken Sie durch das Webtest-Fenster, um eine Liste Ihrer Webtests anzuzeigen.

Öffnen Sie einen bestimmten Webtest.

![Auf bestimmten Webtest klicken][Auf bestimmten Webtest klicken]

Blättern Sie im Fenster des jeweiligen Webtests nach unten bis zu den fehlgeschlagenen Tests, und wählen Sie ein Ergebnis aus.

![Auf bestimmten Webtest klicken][1]

Im Ergebnis wird die Fehlerursache angezeigt.

![Ausführungsergebnis des Webtests][Ausführungsergebnis des Webtests]

Wenn Sie weitere Details wünschen, können Sie die Ergebnisdatei herunterladen und in Visual Studio überprüfen.

## <a name="edit"></a>4. Bearbeiten oder Deaktivieren von Tests

Öffnen Sie einen Test, um ihn zu bearbeiten oder zu deaktivieren.

![Webtest bearbeiten oder deaktivieren][Webtest bearbeiten oder deaktivieren]

Sie können Webtests beispielsweise deaktivieren, während Sie Wartungsarbeiten für den Dienst durchführen.

## <a name="next"></a>Nächste Schritte

[Durchsuchen der Diagnoseprotokolle][Durchsuchen der Diagnoseprotokolle]

[Problembehandlung][Problembehandlung]

## <a name="next"></a>Weitere Informationen

-   [Application Insights][Application Insights]
-   [Hinzufügen von Application Insights zu Ihrem Projekt][Nutzung und Integrität der Webanwendung überwachen]
-   [Überwachen eines Live-Webservers][Überwachen eines Live-Webservers]
-   [Erkunden von Metriken in Application Insights][Erkunden von Metriken in Application Insights]
-   [Durchsuchen der Diagnoseprotokolle][Durchsuchen der Diagnoseprotokolle]
-   [Verfügbarkeitsüberwachung mit Webtests][Verfügbarkeitsüberwachung mit Webtests]
-   [Nutzungsverfolgung mit Ereignissen und Metriken][Nutzungsverfolgung mit Ereignissen und Metriken]
-   [Fragen und Antworten sowie Problembehandlung][Problembehandlung]

<!--Link references-->

  [Erstellen einer neuen Anwendung]: #create
  [Einrichten eines Webtests]: #setup
  [Anzeigen von Überwachungsberichten]: #monitor
  [Bearbeiten oder Deaktivieren von Tests]: #edit
  [Nächste Schritte]: #next
  [Nutzung und Integrität der Webanwendung überwachen]: ../app-insights-monitor-application-health-usage/
  [Neu \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
  [Auf leeren Verfügbarkeitstest klicken]: ./media/appinsights/appinsights-12avail.png
  [Mindestens die URL der Website eintragen]: ./media/appinsights/appinsights-13availChoices.png
  [Im Webtest-Fensterbereich auf „Add“ klicken]: ./media/appinsights/appinsights-16anotherWebtest.png
  [Ergebnisübersicht im Startfenster]: ./media/appinsights/appinsights-14availSummary.png
  [Auf bestimmten Webtest klicken]: ./media/appinsights/appinsights-15webTestList.png
  [1]: ./media/appinsights/appinsights-17-availViewDetails.png
  [Ausführungsergebnis des Webtests]: ./media/appinsights/appinsights-18-availDetails.png
  [Webtest bearbeiten oder deaktivieren]: ./media/appinsights/appinsights-19-availEdit.png
  [Durchsuchen der Diagnoseprotokolle]: ../app-insights-search-diagnostic-logs/
  [Problembehandlung]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Überwachen eines Live-Webservers]: ../app-insights-monitor-performance-live-website-now/
  [Erkunden von Metriken in Application Insights]: ../app-insights-explore-metrics/
  [Verfügbarkeitsüberwachung mit Webtests]: ../app-insights-monitor-web-app-availability/
  [Nutzungsverfolgung mit Ereignissen und Metriken]: ../app-insights-track-usage-custom-events-metrics/
