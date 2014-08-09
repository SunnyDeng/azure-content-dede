<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Trennen eines Datenträgers von einem virtuellen Computer


* [Konzepte](#concepts)
* [Gewusst wie: Suche nach einem an einen virtuellen Computer angefügten
  Datenträger](#finddisks)
* [Gewusst wie: Trennen eines Datenträgers](#detachdisk)

## <a id="concepts"> </a>Konzepte

Ein virtueller Computer in Azure verwendet verschiedene Arten von Datenträgern wie Betriebssystemfestplatten, lokale temporäre Festplatten und optionale Datenträger. Sie können einen Datenträger an einen virtuellen Computer anfügen, um Anwendungsdaten zu speichern. Ein Datenträger ist eine virtuelle Festplatte (virtual hard disk, VHD), die Sie mit Azure entweder lokal auf Ihrem Computer oder in der Cloud erstellen können.

Sie können Datenträger zu jeder Zeit anfügen und trennen. Die Anzahl der Datenträger, die Sie an einen virtuellen Computer anfügen können, ist jedoch begrenzt und hängt von der Größe des Computers ab.

Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Durch diesen Prozess wird der Datenträger nicht aus dem Speicher gelöscht. Wenn Sie die auf dem Datenträger vorhandenen Daten erneut verwenden möchten, können Sie den Datenträger problemlos wieder an denselben oder einen neuen virtuellen Computer anfügen.

Weitere Informationen zur Verwendung von Datenträgern finden Sie unter [Verwalten von Datenträgern und Images][1].

## <a id="finddisks"> </a>Gewusst wie: Suche nach an einen virtuellen Computer angefügten Datenträgern

Sie können die Datenträger, die an einen virtuellen Computer angefügt sind, entweder mithilfe des Dashboards oder auf der Seite "Datenträger" des virtuellen Computers suchen.

### Verwenden des Dashboards für die Suche nach Informationen über angefügte Datenträger

1.  Melden Sie sich auf dem Azure-[Verwaltungsportal][2] an, falls noch nicht geschehen..

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

3.  Klicken Sie auf **Dashboard**. Auf dem Dashboard des virtuellen Computers finden Sie die Anzahl der angefügten Datenträger und deren Namen. Das folgende Beispiel zeigt einen an einen virtuellen Computer angefügten Datenträger:
    
    ![Datenträger suchen](./media/howto-detach-disk-windows-linux/FindDataDisks.png)

**Hinweis:** Mindestens ein Datenträger ist an alle virtuellen Computer angefügt. An jeden virtuellen Computer ist eine Betriebssystemfestplatte angefügt, die nicht getrennt werden kann, ohne den virtuellen Computer zu löschen. Der lokale temporäre Datenträger wird nicht unter den Datenträgern aufgelistet, da er nicht beständig ist.

### Verwenden der Seite "Datenträger" auf virtuellen Computern für die Suche nach Informationen über angefügte Datenträger

1.  Melden Sie sich auf dem Azure-[Verwaltungsportal][2] an, falls noch nicht geschehen..

2.  Klicken Sie auf **Virtuelle Computer** und anschließend auf **Datenträger**. Diese Seite enthält eine Liste aller Datenträger, die für eine Verwendung durch virtuelle Computer zur Verfügung stehen, sowie der Datenträger, die von virtuellen Computern verwendet werden. Die Liste führt sowohl Betriebssystemfestplatten als auch Datenträger auf. Verwenden sie das Dashboard, um zwischen den zwei Arten von Datenträgern zu unterscheiden, die an den virtuellen Computer angefügt sind.
    
    **Hinweis:** Wenn Sie einen neuen Datenträger an einen virtuellen Computer anfügen, können Sie einer VHD-Datei einen Namen zuweisen, der für den Datenträger verwendet werden soll. Azure weist allerdings den Namen des Datenträgers zu. Der Name besteht aus demNamen des Clouddienstes, dem Namen des virtuellen Computers und einem numerischen Bezeichner.

## <a id="detachdisk"> </a>Gewusst wie: Trennen eines Datenträgers

Wenn Sie den Namen des Datenträgers gefunden haben, den Sie trennen möchten, gehen Sie folgendermaßen vor, um den Datenträger vom virtuellen Computer zu trennen.

1.  Melden Sie sich am Azure-Verwaltungsportal an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, wählen Sie den virtuellen Computer mit dem Datenträger, den Sie trennen möchten, und klicken Sie anschließend auf **Datenträger trennen**.

3.  Wählen Sie den Datenträger aus, und aktivieren Sie das Kontrollkästchen, um ihn zu trennen.
    
    ![Details zum Trennen des Datenträgers](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)
    
    Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.

Sie können den Datenträger nun an denselben oder an einen neuen virtuellen Computer anfügen. Anweisungen dazu finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Computer](/de-de/manage/windows/how-to-guides/attach-a-disk/).



[1]: http://go.microsoft.com/fwlink/p/?LinkId=263439
[2]: http://manage.windowsazure.com