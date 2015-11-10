<properties
	pageTitle="Sichern virtueller Azure-Computer | Microsoft Azure"
	description="Verfahren zum Sichern eines virtuellen Azure-Computers."
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
	ms.topic="hero-article"
	ms.date="10/29/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>


# Sichern von virtuellen Azure-Computern
In diesem Artikel werden die Verfahren zum Sichern vorhandener virtueller Computer in Azure erläutert, um diese in Übereinstimmung mit den Sicherungs- und Aufbewahrungsrichtlinien Ihres Unternehmens zu schützen.

Zunächst gibt einige Dinge, die Sie erledigen müssen, bevor Sie einen virtuellen Azure-Computer sichern können. Falls noch nicht erfolgt, erfüllen Sie die [Voraussetzungen](backup-azure-vms-prepare.md) zur Vorbereitung Ihrer Umgebung auf die VM-Sicherung, ehe Sie fortfahren.

Wenn Sie Informationen zum [Planen der VM-Sicherungsinfrastruktur in Azure](backup-azure-vms-introduction.md) oder zu [virtuellen Azure-Computern](https://azure.microsoft.com/documentation/services/virtual-machines/) suchen, folgen Sie diesen Links.

Zur Sicherung virtueller Azure-Computer gehören drei Hauptschritte:

![Drei Schritte zum Sichern eines virtuellen Azure-Computers](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE]Die Sicherung virtueller Computer erfolgt lokal. Sie können keine virtuellen Computer aus einer Region in einem Sicherungstresor in einer anderen Region sichern. Deshalb muss in jeder Azure-Region mit virtuellen Computern, die eine Sicherung erfordern, mindestens ein Sicherungstresor erstellt werden.

## Schritt 1: Ermitteln virtueller Azure-Computer
Der Ermittlungsprozess muss immer als erster Schritt erfolgen, um sicherzustellen, dass alle neuen virtuellen Computer, die dem Abonnement hinzugefügt wurden, identifiziert werden. Bei diesem Vorgang wird Azure nach der Liste virtueller Computer im Abonnement abgefragt. Außerdem werden zusätzliche Informationen wie der Clouddienstname und die Region erfasst.

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü **Azure Virtual Machine** aus.

    ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie unten auf der Seite auf **ERMITTELN**. ![Schaltfläche "Ermitteln"](./media/backup-azure-vms/discover-button-only.png)

    Der Ermittlungsvorgang kann einige Minuten andauern, während die virtuellen Computer in einer Tabelle aufgeführt werden. Es erfolgt am unteren Rand des Bildschirms eine Benachrichtigung, die Ihnen mitteilt, dass der Vorgang ausgeführt wird.

    ![VMs ermitteln](./media/backup-azure-vms/discovering-vms.png)

    Die Benachrichtigung ändert sich, sobald der Vorgang abgeschlossen ist.

    ![Ermitteln abgeschlossen](./media/backup-azure-vms/discovery-complete.png)

##  Schritt 2: Registrieren virtueller Azure-Computer
Sie registrieren einen virtuellen Azure-Computer, um ihn dem Azure Backup-Dienst zuzuordnen. Die Registrierung ist in der Regel eine einmalige Angelegenheit.

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.

2. Wählen Sie im Dropdownmenü **Azure Virtual Machine** aus.

    ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

3. Klicken Sie unten auf der Seite auf **REGISTRIEREN**. ![Schaltfläche "Registrieren"](./media/backup-azure-vms/register-button-only.png)

4. Wählen Sie im Kontextmenü **Elemente registrieren** die virtuellen Computer aus, die Sie registrieren möchten. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst, um sie unterscheiden.

    >[AZURE.TIP]Mehrere virtuelle Computer können gleichzeitig registriert werden.

    Für jeden virtuellen Computer, den Sie ausgewählt haben, wird ein Auftrag erstellt.

5. Klicken Sie in der Benachrichtigung auf **Auftrag anzeigen**, um zur Seite **Aufträge** zu gelangen.

    ![Registrierungsauftrag](./media/backup-azure-vms/register-create-job.png)

   Der virtuelle Computer wird auch in der Liste der registrierten Elemente aufgeführt, und der Status des Registrierungsvorgangs wird angezeigt.

    ![Registering status 1](./media/backup-azure-vms/register-status01.png)

    When the operation completes, the status will change to reflect the *registered* state.

    ![Registration status 2](./media/backup-azure-vms/register-status02.png)

## Schritt 3: Schützen virtueller Azure-Computer
Nun können Sie die Sicherungs- und Aufbewahrungsrichtlinie für den virtuellen Computer definieren. Mehrere virtuelle Computer können in einem einzigen Schritt geschützt werden.

Azure-Sicherungstresore, die nach Mai 2015 erstellt wurden, enthalten eine im Tresor integrierte Standardrichtlinie. Diese Standardrichtlinie sieht eine Standardaufbewahrungsdauer von 30 Tagen und eine tägliche Sicherung vor.

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie auf die Registerkarte **Registrierte Elemente**.
2. Wählen Sie im Dropdownmenü **Azure Virtual Machine** aus.

    ![Workload im Portal auswählen](./media/backup-azure-vms/select-workload.png)

3. Klicken Sie unten auf der Seite auf **SCHÜTZEN**.

    Der Assistent **Elemente schützen** wird angezeigt. Mit diesem Assistenten werden nur virtuelle Computer aufgeführt, die registriert und nicht geschützt sind. Wählen Sie hier die virtuellen Computer aus, die Sie schützen möchten.

    Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst zur Unterscheidung zwischen den virtuellen Computern.

    >[AZURE.TIP]Sie können mehrere virtuelle Computer gleichzeitig schützen.

    ![Effektives Konfigurieren von Schutzeinstellungen](./media/backup-azure-vms/protect-at-scale.png)

4. Wählen Sie einen **Sicherungszeitplan** zum Sichern der virtuellen Computer aus, die Sie ausgewählt haben. Sie können aus einem vorhandenen Satz von Richtlinien wählen oder einen neuen definieren.

    Jeder Sicherungsrichtlinie können mehrere virtuelle Computer zugeordnet sein. Der virtuelle Computer kann jedoch immer nur einer Richtlinie zugeordnet sein.

    ![Schützen mit der neuen Richtlinie](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE]Eine Sicherungsrichtlinie umfasst auch ein Aufbewahrungsschema der geplanten Sicherungen. Bei Wahl einer vorhandenen Sicherungsrichtlinie können Sie die Aufbewahrungsoptionen im nächsten Schritt nicht ändern.

5. Wählen Sie eine **Aufbewahrungsdauer** für die Sicherungen aus.

    ![Schützen mit flexibler Aufbewahrung](./media/backup-azure-vms/policy-retention.png)

    Die Aufbewahrungsrichtlinie gibt die Dauer der Speicherung einer Sicherung an. Sie können basierend auf dem Zeitpunkt der Erstellung der Sicherung unterschiedliche Aufbewahrungsrichtlinien angegeben. Beispiel: Der Sicherungspunkt am Ende jedes Quartals muss für Audits möglicherweise länger aufbewahrt werden als der tägliche Sicherungspunkt (der als Wiederherstellungspunkt für den alltäglichen Betrieb dient), der nur 90 Tage aufbewahrt werden muss.

    ![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/long-term-retention.png)

    In dieser Beispielabbildung:

    - **Aufbewahrungsrichtlinie „Täglich“**: Täglich erstellte Sicherungen werden 30 Tage gespeichert.
    - **Aufbewahrungsrichtlinie „Wöchentlich“**: Sicherungen, die jeden Sonntag erstellt werden, werden 104 Wochen aufbewahrt.
    - **Aufbewahrungsrichtlinie „Monatlich“**: Sicherungen, die am letzten Samstag im Monat erstellt werden, werden 120 Monate aufbewahrt.
    - **Aufbewahrungsrichtlinie „Jährlich“**: Sicherungen, die am ersten Sonntag im Januar erstellt werden, werden 99 Jahre aufbewahrt.

    Ein Auftrag wird erstellt, um die Schutzrichtlinie zu konfigurieren und die virtuellen Computer, die Sie ausgewählt haben, dieser Richtlinie zuzuordnen.

6. Klicken Sie auf die Registerkarte **Auftrag**, und wählen Sie den richtigen Filter zum Anzeigen der Liste mit Aufträgen des Typs **Schutz konfigurieren** aus.

    ![Schutzauftrag konfigurieren](./media/backup-azure-vms/protect-configureprotection.png)

## Erste Sicherung
Sobald der virtuelle Computer durch eine Richtlinie geschützt ist, wird er auf der Registerkarte **Geschützte Elemente** angezeigt und weist den Schutzstatus *Geschützt (erste Sicherung ausstehend)* auf. Standardmäßig ist die erste geplante Sicherung auch die *erste Sicherung*.

So lösen Sie die erste Sicherung unmittelbar nach Konfigurieren des Schutzes aus:

1. Klicken Sie am unteren Rand der Seite **Geschützte Elemente** auf die Schaltfläche **Jetzt sichern**.

    Der Azure Backup-Dienst erstellt einen Sicherungsauftrag für die erste Sicherung.

2. Klicken Sie auf die Registerkarte **Aufträge**, um die Liste der Aufträge anzuzeigen.

    ![Sicherung wird ausgeführt](./media/backup-azure-vms/protect-inprogress.png)

>[AZURE.NOTE]Als Teil des Sicherungsvorgangs gibt der Azure Backup-Dienst einen Befehl an die Sicherungserweiterung auf jedem virtuellen Computer aus, damit alle Schreibvorgänge geleert werden und eine konsistente Momentaufnahme erstellt wird.

Sobald die erste Sicherung abgeschlossen ist, wird der Status des virtuellen Computers auf der Registerkarte **Geschützte Elemente** als *Geschützt* angezeigt.

![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/protect-backedupvm.png)

## Anzeigen von Status und Details der Sicherung
Sobald der Schutz eingerichtet ist, steigt die Anzahl der virtuellen Computer auch auf der **Dashboard**-Zusammenfassungsseite. Auf der Seite **Dashboard** wird auch die Anzahl der Aufträge der letzten 24 Stunden angezeigt, die *erfolgreich* waren, *Fehler* verursacht haben oder *noch ausgeführt* werden. Durch Klicken auf eine beliebige Kategorie wird auf der Seite **Aufträge** ein Drilldown in diese Kategorie durchgeführt.

![Status der Sicherung auf der Dashboard-Seite](./media/backup-azure-vms/dashboard-protectedvms.png)

Werte im Dashboard werden einmal alle 24 Stunden aktualisiert.

## Problembehandlung
Wenn beim Sichern des virtuellen Computers Probleme auftreten, finden Sie in dieser Anleitung zur [Problembehandlung](backup-azure-vms-troubleshoot.md) Hilfe.

## Nächste Schritte

- [Verwalten und Überwachen Ihrer virtuellen Computer](backup-azure-manage-vms.md)
- [Wiederherstellen virtueller Computer](backup-azure-restore-vms.md)

<!---HONumber=Nov15_HO2-->