<properties 
	pageTitle="Aktivieren von Pipelines zum Arbeiten mit lokalen Daten | Azure Data Factory" 
	description="Erfahren Sie, wie Sie eine lokale Datenquelle bei Azure Data Factory registrieren und Daten von/aus der Datenquelle kopieren." 
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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Aktivieren von Pipelines zum Arbeiten mit lokalen Daten

Um Pipelines in einer Azure Data Factory zum Arbeiten mit einer lokalen Datenquelle zu aktivieren, müssen Sie die lokale Datenquelle als verknüpften Dienst zur Data Factory hinzufügen, indem Sie entweder das Azure-Verwaltungsportal oder Azure PowerShell verwenden.
 
Um eine lokale Datenquelle als verknüpften Dienst zur einer Data Factory hinzuzufügen, müssen Sie zunächst das Microsoft Datenverwaltungsgateway auf einen lokalen Computer herunterladen und installieren und den verknüpften Dienst für die lokale Datenquelle zur Verwendung des Gateways konfigurieren.
 
> [WACOM.NOTE] Nur SQL Server wird derzeit als lokale Datenquelle unterstützt.

## <a href="DMG"></a> Datenverwaltungsgateway

**Datenverwaltungsgateway** ist eine Software, die lokale Datenquellen mit Cloud-Diensten auf sichere, verwaltete Weise verbindet. Mit dem Datenverwaltungsgateway können Sie

- **Eine Verbindung mit lokalen Daten für den hybriden Datenzugriff herstellen** - Sie können lokale Daten mit Cloud-Diensten verbinden, um die Cloud-Dienste zu nutzen und gleichzeitig die Geschäftstätigkeit mit lokalen Daten durchzuführen.
- **Einen sicheren Proxy definieren** - Sie können definieren, welche lokalen Datenquellen für Data Management Gateway zur Verfügung gestellt werden, damit dieses Gateway die Datenanforderung von Cloud-Diensten authentifiziert und die lokalen Datenquellen schützt.
- **Ihr Gateway für vollständige Governance verwalten** - Sie erhalten vollständige Überwachung und Protokollierung aller Aktivitäten innerhalb des Datenverwaltungsgateways für die Verwaltung und Governance.
- **Daten effizient verschieben** - Daten werden parallel verschoben, für Ausfallsicherheit bei vorübergehenden Netzwerkproblemen mit automatischer Wiederholungslogik.


Das Datenverwaltungsgateway verfügt über eine Vielzahl von lokalen Datenverbindungsfunktionen, die Folgendes bieten:

- **Keine Auswirkungen auf die Unternehmensfirewall** - Das Datenverwaltungsgateway funktioniert nach der Installation, ohne dass Sie eine Firewallverbindung öffnen oder tiefgreifende Veränderungen an Ihrer Unternehmensnetzwerk-Infrastruktur vornehmen müssen.
- **Verschlüsseln der Anmeldeinformationen mit Ihrem Zertifikat** - Anmeldeinformationen zum Verbinden mit Datenquellen werden mit einem Zertifikat im vollständigen Besitz eines Benutzers verschlüsselt. Ohne das Zertifikat kann niemand die Anmeldeinformationen in Klartext entschlüsseln, einschließlich Microsoft.

### Gatewayinstallation - Bewährte Methoden

1.	Wenn der Hostcomputer in den Ruhezustand wechselt, kann das Gateway nicht auf die Datenanforderung reagieren. Aus diesem Grund konfigurieren Sie vor der Installation des Gateways einen entsprechenden **Energiesparplan** auf dem Computer. 
2.	Das Datenverwaltungsgateway sollte eine Verbindung mit den lokalen Datenquellen herstellen können, die mit dem Azure Data Factory-Dienst registriert werden. Es muss nicht auf dem gleichen Computer wie die Datenquelle sein, jedoch reduziert die **Nähe zur Datenquelle** die Verbindungszeit für das Gateway mit der Datenquelle.

