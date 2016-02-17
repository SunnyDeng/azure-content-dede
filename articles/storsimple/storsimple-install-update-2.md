<properties 
   pageTitle="Installieren von Update 2 auf dem StorSimple-Gerät | Microsoft Azure"
   description="Erfahren Sie, wie Sie Update 2 für die StorSimple 8000-Serie auf Ihrem StorSimple-Gerät der Serie 8000 installieren."
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
   ms.date="01/27/2016"
   ms.author="alkohli" />

# Installieren von Update 2 auf Ihrem StorSimple-Gerät

## Übersicht

In diesem Tutorial wird beschrieben, wie Sie Update 2 auf einem StorSimple-Gerät mit einer früheren Softwareversion im klassischen Azure-Portal ausführen. Im Tutorial werden auch die erforderlichen Schritte für das Update behandelt, wenn ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 des StorSimple-Geräts konfiguriert ist und Sie versuchen, das Update von einer Softwareversion vor Update 1 durchzuführen.

Update 2 umfasst Gerätesoftwareupdates, LSI-Treiberupdates und Updates für die Datenträgerfirmware. Die Gerätesoftware- und LSI-Updates führen nicht zu einer Unterbrechung des Betriebs und können über das klassische Azure-Portal angewendet werden. Die Updates für die Datenträgerfirmware führen zu einer Betriebsunterbrechung und können nur über die Windows PowerShell-Schnittstelle des Geräts angewendet werden.

> [AZURE.IMPORTANT]
 
> -  Update 2 wird möglicherweise nicht sofort angezeigt, da Updates in mehreren Phasen bereitgestellt werden. Suchen Sie in einigen Tagen erneut nach Updates, da dieses Update bald zur Verfügung steht.
> - Vor der Installation wird ein Satz manueller und automatischer Vorabprüfungen durchgeführt, mit denen die Geräteintegrität in Bezug auf Hardwarestatus und Netzwerkkonnektivität ermittelt wird. Diese Vorabprüfungen werden nur ausgeführt, wenn Sie die Updates aus dem klassischen Azure-Portal ausführen. 
> - Wir empfehlen, die Software- und Treiberupdates über das klassische Azure-Portal zu installieren. Sie sollten nur dann die Windows PowerShell-Schnittstelle des Geräts (zum Installieren der Updates) verwenden, wenn die Vorabprüfung für das Gateway im Portal fehlschlägt. Die Installation des Updates kann zwischen 4 und 7 Stunden dauern (Windows-Updates eingeschlossen). Wartungsmodusupdates müssen über die Windows PowerShell-Schnittstelle des Geräts ausgeführt werden. Da Updates im Wartungsmodus den Betrieb unterbrechen, führen sie zu einer Ausfallzeit für Ihr Gerät.
> - Stellen Sie bei Ausführung des optionalen StorSimple Snapshot Manager sicher, dass Sie die Snapshot Manager-Version auf Update 2 aktualisiert haben, bevor Sie das Gerät aktualisieren.

## Vorbereitung auf Updates
Sie müssen die folgenden Schritte ausführen, bevor Sie das Update überprüfen und anwenden können:


1. Erstellen Sie eine Cloudmomentaufnahme der Gerätedaten.

2. Stellen Sie sicher, dass die festen Controller-IP-Adressen routingfähig sind und eine Verbindung mit dem Internet herstellen können. Diese festen IPs werden zum Anwenden von Updates auf Ihr Gerät verwendet. Sie können die Konnektivität testen, indem Sie von der Windows PowerShell-Schnittstelle des Geräts aus auf jedem Controller das folgende Cmdlet ausführen:

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**Beispielausgabe für die Testverbindung, wenn feste IP-Adressen eine Verbindung mit dem Internet herstellen können**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

Nachdem Sie diese manuellen Vorabprüfungen erfolgreich abgeschlossen haben, können Sie mit dem Überprüfen und Installieren der Updates beginnen.

## Installieren von Update 2 über das klassische Azure-Portal 

Dies ist das empfohlene Verfahren zum Aktualisieren des Geräts. Führen Sie die folgenden Schritte aus.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## Installieren von Update 2 als Hotfix 

Verwenden Sie dieses Verfahren nur, wenn die Gatewayüberprüfung beim Versuch, die Updates über das klassische Azure-Portal zu installieren, fehlgeschlagen ist. Die Überprüfung zeigt einen Fehler, weil Sie ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 zugewiesen haben und auf Ihrem Gerät eine Softwareversion vor Update 1 ausgeführt wird.

