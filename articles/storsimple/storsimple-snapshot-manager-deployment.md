<properties 
   pageTitle="Bereitstellen des StorSimple Snapshot Managers | Microsoft Azure"
   description="Beschreibt, wie der StorSimple Snapshot Manager heruntergeladen und installiert wird."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Bereitstellung von StorSimple Snapshot Manager

## Übersicht

Der StorSimple Snapshot Manager ist ein Microsoft Management Console (MMC)-Snap-In, das den Datenschutz und die Sicherungsverwaltung in einer Microsoft Azure StorSimple-Umgebung vereinfacht. Mit dem StorSimple Snapshot Manager können Sie lokalenund cloudbasierte Microsoft Azure StorSimple-Speicher so verwalten, als handele es sich um ein vollständig integriertes Speichersystem, wodurch die Sicherungs- und Wiederherstellungsprozesse vereinfacht und die Kosten reduziert werden.

Dieses Lernprogramm beschreibt die Konfigurationsanforderungen sowie Verfahren zum Installieren, Entfernen und Aktualisieren des StorSimple Snapshot Managers.

## StorSimple Snapshot Manager-Installation

Der StorSimple Snapshot Manager kann auf Computern installiert werden, die unter dem Betriebssystem Windows Server® 2008 R2 SP1, Windows Server 2012, oder Windows Server 2012 R2 ausgeführt werden.

>[AZURE.NOTE]Auf Servern unter Windows 2008 R2 müssen Sie außerdem Windows Server 2008 SP1 und Windows Management Framework 3.0 installieren.

Stellen Sie vor der Installation oder Aktualisierung des StorSimple Snapshot Manager-Snap-Ins für die Microsoft Management Console (MMC) sicher, dass das Microsoft Azure StorSimple-Gerät und der -Hostserver ordnungsgemäß konfiguriert sind.

## Konfigurieren der Voraussetzungen

