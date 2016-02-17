<properties 
	pageTitle="Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe des Data Factory-Editors" 
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline mit Kopieraktivität mithilfe des Data Factory-Editors im klassischen Azure-Portal." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe des Data Factory-Editors
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)



##Dieses Lernprogramm umfasst folgende Punkte
Folgende Themen werden in diesem Lernprogramm behandelt:

Schritt | Beschreibung
-----| -----------
[Schritt 1: Erstellen einer Azure Data Factory](#CreateDataFactory) | In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen **ADFTutorialDataFactory**.  
[Schritt 2: Erstellen von verknüpften Diensten](#CreateLinkedServices) | In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **AzureSqlLinkedService**. "StorageLinkedService" verbindet den Azure-Speicher und "AzureSqlLinkedService" die Azure SQL-Datenbank mit "ADFTutorialDataFactory". Die Eingabedaten für die Pipeline befinden sich in einem Blobcontainer im Azure-Blobspeicher. Ausgabedaten werden in einer Tabelle in der Azure SQL-Datenbank gespeichert. Daher fügen Sie diese beiden Datenspeicher als verknüpfte Dienste der Data Factory hinzu.      
[Schritt 3: Erstellen von Ein- und Ausgabetabellen](#CreateInputAndOutputDataSets) | Im vorherigen Schritt haben Sie verknüpfte Dienste erstellt, die auf Datenspeicher verweisen, die Ein- und Ausgabedaten enthalten. In diesem Schritt definieren Sie die beiden Data Factory-Tabellen **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten darstellen, die in den Datenspeichern gespeichert sind. Für die Tabelle "EmpTableFromBlob" geben Sie den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Für die Tabelle "EmpSQLTable" geben Sie die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden sollen. Sie können auch andere Eigenschaften wie z. B. die Struktur der Daten, die Verfügbarkeit von Daten usw. angeben. 
[Schritt 4: Erstellen und Ausführen einer Pipeline](#CreateAndRunAPipeline) | In diesem Schritt erstellen Sie die Pipeline **ADFTutorialPipeline** in der ADFTutorialDataFactory. Die Pipeline weist eine **Kopieraktivität** auf, die zum Kopieren von Eingabedaten aus dem Azure-Blob in die Azure SQL-Tabelle verwendet wird.
[Schritt 5: Überwachen von Slices und Pipeline](#MonitorDataSetsAndPipeline) | In diesem Schritt überwachen Sie die Slices von Eingabe- und Tabellen im Azure-Portal.
 

## <a name="CreateDataFactory"></a>Schritt 1: Erstellen einer Azure Data Factory
In diesem Schritt erstellen Sie im Azure-Portal eine Azure Data Factory namens **ADFTutorialDataFactory**.

1.	Nach der Anmeldung beim [Azure-Portal][azure-portal] klicken Sie links unten auf **NEU**, wählen **Datenanalyse** auf dem Blatt **Erstellen** aus und klicken auf dem Blatt **Datenanalyse** auf **Data Factory**. 

	![Neu -> Data Factory][image-data-factory-new-datafactory-menu]

6. Gehen Sie auf dem Blatt **Neue Data Factory** so vor:
	1. Geben Sie **ADFTutorialDataFactory** als **Namen** ein. 
	
  		![Blatt "Neue Data Factory"][image-data-factory-getstarted-new-data-factory-blade]
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und führen Sie die folgenden Schritte aus:
		1. Klicken Sie auf **Neue Ressourcengruppe erstellen**.
		2. Geben Sie **ADFTutorialResourceGroup** auf dem Blatt **Ressourcengruppe erstellen** als **Namen** der Ressourcengruppe ein, und klicken Sie auf **OK**. 

			![Ressourcengruppe erstellen][image-data-factory-create-resource-group]

		Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens **ADFTutorialResourceGroup** verwenden. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../resource-group-overview.md).  
7. Auf dem Blatt **Neue Data Factory** ist **Zum Startmenü hinzufügen** aktiviert.
8. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

	Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name "ADFTutorialDataFactory" ist nicht verfügbar** ändern Sie den Namen der Data Factory (z. B.in "IhrNameADFTutorialDataFactory") und wiederholen den Vorgang. Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.
	 
	![Data Factory-Name nicht verfügbar][image-data-factory-name-not-available]
	
	> [AZURE.NOTE] Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.

9. Klicken Sie auf der linken Seite auf **BENACHRICHTIGUNGEN**, und suchen Sie nach Benachrichtigungen zum Erstellungsprozess. Klicken Sie auf **X**, um das Blatt **Benachrichtigungen** zu schließen, falls es geöffnet ist.
10. Nach Abschluss der Erstellung wird das Blatt **DATA FACTORY** wie unten dargestellt angezeigt:

    ![Data Factory-Startseite][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Schritt 2: Erstellen von verknüpften Diensten
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, eine Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank sein.

In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **AzureSqlLinkedService**. Der verknüpfte Dienst "StorageLinkedService" verknüpft ein Azure-Speicherkonto und "AzureSqlLinkedService" eine Azure SQL-Datenbank mit der **ADFTutorialDataFactory**. Später in diesem Lernprogramm erstellen Sie eine Pipeline, die Daten aus einem Blobcontainer in "StorageLinkedService" in eine SQL-Tabelle in "AzureSqlLinkedService" kopiert.

### Erstellen eines verknüpften Diensts für das Azure-Speicherkonto
1.	Klicken Sie auf dem Blatt **DATA FACTORY** auf die Kachel **Erstellen und bereitstellen**, um den **Editor** für die Data Factory zu starten.

	![Kachel "Erstellen und bereitstellen"][image-author-deploy-tile]

	 
5. Klicken Sie im **Editor** auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü **Azure-Speicher** aus. Die JSON-Vorlage zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im rechten Bereich angezeigt werden.

	![Editor – Schaltfläche „Neuer Datenspeicher“][image-editor-newdatastore-button]
    
6. Ersetzen Sie **accountname** und **accountkey** durch die Werte für den Kontonamen und -schlüssel Ihres Azure-Speicherkontos.

	![Editor – Blobspeicher – JSON][image-editor-blob-storage-json]
	
	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst „StorageLinkedService“ bereitzustellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTER DIENST ERFOLGREICH ERSTELLT** auf der Titelleiste des Editors angezeigt wird.

	![Editor – Blobspeicher bereitstellen][image-editor-blob-storage-deploy]

### Erstellen eines verknüpften Diensts für die Azure SQL-Datenbank
1. Klicken Sie im **Data Factory-Editor** auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü **Azure SQL-Datenbank** aus. Die JSON-Vorlage zum Erstellen eines mit der Azure SQL-Datenbank verknüpften Diensts sollte im rechten Bereich angezeigt werden.

	![Editor – Azure SQL-Einstellungen][image-editor-azure-sql-settings]

2. Ersetzen Sie **servername**, **databasename**, ****username@servername** und **password** durch die Namen des Azure SQL-Servers und der Datenbank bzw. durch Benutzerkonto und Kennwort.
3. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst „AzureSqlLinkedService“ zu erstellen und bereitzustellen. 
   

## <a name="CreateInputAndOutputDataSets"></a>Schritt 3: Erstellen von Ein- und Ausgabetabellen
Im vorherigen Schritt haben Sie die verknüpften Dienste **StorageLinkedService** und **AzureSqlLinkedService** erstellt, um ein Azure-Speicherkonto und eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactory** zu verknüpfen. In diesem Schritt definieren Sie die beiden Data Factory-Tabellen **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten darstellen, die in den Datenspeichern gespeichert sind, auf die "StorageLinkedService" und "AzureSqlLinkedService" verweisen. Für die Tabelle "EmpTableFromBlob" geben Sie den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Für die Tabelle "EmpSQLTable" geben Sie die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden sollen.

### Erstellen der Eingabetabelle 
Eine Tabelle ist ein rechteckiges Dataset und verfügt über ein Schema. In diesem Schritt erstellen Sie eine Tabelle namens **EmpBlobTable**, die auf einen Blobcontainer im Azure-Speicher verweist, der vom verknüpften Dienst **StorageLinkedService** dargestellt wird.

1. Klicken Sie im **Editor** für die Data Factory auf der Symbolleiste auf **Neues Dataset** und dann im Dropdownmenü auf **Blobtabelle**. 
2. Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt: 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Beachten Sie Folgendes:
	
	- **dataset type** ist auf **AzureBlob** festgelegt.
	- **linkedServiceName** ist auf **StorageLinkedService** festgelegt. Sie haben diesen verknüpften Dienst in Schritt 2 erstellt.
	- **folderPath** ist auf den Container **adftutorial** festgelegt. Sie können auch den Namen eines Blobs innerhalb des Ordners angeben. Da Sie nicht den Namen des Blobs angeben, werden Daten aus allen Blobs im Container als Eingabedaten betrachtet.  
	- **format: type** ist auf **TextFormat** festgelegt.
	- Die Textdatei enthält die beiden Felder **FirstName** und **LastName**, die durch ein Komma getrennt sind (**columnDelimiter**).	
	- Die Verfügbarkeit (**availability**) ist auf **hourly** festgelegt (**frequency** auf **hour** und **interval** auf **1**), sodass der Data Factory-Dienst den Stammordner im angegebenen Blob-Container (**adftutorial**) stündlich auf Eingabedaten untersucht. 
	

	Wenn Sie keinen **fileName** für eine **Eingabe****tabelle** angeben, werden alle Dateien/Blobs aus dem Eingabeordner (**folderPath**) als Eingaben angesehen. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Beispiele finden Sie in den Beispieldateien des [Lernprogramms][adf-tutorial].
 
	Wenn Sie **fileName** für eine **Ausgabetabelle** nicht angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.&lt;GUID&gt;.txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um die Tabelle **EmpTableFromBlob** bereitzustellen. Vergewissern Sie sich, dass die Meldung **TABELLE ERFOLGREICH ERSTELLT** auf der Titelleiste des Editors angezeigt wird.

### Erstellen der Ausgabetabelle
In diesem Teil des Schritts erstellen Sie eine Ausgabetabelle namens **EmpSQLTable**, die auf eine SQL-Tabelle in der Azure SQL-Datenbank verweist, die durch den verknüpften Dienst **AzureSqlLinkedService** dargestellt wird.

1. Klicken Sie im **Editor** für die Data Factory auf der Symbolleiste auf **Neues Dataset** und dann im Dropdownmenü auf **Azure SQL-Tabelle**. 
2. Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt:

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     Beachten Sie Folgendes:
	
	* **dataset type** ist auf **AzureSQLTable** festgelegt.
	* **linkedServiceName** ist auf **AzureSqlLinkedService** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
	* **tablename** ist auf **emp** festgelegt.
	* Die Tabelle "emp" der Datenbank enthält die drei Spalten **ID**, **FirstName** und **LastName**. "ID" ist jedoch eine Identitätsspalte, weshalb Sie hier nur **FirstName** und **LastName** angeben müssen.
	* Die Verfügbarkeit (**availability**) ist auf **hourly**, (**frequency** auf **hour** und **interval** auf **1**) festgelegt. Der Data Factory-Dienst generiert in der Tabelle **emp** in der Azure SQL-Datenbank stündlich einen Ausgabedatenslice.


3. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um die Tabelle **EmpSQLTable** bereitzustellen.


## <a name="CreateAndRunAPipeline"></a>Schritt 4: Erstellen und Ausführen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, die **EmpTableFromBlob** als Eingabe und **EmpSQLTable** als Ausgabe verwendet.

1. Klicken Sie im **Editor** für die Data Factory auf der Symbolleiste auf die Schaltfläche **Neue Pipeline**. Klicken Sie in der Symbolleiste auf **... (drei Punkte)**, wenn die Schaltfläche nicht angezeigt wird. Alternativ können Sie in der Strukturansicht mit der rechten Maustaste auf **Pipelines** und dann auf **Neue Pipeline** klicken.

	![Editor – Schaltfläche "Neue Pipeline"][image-editor-newpipeline-button]
 
2. Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt:
		
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	Beachten Sie Folgendes:

	- Der Abschnitt "activities" enthält nur eine Aktivität, deren **type** auf **CopyActivity** festgelegt ist.
	- Die Eingabe für die Aktivität ist auf **EmpTableFromBlob** und die Ausgabe auf **EmpSQLTable** festgelegt.
	- Im Abschnitt **transformation** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben.

	Ersetzen Sie den Wert der **start** Eigenschaft durch den aktuellen Tag und den der **End**-Eigenschaft durch den nächsten Tag. Sie können auch nur den Datumsteil angeben und den Uhrzeitteil überspringen. "2015-02-03" entspricht z. B. "2015-02-03T00:00:00Z"
	
	Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Angabe für **end** ist optional, wird aber in diesem Lernprogramm verwendet.
	
	Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an.
	
	Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um die Pipeline **ADFTutorialPipeline** bereitzustellen. Vergewissern Sie sich, dass die Meldung **PIPELINE ERFOLGREICH ERSTELLT** angezeigt wird.
5. Schließen Sie jetzt das Blatt **Editor**, indem Sie auf **X** klicken. Klicken Sie erneut auf **X**, um das Blatt "ADFTutorialDataFactory" mit der Symbolleiste und Strukturansicht zu schließen. Wenn die Meldung **Ihre nicht gespeicherten Änderungen werden verworfen** angezeigt wird, klicken Sie auf **OK**.
6. Sie sollten sich nun wieder auf dem Blatt **DATA FACTORY** für **ADFTutorialDataFactory** befinden.

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.
 
### Anzeigen einer Diagrammansicht der Data Factory 
1. Klicken Sie auf dem Blatt **DATA FACTORY** auf **Diagramm**.

	![Blatt "Data Factory-Blade" – Kachel "Diagramm"][image-datafactoryblade-diagramtile]

2. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden:

	![Diagrammansicht][image-data-factory-get-started-diagram-blade]

	Sie können die Ansicht vergrößern, verkleinern, auf 100 % anpassen, an die Fenstergröße anpassen, Pipelines und Tabellen automatisch positionieren und Informationen zur Datenherkunft anzeigen (d. h. vor- und nachgelagerte Elemente ausgewählter Elemente hervorheben). Sie können auf ein Objekt (in der Ein-/Ausgabetabelle oder Pipeline) doppelklicken, um seine Eigenschaften anzuzeigen. 
3. Klicken Sie in der Diagrammansicht mit der rechten Maustaste auf **ADFTutorialPipeline**, und klicken Sie dann auf**Pipeline öffnen**. Die Aktivitäten in der Pipeline sowie Ein- und Ausgabedatasets für die Aktivitäten sollten angezeigt werden. In diesem Lernprogramm gibt es nur eine Aktivität in der Pipeline (Kopieraktivität) mit "EmpTableBlob" als Eingabedataset und "EmpSQLTable" als Ausgabedataset.   

	![Pipeline öffnen](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Klicken Sie auf der Breadcrumb-Leiste links oben auf **Data Factory**, um zur Diagrammansicht zurückzukehren. In der Diagrammansicht werden alle Pipelines angezeigt. Bei diesem Beispiel haben Sie nur eine Pipeline erstellt.
 

## <a name="MonitorDataSetsAndPipeline"></a>Schritt 5: Überwachen der Datasets und Pipeline
In diesem Schritt verwenden Sie das klassische Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Einzelheiten zur Verwendung von Cmdlets für die Überwachung finden Sie unter [Überwachen und Verwalten von Azure Data Factory mit PowerShell-Cmdlets][monitor-manage-using-powershell].

1. Navigieren Sie zum [klassischen Azure-Portal (Vorschau)][azure-portal], sofern noch nicht geöffnet. 
2. Wenn das Blatt für **ADFTutorialDataFactory** nicht geöffnet ist, klicken Sie zum Öffnen im **Startmenü** auf **ADFTutorialDataFactory**. 
3. In diesem Fenster sollten die Anzahl und Namen der erstellten Tabellen und der Pipeline angezeigt werden.

	![Startseite mit Namen][image-data-factory-get-started-home-page-pipeline-tables]

4. Klicken Sie nun auf die Kachel **Datasets**.
5. Klicken Sie auf dem Blatt **Datasets** auf **EmpTableFromBlob**. Dies ist die Eingabetabelle für **ADFTutorialPipeline**.

	![Datasets mit ausgewähltem "EmpTableFromBlob"][image-data-factory-get-started-datasets-emptable-selected]   
5. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen, da die Datei **emp.txt** ständig im Blobcontainer **adftutorial\\input** vorhanden ist. Überprüfen Sie, ob unten im Abschnitt **Letzte fehlerhafte Slices** keine Slices angezeigt werden.

	Die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** werden anhand der **UHRZEIT DER LETZTEN AKTUALISIERUNG** sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.
    

	-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set-AzureRmDataFactorySliceStatus**, oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
	-  Der Status des Slices ändert sich aufgrund einer Ausführung (z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.).
 
	Klicken Sie auf den Titel der Listen oder auf **...** (Auslassungspunkte), um eine umfangreichere Liste mit Slices anzuzeigen. Klicken Sie auf der Symbolleiste auf **Filter**, um die Slices zu filtern.
	
	Zum Anzeigen der nach Start-/Endzeit sortierten Datenslices klicken Sie auf die Kachel **Datenslices (nach Slicezeit)**.

	![Datenslices nach Slicezeit][DataSlicesBySliceTime]

6. Klicken Sie nun auf dem Blatt **Datasets** auf **EmpSQLTable**. Dies ist die Ausgabetabelle für **ADFTutorialPipeline**.

	![Blatt "Datasets"][image-data-factory-get-started-datasets-blade]



	 
6. Das Blatt **EmpSQLTable** sollte wie unten dargestellt angezeigt werden:

	![Blatt "Tabelle"][image-data-factory-get-started-table-blade]
 
7. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Im Abschnitt **Problemslices** am unteren Rand werden keine Slices angezeigt.
8. Klicken Sie auf **… (Auslassungspunkte)**, um alle Slices anzuzeigen.

	![Blatt "Datenslices"][image-data-factory-get-started-dataslices-blade]

9. Klicken Sie auf einen beliebigen Datenslice in der Liste. Das Blatt **DATENSLICE** sollte angezeigt werden.

	![Blatt "Datenslice"][image-data-factory-get-started-dataslice-blade]
  
	Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slices blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**.

11. Auf dem Blatt **DATENSLICE** sollten in der Liste im unteren Bereich alle Aktivitätsausführungen angezeigt werden. Klicken Sie auf eine **Aktivitätsausführung**, um das Blatt **DETAILS ZUR AKTIVITÄTSAUSFÜHRUNG** anzuzeigen.

	![Aktivitätsausführung – Details][image-data-factory-get-started-activity-run-details]

	
12. Klicken Sie auf **X**, um alle Blätter zu schließen, bis Sie such wieder im Startfenster für **ADFTutorialDataFactory** befinden.
14. (Optional) Klicken Sie auf der Startseite von **ADFTutorialDataFactory** auf **Pipelines**, dann auf dem Blatt **Pipelines** auf **ADFTutorialPipeline**, und führen Sie eine Detailsuche in den Eingabetabellen (**Consumed**) oder Ausgabetabellen (**Produced**) aus.
15. Starten Sie **SQL Server Management Studio**, stellen Sie eine Verbindung mit der Azure SQL-Datenbank her, und überprüfen Sie, ob die Zeilen in die Tabelle **emp** der Datenbank eingefügt wurden.

	![SQL-Abfrageergebnisse][image-data-factory-get-started-sql-query-results]


## Zusammenfassung 
In diesem Lernprogramm haben Sie eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbank zu kopieren. Sie haben mithilfe des Azure-Portals die Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt. Nachfolgend sind die allgemeinen Schritte aufgeführt, die Sie in diesem Lernprogramm ausgeführt haben:

1.	Erstellen einer Azure **Data Factory**.
2.	Erstellen von **verknüpften Diensten**, die Datenspeicher und Computes (sog. **verknüpfte Dienste**) mit der Data Factory verknüpfen.
3.	Erstellen von **Tabellen**, die Eingabe- und Ausgabedaten für Pipelines beschreiben.
4.	Erstellen von **Pipelines**. Eine Pipeline besteht aus einer oder mehreren Aktivitäten, verarbeitet die Eingaben und erzeugt Ausgaben. Sie legen den aktiven Zeitraum für die Pipeline durch Angabe der **Start**- und **Endzeit** für die Pipeline fest. Der aktive Zeitraums definiert die Dauer, für die Datenslices erzeugt werden. 


Eine Liste unterstützten Aktivitäten finden Sie im Thema [Pipelines und Aktivitäten][msdn-activities]. Eine Liste unterstützter verknüpfter Dienste finden Sie im Thema [Verknüpfte Dienste][msdn-linkedservices] in der MSDN Library.
 
Informationen zum Ausführen dieses Lernprogramms mit Azure PowerShell finden Sie unter [Erstellen und Überwachen einer Data Factory mit Azure PowerShell][monitor-manage-using-powershell].


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


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


[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0204_2016-->