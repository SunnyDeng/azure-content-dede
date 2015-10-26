<properties writer="kathydav" editor="tysonn" manager="timlt" />


Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher. Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.

> [AZURE.NOTE]Ein virtueller Computer in Azure verwendet verschiedene Arten von Datenträgern wie Betriebssystemfestplatten, lokale temporäre Festplatten und optionale Datenträger. Datenträger sind die empfohlene Möglichkeit zum Speichern von Daten für einen virtuellen Computer. Weitere Informationen finden Sie unter [Datenträgern und VHDs für virtuelle Computer](../../virtual-machines-disks-vhds.md). Es ist nicht möglich, einen Betriebssystem-Datenträger zu trennen, es sei denn, Sie löschen auch den virtuellen Computer.

## Suchen des Datenträgers

Bevor Sie einen Datenträger von einem virtuellen Computer trennen können, müssen Sie die LUN-Nummer herausfinden, die als Bezeichner des zu trennenden Datenträgers fungiert. Gehen Sie dazu folgendermaßen vor:

1. 	Öffnen Sie die Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Windows, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure über die Azure-Befehlszeilenschnittstelle](../articles/xplat-cli-connect.md).

2.  Stellen Sie sicher, dass Sie sich im standardmäßigen Azure-Dienstverwaltungsmodus befinden, indem Sie `azure config
 	mode asm` eingeben.

3. 	Bestimmen Sie die an Ihren virtuellen Computer angefügten Datenträger, indem Sie `azure vm disk list
	<virtual-machine-name>` wie folgt aufrufen:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

4. 	Notieren Sie sich die LUN **Logical Unit Number (logische Gerätenummer)** des Datenträgers, den Sie trennen möchten.


## Trennen des Datenträgers

Nachdem Sie die LUN des Datenträgers gefunden haben, sind Sie bereit, ihn zu trennen:

1. 	Trennen Sie den ausgewählten Datenträger vom virtuellen Computer mithilfe des folgenden Befehls `azure vm disk detach
 	<virtual-machine-name> <LUN>`:

		$azure vm disk detach ubuntuVMasm 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	Sie können überprüfen, ob der Datenträger getrennt wurde, indem Sie diesen Befehl ausführen:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

Der getrennte Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.

<!---HONumber=Oct15_HO3-->