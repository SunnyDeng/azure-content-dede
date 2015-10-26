<properties
	pageTitle="Backup eines virtuellen Azure-Computers – Backup | Microsoft Azure"
	description="Informationen zum Sichern eines virtuellen Azure-Computers nach der Registrierung"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="09/24/2015" ms.author="trinadhk"; "aashishr"; "jimpark"/>


# Sichern von virtuellen Azure-Computern
Dieser Artikel ist der Hauptleitfaden zur Sicherung virtueller Azure-Computer. Bevor Sie fortfahren, stellen Sie sicher, dass alle [Voraussetzungen](backup-azure-vms-introduction.md#prerequisites) erfüllt werden.

Zur Sicherung virtueller Azure-Computer gehören drei Hauptschritte:

![Drei Schritte zum Sichern eines virtuellen Azure-Computers](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE]Die Sicherung virtueller Computer erfolgt lokal. Der Sicherungstresor einer angegebenen Azure-Region kann nicht zur Sicherung virtueller Computer aus einer anderen Azure-Region verwendet werden. Daher muss in jeder Azure-Region mit virtuellen Computern, die eine Sicherung erfordern, mindestens ein Sicherungstresor erstellt werden.

## 1\. Ermitteln von virtuellen Azure-Computern
Beim Ermittlungsvorgang wird Azure nach der Liste virtueller Computer im Abonnement abgefragt. Außerdem werden zusätzliche Informationen wie der Clouddienstname und die Region erfasst. Der Ermittlungsvorgang sollte immer als erster Schritt ausgeführt werden. Dadurch wird sichergestellt, dass alle neuen virtuellen Computer, die dem Abonnement hinzugefügt wurden, identifiziert werden.

### So starten Sie den Ermittlungsprozess

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie dann auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Workloadtyp **Azure Virtual Machine** aus, und klicken Sie dann auf die Schaltfläche **Auswählen**.

    ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie unten auf der Seite auf die Schaltfläche **ERMITTELN**. ![Schaltfläche "Ermitteln"](./media/backup-azure-vms/discover-button-only.png)

4. Der Ermittlungsvorgang kann einige Minuten andauern, während die virtuellen Computer in einer Tabelle aufgeführt werden. Am unteren Bildschirmrand wird eine Popupbenachrichtigung angezeigt, während der Ermittlungsvorgang ausgeführt wird.

    ![VMs ermitteln](./media/backup-azure-vms/discovering-vms.png)

5. Sobald der Ermittlungsvorgang abgeschlossen ist, wird eine Popupbenachrichtigung angezeigt.

    ![Ermitteln abgeschlossen](./media/backup-azure-vms/discovery-complete.png)

##  2\. Registrieren von virtuellen Azure-Computern
Bevor ein virtueller Computer geschützt werden kann, muss er beim Azure Backup-Dienst registriert werden. Das Hauptziel des Registrierungsprozesses besteht darin, den virtuellen Computer dem Azure Backup-Dienst zuzuordnen. Die Registrierung ist in der Regel eine einmalige Angelegenheit.

>[AZURE.NOTE]Die Sicherungserweiterung wird nicht im Rahmen der Registrierung installiert. Die Installation und Aktualisierung des Backup-Agents ist nun Teil des geplanten Sicherungsauftrags.

### So registrieren Sie virtuelle Computer

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie dann auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü als Workloadtyp **Azure Virtual Machine** aus, und klicken Sie dann auf die Schaltfläche "Auswählen".

    ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie unten auf der Seite auf die Schaltfläche **REGISTRIEREN**.

    ![Schaltfläche "Registrieren"](./media/backup-azure-vms/register-button-only.png)

4. Wählen Sie im Kontextmenü **Elemente registrieren** die virtuellen Computer aus, die Sie registrieren möchten. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst zur Unterscheidung zwischen den virtuellen Computern.

    Der Vorgang **Registrieren** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig für die Registrierung ausgewählt werden können. Dadurch wird der einmalige Aufwand zur Vorbereitung des virtuellen Computers für die Sicherung erheblich reduziert.

5. Für jeden virtuellen Computer, der registriert werden soll, wird ein Auftrag erstellt. Die Popupbenachrichtigung zeigt den Status dieser Aktivität an. Klicken Sie auf **Auftrag anzeigen**, um zur Seite **Aufträge** zu wechseln.

    ![Registrierungsauftrag](./media/backup-azure-vms/register-create-job.png)

6. Der virtuelle Computer wird auch in der Liste der registrierten Elemente aufgeführt, und der Status des Registrierungsvorgangs wird angezeigt.

    ![Registrierungsstatus 1](./media/backup-azure-vms/register-status01.png)

7. Sobald der Vorgang abgeschlossen ist, ändert sich der Status im Portal dem registrierten Status entsprechend.

    ![Registrierungsstatus 2](./media/backup-azure-vms/register-status02.png)

## 3\. Schützen: Sichern von virtuellen Azure-Computern
Dieser Schritt umfasst das Einrichten einer Sicherungs- und Aufbewahrungsrichtlinie für den virtuellen Computer. Mehrere virtuelle Computer können effektiv in einem einzigen Schritt geschützt werden.

>[AZURE.NOTE]Azure-Sicherungstresore, die nach Mai 2015 erstellt wurden, enthalten eine im Tresor integrierte Standardrichtlinie. Diese Standardrichtlinie sieht eine Standardaufbewahrungsdauer von 30 Tagen und eine tägliche Sicherung vor.

Um einen virtuellen Computer zu schützen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie dann auf die Registerkarte **Registrierte Elemente**.
2. Wählen Sie im Dropdownmenü als Workloadtyp **Azure Virtual Machine** aus, und klicken Sie dann auf **Auswählen**.

    ![Workload im Portal auswählen](./media/backup-azure-vms/select-workload.png)

3. Klicken Sie unten auf der Seite auf **SCHÜTZEN**. Der Assistent **Elemente schützen** wird angezeigt. Mit diesem Assistenten werden nur virtuelle Computer aufgeführt, die registriert und nicht geschützt sind.

4. Im Assistenten **Elemente schützen** können die zu schützenden virtuellen Computer ausgewählt werden. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst zur Unterscheidung zwischen den virtuellen Computern.

    Der Vorgang **Schützen** kann skaliert werden. Das bedeutet, dass mehrere virtuelle Computer gleichzeitig ausgewählt werden können, um geschützt zu werden. Dadurch wird der Aufwand zum Schützen des virtuellen Computers erheblich reduziert.

    ![Effektives Konfigurieren von Schutzeinstellungen](./media/backup-azure-vms/protect-at-scale.png)

5. Im zweiten Bildschirm des Assistenten **Elemente schützen** wählen Sie einen Sicherungszeitplan für die Sicherung der ausgewählten virtuellen Computer aus. Wählen Sie aus einem vorhandenen Satz von Richtlinien, oder definieren Sie eine neue.

    Jeder Sicherungstresor kann mehrere Sicherungsrichtlinien aufweisen. Die Richtlinien geben die Details zur zeitlichen Planung und Aufbewahrung der Sicherung an. Beispielsweise kann eine Sicherungsrichtlinie eine tägliche Sicherung um 22:00 Uhr festlegen, während eine andere Sicherungsrichtlinie eine wöchentliche Sicherung am Samstag um 6:00 Uhr vorsieht. Mehrere Sicherungsrichtlinien ermöglichen Flexibilität beim Planen von Sicherungen für Ihre virtuelle Infrastruktur.

    Jeder Sicherungsrichtlinie können mehrere virtuelle Computer zugeordnet sein. Der virtuelle Computer kann zu jedem Zeitpunkt immer nur einer Richtlinie zugeordnet sein.

    ![Schützen mit der neuen Richtlinie](./media/backup-azure-vms/policy-schedule.png)

6. Im dritten Bildschirm des Assistenten **Elemente schützen** wählen Sie eine Aufbewahrungsdauer aus, die den Sicherungen zugeordnet werden soll. Dieser Bildschirm unterstützt das GFS-Aufbewahrungsschema (Grandfather-Father-Son) nach Industriestandard. Erfahren Sie mehr über [langfristige Aufbewahrung](#long-term-retention).

    Eine Sicherungsrichtlinie umfasst auch das Aufbewahrungsschema der geplanten Sicherungen. Durch die Auswahl einer vorhandenen Sicherungsrichtlinie im vorherigen Bildschirm wird die Änderung des Aufbewahrungsschemas deaktiviert, und die Sicherungen entsprechen der in der Richtlinie definierten Aufbewahrungsrichtlinie.

    ![Schützen mit flexibler Aufbewahrung](./media/backup-azure-vms/policy-retention.png)

7. Für jeden virtuellen Computer wird ein Auftrag erstellt, um die Schutzrichtlinie zu konfigurieren und die virtuellen Computer der Richtlinie zuzuordnen. Klicken Sie auf die Registerkarte **Aufträge**, und wählen Sie den richtigen Filter zum Anzeigen der Liste mit Aufträgen des Typs **Schutz konfigurieren** aus.

    ![Schutzauftrag konfigurieren](./media/backup-azure-vms/protect-configureprotection.png)


## Nachfolgende Aktivitäten

### Installieren der Sicherungserweiterung
Der Azure Backup-Dienst verarbeitet nahtlos das Upgrade und Patching der Sicherungserweiterung, ohne dass ein Benutzereingriff erforderlich wäre. Dies erspart dem Benutzer den Agent-Verwaltungsaufwand, der in der Regel mit Sicherungsprodukten verbunden ist.

#### Offline-VMs
Die Sicherungserweiterung wird installiert, wenn der virtuelle Computer (Virtual Machine, VM) ausgeführt wird. Bei einem ausgeführten virtuellen Computer besteht zudem die größte Chance auf einen anwendungskonsistenten Wiederherstellungspunkt. Der Azure Backup-Dienst setzt die Sicherung des virtuellen Computers jedoch auch dann fort, wenn der virtuelle Computer ausgeschaltet wurde und die Erweiterung nicht installiert werden konnte (auch als "Offline-VM" bezeichnet). Die Auswirkungen zeigen sich im Bereich der Konsistenz: In diesem Fall ist der Wiederherstellungspunkt *Ausfallsicher*.

### Erste Sicherung
Sobald der virtuelle Computer durch eine Richtlinie geschützt ist, wird er auf der Registerkarte **Geschützte Elemente** angezeigt und weist den Schutzstatus *Geschützt (Anfangssicherung ausstehend)* auf. Standardmäßig ist die erste geplante Sicherung die Anfangssicherung. Um die erste Sicherung sofort nach dem Konfigurieren des Schutzes auszulösen, klicken Sie am unteren Rand der Seite **Geschützte Elemente** auf die Schaltfläche **Jetzt sichern**.

Der Azure Backup-Dienst erstellt einen Sicherungsauftrag für die erste Sicherung. Klicken Sie auf die Registerkarte **Aufträge**, um die Liste der Aufträge anzuzeigen. Als Teil des Sicherungsvorgangs gibt der Azure Backup-Dienst einen Befehl an die Sicherungserweiterung auf jedem virtuellen Computer aus, damit alle Schreibvorgänge geleert werden und eine konsistente Momentaufnahme erstellt wird.

![Sicherung wird ausgeführt](./media/backup-azure-vms/protect-inprogress.png)

Sobald die erste Sicherung abgeschlossen ist, wird der *Schutzstatus* des virtuellen Computers auf der Registerkarte **Geschützte Elemente** als *Geschützt* angezeigt.

![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/protect-backedupvm.png)

### Anzeigen von Status und Details der Sicherung
Sobald der Schutz eingerichtet ist, steigt die Anzahl der virtuellen Computer auch auf der **Dashboard**-Zusammenfassungsseite. Darüber hinaus wird auf der Seite **Dashboard** die Anzahl der Aufträge der letzten 24 Stunden angezeigt, die erfolgreich waren, Fehler verursacht haben oder noch ausgeführt werden. Durch Klicken auf eine beliebige Kategorie wird auf der Seite **Aufträge** ein Drilldown in diese Kategorie durchgeführt.

![Status der Sicherung auf der Dashboard-Seite](./media/backup-azure-vms/dashboard-protectedvms.png)

>[AZURE.NOTE]Werte im Dashboard werden einmal alle 24 Stunden aktualisiert.

### Langfristige Aufbewahrung
Die Aufbewahrungsrichtlinie gibt die Dauer an, für die die Sicherung gespeichert werden muss. Anstatt für alle Sicherungspunkte lediglich eine Aufbewahrungsdauer anzugeben, können Kunden je nach Sicherungszeitpunkt verschiedene Aufbewahrungsrichtlinien festlegen. Beispiel: Der Sicherungspunkt am Ende jedes Quartals muss für Audits möglicherweise länger aufbewahrt werden als der tägliche Sicherungspunkt (der als Wiederherstellungspunkt für den alltäglichen Betrieb dient), der 90 Tage lang aufbewahrt werden muss.

![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/long-term-retention.png)

1. **Tägliche Aufbewahrungsrichtlinie**: Täglich erstellte Sicherungen werden 30 Tage lang gespeichert.
2. **Wöchentliche Aufbewahrungsrichtlinie**: Sicherungen, die jeden Sonntag erstellt werden, werden 104 Wochen lang aufbewahrt.
3. **Monatliche Aufbewahrungsrichtlinie**: Sicherungen, die am letzten Samstag im Monat erstellt werden, werden 120 Monate lang aufbewahrt.
4. **Jährliche Aufbewahrungsrichtlinie**: Sicherungen, die am ersten Sonntag im Januar erstellt werden, werden 99 Jahre lang aufbewahrt.

## Konsistenz der Wiederherstellungspunkte
Beim Umgang mit Sicherungsdaten machen sich Kunden Gedanken über das Verhalten des virtuellen Computers nach der Wiederherstellung. Typische Kundenfragen sind:

- Kann der virtuelle Computer gestartet werden?
- Stehen Daten auf dem Datenträger zur Verfügung (oder) gibt es Datenverluste?
- Ist die Anwendung in der Lage, die Daten zu lesen (oder) sind die Daten beschädigt?
- Können die Daten von der Anwendung verwertet werden (oder) sind die Daten in sich konsistent, wenn sie von der Anwendung gelesen werden?

In der folgenden Tabelle werden die Arten der Konsistenz aufgeführt, die bei der Azure-VM-Sicherung und -Wiederherstellung relevant sind:

| Konsistenz | VSS-basiert | Erklärung und Details |
|-------------|-----------|---------|
| Anwendungskonsistenz | Ja | Dies ist der ideale Ort für Microsoft-Workloads, da Folgendes gewährleistet wird:<ol><li> Der virtuelle Computer wird *hochgefahren*. <li>Es gibt *keine Datenbeschädigung*. <li>Es gibt *keinen Datenverlust*.<li> Die Daten sind mit der Anwendung konsistent, in der die Daten verwendet werden, da die Anwendung zum Zeitpunkt der Sicherung einbezogen wird – mithilfe von VSS.</ol> Durch den Volumemomentaufnahmedienst (Volume Snapshot Service, VSS) wird sichergestellt, dass die Daten ordnungsgemäß in den Speicher geschrieben werden. Die meisten Microsoft-Workloads verfügen über VSS Writer, die Workload-spezifische Aktionen im Zusammenhang mit der Datenkonsistenz ausführen. Beispielsweise umfasst Microsoft SQL Server einen VSS Writer, der die ordnungsgemäße Durchführung der Schreibvorgänge in die Transaktionsprotokolldatei und die Datenbank gewährleistet.<br><br> Für die Azure-VM-Sicherung bedeutet das Erhalten eines anwendungskonsistenten Wiederherstellungspunkts, dass die Sicherungserweiterung den VSS-Workflow aufrufen und *ordnungsgemäß* abschließen konnte, bevor die Momentaufnahme des virtuellen Computers erstellt wurde. Dies heißt natürlich, dass die VSS Writer aller Anwendungen auf dem virtuellen Azure-Computer ebenfalls aufgerufen wurden.<br><br>Erlernen Sie die [Grundlagen von VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), und erlangen Sie detaillierte Kenntnisse zur [Funktionsweise](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Dateisystemkonsistenz | Ja, für Windows-Computer | Es gibt zwei Szenarien, in denen der Wiederherstellungspunkt dateisystemkonsistent sein kann:<ul><li>Sicherung virtueller Linux-Computer in Azure, da Linux über keine entsprechende Plattform wie VSS verfügt.<li>VSS-Fehler bei der Sicherung für virtuelle Windows-Computer in Azure.</li></ul> In beiden Fällen ist es das beste, Folgendes sicherzustellen: <ol><li> Der virtuelle Computer wird *hochgefahren*. <li>Es gibt *keine Datenbeschädigung*.<li>Es gibt *keinen Datenverlust*.</ol> Anwendungen müssen einen eigenen Reparaturmechanismus für die wiederhergestellten Daten implementieren.|
| Absturzkonsistenz | Nein | Diese Situation entspricht dem Absturz eines Computers (durch eine Teil- oder Vollrückstellung). Dies geschieht normalerweise, wenn zum Zeitpunkt der Sicherung der virtuelle Azure-Computer heruntergefahren wird. Für die Sicherung virtueller Azure-Computer bedeutet das Erhalten eines ausfallsicheren Wiederherstellungspunkts, dass Azure Backup keine Garantie für die Konsistenz der Daten auf dem Speichermedium gewährt – weder im Hinblick auf das Betriebssystem noch im Hinblick auf die Anwendung. Nur Daten, die zum Zeitpunkt der Sicherung bereits auf dem Datenträger vorhanden sind, werden erfasst und gesichert. <br/> <br/> Auch wenn es keine Garantie gibt, wird das Betriebssystem in den meisten Fällen gestartet. In der Regel folgt eine Prozedur zur Datenträgerüberprüfung wie „chkdsk“, um eine mögliche Datenbeschädigung zu reparieren. Alle Daten im Arbeitsspeicher oder Schreibvorgänge, die nicht vollständig auf den Datenträger übertragen wurden, gehen verloren. Die Anwendung folgt normalerweise mit einem eigenen Überprüfungsmechanismus, falls ein Datenrollback durchgeführt werden muss. Für die Sicherung virtueller Azure-Computer bedeutet das Erhalten eines ausfallsicheren Wiederherstellungspunkts, dass Azure Backup keine Garantie für die Konsistenz der Daten im Speicher gewährt – weder im Hinblick auf das Betriebssystem noch im Hinblick auf die Anwendung. Dies geschieht in der Regel, wenn der virtuelle Azure-Computer zum Zeitpunkt der Sicherung heruntergefahren wird.<br><br>Wenn das Transaktionsprotokoll beispielsweise Einträge enthält, die nicht in der Datenbank vorhanden sind, führt die Datenbanksoftware einen Rollback durch, bis die Daten konsistent sind. Beim Umgang mit Daten, die über mehrere virtuelle Laufwerke verteilt sind (z. B. übergreifende Volumes), bietet ein absturzkonsistenter Wiederherstellungspunkt keine Garantie für die Richtigkeit der Daten.|


## Leistung und Ressourcenverwendung
Wie bei lokal bereitgestellter Sicherungssoftware muss die Sicherung von virtuellen Computern in Azure auch im Hinblick auf Kapazität und Ressourcenverwendung geplant werden. Die [Azure Storage-Begrenzungen](azure-subscription-service-limits.md#storage-limits) definieren, wie die VM-Bereitstellungen strukturiert werden, um maximale Leistung bei minimaler Beeinträchtigung der ausgeführten Workloads zu erzielen. Die beiden wichtigsten Azure-Speicherbegrenzungen, die sich auf die Sicherungsleistung auswirken, sind:

- Max. Ausgang pro Speicherkonto
- Gesamtanforderungsrate pro Speicherkonto

Wenn Sicherungsdaten aus dem Kundenspeicherkonto kopiert werden, werden diese auf die Metriken des Speicherkontos für IOPS und Ausgang (Speicherdurchsatz) angerechnet. Zur gleichen Zeit werden die virtuellen Computer ausgeführt und verbrauchen ebenfalls IOPS und Durchsatz. Das Ziel ist, dafür zu sorgen, dass die Gesamtheit des Datenverkehrs – d. h. Sicherung und virtuelle Computer – die Speicherkontobegrenzungen nicht überschreitet.

Sicherungen sind "gierig" und versuchen stets, alle verfügbaren Ressourcen zu nutzen, da die Sicherung möglichst schnell zum Abschluss gebracht werden soll. Sämtliche E/A-Vorgänge sind jedoch begrenzt durch den *Zieldurchsatz pro Einzelblob*, der auf *60 MB pro Sekunde* beschränkt ist. Zur Beschleunigung der Sicherung wird versucht, die einzelnen Datenträger des virtuellen Computers *parallel* zu sichern. Azure Backup versucht also bei einem virtuellen Computer mit vier Datenträgern, alle vier Datenträger parallel zu sichern. Der wichtigste Faktor, der den ausgehenden Sicherungsdatenverkehr für ein Kundenspeicherkonto bestimmt, ist daher die **Anzahl der Datenträger**, die über das Speicherkonto gesichert werden.

Ein weiterer Faktor, der die Leistung beeinträchtigt, ist der **Sicherungszeitplan**. Wenn Sie alle virtuellen Computer für eine gleichzeitige Sicherung konfigurieren, erhöht sich die Anzahl der *parallel* gesicherten Datenträger, da Azure Backup versucht, so viele Datenträger wie möglich zu sichern. Eine Möglichkeit zur Reduzierung des Sicherungsdatenverkehrs für ein Speicherkonto ist daher, die einzelnen virtuellen Computer zu unterschiedlichen Tageszeiten zu sichern und Überlappungen zu vermeiden.

### Kapazitätsplanung
Zusammenfassend folgt aus diesen Faktoren, dass die Speicherkontoverwendung einer gründlichen Planung bedarf. Laden Sie das [Excel-Arbeitsblatt zur Kapazitätsplanung für VM-Sicherungen](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) herunter, um die Auswirkungen Ihrer Datenträger- und Sicherungszeitplanauswahl zu überprüfen.

### Sicherungsdurchsatz
Für jeden zu sichernden Datenträger liest Azure Backup die Blöcke auf dem Datenträger und speichert nur die geänderten Daten (inkrementelle Sicherung). Die folgende Tabelle zeigt durchschnittliche Durchsatzwerte, die bei einer Sicherung mit Azure Backup zu erwarten sind:

| Sicherungsvorgang | Optimaler Durchsatz |
| ---------------- | ---------- |
| Erste Sicherung | 160 MBit/s |
| Inkrementelle Sicherung (DR) | 640 MBit/s <br><br> Dieser Durchsatz kann erheblich sinken, wenn auf dem Datenträger viele verstreute Daten gesichert werden müssen. |

Anhand dieser Informationen können Sie die Zeitdauer schätzen, die zur Sicherung eines Datenträgers mit einer bestimmten Größe benötigt wird.

### Gesamtdauer der VM-Sicherung
Obgleich ein Großteil der Zeit für das Lesen und Kopieren von Daten aufgewendet wird, gibt es noch andere Vorgänge, die sich auf die Gesamtdauer einer VM-Sicherung auswirken:

1. Die Zeit, die zum [Installieren oder Aktualisieren der Sicherungserweiterung](backup-azure-vms.md#offline-vms) benötigt wird.
2. Die Wartezeit in der Warteschlange: Da der Sicherungsdienst Sicherungen von mehreren Kunden verarbeitet, kann Ihr Sicherungsvorgang möglicherweise nicht sofort gestartet werden. Während Spitzenauslastungszeiten können die Wartezeiten aufgrund der Anzahl der verarbeiteten Sicherungen bis zu 8 Stunden betragen. Die Gesamtdauer der VM-Sicherung wird jedoch bei täglichen Sicherungsrichtlinien weniger als 24 Stunden betragen.

## Problembehandlung
Hier finden Sie eine vollständige Liste von Problemumgehungen für Fehler, die bei der Sicherung eines virtuellen Computers auftreten können:

- [Problembehandlung bei der Sicherung virtueller Computer](backup-azure-vms-troubleshoot.md)

## Nächste Schritte
Weitere Informationen zum Einstieg in Azure Backup finden Sie unter:

- [Wiederherstellen virtueller Computer](backup-azure-restore-vms.md)
- [Verwalten virtueller Computer](backup-azure-manage-vms.md)

<!---HONumber=Oct15_HO3-->