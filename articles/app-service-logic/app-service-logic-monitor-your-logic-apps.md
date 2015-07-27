<properties 
	pageTitle="Überwachen von Logik-Apps" 
	description="Erfahren Sie, wie Sie die Ergebnisse Ihrer Logik-Apps überprüfen können." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="stepsic"/>

#Überwachen von Logik-Apps

Nachdem Sie anhand der Schritte, die unter [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md) beschrieben werden, eine Logik-App erstellt haben, wird der vollständige Verlauf ihrer Ausführung im Azure-Portal aufgeführt. Klicken Sie zum Anzeigen des Verlaufs auf der linken Seite des Portalbildschirms auf **Durchsuchen**, und wählen Sie **Logik-Apps** aus. Eine Liste der Logik-Apps in Ihrem Abonnement wird angezeigt. Eine Logik-App kann **aktiviert** oder **deaktiviert** sein. **Aktiviert** bedeutet bei Logik-Apps, dass Trigger Ihre Logik-App als Reaktion auf auslösende Ereignisse ausführen. **Deaktiviert** bedeutet, dass die Logik-App nicht als Reaktion auf Ereignisse ausgeführt wird.

![Übersicht](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Wenn das Blatt Ihrer Logik-App angezeigt wird, gibt es zwei nützliche Abschnitte:

- **Zusammenfassung** gibt Aufschluss über den aktuellen Status und dient als Einstiegspunkt für die Bearbeitung Ihrer Logik-App.
- **Alle Ausführungen** zeigt eine Liste der Ausführungen dieser Logik-App.

##Ausführungen

![Alle Ausführungen](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Diese Liste der Ausführungen zeigt die **Startzeit**, den **Ausführungsbezeichner** (dient zum Aufrufen der REST-API) und die **Dauer** der jeweiligen Ausführungen. Klicken Sie auf eine beliebige Zeile, um Details zur jeweiligen Ausführung anzuzeigen.

Das Blatt "Details" zeigt ein Diagramm mit der Ausführungszeit und Sequenz aller Aktionen in der Ausführung. Darunter ist die vollständige Liste aller Aktionen, die ausgeführt wurden.

![Ausführung und Aktionen](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Schließlich können Sie in den Abschnitten **Eingaben** und **Ausgaben** zu einer bestimmten Aktion alle Daten ablesen, die an die Aktion übergeben und von dieser empfangen wurden. Klicken Sie auf die Links, um den vollständigen Inhalt anzuzeigen (Sie können auch die Links zum Herunterladen des Inhalts kopieren).

Eine andere wichtige Information ist die **Nachverfolgungs-ID**. Dieser Bezeichner wird in den Headern aller Aufrufe der Aktion übergeben. Wenn Sie die Protokollierung in einem eigenen Dienst aktiviert haben, empfehlen wir die Protokollierung der Nachverfolgungs-ID und einen anschließenden Querverweis auf Ihre eigenen Protokolle mittels dieses Bezeichners.

##Triggerverlauf 

Abfragetrigger überprüfen die API in bestimmten Abständen, starten aber nicht unbedingt eine Ausführung, was abhängig von der Antwort ist (`200` bedeutet z. B. Ausführung, während `202` keine Ausführung bedeutet). Der Triggerverlauf bietet Ihnen eine Möglichkeit, alle Aufrufe einzusehen, die erfolgen, aber nicht die Logik-App ausführen (die Antworten vom Typ `202`).

![Triggerverlauf](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Für jeden Trigger können Sie anzeigen, ob er **ausgelöst** bzw. nicht ausgelöst wurde oder ob ein Fehler aufgetreten ist (**Fehler**). Um zu prüfen, warum Ihr Trigger nicht ausgeführt werden konnte, können Sie auf den Link **Ausgaben** klicken. Wenn er ausgelöst wurde, klicken Sie auf den Link **Ausführung**, um zu prüfen, was nach seinem Auslösen passiert ist.

Beachten Sie, dass für *Push*-Trigger hier *nicht* die Zeiten angezeigt werden, zu denen die Ausführungen gestartet wurden. Stattdessen sehen Sie Aufrufe vom Typ *Rückrufregistrierung*. Diese erfolgen, wenn die Logik-App registriert wird, um zurückgerufen zu werden. Falls Ihr Pushtrigger nicht funktioniert, liegt ggf. ein Problem mit der Registrierung vor (was Sie unter "Ausgaben" erkennen können). Andernfalls müssen Sie diese API speziell untersuchen.

##Versionsverwaltung

Es gibt eine weitere Funktion, die zurzeit noch nicht auf der Benutzeroberfläche verfügbar ist, aber über [die REST-API](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409) zur Verfügung steht. Wenn Sie die Definition einer Logik-App aktualisieren, wird die vorherige Version der Definition gespeichert. Dies liegt daran, dass wenn eine Ausführung bereits läuft, diese Ausführung auf die Version der Logik-App verweist, die zum Zeitpunkt des Ausführungsbeginns vorhanden war. Definitionen laufender Ausführungen können nicht geändert werden. Die REST-API für den Versionsverlauf ermöglicht Ihnen den Zugriff auf diese Informationen.
 

<!---HONumber=July15_HO3-->