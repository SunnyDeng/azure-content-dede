<properties 
   pageTitle="Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals | Azure" 
   description="Erfahren Sie, wie Sie das Azure-Vorschauportal zum Erstellen eines Data Lake Analytics-Kontos, Erstellen eines Data Lake Analytics-Auftrags mit U-SQL und Senden des Auftrags verwenden." 
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
   ms.date="10/22/2015"
   ms.author="jgao"/>

# Tutorial: Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Enthält Informationen zum Verwenden des Azure-Vorschauportals zum Erstellen von Azure Data Lake Analytics-Konten, zum Definieren von Data Lake Analytics-Aufträgen in [U-SQL](data-lake-analytics-u-sql-get-started.md) und zum Übermitteln von Aufträgen an Data Lake Analytics-Konten. Weitere Informationen zu Data Lake Analytics finden Sie unter [Übersicht über Azure Data Lake Analytics](data-lake-analytics-overview.md).

In diesem Tutorial entwickeln Sie einen Auftrag, bei dem eine Datei mit tabulatorgetrennten Werten (TSV) gelesen und in eine Datei mit kommagetrennten Werten (CSV) konvertiert wird. Um das gleiche Tutorial unter Verwendung anderer unterstützter Tools zu durchlaufen, klicken Sie auf die Registerkarten oben in diesem Abschnitt. Nachdem Ihr erster Auftrag erfolgreich durchgeführt wurde, können Sie damit beginnen, mit U-SQL komplexere Datentransformationen zu schreiben.

**Grundlegender Data Lake Analytics-Prozess:**

![Azure Data Lake Analytics-Prozessflussdiagramm](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Erstellen Sie ein Data Lake Analytics-Konto.
2. Bereiten Sie die Quelldaten vor. Bei Data Lake Analytics-Aufträgen können Daten entweder aus Azure Data Lake-Speicherkonten oder Azure-BLOB-Speicherkonten gelesen werden. In diesem Beispiel werden Daten aus dem Azure Data Lake-Speicher gelesen.  
3. Entwickeln Sie ein U-SQL-Skript.
4. Übermitteln Sie einen Auftrag (U-SQL-Skript) an das Data Lake Analytics-Konto. Für den Auftrag werden die Quelldaten gelesen, die Daten werden gemäß Anweisung im U-SQL-Skript verarbeitet, und anschließend wird die Ausgabe entweder in einem Data Lake-Speicherkonto oder einem BLOB-Speicherkonto gespeichert.

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/de-DE/pricing/free-trial/).

##Erstellen eines Data Lake Analytics-Kontos

Zum Ausführen von Aufträgen ist ein Data Lake Analytics-Konto erforderlich.

Jedes Data Lake Analytics-Konto ist von einem [Azure Data Lake-Speicher]()konto abhängig. Dieses Konto wird als Data Lake-Standardspeicherkonto bezeichnet. Sie können das Data Lake-Speicherkonto vorher oder während der Erstellung Ihres Data Lake Analytics-Kontos erstellen. In diesem Tutorial erstellen Sie das Data Lake-Speicherkonto mit dem Data Lake Analytics-Konto.

**So erstellen Sie ein Data Lake Analytics-Konto**

