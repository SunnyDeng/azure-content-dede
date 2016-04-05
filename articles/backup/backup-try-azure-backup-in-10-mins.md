<properties
   pageTitle="Sichern von Dateien und Ordnern aus Windows in Azure | Microsoft Azure"
   description="Erfahren Sie, wie Sie Ihre Windows Server-Daten sichern, indem Sie einen Tresor erstellen, den Backup-Agent installieren und Dateien und Ordner in Azure sichern."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="Sichern; Sicherung"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="hero-article"
	 ms.date="03/28/2016"
	 ms.author="jimpark;"/>

# Einführung: Sichern von Dateien und Ordnern von einem Windows-Server oder -Client in Azure

In diesem Artikel wird beschrieben, wie Sie Windows Server-Dateien und -Ordner (oder Windows-Client) per Azure Backup in Azure sichern. Wenn Sie erfahren möchten, wie einfach die Verwendung von Azure Backup ist, sind Sie hier richtig.

Zum Sichern Ihrer Dateien und Ordner sind lediglich die folgenden Schritte erforderlich:

![Schritt 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Einrichtung eines Azure-Abonnements (falls erforderlich)<br> ![Schritt 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Erstellung eines Sicherungstresors und Download der erforderlichen Komponenten<br> ![Schritt 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Vorbereitung Ihres Windows-Servers oder -Clients per Installation und Registrierung dieser Komponenten<br> ![Schritt 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Sicherung der Daten


![Sichern Ihres Windows-Computers mit Azure Backup](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Schritt 1: Erhalten eines Azure-Abonnements

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, mit dem Sie auf alle Azure-Dienste zugreifen können.

>[AZURE.NOTE] Sie können diesen Schritt überspringen, wenn Sie bereits über ein Azure-Abonnement verfügen.

## Schritt 2: Erstellung eines Sicherungstresors und Download der erforderlichen Komponenten

Um Ihre Dateien und Ordner zu sichern, müssen Sie einen Sicherungstresor in der geografischen Region erstellen, in der die Daten gespeichert werden sollen.

1. Melden Sie sich mit Ihrem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com/) an, sofern Sie noch nicht angemeldet sind.

2. Klicken Sie auf **Neu > Hybridintegration > Sicherung**.

    ![Beginnen mit der Vorbereitung zum Sichern Ihrer Dateien und Ordner](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Geben Sie auf dem angezeigten Bildschirm unter **Name** einen Anzeigenamen ein, um den Sicherungstresor zu identifizieren. Geben Sie einen Namen ein, der zwischen 2 und 50 Zeichen enthält. Er muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Bindestriche enthalten.

    b. Wählen Sie unter **Region** die geografische Region für den Sicherungstresor aus. Wenn Sie eine geografische Region in der Nähe Ihres eigenen Standorts auswählen, können Sie dadurch die Netzwerklatenz beim Sichern in Azure reduzieren.

    c. Klicken Sie auf **Tresor erstellen**.

    ![Erstellen eines Tresors](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Sie können die Benachrichtigungen unten im Portal überwachen, um den Status im Blick zu behalten.

    ![Tresorprozess](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Nachdem der Sicherungstresor erstellt wurde, wird er in den Ressourcen für Recovery Services als **Aktiv** aufgeführt.

    ![Tresorstatus „Aktiv“](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Wählen Sie **Speicherredundanzoptionen** aus.

    Der beste Zeitpunkt zum Auswählen der Speicherredundanzoption ist direkt nach dem Erstellen eines Tresors und vor dem Registrieren von Computern für den Tresor. Sobald ein Element beim Tresor registriert wurde, wird die Speicherredundanzoption gesperrt und kann nicht mehr geändert werden.

    Wenn Sie Azure als primären Speicherendpunkt für die Sicherung verwenden, kann es ratsam sein, die Option [Georedundanter Speicher](../storage/storage-redundancy.md#geo-redundant-storage) (Standardeinstellung) zu wählen.

    Wenn Azure als tertiärer Speicherendpunkt für die Sicherung verwendet wird, empfiehlt sich die Auswahl von [Lokal redundanter Speicher](../storage/storage-redundancy.md#locally-redundant-storage). Mit dieser Option werden die Kosten zum Speichern von Daten in Azure gesenkt, und es wird eine geringere Dauerhaftigkeit bereitgestellt, die für tertiäre Kopien ggf. ausreicht.

    a. Klicken Sie auf den Tresor, den Sie gerade erstellt haben.

    b. Klicken Sie auf der Seite „Schnellstart“ auf **Konfigurieren**.

    ![Konfigurieren des Tresors](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Wählen Sie die entsprechende Speicherredundanzoption aus.

    ![Auswählen der Speicherredundanzoption](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    Klicken Sie bei Auswahl von **Lokal redundant** auf **Speichern**, da **Georedundant** die Standardoption ist.

    d. Klicken Sie im linken Navigationsbereich auf **Recovery Services**, um zur Liste mit den Ressourcen für **Recovery Services** zurückzukehren.

    ![Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Sie müssen Ihren Windows-Computer jetzt bei dem eben erstellten Sicherungstresor authentifizieren. Die Authentifizierung erfolgt mithilfe von Tresoranmeldeinformationen.

4.  Klicken Sie auf den Tresor, den Sie gerade erstellt haben.

    ![Auswählen des neuen Tresors](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Klicken Sie auf der Seite **Schnellstart** auf **Tresoranmeldeinformationen herunterladen**.

    ![Herunterladen der Tresoranmeldeinformationen](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    Das Portal generiert Tresoranmeldeinformationen mit einer Kombination aus dem Tresornamen und dem aktuellen Datum.

    >[AZURE.NOTE] Die Datei mit den Tresoranmeldeinformationen wird nur während des Registrierungsworkflows verwendet und läuft nach 48 Stunden ab.

6. Klicken Sie auf **Speichern**, um die Tresoranmeldeinformationen in den lokalen Ordner **Downloads** herunterzuladen. Sie können auch im Menü **Speichern** die Option **Speichern unter** wählen, um einen Speicherort für die Tresoranmeldeinformationen anzugeben.

    Stellen Sie sicher, dass die Tresoranmeldeinformationen an einem Ort gespeichert werden, der von Ihrem Computer aus zugänglich ist. Überprüfen Sie die Zugriffsberechtigungen, wenn die Datei in einer Dateifreigabe/einem SMB gespeichert ist.

    >[AZURE.NOTE] Sie müssen die Tresoranmeldeinformationen jetzt noch nicht öffnen.

    Als Nächstes müssen Sie den Backup-Agent herunterladen.

7. Klicken Sie im linken Navigationsbereich auf **Recovery Services** und dann auf den Sicherungstresor, den Sie bei einem Server registrieren möchten.

    ![Auswählen des Sicherungstresors](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Klicken Sie auf der Seite „Schnellstart“ auf **Agent für Windows Server oder System Center Data Protection Manager oder Windows-Client > Speichern**.

    ![Speichern des Backup-Agents](./media/backup-try-azure-backup-in-10-mins/agent.png)

An diesem Punkt haben Sie die Erstellung eines Sicherungstresors abgeschlossen und die erforderlichen Komponenten heruntergeladen. Nun installieren Sie den Backup-Agent.

## Schritt 3: Vorbereitung Ihres Windows-Servers oder -Clients per Installation und Registrierung der erforderlichen Sicherungskomponenten

1. Doppelklicken Sie nach Abschluss des Downloads am Speicherort auf die Datei **MARSagentinstaller.exe** (Sie können auch auf **Ausführen** klicken, anstatt die Datei im vorherigen Schritt zu speichern).

2. Wählen Sie den erforderlichen **Installationsordner** und **Cacheordner** für den Agent aus.

    Der von Ihnen angegebene Cachespeicherort muss freien Speicherplatz in einer Größenordnung enthalten, die mindestens 5 % der Sicherungsdaten entspricht.

    Klicken Sie auf **Weiter**.

    ![Installations- und Cacheordner](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. (Optional) Wenn Sie zum Herstellen der Internetverbindung einen Proxyserver verwenden, müssen Sie auf dem Bildschirm **Proxykonfiguration** das Kontrollkästchen **Benutzerdefinierte Proxyeinstellungen verwenden** aktivieren und die Details für den Proxyserver eingeben.

    Wenn Sie einen authentifizierten Proxy verwenden, geben Sie in diesem Bildschirm die Informationen zum Benutzernamen und zum Kennwort ein.

    Klicken Sie auf **Weiter**.

    ![Proxykonfiguration](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Klicken Sie auf **Installieren**.

    Der Azure Backup-Agent installiert .NET Framework 4.5 und Windows PowerShell (falls noch nicht geschehen), um die Installation abzuschließen.

5. Klicken Sie nach der Installation des Agents auf **Mit Registrierung fortfahren**, um den Workflow fortzusetzen.

    ![Registrieren](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Navigieren Sie auf dem Bildschirm **Tresor-ID** zu der **Datei mit den Tresoranmeldeinformationen**, die Sie heruntergeladen haben, und wählen Sie sie aus. Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Speicherort verfügbar ist, der für die Setupanwendung zugänglich ist.

    Klicken Sie auf **Weiter**.

    ![Identifizieren des Tresors](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Im Bildschirm **Verschlüsselungseinstellung** können Sie entweder eine Passphrase generieren oder eine Passphrase angeben (mindestens 16 Zeichen). Vergessen Sie nicht, die Passphrase an einem sicheren Speicherort zu speichern.

    > [AZURE.WARNING] Wenn Sie die Passphrase verlieren oder vergessen, kann Microsoft Ihnen bei der Wiederherstellung der Sicherungsdaten nicht behilflich sein. Sie sind im Besitz der Verschlüsselungspassphrase, und Microsoft kann nicht auf die vom Endbenutzer verwendete Passphrase zugreifen. Speichern Sie die Datei an einem sicheren Speicherort, da sie bei einem Wiederherstellungsvorgang benötigt wird.

    Klicken Sie auf **Fertig stellen**.

    ![Verschlüsselungseinstellung](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    Mit dem **Assistenten zum Registrieren von Servern** wird der Server für Microsoft Azure Backup registriert.

8. Lassen Sie nach dem Festlegen des **Verschlüsselungsschlüssels** das Kontrollkästchen **Microsoft Azure Recovery Services-Agent starten** aktiviert, und klicken Sie auf **Schließen**.

    Der Computer wurde jetzt erfolgreich im Tresor registriert, und Sie können Ihre Sicherungsoptionen konfigurieren und planen.

## Schritt 4: Sicherung der Daten

1. Klicken Sie im **Backup-Agent** (wird automatisch geöffnet, wenn Sie das Kontrollkästchen **Microsoft Azure Recovery Services-Agent starten** aktiviert gelassen haben) auf **Sicherung planen**.

    ![Planen eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Klicken Sie auf dem Bildschirm **Erste Schritte** auf **Weiter**.

3. Klicken Sie auf dem Bildschirm **Elemente für Sicherung auswählen** auf **Elemente hinzufügen**. Mit Azure Backup auf einem Windows-Server oder Windows-Client können Sie Dateien und Ordner schützen.

    ![Planen eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Wählen Sie die Elemente aus, die Sie sichern möchten, und klicken Sie auf **OK**.

    Klicken Sie auf **Weiter**.

4. Geben Sie den **Sicherungszeitplan** an, und klicken Sie auf **Weiter**.

    Sie können tägliche (maximal drei pro Tag) oder wöchentliche Sicherungen planen.

    ![Angeben des Sicherungszeitplans](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Wählen Sie die **Aufbewahrungsrichtlinie** für die Sicherungskopie aus. Sie können die tägliche, wöchentliche, monatliche und jährliche Aufbewahrungsrichtlinie an Ihre Bedürfnisse anpassen.

    >[AZURE.NOTE] Der Sicherungszeitplan wird in diesem [Artikel](backup-azure-backup-cloud-as-tape.md) ausführlich beschrieben.

     Klicken Sie auf **Weiter**.

    ![Auswählen der Aufbewahrungsrichtlinie](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Wählen Sie den anfänglichen Sicherungstyp aus.

    Sie können die Sicherung automatisch über das Netzwerk oder offline durchführen. Im restlichen Teil dieses Artikels halten wir uns an den automatischen Sicherungsprozess. Falls Sie es vorziehen, eine Offlinesicherung durchzuführen, finden Sie unter [Workflow zur Offlinesicherung in Azure Backup](backup-azure-backup-import-export.md) weitere Informationen.

    Klicken Sie auf **Weiter**.

    ![Typ der anfänglichen Sicherung](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Lesen Sie sich die Informationen auf dem Bildschirm **Bestätigung** durch, und klicken Sie auf **Fertig stellen**.

8. Nachdem der Assistent die Erstellung des **Sicherungszeitplans** abgeschlossen hat, klicken Sie auf **Schließen**.

9. Klicken Sie im **Backup-Agent** auf **Jetzt sichern**, um das anfängliche Seeding über das Netzwerk abzuschließen.

    ![Windows Server jetzt sichern](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Überprüfen Sie auf dem Bildschirm **Bestätigung** die Einstellungen, die vom Assistenten zum Sichern des Computers verwendet werden, und klicken Sie auf **Sichern**.

11. Klicken Sie auf **Schließen**, um den Assistenten zu schließen. Sie können den Assistenten schließen, bevor der Sicherungsvorgang abgeschlossen ist. Er wird dann im Hintergrund weiter ausgeführt.

12. Wenn die erste Sicherung abgeschlossen ist, wird der Status des Auftrags in der Azure Backup-Konsole in der Ansicht **Aufträge** als „Abgeschlossen“ angezeigt.

    ![Anfängliche Sicherung abgeschlossen](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Glückwunsch! Sie haben Ihre Dateien und Ordner erfolgreich mit Azure Backup gesichert.

## Nächste Schritte
- Weitere Informationen zur Azure-Sicherung finden Sie unter [Übersicht über die Microsoft Azure-Sicherung](backup-introduction-to-azure-backup.md).
- Lesen Sie die weiteren Informationen zum [Vorbereiten Ihrer Umgebung und Sichern von Windows-Computern](backup-configure-vault.md).
- Erfahren Sie, wie Sie Azure-VMs sichern, indem Sie sich diesen [Einführungsartikel](backup-azure-vms-first-look.md) durchlesen.
- Lesen Sie die neuesten Diskussionsbeiträge zu Azure Backup im [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0330_2016-->