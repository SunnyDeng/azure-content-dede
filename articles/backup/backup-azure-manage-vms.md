
<properties
	pageTitle="Azure Backup – Verwalten virtueller Computer"
	description="Erfahren Sie, wie ein virtueller Azure-Computer verwaltet wird."
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/17/2015" ms.author="aashishr"; "jimpark"/>

# Verwalten virtueller Computer


## Verwalten geschützter virtueller Computer

1. Zum Anzeigen und Verwalten von Sicherungseinstellungen für einen virtuellen Computer klicken Sie auf die Registerkarte **Geschützte Elemente**.

  - Klicken Sie auf den Namen eines geschützten Elements, um die Registerkarte **Sicherungsdetails** anzuzeigen, auf der Sie Informationen zur letzten Sicherung finden.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Zum Anzeigen und Verwalten von Sicherungsrichtlinieneinstellungen für einen virtuellen Computer klicken Sie auf die Registerkarte **Richtlinien**.

  - Auf der Registerkarte **Sicherungsrichtlinien** wird die vorhandene Richtlinie angezeigt. Sie können sie nach Bedarf ändern. Wenn Sie eine neue Richtlinie erstellen müssen, klicken Sie auf der Seite **Richtlinien** auf **Erstellen**. Wenn Sie eine Richtlinie entfernen möchten, dürfen ihr keine virtuellen Computer zugeordnet sein.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. Sie erhalten weitere Informationen zu Aktionen oder zum Status eines virtuellen Computers auf der Seite **Aufträge**. Klicken Sie in der Liste auf einen Auftrag, um weitere Details zu erhalten, oder filtern Sie die Aufträge für einen bestimmten virtuellen Computer.

    ![Aufträge](./media/backup-azure-manage-vms/backup-job.png)

## Bedarfsabhängig Sicherung eines virtuellen Computers
Sie können eine bedarfsabhängige Sicherung eines virtuellen Computers erstellen, sobald dieser für den Schutz konfiguriert ist. Wenn die erste Sicherung für den virtuellen Computer ansteht, wird mithilfe einer bedarfsabhängigen Sicherung eine vollständige Kopie des virtuellen Computers im Azure-Sicherungstresor erstellt. Wenn die erste Sicherung abgeschlossen ist, werden bei einer bedarfsabhängigen Sicherung nur Änderungen an einer vorherigen Sicherung an den Azure-Sicherungstresor gesendet.

So führen Sie die bedarfsabhängige Sicherung eines virtuellen Computers aus:

1. Navigieren Sie zur Seite **Geschützte Elemente**, wählen Sie **Azure VM** als **Typ** aus (sofern nicht bereits ausgewählt), und klicken Sie auf die Schaltfläche **Auswählen**.

    ![VM-Typ](./media/backup-azure-manage-vms/vm-type.png)

