<properties
	pageTitle="Schützen virtueller Computer in Azure mit Azure Backup | Microsoft Azure"
	description="Schützen virtueller Azure-Computer mit dem Azure Backup-Dienst Dieses Tutorial erläutert das Erstellen von Tresoren, das Registrieren virtueller Computer, das Erstellen von Richtlinien und das Schützen virtueller Computer in Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/14/2016"
	ms.author="markgal; jimpark"/>


# Einführung: Sichern von virtuellen Azure-Computern

Dieser Artikel ist ein Tutorial, in dem die Schritte zum Vorbereiten Ihrer Azure-Umgebung zum Sichern eines virtuellen Azure-Computers erläutert werden. Dieses Tutorial setzt voraus, dass Sie in Ihrem Azure-Abonnement bereits einen virtuellen Computer verwenden und die entsprechenden Schritte durchgeführt haben, um dem Backup-Dienst den Zugriff auf den virtuellen Computer zu erlauben. Dies sind im Allgemeinen die Schritte, die auszuführen sind.

![Allgemeiner Überblick über den Sicherungsvorgang für virtuelle Computer](./media/backup-azure-vms-first-look/BackupAzureVM.png)


1. Erstellen Sie ein Azure-Abonnement, oder melden Sie sich bei Ihrem Azure-Abonnement an.
2. Erstellen Sie einen Sicherungstresor, oder geben Sie einen vorhandenen Sicherungstresor an. Dieser muss sich *in der gleichen Region wie Ihr virtueller Computer* befinden.
3. Verwenden Sie das Azure-Portal, um die virtuellen Computer im Abonnement zu ermitteln und zu registrieren.
4. Installieren Sie den VM-Agent auf dem virtuellen Computer (wenn Sie einen virtuellen Computer aus dem Azure-Katalog verwenden, ist der VM-Agent bereits vorhanden).
5. Erstellen Sie die Richtlinie für den Schutz der virtuellen Computer.
6. Führen Sie die Sicherung aus.

>[AZURE.NOTE] Azure verfügt über zwei Bereitstellungsmodelle zum Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../resource-manager-deployment-model.md). Der Azure Backup-Dienst unterstützt derzeit keine virtuellen Computer, die auf dem Azure Resource Manager (ARM) basieren (auch als virtuelle IaaS V2-Computer bezeichnet). Da virtuelle IaaS V2-Computer mit der neuen Version des Azure-Portals eingeführt wurden, ist dieses Tutorial für die Verwendung mit dem Typ virtueller Computer gedacht, die im klassischen Azure-Portal erstellt werden können.


## Schritt 1 – Erstellen eines Sicherungstresors für einen virtuellen Computer

Bei einem Sicherungstresor handelt es sich um eine Entität, in der alle Sicherungen und Wiederherstellungspunkte gespeichert werden, die im Laufe der Zeit erstellt wurden. Der Sicherungstresor enthält auch die Sicherungsrichtlinien, die auf die zu sichernden virtuellen Computer angewendet werden.

In der Abbildung sind die Beziehungen zwischen den verschiedenen Azure Backup-Entitäten dargestellt: ![Azure Backup-Entitäten und ihre Beziehungen](./media/backup-azure-vms-prepare/vault-policy-vm.png)

So erstellen Sie einen Sicherungstresor

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com/) an.

2. Klicken Sie im Azure-Portal auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor** > **Schnellerfassung** (siehe Abbildung unten).

    ![Erstellen des Sicherungstresors](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

3. Geben Sie unter **Name** einen Anzeigenamen für den Tresor ein. Der Name muss für das Azure-Abonnement eindeutig sein. Geben Sie einen Namen ein, der zwischen 2 und 50 Zeichen enthält. Er muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Bindestriche enthalten.

4. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Der Tresor **muss** sich in der gleichen Region wie die zu schützenden virtuellen Computer befinden.

    Wenn Sie sich nicht sicher sind, in welcher Region sich Ihr virtueller Computer befindet, schließen Sie das Dialogfeld zur Tresorerstellung, und wechseln Sie zur Liste der virtuellen Computer im Portal. Wenn sich Ihre virtuellen Computer in verschiedenen Regionen befinden, müssen Sie in jeder Region einen Sicherungstresor erstellen. Schließen Sie die Erstellung des Tresors jedoch in der ersten Region ab, bevor Sie zur nächsten wechseln. Es müssen keine Speicherkonten zum Speichern der Sicherungsdaten angegeben werden. Dies erfolgt automatisch über den Sicherungstresor und den Azure Backup-Dienst.

5. Wählen Sie unter **Abonnement** das Abonnement aus, das dem Sicherungstresor zugeordnet werden soll. Es sind nur dann mehrere Auswahlmöglichkeiten verfügbar, wenn Ihr Organisationskonto mehreren Azure-Abonnements zugeordnet ist.

6. Klicken Sie auf **Tresor erstellen**. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Unten im Portal können Sie anhand der Benachrichtigungen den Status prüfen.

    ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-azure-vms-first-look/create-vault-demo.png)

    In einer Meldung wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird auf der Seite **Recovery Services** als **Aktiv** aufgelistet.

    ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