1. Melden Sie sich am neuen [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Neu**, auf **Daten + Analysen** und anschließend auf **Data Lake Analytics**.
6. Geben Sie Folgendes ein bzw. wählen Sie es aus:

    ![Azure Data Lake Analytics-Portalblatt](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Name**: Geben Sie dem Analytics-Konto einen Namen.
	- **Data Lake-Speicher**: Jedes Data Lake Analytics-Konto verfügt über ein abhängiges Data Lake-Speicherkonto. Das Data Lake Analytics-Konto und das abhängige Data Lake-Speicherkonto müssen sich in demselben Azure-Rechenzentrum befinden. Führen Sie die Anweisungen zum Erstellen eines neuen Data Lake-Speicherkontos aus, oder wählen Sie ein vorhandenes Konto aus.
	- **Abonnement**: Wählen Sie das Azure-Abonnement aus, das für das Analytics-Konto verwendet wird.
	- **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md). 
	- **Standort**: Wählen Sie ein Azure-Rechenzentrum für das Data Lake Analytics-Konto aus. 
7. Wählen Sie **An Startmenü anheften**. Dies ist für das folgende Tutorial erforderlich.
8. Klicken Sie auf **Erstellen**. Sie gelangen zum Startmenü des Portals. Dem Startmenü wird eine neue Kachel mit der Bezeichnung „Azure Data Lake Analytics wird bereitgestellt“ hinzugefügt. Es dauert einige Zeit, bis ein Data Lake Analytics-Konto erstellt wurde. Beim Erstellen des Kontos wird es auf einem neuen Blatt des Portals geöffnet.

	![Azure Data Lake Analytics-Portalblatt](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)


Nachdem ein Data Lake Analytics-Konto erstellt wurde, können Sie weitere Data Lake-Speicherkonten und Azure-Speicherkonten hinzufügen. Eine Anleitung finden Sie unter [Verwalten von Datenquellen für Data Lake Analytics-Konten](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

##Vorbereiten von Quelldaten

In diesem Tutorial verarbeiten Sie einige Suchprotokolle. Das Suchprotokoll kann entweder in einem Data Lake-Speicher oder einem Azure-BLOB-Speicher gespeichert werden.

Das Azure-Vorschauportal enthält eine Benutzeroberfläche zum Kopieren einiger Beispieldatendateien in das Data Lake-Standardkonto. Hierzu gehört auch eine Suchprotokolldatei.

**So kopieren Sie Beispieldatendateien**

1. Klicken Sie im Azure-Vorschauportal oben links auf **Microsoft Azure**.
2. Klicken Sie auf die Kachel mit Ihrem Data Lake Analytics-Kontonamen. Der Name wurde hier angeheftet, als das Konto erstellt wurde. Falls das Konto hier nicht angeheftet ist, können Sie die Informationen unter [Öffnen eines Data Lake Analytics-Kontos im Portal](data-lake-analytics-manage-use-portal.md#access-adla-account) nutzen, um das Konto zu öffnen.
3. Erweitern Sie den Bereich **Zusammenfassung**, und klicken Sie dann auf **Auf Beispielaufträge zugreifen**. Ein anderes Blatt mit dem Namen **Beispielaufträge** wird geöffnet.
4. Klicken Sie auf **Beispieldaten kopieren** und dann auf **OK**, um den Vorgang zu bestätigen.
5. Klicken Sie auf **Benachrichtigung** (ein Symbol in Glockenform). Sie sehen den Eintrag **Aktualisierung der Beispieldaten abgeschlossen**. Klicken Sie auf eine Stelle außerhalb des Benachrichtigungsbereichs, um ihn zu schließen.
7. Klicken Sie auf dem Data Lake Analytics-Kontoblatt oben auf **Daten-Explorer**. 

	![Azure Data Lake Analytics, Schaltfläche „Daten-Explorer“](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Zwei Blätter werden geöffnet. Ein Blatt für den **Daten-Explorer** und ein anderes Blatt mit dem Data Lake-Standardspeicherkonto.
8. Klicken Sie auf dem Blatt mit dem Data Lake-Standardspeicherkonto auf **Beispiele**, um den Ordner zu erweitern, und klicken Sie dann auf **Daten**, um den Ordner zu erweitern. Die folgenden Dateien und Ordner werden angezeigt:

    - AmbulanceData/
    - AdsLog.tsv
    - SearchLog.tsv
    - version.txt
    - WebLog.log
    
    In diesem Tutorial verwenden Sie „SearchLog.tsv“.

In der Praxis programmieren Sie Ihre Anwendungen entweder so, dass Daten in ein verknüpftes Speicherkonto geschrieben werden, oder Sie laden die Daten hoch. Informationen zum Hochladen von Dateien finden Sie unter [Hochladen von Daten in den Data Lake-Speicher](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) oder [Hochladen von Daten in den BLOB-Speicher](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

##Erstellen und Übermitteln von Data Lake Analytics-Aufträgen

Nachdem Sie die Quelldaten vorbereitet haben, können Sie mit dem Entwickeln eines U-SQL-Skripts beginnen.

**So übermitteln Sie den Auftrag**

1. Klicken Sie im Portal auf dem Data Lake Analytics-Kontoblatt auf **Neuer Auftrag**. 

	![Azure Data Lake Analytics, Schaltfläche „Neuer Auftrag“](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Wenn das Blatt nicht angezeigt wird, helfen Ihnen die Informationen unter [Öffnen eines Data Lake Analytics-Kontos im Portal](data-lake-analytics-manage-use-portal.md#access-adla-account) weiter.
4. Geben Sie den **Auftragsnamen** und das folgende U-SQL-Skript ein:

	![Azure Data Lake Analytics-U-SQL-Aufträge erstellen](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Mit diesem U-SQL-Skript wird die Quelldatei mithilfe von **Extractors.Tsv()** gelesen, und anschließend wird eine CSV-Datei mithilfe von **Outputters.Csv()** erstellt.
    
    Ändern Sie die beiden Pfade nur, wenn Sie die Quelldatei an einen anderen Speicherort kopieren. Data Lake Analytics erstellt den Ausgabeordner, falls er nicht vorhanden ist. In diesem Fall verwenden wir einfache relative Pfade.
	
	Es ist einfacher, für Dateien, die unter Data Lake-Standardkonten gespeichert sind, relative Pfade zu verwenden. Sie können aber auch absolute Pfade verwenden. Beispiel:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
      

    Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit Azure Data Lake Analytics-U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md) und in der [Referenz zur U-SQL-Sprache](http://go.microsoft.com/fwlink/?LinkId=691348) (in englischer Sprache).
     
5. Klicken Sie oben auf **Auftrag senden**. Ein neuer Bereich mit Auftragsdetails wird geöffnet. In der Titelleiste wird der Auftragsstatus angezeigt.
6. Warten Sie, bis sich der Auftragsstatus in **Erfolgreich** ändert. Wenn der Auftrag abgeschlossen ist, werden die Auftragsdetails im Portal in einem neuen Blatt geöffnet:

    ![Azure Data Lake Analytics-Auftragsdetails](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-completed.png)

    In der vorherigen Abbildung können Sie sehen, dass der Auftrag vom Zustand „Gesendet“ bis zum Zustand „Beendet“ ungefähr 1,5 Minuten gedauert hat.
    
    Falls für den Auftrag ein Fehler auftritt, helfen Ihnen die Informationen unter [Überwachung und Problembehandlung für Data Lake Analytics-Aufträge](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorials.md) weiter.

7. Klicken Sie unten auf dem Blatt **Auftragdetails** in **SearchLog-from-Data-Lake.csv** auf den Auftragsnamen. Sie können für die Ausgabedatei eine Vorschau anzeigen und sie herunterladen, umbenennen und löschen.

    ![Azure Data Lake Analytics-Auftragsausgabe, Dateieigenschaften](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)
8. Klicken Sie auf **Vorschau**, um die Ausgabedatei anzuzeigen.

    ![Azure Data Lake Analytics-Auftragsausgabe, Dateivorschau](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-output-preview.png)

##Weitere Informationen

- Eine komplexere Abfrage finden Sie unter [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Informationen zu den ersten Schritten der Entwicklung von U-SQL-Anwendungen finden Sie unter [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Informationen zum Erlernen von U-SQL finden Sie unter [Erste Schritte mit Azure Data Lake Analytics-U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md).
- Informationen zu Verwaltungsaufgaben finden Sie unter [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-manage-use-portal.md).
- Eine Übersicht über Data Lake Analytics finden Sie unter [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md).
- Wenn Sie dasselbe Tutorial mit anderen Tools verwenden möchten, klicken Sie oben auf der Seite auf die Registerkartenauswahl.

<!---HONumber=Nov15_HO1-->