<properties
   pageTitle="Sichern von Windows Server- oder Windows-Clientdateien und -ordnern in Azure | Microsoft Azure"
   description="Erfahren Sie, wie Windows Server- oder Windows-Clientcomputer in Azure gesichert werden."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/13/2015" ms.author="jimpark"; "aashishr"/>

# Sichern von Windows Server- oder Windows-Clientdateien und -ordnern in Azure
Dieser Artikel behandelt die erforderlichen Schritte zur Sicherung von Windows Server- oder Windows-Clientdateien und -ordnern in Azure.

## Vorbereitung
Vergewissern Sie sich zunächst, dass für den Schutz von Windows Server- und Windows-Clientcomputern mit Microsoft Azure Backup alle [Voraussetzungen](backup-configure-vault.md#before-you-start) erfüllt sind. Die Voraussetzungen umfassen Aufgaben wie das Erstellen eines Sicherungstresors, das Herunterladen von Tresoranmeldedaten, das Installieren des Azure Backup-Agents und das Registrieren des Computers beim Tresor.

## Sichern von Dateien
1. Nach der Registrierung des Computers öffnen Sie das Microsoft Azure Backup-MMC-Snap-In.

    ![Suchergebnis](./media/backup-azure-backup-windows-server/result.png)

2. Klicken Sie auf **Sicherung planen**.

    ![Planen einer Sicherung](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Wählen Sie die Elemente, die Sie sichern möchten. Mit Azure Backup können Sie auf einem Windows Server/Windows-Client (ohne System Center Data Protection Manager) Ihre Dateien und Ordner schützen.

    ![Zu sichernde Elemente](./media/backup-azure-backup-windows-server/items.png)

4. Geben Sie den Sicherungszeitplan und die Aufbewahrungsrichtlinie an, die im folgenden [Artikel](backup-azure-backup-cloud-as-tape.md) im Detail erläutert wird.

5. Wählen Sie die Methode zum Senden der ersten Sicherung aus. Ihre Auswahl zum Abschließen des anfänglichen Seedings ist abhängig von der Datenmenge, die Sie sichern möchten, und von der Internetgeschwindigkeit beim Hochladen. Wenn Sie mehrere GB/TB über eine Verbindung mit hoher Latenz und niedriger Bandbreite sichern möchten, empfiehlt es sich, bei der ersten Sicherung einen Datenträger an das nächstgelegene Azure-Datencenter zu senden. Diese Vorgehensweise heißt "Offlinesicherung" und wird detailliert in diesem [Artikel](backup-azure-backup-import-export.md) behandelt. Wenn Sie über eine ausreichende Bandbreite verfügen, wird empfohlen, die erste Sicherung über das Netzwerk auszuführen.

    ![Erste Sicherung](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Sobald der Prozess zur Sicherungsplanung abgeschlossen ist, wechseln Sie zurück zum MMC-Snap-In, und klicken Sie auf **Jetzt sichern**, um das anfängliche Seeding über das Netzwerk abzuschließen.

    ![Jetzt sichern](./media/backup-azure-backup-windows-server/backupnow.png)

7. Wenn das anfängliche Seeding abgeschlossen ist, wird der Status in der Azure Backup-Konsole in der Anzeige **Aufträge** angezeigt.

    ![Abgeschlossen](./media/backup-azure-backup-windows-server/ircomplete.png)

## Nächste Schritte
- [Verwalten von Windows-Servern oder Windows-Clients](backup-azure-manage-windows-server.md)
- [Wiederherstellen von Windows-Servern oder Windows-Clients aus Azure](backup-azure-restore-windows-server.md)
- [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO3-->