### Überlegungen zur Verwendung des Datenverwaltungsgateways
1.	Eine einzelne Instanz des Datenverwaltungsgateways kann für mehrere lokale Datenquellen verwendet werden, aber denken Sie daran, dass ein **Gateway an ein Azure Data Factory gebunden ist** und nicht für eine andere Data Factory freigegeben werden kann.
2.	Sie können nur **eine Instanz des Datenverwaltungsgateways** auf Ihrem Computer installiert haben. Angenommen, Sie haben zwei Data Factorys, die Zugriff auf lokale Datenquellen benötigen, dann müssen Sie die Gateways auf zwei lokalen Computern installieren, wobei jedes Gateway an eine separate Daten Factory gebunden ist.
3.	Wenn Sie bereits ein Gateway auf dem Computer installiert haben, das ein **Power BI-Szenario** bedient, installieren Sie ein **separates Gateway für die Azure Data Factory** auf einem anderen Computer. 
 

## Exemplarische Vorgehensweise

In dieser exemplarischen Vorgehensweise erstellen Sie eine Data Factory mit einer Pipeline, die Daten aus einer lokalen SQL Server-Datenbank in einen Azure-Blob kopiert. 

### Schritt 1: Erstellen einer Azure Data Factory
In diesem Schritt verwenden Sie das Azure-Verwaltungsportal zum Erstellen einer Azure Data Factory-Instanz mit dem Namen **ADFTutorialOnPremDF**. Sie können auch eine Data Factory mithilfe von Azure Data Factory-Cmdlets erstellen. 

1.	Nach der Anmeldung beim [Azure-Vorschauportal][
2.	azure-preview-portal] klicken Sie in der unteren linken Ecke auf **NEW** und dann auf **Data Factory** im Fenster **New**.

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Wenn die **Data Factory** nicht im Fenster **New** angezeigt wird, führen Sie einen Bildlauf nach unten durch.  
6. Führen Sie im Fenster **Neue Data Factory** Folgendes aus:
	1. Geben Sie **ADFTutorialOnPremDF** als **Name** ein.
	2. Klicken Sie auf **RESOURCE GROUP NAME**, und wählen Sie **ADFTutorialResourceGroup** aus (wenn Sie das Lernprogramm unter [Erste Schritte mit Azure Data Factory][adfgetstarted] ausgeführt haben). Sie können eine vorhandene Ressourcengruppe auswählen oder eine neue erstellen. So erstellen Sie eine neue Ressourcengruppe:
		1. Klicken Sie auf **CNeue Ressourcengruppe erstellen**.
		2. Geben Sie im Fenster **Ressourcengruppe erstellen** unter **Name** einen Namen für die Ressourcengruppe ein, und klicken Sie auf **OK**.

7. Sie sehen, dass **Zum Startboard hinzufügen** im Fenster **Neue Data Factory** aktiviert ist.

	![Add to Startboard][image-data-factory-add-to-startboard]

8. Klicken Sie im Fenster **Neue Data Factory** auf **Erstellen**.
9. Suchen Sie nach Benachrichtigungen vom Erstellungsvorgang im Hub **BENACHRICHTIGUNGEN** auf der linken Seite.

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. Nach Abschluss der Erstellung wird das Fenster "Data Factory" wie unten dargestellt angezeigt:

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. So sollte es auch auf dem **Startboard** angezeigt werden, wie unten dargestellt. Klicken Sie darauf, um das **Data Factory-Fenster** zu öffnen, wenn es nicht bereits geöffnet ist.

	![Startboard][image-data-factory-startboard]

### Schritt 2: Erstellen von verknüpften Diensten 
In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **MyBlobStore** und **OnPremSqlLinkedService**. Der **OnPremSqlLinkedService** verknüpft eine lokale SQL Server-Datenbank, und der verknüpfte Dienst **MyBlobStore** verknüpft einen Azure Blob-Speicher mit der **ADFTutorialDataFactory**. Sie erstellen eine Pipeline weiter unten in dieser exemplarischen Vorgehensweise, die Daten aus der lokalen SQL Server-Datenbank in den Azure Blob-Speicher kopiert. 

