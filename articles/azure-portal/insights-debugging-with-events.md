<properties 
	pageTitle="Debuggen mit Ereignissen" 
	description="Erfahren Sie, wie Ereignisse in Azure angezeigt werden." 
	authors="HaniKN-MSFT" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="hanikn"/>

# Überwachen der Ereignisse, die Auswirkungen auf Ihre Azure-Ressourcen oder Ressourcengruppen haben

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.
2. Klicken Sie auf die Schaltfläche **Durchsuchen** in der linken Navigationsleiste (auch bezeichnet als **Sprungleiste**).  
    ![Browse Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. Wählen Sie dann eine Ressource aus (abhängig von den Ereignissen, die Sie interessieren). Die zu Illustrationszwecken in diesem Dokument gezeigten Screenshots beziehen sich auf Ereignisse für Ressourcengruppen.
4. Klicken Sie im Fenster **Ressourcengruppen** auf den Namen der Ressourcengruppe. Dadurch gelangen Sie in das Fenster der betreffenden Ressourcengruppe.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_SelectRG.png)
5. Das Ressourcengruppenfenster enthält unter anderem einen Teilbereich mit der Bezeichnung **Ereignisse der letzten Woche**. Die einzelnen Balken in diesem Teilbereich entsprechen der Anzahl der Ereignisse, die an den verschiedenen Tagen in der Vorwoche erkannt worden sind. Jeder dieser Balken kann zwei verschiedene Farben annehmen: Blau und Pink. Pink steht für die an dem betreffenden Tag **fehlgeschlagenen** Ereignisse, Blau für alle anderen Ereignisse.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_RGBlade.png)
6. Klicken Sie nun auf den Teil **Ereignisse der letzten Woche**. Daraufhin öffnet sich ein neues Fenster mit dem Titel **Ereignisse**, in dem alle Ereignisse aufgelistet sind, die in der Vorwoche Auswirkungen auf Ihre Ressourcengruppe hatten.
    ![Resource groups](./media/insights-debugging-with-events/Insights_AllEvents.png)
7. Klicken Sie auf eines dieser Ereignisse.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_EventDetails.png)  
    Daraufhin öffnet sich ein neues Fenster mit ausführlichen Details zu dem betreffenden Ereignis. Für **fehlgeschlagene** Ereignisse enthält diese Seite in der Regel einen **Unterstatus** und einen Bereich **Eigenschaften**, in dem Sie nützliche Details für die Fehlerbehebung finden.


<!--HONumber=46--> 
 