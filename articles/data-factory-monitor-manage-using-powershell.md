<properties 
	pageTitle="Lernprogramm: Erstellen und Überwachen einer Azure Data-Factory mit Azure PowerShell" 
	description="Informationen Sie zum Verwenden von Azure PowerShell erstellen und Überwachen von Azure Data-Factorys." 
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
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# Lernprogramm: Erstellen Sie und überwachen Sie eine Daten-Factory mit Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

Die [Erste Schritte mit Azure Data Factory][adf-get-started] Lernprogramm erfahren Sie, wie erstellen und Überwachen einer Azure Data Factory mithilfe der [Azure Preview Portal][azure-preview-portal]. In diesem Lernprogramm werden Sie erstellen und Überwachen eine Azure Data-Factory mithilfe von Azure PowerShell-Cmdlets. Die Pipeline in der Data-Factory, die Sie in diesem Lernprogramm erstellen kopiert Daten aus einer Azure-Blob an einer Azure SQL-Datenbank.

> [AZURE.NOTE]Alle Daten Factory-Cmdlets in diesem Artikel nicht behandelt. Finden Sie unter [Daten Factory-Cmdlet-Referenz][cmdlet-reference] für umfassende Dokumentation zu "Data Factory"-Cmdlets.

##Voraussetzungen
Abgesehen von den Voraussetzungen, die im Lernprogramm Übersichtsthema aufgeführt werden müssen Sie Azure PowerShell auf Ihrem Computer installiert haben. Wenn Sie es nicht noch, herunterladen und installieren [Azure PowerShell][download-azure-powershell] auf Ihrem Computer.

##Inhalt dieses Lernprogramms
In der folgende Tabelle sind die Schritte, die Sie, als Teil des Lernprogramms und ihre Beschreibungen ausführen müssen aufgeführt.

