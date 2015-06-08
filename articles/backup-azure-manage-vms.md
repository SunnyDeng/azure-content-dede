<properties
	pageTitle="Azure Backup - Verwalten von virtuellen Computern"
	description="Enthält Informationen zum Verwalten des virtuellen Computers in Azure"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# Verwalten von virtuellen Computern


## Verwalten von geschützten virtuellen Computern

1. Zum Anzeigen und Verwalten von Einstellungen für einen virtuellen Computer klicken Sie auf die **geschützte Elemente** Registerkarte.

  - Klicken Sie auf den Namen eines geschützten Elements finden Sie unter der **Sicherungsdetails** Registerkarte anzeigt, Informationen über die letzte Sicherung.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Zum Anzeigen und Verwalten von backup Richtlinieneinstellungen für einen virtuellen Computer klicken Sie auf die **Richtlinien** Registerkarte.

  - Die **Sicherungsrichtlinien** Registerkarte zeigt Ihnen die vorhandene Richtlinie. Sie können ändern, je nach Bedarf. Wenn Sie eine neue Richtlinie auf erstellen müssen **Erstellen** auf der **Richtlinien** Seite. Beachten Sie, dass, wenn Sie eine Richtlinie entfernen möchten sie alle virtuellen Maschinen zugeordnet sollte nicht.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. Sie erhalten weitere Informationen zum Status oder Aktionen für eine virtuelle Maschine auf die **Aufträge** Seite. Klicken Sie auf einen Auftrag in der Liste, um weitere Informationen zu erhalten, oder filtern Sie die Aufträge für einen bestimmten virtuellen Computer.

    ![Aufträge](./media/backup-azure-manage-vms/backup-job.png)

## Bedarfsgesteuerte Sicherung eines virtuellen Computers
Sie können on-Demand die Sicherung einer virtuellen sobald dies für den Schutz konfiguriert ist. Wenn die erste Sicherung aussteht für den virtuellen Computer, bei Bedarf erstellt eine vollständige Kopie des virtuellen Computers in Azure-sicherungstresor. Wenn die erste Sicherung abgeschlossen ist, werden bei Bedarf nur Änderungen aus einer vorherigen Sicherung an Azure-sicherungstresor gesendet.

On-Demand-Sicherung eines virtuellen Computers ausführen:

1. Navigieren Sie zu **geschützte Elemente** Seite, und wählen Sie **Azure Virtual Machine** als **Typ** (sofern nicht bereits ausgewählt ist), und klicken Sie auf **auswählen** Schaltfläche.

    ![VM-Typ](./media/backup-azure-manage-vms/vm-type.png)

