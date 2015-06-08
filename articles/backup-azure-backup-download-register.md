<properties
	pageTitle="Herunterladen, installieren und Registrieren der Azure-Sicherungs-agent"
	description="Erfahren Sie, wie und wo den Azure-Sicherungs-Agent herunterladen Installationsschritte und den Azure Backup-Agent mithilfe der Anmeldeinformationen Tresor registrieren"
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

# Herunterladen, installieren und Registrieren von Azure Backup-agent

Nach dem Erstellen des Azure-sicherungstresors, sollte ein Agent auf dem lokalen Server (Windows Server, Windows-Client oder System Center Data Protection Manager-Server) installiert werden, die backup-Daten und die Anwendung in Azure ermöglicht. Dieser Artikel behandelt die erforderlichen Schritte zum Einrichten des Azure-Sicherungs-Agents auf einem Windows-Server oder Windows-Clientcomputer.

1. Melden Sie sich bei der [Verwaltungsportal](https://manage.windowsazure.com/)

2. Klicken Sie auf **Wiederherstellungsdienste**, und wählen Sie den Sicherungstresor aus, den Sie bei einem Server registrieren möchten. Die Seite "Schnellstart" für dieses backup Depot wird angezeigt. <br/> ![Schnellstart][1]

3. Klicken Sie auf der Seite "Schnellstart" auf **für Windows Server oder System Center Data Protection Manager oder Windows-Clients** unter **Agent herunterladen** Option. Klicken Sie auf zu speichern, um ihn auf den lokalen Computer zu kopieren. <br/> ![Speichern von Agents][2]

4. Sobald der Agent installiert ist, einen Doppelklick auf MARSAgentInstaller.exe zum Starten der Installation des Azure Backup-Agents. Wählen Sie den Installationsordner und den Ablageordner für den Agenten erforderlich. Speicherort des angegebenen benötigen Speicherplatz mindestens 5 % der backup-Daten.

5.	Wenn Sie einen Proxyserver für die Verbindung mit dem Internet in die **Proxykonfiguration** Bildschirm, geben Sie die Details des Proxy-Server. Wenn Sie einen authentifizierten Proxy verwenden, geben Sie die Benutzerinformationen und das Kennwort in diesem Bildschirm.

6.	Der Azure-Sicherungs-Agent installieren Sie .NET Framework 4.5 und Windows PowerShell (falls es nicht bereits vorliegt) um die Installation abzuschließen.

7.	Sobald der Agent installiert ist, klicken Sie auf den **fahren Sie mit der Registrierung** um mit dem Workflow fortzufahren. <br/> ![Registrieren][3]

8. Klicken Sie im Bildschirm Anmeldeinformationen Tresor nach, und wählen Sie die tresoranmeldedatendatei für die zuvor heruntergeladen wurde. <br/> ![Tresoranmeldedaten][4] <br/> <br/>
> [AZURE.NOTE]Die tresoranmeldedatendatei gilt nur für 48 Stunden (nachdem sie aus dem Portal heruntergeladen wird). Wenn Sie alle Fehler in diesem Bildschirm (z. B. "tresoranmeldedatendatei abgelaufen") bereitgestellt, Anmeldenamen in der Azure-Portal auftreten und Tresor Anmeldeinformationen erneut herunterladen.
>
> Sicherstellen Sie, dass die tresoranmeldedatendatei verfügbaren an einem Ort die von der Setupanwendung zugegriffen werden kann. Wenn auftreten Zugriff auf verwandte Fehler, kopieren Sie die tresoranmeldedatendatei in ein temporäres Verzeichnis auf diesem Computer, und wiederholen Sie den Vorgang.
>
> Wenn ein ungültiger Tresor Anmeldeinformationen (z. B. "Ungültige Tresor Anmeldeinformationen". Fehler Die Datei ist entweder beschädigt oder enthält nicht die aktuellen Anmeldeinformationen zugeordnet Wiederherstellungsdienst", wiederholen den Vorgang nach dem Herunterladen einer neuen Tresor Anmeldeinformationsdatei aus dem Portal. Dieser Fehler tritt in der Regel, wenn der Benutzer auf die Option zum Herunterladen Tresor Anmeldeinformationen in Azure-Portal in schneller Folge klickt. In diesem Fall ist nur der zweite Tresor Anmeldeinformationsdatei gültig.

9. In der **Verschlüsselungseinstellung** Bildschirm können entweder eine Passphrase zu generieren oder stellen Sie eine Passphrase (mindestens 16 Zeichen) und die Passphrase an einem sicheren Ort speichern. <br/> ![Verschlüsselung][5] <br/> <br/>
> [AZURE.WARNING]Wenn das Kennwort verloren geht oder vergessen; Microsoft kann nicht helfen bei der Wiederherstellung der backup-Daten. Der Benutzer besitzt die verschlüsselungspassphrase, und Microsoft verfügt nicht über alle Einblick in die Passphrase, die vom Endbenutzer verwendet wird. Speichern Sie die Datei an einem sicheren Ort, wie es bei einem Wiederherstellungsvorgang erforderlich sein würde.

10. Nach dem Klicken auf die **Fertig stellen** Schaltfläche des Computers erfolgreich in den Tresor registriert ist und Sie können jetzt sichern auf Microsoft Azure zu starten. Sie können während des Workflows für die Registrierung auf angegebenen Einstellungen ändern den **Eigenschaften ändern** -Option in der Azure-Sicherungs-Mmc-Snap-in <br/> ![Ändern von Eigenschaften][6]

## Nächste Schritte
+ Die Installation und Konfiguration der Azure-Sicherung finden Sie unter [– häufig gestellte Fragen](backup-azure-backup-faq.md) Fragen über den Workflow.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png

<!---HONumber=GIT-SubDir-->