2. Wählen Sie den virtuellen Computer aus, auf dem Sie die abhängige Sicherung erstellen möchten, und klicken am unteren Rand der Seite auf **Jetzt sichern**.

    ![Jetzt sichern](./media/backup-azure-manage-vms/backup-now.png)

    Dadurch wird ein Sicherungsauftrag auf dem ausgewählten virtuellen Computer erstellt. Die Beibehaltungsdauer des Wiederherstellungspunkts, der durch diesen Auftrag erstellt wird, entspricht der Angabe in der Richtlinie für den virtuellen Computer.

    ![Erstellen eines Sicherungsauftrag](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Zum Anzeigen der Richtlinie eines virtuellen Computers führen Sie auf der Seite **Geschützte Elemente** des virtuellen Computers eine Detailsuche durch und navigieren zur Registerkarte "Sicherungsrichtlinie".

3. Nachdem der Auftrag erstellt wurde, klicken Sie auf der Befehlsleiste auf die Schaltfläche **Auftrag anzeigen**, um den entsprechenden Auftrag auf der Seite "Aufträge" anzuzeigen.

    ![Sicherungsauftrag erstellt](./media/backup-azure-manage-vms/created-job.png)

4. Nach erfolgreichem Abschluss des Auftrags wird ein Wiederherstellungspunkt erstellt, den Sie zum Wiederherstellen des virtuellen Computers verwenden können. Dadurch wird auch auf der Seite **Geschützte Elemente** der Wert in der Wiederherstellungspunktspalte um 1 erhöht.

## Beenden des Schutzes für virtuelle Computer
Mit den folgenden Optionen können Sie künftige Sicherungen eines virtuellen Computers beenden:

- Sicherungsdaten beibehalten, die dem virtuellen Computer im Azure Backup-Tresor zugeordnet sind
- Sicherungsdaten löschen, die dem virtuellen Computer zugeordnet sind Sicherungsdaten löschen, die dem virtuellen Computer zugeordnet sind

Bei Wahl der ersten Option können Sie die Sicherungsdaten zum Wiederherstellen des virtuellen Computers nutzen. Klicken Sie [hier](http://azure.microsoft.com/pricing/details/backup/), um die Preisübersicht für virtuelle Computer anzuzeigen.

So beenden Sie den Schutz für einen virtuellen Computer:

1. Navigieren Sie zur Seite **Geschützte Elemente**, wählen Sie **Azure VM** als Filtertyp aus (sofern nicht bereits ausgewählt), und klicken Sie auf die Schaltfläche **Auswählen**.

    ![VM-Typ](./media/backup-azure-manage-vms/vm-type.png)

2. Wählen Sie den virtuellen Computer aus, und klicken Sie am unteren Rand der Seite auf **Schutz beenden**.

    ![Schutz beenden](./media/backup-azure-manage-vms/stop-protection.png)

3. Standardmäßig löscht Azure Backup nicht die dem virtuellen Computer zugeordneten Sicherungsdaten.

    ![Bestätigen des Beendens des Schutzes](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Wenn Sie die gesicherten Daten löschen möchten, aktivieren Sie das Kontrollkästchen.

    ![Kontrollkästchen](./media/backup-azure-manage-vms/checkbox.png)

    Wählen Sie einen Grund für das Beenden der Sicherung. Dies ist zwar optional, hilft aber dem Azure Backup-Team beim Berücksichtigen von Feedback und Priorisieren der Kundenszenarien.

4. Klicken Sie auf die Schaltfläche **Senden** zum Senden des Auftrags **Schutz beenden**. Klicken Sie auf **Auftrag anzeigen**, um den entsprechenden Auftrag auf der Seite **Aufträge** anzuzeigen.

    ![Schutz beenden](./media/backup-azure-manage-vms/stop-protect-success.png)

    Wenn Sie im Assistenten **Schutz beenden** nicht **Zugeordnete Sicherungsdaten löschen** ausgewählt haben, ändert sich nach Abschluss des Auftrags der Schutzstatus in **Schutz beendet**. Die Daten bleiben bei Azure Backup, bis sie explizit gelöscht werden. Sie können die Daten jederzeit löschen, indem Sie den virtuellen Computer auf der Seite **Geschützte Elemente** auswählen und auf **Löschen** klicken.

    ![Beendeter Schutz](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Wenn Sie die Option **Zugeordnete Sicherungsdaten löschen** ausgewählt haben, wird der virtuelle Computer nicht mehr auf der Seite **Geschützte Elemente** angezeigt.

## Erneutes Schützen virtueller Computer
Wenn Sie im Assistenten **Schutz beenden** nicht **Zugeordnete Sicherungsdaten löschen** ausgewählt haben, können Sie den virtuellen Computer erneut schützen, indem Sie ähnliche Schritte wie beim Sichern registrierter virtueller Computer ausführen. Nach Aktivierung des Schutzes werden vor Beenden des Schutzes die Sicherungsdaten dieses virtuellen Computers beibehalten und nach erneuter Aktivierung des Schutzes Wiederherstellungspunkte erstellt.

Nach erneuter Aktivierung des Schutzes ändert sich der Schutzstatus des virtuellen Computers in **Geschützt**, wenn es Wiederherstellungspunkte gibt, die vor dem Zeitpunkt der Aktivierung von **Schutz beenden** liegen.

  ![VM erneut geschützt](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Wenn Sie den virtuellen Computer erneut schützen, können Sie eine andere Richtlinie als die Richtlinie auswählen, gemäß der der virtuelle Computer zuvor geschützt wurde.

## Aufheben der Registrierung virtueller Computer

So entfernen Sie den virtuellen Computer aus dem Sicherungstresor

1. Klicken Sie unten auf der Seite auf die Schaltfläche **REGISTRIERUNG AUFHEBEN**.

    ![Schutz deaktivieren](./media/backup-azure-manage-vms/unregister-button.png)

    In einer Popupbenachrichtigung am unteren Bildschirmrand werden Sie zur Bestätigung aufgefordert. Klicken Sie auf **JA**, um fortzufahren.

    ![Schutz deaktivieren](./media/backup-azure-manage-vms/confirm-unregister.png)

## Löschen von Sicherungsdaten
Sie können Sicherungsdaten löschen, die dem virtuellen Computer zugeordnet sind, und zwar:

- Während des Auftrags "Schutz beenden"
- Nach Abschluss des Auftrags "Schutz beenden" auf einem virtuellen Computer

So löschen Sie Sicherungsdaten auf einem virtuellen Computer mit dem Status "Schutz beendet" nach erfolgreichem Auftrag **Sicherung beenden**:

1. Navigieren Sie zur Seite **Geschützte Elemente**, wählen Sie **Azure VM** als Typ aus (sofern nicht bereits ausgewählt), und klicken Sie auf die Schaltfläche **Auswählen**.

    ![VM-Typ](./media/backup-azure-manage-vms/vm-type.png)

2. Wählen Sie den virtuellen Computer aus. Der virtuelle Computer hat den Status **Schutz beendet**.

    ![Schutz beendet](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Klicken Sie am unteren Rand der Seite auf die Schaltfläche **Löschen**.

    ![Sicherung löschen](./media/backup-azure-manage-vms/delete-backup.png)

4. Wählen Sie im Assistenten **Sicherungsdaten löschen** einen Grund für das Löschen von Sicherungsdaten (dringend empfohlen), und klicken Sie auf **Senden**.

    ![Löschen von Sicherungsdaten](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Dadurch wird ein Auftrag zum Löschen von Sicherungsdaten vom ausgewählten virtuellen Computer erstellt. Klicken Sie auf **Auftrag anzeigen**, um den entsprechenden Auftrag auf der Seite "Aufträge" anzuzeigen.

    ![Löschen von Daten erfolgreich](./media/backup-azure-manage-vms/delete-data-success.png)

    Nach Abschluss des Auftrags wird der Eintrag des virtuellen Computers von der Seite **Geschützte Elemente** entfernt.


###Dashboard

Auf der Seite **Dashboard** können Sie Informationen zu virtuellen Computern in Azure, ihrer Speicherung und den ihnen in den letzten 24 Stunden zugeordneten Aufträgen prüfen. Sie können den Sicherungsstatus und alle zugehörigen Sicherungsfehler anzeigen.

  ![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=August15_HO6-->