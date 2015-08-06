
<properties
	pageTitle="Azure Backup – Wiederherstellen eines virtuellen Computers"
	description="Erfahren Sie, wie ein virtueller Azure-Computer wiederhergestellt wird."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/17/2015" ms.author="trinadhk"; "jimpark"/>

# Wiederherstellen eines virtuellen Computers
Mithilfe der Wiederherstellungsaktion können Sie einen virtuellen Computer aus den Sicherungen im Azure-Sicherungstresor in einem neuen virtuellen Computer wiederherstellen.

## Auswählen eines wiederherzustellenden Elements

1. Navigieren Sie zur Registerkarte **Geschützte Elemente**, und wählen Sie den virtuellen Computer aus, den Sie in einem neuen virtuellen Computer wiederherstellen möchten.

    ![Geschützte Elemente](./media/backup-azure-restore-vms/protected-items.png)

    Die Spalte **Wiederherstellungspunkt** auf der Seite **Geschützte Elemente** teilt Ihnen die Anzahl der Wiederherstellungspunkte für einen virtuellen Computer mit. Die Spalte **Neuester Wiederherstellungspunkt** enthält den Zeitpunkt der letzten Sicherung, aus der ein virtueller Computer wiederhergestellt werden kann.

2. Klicken Sie auf **Wiederherstellen**, um den Assistenten **Element wiederherstellen** zu öffnen.

    ![Wiederherstellen eines Elements](./media/backup-azure-restore-vms/restore-item.png)

## Auswählen eines Wiederherstellungspunkts

