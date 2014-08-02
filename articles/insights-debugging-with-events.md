<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn" />

Überwachen der Ereignisse, die Auswirkungen auf Ihre Azure-Websites oder Ressourcengruppen haben
================================================================================================

1.  Melden Sie sich auf dem [Azure-Portal (Vorschau)](https://portal.azure.com/) an.

2.  Klicken Sie auf die Schaltfläche **Browse** in der linken Navigationsleiste (auch bezeichnet als **Sprungleiste**).

	![Hub durchsuchen](./media/insights-debugging-with-events/Insights_Browse.png)

1.  Wählen Sie dann entweder **Resource groups** oder **Websites** aus (je nachdem, welche Ereignisse Sie untersuchen wollen). Die zu Illustrationszwecken in diesem Dokument gezeigten Screenshots beziehen sich auf Ereignisse für Ressourcengruppen.

2.  Klicken Sie im Fenster **Resource groups** auf den Namen der Ressourcengruppe. Dadurch gelangen Sie in das Fenster der betreffenden Ressourcengruppe.

    ![Ressourcengruppen](./media/insights-debugging-with-events/Insights_SelectRG.png)

3.  Das Ressourcengruppenfenster enthält unter anderem einen Teilbereich mit der Bezeichnung **Events in the past week**. Die einzelnen Balken in diesem Teilbereich entsprechen der Anzahl der Ereignisse, die an den verschiedenen Tagen in der Vorwoche erkannt worden sind. Jeder dieser Balken kann zwei verschiedene Farben annehmen: Blau und Pink. Pink steht für die an dem betreffenden Tag als **Failed** erkannten Ereignisse, Blau für alle anderen Ereignisse.

	![Ressourcengruppen](./media/insights-debugging-with-events/Insights_RGBlade.png)

1.  Klicken Sie nun auf den Teilbereich **Events in the past week**. Daraufhin öffnet sich ein neues Fenster mit dem Titel **Events**, in dem alle Ereignisse aufgelistet sind, die in der Vorwoche Auswirkungen auf Ihre Ressourcengruppe hatten.

	![Ressourcengruppen](./media/insights-debugging-with-events/Insights_AllEvents.png)

1.  Klicken Sie auf eines dieser Ereignisse.

	![Ressourcengruppen](./media/insights-debugging-with-events/Insights_EventDetails.png)

Daraufhin öffnet sich ein neues Fenster mit ausführlichen Details zu dem betreffenden Ereignis. Bei Ereignissen mit dem Status **Failed** enthält diese Seite in der Regel einen **Substatus**- und einen **Properties**-Abschnitt mit nützlichen Details, die für Debuggingzwecke verwendet werden können.

