<properties
   pageTitle="Sichern von Windows Server- oder Windows-Clientdateien und -ordnern in Azure | Microsoft Azure"
   description="Erstellen Sie mithilfe dieser einfachen Methode ein Backup eines Windows Servers oder Windows Clients auf Azure mit diesem. Windows-Dateien und -Ordner lassen sich in wenigen einfachen Schritten in der Cloud sichern."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="Windows Server-Backup; Windows Server sichern"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="02/05/2016"
	 ms.author="jimpark;"/>

# Sichern von Windows Server- oder Windows-Clientdateien und -ordnern in Azure
Das Sichern von Windows-Dateien und -Ordnern in Azure ist mit dieser unkomplizierten Prozedur sehr einfach. Falls noch nicht erfolgt, erfüllen Sie die [Voraussetzungen](backup-configure-vault.md#before-you-start) zur Vorbereitung Ihrer Umgebung auf die Sicherung Ihres Windows-Computers, ehe Sie fortfahren.

## Sichern von Dateien und Ordnern
1. Nach der Registrierung des Computers öffnen Sie das Microsoft Azure Backup-MMC-Snap-In.

    ![Suchergebnis](./media/backup-azure-backup-windows-server/result.png)

2. Klicken Sie auf **Sicherung planen**.

    ![Planen eines Windows Server-Backups](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Wählen Sie die Elemente, die Sie sichern möchten. Mit Azure Backup können Sie auf einem Windows Server/Windows-Client (ohne System Center Data Protection Manager) Ihre Dateien und Ordner schützen.

    ![Elemente eines Windows Server-Backups](./media/backup-azure-backup-windows-server/items.png)

4. Geben Sie den Sicherungszeitplan und die Aufbewahrungsrichtlinie an, die im folgenden [Artikel](backup-azure-backup-cloud-as-tape.md) im Detail erläutert wird.

5. Wählen Sie die Methode zum Senden der ersten Sicherung aus. Ihre Auswahl zum Abschließen des anfänglichen Seedings ist abhängig von der Datenmenge, die Sie sichern möchten, und von der Internetgeschwindigkeit beim Hochladen. Wenn Sie mehrere GB/TB über eine Verbindung mit hoher Latenz und niedriger Bandbreite sichern möchten, empfiehlt es sich, bei der ersten Sicherung einen Datenträger an das nächstgelegene Azure-Datencenter zu senden. Diese Vorgehensweise heißt "Offlinesicherung" und wird detailliert in diesem [Artikel](backup-azure-backup-import-export.md) behandelt. Wenn Sie über eine ausreichende Bandbreite verfügen, wird empfohlen, die erste Sicherung über das Netzwerk auszuführen.

    ![Erstes Windows Server-Backup](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Sobald der Prozess zur Sicherungsplanung abgeschlossen ist, wechseln Sie zurück zum MMC-Snap-In, und klicken Sie auf **Jetzt sichern**, um das anfängliche Seeding über das Netzwerk abzuschließen.

    ![Windows Server jetzt sichern](./media/backup-azure-backup-windows-server/backupnow.png)

7. Wenn das anfängliche Seeding abgeschlossen ist, wird der Status in der Azure Backup-Konsole in der Anzeige **Aufträge** angezeigt.

    ![Abgeschlossen](./media/backup-azure-backup-windows-server/ircomplete.png)

## Nächste Schritte
- [Verwalten von Windows-Servern oder Windows-Clients](backup-azure-manage-windows-server.md)
- [Wiederherstellen von Windows-Servern oder Windows-Clients aus Azure](backup-azure-restore-windows-server.md)
- [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)
- Besuchen Sie das [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0211_2016-->