<properties linkid="web-sites-create-web-jobs" urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Use WebJobs to run background tasks in Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Web Jobs, background tasks" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Verwenden von WebJobs zum Ausführen von Hintergrundaufgaben in Microsoft Azure-Websites

Microsoft Azure-Websites ermöglichen die Ausführung von Programmen oder Skripts auf Ihrer Website entweder bedarfsgesteuert, fortlaufend oder nach einem Zeitplan. Es fallen keine zusätzlichen Kosten für die Verwendung von Microsoft Azure WebJobs an, sofern Sie nicht das weiter unten in diesem Artikel beschriebene Feature **Always on** aktivieren möchten.

## Inhaltsverzeichnis

-   [Akzeptable Dateitypen für Skripte][Akzeptable Dateitypen für Skripte]
-   [Erstellen einer bedarfsgesteuerten Aufgabe][Erstellen einer bedarfsgesteuerten Aufgabe]
-   [Erstellen einer fortlaufend ausgeführten Aufgabe][Erstellen einer fortlaufend ausgeführten Aufgabe]
-   [Erstellen einer geplanten Aufgabe][Erstellen einer geplanten Aufgabe]

    -   [Geplante Aufträge und Azure Scheduler][Geplante Aufträge und Azure Scheduler]
-   [Anzeigen des Auftragsverlaufs][Anzeigen des Auftragsverlaufs]
-   [Hinweise][Hinweise]
-   [Nächste Schritte][Nächste Schritte]

    -   [Effektiver arbeiten mit dem Microsoft Azure WebJobs SDK][Effektiver arbeiten mit dem Microsoft Azure WebJobs SDK]
    -   [Alternative Bereitstellungsmethoden][Alternative Bereitstellungsmethoden]
    -   [Zusätzliche Ressourcen][Zusätzliche Ressourcen]

<a name="acceptablefiles"></a>

## Akzeptable Dateitypen für Skripte

Die folgenden Dateitypen sind als ausführbare Skripts zulässig:

.cmd, .bat, .exe (bei Verwendung von windows cmd)

.ps1 (bei Verwendung von powershell)

.sh (bei Verwendung von bash)

.php (bei Verwendung von php)

.py (bei Verwendung von python)

.js (bei Verwendung von node)

<a name="CreateOnDemand"></a>

## Erstellen einer bedarfsgesteuerten Aufgabe

1.  Klicken Sie auf der Seite **WebJobs** in der Befehlsleiste auf **Hinzufügen**. Das Dialogfeld **Neuer Auftrag** wird angezeigt.

    ![Bedarfsgesteuerte Aufgabe][Bedarfsgesteuerte Aufgabe]

2.  Geben Sie unter **Name** einen Namen für die Aufgabe ein. Der Name muss mit einem Buchstaben oder einer Ziffer beginnen und darf nur die Sonderzeichen "-" und "\_" enthalten.

