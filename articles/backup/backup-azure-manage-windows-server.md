<properties
	pageTitle="Verwalten von Azure Backup-Tresoren und -Servern | Microsoft Azure"
	description="In diesem Tutorial erfahren Sie, wie Sie Azure Backup-Tresore und -Server verwalten."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="jimpark; markgal"/>


# Verwalten von Azure Backup-Tresoren und -Servern
In diesem Artikel finden Sie eine Übersicht der Sicherungsverwaltungsaufgaben, die über das Verwaltungsportal verfügbar sind

1. Melden Sie sich im [Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **Wiederherstellungsdienste** und dann auf den Namen des Sicherungstresors, um die Seite "Schnellstart" anzuzeigen.

    Über die Optionen oben auf der Schnellstartseite können Sie die verfügbaren Verwaltungsaufgaben anzeigen.

    ![Geschützte Elemente](./media/backup-azure-manage-windows-server/RS_tabs.png)

## Dashboard
Klicken Sie auf **Dashboard**, um die Nutzungsübersicht für den Server anzuzeigen. Unten im Dashboard können Sie folgende Aufgaben ausführen:

- **Zertifikat verwalten**. Wenn ein Zertifikat zum Registrieren des Servers verwendet wurde, verwenden Sie diese Aufgabe, um das Zertifikat zu aktualisieren. Wenn Sie Tresoranmeldeinformationen verwenden, verwenden Sie **Zertifikat verwalten** nicht.
- **Löschen**. Dies wird verwendet, um den aktuellen Sicherungstresor zu löschen. Wenn ein Sicherungstresor nicht mehr verwendet wird, können Sie ihn löschen, um Speicherplatz freizugeben. **Löschen** wird erst aktiviert, wenn alle registrierten Server aus dem Tresor gelöscht wurden.
- **Tresorsanmeldeinformationen**. Verwenden Sie diese Menüoption in der Schnelleinsicht, um Ihre Tresoranmeldeinformationen zu konfigurieren.

## Geschützte Elemente
Klicken Sie auf **Geschützte Elemente**, um die Elemente anzuzeigen, die von den Servern gesichert wurden. Diese Liste dient nur zu Informationszwecken.

![Geschützte Elemente](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## Registrierte Elemente
Klicken Sie auf **Registrierte Elemente**, um die Namen der Server anzuzeigen, die für diesen Tresor registriert sind.

![Server gelöscht](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

Jetzt können Sie folgende Aufgaben ausführen:

- **Erneute Registrierung zulassen** – Wenn diese Option für einen Server ausgewählt ist, können Sie den **Registrierungs-Assistenten** des Agents verwenden, um den Server erneut für den Sicherungstresor zu registrieren. Eine erneute Registrierung ist möglicherweise nötig, wenn im Zertifikat ein Fehler auftritt oder wenn ein Server erneut erstellt werden muss.
- **Löschen** – Dies wird verwendet, um einen Server aus dem Sicherungstresor zu löschen. Alle gespeicherten Daten dieses Servers werden sofort gelöscht.

## Nächste Schritte
- [Wiederherstellen von Windows-Servern oder Windows-Clients aus Azure](backup-azure-restore-windows-server.md)
- Weitere Informationen zu Azure Backup finden Sie unter [Azure Backup – Übersicht](backup-introduction-to-azure-backup.md).
- Besuchen Sie das [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0302_2016-->