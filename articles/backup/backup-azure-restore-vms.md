
<properties
	pageTitle="Azure Backup – Wiederherstellen eines virtuellen Computers | Microsoft Azure"
	description="Erfahren Sie, wie ein virtueller Azure-Computer wiederhergestellt wird."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/11/2015" ms.author="trinadhk"; "jimpark"/>

# Wiederherstellen eines virtuellen Computers
Mithilfe der Wiederherstellungsaktion können Sie einen virtuellen Computer aus den Sicherungen im Azure-Sicherungstresor in einem neuen virtuellen Computer wiederherstellen.

## Wiederherstellen von Workflows
### 1\. Auswählen eines wiederherzustellenden Elements

1. Navigieren Sie zur Registerkarte **Geschützte Elemente**, und wählen Sie den virtuellen Computer aus, den Sie in einem neuen virtuellen Computer wiederherstellen möchten.

    ![Geschützte Elemente](./media/backup-azure-restore-vms/protected-items.png)

    Die Spalte **Wiederherstellungspunkt** auf der Seite **Geschützte Elemente** teilt Ihnen die Anzahl der Wiederherstellungspunkte für einen virtuellen Computer mit. Die Spalte **Neuester Wiederherstellungspunkt** enthält den Zeitpunkt der letzten Sicherung, aus der ein virtueller Computer wiederhergestellt werden kann.

2. Klicken Sie auf **Wiederherstellen**, um den Assistenten **Element wiederherstellen** zu öffnen.

    ![Wiederherstellen eines Elements](./media/backup-azure-restore-vms/restore-item.png)

### 2\. Auswählen eines Wiederherstellungspunkts

