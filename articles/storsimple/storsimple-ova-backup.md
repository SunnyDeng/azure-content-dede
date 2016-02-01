<properties 
   pageTitle="Lernprogramm zur StorSimple Virtual Array-Sicherung | Microsoft Azure"
   description="Beschreibt das Sichern von StorSimple Virtual Array-Freigaben und -Volumes."
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

# Sichern des StorSimple Virtual Array (Vorschau)

## Übersicht 

Dieses Lernprogramm bezieht sich nur auf Microsoft Azure StorSimple Virtual Array (auch als „lokales virtuelles StorSimple-Gerät“ oder „virtuelles StorSimple-Gerät“ bezeichnet) mit Version 1.1.1.0 (Vorschau).

Das StorSimple Virtual Array ist ein lokales virtuelles Cloudspeichergerät, das als Dateiserver oder iSCSI-Server konfiguriert werden kann. Es dient zum Erstellen von Sicherungen, zum Wiederherstellen aus Sicherungen und zum Durchführen eines Gerätefailovers, wenn eine Notfallwiederherstellung erforderlich ist. Wenn als Dateiserver konfiguriert ist, ermöglicht es auch die Wiederherstellung auf Elementebene. Dieses Lernprogramm beschreibt das Erstellen geplanter und manueller Sicherungen Ihres StorSimple Virtual Array mit dem klassischen Azure-Portal oder der StorSimple-Web-UI.

>[AZURE.IMPORTANT]
>
>- StorSimple Virtual Array befindet sich in der Vorschauphase und ist für die Evaluierung und Bereitstellungsplanung vorgesehen. Die Installation dieser Vorschauversion in einer Produktionsumgebung wird nicht unterstützt. 
>- Wenn Probleme mit StorSimple Virtual Array auftreten, posten Sie sie im [StorSimple-MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

## Sichern von Freigaben und Volumes

Sicherungen stellen Zeitpunktschutz zur Verfügung und verbessern die Wiederherstellbarkeit bei gleichzeitiger Minimierung der Wiederherstellungszeiten für Freigaben und Volumes. Für das Sichern einer Freigabe oder eines Volumes auf dem StorSimple-Gerät sind zwei Methoden möglich: **geplant** oder **manuell**. Diese Methoden werden in den folgenden Abschnitten erläutert.

> [AZURE.NOTE]In dieser Version werden geplante Sicherungen durch eine Standardrichtlinie erstellt, die täglich zu einer bestimmten Uhrzeit ausgeführt wird und alle Freigaben oder Volumes auf dem Gerät sichert. Derzeit können für geplante Sicherungen keine benutzerdefinierten Richtlinien erstellt werden.

## Ändern des Sicherungszeitplans

Ihr virtuelles StorSimple-Gerät verfügt über eine Standard-Sicherungsrichtlinie, die zu einer angegebenen Uhrzeit (22:30) beginnt und alle Freigaben oder Volumes auf dem Gerät einmal pro Tag sichert. Sie können die Zeit ändern, zu der die Sicherung gestartet wird, die Häufigkeit und Vermerkdauer (Anzahl der beizubehaltenden Sicherungen) können nicht geändert werden. Während dieser Sicherungen wird das gesamte virtuelle Gerät gesichert; aus diesem Grund wird empfohlen, diese Sicherungen außerhalb der Spitzenzeiten zu planen.

Führen Sie die folgenden Schritte im [klassischen Azure-Portal](https://manage.windowsazure.com/) aus, um die Standardstartzeit für die Sicherung zu ändern.

#### So ändern Sie die Startzeit für die Standard-Sicherungsrichtlinie

1. Navigieren Sie zur Registerkarte **Konfiguration** für das Gerät.

2. Legen Sie im Abschnitt **Sicherung** die Startzeit für die tägliche Sicherung fest.

3. Klicken Sie auf **Speichern**.

### Erstellen einer manuellen Sicherung

Neben geplanten Sicherungen können Sie jederzeit eine manuelle bedarfsgesteuerte) Sicherung vornehmen.

#### So erstellen Sie eine manuelle (bedarfsgesteuerte) Sicherung

1. Navigieren Sie zur Registerkarte **Freigaben** oder **Volumes**.

2. Klicken Sie unten auf der Seite auf **Alle sichern**. Sie werden aufgefordert, zu bestätigen, dass Sie die Sicherung jetzt ausführen möchten. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-backup/image3.png), um mit der Sicherung fortzufahren.

    ![Bestätigung der Sicherung](./media/storsimple-ova-backup/image4.png)

    Sie werden benachrichtigt, dass ein Sicherungsauftrag gestartet wird.

    ![Sicherung wird gestartet](./media/storsimple-ova-backup/image5.png)

    Sie werden benachrichtigt, dass der Auftrag erfolgreich erstellt wurde.

    ![Sicherungsauftrag erstellt](./media/storsimple-ova-backup/image7.png)

3. Klicken Sie auf das Symbol **Auftrag anzeigen**, um den Fortschritt des Auftrags nachzuverfolgen.

4. Nachdem der Sicherungsauftrag abgeschlossen wurde, navigieren Sie zur Registerkarte **Sicherungskatalog**. Die abgeschlossene Sicherung sollte angezeigt werden.

    ![Sicherung abgeschlossen](./media/storsimple-ova-backup/image8.png)

5. Legen Sie die Filterauswahl auf das entsprechende Gerät, die Sicherungsrichtlinie und den Zeitraum fest, und klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-backup/image3.png).

    Die Sicherung sollte in der Liste der Sicherungssätze enthalten sein, die im Katalog angezeigt wird.

## Anzeigen vorhandener Sicherungen

Führen Sie die folgenden Schritte im klassischen Azure-Portal aus, um die vorhandenen Sicherungen anzuzeigen.

#### So zeigen Sie vorhandene Sicherungen an

1. Klicken Sie auf der Seite des StorSimple-Manager-Diensts auf die Registerkarte **Sicherungskatalog**.

2. Wählen Sie wie folgt einen Sicherungssatz aus:

    1. Wählen Sie das Gerät aus.

    2. Wählen Sie in der Dropdownliste die Freigabe oder das Volume für die gewünschte Sicherung aus.

    3. Geben Sie den Zeitraum an.

    4. Klicken Sie auf das Häkchensymbol ![](./media/storsimple-ova-backup/image3.png), um diese Abfrage durchzuführen.

    Die der ausgewählten Freigabe bzw. dem ausgewählten Volume zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.

## Nächste Schritte

Erfahren Sie mehr zur [Verwaltung des StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0121_2016-->