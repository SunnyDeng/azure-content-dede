<properties 
	pageTitle="Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe der Azure PowerShell" 
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline mit Kopieraktivität mithilfe der Azure PowerShell." 
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
	ms.topic="get-started-article" 
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe der Azure PowerShell
> [AZURE.SELECTOR]
- [Übersicht über das Tutorial](data-factory-get-started.md)
- [Verwenden des Data Factory-Editors](data-factory-get-started-using-editor.md)
- [Verwenden von Visual Studio](data-factory-get-started-using-vs.md)
- [Mithilfe von PowerShell](data-factory-monitor-manage-using-powershell.md)


Im Lernprogramm [Erste Schritte mit Azure Data Factory][adf-get-started] erfahren Sie, wie Sie eine Azure Data Factory mithilfe des [Azure-Portals][azure-portal] erstellen und überwachen. In diesem Lernprogramm erstellen und überwachen Sie eine Azure Data Factory mithilfe von Azure PowerShell-Cmdlets. Die Pipeline in der Data Factory, die Sie in diesem Lernprogramm erstellen, verwendet eine Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in eine Azure SQL-Datenbank.

Die Kopieraktivität führt die Datenverschiebung in Azure Data Factory durch, und die Aktivität wird von einem global verfügbaren Dienst gestützt, mit dem Daten zwischen verschiedenen Datenspeichern auf sichere, zuverlässige und skalierbare Weise kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

> [AZURE.IMPORTANT] 
Lesen Sie sich den Artikel [Übersicht über das Tutorial](data-factory-get-started.md) durch, und führen Sie die vorbereitenden Schritte aus, bevor Sie mit diesem Tutorial beginnen.
>   
> In diesem Artikel werden nicht alle Data Factory-Cmdlets behandelt. In der [Data Factory-Cmdlet-Referenz](https://msdn.microsoft.com/library/dn820234.aspx) finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.
  

##Voraussetzungen
Neben den im Thema „Tutorial – Übersicht“ aufgeführten vorausgesetzten Komponenten müssen Sie Folgendes installieren:

- **Azure PowerShell**. Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) zum Installieren von Azure PowerShell auf Ihrem Computer.

Bei Verwendung von Azure PowerShell **Version < 1.0** müssen Sie die [hier][old-cmdlet-reference] dokumentierten Cmdlets verwenden. Sie müssen auch die folgenden Befehle ausführen, bevor Sie die Data Factory-Cmdlets verwenden:

1. Starten Sie Azure PowerShell, und führen Sie die folgenden Befehle aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
	1. Führen Sie **Add-AzureAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben.
	2. Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	3. Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
4. Wechseln Sie in den Modus „AzureResourceManager“, da die Azure Data Factory-Cmdlets in diesem Modus verfügbar sind: **Switch-AzureMode AzureResourceManager**.
  

##Dieses Lernprogramm umfasst folgende Punkte
In der folgenden Tabelle sind die in diesem Lernprogramm auszuführen Schritte und ihre Beschreibungen aufgeführt.

