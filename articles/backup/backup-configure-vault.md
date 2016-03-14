<properties
	pageTitle="Vorbereiten Ihrer Umgebung für das Sichern eines Windows-Servers oder Clientcomputers | Microsoft Azure"
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
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Vorbereiten der Umgebung für das Sichern von Windows-Computern in Azure
In diesem Artikel sind die Schritte aufgeführt, die Sie zur Vorbereitung Ihrer Umgebung zum Sichern einer Windows-Maschine in Azure ausführen müssen.

| Schritt | Name | Details |
| :------: | ---- | ------- |
| 1 | [Erstellen eines Tresors](#create-a-backup-vault) | Erstellen eines Tresors im [Azure Backup-Verwaltungsportal](http://manage.windowsazure.com) |
| 2 | [Herunterladen der Tresoranmeldeinformationen](#download-the-vault-credential-file) | Herunterladen der Tresoranmeldeinformationen, die zum Registrieren des Windows-Computers beim Sicherungstresor verwendet werden |
| 3 | [Installieren des Azure Backup-Agents](#download-install-and-register-the-azure-backup-agent) | Installieren des Agents und Registrieren des Servers mit den Tresoranmeldeinformationen beim Sicherungstresor |

Falls Sie alle obigen allgemeinen Schritte bereits ausgeführt haben, können Sie mit dem [Sichern Ihrer Windows-Computer](backup-azure-backup-windows-server.md) beginnen. Führen Sie andernfalls die unten angegebenen ausführlichen Schritte weiter aus, um sicherzustellen, dass Ihre Umgebung richtig vorbereitet ist.

## Vorbereitung
Sie benötigen ein Azure-Konto, um Ihre Umgebung für das Sichern von Windows-Computern vorzubereiten. Falls Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

## Erstellen eines Sicherungstresors
Zum Sichern von Dateien und Daten von einem Windows-Computer oder Data Protection Manager (DPM) in Azure oder beim Sichern von IaaS-VMs in Azure müssen Sie einen Sicherungstresor in der geografischen Region erstellen, in der die Daten gespeichert werden sollen.

**So erstellen Sie einen Sicherungstresor**

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an.

2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor**, und wählen Sie **Schnellerfassung** aus.

    ![Tresor erstellen](./media/backup-configure-vault/createvault1.png)

3. Geben Sie für den Parameter **Name** einen benutzerfreundlichen Namen zur Identifizierung des Sicherungstresors ein. Dieser muss für jedes Abonnement eindeutig sein.

    Wählen Sie für den Parameter **Region** die geografische Region für den Sicherungstresor aus. Die Auswahl bestimmt die geografische Region, an die Ihre Sicherungsdaten gesendet werden. Wenn Sie eine geografische Region in der Nähe Ihres eigenen Standorts auswählen, können Sie dadurch die Netzwerklatenz beim Sichern in Azure reduzieren.

    Klicken Sie auf **Tresor erstellen**, um den Workflow abzuschließen.

    Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen.

    ![Erstellen eines Tresors](./media/backup-configure-vault/creatingvault1.png)

    Nach dem erfolgreichen Erstellen des Sicherungstresors wird dies in einer entsprechenden Meldung angezeigt. Der Tresor wird zudem in den Ressourcen für Recovery Services als **Aktiv** aufgeführt.

    ![Status zum Erstellen eines Tresors](./media/backup-configure-vault/backupvaultstatus1.png)

    >[AZURE.IMPORTANT] Der beste Zeitpunkt zum Identifizieren von Speicherredundanzoptionen ist unmittelbar nach der Erstellung des Tresors, bevor Computer beim Tresor registriert werden. Sobald ein Element beim Tresor registriert wurde, wird die Speicherredundanzoption gesperrt und kann nicht mehr geändert werden.

4. Wählen Sie **Speicherredundanzoptionen** aus.

    Wenn Sie Azure als primären Speicherendpunkt für die Sicherung verwenden (wenn Sie beispielsweise von einem Windows Server in Azure sichern), sollten Sie die Option für [georedundanten Speicher](../storage/storage-redundancy.md#geo-redundant-storage) in Betracht ziehen (Standardeinstellung).

    Bei Verwendung von Azure als tertiärem Speicherendpunkt (Sie verwenden beispielsweise SCDPM, um eine lokale Sicherungskopie vor Ort zu haben, und Azure für Ihre langfristigen Aufbewahrungsanforderungen) sollten Sie einen [lokal redundanten Speicher](../storage/storage-redundancy.md#locally-redundant-storage) auswählen. Dadurch werden die Kosten zum Speichern von Daten in Azure gesenkt, und es wird eine geringere Dauerhaftigkeit für Ihre Daten bereitgestellt, die möglicherweise für tertiäre Kopien ausreicht.

    Erfahren Sie mehr über die Optionen für [georedundanten](../storage/storage-redundancy.md#geo-redundant-storage) und [lokal redundanten](../storage/storage-redundancy.md#locally-redundant-storage) Speicher in dieser [Übersicht](../storage/storage-redundancy.md).

    a. Klicken Sie auf den Tresor, den Sie gerade erstellt haben.

    b. Klicken Sie auf der Seite „Schnellstart“ auf **Konfigurieren**.

    ![Status zum Konfigurieren eines Tresors](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Wählen Sie die entsprechende Speicherredundanzoption aus.

    Sie müssen auf **Speichern** klicken, wenn Sie **Lokal redundant** auswählen, da die Standardeinstellung **Georedundant** lautet.

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d. Klicken Sie im linken Navigationsbereich auf **Recovery Services**, um zur Liste mit den Ressourcen für **Recovery Services** zurückzukehren.

    ![Sicherungstresor auswählen](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

## Herunterladen der Datei mit Tresoranmeldeinformationen
Der lokale Server (Windows-Client oder Windows Server oder Data Protection Manager-Server) muss bei einem Sicherungstresor authentifiziert werden, bevor Daten in Azure gesichert werden können. Die Authentifizierung erfolgt mithilfe von "Tresoranmeldeinformationen". Die Datei mit den Tresoranmeldeinformationen wird über einen sicheren Kanal aus dem Azure-Portal heruntergeladen, und der Azure Backup-Dienst verfügt nicht über den privaten Schlüssel des Zertifikats. Er wird im Portal oder Dienst nicht beibehalten.

Erfahren Sie mehr über die [Verwendung von Tresoranmeldeinformationen zum Authentifizieren mit dem Azure Backup-Dienst](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

**Gehen Sie wie folgt vor, um die Datei mit den Tresoranmeldeinformationen auf einen lokalen Computer herunterzuladen:**

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an.

2. Klicken Sie im linken Navigationsbereich auf **Recovery Services**, und wählen Sie den Sicherungstresor aus, den Sie erstellt haben.

3.  Klicken Sie auf das Cloudsymbol, um die Ansicht *Schnellstart* des Sicherungstresors aufzurufen.

    ![Schnellansicht](./media/backup-configure-vault/quickview.png)

4. Klicken Sie auf der Seite **Schnellstart** auf **Tresoranmeldedaten herunterladen**.

    ![Herunterladen](./media/backup-configure-vault/downloadvc.png)

    Das Portal generiert Tresoranmeldeinformationen mit einer Kombination aus dem Tresornamen und dem aktuellen Datum. Die Datei mit den Tresoranmeldeinformationen wird nur während des Registrierungsworkflows verwendet und läuft nach 48 Stunden ab.

    Sie können die Datei mit den Tresoranmeldeinformationen aus dem Portal herunterladen.

5. Klicken Sie auf **Speichern**, um die Tresoranmeldeinformationen in den Download-Ordner des lokalen Kontos herunterzuladen. Sie können auch im Menü **Speichern** die Option *Speichern unter* auswählen, um einen anderen Speicherort anzugeben.

    Sie müssen die Tresoranmeldeinformationen jetzt nicht öffnen.

    Stellen Sie sicher, dass die Tresoranmeldeinformationen an einem Ort gespeichert werden, der von Ihrem Computer aus zugänglich ist. Wenn sie in einer Dateifreigabe/einem SMB gespeichert sind, überprüfen Sie die Zugriffsberechtigungen.

## Herunterladen, Installieren und Registrieren des Azure Backup-Agents
Nach dem Erstellen des Azure Backup-Tresors sollte auf jedem Windows-Computer (Windows Server, Windows-Client oder System Center Data Protection Manager-Server oder Azure Backup Server-Computer) ein Agent installiert werden, der das Sichern von Daten und Anwendungen in Azure ermöglicht.

**Gehen Sie wie folgt vor, um den Agent herunterzuladen, zu installieren und zu registrieren:**

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an.

2. Klicken Sie auf **Wiederherstellungsdienste**, und wählen Sie den Sicherungstresor aus, den Sie bei einem Server registrieren möchten.

3. Klicken Sie auf der Seite „Schnellstart“ auf **Agent für Windows Server oder System Center Data Protection Manager oder Windows Client > Speichern**.

    ![Agent speichern](./media/backup-configure-vault/agent.png)

4. Klicken Sie nach Abschluss des Downloads von *MARSAgentInstaller.exe* auf **Ausführen** (oder doppelklicken Sie am Speicherort auf **MARSAgentInstaller.exe**). Wählen Sie den *Installationsordner* und den *Cacheordner* aus, die für den Agent erforderlich sind, und klicken Sie auf **Weiter**.

    Der von Ihnen angegebene Cachespeicherort muss freien Speicherplatz in einer Größenordnung enthalten, die mindestens 5 % der Sicherungsdaten entspricht.

    ![Neuerstellung und Zwischenspeicherung](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. Wenn Sie einen Proxyserver für die Verbindung mit dem Internet verwenden, geben Sie im Bildschirm **Proxykonfiguration** die Details des Proxyservers ein. Wenn Sie einen authentifizierten Proxy verwenden, geben Sie auf diesem Bildschirm die Informationen zum Benutzernamen und Kennwort ein und klicken auf **Weiter**.

    Der Azure Backup-Agent installiert .NET Framework 4.5 und Windows PowerShell (falls noch nicht geschehen), um die Installation abzuschließen.

6. Wenn der Agent installiert ist, klicken Sie auf **Mit Registrierung fortfahren**, um den Workflow fortzusetzen.

    ![Registrieren](./media/backup-configure-vault/register.png)

7. Navigieren Sie auf dem Bildschirm **Tresor-ID** zu der *Datei mit den Tresoranmeldeinformationen*, die Sie heruntergeladen haben, und wählen Sie sie aus.

    ![Tresoranmeldeinformationen](./media/backup-configure-vault/vc.png)

    Die Datei mit den Tresoranmeldeinformationen ist nur 48 Stunden lang gültig (nachdem sie aus dem Portal heruntergeladen wurde). Gehen Sie wie folgt vor, wenn Sie auf diesem Bildschirm einen Fehler erkennen (z. B. „Die angegebene Vault-Anmeldedatei ist abgelaufen.“): Melden Sie sich beim Azure-Portal an, und laden Sie die Datei mit den Tresoranmeldeinformationen erneut herunter.

    Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Speicherort verfügbar ist, der für die Setupanwendung zugänglich ist. Wenn Zugriffsfehler auftreten, kopieren Sie die Datei mit den Tresoranmeldeinformationen in einen temporären Speicherort auf diesem Computer, und wiederholen Sie den Vorgang.

    Bei Anzeige eines Fehlers aufgrund von ungültigen Tresoranmeldeinformationen (z. B. „Ungültiger Pfad für Anmeldedatei.“): Die Datei ist entweder beschädigt oder verfügt nicht über die neuesten Anmeldedaten für den Wiederherstellungsdienst. Wiederholen Sie den Vorgang, nachdem Sie eine neue Datei mit Tresoranmeldeinformationen vom Portal heruntergeladen haben. Dieser Fehler tritt in der Regel auf, wenn der Benutzer in schneller Folge auf die Option *Tresoranmeldedaten herunterladen* klickt. In diesem Fall ist nur die zuletzt heruntergeladene Datei mit Tresoranmeldeinformationen gültig.

8. Auf dem Bildschirm **Verschlüsselungseinstellung** können Sie eine Passphrase *generieren* oder *angeben* (mindestens 16 Zeichen). Vergessen Sie nicht, die Passphrase an einem sicheren Speicherort zu speichern.

    ![Verschlüsselung](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] Wenn die Passphrase verloren geht oder vergessen wird, kann Microsoft Ihnen bei der Wiederherstellung der Sicherungsdaten nicht behilflich sein. Der Endbenutzer besitzt die Verschlüsselungspassphrase, und Microsoft kann nicht auf die vom Endbenutzer verwendete Passphrase zugreifen. Speichern Sie die Datei an einem sicheren Speicherort, da sie bei einem Wiederherstellungsvorgang benötigt wird.

9. Klicken Sie auf **Fertig stellen**.

    Der Computer wurde nun erfolgreich beim Tresor registriert, und Sie können mit der Sicherung unter Microsoft Azure beginnen.

## Nächste Schritte
- Für ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) registrieren
- [Sichern Sie einen Windows-Server oder Clientcomputer](backup-azure-backup-windows-server.md).
- Falls noch einige Fragen unbeantwortet sein sollten, hilft Ihnen das Thema [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md) weiter.
- Besuchen Sie das [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0302_2016-->