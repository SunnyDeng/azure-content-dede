<properties
   pageTitle="Sichern von Dateien und Ordnern aus Windows in Azure | Microsoft Azure"
   description="Schützen Sie Ihre Windows Server-Daten, indem Sie einen Tresor erstellen, den Backup-Agent installieren und Dateien und Ordner in Azure sichern."
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
	 ms.date="03/09/2016"
	 ms.author="jimpark;"/>

# Einführung: Sichern von Dateien und Ordnern von einem Windows-Server oder -Client in Azure

Sie können Ihren Windows-Computer (Windows-Client oder Windows-Server) in wenigen Schritten unter Azure sichern.

Sie müssen nur folgende Schritte ausführen:

![Schritt 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Einrichtung eines Azure-Abonnements (falls erforderlich)<br> ![Schritt 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Erstellung eines Sicherungstresors und Download der erforderlichen Komponenten<br> ![Schritt 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Vorbereitung Ihres Windows-Servers oder -Clients per Installation und Registrierung dieser Komponenten<br> ![Schritt 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Schützen Ihrer Daten


![Prozess zum Sichern von Windows-Servern und -Clients](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Schritt 1: Erhalten eines Azure-Abonnements

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, mit dem Sie auf alle Azure-Dienste zugreifen können.

>[AZURE.NOTE] Sie können diesen Schritt überspringen, wenn Sie bereits über ein Azure-Abonnement verfügen.

## Schritt 2: Erstellung eines Sicherungstresors und Download der erforderlichen Komponenten

Sie müssen einen Sicherungstresor in Ihrer geografischen Region erstellen, in dem die Daten gespeichert werden sollen, um Dateien und Daten von einem Windows-Computer in Azure zu sichern.

1. Melden Sie sich mit Ihrem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com/) an, sofern Sie noch nicht angemeldet sind.

2. Klicken Sie auf **Neu > Hybridintegration > Sicherung**.

    ![Tresor erstellen](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Geben Sie auf dem angezeigten Bildschirm unter **Name** einen Anzeigenamen ein, um den Sicherungstresor zu identifizieren. Geben Sie einen Namen ein, der zwischen 2 und 50 Zeichen enthält. Er muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Bindestriche enthalten.

    b. Wählen Sie unter **Region** die geografische Region für den Sicherungstresor aus. Wenn Sie eine geografische Region in der Nähe Ihres eigenen Standorts auswählen, können Sie dadurch die Netzwerklatenz beim Sichern in Azure reduzieren.

    c. Klicken Sie auf **Tresor erstellen**.

    ![Tresor erstellen](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Sie können die Benachrichtigungen unten im Portal überwachen, um den Status im Blick zu behalten.

    ![Erstellen eines Tresors](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Nachdem der Sicherungstresor erstellt wurde, wird er in den Ressourcen für Recovery Services als **Aktiv** aufgeführt.

    ![Status zum Erstellen eines Tresors](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Wählen Sie **Speicherredundanzoptionen** aus.

    Der beste Zeitpunkt zum Auswählen der Speicherredundanzoption ist direkt nach dem Erstellen eines Tresors und vor dem Registrieren von Computern für den Tresor. Sobald ein Element beim Tresor registriert wurde, wird die Speicherredundanzoption gesperrt und kann nicht mehr geändert werden.

    Wenn Sie Azure als primären Speicherendpunkt für die Sicherung verwenden (wenn Sie beispielsweise Ihre Daten von einem Windows-Server in Azure sichern), sollten Sie ggf. die Option für [georedundanten Speicher](../storage/storage-redundancy.md#geo-redundant-storage) (Standardeinstellung) verwenden.

    Wenn Sie Azure als tertiären Speicherendpunkt verwenden (wenn Sie beispielsweise SCDPM zum Erstellen einer lokalen Sicherungskopie und Azure für die langfristige Aufbewahrung benutzen), sollten Sie ggf. einen [lokal redundanten Speicher](../storage/storage-redundancy.md#locally-redundant-storage) auswählen. Dadurch werden die Kosten zum Speichern von Daten in Azure gesenkt, und es wird eine geringere Dauerhaftigkeit für Ihre Daten bereitgestellt, die möglicherweise für tertiäre Kopien ausreicht.

    a. Klicken Sie auf den Tresor, den Sie gerade erstellt haben.

    b. Klicken Sie auf der Seite „Schnellstart“ auf **Konfigurieren**.

    ![Status zum Konfigurieren eines Tresors](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Wählen Sie die entsprechende Speicherredundanzoption aus.

    Sie müssen auf **Speichern** klicken, wenn Sie **Lokal redundant** auswählen, da die Standardeinstellung **Georedundant** ist.

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d. Klicken Sie im linken Navigationsbereich auf **Recovery Services**, um zur Liste mit den Ressourcen für **Recovery Services** zurückzukehren.

    ![Sicherungstresor auswählen](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Ihr Windows-Computer muss jetzt mit dem eben erstellten Sicherungstresor authentifiziert werden. Die Authentifizierung erfolgt mithilfe von Tresoranmeldeinformationen.

4.  Klicken Sie auf den Tresor, den Sie gerade erstellt haben.

    ![Status zum Erstellen eines Tresors](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Klicken Sie auf der Seite **Schnellstart** auf **Tresoranmeldeinformationen herunterladen**.

    ![Herunterladen](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    Das Portal generiert Tresoranmeldeinformationen mit einer Kombination aus dem Tresornamen und dem aktuellen Datum.

    >[AZURE.NOTE] Die Datei mit den Tresoranmeldeinformationen wird nur während des Registrierungsworkflows verwendet und läuft nach 48 Stunden ab.

6. Klicken Sie auf **Speichern**, um die Tresoranmeldeinformationen in den Ordner **Downloads** des lokalen Kontos herunterzuladen. Sie können auch im Menü **Speichern** die Option **Speichern unter** auswählen, um einen anderen Speicherort anzugeben.

    ![Sicherungstresor auswählen](./media/backup-try-azure-backup-in-10-mins/save.png)

    Stellen Sie sicher, dass die Tresoranmeldeinformationen an einem Ort gespeichert werden, der von Ihrem Computer aus zugänglich ist. Überprüfen Sie die Zugriffsberechtigungen, wenn die Datei in einer Dateifreigabe/einem SMB gespeichert ist.

    >[AZURE.NOTE] Sie müssen die Tresoranmeldeinformationen jetzt nicht öffnen.

    Als Nächstes müssen Sie den Backup-Agent herunterladen.

7. Klicken Sie im linken Navigationsbereich auf **Recovery Services** und dann auf den Sicherungstresor, den Sie bei einem Server registrieren möchten.

    ![Sicherungstresor auswählen](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Klicken Sie auf der Seite „Schnellstart“ auf **Agent für Windows Server oder System Center Data Protection Manager oder Windows Client > Speichern** (oder wählen Sie im Menü **Speichern** die Option **Speichern unter** aus, um einen Speicherort für den Agent anzugeben).

    ![Agent speichern](./media/backup-try-azure-backup-in-10-mins/agent.png)

An diesem Punkt haben Sie die Erstellung eines Sicherungstresors abgeschlossen und die erforderlichen Komponenten heruntergeladen. Nun installieren Sie den Backup-Agent.

## Schritt 3: Vorbereitung Ihres Windows-Servers oder -Clients per Installation und Registrierung der erforderlichen Komponenten

1. Doppelklicken Sie nach Abschluss des Downloads am Speicherort auf die Datei **MARSagentinstaller.exe** (Sie können auch auf **Ausführen** klicken, anstatt die Datei im vorherigen Schritt zu speichern).

2. Wählen Sie den erforderlichen **Installationsordner** und **Cacheordner** für den Agent aus.

    Der von Ihnen angegebene Cachespeicherort muss freien Speicherplatz in einer Größenordnung enthalten, die mindestens 5 % der Sicherungsdaten entspricht.

    Klicken Sie auf **Weiter**.

    ![Neuerstellung und Zwischenspeicherung](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. Sie können die Internetverbindung weiterhin mit den standardmäßigen Proxyeinstellungen herstellen. Wenn Sie hierfür einen Proxyserver verwenden, können Sie auf dem Bildschirm **Proxykonfiguration** das Kontrollkästchen **Benutzerdefinierte Proxyeinstellungen verwenden** aktivieren und die Details für den Proxyserver eingeben.

    Wenn Sie einen authentifizierten Proxy verwenden, geben Sie in diesem Bildschirm die Informationen zum Benutzernamen und zum Kennwort ein.

    Klicken Sie auf **Weiter**.

    ![Proxykonfiguration](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Klicken Sie auf **Installieren**.

    ![Proxykonfiguration](./media/backup-try-azure-backup-in-10-mins/agent-installation.png)

    Der Azure Backup-Agent installiert .NET Framework 4.5 und Windows PowerShell (falls noch nicht geschehen), um die Installation abzuschließen.

5. Klicken Sie nach der Installation des Agents auf **Mit Registrierung fortfahren**, um den Workflow fortzusetzen.

    ![Registrieren](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Navigieren Sie auf dem Bildschirm **Tresor-ID** zu der **Datei mit den Tresoranmeldeinformationen**, die Sie heruntergeladen haben, und wählen Sie sie aus. Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Speicherort verfügbar ist, der für die Setupanwendung zugänglich ist.

    Klicken Sie auf **Weiter**.

    ![Tresoranmeldeinformationen](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Im Bildschirm **Verschlüsselungseinstellung** können Sie entweder eine Passphrase generieren oder eine Passphrase angeben (mindestens 16 Zeichen). Vergessen Sie nicht, die Passphrase an einem sicheren Speicherort zu speichern.

    > [AZURE.WARNING] Wenn die Passphrase verloren geht oder vergessen wird, kann Microsoft Ihnen bei der Wiederherstellung der Sicherungsdaten nicht behilflich sein. Der Endbenutzer besitzt die Verschlüsselungspassphrase, und Microsoft kann nicht auf die vom Endbenutzer verwendete Passphrase zugreifen. Speichern Sie die Datei an einem sicheren Speicherort, da sie bei einem Wiederherstellungsvorgang benötigt wird.

    Klicken Sie auf **Fertig stellen**.

    ![Verschlüsselung](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    Mit dem **Assistenten zum Registrieren von Servern** wird der Server für Microsoft Azure Backup registriert.

    ![Verschlüsselung](./media/backup-try-azure-backup-in-10-mins/registering-server.png)

8. Lassen Sie nach dem Festlegen des **Verschlüsselungsschlüssels** das Kontrollkästchen **Microsoft Azure Recovery Services-Agent starten** aktiviert, und klicken Sie auf **Schließen**.

    ![Verschlüsselung](./media/backup-try-azure-backup-in-10-mins/close-server-registration.png)

    Der Computer wurde jetzt erfolgreich im Tresor registriert, und Sie können Ihre Sicherungsoptionen konfigurieren und planen.

## Schritt 4: Schützen Ihrer Daten

1. Klicken Sie im **Backup-Agent** (wird automatisch geöffnet, wenn Sie das Kontrollkästchen **Microsoft Azure Recovery Services-Agent starten** aktiviert gelassen haben) auf **Sicherung planen**.

    ![Planen eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Klicken Sie auf dem Bildschirm **Erste Schritte** auf **Weiter**.

    ![Planen eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/getting-started.png)

3. Klicken Sie auf dem Bildschirm **Elemente für Sicherung auswählen** auf **Elemente hinzufügen**.

    ![Planen eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Wählen Sie die Elemente aus, die Sie sichern möchten, und klicken Sie auf **OK**. Mit Azure Backup auf einem Windows-Server oder Windows-Client können Sie Dateien und Ordner schützen.

    ![Elemente eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/added-items.png)

    Klicken Sie auf **Weiter**.

    ![Elemente eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/selected-items.png)

4. Geben Sie den **Sicherungszeitplan** an, und klicken Sie auf **Weiter**.

    Sie können tägliche (maximal drei pro Tag) oder wöchentliche Sicherungen planen.

    ![Elemente eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Wählen Sie die **Aufbewahrungsrichtlinie** für die Sicherungskopie aus. Sie können die tägliche, wöchentliche, monatliche und jährliche Aufbewahrungsrichtlinie an Ihre Bedürfnisse anpassen.

    >[AZURE.NOTE] Der Sicherungszeitplan wird in diesem [Artikel](backup-azure-backup-cloud-as-tape.md) ausführlich beschrieben.

     Klicken Sie auf **Weiter**.

    ![Elemente eines Windows Server-Backups](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Wählen Sie den anfänglichen Sicherungstyp aus.

    Sie können die Sicherung automatisch über das Netzwerk oder offline durchführen. Im restlichen Teil dieses Artikels halten wir uns an den automatischen Sicherungsprozess. Wenn Sie es vorziehen, eine Offlinesicherung durchzuführen, finden Sie unter [Workflow zur Offlinesicherung in Azure Backup](backup-azure-backup-import-export.md) weitere Informationen.

    Klicken Sie auf **Weiter**.

    ![Erstes Windows Server-Backup](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Lesen Sie die Informationen auf dem Bildschirm **Bestätigung** durch, und klicken Sie auf **Fertig stellen**.

    ![Erstes Windows Server-Backup](./media/backup-try-azure-backup-in-10-mins/confirmation.png)

8. Nachdem der Assistent die Erstellung des **Sicherungszeitplans** abgeschlossen hat, klicken Sie auf **Schließen**.

    ![Erstes Windows Server-Backup](./media/backup-try-azure-backup-in-10-mins/backup-schedule-created.png)

9. Klicken Sie im **Backup-Agent** auf **Jetzt sichern**, um das anfängliche Seeding über das Netzwerk abzuschließen.

    ![Windows Server jetzt sichern](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Überprüfen Sie auf dem Bildschirm **Bestätigung** die Einstellungen, die vom Assistenten zum Sichern des Computers verwendet werden, und klicken Sie auf **Sichern**.

    ![Windows Server jetzt sichern](./media/backup-try-azure-backup-in-10-mins/backup-now-confirmation.png)

11. Klicken Sie auf **Schließen**, um den Assistenten zu schließen. Sie können den Assistenten auch vor Abschluss des **Sicherungsprozesses** schließen, da er im Hintergrund weiter ausgeführt wird.

    ![Windows Server jetzt sichern](./media/backup-try-azure-backup-in-10-mins/backup-progress.png)

12. Wenn die erste Sicherung abgeschlossen ist, wird der Status des Auftrags in der Azure Backup-Konsole in der Ansicht **Aufträge** als „Abgeschlossen“ angezeigt.

    ![Abgeschlossen](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Glückwunsch! Sie haben Ihre Dateien und Ordner erfolgreich mit Azure Backup gesichert.

## Nächste Schritte
- Weitere Informationen zu Azure Backup finden Sie unter [Azure Backup – Übersicht](backup-introduction-to-azure-backup.md).
- Lesen Sie die weiteren Informationen zum [Vorbereiten Ihrer Umgebung für die Sicherung von Windows-Computern](backup-configure-vault.md).
- Lesen Sie die weiteren Informationen zum [Sichern eines Windows-Servers](backup-azure-backup-windows-server.md).
- Besuchen Sie das [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0316_2016-->