<properties
	pageTitle="Einführung in die Sicherung von virtuellen Azure-Computern | Microsoft Azure"
	description="Einführung in die Sicherung von virtuellen Computern in Azure mithilfe des Azure Backup-Diensts"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2015" ms.author="trinadhk";"aashishr";"jimpark"/>

# Sichern virtueller Azure-Computer

Dieser Artikel enthält eine Einführung in die Verwendung von Microsoft Azure Backup zum Schutz Ihrer virtuellen Azure-Computer. In diesem Artikel lernen Sie Folgendes:

- Funktionsweise der Sicherung der virtuellen Azure-Computer
- Verfahren zum Sichern der virtuellen Azure-Computer
- Voraussetzungen für eine reibungslose Sicherung
- Typische Fehler und ihre Behandlung
- Liste der nicht unterstützten Szenarios und Einwirken auf Änderungen am Produkt

Weitere Informationen zu virtuellen Azure-Computern finden Sie in der [Dokumentation zu virtuellen Computern](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Warum die Sicherung des virtuellen Azure-Computers?
Mit Cloud Computing können Anwendungen in einer skalierbaren und hoch verfügbaren Umgebung ausgeführt werden. – Daher hat Microsoft virtuelle Azure-Computer entwickelt. Die mit diesen virtuellen Azure-Computern generierten Daten sind wichtig und müssen zur Verwahrung gesichert werden. Typische Szenarios, bei denen Daten aus Sicherungen wiederhergestellt werden müssen:

- Versehentliches oder böswilliges Löschen von Dateien
- Beschädigung des virtuellen Computers während eines Patch-Updates
- Versehentliches oder böswilliges Löschen des gesamten virtuellen Computers

Daten können auf zwei verschiedene Arten von diesen virtuellen Computern gesichert werden:

- Sichern der einzelnen Datenquellen vom virtuellen Computer
- Sichern des gesamten virtuellen Computers

Gängig ist die Sicherung des gesamten virtuellen Computers, da dies weitaus einfacher zu handhaben ist und zudem die einfache Wiederherstellung der gesamten Anwendung und des gesamten Betriebssystems erleichtert. Azure Backup kann zum Sichern von Gastdaten oder zum Sichern des gesamten virtuellen Computers verwendet werden.

Vorteile der Verwendung von Azure Backup für die Sicherung virtueller Computer:

- Automatisierung der Sicherungs- und Wiederherstellungsworkflows für die virtuellen Computer.
- Anwendungskonsistente Sicherungen, um sicherzustellen, dass wiederhergestellte Daten mit einem konsistenten Status beginnen.
- Keine Ausfallzeiten während der Sicherung virtueller Computer.
- Virtuelle Windows- oder Linux-Computer können gesichert werden.
- Zur einfachen Wiederherstellung sind im Azure Backup-Tresor Wiederherstellungspunkte verfügbar.
- Automatische Bereinigung und Garbage Collection der älteren Wiederherstellungspunkte.

## Wie funktioniert die Sicherung der virtuellen Azure-Computer?
Um einen virtuellen Computer zu sichern, wird zuerst eine Zeitpunkt-Momentaufnahme der Daten benötigt. Der Azure Backup-Dienst initiiert den Sicherungsauftrag zur geplanten Zeit und löst die Sicherungserweiterung zum Erstellen einer Momentaufnahme aus. Die Sicherungserweiterung wird zum Zweck der Konsistenz mit dem VSS-Gastdienst abgestimmt und ruft die Blob-Momentaufnahme-API des Azure Storage-Diensts auf, wenn Konsistenz erzielt wurde. Dadurch kann eine konsistente Momentaufnahme der Datenträger des virtuellen Computers erstellt werden, ohne dass dieser heruntergefahren werden muss.

Nachdem die Momentaufnahme erstellt wurde, werden die Daten vom Azure Backup-Dienst in den Sicherungstresor übertragen. Der Dienst identifiziert und überträgt nur die Datenblöcke, die sich seit der letzten Sicherung geändert haben, sodass die Sicherungen effizient gespeichert werden. Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt. Dieser Wiederherstellungspunkt kann im Azure-Verwaltungsportal angezeigt werden.

![Architektur der Sicherung von virtuellen Azure-Computern](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Für virtuelle Linux-Computer können nur dateikonsistente Sicherungen durchgeführt werden.

## Berechnen von geschützten Instanzen
Für virtuelle Azure-Computer, die mithilfe von Azure Backup gesichert werden, gelten die [Azure Backup-Preise](http://azure.microsoft.com/pricing/details/backup/). Die Berechnung der geschützten Instanzen basiert auf der *tatsächlichen* Größe des virtuellen Computers, d. h. der Summe aller Daten auf dem virtuellen Computer, mit Ausnahme des Ressourcendatenträgers. Ihnen wird *nicht* die maximal unterstützte Größe für jeden mit dem virtuellen Computer verknüpften Datenträger, sondern die Größe der tatsächlich auf dem jeweiligen Datenträger gespeicherten Daten berechnet. Gleichermaßen basiert die Berechnung der Sicherungsspeicherung auf der Menge der mit Azure Backup gespeicherten Daten, d. h. auf der Summe der tatsächlichen Daten in jedem Wiederherstellungspunkt.

Beispiel: virtueller Computer mit A2-Standardgröße und zwei zusätzlichen Datenträgern mit einer maximalen Größe von jeweils 1 TB. In der folgenden Tabelle sind die auf jedem Datenträger tatsächlich gespeicherten Daten aufgeführt:

|Datenträgertyp|Max. Größe|Tatsächliche Daten|
|---------|--------|------|
| Betriebssystem-Datenträger | 1023 GB | 17 GB |
| Lokaler Datenträger/Ressourcendatenträger | 135 GB | 5 GB (bei der Sicherung nicht enthalten) |
| Datenträger 1 |	1023 GB | 30 GB |
| Datenträger 2 | 1023 GB | 0 GB |

Die *tatsächliche* Größe des virtuellen Computers in diesem Fall ist 17 GB + 30 GB + 0 GB = 47 GB. Dies wird in der monatlichen Rechnung als Größe der geschützten Instanz abgerechnet. Mit zunehmender Datenmenge auf dem virtuellen Computer ändert sich auch die Größe der geschützten Instanz entsprechend.

Die Abrechnung erfolgt erst nach dem erfolgreichen Abschluss der ersten Sicherung. Zu diesem Zeitpunkt beginnt die Abrechnung für die Speicherung und die geschützten Instanzen. Die Abrechnung erfolgt so lange, wie *Sicherungsdaten mit Azure Backup* für den virtuellen Computer gespeichert werden. Durch Durchführen des Vorgangs "Schutz beenden" wird die Abrechnung nicht beendet, wenn Sicherungsdaten weiterhin beibehalten werden. Die Abrechnung für einen bestimmten virtuellen Computer endet nur, wenn der Schutz beendet wird *und* alle Sicherungsdaten gelöscht werden. Wenn keine aktiven Sicherungsaufträge vorhanden sind (wenn der Schutz beendet wurde), wird die Größe des virtuellen Computers zum Zeitpunkt der letzten erfolgreichen Sicherung in der monatlichen Rechnung als Größe der geschützten Instanz angesetzt.

## Voraussetzungen
### 1\. Sicherungstresor
Zum Sichern Ihrer virtuellen Azure-Computer müssen Sie zunächst einen Sicherungstresor erstellen. Bei diesem Tresor handelt es sich um eine Entität, in der alle Sicherungen und Wiederherstellungspunkte gespeichert werden, die im Laufe der Zeit erstellt wurden. Der Tresor enthält auch die Sicherungsrichtlinien, die auf die zu sichernden virtuellen Computer angewendet werden.

In der folgenden Abbildung sind die Beziehungen zwischen den verschiedenen Azure Backup-Entitäten dargestellt: ![Azure Backup-Entitäten und ihre Beziehungen](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Erstellen eines Sicherungstresors

1. Melden Sie sich beim [Verwaltungsportal](http://manage.windowsazure.com/) an.

2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor** > **Schnellerfassung**. Wenn mit Ihrem Organisationskonto mehrere Abonnements verknüpft sind, wählen Sie das Abonnement aus, das mit dem Sicherungstresor verknüpft werden soll. In jedem Azure-Abonnement können Sie mehrere Sicherungstresore für die zu schützenden virtuellen Computer erstellen.

3. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird. Dieser muss für jedes Abonnement eindeutig sein.

4. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Beachten Sie, dass die Region des Tresors mit der Region der zu schützenden virtuellen Computer übereinstimmen muss. Wenn Sie über virtuelle Computer in verschiedenen Regionen verfügen, müssen Sie einen Tresor in jeder dieser Regionen erstellen. Es müssen keine Speicherkonten zum Speichern der Sicherungsdaten angegeben werden. Dies erfolgt automatisch über den Sicherungstresor und den Azure Backup-Dienst. ![Erstellen des Sicherungstresors](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

5. Klicken Sie auf **Tresor erstellen**. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Unten im Portal können Sie anhand der Benachrichtigungen den Status prüfen. ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-azure-vms-introduction/creating-vault.png)

6. In einer Meldung wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird dann auf der Seite "Recovery Services" als aktiv aufgeführt. Vergewissern Sie sich nach der Erstellung des Tresors, dass die entsprechende Speicherredundanzoption ausgewählt ist. Weitere Informationen finden Sie unter [Festlegen der Speicherredundanzoption im Sicherungstresor](../backup-azure-backup-create-vault.md#storage-redundancy-options). ![Liste der Sicherungstresore](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. Durch Klicken auf den Sicherungstresor wird die Seite **Schnellstart** geöffnet, auf der die Anweisungen für die Sicherung von virtuellen Azure-Computern angezeigt werden. ![Anweisungen zur Sicherung von virtuellen Computern auf der Dashboard-Seite](./media/backup-azure-vms-introduction/vmbackup-instructions.png)


### 2\. VM-Agent
Bevor Sie einen virtuellen Azure-Computer sichern können, müssen Sie zunächst sicherstellen, dass der Azure VM-Agent auf dem virtuellen Computer ordnungsgemäß installiert ist. Um den virtuellen Computer zu sichern, wird mit dem Azure Backup-Dienst eine Erweiterung für den VM-Agent installiert. Da es sich beim VM-Agent beim Erstellen des virtuellen Computers um eine optional zu installierende Komponente handelt, müssen Sie sicherstellen, dass das Kontrollkästchen für den VM-Agent aktiviert ist, bevor der virtuelle Computer bereitgestellt wird.

Erfahren Sie mehr über den [VM-Agent](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) und [seine Installation](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (in englischer Sprache).

## Einschränkungen

- Die Sicherung von virtuellen Computern auf Grundlage des Azure-Ressourcen-Managers (d. h. IaaS V2) wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit mehr als 16 Datenträgern wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mithilfe von Storage-Premium wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit mehreren reservierten IPs wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit einer reservierten IP und ohne definierten Endpunkt wird nicht unterstützt.
- Die Sicherung von virtuellen Computern mit mehreren NICs oder in einer Konfiguration mit Lastenausgleich wird nicht unterstützt.
- Das Ersetzen eines vorhandenen virtuellen Computers während der Wiederherstellung wird nicht unterstützt. Löschen Sie zuerst den vorhandenen virtuellen Computer und alle zugeordneten Datenträger, und stellen Sie dann die Daten aus der Sicherung wieder her.
- Regionsübergreifende Sicherungs- und Wiederherstellungsvorgänge werden nicht unterstützt.
- Die Sicherung virtueller Computer mithilfe des Azure Backup-Diensts wird in allen öffentlichen Azure-Regionen unterstützt. Dies ist eine [Liste der unterstützten Regionen](http://azure.microsoft.com/regions/#services). Wenn die gewünschte Region derzeit nicht unterstützt wird, wird sie bei der Erstellung des Tresors in der Dropdownliste nicht angezeigt.
- Die Sicherung virtueller Computer mithilfe des Azure Backup-Diensts wird nur für bestimmte Betriebssystemversionen unterstützt:
  - **Linux**: Die Liste der von Azure unterstützten Verteilungen finden Sie [hier](../virtual-machines-linux-endorsed-distributions.md). Andere Bring-Your-Own-Linux-Verteilungen sollten ebenfalls funktionieren, sofern der VM-Agent auf dem virtuellen Computer verfügbar ist.
  - **Windows Server**: Versionen, die älter als Windows Server 2008 R2 sind, werden nicht unterstützt.
- Das Wiederherstellen einer Domänencontroller-VM, die Teil einer Konfiguration mit mehreren Domänencontrollern ist, wird nur über PowerShell unterstützt. Weitere Informationen hierzu finden Sie unter [Wiederherstellen von Multi-DC-Domänencontrollern](backup-azure-restore-vms.md#restoring-domain-controller-vms).

Wenn Sie Anregungen zu gewünschten Funktionen haben, [senden Sie uns Ihr Feedback](http://aka.ms/azurebackup_feedback).

## Nächste Schritte
Informationen für Ihre ersten Schritte mit der Sicherung von virtuellen Computern:

- [Sichern virtueller Computer](backup-azure-vms.md)
- [Wiederherstellen virtueller Computer](backup-azure-restore-vms.md)
- [Verwalten der Sicherung virtueller Computer](backup-azure-manage-vms.md)

<!---HONumber=Oct15_HO1-->