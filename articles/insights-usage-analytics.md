<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="vladj" />

Analysen für Websites
=====================

Würden Sie gern wissen, wie viele Benutzer Ihre Website besucht haben? Oder wie lange das Laden einer Seite durchschnittlich dauert, oder welche Browser verwendet werden? Sie brauchen nur einige wenige Skriptzeilen in Ihre Webseiten einzufügen, um zu erfahren, wie Ihre Kunden Ihre Website nutzen.

![Endbenutzeranalyse](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

Einrichten der Endbenutzeranalyse
---------------------------------

1.  Klicken Sie im Fenster **Web site** auf den Teilbereich **End user analytics**.
2.  Wählen Sie im Fenster **Configuration** das gesamte Instrumentationsskript aus, und kopieren Sie es.

    ![Konfiguration](./media/insights-usage-analytics/Insights_CopyCode.png)

3.  Fügen Sie das Skript in jede Webseite ein, und zwar unmittelbar vor dem abschließenden &lt;/head\>-Tag. Achten Sie darauf, das Skript in alle Ihre Webseiten einzufügen. Wenn Sie mit ASP.NET arbeiten, können Sie dazu das Skript in die Masterseite Ihrer Anwendung einfügen.

4.  Stellen Sie Ihre Webanwendung bereit, und benutzen Sie sie. Nach etwa 5-10 Minuten werden Analysedaten angezeigt.

Erforschen der Daten
--------------------

Im Teilbereich "Browsers" können Sie weitere Details zu den verschiedenen Browsern und den Browser-Versionen aufrufen.

![Browser](./media/insights-usage-analytics/Insights_Browsers.png)

Im Teilbereich "Analytics" wird Folgendes angezeigt: - Eine Aufschlüsselung nach den verschiedenen Gerätetypen, unter anderem Desktop- und Mobilgeräte. - Die 5 am häufigsten aufgerufenen Seiten und Diagramme der Seitenladezeit in der Vorwoche. Auch die Anzahl der Sitzungen und Ansichten wird angezeigt.

![Am häufigsten aufgerufene Seiten](./media/insights-usage-analytics/Insights_TopPages.png)

-   Die in der Vorwoche langsamsten Seiten, damit Sie diese optimieren können, um Ihre geschäftlichen Ziele zu erreichen.

