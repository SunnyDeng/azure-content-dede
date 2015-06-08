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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Aktivieren von Pipelines zum Arbeiten mit lokalen Daten

Um Pipelines in einer Azure Data Factory zum Arbeiten mit einer lokalen Datenquelle zu aktivieren, müssen Sie die lokale Datenquelle als verknüpften Dienst zur Data Factory hinzufügen, indem Sie entweder das Azure-Verwaltungsportal oder Azure PowerShell verwenden.
 
Um eine lokale Datenquelle als verknüpften Dienst zur einer Data Factory hinzuzufügen, müssen Sie zunächst das Microsoft Datenverwaltungsgateway auf einen lokalen Computer herunterladen und installieren und den verknüpften Dienst für die lokale Datenquelle zur Verwendung des Gateways konfigurieren.


## <a href="DMG"></a> Data Management Gateway

**Datenverwaltungsgateway** ist eine Software, die auf lokale Datenquellen um cloud-Dienste auf sichere und verwaltete Weise verbindet. Mit dem Datenverwaltungsgateway können Sie

- **Mit lokalen Daten für den Hybrid-Datenzugriff** – Sie können lokale Daten mit Cloud-Dienste mit Cloud-Diensten nutzen und gleichzeitig die Geschäftstätigkeit mit lokalen Daten verbinden.
- **Definieren eines sicheren** – Sie können definieren, welche lokalen Datenquellen mit Data Management Gateway verfügbar gemacht werden, damit dieses Gateway die Datenanforderung mit Cloud-Diensten authentifiziert und lokalen Datenquellen schützt.
- **Verwalten Sie Ihr Gateway für vollständige Governance** – Sie erhalten vollständige Überwachung und Protokollierung aller Aktivitäten innerhalb der Data Management Gateway für die Verwaltung und Governance.
- **Verschieben von Daten effizient** – Datenaustausch parallel Ausfallsicherheit vorübergehenden Netzwerkproblemen automatische Wiederholungslogik.


Das Datenverwaltungsgateway verfügt über eine Vielzahl von lokalen Datenverbindungsfunktionen, die Folgendes bieten:

- **Keine Auswirkungen auf die Unternehmensfirewall** – Data Management Gateway funktioniert nach der Installation, ohne eine Firewall öffnen Verbindung oder intrusive erfordern Änderungen der Unternehmensnetzwerk-Infrastruktur. 
- **Verschlüsseln von Anmeldeinformationen mit dem Zertifikat** – Anmeldeinformationen zum Verbinden mit Datenquellen verwendet werden mit einem Zertifikat vollständig im Besitz eines Benutzers verschlüsselt. Ohne das Zertifikat kann niemand die Anmeldeinformationen in Klartext entschlüsseln, einschließlich Microsoft.

### Überlegungen zur Verwendung des Datenverwaltungsgateways
1.	Eine einzelne Instanz des Datenverwaltungsgateways kann für mehrere lokale Datenquellen verwendet werden, aber denken Sie daran, eine **Gateway ist mit einer Azure Data-Factory verknüpft** und nicht mit einem anderen Unternehmen für Daten freigegeben werden können.
2.	Sie haben **nur eine Instanz des Datenverwaltungsgateways** auf Ihrem Computer installiert. Angenommen, Sie haben zwei Data Factorys, die Zugriff auf lokale Datenquellen benötigen, dann müssen Sie die Gateways auf zwei lokalen Computern installieren, wobei jedes Gateway an eine separate Daten Factory gebunden ist.
3.	Die **Gateway muss nicht auf dem gleichen Computer wie die Datenquelle werden**, jedoch bleiben die näher an der Datenquelle reduziert die Zeit für das Gateway für die Verbindung mit der Datenquelle. Es wird empfohlen, dass Sie das Gateway auf einem Computer installieren, die von der unterscheidet, die lokale Datenquelle hostet, damit das Gateway nicht für Ressourcen, die mit der Datenquelle im Wettbewerb.
4.	Sie haben **mehrere Gateways auf verschiedenen Computern herstellen einer Verbindung mit der gleichen Datenquelle von lokalen**. Z. B. wird möglicherweise zwei Gateways zwei Data-Factorys für dieselbe lokale Datenquelle jedoch mit sowohl die Factorys für Daten. 
5.	Wenn Sie bereits über ein Gateway, das auf Ihrem Computer Serving installiert haben eine **Power BI** Szenario installieren Sie einen **separate Gateway für Azure Data Factory** auf einem anderen Computer.

