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
	ms.topic="article"
	ms.date="04/30/2015"
	ms.author="aashishr"/>


# Sichern des virtuellen Azure-Computers

Dieser Artikel ist der Hauptleitfaden zur Sicherung virtueller Azure-Computer. Bevor Sie fortfahren, stellen Sie sicher, dass alle [Voraussetzungen][prereq] erfüllt werden.

Zur Sicherung virtueller Azure-Computer gehören drei Hauptschritte:

![Drei Schritte zum Sichern eines virtuellen Azure-Computers][three-steps]

## Ermitteln der virtuellen Azure-Computer
Beim Ermittlungsvorgang wird Azure nach der Liste virtueller Computer im Abonnement abgefragt. Außerdem werden zusätzliche Informationen wie der Cloud Service-Name und die Region erfasst.

> [AZURE.NOTE]Der Ermittlungsvorgang sollte immer als erster Schritt ausgeführt werden. Dadurch wird sichergestellt, dass alle neuen virtuellen Computer, die dem Abonnement hinzugefügt wurden, identifiziert werden.

Gehen Sie zum Auslösen des Ermittlungsvorgangs folgendermaßen vor:

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Art des Workloads **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche **Auswählen**. ![Workload auswählen][select-workload]

3. Klicken Sie unten auf der Seite auf die Schaltfläche **Ermitteln**. ![Schaltfläche "Ermitteln"][discover-button]

4. Der Ermittlungsvorgang kann einige Minuten andauern, während die virtuellen Computer in einer Tabelle aufgeführt werden. Am unteren Bildschirmrand wird eine Popupbenachrichtigung angezeigt, während der Ermittlungsvorgang ausgeführt wird. ![VMs ermitteln][discover-vms]

5. Sobald der Ermittlungsvorgang abgeschlossen ist, wird eine Popupbenachrichtigung angezeigt. ![Ermitteln abgeschlossen][discover-done]

<br><br>
## Registrieren der virtuellen Azure-Computer
Bevor ein virtueller Computer geschützt werden kann, muss er beim Azure Backup-Dienst registriert werden. Der Registrierungsvorgang hat zwei Hauptziele:

1. Aktivieren der Sicherungserweiterung im VM-Agent auf dem virtuellen Computer

2. Zuordnen des virtuellen Computers zum Azure Backup-Dienst

Die Registrierung ist in der Regel eine einmalige Angelegenheit. Der Azure Backup-Dienst verarbeitet nahtlos das Upgrade und Patching der Sicherungserweiterung, ohne dass ein Benutzereingriff erforderlich wäre. Dies erspart dem Benutzer den Agent-Verwaltungsaufwand, der in der Regel mit Backup-Produkten verbunden ist.

### So registrieren Sie die virtuellen Computer

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Art des Workloads **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche "Auswählen". ![Workload auswählen][select-workload]

3. Klicken Sie unten auf der Seite auf die Schaltfläche **Registrieren**. ![Schaltfläche "Registrieren"][register-button]

4. Wählen Sie im Popupfenster **Elemente registrieren** die virtuellen Computer aus, die Sie registrieren möchten. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst zur Unterscheidung zwischen den virtuellen Computern.

  	Der Vorgang **Registrieren** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig für die Registrierung ausgewählt werden können. Dadurch wird der einmalige Aufwand zur Vorbereitung des virtuellen Computers für die Sicherung erheblich reduziert.

  	> [AZURE.NOTE]Hier werden nur die virtuellen Computer angezeigt, die nicht registriert sind und sich in derselben Region befinden wie der Sicherungstresor.

  	![Liste der zu registrierenden VMs][register-vms]

5. Für jeden virtuellen Computer, der registriert werden soll, wird ein Auftrag erstellt. Die Popupbenachrichtigung zeigt den Status dieser Aktivität an. Klicken Sie auf **Auftrag anzeigen**, um zur Seite **Aufträge** zu wechseln. ![Registrierungsauftrag][register-job]

6. Der virtuelle Computer wird auch in der Liste der registrierten Elemente aufgeführt, und der Status des Registrierungsvorgangs wird angezeigt. ![Registrierungsstatus 1][register-status1]

7. Sobald der Vorgang abgeschlossen ist, ändert sich der Status im Portal dem registrierten Status entsprechend. ![Registrierungsstatus 2][register-status2]

