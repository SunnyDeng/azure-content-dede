<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Erkunden von Metriken

Falls Sie [Ihr Webprojekt noch nicht für Application Insights eingerichtet][Ihr Webprojekt noch nicht für Application Insights eingerichtet] haben, tun Sie dies jetzt.

Hier sind die Berichte, die in Application Insights für Ihre Anwendung angezeigt werden. Um aus Visual Studio zu dieser Seite zu gelangen, klicken Sie mit der rechten Maustaste auf Ihr Webprojekt und klicken Sie auf „Application Insights öffnen“. Im Startbereich der Microsoft Azure-Vorschau klicken Sie stattdessen auf Durchsuchen, Application Insights und wählen Sie Ihr Projekt aus.

-   [Anwendungsintegrität][Anwendungsintegrität]
-   [Nutzungsanalyse][Nutzungsanalyse]
-   [Nächste Schritte][Nächste Schritte]

## <a name="health"></a>Anwendungsintegrität

Die Anwendungsintegrität wird überwacht, indem der Code Ihrer Anwendung instrumentiert wird.

![][0]

### Durchschnittliche Reaktionszeit

Misst die Zeit zwischen dem Eingang einer Webanfrage bei Ihrer Anwendung und der zurückgegebenen Antwort.

Die Punkte zeigen einen gleitenden Durchschnitt an. Bei einer großen Anzahl von Anfragen kann es sein, dass einige Anfragen vom Durchschnitt abweichen, ohne dass sich die Grafik offensichtlich verändert.

Suchen Sie nach ungewöhnlichen Spitzen. Im Allgemeinen steigt mit der Anzahl der Anfragen auch die Antwortzeit. Wenn Sie einen überproportionalen Anstieg entdecken, kann es sein, dass Ihre App an ein Ressourcenlimit stößt, wie z. B. Prozessorleistung oder die Kapazität eines verwendeten Diensts.

### Anfragen

Die Anzahl der eingegangenen Anfragen pro Zeitraum. Vergleichen Sie dies mit den Ergebnissen in den anderen Grafiken, um zu sehen, wie sich Ihre Anwendung unter schwankender Last verhält.

### Webtests

![][1]

Unter [Webtests][Webtests] sehen Sie die Ergebnisse von Webanfragen, die in regelmäßigen Abständen von Application Insights-Servern weltweit an Ihren Server geschickt werden.

Prüfen Sie, ob die Ergebnisse mit der Anzahl der Anfragen übereinstimmen.

[Einrichten von Webtests][Webtests].

### Langsamste Anfragen

![][2]

Zeigt an, bei welchen Anfragen möglicherweise eine Leistungssteigerung erforderlich ist.

### Diagnosesuche

![][3]

Falls Sie die [Diagnoseprotokollierung eingerichtet haben][Diagnoseprotokollierung eingerichtet haben], klicken Sie sich durch, um die neuesten Ereignisse anzuzeigen.

### Fehlgeschlagene Anfragen

![][4]

Die Anzahl der Anfragen, die unbehandelte Ausnahmen ausgelöst haben.

## <a name="usage"></a>Nutzungsanalyse

![][5]

Die Nutzungsdaten stammen teilweise aus dem Server und teilweise aus den [Skripts in den Webseiten][Ihr Webprojekt noch nicht für Application Insights eingerichtet].

### Sitzungen pro Browser

Eine *Sitzung* ist ein Zeitraum, der beginnt, wenn ein Benutzer eine beliebige Seite auf Ihrer Website öffnet. Die Sitzung endet, wenn der Benutzer über einen Zeitraum von 30 Minuten keine Webanfrage geschickt hat.

Klicken Sie, um in das Diagramm hereinzuzoomen.

### Häufigste Seitenaufrufe

Zeigt die Gesamtanzahl der vergangenen 24 Stunden an.

Klicken Sie, um Grafiken für Seitenaufrufe der vergangenen Woche anzuzeigen.

### Kacheln neu anordnen

![Klicken Sie auf Einstellungen, Anpassen][Klicken Sie auf Einstellungen, Anpassen]

## <a name="next"></a>Nächste Schritte

[Einrichten von Webtests][Webtests]

[Erfassen und Durchsuchen von Diagnoseprotokollen][Diagnoseprotokollierung eingerichtet haben]

[Problembehandlung][Problembehandlung]

## Weitere Informationen

-   [Application Insights][Application Insights]
-   [Hinzufügen von Application Insights zu Ihrem Projekt][Ihr Webprojekt noch nicht für Application Insights eingerichtet]
-   [Überwachen eines Live-Webservers][Überwachen eines Live-Webservers]
-   [Erkunden von Metriken in Application Insights][Erkunden von Metriken in Application Insights]
-   [Durchsuchen der Diagnoseprotokolle][Diagnoseprotokollierung eingerichtet haben]
-   [Verfügbarkeitsüberwachung mit Webtests][Webtests]
-   [Nutzungsverfolgung mit Ereignissen und Metriken][Nutzungsverfolgung mit Ereignissen und Metriken]
-   [Fragen und Antworten sowie Problembehandlung][Problembehandlung]

<!--Link references-->

  [Ihr Webprojekt noch nicht für Application Insights eingerichtet]: ../app-insights-monitor-application-health-usage/
  [Anwendungsintegrität]: #health
  [Nutzungsanalyse]: #usage
  [Nächste Schritte]: #next
  [0]: ./media/appinsights/appinsights-42reqs.png
  [1]: ./media/appinsights/appinsights-43webtests.png
  [Webtests]: ../app-insights-monitor-web-app-availability/
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-45diagnostic.png
  [Diagnoseprotokollierung eingerichtet haben]: ../app-insights-search-diagnostic-logs/
  [4]: ./media/appinsights/appinsights-46failed.png
  [5]: ./media/appinsights/appinsights-47usage.png
  [Klicken Sie auf Einstellungen, Anpassen]: ./media/appinsights/appinsights-21-customizeblade.png
  [Problembehandlung]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Überwachen eines Live-Webservers]: ../app-insights-monitor-performance-live-website-now/
  [Erkunden von Metriken in Application Insights]: ../app-insights-explore-metrics/
  [Nutzungsverfolgung mit Ereignissen und Metriken]: ../app-insights-track-usage-custom-events-metrics/