### Ports und Sicherheit 
- Öffnet das Installationsprogramm Datenverwaltungsgateway **8050** und **8051** Ports auf dem Gatewaycomputer. Diese Ports werden verwendet, indem Sie die **Anmeldeinformations-Manager** (ClickOnce-Anwendung), können Sie zum Festlegen von Anmeldeinformationen für einen Dienst lokal verknüpft und Testen Sie die Verbindung mit der Datenquelle. Diese Ports aus dem Internet nicht erreicht werden kann, und Sie müssen keine diese in die Unternehmens-Firewall geöffnet.
- Wenn Sie Daten aus und in einer lokalen SQL Server-Datenbank aus einer Azure SQL-Datenbank zu kopieren, stellen Sie Folgendes sicher:
 
	- Firewall auf dem Gatewaycomputer lässt ausgehenden TCP-Kommunikation auf **TCP** Port **1433**
	- Konfigurieren Sie [Azure SQL-Firewall-Einstellungen](https://msdn.microsoft.com/library/azure/jj553530.aspx) Hinzufügen der **IP-Adresse des Gateway-Computer** an der **zulässige IP-Adressen**.

- Beim Kopieren von Daten zu/von lokalen SQL Server zu beliebigen Zielen und das Gateway und die SQL Server-Computer unterscheiden, gehen Sie folgendermaßen vor: [Windows-Firewall konfigurieren](https://msdn.microsoft.com/library/ms175043.aspx) auf dem SQL Server-Computer so, dass das Gateway die Datenbank über Ports zugreifen kann, die SQL Server-Instanz überwacht. Ist es die Standardinstanz Port 1433.

- Starten Sie die **Anmeldeinformations-Manager** Anwendung auf einem Computer, der das Datenverwaltungsgateway zu können, zum Festlegen von Anmeldeinformationen für die Datenquelle und zum Testen der Verbindung zur Datenquelle herstellen können.

### Gateway-Installation – Voraussetzungen 

1.	Die unterstützten **Betriebssystem** Versionen sind, Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012.
2.	Die empfohlene **Konfiguration** für das Gateway-Computer ist mindestens 2 GHz, 4 Kerne, 8 GB RAM und 80 GB Festplatte.
3.	Wenn der Hostcomputer im Ruhezustand befindet, wird nicht das Gateway auf Datenanforderungen reagieren sein. Konfigurieren Sie daher eine entsprechende **Energiesparplan** auf dem Computer vor der Installation des Gateways. Die Gateway-Installation aufgefordert, wenn der Computer den Ruhezustand konfiguriert ist.  


 

## Exemplarische Vorgehensweise

In dieser exemplarischen Vorgehensweise erstellen Sie eine Data Factory mit einer Pipeline, die Daten aus einer lokalen SQL Server-Datenbank in einen Azure-Blob kopiert.

## Schritt 1: Erstellen einer Azure Data-factory
In diesem Schritt verwenden Sie das Azure-Verwaltungsportal zum Erstellen einer Azure Data Factory-Instanz mit dem Namen **ADFTutorialOnPremDF**. Sie können auch eine Data Factory mithilfe von Azure Data Factory-Cmdlets erstellen.

1.	Nach der Anmeldung die [Azure Preview Portal][azure-preview-portal], klicken Sie auf **Neu** Wählen Sie in der unteren linken Ecke **Datenanalyse** in den **Erstellen** Blade, und klicken Sie auf **Data Factory** auf der **Datenanalyse** Blade.

	![Neu -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. In der **neue Daten Factory** Blade:
	1. Geben Sie **ADFTutorialOnPremDF** für den **Namen**.
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME** und wählen Sie **ADFTutorialResourceGroup** (Wenn Sie das Lernprogramm unter werden musste, [Erste Schritte mit Azure Data Factory][adfgetstarted]. Sie können eine vorhandene Ressourcengruppe auswählen oder eine neue erstellen. So erstellen Sie eine neue Ressourcengruppe:
		1. Klicken Sie auf **Erstellen Sie eine neue Ressourcengruppe**.
		2. In der **ressourcengruppenfenster erstellen**, geben Sie einen **Name** für die Ressourcengruppe, und klicken Sie auf **OK**.

7. Beachten Sie, dass **zum Startmenü hinzufügen** wird geprüft, ob die **neue Daten Factory** Blade.

	![Zum Startmenü hinzufügen][image-data-factory-add-to-startboard]

8. In der **neue Daten Factory** Blade, klicken Sie auf **Erstellen**.

	> [AZURE.NOTE]**Daten-Factory-Namen "ADFTutorialOnPremDF" ist nicht verfügbar**  
9. Suchen Sie für Benachrichtigungen von der Erstellungsprozess in der **Benachrichtigungen** Hub auf der linken Seite. Klicken Sie auf **X** zum Schließen der **SERVERPROZESSE** Blade, falls er geöffnet ist.

	![Hub für Benachrichtigungen][image-data-factory-notifications-hub]

11. Nach Abschluss der Erstellung sehen Sie die **Data Factory** Blade wie unten dargestellt:

	![Daten-Factory-Homepage][image-data-factory-datafactory-homepage]

## Schritt 2: Erstellen einer Data Management gateway
5.	Auf der **Data Factory** Blade für **ADFTutorialOnPremDF**, klicken Sie auf **verknüpften Dienste**. 

	![Daten-Factory-Homepage][image-data-factory-home-age]

2.	Auf der **verknüpften Dienste** Blade, klicken Sie auf **+ datenverwaltungsgateway**.

	![Verknüpfte Dienste - hinzufügen Gateway Schaltfläche][image-data-factory-linkedservices-add-gateway-button]

2. In der **Erstellen** Blade, geben Sie **Adftutorialgateway** für den **Namen**, und klicken Sie auf **OK**.

	![Gateway-Blade erstellen][image-data-factory-create-gateway-blade]

3. Klicken Sie auf dem Blatt **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Daraufhin wird das Installationspaket für das Gateway heruntergeladen, installiert, konfiguriert und das Gateway auf dem Computer registriert.

	> [AzURE.NOTE]Verwenden Sie Internet Explorer oder einem kompatiblen Webbrowser Microsoft ClickOnce.

	![Gateway - Blade konfigurieren][image-data-factory-gateway-configure-blade]

	Dies ist die einfachste Möglichkeit (One-Click) zum Herunterladen, Installieren, Konfigurieren und Registrieren des Gateways in einem einzigen Schritt. Sie sehen die **Microsoft-Gateway Konfigurations-Manager** Anwendung auf Ihrem Computer installiert ist. Sie finden auch die ausführbare Datei **ConfigManager.exe** in den Ordner: **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**.

	Sie können auch herunterladen, und installieren Sie Gateway manuell über die Links in diesem Fenster, und registrieren Sie ihn mit dem Schlüssel der **Registrieren mit Schlüssel** Textfeld.
	
	Finden Sie unter [Datenverwaltungsgateway](#DMG) Abschnitt ausführliche Informationen über das Gateway mit best Practices und wichtige Überlegungen.

	>[AZURE.NOTE]Sie müssen ein Administrator auf dem lokalen Computer installieren und konfigurieren den Data Management Gateway erfolgreich sein. Sie können zusätzliche Benutzern die Datenverwaltungsgateway-Benutzer lokale Windows-Gruppe hinzufügen. Die Mitglieder dieser Gruppe werden Data Management Gateway-Konfigurations-Manager verwenden, um das Gateway konfigurieren können.

4. Klicken Sie auf die **Benachrichtigungen** Hub auf der linken Seite. Warten Sie, bis **Express Setup für 'Adftutorialgateway' wurde erfolgreich** Nachricht in die **Benachrichtigungen** Blade.

	![Express-Installation war erfolgreich.][express-setup-succeeded]
5. Klicken Sie auf **OK** auf der **Erstellen** Blade und klicken Sie dann auf die **Neuer datenverwaltungsgateway** Blade.
6. Schließen der **verknüpften Dienste** Blade (durch Drücken von **X** Schaltfläche in der oberen rechten Ecke) und erneut öffnen, die **verknüpften Dienste** Blade an den aktuellen Status des Gateways finden Sie unter. 
7. Überprüfen Sie, ob die **Status** des Gateways ist **Online**. 

	![Gatewaystatus][gateway-status]
5. Starten Sie **Microsoft-Gateway Konfigurations-Manager** Anwendungen auf Ihrem Computer.

	![Gateway-Konfigurations-Manager][image-data-factory-gateway-configuration-manager]

6. Warten Sie, bis die Werte wie folgt festgelegt sind:
	1. Wenn der Dienst **Status** nicht festgelegt ist, um **gestartet**, klicken Sie auf **Starten** starten Sie den Dienst, und warten Sie eine Minute für die anderen Felder aktualisieren.
	2. **Gatewayname** minFreeThreads auf **Adftutorialgateway**.
	3. **Instanzname** minFreeThreads auf **Adftutorialgateway**.
	4. **Status des gatewayschlüssels** minFreeThreads auf **registrierte**.
	5. Die Statusleiste im unteren Bereich werden **mit Datenverwaltungsgateway-Cloud-Dienst verbunden** zusammen mit einem **grünes Häkchen**.
	
7. Auf der **verknüpften Dienste** Blade, überprüfen Sie, ob die **Status** des Gateways ist **gute**.
8. Schließen Sie alle Blades, bis Sie an die **Data Factory** auf der Startseite. 

## Schritt 2: Erstellen von verknüpften Dienste 
In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **SqlServerLinkedService**. Die **SqlServerLinkedService** links von einer lokalen SQL Server-Datenbank und die **StorageLinkedService** verknüpfte Service verknüpft ein Azure-Blob-Speichers, der **ADFTutorialDataFactory**. Sie erstellen eine Pipeline weiter unten in dieser exemplarischen Vorgehensweise, die Daten aus der lokalen SQL Server-Datenbank in den Azure Blob-Speicher kopiert.

### Hinzufügen eines verknüpften Diensts zu einer lokalen SQL Server-Datenbank
1.	In der **DATA FACTORY** Blade, klicken Sie auf **Autor und Bereitstellen von** Kachel zu starten der **Editor** für die Daten-Factory.

	![Kachel "Erstellen und bereitstellen"][image-author-deploy-tile]

	> [AZURE.NOTE][Data-Factory-Editor][data-factory-editor]
2.	In der **Editor**, klicken Sie auf **neuen Datenspeicher** Schaltfläche auf der Symbolleiste, und wählen **lokale SQL Server-Datenbank** aus dem Dropdown-Menü. 

	![Neue Data Store Schaltfläche-Editor][image-editor-newdatastore-onpremsql-button]
    
3.	Die JSON-Vorlage zum Erstellen eines lokalen SQL Server verknüpft-Diensts im rechten Fensterbereich sollte angezeigt werden. ![Lokaler SQL verknüpfte Service - Einstellungen][image-editor-newdatastore-onpremsql-settings]

4.	Gehen Sie in das JSON-Bereich:
	1.	Für die **GatewayName** -Eigenschaft, geben Sie **Adftutorialgateway** gesamten Text in doppelten Anführungszeichen ersetzt.  
	2.	Bei Verwendung von **SQL-Authentifizierung**: 
		1.	Für die **ConnectionString** Eigenschaft ersetzen **< Servername >**, **< Datenbankname >**, **< Benutzername >**, und **< Kennwort >** mit Namen der lokalen SqlServer-Datenbank, Benutzerkonto und Kennwort.	
		2.	Entfernen Sie die letzten beiden Eigenschaften (** Benutzername ** und **Kennwort**) von JSON-Datei, und entfernen Sie die **Komma (,)** Zeichen am Ende der letzten Zeile des verbleibenden JSON-Skripts.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	Bei Verwendung von **Windows-Authentifizierung**:
		1. Für die **ConnectionString** Eigenschaft ersetzen **< Servername >** und **< Datenbankname >** mit dem Namen des lokalen SQLServer und der Datenbank. Legen Sie **integrierte Sicherheit** auf **True**. Entfernen Sie **ID** und **Kennwort** aus der Verbindungszeichenfolge.
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. Klicken Sie auf **Bereitstellen** auf der Symbolleiste auf die SqlServerLinkedService bereitstellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTE SERVICE erstellt erfolgreich** auf der Titelleiste. Außerdem sollte der **SqlServerLinkedService** in der Strukturansicht auf der linken Seite.
		   
	![SqlServerLinkedService wurde erfolgreich bereitgestellt][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]Sie können auch einen verknüpfte SQL-Server-Dienst erstellen, indem Sie auf **neuen Datenspeicher** Symbolleisten-Schaltfläche auf der **verknüpften Dienste** Blade. Wenn Sie diese Route wechseln, legen Sie die Anmeldeinformationen für die Datenquelle mithilfe der Anmeldeinformationen Manager ClickOnce-Anwendung, die auf dem Computer, der Zugriff auf das Portal ausgeführt wird. Wenn Sie das Portal auf einem Computer, die von der Gateway-Computer unterscheidet zugreifen, müssen Sie sicherstellen, dass die Anmeldeinformations-Manager-Anwendung mit dem Gatewaycomputer herstellen kann. Wenn die Anwendung den Gateway-Computer nicht erreichen kann, können nicht zum Festlegen von Anmeldeinformationen für die Datenquelle und zum Testen der Verbindung mit der Datenquelle.

#### Hinzufügen eines verknüpften Diensts für Azure-Speicherkonten
 
1. In der **Editor**, klicken Sie auf **neuen Datenspeicher** Schaltfläche auf der Symbolleiste, und wählen **Azure-Speicher** aus dem Dropdown-Menü. Die JSON-Vorlage zum Erstellen einer Azure-Speicher verknüpft-Diensts im rechten Fensterbereich sollte angezeigt werden. 

	![Neue Data Store Schaltfläche-Editor][image-editor-newdatastore-button]
    
6. Ersetzen Sie **< Kontoname >** und **< Accountkey >** mit dem Kontonamen und Kontowerte für Azure-Speicherkonto.

	![Blob-Speicher JSON-Editor][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Finden Sie unter [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) ausführliche Informationen zu JSON-Eigenschaften.

6. Klicken Sie auf **Bereitstellen** auf der Symbolleiste auf die StorageLinkedService bereitstellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTE SERVICE erstellt erfolgreich** auf der Titelleiste.

	![Editor-Blob-Speicher bereitstellen.][image-editor-blob-storage-deploy]

 
## Schritt 3: Erstellen von Eingabe und Ausgabe von datasets
In diesem Schritt erstellen Sie die Eingabe- und Ausgabedatasets, die ein- und ausgehende Daten für den Kopiervorgang darstellen (lokale SQL Server-Datenbank = > Azure Blob-Speicher). Vor dem Erstellen von Datasets oder Tabellen (rechteckige Datasets) müssen Sie Folgendes ausführen (detaillierte Schritte in der Liste):

- Erstellen Sie eine Tabelle mit dem Namen **emp** in der SQL Server-Datenbank hinzufügen als verknüpfte Dienst auf die Daten Factory und Insert paar Beispiele für Einträge in der Tabelle.
- - Wenn Sie das Lernprogramm unter durchgegangen noch nicht [Erste Schritte mit Azure Data Factory][adfgetstarted] Artikel, erstellen Sie einen Blob-Container mit dem Namen **Adftutorial** in Azure blob-Speicher, das Sie die Factory Daten als verknüpfte Dienst hinzugefügt.

### Vorbereitung des lokalen SQL Servers für das Lernprogramm

1. In der Datenbank, die Sie angegeben haben, für das lokale SQL Server Service verknüpft (** SqlServerLinkedService **), verwenden Sie das folgende SQL-Skript zum Erstellen der **emp** Tabelle in der Datenbank.


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

1.	In der **Data Factory-Editor**, klicken Sie auf **Neues Dataset** auf der Befehlsleiste und auf **lokale SQL**. 
2.	Ersetzen Sie die JSON im rechten Bereich mit dem folgenden Text:    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
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
	
	- Speicherort **Typ** minFreeThreads auf **OnPremisesSqlServerTableLocation**.
	- **TableName** minFreeThreads auf **emp**.
	- **LinkedServiceName** minFreeThreads auf **SqlServerLinkedService** (Sie haben diesen verknüpften Dienst in Schritt2 erstellte).
	- Für eine Eingabetabelle, die nicht durch eine andere Pipeline in Azure Data Factory generiert wird, müssen Sie angeben **WaitOnExternal** Abschnitt in JSON. Dies bedeutet, dass die eingegebenen Daten außerhalb des Azure Data Factory-Diensts erstellt werden.   

	Finden Sie unter [JSON Scripting Reference][json-script-reference] ausführliche Informationen zu JSON-Eigenschaften.

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste auf das Dataset bereitstellen (Tabelle ist ein rechteckiges Dataset). Vergewissern Sie sich, dass Sie eine Meldung in der Titelleiste angezeigt wird **Tabelle erfolgreich bereitgestellt**.


### Erstellen der Ausgabetabelle

1.	In der **Data Factory-Editor**, klicken Sie auf **Neues Dataset** auf der Befehlsleiste und auf **Azure Blob-Speicher**.
2.	Ersetzen Sie die JSON im rechten Bereich mit dem folgenden Text: 

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
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Beachten Sie Folgendes:
	
	- Speicherort **Typ** minFreeThreads auf **AzureBlobLocation**.
	- **LinkedServiceName** minFreeThreads auf **StorageLinkedService** (Sie haben diesen verknüpften Dienst in Schritt2 erstellte).
	- **FolderPath** minFreeThreads auf **Adftutorial/Outfromonpremdf** Outfromonpremdf ist, in den Ordner im Container Adftutorial. Sie müssen nur erstellen die **Adftutorial** Container.
	- Die **Verfügbarkeit** minFreeThreads auf **stündlich** (** Häufigkeit ** festgelegt **Stunde** und **Intervall** festgelegt **1**). Die Service Factory von Daten generiert eine Ausgabe Datenslice pro Stunde in die **emp** Tabelle in Azure SQL-Datenbank. 

	Wenn Sie kein **Dateiname** für eine **Eingabetabelle**, alle Dateien/Blobs aus der Eingabeordner (** FolderPath **) gelten als Eingaben. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Finden Sie die Beispieldateien aus dem [Lernprogramm][adf-tutorial] Beispiele.
 
	Wenn Sie keinen **fileName** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	Festlegen **FolderPath** und **Dateiname** dynamisch auf Grundlage der **SliceStart** Zeit, verwenden Sie die PartitionedBy-Eigenschaft. Im folgenden Beispiel verwendet folderPath die Angaben für Jahr, Monat und Tag aus "SliceStart" (Startzeit des zu verarbeitenden Slices) und "fileName" die Angabe für Stunde aus "SliceStart". Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Finden Sie unter [JSON Scripting Reference][json-script-reference] ausführliche Informationen zu JSON-Eigenschaften.

2.	Klicken Sie auf **Bereitstellen** auf der Befehlsleiste auf das Dataset bereitstellen (Tabelle ist ein rechteckiges Dataset). Vergewissern Sie sich, dass Sie eine Meldung in der Titelleiste angezeigt wird **Tabelle erfolgreich bereitgestellt**.
  

## Schritt 4: Erstellen und Ausführen einer pipeline
In diesem Schritt erstellen Sie eine **Pipeline** mit einem **Kopie Aktivität** verwendet, die **EmpOnPremSQLTable** als Eingabe und **OutputBlobTable** als Ausgabe.

1.	Klicken Sie auf **neue Pipeline** auf der Befehlsleiste. Wenn die Schaltfläche nicht angezeigt wird, klicken Sie auf **... (mit den drei Punkten)** um die Befehlsleiste zu erweitern.
2.	Ersetzen Sie die JSON im rechten Bereich mit dem folgenden Text:   


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
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	Beachten Sie Folgendes:
 
	- Im Bereich Aktivitäten nur Aktivität vorhanden ist, dessen **Typ** minFreeThreads auf **CopyActivity**.
	- **Eingabe** für die Aktivität, dass festgelegt ist **EmpOnPremSQLTable** und **Ausgabe** für die Aktivität, dass festgelegt ist **OutputBlobTable**.
	- In der **Transformation** Abschnitt **SQLSource aus** angegeben ist, als die **Datenquellentyp** und **BlobSink **angegeben wird die **Senke Typ**. - SQL-Abfrage **Wählen Sie * aus emp** angegeben ist, für die **SqlReaderQuery** -Eigenschaft des **SQLSource aus**.

	> [AZURE.NOTE]Ersetzen Sie den Wert der **start** Eigenschaft mit dem aktuellen Tag und **End** Wert mit am nächsten Tag. Sowohl start und Ende Uhrzeitangaben müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601). Zum Beispiel: 2014-10-14T16:32:41Z. Die **End** Zeit ist optional, aber wir werden es in diesem Lernprogramm verwenden. Wenn Sie keinen Wert zum Angeben der **End** -Eigenschaft wird berechnet als "** Start + 48 Stunden **". Geben Sie zum Ausführen der Pipeline auf unbestimmte Zeit **9, 9/9999** als Wert für die **End** Eigenschaft. Definieren Sie die Dauer in dem die Datensegmente verarbeitet werden basierend auf den **Verfügbarkeit** Eigenschaften, die für jede Tabelle Azure Data Factory definiert wurden. Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste auf das Dataset bereitstellen (Tabelle ist ein rechteckiges Dataset). Vergewissern Sie sich, dass Sie eine Meldung in der Titelleiste angezeigt wird **PIPELINE wurde erfolgreich bereitgestellt**.
5. Schließen Sie jetzt die **Editor** Blade, indem Sie auf **X**. Klicken Sie auf **X** zu schließen das Fenster "ADFTutorialDataFactory" in der Symbolleiste und der Struktur an. Wenn Sie sehen **Ihre nicht gespeicherten Änderungen werden verworfen,** auf **OK**.
6. Sie sollte an die **DATA FACTORY** Blade für die **ADFTutorialOnPremDF**.

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.

### Die Factory Daten in einer Diagrammansicht anzeigen 
1. In der **Azure Preview Portal**, klicken Sie auf **Diagramm** Kachel auf der Startseite für die **ADFTutorialOnPremDF** "Data Factory".:

	![Diagramm-Link][image-data-factory-diagram-link]

2. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden:

	![Diagrammansicht][image-data-factory-diagram-view]

	Sie können vergrößern, verkleinern, zoom auf 100 % Zoom anpassen, automatisch Positionieren von Pipelines und Tabellen und Anzeigen von Informationen über die Datenherkunft (hervorgehoben Upstream- und downstream-Elemente der ausgewählten Elemente). Sie können Double-Blick auf ein Objekt (Eingabe/Ausgabe-Tabelle oder Pipeline), um Eigenschaften dafür anzuzeigen.

## Schritt 5: Überwachen der Datasets und pipelines
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Ausführliche Informationen zum Verwenden von Cmdlets für die Überwachung finden Sie unter [Überwachen und Verwalten von Azure Data Factory mit PowerShell][monitor-manage-powershell].

1. Navigieren Sie zu **Azure Preview Portal** (Wenn Sie es geschlossen haben)
2. Wenn das Fenster für **ADFTutorialOnPremDF** nicht geöffnet ist, öffnen es durch Klicken auf **ADFTutorialOnPremDF** auf der **Startmenü**.
3. Sollte der **Anzahl** und **Namen** von Tabellen und der Pipeline, die Sie auf diese Blade erstellt haben.

	![Daten-Factory-Homepage][image-data-factory-homepage-2]
4. Klicken Sie nun **Datasets** Kachel.
5. Auf der **Datasets** Blade, klicken Sie auf die **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable Segmente][image-data-factory-onprem-sqltable-slices]

6. Beachten Sie, die bereits die Datensegmente bis zum aktuellen Zeitpunkt hergestellt wurden, und sie sind **bereit**. Das liegt daran, dass Sie die Daten in die SQL Server-Datenbank eingefügt haben und sie immer vorhanden sind. Vergewissern Sie sich, dass keine Segmente angezeigt der **Problem Segmente** Abschnitt unten.


	Beide **kürzlich aktualisierte Segmente** und **Slices einer vor kurzem fehlschlug** Listen sortiert die **Zeit der letzten Aktualisierung**. Der Zeitpunkt des Updates eines Segments wird in den folgenden Situationen geändert.
    

	-  Sie den Status eines Segments manuell aktualisieren, z. B. mit der **Set AzureDataFactorySliceStatus** (oder), indem Sie auf **Ausführen** auf der **SLICE** Blade für das Segment.
	-  Das Segment ändert sich der Status aufgrund einer Ausführung (z. B. eine Ausführung gestartet, eine Ausführung beendet wurde und fehlgeschlagen ist, eine Ausführung beendet wurde und erfolgreich war, usw.).
 
	Klicken Sie auf den Titel der Listen oder **... (Ellipsen)** die Liste die größere Segmente anzeigen zu können. Klicken Sie auf **Filter** auf der Symbolleiste, um Segmente zu filtern.
	
	Klicken Sie zum Anzeigen der Datenslices sortiert nach den Slice Start-/Endzeiten stattdessen **Datenslices (von Slice-Zeit)** Kachel.

7. Jetzt In der **Datasets** Blade, klicken Sie auf **OutputBlobTable**.

	![OputputBlobTable Segmente][image-data-factory-output-blobtable-slices]
8. Bestätigen Sie, dass die Segmente bis zum aktuellen Zeitpunkt erstellt werden, und **bereit**. Warten Sie, bis der Status der Segmente bis zum aktuellen Zeitpunkt, um festgelegt werden **bereit**.
9. Klicken Sie auf ein Segment der Daten aus der Liste aus, und sollte die **DATENSLICE** Blade.

	![Daten Slice-Blade][image-data-factory-dataslice-blade]

	Wenn das Segment nicht in die **bereit** Status, sehen Sie die upstream Segmente, die nicht bereit sind, und blockieren das aktuelle Segment in Ausführen der **Upstream-Segmente, die nicht bereit sind** Liste.

10. Klicken Sie auf die **Aktivität ausführen** aus der Liste unten, um finden Sie unter **Aktivität auszuführen, Details**.

	![Aktivität Testlaufdetails blade][image-data-factory-activity-run-details]

11. Klicken Sie auf **X** alle Blades schließen, bis Sie wieder an die home-Blade für den **ADFTutorialOnPremDF**.
14. (optional) Klicken Sie auf **Pipelines**, klicken Sie auf **ADFTutorialOnPremDF**, und einen Drillthrough von Eingabetabellen (** verbraucht **) oder Tabellen ausgeben (** produziert **).
15. Verwenden Sie Tools wie z. B. **Azure Storage Explorer** überprüfen Sie die Ausgabe.

	![Azure-Speicher-Explorer][image-data-factory-stroage-explorer]


## Erstellen und registrieren ein Gateway mit Azure PowerShell 
Dieser Abschnitt beschreibt das Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets.

1. Starten Sie **Azure PowerShell** im Administratormodus. 
2. Die Azure Data Factory-Cmdlets stehen in der **AzureResourceManager** Modus. Führen Sie den folgenden Befehl zum Wechseln der **AzureResourceManager** Modus.     

        switch-azuremode AzureResourceManager


2. Verwenden der **neu AzureDataFactoryGateway** Cmdlet, um eine logische Gateway wie folgt erstellen:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Beispielbefehl und Ausgabe**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. Verwenden der **neu AzureDataFactoryGatewayKey** -Cmdlet zum Generieren eines Registrierungsschlüssels für das neu erstellte Gateway und speichern Sie den Schlüssel in eine lokale Variable **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Beispielausgabe für den Befehl:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. In Azure PowerShell, wechseln Sie zum Ordner: **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript** und führen Sie **RegisterGateway.ps1** -Skript die lokale Variable zugeordnet **$Key** wie in den folgenden Befehl zum Registrieren des Client-Agents auf dem Computer mit dem logischen Gateway installiert Sie zuvor erstellen.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Können Sie die **Get-AzureDataFactoryGateway** -Cmdlet zum Abrufen der Liste der Gateways in Ihrem Unternehmen Daten. Wenn die **Status** zeigt **online**, bedeutet das Gateway ist einsatzbereit.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Entfernen Sie ein Gateway mit der **Entfernen AzureDataFactoryGateway** -Cmdlet und Update-Beschreibung für ein Gateway mit der **Set AzureDataFactoryGateway** Cmdlets. Syntax und andere Details zu diesen Cmdlets finden Sie in der Data Factory-Cmdlet-Referenz.



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
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

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=GIT-SubDir-->