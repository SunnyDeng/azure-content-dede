<properties 
   pageTitle="Installieren von Update 1.2 auf dem StorSimple-Gerät | Microsoft Azure"
   description="Erfahren Sie, wie Sie Update 1.2 für die StorSimple 8000-Serie auf Ihrem StorSimple-Gerät der 8000-Serie installieren."
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
   ms.date="11/16/2015"
   ms.author="alkohli" />

# Installieren von Update 1.2 auf Ihrem StorSimple-Gerät

## Übersicht

In diesem Tutorial wird erläutert, wie Sie Update 1.2 auf einem StorSimple-Gerät installieren, auf dem eine Softwareversion vor Update 1 installiert ist. Es werden außerdem die zusätzlichen Schritte beschrieben, die für das Update erforderlich sind, wenn ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 des StorSimple-Geräts konfiguriert ist.

Update 1.2 umfasst Gerätesoftwareupdates, LSI-Treiberupdates und Updates für die Datenträgerfirmware. Die Software- und LSI-Treiberupdates führen nicht zu einer Unterbrechung des Betriebs und können über das Azure-Portal angewendet werden. Die Updates für die Datenträgerfirmware führen zu einer Betriebsunterbrechung und können nur über die Windows PowerShell-Schnittstelle des Geräts angewendet werden.

Abhängig davon, welche Version auf Ihrem Gerät ausgeführt wird, wird Update 1.2 angewendet. Prüfen Sie die Softwareversion Ihres Geräts, indem Sie zum Abschnitt **Auf einen Blick** im **Dashboard** für Ihr Gerät wechseln.

</br>

| Ausgeführte Softwareversion | Was geschieht im Portal? |
|---------------------------------|--------------------------------------------------------------|
| Release – GA | Wenn Sie die Releaseversion (GA) ausführen, wenden Sie dieses Update nicht an. [Wenden Sie sich an den Microsoft Support](storsimple-contact-microsoft-support.md), um Ihr Gerät zu aktualisieren.|
| Update 0.1 | Das Portal wendet Update 1.2 an. |
| Update 0.2 | Das Portal wendet Update 1.2 an. |
| Update 0.3 | Das Portal wendet Update 1.2 an. |
| Update 1 | Dieses Update ist nicht verfügbar. |
| Update 1.1 | Dieses Update ist nicht verfügbar. |

</br>

> [AZURE.IMPORTANT]
 
> -  Update 1.2 wird möglicherweise nicht sofort angezeigt, da Updates in mehreren Phasen bereitgestellt werden. Suchen Sie in einigen Tagen erneut nach Updates, da dieses Update bald zur Verfügung steht.
> - Dieses Update umfasst einen Satz manueller und automatischer Vorabprüfungen, mit denen die Geräteintegrität in Bezug auf Hardwarestatus und Netzwerkkonnektivität ermittelt wird. Diese Vorabprüfungen werden nur ausgeführt, wenn Sie die Updates aus dem Azure-Portal ausführen. 
> - Wir empfehlen, die Software- und Treiberupdates über das Azure-Portal zu installieren. Sie sollten nur dann die Windows PowerShell-Schnittstelle des Geräts (zum Installieren der Updates) verwenden, wenn die Vorabprüfung für das Gateway im Portal fehlschlägt. Die Installation des Updates kann zwischen 5 und 10 Stunden dauern (Windows-Updates eingeschlossen). Wartungsmodusupdates müssen über die Windows PowerShell-Schnittstelle des Geräts ausgeführt werden. Da Updates im Wartungsmodus den Betrieb unterbrechen, führen sie zu einer Ausfallzeit für Ihr Gerät.

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

## Installieren von Update 1.2 über das Azure-Portal 

Führen Sie dieses Verfahren nur dann aus, wenn Sie ein Gateway auf der DATA 0-Netzwerkschnittstelle Ihres Geräts konfiguriert haben. Führen Sie dazu die folgenden Schritte aus, um Ihr Gerät zu aktualisieren:

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]

## Installieren von Update 1.2 auf einem Gerät mit einem Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 

Sie sollten dieses Verfahren nur dann verwenden, wenn die Gatewayüberprüfung beim Versuch, die Updates über das Azure-Portal zu installieren, fehlgeschlagen ist. Die Überprüfung zeigt einen Fehler, weil Sie ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 zugewiesen haben und auf Ihrem Gerät eine Softwareversion vor Update 1 ausgeführt wird. Wenn Ihr Gerät kein Gateway aufweist, das auf eine andere Netzwerkschnittstelle als DATA 0 eingestellt ist, können Sie das Gerät direkt über das Azure-Portal aktualisieren. Weitere Informationen finden Sie unter [Installieren von Update 1.2 über das Azure-Portal](#install-update-12-via-the-azure-portal).

Die Softwareversionen, die mithilfe dieser Methode aktualisiert werden können, sind Update 0.1, Update 0.2 und Update 0.3.


> [AZURE.IMPORTANT]
> 
> - Wenn auf dem Gerät die Releaseversion (GA) ausgeführt wird, wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md), um Hilfe bei diesem Update zu erhalten.
> - Dieses Verfahren muss nur einmal ausgeführt werden, um Update 1.2 anzuwenden. Über das Azure-Verwaltungsportal können Sie nachfolgende Updates installieren.

