<properties 
   pageTitle="Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz"
   description="Erläutert, wie Sie den Sicherungskatalog zum Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz verwenden."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/13/2015"
   ms.author="v-sharos" />

#Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz

##Übersicht

Auf der Seite **Sicherungskatalog** werden alle Sicherungssätze angezeigt, die mithilfe manueller oder automatisierter Sicherungen erstellt wurden. Sie können auf dieser Seite alle Sicherungen für eine Sicherungsrichtlinie oder ein Volume auflisten, Sicherungen auswählen oder löschen oder eine Sicherung zum Wiederherstellen oder Klonen eines Volumes verwenden.

 ![Seite "Sicherungskatalog"](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

In diesem Lernprogramm erfahren Sie, wie Sie Ihr Gerät mithilfe der Seite **Sicherungskatalog** aus einem Sicherungssatz wiederherstellen.

##So verwenden Sie den Sicherungskatalog 

Die Seite **Sicherungskatalog** bietet eine Abfrage, mit der Sie die Auswahl der Sicherungssätze einschränken können. Sie können die abgerufenen Sicherungssätze anhand der folgenden Parameter filtern:

- **Gerät** – das Gerät, auf dem der Sicherungssatz erstellt wurde.
- **Sicherungsrichtlinie** oder **Volume** – die Sicherungsrichtlinie oder das Volume, der oder dem dieser Sicherungssatz zugeordnet ist.
- **Von** und **Bis** – der Datums- und Uhrzeitbereich, in dem die Sicherung erstellt wurde.

Die gefilterten Sicherungssätze werden dann basierend auf den folgenden Attributen in Tabellenform angezeigt:

- **Name** – der Name der Sicherungsrichtlinie oder des Volumes, der oder dem dieser Sicherungssatz zugeordnet ist.
- **Größe** – die tatsächliche Größe des Sicherungssatzes.
- **Erstellt am** – das Datum und die Uhrzeit der Erstellung der Sicherungen. 
- **Typ** – Sicherungssätze können lokale Momentaufnahmen oder Cloud-Momentaufnahmen sein. Eine lokale Momentaufnahme ist eine Sicherung aller Volumedaten, die auf dem lokalen Gerät gespeichert ist, während die Sicherung von Volumedaten in der Cloud als Cloud-Momentaufnahme bezeichnet wird. Lokale Momentaufnahmen bieten schnelleren Zugriff, während Cloud-Momentaufnahmen für Datenstabilität ausgewählt werden.
- **Initiiert von** – die Sicherungen können automatisch nach einem Zeitplan oder manuell durch einen Benutzer initiiert werden. \(Sie können eine Sicherungsrichtlinie verwenden, um Sicherungen zu planen. Es ist aber auch möglich, mithilfe der Option **Sicherung erstellen** eine interaktive Sicherung durchzuführen.\)

##Wiederherstellen von Geräten aus einer Sicherung

Sie können Ihr Gerät mithilfe der Seite **Sicherungskatalog** aus einem bestimmten Sicherungssatz wiederherstellen. Beachten Sie dabei, dass das Volume durch die Wiederherstellung auf den Zustand zum Zeitpunkt der Sicherung zurückgesetzt wird. Alle Daten, die nach dem Sicherungsvorgang hinzugefügt wurden, gehen verloren.

> [AZURE.WARNING]Beim Wiederherstellen aus einer Sicherung werden die vorhandenen Volumes durch die Sicherung ersetzt. Dadurch können Daten verloren gehen, die nach dem Erstellen der Sicherung geschrieben wurden.


###So führen Sie eine Wiederherstellung aus einem Sicherungssatz durch

1. Klicken Sie auf der Seite des StorSimple-Manager-Diensts auf die Registerkarte **Sicherungskatalog**.

    ![Sicherungskatalog](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Wählen Sie wie folgt einen Sicherungssatz aus:
  1. Wählen Sie das entsprechende Gerät aus.
  2. Wählen Sie in der Dropdownliste das Volume oder die Sicherungsrichtlinie für die gewünschte Sicherung aus.
  3. Geben Sie den Zeitraum an.
  4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png), um diese Abfrage durchzuführen.
 
    Die dem ausgewählten Volume oder der Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.

3. Erweitern Sie einen Sicherungssatz, um die zugehörigen Volumes anzuzeigen. Diese Volumes müssen auf dem Host und dem Gerät offline geschaltet werden, bevor sie wiederhergestellt werden können. Greifen Sie auf der Seite **Volumecontainer** auf die Volumes zu, und führen Sie dann die Schritte in [Offlineschalten eines Volumes](storsimple-manage-volumes.md#take-a-volume-offline) durch, um diese offline zu schalten.

    >  [AZURE.IMPORTANT]Vergewissern Sie sich, dass die Volumes auf dem Host offline sind, bevor Sie diese auf dem Gerät offline schalten. Wenn Sie diese Volumes nicht zunächst auf dem Host offline schalten, schaltet der StorSimple-Manager-Dienst diese automatisch auf dem Host offline. Dies kann möglicherweise zu Schäden an den Daten führen.

4. Navigieren Sie zurück zur Registerkarte **Sicherungskatalog**, und wählen Sie einen Sicherungssatz aus.

5. Klicken Sie unten auf der Seite auf **Wiederherstellen**.

6. Sie werden aufgefordert, diesen Schritt zu bestätigen.

    ![Bestätigungsseite](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Überprüfen Sie die Informationen für die Wiederherstellung, und klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Damit initiieren Sie einen Wiederherstellungsauftrag, den Sie auf der Seite **Jobs** anzeigen können.

8. Nachdem die Wiederherstellung abgeschlossen ist, können Sie überprüfen, ob die Inhalte der Volumes durch die aus der Sicherung ersetzt wurden.

##Nächste Schritte

Erfahren Sie, wie Sie [StorSimple-Volumes verwalten](storsimple-manage-volumes.md).
<!--HONumber=52-->
