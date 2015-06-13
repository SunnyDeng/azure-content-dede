<properties
	pageTitle="Azure Backup - Sicherung und Wiederherstellung von einem Windows-Server oder Windows-Client"
	description="Informationen Sie zum Sichern und Wiederherstellen von einem Windows-Server oder Windows-Client. Der Artikel befasst sich auch Wiederherstellungsmethode"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/02/2015"
	 ms.author="prvijay"/>

# Sicherung und Wiederherstellung von einem Windows-Server oder Windows-Client-Computer
Dieser Artikel behandelt die erforderlichen Schritte zum Sichern von WindowsServer oder einem Windows-Client-Computer. Dazu gehört auch die erforderlichen Schritte für die gesicherten Dateien auf dem gleichen Computer und die erforderlichen Schritte zum Wiederherstellen der gesicherten Dateien auf einem anderen Computer wiederherstellen.

## Sichern von Dateien
1. Sobald der Computer registriert ist, öffnen Sie Microsoft Azure Backup-Mmc-Snap-in. <br/> ![Suchergebnis][1]

2. Klicken Sie auf **Sicherung planen** <br/> ![Sicherung planen][2]

3. Wählen Sie die Elemente, die zu sichern. Azure-Sicherung auf einem Windows Server-Windows-Client (d. h. ohne System Center Data Protection Manager) können Sie Dateien und Ordner zu schützen. <br/> ![Zu sichernde Elemente][3]

4. Angeben die Zeitplan und Aufbewahrung Sicherungsrichtlinie die erläutert wird ausführlich in der folgenden [Artikel](backup-azure-backup-cloud-as-tape.md)

