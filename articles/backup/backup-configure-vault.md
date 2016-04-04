<properties
	pageTitle="Sichern eines Windows-Servers oder -Clients in Azure | Microsoft Azure"
	description="Sichern Sie Windows-Server oder -Clients in Azure, indem Sie einen Sicherungstresor erstellen, Anmeldeinformationen herunterladen, den Backup-Agent installieren und eine erste Sicherung Ihrer Dateien und Ordner durchführen."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="backup vault; back up a Windows Server; backup windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/19/2016"
	ms.author="jimpark; trinadhk; markgal"/>


# Sichern eines Windows-Servers oder -Clients in Azure
In diesem Artikel werden die Verfahren beschrieben, die Sie zur Vorbereitung Ihrer Umgebung und zum Sichern eines Windows-Servers (oder -Clients) in Azure ausführen müssen. Darüber hinaus erfahren Sie, welche Punkte Sie bei der Bereitstellung Ihrer Sicherungslösung beachten müssen. Wenn Sie Azure Backup das erste Mal einsetzen, finden Sie in [diesem Artikel](backup-configure-vault.md) Informationen für den Schnelleinstieg.

![Tresor erstellen](./media/backup-configure-vault/initial-backup-process.png)

## Vorbereitung
Um Windows-Server (oder -Clients) in Azure zu sichern, benötigen Sie ein Azure-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

## Schritt 1: Erstellen eines Sicherungstresors
Um Dateien und Ordner von einem Windows-Server oder -Client in Azure zu sichern, müssen Sie einen Sicherungstresor in der geografischen Region erstellen, in der die Daten gespeichert werden sollen.

### So erstellen Sie einen Sicherungstresor

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an.

2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor**, und wählen Sie **Schnellerfassung** aus.

3. Geben Sie für den Parameter **Name** einen benutzerfreundlichen Namen zur Identifizierung des Sicherungstresors ein. Geben Sie einen Namen ein, der zwischen 2 und 50 Zeichen enthält. Er muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Dieser muss für jedes Abonnement eindeutig sein.

    Wählen Sie für den Parameter **Region** die geografische Region für den Sicherungstresor aus. Die Auswahl bestimmt die geografische Region, an die Ihre Sicherungsdaten gesendet werden. Wenn Sie eine geografische Region in der Nähe Ihres eigenen Standorts auswählen, können Sie dadurch die Netzwerklatenz beim Sichern in Azure reduzieren.

    Klicken Sie auf **Tresor erstellen**.

    ![Tresor erstellen](./media/backup-configure-vault/demo-vault-name.png)

    Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen.

    Nach dem erfolgreichen Erstellen des Sicherungstresors wird dies in einer entsprechenden Meldung angezeigt. Der Tresor wird zudem in den Ressourcen für Recovery Services als **Aktiv** aufgeführt.

    ![Status zum Erstellen eines Tresors](./media/backup-configure-vault/recovery-services-select-vault.png)