1. Auf dem Bildschirm **Wählen Sie einen Wiederherstellungspunkt** können Sie eine Wiederherstellung vom neuesten Wiederherstellungspunkt oder von einem früheren Zeitpunkt durchführen. Die ausgewählte Standardoption beim Öffnen des Assistenten ist *Neuester Wiederherstellungspunkt*.

    ![Auswählen eines Wiederherstellungspunkts](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Um einen früheren Zeitpunkt auszuwählen, wählen Sie in der Dropdownliste die Option **Datum auswählen**. Wählen Sie dann durch Klicken auf das **Kalendersymbol** ein Datum im Kalendersteuerelement aus. Im Steuerelement enthalten alle Datumsangaben mit Wiederherstellungspunkten eine hellgraue Schattierung und können vom Benutzer ausgewählt werden.

    ![Auswählen eines Datums](./media/backup-azure-restore-vms/select-date.png)

    Nachdem Sie im Kalendersteuerelement auf ein Datum geklickt haben, werden die an diesem Datum verfügbaren Wiederherstellungspunkte in der folgenden Tabelle mit Wiederherstellungspunkten angezeigt. Die Spalte **Zeit** gibt die Zeit an, zu der die Momentaufnahme erstellt wurde. Die Spalte **Typ** zeigt die [Konsistenz](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) des Wiederherstellungspunkts an. Die Tabellenüberschrift zeigt in Klammern die Anzahl der Wiederherstellungspunkte an, die an diesem Tag verfügbar sind.

    ![Wiederherstellungspunkte](./media/backup-azure-restore-vms/recovery-points.png)

3. Wählen Sie den Wiederherstellungspunkt in der Tabelle **Wiederherstellungspunkte** aus, und klicken Sie auf den Pfeil „Weiter“, um zum nächsten Bildschirm zu gelangen.

### 3\. Angeben eines Zielspeicherorts

1. Geben Sie im Bildschirm **Wiederherstellungsinstanz auswählen** Details an, wo der virtuelle Computer wiederhergestellt werden soll.

  - Geben Sie den Namen des virtuellen Computers an: In einem bestimmten Clouddienst sollte der Name des virtuellen Computers eindeutig sein. Wenn Sie beabsichtigen, einen vorhandenen virtuellen Computer durch denselben Namen zu ersetzen, löschen Sie zuerst den vorhandenen virtuellen Computer und die Datenträger, und stellen Sie dann die Daten aus Azure Backup wieder her.
  - Wählen Sie einen Clouddienst für den virtuellen Computer aus: Dies ist für das Erstellen eines virtuellen Computers erforderlich. Sie können entweder einen vorhandenen Clouddienst verwenden oder einen neuen Clouddienst erstellen.

        Es kann ein beliebiger Name für den Clouddienst gewählt werden, dieser muss jedoch eindeutig sein. Typischerweise wird der Clouddienstname einer öffentlich zugänglichen URL der Form "[Clouddienst].cloudapp.net" zugeordnet. Azure lässt die Erstellung eines neuen Clouddiensts nicht zu, wenn der Name bereits verwendet wird. Wenn Sie einen neuen Clouddienst erstellen, erhält dieser denselben Namen wie der virtuelle Computer – deshalb sollte der gewählte VM-Name eindeutig sein, um auf den zugeordneten Clouddienst angewendet werden zu können.

        Es werden nur Clouddienste und virtuelle Netzwerke angezeigt, die keiner Affinitätsgruppe in den Details zur Instanzenwiederherstellung zugeordnet sind. [Weitere Informationen](https://msdn.microsoft.com/de-de/library/azure/jj156085.aspx).

2. Wählen Sie ein Speicherkonto für den virtuellen Computer aus: Dies ist für das Erstellen des virtuellen Computers erforderlich. Sie können aus vorhandenen Speicherkonten in der gleichen Region auswählen, in der sich auch der Azure Backup-Tresor befindet. Wir unterstützen keine Speicherkonten, die zonenredundant sind oder dem Premium-Speichertyp entsprechen.

    Wenn es keine Speicherkonten mit unterstützter Konfiguration gibt, erstellen Sie ein Speicherkonto mit unterstützter Konfiguration, bevor Sie mit dem Wiederherstellungsvorgang beginnen.

    ![Auswählen eines virtuellen Netzwerks](./media/backup-azure-restore-vms/restore-sa.png)

3. Wählen Sie ein virtuelles Netzwerk aus: Das virtuelle Netzwerk (VNET) für den virtuellen Computer muss zum Zeitpunkt der Erstellung des virtuellen Computers ausgewählt werden. Die Wiederherstellungsoberfläche zeigt alle VNETs innerhalb dieses Abonnements, die verwendet werden können. Es ist nicht notwendig, ein VNET für die wiederhergestellte VM auszuwählen – Sie können mit dem wiederhergestellten virtuellen Computer auch dann eine Verbindung über das Internet herstellen, wenn das VNET nicht angewendet wird.

    Wenn der Clouddienst einem virtuellen Netzwerk zugeordnet wird, können Sie das virtuelle Netzwerk nicht ändern.

    ![Auswählen eines virtuellen Netzwerks](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Wählen Sie ein Subnetz: Für den Fall, dass das VNET über Subnetze verfügt, wird standardmäßig das erste Subnetz ausgewählt. Wählen Sie in den Dropdownoptionen das Subnetz Ihrer Wahl aus. Rufen Sie Details zu Subnetzen auf, indem Sie auf der [Portal-Startseite](https://manage.windowsazure.com/) unter „Netzwerkerweiterungen“ die Option **Virtual Networks** und anschließend das virtuelle Netzwerk auswählen und einen Drilldown für „Konfigurieren“ ausführen.

    ![Auswählen eines Subnetzes](./media/backup-azure-restore-vms/select-subnet.png)

5. Klicken Sie im Assistenten auf das Symbol **Senden**, um die Details zu übermitteln und einen Wiederherstellungsauftrag zu erstellen.

## Nachverfolgen des Wiederherstellungsvorgangs
Nachdem Sie alle Informationen in den Wiederherstellungs-Assistenten eingegeben und übermittelt haben, versucht Azure Backup einen Auftrag zur Nachverfolgung des Wiederherstellungsvorgangs zu erstellen.

![Erstellen eines Wiederherstellungsauftrags](./media/backup-azure-restore-vms/create-restore-job.png)

Wenn die Auftragserstellung erfolgreich ist, wird in einer Popupbenachrichtigung angezeigt, dass der Auftrag erstellt wird. Sie können weitere Details abrufen, indem Sie auf die Schaltfläche **Auftrag anzeigen** klicken. So gelangen Sie zur Registerkarte **Aufträge**.

![Wiederherstellungsauftrag erstellt](./media/backup-azure-restore-vms/restore-job-created.png)

Nach dem Abschluss des Wiederherstellungsvorgangs wird dieser auf der Registerkarte **Aufträge** als "Abgeschlossen" markiert.

![Wiederherstellungsauftrag abgeschlossen](./media/backup-azure-restore-vms/restore-job-complete.png)

Nach dem Wiederherstellen des virtuellen Computers müssen Sie möglicherweise die auf dem ursprünglichen virtuellen Computer vorhandenen Erweiterungen neu installieren und für den virtuellen Computer im Azure-Portal [die Endpunkte ändern](virtual-machines-set-up-endpoints).

## Wiederherstellen von virtuellen Computern des Domänencontrollers
Die Sicherung von virtuellen Computern des Domänencontrollers wird in Azure Backup unterstützt. Der Wiederherstellungsvorgang erfordert jedoch besondere Sorgfalt. Der Wiederherstellungsvorgang für virtuelle Computer eines Domänencontrollers in einer Konfiguration mit einem einzelnen Domänencontroller unterscheidet sich stark von der Wiederherstellung von virtuellen Computern in einer Konfiguration mit mehreren Domänencontrollern.

### Einzelner Domänencontroller
Der virtuelle Computer kann (wie alle anderen virtuellen Computer) über das Azure-Portal oder mithilfe von PowerShell wiederhergestellt werden.

### Mehrere Domänencontroller
In einer Umgebung mit mehreren Domänencontrollern synchronisieren die Domänencontroller Daten mit einer eigenen Methode. Wenn ein älterer Sicherungspunkt *ohne entsprechende Vorsichtsmaßnahmen* wiederhergestellt wird, kann das USN-Rollback eine Umgebung mit mehreren Domänencontrollern durcheinanderbringen. Die richtige Methode zum Wiederherstellen eines solchen virtuellen Computers besteht im Starten im DSRM-Modus.

Das Problem tritt auf, weil der DSRM-Modus in Azure nicht vorhanden ist. Zum Wiederherstellen eines virtuellen Computers können Sie also nicht das Azure-Portal verwenden. Der einzige unterstützte Wiederherstellungsmechanismus ist die datenträgerbasierte Wiederherstellung mithilfe von PowerShell.

>[AZURE.WARNING]Verwenden Sie für virtuelle Computer des Domänencontrollers in einer Umgebung mit mehreren Domänencontrollern nicht das Azure-Portal für die Wiederherstellung. Nur die PowerShell-basierte Wiederherstellung wird unterstützt.

Hier finden Sie weitere Informationen zum [USN-Rollback-Problem](https://technet.microsoft.com/library/dd363553) und den vorgeschlagenen Strategien zum Beheben des Problems.

## Nächste Schritte
- [Problembehandlung](backup-azure-vms-troubleshoot.md#restore)
- [Verwalten virtueller Computer](backup-azure-manage-vms.md)

<!---HONumber=September15_HO1-->