<properties writer="kathydav" editor="tysonn" manager="timlt" />

Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher.

Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.

> [AZURE.NOTE] Es ist nicht möglich, einen Betriebssystem-Datenträger zu trennen, es sei denn, Sie löschen auch den virtuellen Computer.


## Suchen des Datenträgers

Wenn Sie den Namen des Datenträgers nicht kennen oder diesen vor dem Trennen überprüfen möchten, führen Sie die folgenden Schritte aus.


1. Melden Sie sich beim [klassischen Azure-Portal](http://manage.windowsazure.com) an.

2. Klicken Sie auf **Virtuelle Computer**, auf den Namen des virtuellen Computers und dann auf **Dashboard**.

3. Unter **Datenträger** werden in der Tabelle die Namen und Typen sämtlicher angefügter Datenträger aufgeführt. Beispielsweise zeigt dieser Bildschirm einen virtuellen Computer mit einer Betriebssystemfestplatte und einem Datenträger:

	![Datenträger suchen](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Trennen des Datenträgers

1. Klicken Sie auf **Virtuelle Computer**, wählen Sie den Namen des virtuellen Computer mit dem Datenträger, den Sie trennen möchten. Klicken Sie anschließend auf **Dashboard**.

2. Klicken Sie auf der Befehlsleiste auf **Trennen des Datenträgers**.

3. Wählen Sie den Datenträger aus, und aktivieren Sie das Kontrollkästchen, um ihn zu trennen.

	![Details zum Trennen des Datenträgers](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.

<!---HONumber=AcomDC_0211_2016-->