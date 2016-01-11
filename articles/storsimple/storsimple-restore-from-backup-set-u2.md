<properties 
   pageTitle="Wiederherstellen eines StorSimple-Volumes aus einer Sicherung | Microsoft Azure"
   description="Erläutert, wie Sie die Seite ";Sicherungskatalog"; des StorSimple Manager-Diensts zum Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz verwenden."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="v-sharos" />

# Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## Übersicht

Auf der Seite **Sicherungskatalog** werden alle Sicherungssätze angezeigt, die mithilfe manueller oder automatisierter Sicherungen erstellt wurden. Sie können auf dieser Seite alle Sicherungen für eine Sicherungsrichtlinie oder ein Volume auflisten, Sicherungen auswählen oder löschen oder eine Sicherung zum Wiederherstellen oder Klonen eines Volumes verwenden.

 ![Seite "Sicherungskatalog"](./media/storsimple-restore-from-backup-set-u2/HCS_BackupCatalog.png)

In diesem Tutorial erfahren Sie, wie Sie Ihr Gerät mithilfe der Seite **Sicherungskatalog** aus einem Sicherungssatz wiederherstellen.

Sie können ein Volume aus einer lokalen oder aus einer Cloudsicherung wiederherstellen. In beiden Fällen wird das Volume durch die Wiederherstellung sofort online geschaltet, während die Daten im Hintergrund heruntergeladen werden.

Bevor Sie einen Wiederherstellungsvorgang initiieren, sollten Sie Folgendes beachten:

