<properties 
	pageTitle="Empfangen von Warnbenachrichtigungen" 
	description="Benachrichtigt werden, wenn die Bedingungen von Warnungsregeln erfüllt sind." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# Empfangen von Warnbenachrichtigungen

Sie können auf der Grundlage von Überwachungsmetriken für Ihre Azure-Services oder von Ereignissen, die bei diesen auftreten, eine Warnung empfangen.

Bei einer Warnungsregel, die auf einem Metrikwert basiert, wird die Regel aktiv und kann eine Benachrichtigung senden, sobald der Wert einer angegebenen Metrik einen bestimmten Schwellenwert überschreitet. Bei einer Warnungsregel, die auf Ereignissen basiert, kann eine Regel bei *jedem* Ereignis eine Benachrichtigung senden oder nur dann, wenn eine bestimmte Anzahl von Ereignissen aufgetreten ist.

Beim Erstellen einer Warnungsregel können Sie Optionen auswählen, um eine E-Mail-Benachrichtigung an den Dienstadministrator und Co-Administratoren oder an einen anderen angegebenen Administrator zu senden. Eine Benachrichtigungs-E-Mail wird gesendet, wenn die Regel aktiv wird und nachdem eine Warnungsbedingung aufgelöst wurde.

Über die [REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx) oder das [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) können Sie Informationen zu Warnungsregeln programmgesteuert konfigurieren und abrufen.

## Erstellen einer Warnungsregel

1. Klicken Sie im [Portal](https://portal.azure.com/) auf **Durchsuchen** und anschließend auf eine Ressource, die Sie überwachen möchten.

2. Klicken Sie im Bereich **Operations** auf die Kachel **Warnungsregeln**.

3. Klicken Sie auf den Befehl **Warnung hinzufügen**. ![Warnung hinzufügen](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Sie können einen Namen für die Warnungsregel eingeben und eine Beschreibung auswählen, die in der Benachrichtigungs-E-Mail angezeigt wird.

5. Bei Auswahl von **Metrics** geben Sie eine Bedingung und einen Schwellenwert für die Metrik an. Dies ist der Zeitraum, den Azure verwendet, um die Warnungsaktivität zu überwachen und darzustellen. ![Bedingung und Schwellenwert](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Sie können auch **Events** auswählen, um eine Benachrichtigung zu erhalten, wenn ein bestimmtes Ereignis auftritt. ![Ereignisse](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Anschließend können Sie festlegen, dass eine E-Mail-Benachrichtigung an die zuständigen Administratoren gesendet wird.

Nachdem Sie auf **Speichern** geklickt haben, werden Sie innerhalb weniger Minuten informiert, sobald die von Ihnen gewählte Metrik den Schwellenwert überschreitet.

## Verwalten von Warnungsregeln

Sobald Sie eine Warnungsregel erstellt haben, können Sie eine Vorschau Ihres Warnschwellenwerts im Vergleich zur Metrik des vorhergehenden Tages anzeigen.

![Ereignisse](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Sie können diese Warnungsregel natürlich bearbeiten und **deaktivieren** und wieder **aktivieren**, wenn Sie vorübergehend keine Benachrichtigungen mehr empfangen möchten.

## Nächste Schritte

* [Überwachen von Dienstmetriken](insights-how-to-customize-monitoring.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Aktivieren von Überwachung und Diagnose](insights-how-to-use-diagnostics.md), um detaillierte Hochfrequenzmetriken zu Ihrem Dienst zu sammeln.
* [Überwachen der Verfügbarkeit und Reaktionsfähigkeit einer beliebigen Webseite](../app-insights-monitor-web-app-availability.md) mit Application Insights, um zu ermitteln, ob eine Seite offline ist.
* [Überwachen der Anwendungsleistung](insights-perf-analytics.md), um präzise Informationen zur Leistung Ihres Codes in der Cloud zu ermitteln.
* [Anzeigen von Ereignissen und Überwachungsprotokollen](insights-debugging-with-events.md), um sich über sämtliche Aktivitäten Ihres Diensts zu informieren.
* [Nachverfolgen des Dienststatus](insights-service-health.md), um den Zeitpunkt von Leistungsabfällen oder Dienstunterbrechungen zu ermitteln, die bei Azure aufgetreten sind.
 

<!---HONumber=62-->