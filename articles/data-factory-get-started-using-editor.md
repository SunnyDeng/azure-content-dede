<properties 
	pageTitle="Erste Schritte mit Azure Data Factory" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine Beispieldatenpipeline erstellen, die Daten von einem Blob an eine Azure SQL-Datenbankinstanz kopiert." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Lernprogramm: Erstellen und Überwachen einer Factory mit Daten-factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##Inhalt dieses Lernprogramms
Dieses Lernprogramm enthält die folgenden Schritte aus:

Schritt | Beschreibung
-----| -----------
[Schritt 1: Erstellen einer Azure Data-Factory](#CreateDataFactory) | In diesem Schritt erstellen Sie eine Azure Data-Factory, die mit dem Namen **ADFTutorialDataFactory**.  
[Schritt 2: Erstellen von verknüpften Dienste](#CreateLinkedServices) | In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **AzureSqlLinkedService**. Der StorageLinkedService verbindet den Azure-Speicher und AzureSqlLinkedService die Azure SQL-Datenbank mit der ADFTutorialDataFactory verknüpft. Die Eingabedaten für die Pipeline befindet sich in einem blobcontainer im Azure Blob-Speicher und Ausgabedaten in einer Tabelle in der Azure SQL-Datenbank gespeichert werden. Daher fügen Sie diese beiden Datenspeichern als verknüpfte Dienste, der Daten-Factory.      
[Schritt 3: Erstellen von Eingabe- und Ausgabe von Tabellen](#CreateInputAndOutputDataSets) | Im vorherigen Schritt erstellt Sie verknüpfte Dienste, die auf Datenspeicher zu verweisen, die Eingabe/Ausgabe-Daten enthalten. In diesem Schritt definieren Sie zwei Tabellen für die Daten-Factory-- **EmpTableFromBlob** und **EmpSQLTable** –, die Eingabe/Ausgabe-Daten, die in den Datenspeichern gespeichert darstellen. Für die EmpTableFromBlob Geben Sie den blobcontainer, der ein Blob mit den Quelldaten und für die EmpSQLTable enthält, geben Sie die SQL-Tabelle, die die Ausgabedaten gespeichert werden sollen. Sie können auch andere Eigenschaften wie z. B. die Struktur der Daten, die Verfügbarkeit von Daten usw. angeben... 
[Schritt 4: Erstellen und Ausführen einer pipeline](#CreateAndRunAPipeline) | In diesem Schritt erstellen Sie eine Pipeline, die mit dem Namen **ADFTutorialPipeline** in den ADFTutorialDataFactory. Die Pipeline haben einen **Kopie Aktivität** dass es sich bei der Ausgabe Azure SQL-Tabelle kopiert, die Eingabe von Daten aus dem Azure blob.
[Schritt 5: Überwachen von Slices und pipeline](#MonitorDataSetsAndPipeline) | In diesem Schritt überwachen Sie die Segmente des Eingabe- und Tabellen, mithilfe von Azure-Vorschauportal.
 

## <a name="CreateDataFactory"></a>Schritt 1: Erstellen einer Azure Data-Factory
In diesem Schritt verwenden Sie das Azure Preview Portal zum Erstellen einer Azure Data-Factory, die mit dem Namen **ADFTutorialDataFactory**.

1.	Nach der Anmeldung die [Azure Preview Portal][azure-preview-portal], klicken Sie auf **Neu** Wählen Sie in der unteren linken Ecke **Datenanalyse** in die **Erstellen** Blade, und klicken Sie auf **Data Factory** in den **Datenanalyse** Blade. 

	![Neu -> DataFactory][image-data-factory-new-datafactory-menu]

6. In der **neue Daten Factory** Blade:
	1. Geben Sie **ADFTutorialDataFactory** für den **Namen**. 
	
  		![Neue Daten Factory-blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME** und gehen Sie folgendermaßen vor:
		1. Klicken Sie auf **Erstellen Sie eine neue Ressourcengruppe**.
		2. In der **Erstellen der Ressourcengruppe** Blade, geben Sie **ADFTutorialResourceGroup** für den **Namen** die Ressourcengruppe, und klicken Sie auf **OK**. 

			![Ressourcengruppe erstellen][image-data-factory-create-resource-group]

		Einige der Schritte in diesem Lernprogramm wird davon ausgegangen, dass Sie den Namen verwenden: **ADFTutorialResourceGroup** für die Ressourcengruppe. Weitere Informationen zu Ressourcengruppen finden Sie unter [Ressourcengruppen zum Verwalten von Azure-Ressourcen mit](resource-group-overview.md).  
7. In der **neue Daten Factory** Blade, beachten Sie, dass **zum Startmenü hinzufügen** ausgewählt ist.
8. Klicken Sie auf **Erstellen** in den **neue Daten Factory** Blade.

	> [AZURE.NOTE]Der Name der Azure Data Factory muss global eindeutig sein. Wenn Sie die Fehlermeldung: **Daten Factory Name "ADFTutorialDataFactory" ist nicht verfügbar**, ändern Sie den Namen der Factory Daten (z. B. YournameADFTutorialDataFactory), und versuchen Sie erneut zu erstellen. Verwenden Sie diesen Namen anstelle von ADFTutorialFactory, während die verbleibenden Schritte in diesem Lernprogramm ausführen. Finden Sie unter [Daten Factory - Benennungsregeln für][data-factory-naming-rules] Thema Benennungsregeln für Daten-Factory-Artefakte.
	 
	![Daten-Factory-Name ist nicht verfügbar][image-data-factory-name-not-available]

9. Klicken Sie auf **Benachrichtigungen** Hub auf der linken und sich für Benachrichtigungen von der Erstellungsprozess. Klicken Sie auf **X** zum Schließen der **Benachrichtigungen** Blade, falls er geöffnet ist.
10. Wenn die Erstellung abgeschlossen ist, sehen Sie die **DATA FACTORY** Blade wie unten dargestellt.

    ![Daten-Factory-Homepage][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Schritt 2: Erstellen von verknüpften Dienste
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, eine Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank sein.

In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **AzureSqlLinkedService**. StorageLinkedService verknüpft den Dienst Links Azure-Speicherkonto und AzureSqlLinkedService links einer Azure SQL-Datenbank, auf die **ADFTutorialDataFactory**. Erstellen Sie eine Pipeline weiter unten in diesem Lernprogramm, die Daten aus einem Blob-Container in StorageLinkedService in einer SQL-Tabelle in AzureSqlLinkedService kopiert.

### Erstellen Sie einen verknüpften Dienst für den Azure-Speicher-Konto
1.	In der **DATA FACTORY** Blade, klicken Sie auf **Autor und Bereitstellen von** Kachel zu starten der **Editor** für die Daten-Factory.

	![Kachel "Erstellen und bereitstellen"][image-author-deploy-tile]

	> [AZURE.NOTE]Finden Sie unter [Data Factory-Editor][data-factory-editor] Thema ausführliche Übersicht über den Daten-Factory-Editor.
	 
5. In der **Editor**, klicken Sie auf **neuen Datenspeicher** Schaltfläche auf der Symbolleiste, und wählen **Azure-Speicher** aus dem Dropdown-Menü. Die JSON-Vorlage zum Erstellen einer Azure-Speicher verknüpft-Diensts im rechten Fensterbereich sollte angezeigt werden.

	![Neue Data Store Schaltfläche-Editor][image-editor-newdatastore-button]
    
6. Ersetzen Sie **Accountname** und **Accountkey** mit dem Kontonamen und Kontowerte für Azure-Speicherkonto.

	![Blob-Speicher JSON-Editor][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Finden Sie unter [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) ausführliche Informationen zu JSON-Eigenschaften.

6. Klicken Sie auf **Bereitstellen** auf der Symbolleiste auf die StorageLinkedService bereitstellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTE SERVICE erstellt erfolgreich** auf der Titelleiste.

	![Editor-Blob-Speicher bereitstellen.][image-editor-blob-storage-deploy]

### Erstellen Sie einen verknüpften Dienst für Azure SQL-Datenbank
1. In der **Data Factory-Editor** , klicken Sie auf **neuen Datenspeicher** Schaltfläche auf der Symbolleiste, und wählen **Azure SQL-Datenbank** aus dem Dropdown-Menü. Die JSON-Vorlage zum Erstellen des SQL Azure-verknüpfte Diensts im rechten Fensterbereich sollte angezeigt werden.

	![Editr Azure SQL-Einstellungen][image-editor-azure-sql-settings]

2. Ersetzen Sie **Servername**, **Databasename**, **username@servername**, und **Kennwort** mit dem Namen Ihrer Azure SQL Server, Datenbank, Benutzerkonto und Kennwort. 
3. Klicken Sie auf **Bereitstellen** auf der Symbolleiste erstellen und Bereitstellen der AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Schritt 3: Erstellen von Eingabe- und Ausgabe von Tabellen
Im vorherigen Schritt erstellten Sie verknüpfte Dienste **StorageLinkedService** und **AzureSqlLinkedService** eine Azure Storage-Konto und Azure SQL-Datenbank mit der Factory Daten zu verknüpfen: **ADFTutorialDataFactory**. In diesem Schritt definieren Sie zwei Tabellen für die Daten-Factory-- **EmpTableFromBlob** und **EmpSQLTable** --darstellen die Eingabe/Ausgabe-Daten, die in den Datenspeichern bezeichneten StorageLinkedService und AzureSqlLinkedService gespeichert ist. Für EmpTableFromBlob Geben Sie den blobcontainer, der ein Blob mit den Quelldaten und EmpSQLTable enthält, geben Sie die SQL-Tabelle, die die Ausgabedaten gespeichert werden sollen.

### Erstellen der Eingabetabelle 
Eine Tabelle ist ein rechteckiges Dataset und verfügt über ein Schema. In diesem Schritt erstellen Sie eine Tabelle namens **EmpBlobTable** verweist auf einen Blob-Container in der Azure-Speicher, dargestellt durch die **StorageLinkedService** Dienst verknüpft.

1. In der **Editor** die Factory Daten klicken Sie auf **Neues Dataset** auf der Symbolleiste, und klicken Sie auf die Schaltfläche **Tabelle Blob** aus dem Dropdown-Menü. 
2. JSON im rechten Bereich mit der folgenden JSON-Ausschnitt zu ersetzen: 

        {
     	    "name": "EmpTableFromBlob",
		    "properties":
    		{
        		"structure":  
       			 [ 
            		{ "name": "FirstName", "type": "String"},
            		{ "name": "LastName", "type": "String"}
        		],
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     Beachten Sie Folgendes:
	
	- Speicherort **Typ** minFreeThreads auf **AzureBlobLocation**.
	- **LinkedServiceName** minFreeThreads auf **StorageLinkedService**. Sie mussten diese verknüpfte Dienst in Schritt2 erstellt.
	- **FolderPath** festgelegt ist, um die **Adftutorial** Container. Sie können auch den Namen eines Blobs innerhalb des Ordners angeben. Da Sie nicht den Namen des Blobs angeben, werden Daten aus allen Blobs im Container als Eingabedaten betrachtet.  
	- Format **Typ** minFreeThreads auf **TextFormat**
	- Es gibt zwei Felder in der Textdatei – **FirstName** und **LastName** – durch ein Komma getrennt (** ColumnDelimiter **)	
	- Die **Verfügbarkeit** festgelegt ist, um **stündlich** (** Häufigkeit ** festgelegt ist **Stunde** und **Intervall** festgelegt ist, um **1** ), so dass der Dienst Daten Factory Eingabedaten pro Stunde im Stammordner im Blob-Container (** Adftutorial **) angegeben. 
	

	Wenn Sie kein **Dateiname** für eine **input** **Tabelle**, alle Dateien/Blobs aus der Eingabeordner (** FolderPath **) gelten als Eingaben. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Finden Sie die Beispieldateien aus dem [Lernprogramm][adf-tutorial] Beispiele.
 
	Wenn Sie nicht angeben eine **Dateiname** für eine **Ausgabetabelle**, die generierte Dateien in die **FolderPath** werden im folgenden Format benannt: Daten & Lt; GUID & Gt;. TXT (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die **partitionedBy**-Eigenschaft. Im folgenden Beispiel verwendet folderPath die Angaben für Jahr, Monat und Tag aus "SliceStart" (Startzeit des zu verarbeitenden Slices) und "fileName" die Angabe für Stunde aus "SliceStart". Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE]Finden Sie unter [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) ausführliche Informationen zu JSON-Eigenschaften.

2. Klicken Sie auf **Bereitstellen** auf der Symbolleiste erstellen und Bereitstellen der **EmpTableFromBlob** Tabelle. Vergewissern Sie sich, dass Sie sehen die **Tabelle erfolgreich erstellt** Nachricht in der Titelleiste des Editors.

### Erstellen der Ausgabetabelle
In diesem Teil des Schritts, erstellen Sie eine Ausgabetabelle mit dem Namen **EmpSQLTable** verweist auf eine SQL-Tabelle in SQL Azure-Datenbank werden dargestellt, durch die **AzureSqlLinkedService** Dienst verknüpft.

1. In der **Editor** finden Sie die Factory Daten **Neues Dataset** auf der Symbolleiste, und klicken Sie auf die Schaltfläche **Azure SQL-Tabelle** aus dem Dropdown-Menü. 
2. JSON im rechten Bereich mit der folgenden JSON-Ausschnitt zu ersetzen:

        {
    		"name": "EmpSQLTable",
    		"properties":
    		{
        		"structure":
        		[
                	{ "name": "FirstName", "type": "String"},
                	{ "name": "LastName", "type": "String"}
        		],
        		"location":
        		{
            		"type": "AzureSqlTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "AzureSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Beachten Sie Folgendes:
	
	* Speicherort **Typ** minFreeThreads auf **AzureSQLTableLocation**.
	* **LinkedServiceName** minFreeThreads auf **AzureSqlLinkedService** (Sie haben diesen verknüpften Dienst in Schritt2 erstellte).
	* **Tablename** minFreeThreads auf **emp**.
	* Befinden sich drei Spalten – **ID**, **FirstName**, und **LastName** – in der Tabelle emp in der Datenbank-ID ist eine Identitätsspalte, daher Sie nur angeben müssen, aber **FirstName** und **LastName** hier.
	* Die **Verfügbarkeit** minFreeThreads auf **stündlich** (** Häufigkeit ** festgelegt **Stunde** und **Intervall** festgelegt **1**). Die Service Factory von Daten generiert eine Ausgabe Datenslice pro Stunde in die **emp** Tabelle in der Azure SQL-Datenbank.


3. Klicken Sie auf **Bereitstellen** auf der Symbolleiste erstellen und Bereitstellen der **EmpSQLTable** Tabelle.


## <a name="CreateAndRunAPipeline"></a>Schritt 4: Erstellen und Ausführen einer pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopie Aktivität** verwendet, die **EmpTableFromBlob** als Eingabe und **EmpSQLTable** als Ausgabe.

1. In der **Editor** finden Sie die Factory Daten **neue Pipeline** auf der Symbolleiste auf die Schaltfläche. Klicken Sie in der Symbolleiste auf **... (drei Punkte)**, wenn die Schaltfläche nicht angezeigt wird. Alternativ können Sie in der Strukturansicht mit der rechten Maustaste auf **Pipelines** und dann auf **Neue Pipeline** klicken.

	![Schaltfläche für neue Pipeline-Editor][image-editor-newpipeline-button]
 
2. JSON im rechten Bereich mit der folgenden JSON-Ausschnitt zu ersetzen:

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpTableFromBlob"} ],
						"outputs": [ {"name": "EmpSQLTable"} ],		
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		
					}
        		],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	Beachten Sie Folgendes:

	- Im Bereich Aktivitäten ist es nur eine Aktivität, deren **Typ** minFreeThreads auf **CopyActivity**.
	- Geben Sie für die Aktivität, dass festgelegt ist **EmpTableFromBlob** und Ausgabe für die Aktivität, dass festgelegt ist **EmpSQLTable**.
	- In der **Transformation** Abschnitt **BlobSource** als Quelltyp angegeben ist und **SqlSink** als Senkentyp angegeben ist.

	> [AZURE.NOTE]Ersetzen Sie den Wert der **start** Eigenschaft mit dem aktuellen Tag und **End** Wert mit am nächsten Tag. Sie können nur den Datumsteil angeben und den Zeitteil des Datums der Uhrzeit übergehen. Zum Beispiel "2015-02-03" entspricht "2015-02-03T00:00:00Z" sowohl start und Ende Uhrzeitangaben müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601). Zum Beispiel: 2014-10-14T16:32:41Z. Die **End** Zeit ist optional, aber wir werden es in diesem Lernprogramm verwenden. Wenn Sie keinen Wert zum Angeben der **End** -Eigenschaft wird berechnet als "** Start + 48 Stunden **". Geben Sie zum Ausführen der Pipeline auf unbestimmte Zeit **9999-09-09** als Wert für die **End** Eigenschaft. Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
	> [AZURE.NOTE]Finden Sie unter [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) ausführliche Informationen zu JSON-Eigenschaften.

4. Klicken Sie auf **Bereitstellen** auf der Symbolleiste erstellen und Bereitstellen der **ADFTutorialPipeline**. Vergewissern Sie sich, dass Sie sehen die **PIPELINE wurde erfolgreich erstellt** Nachricht.
5. Schließen Sie jetzt die **Editor** Blade, indem Sie auf **X**. Klicken Sie auf **X** zu schließen das Fenster "ADFTutorialDataFactory" in der Symbolleiste und der Struktur an. Wenn Sie sehen **Ihre nicht gespeicherten Änderungen werden verworfen,** auf **OK**.
6. Sie sollte an die **DATA FACTORY** Blade für die **ADFTutorialDataFactory**.

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.
 
### Die Factory Daten in einer Diagrammansicht anzeigen 
1. In der **DATA FACTORY** Blade, klicken Sie auf **Diagramm**.

	![Daten-Factory-Blade - Diagramm-Kachel][image-datafactoryblade-diagramtile]

2. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden:

	![Diagrammansicht][image-data-factory-get-started-diagram-blade]

	Sie können vergrößern, verkleinern, zoom auf 100 % Zoom anpassen, automatisch Positionieren von Pipelines und Tabellen und Anzeigen von Informationen über die Datenherkunft (hervorgehoben Upstream- und downstream-Elemente der ausgewählten Elemente). Sie können Double-Blick auf ein Objekt (Eingabe/Ausgabe-Tabelle oder Pipeline), um Eigenschaften dafür anzuzeigen. 
3. Mit der rechten Maustaste auf **ADFTutorialPipeline** in der Diagrammansicht und auf **offene Pipeline**. Die Aktivitäten in der Pipeline sowie Eingabe- und Datasets für die Aktivitäten sollte angezeigt werden. In diesem Lernprogramm haben Sie nur eine Aktivität in der Pipeline (Copy-Aktivität) mit EmpTableBlob als Eingabe-Dataset und EmpSQLTable als ausgabedataset.   

	![Offene Pipeline](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Klicken Sie auf **Data Factory** in die Breadcrumb-Leiste in der oberen linken Ecke, um die Diagrammansicht zurückzukehren. Die Diagrammansicht zeigt alle Leitungen. In diesem Beispiel haben Sie nur eine Pipeline erstellt.
 

## <a name="MonitorDataSetsAndPipeline"></a>Schritt 5: Überwachen der Datasets und die pipeline
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Ausführliche Informationen zum Verwenden von Cmdlets für die Überwachung finden Sie unter [Überwachen und Verwalten von Daten Factory mit PowerShell-Cmdlets][monitor-manage-using-powershell].

1. Navigieren Sie zu [Azure-Verwaltungsportal (Vorschau)][azure-preview-portal] Wenn Sie geöffnet haben. 
2. Wenn das Fenster für **ADFTutorialDataFactory** nicht geöffnet ist, öffnen es durch Klicken auf **ADFTutorialDataFactory** auf der **Startmenü**. 
3. In diesem Fenster sollten die Anzahl und Namen der erstellten Tabellen und der Pipeline angezeigt werden.

	![Startseite mit Namen][image-data-factory-get-started-home-page-pipeline-tables]

4. Klicken Sie nun **Datasets** Kachel.
5. In der **Datasets** Blade, klicken Sie auf **EmpTableFromBlob**. Dies ist die Eingabetabelle für die **ADFTutorialPipeline**.

	![Datasets mit EmpTableFromBlob ausgewählt][image-data-factory-get-started-datasets-emptable-selected]   
5. Beachten Sie, die bereits die Datensegmente bis zum aktuellen Zeitpunkt hergestellt wurden, und sie sind **bereit** da die **emp.txt** Datei jederzeit in den Blob-Container vorhanden: **Adftutorial\input**. Vergewissern Sie sich, dass keine Segmente angezeigt der **Slices einer vor kurzem fehlschlug** Abschnitt unten.

	Beide **kürzlich aktualisierte Segmente** und **Slices einer vor kurzem fehlschlug** Listen sortiert die **Zeit der letzten Aktualisierung**. Der Zeitpunkt des Updates eines Segments wird in den folgenden Situationen geändert.
    

	-  Sie den Status eines Segments manuell aktualisieren, z. B. mit der **Set AzureDataFactorySliceStatus** (oder), indem Sie auf **Ausführen** auf der **SLICE** Blade für das Segment.
	-  Das Segment ändert sich der Status aufgrund einer Ausführung (z. B. eine Ausführung gestartet, eine Ausführung beendet wurde und fehlgeschlagen ist, eine Ausführung beendet wurde und erfolgreich war, usw.).
 
	Klicken Sie auf den Titel der Listen oder **... (Ellipsen)** die Liste die größere Segmente anzeigen zu können. Klicken Sie auf **Filter** auf der Symbolleiste, um Segmente zu filtern.
	
	Klicken Sie zum Anzeigen der Datenslices sortiert nach den Slice Start-/Endzeiten stattdessen **Datenslices (von Slice-Zeit)** Kachel.

	![Datenslices Slice Zeit][DataSlicesBySliceTime]

6. Jetzt in der **Datasets** Blade, klicken Sie auf die **EmpSQLTable**. Dies ist die Ausgabentabelle für die **ADFTutorialPipeline**.

	![Datasets blade][image-data-factory-get-started-datasets-blade]



	 
6. Daraufhin sollte die **EmpSQLTable** Blade wie unten dargestellt:

	![Tabelle blade][image-data-factory-get-started-table-blade]
 
7. Beachten Sie, die bereits die Datensegmente bis zum aktuellen Zeitpunkt hergestellt wurden, und sie sind **bereit**. Keine Segmente werden in der **Problem Segmente** Abschnitt unten.
8. Klicken Sie auf **... (Mit den drei Punkten)** Um alle Segmente anzuzeigen.

	![Daten Slices blade][image-data-factory-get-started-dataslices-blade]

9. Klicken Sie auf ein Segment der Daten aus der Liste aus, und sollte die **DATENSLICE** Blade.

	![Daten Slice-blade][image-data-factory-get-started-dataslice-blade]
  
	Wenn das Segment nicht in die **bereit** Status, sehen Sie die upstream Segmente, die nicht bereit sind, und blockieren das aktuelle Segment in Ausführen der **Upstream-Segmente, die nicht bereit sind** Liste.

11. In der **DATENSLICE** Blade sollte alle Aktivitäten, die in der Liste unten ausgeführt wird. Klicken Sie auf ein **Aktivität ausführen** finden Sie unter der **AKTIVITÄTSDETAILS ausführen** Blade.

	![Aktivität auszuführen, Details][image-data-factory-get-started-activity-run-details]

	
12. Klicken Sie auf **X** alle Blades schließen, bis Sie wieder an die home-Blade für den **ADFTutorialDataFactory**.
14. (optional) Klicken Sie auf **Pipelines** auf der Startseite für **ADFTutorialDataFactory**, klicken Sie auf **ADFTutorialPipeline** in den **Pipelines** Blade und Drillthrough Eingabetabellen (** verbraucht **) oder Tabellen ausgeben (** produziert **).
15. Starten Sie **SQL Server Management Studio**, Herstellen einer Verbindung mit einer Azure SQL-Datenbank, und stellen Sie sicher, dass die Zeilen eingefügt werden, die **emp** Tabelle in der Datenbank.

	![Ergebnisse der SQL-Abfrage][image-data-factory-get-started-sql-query-results]


## Zusammenfassung 
In diesem Lernprogramm haben Sie eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbank zu kopieren. Das Azure Preview Portal wird verwendet, die Daten-Factory, verknüpften Dienste, Tabellen und einer Pipeline zu erstellen. Nachfolgend sind die allgemeinen Schritte aufgeführt, die Sie in diesem Lernprogramm ausgeführt haben:

1.	Erstellen eine Azure **Daten Factory**.
2.	Erstellen **Services verknüpft** zu verknüpfen, die Daten speichert und berechnet (bezeichnet als **verknüpften Dienste**) der Daten-Factory.
3.	Erstellen Sie **Tabellen** beschrieben, die Eingabe und Ausgabedaten für Pipelines.
4.	Erstellen Sie **Pipelines**. Eine Pipeline besteht aus einer oder mehreren Aktivitäten, verarbeitet die Eingaben und erzeugt Ausgaben. Legen Sie das aktive Zeitraums für die Pipeline durch Angabe **Start** Zeit und **End** für die Pipeline. Der aktive Zeitraums definiert die Dauer, für die Datenslices erzeugt werden. 


> [AZURE.NOTE]Eine Liste der unterstützten Aktivitäten, finden Sie unter [Pipelines und Aktivitäten][msdn-activities] Thema sowie eine Liste der unterstützten verknüpften Dienste, finden Sie unter [verknüpften Dienste][msdn-linkedservices] Thema in der MSDN Library.
> 
> Zum Ausführen dieses Lernprogramms mit Azure PowerShell finden Sie unter [Erstellen und Überwachen einer Daten-Factory mit Azure PowerShell][monitor-manage-using-powershell].

## Nächste Schritte

Artikel | Beschreibung
------ | ---------------
[Kopieren von Daten mit Azure Data Factory - Copy-Aktivität][copy-activity] | Dieser Artikel enthält eine ausführliche Beschreibung der **Kopie Aktivität** in diesem Lernprogramm verwendet. 
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise beim Kopieren von Daten aus einer **lokale SQL Server-Datenbank** in Azure-Blob. 
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien, die mit Daten-Factory][adf-tutorial] | Dieser Artikel bietet eine **End-to-End-Exemplarische Vorgehensweise** veranschaulicht, wie die Implementierung einer **realen Szenario** mithilfe von Azure Data Factory zum Transformieren von Daten aus den Protokolldateien Einblicke in.
[Problembehandlung bei Data Factory][troubleshoot] | Dieser Artikel beschreibt, wie **Problembehandlung bei** Azure Data Factory ausgibt. Sie können die exemplarische Vorgehensweise in diesem Artikel mit "ADFTutorialDataFactory" ausprobieren, indem Sie einen Fehler einbauen (die Tabelle in der Azure SQL-Datenbank löschen). 
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Entwicklerreferenz hat die umfassendes Referenzmaterial für die Cmdlets, JSON-Skripts, Funktionen usw.... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir-->