Die Softwareversionen, die mit dem Hotfixverfahren aktualisiert werden können, sind Update 0.1, Update 0.2 und Update 0.3 sowie Update 1, Update 1.1 und Update 1.2. Das Hotfixverfahren umfasst die folgenden drei Schritte:

- Laden Sie die Hotfixes aus dem Microsoft Update-Katalog herunter.
- Installieren und überprüfen Sie die Hotfixes für den normalen Modus.
- Installieren und überprüfen Sie den Hotfix für den Wartungsmodus.

Die Hotfixes, die mit diesem Verfahren angewendet werden, sind unten in der Tabelle angegeben:

| Reihenfolge | KB | Name | Updatetyp |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | Softwareupdate | Normal |
| 2 | KB3121900 | LSI-Treiber | Normal |
| 3 | KB3080728 | Storport-Fix | Normal |
| 4 | KB3090322 | Spaceport-Fix | Normal |
| 5 | KB3121899 | Datenträgerfirmware | Wartung |


> [AZURE.IMPORTANT] 
> 
> - Wenn auf dem Gerät die Releaseversion (GA) ausgeführt wird, wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md), um Hilfe bei diesem Update zu erhalten.
> - Dieses Verfahren muss nur einmal ausgeführt werden, um Update 2 anzuwenden. Über das klassischen Azure-Portal können Sie nachfolgende Updates installieren.
> - Jede Hotfixinstallation kann etwa 20 Minuten dauern. Die gesamte Installationsdauer beträgt fast zwei Stunden. 
> - Stellen Sie vor dem Verwenden dieses Verfahrens zum Anwenden des Updates sicher, dass beide Gerätecontroller online und alle Hardwarekomponenten fehlerfrei sind.

Führen Sie die folgenden Schritte aus, um Update 2 als Hotfix anzuwenden.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## Problembehandlung bei Update-Fehlern

**Was geschieht, wenn über eine Meldung angezeigt wird, dass die Prüfungen vor dem Upgrade fehlgeschlagen sind?**

Wenn eine Vorabprüfung fehlschlägt, beachten Sie unbedingt die ausführliche Benachrichtigungsleiste unten auf der Seite. Dort erfahren Sie, welche Vorabprüfung fehlgeschlagen ist. Die folgende Abbildung zeigt ein Beispiel, in dem eine solche Benachrichtigung angezeigt wird. In diesem Fall sind die Integritätsprüfungen für Controller und für Hardware-Komponenten fehlgeschlagen. Im Abschnitt **Hardwarestatus** können Sie sehen, dass **Controller 0**- und **Controller 1**-Komponenten Ihre Aufmerksamkeit erfordern.
 
  ![Fehler bei der Vorabprüfung](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

Sie müssen sicherstellen, dass beide Controller fehlerfrei und online sind. Sie müssen außerdem sicherstellen, dass alle Hardwarekomponenten im StorSimple-Gerät auf der Seite "Wartung" als fehlerfrei angezeigt werden. Sie können dann versuchen, Updates zu installieren. Wenn Sie Probleme mit Hardware-Komponenten nicht beheben können, müssen Sie sich an den Microsoft Support wenden, um die nächsten Schritte durchzuführen.

**Was geschieht, wenn Sie die Fehlermeldung "Updates konnten nicht installiert werden" erhalten und empfohlen wird, die Anleitung zur Update-Problembehandlung hinzuzuziehen, um die Fehlerursache zu bestimmen?**

Eine wahrscheinliche Ursache könnte sein, dass Sie nicht mit den Microsoft Update-Servern verbunden sind. In diesem Fall muss eine manuelle Überprüfung erfolgen. Wenn die Verbindung mit dem Updateserver unterbrochen wird, schlägt der Aktualisierungsauftrag fehl. Sie können das folgende Cmdlet in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausführen, um die Konnektivität zu prüfen:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Führen Sie das Cmdlet auf beiden Controllern aus.
 
Wenn Sie sichergestellt haben, dass die Verbindung vorhanden ist, und dieses Problem weiter besteht, wenden Sie sich an den Microsoft Support, um die nächsten Schritte durchzuführen.


## Nächste Schritte

Weitere Informationen finden Sie unter [Update 2-Version](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0128_2016-->