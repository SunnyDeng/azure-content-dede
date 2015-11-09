<properties 
   pageTitle="Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio | Azure" 
   description="Erfahren Sie, wie Sie Data Lake-Tools für Visual Studio installieren und U-SQL-Skripts entwickeln und testen." 
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
   ms.date="10/28/2015"
   ms.author="jgao"/>

# Tutorial: Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Erfahren Sie, wie Sie Data Lake-Tools für Visual Studio installieren und zum Schreiben und Testen von U-SQL-Skripts verwenden.

U-SQL ist eine hyperskalierbare, hochgradig erweiterbare Sprache zum Vorbereiten, Transformieren und Analysieren aller Daten im „Data Lake“ und darüber hinaus. Weitere Informationen finden Sie unter [U-SQL-Referenz](http://go.microsoft.com/fwlink/p/?LinkId=691348).


**Voraussetzungen**

- **Visual Studio 2015, Visual Studio 2013 Update 4 oder Visual Studio 2012 mit Installation von Visual C++** 
- **Microsoft Azure SDK für .NET Version 2.5 oder höher**. Führen Sie die Installation mit dem [Webplattform-Installer](http://www.microsoft.com/web/downloads/platform.aspx) durch.
- **[Data Lake-Tools für Visual Studio](http://aka.ms/adltoolsvs)** 

    Nachdem die Installation der Data Lake-Tools für Visual Studio abgeschlossen ist, wird in Visual Studio ein Data Lake-Menü angezeigt:
    
    ![U-SQL-Menü in Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Arbeiten Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md) die folgenden beiden Abschnitte durch**:

	- [Erstellen eines Azure Data Lake Analytics-Kontos](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)
	- [Hochladen von „SearchLog.tsv“ in das standardmäßige Data Lake-Speicherkonto](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account)

	Für die Data Lake-Tools wird das Erstellen von Data Lake Analytics-Konten nicht unterstützt. Sie müssen sie also mit dem Azure-Vorschauportal oder über Azure PowerShell, das .NET SDK oder die Azure-Befehlszeilenschnittstelle erstellen. Zum Ausführen eines Data Lake Analytics-Auftrags benötigen Sie einige Daten. Auch wenn die Data Lake-Tools das Hochladen von Daten unterstützen, verwenden Sie das Portal zum Hochladen der Beispieldaten, um das Durcharbeiten dieses Tutorials zu vereinfachen.

## Herstellen einer Verbindung mit Azure

**So stellen Sie eine Verbindung mit Data Lake Analytics her**

1. Öffnen Sie Visual Studio.
2. Klicken Sie im Menü **U-SQL** auf **Optionen und Einstellungen**.
4. Klicken Sie auf **Anmelden** oder **Benutzer wechseln**, wenn eine Person angemeldet ist, und befolgen Sie die Schritte für die Anmeldung.
5. Klicken Sie auf **OK**, um das Dialogfeld „Optionen und Einstellungen“ zu schließen.

**So durchsuchen Sie Ihre Data Lake Analytics-Konten**

1. Öffnen Sie in Visual Studio den **Server-Explorer**, indem Sie **STRG+ALT+S** drücken.
2. Erweitern Sie in **Server-Explorer** erst **Azure** und dann **Data Lake Analytics**. Es wird eine Liste mit Ihren Data Lake Analytics-Konten angezeigt, falls Konten vorhanden sind. Es ist nicht möglich, Data Lake Analytics-Konten über Visual Studio zu erstellen. Informationen zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md) oder [Erste Schritte mit Azure Data Lake Analytics mithilfe von Azure PowerShell](knoa-get-started-powershell.md).

## Hochladen von Quelldatendateien

Sie haben in diesem Tutorial im Abschnitt **Voraussetzungen** bereits Daten hochgeladen.

Falls Sie Ihre eigenen Daten verwenden möchten, können Sie die hier angegebene Vorgehensweise zum Hochladen von Daten über die Data Lake-Tools nutzen.

**So laden Sie die Datei in das abhängige Azure Data Lake-Konto hoch**

1. Erweitern Sie in **Server-Explorer** nacheinander die Optionen **Azure** und **Data Lake Analytics** und dann Ihr Data Lake Analytics-Konto und die Option **Speicherkonten**. Sie sehen das standardmäßige Data Lake-Speicherkonto und die verknüpften Data Lake-Speicherkonten sowie die verknüpften Azure-Speicherkonten. Das Data Lake-Standardkonto hat die Bezeichnung „Standardspeicherkonto“.
2. Klicken Sie mit der rechten Maustaste auf das standardmäßige Data Lake-Speicherkonto, und klicken Sie dann auf **Explorer**. Der Explorer-Bereich mit den Data Lake-Tools für Visual Studio wird geöffnet. Links wird eine Verzeichnisstruktur angezeigt, und rechts befindet sich die Inhaltsansicht.
3. Navigieren Sie zu dem Ordner, in den Sie die Dateien hochladen möchten. 
4. Klicken Sie mit der rechten Maustaste auf einen leeren Bereich, und klicken Sie dann auf **Hochladen**. 

	![Visual Studio-U-SQL-Projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**So laden Sie die Datei in ein Azure-BLOB-Speicherkonto hoch**

1. Erweitern Sie in **Server-Explorer** nacheinander die Optionen **Azure** und **Data Lake Analytics** und dann Ihr Data Lake Analytics-Konto und die Option **Speicherkonten**. Sie sehen das standardmäßige Data Lake-Speicherkonto und die verknüpften Data Lake-Speicherkonten sowie die verknüpften Azure-Speicherkonten. 
2. Erweitern Sie das Azure-Speicherkonto.
3. Klicken Sie mit der rechten Maustaste auf den Container, in den Sie Dateien hochladen möchten, und klicken Sie dann auf **Explorer**.
4. Navigieren Sie zu dem Ordner, in den Sie die Dateien hochladen möchten. 
5. Klicken Sie mit der rechten Maustaste auf einen leeren Bereich, und klicken Sie dann auf **Hochladen**. 

## Entwickeln und Testen von U-SQL-Skripts 

Die Data Lake Analytics-Aufträge werden in der Sprache U-SQL geschrieben. Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit der U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md) und in der [Referenz zur U-SQL-Sprache](http://go.microsoft.com/fwlink/?LinkId=691348) (in englischer Sprache).

**So erstellen und übermitteln Sie einen Data Lake Analytics-Auftrag**

1. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.
2. Wählen Sie den Typ **U-SQL-Projekt** aus.

	![Neues Visual Studio-U-SQL-Projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klicken Sie auf **OK**. Visual Studio erstellt eine Projektmappe mit der Datei **Script.usql**.
4. Geben Sie das folgende Skript in **Script.usql** ein:

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
    
    Ändern Sie die beiden Pfade nur, wenn Sie die Quelldatei an einen anderen Speicherort kopiert haben. Data Lake Analytics erstellt den Ausgabeordner, falls er nicht vorhanden ist.
	
	Es ist einfacher, für Dateien, die unter Data Lake-Standardkonten gespeichert sind, relative Pfade zu verwenden. Sie können aber auch absolute Pfade verwenden. Beispiel:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Sie müssen absolute Pfade verwenden, um auf Dateien in verknüpften Speicherkonten zuzugreifen. Die Syntax für Dateien, die unter dem verknüpften Azure-Speicherkonto gespeichert werden, lautet wie folgt:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Azure-BLOB-Container mit öffentlichen Blobs oder Zugriffsberechtigungen für öffentliche Container werden derzeit nicht unterstützt.

	Beachten Sie die folgenden Features:

	- **IntelliSense**
	 
		Der Name wird automatisch vervollständigt, und die Member werden für Rowsets, Klassen, Datenbanken, Schemas und benutzerdefinierte Objekte (User Defined Objects, UDOs) angezeigt.
		 
		IntelliSense für Katalogentitäten (Datenbanken, Schemas, Tabellen, UDOs usw.) bezieht sich auf Ihr Compute-Konto. Sie können das aktuelle aktive Compute-Konto, die Datenbank und das Schema in der obersten Symbolleiste überprüfen und über die Dropdownlisten wechseln.
 
	- **Automatische Formatierung**
	
		Benutzer können den Einzug des Scope-Skripts basierend auf der Codestruktur unter „Bearbeiten“ -> „Erweitert“ ändern:
  
		- Dokument formatieren (STRG+E, D) : Das gesamte Dokument wird formatiert.   
		- Auswahl formatieren (STRG+K, STRG+F): Die Auswahl wird formatiert. Wenn keine Auswahl getroffen wurde, wird mit dieser Tastenkombination die Zeile formatiert, in der sich der Cursor befindet.  
		
		Sie können alle Formatierungsregeln unter „Extras“ -> „Optionen“ -> „Text-Editor“ - > „SIP“ -> „Formatierung“ konfigurieren.  
	- **Intelligenter Einzug**
	 
		Mit Data Lake-Tools für Visual Studio können Sie automatische Einzüge für Ausdrücke verwenden, während Sie Skripts schreiben. Dieses Feature ist standardmäßig deaktiviert. Benutzer müssen es aktivieren, indem sie die Option „U-SQL“ -> „Optionen und Einstellungen“ -> „Schalter“ -> „Intelligenten Einzug aktivieren“ aktivieren.

	- **„Gehe zu Definition“ und „Alle Verweise suchen“**
	
		Klicken Sie mit der rechten Maustaste auf den Namen eines Elements vom Typ Rowset/Parameter/Spalte/UDO usw. Wenn Sie dann auf „Gehe zu Definition“ (F12) klicken, können Sie zur dazugehörigen Definition navigieren. Wenn Sie auf „Alle Verweise suchen“ (UMSCHALT+F12) klicken, werden alle Verweise angezeigt.

	- **Azure-Pfad einfügen**
		
		Sie müssen sich den Azure-Dateipfad nicht merken und ihn beim Schreiben des Skripts nicht manuell eingeben. Data Lake-Tools für Visual Studio bieten eine einfache Möglichkeit: Klicken Sie mit der rechten Maustaste im Editor, und klicken Sie dann auf „Azure-Pfad einfügen“. Navigieren Sie im Azure-BLOB-Browserdialogfeld zur Datei. Klicken Sie auf die Schaltfläche „OK“. Der Dateipfad wird in Ihren Code eingefügt.

5. Geben Sie das Data Lake Analytics-Konto, die -Datenbank und das -Schema an:

	![Visual Studio-U-SQL-Projekt senden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Weitere Informationen finden Sie unter [Verwenden des U-SQL-Katalogs](data-lake-analytics-use-u-sql-catalog.md).

5. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript erstellen**. Überprüfen Sie das Ergebnis im Ausgabebereich.
6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript senden**. Optional können Sie auch im Script.usql-Bereich auf **Senden** klicken. Betrachten Sie hierzu den vorherigen Screenshot. Klicken Sie neben der Schaltfläche „Senden“ auf den Pfeil nach unten, um die erweiterten Optionen für das Senden zu verwenden:
7. Geben Sie **Auftragsname** an, überprüfen Sie das **Analytics-Konto**, und klicken Sie auf **Senden**. Die Sendeergebnisse und die Auftragsverknüpfung sind im Ergebnisfenster der Data Lake-Tools für Visual Studio verfügbar, nachdem die Übermittlung abgeschlossen ist.

	![Visual Studio-U-SQL-Projekt senden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. Sie müssen auf die Schaltfläche „Aktualisieren“ klicken, um den letzten Auftragsstatus anzuzeigen und den Bildschirm zu aktualisieren. Wenn der Auftrag erfolgreich ist, werden dafür die Informationen **Auftragsdiagramm**, **Metadatenvorgänge**, **Zustandsverlauf** und **Diagnose** angezeigt:

	![U-SQL Visual Studio Data Lake Analytics-Diagramm zur Auftragsleistung](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

	* API-Zusammenfassung: Hier können Sie die zusammengefassten Informationen des aktuellen Auftrags anzeigen, z. B. Zustand, Status, Ausführungszeit, Laufzeitname, Absender usw.   
	* Auftragsdetails: Hier werden ausführliche Informationen zum Auftrag bereitgestellt, z. B. Skript, Ressource, Scheitelpunktausführungsansicht.
	* Auftragsdiagramm: Es werden vier Diagramme zur Visualisierung der Auftragsinformationen bereitgestellt: Status, gelesene Daten, geschriebene Daten, Ausführungszeit, durchschnittliche Ausführungszeit pro Knoten, Eingangsdurchsatz, Ausgangsdurchsatz.
	* Metadatenvorgänge: Hier werden alle Metadatenvorgänge angezeigt.
	* Zustandsverlauf. 
	* Diagnose: Mit den Data Lake-Tools für Visual Studio wird die Auftragsausführung automatisch diagnostiziert. Sie erhalten Benachrichtigungen, falls für die Aufträge Fehler oder Leistungsprobleme auftreten. Weitere Informationen finden Sie unter „Auftragsdiagnose“ (Link TBD). 
	
**So überprüfen Sie den Auftragsstatus**

1. Erweitern Sie in Server-Explorer nacheinander die Optionen **Azure** und **Data Lake Analytics** und dann Ihr Data Lake Analytics-Konto.
2. Doppelklicken Sie auf **Aufträge**, um eine Liste mit den Aufträgen anzuzeigen.
2. Klicken Sie auf einen Auftrag, um seinen Zustand anzuzeigen.

**So zeigen Sie die Auftragsausgabe an**

1. Erweitern Sie in **Server-Explorer** nacheinander **Azure**, **Data Lake Analytics**, Ihr Data Lake Analytics-Konto und **Speicherkonten**. Klicken Sie mit der rechten Maustaste auf das Data Lake-Standardspeicherkonto, und klicken Sie dann auf **Explorer**. 
2.  Doppelklicken Sie auf **Ausgabe**, um den Ordner zu öffnen.
3.  Doppelklicken Sie auf **SearchLog-From-adltools.csv**.


###Auftragswiedergabe 

Mithilfe der Auftragswiedergabe können Sie den Status der Auftragsausführung verfolgen und Unregelmäßigkeiten und Engpässe in Bezug auf die Leistung visuell erkennen. Sie können diese Funktion sowohl vor dem Ende der Auftragsausführung (also während der aktiven Ausführung des Auftrags) als auch nach Abschluss der Ausführung verwenden. Wenn die Wiedergabe während der Auftragsausführung erfolgt, können die Benutzer den Fortschritt bis zum gegenwärtigen Zeitpunkt wiedergeben.

**So zeigen Sie den Status der Auftragsausführung an**

1. Klicken Sie oben rechts auf **Profil laden**. Betrachten Sie hierzu den vorherigen Screenshot.
2. Klicken Sie unten links auf die Wiedergabeschaltfläche, um den Status der Auftragsausführung zu überprüfen. 
3. Klicken Sie während der Wiedergabe auf **Anhalten**, um die Wiedergabe zu beenden, oder ziehen Sie die Fortschrittsleiste direkt an bestimmte Positionen. 


###Heat Map 

In den Data Lake-Tools für Visual Studio werden auswählbare Farbüberlagerungen für die Auftragsanzeige bereitgestellt, um Status, Ein-/Ausgang von Daten, Ausführungszeit, E/A-Durchsatz für jede Phase anzeigen zu können. So können Benutzer potenzielle Probleme und die Verteilung von Auftragseigenschaften direkt und intuitiv ermitteln. Sie können eine anzuzeigende Datenquelle in der Dropdownliste auswählen.



##Weitere Informationen

Informationen zu den ersten Schritten mit Data Lake Analytics unter Verwendung unterschiedlicher Tools finden Sie unter:

- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
- [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des .NET SDK](data-lake-analytics-get-started-net-sdk.md)

Weitere Themen zur Entwicklung:

- [Analysieren von Weblogs mit Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
- [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Erste Schritte mit Azure Data Lake Analytics-U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md)
- [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-user-defined-operators.md)

<!---HONumber=Nov15_HO1-->