7. Klicken Sie in der Liste der Tresore auf der Seite **Recovery Services** auf den erstellten Tresor, um die Seite **Schnellstart** zu öffnen.

    ![Liste der Sicherungstresore](./media/backup-azure-vms-first-look/active-vault-demo.png)

8. Klicken Sie auf der Seite **Schnellstart** auf **Konfigurieren**, um die Option für die Speicherreplikation zu öffnen. ![Liste der Sicherungstresore](./media/backup-azure-vms-first-look/configure-storage.png)

9. Wählen Sie in der Option **Speicherreplikation** die Replikationsoption für Ihren Tresor aus.

    ![Liste der Sicherungstresore](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Standardmäßig verfügt Ihr Tresor über einen georedundanten Speicher. Wenn Sie Azure als primären Speicherendpunkt für die Sicherung verwenden, wird empfohlen, dass Sie den georedundanten Speicher weiterhin empfehlen. Wenn Sie Azure als nicht primären Speicherendpunkt für die Sicherung verwenden, können Sie auch den lokal redundanten Speicher wählen, was die Kosten für die Datenspeicherung in Azure verringert. In dieser [Übersicht](../storage/storage-redundancy.md) erfahren Sie mehr über die Optionen für [georedundante](../storage/storage-redundancy.md#geo-redundant-storage) und [lokal redundante](../storage/storage-redundancy.md#locally-redundant-storage) Speicher.

Wenn Sie die Speicheroption für Ihren Tresor ausgewählt haben, können Sie den virtuellen Computer dem Tresor zuordnen. Ermitteln und registrieren Sie die virtuellen Azure-Computer, um mit der Zuordnung zu beginnen.

## Schritt 2 – Ermitteln und Registrieren virtueller Azure-Computer
Führen Sie zunächst den Ermittlungsprozess durch, bevor Sie einen virtuellen Computer registrieren, um sicherzustellen, dass alle neuen virtuellen Computer, die dem Abonnement hinzugefügt wurden, identifiziert werden. Bei diesem Vorgang wird Azure nach der Liste virtueller Computer im Abonnement abgefragt. Außerdem werden zusätzliche Informationen wie der Clouddienstname und die Region erfasst.

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com/) an.

2. Klicken Sie im klassischen Azure-Portal auf **Recovery Services**, um die Liste der Recovery Services-Tresore zu öffnen. ![Workload auswählen](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. Wählen Sie in der Liste der **Recovery Services**-Tresore den Tresor aus, den Sie zum Sichern eines virtuellen Computers verwenden möchten.

    Wenn Sie Ihren Tresor auswählen, wird er auf der Seite **Schnellstart** geöffnet.

4. Klicken Sie im Tresormenü (oben auf der Seite) auf **Registrierte Elemente**.

5. Wählen Sie im Menü **Typ** die Option **Virtueller Azure-Computer** aus.

    ![Workload auswählen](./media/backup-azure-vms/discovery-select-workload.png)

6. Klicken Sie unten auf der Seite auf **ERMITTELN**. ![Schaltfläche „Ermitteln“](./media/backup-azure-vms/discover-button-only.png)

    Der Ermittlungsvorgang kann einige Minuten dauern, während die virtuellen Computer in einer Tabelle aufgeführt werden. Am unteren Rand des Bildschirms wird eine Benachrichtigung angezeigt, die Ihnen mitteilt, dass der Vorgang ausgeführt wird.

    ![VMs ermitteln](./media/backup-azure-vms/discovering-vms.png)

    Die Benachrichtigung ändert sich, sobald der Vorgang abgeschlossen ist.

    ![Ermittlung abgeschlossen](./media/backup-azure-vms-first-look/discovery-complete.png)

7. Klicken Sie unten auf der Seite auf **REGISTRIEREN**. ![Schaltfläche „Registrieren“](./media/backup-azure-vms-first-look/register-icon.png)

8. Wählen Sie im Kontextmenü **Elemente registrieren** die virtuellen Computer aus, die Sie registrieren möchten. Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst, um sie unterscheiden.

    >[AZURE.TIP] Mehrere virtuelle Computer können gleichzeitig registriert werden.

    Für jeden virtuellen Computer, den Sie ausgewählt haben, wird ein Auftrag erstellt.

9. Klicken Sie in der Benachrichtigung auf **Auftrag anzeigen**, um zur Seite **Aufträge** zu gelangen.

    ![Registrierungsauftrag](./media/backup-azure-vms/register-create-job.png)

    Der virtuelle Computer wird auch in der Liste der registrierten Elemente aufgeführt, ebenso wie der Status des Registrierungsvorgangs.

    ![Registrierungsstatus 1](./media/backup-azure-vms/register-status01.png)

    Wenn der Vorgang abgeschlossen ist, ändert sich der Status dem *registrierten* Status entsprechend.

    ![Registrierungsstatus 2](./media/backup-azure-vms/register-status02.png)

## Schritt 3 – Installieren Sie den VM-Agent auf dem virtuellen Computer.

Der Azure VM-Agent muss auf dem virtuellen Azure-Computer installiert werden, damit die Sicherungserweiterung funktioniert. Wenn Ihr virtueller Computer aus dem Azure-Katalog erstellt wurde, ist der VM-Agent auf dem virtuellen Computer bereits vorhanden. Auf virtuellen Computern, die aus lokalen Datencentern migriert werden, ist der VM-Agent jedoch nicht installiert. In diesem Fall muss der VM-Agent explizit installiert werden. Bevor Sie einen virtuellen Azure-Computer sichern können, müssen Sie sicherstellen, dass der Azure VM-Agent auf dem virtuellen Computer korrekt installiert ist (s. Tabelle unten). Wenn Sie einen benutzerdefinierten virtuellen Computer erstellen, [müssen Sie das Kontrollkästchen **VM-Agent installieren** aktivieren](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md), bevor Sie den virtuellen Computer bereitstellen.

Erfahren Sie mehr über den [VM-Agent](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) und [seine Installation](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md) (in englischer Sprache).

Die folgende Tabelle enthält weitere Informationen zum VM-Agent für virtuelle Windows- und Linux-Computer.

| **Vorgang** | **Windows** | **Linux** |
| --- | --- | --- |
| Installieren des VM-Agent | <li>Laden Sie den [Agent-MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie ihn. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen. <li>[Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), um anzugeben, dass der Agent installiert wurde. | <li>Installieren Sie den neuesten [Linux-Agent](https://github.com/Azure/WALinuxAgent) aus GitHub. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen. <li> [Aktualisieren Sie die VM-Eigenschaft](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), um anzugeben, dass der Agent installiert wurde. |
| Aktualisieren des VM-Agents | Das Aktualisieren des VM-Agents ist so einfach wie das Neuinstallieren der [Binärdateien für den VM-Agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Stellen Sie sicher, dass kein Sicherungsvorgang ausgeführt wird, während der VM-Agent aktualisiert wird. | Folgen Sie den Anweisungen unter [Aktualisieren des Linux-VM-Agent](../virtual-machines-linux-update-agent.md). <br>Stellen Sie sicher, dass kein Sicherungsvorgang ausgeführt wird, während der VM-Agent aktualisiert wird. |
| Überprüfen der VM-Agent-Installation | <li>Navigieren Sie auf dem virtuellen Azure-Computer zum Ordner *C:\\WindowsAzure\\Packages*. <li>Dieser Ordner enthält die Datei „WaAppAgent.exe“.<li> Klicken Sie mit der rechten Maustaste auf die Datei, wechseln Sie zu **Eigenschaften**, und wählen Sie dann die Registerkarte **Details** aus. Im Feld mit der Produktversion sollte 2.6.1198.718 oder eine höhere Version angegeben sein. | N/V |


### Backup-Erweiterung

Sobald der VM-Agent auf dem virtuellen Computer installiert wurde, installiert der Azure Backup-Dienst die Sicherungserweiterung für den VM-Agent. Der Azure Backup-Dienst installiert nahtlos und ohne zusätzlichen Benutzereingriff Upgrades und Patches für die Backup-Erweiterung.

Die Sicherungserweiterung wird vom Sicherungsdienst installiert, unabhängig davon, ob der virtuelle Computer ausgeführt wird oder nicht. Bei einem ausgeführten virtuellen Computer besteht zudem die größte Chance auf einen anwendungskonsistenten Wiederherstellungspunkt. Der Azure Backup-Dienst setzt die Sicherung des virtuellen Computers jedoch auch dann fort, wenn dieser ausgeschaltet wurde und die Erweiterung nicht installiert werden konnte. Dies wird als Offline-VM bezeichnet. In diesem Fall ist der Wiederherstellungspunkt *absturzkonsistent*.


## Schritt 4 – Schützen virtueller Azure-Computer
Nun können Sie die Sicherungs- und Aufbewahrungsrichtlinie für den virtuellen Computer definieren. Mehrere virtuelle Computer können in einem einzigen Schritt geschützt werden. Azure-Sicherungstresore, die nach Mai 2015 erstellt wurden, enthalten eine im Tresor integrierte Standardrichtlinie. Diese Standardrichtlinie sieht eine Standardaufbewahrungsdauer von 30 Tagen und eine einmalige tägliche Sicherung vor.

1. Navigieren Sie zum Sicherungstresor, der sich im Azure-Portal unter **Recovery Services** befindet, und klicken Sie anschließend auf **Registrierte Elemente**.
2. Wählen Sie im Dropdownmenü **Virtueller Azure-Computer** aus.

    ![Workload im Portal auswählen](./media/backup-azure-vms/select-workload.png)

3. Klicken Sie unten auf der Seite auf **SCHÜTZEN**. ![Klicken Sie auf „Schützen“.](./media/backup-azure-vms-first-look/protect-icon.png)

    Der **Assistent zum Schützen von Elementen** wird angezeigt. Dieser führt *nur* die virtuellen Computer auf, die registriert wurden und nicht geschützt sind.

    ![Effektives Konfigurieren von Schutzeinstellungen](./media/backup-azure-vms/protect-at-scale.png)

4. Wählen Sie die virtuellen Computer aus, die Sie schützen möchten.

    Wenn zwei oder mehr virtuelle Computer denselben Namen aufweisen, verwenden Sie den Clouddienst, um zwischen den virtuellen Computern zu unterscheiden.

5. Wählen Sie unter **Schutz konfigurieren** eine vorhandene Richtlinie aus, oder erstellen Sie eine neue Richtlinie, um die ermittelten virtuellen Computer zu schützen.

    Jeder Sicherungsrichtlinie können mehrere virtuelle Computer zugeordnet sein. Ein virtueller Computer kann jedoch immer nur einer Richtlinie zugeordnet sein.

    ![Schützen mit der neuen Richtlinie](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] Eine Sicherungsrichtlinie umfasst auch ein Aufbewahrungsschema für die geplanten Sicherungen. Bei Wahl einer vorhandenen Sicherungsrichtlinie können Sie die Aufbewahrungsoptionen im nächsten Schritt nicht ändern.

6. Wählen Sie unter **Beibehaltungsdauer** die täglichen, wöchentlichen, monatlichen und jährlichen Bereiche für die einzelnen Sicherungspunkte aus.

    ![Schützen mit flexibler Aufbewahrung](./media/backup-azure-vms/policy-retention.png)

    Die Aufbewahrungsrichtlinie legt fest, für welchen Zeitraum eine Sicherung aufbewahrt wird. Je nach Erstellungszeitpunkt der Sicherung können Sie unterschiedliche Aufbewahrungsrichtlinien festlegen. Der Sicherungspunkt am Ende jedes Quartals muss beispielsweise ggf. länger aufbewahrt werden (für Audits), während der tägliche Sicherungspunkt (der als Wiederherstellungspunkt für den alltäglichen Betrieb dient) nur 90 Tage aufbewahrt werden muss.

    ![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/long-term-retention.png)

    In dieser Beispielabbildung:

    - **Tägliche Aufbewahrungsrichtlinie**: Täglich erstellte Sicherungen werden 30 Tage lang gespeichert.
    - **Wöchentliche Aufbewahrungsrichtlinie**: Sicherungen, die jeden Sonntag erstellt werden, werden 104 Wochen lang aufbewahrt.
    - **Monatliche Aufbewahrungsrichtlinie**: Sicherungen, die am letzten Samstag im Monat erstellt werden, werden 120 Monate lang aufbewahrt.
    - **Jährliche Aufbewahrungsrichtlinie**: Sicherungen, die am ersten Sonntag im Januar erstellt werden, werden 99 Jahre lang aufbewahrt.

    Ein Auftrag wird erstellt, um die Schutzrichtlinie zu konfigurieren und die virtuellen Computer, die Sie ausgewählt haben, dieser Richtlinie zuzuordnen.

6. Klicken Sie auf die Registerkarte **Auftrag**, und wählen Sie den richtigen Filter zum Anzeigen der Liste mit Aufträgen des Typs **Schutz konfigurieren** aus.

    ![Schutzauftrag konfigurieren](./media/backup-azure-vms/protect-configureprotection.png)


## Schritt 5 – Erste Sicherung

Wenn ein virtueller Computer mit eine Richtlinie geschützt wird, können Sie diese Beziehung auf der Registerkarte **Geschützte Elemente** anzeigen. Bis zur ersten Sicherung eines virtuellen Computers wird der **Schutzstatus** als **Geschützt (erste Sicherung ausstehend)** angezeigt. Standardmäßig ist die erste geplante Sicherung die *Anfangssicherung*.

![Sicherung ausstehend](./media/backup-azure-vms-first-look/protection-pending-border.png)

So lösen Sie die erste Sicherung unmittelbar nach Konfigurieren des Schutzes aus:

1. Klicken Sie am unteren Rand der Seite **Geschützte Elemente** auf die Schaltfläche **Jetzt sichern**. ![Symbol „Jetzt sichern“](./media/backup-azure-vms-first-look/backup-now-icon.png)

    Der Azure Backup-Dienst erstellt einen Sicherungsauftrag für die erste Sicherung.

2. Klicken Sie auf die Registerkarte **Aufträge**, um die Liste der Aufträge anzuzeigen.

    ![Sicherung wird ausgeführt](./media/backup-azure-vms-first-look/protect-inprogress.png)

    >[AZURE.NOTE] Als Teil des Sicherungsvorgangs gibt der Azure Backup-Dienst einen Befehl an die Sicherungserweiterung auf jedem virtuellen Computer aus, damit alle Schreibvorgänge geleert werden und eine konsistente Momentaufnahme erstellt wird.

    Sobald die erste Sicherung abgeschlossen ist, wird der Status des virtuellen Computers auf der Registerkarte **Geschützte Elemente** als *Geschützt* angezeigt.

    ![Virtueller Computer wird mit Wiederherstellungspunkt gesichert](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] Das Sichern virtueller Computer ist ein örtlich gebundener Vorgang. Sie können keine virtuellen Computer aus einer Region in einem Sicherungstresor in einer anderen Region sichern. Deshalb muss in jeder Azure-Region mit virtuellen Computern, die gesichert werden sollen, mindestens ein Sicherungstresor erstellt werden.

## Nächste Schritte
Nachdem Sie einen virtuellen Computer erfolgreich gesichert haben, sind unter Umständen weitere Schritte von Interesse. Machen Sie sich zunächst mit dem Wiederherstellen von Daten auf einem virtuellen Computer vertraut. Außerdem gibt es einige Verwaltungsaufgaben, mithilfe derer Sie besser erkennen, wie Sie Ihre Daten schützen und Kosten senken können.

- [Verwalten und Überwachen Ihrer virtuellen Computer](backup-azure-manage-vms.md)
- [Wiederherstellen virtueller Computer](backup-azure-restore-vms.md)
- [Anleitungen zur Problembehandlung](backup-azure-vms-troubleshoot.md)


## Fragen?
Wenn Sie Fragen haben oder Anregungen zu gewünschten Funktionen mitteilen möchten, [senden Sie uns Ihr Feedback](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0323_2016-->