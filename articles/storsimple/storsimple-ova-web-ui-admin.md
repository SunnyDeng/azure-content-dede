<properties 
   pageTitle="Verwaltung des StorSimple Virtual Array über die Web-UI | Microsoft Azure"
   description="Beschreibt die grundlegenden Verwaltungsaufgaben über die Web-UI des StorSimple Virtual Array."
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
   ms.date="01/22/2016"
   ms.author="alkohli" />

# Verwaltung des StorSimple Virtual Array (Vorschau) mithilfe der Web-UI

![Setup-Prozessablauf](./media/storsimple-ova-web-ui-admin/manage4.png)

## Übersicht

Die Lernprogramme in diesem Artikel beziehen sich auf Microsoft Azure StorSimple Virtual Array (auch als „lokales virtuelles StorSimple-Gerät“ bezeichnet) mit Version 1.1.1.0 (Vorschau). Dieser Artikel beschreibt einige der komplexen Workflows und Verwaltungsaufgaben, die auf dem StorSimple Virtual Array durchgeführt werden können. Zur Verwaltung des StorSimple Virtual Array können zwei Benutzeroberflächen verwendet werden: die Benutzeroberfläche des StorSimple Manager-Dienstes (als „Portal-UI“ bezeichnet) und die lokale Web-UI für das Gerät. Der Schwerpunkt dieses Artikels liegt auf den Aufgaben, die mithilfe der Web-UI ausgeführt werden können.

