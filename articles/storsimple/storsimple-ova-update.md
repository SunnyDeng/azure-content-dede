<properties 
   pageTitle="Anwenden von StorSimple Virtual Array-Updates | Microsoft Azure"
   description="Hier wird beschrieben, wie Sie über die Webbenutzeroberfläche von StorSimple Virtual Array Updates und Hotfixes installieren."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Anwenden von Updates und Hotfixes auf StorSimple Virtual Array (Vorschau)

## Übersicht

Unter Umständen müssen Sie Softwareupdates oder -hotfixes installieren, um StorSimple Virtual Array auf dem neuesten Stand zu halten. Im Allgemeinen wird empfohlen, Updates über das klassische Azure-Portal zu installieren. Wenn das Portal nicht verfügbar ist, können Sie zum Anwenden von Hotfixes oder Updates jedoch auch die lokale Webbenutzeroberfläche verwenden. In diesem Tutorial wird beschrieben, wie Sie die lokale Webbenutzeroberfläche zum Anwenden eines Updates oder Hotfixes nutzen. (Informationen zur Vorgehensweise beim Installieren von Updates über das klassische Portal finden Sie unter [Installieren regelmäßiger Updates im klassischen Azure-Portal](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).)

Bedenken Sie, dass bei der Update- oder Hotfixinstallation das Gerät unter Umständen neu gestartet wird. Falls es sich bei StorSimple Virtual Array um ein Gerät mit einem Knoten handelt, werden alle aktuellen IOs unterbrochen, und das Gerät fällt einige Zeit aus.

## Verwenden der lokalen Webbenutzeroberfläche zum Anwenden eines Updates oder Hotfixes

Stellen Sie vor der Update- oder Hotfixinstallation sicher, dass das Update oder der Hotfix entweder lokal auf Ihren Host heruntergeladen wurde bzw. dass auf das Update oder den Hotfix über eine Netzwerkfreigabe zugegriffen werden kann.

#### So installieren Sie das Update oder den Hotfix

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**.

2. Geben Sie in **Updatedateipfad** den Dateinamen für das Update oder den Hotfix ein. Sie können auch zur Installationsdatei des Updates oder Hotfixes navigieren, sofern sie auf einer Netzwerkfreigabe abgelegt wurde. Das Update wird gestartet. Nach Abschluss des Updates erhalten Sie eine Benachrichtigung.

    ![Gerät aktualisieren](./media/storsimple-ova-update/image43.png)

## Nächste Schritte

Erfahren Sie, wie Sie [StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0121_2016-->