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
	ms.date="02/13/2015" 
	ms.author="spelluru"/>

# Erste Schritte mit Azure Data Factory
Dieser Artikel hilft Ihnen beim Einstieg in die Verwendung von Azure Data Factory. Das Lernprogramm in diesem Artikel zeigt, wie Sie eine Azure Data Factory erstellen und dann eine Pipeline in der Data Factory erstellen, um Beispieldaten aus einem Azure Blob-Speicher in eine Azure SQL-Datenbank zu kopieren.

Die folgende Liste enthält die Schritte, die Entwickler typischerweise ausführen müssen: 

1.	Erstellen einer **Azure Data Factory**.
2.	Erstellen von **verknüpften Diensten** zum Verknüpfen von Datenspeichern und Serverdiensten mit der Data Factory.  Ein verknüpfter Dienst kann z. B. eine Azure SQL-Datenbank oder einen HDInsight-Cluster mit der Data Factory verknüpfen.
3.	Erstellen von **Tabellen**, die Eingabe- und Ausgabedaten für Pipelines beschreiben. Tabellen geben auch den tatsächlichen Speicherort von Daten in den mit einer Data Factory verknüpften Datenspeichern an. Eine Tabelle kann z. B. den Namen der SQL-Tabelle in einer Azure SQL-Datenbank (oder) einen Blob-Container in einem Azure-Blob angeben. 
4.	Erstellen von **Pipelines**. Eine Pipeline besteht aus einer oder mehreren Aktivitäten, die Eingabedaten verbrauchen und Ausgabedaten erzeugen. Eine Kopieraktivität kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher, und eine HDInsight-Aktivität verarbeitet Eingabedaten unter Verwendung von Hive/Pig-Skripts.  
5.	Angeben des **aktiven Zeitraums** für Pipelines (zur Datenverarbeitung). Der aktive Zeitraum definiert die Dauer, für die Datenslices erzeugt werden. Sie können Startdatum/-uhrzeit und Enddatum/-uhrzeit für eine Pipeline angeben, (oder) können Sie permanent ausführen.

In diesem Lernprogramm lernen Sie Folgendes: 

1.	Verwenden des **Azure-Vorschauportals** zum Erstellen einer Azure Data Factory und verknüpfter Dienste für Datenspeicher.
2.	Verwenden von **Azure PowerShell** zum Erstellen von Tabellen und einer Pipeline. In dieser Version des Portals wird die Erstellung von Tabellen und Pipelines nicht unterstützt.


##Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen] [azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
- Sie müssen [Azure PowerShell][download-azure-powershell] auf Ihren Computer herunterladen und installieren.
- Lesen Sie das Thema [Einführung in Azure Data Factory][data-factory-introduction].
- Azure-Speicherkonto In diesem Lernprogramm verwenden Sie den Blob-Speicher als Quelldatenspeicher. Schritte zum Erstellen eines Azure-Speichers finden Sie unter [Informationen zu Speicherkonten][data-factory-create-storage].
- Azure SQL-Datenbank In diesem Lernprogramm erstellen Sie eine Beispieldatenbank und verwenden sie als Zieldatenspeicher. Schritte zum Erstellen einer Azure SQL-Datenbank finden Sie unter [Erstellen und Konfigurieren einer Azure SQL-Datenbank][data-factory-create-sql-database].

##Inhalt dieses Lernprogramms