Schritt | Beschreibung
-----| -----------
[Schritt 1: Erstellen einer Azure Data-Factory](#CreateDataFactory) | In diesem Schritt erstellen Sie eine Azure Data-Factory, die mit dem Namen **ADFTutorialDataFactoryPSH**. 
[Schritt 2: Erstellen von verknüpften Dienste](#CreateLinkedServices) | In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **AzureSqlLinkedService**. Der StorageLinkedService verbindet einen Azure-Speicher und AzureSqlLinkedService eine Azure SQL-Datenbank mit der ADFTutorialDataFactoryPSH verknüpft.
[Schritt 3: Erstellen von Eingabe und Ausgabe von datasets](#CreateInputAndOutputDataSets) | In diesem Schritt definieren Sie zwei Datasets (** EmpTableFromBlob ** und **EmpSQLTable**), dienen als Eingabe- und Tabellen für die **Kopie Aktivität** in den ADFTutorialPipeline, den Sie im nächsten Schritt erstellen.
[Schritt 4: Erstellen und Ausführen einer pipeline](#CreateAndRunAPipeline) | In diesem Schritt erstellen Sie eine Pipeline, die mit dem Namen **ADFTutorialPipeline** in der Factory Daten: **ADFTutorialDataFactoryPSH**... Die Pipeline haben eine **Kopie Aktivität** kopiert, die Daten aus einer Azure-Blob, das eine Ausgabetabelle der Azure-Datenbank.
[Schritt 5: Überwachen von Datasets und pipeline](#MonitorDataSetsAndPipeline) | In diesem Schritt werden Sie die Datasets und die Pipeline mit Azure PowerShell in diesem Schritt überwachen.

## <a name="CreateDataFactory"></a>Schritt 1: Erstellen einer Azure Data-Factory
In diesem Schritt verwenden Sie Azure PowerShell zum Erstellen einer Azure Data-Factory, die mit dem Namen **ADFTutorialDataFactoryPSH**.

1. Starten Sie **Azure PowerShell** und führen Sie die folgenden Befehle aus. Lassen Sie die Azure PowerShell bis zum Ende dieses Lernprogramms geöffnet. Wenn Sie zu schließen und erneut öffnen, müssen Sie diese Befehle erneut ausführen.
	- Führen Sie **Add-AzureAccount** und geben Sie den Benutzernamen und das Kennwort, das Sie verwenden, um das Azure Preview Portal anmelden.  
	- Führen Sie **Get-AzureSubscription** alle Abonnements für dieses Konto anzeigen.
	- Führen Sie **Select-AzureSubscription** das Abonnement aus, die Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Vorschauportal verwendet haben. 
2. Wechseln Sie zu **AzureResourceManager** Modus als "Data Factory" Azure-Cmdlets sind in diesem Modus verfügbar.

		Switch-AzureMode AzureResourceManager 
3. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen: **ADFTutorialResourceGroup** durch den folgenden Befehl ausführen.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Einige der Schritte in diesem Lernprogramm wird davon ausgegangen, dass Sie die Ressourcengruppe mit dem Namen verwenden **ADFTutorialResourceGroup**. Bei Verwendung eine anderen Ressourcengruppe müssen Sie anstelle von ADFTutorialResourceGroup in diesem Lernprogramm verwenden. 
4. Führen Sie die **neu AzureDataFactory** -Cmdlet zum Erstellen einer Daten-Factory, die mit dem Namen: **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]Der Name der Azure Data Factory muss global eindeutig sein. Wenn Sie die Fehlermeldung: **Daten Factory Name "ADFTutorialDataFactoryPSH" ist nicht verfügbar**, ändern Sie den Namen (z. B. YournameADFTutorialDataFactoryPSH). Verwenden Sie diesen Namen anstelle von ADFTutorialFactoryPSH beim Ausführen der Schritte in diesem Lernprogramm.

## <a name="CreateLinkedServices"></a>Schritt 2: Erstellen von verknüpften Dienste
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, Azure SQL-Datenbank oder einer lokalen SQL Server-Datenbank, die Eingabedaten enthält oder die Ausgabedaten für eine Pipeline Data Factory speichert. Ein Computingdienst ist der Dienst, der Eingabedaten verarbeitet und die ausgegebenen Daten erzeugt.

In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **AzureSqlLinkedService**. StorageLinkedService verknüpft den Dienst Links Azure-Speicherkonto und AzureSqlLinkedService eine Azure SQL-Datenbank mit der Factory Daten verknüpft: **ADFTutorialDataFactoryPSH**. Erstellen Sie eine Pipeline weiter unten in diesem Lernprogramm, die Daten aus einem Blob-Container in StorageLinkedService in einer SQL-Tabelle in AzureSqlLinkedService kopiert.

### Erstellen eines verknüpften Diensts für ein Azure-Speicherkonto
1.	Erstellen Sie eine JSON-Datei mit dem Namen **StorageLinkedService.json** in den **C:\ADFGetStartedPSH** mit folgendem Inhalt. Erstellen Sie den Ordner ADFGetStartedPSH, wenn sie nicht bereits vorhanden ist.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	In der **Azure PowerShell**, wechseln Sie zu den **ADFGetStartedPSH** Ordner. 
3.	Können Sie die **neu AzureDataFactoryLinkedService** -Cmdlet zum Erstellen eines verknüpften Diensts. Dieses Cmdlet und andere Daten Factory-Cmdlets, die Sie in diesem Lernprogramm verwenden, müssen Sie Werte übergeben, für die **ResourceGroupName** und **DataFactoryName** Parameter. Sie können auch **Get-AzureDataFactory** rufen Sie ein DataFactory-Objekt, und übergeben das Objekt ohne Eingabe von ResourceGroupName und DataFactoryName jedes Mal ein Cmdlet ausführen. Führen Sie folgenden Befehl die Ausgabe der Zuweisen der **Get-AzureDataFactory** Cmdlet, um eine Variable: **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	Führen Sie nun die **neu AzureDataFactoryLinkedService** -Cmdlet zum Erstellen des Dienstes verknüpften: **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Hadn't Ausführen der **Get-AzureDataFactory** Cmdlet und die Ausgabe an **$df** Variable, müssten Sie folgende Werte für die Parameter ResourceGroupName und DataFactoryName angeben.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Wenn Sie die Azure PowerShell in der Mitte des Lernprogramms schließen, werden Sie ausgeführt haben das Cmdlet "Get-AzureDataFactory" beim nächsten Starten von Azure PowerShell, um das Lernprogramm abgeschlossen.

### Erstellen eines verknüpften Diensts für eine Azure SQL-Datenbank
1.	Erstellen Sie eine JSON-Datei mit dem Namen AzureSqlLinkedService.json mit folgendem Inhalt.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	Führen Sie den folgenden Befehl zum Erstellen eines verknüpften Diensts. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]Überprüfen Sie, ob die **Zulassen des Zugriffs auf Azure Services** Einstellung für Ihren Azure SQL-Server aktiviert ist. Um zu überprüfen, und schalten Sie ihn aus, führen Sie folgende Schritte aus:
	>
	> <ol>
<li>Klicken Sie auf <b>Durchsuchen</b> Hub auf der linken Seite und auf <b>SQL Server</b>.</li>
<li>Wählen Sie Ihren Server, und klicken Sie auf <b>Einstellungen</b> auf der <b>SQL SERVER</b> Blade.</li>
<li>In der <b>Einstellungen</b> Blade, klicken Sie auf <b>Firewall</b>.</li>
<li>In der <b>Firewalll-Einstellungen</b> Blade, klicken Sie auf <b>auf</b> für <b>ermöglichen den Zugriff auf Azure Services</b>.</li>
<li>Klicken Sie auf <b>ACTIVE</b> Hub auf der linken Seite, wechseln zu der <b>Data Factory</b> Blade Sie waren.</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>Schritt 3: Erstellen von Eingabe- und Ausgabe von Tabellen

Im vorherigen Schritt erstellten Sie verknüpfte Dienste **StorageLinkedService** und **AzureSqlLinkedService** eine Azure Storage-Konto und Azure SQL-Datenbank mit der Factory Daten zu verknüpfen: **ADFTutorialDataFactoryPSH**. In diesem Schritt erstellen Sie Tabellen, die die Eingabe darstellen und Ausgabedaten für die Aktivität der Kopie in der Pipeline, die Sie im nächsten Schritt erstellen werden.

Eine Tabelle ist ein rechteckiges Dataset und die einzige Art von Dataset, die zu diesem Zeitpunkt unterstützt wird. In diesem Lernprogramm die Eingabetabelle verweist auf einen Blob-Container in der Azure-Speicher, StorageLinkedService verweist auf und Ausgabetabelle verweist auf eine SQL-Tabelle in der Azure SQL-Datenbank, das auf AzureSqlLinkedService verweist.

### Vorbereiten von Azure Blob-Speicher und Azure SQL-Datenbank für das Lernprogramm
Überspringen Sie diesen Schritt, wenn Sie das Lernprogramm unter durchgegangen sind [Erste Schritte mit Azure Data Factory][adf-get-started] Artikel.

Sie müssen die folgenden Schritte zur Vorbereitung der Azure-Blob-Speicher und Azure SQL-Datenbank für dieses Lernprogramm ausführen.
 
* Erstellen Sie einen Blob-Container mit dem Namen **Adftutorial** in Azure blob-Speicher, **StorageLinkedService** verweist. 
* Erstellen und Hochladen eine Textdatei **emp.txt**, wie ein Blob, das **Adftutorial** Container. 
* Erstellen Sie eine Tabelle mit dem Namen **emp** in Azure SQL-Datenbank in SQL Azure-Datenbank, die **AzureSqlLinkedService** verweist.


1. Starten Sie den Editor, fügen Sie den folgenden Text, und speichern Sie sie als **emp.txt**  **C:\ADFGetStartedPSH** Ordner auf Ihrer Festplatte. 

        John, Doe
		Jane, Doe
				
2. Verwenden Sie Tools wie z. B. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) zum Erstellen der **Adftutorial** Container und zum Hochladen der **emp.txt** Datei auf den Container.

    ![Azure-Speicher-Explorer][image-data-factory-get-started-storage-explorer]
3. Verwenden Sie das folgende SQL-Skript zum Erstellen der **emp** Tabelle in Azure SQL-Datenbank.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Wenn Sie SQL Server 2014 auf Ihrem Computer installiert haben: befolgen Sie die Anweisungen aus [Schritt2: Herstellen einer Verbindung mit SQL-Datenbank Verwalten von Azure SQL-Datenbank mit SQL Server Management Studio][sql-management-studio] Artikel Ihre Azure SQL-Server herstellen, und führen Sie das SQL-Skript.

	Wenn Sie Visual Studio 2013 auf Ihrem Computer installiert haben: im Azure-Vorschauportal ([http://portal.azure.com](http://portal.sazure.com)), klicken Sie auf **Durchsuchen** -Hub auf Links auf **SQL Server**, wählen Sie die Datenbank, und klicken Sie auf **in Visual Studio geöffnet** Schaltfläche auf der Symbolleiste der Azure SQL-Server herstellen, und führen Sie das Skript. Wenn der Client Zugriff auf den Azure SQL-Server nicht zulässig ist, müssen Sie die Firewall für den Azure SQL-Server ermöglicht den Zugriff auf Ihren Computer (IP-Adresse) konfigurieren. Finden Sie im Artikel über die Schritte zum Konfigurieren der Firewall für Ihren Azure SQL-Server aus.
		
### Erstellen der Eingabetabelle 
Eine Tabelle ist ein rechteckiges Dataset und verfügt über ein Schema. In diesem Schritt erstellen Sie eine Tabelle namens **EmpBlobTable** verweist auf einen Blob-Container in der Azure-Speicher, dargestellt durch die **StorageLinkedService** Dienst verknüpft. Dieses Blob-Containers (** Adftutorial **) enthält die Eingabedaten in der Datei: **emp.txt**.

1.	Erstellen Sie eine JSON-Datei mit dem Namen **EmpBlobTable.json** in den **C:\ADFGetStartedPSH** Ordner mit folgendem Inhalt:

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
	                "waitonexternal": {}
	                }
	        }
		}
	
	Beachten Sie Folgendes:
	
	- Speicherort **Typ** minFreeThreads auf **AzureBlobLocation**.
	- **LinkedServiceName** minFreeThreads auf **StorageLinkedService**. 
	- **FolderPath** festgelegt ist, um die **Adftutorial** Container. Sie können auch den Namen eines Blobs innerhalb des Ordners angeben. Da Sie nicht den Namen des Blobs angeben, werden Daten aus allen Blobs im Container als Eingabedaten betrachtet.  
	- Format **Typ** minFreeThreads auf **TextFormat**
	- Es gibt zwei Felder in der Textdatei – **FirstName** und **LastName** – durch ein Komma getrennt (** ColumnDelimiter **)	
	- Die **Verfügbarkeit** festgelegt ist, um **stündlich** (** Häufigkeit ** festgelegt ist **Stunde** und **Intervall** festgelegt ist, um **1** ), so dass der Dienst Daten Factory Eingabedaten pro Stunde im Stammordner im Blob-Container (** Adftutorial **) angegeben.

	Wenn Sie kein **Dateiname** für eine **input** **Tabelle**, alle Dateien/Blobs aus der Eingabeordner (** FolderPath **) gelten als Eingaben. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Finden Sie die Beispieldateien aus dem [Lernprogramm][adf-tutorial] Beispiele.
 
	Wenn Sie nicht angeben einer **Dateiname** für eine **Ausgabetabelle**, die generierte Dateien in der **FolderPath** werden im folgenden Format benannt: Daten. < Guid > .txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

2.	Führen Sie den folgenden Befehl zum Erstellen der Tabelle Daten-Factory.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Erstellen der Ausgabetabelle
In diesem Teil des Schritts, erstellen Sie eine Ausgabetabelle mit dem Namen **EmpSQLTable** die auf einer SQL-Tabelle verweist (** emp **) in der Azure SQL-Datenbank, die durch dargestellt ist die **AzureSqlLinkedService** Dienst verknüpft. Die Pipeline kopiert Daten aus der Eingabe-Blob, das die **emp** Tabelle.

1.	Erstellen Sie eine JSON-Datei mit dem Namen **EmpSQLTable.json** in den **C:\ADFGetStartedPSH** Ordner mit folgendem Inhalt.
		
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
		            "type": "AzureSQLTableLocation",
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
	* **LinkedServiceName** minFreeThreads auf **AzureSqlLinkedService**.
	* **Tablename** minFreeThreads auf **emp**.
	* Befinden sich drei Spalten – **ID**, **FirstName**, und **LastName** – in der Tabelle emp in der Datenbank-ID ist eine Identitätsspalte, daher Sie nur angeben müssen, aber **FirstName** und **LastName** hier.
	* Die **Verfügbarkeit** minFreeThreads auf **stündlich** (** Häufigkeit ** festgelegt **Stunde** und **Intervall** festgelegt **1**). Die Service Factory von Daten generiert eine Ausgabe Datenslice pro Stunde in die **emp** Tabelle in der Azure SQL-Datenbank.

2.	Führen Sie den folgenden Befehl zum Erstellen der Tabelle Daten-Factory.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Schritt 4: Erstellen und Ausführen einer pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopie Aktivität** verwendet, die **EmpTableFromBlob** als Eingabe und **EmpSQLTable** als Ausgabe.

1.	Erstellen Sie eine JSON-Datei mit dem Namen **ADFTutorialPipeline.json** in den **C:\ADFGetStartedPSH** Ordner mit folgendem Inhalt: 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
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
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	Beachten Sie Folgendes:

	- Im Bereich Aktivitäten ist es nur eine Aktivität, deren **Typ** minFreeThreads auf **CopyActivity**.
	- Geben Sie für die Aktivität, dass festgelegt ist **EmpTableFromBlob** und Ausgabe für die Aktivität, dass festgelegt ist **EmpSQLTable**.
	- In der **Transformation** Abschnitt **BlobSource** als Quelltyp angegeben ist und **SqlSink** als Senkentyp angegeben ist.

	> [AZURE.NOTE]Ersetzen Sie den Wert der **start** Eigenschaft mit dem aktuellen Tag und **End** Wert mit am nächsten Tag. Sowohl start und Ende Uhrzeitangaben müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601). Zum Beispiel: 2014-10-14T16:32:41Z. Die **End** Zeit ist optional, aber wir werden es in diesem Lernprogramm verwenden. Wenn Sie keinen Wert zum Angeben der **End** -Eigenschaft wird berechnet als "** Start + 48 Stunden **". Geben Sie zum Ausführen der Pipeline auf unbestimmte Zeit **9, 9/9999** als Wert für die **End** Eigenschaft. Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
	> [JSON-Scripting-Referenz](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	Führen Sie den folgenden Befehl zum Erstellen der Tabelle Daten-Factory. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.

## <a name="MonitorDataSetsAndPipeline"></a>Schritt 5: Überwachen der Datasets und die pipeline
In diesem Schritt verwenden Sie Azure PowerShell zur Überwachung der Aktivitäten in einem Azure Data-Factory.

1.	Führen Sie **Get-AzureDataFactory** und die Ausgabe einer Variablen $df zuzuweisen.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Führen Sie **Get-AzureDataFactorySlice** Details über alle Segmente der der **EmpSQLTable**, ist der Ausgabetabelle der Pipeline.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]Ersetzen Sie Jahr, Monat und Datumsteil der **StartDateTime** Parameter mit dem aktuellen Jahr, Monat und Datum. Dieser sollte übereinstimmen, die **Starten** Wert in der Pipeline JSON.

	24 Segmente, eines für jede Stunde von 12: 00 Uhr des aktuellen Tag um 12: 00 Uhr des nächsten Tages sollte angezeigt werden.
	
	**Erste Segment:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Letzte Schicht:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Führen Sie **Get-AzureDataFactoryRun** die Details der abzurufenden Aktivität ausgeführt wird, für eine **bestimmten** Slice. Ändern Sie den Wert von der **StartDateTime** Parameter entsprechend der **Start** Ausführungszeit des Segments aus der obigen Ausgabe. Der Wert der **StartDateTime** muss [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601). Zum Beispiel: 2014-03-03T22:00:00Z.

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

