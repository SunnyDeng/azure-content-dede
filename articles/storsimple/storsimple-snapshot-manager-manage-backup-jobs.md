<properties 
   pageTitle="Verwenden von StorSimple Snapshot Manager zum Anzeigen und Verwalten von Sicherungsaufträgen | Microsoft Azure"
   description="Beschreibt, wie das MMC-Snap-in von StorSimple Snapshot Manager zum Anzeigen und Verwalten von geplanten, aktuell ausgeführten und abgeschlossenen Sicherungsaufträge verwendet wird."
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


# Verwenden von StorSimple Snapshot Manager zum Anzeigen und Verwalten von Sicherungsaufträgen

## Übersicht

Der **Aufträge**-Knoten im Fenster **Bereich** zeigt Sicherungsaufträge in den Phasen **Geplant**, **Letzte 24 Stunden** und **Wird ausgeführt**, die Sie interaktiv oder durch eine konfigurierte Richtlinie initiiert haben.

In diesem Lernprogramm wird erklärt, wie Sie den **Aufträge**-Knoten verwenden können, um Informationen über geplante, aktuelle und aktuell ausgeführte Sicherungsaufträge anzuzeigen. (Die Liste der Aufträge und die entsprechenden Informationen werden im Bereich **Ergebnisse** angezeigt.) Darüber hinaus können Sie mit der rechten Maustaste auf einen aufgelisteten Auftrag klicken, sodass ein Kontextmenü mit verfügbaren Aktionen erscheint.

## Anzeigen geplanter Aufträge

Verwenden Sie das folgende Verfahren, um geplante Sicherungsaufträge anzuzeigen.

#### So zeigen Sie geplante Aufträge an:

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten. 

2. Erweitern Sie im Fenster **Bereich** den **Aufträge**-Knoten, und klicken Sie auf **Geplant**. Die folgenden Informationen werden im **Ergebnisbereich** angezeigt:

    - **Name** – der Name der geplanten Momentaufnahme

    - **Nächste Ausführung** – das Datum und die Uhrzeit der nächsten geplanten Momentaufnahme

    - **Letzte Ausführung** – das Datum und die Uhrzeit der letzten geplanten Momentaufnahme

    >[AZURE.NOTE]Bei einmaligen Momentaufnahmen sind die Informationen **Nächste Ausführung** und **Letzte Ausführung** identisch.
 
    ![Geplante Sicherungsaufträge](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png)
 
3. Um zusätzliche Aktionen für einen bestimmten Auftrag durchzuführen, klicken Sie mit der rechten Maustaste auf den Namen des Auftrags im Bereich **Ergebnisse**, und wählen Sie aus den Menüoptionen.

## Anzeigen kürzlich ausgeführter Aufträge

Verwenden Sie das folgende Verfahren zum Anzeigen von Sicherungs- und Wiederherstellungsaufträgen, die in den letzten 24 Stunden abgeschlossen wurden.

#### So zeigen Sie kürzlich ausgeführte Aufträge an:

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten.

2. Erweitern Sie im Fenster **Bereich** den **Aufträge**-Knoten, und klicken Sie auf **Letzte 24 Stunden**. Der Bereich **Ergebnisse** zeigt Sicherungsaufträge für die letzten 24 Stunden an (bis maximal 64 Aufträge). Die folgenden Informationen erscheinen im Bereich **Ergebnisse**, abhängig von den unter **Ansicht** angegebenen Optionen:

    - **Name** – der Name der geplanten Momentaufnahme.
 
    - **Gestartet** – das Datum und die Startzeit der Momentaufnahme.

    - **Beendet** – das Datum und die Uhrzeit, zu der die Momentaufnahme abgeschlossen oder beendet wurde.

    - **Verstrichen** – die Zeitspanne zwischen den Zeiten **Gestartet** und **Beendet**.

    - **Status** – der Status des zuletzt abgeschlossenen Auftrags. **Erfolgreich** gibt an, dass die Sicherung erfolgreich erstellt wurde. **Fehlgeschlagen** gibt an, dass der Auftrag nicht erfolgreich ausgeführt wurde.

    - **Informationen** – die Ursache für den Fehler.

    - **Verarbeitete Bytes (MB)** – die Menge der Daten aus der Volumegruppe, die (in MB) verarbeitet wurde.

    ![Aufträge, die in den letzten 24 Stunden ausgeführt wurden](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png)

3. Um zusätzliche Aktionen für einen bestimmten Auftrag durchzuführen, klicken Sie mit der rechten Maustaste auf den Namen des Auftrags im Bereich **Ergebnisse**, und wählen Sie aus den Menüoptionen.

    ![Löschen eines Auftrags](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)
     
## Anzeigen aktuell ausgeführter Aufträge

Verwenden Sie das folgende Verfahren zum Anzeigen von Aufträgen, die derzeit ausgeführt werden.

#### So zeigen Sie aktuell ausgeführte Aufträge an:

1. Klicken Sie auf das Desktopsymbol, um StorSimple Snapshot Manager zu starten.

2. Erweitern Sie im Fenster **Bereich** den **Aufträge**-Knoten, und klicken Sie auf **Ausgeführt**. Abhängig von den unter **Ansicht** angegebenen Optionen erscheinen die folgenden Informationen im Bereich **Ergebnisse**:

    - **Name** – der Name der geplanten Momentaufnahme.

    - **Gestartet** – das Datum und die Startzeit der Momentaufnahme.

    - **Prüfpunkt** – die aktuelle Sicherungsaktion.

    - **Status** – der Prozentsatz der Fertigstellung.
    
    - **Verstrichene** – die Zeitspanne, die seit dem Start der Sicherung vergangen ist.

    - **Durchschnittlicher Durchsatz (MB)** – die durchschnittliche Menge übermittelter Daten, ausgedrückt in Megabyte (MB).

    - **Verarbeitete Bytes (MB)** – die Menge der Daten aus der Volumegruppe, die (in MB) verarbeitet wurde.

    - **Geschriebene Bytes (MB)** – die Menge der Daten, die in die Sicherung (in MB) geschrieben wurde.

    ![Aufträge, die derzeit ausgeführt werden](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Um zusätzliche Aktionen für einen bestimmten Auftrag durchzuführen, klicken Sie mit der rechten Maustaste auf den Namen des Auftrags im Bereich **Ergebnisse**, und wählen Sie aus den Menüoptionen.

## Nächste Schritte

[Verwenden von StorSimple Snapshot Manager zum Verwalten des Sicherungskatalogs](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

<!---HONumber=August15_HO8-->