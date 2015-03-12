<properties 
	pageTitle="Verwenden von WebJobs zum Ausführen von Hintergrundaufgaben in Microsoft Azure-Websites" 
	description="Erfahren Sie, wie Sie Hintergrundaufgaben in Microsoft Azure-Websites ausführen." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="timamm" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2014" 
	ms.author="cephalin"/>

#Verwenden von WebJobs zum Ausführen von Hintergrundaufgaben in Microsoft Azure-Websites

Azure-Websites ermöglicht das Ausführen von Programmen oder Skripts in Ihrer Website auf drei Arten: bei Bedarf, kontinuierlich oder gemäß einem Zeitplan. Für die Nutzung von Microsoft Azure-Webaufträgen fallen keine zusätzlichen Kosten an.

Dieser Artikel zeigt, wie Sie WebJobs mithilfe des Azure-Verwaltungsportals bereitstellen. Informationen zum Bereitstellen über Visual Studio oder einen kontinuierlichen Bereitstellungsprozess finden Sie unter [Bereitstellen von Azure-Webaufträgen auf Azure-Websites](http://azure.microsoft.com/documentation/articles/websites-dotnet-deploy-webjobs).

Das Azure WebJobs-SDK vereinfacht zahlreiche WebJobs-Programmieraufgaben. Weitere Informationen finden Sie unter [Was ist das Azure-Webaufträge-SDK?](../websites-dotnet-webjobs-sdk).

## Inhaltsverzeichnis ##
- [Akzeptable Dateitypen für Skripts](#acceptablefiles)
- [Erstellen einer bedarfsgesteuerten Aufgabe](#CreateOnDemand)
- [Erstellen einer fortlaufend ausgeführten Aufgabe](#CreateContinuous)
- [Erstellen einer geplanten Aufgabe](#CreateScheduled)
	- [Geplante Aufträge und Azure Scheduler](#Scheduler)
- [Anzeigen des Auftragsverlaufs](#ViewJobHistory)
- [Hinweise](#WHPNotes)
- [Nächste Schritte](#NextSteps)

## <a name="acceptablefiles"></a>Akzeptable Dateitypen für Skripts und Programme

Die folgenden Dateitypen werden akzeptiert:

* .cmd, .bat, .exe (bei Verwendung von windows cmd)
* .ps1 (bei Verwendung von powershell)
* .sh (bei Verwendung von bash)
* .php (bei Verwendung von php)
* .py (bei Verwendung von python)
* .js (bei Verwendung von node)

## <a name="CreateOnDemand"></a>Erstellen einer bedarfsgesteuerten Aufgabe

1. Klicken Sie auf der Seite **Webaufträge** in der Befehlsleiste auf **Hinzufügen**. Das Dialogfeld **Neuer Auftrag** wird angezeigt.
	
	![Bedarfsgesteuerte Aufgabe][OnDemandWebJob]
	
2. Geben Sie unter **Name** einen Namen für die Aufgabe ein. Der Name muss mit einem Buchstaben oder einer Ziffer beginnen und darf nur die Sonderzeichen "-" und "_" enthalten.
	
3. Navigieren Sie im Feld **Inhalt (ZIP-Dateien - höchstens 100 MB)** zu der ZIP-Datei mit Ihrem Skript. Die ZIP-Datei sollte die ausführbare Datei ((.exe, .cmd, .bat, .sh, .php, .py, .js) und alle Hilfsdateien enthalten, die zum Ausführen des Programms oder Skripts benötigt werden.
	
4. Wählen Sie im Feld **Ausführungshäufigkeit** die Option **Bedarfsgesteuert ausführen** aus.
	
5. Aktivieren Sie das Kontrollkästchen rechts unten im Dialogfeld, um das Skript auf die Website hochzuladen. Der Name, den Sie der Aufgabe gegeben haben, wird in der Liste angezeigt:
	
	![Aufgabenliste][WebJobsList]
	
6. Wählen Sie zur Ausführung des Skripts den Namen in der Liste aus, und klicken Sie in der Befehlsleiste unten auf der Portalseite auf **Einmal ausführen**.
	
	![Einmal ausführen][RunOnce]

## <a name="CreateContinuous"></a>Erstellen einer fortlaufend ausgeführten Aufgabe

1. Führen Sie zum Erstellen einer fortlaufend ausgeführten Aufgabe dieselben Schritte wie zum Erstellen einer einmalig ausgeführten Aufgabe aus, wählen Sie im Feld **Ausführungshäufigkeit** jedoch **Fortlaufend ausführen** aus.
	
	![Neue fortlaufende Aufgabe][NewContinuousJob]
	
2. Wählen Sie zum Starten oder Beenden einer fortlaufend ausgeführten Aufgabe die Aufgabe in der Liste aus, und klicken Sie in der Befehlsleiste auf **Starten** oder **Beenden**.

> [AZURE.NOTE] Wenn die Website auf mehr als einer Instanz ausgeführt wird, wird auf allen Instanzen eine fortlaufend ausgeführte Aufgabe ausgeführt. Bedarfsgesteuerte und geplante Aufgaben werden auf einer einzigen Instanz ausgeführt, die für den Lastenausgleich durch Microsoft Azure ausgewählt wurde.

> [AZURE.NOTE]
> Bei fortlaufenden Aufgaben wird empfohlen, auf der Seite "Konfigurieren" der Website die Option **Immer bereit** zu aktivieren. Das Feature "Always On" ist im Modus "Basic" und "Standard" verfügbar und verhindert, dass Websites entladen werden, selbst wenn sie einige Zeit nicht genutzt wurden. Wenn Ihre Website immer geladen ist, kann die fortlaufende Aufgabe zuverlässiger ausgeführt werden. 

## <a name="CreateScheduled"></a>Erstellen einer geplanten Aufgabe

1. Zum Erstellen einer geplanten Aufgabe führen Sie dieselben Schritte wie zuvor aus, wählen jedoch im Feld **Ausführungshäufigkeit** die Option **Gemäß einem Zeitplan ausführen** aus.
	
	![Neuer geplanter Auftrag][NewScheduledJob]
	
2. Wählen Sie die **Planerregion** für den Auftrag aus, und klicken Sie dann auf den Pfeil unten rechts im Dialogfeld, um zum nächsten Bildschirm zu wechseln.

3. Wählen Sie im Dialogfeld **Auftrag erstellen** den gewünschten **Wiederholungstyp** aus: **Einmaliger Auftrag** oder **Periodischer Auftrag**.
	
	![Planen der Wiederholung][SchdRecurrence]
	
4. Wählen Sie außerdem eine **Startzeit** aus: **Jetzt** oder **Zu einer bestimmten Zeit**.
	
	![Planen der Startzeit][SchdStart]
	
5. Wenn Sie zu einer bestimmten Zeit starten möchten, wählen Sie die Startzeitwerte unter **Startet am** aus.
	
	![Planen des Starts zu einem bestimmten Zeitpunkt][SchdStartOn]
	
6. Wenn Sie einen periodischen Auftrag ausgewählt haben, können Sie mit der Option **Wiederholen alle** die Häufigkeit und mit der Option **Endet am** eine Endzeit angeben.
	
	![Planen der Wiederholung][SchdRecurEvery]
	
7. Wenn Sie **Wochen** auswählen, können Sie das Kontrollkästchen **In einem bestimmten Zeitplan** aktivieren und die Wochentage angeben, an denen der Auftrag ausgeführt werden soll.
	
	![Planen der Wochentage][SchdWeeksOnParticular]
	
8. Wenn Sie **Monate** auswählen und das Kontrollkästchen **In einem bestimmten Zeitplan** aktivieren, können Sie festlegen, dass der Auftrag an bestimmten bezifferten **Tagen** im Monat ausgeführt wird. 
	
	![Planen bestimmter Datumsangaben im Monat][SchdMonthsOnPartDays]
	
9. Wenn Sie **Wochentage** auswählen, können Sie angeben, an welchem Wochentag bzw. welchen Wochentagen im Monat der Auftrag ausgeführt werden soll.
	
	![Planen bestimmter Wochentage in einem Monat][SchdMonthsOnPartWeekDays]
	
10. Schließlich können Sie mit der Option **Vorkommen** auswählen, in welcher Woche im Monat (erste, zweite, dritte usw.) der Auftrag an den angegebenen Wochentagen ausgeführt werden soll.
	
	![Planen bestimmter Wochentage in bestimmten Wochen in einem Monat][SchdMonthsOnPartWeekDaysOccurences]
	
11. Nachdem Sie mindestens einen Auftrag erstellt haben, werden die Namen zusammen mit dem Status, dem Zeitplantyp und anderen Informationen auf der Registerkarte "Webaufträge" angezeigt. Die Verlaufsinformationen für die letzten 30 Aufgaben werden gespeichert.
	
	![Liste der Aufträge][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Geplante Aufträge und Azure Scheduler

Geplante Aufträge können im Azure Scheduler-Portal weiter konfiguriert werden.

1.	Klicken Sie auf der Seite mit den Webaufträgen auf den Link **Zeitplan des Auftrags**, um zur Azure Scheduler-Portalseite zu navigieren. 
	
	![Link zu Azure Scheduler][LinkToScheduler]
	
2. Klicken Sie auf der Seite "Scheduler" auf den Auftrag.
	
	![Auftrag auf der Scheduler-Portalseite][SchedulerPortal]
	
3. Die Seite **Auftragsaktion** wird geöffnet. Dort können Sie den Auftrag weiter konfigurieren. 
	
	![Seite "Auftragsaktion" im Scheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Anzeigen des Auftragsverlaufs

1. Klicken Sie zum Anzeigen des Ausführungsverlaufs eines Auftrags (einschließlich der mit dem Webaufträge-SDK erstellten Aufträge) in der Spalte **Protokolle** auf den zugehörigen Link. (Sie können das Zwischenablagesymbol verwenden, um die URL der Protokolldateiseite in die Zwischenablage zu kopieren, wenn Sie dies wünschen.)
	
	![Link zu Protokollen][WebJobLogs]
		
2. Durch das Klicken auf den Link wird die WebJobs-Detailseite für die Aufgabe geöffnet. Auf dieser Seite werden der Name des ausgeführten Befehls, die letzten Ausführungszeiten und Angaben zu Erfolg oder Fehlern angezeigt. Klicken Sie unter **Kürzlich ausgeführte Aufträge** auf eine Zeit, um weitere Details anzuzeigen.
	
	![Details von Webaufträgen][WebJobDetails]
	
3. Die Seite **Ausführungsdetails zu Webaufträgen** wird angezeigt. Klicken Sie auf **Ausgabe ein-/ausblenden**, um den Text der Protokollinhalte anzuzeigen. Das Ausgabeprotokoll liegt im Textformat vor. 
	
	![Ausführungsdetails zu Webaufträgen][WebJobRunDetails]
	
4. Klicken Sie auf den Link **Download**, um den Ausgabetext in einem separaten Browserfenster zu öffnen. Klicken Sie mit der rechten Maustaste auf den Link und verwenden Sie die Browseroptionen, um den Dateiinhalt zu speichern, um den Text selbst herunterzuladen.
	
	![Herunterladen der Protokollausgabe][DownloadLogOutput]
	
5. Der Link **Webaufträge** oben auf der Seite stellt eine einfache Möglichkeit dar, eine Liste der Webaufträge im Dashboard mit den Verlaufsdaten anzuzeigen.
	
	![Link zu einer Liste mit Webaufträgen][WebJobsLinkToDashboardList]
	
	![Auftragsliste in Dashboard mit Verlaufsdaten][WebJobsListInJobsDashboard]
	
	Wenn Sie auf einen dieser Links klicken, werden Sie zur Seite "WebJob Details" für den ausgewählten Auftrag weitergeleitet.


## <a name="WHPNotes"></a>Hinweise
	
- Ab März 2014 können Websites im kostenlosen Modus nach 20 Minuten ablaufen, wenn keine Anforderungen bei der SCM (Bereitstellungs)-Website eingehen und das Websiteportal in Azure nicht geöffnet ist. Dieses Verhalten wird durch Anforderungen bei der tatsächlichen Website nicht zurückgesetzt.
- Code für einen fortlaufenden Auftrag muss zur Ausführung in einer Endlosschleife geschrieben sein.
- Fortlaufende Aufträge werden nur dann fortlaufend ausgeführt, wenn die Website aktiv ist.
- In den Modi "Basic" und "Standard" ist das Feature Always on verfügbar. Ist es aktiviert, wird verhindert, dass Websites in den Leerlauf wechseln.

## <a name="NextSteps"></a>Nächste Schritte
 
Weitere Informationen finden Sie unter [Empfohlene Ressourcen für Azure-Webaufträge][WebJobsRecommendedResources].

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

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




<!--HONumber=42-->
