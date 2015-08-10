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
	ms.date="07/06/2015"
	ms.author="aashishr"/>


# Sichern von virtuellen Azure-Computern
Dieser Artikel ist der Hauptleitfaden zur Sicherung virtueller Azure-Computer. Bevor Sie fortfahren, stellen Sie sicher, dass alle [Voraussetzungen](backup-azure-vms-introduction.md#prerequisites) erfüllt werden.

Zur Sicherung virtueller Azure-Computer gehören drei Hauptschritte:

![Drei Schritte zum Sichern eines virtuellen Azure-Computers](./media/backup-azure-vms/3-steps-for-backup.png)

## 1\. Ermitteln von virtuellen Azure-Computern
Beim Ermittlungsvorgang wird Azure nach der Liste virtueller Computer im Abonnement abgefragt. Außerdem werden zusätzliche Informationen wie der Clouddienstname und die Region erfasst.

> [AZURE.NOTE]Der Ermittlungsvorgang sollte immer als erster Schritt ausgeführt werden. Dadurch wird sichergestellt, dass alle neuen virtuellen Computer, die dem Abonnement hinzugefügt wurden, identifiziert werden.

### So lösen Sie den Ermittlungsprozess aus

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Art des Workloads **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche **Auswählen**. ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie unten auf der Seite auf die Schaltfläche **ERMITTELN**. ![Schaltfläche "Ermitteln"](./media/backup-azure-vms/discover-button-only.png)

4. Der Ermittlungsvorgang kann einige Minuten andauern, während die virtuellen Computer in einer Tabelle aufgeführt werden. Am unteren Bildschirmrand wird eine Popupbenachrichtigung angezeigt, während der Ermittlungsvorgang ausgeführt wird. ![VMs ermitteln](./media/backup-azure-vms/discovering-vms.png)

5. Sobald der Ermittlungsvorgang abgeschlossen ist, wird eine Popupbenachrichtigung angezeigt. ![Ermitteln abgeschlossen](./media/backup-azure-vms/discovery-complete.png)

##  2\. Registrieren von virtuellen Azure-Computern
Bevor ein virtueller Computer geschützt werden kann, muss er beim Azure Backup-Dienst registriert werden. Der Registrierungsvorgang hat zwei Hauptziele:

1. Aktivieren der Sicherungserweiterung im VM-Agent auf dem virtuellen Computer

2. Zuordnen des virtuellen Computers zum Azure Backup-Dienst

Die Registrierung ist in der Regel eine einmalige Angelegenheit. Der Azure Backup-Dienst verarbeitet nahtlos das Upgrade und Patching der Sicherungserweiterung, ohne dass ein Benutzereingriff erforderlich wäre. Dies erspart dem Benutzer den Agent-Verwaltungsaufwand, der in der Regel mit Backup-Produkten verbunden ist.

### So registrieren Sie virtuelle Computer

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Workloadtyp **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche "Auswählen". ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie unten auf der Seite auf die Schaltfläche **REGISTRIEREN**. ![Schaltfläche "Registrieren"](./media/backup-azure-vms/register-button-only.png)

4. Wählen Sie im Popupfenster **Elemente registrieren** die virtuellen Computer aus, die Sie registrieren möchten. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst zur Unterscheidung zwischen den virtuellen Computern.

    Der Vorgang **Registrieren** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig für die Registrierung ausgewählt werden können. Dadurch wird der einmalige Aufwand zur Vorbereitung des virtuellen Computers für die Sicherung erheblich reduziert.

    >[AZURE.NOTE]Es werden nur die virtuellen Computer angezeigt, die nicht registriert sind und sich in derselben Region befinden wie der Sicherungstresor.

5. Für jeden virtuellen Computer, der registriert werden soll, wird ein Auftrag erstellt. Die Popupbenachrichtigung zeigt den Status dieser Aktivität an. Klicken Sie auf **Auftrag anzeigen**, um zur Seite **Aufträge** zu wechseln. ![Registrierungsauftrag](./media/backup-azure-vms/register-create-job.png)

6. Der virtuelle Computer wird auch in der Liste der registrierten Elemente aufgeführt, und der Status des Registrierungsvorgangs wird angezeigt. ![Registrierungsstatus 1](./media/backup-azure-vms/register-status01.png)

7. Sobald der Vorgang abgeschlossen ist, ändert sich der Status im Portal dem registrierten Status entsprechend. ![Registrierungsstatus 2](./media/backup-azure-vms/register-status02.png)

## 3\. Schützen: Sichern von virtuellen Azure-Computern
Dieser Schritt umfasst das Einrichten einer Sicherungs- und Beibehaltungsrichtlinie für den virtuellen Computer. Um einen virtuellen Computer zu schützen, führen Sie die folgenden Schritte aus:

### So sichern Sie virtuelle Azure-Computer
1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.
2. Wählen Sie im Dropdownmenü als Art des Workloads **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche **Auswählen**. ![Workload im Portal auswählen](./media/backup-azure-vms/select-workload.png)

3. Klicken Sie unten auf der Seite auf die Schaltfläche **SCHÜTZEN**.

4. Hierdurch wird ein Assistent **Elemente schützen** geöffnet, in dem die zu schützenden virtuellen Computer ausgewählt werden können. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst zur Unterscheidung zwischen den virtuellen Computern.

    Der Vorgang **Schützen** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig für die Registrierung ausgewählt werden können. Dadurch wird der Aufwand zum Schützen des virtuellen Computers erheblich reduziert.

    >[AZURE.NOTE]Hier werden nur die virtuellen Computer angezeigt, die ordnungsgemäß beim Azure Backup-Dienst registriert sind und sich in derselben Region befinden wie der Sicherungstresor.

5. Im zweiten Bildschirm des Assistenten **Elemente schützen** wählen Sie eine Sicherungs- und Beibehaltungsrichtlinie für die Sicherung der ausgewählten virtuellen Computer aus. Wählen Sie aus einem vorhandenen Satz von Richtlinien, oder definieren Sie eine neue.

    >[AZURE.NOTE]Für die Vorschau werden bis zu 30 Tage Beibehaltung und maximal eine Sicherung pro Tag unterstützt.

    Jeder Sicherungstresor kann mehrere Sicherungsrichtlinien aufweisen. Die Richtlinien geben die Details zur zeitlichen Planung und Aufbewahrung der Sicherung an. Beispielsweise kann eine Sicherungsrichtlinie eine tägliche Sicherung um 10:00 Uhr festlegen, während eine andere Sicherungsrichtlinie eine wöchentliche Sicherung um 6:00 Uhr vorsieht. Mehrere Sicherungsrichtlinien ermöglichen Flexibilität beim Planen von Sicherungen für Ihre virtuelle Infrastruktur.

    Jeder Sicherungsrichtlinie können mehrere virtuelle Computer zugeordnet sein. Der virtuelle Computer kann zu jedem Zeitpunkt immer nur einer Richtlinie zugeordnet sein.

6. Für jeden virtuellen Computer wird ein Auftrag erstellt, um die Schutzrichtlinie zu konfigurieren und die virtuellen Computer der Richtlinie zuzuordnen. Klicken Sie auf die Registerkarte **Aufträge**, und wählen Sie den richtigen Filter zum Anzeigen der Liste von **Schutz konfigurieren**-Aufträgen. ![Schutzauftrag konfigurieren](./media/backup-azure-vms/protect-configureprotection.png)

7. Wenn die Konfiguration abgeschlossen ist, sind die virtuellen Computer durch eine Richtlinie geschützt. Die erste Sicherung wird zum geplanten Sicherungszeitpunkt durchgeführt. Der virtuelle Computer wird nun auf der Registerkarte **Geschützte Elemente** angezeigt und weist den Schutzstatus *Geschützt * \(Anfangssicherung ausstehend\) auf.
    >[AZURE.NOTE]Derzeit ist das Starten der Anfangssicherung gleich nach dem Konfigurieren des Schutzes nicht als Option verfügbar.

8. Zum geplanten Zeitpunkt erstellt der Azure Backup-Dienst einen Sicherungsauftrag für jeden virtuellen Computer, der gesichert werden muss. Klicken Sie auf die Registerkarte **Aufträge**, um die Liste der **Sicherungsaufträge** anzuzeigen. Als Teil des Sicherungsvorgangs gibt der Azure Backup-Dienst einen Befehl an die Sicherungserweiterung auf jedem virtuellen Computer aus, damit alle Schreibvorgänge geleert werden und eine konsistente Momentaufnahme erstellt wird. ![Sicherung wird ausgeführt](./media/backup-azure-vms/protect-inprogress.png)

9. Zum Abschluss des Vorgangs wird der Schutzstatus des virtuellen Computers auf der Registerkarte **Geschützte Elemente** als *Geschützt* angezeigt. ![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/protect-backedupvm.png)

## Anzeigen von Status und Details der Sicherung
Sobald der Schutz eingerichtet ist, steigt die Anzahl der virtuellen Computer auch auf der **Dashboard**-Zusammenfassungsseite. Darüber hinaus zeigt die Dashboard-Seite die Anzahl der Aufträge in den letzten 24 Stunden an, die erfolgreich waren, Fehler hervorgerufen haben oder noch ausgeführt werden. Durch Klicken auf jede Kategorie wird auf der Seite **Aufträge** ein Drilldown in diese Kategorie durchgeführt. ![Status der Sicherung auf der Dashboard-Seite](./media/backup-azure-vms/dashboard-protectedvms.png)

## Problembehandlung
Sie können die Problembehandlung für Fehler, die beim Verwenden von Azure Backup auftreten, mit den Informationen in der unten angegebenen Tabelle durchführen.

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Ermittlung | Fehler beim Entdecken neuer Elemente – Microsoft Azure Backup hat einen internen Fehler festgestellt. Warten Sie einige Minuten, und versuchen Sie es dann erneut. | Wiederholen Sie den Ermittlungsvorgang nach 15 Minuten.
| Ermittlung | Fehler beim Entdecken neuer Elemente – Ein anderer Ermittlungsvorgang wird bereits ausgeführt. Bitte warten Sie, bis der aktuelle Ermittlungsvorgang abgeschlossen ist. | Keine |
| Registrieren | Die Azure VM-Rolle befindet sich nicht im Zustand zum Installieren der Erweiterung – Prüfen Sie, ob der virtuelle Computer den Zustand "Wird ausgeführt" aufweist. Für die Azure Recovery Services-Erweiterung muss der virtuelle Computer ausgeführt werden. | Starten Sie den virtuellen Computer, und wiederholen Sie den Registrierungsvorgang, wenn der Computer den Zustand "Wird ausgeführt" aufweist.|
| Registrieren | Für die Anzahl von Datenträgern für Daten, die dem virtuellen Computer zugeordnet sind, wurde die unterstützte Obergrenze überschritten. Trennen Sie einige Datenträger für Daten von diesem virtuellen Computer, und wiederholen Sie den Vorgang. Azure Backup unterstützt bis zu fünf Datenträger für Daten, die für die Sicherung an einen virtuellen Azure-Computer angeschlossen sind. | Keine |
| Registrieren | Für Microsoft Azure Backup ist ein interner Fehler aufgetreten. Warten Sie einige Minuten, und wiederholen Sie anschließend den Vorgang. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dieser Fehler kann aufgrund einer der folgenden nicht unterstützten Konfigurationen auftreten: <ol><li>Premium LRS <li>Multi-NIC <li>Load Balancer </ol> |
| Registrieren | Zertifikat für VM-Gast-Agent wurde nicht gefunden | Befolgen Sie diese Anweisungen, um den Fehler zu beheben: <ol><li>Laden Sie [hier](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) die aktuelle Version des VM-Agents herunter. Stellen Sie sicher, dass die Version des heruntergeladenen Agents 2.6.1198.718 oder höher ist. <li>Installieren Sie den VM-Agent auf dem virtuellen Computer.</ol> [Erfahren Sie mehr](#validating-vm-agent-installation) darüber, wie Sie die Version des VM-Agents überprüfen. |
| Registrieren | Fehler bei der Registrierung aufgrund einer Zeitüberschreitung beim Installieren des Agents | Überprüfen Sie, ob die Betriebssystemversion des virtuellen Computers unterstützt wird. |
| Registrieren | Fehler beim Ausführen des Befehls – Für dieses Element wird ein anderer Vorgang ausgeführt. Warten Sie, bis der aktuelle Vorgang abgeschlossen ist. | Keine |
| Sicherung | Zeitüberschreitung beim Kopieren von VHDs aus dem Sicherungstresor – Versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dies passiert, wenn zu viele zu kopierende Daten vorhanden sind. Vergewissern Sie sich, dass Sie weniger als sechs Datenträger verwenden. |
| Sicherung | Zeitüberschreitung bei Momentaufnahme für VM-Unteraufgabe – Versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dieser Fehler wird ausgelöst, wenn ein Problem mit dem VM-Agent besteht oder der Netzwerkzugriff auf die Azure-Infrastruktur blockiert ist. <ul><li>Weitere Informationen [zum Debuggen von Problemen mit dem VM-Agent](#Troubleshooting-vm-agent-related-issues) <li>Weitere Informationen [zum Debuggen von Netzwerkproblemen](#troubleshooting-networking-issues) </ul> |
| Sicherung | Interner Fehler bei der Sicherung – Versuchen Sie, den Vorgang nach einigen Minuten zu wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. | Dieser Fehler kann aus zwei Gründen auftreten: <ol><li> Es sind zu viele zu kopierende Daten vorhanden. Vergewissern Sie sich, dass Sie weniger als sechs Datenträger verwenden. <li>Der ursprüngliche virtuelle Computer wurde gelöscht, sodass keine Sicherung erstellt werden kann. Um die Sicherungsdaten für einen gelöschten virtuellen Computer beizubehalten und gleichzeitig die Sicherungsfehler zu vermeiden, heben Sie den Schutz für den virtuellen Computer auf und wählen die Option zum Beibehalten der Daten. So werden der Sicherungszeitplan und die wiederkehrenden Fehlermeldungen vermieden. |
| Sicherung | Fehler beim Installieren der Azure Recovery Services-Erweiterung auf dem ausgewählten Element – Der VM-Agent ist eine Voraussetzung für die Azure Recovery Services-Erweiterung. Installieren Sie den Azure-VM-Agent, und starten Sie den Registrierungsvorgang erneut. | <ol> <li>Überprüfen Sie, ob der VM-Agent richtig installiert wurde. <li>Stellen Sie sicher, dass das Flag für die VM-Konfiguration richtig festgelegt wurde.</ol> [Erfahren Sie mehr](#validating-vm-agent-installation) über die VM-Agent-Installation und die dazugehörige Überprüfung. |
| Sicherung | Fehler bei der Ausführung des Befehls – Für dieses Element wird ein anderer Vorgang ausgeführt. Warten Sie, bis der aktuelle Vorgang abgeschlossen ist, und wiederholen Sie anschließend Ihren Vorgang. | Ein vorhandener Sicherungs- oder Wiederherstellungsauftrag wird für den virtuellen Computer ausgeführt. Ein neuer Auftrag kann erst gestartet werden, wenn die Ausführung des aktuellen Auftrags abgeschlossen ist. <br><br>Falls Sie sich eine Option zum Abbrechen eines laufenden Auftrags wünschen, können Sie Ihre Stimme für diese Option im [Azure-Feedbackforum](http://feedback.azure.com/forums/258995-azure-backup-and-scdpm/suggestions/7941501-add-feature-to-allow-cancellation-of-backup-restor) abgeben. |

### Problembehandlung bei VM-Agent-Problemen

#### Einrichten des VM-Agents
Normalerweise ist der VM-Agent auf virtuellen Computern, die über den Azure-Katalog erstellt werden, bereits vorhanden. Auf virtuellen Computern, die aus lokalen Rechenzentren migriert werden, ist der VM-Agent aber nicht installiert. Für diese virtuellen Computer muss der VM-Agent explizit installiert werden. Erfahren Sie mehr über das [Installieren des VM-Agents auf einem vorhandenen virtuellen Computer](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Für virtuelle Windows-Computer:

- Laden Sie den [Agent-MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie ihn. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen.
- [Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), um anzugeben, dass der Agent installiert wurde.


#### Aktualisieren des VM-Agents
Das Aktualisieren des VM-Agents ist so einfach wie das Neuinstallieren der [Binärdateien für den VM-Agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Allerdings müssen Sie sicherstellen, dass kein Sicherungsvorgang ausgeführt wird, während der VM-Agent aktualisiert wird.


#### Überprüfen der VM-Agent-Installation
So überprüfen Sie die Version des VM-Agents auf virtuellen Windows-Computern

1. Melden Sie sich am virtuellen Azure-Computer an, und navigieren Sie zum Ordner *C:\\WindowsAzure\\Packages*. Dieser Ordner enthält die Datei "WaAppAgent.exe".
2. Klicken Sie mit der rechten Maustaste auf die Datei, wechseln Sie zu **Eigenschaften**, und wählen Sie dann die Registerkarte **Details**. Im Feld mit der Produktversion sollte 2.6.1198.718 oder eine höhere Version angegeben sein.

### Problembehandlung bei Netzwerkproblemen
Wie bei allen Erweiterungen ist für die Backup-Erweiterung der Zugriff auf das öffentliche Internet erforderlich, damit sie funktioniert. Wenn kein Zugriff auf das öffentliche Internet besteht, kann dies zu unterschiedlichen Ergebnissen führen:

- Beim Installieren der Erweiterung kann ein Fehler auftreten.
- Bei den Sicherungsvorgängen \(z. B. Datenträger-Momentaufnahme\) kann ein Fehler auftreten.
- Beim Anzeigen des Status für den Sicherungsvorgang kann ein Fehler auftreten.

Die Notwendigkeit zur Auflösung von öffentlichen Internetadressen wird [hier](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx) beschrieben. Sie müssen die DNS-Konfigurationen für das VNET überprüfen und sicherstellen, dass die Azure-URIs aufgelöst werden können.

Nachdem die Namensauflösung richtig eingerichtet wurde, muss auch der Zugriff auf die Azure-IP-Adressen bereitgestellt werden. Führen Sie die folgenden Schritte aus, um die Blockierung des Zugriffs auf die Azure-Infrastruktur aufzuheben:

1. Beschaffen Sie sich die Liste mit den [IP-Adressen des Azure-Rechenzentrums](https://msdn.microsoft.com/library/azure/dn175718.aspx), die auf der Positivliste stehen sollen.
2. Heben Sie Blockierung für die IP-Adressen mit dem [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx)-Cmdlet auf. Führen Sie dieses Cmdlet auf dem virtuellen Azure-Computer in einem PowerShell-Fenster mit erhöhten Rechten aus \(Als Administrator ausführen\).


## Konsistenz der Wiederherstellungspunkte
Beim Umgang mit Sicherungsdaten machen sich Kunden Gedanken über das Verhalten des virtuellen Computers nach der Wiederherstellung. Typische Kundenfragen sind:

- Kann der virtuelle Computer gestartet werden?
- Stehen Daten auf dem Datenträger zur Verfügung \(oder\) gibt es Datenverluste?
- Ist die Anwendung in der Lage, die Daten zu lesen \(oder\) sind die Daten beschädigt?
- Können die Daten von der Anwendung verwertet werden \(oder\) sind die Daten in sich konsistent, wenn sie von der Anwendung gelesen werden?

In der folgenden Tabelle werden die Arten der Konsistenz aufgeführt, die bei der Azure-VM-Sicherung und -Wiederherstellung relevant sind:

| Konsistenz | VSS-basiert | Erklärung und Details |
|-------------|-----------|---------|
| Anwendungskonsistenz | Ja | Dies ist der ideale Ort für Microsoft-Workloads, da dadurch Folgendes gewährleistet wird:<ol><li> Der virtuelle Computer wird *hochgefahren* <li>Es gibt *keine Beschädigung* <li>Es gibt keinen *Datenverlust* <li> Die Daten sind mit der Anwendung konsistent, in der die Daten verwendet werden, da die Anwendung zum Zeitpunkt der Sicherung einbezogen wird – mithilfe von VSS</ol> Durch den Volumemomentaufnahmedienst \(Volume Snapshot Service, VSS\) wird sichergestellt, dass die Daten ordnungsgemäß in den Speicher geschrieben werden. Die meisten Microsoft-Workloads verfügen über VSS Writer, die Workload-spezifische Aktionen im Zusammenhang mit der Datenkonsistenz ausführen. Beispielsweise umfasst Microsoft SQL Server einen VSS Writer, der die ordnungsgemäße Durchführung der Schreibvorgänge in die Transaktionsprotokolldatei und die Datenbank gewährleistet.<br><br> Für die Azure-VM-Sicherung bedeutet das Erhalten eines anwendungskonsistenten Wiederherstellungspunkts, dass die Sicherungserweiterung den VSS-Workflow aufrufen und *ordnungsgemäß* abschließen konnte, bevor die Momentaufnahme des virtuellen Computers erstellt wurde. Dies heißt natürlich, dass die VSS Writer aller Anwendungen auf dem virtuellen Azure-Computer ebenfalls aufgerufen wurden.<br><br>Erlernen Sie die [Grundlagen von VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), und erlangen Sie detaillierte Kenntnisse zur [Funktionsweise](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Dateisystemkonsistenz | Ja, für Windows-Computer | Es gibt zwei Szenarien, in denen der Wiederherstellungspunkt dateisystemkonsistent sein kann:<ul><li>Sicherung virtueller Linux-Computer in Azure, da Linux über keine entsprechende Plattform wie VSS verfügt.<li>VSS-Fehler bei der Sicherung für virtuelle Windows-Computer in Azure.</li></ul> In beiden Fällen ist es das beste, Folgendes sicherzustellen: <ol><li> Der virtuelle Computer *wird hochgefahren*. <li>Es gibt *keine Datenbeschädigung*. <li>Es gibt keinen *Datenverlust*.</ol> Anwendungen müssen einen eigenen Reparaturmechanismus für die wiederhergestellten Daten implementieren.|
| Absturzkonsistenz | Nein | Diese Situation entspricht dem Absturz eines Computers \(durch eine Teil- oder Vollrückstellung\). Dies geschieht normalerweise, wenn zum Zeitpunkt der Sicherung der virtuelle Azure-Computer heruntergefahren wird. Für die Sicherung virtueller Azure-Computer bedeutet das Erhalten eines ausfallsicheren Wiederherstellungspunkts, dass Azure Backup keine Garantie für die Konsistenz der Daten auf dem Speichermedium gewährt – weder im Hinblick auf das Betriebssystem noch im Hinblick auf die Anwendung. Nur Daten, die zum Zeitpunkt der Sicherung bereits auf dem Datenträger vorhanden sind, werden erfasst und gesichert. <br/> <br/> Auch wenn es keine Garantie gibt, wird das Betriebssystem in den meisten Fällen gestartet. In der Regel folgt eine Prozedur zur Datenträgerüberprüfung wie „chkdsk“, um eine mögliche Datenbeschädigung zu reparieren. Alle Daten im Arbeitsspeicher oder Schreibvorgänge, die nicht vollständig auf den Datenträger übertragen wurden, gehen verloren. Die Anwendung folgt normalerweise mit einem eigenen Überprüfungsmechanismus, falls ein Datenrollback durchgeführt werden muss. Für die Sicherung virtueller Azure-Computer bedeutet das Erhalten eines ausfallsicheren Wiederherstellungspunkts, dass Azure Backup keine Garantie für die Konsistenz der Daten im Speicher gewährt – weder im Hinblick auf das Betriebssystem noch im Hinblick auf die Anwendung. Dies geschieht in der Regel, wenn der virtuelle Azure-Computer zum Zeitpunkt der Sicherung heruntergefahren wird.<br><br>Wenn das Transaktionsprotokoll beispielsweise Einträge enthält, die nicht in der Datenbank vorhanden sind, führt die Datenbanksoftware einen Rollback durch, bis die Daten konsistent sind. Beim Umgang mit Daten, die über mehrere virtuelle Laufwerke verteilt sind \(z. B. übergreifende Volumes\), bietet ein absturzkonsistenter Wiederherstellungspunkt keine Garantie für die Richtigkeit der Daten.|

## Nächste Schritte
Weitere Informationen zum Einstieg in Azure Backup finden Sie unter:

- [Wiederherstellen virtueller Computer](backup-azure-restore-vms.md)
- [Verwalten virtueller Computer](backup-azure-manage-vms)

<!---HONumber=July15_HO5-->