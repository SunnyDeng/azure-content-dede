
Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher. Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.

> [AZURE.NOTE]Ein virtueller Computer in Azure verwendet verschiedene Arten von Datenträgern wie Betriebssystemfestplatten, lokale temporäre Festplatten und optionale Datenträger. Datenträger sind die empfohlene Möglichkeit zum Speichern von Daten für einen virtuellen Computer. Weitere Informationen finden Sie unter [Datenträgern und VHDs für virtuelle Computer](../../virtual-machines-disks-vhds.md). Es ist nicht möglich, einen Betriebssystem-Datenträger zu trennen, es sei denn, Sie löschen auch den virtuellen Computer.

## Suchen des Datenträgers##

Wenn Sie den Namen des Datenträgers nicht kennen oder diesen vor dem Trennen überprüfen möchten, führen Sie die folgenden Schritte aus.

> [AZURE.NOTE]Azure weist dem Datenträger automatisch einen Namen zu, wenn Sie ihn anfügen. Der Name besteht aus dem Namen des Clouddiensts, dem Namen des virtuellen Computers und einer Zahl.

1. Melden Sie sich auf dem [Azure-Portal](http://manage.windowsazure.com) an, falls Sie dies noch nicht getan haben.

2. Klicken Sie auf **Virtuelle Computer**, auf den Namen des virtuellen Computers und dann auf **Dashboard**.

3. Unter **Datenträger** werden in der Tabelle die Namen und Typen sämtlicher angefügter Datenträger aufgeführt. Beispielsweise zeigt dieser Bildschirm einen virtuellen Computer mit einer Betriebssystemfestplatte und einem Datenträger:

	![Datenträger suchen](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Trennen des Datenträgers##

Nachdem Sie den Namen des Datenträgers gefunden haben, sind Sie bereit, den Datenträger zu trennen:

1. Klicken Sie auf **Virtuelle Computer**, wählen Sie den Namen des virtuellen Computer mit dem Datenträger, den Sie trennen möchten. Klicken Sie anschließend auf **Dashboard**.
2. Klicken Sie auf der Befehlsleiste auf **Trennen des Datenträgers**.

3. Wählen Sie den Datenträger aus, und aktivieren Sie das Kontrollkästchen, um ihn zu trennen.

	![Details zum Trennen des Datenträgers](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.

<!---HONumber=July15_HO2-->