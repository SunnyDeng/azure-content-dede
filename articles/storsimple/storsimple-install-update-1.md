<properties 
   pageTitle="Installieren von Update 1 auf dem StorSimple-Gerät"
   description="Erfahren Sie, wie Sie Update 1 der StorSimple 8000 Serie auf Ihrem Gerät installieren."
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
   ms.date="05/27/2015"
   ms.author="v-sharos" />

# Installieren von Update 1 auf dem StorSimple-Gerät

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie Update 1 auf einem StorSimple-Gerät installieren, auf dem eine Version vor Update 1 ausgeführt wird. Auf dem Gerät wird möglicherweise die allgemein verfügbare GA-Version oder Update 0.1-, Update 0.2- bzw. Update 0.3-Software ausgeführt. In diesem Lernprogramm erfahren Sie außerdem, was zu tun ist, wenn ein Gateway für eine andere Netzwerkschnittstelle als DATA 0 auf dem StorSimple-Gerät konfiguriert ist.

Wenn auf Ihrem Gerät eine Version vor Update 1.0 ausgeführt wird, werden bei dieser Installation Prüfungen auf dem Gerät durchgeführt. Diese Prüfungen ermitteln den Gerätezustand im Hinblick auf Hardwarestatus und Netzwerkkonnektivität.

Sie werden aufgefordert, eine manuelle Vorprüfung durchzuführen, um Folgendes sicherzustellen:

- Die stationären Controller-IP-Adressen sind routbar und können eine Verbindung mit dem Internet herstellen. Diese IP-Adressen werden verwendet, um Ihr StorSimple-Gerät mit Updates zu versorgen. Sie können dies prüfen, indem Sie das folgende Cmdlet auf jedem Controller ausführen:

    `Test-Connection -Source <fixed IP of your device controller> <Destination IP> `
 
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

## Installieren von Update 1 auf einem Gerät mit einem Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 

Dieses Verfahren gilt für StorSimple-Geräte mit einer Software-Version vor Update 1.0 und einem Gateway, das auf eine andere Netzwerkschnittstelle als DATA 0 eingestellt ist.
 
Wenn Ihr Gerät kein Gateway aufweist, das auf eine andere Netzwerkschnittstelle als DATA 0 eingestellt ist, können Sie das Gerät direkt über das Verwaltungsportal aktualisieren. Siehe [Verwenden des Verwaltungsportals zum Installieren von Update 1](#use-the-management-portal-to-install-update-1).
 
> [AZURE.NOTE]Dieses Verfahren muss nur einmal durchgeführt werden, um Update 1.0 anzuwenden. Über das Azure-Verwaltungsportal können Sie nachfolgende Updates anwenden.
 
Wenn auf Ihrem Gerät Software vor Update 1.0 ausgeführt wird und ein Gateway für eine andere Netzwerkschnittstelle als DATA 0 eingestellt ist, haben Sie die folgenden beiden Möglichkeiten zum Anwenden von Update 1.0:

- **Option 1**: Laden Sie das Update herunter, und wenden Sie es mithilfe des Cmdlets [Start HcsHotfix](https://technet.microsoft.com/library/dn688134.aspx) in der Windows PowerShell-Schnittstelle des Geräts an. Dies ist die empfohlene Methode.

- **Option 2**: Installieren Sie das Update direkt über das Verwaltungsportal.
 
Ausführliche Anweisungen für jede dieser Optionen werden in den folgenden Abschnitten bereitgestellt.

### Option 1: Verwenden von Windows PowerShell für StorSimple zum Anwenden von Update 1

Stellen Sie Folgendes sicher, bevor Sie dieses Verfahren zum Anwenden des Updates verwenden:

- Beide Gerätecontroller sind online.

- DATA 2 und DATA 3 sind deaktiviert. Sie müssen dies nur sicherstellen, wenn auf den Geräten die GA-Version ausgeführt wird. Bei Geräten, auf denen Update 0.2 und 0.3 ausgeführt wird, ist diese Deaktivierung nicht erforderlich. Nachdem das Update abgeschlossen ist, können Sie diese Netzwerkschnittstellen erneut aktivieren.
 
Führen Sie die folgenden Schritte aus, um Update 1.0 anzuwenden: Das Update kann einige Stunden dauern.

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]

### Option 2: Verwenden des Azure-Verwaltungsportals zum Anwenden von Update 1

Das Update kann einige Stunden dauern. Wenn sich Ihre Hosts in unterschiedlichen Subnetzen befinden, kann das Entfernen der Gateway-Konfiguration auf den iSCSI-Schnittstellen zu Ausfallzeiten führen. Es wird empfohlen, DATA 0 für iSCSI-Datenverkehr zu konfigurieren, um Ausfallzeiten zu reduzieren.
 
Führen Sie die folgenden Schritte aus, um die Gatewayeinstellung zu löschen, und wenden Sie dann das Update an.
 
[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

## Problembehandlung bei Update-Fehlern

**Was geschieht, wenn über eine Meldung angezeigt wird, dass die Prüfungen vor dem Upgrade fehlgeschlagen sind?**

Wenn eine Vorabprüfung fehlschlägt, beachten Sie unbedingt die ausführliche Benachrichtigungsleiste unten auf der Seite. Dort erfahren Sie, welche Vorabprüfung fehlgeschlagen ist. Die folgende Abbildung zeigt ein Beispiel, in dem eine solche Benachrichtigung angezeigt wird. In diesem Fall sind die Integritätsprüfungen für Controller und für Hardware-Komponenten fehlgeschlagen. Unter dem Abschnitt **Hardwarestatus** können Sie sehen, dass Controller 0 -und Controller 1-Komponenten Aufmerksamkeit erfordern.
 
  ![Fehler bei der Vorabprüfung](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Sie müssen sicherstellen, dass beide Controller fehlerfrei und online sind. Sie müssen außerdem sicherstellen, dass alle Hardwarekomponenten im StorSimple-Gerät auf der Seite "Wartung" als fehlerfrei angezeigt werden. Sie können dann versuchen, Updates zu installieren. Wenn Sie Probleme mit Hardware-Komponenten nicht beheben können, müssen Sie sich an den Microsoft-Support wenden, um die nächsten Schritte durchzuführen.

**Was geschieht, wenn Sie die Fehlermeldung "Updates konnten nicht installiert werden"erhalten und empfohlen wird, die Anleitung zur Update-Problembehandlung hinzuzuziehen, um die Fehlerursache zu bestimmen?**

Eine wahrscheinliche Ursache könnte sein, dass Sie nicht mit den Microsoft Update-Servern verbunden sind. In diesem Fall muss eine manuelle Überprüfung erfolgen. Wenn die Verbindung mit dem Updateserver unterbrochen wird, schlägt der Aktualisierungsauftrag fehl. Sie können das folgende Cmdlet in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausführen, um die Konnektivität zu prüfen:

 `Test-Connection -Source <Fixed IP of your device controller> <Destination IP>`

Führen Sie das Cmdlet auf beiden Controllern aus.
 
Wenn Sie sichergestellt haben, dass die Verbindung vorhanden ist, und dieses Problem weiter besteht, wenden Sie sich an den Microsoft Support, um die nächsten Schritte durchzuführen.

## Nächste Schritte

Erfahren Sie mehr über [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=58--> 