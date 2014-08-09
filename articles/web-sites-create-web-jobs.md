<properties linkid="web-sites-create-web-jobs" urlDisplayName="How to create web jobs in Microsoft Azure" pageTitle="How to Create Web Jobs in Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Web Jobs" description="Learn how to create web jobs in Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create Web Jobs in Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Verwenden des WebJobs-Features in Microsoft Azure-Websites
==========================================================

Microsoft Azure-Websites ermöglichen die Ausführung von Programmen oder Skripten auf Ihrer Website entweder bedarfsgesteuert, fortlaufend oder nach einem Zeitplan. Es fallen keine zusätzlichen Kosten für die Verwendung von Microsoft Azure WebJobs an, sofern Sie nicht das weiter unten in diesem Artikel beschriebene Feature **Immer bereit** aktivieren möchten.

Inhaltsverzeichnis
------------------

-   [Akzeptable Dateitypen für Skripte](#acceptablefiles)
-   [Erstellen einer bedarfsgesteuerten Aufgabe](#CreateOnDemand)
-   [Erstellen einer fortlaufend ausgeführten Aufgabe](#CreateContinuous)
-   [Erstellen einer geplanten Aufgabe](#CreateScheduled)
    -   [Geplante Aufträge und Azure Scheduler](#Scheduler)
-   [Anzeigen des Auftragsverlaufs](#ViewJobHistory)
-   [Hinweise](#WHPNotes)
-   [Nächste Schritte](#NextSteps)
    -   [Effektiver arbeiten mit dem Microsoft Azure WebJobs SDK](#WebJobsSDK)
    -   [Alternative Bereitstellungsmethoden](#AlternateDeployments)
    -   [Weitere Ressourcen](#AdditionalResources)

## Akzeptable Dateitypen für Skripte

Die folgenden Dateitypen können als ausführbare Skripte verwendet werden:

.cmd, .bat, .exe (bei Verwendung von windows cmd)

.ps1 (bei Verwendung von powershell)

.sh (bei Verwendung von bash)

.php (bei Verwendung von php)

.py (bei Verwendung von python)

.js (bei Verwendung von node)

## Erstellen einer bedarfsgesteuerten Aufgabe

1.  Klicken Sie auf der Seite **WebJobs** in der Befehlsleiste auf **Hinzufügen**. Das Dialogfeld **Neuer Auftrag** wird angezeigt.

    ![Bedarfsgesteuerte Aufgabe](./media/web-sites-create-web-jobs/01aOnDemandWebJob.png)

2.  Geben Sie unter **Name** einen Namen für die Aufgabe ein. Der Name muss mit einem Buchstaben oder einer Ziffer beginnen und darf nur die Sonderzeichen "-" und "\_" enthalten.

3.  Navigieren Sie im Feld **Content (Zip Files - 100MB Max)** zu der ZIP-Datei mit dem Skript. Die ZIP-Datei sollte die ausführbare Datei ((.exe, .cmd, .bat, .sh, .php, .py, .js) und alle Hilfsdateien enthalten, die zum Ausführen des Programms oder Skripts benötigt werden.

4.  Wählen Sie im Feld **How to Run** die Option **Run on Demand** aus.

5.  Aktivieren Sie das Kontrollkästchen rechts unten im Dialogfeld, um das Skript auf die Website hochzuladen. Der Name, den Sie der Aufgabe gegeben haben, wird in der Liste angezeigt:

    ![Aufgabenliste](./media/web-sites-create-web-jobs/02aWebJobsList.png)

6.  Wählen Sie zur Ausführung des Skripts den Namen in der Liste aus, und klicken Sie in der Befehlsleiste unten auf der Portalseite auf **Einmal ausführen**.

    ![Einmal ausführen](./media/web-sites-create-web-jobs/13RunOnce.png)

## Erstellen einer fortlaufend ausgeführten Aufgabe

1.  Führen Sie zum Erstellen einer fortlaufend ausgeführten Aufgabe dieselben Schritte wie zum Erstellen einer einmal ausgeführten Aufgabe aus, wählen Sie im Feld **How to Run** jedoch **Fortlaufend ausführen** aus.

    ![Neue fortlaufende Aufgabe](./media/web-sites-create-web-jobs/03aNewContinuousJob.png)

2.  Wählen Sie die Aufgabe in der Liste aus und klicken Sie in der Befehlsleiste auf **Start** oder **Beenden**, um eine fortlaufend ausgeführte Aufgabe zu starten oder zu beenden.

> [WACOM.NOTE] Wenn die Website auf mehr als einer Instanz ausgeführt wird, wird auf allen Ihren Instanzen eine fortlaufend ausgeführte Aufgabe ausgeführt. Bedarfsgesteuerte und geplante Aufgaben werden auf einer einzigen Instanz ausgeführt, die von Microsoft Azure für den Lastenausgleich ausgewählt wurde.

> [WACOM.NOTE]
> Bei fortlaufenden Aufgaben wird empfohlen, auf der Seite **Konfigurieren** der Website die Option **Immer bereit** zu aktivieren. Das Feature **Immer bereit** ist im Modus **Grundlegend** und **Standard** verfügbar und verhindert, dass Websites entladen werden, selbst wenn sie einige Zeit im Leerlauf waren. Wenn Ihre Website immer geladen ist, kann die fortlaufend ausgeführte Aufgabe zuverlässiger ausgeführt werden.

## Erstellen einer geplanten Aufgabe
1. Führen Sie zum Erstellen einer geplanten Aufgabe dieselben Schritte wie zuvor aus, wählen Sie jedoch im Feld **How to Run** die Option **Run on a schedule** aus.

	![New Scheduled Job][NewScheduledJob]

2.  Wählen Sie die Scheduler-Region für den Auftrag aus, und klicken Sie dann auf den Pfeil unten rechts im Dialogfeld, um zum nächsten Bildschirm zu wechseln.

3.  Wählen Sie im Dialogfeld Auftrag erstellen den gewünschten Wiederholungstyp aus: **Einmaliger Auftrag** oder **Periodischer Auftrag**.

    ![Wiederholung planen](./media/web-sites-create-web-jobs/05SchdRecurrence.png)

4.  Wählen Sie außerdem eine Startzeit aus: **Now** oder **Bestimmte Zeit**.

    ![Geplante Startzeit](./media/web-sites-create-web-jobs/06SchdStart.png)

5.  Wenn Sie zu einer bestimmten Zeit starten möchten, wählen Sie die Startzeitwerte unter **Starting On** aus.

    ![Start zu einer bestimmten Zeit planen](./media/web-sites-create-web-jobs/07SchdStartOn.png)

6.  Wenn Sie einen periodischen Auftrag ausgewählt haben, können Sie mit der Option **Wiederholen alle** die Häufigkeit und mit der Option **Ending On** eine Endzeit angeben.

    ![Wiederholung planen](./media/web-sites-create-web-jobs/08SchdRecurEvery.png)

7.  Wenn Sie **Wochen** auswählen, können Sie das Kontrollkästchen **On a Particular Schedule** aktivieren und die Wochentage angeben, an denen der Auftrag ausgeführt werden soll.

    ![Wochentage planen](./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png)

8.  Wenn Sie **Monate** auswählen und das Kontrollkästchen **On a Particular Schedule** aktivieren, können Sie festlegen, dass der Auftrag an bestimmten bezifferten Tagen im Monat ausgeführt wird.

    ![Bestimmte Daten im Monat planen](./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png)

9.  Wenn Sie **Wochentage** auswählen, können Sie angeben, an welchem Wochentag bzw. welchen Wochentagen im Monat der Auftrag ausgeführt werden soll.

    ![Bestimmte Wochentage in einem Monat planen](./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png)

10.  Schließlich können Sie mit der Option **Vorkommen** auswählen, in welcher Woche im Monat (erste, zweite, dritte usw.) der Auftrag an den angegebenen Wochentagen ausgeführt werden soll.

    ![Bestimmte Wochentage in bestimmten Wochen in einem Monat planen](./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png)

11. Nachdem Sie mindestens einen Auftrag erstellt haben, werden die Namen zusammen mit dem Status, dem Zeitplantyp und anderen Informationen auf der Registerkarte **WebJobs** angezeigt. Die Verlaufsinformationen für die letzten 30 Aufgaben werden gespeichert.

    ![Auftragsliste](./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png)

## Geplante Aufträge und mit Azure Scheduler
Geplante Aufträge können im Azure Scheduler-Portal weiter konfiguriert werden.

1.  Klicken Sie auf der Seite **WebJobs** auf den Link **Zeitplan** des Auftrags, um zur Azure Scheduler-Portalseite zu navigieren.

    ![Link zu Azure Scheduler](./media/web-sites-create-web-jobs/31LinkToScheduler.png)

2.  Klicken Sie auf der Seite **Scheduler** auf den Auftrag.

    ![Auftrag auf der Scheduler-Portalseite](./media/web-sites-create-web-jobs/32SchedulerPortal.png)

3.  Die Seite **Auftragsaktion** wird geöffnet. Dort können Sie den Auftrag weiter konfigurieren.

    ![Seite "Auftragsaktion" im Scheduler](./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png)

## Anzeigen des Auftragsverlaufs##
1. Klicken Sie zum Anzeigen des Ausführungsverlaufs eines Auftrags (einschließlich der mit dem WebJobs SDK erstellten Aufträge) auf den zugehörigen Link in der Spalte **Protokolle**. (Sie können das Zwischenablagesymbol verwenden, um die URL der Protokolldateiseite in die Zwischenablage zu kopieren, wenn Sie dies wünschen.)

    ![Logs Link][WebJobLogs]

2.  Durch das Klicken auf den Link wird die WebJobs-Detailseite für die Aufgabe geöffnet. Auf dieser Seite werden der Name des ausgeführten Befehls, die letzten Ausführungszeiten und Angaben zu Erfolg oder Fehlern angezeigt. Klicken Sie unter **Recent job runs** auf eine Zeit, um weitere Details anzuzeigen.

    ![WebJobDetails](./media/web-sites-create-web-jobs/15WebJobDetails.png)

3.  Die Seite **WebJob Run Details** wird geöffnet. Klicken Sie auf **Toggle Output**, um den Text des Protokollinhalts anzuzeigen. Das Ausgabeprotokoll liegt im Textformat vor.

    ![Ausführungsdetails zu WebJobs](./media/web-sites-create-web-jobs/16WebJobRunDetails.png)

4.  Klicken Sie auf den Link **Download**, um den Ausgabetext in einem separaten Browserfenster zu öffnen. Klicken Sie mit der rechten Maustaste auf den Link und verwenden Sie die Browseroptionen, um den Dateiinhalt zu speichern, um den Text selbst herunterzuladen.

    ![Protokollausgabe herunterladen](./media/web-sites-create-web-jobs/17DownloadLogOutput.png)

5.  Der Link **WebJobs** oben auf der Seite stellt eine einfache Möglichkeit dar, eine Liste der WebJobs im Verlaufsdashboard zu erhalten.

    ![Link zur WebJobs-Liste](./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png)

    ![Liste der Aufträge im Verlaufsdashboard](./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png)

    Wenn Sie auf einen dieser Links klicken, werden Sie zur Seite **WebJob Details** für den ausgewählten Auftrag weitergeleitet.

## Hinweise

-   Ab März 2014 können Websites im kostenlosen Modus nach 20 Minuten ablaufen, wenn keine Anforderungen bei der scm (Bereitstellungs)-Website eingehen und das Websiteportal in Azure nicht geöffnet ist. Dieses Verhalten wird durch Anforderungen bei der tatsächlichen Website nicht zurückgesetzt.

-   Code für einen fortlaufenden Auftrag muss zur Ausführung in einer Endlosschleife geschrieben sein.

-   Fortlaufende Aufträge werden nur dann fortlaufend ausgeführt, wenn die Website aktiv ist.

-   In den Modi **Grundlegend** und **Standard** ist das Feature **Immer bereit** verfügbar. Ist es aktiviert, wird verhindert, dass Websites in den Leerlauf wechseln.

## Nächste Schritte##

### Effektiver arbeiten mit dem Microsoft Azure WebJobs SDK###
Mit dem Microsoft Azure WebJobs SDK wird das Hinzufügen von Hintergrundverarbeitung zu Microsoft Azure-Websites vereinfacht. Microsoft Azure Storage ist in das SDK integriert, wodurch eine Funktion im Programm ausgelöst wird, wenn Elemente zu Warteschlangen, Blobs oder Tabellen hinzugefügt werden. Das Dashboard ist jetzt in das Azure-Portal integriert und enthält umfassende Überwachungs- und Diagnosefeatures für die Programme, die Sie mit dem SDK schreiben. Die Überwachungs- und Diagnosefeatures sind in das SDK integriert, Sie müssen keinen speziellen Code in Ihrem Programm hinzufügen.

Weitere Informationen finden Sie im Lernprogramm [Getting Started with Microsoft Azure WebJobs SDK](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) (in englischer Sprache). Das Lernprogramm bietet einen Überblick über die Features des WebJobs SDK und führt Sie durch die Erstellung und Ausführung eines einfachen Hello World-Hintergrundprozesses.

Eine exemplarische Vorgehensweise zu einer mit dem Microsoft Azure WebJobs SDK erstellten Beispiel-Befehlszeilenapp finden Sie unter [Introducing Microsoft Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx) (in englischer Sprache).

### Alternative Bereitstellungsmethoden###
Wenn Sie Ihre Skripte nicht über die WebJobs-Portalseite hochladen möchten, können Sie FTP, Git oder Web Deploy verwenden. Weitere Informationen finden Sie unter [How to deploy Microsoft Azure WebJobs](http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs) und [Git deploying a .NET console app to Azure using WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app) (jeweils in englischer Sprache).

### Weitere Ressourcen###
- Eine kommentierte Liste der Links für das WebJobs-Feature finden Sie unter Using the WebJobs feature of Microsoft Azure Web Sites (in englischer Sprache).

- WebJobs-bezogene Videos:

    [Azure WebJobs 101 - Grundlegende WebJobs mit Jamie Espinosa](http://www.windowsazure.com/de-de/documentation/videos/azure-webjobs-basics/)

    [Azure WebJobs 102 – Geplante WebJobs und das WebJobs-Dashboard mit Jamie Espinosa](http://www.windowsazure.com/de-de/documentation/videos/azure-webjobs-schedule-and-dashboard/)

    [Azure Scheduler 101 - Kevin Lam erklärt die Erstellung von Zeitplänen](http://www.windowsazure.com/de-de/documentation/videos/azure-scheduler-how-to/)



<!-- LINKS -->
[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx

[HanselIntro]:http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx

[AmitDeploy]:http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs

[AmitConsole]:http://blog.amitapple.com/post/73574681678/git-deploy-console-app

[RickWebJobsCurah]:http://go.microsoft.com/fwlink/?LinkId=390226

<!-- IMAGES -->
[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png