<properties 
   pageTitle="Verwalten von StorSimple-Sicherungsrichtlinien | Microsoft Azure"
   description="Beschreibt, wie Sie den StorSimple Manager-Dienst zum Erstellen und Verwalten von Sicherungsaufträgen und Sicherungszeitplänen verwenden können."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/17/2015"
   ms.author="v-sharos"/>

# Verwalten von StorSimple-Sicherungsrichtlinien

## Übersicht

In diesem Lernprogramm wird erläutert, wie Sie die Seite **Sicherungsrichtlinien** des StorSimple-Manager-Dienstes verwenden, um Sicherungsprozesse und die Sicherungsaufbewahrung für Ihre StorSimple-Volumes zu steuern. Außerdem wird beschrieben, wie eine manuelle Sicherung durchgeführt wird.

Über die Seite **Sicherungsrichtlinien** können Sie Sicherungsrichtlinien verwalten und lokale sowie Cloud-Momentaufnahmen zeitlich planen. (Sicherungsrichtlinien werden zum Konfigurieren von Sicherungszeitplänen und der Sicherungsaufbewahrung für eine Sammlung von Volumes verwendet.) Mithilfe von Sicherungsrichtlinien können Sie eine Momentaufnahme mehrerer Volumes gleichzeitig erstellen. Dies bedeutet, dass es sich bei durch Sicherungsrichtlinien erstellten Sicherungen um ausfallsichere Kopien handelt. Auf dieser Seite werden alle Sicherungsrichtlinien, ihre Typen, die zugehörigen Volumes, die Anzahl der aufbewahrten Sicherungen und die Option zum Aktivieren dieser Richtlinien aufgeführt.

Auf der Seite **Sicherungsrichtlinien** können Sie außerdem die vorhandenen Sicherungsrichtlinien anhand von einem oder mehreren der folgenden Felder filtern:

- **Richtlinienname** – Der der Richtlinie zugeordnete Name. Die folgenden verschiedenen Richtlinientypen sind verfügbar:

   - Geplante Richtlinien, die explizit vom Benutzer erstellt werden.
   - Automatische Richtlinien, die erstellt werden, wenn die Option "Standardsicherung für dieses Volume" zum Zeitpunkt der Volumeerstellung aktiviert war Diese Richtlinien tragen den Namen "VolumeName_Default". Dabei bezieht sich "VolumeName" auf den Namen des StorSimple-Volumes, das vom Benutzer im Verwaltungsportal konfiguriert wird. Die automatischen Richtlinien bewirken tägliche Cloud-Momentaufnahmen, die um 22:30 Uhr Gerätezeit beginnen.
   - Importierte Richtlinien, die ursprünglich im StorSimple-Momentaufnahme-Manager erstellt wurden. Diese weisen ein Tag auf, das den StorSimple-Momentaufnahme-Manager-Host beschreibt, von dem die Richtlinien importiert wurden.

- **Volumes** – Die Volumes, die der Richtlinie zugeordnet sind. Alle Volumes, die einer Sicherungsrichtlinie zugeordnet sind, werden beim Erstellen von Sicherungen gruppiert.

- **Letzte erfolgreiche Sicherung** – Das Datum und die Uhrzeit der letzten erfolgreichen Sicherung, die mit dieser Richtlinie erstellt wurde.

- **Nächste Sicherung** – Das Datum und die Uhrzeit der nächsten geplanten Sicherung, die von dieser Richtlinie initiiert wird.

- **Zeitpläne** – Die Anzahl der Zeitpläne, die der Sicherungsrichtlinie zugeordnet sind.

Auf dieser Seite können die folgenden allgemeinen Vorgänge ausgeführt werden:

- Hinzufügen einer Sicherungsrichtlinie 
- Hinzufügen oder Ändern eines Zeitplans 
- Löschen einer Sicherungsrichtlinie 
- Erstellen einer manuellen Sicherung 
- Erstellen einer benutzerdefinierten Sicherungsrichtlinie mit mehreren Volumes und Zeitplänen 

## Hinzufügen einer Sicherungsrichtlinie

Fügen Sie eine Sicherungsrichtlinie zum automatischen Planen Ihrer Sicherungen hinzu. Führen Sie die folgenden Schritte im Verwaltungsportal aus, um eine Sicherungsrichtlinie für Ihr StorSimple-Gerät hinzuzufügen. Nachdem Sie die Richtlinie hinzugefügt haben, können Sie einen Zeitplan definieren (siehe [Hinzufügen oder Ändern eines Zeitplans](#add-or-modify-a-schedule).

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]


## Hinzufügen oder Ändern eines Zeitplans

Sie können einen Zeitplan hinzufügen oder ändern, der an eine vorhandene Sicherungsrichtlinie auf Ihrem StorSimple-Gerät angefügt wird. Führen Sie die folgenden Schritte im Verwaltungsportal aus, um einen Zeitplan hinzuzufügen oder zu ändern:

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## Löschen einer Sicherungsrichtlinie

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um eine Sicherungsrichtlinie auf Ihrem StorSimple-Gerät zu löschen.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## Erstellen einer manuellen Sicherung

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um bei Bedarf eine (manuelle) Sicherung für ein einzelnes Volume zu erstellen.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## Erstellen einer benutzerdefinierten Sicherungsrichtlinie mit mehreren Volumes und Zeitplänen

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um eine benutzerdefinierte Sicherungsrichtlinie mit mehreren Volumes und Zeitplänen zu erstellen.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## Nächste Schritte

Erfahren Sie, wie Sie StorSimple-Sicherungen mit dem [StorSimple-Momentaufnahme-Manager](https://msdn.microsoft.com/library/azure/dn772365.aspx) verwalten.

<!---HONumber=58_postMigration-->