5. Wählen Sie die Methode zum Senden der ersten Sicherung aus. Gewählter abschließen, das anfängliche seeding hängt die Menge der Daten, die Sie sichern und die Verbindungsgeschwindigkeit Ihrer Internet hochladen möchten. Wenn Sie, backup GB planen / TB Daten über eine mit hoher Latenz und niedriger Bandbreite, empfiehlt es sich, dass Sie die erste Sicherung ausführen, indem Sie einen Datenträger an den nächstgelegenen Azure-Rechenzentrum verwendet werden soll. Dies heißt "Offline Backup" und wird detailliert in dieser [Artikel](https://msdn.microsoft.com/library/azure/dn894419.aspx). Wenn Sie über ausreichende Bandbreite verfügen, wird empfohlen, dass Sie die erste Sicherung über das Netzwerk ausführen. <br/> ![Anfängliche Sicherung][4]

6. Sobald der Workflow abgeschlossen ist, wechseln Sie zurück zur Mmc-snap-in, und klicken Sie auf **Jetzt sichern** abgeschlossen, das anfängliche seeding über das Netzwerk. <br/> ![Jetzt sichern][5]

7. Sobald das anfängliche seeding abgeschlossen ist, die **Aufträge** Ansicht in der Azure-Sicherungs-Konsole zeigt den Status an. <br/> ![Vollständige IR][6]

## Wiederherstellen von Daten auf dem gleichen Computer
Wenn Sie versehentlich eine Datei gelöscht und ein Dateisystemvolume auf demselben Computer wiederherstellen (aus dem die Sicherung erstellt wird) werden soll, würde die folgenden Schritte Sie zum Wiederherstellen der Daten unterstützen.

1. Öffnen der **Microsoft Azure Backup** in ausrichten.

2. Klicken Sie auf **Daten wiederherstellen** an den Workflow zu initiieren. <br/> ![Wiederherstellen von Daten][7]

3. Wählen Sie **dieses Servers (* Yourmachinename *)** option, wie Sie die gesicherte Datei auf demselben Computer wiederherstellen möchten. <br/> ![Demselben Computer][8]

4. Sie können sich entscheiden, um **zu Dateien navigieren** oder **nach Dateien suchen**. Lassen Sie die Standardoption, wenn Sie beabsichtigen, eine oder mehrere Dateien wiederherstellen, deren Pfad bezeichnet wird. Wenn Sie nicht über die Ordnerstruktur sicher sind, aber nach einer Datei suchen möchten, wählen Sie die **nach Dateien suchen** Option. In diesem Abschnitt werden wir die Standardoption fort. <br/> ![Dateien durchsuchen][9]

5. Wählen Sie im nächsten Bildschirm das Volume aus dem Sie die Datei wiederherstellen möchten. Der Bildschirm können, die Sie von einem beliebigen Punkt wiederherstellen. Datumsangaben im erscheinenden **Fett** Geben Sie im Kalender-Steuerelement an die Verfügbarkeit eines Wiederherstellungspunkts. Sobald ein Datum ausgewählt wird, basierend auf Ihren Sicherungszeitplan (und den Erfolg eines Sicherungsvorgangs) Sie können einen Zeitpunkt auswählen aus der **Zeit** Dropdown-Liste. <br/> ![Volume und Datum][10]

6. Wählen Sie im nächsten Bildschirm die Elemente, die Sie wiederherstellen möchten. Sie können eine Mehrfachauswahl Ordner/Dateien, die Sie wiederherstellen möchten. <br/> ![Ausgewählte Dateien][11]

7. Geben Sie im nächsten Bildschirm die Recovery-Parameter. <br/> ![Wiederherstellungsoptionen][12]
  + Sie können eine Wiederherstellung am ursprünglichen Speicherort (in der Datei oder der Ordner überschrieben werden würden) oder an einen anderen Speicherort auf demselben Computer.

  + Wenn die Datei oder der Ordner die Sie wiederherstellen möchten, die am Zielspeicherort vorhanden ist, haben Sie die Möglichkeit zum Erstellen von Kopien (zwei Versionen derselben Datei), oder die Dateien im Zielverzeichnis überschrieben oder überspringen Sie die Wiederherstellung der Dateien, die im Ziel vorhanden.

  + Es wird empfohlen, die Standardoption wiederherstellen die ACLs auf die Dateien, die wiederhergestellt werden zu lassen.

8. Diese Eingaben bereitgestellt werden, wird gestartet und Recovery-Workflow die Dateien auf diesem Computer wiederhergestellt werden können.

## An einen anderen Computer wiederherstellen
Wenn der gesamte Server verloren geht, können Sie die Dateisystemvolume auf einem anderen Computer wiederherstellen. Die folgenden Schritte beschreiben den Workflow.

Der Benennungsstandards die Schritte lauten wie folgt: + *Quellcomputer* – der ursprüngliche Computer, von denen die Sicherung erstellt wurde, und die zurzeit nicht verfügbar ist.

  + *Zielcomputer* – den Computer, auf dem die Daten abgerufen werden.

  + *Beispiel Tresor* – der sicherungstresor, der die *Quellcomputer* und *Zielcomputer* registriert sind. <br/>

> [AZURE.NOTE]Sicherungen, die von einem Computer können nicht auf einem Computer wiederhergestellt werden, die eine frühere Version des Betriebssystems ausgeführt wird. Z. B. Wenn Sie Sicherungen auf einem Windows 7-Computer erstellt werden, können sie auf einem Windows 8 oder höher Computer wiederhergestellt werden. Jedoch wird die umgekehrt nicht gegeben sind.

1. Öffnen der **Microsoft Azure Backup** ausrichten, in der *Zielcomputer*.

2. Sicherstellen, dass die *Zielcomputer* und *Quellcomputer* dasselbe Depot Sicherung wiederhergestellt werden (in diesem Artikel - *Beispiel Tresor*).

3. Klicken Sie auf **Daten wiederherstellen** an den Workflow zu initiieren. <br/> ![Wiederherstellen von Daten][7]

4. Wählen Sie **einem anderen Server** <br/> ![Einen anderen Server][13]

5. Geben Sie den Tresor Anmeldeinformationen entspricht der *Beispiel Tresor*. Ist der Anmeldeinformationsdatei Tresor ungültig (oder abgelaufen), laden Sie eine neue Tresor Anmeldeinformationen-Datei aus der *Beispiel Tresor* im Azure-Portal. Sobald der Anmeldeinformationsdatei Tresor bereitgestellt wird, wird der sicherungstresor anhand der Anmeldeinformationsdatei Tresor angezeigt.

6. Wählen Sie die *Quellcomputer* aus der Liste der angezeigten Computern. <br/> ![Liste der Computer][14]

7. Wählen Sie entweder wie zuvor das **nach Dateien suchen** oder **nach Dateien suchen** Option. In diesem Abschnitt verwenden wir die **nach Dateien suchen** Option. <br/> ![Suchen,][15]

8. Wählen Sie das Volume und Datum auf dem nächsten Bildschirm. Suchen Sie nach den Namen von Dateien/Ordner die Sie wiederherstellen möchten. <br/> ![Nach Artikeln suchen][16]

9. Wählen Sie den Speicherort, zu dem die Dateien wiederhergestellt werden muss. <br/> ![Position wiederherstellen][17]

10. Geben Sie die verschlüsselungspassphrase, die während der bereitgestellt wurde *des Quellcomputers* Registrierung *Beispiel Tresor*. <br/> ![Verschlüsselung][18]

Klicken Sie nach die Eingabe bereitgestellt wird, auf die **Wiederherstellen** Schaltfläche die Wiederherstellung der gesicherten Dateien in das Ziel bereitgestellt löst.

<!--Image references-->
[1]: ./media/backup-azure-backup-and-recover/result.png
[2]: ./media/backup-azure-backup-and-recover/schedulebackup.png
[3]: ./media/backup-azure-backup-and-recover/items.png
[4]: ./media/backup-azure-backup-and-recover/initialbackup.png
[5]: ./media/backup-azure-backup-and-recover/backupnow.png
[6]: ./media/backup-azure-backup-and-recover/ircomplete.png

[7]: ./media/backup-azure-backup-and-recover/recover.png
[8]: ./media/backup-azure-backup-and-recover/samemachine.png
[9]: ./media/backup-azure-backup-and-recover/browseandsearch.png
[10]: ./media/backup-azure-backup-and-recover/volanddate.png
[11]: ./media/backup-azure-backup-and-recover/selectfiles.png
[12]: ./media/backup-azure-backup-and-recover/recoveroptions.png

[13]: ./media/backup-azure-backup-and-recover/anotherserver.png
[14]: ./media/backup-azure-backup-and-recover/machinelist.png
[15]: ./media/backup-azure-backup-and-recover/search.png
[16]: ./media/backup-azure-backup-and-recover/searchitems.png
[17]: ./media/backup-azure-backup-and-recover/restorelocation.png
[18]: ./media/backup-azure-backup-and-recover/encryption.png

<!---HONumber=GIT-SubDir--> 