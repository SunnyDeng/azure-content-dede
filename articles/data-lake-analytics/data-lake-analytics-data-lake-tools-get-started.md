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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/07/2015"
   ms.author="jgao"/>

# Tutorial: Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Erfahren Sie, wie Sie Data Lake-Tools für Visual Studio installieren und zum Schreiben und Testen von U-SQL-Skripts verwenden.

U-SQL ist eine hyperskalierbare, hochgradig erweiterbare Sprache zum Vorbereiten, Transformieren und Analysieren aller Daten im „Data Lake“ und darüber hinaus. Weitere Informationen finden Sie unter [U-SQL-Referenz](http://go.microsoft.com/fwlink/p/?LinkId=691348).


###Voraussetzungen

- **Visual Studio 2015, Visual Studio 2013 Update 4 oder Visual Studio 2012 mit Installation von Visual C++** 
- **Microsoft Azure SDK für .NET ab Version 2.7.1**. Führen Sie die Installation mit dem [Webplattform-Installer](http://www.microsoft.com/web/downloads/platform.aspx) durch.
- **[Data Lake-Tools für Visual Studio](http://aka.ms/adltoolsvs)** 

    Nachdem die Installation der Data Lake-Tools für Visual Studio abgeschlossen ist, wird in Visual Studio ein Data Lake-Menü angezeigt:
    
    ![U-SQL-Menü in Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Arbeiten Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)** die folgenden beiden Abschnitte durch:

	- [Erstellen eines Azure Data Lake Analytics-Kontos](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)
	- [Hochladen von „SearchLog.tsv“ in das standardmäßige Data Lake-Speicherkonto](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account)

    Zur Vereinfachung stellen wir Ihnen ein PowerShell-Beispielskript zur Verfügung, mit dem Sie einen Data Lake Analytics-Dienst erstellen und die Quelldatendatei hochladen können. Sie finden dieses Skript unter [Anhang A: PowerShell-Beispiel zur Vorbereitung des Tutorials](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).
    
	Für die Data Lake-Tools wird das Erstellen von Data Lake Analytics-Konten nicht unterstützt. Sie müssen sie also mit dem Azure-Portal oder über Azure PowerShell, das .NET SDK oder die Azure-Befehlszeilenschnittstelle erstellen. Zum Ausführen eines Data Lake Analytics-Auftrags benötigen Sie einige Daten. Auch wenn die Data Lake-Tools das Hochladen von Daten unterstützen, verwenden Sie das Portal zum Hochladen der Beispieldaten, um das Durcharbeiten dieses Tutorials zu vereinfachen.

## Herstellen einer Verbindung mit Azure

**So stellen Sie eine Verbindung mit Data Lake Analytics her**

1. Öffnen Sie Visual Studio.
2. Klicken Sie im Menü **Ansicht** auf **Server-Explorer**, um den Server-Explorer zu öffnen. Oder drücken Sie **STRG+ALT+S**.
3. Klicken Sie mit der rechten Maustaste auf **Azure**, klicken Sie auf „Verbindung mit Microsoft Azure-Abonnement herstellen“, und befolgen Sie die Anweisungen.
4. Erweitern Sie in **Server-Explorer** erst **Azure** und dann **Data Lake Analytics**. Es wird eine Liste mit Ihren Data Lake Analytics-Konten angezeigt, falls Konten vorhanden sind. Es ist nicht möglich, Data Lake Analytics-Konten über Visual Studio zu erstellen. Informationen zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md) oder [Erste Schritte mit Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics--get-started-powershell.md).

## Hochladen von Quelldatendateien

Sie haben in diesem Tutorial im Abschnitt **Voraussetzungen** bereits Daten hochgeladen.

Falls Sie Ihre eigenen Daten verwenden möchten, können Sie die hier angegebene Vorgehensweise zum Hochladen von Daten über die Data Lake-Tools nutzen.

**So laden Sie Dateien in das abhängige Azure Data Lake-Konto hoch**

1. Erweitern Sie in **Server-Explorer** nacheinander die Optionen **Azure** und **Data Lake Analytics** und dann Ihr Data Lake Analytics-Konto und die Option **Speicherkonten**. Sie sehen das standardmäßige Data Lake-Speicherkonto und die verknüpften Data Lake-Speicherkonten sowie die verknüpften Azure-Speicherkonten. Das Data Lake-Standardkonto hat die Bezeichnung „Standardspeicherkonto“.
2. Klicken Sie mit der rechten Maustaste auf das standardmäßige Data Lake-Speicherkonto, und klicken Sie dann auf **Explorer**. Der Explorer-Bereich mit den Data Lake-Tools für Visual Studio wird geöffnet. Links wird eine Verzeichnisstruktur angezeigt, und rechts befindet sich die Inhaltsansicht.
3. Navigieren Sie zu dem Ordner, in den Sie die Dateien hochladen möchten. 
4. Klicken Sie mit der rechten Maustaste auf einen leeren Bereich, und klicken Sie dann auf **Hochladen**. 

	![Visual Studio-U-SQL-Projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**So laden Sie Dateien in ein verknüpftes Azure Blob Storage-Konto hoch**

1. Erweitern Sie in **Server-Explorer** nacheinander die Optionen **Azure** und **Data Lake Analytics** und dann Ihr Data Lake Analytics-Konto und die Option **Speicherkonten**. Sie sehen das standardmäßige Data Lake-Speicherkonto und die verknüpften Data Lake-Speicherkonten sowie die verknüpften Azure-Speicherkonten. 
2. Erweitern Sie das Azure-Speicherkonto.
3. Klicken Sie mit der rechten Maustaste auf den Container, in den Sie Dateien hochladen möchten, und klicken Sie dann auf **Explorer**. Wenn Sie keinen Container besitzen, müssen Sie zunächst einen mit dem Azure-Portal, Azure PowerShell oder anderen Tools erstellen.
4. Navigieren Sie zu dem Ordner, in den Sie die Dateien hochladen möchten. 
5. Klicken Sie mit der rechten Maustaste auf einen leeren Bereich, und klicken Sie dann auf **Hochladen**. 

## Entwickeln eines U-SQL-Skripts 

Die Data Lake Analytics-Aufträge werden in der Sprache U-SQL geschrieben. Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit der U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md) und in der [U-SQL Language Reference](http://go.microsoft.com/fwlink/?LinkId=691348) (in englischer Sprache).

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

	Mit diesem U-SQL-Skript wird die Quelldatendatei mithilfe von **Extractors.Tsv()** gelesen, und anschließend wird eine CSV-Datei mithilfe von **Outputters.Csv()** erstellt.
    
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
 
    - **Erweitern von Spalten mit ***

        Klicken Sie rechts neben das Sternchen-Symbol („*“), und dann sehen Sie unter dem Sternchen eine blaue Unterstreichung. Zeigen Sie mit dem Mauszeiger auf die blaue Unterstreichung, und klicken Sie auf den Pfeil nach unten. ![Data Lake visual studio tools expand *](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

        Klicken Sie auf **Spalten erweitern**, und das Tool ersetzt das Sternchen („*“) durch die Spaltennamen.
    
	- **Automatische Formatierung**
	
		Benutzer können den Einzug des Scope-Skripts basierend auf der Codestruktur unter „Bearbeiten“ -> „Erweitert“ ändern:
  
		- Dokument formatieren (STRG+E, D): Das gesamte Dokument wird formatiert.   
		- Auswahl formatieren (STRG+K, STRG+F): Die Auswahl wird formatiert. Wenn keine Auswahl getroffen wurde, wird mit dieser Tastenkombination die Zeile formatiert, in der sich der Cursor befindet.  
		
		Sie können alle Formatierungsregeln unter „Extras“ -> „Optionen“ -> „Text-Editor“ - > „SIP“ -> „Formatierung“ konfigurieren.  
	- **Intelligenter Einzug**
	 
		Mit Data Lake-Tools für Visual Studio können Sie automatische Einzüge für Ausdrücke verwenden, während Sie Skripts schreiben. Dieses Feature ist standardmäßig deaktiviert. Benutzer müssen es aktivieren, indem sie die Option „U-SQL“ -> „Optionen und Einstellungen“ -> „Schalter“ -> „Intelligenten Einzug aktivieren“ aktivieren.

	- **„Gehe zu Definition“ und „Alle Verweise suchen“**
	
		Klicken Sie mit der rechten Maustaste auf den Namen eines Elements vom Typ Rowset/Parameter/Spalte/UDO usw. Wenn Sie dann auf „Gehe zu Definition“ (F12) klicken, können Sie zur dazugehörigen Definition navigieren. Wenn Sie auf „Alle Verweise suchen“ (UMSCHALT+F12) klicken, werden alle Verweise angezeigt.

	- **Azure-Pfad einfügen**
		
		Sie müssen sich den Azure-Dateipfad nicht merken und ihn beim Schreiben des Skripts nicht manuell eingeben. Data Lake-Tools für Visual Studio bieten eine einfache Möglichkeit: Klicken Sie mit der rechten Maustaste im Editor, und klicken Sie dann auf „Azure-Pfad einfügen“. Navigieren Sie im Azure-BLOB-Browserdialogfeld zur Datei. Klicken Sie auf **OK**. Der Dateipfad wird in Ihren Code eingefügt.

5. Geben Sie das Data Lake Analytics-Konto, die -Datenbank und das -Schema an. Sie können **(lokal)** auswählen, um das Skript zu Testzwecken lokal auszuführen. Weitere Informationen finden Sie unter [Lokales Ausführen von U-SQL](#run-u-sql-locally).

	![Visual Studio-U-SQL-Projekt senden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Weitere Informationen finden Sie unter [Verwenden des U-SQL-Katalogs](data-lake-analytics-use-u-sql-catalog.md).

5. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript erstellen**. Überprüfen Sie das Ergebnis im Ausgabebereich.
6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und klicken Sie dann auf **Skript senden**. Optional können Sie auch im Script.usql-Bereich auf **Senden** klicken. Betrachten Sie hierzu den vorherigen Screenshot. Klicken Sie neben der Schaltfläche „Senden“ auf den Pfeil nach unten, um die erweiterten Optionen für das Senden zu verwenden:
7. Geben Sie **Auftragsname** an, überprüfen Sie das **Analytics-Konto**, und klicken Sie auf **Senden**. Die Sendeergebnisse und die Auftragsverknüpfung sind im Ergebnisfenster der Data Lake-Tools für Visual Studio verfügbar, nachdem die Übermittlung abgeschlossen ist.

	![Visual Studio-U-SQL-Projekt senden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. Sie müssen auf die Schaltfläche „Aktualisieren“ klicken, um den letzten Auftragsstatus anzuzeigen und den Bildschirm zu aktualisieren. Wenn der Auftrag erfolgreich ist, werden dafür die Informationen **Auftragsdiagramm**, **Metadatenvorgänge**, **Zustandsverlauf** und **Diagnose** angezeigt:

	![U-SQL Visual Studio Data Lake Analytics-Diagramm zur Auftragsleistung](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

	* Auftragszusammenfassung. Hier können Sie die zusammengefassten Informationen des aktuellen Auftrags anzeigen, z. B. Zustand, Status, Ausführungszeit, Laufzeitname, Absender usw.   
	* Auftragsdetails. Hier werden ausführliche Informationen zum Auftrag bereitgestellt, z. B. Skript, Ressource, Scheitelpunktausführungsansicht.
	* Auftragsdiagramm: Es werden vier Diagramme zur Visualisierung der Auftragsinformationen bereitgestellt: Status, gelesene Daten, geschriebene Daten, Ausführungszeit, durchschnittliche Ausführungszeit pro Knoten, Eingangsdurchsatz, Ausgangsdurchsatz.
	* Metadatenvorgänge: Hier werden alle Metadatenvorgänge angezeigt.
	* Zustandsverlauf. 
	* Diagnose. Mit den Data Lake-Tools für Visual Studio wird die Auftragsausführung automatisch diagnostiziert. Sie erhalten Benachrichtigungen, falls für die Aufträge Fehler oder Leistungsprobleme auftreten. Weitere Informationen finden Sie unter „Auftragsdiagnose“ (Link TBD). 
	
**So überprüfen Sie den Auftragsstatus**

1. Erweitern Sie in Server-Explorer nacheinander die Optionen **Azure** und **Data Lake Analytics** und dann Ihren Data Lake Analytics-Kontonamen.
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


###Wärmebild 

In den Data Lake-Tools für Visual Studio werden auswählbare Farbüberlagerungen für die Auftragsanzeige bereitgestellt, um Status, Ein-/Ausgang von Daten, Ausführungszeit, E/A-Durchsatz für jede Phase anzeigen zu können. So können Benutzer potenzielle Probleme und die Verteilung von Auftragseigenschaften direkt und intuitiv ermitteln. Sie können eine anzuzeigende Datenquelle in der Dropdownliste auswählen.

## Lokales Ausführen von U-SQL

Mithilfe der U-SQL-Oberfläche zum lokalen Ausführen in Visual Studio können Sie Folgendes:

- U-SQL-Skripts zusammen mit C#-Assemblys lokal ausführen 
- C#-Assemblys lokal debuggen 
- Lokale Datenbanken, Assemblys, Schemas und Tabellen in Server-Explorer so erstellen, löschen und anzeigen, wie Sie es auch für Ihren Azure Data Lake Analytics-Dienst können. 

In Visual Studio wird ein Konto *Lokal* angezeigt, und das Installationsprogramm erstellt einen Ordner *DataRoot* im Pfad *C:\\LocalRunRoot*. Der Ordner „DataRoot“ wird wie folgt verwendet:

- Speichern von Metadaten, einschließlich Tabellen, Datenbanken, TVFs usw.
- Für ein bestimmtes Skript: Wenn in Eingabe-/Ausgabepfaden auf einen relativen Pfad verwiesen wird, wird DataRoot nachgeschlagen (sowie der Pfad des Skripts, wenn er zur Eingabe gehört).
- Auf den Ordner „DataRoot“ wird nicht verwiesen, wenn Sie versuchen, eine Assembly zu registrieren, und dabei einen relativen Pfad verwenden (Weitere Details finden Sie im Abschnitt unter „Verwenden von Assemblys bei lokalen Ausführungen“.)

Das folgende Video veranschaulicht das Feature zum lokalen Ausführen von U-SQL:

>[AZURE.VIDEO usql-localrun]

### Bekannte Probleme und Einschränkungen

- Die lokale Ausführung von U-SQL unterstützt keine lokalen Abfragen von Dateigruppen. Weitere Informationen finden Sie unter [U-SQL-Dateigruppen](https://msdn.microsoft.com/library/azure/mt621294.aspx). Dies wird in Zukunft behoben.
- Langsame Leistung aufgrund geringer Parallelität, da Auftragspläne nacheinander in einem einzelnen Prozess ausgeführt werden. 
- Die lokale Ausführung kann keine Auftragsdiagramme in Visual Studio anzeigen. Dies wird in Zukunft behoben. 
- In Server-Explorer kann keine Tabelle/Datenbank usw. für das lokale Konto erstellt werden.
- Wenn auf einen relativen Pfad verwiesen wird:

    - In der Skripteingabe (EXTRACT * FROM "/path/abc"): Sowohl der „DataRoot“-Pfad als auch der Skriptpfad werden durchsucht. 
    - In der Skriptausgabe (OUTPUT TO "pfad/abc"): Der „DataRoot“-Pfad wird als Ausgabeordner verwendet. 
    - In der Assemblyregistrierung (CRREATE ASSEMBLY xyz FROM "/path/abc"): Der Skriptpfad wird durchsucht, aber nicht DataRoot. 
    - In registrierten TVF-/Ansichts- oder anderen Metadatenentitäten: Der „DataRoot“-Pfad wird durchsucht, aber nicht der Skriptpfad. 
    
    Bei Skripts, die im Data Lake-Dienst ausgeführt werden, wird das Standardspeicherkonto als Stammordner verwendet und dementsprechend durchsucht.

### Lokales Testen von U-SQL-Skripts
Anleitungen zur Entwicklung von U-SQL-Skripts finden Sie unter [Entwickeln eines U-SQL-Skripts](#develop-and-test-u-sql-scripts). Wählen Sie zum lokalen Erstellen und Ausführen von U-SQL-Skripts in der Cluster-Dropdownliste **(Lokal)** aus, und klicken Sie dann auf **Senden**. Stellen Sie sicher, dass Sie auf die richtigen Daten verweisen – verweisen Sie entweder auf den absoluten Pfad, oder legen Sie die Daten im Ordner „DataRoot“ ab.

![Lokales Senden von Visual Studio-U-SQL-Projekten](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-local-run.png)

Sie können auch mit der rechten Maustaste auf ein Skript klicken und dann im Kontextmenü auf **Lokalen Plan ausführen** klicken, oder Sie drücken **STRG+F5**, um die lokale Ausführung auszulösen.

### Verwenden von Assemblys bei der lokalen Ausführung

Es gibt zwei Möglichkeiten zum Ausführen der angepassten C#-Dateien:

- Schreiben Sie die Assemblys in die CodeBehind-Datei, und die Assemblys werden automatisch registriert und gelöscht, nachdem das Skript ausgeführt wurde. 
- Erstellen Sie ein C#-Assemblyprojekt, und registrieren Sie die Ausgabe-DLL für das lokale Konto über ein ähnliches Skript wie unten gezeigt. Beachten Sie, dass sich der Pfad relativ auf das Skript, nicht auf den Ordner „DataRoot“ bezieht.

![Verwenden von Assemblys bei der lokalen U-SQL-Ausführung](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-local-run-assembly.png)
 
### Lokales Debuggen von Skripts und C#-Assemblys

Sie können C#-Assemblys debuggen, ohne sie zu senden und beim Azure Data Lake Analytics-Dienst zu registrieren. Sie können sowohl in der Code-Behind-Datei als auch in einem referenzierten C#-Projekt Haltepunkte festlegen.

**So debuggen Sie lokalen Code in der CodeBehind-Datei:** 1. Legen Sie Haltepunkte in der CodeBehind-Datei fest. 2. Drücken Sie **F5**, um das Skript lokal zu debuggen.

Das folgende Verfahren funktioniert nur in Visual Studio 2015. In älteren Versionen von Visual Studio müssen Sie eventuell die PDB-Dateien manuell hinzufügen.

**So debuggen Sie lokalen Code in einem referenzierten C#-Projekt:** 1. Erstellen Sie ein C#-Assemblyprojekt, und erstellen Sie es, um die Ausgabe-DLL zu generieren. 2. Registrieren Sie die DLL mithilfe einer U-SQL-Anweisung:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
3.	Legen Sie Haltepunkte im C#-Code fest.
4.	Drücken Sie **F5**, um das Skript mit Verweis auf die C#-DLL lokal zu debuggen.  

##Weitere Informationen

Informationen zu den ersten Schritten mit Data Lake Analytics unter Verwendung unterschiedlicher Tools finden Sie unter:

- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
- [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des .NET SDK](data-lake-analytics-get-started-net-sdk.md)

Weitere Themen zur Entwicklung:

- [Analysieren von Weblogs mit Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
- [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Erste Schritte mit Azure Data Lake Analytics-U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md)
- [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-user-defined-operators.md)

##Anhang A: PowerShell-Beispiel zur Vorbereitung des Tutorials

Mit dem folgenden PowerShell-Skript werden das Azure Data Lake Analytics-Konto und die Quelldaten für Sie vorbereitetet, sodass Sie zu [Entwickeln eines U-SQL-Skripts](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts) springen können.

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>" 
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location 

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion

<!---HONumber=AcomDC_0114_2016-->