> [AZURE.NOTE]Finden Sie unter [Daten Factory-Cmdlet-Referenz][cmdlet-reference] für umfassende Dokumentation zu "Data Factory"-Cmdlets.

## Nächste Schritte

Artikel | Beschreibung
------ | ---------------
[Kopieren von Daten mit Azure Data Factory - Copy-Aktivität][copy-activity] | Dieser Artikel enthält eine ausführliche Beschreibung der **Kopie Aktivität** in diesem Lernprogramm verwendet. 
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise beim Kopieren von Daten aus einer **lokale SQL Server-Datenbank** in Azure-Blob. 
[Verwenden von Pig und Hive Data Factory][use-pig-and-hive-with-data-factory] | Dieser Artikel hat eine exemplarische Vorgehensweise, die zeigt, wie Sie **HDInsight-Aktivität** zum Ausführen einer **Hive/Pig** Skript zum Verarbeiten von Eingabedaten in Ausgabedaten zu erzeugen.
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien, die mit Daten-Factory][adf-tutorial] | Dieser Artikel bietet eine **End-to-End-Exemplarische Vorgehensweise** veranschaulicht, wie die Implementierung einer **realen Szenario** mithilfe von Azure Data Factory zum Transformieren von Daten aus den Protokolldateien Einblicke in.
[Verwenden von benutzerdefinierten Aktivitäten in einer Factory Daten][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit einer schrittweisen Anleitung zum Erstellen einer **benutzerdefinierte Aktivität** und es in eine Pipeline verwendet wird. 
[Problembehandlung bei Data Factory][troubleshoot] | Dieser Artikel beschreibt, wie **Problembehandlung bei** Azure Data Factory ausgibt. Sie können die exemplarische Vorgehensweise in diesem Artikel mit "ADFTutorialDataFactory" ausprobieren, indem Sie einen Fehler einbauen (die Tabelle in der Azure SQL-Datenbank löschen). 
[Azure Data Factory-Cmdlet-Referenz][cmdlet-reference] | Dieser Verweis Inhalt enthält Details über alle die **Data Factory Cmdlets**.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Entwicklerreferenz hat die umfassendes Referenzmaterial für die Cmdlets, JSON-Skripts, Funktionen usw.... 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: powershell-install-configure.md
[data-factory-create-sql-database]: sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir-->