<br><br>
## Sichern des virtuellen Azure-Computers
Dieser Schritt umfasst das Einrichten einer Sicherungs- und Beibehaltungsrichtlinie für den virtuellen Computer. Um einen virtuellen Computer zu schützen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Art des Workloads **Azure Virtual Machine** aus, und klicken Sie auf die Schaltfläche "Auswählen". ![Workload im Portal auswählen][select-workload]

3. Klicken Sie unten auf der Seite auf die Schaltfläche **Schützen**.

4. Hierdurch wird ein Assistent **Elemente schützen** geöffnet, in dem die zu schützenden virtuellen Computer ausgewählt werden können. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst zur Unterscheidung zwischen den virtuellen Computern. Der Vorgang **Schützen** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig für die Registrierung ausgewählt werden können. Dadurch wird der Aufwand zum Schützen des virtuellen Computers erheblich reduziert.

	> [AZURE.NOTE]Hier werden nur die virtuellen Computer angezeigt, die ordnungsgemäß beim Azure Backup-Dienst registriert sind und sich in derselben Region befinden wie der Sicherungstresor.

	![Zu schützende Elemente auswählen][protect-wizard1]

5. Im zweiten Bildschirm des Assistenten **Elemente schützen** wählen Sie eine Sicherungs- und Beibehaltungsrichtlinie für die Sicherung der ausgewählten virtuellen Computer aus. Wählen Sie aus einem vorhandenen Satz von Richtlinien, oder definieren Sie eine neue.

	> [AZURE.NOTE]Für die Vorschau werden bis zu 30 Tage Beibehaltung und maximal eine Sicherung pro Tag unterstützt.

	![Schutzrichtlinie auswählen][protect-wizard2]

	Jeder Sicherungstresor kann mehrere Sicherungsrichtlinien aufweisen. Die Richtlinien geben die Details zur zeitlichen Planung und Aufbewahrung der Sicherung an. Beispielsweise kann eine Sicherungsrichtlinie eine tägliche Sicherung um 10:00 Uhr festlegen, während eine andere Sicherungsrichtlinie eine wöchentliche Sicherung um 6:00 Uhr vorsieht. Mehrere Sicherungsrichtlinien ermöglichen Flexibilität beim Planen von Sicherungen für Ihre virtuelle Infrastruktur. Jeder Sicherungsrichtlinie können mehrere virtuelle Computer zugeordnet sein. Der virtuelle Computer kann zu jedem Zeitpunkt immer nur einer Richtlinie zugeordnet sein.

6. Für jeden virtuellen Computer wird ein Auftrag erstellt, um die Schutzrichtlinie zu konfigurieren und die virtuellen Computer der Richtlinie zuzuordnen. Klicken Sie auf die Registerkarte **Aufträge**, und wählen Sie den richtigen Filter zum Anzeigen der Liste von **Schutz konfigurieren**-Aufträgen. ![Schutzauftrag konfigurieren][protect-configure]

7. Wenn die Konfiguration abgeschlossen ist, sind die virtuellen Computer durch eine Richtlinie geschützt. Die erste Sicherung wird zum geplanten Sicherungszeitpunkt durchgeführt. Der virtuelle Computer wird nun auf der Registerkarte **Geschützte Elemente** angezeigt und weist den Schutzstatus *Geschützt * (Anfangssicherung ausstehend) auf.

	> [AZURE.NOTE]Derzeit ist das Starten der Anfangssicherung gleich nach dem Konfigurieren des Schutzes nicht als Option verfügbar.

8. Zum geplanten Zeitpunkt erstellt der Azure Backup-Dienst einen Sicherungsauftrag für jeden virtuellen Computer, der gesichert werden muss. Klicken Sie auf die Registerkarte **Aufträge**, um die Liste der **Sicherungsaufträge** anzuzeigen. Als Teil des Sicherungsvorgangs gibt der Azure Backup-Dienst einen Befehl an die Sicherungserweiterung auf jedem virtuellen Computer aus, damit alle Schreibvorgänge geleert werden und eine konsistente Momentaufnahme erstellt wird. ![Sicherung wird ausgeführt][protect-inprogress]

9. Zum Abschluss des Vorgangs wird der Schutzstatus des virtuellen Computers auf der Registerkarte **Geschützte Elemente** als *Geschützt* angezeigt. ![Virtueller Computer wird mit Wiederherstellungspunkt gesichert][protect-backedup]


