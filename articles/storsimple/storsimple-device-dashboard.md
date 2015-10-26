<properties 
   pageTitle="Verwenden des Dashboards des StorSimple Manager-Geräts | Microsoft Azure"
   description="Beschreibt das Dashboard des StorSimple Manager-Geräts und erläutert, wie das Dashboard verwendet wird, um Speichermetriken und verbundene Initiatoren anzuzeigen sowie nach der Seriennummer und dem IQN zu suchen."
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
   ms.date="09/15/2015"
   ms.author="alkohli" />

# Verwenden des StorSimple Manager-Gerätedashboards

## Übersicht

Das StorSimple Manager-Gerätedashboard bietet Ihnen einen Überblick über die Informationen für ein bestimmtes StorSimple-Gerät, wodurch es sich deutlich vom Dienstdashboard unterscheidet, das Ihnen Informationen zu allen Geräten bietet, die in Ihrer Microsoft Azure StorSimple-Lösung enthalten sind.

![Seite für Gerätedashboard](./media/storsimple-device-dashboard/HCS_DeviceDashboardPage.png)

Das Dashboard enthält die folgenden Informationen:

- **Diagrammfläche** – Sie sehen die zugehörigen Speichermetriken im Diagrammbereich oben auf dem Dashboard. In diesem Diagramm können Sie Metriken für den gesamten primären Speicher (die Menge der Daten, die von Hosts in das Gerät geschrieben wurden) sowie für den gesamten Cloudspeicher sehen, der von Ihrem Gerät in einem Zeitraum beansprucht wurde.

     In diesem Zusammenhang bezieht sich *primärer Speicher* auf die Gesamtmenge der Daten, die vom Host geschrieben wurden. Er kann sowohl lokal gespeicherte Daten als auch Daten umfassen, die in die Cloud ausgelagert wurden. *Cloudspeicher* ist demgegenüber ein Maß für die Gesamtmenge der Daten, die in der Cloud gespeichert sind. Dies umfasst ausgelagerte Daten und Sicherungen. Beachten Sie Folgendes: In der Cloud gespeicherte Daten sind dedupliziert und komprimiert, der Primärspeicher dagegen zeigt die Menge an Speicherplatz an, der verwendet wird, bevor die Daten dedupliziert und komprimiert werden. (Sie können diese beiden Zahlen vergleichen, um einen Eindruck von der Komprimierungsrate zu erhalten.) Sowohl für primären als auch für Cloudspeicher basieren die angezeigten Mengen auf der Nachverfolgungshäufigkeit, die Sie konfigurieren. Wenn Sie beispielsweise eine Häufigkeit von einer Woche wählen, werden im Diagramm Daten für jeden Tag der vorherigen Woche angezeigt.
 
	 Sie können das Diagramm wie folgt konfigurieren:

	 - Um die Menge des über einen Zeitraum beanspruchten Cloudspeichers anzuzeigen, aktivieren Sie die Option **VERWENDETER CLOUDSPEICHER**. Um die gesamte Speichermenge anzuzeigen, die vom Host geschrieben wurde, aktivieren Sie die Option **VERWENDETER PRIMÄRER SPEICHER**. In der Abbildung sind beide Optionen aktiviert. Daher werden im Diagramm sowohl die Menge für den Cloudspeicher als auch die Menge für den primären Speicher angezeigt. 
	 - Verwenden Sie das Dropdownmenü in der oberen rechten Ecke des Diagramms, um einen Zeitraum von einer Woche, einem Monat, drei Monaten oder einem Jahr anzugeben. Beachten Sie, dass das Diagramm auf oberster Ebene nur einmal täglich aktualisiert wird und daher die Gesamtmengen des Vortags wiedergibt.

     Weitere Informationen finden Sie unter [Überwachen Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-monitor-device.md).

- **Verwendungsübersicht** – Im Bereich **Verwendungsübersicht** können Sie die Menge des verwendeten primären Speichers, die Menge des bereitgestellten Speichers und die maximale Speicherkapazität für Ihr Gerät sehen. Durch Vergleichen dieser Verwendungszahlen mit der maximale Menge von Speicher, der verfügbar ist, können Sie auf einen Blick feststellen, ob Sie weiteren Speicher benötigen. Beachten Sie, dass diese Übersicht alle 15 Minuten aktualisiert wird und in ihr wegen der unterschiedlichen Aktualisierungshäufigkeit möglicherweise andere Zahlen angezeigt werden als im oberen Diagrammbereich, der täglich aktualisiert wird. Weitere Informationen finden Sie unter [Überwachen Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-monitor-device.md).


