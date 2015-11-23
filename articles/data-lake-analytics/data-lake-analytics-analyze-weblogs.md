<properties 
   pageTitle="Analysieren von Websiteprotokollen mit Azure Data Lake Analytics | Azure" 
   description="Erfahren Sie, wie Sie Websiteprotokolle mit Azure Data Lake Analytics analysieren." 
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
   ms.date="11/09/2015"
   ms.author="jgao"/>

# Tutorial: Analysieren von Websiteprotokollen mit Azure Data Lake Analytics

Erfahren Sie, wie Sie Websiteprotokolle mit Azure Data Lake Analytics analysieren, um insbesondere herauszufinden, bei welchen Verweisern beim Versuch des Besuchs der Website Fehler aufgetreten sind.

>[AZURE.NOTE]Wenn Sie lediglich die Anwendung in Aktion sehen möchten, sparen Sie Zeit, indem Sie die [interaktiven Tutorials zum Verwenden von Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md) durchlaufen. Dieses Tutorial basiert auf dem gleiche Szenario und gleichen Code. Zweck dieses Tutorials ist, Entwicklern das Erstellen und Ausführen einer Data Lake Analytics-Anwendung von A bis Z zu veranschaulichen.

## Voraussetzungen:

- **Visual Studio 2015, Visual Studio 2013 Update 4 oder Visual Studio 2012 mit Installation von Visual C++**. 
- **Microsoft Azure SDK für .NET ab Version 2.5**. Führen Sie die Installation mit dem [Webplattform-Installer](http://www.microsoft.com/web/downloads/platform.aspx) durch.
- **[Data Lake-Tools für Visual Studio](http://aka.ms/adltoolsvs)**. 

	Nachdem die Data Lake-Tools für Visual Studio installiert wurden, wird in Visual Studio das Menü **Data Lake** angezeigt:
	
	![Menü „U-SQL“ in Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Grundkenntnisse in Data Lake Analytics und Data Lake-Tools für Visual Studio**. Informationen zu den ersten Schritten finden Sie unter:
 
	- [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
	- [Entwickeln von U-SQL-Skripts mit den Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)

- **Data Lake Analytics-Konto.** Siehe [Erstellen eines Azure Data Lake Analytics-Kontos](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

	In den Data Lake-Tools wird das Erstellen von Data Lake Analytics-Konten nicht unterstützt. Sie müssen sie also über das Azure-Vorschauportal, Azure PowerShell, das .NET SDK oder die Azure-Befehlszeilenschnittstelle erstellen. 
- **Hochladen der Beispieldaten in das Data Lake Analytics-Konto.** Siehe [Hochladen von „SearchLog.tsv“ in das standardmäßige Data Lake Storage-Konto](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Zum Ausführen eines Data Lake Analytics-Auftrags benötigen Sie einige Daten. Auch wenn die Data Lake-Tools das Hochladen von Daten unterstützen, verwenden Sie das Portal zum Hochladen der Beispieldaten, um das Durcharbeiten dieses Tutorials zu vereinfachen.
 
## Herstellen einer Verbindung mit Azure 

Bevor Sie U-SQL-Skripts erstellen und testen können, müssen Sie zuerst eine Verbindung mit Azure herstellen.

**So stellen Sie eine Verbindung mit Data Lake Analytics her**

1. Öffnen Sie Visual Studio.
2. Klicken Sie im Menü **Data Lake** auf **Optionen und Einstellungen**.
4. Klicken Sie auf **Anmelden** oder **Benutzer wechseln**, wenn jemand angemeldet ist, und befolgen Sie die Anweisungen.
5. Klicken Sie auf **OK**, um das Dialogfeld „Optionen und Einstellungen“ zu schließen.

**So durchsuchen Sie Ihre Data Lake Analytics-Konten**

1. Öffnen Sie in Visual Studio den **Server-Explorer**, indem Sie **STRG+ALT+S** drücken.
2. Erweitern Sie in **Server-Explorer** erst **Azure** und dann **Data Lake Analytics**. Es wird eine Liste mit Ihren Data Lake Analytics-Konten angezeigt, falls Konten vorhanden sind. Es ist nicht möglich, Data Lake Analytics-Konten über Visual Studio zu erstellen. Informationen zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md) oder [Erste Schritte mit Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## Entwickeln einer U-SQL-Anwendung 

Eine U-SQL-Anwendung ist größtenteils ein U-SQL-Skript. Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit U-SQL](data-lake-analytics-u-sql-get-started.md).

Sie können der Anwendung außerdem benutzerdefinierte Operatoren hinzufügen. Weitere Informationen finden Sie unter [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-develop-user-defined-operators.md).
 
**So erstellen und übermitteln Sie einen Data Lake Analytics-Auftrag**

1. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.
2. Wählen Sie den Typ „U-SQL-Projekt“ aus.

	![Neues U-SQL-Projekt in Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
	
3. Klicken Sie auf **OK**. Visual Studio erstellt eine Projektmappe mit der Datei „Script.usql“.
4. Fügen Sie das folgende Skript in die Datei „Script.usql“ ein:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;
        
        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema. 
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string, 
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string, 
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string, 
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int, 
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN
        
            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
		    RETURN;
		END;
        
        // Create a table for storing referrers and status 
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS 
        
        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer, 
                sc_status;
        
    Grundlegendes zu U-SQL finden Sie unter [Erste Schritte mit der Sprache U-SQL für Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
       
5. Fügen Sie Ihrem Projekt ein neues U-SQL-Skript hinzu, und geben Sie Folgendes ein:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;
        
        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Wechseln Sie zurück zum ersten U-SQL-Skript, und geben Sie neben der Schaltfläche **Senden** Ihr Analytics-Konto an.
7. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript erstellen**. Überprüfen Sie das Ergebnis im Ausgabebereich.
8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript senden**.
9. Überprüfen Sie, ob das **Analytics-Konto** jenes ist, in dem Sie den Auftrag ausführen möchten, und klicken Sie dann auf **Senden**. Nach Abschluss der Übermittlung werden im Fenster „Ergebnisse“ von Data Lake-Tools für Visual Studio Ergebnisse und ein Auftragslink angezeigt.
10. Warten Sie, bis der Auftrag erfolgreich abgeschlossen wurde. Sollte der Auftrag misslingen, fehlt meist die Quelldatei. Siehe den Abschnitt „Voraussetzung“ in diesem Tutorial. Weitere Informationen zur Problembehandlung finden Sie unter [Überwachung und Problembehandlung von Azure Data Lake Analytics-Aufträgen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Nach Abschluss des Auftrags sollte der folgende Bildschirm angezeigt werden:
    
    ![Data Lake Analytics, Analysieren von Websiteprotokollen](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Wiederholen Sie jetzt die Schritte 7 bis 10 für **Script1.usql**.

>[AZURE.NOTE]Sie können Daten nicht aus einer U-SQL-Tabelle lesen oder in diese schreiben, wenn die Tabelle im selben Skript erstellt oder geändert wurde. Aus diesem Grund werden in diesem Beispiel zwei Skripts verwendet.

**So zeigen Sie die Auftragsausgabe an**

1. Erweitern Sie in **Server-Explorer** nacheinander **Azure**, **Data Lake Analytics**, Ihr Data Lake Analytics-Konto und **Speicherkonten**. Klicken Sie mit der rechten Maustaste auf das Data Lake-Standardspeicherkonto, und klicken Sie dann auf **Explorer**. 
2.  Doppelklicken Sie auf **Beispiele**, um den Ordner zu öffnen, und doppelklicken Sie dann auf **Ausgaben**.
3.  Doppelklicken Sie auf **UnsuccessfulResponses.log**.
4.  Sie können auch in der Diagrammansicht des Auftrags auf die Ausgabedatei doppelklicken, um direkt zur Ausgabe zu navigieren.
        
## Weitere Informationen

Informationen zu den ersten Schritten mit Data Lake Analytics unter Verwendung unterschiedlicher Tools finden Sie unter:

- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
- [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des .NET SDK](data-lake-analytics-get-started-net-sdk.md)

Weitere Themen zur Entwicklung:

- [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
- [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-user-defined-operators.md)

<!---HONumber=Nov15_HO3-->