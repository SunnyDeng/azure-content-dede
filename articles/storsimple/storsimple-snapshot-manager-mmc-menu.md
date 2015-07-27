<properties 
   pageTitle="Verwenden der MMC-Menüaktionen im StorSimple Snapshot Manager | Microsoft Azure"
   description="Beschreibt die Verwendung der Menüaktionen der Microsoft Management Console (MMC) im StorSimple Snapshot Manager."
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

# Verwenden der MMC-Menüaktionen im StorSimple Snapshot Manager

## Übersicht

In StorSimple Snapshot Manager werden die folgenden Aktionen in allen Aktionsmenüs und allen Varianten des Bereichs **Aktionen** aufgeführt.

- Ansicht
- New Window from Here 
- Aktualisieren 
- Liste exportieren 
- Hilfe 

Diese Aktionen sind Teil der Microsoft Management Console (MMC) und nicht spezifisch für StorSimple Snapshot Manager.

In diesem Lernprogramm werden diese Aktionen und ihre Verwendung in StorSimple Snapshot Manager beschrieben.

## Ansicht

Sie können mit der Option **Ansicht** die Anzeige im **Ergebnisbereich** und im Konsolenfenster ändern.

#### So ändern Sie die Ansicht im Ergebnisbereich

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, und klicken Sie mit der rechten Maustaste auf ein Element im **Ergebnisbereich**, und klicken Sie dann auf die Option **Ansicht**.

3. Um im **Ergebnisbereich** Spalten hinzuzufügen oder zu entfernen, klicken Sie auf **Spalten hinzufügen/entfernen**. Das Dialogfeld **Spalten hinzufügen/entfernen** wird angezeigt.

    ![Hinzufügen oder Entfernen von Spalten aus dem Ergebnisbereich](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png)

4. Füllen Sie das Formular wie folgt aus:

    - Wählen Sie Elemente aus der Liste **Verfügbare Spalten** aus, und klicken Sie auf **Hinzufügen**, um sie der Liste **Angezeigte Spalten** hinzuzufügen. 

    - Klicken Sie auf Elemente in der Liste **Angezeigte Spalten**, und klicken Sie auf **Entfernen**, um sie aus der Liste zu entfernen.

    - Wählen Sie ein Element aus der Liste **Angezeigte Spalten** aus, und klicken Sie auf **Nach oben** oder **Nach unten**, um das Element in der Liste nach oben bzw. unten zu verschieben.

    - Klicken Sie auf **Standardeinstellungen wiederherstellen**, um die Standardkonfiguration des **Ergebnisbereichs** wiederherzustellen.

5. Wenn Sie mit der Auswahl fertig sind, klicken Sie auf **OK**.

#### So ändern Sie die Ansicht des Konsolenfensters

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf einen beliebigen Knoten, klicken Sie auf **Ansicht**, und klicken Sie dann auf **Anpassen**. Das Dialogfeld **Anpassen** wird angezeigt.

    ![Anpassen des Konsolenfensters](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png)

3. Aktivieren oder deaktivieren Sie die Kontrollkästchen, um die jeweiligen Elemente im Konsolenfenster ein- oder auszublenden. Wenn Sie mit der Auswahl fertig sind, klicken Sie auf **OK**.

## New Window from Here

Mit der Option **New Window from Here** können Sie ein neues Fenster öffnen.

#### So öffnen Sie ein neues Konsolenfenster

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf einen beliebigen Knoten, und klicken Sie dann auf **New Window from Here**.

    Ein neues Fenster wird angezeigt, in dem nur der ausgewählte Gültigkeitsbereich berücksichtigt wird. Wenn Sie beispielsweise mit der rechten Maustaste auf den Knoten **Sicherungsrichtlinien** klicken, werden im neuen Fenster nur der Knoten **Sicherungsrichtlinien** im **Bereichsfenster** und eine Liste der definierten Sicherungsrichtlinien im **Ergebnisbereich** angezeigt. Siehe folgendes Beispiel.

    ![New Window from Here](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png)
 
## Aktualisieren

Mit der Aktion **Aktualisieren** können Sie das Konsolenfenster aktualisieren.

#### So aktualisieren Sie das Konsolenfenster

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, klicken Sie mit der rechten Maustaste auf ein Element im **Ergebnisbereich**, und klicken Sie dann auf **Aktualisieren**.

## Liste exportieren

Mit der Aktion **Liste exportieren** können Sie eine Liste in einer Datei mit kommagetrennten Werten (CSV) speichern. Beispielsweise können Sie die Liste der Sicherungsrichtlinien oder den Sicherungskatalog exportieren. Sie können die CSV-Datei dann zur Analyse in eine Tabellenkalkulationsanwendung importieren.

#### So speichern Sie eine Liste in einer Datei mit kommagetrennten Werten (CSV)

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten. 

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, klicken Sie mit der rechten Maustaste auf ein Element im **Ergebnisbereich**, und klicken Sie dann auf **Liste exportieren**.

3. Das Dialogfeld **Liste exportieren** wird angezeigt. Füllen Sie das Formular wie folgt aus:

    1. Geben Sie im Feld **Dateiname** einen Namen für die CSV-Datei ein, oder klicken Sie auf den Pfeil, um einen Namen aus der Dropdownliste auszuwählen.

    2. Klicken Sie im Feld **Dateityp** auf den Pfeil, und wählen Sie einen Dateityp aus der Dropdownliste aus.

    3. Um nur ausgewählte Elemente zu speichern, markieren Sie die Zeilen, und klicken Sie dann auf das Kontrollkästchen **Nur markierte Zeilen speichern**. Um alle exportierten Listen zu speichern, deaktivieren Sie das Kontrollkästchen **Nur markierte Zeilen speichern**.

    4. Klicken Sie auf **Speichern**.

    ![Exportieren von Listen als CSV-Datei](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png)
 
## Hilfe

Über das Menü **Hilfe** können Sie die verfügbare Onlinehilfe für den StorSimple Snapshot Manager und die MMC anzeigen.

#### So zeigen Sie die verfügbare Onlinehilfe an

1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.

2. Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, klicken Sie mit der rechten Maustaste auf ein Element im **Ergebnisbereich**, und klicken Sie dann auf **Hilfe**.

## Nächste Schritte

[Erfahren Sie mehr über die Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md)

<!---HONumber=July15_HO3-->