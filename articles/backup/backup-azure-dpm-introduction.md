<properties
	pageTitle="Einführung in Azure DPM Backup | Microsoft Azure"
	description="Eine Einführung in die Sicherung von DPM-Servern mithilfe des Azure Backup-Diensts"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="jimpark"/>

# Einführung in Azure DPM Backup

Dieser Artikel enthält eine Einführung zur Verwendung von Microsoft Azure Backup zum Schutz von DPM-Servern und -Workloads (System Center Data Protection Manager). In diesem Artikel erfahren Sie Folgendes:

- Funktionsweise der Azure DPM-Serversicherung
- Voraussetzungen für eine reibungslose Sicherung
- Typische Fehler und ihre Behandlung
- Unterstützte Szenarien

System Center DPM sichert Datei- und Anwendungsdaten. Daten, die in DPM gesichert werden, können auf Band, auf Festplatte gespeichert oder in Azure mit Microsoft Azure Backup gesichert werden. DPM interagiert mit Azure Backup wie folgt:

- **DPM als physischer Server oder auf einem lokalen virtuellen Computer bereitgestellt** – Wenn DPM als physischer Server oder als lokaler Hyper-V-Computer bereitgestellt wird, können Sie Daten im Azure Backup-Tresor zusätzlich zur Festplatten- und Bandsicherung sichern.
- **DPM als virtueller Azure-Computer bereitgestellt** – Ab System Center 2012 R2 mit Update 3 kann DPM als virtueller Azure-Computer bereitgestellt werden. Wenn DPM als virtueller Azure-Computer bereitgestellt wird, können Sie Daten auf Azure-Festplatten sichern, die an den virtuellen Azure DPM-Computer angeschlossen sind, oder Sie können die Datenspeicherung durch Sichern in einem Azure Backup-Tresor auslagern.

## Warum sollten Sie DPM-Server sichern?

Vorteile der Verwendung von Azure Backup für die Sicherung von DPM-Servern:

- Für die lokale DPM-Bereitstellung können Sie Azure Backup als Alternative zur langfristigen Bereitstellung auf Band verwenden.
- Bei DPM-Bereitstellungen in Azure ermöglicht Azure Backup das Auslagern des Speichers vom Azure-Datenträger, sodass Sie vertikal skalieren können, indem Sie ältere Daten in Azure Backup und neue Daten auf dem Datenträger speichern.

## Funktionsweise der DPM-Serversicherung
Um einen virtuellen Computer zu sichern, wird zuerst eine Zeitpunkt-Momentaufnahme der Daten benötigt. Der Azure Backup-Dienst initiiert den Sicherungsauftrag zur geplanten Zeit und löst die Sicherungserweiterung zum Erstellen einer Momentaufnahme aus. Die Sicherungserweiterung wird zum Zweck der Konsistenz mit dem VSS-Gastdienst abgestimmt und ruft die Blob-Momentaufnahme-API des Azure-Speicherdiensts auf, wenn Konsistenz erzielt wurde. Dadurch kann eine konsistente Momentaufnahme der Datenträger des virtuellen Computers erstellt werden, ohne dass dieser heruntergefahren werden muss.

Nachdem die Momentaufnahme erstellt wurde, werden die Daten vom Azure Backup-Dienst in den Sicherungstresor übertragen. Der Dienst identifiziert und überträgt nur die Datenblöcke, die sich seit der letzten Sicherung geändert haben, sodass die Sicherungen effizient gespeichert und das Netzwerk effizient genutzt werden. Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt. Dieser Wiederherstellungspunkt kann im Azure-Verwaltungsportal angezeigt werden.

>[AZURE.NOTE]Für virtuelle Linux-Computer können nur dateikonsistente Sicherungen durchgeführt werden.

## Voraussetzungen
Bereiten Sie Azure Backup wie folgt zum Sichern von DPM-Daten vor:

1. **Erstellen eines Sicherungstresors** – Erstellen Sie einen Tresors in der Azure Backup-Konsole.
2. **Herunterladen von Tresoranmeldedaten** – Laden Sie in Azure Backup das Verwaltungszertifikat hoch, das Sie für den Tresor erstellt haben.
3. **Installieren des Azure Backup-Agents und Registrieren des Servers** – Installieren Sie von Azure Backup aus, den Agent auf jedem DPM-Server, und registrieren Sie den DPM-Server im Sicherungstresor.

