<properties
   pageTitle="Wiederherstellen von Daten auf Windows Server- oder Windows-Clientcomputern aus Azure | Microsoft Azure"
   description="Erfahren Sie, wie Daten auf einem Windows Server- oder Windows-Clientcomputer wiederhergestellt werden."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/21/2015" ms.author="jimpark"; "aashishr"/>

# Wiederherstellen von Windows Server- oder Windows-Clientcomputern
In diesem Artikel werden die erforderlichen Schritte für zwei Arten von Wiederherstellungsvorgängen erläutert:

- Wiederherstellen von Daten auf dem gleichen Computer, auf dem die Sicherungen erstellt wurden
- Wiederherstellen von Daten auf einem beliebigen anderen Computer

In beiden Fällen werden die Daten aus dem Azure Backup-Tresor abgerufen.

## Wiederherstellen von Daten auf demselben Computer
Wenn Sie versehentlich eine Datei gelöscht haben und sie auf demselben Computer wiederherstellen möchten (auf dem die Sicherung erstellt wurde), können Sie die folgenden Schritte zum Wiederherstellen der Daten ausführen.

1. Öffnen Sie das Snap-In **Microsoft Azure Backup**.
2. Klicken Sie auf **Daten wiederherstellen**, um den Workflow zu initiieren.

    ![Wiederherstellen von Daten](./media/backup-azure-restore-windows-server/recover.png)

3. Wählen Sie die Option **Dieser Server (*NameIhresComputers*)**, wenn Sie die gesicherte Datei auf demselben Computer wiederherstellen möchten.

    ![Auf demselben Computer](./media/backup-azure-restore-windows-server/samemachine.png)

4. Sie können entscheiden, ob Sie **Dateien durchsuchen** oder **Dateien suchen** möchten.

    Behalten Sie die Standardoption bei, wenn Sie beabsichtigen, eine oder mehrere Dateien wiederherzustellen, deren Pfad bekannt ist. Wenn Sie die Ordnerstruktur nicht genau kennen und nach einer Datei suchen möchten, wählen Sie die Option **Dateien suchen**. In diesem Abschnitt verwenden wir die Standardoption.

    ![Dateien durchsuchen](./media/backup-azure-restore-windows-server/browseandsearch.png)

5. Wählen Sie das Volume aus, von dem Sie die Datei wiederherstellen möchten.

    Sie können den Zustand eines beliebigen Zeitpunkts wiederherstellen. Datumsangaben, die im Kalendersteuerelement **fett** dargestellt werden, geben die Verfügbarkeit eines Wiederherstellungspunkts an. Nachdem Sie ein Datum ausgewählt haben, können Sie basierend auf Ihrem Sicherungszeitplan (und dem Erfolg eines Sicherungsvorgangs) einen Zeitpunkt in der Dropdownliste **Zeit** auswählen.

    ![Volume und Datum](./media/backup-azure-restore-windows-server/volanddate.png)

6. Wählen Sie die Elemente, die wiederhergestellt werden sollen. Sie können eine Mehrfachauswahl von Ordnern/Dateien treffen, die Sie wiederherstellen möchten.

    ![Auswählen von Dateien](./media/backup-azure-restore-windows-server/selectfiles.png)

7. Geben Sie die Wiederherstellungsparameter an.

    ![Wiederherstellungsoptionen](./media/backup-azure-restore-windows-server/recoveroptions.png)

  - Sie können eine Wiederherstellung am ursprünglichen Speicherort (bei der die Datei oder der Ordner überschrieben wird) oder an einem anderen Speicherort auf demselben Computer durchführen.
  - Wenn die Datei oder der Ordner für die Wiederherstellung bereits am Zielspeicherort vorhanden ist, können Sie Kopien erstellen (zwei Versionen derselben Datei), die Dateien im Zielverzeichnis überschreiben oder die Wiederherstellung der Dateien überspringen, die am Zielspeicherort vorhanden sind.
  - Es wird dringend empfohlen, die Standardoption beizubehalten und die ACLs für die wiederherzustellenden Dateien ebenfalls wiederherzustellen.

