<properties 
   pageTitle="Verwenden des StorSimple Snapshot Managers zum Verbinden und Verwalten von StorSimple-Geräten | Microsoft Azure"
   description="Beschreibt, wie das MMC-Snap-In StorSimple Snapshot Manager zum Herstellen von Verbindungen mit StorSimple-Geräten und zu deren Verwaltung verwendet wird."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Verwenden des StorSimple Snapshot Managers zum Verbinden und Verwalten von StorSimple-Geräten

## Übersicht

Mithilfe der Knoten im **Bereichsfenster** des StorSimple Snapshot Managers können Sie die importierten Daten von StorSimple-Geräten überprüfen und verbundene Speichergeräte aktualisieren. Darüber hinaus können Sie beim Klicken auf den Knoten **Geräte** eine Liste der verbundenen Geräte und die zugehörigen Statusinformationen im **Ergebnisbereich** anzeigen.

![Verbundene Geräte](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Abbildung 1: Verbundenes Gerät im StorSimple Snapshot Manager**

Je nach Ihrer Auswahl für die **Ansicht** werden im **Ergebnisbereich** die folgenden Informationen über jedes Gerät angezeigt. (Weitere Informationen zum Konfigurieren einer Ansicht finden Sie unter [Menü "Ansicht"](storsimple-use-snapshot-manager.md#view-menu).


| Ergebnisspalte |Beschreibung |
|:----------------|:--------------------| 
| Name | Der Name des Geräts wie im Microsoft Azure-Verwaltungsportal konfiguriert|
| Modell | Die Modellnummer des Geräts|
| Version | Die Version der auf dem Gerät installierten Software |
| Status | Gibt an, ob das Gerät verfügbar ist |
| Letzte Synchronisierung | Datum und Uhrzeit der letzten Synchronisierung des Geräts |
| Seriennummer | Die Seriennummer des Geräts |
 
Wenn Sie im **Bereichsfenster** mit der rechten Maustaste auf den Knoten **Geräte** klicken, können Sie aus den folgenden Aktionen auswählen:

- Hinzufügen oder Ersetzen eines Geräts 
- Verbinden eines Geräts und Überprüfen von Importvorgängen 
- Aktualisieren verbundener Geräte 

Wenn Sie auf den Knoten **Geräte** klicken, und dann mit der rechten Maustaste auf einen Gerätenamen im **Ergebnisbereich** klicken, können Sie aus den folgenden Aktionen auswählen:

- Authentifizieren eines Geräts 
- Anzeigen von Gerätedetails 
- Aktualisieren eines Geräts 
- Löschen einer Gerätekonfiguration 
- Ändern eines Gerätekennworts

>[AZURE.NOTE]Alle diese Aktionen stehen auch im Bereich **Aktionen** zur Verfügung.
 
In diesem Lernprogramm wird erläutert, wie Sie dem StorSimple Snapshot Manager Verbindungen mit Geräten herstellen und diese verwalten. Außerdem wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

- Hinzufügen oder Ersetzen eines Geräts 
- Verbinden eines Geräts und Überprüfen von Importvorgängen 
- Aktualisieren verbundener Geräte 
- Authentifizieren eines Geräts 
- Anzeigen von Gerätedetails 
- Aktualisieren eines einzelnen Geräts 
- Löschen einer Gerätekonfiguration 
- Ändern eines abgelaufenen Gerätekennworts
- Ersetzen eines ausgefallenen Geräts

>[AZURE.NOTE]Allgemeine Informationen zu Features der StorSimple Snapshot Manager-Benutzeroberfläche finden Sie unter [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md).


## Hinzufügen oder Ersetzen eines Geräts

Gehen Sie folgendermaßen vor, um ein StorSimple-Gerät hinzuzufügen oder zu ersetzen.

#### So fügen Sie ein Gerät hinzu oder ersetzen es

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf den Knoten **Geräte**, und klicken Sie dann auf **Gerät konfigurieren**. Das Dialogfeld **Gerät konfigurieren** wird angezeigt.

    ![Konfigurieren eines StorSimple-Geräts](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png)

3. Wählen Sie in der Dropdownliste **Gerät** die IP-Adresse des Geräts oder des virtuellen Geräts aus.

4. Geben Sie im Textfeld **Kennwort** das StorSimple Snapshot Manager-Kennwort ein, das Sie im Azure-Verwaltungsportal für das Gerät erstellt haben. Klicken Sie auf **OK**. Der StorSimple Snapshot Manager sucht nach dem angegebenen Gerät.

    - Wenn das Gerät verfügbar ist, fügt der StorSimple Snapshot Manager eine Verbindung hinzu. 

    - Wenn das Gerät aus irgendeinem Grund nicht verfügbar ist, gibt der StorSimple Snapshot Manager eine Fehlermeldung zurück. Klicken Sie auf **OK**, um die Fehlermeldung zu schließen, und klicken Sie dann auf **Abbrechen**, um das Dialogfeld **Gerät konfigurieren** zu schließen.

## Verbinden eines Geräts und Überprüfen von Importvorgängen

Gehen Sie folgendermaßen vor, um ein StorSimple-Gerät zu verbinden und zu überprüfen, ob alle vorhandenen Volumegruppen mit zugehörigen Sicherungen importiert werden.

#### So verbinden Sie ein Gerät und überprüfen Importvorgänge

1. Befolgen Sie zum Verbinden eines Geräts mit dem StorSimple Snapshot Manager die Anweisungen zum Hinzufügen oder Ersetzen eines Geräts. Beim Herstellen einer Verbindung mit einem Gerät reagiert der StorSimple Snapshot Manager wie folgt:

    - Wenn das Gerät aus irgendeinem Grund nicht verfügbar ist, gibt der StorSimple Snapshot Manager eine Fehlermeldung zurück. 

   - Wenn das Gerät verfügbar ist, fügt der StorSimple Snapshot Manager eine Verbindung hinzu. Wenn Sie das Gerät auswählen, wird es im **Ergebnisbereich** angezeigt. Das Statusfeld gibt dabei an, ob das Gerät **verfügbar** ist. Der StorSimple Snapshot Manager importiert alle Volumegruppen, die für das Gerät konfiguriert wurden, sofern den Volumegruppen Sicherungen zugeordnet sind. Sicherungsrichtlinien werden nicht importiert. Volumegruppen ohne zugehörige Sicherungen werden nicht importiert.

2. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

3. Klicken Sie mit der rechten Maustaste auf den obersten Knoten im **Bereichsfenster**, und klicken Sie dann auf **Toggle Imports Display**.

    ![Aktivieren von "Toggle Imports Display"](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png)

4. Das Dialogfeld **Toggle Imports Display** mit dem Status der importierten Volumegruppen und Sicherungen wird angezeigt. Klicken Sie auf **OK**.

Nachdem die Volumegruppen und Sicherungen erfolgreich importiert wurden, können Sie sie im StorSimple Snapshot Manager so verwalten, wie Sie auch Volumegruppen und Sicherungen verwalten, die Sie im StorSimple Snapshot Manager erstellt und konfiguriert haben.

## Aktualisieren verbundener Geräte

Gehen Sie folgendermaßen vor, um die verbundenen StorSimple-Geräte mit dem StorSimple Snapshot Manager zu synchronisieren.

####So aktualisieren Sie verbundene Geräte

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf **Geräte**, und klicken Sie dann auf **Geräte aktualisieren**. Damit werden die verbundenen Geräte mit StorSimple Snapshot Manager synchronisiert, sodass Sie die Volumegruppen und Sicherungen einschließlich aktueller kürzlichen Änderungen anzeigen können.

    ![Aktualisieren der StorSimple-Geräte](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
Mit der Aktion **Geräte aktualisieren** werden neue Volumegruppen und die zugehörigen Sicherungen von den verbundenen Geräten abgerufen. Im Gegensatz zur Aktion **Rescan volumes**, die im Knoten **Volumes** verfügbar ist, wird mit **Geräte aktualisieren** nicht die Sicherungsregistrierung wiederhergestellt.

## Authentifizieren eines Geräts

Gehen Sie folgendermaßen vor, um ein StorSimple-Gerät im StorSimple Snapshot Manager zu authentifizieren.

#### So authentifizieren Sie ein Gerät

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** auf **Geräte**.

3. Klicken Sie im **Ergebnisbereich** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Authentifizieren**.

4. Das Dialogfeld **Authentifizieren** wird angezeigt. Geben Sie das Kennwort für das Gerät ein, und klicken Sie dann auf **OK**.

    ![Authentifizieren (Dialogfeld)](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png)
 
## Anzeigen von Gerätedetails

Gehen Sie folgendermaßen vor, um Details zu einem StorSimple-Gerät anzuzeigen und bei Bedarf das Gerät erneut mit dem StorSimple Snapshot Manager zu synchronisieren.

#### So zeigen Sie Gerätedetails an und synchronisieren sie erneut

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** auf **Geräte**.

3. Klicken Sie im **Ergebnisbereich** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Details**.

4. Das Dialogfeld **Gerätedetails** wird angezeigt. In diesem Feld werden Name, Modell, Version, Seriennummer, Status, qualifizierter iSCSI-Name (IQN) des Ziels und Datum und Uhrzeit der letzten Synchronisierung angezeigt.

   - Klicken Sie auf **Resync**, um das Gerät zu synchronisieren.

   - Klicken Sie auf **OK** oder **Abbrechen**, um das Dialogfeld zu schließen.

    ![Gerätedetails](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png)
 
## Aktualisieren eines einzelnen Geräts

Gehen Sie folgendermaßen vor, um ein einzelnes mit StorSimple Snapshot Manager verbundenes StorSimple-Gerät erneut zu synchronisieren.

#### So aktualisieren Sie ein Gerät

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten. 

2. Klicken Sie im **Bereichsfenster** auf **Geräte**.

3. Klicken Sie im **Ergebnisbereich** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Gerät aktualisieren**. Damit wird das Gerät mit dem StorSimple Snapshot Manager synchronisiert.

## Löschen einer Gerätekonfiguration

Gehen Sie folgendermaßen vor, um die Konfiguration eines einzelnen mit StorSimple Snapshot Manager verbundenen StorSimple-Geräts zu löschen.

#### So löschen Sie eine Gerätekonfiguration

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten. 

2. Klicken Sie im **Bereichsfenster** auf **Geräte**.

3. Klicken Sie im **Ergebnisbereich** mit der rechten Maustaste auf den Namen des Geräts, und klicken Sie dann auf **Löschen**.

4. Die folgende Meldung wird angezeigt. Klicken Sie auf **Ja**, um die Konfiguration zu löschen, oder auf **Nein**, um den Löschvorgang abzubrechen.

    ![Löschen der Gerätekonfiguration](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## Ändern eines abgelaufenen Gerätekennworts

Sie müssen ein Kennwort eingeben, um ein StorSimple-Gerät im StorSimple Snapshot Manager zu authentifizieren. Sie konfigurieren dieses Kennwort, wenn Sie das Gerät mit der Windows PowerShell-Schnittstelle einrichten. Allerdings kann das Kennwort ablaufen. In diesem Fall können Sie das Kennwort im Microsoft Azure-Verwaltungsportal ändern. Da das Gerät im StorSimple Snapshot Manager konfiguriert wurde, bevor das Kennwort abgelaufen ist, müssen Sie das Gerät im StorSimple Snapshot Manager anschließend erneut authentifizieren.

#### So ändern Sie das abgelaufene Kennwort

1. Starten Sie im Verwaltungsportal den StorSimple Manager-Dienst.

2. Klicken Sie auf **Geräte** > **Konfigurieren** für das Gerät.

3. Scrollen Sie nach unten zum Abschnitt "StorSimple Snapshot Manager". Geben Sie ein Kennwort mit einer Länge von 14–15 Zeichen ein. Das Kennwort muss eine Kombination aus drei der vier folgenden Elemente sein: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen.

4. Geben Sie das Kennwort zur Bestätigung erneut ein.

5. Klicken Sie unten auf der Seite auf **Speichern**.

#### So authentifizieren Sie das Gerät erneut

1. Starten Sie StorSimple Snapshot Manager.

2. Klicken Sie im **Bereichsfenster** auf **Geräte**. Im **Ergebnisbereich** wird eine Liste der konfigurierten Geräte angezeigt.

3. Wählen Sie das Gerät aus, klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Authentifizieren**.

4. Geben Sie im Fenster **Authentifizieren** das neue Kennwort ein.

5. Wählen Sie das Gerät aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Gerät aktualisieren** aus. Damit wird das Gerät mit dem StorSimple Snapshot Manager synchronisiert.

## Ersetzen eines ausgefallenen Geräts

Wenn ein StorSimple-Gerät ausfällt und durch ein Austauschgerät ersetzt wird (Failover), gehen Sie folgendermaßen vor, um eine Verbindung mit dem neuen Gerät herzustellen und die zugehörigen Sicherungen anzuzeigen.

#### So stellen Sie nach einem Failover eine Verbindung mit einem neuen Gerät her

1. Konfigurieren Sie die iSCSI-Verbindung mit dem neuen Gerät neu. Anweisungen hierzu finden Sie unter "Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes" in [Bereitstellen lokaler StorSimple-Geräte](storsimple-deployment-walkthrough.md). 

>[AZURE.NOTE]Verfügt das neue StorSimple-Gerät über dieselbe IP-Adresse wie das alte, können Sie eventuell die alte Konfiguration verwenden.

2. Beenden Sie den Microsoft StorSimple-Verwaltungsdienst:

    1. Starten Sie den Server-Manager.

    2. Wählen Sie im Dashboard des Server-Managers im Menü **Extras** die Option **Dienste** aus.

    3. Wählen Sie im Fenster **Dienste** den **Microsoft StorSimple-Verwaltungsdienst** aus.

    4. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Den Dienst beenden**.

3. Entfernen Sie die Konfigurationsinformationen für das alte Gerät:

    1. Navigieren Sie im Datei-Explorer zu "C:\ProgramData\Microsoft\StorSimple\BACatalog". 

    2. Löschen Sie die Dateien im Ordner "BACatalog".

4. Starten Sie den Microsoft StorSimple-Verwaltungsdienst neu:

    1. Wählen Sie im Dashboard des Server-Managers im Menü **Extras** die Option **Dienste** aus. 

    2. Wählen Sie im Fenster **Dienste** den **Microsoft StorSimple-Verwaltungsdienst** aus.

    3. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Dienst neu starten**.

5. Starten Sie StorSimple Snapshot Manager.

6. Um das neue StorSimple-Gerät zu konfigurieren, führen Sie die Schritte unter [Schritt 2: Herstellen einer Verbindung mit einem StorSimple-Geräts](storsimple-snapshot-manager-deployment.md#step-2:-connect-a-storsimple-device) in [Bereitstellen des StorSimple Snapshot Managers](storsimple-snapshot-manager-deployment.md) aus.

7. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf den Knoten der obersten Ebene (StorSimple Snapshot Manager im Beispiel), und klicken Sie dann auf **Toggle Imports Display**.

8. Es wird eine Meldung angezeigt, wenn die importierten Volumegruppen und Sicherungen im StorSimple Snapshot Manager angezeigt werden. Klicken Sie auf **OK**.

## Nächste Schritte

[Verwenden des StorSimple Snapshot Managers zum Anzeigen und Verwalten von Volumes](storsimple-snapshot-manager-manage-volumes.md)

<!---HONumber=July15_HO4-->