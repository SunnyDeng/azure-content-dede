<properties
   pageTitle="Azure Backup – Sicherung und Wiederherstellung von Windows-Server- oder Windows-Clientcomputern"
   description="Erfahren Sie, wie Windows-Server- oder Windows-Clientcomputer gesichert und wiederhergestellt werden. Der Artikel befasst sich außerdem mit der Wiederherstellung auf einem anderen Server."
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

# Sicherung und Wiederherstellung von Windows-Server- oder Windows-Clientcomputern
Dieser Artikel behandelt die erforderlichen Schritte zur Sicherung von Windows-Server- oder Windows-Clientcomputern. Dabei werden auch die Schritte berücksichtigt, die zum Wiederherstellen der gesicherten Dateien auf demselben Computer erforderlich sind, sowie die Schritte zum Wiederherstellen der gesicherten Dateien auf einem anderen Computer.

## Sichern von Dateien
1. Nach der Registrierung des Computers öffnen Sie das Microsoft Azure Backup-MMC-Snap-In. <br/> ![Suchergebnis][1]

2. Klicken Sie auf **Sicherung planen**. <br/> ![Planen einer Sicherung][2]

3. Wählen Sie die zu sichernden Elemente aus. Mit Azure Backup können Sie auf einem Windows Server/Windows-Client (ohne System Center Data Protection Manager) Ihre Dateien und Ordner schützen. <br/> ![Zu sichernde Elemente][3]

4. Geben Sie den Sicherungszeitplan und die Aufbewahrungsrichtlinie an, die im folgenden [Artikel](backup-azure-backup-cloud-as-tape.md) im Detail erläutert wird.

