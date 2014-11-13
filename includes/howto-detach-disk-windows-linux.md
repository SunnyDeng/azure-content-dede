<properties writer="kathydav" editor="tysonn" manager="timlt" />

# Trennen eines Datenträgers von einem virtuellen Computer

-   [Schritt 1: Suchen des Datenträgers][Schritt 1: Suchen des Datenträgers]
-   [Schritt 2: Trennen eines Datenträgers][Schritt 2: Trennen eines Datenträgers]

Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher. Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.

> [WACOM.NOTE] Ein virtueller Computer in Azure verwendet verschiedene Arten von Datenträgern wie Betriebssystemfestplatten, lokale temporäre Festplatten und optionale Datenträger. Datenträger sind die empfohlene Möglichkeit zum Speichern von Daten für einen virtuellen Computer. Unter [Datenträger und Images verwalten][Datenträger und Images verwalten] finden Sie Details in Bezug auf Datenträger. Anweisungen dazu finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Computer][Anfügen eines Datenträgers an einen virtuellen Computer].

## <span id="finddisks"></span> </a>Schritt 1: Suchen des Datenträgers

Wenn Sie den Namen des Datenträgers nicht kennen oder überprüfen möchten, bevor Sie den Datenträger trennen, befolgen Sie diese Schritte.

> [WACOM.NOTE] Azure weist dem Datenträger automatisch einen Namen zu, wenn Sie ihn anfügen. Der Name besteht aus dem Namen des Cloud-Diensts, dem Namen des virtuellen Computers und einer Zahl.

1.  Melden Sie sich auf dem Azure-[Verwaltungsportal][Verwaltungsportal] an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus. Das Dashboard des virtuellen Computers wird geöffnet.

3.  Unter **Datenträger** werden in der Tabelle die Namen und Typen sämtlicher angefügter Datenträger aufgeführt. Beispielsweise zeigt dieser Bildschirm einen virtuellen Computer mit einer Betriebssystemfestplatte und einem Datenträger:

    ![Datenträger suchen][Datenträger suchen]

## <span id="detachdisk"></span> </a>Schritt 2: Trennen des Datenträgers

Nachdem Sie den Namen des Datenträgers gefunden haben, sind Sie bereit, den Datenträger zu trennen:

1.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie den virtuellen Computer aus, der über den Datenträger verfügt, den Sie trennen möchten.
2.  Klicken Sie auf der Befehlsleiste auf die Option zum Trennen des Datenträgers.

3.  Wählen Sie den Datenträger aus, und aktivieren Sie das Kontrollkästchen, um ihn zu trennen.

    ![Details zum Trennen des Datenträgers][Details zum Trennen des Datenträgers]

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.

  [Schritt 1: Suchen des Datenträgers]: #finddisks
  [Schritt 2: Trennen eines Datenträgers]: #detachdisk
  [Datenträger und Images verwalten]: http://go.microsoft.com/fwlink/p/?LinkId=263439
  [Anfügen eines Datenträgers an einen virtuellen Computer]: /de-de/manage/windows/how-to-guides/attach-a-disk/
  [Verwaltungsportal]: http://manage.windowsazure.com
  [Datenträger suchen]: ./media/howto-detach-disk-windows-linux/FindDataDisks.png
  [Details zum Trennen des Datenträgers]: ./media/howto-detach-disk-windows-linux/DetachDiskDetails.png
