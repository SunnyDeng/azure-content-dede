<properties
	pageTitle="Konfigurieren von Azure Backup-Diensten zur Vorbereitung auf die Sicherung von Windows-Servern | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie der Backup-Dienst in der Microsoft Azure Cloud verwendet wird, um Windows Server in der Cloud zu sichern."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="11/03/2015" ms.author="jimpark"; "aashishr"/>

# Vorbereiten der Umgebung für die Sicherung von Windows-Computern

In diesem Artikel erfahren Sie, wie Sie das Feature Azure Backup aktivieren. Um Windows-Server oder Windows-Clients auf Azure zu sichern, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
>[AZURE.NOTE]Früher mussten Sie ein X.509 v3-Zertifikat erstellen oder beziehen, um Ihren Sicherungsserver zu registrieren. Zertifikate werden weiterhin unterstützt, aber jetzt können Sie direkt auf der Seite "Schnellstart" Tresoranmeldeinformationen generieren, wodurch der Azure-Tresor einfacher bei einem Server registriert werden kann.

## Vorbereitung
Um Dateien und Daten von Ihrem Windows-Server auf Azure zu sichern, müssen Sie zunächst folgende Schritte durchführen:

- **Erstellen eines Sicherungstresors** – Erstellen Sie einen Tresors in der Azure Backup-Konsole.
- **Herunterladen von Tresoranmeldedaten** – Laden Sie in Azure Backup das Verwaltungszertifikat hoch, das Sie für den Tresor erstellt haben.
- **Installieren des Azure Backup-Agents und Registrieren des Servers**: Installieren Sie von Azure Backup aus den Agent, und registrieren Sie den Server im Sicherungstresor.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Nächste Schritte
- [Sichern von Windows-Servern oder Windows-Clients](backup-azure-backup-windows-server.md)
- [Verwalten von Windows-Servern oder Windows-Clients](backup-azure-manage-windows-server.md)
- [Wiederherstellen von Windows-Servern oder Windows-Clients aus Azure](backup-azure-restore-windows-server.md)
- [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)
- [Azure Backup – Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=Nov15_HO3-->