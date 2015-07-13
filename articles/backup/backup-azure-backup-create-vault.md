<properties
   pageTitle="Azure Backup – Erstellen eines Sicherungstresors und Angeben der Speicherredundanz"
   description="Erfahren Sie, wie ein Sicherungstresor erstellt wird, und geben Sie in Azure Backup Optionen für die Speicherredundanz an."
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
Zum Sichern von Dateien und Daten von Windows Server oder System Center Data Protection Manager (SCDPM) in Azure oder beim Sichern von Sie IaaS-VMs in Azure müssen Sie einen Sicherungstresor in Ihrer geografischen Region erstellen, in dem die Daten gespeichert werden sollen.

Dieses Lernprogramm führt Sie durch die Erstellung des Tresors, den Sie zum Speichern von Sicherungen verwenden.

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an.
2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor**, und wählen Sie **Schnellerfassung**. <br/> ![Tresor erstellen][1]

3. Geben Sie für den Parameter **Name** einen benutzerfreundlichen Namen zur Identifizierung des Sicherungstresors ein. Dieser muss für jedes Abonnement eindeutig sein.

4. Wählen Sie für den Parameter **Region** die geografische Region für den Sicherungstresor aus. Die Auswahl bestimmt den geografischen Standort, an den Ihre Sicherungsdaten gesendet werden. Wenn Sie einen geografischen Standort in der Nähe Ihres eigenen Standorts auswählen, können Sie dadurch die Netzwerklatenz beim Sichern in Azure reduzieren.

5. Klicken Sie auf **Tresor erstellen**, um den Workflow abzuschließen. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Um den Status zu überprüfen, können Sie die Benachrichtigungen am unteren Rand des Portals überwachen. <br/> ![Erstellen eines Tresors][2]

6. Nach dem Erstellen des Sicherungstresors erhalten Sie eine Nachricht, dass der Tresor erfolgreich erstellt wurde. Er wird anschließend in den Ressourcen der Recovery Services als **Aktiv** angezeigt. <br/> ![Status zum Erstellen eines Tresors][3]


## Azure Backup – Speicherredundanzoptionen

Der beste Zeitpunkt zum Identifizieren von Speicherredundanzoptionen ist unmittelbar nach der Erstellung des Tresors, bevor Computer beim Tresor registriert werden. Sobald ein Element beim Tresor registriert wurde, wird die Speicherredundanzoption gesperrt und kann nicht mehr geändert werden.

Die Speicherredundanz des Back-End-Speichers von Azure Backup wird durch Ihre geschäftlichen Anforderungen bestimmt. Wenn Sie Azure als primären Speicherendpunkt für die Sicherung verwenden (wenn Sie beispielsweise von einem Windows Server in Azure sichern), sollten Sie die Option "Georedundanter Speicher" in Betracht ziehen (Standardeinstellung). Diese finden Sie unter der Option **Konfigurieren** Ihres Sicherungstresors. <br/> ![GRS][4]

### Georedundanter Speicher (GRS)
GRS bewahrt sechs Kopien Ihrer Daten auf. Mit GRS werden Ihre Daten dreimal innerhalb der primären Region und dreimal in einer sekundären Region Hunderte von Kilometern von der primären Region entfernt repliziert, wodurch höchste Beständigkeit erreicht wird. Im Fall eines Fehlers in der primären Region stellt Azure Backup durch das Speichern von Daten in GRS sicher, dass Ihre Daten dauerhaft in zwei verschiedenen Regionen aufbewahrt werden.

### Lokal redundanter Speicher (LRS)
Lokal redundanter Speicher (LRS) verwaltet drei Kopien Ihrer Daten. LRS wird innerhalb eines einzelnen Standorts dreimal in einer einzelnen Region repliziert. LRS schützt Ihre Daten vor normalen Hardwareausfällen, jedoch nicht vor dem Ausfall einer ganzen Azure-Einrichtung.

Bei Verwendung von Azure als tertiären Speicherendpunkt (Sie verwenden beispielsweise SCDPM, um eine lokale Sicherungskopie vor Ort zu haben, und Azure für Ihre langfristigen Aufbewahrungsanforderungen), sollten Sie unter der Option **Konfigurieren** Ihres Sicherungstresors die Option "Lokal redundanter Speicher" auswählen. Dadurch werden die Kosten zum Speichern von Daten in Azure gesenkt, und es wird eine geringere Dauerhaftigkeit für Ihre Daten bereitgestellt, die möglicherweise für tertiäre Kopien ausreicht. <br/> ![LRS][5]




## Hinweis

+ Ab März 2015 Kunden steht den Kunden keine programmgesteuerte Möglichkeit (wie PowerShell) zur Erstellung eines Sicherungstresors zur Verfügung.

+ Die Speicherredundanz sollte unmittelbar nach der Erstellung des Tresors ausgewählt werden, bevor Computer beim Tresor registriert werden. Sobald ein Element beim Tresor registriert wurde, wird die Speicherredundanzoption gesperrt und kann nicht mehr geändert werden.

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png
 

<!---HONumber=62-->