1. Auf dem Bildschirm **Wählen Sie einen Wiederherstellungspunkt** können Sie eine Wiederherstellung vom neuesten Wiederherstellungspunkt oder von einem früheren Zeitpunkt durchführen. Die ausgewählte Standardoption beim Öffnen des Assistenten ist *Neuester Wiederherstellungspunkt*.

    ![Auswählen eines Wiederherstellungspunkts](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Um einen früheren Zeitpunkt auszuwählen, wählen Sie in der Dropdownliste die Option **Datum auswählen** und dann ein Datum im Kalendersteuerelement durch Klicken auf das **Kalendersymbol** aus. Im Steuerelement enthalten alle Datumsangaben mit Wiederherstellungspunkten eine hellgraue Schattierung und können vom Benutzer ausgewählt werden.

    ![Auswählen eines Datums](./media/backup-azure-restore-vms/select-date.png)

    Nachdem Sie im Kalendersteuerelement auf ein Datum geklickt haben, werden die an diesem Datum verfügbaren Wiederherstellungspunkte in der folgenden Tabelle mit Wiederherstellungspunkten angezeigt. Die Spalte **Zeit** gibt die Zeit an, zu der die Momentaufnahme erstellt wurde. Die Spalte **Typ** zeigt die [Konsistenz](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) des Wiederherstellungspunkts an. Die Tabellenüberschrift zeigt in Klammern die Anzahl der Wiederherstellungspunkte an, die an diesem Tag verfügbar sind.

    ![Wiederherstellungspunkte](./media/backup-azure-restore-vms/recovery-points.png)

3. Wählen Sie den Wiederherstellungspunkt in der Tabelle **Wiederherstellungspunkte** aus, und klicken Sie auf den Pfeil "Weiter", um zum nächsten Bildschirm zu gelangen.

## Angeben eines Zielspeicherorts

1. Geben Sie im Bildschirm **Wiederherstellungsinstanz auswählen** Details an, wo der virtuelle Computer wiederhergestellt werden soll.

  - Geben Sie den Namen des virtuellen Computers an: In einem bestimmten Clouddienst sollte der Name des virtuellen Computers eindeutig sein. Wenn Sie beabsichtigen, einen vorhandenen virtuellen Computer durch denselben Namen zu ersetzen, löschen Sie zuerst den vorhandenen virtuellen Computer und die Datenträger, und stellen Sie dann die Daten aus Azure Backup wieder her.
  - Wählen Sie einen Clouddienst für den virtuellen Computer aus: Dies ist für das Erstellen eines virtuellen Computers erforderlich. Sie können entweder einen vorhandenen Clouddienst verwenden oder einen neuen Clouddienst erstellen.

        Der ausgewählte Name des Clouddienstes sollte global eindeutig sein. Typischerweise ist dieser Name mit einer öffentlichen URL nach dem Muster [clouddienst].cloudapp.net verknüpft. Azure gestattet es Ihnen nicht, einen neuen Clouddienst zu erstellen, wenn der Name bereits verwendet wird. Wenn Sie einen neuen Clouddienst erstellen möchten, erhält dieser den gleichen Namen wie der virtuelle Computer. In diesem Fall sollte der Name des virtuellen Computers eindeutig genug sein, um auf den neuen Clouddienst angewendet zu werden.

        Wir zeigen ausschließlich Clouddienste und virtuelle Netzwerke an, die nicht mit Affinitätsgruppen in den Details der Wiederherstellungsinstanzen verbunden sind. [Erfahren Sie mehr] (https://msdn.microsoft.com/en-us/library/azure/jj156085.aspx).

2. Wählen Sie ein Speicherkonto für den virtuellen Computer aus: Dies ist für das Erstellen des virtuellen Computers erforderlich. Sie können aus vorhandenen Speicherkonten in der gleichen Region auswählen, in der sich auch der Azure Backup-Tresor befindet. Wir unterstützen keine Speicherkonten, die zonenredundant sind oder dem Premium-Speichertyp entsprechen.

    Wenn es keine Speicherkonten mit unterstützter Konfiguration gibt, erstellen Sie ein Speicherkonto mit unterstützter Konfiguration, bevor Sie mit dem Wiederherstellungsvorgang beginnen.

    ![Auswählen eines virtuellen Netzwerks](./media/backup-azure-restore-vms/restore-sa.png)

3. Wählen Sie ein virtuelles Netzwerk aus: Das virtuelle Netzwerk (VNET) für den virtuellen Computer muss zum Zeitpunkt der Erstellung des virtuellen Computers ausgewählt werden. Die Wiederherstellungsoberfläche zeigt alle VNETs innerhalb dieses Abonnements, die verwendet werden können. Es ist nicht notwendig, ein VNET für die wiederhergestellte VM auszuwählen – Sie können mit dem wiederhergestellten virtuellen Computer auch dann eine Verbindung über das Internet herstellen, wenn das VNET nicht angewendet wird.

    Wenn der Clouddienst einem virtuellen Netzwerk zugeordnet wird, können Sie das virtuelle Netzwerk nicht ändern.

    ![Auswählen eines virtuellen Netzwerks](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Wählen Sie ein Subnetz: Für den Fall, dass das VNET über Subnetze verfügt, wird standardmäßig das erste Subnetz ausgewählt. Wählen Sie in den Dropdownoptionen das Subnetz Ihrer Wahl aus. Details zu Subnetzen finden Sie in der Erweiterung "Netzwerke" der [Portal-Startseite](https://manage.windowsazure.com/). Wechseln Sie zu **Virtuelle Netzwerke**, wählen Sie das virtuelle Netzwerk aus, und führen Sie einen Drilldown in "Konfigurieren" aus, um Detailinformationen zu Subnetzen anzuzeigen.

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

## Problembehandlung
Bei den meisten Fehlern können Sie die empfohlene Aktion befolgen, die unter "Fehlerdetails" vorgeschlagen wird. Hier sind einige weitere Punkte, die Sie bei der Problembehandlung unterstützen:

| Sicherungsvorgang | Fehlerdetails | Problemumgehung |
| -------- | -------- | -------|
| Wiederherstellen | Cloudinterner Fehler bei der Wiederherstellung | <ol><li>Der Clouddienst, in dem Sie die Wiederherstellung durchführen möchten, ist mit DNS-Einstellungen konfiguriert. Prüfen Sie <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Wenn "Address" konfiguriert ist, bedeutet dies, dass DNS-Einstellungen konfiguriert wurden.<br> <li>Der Clouddienst, in dem Sie die Wiederherstellung durchführen möchten, ist mit ReservedIP konfiguriert, und vorhandene virtuelle Computer im Clouddienst befinden sich im Zustand "Beendet".<br>Mit den folgenden Powershell-Cmdlets können Sie prüfen, ob ein Clouddienst über eine reservierte IP-Adresse verfügt:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |

## Nächste Schritte
- [Verwalten virtueller Computer](backup-azure-manage-vms)

<!----HONumber=July15_HO4-->