- **Das Volume muss offline geschaltet werden**. Schalten Sie das Volume sowohl auf dem Host als auch auf dem Gerät offline, bevor Sie die Wiederherstellung initiieren. Auch wenn das Volume durch die Wiederherstellung automatisch auf dem Gerät online geschaltet wird, müssen Sie das Gerät auf dem Host manuell online schalten. Sie können das Volume auf dem Host online schalten, sobald das Volume auf dem Gerät online ist. (Sie müssen nicht warten, bis die Wiederherstellung abgeschlossen ist.) Anleitungen hierzu finden Sie unter [Offlienschalten von Volumes](storsimple-manage-volumes.md#take-a-volume-offline)

- **Volumetyp nach der Wiederherstellung**: Gelöschte Volumes werden basierend auf dem Typ in der Momentaufnahme wiederhergestellt; das heißt, lokale Volumes werden als lokale Volumes wiederhergestellt, und mehrstufige Volumes werden als mehrstufige Volumes wiederhergestellt.

    Bei vorhandenen Volumes überschreibt der aktuelle Verwendungstyp des Volumes den Typ, der in der Momentaufnahme gespeichert ist. Wenn Sie beispielsweise ein Volume aus einer Momentaufnahme wiederherstellen, die erstellt wurde, als das Volume mehrstufig war, und der Volumetyp jetzt (aufgrund eines Konvertierungsvorgangs) lokal lautet, wird das Volume als lokales Volume wiederhergestellt. In gleicher Weise gilt: Wenn ein vorhandenes lokales Volume erweitert wurde und anschließend aus einer älteren Momentaufnahme, bei der das Volume kleiner war, wiederhergestellt wird, behält das wiederhergestellte Volume die aktuelle, erweiterte Größe bei.

    Sie können ein Volume nicht von einem mehrstufigen Volume in ein lokales Volume oder von einem lokalen Volume in ein mehrstufiges Volume konvertieren, während das Volume wiederhergestellt wird. Warten Sie, bis die Wiederherstellung abgeschlossen ist. Anschließend können Sie das Volume in einen anderen Typ konvertieren. Informationen zum Konvertieren eines Volumes finden Sie unter [Ändern des Volumetyps](storsimple-manage-volumes-u2.md#change-the-volume-type).

- **Die Größe des Volumes spiegelt sich im wiederhergestellten Volume wider**. Dies ist ein wichtiger Aspekt, wenn Sie ein gelöschtes lokales Volume wiederherstellen (weil lokale Volumes vollständig bereitgestellt werden). Stellen Sie sicher, dass Sie über genügend Speicherplatz verfügen, bevor Sie versuchen, ein gelöschtes lokales Volume wiederherzustellen.

- **Ein Volume kann während der Wiederherstellung nicht erweitert werden**. Warten Sie, bis die Wiederherstellung abgeschlossen ist, bevor Sie versuchen, das Volume zu erweitern. Informationen zum Erweitern eines Datenträgers finden Sie unter [Ändern eines Volumes](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Sie können eine Sicherung ausführen, während Sie ein lokales Volume wiederherstellen**. Verfahren finden Sie unter [Verwalten von Sicherungsrichtlinien mithilfe des StorSimple Manager-Diensts](storsimple-manage-backup-policies.md).

- **Sie können einen Wiederherstellungsvorgang abbrechen**. Wenn Sie den Wiederherstellungsauftrag abbrechen, wird das Volume in den Zustand zurückversetzt, in dem es sich vor dem Start des Wiederherstellungsvorgangs befand. Verfahren hierzu finden Sie unter [Abbrechen eines Auftrags](storsimple-manage-jobs.md#cancel-a-job).

## So verwenden Sie den Sicherungskatalog

Die Seite **Sicherungskatalog** bietet eine Abfrage, mit der Sie die Auswahl der Sicherungssätze einschränken können. Sie können die abgerufenen Sicherungssätze anhand der folgenden Parameter filtern:

- **Gerät** – das Gerät, auf dem der Sicherungssatz erstellt wurde.
- **Sicherungsrichtlinie** oder **Volume** – die Sicherungsrichtlinie oder das Volume, der oder dem dieser Sicherungssatz zugeordnet ist.
- **Von** und **Bis** – der Datums- und Uhrzeitbereich, in dem die Sicherung erstellt wurde.

Die gefilterten Sicherungssätze werden dann basierend auf den folgenden Attributen in Tabellenform angezeigt:

- **Name** – der Name der Sicherungsrichtlinie oder des Volumes, der oder dem dieser Sicherungssatz zugeordnet ist.
- **Größe** – die tatsächliche Größe des Sicherungssatzes.
- **Erstellt am** – das Datum und die Uhrzeit der Erstellung der Sicherungen. 
- **Typ** – Sicherungssätze können lokale Momentaufnahmen oder Cloudmomentaufnahmen sein. Eine lokale Momentaufnahme ist eine Sicherung aller Volumedaten, die auf dem lokalen Gerät gespeichert ist, während die Sicherung von Volumedaten in der Cloud als Cloudmomentaufnahme bezeichnet wird. Lokale Momentaufnahmen bieten schnelleren Zugriff, während Cloudmomentaufnahmen für Datenstabilität ausgewählt werden.
- **Initiiert von** – die Sicherungen können automatisch nach einem Zeitplan oder manuell durch einen Benutzer initiiert werden. (Sie können eine Sicherungsrichtlinie verwenden, um Sicherungen zu planen. Es ist aber auch möglich, mithilfe der Option **Sicherung erstellen** eine interaktive Sicherung durchzuführen.)

## So stellen Sie Ihr StorSimple-Volume aus einer Sicherung wieder her

Sie können Ihr StorSimple-Volume auf der Seite **Sicherungskatalog** aus einer bestimmten Sicherung wiederherstellen. Beachten Sie dabei, dass das Volume durch die Wiederherstellung auf den Zustand zum Zeitpunkt der Sicherung zurückgesetzt wird. Alle Daten, die nach dem Sicherungsvorgang hinzugefügt wurden, gehen verloren.

> [AZURE.WARNING]Beim Wiederherstellen aus einer Sicherung werden die vorhandenen Volumes durch die Sicherung ersetzt. Dadurch können Daten verloren gehen, die nach dem Erstellen der Sicherung geschrieben wurden.

### So stellen Sie Ihr Volume wieder her

1. Klicken Sie auf der Seite des StorSimple-Manager-Diensts auf die Registerkarte **Sicherungskatalog**.

    ![Sicherungskatalog](./media/storsimple-restore-from-backup-set-u2/HCS_Restore.png)

2. Wählen Sie wie folgt einen Sicherungssatz aus:
  1. Wählen Sie das entsprechende Gerät aus.
  2. Wählen Sie in der Dropdownliste das Volume oder die Sicherungsrichtlinie für die gewünschte Sicherung aus.
  3. Geben Sie den Zeitraum an.
  4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png), um diese Abfrage durchzuführen.
 
    Die dem ausgewählten Volume oder der Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.

3. Erweitern Sie einen Sicherungssatz, um die zugehörigen Volumes anzuzeigen. Diese Volumes müssen auf dem Host und dem Gerät offline geschaltet werden, bevor sie wiederhergestellt werden können. Greifen Sie auf der Seite **Volumecontainer** auf die Volumes zu, und führen Sie dann die Schritte in [Offlineschalten eines Volumes](storsimple-manage-volumes-u2.md#take-a-volume-offline) durch, um diese offline zu schalten.

    > [AZURE.IMPORTANT]Vergewissern Sie sich, dass die Volumes auf dem Host offline sind, bevor Sie diese auf dem Gerät offline schalten. Wenn Sie die Volumes auf dem Host nicht offline schalten, kann es zur Beschädigung von Daten kommen.

4. Navigieren Sie zurück zur Registerkarte **Sicherungskatalog**, und wählen Sie einen Sicherungssatz aus.

5. Klicken Sie unten auf der Seite auf **Wiederherstellen**.

6. Sie werden aufgefordert, diesen Schritt zu bestätigen. Überprüfen Sie die Wiederherstellungsinformationen, und aktivieren Sie dann das Kontrollkästchen zur Bestätigung.

    ![Bestätigungsseite](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Damit initiieren Sie einen Wiederherstellungsauftrag, den Sie auf der Seite **Jobs** anzeigen können.

8. Nachdem die Wiederherstellung abgeschlossen ist, können Sie überprüfen, ob die Inhalte der Volumes durch die aus der Sicherung ersetzt wurden.

![Video verfügbar](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Video verfügbar**

Um ein Video zu schauen, in dem gezeigt wird, wie Sie mithilfe des Klons und Wiederherstellungsfunktionen in StorSimple gelöschte Dateien wiederherstellen können, klicken Sie [hier](http://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## Bei einem Wiederherstellungsfehler

Sie erhalten eine Warnung, wenn der Wiederherstellungsvorgang aus irgendeinem Grund nicht durchgeführt werden kann. Aktualisieren Sie in diesem Fall die Sicherungsliste, um zu überprüfen, ob die Sicherung noch gültig ist. Wenn die Sicherung gültig ist und die Wiederherstellung aus der Cloud erfolgt, wird der Fehler möglicherweise durch Verbindungsprobleme verursacht.

Um den Wiederherstellungsvorgang erfolgreich abzuschließen, schalten Sie das Volume auf dem Host offline, und wiederholen Sie den Wiederherstellungsvorgang. Beachten Sie, dass Änderungen an den Volumedaten, die während des Wiederherstellungsvorgangs ausgeführt wurden, verloren gehen.

## Nächste Schritte

- Erfahren Sie, wie Sie [StorSimple-Volumes verwalten](storsimple-manage-volumes-u2.md).

- Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple Manager-Diensts verwalten](storsimple-manager-service-administration-u2.md).

<!---HONumber=AcomDC_1223_2015-->