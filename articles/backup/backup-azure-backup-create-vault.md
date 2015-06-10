<properties
	pageTitle="Azure-Sicherung – Erstellen eines sicherungstresors, und geben Sie die Speicherredundanz"
	description="Enthält Informationen zum Erstellen eines sicherungstresors, geben Sie in Azure Backup-Optionen für Speicherredundanz"
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
	 ms.date="04/08/2015"
	 ms.author="prvijay"/>

# Erstellen eines Sicherungstresors
Zum Sichern von Dateien und Daten von Ihrem Windows Server oder System Center Data Protection Manager (SCDPM) in Azure oder beim IaaS-VMs in Azure sichern, müssen Sie einen sicherungstresor in der geografischen Region erstellen, in dem Sie die Daten speichern möchten.

Dieses Lernprogramm führt Sie durch die Erstellung des Depots, die Sie zum Speichern von Sicherungen verwenden.

1. Melden Sie sich bei der [Verwaltungsportal](https://manage.windowsazure.com/)
2. Klicken Sie auf **Neu** -> **Data Services** -> **Recovery Services** -> **Sicherungstresor** und wählen Sie **Schnellerfassung** <br/> ![Tresor erstellen][1]

3. Für den **Namen** Parameter, geben Sie einen Anzeigenamen ein, den sicherungstresor zu identifizieren. Dieser muss für jedes Abonnement eindeutig sein.

4. Für die **Region** -Parameter, die geografische Region für den sicherungstresor auswählen. Die Auswahl bestimmt die geografische, an den Ihre Sicherungsdaten gesendet werden. Eine geografische nahe an Ihrem Standort die Option auswählen, können Sie die Netzwerklatenz reduzieren, in Azure sichern.

5. Klicken Sie auf **Tresor erstellen** um den Workflow abzuschließen. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Um den Status zu überprüfen, können Sie die Benachrichtigungen am unteren Rand des Portals überwachen. <br/> ![Tresor erstellen][2]

6. Nachdem der sicherungstresor erstellt wurde, eine Nachricht teilt Ihnen der Tresor erfolgreich erstellt und werden Wiederherstellungsdienste als in den Ressourcen aufgeführt werden **Active**. <br/> ![Erstellen von tresorstatus][3]


## Azure Backup - Speicheroptionen für Redundanz

Der beste Zeitpunkt zum Identifizieren von Redundanz Speicheroption wird unmittelbar nach der Erstellung der Tresor und bevor alle Computer in den Tresor registriert sind. Sobald ein Element in den Tresor registriert wurde, wird Redundanz Speicheroption ist gesperrt und kann nicht geändert werden.

Ihre geschäftlichen Anforderungen würde die Speicherredundanz Azure Backup Back-End-Speicher bestimmen. Bei Verwendung von Azure als primäre backup-Speicher-Endpunkt (z. B. Sichern in Azure von einem Windows-Server), sollten Sie Geo-Redundant Storage-Option auswählen (Standardeinstellung). Dies tritt unter den **konfigurieren** Möglichkeit, Ihr sicherungstresor. <br/> ![GRS][4]

### Georedundanter Speicher (GRS)
GRS bewahrt sechs Kopien Ihrer Daten auf. Mit GRS werden Ihre Daten dreimal innerhalb der primären Region und dreimal in einer sekundären Region Hunderte von Kilometern von der primären Region entfernt repliziert, wodurch höchste Beständigkeit erreicht wird. Im Fall eines Fehlers in der primären Region, durch das Speichern von Daten in GRS Azure-Sicherung werden sichergestellt, dass die Daten dauerhaft in zwei verschiedenen Regionen.

### Lokal redundanter Speicher (LRS)
Lokal redundanter Speicher (LRS) verwaltet drei Kopien Ihrer Daten. LRS wird innerhalb eines einzelnen Standorts dreimal in einer einzelnen Region repliziert. LRS schützt Ihre Daten vor normalen Hardwarefehlern, jedoch nicht vor dem Ausfall einer gesamten Azure-Funktion.

Bei Verwendung von Azure als Sicherungsspeicher tertiären Endpunkt (z. B. Sie SCDPM verwenden, um eine lokale Sicherung lokalen & mithilfe von Azure für die langfristige Anforderungen kopieren), sollten Sie Auswahl lokal redundanter Speicher aus der **konfigurieren** Möglichkeit, Ihre sicherungstresor. Dadurch wird die Kosten zum Speichern von Daten in Azure, beim Bereitstellen einer niedrigeren Ebene der Dauerhaftigkeit für Ihre Daten, die möglicherweise für Dritte Kopien zulässig. <br/> ![LRS][5]




## Hinweis

+ Ab März 2015 Kunden müssen keine programmgesteuerte (z.B.: PowerShell) die Möglichkeit zum Erstellen eines sicherungstresors.

+ Unmittelbar nach der Erstellung der Tresor und bevor alle Computer in den Tresor registriert sind, sollte die Speicherredundanz ausgewählt werden. Sobald ein Element in den Tresor registriert wurde, wird Redundanz Speicheroption ist gesperrt und kann nicht geändert werden.

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png

<!---HONumber=GIT-SubDir--> 