Schritt | Beschreibung
-----| -----------
[Schritt 1: Erstellen einer Azure Data Factory](#CreateDataFactory) | In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen **ADFTutorialDataFactoryPSH**. 
[Schritt 2: Erstellen von verknüpften Diensten](#CreateLinkedServices) | In diesem Schritt erstellen Sie die beiden verknüpften Dienste **StorageLinkedService** und **AzureSqlLinkedService**. Der Dienst StorageLinkedService verbindet einen Azure-Speicher und der Dienst AzureSqlLinkedService die Azure SQL-Datenbank mit "ADFTutorialDataFactoryPSH".
[Schritt 3: Erstellen von Eingabe- und Ausgabedatasets](#CreateInputAndOutputDataSets) | In diesem Schritt definieren Sie zwei Datasets (**EmpTableFromBlob** und **EmpSQLTable**), die als Ein- und Ausgabetabellen für die **Kopieraktivität** in der ADFTutorialPipeline (die im nächsten Schritt erstellt wird) verwendet werden.
[Schritt 4: Erstellen und Ausführen einer Pipeline](#CreateAndRunAPipeline) | In diesem Schritt erstellen Sie die Pipeline **ADFTutorialPipeline** in der Data Factory **ADFTutorialDataFactoryPSH**. Die Pipeline weist eine **Kopieraktivität** auf, die Daten aus einem Azure-Blob in eine Ausgabedatenbanktabelle in Azure kopiert.
[Schritt 5: Überwachen der Datasets und Pipeline](#MonitorDataSetsAndPipeline) | In diesem Schritt überwachen Sie die Datasets und die Pipeline mit Azure PowerShell.

## <a name="CreateDataFactory"></a>Schritt 1: Erstellen einer Azure Data Factory
In diesem Schritt erstellen Sie mit Azure PowerShell eine Azure Data Factory namens **ADFTutorialDataFactoryPSH**.

1. Starten Sie Azure PowerShell, und führen Sie den folgenden Befehl aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
	- Führen Sie **Login-AzureRmAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription <Name of the subscription>** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
3. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup**, indem Sie den folgenden Befehl ausführen.
   
		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens **ADFTutorialResourceGroup** verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie diese anstelle der Ressourcengruppe ADFTutorialResourceGroup verwenden. 
4. Führen Sie das Cmdlet **New-AzureRmDataFactory** zum Erstellen einer Data Factory mit dem Namen **ADFTutorialDataFactoryPSH** aus.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

	Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name "ADFTutorialDataFactoryPSH" nicht verfügbar** ändern Sie den Namen (z. B. in "IhrNameADFTutorialDataFactoryPSH"). Verwenden Sie diesen Namen beim Ausführen der Schritte in diesem Lernprogramm anstelle von "ADFTutorialDataFactoryPSH". Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.

	> [AZURE.NOTE] Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.

## <a name="CreateLinkedServices"></a>Schritt 2: Erstellen von verknüpften Diensten
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, eine Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank sein, die Eingabedaten enthält oder die Ausgabedaten für eine Data Factory-Pipeline speichert. Ein Serverdienst ist der Dienst, der Eingabedaten verarbeitet und Ausgabedaten erzeugt.

In diesem Schritt erstellen Sie die beiden verknüpften Dienste **StorageLinkedService** und **AzureSqlLinkedService**. Der verknüpfte Dienst "StorageLinkedService" verknüpft ein Azure-Speicherkonto und der Dienst "AzureSqlLinkedService" eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactoryPSH**. Später in diesem Lernprogramm erstellen Sie eine Pipeline, die Daten aus einem Blobcontainer in "StorageLinkedService" in eine SQL-Tabelle in "AzureSqlLinkedService" kopiert.

### Erstellen eines verknüpften Diensts für ein Azure-Speicherkonto
1.	Erstellen Sie in **C:\\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **StorageLinkedService.json** mit folgendem Inhalt. Erstellen Sie den Ordner "ADFGetStartedPSH", falls dieser noch nicht vorhanden ist.

			{
		  		"name": "StorageLinkedService",
		  		"properties": {
	    			"type": "AzureStorage",
		    		"typeProperties": {
		      			"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    		}
		  		}
			}

	Ersetzen Sie **accountname** durch den Namen Ihres Azure-Speicherkontos und **accountkey** durch den Schlüssel Ihres Azure-Speicherkontos.
2.	Wechseln Sie in **Azure PowerShell** zum Ordner **ADFGetStartedPSH**. 
3.	Verwenden Sie das Cmdlet **New-AzureRmDataFactoryLinkedService**, um einen verknüpften Dienst zu erstellen. Dieses Cmdlet und andere Data Factory-Cmdlets, die Sie in diesem Lernprogramm verwenden, erfordern das Eingeben von Werten für die Parameter **ResourceGroupName** und **DataFactoryName**. Sie können auch **Get-AzureRmDataFactory** verwenden, um ein DataFactory-Objekt abzurufen und das Objekt ohne Eingabe von „ResourceGroupName“ und „DataFactoryName“ bei jeder Ausführung eines Cmdlets zu übergeben. Führen Sie folgenden Befehl aus, um die Ausgabe des Cmdlets **Get-AzureRmDataFactory** der Variablen **$df** zuzuweisen. 

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	Führen Sie nun das Cmdlet **New-AzureRmDataFactoryLinkedService** zum Erstellen des verknüpften Diensts **StorageLinkedService** aus.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Wenn Sie das Cmdlet **Get-AzureRmDataFactory** noch nicht ausgeführt und die Ausgabe nicht der Variablen **$df** zugewiesen hätten, müssten Sie für die Parameter „ResourceGroupName“ und „DataFactoryName“ die folgenden Werte angeben.
		
		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Wenn Sie Azure PowerShell mitten im Lernprogramm schließen, müssen Sie das Cmdlet „Get-AzureRmDataFactory“ beim nächsten Start von Azure PowerShell ausführen, um das Lernprogramm abzuschließen.

### Erstellen eines verknüpften Diensts für eine Azure SQL-Datenbank
1.	Erstellen Sie die JSON-Datei "AzureSqlLinkedService.json" mit folgendem Inhalt.

			{
				"name": "AzureSqlLinkedService",
				"properties": {
					"type": "AzureSqlDatabase",
					"typeProperties": {
				      	"connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
					}
		  		}
			}

	Ersetzen Sie **servername**, **databasename**, ****username@servername** und **password** durch die Namen des Azure SQL-Servers und der Datenbank bzw. durch Benutzerkonto und Kennwort.

2.	Führen Sie den folgenden Befehl aus, um einen verknüpften Dienst zu erstellen.
	
		New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Prüfen Sie, ob die Einstellung **Zugriff auf Azure-Dienste zulassen** für Ihren Azure SQL-Server aktiviert ist. Führen Sie zum Prüfen und Aktivieren die folgenden Schritte aus:

	1. Klicken Sie links auf den Hub **DURCHSUCHEN** und dann auf **SQL-Server**.
	2. Wählen Sie Ihren Server aus, und klicken Sie auf dem Blatt "SQL SERVER" auf **EINSTELLUNGEN**.
	3. Klicken Sie auf dem Blatt **EINSTELLUNGEN** auf **Firewall**.
	4. Klicken Sie auf dem Blatt **Firewalleinstellungen** für **Zugriff auf Azure-Dienste erlauben** auf **EIN**.
	5. Klicken Sie links auf den Hub **AKTIV**, um zum Blatt **Data Factory** zu wechseln, auf dem Sie sich zuvor befunden haben.
	

## <a name="CreateInputAndOutputDataSets"></a>Schritt 3: Erstellen von Ein- und Ausgabetabellen

Im vorherigen Schritt haben Sie die verknüpften Dienste **StorageLinkedService** und **AzureSqlLinkedService** erstellt, um ein Azure-Speicherkonto und eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactoryPSH** zu verknüpfen. In diesem Schritt erstellen Sie Datasets mit den Eingabe- und Ausgabedaten für die Kopieraktivität in der Pipeline, die Sie im nächsten Schritt erstellen.

Eine Tabelle ist ein rechteckiges Dataset und die einzige Art von Dataset, die zu diesem Zeitpunkt unterstützt wird. Die Eingabetabelle in diesem Lernprogramm verweist auf den Blobcontainer im Azure-Speicher, auf den "StorageLinkedService" verweist. Die Ausgabetabelle verweist auf die SQL-Tabelle in der Azure SQL-Datenbank, auf die "AzureSqlLinkedService" verweist.

### Vorbereiten von Azure Blob-Speicher und Azure SQL-Datenbank für das Lernprogramm
Überspringen Sie diesen Schritt, wenn Sie das Lernprogramm im Artikel [Erste Schritte mit Azure Data Factory][adf-get-started] durchlaufen haben.

Führen Sie die folgenden Schritte zur Vorbereitung des Azure-Blobspeichers und der Azure SQL-Datenbank für dieses Lernprogramm aus.
 
* Erstellen Sie im Azure-Blobspeicher einen Blobcontainer namens **adftutorial**, auf den **StorageLinkedService** verweist. 
* Erstellen Sie die Textdatei **emp.txt**, und laden Sie diese als Blob in den Container **adftutorial** hoch. 
* Erstellen Sie eine Tabelle namens **emp** in der Azure SQL-Datenbank, auf die **AzureSqlLinkedService** verweist.


1. Öffnen Sie den Editor, fügen Sie den folgenden Text ein, und speichern Sie die Datei als **emp.txt** im Ordner **C:\\ADFGetStartedPSH** auf Ihrer Festplatte. 

        John, Doe
		Jane, Doe
				
2. Verwenden Sie Tools wie [Azure-Speicher-Explorer](https://azurestorageexplorer.codeplex.com/) zum Erstellen des Containers **adftutorial** und zum Hochladen der Datei **emp.txt** in den Container.

    ![Azure-Speicher-Explorer][image-data-factory-get-started-storage-explorer]
3. Verwenden Sie das folgende SQL-Skript, um die Tabelle **emp** in der Azure SQL-Datenbank zu erstellen.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Wenn Sie SQL Server 2014 auf Ihrem Computer installiert haben: Befolgen Sie die Anweisungen unter [Schritt 2: Herstellen einer Verbindung mit der SQL-Datenbank][sql-management-studio] im Artikel "Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio", um eine Verbindung mit Ihrem Azure SQL-Server herzustellen und das SQL-Skript auszuführen.

	Wenn Sie Visual Studio 2013 auf Ihrem Computer installiert haben: Klicken Sie im Azure-Portal ([http://portal.azure.com](http://portal.sazure.com)) links auf den Hub **Durchsuchen** und dann auf **SQL-Server**. Wählen Sie Ihre Datenbank aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **In Visual Studio öffnen**, um eine Verbindung mit Ihrem Azure SQL-Server herzustellen und das SQL-Skript auszuführen. Wenn Ihr Client nicht auf den Azure SQL-Server zugreifen darf, müssen Sie die Firewall für Ihren Azure SQL-Server so konfigurieren, dass der Zugriff über Ihren Computer (bzw. dessen IP-Adresse) ermöglicht wird. Im vorherigen Artikel finden Sie Schritte zum Konfigurieren der Firewall für Ihren Azure SQL-Server.
		
### Erstellen der Eingabetabelle 
Eine Tabelle ist ein rechteckiges Dataset und verfügt über ein Schema. In diesem Schritt erstellen Sie eine Tabelle namens **EmpBlobTable**, die auf einen Blobcontainer im Azure-Speicher verweist, der vom verknüpften Dienst **StorageLinkedService** dargestellt wird. Dieser Blob-Container (**adftutorial**) enthält die Eingabedaten in der Datei **emp.txt**.

1.	Erstellen Sie im Ordner **C:\\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **EmpBlobTable.json** mit folgendem Inhalt:

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
				  "fileName": "emp.txt",
			      "folderPath": "adftutorial/",
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
	- **linkedServiceName** ist auf **StorageLinkedService** festgelegt. 
	- **folderPath** ist auf den Container **adftutorial** festgelegt. 
	- **fileName** hat den Wert **emp.txt**. Wenn Sie nicht den Namen des Blobs angeben, werden Daten aus allen Blobs im Container als Eingabedaten betrachtet.  
	- **format: type** ist auf **TextFormat** festgelegt.
	- Die Textdatei enthält die beiden Felder **FirstName** und **LastName**, die durch ein Komma getrennt sind (**columnDelimiter**).	
	- Die Verfügbarkeit (**availability**) ist auf **hourly** festgelegt (**frequency** auf **hour** und **interval** auf **1**), sodass der Data Factory-Dienst den Stammordner im angegebenen Blob-Container (**adftutorial**) stündlich auf Eingabedaten untersucht.

	Wenn Sie keinen **fileName** für eine **Eingabe****tabelle** angeben, werden alle Dateien/Blobs aus dem Eingabeordner (**folderPath**) als Eingaben angesehen. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Beispiele finden Sie in den Beispieldateien des [Lernprogramms][adf-tutorial].
 
	Wenn Sie keinen **fileName** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

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

2.	Führen Sie den folgenden Befehl zum Erstellen des Data Factory-Datensatzes aus.

		New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Erstellen der Ausgabetabelle
In diesem Teil des Schritts erstellen Sie eine Ausgabetabelle namens **EmpSQLTable**, die auf eine SQL-Tabelle (**emp**) in der Azure SQL-Datenbank verweist, die durch den verknüpften Dienst **AzureSqlLinkedService** dargestellt wird. Die Pipeline kopiert Daten aus dem Eingabeblob in die Tabelle **emp**.

1.	Erstellen Sie im Ordner **C:\\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **EmpSQLTable.json** mit folgendem Inhalt:
		
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
	* **linkedServiceName** ist auf **AzureSqlLinkedService** festgelegt.
	* **tablename** ist auf **emp** festgelegt.
	* Die Tabelle "emp" der Datenbank enthält die drei Spalten **ID**, **FirstName** und **LastName**. "ID" ist jedoch eine Identitätsspalte, weshalb Sie hier nur **FirstName** und **LastName** angeben müssen.
	* Die Verfügbarkeit (**availability**) ist auf **hourly**, (**frequency** auf **hour** und **interval** auf **1**) festgelegt. Der Data Factory-Dienst generiert in der Tabelle **emp** in der Azure SQL-Datenbank stündlich einen Ausgabedatenslice.

2.	Führen Sie den folgenden Befehl zum Erstellen des Data Factory-Datensatzes aus.
	
		New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Schritt 4: Erstellen und Ausführen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, die **EmpTableFromBlob** als Eingabe und **EmpSQLTable** als Ausgabe verwendet.

1.	Erstellen Sie eine JSON-Datei mit dem Namen **ADFTutorialPipeline.json** im Ordner **C:\\ADFGetStartedPSH ** mit dem folgenden Inhalt: 
	
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
			            "type": "SqlSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2015-12-09T00:00:00Z",
			    "end": "2015-12-10T00:00:00Z",
			    "isPaused": false
			  }
			}

	Beachten Sie Folgendes:

	- Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **type** auf **Copy** festgelegt ist.
	- Die Eingabe für die Aktivität ist auf **EmpTableFromBlob** und die Ausgabe auf **EmpSQLTable** festgelegt.
	- Im Abschnitt **transformation** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben.

	Ersetzen Sie den Wert der Eigenschaft **start** durch den aktuellen Tag und den Wert der Eigenschaft **end** durch den nächsten Tag. Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Angabe für **end** ist optional, wird aber in diesem Lernprogramm verwendet.
	
	Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9/9/9999** an.
	
	Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](http://go.microsoft.com/fwlink/?LinkId=516971).
2.	Führen Sie den folgenden Befehl zum Erstellen der Data Factory-Tabelle aus. 
		
		New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.

## <a name="MonitorDataSetsAndPipeline"></a>Schritt 5: Überwachen der Datasets und Pipeline
In diesem Schritt verwenden Sie Azure PowerShell zur Überwachung der Aktivitäten in einer Azure Data Factory.

1.	Führen Sie **Get-AzureRmDataFactory** aus, und weisen Sie die Ausgabe der Variablen „$df“ zu.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Führen Sie **Get-AzureRmDataFactorySlice** aus, um Details zu allen Slices in der Tabelle **EmpSQLTable** zu erhalten. Dies ist die Ausgabetabelle der Pipeline.

		Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	Ersetzen Sie den Jahres-, Monats- und Datumsteil des Parameters **StartDateTime** durch aktuelle Werte für Jahr, Monat und Datum. Der Wert muss mit dem Wert **Start** im JSON-Code der Pipeline übereinstimmen.

	Es sollten 24 Slices angezeigt werden (einer für jede Stunde von 12:00 Uhr des aktuellen Tages bis 12:00 Uhr des nächsten Tages).
	
	**Erster Slice:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     :
		LongRetryCount    : 0

	**Letzter Slice:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Führen Sie **Get-AzureRmDataFactoryRun** aus, um die Details der Aktivitätsausführungen für einen **bestimmten** Slice abzurufen. Ändern Sie den Wert des Parameters **StartDateTime** entsprechend des Zeitwerts **Start** für den Slice der vorangegangenen Ausgabe. Der Wert von **StartDateTime** muss im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben sein. Beispiel: 2014-03-03T22:00:00Z.

		Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

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

In der [Data Factory-Cmdlet-Referenz][cmdlet-reference] finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.

## Siehe auch
Ausführliche Informationen zur **Kopieraktivität** in Azure Data Factory finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).


[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[adf-get-started]: data-factory-get-started.md
[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0302_2016-->