## Anzeigen von Status und Details der Sicherung
Sobald der Schutz eingerichtet ist, steigt die Anzahl der virtuellen Computer auch auf der **Dashboard**-Zusammenfassungsseite. Darüber hinaus zeigt die Dashboard-Seite die Anzahl der Aufträge in den letzten 24 Stunden an, die erfolgreich waren, Fehler hervorgerufen haben oder noch ausgeführt werden. Durch Klicken auf jede Kategorie wird auf der Seite **Aufträge** ein Drilldown in diese Kategorie durchgeführt. ![Status der Sicherung auf der Dashboard-Seite][dashboard]


## Problembehandlung
Sie können Probleme bei der Ermittlung und Registrierung anhand der folgenden Informationen behandeln.

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Ermittlung | Fehler beim Entdecken neuer Elemente – Microsoft Azure Backup hat einen internen Fehler festgestellt. Warten Sie einige Minuten, und versuchen Sie es dann erneut. | Wiederholen Sie den Ermittlungsvorgang nach 15 Minuten.
| Ermittlung | Fehler beim Entdecken neuer Elemente – Ein anderer Ermittlungsvorgang wird bereits ausgeführt. Bitte warten Sie, bis der aktuelle Ermittlungsvorgang abgeschlossen ist. | Warten Sie, bis der vorhandene Ermittlungsvorgang abgeschlossen ist. |
| Registrieren | Die Azure VM-Rolle befindet sich nicht im Zustand zum Installieren der Erweiterung – Prüfen Sie, ob der virtuelle Computer den Zustand "Wird ausgeführt" aufweist. Für die Azure Recovery Services-Erweiterung muss der virtuelle Computer ausgeführt werden. | Starten Sie den virtuellen Computer, und wiederholen Sie den Registrierungsvorgang, wenn der Computer den Zustand "Wird ausgeführt" aufweist.|


## Konsistenz der Wiederherstellungspunkte
Beim Umgang mit Sicherungsdaten machen sich Kunden Gedanken über das Verhalten des virtuellen Computers nach der Wiederherstellung. Typische Kundenfragen sind:

+ Kann der virtuelle Computer gestartet werden?

+ Stehen Daten auf dem Datenträger zur Verfügung (oder) gibt es Datenverluste?

+ Ist die Anwendung in der Lage, die Daten zu lesen (oder) sind die Daten beschädigt?

+ Können die Daten von der Anwendung verwertet werden (oder) sind die Daten in sich konsistent, wenn sie von der Anwendung gelesen werden?

In der folgenden Tabelle werden die Arten der Konsistenz aufgeführt, die bei der Azure-VM-Sicherung und -Wiederherstellung relevant sind:

