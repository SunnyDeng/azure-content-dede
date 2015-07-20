<properties 
   pageTitle="Installieren von Update 1 auf dem StorSimple-Gerät"
   description="Erfahren Sie, wie Sie Update 1 der StorSimple 8000 Serie auf Ihrem Gerät installieren."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# Installieren von Update 1 auf dem StorSimple-Gerät

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie Update 1 auf einem StorSimple-Gerät installieren, auf dem eine Version vor Update 1 ausgeführt wird. Auf dem Gerät wird möglicherweise die allgemein verfügbare GA-Version oder Update 0.1-, Update 0.2- bzw. Update 0.3-Software ausgeführt.

Wenn auf Ihrem Gerät eine Version vor Update 1 ausgeführt wird, werden bei dieser Installation Prüfungen auf dem Gerät durchgeführt. Diese Prüfungen ermitteln den Gerätezustand im Hinblick auf Hardwarestatus und Netzwerkkonnektivität.

Sie werden aufgefordert, eine manuelle Vorprüfung durchzuführen, um Folgendes sicherzustellen:

- Die stationären Controller-IP-Adressen sind routbar und können eine Verbindung mit dem Internet herstellen. Diese IP-Adressen werden verwendet, um Ihr StorSimple-Gerät mit Updates zu versorgen. Sie können dies prüfen, indem Sie das folgende Cmdlet auf jedem Controller ausführen:

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
	    
	    


- Vor der Aktualisierung des Geräts empfehlen wir, eine Cloud-Momentaufnahme der Gerätedaten zu erstellen.

Nachdem Sie die manuellen Prüfungen (siehe oben) überprüft und bestätigt haben, wird eine Reihe von automatischen Prüfungen vor dem Update durchgeführt. Diese umfassen:

- **Controller Integritätsprüfungen**, um sicherzustellen, dass die beiden Gerätecontroller fehlerfrei und online sind.

- **Integritätsprüfungen für Hardware-Komponente**, um zu überprüfen, ob alle Hardwarekomponenten auf dem StorSimple-Gerät fehlerfrei sind.

- **DATA 0-Prüfungen**, um sicherzustellen, dass DATA 0 auf Ihrem Gerät aktiviert ist. Wenn diese Schnittstelle nicht aktiviert ist, müssen Sie sie aktivieren, und es dann erneut versuchen.

- **DATA 2- und DATA 3-Prüfungen**, um sicherzustellen, dass DATA 2- und DATA 3-Netzwerkschnittstellen nicht aktiviert sind. Wenn diese Schnittstellen aktiviert sind, müssen Sie deaktiviert werden, bevor Sie Ihr Gerät aktualisieren. Diese Prüfung wird nur bei der Aktualisierung auf einem Gerät durchgeführt, auf dem GA-Software ausgeführt wird. Bei Geräten, auf denen Versionen von 0.1, 0.2 oder 0.3 ausgeführt werden, ist diese Prüfung nicht erforderlich.

- **Gateway-Prüfung** auf jedem Gerät, auf dem eine Version vor Update 1 ausgeführt wird. Diese Prüfung wird nur auf Geräten durchgeführt, auf denen ein Gateway für eine andere Netzwerkschnittstelle als DATA 0 konfiguriert ist.
 
Update 1 wird nur angewendet, wenn alle vor dem Update durchgeführten Prüfungen erfolgreich abgeschlossen wurden. Nachdem Sie Update 1 auf dem StorSimple-Gerät angewendet haben, werden bei zukünftigen Updates die Data 2- und Data 3-Prüfungen sowie die Gateway-Prüfung nicht mehr durchgeführt. Die anderen Vorabprüfungen werden durchgeführt.

## Verwenden des Verwaltungsportals zum Installieren von Update 1

Es wird empfohlen, das Azure-Verwaltungsportal zu verwenden, um ein Gerät zu aktualisieren, aus dem die GA-Version ausgeführt wird. Führen Sie dazu die folgenden Schritte aus, um Ihr Gerät zu aktualisieren:

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]


## Problembehandlung bei Update-Fehlern

**Was geschieht, wenn über eine Meldung angezeigt wird, dass die Prüfungen vor dem Upgrade fehlgeschlagen sind?**

Wenn eine Vorabprüfung fehlschlägt, beachten Sie unbedingt die ausführliche Benachrichtigungsleiste unten auf der Seite. Dort erfahren Sie, welche Vorabprüfung fehlgeschlagen ist. Die folgende Abbildung zeigt ein Beispiel, in dem eine solche Benachrichtigung angezeigt wird. In diesem Fall sind die Integritätsprüfungen für Controller und für Hardware-Komponenten fehlgeschlagen. Unter dem Abschnitt **Hardwarestatus** können Sie sehen, dass Controller 0 -und Controller 1-Komponenten Aufmerksamkeit erfordern.
 
  ![Fehler bei der Vorabprüfung](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Sie müssen sicherstellen, dass beide Controller fehlerfrei und online sind. Sie müssen außerdem sicherstellen, dass alle Hardwarekomponenten im StorSimple-Gerät auf der Seite "Wartung" als fehlerfrei angezeigt werden. Sie können dann versuchen, Updates zu installieren. Wenn Sie Probleme mit Hardware-Komponenten nicht beheben können, müssen Sie sich an den Microsoft-Support wenden, um die nächsten Schritte durchzuführen.

**Was geschieht, wenn Sie die Fehlermeldung "Updates konnten nicht installiert werden" erhalten und empfohlen wird, die Anleitung zur Update-Problembehandlung hinzuzuziehen, um die Fehlerursache zu bestimmen?**

Eine wahrscheinliche Ursache könnte sein, dass Sie nicht mit den Microsoft Update-Servern verbunden sind. In diesem Fall muss eine manuelle Überprüfung erfolgen. Wenn die Verbindung mit dem Updateserver unterbrochen wird, schlägt der Aktualisierungsauftrag fehl. Sie können das folgende Cmdlet in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausführen, um die Konnektivität zu prüfen:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Führen Sie das Cmdlet auf beiden Controllern aus.
 
Wenn Sie sichergestellt haben, dass die Verbindung vorhanden ist, und dieses Problem weiter besteht, wenden Sie sich an den Microsoft Support, um die nächsten Schritte durchzuführen.

## Nächste Schritte

Erfahren Sie mehr über [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=July15_HO2-->