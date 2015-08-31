<properties 
   pageTitle="Verwenden des StorSimple Snapshot Managers zum Erstellen und Verwalten von Sicherungsrichtlinien | Microsoft Azure"
   description="Beschreibt, wie das StorSimple Snapshot Manager-MMC-Snap-in zum Erstellen und Verwalten von Sicherungsrichtlinien verwendet wird, die geplante Sicherungen steuern."
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
   ms.date="08/17/2015"
   ms.author="v-sharos" />

# Verwenden des StorSimple Snapshot Managers zum Erstellen und Verwalten von Sicherungsrichtlinien

## Übersicht

Eine Sicherungsrichtlinie erstellt einen Zeitplan für die Sicherung von Volumedaten lokal oder in der Cloud. Wenn Sie eine Sicherungsrichtlinie erstellen, können Sie auch eine Aufbewahrungsrichtlinie angeben. (Sie können bis zu 64 Momentaufnahmen beibehalten.) Weitere Informationen zu Sicherungsrichtlinien finden Sie unter [Sicherungstypen und -richtlinien](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies).

In diesem Lernprogramm werden folgende Punkte erläutert:

- Erstellen einer Sicherungsrichtlinie 
- Bearbeiten einer Sicherungsrichtlinie 
- Löschen einer Sicherungsrichtlinie 

## Erstellen einer Sicherungsrichtlinie

Verwenden Sie das folgende Verfahren, um eine neue Sicherungsrichtlinie zu erstellen.

#### So erstellen Sie eine Sicherungsrichtlinie

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten.

2. Klicken Sie im Fenster **Bereich** mit der rechten Maustaste auf **Sicherungsrichtlinien**, und klicken Sie dann auf **Sicherungsrichtlinie erstellen**.

    ![Erstellen einer Sicherungsrichtlinie](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Das Dialogfeld **Richtlinie erstellen** wird angezeigt.

    ![Erstellen einer Richtlinie – Registerkarte "Allgemein"](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. Vervollständigen Sie auf der Registerkarte **Allgemein** die folgenden Informationen:

   1. Geben Sie im Textfeld **Name** einen Namen für die Richtlinie ein.

   2. Geben Sie im Textfeld **Volumegruppe** den Namen der Volumegruppe ein, die der Richtlinie zugeordnet ist.

   3. Wählen Sie entweder **lokale Momentaufnahme** oder **cloudbasierte Momentaufnahme**.

   4. Wählen Sie die Anzahl der Momentaufnahmen, die beibehalten werden sollen. Wenn Sie die Option **Alle** auswählen, werden 64 Momentaufnahmen (die maximale Anzahl) beibehalten.

4. Klicken Sie auf die Registerkarte **Zeitplan**.

    ![Erstellen einer Richtlinie – Registerkarte "Zeitplan"](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. Vervollständigen Sie auf der Registerkarte **Zeitplan** die folgenden Informationen:

   1. Klicken Sie auf das Kontrollkästchen **Aktivieren**, um die nächste Sicherung zu planen.

   2. Wählen Sie unter **Einstellungen** **Einmalig**, **Täglich**, **Wöchentlich** oder **Monatlich** aus.

   3. Klicken Sie im Textfeld **Start** auf das Kalendersymbol, und wählen Sie ein Startdatum.

   4. Unter **Erweiterte Einstellungen** können Sie optional Wiederholungszeitpläne und ein Enddatum festlegen.

   5. Klicken Sie auf **OK**.

Nachdem Sie eine Sicherungsrichtlinie erstellt haben, werden die folgenden Informationen im Bereich **Ergebnisse** angezeigt:

- **Name** – der Name der Sicherungsrichtlinie.

- **Typ** – lokale oder cloudbasierte Momentaufnahme.

- **Volumegruppe** – die der Richtlinie zugeordnete Volumegruppe.

- **Aufbewahrung** – die Anzahl der beibehaltenen Momentaufnahmen, maximal 64.

- **Erstellt** – das Datum, an dem diese Richtlinie erstellt wurde.

- **Aktiviert** – ob die Richtlinie derzeit angewendet wird: **True** gibt an, dass sie aktiviert ist, **False** gibt an, dass sie nicht aktiviert ist.

## Bearbeiten einer Sicherungsrichtlinie

Verwenden Sie das folgende Verfahren, um eine vorhandene Sicherungsrichtlinie bearbeiten.

#### So bearbeiten Sie eine Sicherungsrichtlinie

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten. 

2. Klicken Sie im Fenster **Bereich** auf den Knoten **Sicherungsrichtlinien**. Alle Sicherungsrichtlinien werden im Bereich **Ergebnisse** angezeigt.

3. Klicken Sie mit der rechten Maustaste auf die Richtlinie, die Sie bearbeiten möchten, und klicken Sie dann auf **Bearbeiten**.

    ![Bearbeiten einer Sicherungsrichtlinie](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)

4. Wenn das Fenster **Erstellen einer Richtlinie** angezeigt wird, geben Sie Ihre Änderungen ein, und klicken Sie dann auf **OK**.

## Löschen einer Sicherungsrichtlinie

Verwenden Sie das folgende Verfahren, um eine Sicherungsrichtlinie zu löschen.

#### So löschen Sie eine Sicherungsrichtlinie

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten. 

2. Klicken Sie im Fenster **Bereich** auf den Knoten **Sicherungsrichtlinien**. Alle Sicherungsrichtlinien werden im Bereich **Ergebnisse** angezeigt.

3. Klicken Sie mit der rechten Maustaste auf die Sicherungsrichtlinie, die Sie löschen möchten, und klicken Sie dann auf **Löschen**.
4. Wenn die Bestätigungsmeldung angezeigt wird, klicken Sie auf **Ja**.

    ![Bestätigung der gelöschten Sicherungsrichtlinie](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## Nächste Schritte

[Verwenden von StorSimple Snapshot Manager zum Anzeigen und Verwalten von Sicherungsaufträgen](storsimple-snapshot-manager-manage-backup-jobs.md).

<!---HONumber=August15_HO8-->