- **Warnungen** – Der Bereich **Warnungen** enthält eine Übersicht über die Warnungen für Ihr Gerät. Warnungen sind nach Schweregrad gruppiert, und für jeden Schweregrad wird die Anzahl von Warnungen bereitgestellt. Durch Klicken auf den Warnungsschweregrad wird eine angepasste Ansicht der Registerkarte „Warnungen“ angezeigt, sodass nur Warnungen dieses Schweregrads für das Gerät angezeigt werden.

- **Aufträge** – Im Bereich **Aufträge** wird das Ergebnis der letzten Auftragsaktivität angezeigt. Hierüber können Sie sich vergewissern, dass das System erwartungsgemäß arbeitet, oder feststellen, dass Sie Korrekturmaßnahmen ergreifen müssen. Um weitere Informationen über zuletzt abgeschlossene Aufträge anzuzeigen, klicken Sie auf **Erfolgreiche Aufträge in den letzten 24 Stunden**.

- Der Bereich **Auf einen Blick** auf der rechten Seite des Dashboards enthält hilfreiche Informationen wie Gerätemodell, Seriennummer, Status, Beschreibung und Anzahl von Volumes.

Außerdem können Sie über das Gerätedashboard Failover konfigurieren und verbundene Initiatoren anzeigen.

Die häufigen Aufgaben, die auf dieser Seite ausgeführt werden können, sind:

- Anzeigen verbundener Initiatoren

- Suchen nach der Seriennummer des Geräts

- Suchen nach dem Ziel-IQN des Geräts

## Anzeigen verbundener Initiatoren

Sie können die mit dem Gerät verbundenen iSCSI-Initiatoren sehen, indem Sie auf den Link **Verbundene Initiatoren anzeigen** klicken, der im **Auf einen Blick**-Bereich Ihres Gerätedashboards angezeigt wird. Diese Seite enthält eine tabellarische Liste der Initiatoren, die erfolgreich mit dem Gerät verbunden wurden. Für jeden Initiator wird Folgendes angezeigt:

- Der IQN (iSCSI Qualified Name) des verbundenen Initiators

- Der Name des Zugriffssteuerungsdatensatzes (Access Control Record, ACR), der diesen verbundenen Initiator zulässt

- Die IP-Adresse des verbundenen Initiators

- Die Netzwerkschnittstellen, an die der Initiator am Speichergerät angeschlossen ist. Diese können von DATA 0 bis DATA 5 reichen.

- Alle Volumes, auf die der verbundene Initiator gemäß der aktuellen ACR-Konfiguration zugreifen darf

Wenn Sie in dieser Liste unerwartete Initiatoren sehen oder die erwarteten Initiatoren nicht sehen, sollten Sie die ACR-Konfiguration überprüfen. Maximal 512 Initiatoren können mit einem Gerät verbunden werden.

## Suchen nach der Seriennummer des Geräts

Möglicherweise benötigen Sie die Seriennummer des Geräts, wenn Sie Microsoft Multipfad-E/A auf dem Gerät konfigurieren. Führen Sie die folgenden Schritte aus, um nach der Seriennummer des Geräts zu suchen.

#### So suchen Sie nach der Seriennummer des Geräts

1. Navigieren Sie zu **Geräte** > **Dashboard**.

2. Suchen Sie im rechten Abschnitt des Dashboards nach dem Bereich **Auf einen Blick**.

3. Scrollen Sie nach unten, und suchen Sie nach der Seriennummer.

## Suchen nach dem Ziel-IQN des Geräts

Möglicherweise benötigen Sie den Ziel-IQN des Geräts, wenn Sie das Challenge Handshake Authentication-Protokoll (CHAP) auf dem StorSimple-Gerät konfigurieren. Führen Sie die folgenden Schritte aus, um nach dem Ziel-IQN zu suchen.

### So suchen Sie nach dem Ziel-IQN

1. Navigieren Sie zu **Geräte** > **Dashboard**.

1. Suchen Sie im rechten Abschnitt des Dashboards nach dem Bereich **Auf einen Blick**.

1. Scrollen Sie nach unten, und suchen Sie nach dem Ziel-IQN.

## Nächste Schritte

- Erfahren Sie mehr über das [StorSimple Manager-Dienstdashboard](storsimple-service-dashboard.md).
- Erfahren Sie mehr zum [Verwenden Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->