4. Wählen Sie die **Speicherredundanzoption** aus.

    >[AZURE.IMPORTANT] Der beste Zeitpunkt zum Identifizieren von Speicherredundanzoptionen ist unmittelbar nach der Erstellung des Tresors, bevor Computer beim Tresor registriert werden. Sobald ein Element beim Tresor registriert wurde, wird die Speicherredundanzoption gesperrt und kann nicht mehr geändert werden.

    Wenn Sie Azure als primären Speicherendpunkt für die Sicherung verwenden (wenn Sie beispielsweise von einem Windows-Server in Azure sichern), sollten Sie die Option [Georedundanter Speicher](../storage/storage-redundancy.md#geo-redundant-storage) in Betracht ziehen (die Standardeinstellung).

    Wenn Sie Azure als tertiären Speicherendpunkt verwenden (wenn Sie beispielsweise SCDPM zum Erstellen einer lokalen Sicherungskopie und Azure für die langfristige Aufbewahrung benutzen), sollten Sie ggf. einen [lokal redundanten Speicher](../storage/storage-redundancy.md#locally-redundant-storage) auswählen. Dadurch werden die Kosten zum Speichern von Daten in Azure gesenkt, und es wird eine geringere Dauerhaftigkeit für Ihre Daten bereitgestellt, die möglicherweise für tertiäre Kopien ausreicht.

    **So wählen Sie die Speicherredundanzoption aus**

    a. Klicken Sie auf den Tresor, den Sie gerade erstellt haben.

    b. Klicken Sie auf der Seite **Schnellstart** auf **Konfigurieren**.

    ![Status zum Konfigurieren eines Tresors](./media/backup-configure-vault/configure-vault.png)

    c. Wählen Sie die entsprechende Speicherredundanzoption aus.

    Sie müssen auf **Speichern** klicken, wenn Sie **Lokal redundant** auswählen, da die Standardeinstellung **Georedundant** ist.

    d. Klicken Sie im linken Navigationsbereich auf **Recovery Services**, um zur Liste mit den Ressourcen für **Recovery Services** zurückzukehren.

## Schritt 2: Herunterladen der Datei mit Tresoranmeldeinformationen
Der lokale Server (Windows-Server oder Windows-Client) muss bei einem Sicherungstresor authentifiziert werden, bevor Daten in Azure gesichert werden können. Die Authentifizierung erfolgt mithilfe von *Tresoranmeldeinformationen*. Die Datei mit Tresoranmeldeinformationen wird über einen sicheren Kanal aus dem Azure-Portal heruntergeladen. Dem Azure Backup-Dienst ist der private Schlüssel des Zertifikats, der nicht im Portal oder Dienst gespeichert ist, unbekannt.

Erfahren Sie mehr über die [Verwendung von Tresoranmeldeinformationen zum Authentifizieren mit dem Azure Backup-Dienst](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

### So laden Sie die Datei mit den Tresoranmeldeinformationen auf einen lokalen Computer herunter

1. Klicken Sie im linken Navigationsbereich auf **Recovery Services**, und wählen Sie den Sicherungstresor aus, den Sie erstellt haben.

    ![Abgeschlossen](./media/backup-configure-vault/rs-left-nav.png)

2.  Klicken Sie auf der Seite **Schnellstart** auf **Tresoranmeldedaten herunterladen**.

    Das Portal generiert Tresoranmeldeinformationen mit einer Kombination aus dem Tresornamen und dem aktuellen Datum. Die Datei mit den Tresoranmeldeinformationen wird nur während des Registrierungsworkflows verwendet und läuft nach 48 Stunden ab.

    Sie können die Datei mit den Tresoranmeldeinformationen aus dem Portal herunterladen.

3. Klicken Sie auf **Speichern**, um die Tresoranmeldeinformationen in den Download-Ordner des lokalen Kontos herunterzuladen. Sie können auch im Menü **Speichern** die Option **Speichern unter** auswählen, um einen anderen Speicherort anzugeben.

    >[AZURE.NOTE] Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Ort gespeichert wird, der von Ihrem Computer aus zugänglich ist. Wenn sie in einer Dateifreigabe/einem SMB gespeichert sind, überprüfen Sie die Zugriffsberechtigungen.

## Schritt 3: Herunterladen, Installieren und Registrieren des Azure Backup-Agents
Nachdem Sie den Azure Backup-Tresor erstellt und die Tresoranmeldeinformationen heruntergeladen haben, muss ein Agent auf jedem Ihrer Windows-Computer (Windows-Server oder Windows-Client) installiert werden.

### So laden Sie den Agent herunter und installieren und registrieren den Agent

1. Klicken Sie auf **Wiederherstellungsdienste**, und wählen Sie den Sicherungstresor aus, den Sie bei einem Server registrieren möchten.

2. Klicken Sie auf der Seite „Schnellstart“ auf **Agent für Windows Server oder System Center Data Protection Manager oder Windows-Client > Speichern**.

    ![Agent speichern](./media/backup-configure-vault/agent.png)

3. Klicken Sie nach Abschluss des Downloads von *MARSAgentInstaller.exe* auf **Ausführen** (oder doppelklicken Sie am Speicherort auf **MARSAgentInstaller.exe**).

4. 	Wählen Sie den *Installationsordner* und den *Cacheordner* aus, die für den Agent erforderlich sind, und klicken Sie auf **Weiter**.

    Der von Ihnen angegebene Cachespeicherort muss freien Speicherplatz in einer Größenordnung enthalten, die mindestens 5 % der Sicherungsdaten entspricht.

5. Sie können die Internetverbindung weiterhin mit den standardmäßigen Proxyeinstellungen herstellen. Wenn Sie hierfür einen Proxyserver verwenden, können Sie auf dem Bildschirm **Proxykonfiguration** das Kontrollkästchen **Benutzerdefinierte Proxyeinstellungen verwenden** aktivieren und die Details für den Proxyserver eingeben.

    Wenn Sie einen authentifizierten Proxy verwenden, geben Sie in diesem Bildschirm die Informationen zum Benutzernamen und zum Kennwort ein.

    Klicken Sie auf **Weiter**.

6. Klicken Sie auf **Installieren**, um mit der Installation des Agents zu beginnen.

    Der Azure Backup-Agent installiert .NET Framework 4.5 und Windows PowerShell (falls noch nicht geschehen), um die Installation abzuschließen.

7. Klicken Sie nach der Installation des Agents auf **Mit Registrierung fortfahren**, um den Workflow fortzusetzen.

8. Navigieren Sie auf dem Bildschirm **Tresor-ID** zu der *Datei mit den Tresoranmeldeinformationen*, die Sie heruntergeladen haben, und wählen Sie sie aus.

    Die Datei mit den Tresoranmeldeinformationen ist nur 48 Stunden lang gültig (nachdem sie aus dem Portal heruntergeladen wurde). Wenn in diesem Bildschirm Fehler auftreten (z. B. „Datei mit Tresoranmeldeinformationen abgelaufen“), melden Sie sich beim Azure-Portal an, und laden Sie die Datei mit den Tresoranmeldeinformationen erneut herunter.

    Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Speicherort verfügbar ist, der für die Setupanwendung zugänglich ist. Wenn Zugriffsfehler auftreten, kopieren Sie die Datei mit den Tresoranmeldeinformationen in einen temporären Speicherort auf diesem Computer, und wiederholen Sie den Vorgang.

    Wenn ein Fehler wegen ungültiger Tresoranmeldeinformationen angezeigt wird (z. B. „Ungültige Tresoranmeldeinformationen angegeben“), ist die Datei entweder beschädigt oder enthält nicht die aktuellen Anmeldeinformationen, die dem Wiederherstellungsdienst zugeordnet sind. Wiederholen Sie den Vorgang, nachdem Sie eine neue Datei mit Tresoranmeldeinformationen vom Portal heruntergeladen haben. Dieser Fehler tritt in der Regel auf, wenn der Benutzer in schneller Folge auf die Option *Tresoranmeldedaten herunterladen* klickt. In diesem Fall ist nur die zuletzt heruntergeladene Datei mit Tresoranmeldeinformationen gültig.

9. Auf dem Bildschirm **Verschlüsselungseinstellung** können Sie entweder eine Passphrase *generieren* oder eine Passphrase *angeben* (mindestens 16 Zeichen). Vergessen Sie nicht, die Passphrase an einem sicheren Speicherort zu speichern.

    Klicken Sie auf **Fertig stellen**.

    > [AZURE.WARNING] Wenn die Passphrase verloren geht oder vergessen wird, kann Microsoft Ihnen bei der Wiederherstellung der Sicherungsdaten nicht behilflich sein. Der Endbenutzer besitzt die Verschlüsselungspassphrase, und Microsoft kann nicht auf die vom Endbenutzer verwendete Passphrase zugreifen. Speichern Sie die Datei an einem sicheren Speicherort, da sie bei einem Wiederherstellungsvorgang benötigt wird.

    Mit dem **Assistenten zum Registrieren von Servern** wird der Server für Microsoft Azure Backup registriert.

10. Lassen Sie nach dem Festlegen des **Verschlüsselungsschlüssels** das Kontrollkästchen **Microsoft Azure Recovery Services-Agent starten** aktiviert, und klicken Sie auf **Schließen**.

## Schritt 4: Abschließen der ersten Sicherung

Die erste Sicherung umfasst zwei wichtige Aufgaben: **Erstellen des Sicherungszeitplans** und **Erstmaliges Sichern von Dateien und Ordnern**. Nachdem Sie die erste Sicherung basierend auf dem von Ihnen definierten Zeitplan abgeschlossen haben, erstellt die Sicherungsrichtlinie Sicherungspunkte. Mithilfe dieser Sicherungspunkte können Sie die Daten bei Bedarf wiederherstellen.

### Planen der Sicherung

1. Öffnen Sie den **Microsoft Azure Backup-Agent** (dieser wird automatisch geöffnet, wenn Sie beim Schließen des **Assistenten für die Server-Registrierung** das Kontrollkästchen **Microsoft Azure Recovery Services-Agent starten** aktiviert gelassen haben. Alternativ können Sie auf Ihrem Computer nach *Microsoft Azure Backup* suchen).

    ![Planen eines Windows Server-Backups](./media/backup-configure-vault/snap-in-search.png)

2. Klicken Sie im **Microsoft Azure Backup-Agent** auf **Sicherung planen**.

    ![Planen eines Windows Server-Backups](./media/backup-configure-vault/schedule-backup-close.png)

3. Klicken Sie auf dem Bildschirm **Assistent zum Planen der Sicherung – Erste Schritte** auf **Weiter**.

4. Klicken Sie auf dem Bildschirm **Elemente für Sicherung auswählen** auf **Elemente hinzufügen**.

5. Wählen Sie die Dateien und Ordner aus, die Sie sichern möchten, und klicken Sie auf **OK**.

6. Klicken Sie auf **Weiter**.

7. Geben Sie auf dem Bildschirm **Sicherungszeitplan angeben** den **Sicherungszeitplan** an, und klicken Sie auf **Weiter**.

    Sie können tägliche (maximal drei pro Tag) oder wöchentliche Sicherungen planen.

    ![Elemente eines Windows Server-Backups](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Die Festlegung des Sicherungszeitplans wird in diesem [Artikel](backup-azure-backup-cloud-as-tape.md) ausführlich beschrieben.

8. Wählen Sie auf dem Bildschirm **Aufbewahrungsrichtlinie auswählen** die **Aufbewahrungsrichtlinie** für die Sicherungskopie aus.

    Die Aufbewahrungsrichtlinie gibt an, wie lange die Sicherung gespeichert werden soll. Statt lediglich eine einfache Richtlinie für alle Sicherungspunkte anzugeben, können Sie je nach Sicherungszeitpunkt verschiedene Aufbewahrungsrichtlinien festlegen. Sie können die tägliche, wöchentliche, monatliche und jährliche Aufbewahrungsrichtlinie an Ihre Anforderungen anpassen.

     Klicken Sie auf **Weiter**.

9. Wählen Sie auf dem Bildschirm **Erstsicherungstyp wählen** den Typ für die erste Sicherung**.

    Sie können die Sicherung automatisch über das Netzwerk oder offline durchführen. In den verbleibenden Abschnitten dieses Artikels wird das Verfahren für eine automatische Sicherung beschrieben. Wenn Sie es vorziehen, eine Offlinesicherung durchzuführen, finden Sie unter [Workflow zur Offlinesicherung in Azure Backup](backup-azure-backup-import-export.md) weitere Informationen.

    Lassen Sie die Option **Automatisch über das Netzwerk** aktiviert, und klicken Sie auf **Weiter**.

10. Lesen Sie die Informationen auf dem Bildschirm **Bestätigung** durch, und klicken Sie auf **Fertig stellen**.

11. Nachdem der Assistent die Erstellung des **Sicherungszeitplans** abgeschlossen hat, klicken Sie auf **Schließen**.

### Aktivieren der Netzwerkdrosselung (optional)

Der Azure Backup-Agent ermöglicht eine Netzwerkdrosselung. Über die Drosselung wird die Verwendung der Netzwerkbandbreite bei der Datenübertragung gesteuert. Diese Steuerungsmöglichkeit kann hilfreich sein, wenn Sie Daten während der Geschäftszeiten sichern möchten, der Sicherungsprozess aber keine Auswirkung auf den weiteren Internetdatenverkehr haben soll. Die Drosselung der Datenübertragung gilt für Sicherungs- und Wiederherstellungsaktivitäten.

1. Klicken Sie im **Backup-Agent** auf **Eigenschaften ändern**.

    ![Eigenschaften ändern](./media/backup-configure-vault/change-properties.png)

2. Aktivieren Sie auf der Registerkarte **Drosselung** das Kontrollkästchen **Internet-Bandbreiteneinschränkung für Sicherungsvorgänge aktivieren**.

    ![Netzwerkdrosselung](./media/backup-configure-vault/throttling-dialog.png)

3. Nachdem Sie die Drosselung aktiviert haben, geben Sie die zulässige Bandbreite für die Sicherungsdatenübertragung für **Arbeitsstunden** und **Arbeitsfreie Stunden** an.

    Die Bandbreitenwerte beginnen bei 512 Kilobytes pro Sekunde (KBit/s) und können mit bis zu 1023 Megabytes pro Sekunde (MBit/s) angegeben werden. Sie können außerdem den Start und das Ende für die **Arbeitsstunden** angeben und festlegen, welche Tage der Woche als Arbeitstage betrachtet werden. Die Zeiten außerhalb der angegebenen Arbeitsstunden werden als arbeitsfreie Stunden betrachtet.

4. Klicken Sie auf **OK**.

### Jetzt sichern

1. Klicken Sie im **Backup-Agent** auf **Jetzt sichern**, um das anfängliche Seeding über das Netzwerk abzuschließen.

    ![Windows Server jetzt sichern](./media/backup-configure-vault/backup-now.png)

2. Überprüfen Sie auf dem Bildschirm **Bestätigung** die Einstellungen, die vom Assistenten zum Sichern des Computers verwendet werden, und klicken Sie auf **Sichern**.

3. Klicken Sie auf **Schließen**, um den Assistenten zu schließen. Sie können den Assistenten auch vor Abschluss des **Sicherungsprozesses** schließen, da er im Hintergrund weiter ausgeführt wird.

Wenn die erste Sicherung abgeschlossen ist, wird der Status des Auftrags in der **Azure Backup-Konsole** als *Auftrag abgeschlossen* angezeigt.

![Abgeschlossen](./media/backup-configure-vault/ircomplete.png)

## Nächste Schritte
- Registrieren Sie sich für ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/).

Zusätzliche Informationen zum Sichern von virtuellen Computern oder anderen Workloads finden Sie hier:

- [Sichern von IaaS-VMs](backup-azure-vms-prepare.md)
- [Sichern von Workloads in Azure mit Microsoft Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Sichern von Workloads in Azure mit DPM](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0323_2016-->