Wenn auf Ihrem Gerät eine Softwareversion vor Update 1 ausgeführt wird und ein Gateway für eine andere Netzwerkschnittstelle als DATA 0 eingerichtet wurde, haben Sie die folgenden Möglichkeiten zum Anwenden von Update 1.2:

- **Option 1**: Laden Sie das Update herunter, und wenden Sie es mithilfe des Cmdlets `Start-HcsHotfix` über die Windows PowerShell-Schnittstelle des Geräts an. Dies ist die empfohlene Methode. **Verwenden Sie diese Methode nicht zum Anwenden von Update 1.2, wenn auf Ihrem Gerät Update 1.0 oder Update 1.1 ausgeführt wird.** 

- **Option 2**: Entfernen Sie die Gatewaykonfiguration, und installieren Sie das Update direkt über das Azure-Portal.


Ausführliche Anweisungen für jede dieser Optionen werden in den folgenden Abschnitten bereitgestellt.

## Option 1: Verwenden von Windows PowerShell für StorSimple zum Anwenden von Update 1.2 als Hotfix

Sie sollten dieses Verfahren nur dann verwenden, wenn Sie Update 0.1, 0.2 oder 0.3 ausführen und die Gatewayüberprüfung beim Installieren der Updates über das Azure-Portal fehlgeschlagen ist. Wenn Sie die Releaseversion (GA) der Software ausführen, wenden Sie sich zum Aktualisieren Ihres Geräts an den [Microsoft Support](storsimple-contact-microsoft-support.md).

Stellen Sie Folgendes sicher, bevor Sie dieses Verfahren zum Anwenden des Updates verwenden:

- Beide Gerätecontroller sind online.

Führen Sie die folgenden Schritte aus, um Update 1.2 anzuwenden. **Die Installation des Updates nimmt etwa 2 Stunden in Anspruch (ca. 30 Minuten für die Software, 30 Minuten für die Treiber, 45 Minuten für die Datenträgerfirmware).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## Option 2: Verwenden des Azure-Portals zum Anwenden von Update 1.2 nach dem Entfernen der Gatewaykonfiguration

Dieses Verfahren gilt nur für StorSimple-Geräte mit einer Softwareversion vor Update 1 und einem Gateway, das auf einer anderen Netzwerkschnittstelle als DATA 0 eingerichtet ist. Sie müssen die Gatewayeinstellung deaktivieren, bevor Sie das Update anwenden.
 
Das Update kann einige Stunden dauern. Wenn sich Ihre Hosts in unterschiedlichen Subnetzen befinden, kann das Entfernen der Gateway-Konfiguration auf den iSCSI-Schnittstellen zu Ausfallzeiten führen. Es wird empfohlen, DATA 0 für iSCSI-Datenverkehr zu konfigurieren, um Ausfallzeiten zu reduzieren.
 
Führen Sie die folgenden Schritte aus, um die Netzwerkschnittstelle mit dem Gateway zu deaktivieren und anschließend das Update anzuwenden.
 
[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

## Problembehandlung bei Update-Fehlern

**Was geschieht, wenn über eine Meldung angezeigt wird, dass die Prüfungen vor dem Upgrade fehlgeschlagen sind?**

Wenn eine Vorabprüfung fehlschlägt, beachten Sie unbedingt die ausführliche Benachrichtigungsleiste unten auf der Seite. Dort erfahren Sie, welche Vorabprüfung fehlgeschlagen ist. Die folgende Abbildung zeigt ein Beispiel, in dem eine solche Benachrichtigung angezeigt wird. In diesem Fall sind die Integritätsprüfungen für Controller und für Hardware-Komponenten fehlgeschlagen. Im Abschnitt **Hardwarestatus** können Sie sehen, dass **Controller 0**- und **Controller 1**-Komponenten Ihre Aufmerksamkeit erfordern.
 
  ![Fehler bei der Vorabprüfung](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Sie müssen sicherstellen, dass beide Controller fehlerfrei und online sind. Sie müssen außerdem sicherstellen, dass alle Hardwarekomponenten im StorSimple-Gerät auf der Seite "Wartung" als fehlerfrei angezeigt werden. Sie können dann versuchen, Updates zu installieren. Wenn Sie Probleme mit Hardware-Komponenten nicht beheben können, müssen Sie sich an den Microsoft Support wenden, um die nächsten Schritte durchzuführen.

**Was geschieht, wenn Sie die Fehlermeldung "Updates konnten nicht installiert werden" erhalten und empfohlen wird, die Anleitung zur Update-Problembehandlung hinzuzuziehen, um die Fehlerursache zu bestimmen?**

Eine wahrscheinliche Ursache könnte sein, dass Sie nicht mit den Microsoft Update-Servern verbunden sind. In diesem Fall muss eine manuelle Überprüfung erfolgen. Wenn die Verbindung mit dem Updateserver unterbrochen wird, schlägt der Aktualisierungsauftrag fehl. Sie können das folgende Cmdlet in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausführen, um die Konnektivität zu prüfen:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Führen Sie das Cmdlet auf beiden Controllern aus.
 
Wenn Sie sichergestellt haben, dass die Verbindung vorhanden ist, und dieses Problem weiter besteht, wenden Sie sich an den Microsoft Support, um die nächsten Schritte durchzuführen.


## Nächste Schritte

Weitere Informationen finden Sie unter [Versionshinweise zu Update 1.2](storsimple-update1-release-notes.md)

<!---HONumber=Nov15_HO4-->