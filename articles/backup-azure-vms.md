<properties
	pageTitle="Sichern eines virtuellen Azure-Computers - Backup"
	description="Informationen zum Sichern eines virtuellen Azure-Computers nach der Registrierung"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/26/2015"
	ms.author="aashishr"/>


# Sichern von Azure Virtual machines

Dieser Artikel ist der Hauptleitfaden zur Sicherung virtueller Azure-Computer. Bevor Sie fortfahren, stellen Sie sicher, dass alle [Voraussetzungen](backup-azure-vms-introduction.md#prerequisites) erfüllt werden.

Zur Sicherung virtueller Azure-Computer gehören drei Hauptschritte:

![Drei Schritte zum Sichern eines virtuellen Azure-Computers](./media/backup-azure-vms/3-steps-for-backup.png)

## Virtuelle Computer in Azure zu ermitteln.
Ermittlung Prozess Abfragen Azure für die Liste der virtuellen Computer im Abonnement, zusammen mit weiteren Informationen wie den Namen des Cloud-Dienst und die Region.

> [AZURE.NOTE]Der Ermittlungsvorgang sollte immer als erster Schritt ausgeführt werden. Dadurch wird sichergestellt, dass alle neuen virtuellen Computer, die dem Abonnement hinzugefügt wurden, identifiziert werden.

Gehen Sie zum Auslösen des Ermittlungsvorgangs folgendermaßen vor:

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Art des Workloads **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche **Auswählen**. ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie auf die **DISCOVER** am unteren Rand der Seite. ![Schaltfläche "Ermitteln"](./media/backup-azure-vms/discover-button.png)

4. Der Ermittlungsvorgang kann einige Minuten andauern, während die virtuellen Computer in einer Tabelle aufgeführt werden. Am unteren Bildschirmrand wird eine Popupbenachrichtigung angezeigt, während der Ermittlungsvorgang ausgeführt wird. ![VMs ermitteln](./media/backup-azure-vms/discovering-vms.png)

5. Sobald der Ermittlungsvorgang abgeschlossen ist, wird eine Popupbenachrichtigung angezeigt. ![Ermitteln abgeschlossen](./media/backup-azure-vms/discovery-complete.png)


## Registrieren von virtuellen Computern in Azure
Bevor Sie ein virtuellen Computer geschützt werden können, muss bei Azure Backup-Dienst registriert werden. Der Registrierungsvorgang hat zwei Hauptziele:

1. Aktivieren der Sicherungserweiterung im VM-Agent auf dem virtuellen Computer

2. Die virtuelle Maschine mit der Azure-Sicherungsdienst zuordnen

Die Registrierung ist in der Regel eine einmalige Angelegenheit. Der Azure Backup-Dienst verarbeitet nahtlos das Upgrade und Patching der Sicherungserweiterung, ohne dass ein Benutzereingriff erforderlich wäre. Dies erspart dem Benutzer den Agent-Verwaltungsaufwand, der in der Regel mit Backup-Produkten verbunden ist.

### Zum Registrieren von virtuellen Maschinen

1. Navigieren Sie in den sicherungstresor, das sich unter **Recovery Services** in der Azure-Portal, und klicken Sie auf die **Elemente registriert** Registerkarte

2. Wählen Sie die Art der Arbeitslast in dem Dropdown-Menü als **Azure Virtual Machine** und klicken Sie auf die Schaltfläche auswählen. ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie auf die **registrieren** am unteren Rand der Seite. ![Schaltfläche "Registrieren"](./media/backup-azure-vms/register-button.png)

4. Wählen Sie im Popupfenster **Elemente registrieren** die virtuellen Computer aus, die Sie registrieren möchten. Treten verwenden zwei oder mehr virtuelle Computer mit dem gleichen Namen den Cloud-Dienst zur Unterscheidung zwischen den virtuellen Computern.

    Der Vorgang **Registrieren** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig für die Registrierung ausgewählt werden können. Dadurch wird der einmalige Aufwand zur Vorbereitung des virtuellen Computers für die Sicherung erheblich reduziert.

    >[AZURE.NOTE]Die virtuellen Computer, die nicht registriert sind und in der gleichen Region wie den sicherungstresor wird angezeigt.

5. Für jeden virtuellen Computer, der registriert werden soll, wird ein Auftrag erstellt. Die Popupbenachrichtigung zeigt den Status dieser Aktivität an. Klicken Sie auf **Auftrag anzeigen**, um zur Seite **Aufträge** zu wechseln. ![Registrierungsauftrag](./media/backup-azure-vms/register-create-job.png)

6. Der virtuelle Computer wird auch in der Liste der registrierten Elemente aufgeführt, und der Status des Registrierungsvorgangs wird angezeigt. ![Registrierungsstatus 1](./media/backup-azure-vms/register-status01.png)

7. Sobald der Vorgang abgeschlossen ist, ändert sich der Status im Portal dem registrierten Status entsprechend. ![Registrierungsstatus 2](./media/backup-azure-vms/register-status02.png)

## Sichern von Azure Virtual machines
Dieser Schritt umfasst das Einrichten einer Sicherungs- und Beibehaltungsrichtlinie für den virtuellen Computer. Um einen virtuellen Computer zu schützen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.
2. Wählen Sie im Dropdownmenü als Art des Workloads **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche **Auswählen**. ![Workload im Portal auswählen](./media/backup-azure-vms/select-workload.png)

3. Klicken Sie auf die **schützen** am unteren Rand der Seite.

4. Hierdurch wird ein Assistent **Elemente schützen** geöffnet, in dem die zu schützenden virtuellen Computer ausgewählt werden können. Treten verwenden zwei oder mehr virtuelle Computer mit dem gleichen Namen den Cloud-Dienst zur Unterscheidung zwischen den virtuellen Computern.

    Der Vorgang **Schützen** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig für die Registrierung ausgewählt werden können. Dadurch wird der Aufwand zum Schützen des virtuellen Computers erheblich reduziert.

    >[AZURE.NOTE]Hier werden nur die virtuellen Computer angezeigt, die ordnungsgemäß beim Azure Backup-Dienst registriert sind und sich in derselben Region befinden wie der Sicherungstresor.

5. Im zweiten Bildschirm des Assistenten **Elemente schützen** wählen Sie eine Sicherungs- und Beibehaltungsrichtlinie für die Sicherung der ausgewählten virtuellen Computer aus. Wählen Sie aus einem vorhandenen Satz von Richtlinien, oder definieren Sie eine neue.

    >[AZURE.NOTE]Für die Vorschau werden bis zu 30 Tage Beibehaltung und maximal eine Sicherung pro Tag unterstützt.

    Jeder Sicherungstresor kann mehrere Sicherungsrichtlinien aufweisen. Die Richtlinien geben die Details zur zeitlichen Planung und Aufbewahrung der Sicherung an. Beispielsweise kann eine Sicherungsrichtlinie eine tägliche Sicherung um 10:00 Uhr festlegen, während eine andere Sicherungsrichtlinie eine wöchentliche Sicherung um 6:00 Uhr vorsieht. Mehrere Sicherungsrichtlinien ermöglichen Flexibilität beim Planen von Sicherungen für Ihre virtuelle Infrastruktur.

    Jeder Sicherungsrichtlinie können mehrere virtuelle Computer zugeordnet sein. Der virtuelle Computer kann zu jedem Zeitpunkt immer nur einer Richtlinie zugeordnet sein.

6. Für jeden virtuellen Computer wird ein Auftrag erstellt, um die Schutzrichtlinie zu konfigurieren und die virtuellen Computer der Richtlinie zuzuordnen. Klicken Sie auf die Registerkarte **Aufträge**, und wählen Sie den richtigen Filter zum Anzeigen der Liste von **Schutz konfigurieren**-Aufträgen. ![Schutzauftrag konfigurieren](./media/backup-azure-vms/protect-configureprotection.png)

7. Wenn die Konfiguration abgeschlossen ist, sind die virtuellen Computer durch eine Richtlinie geschützt. Die erste Sicherung wird zum geplanten Sicherungszeitpunkt durchgeführt. Der virtuelle Computer wird nun auf der Registerkarte **Geschützte Elemente** angezeigt und weist den Schutzstatus *Geschützt * (Anfangssicherung ausstehend) auf.
    >[AZURE.NOTE]Derzeit ist das Starten der Anfangssicherung gleich nach dem Konfigurieren des Schutzes nicht als Option verfügbar.

8. Zum geplanten Zeitpunkt erstellt der Azure Backup-Dienst einen Sicherungsauftrag für jeden virtuellen Computer, der gesichert werden muss. Klicken Sie auf die Registerkarte **Aufträge**, um die Liste der **Sicherungsaufträge** anzuzeigen. Als Teil des Sicherungsvorgangs gibt der Azure Backup-Dienst einen Befehl an die Sicherungserweiterung auf jedem virtuellen Computer aus, damit alle Schreibvorgänge geleert werden und eine konsistente Momentaufnahme erstellt wird. ![Sicherung wird ausgeführt](./media/backup-azure-vms/protect-inprogress.png)

9. Zum Abschluss des Vorgangs wird der Schutzstatus des virtuellen Computers auf der Registerkarte **Geschützte Elemente** als *Geschützt* angezeigt. ![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/protect-backedupvm.png)

## Anzeigen von Sicherungsstatus und details
Sobald der Schutz eingerichtet ist, steigt die Anzahl der virtuellen Computer auch auf der **Dashboard**-Zusammenfassungsseite. Darüber hinaus zeigt die Dashboard-Seite die Anzahl der Aufträge in den letzten 24 Stunden an, die erfolgreich waren, Fehler hervorgerufen haben oder noch ausgeführt werden. Durch Klicken auf jede Kategorie wird auf der Seite **Aufträge** ein Drilldown in diese Kategorie durchgeführt. ![Status der Sicherung auf der Dashboard-Seite](./media/backup-azure-vms/dashboard-protectedvms.png)

## Problembehandlung
Behandeln Sie Fehler, die beim Verwenden von Azure-Sicherung mit Informationen in der folgenden Tabelle aufgeführt ist.

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Ermittlung | Fehler beim Entdecken neuer Elemente – Microsoft Azure Backup hat einen internen Fehler festgestellt. Warten Sie einige Minuten, und versuchen Sie es dann erneut. | Wiederholen Sie den Ermittlungsvorgang nach 15 Minuten.
| Ermittlung | Fehler beim Entdecken neuer Elemente – Ein anderer Ermittlungsvorgang wird bereits ausgeführt. Bitte warten Sie, bis der aktuelle Ermittlungsvorgang abgeschlossen ist. | Keine |
| Registrieren | Die Azure VM-Rolle befindet sich nicht im Zustand zum Installieren der Erweiterung – Prüfen Sie, ob der virtuelle Computer den Zustand "Wird ausgeführt" aufweist. Für die Azure Recovery Services-Erweiterung muss der virtuelle Computer ausgeführt werden. | Starten Sie den virtuellen Computer, und wiederholen Sie den Registrierungsvorgang, wenn der Computer den Zustand "Wird ausgeführt" aufweist.|
| Registrieren | Anzahl an Datenträgern, die mit dem virtuellen Computer angefügt wurde das unterstützte Limit überschritten – bitte einige Datenträger auf diesem virtuellen Computer trennen, und wiederholen Sie den Vorgang. Azure-Sicherung unterstützt bis zu fünf Datenträger angefügt wird, einen virtuellen Computer in Azure für die Sicherung | Keine |
| Registrieren | Microsoft Azure-Sicherung: internen Fehler - warten Sie einige Minuten und versuchen Sie dann den Vorgang erneut aus. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support. | Sie können diese Fehlermeldung aufgrund einer der folgenden nicht unterstützten Konfigurationen: <ol><li>Premium LRS <li>Multi-NIC <li>Lastenausgleich </ol> |
| Registrieren | VM Gast-Agent-Zertifikat wurde nicht gefunden. | Befolgen Sie diese Anweisungen, um den Fehler zu beheben: <ol><li>Downloaden Sie die neueste Version von der VM-Agent aus [hier](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Stellen Sie sicher, dass die Version des heruntergeladenen Agenten 2.6.1198.718 oder höher. <li>Installieren Sie VM-Agent auf dem virtuellen Computer.</ol> [Erfahren Sie mehr](#validating-vm-agent-installation) so überprüfen die Version des VM-Agents. |
| Registrieren | Fehler bei der Registrierung mit Timeout des Vorgangs Agent installieren | Überprüfen Sie, ob die Betriebssystemversion des virtuellen Computers unterstützt wird. |
| Registrieren | Befehl fehlgeschlagen - läuft ein anderer Vorgang für diesen Artikel. Warten Sie, bis der vorherige Vorgang abgeschlossen ist | Keine |
| Sicherung | Kopieren von VHDs aus sicherungstresor Timeout - wiederholen Sie den Vorgang in einigen Minuten. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support. | Dies passiert, wenn zu viele Daten kopiert werden. Überprüfen Sie, wenn Sie weniger als 6 Datenträger verfügen. |
| Sicherung | Snapshot-VM-Sub-Task ein Timeout - wiederholen Sie den Vorgang in einigen Minuten. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support | Dieser Fehler wird immer dann ausgelöst, wenn ein mit der VM-Agent Problem oder Netzwerkzugriff auf Azure-Infrastruktur in irgendeiner Weise blockiert. <ul><li>Erfahren Sie mehr über [VM-Agent Debugprobleme](#Troubleshooting-vm-agent-related-issues) <li>erfahren Sie mehr über [Debuggen Netzwerkprobleme](#troubleshooting-networking-issues) </ul> |
| Sicherung | Fehler bei der Sicherung interner Fehler: Wiederholen Sie den Vorgang in einigen Minuten. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support | Sie können diesen Fehler erhalten, zwei Gründen: <ol><li> zu viele Daten kopiert werden. Überprüfen Sie, wenn Sie weniger als 6 Laufwerke. <li>Der ursprüngliche virtuellen Computer gelöscht wurde und daher die Sicherung nicht ausgeführt werden kann. Um die Sicherungsdaten für einen gelöschten virtuellen Computer beibehalten, aber Beenden der Sicherung Fehler, Aufheben des Schutzes des virtuellen Computers, und wählen Sie die Option zum Beibehalten der Daten. Dies beendet den Sicherungszeitplan und auch die regelmäßige Fehlermeldungen. |
| Sicherung | Fehler beim Installieren von Azure Recovery Services ist die Erweiterung auf dem ausgewählten Element - VM-Agent eine Voraussetzung für Azure Recovery Services-Erweiterung. Installieren Sie den Azure-VM-Agent und die Registrierung des Neustarts | <ol> <li>Überprüfen, ob der VM-Agent ordnungsgemäß installiert wurde. <li>Stellen Sie sicher, dass das Flag für die VM-Konfiguration ordnungsgemäß festgelegt ist.</ol> [Weitere](#validating-vm-agent-installation) über VM-Agent-Installation, und überprüfen Sie die VM-Agent-Installation. |
| Sicherung | Befehl fehlgeschlagen - läuft ein anderer Vorgang derzeit für diesen Artikel. Warten Sie, bis der vorherige Vorgang abgeschlossen ist, und wiederholen Sie dann | Eine vorhandene Sicherungs- oder Wiederherstellungsauftrag für den virtuellen Computer ausgeführt wird, und ein neuer Auftrag kann nicht gestartet werden, während der vorhandene Auftrag ausgeführt wird. <br><br>Wenn Sie die Option aus, um einen fortlaufenden Auftrag abbrechen möchten, fügen Sie Ihre Stimme zu den [Feedback von Azure-Forum](http://feedback.azure.com/forums/258995-azure-backup-and-scdpm/suggestions/7941501-add-feature-to-allow-cancellation-of-backup-restor). |

### Problembehandlung bei VM-Agent-Problemen

#### Einrichten der VM-Agent
In der Regel ist der VM-Agent bereits vorhanden in virtuellen Computern, die von der Azure-Katalog erstellt werden. Virtuelle Computer, die von lokalen Rechenzentren migriert werden müssen jedoch nicht der VM-Agent installiert. Für eine solche virtuelle Computer muss die VM-Agent explizit installiert werden. Erfahren Sie mehr über [installieren den VM-Agent auf einem vorhandenen virtuellen Computer](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Für Windows-VMs:

- Herunterladen und Installieren der [-Agent-MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sie benötigen Administratorberechtigungen, um die Installation abzuschließen.
- [Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) um anzugeben, dass der Agent installiert ist.


#### Aktualisieren des VM-Agents
Aktualisieren der VM-Agent ist so einfach wie die Neuinstallation der [VM-Agent-Binärdateien](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Allerdings müssen Sie sicherstellen, dass kein backup-Vorgang ausgeführt wird, während der VM-Agent aktualisiert wird.


#### VM-Agent-Installation wird überprüft.
So überprüfen Sie für die VM-Agent-Version auf Windows-VMs

- Die Anmeldeinformationen in der Azure-VM, und navigieren Sie zu dem Ordner *C:\WindowsAzure\Packages*.
- Finden Sie die Datei WaAppAgent.exe vorhanden.
- Mit der rechten Maustaste auf die Datei, wechseln Sie zur **Eigenschaften**, und wählen Sie dann die **Details** Registerkarte.
- Das Feld Produktversion sollte 2.6.1198.718 oder höher

### Problembehandlung bei Netzwerkproblemen
Benötigen Zugriff auf das öffentliche Internet funktioniert, wie alle Erweiterungen Backup-Erweiterung. Keinen Zugriff auf das öffentliche Internet kann sich auf vielfältige Weise darstellen:

- Die Erweiterungsinstallation kann fehlschlagen.
- Der backup-Vorgänge (z. B. Datenträger Snapshot) können fehlschlagen.
- Anzeigen des Status des Sicherungsvorgangs kann fehlschlagen.

Wurde die Notwendigkeit einer öffentlichen Internet-Adressen auflösen formuliert wurde [hier](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Sie benötigen, überprüfen Sie die DNS-Konfigurationen für die VNET, und stellen Sie sicher, dass die Azure-URIs aufgelöst werden kann.

Nachdem die Namensauflösung ordnungsgemäß erfolgt ist, muss Zugriff auf den Azure-IP-Adressen auch bereitgestellt werden. Gehen Sie folgendermaßen vor, um den Zugriff auf den Azure-Infrastruktur zu entsperren:

1. Ruft die Liste der [Azure-Rechenzentrum IPs]()https://msdn.microsoft.com/
2. / library/azure/dn175718.aspx) auf die weiße Liste aufgenommen werden.
2. Zulassen der IP-Adressen mit der [neu-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) Commandlet. Führen Sie dieses Cmdlet in der Azure-VM in einem erhöhten PowerShell-Fenster (als Administrator ausführen).


## Konsistenz der Wiederherstellungspunkte
Beim Umgang mit backup-Daten sorgen Kunden über das Verhalten des virtuellen Computers, nachdem es wiederhergestellt wurde. Typische Kundenfragen sind:

- Kann der virtuelle Computer gestartet werden?
- Stehen Daten auf dem Datenträger zur Verfügung (oder) gibt es Datenverluste?
- Ist die Anwendung in der Lage, die Daten zu lesen (oder) sind die Daten beschädigt?
- Können die Daten von der Anwendung verwertet werden (oder) sind die Daten in sich konsistent, wenn sie von der Anwendung gelesen werden?

In der folgenden Tabelle werden die Arten der Konsistenz aufgeführt, die bei der Azure-VM-Sicherung und -Wiederherstellung relevant sind:

| Konsistenz | VSS-basiert | Erklärung und Details |
|-------------|-----------|---------|
| Anwendungskonsistenz | Ja | Dies ist der ideale Ort für Microsoft-Workloads, da dadurch Folgendes gewährleistet wird:<ol><li> Der virtuelle Computer wird *hochgefahren* <li>Es gibt *keine Beschädigung* <li>Es gibt keinen *Datenverlust* <li> Die Daten sind mit der Anwendung konsistent, in der die Daten verwendet werden, da die Anwendung zum Zeitpunkt der Sicherung einbezogen wird – mithilfe von VSS</ol> Durch den Volumemomentaufnahmedienst (Volume Snapshot Service, VSS) wird sichergestellt, dass die Daten ordnungsgemäß in den Speicher geschrieben werden. Die meisten Microsoft-Workloads verfügen über VSS Writer, die Workload-spezifische Aktionen im Zusammenhang mit der Datenkonsistenz ausführen. Beispielsweise umfasst Microsoft SQL Server einen VSS Writer, der die ordnungsgemäße Durchführung der Schreibvorgänge in die Transaktionsprotokolldatei und die Datenbank gewährleistet.<br><br> Für die Azure-VM-Sicherung bedeutet das Erhalten eines anwendungskonsistenten Wiederherstellungspunkts, dass die Sicherungserweiterung den VSS-Workflow aufrufen und *ordnungsgemäß* abschließen konnte, bevor die Momentaufnahme des virtuellen Computers erstellt wurde. Dies heißt natürlich, dass die VSS Writer aller Anwendungen auf dem virtuellen Azure-Computer ebenfalls aufgerufen wurden.<br><br>Erlernen Sie die [Grundlagen von VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), und erlangen Sie detaillierte Kenntnisse zur [Funktionsweise](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Dateisystemkonsistenz | Ja, für Windows-Computer | Es gibt zwei Szenarien, in denen der Wiederherstellungspunkt dateisystemkonsistent sein kann:<ul><li>Sicherung virtueller Linux-Computer in Azure, da Linux über keine entsprechende Plattform wie VSS verfügt.<li>VSS-Fehler bei der Sicherung für virtuelle Windows-Computer in Azure.</li></ul> In beiden Fällen ist es das beste, Folgendes sicherzustellen: <ol><li> Der virtuelle Computer *wird hochgefahren*. <li>Es gibt *keine Datenbeschädigung*. <li>Es gibt keinen *Datenverlust*.</ol> Anwendungen müssen einen eigenen Reparaturmechanismus für die wiederhergestellten Daten implementieren.|
| Absturzkonsistenz | Nein | Diese Situation entspricht dem Absturz eines Computers (durch eine Teil- oder Vollrückstellung). Es gibt keine Garantie für die Konsistenz der Daten auf dem Speichermedium. Nur Daten, die zum Zeitpunkt der Sicherung bereits auf dem Datenträger vorhanden sind, werden erfasst und gesichert. <ol><li>Auch wenn es keine Garantie gibt, wird das Betriebssystem in den meisten Fällen gestartet.<li>In der Regel folgt eine Prozedur zur Datenträgerüberprüfung wie chkdsk, um eine eventuelle Datenbeschädigung zu reparieren.<li> Alle Daten im Arbeitsspeicher oder Schreibvorgänge, die nicht vollständig auf den Datenträger geleert wurden, gehen verloren.<li> Die Anwendung folgt normalerweise mit einem eigenen Überprüfungsmechanismus, falls ein Datenrollback durchgeführt werden muss. </ol>Für die Sicherung virtueller Azure-Computer bedeutet das Erhalten eines absturzkonsistenten Wiederherstellungspunkts, dass Azure Backup keine Garantie für die Konsistenz der Daten im Speicher bietet - weder im Hinblick auf das Betriebssystem noch im Hinblick auf die Anwendung. Dies geschieht in der Regel, wenn der virtuelle Azure-Computer zum Zeitpunkt der Sicherung heruntergefahren wird.<br><br>Wenn das Transaktionsprotokoll beispielsweise Einträge enthält, die nicht in der Datenbank vorhanden sind, führt die Datenbanksoftware einen Rollback durch, bis die Daten konsistent sind. Beim Umgang mit Daten, die über mehrere virtuelle Laufwerke verteilt sind (z. B. übergreifende Volumes), bietet ein absturzkonsistenter Wiederherstellungspunkt keine Garantie für die Richtigkeit der Daten.|

## Nächste Schritte
Weitere Informationen zum Einstieg in Azure Backup finden Sie unter:

- [Wiederherstellen virtueller Computer](backup-azure-restore-vms.md)
- [Verwalten von virtuellen Computern](backup-azure-manage-vms)

<!---HONumber=GIT-SubDir-->