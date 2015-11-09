<properties 
   pageTitle="Problembehandlung für Azure Data Lake Analytics-Aufträge mithilfe des Azure-Vorschauportals | Azure" 
   description="Erfahren Sie, wie die Problembehandlung von Data Lake Analytics-Aufträgen im Azure-Vorschauportal erfolgt." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/27/2015"
   ms.author="jgao"/>

# Problembehandlung für Azure Data Lake Analytics-Aufträge mithilfe des Azure-Vorschauportals

Erfahren Sie, wie die Problembehandlung von Data Lake Analytics-Aufträgen im Azure-Vorschauportal erfolgt.

In diesem Tutorial simulieren Sie das Problem einer fehlenden Quelldatei, das Sie anschließend im Azure-Vorschauportal beheben.

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Grundlegende Kenntnisse des Data Lake Analytics-Auftragsprozesses**. Siehe [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-use-portal.md).
- **Ein Data Lake Analytics-Konto.** Siehe [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-use-portal.md#create-adl-analytics-account).
- **Kopieren der Beispieldaten in das Data Lake Store-Standardkonto**. Siehe [Vorbereiten von Quelldaten](data-lake-analytics-get-started-use-portal.md.md#prepare-source-data).

##Übermitteln eines Data Lake Analytics-Auftrags

Nun erstellen Sie einen U-SQL-Auftrag mit einem ungültigen Quelldateinamen.

**So übermitteln Sie den Auftrag**

1. Klicken Sie links oben im Azure-Vorschauportal auf **Microsoft Azure**.
2. Klicken Sie auf die Kachel mit Ihrem Data Lake Analytics-Kontonamen. Der Name wurde hier angeheftet, als das Konto erstellt wurde. Falls das Konto hier nicht angeheftet ist, siehe [Öffnen eines Data Lake Analytics-Kontos im Portal](data-lake-analytics-manage-use-portal.md#access-adla-account).
3. Klicken Sie im oberen Menü auf **Neuer Auftrag**.
4. Geben Sie den Auftragsnamen und das folgende U-SQL-Skript ein:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    Die im Skript definierte Quelldatei heißt **/Samples/Data/SearchLog.tsv1**, sollte aber **/Samples/Data/SearchLog.tsv** heißen.
     
5. Klicken Sie oben auf **Auftrag senden**. Ein neuer Bereich mit Auftragsdetails wird geöffnet. Auf der Titelleiste wird der Auftragsstatus angezeigt. Bis zum Abschluss des Vorgangs dauert es einige Minuten. Klicken Sie zum Abrufen des aktuellen Status auf **Aktualisieren**.
6. Warten Sie, bis sich der Auftragsstatus in **Fehler** ändert. Wenn der Auftrag als **Erfolgreich** angezeigt wird, liegt das daran, dass Sie den Ordner „/Samples“ nicht entfernt haben. Siehe die Informationen im Abschnitt **Voraussetzung** weiter oben in diesem Tutorial.

Sie werden sich vielleicht fragen, warum das Ganze bei einem so kleinen Auftrag so lange dauert. Bedenken Sie, dass Data Lake Analytics auf die Big Data-Verarbeitung ausgelegt ist. Seine Leistung wird deutlich, sobald eine große Menge von Daten mithilfe seines verteilten Systems verarbeitet wird.

Angenommen, Sie haben den Auftrag übermittelt und das Portal geschlossen. Im nächsten Abschnitt erfahren Sie, wie Sie das Problem mit dem Auftrag beheben.


## Beheben des Problems mit dem Auftrag

Im letzten Abschnitt haben Sie ohne Erfolg einen Auftrag übermittelt.

**So zeigen Sie alle Aufträge an**

1. Klicken Sie links oben im Azure-Portal auf **Microsoft Azure**.
2. Klicken Sie auf die Kachel mit Ihrem Data Lake Analytics-Kontonamen. Die Zusammenfassung des Auftrags wird auf der Kachel **Auftragsverwaltung** gezeigt.

    ![Azure Data Lake Analytics – Auftragsverwaltung](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    Auf der Kachel „Auftragsverwaltung“ wird der Auftragsstatus gezeigt. Sie sehen, dass ein Auftrag nicht erfolgreich war.
   
3. Klicken Sie auf die Kachel **Auftragsverwaltung**, um die Aufträge anzuzeigen. Die Aufträge haben die Kategorien **Wird ausgeführt**, **In Warteschlange** und **Beendet**. Der fehlerhafte Auftrag sollte im Abschnitt **Beendet** angezeigt werden. Es muss der erste in der Liste sein. Wenn viele Aufträge vorhanden sind, können Sie auf **Filter** klicken, um Aufträge einfacher zu finden.

    ![Azure Data Lake Analytics – Filtern von Aufträgen](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Klicken Sie in der Liste auf den fehlerhaften Auftrag, um die Auftragsdetails in einem neuen Blatt zu öffnen:

    ![Azure Data Lake Analytics – Fehlerhafter Auftrag](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    Beachten Sie die Schaltfläche **Erneut senden** . Nachdem Sie das Problem behoben haben, können Sie den Auftrag erneut senden.

5. Klicken Sie auf den markierten Teil im vorigen Bildschirmfoto, um die Fehlerdetails zu öffnen. Die Ausgabe sollte etwa so aussehen:

    ![Azure Data Lake Analytics – Details zum fehlerhaften Auftrag](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Sie werden informiert, dass der Quellordner nicht gefunden wurde.
    
6. Klicken Sie auf **Skript duplizieren**.
7. Ändern Sie den Pfad **FROM** in Folgendes:

    „/Samples/Data/SearchLog.tsv“

8. Klicken Sie auf **Auftrag senden**.


##Weitere Informationen

- [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md)
- [Erste Schritte mit Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Erste Schritte mit Azure Data Lake Analytics und U-SQL mithilfe von Visual Studio](data-lake-analytics-get-started-u-sql-studio.md)
- [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-manage-use-portal.md)

<!---HONumber=Nov15_HO1-->