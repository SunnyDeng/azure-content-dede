<properties 
   pageTitle="Deaktivieren und Löschen eines StorSimple-Geräts | Microsoft Azure"
   description="Beschreibt, wie ein StorSimple-Gerät aus dem Dienst entfernt wird, indem es zunächst deaktiviert und anschließend gelöscht wird."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="v-sharos" />

# Deaktivieren und Löschen eines StorSimple-Geräts

In diesem Tutorial wird erläutert, wie ein StorSimple-Gerät aus dem Dienst entfernt wird, indem es deaktiviert und anschließend gelöscht wird.

>[AZURE.NOTE]Sie müssen ein Gerät deaktivieren, bevor Sie es löschen können.

## Deaktivieren eines Geräts

Möglicherweise möchten Sie ein Gerät außer Betrieb nehmen. In diesem Fall muss das Gerät deaktiviert werden. Das Deaktivieren trennt die Verbindung zwischen dem Gerät und dem entsprechenden StorSimple Manager-Dienst.

>[AZURE.WARNING]Die Deaktivierung ist ein ENDGÜLTIGER Vorgang und kann nicht rückgängig gemacht werden. Ein deaktiviertes Gerät kann nicht mit dem StorSimple Manager-Dienst registriert werden, es sei denn, die Werkseinstellungen werden zunächst zurückgesetzt.

Wenn Sie ein Gerät deaktivieren, sind alle Daten, die lokal auf dem Gerät gespeichert wurden, nicht mehr zugänglich. Nur Daten auf dem Gerät, die in der Cloud gespeichert wurden, können wiederhergestellt werden. Wenn ein Gerät einmal deaktiviert wurde, müssen die Werkseinstellungen zurückgesetzt werden, bevor es erneut mit einem vorhandenen oder neuen Dienst verwendet werden kann. Das Zurücksetzen der Werkseinstellungen löscht alle Daten, die lokal auf Ihrem Gerät gespeichert wurden. Daher ist es wichtig, dass Sie von allen Daten eine Momentaufnahme in der Cloud machen, bevor Sie ein Gerät deaktivieren. Dadurch können Sie alle Daten zu einem späteren Zeitpunkt wiederherstellen.

Bei einem virtuellen StorSimple-Gerät löscht die Deaktivierung die virtuelle Maschine und die Ressourcen, die bei der Bereitstellung erstellt wurden. Nach der Deaktivierung kann das virtuelle Gerät nicht im vorherigen Zustand wiederhergestellt werden. Vor dem Deaktivieren eines virtuellen StorSimple-Geräts müssen alle von diesem virtuellen Gerät abhängigen Clients und Hosts beendet oder entfernt werden.

### Deaktivieren und Löschen von Daten

Wenn Sie das Gerät vollständig löschen und die Gerätedaten nicht beibehalten wollen, führen Sie die folgenden Schritte aus:

1. Vor dem Deaktivieren eines Geräts müssen Sie alle Volumecontainer (und die Volumes) löschen, die dem Gerät zugeordnet sind. Sie können Volumecontainer nur löschen, nachdem Sie die zugeordneten Sicherungen gelöscht haben.

2. Deaktivieren Sie das Gerät. Anweisungen finden Sie unter [Schritte zum Deaktivieren](#steps-to-deactivate).

3. Sie können nach der Deaktivierung das Gerät vollständig löschen. Anweisungen finden Sie unter [Löschen eines Geräts](#delete-a-device).

### Deaktivieren und Beibehalten von Daten

Wenn Sie das Gerät löschen, aber die Gerätedaten beibehalten wollen, führen Sie die folgenden Schritte aus:

1. Deaktivieren Sie das Gerät. Alle Volumecontainer und die Momentaufnahmen des Geräts bleiben erhalten. Anweisungen finden Sie unter [Schritte zum Deaktivieren](#steps-to-deactivate).

2. Sie können nun ein Failover der Volumecontainer und der zugehörigen Momentaufnahmen durchführen. Informationen zu den Verfahren finden Sie unter [Ausführen eines Failovers und einer Notfallwiederherstellung für das StorSimple-Gerät](storsimple-device-failover-disaster-recovery.md).

3. Sie können nach der Deaktivierung und der Ausführung des Failovers das Gerät vollständig löschen. Anweisungen finden Sie unter [Löschen eines Geräts](#delete-a-device).

### Schritte zum Deaktivieren

Verwenden Sie das folgende Verfahren, um ein Gerät zur Vorbereitung auf das Löschen zu deaktivieren.

#### So deaktivieren Sie ein Gerät

1. Wählen Sie auf der Seite **Geräte** des StorSimple Manager-Diensts das Gerät aus, das Sie deaktivieren möchten, und klicken Sie unten auf der Seite auf **Deaktivieren**.

2. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie auf **Ja**, um fortzufahren. Der Prozess zum Deaktivieren beginnt und dauert einige Minuten.

    Auf einem virtuellen StorSimple-Gerät führt das Deaktivieren zu den folgenden Aktionen:

      - Das virtuelle StorSimple-Gerät wird entfernt.

      - Die für das virtuelle StorSimple-Gerät erstellten OSDisks und Datenträger werden entfernt.

      - Der bei der Bereitstellung erstellte gehostete Dienst und das Virtual Network werden beibehalten. Wenn diese Entitäten nicht verwendet werden, sollten sie manuell gelöscht werden.

      - Vom virtuellen StorSimple-Gerät erstellte Cloudmomentaufnahmen werden beibehalten.

<!--After the device is deactivated, you will need to perform a failover before you can delete it completely. For failover instructions, go to [Failover and disaster recovery for your StorSimple device](storsimple-device-failover-disaster-recovery.md).-->
## Löschen eines Geräts

Sie können nur Geräte löschen, die deaktiviert wurden. Das Löschen eines Geräts entfernt es aus der Liste der mit dem Dienst verbundenen Geräte. Der Dienst kann das gelöschte Gerät dann nicht mehr länger verwalten.

#### So löschen Sie ein Gerät

1. Wählen Sie auf der Seite **Geräte** des StorSimple Manager-Diensts ein deaktiviertes Gerät, das Sie löschen möchten.

2. Klicken Sie unten auf der Seite auf **Löschen**.

3. Sie werden aufgefordert, diesen Schritt zu bestätigen. Klicken Sie auf **Ja**, um fortzufahren.

Es dauert einige Minuten, bis das Gerät gelöscht wird.

## Nächste Schritte
- Um das deaktivierte Gerät mit den Werkseinstellungen wiederherzustellen, wechseln Sie zu [Zurücksetzen des Geräts auf die Standardwerkseinstellungen](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Um technische Unterstützung zu erhalten, [kontaktieren Sie den Microsoft-Support](storsimple-contact-microsoft-support.md).

- Weitere Informationen zum Verwenden des StorSimple Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->