>[AZURE.IMPORTANT] 
>
>- StorSimple Virtual Array befindet sich in der Vorschauphase und ist für die Evaluierung und Bereitstellungsplanung vorgesehen. Die Installation dieser Vorschauversion in einer Produktionsumgebung wird nicht unterstützt. 
>- Wenn Probleme mit StorSimple Virtual Array auftreten, posten Sie sie im [StorSimple-MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

Dieser Artikel enthält folgende Lernprogramme:

- Abrufen des Verschlüsselungsschlüssels für Dienstdaten
- Beheben von Setup-Fehlern für die Web-UI
- Generieren eines Protokollpakets
- Herunterfahren oder Neustarten des Geräts

## Abrufen des Verschlüsselungsschlüssels für Dienstdaten

Wenn Sie das erste Gerät beim StorSimple Manager-Dienst registrieren, wird ein Verschlüsselungsschlüssel für Dienstdaten generiert. Dieser Schlüssel ist dann zusammen mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte beim StorSimple Manager-Dienst erforderlich.

Wenn Sie Ihren Verschlüsselungsschlüssel für Dienstdaten verlegt haben und ihn abrufen müssen, führen Sie die folgenden Schritte auf der lokalen Web-UI des bei Ihrem Dienst registrierten Geräts aus.

#### So rufen Sie den Verschlüsselungsschlüssel für Dienstdaten ab

1. Stellen Sie eine Verbindung mit der lokalen Web-UI. Wechseln Sie zu **Konfiguration** > **Cloudeinstellungen**.

    ![Verschlüsselungsschlüssel 1 für Dienstdaten abrufen](./media/storsimple-ova-web-ui-admin/image27.png)

2. Klicken Sie unten auf der Seite auf **Dienstdaten-Verschlüsselungsschlüssel abrufen**. Ein Schlüssel wird angezeigt. Kopieren und speichern Sie diesen Schlüssel.

    ![Verschlüsselungsschlüssel 2 für Dienstdaten abrufen](./media/storsimple-ova-web-ui-admin/image28.png)


## Beheben von Setup-Fehlern für die Web-UI

In einigen Fällen können bei der Konfiguration des Geräts über die lokale Web-UI Fehler auftreten. Zum Diagnostizieren und Beheben solcher Fehler können Sie die Diagnosetests ausführen.

#### So führen Sie die Diagnosetests aus

1. Wechseln Sie auf der lokalen Web-UI zu **Problembehandlung** > **Diagnosetests**.

    ![Diagnose 1 ausführen](./media/storsimple-ova-web-ui-admin/image29.png)

2. Klicken Sie unten auf der Seite auf **Diagnosetests ausführen**. Dadurch werden Tests initiiert, um mögliche Probleme mit Ihren Netzwerk-, Geräte-, Webproxy-, Zeit- oder Cloudeinstellungen zu diagnostizieren. Sie werden benachrichtigt, dass auf dem Gerät Tests ausgeführt werden.

3. Nach Abschluss der Tests werden die Ergebnisse angezeigt. Das folgende Beispiel zeigt die Ergebnisse von Diagnosetests. Beachten Sie, dass die Webproxyeinstellungen auf diesem Gerät nicht konfiguriert wurden und daher der Webproxytest nicht ausgeführt wurde. Alle anderen Tests für Netzwerkeinstellungen, DNS-Server und Uhrzeiteinstellungen wurden erfolgreich ausgeführt.

    ![Diagnose 2 ausführen](./media/storsimple-ova-web-ui-admin/image30.png)

## Generieren eines Protokollpakets

Ein Protokollpaket enthält alle relevanten Protokolle, die das Microsoft Support-Team bei der Behandlung von Geräteproblemen unterstützen können. In dieser Version kann ein Protokollpaket über die lokale Web-UI generiert werden.

#### So generieren Sie das Protokollpaket

1. Wechseln Sie auf der lokalen Web-UI zu **Problembehandlung** > **Systemprotokolle**.

    ![Protokollpaket 1 generieren](./media/storsimple-ova-web-ui-admin/image31.png)

2. Klicken Sie unten auf der Seite auf **Protokollpaket erstellen**. Ein Paket mit dem Systemprotokollen wird erstellt. Dies dauert einige Minuten.

    ![Protokollpaket 2 generieren](./media/storsimple-ova-web-ui-admin/image32.png)

    Sie werden benachrichtigt, nachdem das Paket erfolgreich erstellt wurde. Die Seite wird aktualisiert und gibt dann die Uhrzeit und das Datum der Paketerstellung an.

    ![Protokollpaket 3 generieren](./media/storsimple-ova-web-ui-admin/image33.png)

3. Klicken Sie auf **Protokollpaket herunterladen**. Ein ZIP-Paket wird auf Ihr System heruntergeladen.

    ![Protokollpaket 4 generieren](./media/storsimple-ova-web-ui-admin/image34.png)

4. Entpacken Sie das heruntergeladene Protokollpaket, um die Systemprotokolldateien anzuzeigen.

## Herunterfahren und Neustarten des Geräts

Sie können Ihr virtuelles Gerät über die lokale Web-UI herunterfahren oder neu starten.

#### So fahren Sie das virtuelle Gerät herunter

1. Wechseln Sie auf der lokalen Web-UI zu **Wartung** > **Energieeinstellungen**.

2. Klicken Sie unten auf der Seite auf **Herunterfahren**.

    ![Herunterfahren des Geräts 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Eine Warnung wird angezeigt, die besagt, dass durch ein Herunterfahren des Geräts alle ausgeführten IOs unterbrochen werden und ein Ausfall verursacht wird. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Warnung vor Herunterfahren des Geräts](./media/storsimple-ova-web-ui-admin/image37.png)

    Sie werden benachrichtigt, dass das Herunterfahren initiiert wurde.

    ![Herunterfahren des Geräts gestartet](./media/storsimple-ova-web-ui-admin/image38.png)

    Das Gerät wird jetzt heruntergefahren. Wenn Sie Ihr Gerät starten möchten, müssen Sie dies über den Hyper-V-Manager erledigen.

#### So starten Sie das virtuelle Gerät neu

1. Wechseln Sie auf der lokalen Web-UI zu **Wartung** > **Energieeinstellungen**.

2. Klicken Sie unten auf der Seite auf **Neu starten**.

    ![Geräteneustart](./media/storsimple-ova-web-ui-admin/image36.png)

3. Eine Warnung wird angezeigt, die besagt, dass durch einen Neustart des Geräts alle ausgeführten IOs unterbrochen werden und ein Ausfall verursacht wird. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Warnung vor Neustart](./media/storsimple-ova-web-ui-admin/image37.png)

    Sie werden benachrichtigt, dass der Neustart initiiert wurde.

    ![Neustart initiiert](./media/storsimple-ova-web-ui-admin/image39.png)

    Während des Neustarts wird die Verbindung mit der Benutzeroberfläche getrennt. Sie können den Neustart durch Aktualisieren der Benutzeroberfläche in regelmäßigen Abständen überwachen. Überwachen Sie den Neustartstatus des Geräts alternativ über den Hyper-V-Manager.

## Nächste Schritte

Erfahren Sie, wie Sie den [StorSimple Manager-Dienst zum Verwalten Ihres Geräts verwenden](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0128_2016-->