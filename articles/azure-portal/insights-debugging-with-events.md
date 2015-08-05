<properties 
	pageTitle="Anzeigen von Ereignis- und Überwachungsprotokollen" 
	description="Erfahren Sie, wie Sie sämtliche Ereignisse anzeigen, die in Ihrem Azure-Abonnement auftreten." 
	authors="HaniKN-MSFT" 
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
	ms.date="04/28/2015" 
	ms.author="hanikn"/>

# Anzeigen von Ereignis- und Überwachungsprotokollen

Alle Vorgänge für Azure-Ressourcen – von der Erstellung und Löschung bis hin zum Erteilen oder Widerrufen des Zugriffs – werden vollständig vom Azure-Ressourcen-Manager überwacht Sie können die generierten Protokolle im Azure-Portal durchsuchen, und Sie können darüber hinaus mithilfe von [REST-API](https://msdn.microsoft.com/library/azure/dn931927.aspx) oder [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) programmgesteuert auf sämtliche Ereignisse zugreifen.

## Durchsuchen von Ereignissen mit Auswirkungen auf Ihr Azure-Abonnement

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Durchsuchen**, und wählen Sie **Überwachungsprotokolle** aus. ![Hub durchsuchen](./media/insights-debugging-with-events/Insights_Browse.png)
3. Es wird ein Blatt geöffnet, das alle Ereignisse der letzten 7 Tage anzeigt, die sich auf eines Ihrer Abonnements ausgewirkt hat. Ganz oben sehen Sie ein Diagramm mit Daten nach Schweregrad, darunter wird eine vollständige Liste der Protokolle angezeigt: ![Alle Ereignisse](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE]Sie können nur die letzten 500 Ereignisse für ein Abonnement im Azure-Portal anzeigen.

4. Sie können auf einen beliebigen Protokolleintrag klicken, um die zugehörigen Ereignisse anzuzeigen. Wenn Sie beispielsweise ein Element in einer Ressourcengruppe bereitstellen, werden möglicherweise zahlreiche Ressourcen erstellt oder geändert. Für jeden Eintrag werden die folgenden Informationen angezeigt:
    * Die **Stufe** des Ereignisses – beispielsweise kann es sich lediglich um einen Hinweis (**Information**) handeln, oder die Informationen weisen auf einen fehlerhaften Vorgang hin, über den Sie informiert werden müssen (**Fehler**). 
    * Der **Status** – der abschließende Status lautet im Allgemeinen **Erfolgreich** oder **Fehler**, kann aber für Vorgänge mit langer Ausführungszeit auch **Akzeptiert** lauten.
    * Informationen dazu, *wann* das Ereignis aufgetreten ist.
    * Informationen dazu, *wer* den Vorgang ausgeführt hat (sofern anwendbar). Nicht alle Vorgänge werden von Benutzern ausgeführt, einige Vorgänge werden von Back-End-Diensten initiiert, sodass sie keinen **Aufrufer** haben.
    * Die **Korrelations-ID** des Ereignisses – dies ist der eindeutige Bezeichner für diesen Satz an Vorgängen.

5. Von hier aus können Sie zum Blatt mit Details wechseln, um ausführliche Informationen zum Ereignis anzuzeigen.
   
    ![Ressourcengruppen](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Bei Ereignissen mit dem Status **Fehler** enthält diese Seite in der Regel einen **Unterstatus** und einen Abschnitt **Eigenschaften** mit nützlichen Details, die für Debuggingzwecke verwendet werden können.

## Filtern von spezifischen Protokollen

Um nur Ereignisse anzuzeigen, die für eine bestimmte Entität gelten oder einen bestimmten Typ aufweisen, können Sie die Informationen auf dem Blatt "Überwachungsprotokolle" durch Klicken auf den Befehl **Filter** filtern. Sie können auch das Blatt "Filter" benutzen, um die **Zeitspanne** für die angezeigten Überwachungsprotokolle zu ändern.

Nachdem Sie auf diesen Befehl geklickt haben, wird ein neues Blatt geöffnet:

![Filter](./media/insights-debugging-with-events/Insights_EventFilter.png)

Es gibt vier Arten von Filtern:

1. Nach Abonnement
2. Nach **Ressourcengruppe**
3. Nach **Ressourcentyp**
4. Nach **Ressource** – zur Filterung nach einer bestimmten Ressource müssen Sie die vollständige *Ressourcen-ID* der Ressource angeben, an der Sie interessiert sind

Zusätzlich können Sie Ereignisse auch nach dem Benutzer filtern, der das Ereignis ausgeführt hat, oder Sie filtern nach der Ereignisstufe.

Nachdem Sie ausgewählt haben, was Sie anzeigen möchten, klicken Sie unten auf dem Blatt auf die Schaltfläche **Aktualisieren**.

## Überwachen von Ereignissen mit Auswirkungen auf bestimmte Ressourcen

1. Klicken Sie auf **Durchsuchen**, um nach der Ressource zu suchen, an der Sie interessiert sind. Sie können alle Protokolle für eine gesamte **Ressourcengruppe** anzeigen.
2. Führen Sie auf dem Blatt für die Ressource einen Bildlauf bis zur Kachel **Ereignisse** durch. ![Kachel "Ereignisse"](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Klicken Sie auf diese Kachel, um die gefilterten Ereignisse für die ausgewählte Ressource anzuzeigen. Sie können mit dem Befehl **Filter** den Zeitraum ändern oder spezifischere Filter anwenden.

## Nächste Schritte

* [Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md) immer dann, wenn ein Ereignis auftritt.
* [Überwachen von Dienstmetriken](insights-how-to-customize-monitoring.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Nachverfolgen der Dienstintegrität](insights-service-health.md), um den Zeitpunkt von Leistungsabfällen oder Dienstunterbrechungen zu ermitteln, die in Azure aufgetreten sind.  

<!---HONumber=July15_HO4-->