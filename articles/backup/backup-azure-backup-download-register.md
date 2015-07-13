<properties
   pageTitle="Herunterladen, Installieren und Registrieren eines Azure Backup-Agents"
   description="Erfahren Sie, wie und wo Sie den Azure Backup-Agent herunterladen, Installationsschritte durchführen und den Azure Backup-Agent mithilfe der Tresoranmeldeinformationen registrieren."
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="04/08/2015"
   ms.author="prvijay"/>

# Herunterladen, Installieren und Registrieren eines Azure Backup-Agents

Nach dem Erstellen des Azure Backup-Tresors sollte auf jedem lokalen Server (Windows Server, Windows-Client oder System Center Data Protection Manager-Server) ein Agent installiert werden, der Ihnen das Sichern von Daten und Anwendungen in Azure ermöglicht. Dieser Artikel behandelt die erforderlichen Schritte zur Einrichtung des Azure Backup-Agents auf einem Windows Server- oder Windows-Clientcomputer.

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an.

2. Klicken Sie auf **Recovery Services**, und wählen Sie den Sicherungstresor aus, den Sie bei einem Server registrieren möchten. Die Seite "Schnellstart" für diesen Sicherungstresor wird angezeigt. <br/> ![Schnellstart][1]

3. Klicken Sie auf der Seite "Schnellstart" unter der Option **Agent herunterladen** auf **Für Windows Server oder System Center Data Protection Manager oder Windows-Client**. Klicken Sie auf "Speichern", um ihn auf den lokalen Computer zu kopieren. <br/> ![Agent speichern][2]

4. Sobald der Agent installiert ist, doppelklicken Sie auf "MARSAgentInstaller.exe", um die Installation des Azure Backup-Agents zu starten. Wählen Sie den Installationsordner und den Ablageordner aus, der für den Agent erforderlich ist. Der verfügbare Speicherplatz am angegebenen Speicherort muss mindestens 5 % der Sicherungsdaten betragen.

5.	Wenn Sie einen Proxyserver für die Verbindung mit dem Internet verwenden, geben Sie im Bildschirm **Proxykonfiguration** die Details des Proxyservers ein. Wenn Sie einen authentifizierten Proxy verwenden, geben Sie in diesem Bildschirm die Informationen zum Benutzernamen und zum Kennwort ein.

6.	Der Azure Backup-Agent installiert das .NET Framework 4.5 und Windows PowerShell (falls noch nicht verfügbar), um die Installation abzuschließen.

7.	Sobald der Agent installiert ist, klicken Sie auf die Schaltfläche **Mit Registrierung fortfahren**, um den Workflow fortzusetzen. <br/> ![Registrieren][3]

8. Suchen Sie im Bildschirm für die Tresoranmeldeinformationen nach der zuvor heruntergeladenen Datei mit den Tresoranmeldeinformationen, und wählen Sie sie aus. <br/> ![Tresoranmeldeinformationen][4] <br/> <br/>
> [AZURE.NOTE]Die Datei mit den Tresoranmeldeinformationen gilt nur für 48 Stunden (nachdem sie aus dem Portal heruntergeladen wurde). Wenn in diesem Bildschirm Fehler auftreten (z. B. "Datei mit Tresoranmeldeinformationen abgelaufen"), melden Sie sich beim Azure-Portal an, und laden Sie die Datei mit den Tresoranmeldeinformationen erneut herunter.
>
> Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Speicherort verfügbar ist, der für die Setupanwendung zugänglich ist. Wenn Zugriffsfehler auftreten, kopieren Sie die Datei mit den Tresoranmeldeinformationen in einen temporären Speicherort auf diesem Computer, und wiederholen Sie den Vorgang.
>
> Wenn Sie einen Fehler wegen ungültiger Tresoranmeldeinformationen erhalten (z. B. "Ungültige Tresoranmeldeinformationen angegeben"), ist die Datei entweder beschädigt oder enthält nicht die aktuellen Anmeldeinformationen, die dem Wiederherstellungsdienst zugeordnet sind. Wiederholen Sie den Vorgang, nachdem Sie eine neue Datei mit Tresoranmeldeinformationen aus dem Portal heruntergeladen haben. Dieser Fehler tritt in der Regel auf, wenn der Benutzer im Azure-Portal in schneller Folge auf die Option zum Herunterladen der Tresoranmeldeinformationen klickt. In diesem Fall ist nur die zweite Datei mit Tresoranmeldeinformationen gültig.

9. Im Bildschirm **Verschlüsselungseinstellung** können Sie entweder eine Passphrase generieren oder eine Passphrase bereitstellen (mindestens 16 Zeichen) und die Passphrase an einem sicheren Ort aufbewahren. <br/> ![Verschlüsselung][5] <br/> <br/>
> [AZURE.WARNING]Wenn die Passphrase verloren geht oder vergessen wird, kann Microsoft Ihnen bei der Wiederherstellung der Sicherungsdaten nicht behilflich sein. Der Benutzer besitzt die Verschlüsselungspassphrase, und Microsoft kann nicht auf die vom Endbenutzer verwendete Passphrase zugreifen. Speichern Sie die Datei an einem sicheren Ort, da sie bei einem Wiederherstellungsvorgang erforderlich ist.

10. Nach dem Klicken auf die Schaltfläche **Fertig stellen** wird der Computer erfolgreich beim Tresor registriert, und Sie können mit der Sicherung in Microsoft Azure beginnen. Sie können die während des Registrierungsworkflows angegebenen Einstellungen ändern, indem Sie im Azure Backup-MMC-Snap-In auf die Option **Eigenschaften ändern** klicken. <br/> ![Eigenschaften ändern][6]

## Nächste Schritte
+ Bei Fragen zum Workflow finden Sie weitere Informationen im Abschnitt zur Installation und Konfiguration in den [häufig gestellten Fragen](backup-azure-backup-faq.md) zu Azure Backup.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png
 

<!---HONumber=62-->