3.  Navigieren Sie im Feld **Inhalt (ZIP-Dateien – höchstens 100 MB)** zu der ZIP-Datei mit dem Skript. Die ZIP-Datei sollte die ausführbare Datei ((.exe, .cmd, .bat, .sh, .php, .py, .js) und alle Hilfsdateien enthalten, die zum Ausführen des Programms oder Skripts benötigt werden.

4.  Wählen Sie im Feld für die Ausführungshäufigkeit die Option **Bedarfsgesteuert ausführen** aus.

5.  Aktivieren Sie das Kontrollkästchen rechts unten im Dialogfeld, um das Skript auf die Website hochzuladen. Der Name, den Sie der Aufgabe gegeben haben, wird in der Liste angezeigt:

    ![Aufgabenliste][Aufgabenliste]

6.  Wählen Sie zur Ausführung des Skripts den Namen in der Liste aus, und klicken Sie in der Befehlsleiste unten auf der Portalseite auf **Einmal ausführen**.

    ![Einmal ausführen][Einmal ausführen]

<a name="CreateContinuous"></a>

## Erstellen einer fortlaufend ausgeführten Aufgabe

1.  Führen Sie zum Erstellen einer fortlaufend ausgeführten Aufgabe dieselben Schritte wie zum Erstellen einer einmal ausgeführten Aufgabe aus, wählen Sie im Feld für die Ausführungshäufigkeit jedoch **Fortlaufend ausführen** aus.

    ![Neue fortlaufende Aufgabe][Neue fortlaufende Aufgabe]

2.  Wählen Sie die Aufgabe in der Liste aus und klicken Sie in der Befehlsleiste auf **Start** oder **Beenden**, um eine fortlaufend ausgeführte Aufgabe zu starten oder zu beenden.

> [WACOM.NOTE] Wenn die Website auf mehr als einer Instanz ausgeführt wird, wird auf allen Ihren Instanzen eine fortlaufend ausgeführte Aufgabe ausgeführt. Bedarfsgesteuerte und geplante Aufgaben werden auf einer einzigen Instanz ausgeführt, die von Microsoft Azure für den Lastenausgleich ausgewählt wurde.

> [WACOM.NOTE]
> Bei fortlaufenden Aufgaben wird empfohlen, auf der Seite **Konfigurieren** der Website die Option **Always On** zu aktivieren. Das Feature "Always On" ist im Modus **Basic** und **Standard** verfügbar und verhindert, dass Websites entladen werden, selbst wenn sie einige Zeit nicht genutzt wurden. Wenn Ihre Website immer geladen ist, kann die fortlaufende Aufgabe zuverlässiger ausgeführt werden.

<a name="CreateScheduled"></a>

## Erstellen einer geplanten Aufgabe

1.  Zum Erstellen einer geplanten Aufgabe führen Sie dieselben Schritte wie zuvor aus, wählen jedoch im Feld für die Ausführungshäufigkeit die Option **Gemäß einem Zeitplan ausführen** aus.

    ![Neuer geplanter Auftrag][Neuer geplanter Auftrag]

2.  Wählen Sie die Scheduler-Region für den Auftrag aus, und klicken Sie dann auf den Pfeil unten rechts im Dialogfeld, um zum nächsten Bildschirm zu wechseln.

3.  Wählen Sie im Dialogfeld <strong>Auftrag erstellen<strong> den gewünschten Wiederholungstyp aus: **Einmaliger Auftrag** oder **Periodischer Auftrag**.

    ![Wiederholung planen][Wiederholung planen]

4.  Wählen Sie außerdem eine Startzeit aus: **Jetzt** oder **Bestimmte Zeit**.

    ![Geplante Startzeit][Geplante Startzeit]

5.  Wenn Sie zu einer bestimmten Zeit starten möchten, wählen Sie die Startzeitwerte unter **Startet am** aus.

    ![Start zu einer bestimmten Zeit planen][Start zu einer bestimmten Zeit planen]

6.  Wenn Sie einen periodischen Auftrag ausgewählt haben, können Sie mit der Option **Wiederholen alle** die Häufigkeit und mit der Option **Endet am** eine Endzeit angeben.

    ![Wiederholung planen][1]

7.  Wenn Sie **Wochen** auswählen, können Sie das Kontrollkästchen **In einem bestimmten Zeitplan** aktivieren und die Wochentage angeben, an denen der Auftrag ausgeführt werden soll.

    ![Wochentage planen][Wochentage planen]

8.  Wenn Sie **Monate** auswählen und das Kontrollkästchen **In einem bestimmten Zeitplan** aktivieren, können Sie festlegen, dass der Auftrag an bestimmten bezifferten Tagen im Monat ausgeführt wird.

    ![Bestimmte Daten im Monat planen][Bestimmte Daten im Monat planen]

9.  Wenn Sie **Wochentage** auswählen, können Sie angeben, an welchem Wochentag bzw. welchen Wochentagen im Monat der Auftrag ausgeführt werden soll.

    ![Bestimmte Wochentage in einem Monat planen][Bestimmte Wochentage in einem Monat planen]

10. Schließlich können Sie mit der Option **Vorkommen** auswählen, in welcher Woche im Monat (erste, zweite, dritte usw.) der Auftrag an den angegebenen Wochentagen ausgeführt werden soll.

    ![Bestimmte Wochentage in bestimmten Wochen in einem Monat planen][Bestimmte Wochentage in bestimmten Wochen in einem Monat planen]

11. Nachdem Sie mindestens einen Auftrag erstellt haben, werden die Namen zusammen mit dem Status, dem Zeitplantyp und anderen Informationen auf der Registerkarte **WebJobs** angezeigt. Die Verlaufsinformationen für die letzten 30 Aufgaben werden gespeichert.

    ![Auftragsliste][Auftragsliste]

<a name="Scheduler"></a>

### Geplante Aufträge und Azure Scheduler

Geplante Aufträge können im Azure Scheduler-Portal weiter konfiguriert werden.

1.  Klicken Sie auf der Seite **WebJobs** auf den Link **Zeitplan** des Auftrags, um zur Azure Scheduler-Portalseite zu navigieren.

    ![Link zu Azure Scheduler][Link zu Azure Scheduler]

2.  Klicken Sie auf der Seite **Scheduler** auf den Auftrag.

    ![Auftrag auf der Scheduler-Portalseite][Auftrag auf der Scheduler-Portalseite]

3.  Die Seite **Auftragsaktion** wird geöffnet. Dort können Sie den Auftrag weiter konfigurieren.

    ![Seite "Auftragsaktion" im Scheduler][Seite "Auftragsaktion" im Scheduler]

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>

## Anzeigen des Auftragsverlaufs

1.  Klicken Sie zum Anzeigen des Ausführungsverlaufs eines Auftrags (einschließlich der mit dem WebJobs-SDK erstellten Aufträge) in der Spalte **Protokolle** auf den zugehörigen Link. (Sie können das Zwischenablagesymbol verwenden, um die URL der Protokolldateiseite in die Zwischenablage zu kopieren, wenn Sie dies wünschen.)

    ![Link "Protokolle"][Link "Protokolle"]

2.  Durch das Klicken auf den Link wird die WebJobs-Detailseite für die Aufgabe geöffnet. Auf dieser Seite werden der Name des ausgeführten Befehls, die letzten Ausführungszeiten und Angaben zu Erfolg oder Fehlern angezeigt. Klicken Sie unter **Recent job runs** auf eine Zeit, um weitere Details anzuzeigen.

    ![WebJobDetails][WebJobDetails]

3.  Die Seite **WebJob Run Details** wird geöffnet. Klicken Sie auf **Toggle Output**, um den Text des Protokollinhalts anzuzeigen. Das Ausgabeprotokoll liegt im Textformat vor.

    ![Ausführungsdetails zu WebJobs][Ausführungsdetails zu WebJobs]

4.  Klicken Sie auf den Link **Download**, um den Ausgabetext in einem separaten Browserfenster zu öffnen. Klicken Sie mit der rechten Maustaste auf den Link und verwenden Sie die Browseroptionen, um den Dateiinhalt zu speichern, um den Text selbst herunterzuladen.

    ![Protokollausgabe herunterladen][Protokollausgabe herunterladen]

5.  Der Link **WebJobs** oben auf der Seite stellt eine einfache Möglichkeit dar, eine Liste der WebJobs im Verlaufsdashboard zu erhalten.

    ![Link zur WebJobs-Liste][Link zur WebJobs-Liste]

    ![Liste der Aufträge im Verlaufsdashboard][Liste der Aufträge im Verlaufsdashboard]

    Wenn Sie auf einen dieser Links klicken, werden Sie zur Seite **WebJob Details** für den ausgewählten Auftrag weitergeleitet.

<a name="WHPNotes"></a>

## Hinweise

-   Ab März 2014 können Websites im kostenlosen Modus nach 20 Minuten ablaufen, wenn keine Anforderungen bei der SCM (Bereitstellungs)-Website eingehen und das Websiteportal in Azure nicht geöffnet ist. Dieses Verhalten wird durch Anforderungen bei der tatsächlichen Website nicht zurückgesetzt.

-   Code für einen fortlaufenden Auftrag muss zur Ausführung in einer Endlosschleife geschrieben sein.

-   Fortlaufende Aufträge werden nur dann fortlaufend ausgeführt, wenn die Website aktiv ist.

-   In den Modi **Basic** und **Standard** ist das Feature **Always on** verfügbar. Ist es aktiviert, wird verhindert, dass Websites in den Leerlauf wechseln.

<a name="NextSteps"></a>

## Nächste Schritte

<!-- ======= Do More with the Microsoft Azure WebJobs SDK ======== -->

<a name="WebJobsSDK"></a>

### Effektiver arbeiten mit dem Microsoft Azure WebJobs SDK

Das Microsoft Azure WebJobs SDK-vereinfacht die Programmierung von WebJobs, die auf Warteschlangen, Blobs oder Tabellen von Azure Storage oder Azure Service Bus-Warteschlangen basieren. Das Dashboard ist jetzt in das Azure-Portal integriert und enthält umfassende Überwachungs- und Diagnosefeatures für die Programme, die Sie mit dem SDK schreiben. Die Überwachungs- und Diagnosefeatures sind in das SDK integriert, Sie müssen keinen speziellen Code in Ihrem Programm hinzufügen.

Weitere Informationen finden Sie im Lernprogramm [Getting Started with Microsoft Azure WebJobs SDK][Getting Started with Microsoft Azure WebJobs SDK] (in englischer Sprache).

<!-- =========== Alternative Methods of Deployment ============= -->

<a name="AlternateDeployments"></a>

### Alternative Bereitstellungsmethoden

Visual Studio 2013 enthält Features zur Automatisierung der Bereitstellung von Konsolenanwendungsprojekten als WebJobs. Weitere Informationen finden Sie unter [Bereitstellen von Azure WebJobs auf Azure-Websites][Bereitstellen von Azure WebJobs auf Azure-Websites].

Für die Bereitstellung von WebJobs können Sie auch FTP, Git oder Web Deploy verwenden. Weitere Informationen finden Sie unter [Bereitstellen einer .NET-Konsolenanwendung mit WebJobs auf Azure][Bereitstellen einer .NET-Konsolenanwendung mit WebJobs auf Azure] und [Bereitstellen von Windows Azure-WebJobs][Bereitstellen von Windows Azure-WebJobs].

<a name="AdditionalResources"></a>

### Zusätzliche Ressourcen

-   Eine kommentierte Liste mit Links zur WebJobs-Funktion finden Sie unter [Azure-WebJobs – empfohlene Ressourcen][Azure-WebJobs – empfohlene Ressourcen].

-   WebJobs-bezogene Videos:

    [Azure WebJobs 101 - Grundlegende WebJobs mit Jamie Espinosa][Azure WebJobs 101 - Grundlegende WebJobs mit Jamie Espinosa]

    [Azure WebJobs 102 – Geplante WebJobs und das WebJobs-Dashboard mit Jamie Espinosa][Azure WebJobs 102 – Geplante WebJobs und das WebJobs-Dashboard mit Jamie Espinosa]

    [Azure Scheduler 101 - Kevin Lam erklärt die Erstellung von Zeitplänen][Azure Scheduler 101 - Kevin Lam erklärt die Erstellung von Zeitplänen]

### Erste Schritte

Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion][Kostenlose Microsoft Azure-Testversion].



  [Akzeptable Dateitypen für Skripte]: #acceptablefiles
  [Erstellen einer bedarfsgesteuerten Aufgabe]: #CreateOnDemand
  [Erstellen einer fortlaufend ausgeführten Aufgabe]: #CreateContinuous
  [Erstellen einer geplanten Aufgabe]: #CreateScheduled
  [Geplante Aufträge und Azure Scheduler]: #Scheduler
  [Anzeigen des Auftragsverlaufs]: #ViewJobHistory
  [Hinweise]: #WHPNotes
  [Nächste Schritte]: #NextSteps
  [Effektiver arbeiten mit dem Microsoft Azure WebJobs SDK]: #WebJobsSDK
  [Alternative Bereitstellungsmethoden]: #AlternateDeployments
  [Zusätzliche Ressourcen]: #AdditionalResources
  [Bedarfsgesteuerte Aufgabe]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
  [Aufgabenliste]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
  [Einmal ausführen]: ./media/web-sites-create-web-jobs/13RunOnce.png
  [Neue fortlaufende Aufgabe]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
  [Neuer geplanter Auftrag]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
  [Wiederholung planen]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
  [Geplante Startzeit]: ./media/web-sites-create-web-jobs/06SchdStart.png
  [Start zu einer bestimmten Zeit planen]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
  [1]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
  [Wochentage planen]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
  [Bestimmte Daten im Monat planen]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
  [Bestimmte Wochentage in einem Monat planen]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
  [Bestimmte Wochentage in bestimmten Wochen in einem Monat planen]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
  [Auftragsliste]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
  [Link zu Azure Scheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
  [Auftrag auf der Scheduler-Portalseite]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
  [Seite "Auftragsaktion" im Scheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
  [Link "Protokolle"]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
  [WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
  [Ausführungsdetails zu WebJobs]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
  [Protokollausgabe herunterladen]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
  [Link zur WebJobs-Liste]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
  [Liste der Aufträge im Verlaufsdashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
  [Getting Started with Microsoft Azure WebJobs SDK]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Bereitstellen von Azure WebJobs auf Azure-Websites]: http://azure.microsoft.com/de-de/documentation/articles/websites-dotnet-deploy-webjobs
  [Bereitstellen einer .NET-Konsolenanwendung mit WebJobs auf Azure]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
  [Bereitstellen von Windows Azure-WebJobs]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
  [Azure-WebJobs – empfohlene Ressourcen]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Azure WebJobs 101 - Grundlegende WebJobs mit Jamie Espinosa]: http://www.windowsazure.com/de-de/documentation/videos/azure-webjobs-basics/
  [Azure WebJobs 102 – Geplante WebJobs und das WebJobs-Dashboard mit Jamie Espinosa]: http://www.windowsazure.com/de-de/documentation/videos/azure-webjobs-schedule-and-dashboard/
  [Azure Scheduler 101 - Kevin Lam erklärt die Erstellung von Zeitplänen]: http://www.windowsazure.com/de-de/documentation/videos/azure-scheduler-how-to/
  [Kostenlose Microsoft Azure-Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
