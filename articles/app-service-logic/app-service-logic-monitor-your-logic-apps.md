<properties 
	pageTitle="Überwachen Ihrer Logik-Apps in Azure App Service | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die Ergebnisse Ihrer Logik-Apps überprüfen können." 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="stepsic"/>

# Überwachen von Logik-Apps

Nach dem [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md) können Sie den vollständigen Verlauf ihrer Ausführung im Azure-Portal einsehen. Um den Verlauf anzuzeigen, wählen Sie **Durchsuchen** und dann **Logik-Apps** aus. Eine Liste der Logik-Apps in Ihrem Abonnement wird angezeigt. Jede Logik-App lässt sich auswählen, **aktivieren** und **deaktivieren**. **Aktivierte** Logik-Apps bedeutet, dass Trigger Ihre Logik-App in Reaktion auf auslösende Ereignisse ausführen. **Deaktivierte** Logik-Apps werden nicht als Reaktion auf Ereignisse ausgeführt.

![Übersicht](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Wenn das Blatt Ihrer Logik-App angezeigt wird, gibt es zwei nützliche Abschnitte:

- **Zusammenfassung** gibt Aufschluss über den aktuellen Status und dient als Einstiegspunkt für die Bearbeitung Ihrer Logik-App.
- **Alle Ausführungen** zeigt eine Liste der Ausführungen dieser Logik-App.

## Anzeigen der Ausführungen Ihrer App

![Alle Ausführungen](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Diese Liste der Ausführungen zeigt die **Startzeit**, den **Ausführungsbezeichner** (dient zum Aufrufen der REST-API) und die **Dauer** der jeweiligen Ausführung. Wählen Sie eine beliebige Zeile aus, um Details zur jeweiligen Ausführung anzuzeigen.

Das Blatt "Details" zeigt ein Diagramm mit der Ausführungszeit und Sequenz aller Aktionen in der Ausführung. Im Folgenden finden Sie die vollständige Liste aller Aktionen, die ausgeführt wurden:

![Ausführung und Aktionen](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Schließlich können Sie in den Abschnitten **Eingaben** und **Ausgaben** zu einer bestimmten Aktion alle Daten ablesen, die an die Aktion übergeben und von dieser empfangen wurden. Wählen Sie die Links aus, um den vollständigen Inhalt anzuzeigen (Sie können auch die Links zum Herunterladen des Inhalts kopieren).

Eine andere wichtige Information ist die **Nachverfolgungs-ID**. Dieser Bezeichner wird in den Headern aller Aufrufe der Aktion übergeben. Wenn Sie die Protokollierung in einem eigenen Dienst aktiviert haben, empfehlen wir die Protokollierung der Nachverfolgungs-ID und einen anschließenden Querverweis auf Ihre eigenen Protokolle mittels dieses Bezeichners.

## Anzeigen des Triggerverlaufs 

Abfragetrigger überprüfen die API in bestimmten Abständen, starten aber nicht unbedingt eine Ausführung, was abhängig von der Antwort ist (`200` bedeutet z. B. Ausführung, während `202` keine Ausführung bedeutet). Der Triggerverlauf bietet Ihnen eine Möglichkeit, alle Aufrufe einzusehen, die erfolgen, aber nicht die Logik-App ausführen (die Antworten vom Typ `202`):

![Triggerverlauf](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Für jeden Trigger können Sie anzeigen, ob er **ausgelöst** bzw. nicht ausgelöst wurde, oder ob ein **Fehler** aufgetreten ist. Um zu prüfen, warum Ihr Trigger nicht ausgeführt werden konnte, wählen Sie den Link **Ausgaben**. Wenn er ausgelöst wurde, wählen Sie den Link **Ausführung**, um zu prüfen, was nach seinem Auslösen passiert ist.

Beachten Sie, dass für *Pushtrigger* *nicht* angezeigt wird, wie oft hier eine Ausführung gestartet wurde. Stattdessen sehen Sie die *Rückrufregistrierungs*-Aufrufe, wenn die Logik-App sich registriert, um zurückgerufen zu werden. Falls Ihr Pushtrigger nicht funktioniert, liegt ggf. ein Problem mit der Registrierung vor (was Sie unter "Ausgaben" erkennen können). Andernfalls müssen Sie diese API speziell untersuchen.

## Aktivieren der Versionsverwaltung

Es gibt eine Funktion, die auf der Benutzeroberfläche derzeit nicht verfügbar ist (in Planung), aber über die [REST-API](https://msdn.microsoft.com/library/azure/mt643788.aspx) zur Verfügung steht. Wenn Sie die Definition einer Logik-App aktualisieren, wird die vorherige Version der Definition gespeichert. Dies liegt daran, dass wenn eine Ausführung bereits läuft, diese Ausführung auf die Version der Logik-App verweist, die zum Zeitpunkt des Ausführungsbeginns vorhanden war. Definitionen laufender Ausführungen können nicht geändert werden. Die REST-API für den Versionsverlauf ermöglicht Ihnen den Zugriff auf diese Informationen.
 

<!---HONumber=AcomDC_0302_2016-->