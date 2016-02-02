<properties
	pageTitle="Vorbereiten der Umgebung zum Sichern von Windows Server- oder Windows-Clientinstanzen | Microsoft Azure"
	description="Bereiten Sie Ihre Umgebung für die Sicherung von Windows vor, indem Sie einen Sicherungstresor erstellen, Anmeldeinformationen herunterladen und den Backup-Agent installieren."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="Sicherungstresor; Backup-Agent; Backup Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Vorbereiten der Umgebung für die Sicherung von Windows-Computern
In diesem Artikel erfahren Sie, wie Sie die Sicherung einer Windows Server- oder Windows Clientinstanz in Azure vorbereiten. Hierzu benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

Wenn Sie dies bereits erledigt haben, können Sie mit dem [Sichern Ihrer Windows-Computer](backup-azure-backup-windows-server.md) beginnen. Andernfalls führen Sie die unten stehenden Schritte durch, um sicherzustellen, dass Ihre Umgebung vorbereitet ist.

>[AZURE.NOTE] Früher mussten Sie ein X.509 v3-Zertifikat erstellen oder beziehen, um Ihren Sicherungsserver zu registrieren. Zertifikate werden weiterhin unterstützt, aber jetzt können Sie direkt auf der Seite "Schnellstart" Tresoranmeldeinformationen generieren, wodurch der Azure-Tresor einfacher bei einem Server registriert werden kann.

## Vorbereitung
Um Dateien und Daten von Ihrem Windows-Server auf Azure zu sichern, müssen Sie zunächst folgende Schritte durchführen:

- **Erstellen eines Sicherungstresors**: Erstellen Sie einen Tresor im [Azure Backup-Verwaltungsportal](http://manage.windowsazure.com).
- **Herunterladen der Tresoranmeldeinformationen**: Laden Sie im Azure Backup-Tresor von der Dashboardseite die Anmeldeinformationen für den Tresor herunter, die zum Registrieren des Windows-Computers beim Sicherungstresor verwendet werden.
- **Installieren des Azure Backup-Agents und Registrieren des Servers**: Klicken Sie auf der Dashboardseite auf den Link zum Herunterladen des [Azure Backup-Agents](http://aka.ms/azurebackup_agent). Installieren Sie den Agent, und registrieren Sie den Server mithilfe der Tresoranmeldeinformationen beim Sicherungstresor.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Nächste Schritte
- [Sichern von Windows-Servern oder Windows-Clients](backup-azure-backup-windows-server.md)
- [Verwalten von Windows-Servern oder Windows-Clients](backup-azure-manage-windows-server.md)
- [Wiederherstellen von Windows-Servern oder Windows-Clients aus Azure](backup-azure-restore-windows-server.md)
- [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)
- [Azure Backup – Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0128_2016-->