5. Wählen Sie die Methode zum Senden der ersten Sicherung aus. Ihre Auswahl zum Abschließen des anfänglichen Seedings ist abhängig von der Datenmenge, die Sie sichern möchten, und von der Internetgeschwindigkeit beim Hochladen. Wenn Sie mehrere GB/TB über eine Verbindung mit hoher Latenz und niedriger Bandbreite sichern möchten, empfiehlt es sich, bei der ersten Sicherung einen Datenträger an das nächstgelegene Azure-Datencenter zu senden. Diese Vorgehensweise heißt "Offlinesicherung" und wird detailliert in diesem [Artikel](https://msdn.microsoft.com/library/azure/dn894419.aspx) behandelt. Wenn Sie über eine ausreichende Bandbreite verfügen, wird empfohlen, die erste Sicherung über das Netzwerk auszuführen. <br/> ![Erste Sicherung][4]

6. Sobald der Workflow abgeschlossen ist, wechseln Sie zurück zum MMC-Snap-In, und klicken Sie auf **Jetzt sichern**, um das anfängliche Seeding über das Netzwerk abzuschließen. <br/> ![Jetzt sichern][5]

7. Sobald das anfängliche Seeding abgeschlossen ist, wird der Status in der Azure Backup-Konsole in der Anzeige **Aufträge** angezeigt. <br/> ![Abgeschlossen][6]

## Wiederherstellen von Daten auf demselben Computer
Wenn Sie versehentlich eine Datei gelöscht haben und eine Datei/ein Volume auf demselben Computer wiederherstellen möchten (auf dem die Sicherung erstellt wurde), können Sie die folgenden Schritte zum Wiederherstellen der Daten befolgen.

1. Öffnen Sie das Snap-In **Microsoft Azure Backup**.

2. Klicken Sie auf **Daten wiederherstellen**, um den Workflow zu initiieren. <br/> ![Wiederherstellen von Daten][7]

3. Wählen Sie die Option **Dieser Server (*NameIhresComputers*)**, da Sie die gesicherte Datei auf demselben Computer wiederherstellen möchten. <br/> ![Auf demselben Computer][8]

4. Sie können sich entscheiden, ob Sie **Dateien durchsuchen** oder **Dateien suchen** möchten. Behalten Sie die Standardoption bei, wenn Sie beabsichtigen, eine oder mehrere Dateien wiederherzustellen, deren Pfad bekannt ist. Wenn Sie die Ordnerstruktur nicht genau kennen und nach einer Datei suchen möchten, wählen Sie die Option **Dateien suchen**. In diesem Abschnitt verwenden wir die Standardoption. <br/> ![Dateien durchsuchen][9]

5. Wählen Sie im nächsten Bildschirm das Volume aus, von dem Sie die Datei wiederherstellen möchten. Der Bildschirm ermöglicht Ihnen die Wiederherstellung von einem beliebigen Zeitpunkt. Datumsangaben, die im Kalendersteuerelement **fett** dargestellt werden, geben die Verfügbarkeit eines Wiederherstellungspunkts an. Nachdem Sie ein Datum ausgewählt haben, können Sie basierend auf Ihrem Sicherungszeitplan (und dem Erfolg eines Sicherungsvorgangs) einen Zeitpunkt in der **Zeit**-Dropdownliste auswählen. <br/> ![Volume und Datum][10]

6. Wählen Sie im nächsten Bildschirm die Elemente aus, die Sie wiederherstellen möchten. Sie können eine Mehrfachauswahl von Ordnern/Dateien treffen, die Sie wiederherstellen möchten. <br/> ![Auswählen von Dateien][11]

7. Geben Sie im nächsten Bildschirm die Wiederherstellungsparameter an. <br/> ![Wiederherstellungsoptionen][12]
  + Sie können eine Wiederherstellung am ursprünglichen Speicherort (bei der die Datei oder der Ordner überschrieben wird) oder an einem anderen Speicherort auf demselben Computer durchführen.

  + Wenn die Datei oder der Ordner für die Wiederherstellung bereits am Zielspeicherort vorhanden ist, können Sie Kopien erstellen (zwei Versionen derselben Datei), die Dateien im Zielverzeichnis überschreiben oder die Wiederherstellung der Dateien überspringen, die am Zielspeicherort vorhanden sind.

  + Es wird dringend empfohlen, die Standardoption beizubehalten und die ACLs für die wiederherzustellenden Dateien ebenfalls wiederherzustellen.

8. Nachdem Sie diese Eingaben bereitgestellt haben, beginnt der Wiederherstellungsworkflow, bei dem die Dateien auf diesem Computer wiederhergestellt werden.

## Wiederherstellen auf einem anderen Computer
Wenn der gesamte Server verloren geht, können Sie die Datei/das Volume dennoch auf einem anderen Computer wiederherstellen. Der Workflow wird in den folgenden Schritten beschrieben.

In den Schritten werden folgende Bezeichnungen verwendet: + *Quellcomputer* – Der ursprüngliche Computer, von dem die Sicherung erstellt wurde und der zurzeit nicht verfügbar ist.

  + *Zielcomputer* – Der Computer, auf dem die Daten abgerufen werden.

  + *Beispieltresor* – Der Sicherungstresor, bei dem der *Quellcomputer* und der *Zielcomputer* registriert sind. <br/>

> [AZURE.NOTE]Sicherungen von einem Computer können nicht auf einem Computer wiederhergestellt werden, auf dem eine frühere Version des Betriebssystems ausgeführt wird. Wenn beispielsweise Sicherungen auf einem Windows 7-Computer erstellt werden, können diese auf einem Computer unter Windows 8 oder höher wiederhergestellt werden. Umgekehrt ist dies jedoch nicht möglich.

1. Öffnen Sie das Snap-In **Microsoft Azure Backup** auf dem *Zielcomputer*.

2. Stellen Sie sicher, dass der *Zielcomputer* und der *Quellcomputer* in demselben Sicherungstresor wiederhergestellt werden (in diesem Artikel *Beispieltresor* genannt).

3. Klicken Sie auf **Daten wiederherstellen**, um den Workflow zu initiieren. <br/> ![Wiederherstellen von Daten][7]

4. Wählen Sie **Anderer Server**. <br/> ![Anderer Server][13]

5. Geben Sie die Datei mit den Tresoranmeldeinformationen an, die dem *Beispieltresor* entspricht. Wenn die Anmeldeinformationsdatei für den Tresor ungültig (oder abgelaufen) ist, laden Sie eine neue Anmeldeinformationsdatei für den Tresor aus dem *Beispieltresor* im Azure-Portal herunter. Sobald die Anmeldeinformationsdatei für den Tresor bereitgestellt wurde, wird der Sicherungstresor angezeigt, der der Tresor-Anmeldeinformationsdatei entspricht.

6. Wählen Sie den *Quellcomputer* aus der Liste der angezeigten Computer aus. <br/> ![Liste der Computer][14]

7. Wie oben beschrieben, wählen Sie entweder **Dateien suchen** oder **Dateien durchsuchen**. In diesem Abschnitt verwenden wir die Option **Dateien suchen**. <br/> ![Suchen,][15]

8. Wählen Sie im nächsten Bildschirm das Volume und das Datum aus. Suchen Sie nach den Ordner-/Dateinamen, die Sie wiederherstellen möchten. <br/> ![Elemente suchen][16]

9. Wählen Sie den Speicherort aus, an dem die Dateien wiederhergestellt werden sollen. <br/> ![Wiederherstellungsspeicherort][17]

10. Geben Sie die Verschlüsselungspassphrase an, die während der Registrierung des *Quellcomputers* beim *Beispieltresor* bereitgestellt wurde. <br/> ![Verschlüsselung][18]

Klicken Sie nach dem Bereitstellen der Eingabe auf die Schaltfläche **Wiederherstellen**. Die Wiederherstellung der gesicherten Dateien im angegebenen Ziel wird ausgelöst.

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
 

<!---HONumber=62-->