8. Sobald Sie diese Eingaben bereitgestellt haben, klicken Sie auf **Weiter**. Der Wiederherstellungsworkflow, bei dem die Dateien auf diesem Computer wiederhergestellt werden, wird gestartet.

## Wiederherstellen auf einem anderen Computer
Wenn der gesamte Server verloren geht, können Sie Daten aus Azure Backup dennoch auf einem anderen Computer wiederherstellen. Der Workflow wird in den folgenden Schritten beschrieben.

In diesen Schritten wird folgende Terminologie verwendet:

- *Quellcomputer* – Der ursprüngliche Computer, von dem die Sicherung erstellt wurde und der zurzeit nicht verfügbar ist.
- *Zielcomputer* – Der Computer, auf dem die Daten wiederhergestellt werden.
- *Beispieltresor* – Der Sicherungstresor, bei dem der *Quellcomputer* und der *Zielcomputer* registriert sind. <br/>

> [AZURE.NOTE]Sicherungen von einem Computer können nicht auf einem Computer wiederhergestellt werden, auf dem eine frühere Version des Betriebssystems ausgeführt wird. Wenn beispielsweise Sicherungen auf einem Windows 7-Computer erstellt werden, können diese auf einem Computer unter Windows 8 oder höher wiederhergestellt werden. Umgekehrt ist dies jedoch nicht möglich.

1. Öffnen Sie das Snap-In **Microsoft Azure Backup** auf dem *Zielcomputer*.
2. Stellen Sie sicher, dass der *Zielcomputer* und der *Quellcomputer* in demselben Sicherungstresor registriert sind.
3. Klicken Sie auf **Daten wiederherstellen**, um den Workflow zu initiieren.

    ![Wiederherstellen von Daten](./media/backup-azure-restore-windows-server/recover.png)

4. Wählen Sie **Anderer Server**

    ![Anderer Server](./media/backup-azure-restore-windows-server/anotherserver.png)

5. Geben Sie die Datei mit den Tresoranmeldeinformationen an, die dem *Beispieltresor* entspricht. Wenn die Datei mit den Tresoranmeldeinformationen ungültig (oder abgelaufen) ist, laden Sie eine neue Anmeldeinformationsdatei für den Tresor aus dem *Beispieltresor* im Azure-Portal herunter. Sobald die Anmeldeinformationsdatei für den Tresor bereitgestellt wurde, wird der Sicherungstresor angezeigt, der der Tresor-Anmeldeinformationsdatei entspricht.

6. Wählen Sie den *Quellcomputer* aus der Liste der angezeigten Computer aus.

    ![Liste der Computer](./media/backup-azure-restore-windows-server/machinelist.png)

7. Wählen Sie entweder die Option **Dateien suchen** oder **Dateien durchsuchen**. In diesem Abschnitt verwenden wir die Option **Dateien suchen**.

    ![Suchen,](./media/backup-azure-restore-windows-server/search.png)

8. Wählen Sie im nächsten Bildschirm das Volume und das Datum aus. Suchen Sie nach den Ordner-/Dateinamen, die Sie wiederherstellen möchten.

    ![Elemente suchen](./media/backup-azure-restore-windows-server/searchitems.png)

9. Wählen Sie den Speicherort aus, an dem die Dateien wiederhergestellt werden sollen.

    ![Wiederherstellungsspeicherort](./media/backup-azure-restore-windows-server/restorelocation.png)

10. Geben Sie die Verschlüsselungspassphrase an, die während der Registrierung des *Quellcomputers* beim *Beispieltresor* bereitgestellt wurde.

    ![Verschlüsselung](./media/backup-azure-restore-windows-server/encryption.png)

11. Klicken Sie nach dem Bereitstellen der Eingabe auf **Wiederherstellen**. Die Wiederherstellung der gesicherten Dateien am angegebenen Ziel wird ausgelöst.

## Nächste Schritte
- [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)
- Besuchen Sie das [Azure Sicherungs-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## Weitere Informationen
- [Azure Backup – Übersicht](http://go.microsoft.com/fwlink/p/?LinkId=222425)
- [Sichern von virtuellen Azure-Computern](backup-azure-vms-introduction.md)
- [Sichern von Microsoft-Workloads](backup-azure-dpm-introduction.md)

<!---HONumber=Oct15_HO4-->