### Erstellen eines Sicherungstresors
Zum Sichern Ihrer virtuellen Azure-Computer müssen Sie zunächst einen Sicherungstresor erstellen. Bei diesem Tresor handelt es sich um eine Entität, in der alle Sicherungen und Wiederherstellungspunkte gespeichert werden, die im Laufe der Zeit erstellt wurden. Der Tresor enthält auch die Sicherungsrichtlinien, die auf die zu sichernden virtuellen Computer angewendet werden.

1. Melden Sie sich beim [Verwaltungsportal](http://manage.windowsazure.com/) an.
2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor** > **Schnellerfassung**. Wenn mit Ihrem Organisationskonto mehrere Abonnements verknüpft sind, wählen Sie das Abonnement aus, das mit dem Sicherungstresor verknüpft werden soll. In jedem Azure-Abonnement können Sie mehrere Sicherungstresore für die zu schützenden Daten erstellen.
3. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird. Dieser muss für jedes Abonnement eindeutig sein.
4. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Beachten Sie, dass die Region des Tresors mit der Region der zu schützenden virtuellen Computer übereinstimmen muss. Wenn Sie über virtuelle Computer in verschiedenen Regionen verfügen, müssen Sie einen Tresor in jeder dieser Regionen erstellen. Es müssen keine Speicherkonten zum Speichern der Sicherungsdaten angegeben werden. Dies erfolgt automatisch über den Sicherungstresor und den Azure Backup-Dienst.
    > [AZURE.NOTE] Die Sicherung virtueller Computer mithilfe des Azure Backup-Dienstes wird nur in bestimmten [Regionen unterstützt](http://azure.microsoft.com/regions/#services). Sehen Sie in der Liste der unterstützten Regionen nach. Falls die gesuchte Region aktuell nicht unterstützt wird, erscheint sie während der Erstellung des Tresors nicht in der Dropdownliste.
 
5. Geben Sie unter **Abonnement** das Azure-Abonnement ein, mit dem Sie den Sicherungstresor verwenden möchten.
6. Klicken Sie auf **Tresor erstellen**.

    Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Unten im Portal können Sie anhand der Benachrichtigungen den Status prüfen. ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-azure-dpm-introduction/creating-vault.png)

    In einer Meldung wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird dann auf der Seite "Recovery Services" als **Aktiv** aufgeführt.

    ![Liste der Sicherungstresore](./media/backup-azure-dpm-introduction/backup_vaultslist.png)

7. Durch Klicken auf den Sicherungstresor wird die Seite **Schnellstart** geöffnet, auf der die Anweisungen für die Sicherung von DPM-Servern angezeigt werden. ![Anweisungen zur Sicherung von virtuellen Computern auf der Dashboard-Seite](./media/backup-azure-dpm-introduction/vmbackup-instructions.png)

    > [AZURE.NOTE]Vergewissern Sie sich nach der Erstellung des Tresors, dass die entsprechende Speicherredundanzoption ausgewählt ist. Weitere Informationen finden Sie unter [Festlegen der Speicherredundanzoption im Sicherungstresor](http://azure.microsoft.com/documentation/articles/backup-azure-backup-create-vault/#azure-backup---storage-redundancy-options).

### Herunterladen der Tresoranmeldedaten
1. Klicken Sie auf **Recovery Services** und dann auf den Sicherungstresor. Klicken Sie auf der Seite **Schnellstart** auf **Tresoranmeldedaten herunterladen**, um die Anmeldedatendatei herunterzuladen und an einem sicheren Ort zu speichern. Sie können die Anmeldedaten nicht bearbeiten, daher müssen Sie den Pfad nicht öffnen. Aus Gründen der Sicherheit läuft der Schlüssel in der Datei nach 48 Stunden ab.

2. Kopieren Sie die Datei an einen sicheren Speicherort, auf den leicht durch die DPM-Server zugegriffen werden kann, die Sie im Azure Backup-Tresor registrieren möchten. Sie müssen die Datei auszuwählen, wenn Sie den Azure Backup-Agent installieren.

### Installieren des Azure Backup-Agents und Registrieren des Servers
Sie laden die Agent-Installationsdatei herunter und führen sie auf jedem DPM-Server aus, der zu sichernde Daten enthält. Agents werden im **Azure Download Center** gespeichert und haben einen eigenen Installationsvorgang. Beim Ausführen von Setup wird der Agent installiert und der DPM-Server beim Tresor registriert. Beachten Sie Folgendes:

- Sie benötigen Administratorberechtigungen auf dem DPM-Server, um den Agent zu installieren.
- Zum Installieren auf mehreren DPM-Servern können Sie die Installationsdatei auf einer freigegebenen Netzwerkressource platzieren oder Gruppenrichtlinien- bzw. Verwaltungsprodukte wie System Center Configuration Manager für die Agentinstallation verwenden.
- Sie müssen den DPM-Server nach der Installation nicht neu starten.

#### So installieren Sie den Sicherungsagent und registrieren den Server

1. Wählen Sie auf der Seite **Schnellstart** des Azure Backup-Tresors unter **Azure Backup-Agent herunterladen** die Option **Für Windows Server oder System Center Data Protection Manager oder Windows-Client** aus. Laden Sie die Anwendung auf den DPM-Server herunter, auf dem sie ausgeführt werden soll.
2. Führen Sie die Setupdatei **MARSAgentInstaller.exe** aus. Akzeptieren Sie die Nutzungsbedingungen, und wählen Sie die Installation fehlender, erforderlicher Software aus.
3. Wählen Sie auf der Seite **Installationseinstellungen** den **Installationsordner** und den **Cachespeicherort** aus.

    Der Standard-Cacheordner ist <system drive>:\Program Files\Azure Backup Agent. Am Cachespeicherort erstellt der Installationsvorgangs einen Ordner namens **Scratch** im Ordner **Azure Backup-Agent**. Der Cachespeicherort muss mindestens über 2,5 Gigabyte (GB) (oder 10 % der Größe der Daten, die in Azure gesichert werden) freien Speicherplatz verfügen. Nur lokale Systemadministratoren und Mitglieder der Gruppe "Administratoren" haben Zugriff auf das Cacheverzeichnis, um Denial-of-Service-Angriffe zu verhindern.

4. Legen Sie auf der Seite **Proxykonfiguration** benutzerdefinierte Proxyeinstellungen für den Agent für die Verbindung mit Windows Azure fest. Wenn Sie keine Einstellungen konfigurieren, werden die Standardeinstellungen für den Internetzugriff auf dem DPM-Server verwendet. Beachten Sie, dass Sie, wenn Sie einen Proxyserver verwenden, der eine Authentifizierung erfordert, die Details auf dieser Seite eingeben sollten.
5. Auf der Seite **Microsoft-Updates abonnieren** sollten Sie Updates aktivieren. Wenn der Server bereits für automatische Updates aktiviert ist, wird dieser Schritt übersprungen. Beachten Sie, dass die Microsoft Update-Einstellungen für alle Microsoft-Produktupdates gelten und nicht exklusiv für den Azure Backup-Agent gelten.
6. Die Seite **Installation** wird angezeigt. Die Installation überprüft, ob die erforderliche Software installiert ist, und schließt das Setup ab. Danach erhalten Sie eine Nachricht, dass der Azure Backup-Agent erfolgreich installiert wurde. Jetzt können Sie nach Updates suchen. Es wird empfohlen, die Suche nach Updates zuzulassen.
7. Klicken Sie auf **Mit Registrierung fortfahren**, um den Server beim Tresor zu registrieren.
8. Wählen Sie auf der Seite **Tresor-ID** die Tresorregistrierungsdatei aus, die Sie im Azure Backup-Tresor generiert haben.
9. Geben Sie auf der Seite **Verschlüsselungseinstellung** Passphrasedetails an, oder generieren Sie eine Passphrase automatisch.
10. Klicken Sie auf "Passphrase generieren" gefolgt von "In Zwischenablage kopieren". Sie erhalten eine Meldung, dass Ihre Passphrase in die Zwischenablage kopiert wurde. Sie sollten Editor öffnen und die Passphrase aus der Zwischenablage einfügen. Speichern Sie die Datei, drucken Sie sie, und bewahren Sie sie an einem sicheren Ort auf. Klicken Sie auf "Registrieren", um den DPM-Server im Sicherungstresor zu registrieren.

    > [AZURE.TIP] Vergessen Sie bei dem Schritt „Verschlüsselungseinstellung“ nicht, die Passphrase in die Zwischenablage zu kopieren.
11. Klicken Sie auf **Registrieren**.

    Nachdem die Registrierung abgeschlossen ist, zeigt die DPM-Konsole die Verfügbarkeit von Azure Backup.

    Azure Backup verschlüsselt immer Daten an der Quelle mit der Passphrase (alphanumerische Zeichenfolge), die Sie angeben oder automatisch generieren.
    >[AZURE.NOTE]Azure Backup verwaltet nie die Passphrase, und bei einem Verlust können die Daten nicht wiederhergestellt werden. Es wird dringend empfohlen, den Schlüssel an einen externen Speicherort aufzubewahren.

Wenn Sie eine Passphrase angeben und auf **Fertig stellen** klicken, dauert es einige Sekunden, bis der Agent den Produktionsserver beim Sicherungstresor registriert hat. Sobald die Registrierung beim Tresor abgeschlossen ist, wird eine Zusammenfassung auf der Seite **Serverregistrierung** angezeigt.

## Anforderungen (und Einschränkungen)

- DPM kann als physischer Server oder als virtueller Hyper-V-Computer, der auf System Center 2012 SP1 oder System Center 2012 R2 installiert ist, ausgeführt werden. DPM kann auch als virtueller Azure-Computer unter System Center 2012 R2 mit mindestens DPM 2012 R2 Update Rollup 3 oder auf einem virtuellen Windows-Computer in VMWare in System Center 2012 R2 mit mindestens Update Rollup 5 ausgeführt werden.
- Wenn Sie DPM mit System Center 2012 SP1 ausführen, sollten Sie Update Rollup 2 für System Center Data Protection Manager SP1 installieren. Dies ist erforderlich, bevor Sie den Azure Backup-Agent installieren können.
- Auf dem DPM-Server muss Windows PowerShell und .Net Framework 4.5 installiert sein.
- DPM kann die meisten Workloads in Azure Backup sichern. Eine vollständige Liste der Unterstützungen finden Sie in den Azure Backup-Elementen weiter unten.
- Mit der Option "Kopieren auf Band" können Daten, die in Azure Backup gespeichert sind, nicht wiederhergestellt werden.
- Sie benötigen ein Azure-Konto mit aktivierter Azure Backup-Funktion. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Erfahren Sie mehr über [Preisgestaltung von Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
- Für das Verwenden von Azure Backup muss der Azure Backup-Agent auf den Servern installiert werden, die Sie sichern möchten. Jeder Server muss über mindestens 2,5 GB lokalen freien Speicherplatz für den Cachespeicherort verfügen, obwohl 15 GB freier lokaler Speicherplatz für den Cachespeicherort empfohlen wird.
- Daten werden im Azure-Tresorspeicher gespeichert. Es gibt keine Beschränkung der Datenmenge, die Sie in einem Azure Backup-Tresor sichern, aber die Größe einer Datenquelle (z. B. ein virtueller Computer oder eine Datenbank) sollte 1,65 TB nicht überschreiten.

Diese Dateitypen werden für die Sicherung in Azure unterstützt:

- Verschlüsselt (nur vollständige Sicherungen)
- Komprimiert (inkrementelle Sicherungen werden unterstützt)
- Platzsparend (inkrementelle Sicherungen werden unterstützt)
- Komprimiert und platzsparend (als platzsparend behandelt)

Diese werden nicht unterstützt:

- Server auf Dateisystemen, die nach Groß-/Kleinschreibung unterscheiden, werden nicht unterstützt.
- Feste Links (übersprungen)
- Analysepunkte (übersprungen)
- Verschlüsselt und komprimiert (übersprungen)
- Verschlüsselt und platzsparend (übersprungen)
- Komprimierter Stream
- Platzsparender Stream

>[AZURE.NOTE]Ab System Center 2012 DPM mit SP1 können Sie Workloads, die von DPM geschützt werden, in Azure mithilfe von Microsoft Azure Backup sichern.

<!---HONumber=62-->