Schritt | Beschreibung
-----| -----------
[Schritt 1: Erstellen einer Azure Data Factory](#CreateDataFactory) In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen **ADFTutorialDataFactory**. 
[Schritt 2: Erstellen von verknüpften Diensten](#CreateLinkedServices) | In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **MyBlobStore** und **MyAzureSQLStore**. "MyBlobStore" verknüpft einen Azure-Speicher und "MyAzureSQLStore" eine Azure SQL-Datenbank mit "ADFTutorialDataFactory".
[Schritt 3: Erstellen von Eingabe- und Ausgabedatasets](#CreateInputAndOutputDataSets) | In diesem Schritt definieren Sie zwei Datasets (**EmpTableFromBlob** und **EmpSQLTable**), die als Ein- und Ausgabe für die **Kopieraktivität** in "ADFTutorialPipeline" (wird im nächsten Schritt erstellt) verwendet werden.
[Schritt 4: Erstellen und Ausführen einer Pipeline](#CreateAndRunAPipeline) | In diesem Schritt erstellen Sie eine Pipeline namens **ADFTutorialPipeline**. Die Pipeline verfügt über eine **Kopieraktivität**, die Daten aus einem Azure-Blob in eine Azure-Ausgabe-Datenbanktabelle kopiert.
[Schritt 5: Überwachen der Datasets und der Pipeline](#MonitorDataSetsAndPipeline) | In diesem Schritt werden Sie die Datasets und die Pipeline mit Azure Management Studio überwachen.
 


## <a name="CreateDataFactory"></a>Schritt 1: Erstellen einer Azure Data Factory
In diesem Schritt erstellen Sie mit dem Azure-Vorschauportal eine Azure Data Factory namens **ADFTutorialDataFactory**.

1.	Nach der Anmeldung beim [Azure-Vorschauportal][azure-preview-portal] klicken Sie in der unteren linken Ecke auf **NEU**, und klicken Sie dann im Fenster **Neu** auf **Data Factory**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Wenn die **Data Factory** nicht im Fenster **Neu** angezeigt wird, führen Sie einen Bildlauf nach unten durch.  


6. Führen Sie im Fenster **Neue Data Factory** Folgendes aus:
	1. Geben Sie **ADFTutorialDataFactory** als **Namen** ein. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und führen Sie die folgenden Schritte aus:
		1. Klicken Sie auf **Neue Ressourcengruppe erstellen**.
		2. Geben Sie im Fenster **Ressourcengruppe erstellen** **ADFTutorialResourceGroup** für den **Namen** der Ressourcengruppe ein, und klicken Sie auf **OK**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens **ADFTutorialResourceGroup** verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie anstelle von "ADFTutorialResourceGroup" die Ressourcengruppe verwenden, die Sie hier auswählen.  
7. Im Fenster **Neue Data Factory** ist **Zum Startmenü hinzufügen** aktiviert.
8. Klicken Sie im Fenster **Neue Data Factory** auf **Erstellen**.

	> [WACOM.NOTE] Der Name der Azure Data Factory muss global eindeutig sein. Wenn Sie die folgende Fehlermeldung erhalten: **Data Factory-Name "ADFTutorialDataFactory" ist nicht verfügbar**, ändern Sie den Namen (z. B. "IhrNameADFTutorialDataFactory"). Verwenden Sie diesen Namen beim Ausführen der Schritte in diesem Lernprogramm anstelle von "ADFTutorialFactory".  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. Klicken Sie auf der linken Seite auf den Hub **BENACHRICHTIGUNGEN**, und suchen Sie nach Benachrichtigungen des Erstellungsprozesses.
10. Nach Abschluss der Erstellung wird das Fenster "Data Factory" wie unten dargestellt angezeigt:
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. Sie können das Fenster auch wie unten gezeigt über das **Startmenü** öffnen, indem Sie auf **ADFTutorialFactory** klicken. 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>Schritt 2: Erstellen von verknüpften Diensten
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, eine Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank sein.

In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **MyBlobStore** und **MyAzureSQLStore**. Der verknüpfte Dienst "MyBlobStore" verknüpft ein Azure-Speicherkonto und "MyAzureSQLStore" eine Azure SQL-Datenbank mit der **ADFTutorialDataFactory**. Später in diesem Lernprogramm erstellen Sie eine Pipeline, die Daten aus einem Blob-Container in "MyBlobStore" in eine SQL-Tabelle in "MyAzureSQLStore" kopiert.

### Erstellen eines verknüpften Diensts für ein Azure-Speicherkonto
1.	Klicken Sie im Fenster **DATA FACTORY** auf die Kachel **Verknüpfte Dienste**, um das Fenster **Verknüpfte Dienste** zu öffnen.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. Klicken Sie im Fenster **Verknüpfte Dienste** auf **Datenspeicher hinzufügen**.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. Gehen Sie im Fenster **Neuer Datenspeicher** folgendermaßen vor:  
	1. Geben Sie einen **Namen** für den Datenspeicher ein. Geben Sie im Rahmen des Lernprogramms **MyBlobStore** als **Name** ein.
	2. Geben Sie eine **Beschreibung** (optional) für den Datenspeicher ein.
	3. Klicken Sie auf **Typ**.
	4. Wählen Sie **Azure-Speicherkonto** aus, und klicken Sie auf **OK**.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  Jetzt sind Sie zurück im Fenster **Neuer Datenspeicher**, das folgendermaßen aussieht:

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Geben Sie den **Kontonamen** und **Kontoschlüssel** für Ihr Azure-Speicherkonto ein, und klicken Sie auf **OK**.   

6. Nachdem Sie im Fenster **Neuer Datenspeicher** auf **OK** geklickt haben, sollte **myblobstore** in der Liste **DATENSPEICHER** im Fenster **Verknüpfte Dienste** angezeigt werden. Überprüfen Sie den Hub **BENACHRICHTIGUNGEN** (auf der linken Seite) auf Nachrichten.

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Erstellen eines verknüpften Diensts für eine Azure SQL-Datenbank
1. Klicken Sie im Fenster **Verknüpfte Dienste** auf der Symbolleiste auf **Datenspeicher hinzufügen**, um eine weitere Datenquelle (Azure SQL-Datenbank) hinzuzufügen.
2. Gehen Sie im Fenster "Neuer Datenspeicher" folgendermaßen vor:
	1. Geben Sie einen **Namen** für den Datenspeicher ein. Geben Sie im Rahmen des Lernprogramms **MyAzureSQLStore** in das Feld **NAME** ein. 
	2. Geben Sie eine **BESCHREIBUNG** (optional) für den Datenspeicher ein.
	3. Klicken Sie auf **Typ**, und wählen Sie **Azure SQL-Datenbank** aus.
3. Geben Sie **Server**, **Datenbank**, **Benutzername** und **Kennwort** für die Azure SQL-Datenbank ein, und klicken Sie auf **OK**.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. Nachdem Sie im Fenster **Neuer Datenspeicher** auf **OK** geklickt haben, sollten beide Datenspeicher im Fenster **Verknüpfte Dienste** angezeigt werden.

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>Schritt 3: Erstellen von Eingabe- und Ausgabetabellen

Im vorherigen Schritt haben Sie die verknüpften Dienste **MyBlobStore** und **MyAzureSQLStore** erstellt, um ein Azure-Speicherkonto und eine Azure SQL-Datenbank mit der folgenden Data Factory zu verknüpfen: **ADFTutorialDataFactory**. In diesem Schritt erstellen Sie Tabellen, die die Eingabe- und Ausgabedaten für die Kopieraktivität in der Pipeline darstellen, die Sie im nächsten Schritt erstellen. 

Eine Tabelle ist ein rechteckiges Dataset und die einzige Art von Dataset, die zu diesem Zeitpunkt unterstützt wird. Die Eingabetabelle verweist auf einen Blob-Container im Azure-Speicher, auf den "MyBlobStore" verweist, und die Ausgabetabelle verweist auf eine SQL-Tabelle in der Azure SQL-Datenbank, auf die "MyAzureSQLStore" verweist.  
 
Das Erstellen von Datasets und Pipelines wird vom Azure-Vorschauportal derzeit nicht unterstützt, daher müssen Sie Azure PowerShell-Cmdlets verwenden, um Tabellen und Pipelines zu erstellen. Vor dem Erstellen von Tabellen müssen Sie zunächst die folgenden Schritte ausführen (detaillierte Schritte finden Sie im Anschluss an die Liste).

* Erstellen Sie einen Blob-Container namens **adftutorial** in dem Azure Blob-Speicher, auf den "MyBlobStore" verweist. 
* Erstellen Sie eine Textdatei, **emp.txt**, und laden Sie diese als Blob in den Container **Adftutorial** hoch. 
* Erstellen Sie eine Tabelle namens **emp** in der Azure SQL-Datenbank, auf die "MyAzureSQLStore" verweist.
* Erstellen Sie auf Ihrer Festplatte einen Ordner namens **ADFGetStarted**.  

### Vorbereiten von Azure Blob-Speicher und Azure SQL-Datenbank für das Lernprogramm
1. Öffnen Sie den Editor, fügen Sie den folgenden Text ein, und speichern Sie die Datei als **emp.txt** im Ordner **C:\ADFGetStarted** auf Ihrer Festplatte. 

        John, Doe
		Jane, Doe
				
2. Verwenden Sie Tools wie [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) zum Erstellen des Containers **Adftutorial** und zum Hochladen der Datei **emp.txt** in den Container.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Verwenden Sie das folgende SQL-Skript, um die Tabelle **emp** in der Azure SQL-Datenbank zu erstellen. Sie können die Azure SQL-Verwaltungskonsole zum Herstellen einer Verbindung mit einer Azure SQL-Datenbank und zum Ausführen des SQL-Skripts verwenden. Sie können auch SQL Server Management Studio verwenden, um diese Aufgabe durchzuführen. 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	Um die Azure SQL-Verwaltungskonsole zu starten, klicken Sie auf **VERWALTEN**, wie in der folgenden Abbildung gezeigt:
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### Erstellen der Eingabetabelle 
Eine Tabelle ist ein rechteckiges Dataset und verfügt über ein Schema. In diesem Schritt erstellen Sie eine Tabelle namens **EmpBlobTable**, die auf einen Blob-Container im Azure-Speicher verweist, der durch den verknüpften Dienst **MyBlobStore** dargestellt wird.


1. Erstellen Sie eine JSON-Datei für ein Data Factory-Tabelle, die Daten in der Datei "emp.txt" im Blob darstellt. Öffnen Sie den Editor, kopieren Sie das folgende JSON-Skript, und speichern Sie die Datei als "EmpBlobTable.json" im Ordner "C:\ADFGetStarted".


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
            		"linkedServiceName": "MyBlobStore"
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
	
	- **location type** ist auf **AzureBlobLocation** festgelegt.
	- **linkedServiceName** ist auf **MyBlobStore** festgelegt. Sie haben diesen verknüpften Dienst in Schritt 2 erstellt).
	- **folderPath** ist auf den Container **adftutorial** festgelegt. Sie können auch den Namen eines Blobs innerhalb des Ordners angeben. Da Sie nicht den Namen des Blobs angeben, werden Daten aus allen Blobs im Container als Eingabedaten betrachtet.  
	- **format type** ist auf **TextFormat** festgelegt.
	- - Die Textdatei enthält zwei Felder, **FirstName** und **ColumnName**, die durch ein Komma getrennt sind ("columDelimiter").	
	- Die Verfügbarkeit (**availability**) ist auf **hourly** festgelegt (**frequency** auf **hour** und **interval** auf **1**). Der Data Factory-Dienst sucht stündlich im Stammordner des angegebenen Blob-Containers (**adftutorial**) nach Eingabedaten.

	Wenn Sie keinen **Dateinamen** für eine **Eingabetabelle** angeben, werden alle Dateien/Blobs aus dem Eingabeordner (**folderPath**) als Eingaben angesehen. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Beispiele finden Sie in den Beispieldateien des [Lernprogramms][adf-tutorial].
 
	Wenn Sie keinen **Dateinamen** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt (Beispiel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die "partitionedBy"-Eigenschaft. Im folgenden Beispiel verwendet folderPath die Angaben für Jahr, Monat und Tag aus SliceStart (Startzeit des zu vearbeitenden Slices) und fileName die Angabe für Stunde aus SliceStart. Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt. 

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

2. Starten Sie **Azure PowerShell**, und führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager**-Modus verfügbar.

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- Führen Sie **Add-AzureAccount** aus, und geben Sie denselben Benutzernamen und dasselbe Kennwort ein, die Sie bei der Anmeldung beim Azure-Vorschauportal verwendet haben.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Vorschauportal verwendet haben.

3. Verwenden Sie das **New-AzureDataFactoryTable**-Cmdlet, um die Eingabetabelle mit der Datei **EmpBlobTable.json** zu erstellen.


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	Einzelheiten zu diesem und anderen Data Factory-Cmdlets finden Sie in der [Data Factory-Cmdlet-Referenz][cmdlet-reference].
 
### Erstellen der Ausgabetabelle
In diesem Teil des Schritts erstellen Sie eine Ausgabetabelle namens **EmpSQLTable**, die auf eine SQL-Tabelle in der Azure SQL-Datenbank verweist, die durch den verknüpften Dienst **MyAzureSQLStore** dargestellt wird. 

1. Erstellen Sie eine JSON-Datei für eine Data Factory-Tabelle, die Daten in der Azure SQL-Datenbank darstellt. Öffnen Sie den Editor, kopieren Sie das folgende JSON-Skript, und speichern Sie es als **EmpSQLTable.json** im Ordner **C:\ADFGetStarted**.



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
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Beachten Sie Folgendes: 
	
	* **location type** ist auf **AzureSQLTableLocation** festgelegt.
	* **linkedServiceName** ist auf **MyAzureSQLStore** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
	* **tablename** ist auf **emp** festgelegt.
	* Die Tabelle "emp" der Datenbank enthält drei Spalten - **ID**, **FirstName** und **LastName**, "ID" ist jedoch eine Identitätsspalte, daher müssen Sie hier nur **FirstName** und **LastName** angeben.
	* Die Verfügbarkeit (**availability**) ist auf **hourly** festgelegt ("frequency" auf "hour" und "interval" auf "1").  Der Data Factory-Dienst generiert jede Stunde einen Ausgabedatenslice in der Tabelle **emp** in der Azure SQL-Datenbank.


2. Führen Sie in **Azure PowerShell** den folgenden Befehl aus, um eine weitere Data Factory-Tabelle zu erstellen, die die Tabelle **emp** in der Azure SQL-Datenbank darstellt.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>Schritt 4: Erstellen und Ausführen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, die **EmpTableFromBlob** als Eingabe und **EmpSQLTable** als Ausgabe verwendet.

1. Erstellen Sie eine JSON-Datei für die Pipeline. Öffnen Sie den Editor, kopieren Sie das folgende JSON-Skript, und speichern Sie es als **EADFTutorialPipeline.json** im Ordner **C:\ADFGetStarted**.


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
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
        		]
      		}
		} 

	Beachten Sie Folgendes:

	- Der Abschnitt "activities" enthält nur eine Aktivität, deren **Typ** auf **CopyActivity** festgelegt ist.
	- Die Eingabe für die Aktivität ist auf **EmpTableFromBlob** und die Ausgabe auf **EmpSQLTable** festgelegt.
	- Im Abschnitt **transformation** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben.

2. Verwenden Sie das **New-AzureDataFactoryPipeline**-Cmdlet zum Erstellen einer Pipeline mit der von Ihnen erstellten Datei **ADFTutorialPipeline.json**.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. Sobald die Pipelines erstellt wurden, können Sie die Dauer angeben, in der die Datenverarbeitung von Daten erfolgt. Durch Angabe des aktiven Zeitraums für eine Pipeline definieren Sie die Dauer, für die die Datenslices verarbeitet werden, basierend auf den Verfügbarkeitseigenschaften, die für jede Azure Data Factory-Tabelle definiert wurden.  Führen Sie den folgenden PowerShell-Befehl aus, um den aktiven Zeitraum für die Pipeline festzulegen, und geben Sie "Y" zum Bestätigen ein. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] Ersetzen Sie den **StartDateTime**-Wert durch den aktuellen Tag und den **EndDateTime**-Wert durch den nächsten Tag. "StartDateTime" und "EndDateTime" sind UTC-Zeiten und müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. **EndDateTime** ist optional, wird aber in diesem Lernprogramm verwendet. 
	> Wenn Sie keinen **EndDateTime**-Wert angeben, wird er als **StartDateTime + 48 Stunden** berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie **9/9/9999** als **EndDateTime** an.  
	
	Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.

4. Klicken Sie im **Azure-Portal** im Fenster **DATA FACTORY** für **ADFTutorialDataFactory** auf **Diagramm**.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden: (Doppelklicken Sie auf einen Titel, um Details über das Artefakt anzuzeigen, das das Fenster darstellt).

	![Diagram view][image-data-factory-get-started-diagram-blade]

**Herzlichen Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.

## <a name="MonitorDataSetsAndPipeline"></a>Schritt 5: Überwachen der Datasets und der Pipeline
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Einzelheiten zur Verwendung von Cmdlets für die Überwachung finden Sie unter [Überwachen und Verwalten von Azure Data Factory mit PowerShell-Cmdlets][monitor-manage-using-powershell].

1. Navigieren Sie zum [Azure-Vorschauportal][azure-preview-portal], sofern es noch nicht geöffnet ist. 
2. Wenn das Fenster für **ADFTutorialDataFactory** nicht geöffnet ist, klicken Sie zum Öffnen im **Startmenü** auf **ADFTutorialDataFactory**. 
3. In diesem Fenster sollten die Anzahl und die Namen der von Ihnen erstellten Tabellen und Pipelines angezeigt werden.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. Klicken Sie nun auf die Kachel **Datasets**.
5. Klicken Sie im Fenster **Datasets** auf **EmpTableFromBlob**. Dies ist die Eingabetabelle für **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen, da die Datei **emp.txt** ständig im Blob-Container vorhanden ist: **adftutorial\input**. Überprüfen Sie, ob keine Slices im Abschnitt **Problemslices** am unteren Rand angezeigt werden.
6. Klicken Sie nun im Fenster **Datasets** auf **EmpSQLTable**. Dies ist die Ausgabetabelle für **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. Das Fenster **EmpSQLTable** sollte wie unten dargestellt angezeigt werden:

	![table blade][image-data-factory-get-started-table-blade]
 
7. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Im Abschnitt **Problemslices** am unteren Rand werden keine Slices angezeigt.
8. Klicken Sie auf **... (Auslassungspunkte)**, um alle Slices anzuzeigen.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Klicken Sie auf einen beliebigen Datenslice in der Liste; das Fenster **DATENSLICE** sollte angezeigt werden.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. Im Fenster **DATENSLICE** sollten in der Liste im unteren Bereich alle Aktivitätsausführungen angezeigt werden. Klicken Sie auf eine **Aktivitätsausführung**, um das Fenster **DETAILS ZUR AKTIVITÄTSAUSFÜHRUNG** anzuzeigen. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Klicken Sie auf **X**, um alle Fenster zu schließen, bis Sie wieder im Startfenster für **ADFTutorialDataFactory** sind.
14. (Optional) Klicken Sie auf der Startseite für **ADFTutorialDataFactory** auf **Pipelines**, klicken Sie im Fenster**Pipelines** auf **ADFTutorialPipeline** und führen Sie ein Drillthrough für die Eingabetabellen (**Consumed**) oder Ausgabetabellen (**Produced**) aus.
15. Starten Sie **SQL Server Management Studio**, stellen Sie eine Verbindung mit der Azure SQL-Datenbank her, und überprüfen Sie, ob die Zeilen in die Tabelle **emp** der Datenbank eingefügt wurden.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Zusammenfassung 
In diesem Lernprogramm haben Sie eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbank zu kopieren. Sie haben mithilfe des Azure-Vorschauportals die Data Factory und verknüpfte Dienste erstellt. Mithilfe von Azure PowerShell-Cmdlets haben Sie Tabellen und eine Pipeline erstellt und dann die Pipeline geplant. Nachfolgend sind die allgemeinen Schritte aufgeführt, die Sie in diesem Lernprogramm ausgeführt haben:  

1.	Erstellen einer Azure **Data Factory**
2.	Erstellen von **verknüpften Diensten**, die Datenspeicher und Computes verknüpfen (in der Data Factory als **Verknüpfte Dienste**) bezeichnet.
3.	Erstellen von **Tabellen**, die Eingabe- und Ausgabedaten für Pipelines beschreiben.
4.	Erstellen von **Pipelines**. Eine Pipeline besteht aus einer oder mehreren Aktivitäten, verarbeitet die Eingaben und erzeugt Ausgaben. 
5.	Angeben des **aktiven Zeitraums** für Pipelines (zur Datenverarbeitung). Der aktive Zeitraums definiert die Dauer, für die Datenslices erzeugt werden.

## Nächste Schritte

Artikel | Beschreibung
------ | ---------------
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer **umfassenden exemplarischen Vorgehensweise** die Implementierung eines **realen Szenarios** mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.
[Kopieren von Daten mit Azure Data Factory - Kopieraktivität][copy-activity] | In diesem Artikel wird die in diesem Lernprogramm verwendete **Kopieraktivität** ausführlich beschrieben.
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer **lokalen SQL Server-Datenbank** in einen Azure-BLOB.
[Verwenden von Pig und Hive mit Data Factory][use-pig-and-hive-with-data-factory] | Dieser Artikel enthält eine exemplarische Vorgehensweise, in der gezeigt wird, wie eine **HDInsight-Aktivität** verwendet wird, um ein **Hive-/Pig**-Skript zur Verarbeitung von Eingabedaten auszuführen, um Ausgabedaten zu produzieren.
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer **benutzerdefinierten Aktivität** und deren Verwendung in einer Pipeline.
[Überwachen und Verwalten von Azure Data Factory mit Azure PowerShell][monitor-manage-using-powershell] | In diesem Artikel wird das **Überwachen und Verwalten** einer Azure Data Factory mithilfe von **Azure PowerShell-Cmdlets** beschrieben. Sie können die Beispiele in diesem Artikel mit "ADFTutorialDataFactory" ausprobieren.
[Problembehandlung für Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie **Probleme in Azure Data Factory behoben werden**. Sie können die exemplarische Vorgehensweise in diesem Artikel mit "ADFTutorialDataFactory" ausprobieren, indem Sie einen Fehler einbauen (die Tabelle in der Azure SQL-Datenbank löschen). 
[Azure Data Factory-Cmdlet-Referenz][cmdlet-reference] | Diese Referenz enthält Einzelheiten zu allen **Data Factory-Cmdlets**.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
