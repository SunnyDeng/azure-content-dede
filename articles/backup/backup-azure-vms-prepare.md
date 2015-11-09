<properties
	pageTitle="Vorbereiten der Umgebung für die Sicherung virtueller Azure-Computer | Microsoft Azure"
	description="Bereiten Sie die Umgebung für die Sicherung virtueller Azure-Computer vor."
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
	ms.date="10/23/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Vorbereiten der Umgebung für die Sicherung virtueller Azure-Computer
Bevor Sie einen virtuellen Azure-Computer sichern, müssen Sie die folgenden Voraussetzungen erfüllen, um die Umgebung vorzubereiten. Wenn Sie dies bereits erledigt haben, können Sie das [Sichern Ihre virtuellen Computer](backup-azure-vms.md) starten. Führen Sie andernfalls die folgenden Schritte aus, um Ihre Umgebung ordnungsgemäß vorzubereiten.

## 1\. Sicherungstresor
Zum Sichern Ihrer virtuellen Azure-Computer müssen Sie zunächst einen Sicherungstresor erstellen. Bei einem Tresor handelt es sich um eine Entität, in der alle Sicherungen und Wiederherstellungspunkte gespeichert werden, die im Laufe der Zeit erstellt wurden. Der Tresor enthält auch die Sicherungsrichtlinien, die auf die zu sichernden virtuellen Computer angewendet werden.

In der Abbildung sind die Beziehungen zwischen den verschiedenen Azure Backup-Entitäten dargestellt: ![Azure Backup-Entitäten und ihre Beziehungen](./media/backup-azure-vms-prepare/vault-policy-vm.png)

So erstellen Sie einen Sicherungstresor

