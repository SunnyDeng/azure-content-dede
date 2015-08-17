<properties 
   pageTitle="Verwenden Sie StorSimple Snapshot Manager zum Verwalten des Sicherungskatalogs | Microsoft Azure"
   description="Beschreibt, wie das StorSimple Snapshot Manager-MMC-Snap-In zum Erstellen und Verwalten von Sicherungsrichtlinien verwendet wird, die geplante Sicherungen steuern."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Verwenden Sie StorSimple Snapshot Manager zum Verwalten des Sicherungkatalogs

## Übersicht

Die primäre Funktion des StorSimple Snapshot Managers ist das Erstellen anwendungskonsistenter Sicherungkopien von Azure StorSimple-Volumes in Form von Momentaufnahmen. Momentaufnahmen werden dann in einer XML-Datei aufgeführt, die *Sicherungkatalog* genannt wird. Der Sicherungkatalog gliedert Momentaufnahmen nach Volumegruppe, und anschließend nach lokaler oder cloudbasierter Momentaufnahme.

Sie können den Sicherungskatalog durch Erweitern des Knotens **Sicherungskatalog** im Fenster **Bereich**, und dem anschließendem Erweitern der Volumegruppe anzeigen.

- Wenn Sie auf den Namen der Volumegruppe klicken, wird im Bereich **Ergebnisse** die Anzahl der lokalen und cloudbasierten Momentaufnahmen angezeigt, die für diese Volumegruppe verfügbar sind. 

