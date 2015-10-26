<properties
   pageTitle="Klonen von StorSimple-Volumes | Microsoft Azure"
   description="Beschreibt die verschiedenen Klontypen und ihre Verwendung und wie Sie mit einem Sicherungssatz ein einzelnes Volume klonen."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/12/2015"
   ms.author="alkohli" />

# Klonen eines Volumes mithilfe des StorSimple Manager-Diensts

## Übersicht

Auf der Seite **Sicherungskatalog** des StorSimple Manager-Diensts werden alle Sicherungssätze angezeigt, die mithilfe manueller oder automatisierter Sicherungen erstellt wurden. Sie können auf dieser Seite alle Sicherungen für eine Sicherungsrichtlinie oder ein Volume auflisten, Sicherungen auswählen oder löschen oder eine Sicherung zum Wiederherstellen oder Klonen eines Volumes verwenden.

![Seite "Sicherungskatalog"](./media/storsimple-clone-volume/HCS_BackupCatalog.png)

In diesem Tutorial wird beschrieben, wie Sie einen Sicherungssatz zum Klonen eines einzelnen Volumes verwenden können. Außerdem wird der Unterschied zwischen *vorübergehenden* und *dauerhaften* Klonen beschrieben.

## Erstellen von Klonen von einem Volume

Sie können einen Klon anhand einer lokalen oder einer Cloudmomentaufnahme auf dem gleichen Gerät, auf einem anderen Gerät oder sogar auf einem virtuellen Computer erstellen.

#### So klonen Sie ein Volume

1. Klicken Sie auf der Seite des StorSimple-Manager-Diensts auf die Registerkarte **Sicherungskatalog**, und wählen Sie einen Sicherungssatz.

2. Erweitern Sie einen Sicherungssatz, um die zugehörigen Volumes anzuzeigen. Klicken Sie auf ein Volume im Sicherungssatz, um dieses auszuwählen.

     ![Volume klonen](./media/storsimple-clone-volume/HCS_Clone.png)

3. Klicken Sie auf **Klonen**, um mit dem Klonen des ausgewählten Volumes zu beginnen.

4. Führen Sie im Assistenten zum Klonen von Volumes unter **Name und Pfad angeben** folgende Schritte aus:

  1. Ermitteln Sie ein Zielgerät. Dies ist der Speicherort, an dem der Klon erstellt wird. Sie können das gleiche Gerät auswählen oder ein anderes Gerät angeben. Bei Auswahl eines Volumes, das anderen Cloud-Service Providern als Azure zugeordnet ist, werden in der Dropdownliste für das Zielgerät nur physische Geräte angezeigt. Ein Volume kann nicht auf ein virtuelles Gerät geklont werden, wenn es anderen Cloud-Service Providern zugeordnet ist.

        >  [AZURE.NOTE] Make sure that the capacity required for the clone is lower than the capacity available on the target device.
  2. Geben Sie einen eindeutigen Volumenamen für Ihren Klon an. Der Name muss zwischen 3 und 127 Zeichen lang sein.
  3. Klicken Sie auf den Pfeil ![Pfeilsymbol](./media/storsimple-clone-volume/HCS_ArrowIcon.png), um zur nächsten Seite zu wechseln.

5. Führen Sie unter **Hosts angeben, die dieses Volume verwenden können** folgende Schritte aus:

  1. Geben Sie einen Zugriffssteuerungsdatensatz (ACR) für den Klon an. Sie können einen neuen ACR hinzufügen oder diesen aus der Liste mit vorhandenen ACRs auswählen.
  2. Klicken Sie auf das Häkchen ![Häkchensymbol](./media/storsimple-clone-volume/HCS_CheckIcon.png), um den Vorgang abzuschließen.

6. Es wird ein Klonauftrag initiiert. Sie werden benachrichtigt, wenn der Klon erstellt wurde. Klicken Sie auf **Auftrag anzeigen**, um den Klonauftrag auf der Seite **Aufträge** zu überwachen.

7. Nach Abschluss des Klonauftrags:

  1. Navigieren Sie zur Seite **Geräte**, und klicken Sie auf die Registerkarte **Volumecontainer**.
  2. Wählen Sie den Volumecontainer aus, der dem geklonten Ausgangsvolume zugeordnet ist. In der Liste der Volumes sollte der gerade erstellte Klon angezeigt werden.

>[AZURE.NOTE]Die Überwachung und die Standardsicherung werden auf geklonten Volumes automatisch deaktiviert.

Ein auf diese Weise erstellter Klon ist ein vorübergehender Klon. Weitere Informationen zu Klontypen finden Sie unter [Vergleich von vorübergehenden und dauerhaften Klonen](#transient-vs.-permanent-clones).

Dieser Klon ist jetzt ein reguläres Volume, und jeder mit einem Volume mögliche Vorgang kann auch für den Klon durchgeführt werden. Sie müssen dieses Volume für Sicherungen konfigurieren.

## Vergleich von vorübergehenden und dauerhaften Klonen

Sie können ein bestimmtes Volume aus einem Sicherungssatz klonen. Ein auf diese Weise erstellter Klon ist ein *vorübergehender* Klon. Der vorübergehende Klon enthält Verweise auf das ursprüngliche Volume, und er verwendet dieses für das Lesen während des lokalen Schreibens. Dies kann zu einer Leistungsverminderung führen. Dies gilt insbesondere dann, wenn das geklonte Volume groß ist.

Nachdem Sie eine Cloudmomentaufnahme eines vorübergehenden Klons erstellt haben, wird der daraus resultierende Klon ein *dauerhafter* Klon. Der dauerhafte Klon ist unabhängig und verfügt nicht über Verweise auf das ursprüngliche Volume, aus dem er geklont wurde. Für eine höhere Leistung wird das Erstellen dauerhafter Klone empfohlen.

## Szenarios für vorübergehende und dauerhafte Klone

In den folgenden Abschnitten werden beispielhafte Situationen beschreiben, in denen vorübergehende und dauerhafte Klone verwendet werden können.

### Wiederherstellung auf Elementebene mit einem vorübergehenden Klon

Sie müssen eine ein Jahr alte Datei mit einer Microsoft PowerPoint-Präsentation wiederherstellen. Der IT-Administrator identifiziert die entsprechende Sicherung aus diesem Zeitraum und filtert das Volume dann. Anschließend klont der Administrator das Volume, ermittelt die Datei, nach der Sie suchen, und stellt sie Ihnen dann zur Verfügung. In diesem Szenario wird ein vorübergehender Klon verwendet.
 
![Video verfügbar](./media/storsimple-clone-volume/Video_icon.png) **Video verfügbar**

Um ein Video zu schauen, in dem gezeigt wird, wie Sie mithilfe des Klons und Wiederherstellungsfunktionen in StorSimple gelöschte Dateien wiederherstellen können, klicken Sie [hier](http://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### Testen in der Produktionsumgebung mit einem dauerhaften Klon

Sie müssen einen Testfehler in der Produktionsumgebung überprüfen. Sie erstellen einen Klon des Volumes in der Produktionsumgebung. Zur Leistungsoptimierung müssen Sie eine Cloudmomentaufnahme dieses Klons erstellen. Das geklonte Volume ist jetzt unabhängig, wodurch eine höhere Leistung erzielt wird. In diesem Szenario wird ein dauerhafter Klon verwendet.

## Nächste Schritte
- Erfahren Sie, wie Sie [StorSimple-Volumes aus einem Sicherungssatz wiederherstellen](storsimple-restore-from-backup-set.md).

- Informationen zum [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

 

<!---HONumber=Oct15_HO3-->