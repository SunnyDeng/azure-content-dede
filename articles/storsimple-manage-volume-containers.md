<properties 
   pageTitle="Verwalten der StorSimple-Volumecontainer"
   description="Beschreibt, wie Sie die Seite &quot;Volumecontainer&quot; des StorSimple-Manager-Dienstes zum Hinzufügen, Ändern oder Löschen eines Volumecontainers verwenden können."
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
   ms.date="05/13/2015"
   ms.author="v-sharos" />

# Verwalten der StorSimple-Volumecontainer

## Übersicht

In diesem Lernprogramm wird erläutert, wie Sie den StorSimple-Manager-Dienst zum Erstellen und Verwalten von StorSimple-Volumecontainern verwenden.

Ein Volumecontainer in einem Microsoft Azure StorSimple-Gerät enthält ein oder mehrere Volumes, die gemeinsame Einstellungen für das Speicherkonto, die Verschlüsselung und die Bandbreitenauslastung haben. Ein Gerät kann mehrere Volumecontainer für alle Volumes aufweisen.

Ein Volumecontainer verfügt über die folgenden Attribute:

- **Volumes** – Die nach Bedarf bereitgestellten StorSimple-Volumes, die im Volumecontainer enthalten sind. Ein Volumecontainer kann bis zu 256 nach Bedarf bereitgestellte StorSimple-Volumes enthalten.

- **Verschlüsselung** – Ein Verschlüsselungsschlüssel, der für jeden Volumecontainer definiert werden kann. Mit diesem Schlüssel werden die vom StorSimple-Gerät an die Cloud gesendeten Daten verschlüsselt.  Zusammen mit dem vom Benutzer eingegebenen Schlüssel wird ein AES-256-Bit-Schlüssel (gemäß militärischen Sicherheitsstandards) verwendet. Zum Schutz Ihrer Daten wird empfohlen, immer die Cloud-Speicherverschlüsselung zu aktivieren.

- **Speicherkonto** – Das mit dem Anbieter des Cloud-Speicherdienstes verknüpfte Speicherkonto. Alle Volumes in einem Volumecontainer nutzen dieses Speicherkonto gemeinsam. Sie können ein Speicherkonto aus einer vorhandenen Liste auswählen oder beim Erstellen des Volumecontainers ein neues Konto erstellen und dann die Anmeldeinformationen für das Konto angeben.

- **Cloud-Bandbreite** – Die vom Gerät beanspruchte Bandbreite, wenn Daten vom Gerät an die Cloud gesendet werden. Sie können die Bandbreite steuern, indem Sie beim Definieren dieses Containers einen Wert zwischen 1 und 1.000 MBit/s angeben. Wenn das Gerät die gesamte verfügbare Bandbreite nutzen soll, legen Sie dieses Feld auf "Unbegrenzt" fest. Sie können auch eine Bandbreitenvorlage erstellen und anwenden, um Bandbreite basierend auf einem Zeitplan zuzuweisen.

![Seite "Volumecontainer"](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Bei den folgenden Verfahren wird erläutert, wie Sie die Seite **Volumecontainer** verwenden, um die folgenden allgemeinen Vorgänge durchzuführen:

- Hinzufügen eines Volumecontainers 
- Ändern eines Volumecontainers 
- Löschen eines Volumecontainers 

## Hinzufügen eines Volumecontainers

Führen Sie die folgenden Schritte aus, um einen Volumecontainer zu erstellen.

[AZURE.INCLUDE [storsimple-add-volume-container](../includes/storsimple-add-volume-container.md)]


## Ändern eines Volumecontainers

Führen Sie die folgenden Schritte aus, um einen Volumecontainer zu ändern.

[AZURE.INCLUDE [storsimple-modify-volume-container](../includes/storsimple-modify-volume-container.md)]


## Löschen eines Volumecontainers

In einem Volumecontainer sind Volumes enthalten. Er kann nur gelöscht werden, nachdem alle darin enthaltenen Volumes gelöscht wurden. Führen Sie die folgenden Schritte aus, um einen Volumecontainer zu löschen.

[AZURE.INCLUDE [storsimple-delete-volume-container](../includes/storsimple-delete-volume-container.md)]

## Nächste Schritte

Erfahren Sie mehr über das [Verwalten von StorSimple-Volumes](storsimple-manage-volumes.md).

<!---HONumber=58-->