- Wenn Sie auf **Lokale Momentaufnahme** oder **Cloud-Momentaufnahme** klicken, werden im Bereich **Ergebnisse** die folgenden Informationen über jede Sicherungsmomentaufnahme angezeigt (je nach Einstellungen in der **Ansicht**):

    - **Name** – die Zeit, zu der die Momentaufnahme erstellt wurde. 

    - **Typ** – ob es sich um eine lokale oder eine cloudbasierte Momentaufnahme handelt.

    - **Besitzer** – der Besitzer des Inhalts.

    - **Verfügbar** – ob die Momentaufnahme aktuell verfügbar ist. „True“ gibt an, dass die Momentaufnahme verfügbar ist und wiederhergestellt werden kann. „False“ gibt an, dass die Momentaufnahme nicht mehr verfügbar ist.

    - **Importiert** – ob die Sicherung importiert wurde. **True** gibt an, dass die Sicherung zum Zeitpunkt der Konfiguration des Geräts in StorSimple Snapshot Manager aus dem StorSimple Manager-Dienst importiert wurde. **False** gibt an, dass er nicht importiert, sondern vom StorSimple Snapshot Manager erstellt wurde. (Sie können problemlos eine importierte Volumegruppe identifizieren, da ein Suffix hinzugefügt wird, das das Gerät identifiziert, aus dem die Volume-Gruppe importiert wurde.)

    ![Sicherungskatalog](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Wenn Sie die **Lokale Momentaufnahme** oder die **Cloud-Momentaufnahme** erweitern, und dann auf den Namen einer einzelnen Momentaufnahme klicken, werden im Bereich **Ergebnisse** die folgenden ausgewählten Informationen zur Momentaufnahme angezeigt:

    - **Name** – das Volume, das durch den Laufwerkbuchstaben identifiziert wird. 

    - **Lokaler Namen** – der lokale Name des Laufwerks (falls verfügbar).

    - **Gerät** – der Name des Geräts, auf dem sich das Volume befindet.

    - **Verfügbar** – ob die Momentaufnahme aktuell verfügbar ist. **True** gibt an, dass die Momentaufnahme verfügbar ist und wiederhergestellt werden kann. **False** gibt an, dass die Momentaufnahme nicht mehr verfügbar ist.

In diesem Lernprogramm wird beschrieben, wie Sie mit dem Knoten **Sicherungskatalog** die folgenden Aufgaben ausführen können:

- Wiederherstellen eines Volumes 
- Klonen eines Volumes oder einer Volumegruppe 
- Löschen einer Sicherung 
- Wiederherstellen einer Datei
- Wiederherstellen der Storsimple Snapshot Manager-Datenbank

## Wiederherstellen eines Volumes

Verwenden Sie das folgende Verfahren, um ein Volume aus einer Sicherung wiederherzustellen.

#### Voraussetzungen

Wenn Sie dies nicht bereits getan haben, erstellen Sie ein Volume und die Volumegruppe, und löschen Sie anschließend das Volume. Standardmäßig sichert der StorSimple Snapshot Manager ein Volume, bevor es gelöscht werden kann. Diese Vorsichtsmaßnahme kann Datenverluste vermeiden, wenn das Volume unbeabsichtigt gelöscht wird oder wenn die Daten aus einem beliebigen Grund wiederhergestellt werden müssen.

Der StorSimple Snapshot Manager zeigt die folgende Meldung an, während aus Vorsichtsmaßnahmen die Sicherung erstellt wird.

![Nachricht zur automatischen Momentaufnahme](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png)

>[AZURE.IMPORTANT]Sie können kein Volume löschen, das Teil einer Volumegruppe ist. Die Löschoption ist nicht verfügbar. <br>

#### So stellen Sie ein Volume wieder her

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten. 

2. Erweitern Sie im Fenster **Bereich** den Knoten **Sicherungskatalog**, erweitern Sie eine Volumegruppe, und klicken Sie dann auf**Lokale Momentaufnahmen** oder **Cloud-Momentaufnahmen**. Eine Liste von Sicherungsmomentaufnahmen wird im Bereich **Ergebnisse** angezeigt.

3. Suchen Sie die Sicherung, die Sie wiederherstellen möchten, klicken Sie mit der rechten Maustaste darauf, und anschließend auf **Wiederherstellen**.

    ![Wiederherstellen des Sicherungskatalogs](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png)

4. Überprüfen Sie auf der Bestätigungsseite die Details, geben Sie **Bestätigen** ein, und klicken Sie dann auf **OK**. Der StorSimple Snapshot Manager verwendet die Sicherung, um das Volume wiederherzustellen.

    ![Bestätigungsmeldung zur Wiederherstellung](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png)

5. Sie können die Wiederherstellungsaktion während der Ausführung überwachen. Erweitern Sie im Fenster **Bereich** den **Aufträge**-Knoten, und klicken Sie dann auf **Ausgeführt**. Die Auftragsdetails werden im Bereich **Ergebnisse** angezeigt. Wenn der Wiederherstellungsvorgang abgeschlossen ist, werden die Auftragsdetails in die Liste **Letzte 24 Stunden** übertragen.

## Klonen eines Volumes oder einer Volumegruppe

Verwenden Sie das folgende Verfahren, um ein Duplikat (Klon) eines Volumes oder einer Volumegruppe zu erstellen.

#### So klonen Sie ein Volume oder eine Volumegruppe

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten.

2. Erweitern Sie im Fenster **Bereich** den Knoten **Sicherungskatalog**, erweitern Sie eine Volumegruppe, und klicken Sie dann auf **Cloud-Momentaufnahmen**. Eine Liste der Sicherungen wird im Bereich **Ergebnisse** angezeigt.

3. Suchen Sie das Volume oder die Volumegruppe, die Sie klonen möchten, und klicken Sie mit der rechten Maustaste auf den Namen des Volumens oder der Volumengruppe, und anschließend auf **Klonen**. Das Dialogfeld **Cloud-Momentaufnahme klonen** wird angezeigt.

    ![Klonen einer Cloud-Momentaufnahme](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png)

4. Vervollständigen Sie das Dialogfeld **Cloud-Momentaufnahme klonen** wie folgt:

    1. Geben Sie im Textfeld **Name** einen Namen für das geklonte Volume ein. Dieser Name wird im Knoten **Volumes** angezeigt. 

    2. (Optional) Wählen Sie **Laufwerk** aus, und anschließend einen Laufwerkbuchstaben aus der Dropdown-Liste.

    3. (Optional) Wählen Sie **Ordner (NTFS)** aus, und geben Sie einen Ordnerpfad ein oder klicken Sie auf „Durchsuchen“, und wählen dann einen Speicherort für den Ordner.

    4. Klicken Sie auf **Erstellen**.

5. Wenn der Klonvorgang abgeschlossen ist, müssen Sie das geklonte Volume initialisieren. Starten Sie den Server-Manager, und starten Sie dann die Datenträgerverwaltung. Ausführliche Anweisungen finden Sie unter [Laden von Volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Nach der Initialisierung wird das Volume unter dem Knoten **Volumes** im Fenster **Bereich** aufgeführt. Wenn Sie das aufgeführte Volume nicht erscheint, aktualisieren Sie die Liste der Volumes (mit der rechten Maustaste auf den Knoten **Volumes** klicken, und dann auf **Aktualisieren**).

## Löschen einer Sicherung

Verwenden Sie für das Löschen einer Momentaufnahme aus dem Sicherungskatalog das folgende Verfahren.

>[AZURE.NOTE]Das Löschen einer Momentaufnahme löscht die gesicherten Daten, die der Momentaufnahme zugeordnet sind. Allerdings kann das Bereinigen von Daten in der Cloud einige Zeit dauern.<br>
 
#### So löschen Sie eine Sicherung

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten.

2. Erweitern Sie im Fenster **Bereich** den Knoten **Sicherungskatalog**, erweitern Sie eine Volumegruppe, und klicken Sie dann auf**Lokale Momentaufnahmen** oder **Cloud-Momentaufnahmen**. Eine Liste von Momentaufnahmen wird im Bereich **Ergebnisse** angezeigt.

3. Klicken Sie mit der rechten Maustaste auf die Momentaufnahme, die Sie löschen möchten, und klicken Sie dann auf **Löschen**.

4. Wenn die Bestätigungsmeldung angezeigt wird, klicken Sie auf **OK**.

## Wiederherstellen einer Datei

Wenn eine Datei versehentlich von einem Volume gelöscht wird, können Sie die Datei wiederherstellen, indem Sie eine Momentaufnahme von vor dem Löschvorgang abrufen. Mit dieser Momentaufnahme erstellen Sie einen Klon des Volumes, und kopieren dann die Datei aus dem geklonten Volume in das ursprüngliche Volume.

#### Voraussetzungen

Bevor Sie loslegen, stellen Sie sicher, dass Sie über eine aktuelle Sicherung der Volumegruppe verfügen. Löschen Sie dann eine Datei auf einem der Volumes in der Volumegruppe. Gehen Sie schließlich folgendermaßen vor, um die gelöschte Datei aus der Sicherung wiederherzustellen.

#### So stellen Sie eine gelöschte Datei wieder her

1. Klicken Sie auf das Desktopsymbol des StorSimple Snapshot Managers. Das Fenster der StorSimple Snapshot Manager-Konsole wird angezeigt. 

2. Erweitern Sie im Fenster **Bereich** den Knoten **Sicherungskatalog**, und navigieren Sie zu einer Momentaufnahme, die die gelöschte Datei enthält. In der Regel sollten Sie eine Momentaufnahme auswählen, die unmittelbar vor dem Löschvorgang erstellt wurde.

3. Suchen Sie das Volume, das Sie klonen möchten, klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Klonen**. Das Dialogfeld **Cloud-Momentaufnahme klonen** wird angezeigt.

    ![Klonen einer Cloud-Momentaufnahme](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png)

4. Vervollständigen Sie das Dialogfeld **Cloud-Momentaufnahme klonen** wie folgt:

   1. Geben Sie im Textfeld **Name** einen Namen für das geklonte Volume ein. Dieser Name wird im Knoten **Volumes** angezeigt.

   2. (Optional) Wählen Sie **Laufwerk** aus, und anschließend einen Laufwerkbuchstaben aus der Dropdown-Liste.

   3. (Optional) Wählen Sie **Ordner (NTFS)** aus, und geben Sie einen Ordnerpfad ein oder klicken Sie auf **Durchsuchen**, und wählen dann einen Speicherort für den Ordner.

   4. Klicken Sie auf **Erstellen**.

5. Wenn der Klonvorgang abgeschlossen ist, müssen Sie das geklonte Volume initialisieren. Starten Sie den Server-Manager, und starten Sie dann die Datenträgerverwaltung. Ausführliche Anweisungen finden Sie unter [Laden von Volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Nach der Initialisierung wird das Volume unter dem Knoten **Volumes** im Fenster **Bereich** aufgeführt.

    Wenn Sie das aufgeführte Volume nicht erscheint, aktualisieren Sie die Liste der Volumes (mit der rechten Maustaste auf den Knoten **Volumes** klicken, und dann auf **Aktualisieren**).

6. Öffnen Sie den NTFS-Ordner, der das geklonte Volume enthält, erweitern Sie den Knoten **Volumes**, und öffnen Sie dann das geklonte Volume. Suchen Sie die Datei, die Sie wiederherstellen möchten, und kopieren Sie sie auf das primäre Volume.

7. Nach der Wiederherstellung der Datei können Sie den NTFS-Ordner löschen, der das geklonte Volume enthält.

## Wiederherstellen der StorSimple Snapshot Manager-Datenbank

Sie sollten die StorSimple Snapshot Manager-Datenbank auf dem Hostcomputer regelmäßig sichern. Wenn ein Notfall eintritt oder der Hostcomputer aus irgendeinem Grund fehlschlägt, können Sie die Datenbank aus der Sicherung wiederherstellen. Das Erstellen der Datenbanksicherung ist ein manueller Prozess.

#### So sichern Sie eine Datenbank und stellen sie wieder her

1. Beenden Sie den Microsoft StorSimple-Verwaltungsdienst:

    1. Starten Sie den Server-Manager.

    2. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** **Dienste**.

    3. Wählen Sie im Fenster **Dienste** **Microsoft StorSimple-Verwaltungsdienst**.

    4. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Beenden des Dienstes**.

2. Navigieren Sie auf dem Hostcomputer zu C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

    >[AZURE.NOTE]ProgramData ist ein versteckter Ordner.
 
3. Suchen Sie die XML-Katalogdatei, kopieren Sie die Datei, und speichern Sie die Kopie an einem sicheren Ort oder in der Cloud. Wenn der Host ausfällt, können Sie diese Sicherungsdatei verwenden, um Sicherungsrichtlinien wiederzuherstellen, die Sie im StorSimple Snapshot Manager erstellt haben.

    ![Azure StorSimple-Sicherungskatalogdatei](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Starten Sie den Microsoft StorSimple-Verwaltungsdienst neu:

    1. Wählen Sie im Dashboard des Server-Managers im Menü **Tools** **Dienste**.
    
    2. Wählen Sie im Fenster **Dienste** **Microsoft StorSimple-Verwaltungsdienst**.

    3. Klicken Sie im rechten Bereich unter **Microsoft StorSimple-Verwaltungsdienst** auf **Neustart des Dienstes**.

5. Navigieren Sie auf dem Hostcomputer zu C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

6. Löschen Sie die XML-Katalogdatei, und ersetzen Sie sie durch die Sicherungskopie, die Sie erstellt haben.

7. Klicken Sie auf das Desktopsymbol „StorSimple Snapshot Manager“, um den StorSimple Snapshot Manager zu starten.

## Nächste Schritte

Informieren Sie sich unter den verfügbaren [StorSimple Snapshot Manager-Aufgaben und -Workflows](storsimple-what-is-snapshot-manager.md#storsimple-snapshot-manager-tasks-and-workflows).

<!---HONumber=August15_HO6-->