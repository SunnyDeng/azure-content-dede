

## Mehrfach- und Einzelinstanz-VMs
Für viele Kunden, die Azure nutzen, ist es entscheidend, dass sie planen können, wann ihre VMs einer geplanten Wartung unterzogen werden, da dies mit ca. 15 Minuten Ausfallzeit verbunden ist. Sie können Verfügbarkeitsgruppen nutzen, um zu steuern, wann bereitgestellte VMs geplanter Wartung unterzogen werden.

Es gibt zwei mögliche Konfigurationen für VMs, die in Azure ausgeführt werden. VMs werden entweder als Mehr- oder Einzelinstanz ausgeführt. Wenn VMs sich in Verfügbarkeitsgruppen befinden, werden sie als Mehrfachinstanz konfiguriert. Beachten Sie, dass sogar einzelne VMs in einer Verfügbarkeitsgruppe bereitgestellt werden können und als Mehrfachinstanz behandelt werden. Wenn VMs sich NICHT in einer Verfügbarkeitsgruppe befinden, werden sie als Einzelinstanz konfiguriert. Ausführliche Informationen über Verfügbarkeitsgruppen finden Sie unter [Manage the Availability of your Windows Virtual Machines](../articles/virtual-machines/virtual-machines-windows-manage-availability.md) (Verwalten der Verfügbarkeit Ihrer virtuellen Windows-Computer) oder [Manage the Availability of your Linux Virtual Machines](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) (Verwalten der Verfügbarkeit Ihrer virtuellen Linux-Computer).

Geplante Wartungsupdates für Einzelinstanz- und Mehrfachinstanz-VMs treten separat auf. Durch Neukonfiguration Ihrer VMs als Einzelinstanz (wenn sie Mehrfachinstanzen sind) oder Mehrfachinstanz (wenn sie Einzelinstanzen sind) können Sie steuern, wann Ihre VMs der geplanten Wartung unterzogen werden. Ausführliche Informationen zur geplanten Wartung für Azure-VMs finden Sie unter [Planned maintenance for Azure Linux virtual machines](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md) (Geplante Wartung für virtuelle Azure-Linux-Computer) oder [Planned maintenance for Azure virtual machines](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md) (Geplante Wartung für virtuelle Azure-Computer).

## Für die Mehrfachinstanzkonfiguration
Sie können den Zeitraum auswählen, zu dem die geplante Wartung Ihre VMs beeinflusst, die in einer Verfügbarkeitsgruppenkonfiguration bereitgestellt werden, indem Sie diese VMs aus Verfügbarkeitsgruppen entfernen.
1.	Sieben Kalendertage vor der geplanten Wartung Ihrer VMs in einer Mehrfachinstanzkonfiguration erhalten Sie eine E-Mail. Die Abonnement-IDs und Namen der betroffenen Mehrfachinstanz-VMs werden im Text der E-Mail erwähnt.
2.	Während dieser 7 Tage können Sie den Zeitraum auswählen, zu dem Ihre Instanzen aktualisiert werden, indem Sie Ihre Mehrfachinstanz-VMs in dieser Region aus ihrer Verfügbarkeitsgruppe entfernen. Diese Änderung in der Konfiguration führt zu einem Neustart, da der virtuelle Computer von einem physischen Host, dem Ziel der Wartung, auf einen anderen physischen Host verschoben wird, der kein Wartungsziel ist. 
3.	Sie können die VM im klassischen Portal aus ihrer Verfügbarkeitsgruppe entfernen. 
   
        a.	In the Classic portal, click on the VM and then select “configure.” 
        
        b.	Under “settings”, you can see which Availability Set the VM is in.
        
    ![Verfügbarkeitsgruppenauswahl](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

        c.	In the availability set dropdown menu, select “remove from availability set.”
        
    ![Entfernen aus Gruppe](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)

        d.	At the bottom, select “save.” Select “yes” to acknowledge that this action will restart the VM.
4.	Diese VMs werden zu Einzelinstanz-Hosts verschoben und während der geplanten Wartung für Verfügbarkeitsgruppenkonfigurationen nicht aktualisiert.
5.	Nach Abschluss der Aktualisierung der Verfügbarkeitsgruppen-VMs (gemäß dem in der ursprünglichen E-Mail beschriebenen Zeitplan) sollten Sie die VMs wieder ihren Verfügbarkeitsgruppen hinzufügen, und sie werden als Mehrfachinstanz-VMs neu konfiguriert. Verschieben der VMs von der Einzelinstanz zurück zur Mehrfachinstanz führt zu einem Neustart. Nachdem alle Mehrfachinstanzupdates für die gesamte Azure-Umgebung abgeschlossen sind, folgt in der Regel die Einzelinstanzwartung.

Beachten Sie, dass dies auch mithilfe von Azure PowerShell erzielt werden kann: Get-AzureVM-ServiceName „<VmCloudServiceName>“-Name „<VmName>“ | Remove-AzureAvailabilitySet | Update-AzureVM

## Für die Einzelinstanzkonfiguration
Sie können den Zeitraum auswählen, zu dem die geplante Wartung Ihre VMs in einer Einzelinstanzkonfiguration beeinflusst, indem Sie diese VMs Verfügbarkeitsgruppen hinzufügen. Schrittweise Anleitung
1.	Sieben Kalendertage vor der geplanten Wartung Ihrer VMs in einer Einzelinstanzkonfiguration erhalten Sie eine E-Mail. Die Abonnement-IDs und Namen der betroffenen Einzelinstanz-VMs werden im Text der E-Mail erwähnt. 
2.	Während dieser 7 Tage können Sie den Zeitraum auswählen, in dem Ihre Instanz neu startet, indem Sie Ihre Einzelinstanz-VMs in eine Verfügbarkeitsgruppe verschieben, die sich in der gleichen Region befindet. Diese Änderung in der Konfiguration führt zu einem Neustart, da der virtuelle Computer von einem physischen Host, dem Ziel der Wartung, auf einen anderen physischen Host verschoben wird, der kein Wartungsziel ist.
3.	Führen Sie hier die Anweisungen zum Hinzufügen vorhandener VMs zu Verfügbarkeitsgruppen mit dem klassischen Portal und Azure PowerShell aus (siehe Azure PowerShell-Beispiel im Hinweis unten).
4.	Sobald diese VMs als Mehrfachinstanzen neu konfiguriert sind, werden sie von der geplanten Wartung für Einzelinstanz-VMs ausgeschlossen.
5.	Nach Abschluss der Aktualisierung der Einzelinstanz-VMs (gemäß dem in der ursprünglichen E-Mail beschriebenen Zeitplan) können Sie die VMs aus ihren Verfügbarkeitsgruppen entfernen, und sie werden als Einzelinstanz-VMs neu konfiguriert.

Beachten Sie, dass dies auch mit Azure PowerShell erzielt werden kann:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0323_2016-->