Die folgenden Schritte bieten einen allgemeinen Überblick über die Konfigurationsaufgaben, die Sie ausführen müssen, bevor Sie den StorSimple Snapshot Manager installieren. Vollständige Microsoft Azure StorSimple-Konfigurations- und Setupinformationen, einschließlich Systemanforderungen und detaillierten Anweisungen, finden Sie unter [Bereitstellen des lokalen StorSimple-Geräts](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Bevor Sie loslegen, gehen Sie die [Vorinstallationsliste](storsimple-deployment-walkthrough.md#pre-installation-checklist) und die [Bereitstellungsvoraussetzungen](storsimple-deployment-walkthrough.md#deployment-prerequisites) in [Bereitstellen lokaler StorSimple-Geräte](storsimple-deployment-walkthrough.md) durch. <br>
 
### Vor der Installation des StorSimple Snapshot Managers

1. Entpacken, Bereitstellen und Verbinden des Microsoft Azure StorSimple-Geräts, wie in [Installieren des StorSimple-8100-Geräts](storsimple-8100-hardware-installation.md) oder in [Installieren des StorSimple-8600-Geräts](storsimple-8600-hardware-installation.md) beschrieben.

2. Stellen Sie sicher, dass auf Ihrem Computer eines der folgenden Betriebssysteme ausgeführt wird:

    - Windows Server 2008 R2 (auf Servern unter Windows 2008 R2 müssen Sie außerdem Windows Server 2008 SP1 und Windows Management Framework 3.0 installieren)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    > [AZURE.NOTE]Für ein virtuelles StorSimple-Gerät muss der Host ein virtueller Microsoft Azure-Computer sein.

3. Stellen Sie sicher, dass Sie die Microsoft Azure StorSimple-Konfigurationsanforderungen erfüllen. Weitere Informationen finden Sie unter [Voraussetzungen für die Bereitstellung](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Verbinden Sie das Gerät mit dem Host, und führen Sie die Erstkonfiguration durch. Weitere Informationen finden Sie unter [Bereitstellungsschritte](storsimple-deployment-walkthrough.md#deployment-steps).

5. Erstellen Sie Volumes auf dem Gerät, weisen Sie sie dem Host zu, und stellen Sie sicher, dass der Host die Volumes bereitstellen und verwenden kann. Der StorSimple Snapshot Manager unterstützt die folgenden Typen von Volumes:

    - Basisvolumes
    - Einfache Volumes
    - Dynamische Volumes
    - Gespiegelte dynamische Volumes (RAID 1)
    - Freigegebene Clustervolumes
 
    Informationen zum Erstellen von Volumes auf dem StorSimple-Gerät oder dem virtuellen StorSimple-Gerät finden Sie unter "Schritt 6: Erstellen eines Volumes" in [Bereitstellen lokaler StorSimple-Geräte](storsimple-deployment-walkthrough.md).

## Installieren eines neuen StorSimple Snapshot Managers

Stellen Sie vor der Installation von StorSimple Snapshot Manager sicher, dass die auf dem StorSimple-Gerät oder dem virtuellen StorSimple-Gerät erstellten Volumes wie in [Konfigurieren der Voraussetzungen](#configure-prerequisites) beschrieben bereitgestellt, initialisiert und formatiert sind.

>[AZURE.IMPORTANT]
>
>- Für ein virtuelles StorSimple-Gerät muss der Host ein virtueller Microsoft Azure-Computer sein. 
>
>- Der Host muss unter Windows 2008 R2, Windows Server 2012 oder Windows Server 2012 R2 ausgeführt werden. Wenn Ihr Server unter Windows Server 2008 R2 ausgeführt wird, müssen Sie auch Windows Server 2008 SP1 und Windows Management Framework 3.0 installieren.
>
>- Sie müssen eine iSCSI-Verbindung vom Host zum StorSimple-Gerät konfigurieren, bevor Sie das Gerät mit dem StorSimple Snapshot Manager verbinden können.

Gehen Sie folgendermaßen vor, um eine Neuinstallation des StorSimple Snapshot Managers durchzuführen. Wenn Sie ein Upgrade installieren, wechseln Sie zu [Upgrade oder Neuinstallation des StorSimple Snapshot Managers](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Schritt 1: Installieren des StorSimple Snapshot Managers 
- Schritt 2: Verbinden eines Geräts mit dem StorSimple Snapshot Manager 
- Schritt 3: Überprüfen der Verbindung mit dem Gerät 

###Schritt 1: Installieren des StorSimple Snapshot Managers

Führen Sie die folgenden Schritte aus, um den StorSimple Snapshot Manager zu installieren.

#### So installieren Sie den StorSimple Snapshot Manager

1. Laden Sie die StorSimple Snapshot Manager-Software herunter (wechseln Sie zu [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) im Microsoft Download Center), und speichern Sie sie lokal auf dem Host.

2. Klicken Sie im Datei-Explorer mit der rechten Maustaste auf den komprimierten Ordner, und klicken Sie dann auf **Alle extrahieren**.

3. Geben Sie im Fenster **Komprimierte (gezippte) Ordner extrahieren** im Feld **Ziel auswählen und Dateien extrahieren** den Pfad ein, wo die Datei extrahiert werden soll, oder navigieren Sie dort hin.

    >[AZURE.IMPORTANT]Sie müssen auf dem Laufwerk C: StorSimple Snapshot Manager installieren.
 
4. Wählen Sie **Extrahierte Dateien nach Extrahierung anzeigen**, und klicken Sie dann auf **Extrahieren**.

    ![Dialogfeld „Extrahieren von Dateien“](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png)

4. Wenn die Extrahierung beendet ist, wird der Zielordner geöffnet. Doppelklicken Sie auf das Anwendungssymbol für das Setup, das im Zielordner angezeigt wird.

5. Wenn die Meldung **Setup erfolgreich** erscheint, klicken Sie auf **Schließen**. Das Symbol für den StorSimple Snapshot Manager sollte auf dem Desktop angezeigt werden.

    ![Desktopsymbol](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png)

### Schritt 2: Verbinden eines Geräts mit dem StorSimple Snapshot Manager

Führen Sie die folgenden Schritte aus, um den StorSimple Snapshot Manager mit einem StorSimple-Gerät zu verbinden.

#### So verbinden Sie den StorSimple Snapshot Manager mit einem Gerät

1. Klicken Sie auf das StorSimple Snapshot Manager-Symbol auf Ihrem Desktop. Das StorSimple Snapshot Manager-Fenster erscheint. Das Fenster enthält einen **Bereich**-, **Ergebnisse**-, und **Aktionen**-Bereich. 

    ![Benutzeroberfläche des StorSimple Snapshot Managers](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)

    - Das Fenster **Bereich** (der linke Bereich) enthält eine Liste an Knoten, die in einer Baumstruktur angeordnet sind. Sie können einige Knoten zum Auswählen einer Ansicht oder bestimmter Daten in Bezug auf diesen Knoten erweitern. Klicken Sie zum Erweitern oder Reduzieren eines Knotens auf das Pfeilsymbol. Klicken Sie mit der rechten Maustaste auf ein Element im Fenster **Bereich**, um eine Liste der verfügbaren Aktionen für das Element anzuzeigen. 

    - Der Bereich **Ergebnisse** (der mittleren Bereich) enthält ausführliche Statusinformationen über den Knoten, die Ansicht oder die Daten, die Sie im Fenster **Bereich** ausgewählt haben.

    - Im Bereich **Aktionen** werden die Vorgänge aufgezählt, die Sie am Knoten, der Ansicht oder den Daten ausführen können, die Sie im Fenster **Bereich** ausgewählt haben.

    Eine vollständige Beschreibung der StorSimple Snapshot Manager-Benutzeroberfläche finden Sie unter [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md).

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf den Knoten **Geräte**, und klicken Sie dann auf **Gerät konfigurieren**. Das Dialogfeld **Gerät konfigurieren** wird angezeigt.

    ![Konfigurieren eines Geräts](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png)

3. Wählen Sie im Listenfeld **Gerät** die IP-Adresse des Microsoft Azure StorSimple-Geräts oder des virtuellen Geräts aus. Geben Sie im Textfeld **Kennwort** das StorSimple Snapshot Manager-Kennwort ein, das Sie im Microsoft Azure-Verwaltungsportal für das Gerät erstellt haben. Klicken Sie auf **OK**.

4. Der StorSimple Snapshot Manager sucht nach dem von Ihnen angegebenen Gerät. Wenn das Gerät verfügbar ist, fügt der StorSimple Snapshot Manager eine Verbindung hinzu. Sie können [die Verbindung mit dem Gerät überprüfen](#to-verify-the-connection), um zu bestätigen, dass die Verbindung erfolgreich hinzugefügt wurde.

    Wenn das Gerät aus irgendeinem Grund nicht verfügbar ist, gibt der StorSimple Snapshot Manager eine Fehlermeldung zurück. Klicken Sie auf **OK**, um die Fehlermeldung zu schließen, und klicken Sie dann auf **Abbrechen**, um das Dialogfeld **Gerät konfigurieren** zu schließen.

5. Beim Herstellen einer Verbindung mit einem Gerät importiert der StorSimple Snapshot Manager jede Volumegruppe, die für das Gerät konfiguriert wurde, vorausgesetzt, dass die Volumegruppe über zugeordnete Sicherungen verfügt. Volumegruppen ohne zugehörige Sicherungen werden nicht importiert. Darüber hinaus werden Sicherungsrichtlinien, die für eine Volumegruppe erstellt wurden, nicht importiert. Um die importierten Gruppen anzuzeigen, klicken Sie mit der rechten Maustaste ganz oben auf den Knoten **Volumegruppen** im Fenster **Bereich**, und klicken Sie dann auf**Importierte Gruppen umschalten**.

### Schritt 3: Überprüfen der Verbindung mit dem Gerät

Gehen Sie folgendermaßen vor, um sicherzustellen, dass der StorSimple Snapshot Manager mit dem StorSimple-Gerät verbunden ist.

#### So überprüfen Sie die Verbindung

1. Klicken Sie im Fenster **Bereich** auf den Knoten **Geräte**.

    ![Gerätestatus des StorSimple Snapshot Managers](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png)

2. Überprüfen Sie den Bereich **Ergebnisse**:

   - Wenn ein grüner Indikator auf dem Gerätesymbol und **Verfügbar** in der **Status**spalte angezeigt wird, dann ist das Gerät verbunden. 

   - Wenn ein roter Indikator auf dem Gerätesymbol und „Nicht verfügbar“ in der **Status**spalte angezeigt wird, dann ist das Gerät nicht verbunden.

   - Wenn **Wird aktualisiert** in der **Status**spalte angezeigt wird, dann ruft der StorSimple Snapshot Manager Volumegruppen und zugehörige Sicherungen für ein verbundenes Gerät ab.

## Aktualisierung oder Neuinstallation des StorSimple Snapshot Managers

Sie sollten den StorSimple Snapshot Manager vollständig deinstallieren, bevor Sie die Software aktualisieren oder neu installieren.

Sichern Sie vor der Neuinstallation des StorSimple Snapshot Managers die vorhandene StorSimple Snapshot Manager-Datenbank auf dem Hostcomputer. Dies spart die Sicherungsrichtlinien und Konfigurationsinformationen, sodass Sie diese Daten problemlos aus einer Sicherung wiederherstellen können.

Gehen Sie folgendermaßen vor, wenn Sie den StorSimple Snapshot Manager aktualisieren oder erneut installieren:

- Schritt 1: Deinstallieren Sie StorSimple Snapshot Manager 
- Schritt 2: Sichern Sie die StorSimple Snapshot Manager-Datenbank 
- Schritt 3: Installieren Sie den StorSimple Snapshot Managern neu und stellen Sie die Datenbank wieder her 

### Schritt 1: Deinstallieren Sie StorSimple Snapshot Manager

Führen Sie die folgenden Schritte aus, um den StorSimple Snapshot Manager zu deinstallieren.

#### So deinstallieren Sie den StorSimple Snapshot Manager

1. Öffnen Sie auf dem Hostcomputer die **Systemsteuerung**, klicken Sie auf **Programme**, und dann auf **Programme und Funktionen**.

2. Klicken Sie im linken Bereich auf **Deinstallieren oder Ändern eines Programms**.

3. Klicken Sie mit der rechten Maustaste auf **StorSimple Snapshot Manager**, und dann auf **Deinstallieren**.

4. Dadurch wird das Setup-Programm für den StorSimple Snapshot Manager gestartet. Klicken Sie auf **Setup ändern**, und dann auf **Deinstallieren**.

    >[AZURE.NOTE]Wenn im Hintergrund MMC-Prozesse laufen, z. B. der StorSimple Snapshot Manager oder die Datenträgerverwaltung, dann schlägt die Deinstallation fehl, und Sie erhalten eine Nachricht, dass vor der Deinstallation des Programms alle Instanzen der MMC geschlossen werden müssen. Wählen Sie **Anwendungen automatisch schließen und erneut starten, nachdem das Setup abgeschlossen wurde**, und klicken Sie dann auf **OK**.
 
5. Nach Abschluss der Deinstallation erscheint die Meldung **Setup wurde erfolgreich abgeschlossen**. Klicken Sie auf **Schließen**.

### Schritt 2: Sichern Sie die StorSimple Snapshot Manager-Datenbank

Führen Sie die folgenden Schritte aus, um eine Kopie der StorSimple Snapshot Manager-Datenbank zu erstellen und zu speichern.

#### So sichern Sie die Datenbank

1. Beenden Sie den Microsoft StorSimple-Verwaltungsdienst:

   1. Starten Sie den Server-Manager.

   2. Wählen Sie im Dashboard des Server-Managers im Menü **Extras** die Option **Dienste** aus.

   3. Wählen Sie auf der Seite **Dienste** **Microsoft StorSimple-Verwaltungsdienst** aus.

   4. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Den Dienst beenden**.

        ![Stop the StorSimple Manager service](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Navigieren Sie zu C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

    >[AZURE.NOTE]ProgramData ist ein versteckter Ordner.

3. Suchen Sie die XML-Katalogdatei, kopieren Sie die Datei, und speichern Sie die Kopie an einem sicheren Ort oder in der Cloud.

    ![StorSimple-Sicherungskatalogdatei](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Starten Sie den Microsoft StorSimple-Verwaltungsdienst neu:

    1. Wählen Sie im Dashboard des Server-Managers im Menü **Extras** die Option **Dienste** aus.

    2. Wählen Sie auf der Seite **Dienste** den **Microsoft StorSimple-Verwaltungsdienst** aus.

    3. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Den Dienst neustarten**.

### Schritt 3: Installieren Sie den StorSimple Snapshot Managern neu und stellen Sie die Datenbank wieder her

Um den StorSimple Snapshot Manager erneut zu installieren, führen Sie die Schritte in [Installieren eines neuen StorSimple Snapshot Managers](#install-a-new-storsimple-snapshot-manager) aus. Verwenden Sie dann das folgende Verfahren zum Wiederherstellen der StorSimple Snapshot Manager-Datenbank.

#### So stellen Sie eine Datenbank wieder her

1. Beenden Sie den Microsoft StorSimple-Verwaltungsdienst:

    1. Starten Sie den Server-Manager.

    2. Wählen Sie im Dashboard des Server-Managers im Menü **Extras** die Option **Dienste** aus.

    3. Wählen Sie auf der Seite **Dienste** **Microsoft StorSimple-Verwaltungsdienst** aus.

    4. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Den Dienst beenden**.

2. Navigieren Sie zu C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

     >[AZURE.NOTE]ProgramData ist ein versteckter Ordner.

3. Löschen Sie die XML-Katalogdatei, und ersetzen Sie sie durch die Version, die Sie zuvor gespeichert haben.

4. Starten Sie den Microsoft StorSimple-Verwaltungsdienst neu:

    1. Wählen Sie im Dashboard des Server-Managers im Menü **Extras** die Option **Dienste** aus.

    2. Wählen Sie auf der Seite **Dienste** **Microsoft StorSimple-Verwaltungsdienst** aus.

    3. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Den Dienst neustarten**.

## Nächste Schritte

Weitere Informationen zum StorSimple Snapshot Manager finden Sie unter [Was ist der StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

Weitere Informationen zur StorSimple Snapshot Manager-Benutzeroberfläche finden Sie unter [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md)

<!---HONumber=July15_HO3-->