### Hinzufügen eines verknüpften Diensts zu einer lokalen SQL Server-Datenbank
1.	Klicken Sie im Fenster **Data Factory** für **ADFTutorialOnPremDF** auf **Verknüpfte Dienste**. 

	![Data Factory Home Page][image-data-factory-home-age]

2.	Klicken Sie im Fenster **Verknüpfte Dienste** auf **+ Daten-Gateway**.

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. Geben Sie im Fenster **Erstellen** den Namen **adftutorialgateway** unter **Name** ein, und klicken Sie auf **OK**. 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. Klicken Sie im Fenster **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Daraufhin wird das Installationspaket für das Gateway heruntergeladen, installiert, konfiguriert und das Gateway auf dem Computer registriert.

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	Dies ist die einfachste Möglichkeit (One-Click) zum Herunterladen, Installieren, Konfigurieren und Registrieren des Gateways in einem einzigen Schritt. Sie können sehen, dass die Anwendung **Microsoft Datenverwaltungsgateway - Konfigurations-Manager** auf Ihrem Computer installiert ist. Die ausführbare Datei **ConfigManager.exe** finden Sie im Ordner: **C:\Programme\Microsoft Data Management Gateway\1.0\Shared**.

	Sie können das Gateway auch manuell über die Links in diesem Fenster herunterladen und installieren und mit dem Schlüssel im Textfeld **MIT SCHLÜSSEL REGISTRIEREN** registrieren.
	
	Im Abschnitt zum [Datenverwaltungsgateway ](#DMG) finden Sie ausführliche Informationen über das Gateway mit bewährten Methoden und wichtigen Überlegungen. 

4. Klicken Sie im Fenster **Neues Daten-Gateway** auf **OK**.
5. Starten Sie den **Konfigurations-Manager für das Microsoft Datenverwaltungsgateway**   auf Ihrem Computer.

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. Warten Sie, bis die Werte wie folgt festgelegt sind:
	1. Wenn der **Status** des Diensts nicht auf **Gestartet** festgelegt ist, klicken Sie auf **Dienst starten**, um den Dienst starten, und warten Sie eine Minute, bis die anderen Felder aktualisiert wurden.
	2. **Gatewayname** wird auf **adftutorialgateway** eingestellt.
	3. **Instanzname** wird auf **adftutorialgateway** eingestellt.
	4. **Gateway-Schlüsselstatus** wird auf **Registriert** eingestellt.
	5. Die Statusleiste im unteren Bereich zeigt **Verbunden mit Datenverwaltungsgateway-Cloud-Dienst** zusammen mit einem **grünen Häkchen** an.  

7. Vergewissern Sie sich im Fenster **Verknüpfte Dienste**, dass der **Status** des Gateways **Gut** lautet, und klicken Sie auf **+ Datenspeicher**. Sie müssen möglicherweise das Fenster schließen und wieder öffnen, um es zu aktualisieren. 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. Geben Sie im Fenster **Neuer Datenspeicher** **OnPremSqlLinkedService** als **Name** ein.
9. Klicken Sie auf **TYP (Einstellungen erforderlich)**, und wählen Sie **SQL Server**. 
10. Klicken Sie im Fenster **Neuer Datenspeicher** auf **DATEN-GATEWAY (erforderliche Einstellungen konfigurieren)**.

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. Wählen Sie das zuvor erstellte **adftutorialgateway** aus. 
12.	Klicken Sie im Fenster **Neuer Datenspeicher** auf **ANMELDEINFORMATIONEN**, um das Fenster **Anmeldeinformationen** anzuzeigen.

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	Klicken Sie im Fenster **Anmeldeinformationen** auf **Klicken Sie hier, um Anmeldeinformationen auf sichere Weise festzulegen.**. Es wird folgendes Popup-Dialogfeld geöffnet.

	![One Click application install][image-data-factory-oneclick-install]

14. Klicken Sie auf **Ausführen**, um die Anwendung **Anmeldeinformations-Manager** zu installieren und das Dialogfeld "Anmeldeinformationen festlegen" angezeigt wird. 
15.	Geben Sie im Dialogfeld **Anmeldeinformationen festlegen** den **Benutzernamen** und das **Kennwort** das der Dienst verwenden kann, um auf die lokale SQL Server-Datenbank zuzugreifen, und klicken Sie auf **OK**. In diesem Dialogfeld wird nur die **SQL-Authentifizierung** unterstützt. Warten Sie, bis das Dialogfeld geschlossen wird.
16.	Klicken Sie im Fenster **Anmeldeinformationen** auf **OK**, und klicken Sie dann im Fenster **Neuer Datenspeicher** auf **OK**. Sie sollten den verknüpften Dienst **OnPremSqlLinkedService** sehen, den Sie im Fenster "Verknüpfte Dienste" hinzugefügt haben.

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Hinzufügen eines verknüpften Diensts für Azure-Speicherkonten

1.	Klicken Sie im Fenster **Data Factory** auf die Kachel **Verknüpfte Dienste**, um das Fenster **Verknüpfte Dienste** zu öffnen.
2. Klicken Sie im Fenster **Verknüpfte Dienste** auf **Datenspeicher hinzufügen**.	
3. Führen Sie im Fenster **Neuer Datenspeicher** Folgendes aus:
	1. Geben Sie einen **Namen** für den Datenspeicher ein. Geben Sie im Rahmen des Lernprogramms **MyBlobStore** als **Name** ein.
	2. Geben Sie die **Beschreibung (optional)** für den Datenspeicher ein.
	3. Klicken Sie auf **Type**.
	4. Wählen Sie **Azure-Speicherkonto** aus, und klicken Sie auf **OK**.
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. Jetzt sind Sie zurück im Fenster **Neuer Datenspeicher**, das folgendermaßen aussieht:

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. Geben Sie den **Kontonamen** und **Kontoschlüssel** für Ihr Azure-Speicherkonto ein, und klicken Sie auf **OK**.

  
6. Nachdem Sie im Fenster **Neuer Datenspeicher** auf **OK** geklickt haben, sollten Sie **MyBlobStore** in der Liste der **DATENSPEICHER** im Fenster **Verknüpfte Dienste** sehen. Überprüfen Sie den Hub **BENACHRICHTIGUNGEN** (auf der linken Seite) auf Nachrichten.

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## Schritt 3: Erstellen von Eingabe- und Ausgabedatasets
In diesem Schritt erstellen Sie die Eingabe- und Ausgabedatasets, die ein- und ausgehende Daten für den Kopiervorgang darstellen (lokale SQL Server-Datenbank = > Azure Blob-Speicher). Das Erstellen von Datasets und Pipelines wird vom Azure-Portal noch nicht unterstützt, sodass Sie Azure PowerShell-Cmdlets zum Erstellen von Tabellen und Pipelines verwenden. Vor dem Erstellen von Datasets oder Tabellen (rechteckige Datasets) müssen Sie Folgendes ausführen (detaillierte Schritte in der Liste):

- Erstellen Sie eine Tabelle mit dem Namen **emp** in der SQL Server-Datenbank, die Sie als verknüpften Dienst zur Data Factory hinzugefügt haben, und fügen Sie verschiedene Beispieleinträge in die Tabelle ein.
- - Wenn Sie das Lernprogramm [Erste Schritte mit Azure Data Factory][adfgetstarted] noch nicht durchgeführt haben, erstellen Sie einen Blob-Container mit dem Namen **adftutorial** im Azure Blob-Speicherkonto, das Sie als verknüpften Dienst zur Data Factory hinzugefügt haben.

### Vorbereitung des lokalen SQL Servers für das Lernprogramm

1. Verwenden Sie in der Datenbank, die Sie für den verknüpften Dienst der lokalen SQL Server-Datenbank (**OnPremSqlLinkedService**) angegeben haben, das folgende SQL-Skript zum Erstellen der Tabelle **emp** in der Datenbank.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Fügen Sie einige Beispiele in die Tabelle ein: 


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Erstellen der Eingabetabelle

1.	Erstellen Sie eine JSON-Datei für eine Data Factory-Tabelle, die Daten aus der Tabelle **emp** in der SQL Server-Datenbank darstellt. Öffnen Sie den **Editor**, kopieren Sie das folgende JSON-Skript, und speichern Sie es als **EmpOnPremSQLTable.json** im Ordner C:\ADFGetStarted\**OnPrem**. Erstellen Sie den Unterordner **OnPrem** in **C:\ADFGetStarted**, falls er noch nicht vorhanden ist. 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	Beachten Sie Folgendes: 
	
	- location **type** ist auf **OnPremisesSqlServerTableLocation** festgelegt.
	- **tableName** ist auf **emp** festgelegt.
	- **linkedServiceName** ist auf **OnPremSqlLinkedService** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
	- Für eine Eingabetabelle, die nicht durch eine andere Pipeline in Azure Data Factory generiert wird, müssen Sie den Abschnitt **waitOnExternal** in der JSON-Datei angeben. Dies bedeutet, dass die eingegebenen Daten außerhalb des Azure Data Factory-Diensts erstellt werden.   

	Details zu den JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

2. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager**-Modus verfügbar. Starten Sie **Azure PowerShell**,  und führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln.     

        switch-azuremode AzureResourceManager

	Laden Sie [Azure PowerShell][azure-powershell-install] herunter, falls es noch nicht auf Ihrem Computer installiert ist.
3. Verwenden Sie das **New-AzureDataFactoryTable**-Cmdlet, um die Eingabetabelle mit der Datei **EmpOnPremSQLTable.json** zu erstellen.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	Passen Sie den Befehl bei Verwendung einer anderen Ressourcengruppe an.

### Erstellen der Ausgabetabelle

1.	Erstellen Sie eine JSON-Datei für eine Data Factory-Tabelle als Ausgabe für die Pipeline, die Sie im nächsten Schritt erstellen. Öffnen Sie den Editor, kopieren Sie das folgende JSON-Skript, und speichern Sie es als **OutputBlobTable.json** im Ordner **C:\ADFGetStarted\OnPrem**.

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Beachten Sie Folgendes: 
	
	- location **type** ist auf **AzureBlobLocation** festgelegt.
	- **linkedServiceName** ist auf **MyBlobStore** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
	- **folderPath** ist auf **adftutorial/outfromonpremdf** festgelegt, wobei outfromonpremdf der Ordner im adftutorial-Container ist. Sie müssen lediglich den **adftutorial**-Container erstellen.
	- **availability** ist auf **hourly** festgelegt (**frequency** auf **hour** und **interval** auf **1**).  Der Data Factory-Dienst generiert jede Stunde einen Ausgabedatenslice in der Tabelle **emp** in der Azure SQL-Datenbank. 

	Wenn Sie keinen Dateinamen (**fileName**) für eine **Eingabetabelle** angeben, werden alle Dateien/Blobs aus dem Eingabeordner (**folderPath**) als Eingaben angesehen. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Beachten Sie auch die Beispieldateien in diesem [Lernprogramm][adf-tutorial].
 
	Wenn Sie keinen **fileName** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt (Beispiel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die partitionedBy-Eigenschaft. Im folgenden Beispiel verwendet folderPath die Angaben für Jahr, Monat und Tag aus SliceStart (Startzeit des zu vearbeitenden Slices) und fileName die Angabe für Stunde aus SliceStart. Wenn beispielsweise ein Slice für den Zeitpunkt 2014-10-20T08:00:00 erzeugt wird, wird folderName auf wikidatagateway/wikisampledataout/2014/10/20 und filName auf 08.csv festgelegt. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Details zu den JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

2.	Führen Sie in **Azure PowerShell** den folgenden Befehl zum Erstellen einer weiteren Data Factory-Tabelle zur Darstellung der Tabelle in der Azure SQL-Datenbank aus.

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## Schritt 4: Erstellen und Ausführen einer Pipeline
In diesem Schritt erstellen Sie eine **Pipeline** mit einer **Kopieraktivität**, die **EmpOnPremSQLTable** als Eingabe und **OutputBlobTable** als Ausgabe verwendet.

1.	Erstellen Sie eine JSON-Datei für die Pipeline. Öffnen Sie den Editor, kopieren Sie das folgende JSON-Skript, und speichern Sie es als **ADFTutorialPipelineOnPrem.json** im Ordner **C:\ADFGetStarted\OnPrem**.
	 

        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
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
 
	- Der Abschnitt "activities" enthält nur eine Aktivität, deren **type** auf **CopyActivity** festgelegt ist.
	- **Input** für die Aktivität ist auf **EmpOnPremSQLTable** und **output** auf **OutputBlobTable** festgelegt.
	- Im Abschnitt **transformation** ist **SqlSource** als **Quelltyp** und **BlobSink** als **Senkentyp** angegeben.
	- Die SQL-Abfrage **select * from emp** ist für die Eigenschaft **sqlReaderQuery** von **SqlSource** angegeben.

2. Verwenden Sie das **New-AzureDataFactoryPipeline**-Cmdlet zum Erstellen einer Pipeline mit der von Ihnen erstellten Datei **ADFTutorialPipeline.json**.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. Sobald die Pipeline erstellt wurde, können Sie die Dauer angeben, für die Daten verarbeitet werden. Durch Angabe des aktiven Zeitraums für eine Pipeline definieren Sie die Dauer, für die die Datenslices verarbeitet werden, basierend auf den **Verfügbarkeits**-Eigenschaften, die für jede Azure Data Factory-Tabelle definiert wurden.  Führen Sie den folgenden PowerShell-Befehl aus, um den aktiven Zeitraum für die Pipeline festzulegen, und geben Sie Y zum Bestätigen ein. 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] Ersetzen Sie den **StartDateTime**-Wert durch den aktuellen Tag und den **EndDateTime**-Wert durch den nächsten Tag. StartDateTime und EndDateTime sind UTC-Zeiten und müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. EndDateTime ist optional, wird aber in diesem Lernprogramm verwendet.
	> Wenn Sie keinen **EndDateTime**-Wert angeben, wird er als "**StartDateTime + 48 Stunden**" berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie **9/9/9999** als **EndDateTime** an.

	Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
4. Klicken Sie im **Azure-Vorschauportal** auf der Startseite für die Data Factory **ADFTutorialOnPremDF** auf die Kachel **Diagramm**. :

	![Diagram Link][image-data-factory-diagram-link]

5. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden:

	![Diagram View][image-data-factory-diagram-view]

	**Glückwunsch!**Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine  	Pipeline erstellt und den Workflow gestartet.

## Schritt 5: Überwachen der Datasets und Pipelines
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Weitere Informationen zum Verwenden von Cmdlets für die Überwachung finden Sie unter [Überwachen und Verwalten von Azure Data Factory mithilfe von PowerShell][monitor-manage-powershell].

1. Navigieren Sie zum **Azure-Vorschauportal** (falls Sie es geschlossen haben).
2. Wenn das Fenster für **ADFTutorialOnPremDF** nicht geöffnet ist, klicken auf im **Startmenü** zum Öffnen auf **ADFTutorialOnPremDF**.
3. In diesem Fenster sollten die **Anzahl** und **Namen** der erstellten Tabellen und der Pipeline angezeigt werden.

	![Data Factory Home Page][image-data-factory-homepage-2]
4. Klicken Sie nun auf die Kachel **Datasets**.
5. Klicken Sie im Fenster **Datasets** auf **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Das liegt daran, dass Sie die Daten in die SQL Server-Datenbank eingefügt haben und sie immer vorhanden sind. Überprüfen Sie, ob keine Slices im Abschnitt **Problemslices** am unteren Rand angezeigt werden.
7. Klicken Sie jetzt im Fenster **Datasets** auf **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Überprüfen Sie, ob die Slices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen.
9. Klicken Sie auf einen beliebigen Datenslice in der Liste; das Fenster **DATENSLICE** sollte angezeigt werden.

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. Klicken Sie auf die **Aktivitätsausführung** in der Liste unten, um die **Details zur Aktivitätsausführung** anzuzeigen.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Klicken Sie auf **X**, um alle Fenster zu schließen, bis Sie wieder im Startfenster für **ADFTutorialOnPremDF** sind.
14. (optional) Klicken Sie auf **Pipelines**, dann auf **ADFTutorialOnPremDF**, und zeigen Sie Details zu den Eingabetabellen (**Verbraucht**) oder Ausgabetabellen (**Erstellt**) an.
15. Verwenden Sie Tools wie z. B. **Azure Storage Explorer**, um die Ausgabe zu überprüfen.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets
Dieser Abschnitt beschreibt das Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets. 

1. Starten Sie **Azure PowerShell** im Administratormodus. 
2. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager**-Modus verfügbar. Führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln.     

        switch-azuremode AzureResourceManager


2. Verwenden Sie das **New-AzureDataFactoryGateway**-Cmdlet, um wie folgt ein logisches Gateway zu erstellen:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**Beispiel für eine Befehlszeile und Ausgabe**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. Verwenden Sie das **New-AzureDataFactoryGatewayKey**-Cmdlet, um einen Registrierungsschlüssel für das neu erstellte Gateway zu generieren, und speichern Sie den Schlüssel in einer lokalen Variablen **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**Beispiel für eine Befehlszeilenausgabe:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Wechseln Sie in Azure PowerShell zu folgendem Ordner: **C:\Programme\Microsoft Data Management Gateway\1.0\PowerShellScript\**, und führen Sie das Skript **RegisterGateway.ps1** aus, das mit der lokalen Variablen **$Key** verknüpft ist, wie im folgenden Befehl zum Registrieren des Client-Agents auf dem Computer mit dem logischen Gateway, das Sie zuvor erstellt haben, dargestellt.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Mit dem **Get-AzureDataFactoryGateway**-Cmdlet können Sie die Liste mit Gateways in Ihrer Data Factory abrufen. Wenn der **Status** **online** lautet, bedeutet dies, dass Ihr Gateway einsatzbereit ist.

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

Sie können ein Gateway mit dem **Remove-AzureDataFactoryGateway**-Cmdlet entfernen und die Beschreibung für das Gateway mithilfe des **Set-AzureDataFactoryGateway**-Cmdlets aktualisieren. Syntax und andere Details zu diesen Cmdlets finden Sie in der Data Factory-Cmdlet-Referenz.  




## Weitere Informationen

Artikel | Beschreibung
------ | ---------------
[Erste Schritte mit Azure Data Factory][adfgetstarted] | In diesem Artikel finden Sie ein umfassendes Lernprogramm, das zeigt, wie Sie eine Beispiel-Azure Data Factory erstellen, die Daten aus einem Azure-Blob mit einer Azure SQL-Datenbank kopiert.
[Verwenden von Pig und Hive mit Data Factory][use-pig-and-hive-with-data-factory] | In diesem Artikel wird in einer exemplarischen Vorgehensweise die Verwendung der  HDInsight-Aktivität zum Ausführen eines Hive/Pig-Skripts veranschaulicht, das Eingabedaten verarbeitet, um Ausgabedaten zu erzeugen. 
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | In diesem Artikel werden in einer exemplarischen Vorgehensweise mit schrittweisen Anweisungen die Erstellung einer benutzerdefinierten Aktivität und ihre Verwendung in einer Pipeline veranschaulicht. 
[Problembehandlung bei Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Sie Azure Data Factory-Probleme beheben.  
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/


[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!--HONumber=46--> 