1. Melden Sie sich im [Verwaltungsportal](http://manage.windowsazure.com/) an.

2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor** > **Schnellerfassung**. Wenn mit Ihrem Organisationskonto mehrere Abonnements verknüpft sind, wählen Sie das Abonnement aus, das mit dem Sicherungstresor verknüpft werden soll. In jedem Azure-Abonnement können Sie mehrere Sicherungstresore für die zu schützenden virtuellen Computer erstellen.

3. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird. Dieser muss für jedes Abonnement eindeutig sein.

4. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Die Region des Tresors muss mit der Region der zu schützenden virtuellen Computer übereinstimmen. Wenn Sie über virtuelle Computer in verschiedenen Regionen verfügen, müssen Sie einen Tresor in jeder dieser Regionen erstellen. Es müssen keine Speicherkonten zum Speichern der Sicherungsdaten angegeben werden. Dies erfolgt automatisch über den Sicherungstresor und den Azure Backup-Dienst.

    ![Erstellen des Sicherungstresors](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Klicken Sie auf **Tresor erstellen**. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Unten im Portal können Sie anhand der Benachrichtigungen den Status prüfen.

    ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-azure-vms-prepare/creating-vault.png)

6. In einer Meldung wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird dann auf der Seite "Recovery Services" als aktiv aufgeführt. Vergewissern Sie sich nach der Erstellung des Tresors, dass die entsprechende Speicherredundanzoption ausgewählt ist. Weitere Informationen finden Sie unter [Festlegen der Speicherredundanzoption im Sicherungstresor](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Liste der Sicherungstresore](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Klicken Sie auf den Sicherungstresor, um die Seite **Schnellstart** zu öffnen, auf der die Anweisungen für das Sichern von virtuellen Azure-Computern angezeigt werden.

    ![Anweisungen zur Sicherung von virtuellen Computern auf der Dashboard-Seite](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## 2\. VM-Agent
Bevor Sie einen virtuellen Azure-Computer sichern können, müssen Sie zunächst sicherstellen, dass der Azure VM-Agent auf dem virtuellen Computer ordnungsgemäß installiert ist. Da es sich beim VM-Agent um eine für das Erstellen des virtuellen Computers optionale Komponente handelt, müssen Sie sicherstellen, dass das Kontrollkästchen für den VM-Agent aktiviert ist, bevor der virtuelle Computer bereitgestellt wird.

Erfahren Sie mehr über den [VM-Agent](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) und [seine Installation](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (in englischer Sprache).

### Backup-Erweiterung
Um den virtuellen Computer zu sichern, wird mit dem Azure Backup-Dienst eine Erweiterung für den VM-Agent installiert. Der Azure Backup-Dienst installiert nahtlos und ohne zusätzlichen Benutzereingriff Upgrades und Patches für die Backup-Erweiterung.

Die Sicherungserweiterung wird installiert, wenn der virtuelle Computer (Virtual Machine, VM) ausgeführt wird. Bei einem ausgeführten virtuellen Computer besteht zudem die größte Chance auf einen anwendungskonsistenten Wiederherstellungspunkt. Der Azure Backup-Dienst setzt die Sicherung des virtuellen Computers jedoch auch dann fort, wenn der virtuelle Computer ausgeschaltet wurde und die Erweiterung nicht installiert werden konnte (auch als „Offline-VM“ bezeichnet). In diesem Fall ist der Wiederherstellungspunkt *ausfallsicher*, wie oben beschrieben.

## 3\. Netzwerkverbindung
Die Backup-Erweiterung benötigt eine Verbindung mit dem Internet, um einwandfrei zu funktionieren, da sie Befehle an einen Azure Storage-Endpunkt (HTTP-URL) sendet, um die Momentaufnahmen des virtuellen Computers zu verwalten. Ohne Internetverbindung erfolgt bei diesen HTTP-Anfragen vom virtuellen Computer ein Timeout, und der Sicherungsvorgang schlägt fehl.

## Einschränkungen

- Die Sicherung von virtuellen Computern auf Grundlage des Azure-Ressourcen-Managers (d. h. IaaS V2) wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit mehr als 16 Datenträgern wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mithilfe von Storage Premium wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit mehreren reservierten IPs wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit einer reservierten IP und ohne definierten Endpunkt wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mithilfe von mehreren NICs wird nicht unterstützt.
- Die Sicherung von virtuellen Computern in einer Konfiguration mit Lastenausgleich (intern oder mit Internetverbindung) wird nicht unterstützt.
- Das Ersetzen eines vorhandenen virtuellen Computers während der Wiederherstellung wird nicht unterstützt. Löschen Sie zuerst den vorhandenen virtuellen Computer und alle zugeordneten Datenträger, und stellen Sie dann die Daten aus der Sicherung wieder her.
- Regionsübergreifende Sicherungs- und Wiederherstellungsvorgänge werden nicht unterstützt.
- Die Sicherung virtueller Computer mithilfe des Azure Backup-Diensts wird in allen öffentlichen Azure-Regionen unterstützt. Dies ist eine Liste der [Azure-Regionen](http://azure.microsoft.com/regions/#services). Wenn die gewünschte Region derzeit nicht unterstützt wird, wird sie bei der Erstellung des Tresors in der Dropdownliste nicht angezeigt.
- Die Sicherung virtueller Computer mithilfe des Azure Backup-Diensts wird nur für bestimmte Betriebssystemversionen unterstützt:
  - **Linux**: Die Liste der von Azure unterstützten Verteilungen finden Sie [hier](../virtual-machines-linux-endorsed-distributions.md). Andere Bring-Your-Own-Linux-Verteilungen sollten ebenfalls funktionieren, sofern der VM-Agent auf dem virtuellen Computer verfügbar ist.
  - **Windows Server**: Versionen, die älter als Windows Server 2008 R2 sind, werden nicht unterstützt.
- Das Wiederherstellen einer Domänencontroller-VM, die Teil einer Konfiguration mit mehreren Domänencontrollern ist, wird nur über PowerShell unterstützt. Weitere Informationen hierzu finden Sie unter [Wiederherstellen von Multi-DC-Domänencontrollern](backup-azure-restore-vms.md#restoring-domain-controller-vms).

## Fragen?
Wenn Sie Fragen oder Anregungen zu gewünschten Funktionen haben, [senden Sie uns Ihr Feedback](http://aka.ms/azurebackup_feedback).

## Nächste Schritte

- [Planen der Sicherungsinfrastruktur für virtuelle Computer](backup-azure-vms-introduction.md)
- [Sichern virtueller Computer](backup-azure-vms.md)
- [Verwalten der Sicherung virtueller Computer](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO1-->