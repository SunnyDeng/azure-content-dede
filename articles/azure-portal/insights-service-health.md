<properties 
	pageTitle="Nachverfolgen der Dienstintegrität" 
	description="Ermitteln Sie den Zeitpunkt von Leistungsabfällen oder Dienstunterbrechungen, die bei Azure aufgetreten sind." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Nachverfolgen der Dienstintegrität

Azure informiert jeweils aktuell über Dienstunterbrechungen oder Leistungsabfälle. Sie können solche Ereignisse im Azure-Portal durchsuchen, und Sie können darüber hinaus mithilfe von [REST-API](https://msdn.microsoft.com/library/azure/dn931927.aspx) oder [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) programmgesteuert auf sämtliche Ereignisse zugreifen.

## Anzeigen der Dienstintegrität nach Region

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Auf der **Startseite** sehen Sie eine Kachel namens **Dienstintegrität** ![Startseite](./media/insights-service-health/Insights_Home.png).

3. Wenn Sie auf diese Kachel klicken, wird eine Liste aller Regionen in Azure angezeigt. Sie können auf eine beliebige Region klicken, um die Dienstintegrität für diese Region im Verlauf zu sehen. ![Startseite](./media/insights-service-health/Insights_Regions.png)

4. Außerdem können Sie die Details für einen einzelnen Incident anzeigen, indem Sie in der Tabelle darauf klicken.

## Durchsuchen der vollständigen Dienstintegritätsprotokolle

2. Klicken Sie auf **Durchsuchen**, und wählen Sie **Überwachungsprotokolle** aus. ![Hub durchsuchen](./media/insights-service-health/Insights_Browse.png)

3. Es wird ein Blatt geöffnet, das alle Ereignisse der letzten 7 Tage anzeigt, die sich auf eines Ihrer Abonnements ausgewirkt haben. In dieser Liste sind die Einträge zur Dienstintegrität enthalten. Da die Liste jedoch sehr lang sein kann, ist es unter Umständen schwierig, diese Einträge zu finden.

4. Klicken Sie auf den Befehl **Filter**.

5. Wählen Sie **Ereigniskategorie** und dann **Dienstintegrität**: ![Alle Ereignisse](./media/insights-service-health/Insights_Filter.png)

6. Klicken Sie auf **Aktualisiert**.

7. Jetzt sehen Sie alle Dienstintegritätsereignisse, die sich auf Ihr Abonnement ausgewirkt haben: ![Ressourcengruppen](./media/insights-service-health/Insights_HealthEvent.png)

8. Von hier aus können Sie zum Blatt mit Details wechseln, um ausführliche Informationen zum Ereignis anzuzeigen.
   
## Nächste Schritte

* [Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md) immer dann, wenn ein Ereignis auftritt.
* [Überwachen von Dienstmetriken](insights-how-to-customize-monitoring.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Überwachen der Verfügbarkeit und Reaktionsfähigkeit einer beliebigen Webseite](../app-insights-monitor-web-app-availability.md) mit Application Insights, um zu ermitteln, ob eine Seite offline ist.
 

<!---HONumber=Oct15_HO3-->