| Konsistenz | VSS-basiert | Erklärung und Details |
|-------------|-----------|---------|
| Anwendungskonsistenz | Ja | Dies ist der ideale Ort für Microsoft-Workloads, da dadurch Folgendes gewährleistet wird:<ol><li> Der virtuelle Computer wird *hochgefahren* <li>Es gibt *keine Beschädigung* <li>Es gibt keinen *Datenverlust* <li> Die Daten sind mit der Anwendung konsistent, in der die Daten verwendet werden, da die Anwendung zum Zeitpunkt der Sicherung einbezogen wird – mithilfe von VSS</ol> Durch den Volumemomentaufnahmedienst (Volume Snapshot Service, VSS) wird sichergestellt, dass die Daten ordnungsgemäß in den Speicher geschrieben werden. Die meisten Microsoft-Workloads verfügen über VSS Writer, die Workload-spezifische Aktionen im Zusammenhang mit der Datenkonsistenz ausführen. Beispielsweise umfasst Microsoft SQL Server einen VSS Writer, der die ordnungsgemäße Durchführung der Schreibvorgänge in die Transaktionsprotokolldatei und die Datenbank gewährleistet.<br><br> Für die Azure-VM-Sicherung bedeutet das Erhalten eines anwendungskonsistenten Wiederherstellungspunkts, dass die Sicherungserweiterung den VSS-Workflow aufrufen und *ordnungsgemäß* abschließen konnte, bevor die Momentaufnahme des virtuellen Computers erstellt wurde. Dies heißt natürlich, dass die VSS Writer aller Anwendungen auf dem virtuellen Azure-Computer ebenfalls aufgerufen wurden.<br><br>Erlernen Sie die [Grundlagen von VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), und erlangen Sie detaillierte Kenntnisse zur [Funktionsweise](https://technet.microsoft.com/de-de/library/cc785914%28v=ws.10%29.aspx). |
| Dateisystemkonsistenz | Ja, für Windows-Computer | Es gibt zwei Szenarien, in denen der Wiederherstellungspunkt dateisystemkonsistent sein kann:<ul><li>Sicherung virtueller Linux-Computer in Azure, da Linux über keine entsprechende Plattform wie VSS verfügt.<li>VSS-Fehler bei der Sicherung für virtuelle Windows-Computer in Azure.</li></ul> In beiden Fällen ist es das beste, Folgendes sicherzustellen: <ol><li> Der virtuelle Computer *wird hochgefahren*. <li>Es gibt *keine Datenbeschädigung*. <li>Es gibt keinen *Datenverlust*.</ol> Anwendungen müssen einen eigenen Reparaturmechanismus für die wiederhergestellten Daten implementieren.|
| Absturzkonsistenz | Nein | Diese Situation entspricht dem Absturz eines Computers (durch eine Teil- oder Vollrückstellung). Es gibt keine Garantie für die Konsistenz der Daten auf dem Speichermedium. Nur Daten, die zum Zeitpunkt der Sicherung bereits auf dem Datenträger vorhanden sind, werden erfasst und gesichert. <ol><li>Auch wenn es keine Garantie gibt, wird das Betriebssystem in den meisten Fällen gestartet.<li>In der Regel folgt eine Prozedur zur Datenträgerüberprüfung wie chkdsk, um eine eventuelle Datenbeschädigung zu reparieren.<li> Alle Daten im Arbeitsspeicher oder Schreibvorgänge, die nicht vollständig auf den Datenträger geleert wurden, gehen verloren.<li> Die Anwendung folgt normalerweise mit einem eigenen Überprüfungsmechanismus, falls ein Datenrollback durchgeführt werden muss. </ol>Für die Sicherung virtueller Azure-Computer bedeutet das Erhalten eines absturzkonsistenten Wiederherstellungspunkts, dass Azure Backup keine Garantie für die Konsistenz der Daten im Speicher bietet - weder im Hinblick auf das Betriebssystem noch im Hinblick auf die Anwendung. Dies geschieht in der Regel, wenn der virtuelle Azure-Computer zum Zeitpunkt der Sicherung heruntergefahren wird.<br><br>Wenn das Transaktionsprotokoll beispielsweise Einträge enthält, die nicht in der Datenbank vorhanden sind, führt die Datenbanksoftware einen Rollback durch, bis die Daten konsistent sind. Beim Umgang mit Daten, die über mehrere virtuelle Laufwerke verteilt sind (z. B. übergreifende Volumes), bietet ein absturzkonsistenter Wiederherstellungspunkt keine Garantie für die Richtigkeit der Daten.|



## Nächste Schritte
Weitere Informationen zum Einstieg in Azure Backup finden Sie unter:

- [Wiederherstellen virtueller Computer](backup-azure-restore-vms.md)

[three-steps]: ./media/backup-azure-vms/3-steps-for-backup.png
[select-workload]: ./media/backup-azure-vms/discovery-select-workload.png
[discover-button]: ./media/backup-azure-vms/discover-button.png
[discover-vms]: ./media/backup-azure-vms/discovering-vms.png
[discover-done]: ./media/backup-azure-vms/discovery-complete.png
[register-button]: ./media/backup-azure-vms/register-button.png
[register-job]: ./media/backup-azure-vms/register-create-job.png
[register-vms]: ./media/backup-azure-vms/register-popup.png
[register-status1]: ./media/backup-azure-vms/register-status01.png
[register-status2]: ./media/backup-azure-vms/register-status02.png
[select-workload]: ./media/backup-azure-vms/select-workload.png
[protect-wizard1]: ./media/backup-azure-vms/protect-wizard1.png
[protect-wizard2]: ./media/backup-azure-vms/protect-wizard2.png
[protect-configure]: ./media/backup-azure-vms/protect-configureprotection.png
[protect-inprogress]: ./media/backup-azure-vms/protect-inprogress.png
[protect-backedup]: ./media/backup-azure-vms/protect-backedupvm.png
[dashboard]: ./media/backup-azure-vms/dashboard-protectedvms.png
[prereq]: http://azure.microsoft.com/documentation/articles/backup-azure-vms-introduction/#prerequisites

<!--HONumber=54-->