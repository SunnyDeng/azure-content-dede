<properties
   pageTitle="Installieren von Update 2 auf dem StorSimple-Gerät | Microsoft Azure"
   description="Erfahren Sie, wie Sie Update 2 für die StorSimple 8000-Serie auf Ihrem StorSimple-Gerät der Serie 8000 installieren."
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
   ms.date="03/21/2016"
   ms.author="alkohli" />

# Installieren von Update 2 auf Ihrem StorSimple-Gerät

## Übersicht

In diesem Tutorial wird beschrieben, wie Sie Update 2 auf einem StorSimple-Gerät mit einer früheren Softwareversion im klassischen Azure-Portal ausführen. Im Tutorial werden auch die erforderlichen Schritte für das Update behandelt, wenn ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 des StorSimple-Geräts konfiguriert ist und Sie versuchen, das Update von einer Softwareversion vor Update 1 durchzuführen.

Update 2 umfasst Gerätesoftwareupdates, LSI-Treiberupdates und Updates für die Datenträgerfirmware. Die Gerätesoftware- und LSI-Updates führen nicht zu einer Unterbrechung des Betriebs und können über das klassische Azure-Portal angewendet werden. Die Updates für die Datenträgerfirmware führen zu einer Betriebsunterbrechung und können nur über die Windows PowerShell-Schnittstelle des Geräts angewendet werden.

> [AZURE.IMPORTANT]

> -  Update 2 wird möglicherweise nicht sofort angezeigt, da Updates in mehreren Phasen bereitgestellt werden. Suchen Sie in einigen Tagen erneut nach Updates, da dieses Update bald zur Verfügung steht.
> - Vor der Installation wird ein Satz manueller und automatischer Vorabprüfungen durchgeführt, mit denen die Geräteintegrität in Bezug auf Hardwarestatus und Netzwerkkonnektivität ermittelt wird. Diese Vorabprüfungen werden nur ausgeführt, wenn Sie die Updates aus dem klassischen Azure-Portal ausführen.
> - Wir empfehlen, die Software- und Treiberupdates über das klassische Azure-Portal zu installieren. Sie sollten nur dann die Windows PowerShell-Schnittstelle des Geräts (zum Installieren der Updates) verwenden, wenn die Vorabprüfung für das Gateway im Portal fehlschlägt. Die Installation des Updates kann zwischen 4 und 7 Stunden dauern (Windows-Updates eingeschlossen). Wartungsmodusupdates müssen über die Windows PowerShell-Schnittstelle des Geräts ausgeführt werden. Da Updates im Wartungsmodus den Betrieb unterbrechen, führen sie zu einer Ausfallzeit für Ihr Gerät.
> - Stellen Sie bei Ausführung des optionalen StorSimple Snapshot Manager sicher, dass Sie die Snapshot Manager-Version auf Update 2 aktualisiert haben, bevor Sie das Gerät aktualisieren.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installieren von Update 2 über das klassische Azure-Portal

Führen Sie die folgenden Schritte aus, um Ihr Gerät auf [Update 2](storsimple-update2-release-notes.md) zu aktualisieren.


> [AZURE.NOTE]
Mit Update 2 kann Microsoft zusätzliche Diagnoseinformationen vom Gerät abrufen. Dadurch können wir besser Informationen auf dem Gerät sammeln und Probleme diagnostizieren, wenn unser Betriebsteam Geräte mit Problemen ermittelt. Durch Akzeptieren von Update 2 gestatten Sie uns die Bereitstellung dieses proaktiven Supports.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Stellen Sie sicher, dass auf Ihrem Gerät Folgendes ausgeführt wird: **StorSimple 8000 Series Update 2 (6.3.9600.17673)**. Das **Datum der letzten Aktualisierung** hat sich ebenfalls geändert. Sie werden auch feststellen, dass Wartungsmodus-Updates verfügbar sind. (Diese Meldung wird nach der Updateinstallation unter Umständen noch bis zu 24 Stunden lang angezeigt.)

    Wartungsmodus-Updates unterbrechen die Verwendung, führen zu Ausfallzeiten des Geräts und können nur über die Windows PowerShell-Schnittstelle Ihres Geräts angewendet werden. In einigen Fällen ist die Datenträgerfirmware beim Ausführen von Update 1.2 unter Umständen bereits auf dem neuesten Stand, sodass keine Wartungsmodus-Updates installiert werden müssen.

13. Laden Sie die Wartungsmodus-Updates herunter, indem Sie mithilfe der unter [Herunterladen von Hotfixes](#to-download-hotfixes) angegebenen Schritte nach KB3121899 suchen und es herunterladen, um Datenträger-Firmwareupdates zu installieren. (Die anderen Updates müssten inzwischen bereits installiert sein.)

13. Führen Sie die unter [Installieren und Überprüfen von Wartungsmodus-Hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) angegebenen Schritte aus, um die Wartungsmodus-Updates zu installieren.


## Installieren von Update 2 als Hotfix

Gehen Sie wie folgt vor, falls beim Installieren über das klassische Azure-Portal ein Fehler bei der Gatewayprüfung auftritt. Die Überprüfung zeigt einen Fehler, weil Sie ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 zugewiesen haben und auf Ihrem Gerät eine Softwareversion vor Update 1 ausgeführt wird.

Die Softwareversionen, die mit dem Hotfixverfahren aktualisiert werden können, sind Update 0.1, Update 0.2 und Update 0.3 sowie Update 1, Update 1.1 und Update 1.2. Das Hotfixverfahren umfasst die folgenden drei Schritte:

- Laden Sie die Hotfixes aus dem Microsoft Update-Katalog herunter.
- Installieren und überprüfen Sie die Hotfixes für den normalen Modus.
- Installieren und überprüfen Sie den Hotfix für den Wartungsmodus.

Wenn Sie Update 2 als Hotfix installieren möchten, müssen Sie folgende Hotfixes herunterladen und installieren:

| Reihenfolge | KB | Beschreibung | Updatetyp |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | Softwareupdate | Normal |
| 2 | KB3121900 | LSI-Treiber | Normal |
| 3 | KB3080728 | Storport-Fix </br> Windows Server 2012 R2 | Normal |
| 4 | KB3090322 | Spaceport-Fix </br> Windows Server 2012 R2 | Normal |
| 5 | KB3121899 | Datenträgerfirmware | Wartung |


> [AZURE.IMPORTANT]
>
> - Wenn auf dem Gerät die Releaseversion (GA) ausgeführt wird, wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md), um Hilfe bei diesem Update zu erhalten.
> - Dieses Verfahren muss nur einmal ausgeführt werden, um Update 2 anzuwenden. Über das klassischen Azure-Portal können Sie nachfolgende Updates installieren.
> - Jede Hotfixinstallation kann etwa 20 Minuten dauern. Die gesamte Installationsdauer beträgt fast zwei Stunden.
> - Stellen Sie vor dem Verwenden dieses Verfahrens zum Anwenden des Updates sicher, dass beide Gerätecontroller online und alle Hardwarekomponenten fehlerfrei sind.

Führen Sie die folgenden Schritte aus, um dieses Update als Hotfix anzuwenden.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## Nächste Schritte

Weitere Informationen finden Sie unter [Update 2-Version](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0323_2016-->