2. Wählen Sie den virtuellen Computer, die Sie auf die Sicherung auf Anforderung, und klicken auf **Jetzt sichern** am unteren Rand der Seite.

    ![Jetzt sichern](./media/backup-azure-manage-vms/backup-now.png)

    Dadurch wird einen Sicherungsauftrag auf dem ausgewählten virtuellen Computer erstellt. Die Beibehaltungsdauer des Wiederherstellungspunkts, die durch diesen Auftrag erstellt wird, angegeben in der Richtlinie für den virtuellen Computer identisch sein.

    ![Sicherungsauftrag erstellen](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Zum Anzeigen der Richtlinie mit einem virtuellen Computer verbundenen Drilldown virtuellen Computer in der **geschützte Elemente** Seite, und wechseln Sie zur Registerkarte für die Sicherungsrichtlinie.

3. Nachdem der Auftrag erstellt wurde, klicken Sie auf **Auftrag anzeigen** Schaltfläche in der Popup-Leiste finden in den entsprechenden Auftrag in der Seite "Aufträge".

    ![Der Sicherungsauftrag wurde erstellt](./media/backup-azure-manage-vms/created-job.png)

4. Nach erfolgreichem Abschluss des Auftrags wird ein Wiederherstellungspunkt erstellt werden, die Sie zum Wiederherstellen des virtuellen Computers verwenden können. Dies wird auch den Recovery Point-Spaltenwert um 1 erhöht werden in **geschützte Elemente** Seite.

## Beenden Sie den Schutz von virtuellen Maschinen
Sie können auch die zukünftigen Sicherungen eines virtuellen Computers mit den folgenden Optionen beenden:

- Beibehalten von Sicherungsdaten zugeordneten virtuellen Computer in Azure-sicherungstresor
- Löschen Sie die Sicherungsdaten auf der virtuellen Maschine zugeordnet

Wenn Sie die erste Option ausgewählt haben, können Sie die backup-Daten, zum Wiederherstellen des virtuellen Computers. Weitere Informationen zur Preisgestaltung bei solchen Computern, klicken Sie auf [hier](http://azure.microsoft.com/pricing/details/backup/).

Beim Beenden des Schutzes für einen virtuellen Computer:

1. Navigieren Sie zu **geschützte Elemente** Seite, und wählen Sie **virtuellen Azure-Computer** als Filtertyp (sofern nicht bereits ausgewählt ist), und klicken Sie auf **auswählen** Schaltfläche.

    ![VM-Typ](./media/backup-azure-manage-vms/vm-type.png)

2. Wählen Sie den virtuellen Computer, und klicken Sie auf **Schutz beenden** am unteren Rand der Seite.

    ![Beenden des Schutzes](./media/backup-azure-manage-vms/stop-protection.png)

3. Standardmäßig nicht Azure-Sicherung der virtuellen Maschine zugeordneten Sicherungsdaten gelöscht.

    ![Bestätigen Sie das Beenden des Schutzes](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Wenn Sie die gesicherten Daten löschen möchten, aktivieren Sie das Kontrollkästchen.

    ![Kontrollkästchen](./media/backup-azure-manage-vms/checkbox.png)

    Wählen Sie einen Grund für das Beenden der Sicherungs. Dies ist, zwar optional hilft somit einen Grund Azure-Sicherung auf das Feedback und Priorisieren der Kundenszenarien.

4. Klicken Sie auf **Senden** Schaltfläche zum Senden der **Beenden des Schutzes** Auftrag. Klicken Sie auf **Auftrag anzeigen** finden Sie unter dem Projekt im entsprechenden **Aufträge** Seite.

    ![Beenden des Schutzes](./media/backup-azure-manage-vms/stop-protect-success.png)

    Wenn Sie nicht ausgewählt haben **Delete verbundenen Sicherungsdaten** option während der **Schutz beenden** -Assistenten, und klicken Sie dann auf Post Auftragsabschluss, ändert sich der Schutzstatus **Schutz beendet**.

    ![Beendete Schutz](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Wenn Sie ausgewählt haben die **Delete verbundenen Sicherungsdaten** -Option in virtuellen Computer nicht Teil des **geschützte Elemente** Seite.

## Virtuelle Maschine erneut schützen
Wenn Sie nicht ausgewählt haben die **Delete außerordentliche Sicherungsdaten** option **Schutz beenden**, können Sie den virtuellen Computer erneut schützen, mithilfe der Schritte ähnelt der registrierten virtuellen Computern sichern. Sobald geschützt, diese virtuelle Maschine wird Sicherungsdaten, die vor dem Beenden des Schutzes beibehalten und Wiederherstellungspunkte, nach dem erstellt erneut schützen.

Nach erneut schützen des virtuellen Computers Schutzstatus werden geändert, **Protected** treten Wiederherstellungspunkte vor **Schutz beenden**.

  ![VM erneut geschützt](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Wenn der virtuelle Computer schützen, können Sie auswählen, eine andere Richtlinie als die Richtlinie mit denen virtuellen Computer zunächst verschlüsselt wurde.

## Registrierung der virtuellen Maschinen

Wenn Sie den virtuellen Computer aus dem sicherungstresor entfernen möchten:

1. Klicken Sie auf die **UNREGISTER** am unteren Rand der Seite.

    ![Deaktivieren Sie den Schutz](./media/backup-azure-manage-vms/unregister-button.png)

    Eine Popupbenachrichtigung erscheint am unteren Bildschirmrand Bestätigung anfordern. Klicken Sie auf **Ja** um den Vorgang fortzusetzen.

    ![Deaktivieren Sie den Schutz](./media/backup-azure-manage-vms/confirm-unregister.png)

## Löschen von Backup-Daten
Sie können die Sicherungsdaten auf einer virtuellen Maschine zugeordnet sind, entweder löschen:

- Während der Schutzauftrag
- Nach einem Beenden des Schutzes ist auf einem virtuellen Computer Auftrag abgeschlossen

So löschen Sie backup-Daten auf einer virtuellen Maschine in der "Schutz nicht mehr" Buchen Status erfolgreicher **Backup beenden** Auftrag:

1. Navigieren Sie zu **geschützte Elemente** Seite, und wählen Sie **Azure Virtual Machine** als geben, und klicken Sie auf **auswählen** Schaltfläche.

    ![VM-Typ](./media/backup-azure-manage-vms/vm-type.png)

2. Wählen Sie den virtuellen Computer. Die virtuelle Maschine werden in **Schutz beendet** Zustand.

    ![Schutz beendet](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Klicken Sie auf die **Löschen** am unteren Rand der Seite.

    ![Löschen von Sicherung](./media/backup-azure-manage-vms/delete-backup.png)

4. In der **Löschen von Sicherungsdaten** -Assistenten, wählen Sie einen Grund für das Löschen von Sicherungsdaten (empfohlen), und klicken Sie auf **Senden**.

    ![Löschen Sie die backup-Daten](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Dadurch wird einen Auftrag zum Löschen von Sicherungsdaten von ausgewählten virtuellen Computer erstellt. Klicken Sie auf **Auftrag anzeigen** entsprechenden Auftrag im Seite Aufträge angezeigt.

    ![Löschen von Daten erfolgreich](./media/backup-azure-manage-vms/delete-data-success.png)

    Nach Abschluss des Auftrags Eintrag entspricht, auf der virtuellen Maschine entfernt werden aus **geschützte Elemente** Seite.


###Dashboard

Auf der **Dashboard** Seite Sie Informationen zur Azure Virtual Machines, ihre Speicher- und Aufträge, die in den letzten 24 Stunden zugeordnet finden. Sie können Sicherungsstatus und alle zugehörigen Sicherungsfehlern anzeigen.

  ![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=GIT-SubDir-->