<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills"></tags>

# Analysen für Websites

Würden Sie gern wissen, wie viele Benutzer Ihre Website besucht haben? Oder wie lange das Laden einer Seite durchschnittlich dauert, oder welche Browser verwendet werden? Sie brauchen nur einige wenige Skriptzeilen in Ihre Webseiten einzufügen, um zu erfahren, wie Ihre Kunden Ihre Website nutzen.

![Endbenutzeranalyse][Endbenutzeranalyse]

## Einrichten der Endbenutzeranalyse

1.  Klicken Sie im Fenster **Web site** auf den Teilbereich **End user analytics**.
2.  Wählen Sie im Fenster **Configuration** das gesamte Instrumentationsskript aus, und kopieren Sie es.  
    ![Fenster 'Configuration'][Fenster 'Configuration']
3.  Fügen Sie das Skript in jede Webseite ein, und zwar unmittelbar vor dem abschließenden
    Tag. Achten Sie darauf, das Skript in alle Ihre Webseiten einzufügen. Wenn Sie mit ASP.NET arbeiten, können Sie dazu das Skript in die Masterseite Ihrer Anwendung einfügen.
4.  Stellen Sie Ihre Webanwendung bereit, und benutzen Sie sie. Nach etwa 5-10 Minuten werden Analysedaten angezeigt.

## Erforschen der Daten

Im Teilbereich "Browsers" können Sie weitere Details zu den verschiedenen Browsern und den Browser-Versionen aufrufen.

![Browser][Browser]

Im Teilbereich "Analytics" wird Folgendes angezeigt:

-   Eine Aufschlüsselung nach den verschiedenen Gerätetypen, unter anderem Desktop- und Mobilgeräte
-   Die fünf am häufigsten aufgerufenen Seiten und Diagramme der Seitenladezeit in der Vorwoche. Auch die Anzahl der Sitzungen und Ansichten wird angezeigt.  
    ![Am häufigsten aufgerufene Seiten][Am häufigsten aufgerufene Seiten]
-   Die in der Vorwoche langsamsten Seiten, damit Sie diese optimieren können, um Ihre geschäftlichen Ziele zu erreichen.

  [Endbenutzeranalyse]: ./media/insights-usage-analytics/Insights_ConfiguredExperience.png
  [Fenster 'Configuration']: ./media/insights-usage-analytics/Insights_CopyCode.png
  [Browser]: ./media/insights-usage-analytics/Insights_Browsers.png
  [Am häufigsten aufgerufene Seiten]: ./media/insights-usage-analytics/Insights_TopPages.png
