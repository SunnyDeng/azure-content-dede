<properties 
	pageTitle="Aktivieren von Pipelines zum Arbeiten mit lokalen Daten | Microsoft Azure" 
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
	ms.date="07/10/2015" 
	ms.author="spelluru"/>

# Aktivieren von Pipelines zum Arbeiten mit lokalen Daten

Um Pipelines in einer Azure Data Factory zum Arbeiten mit einer lokalen Datenquelle zu aktivieren, müssen Sie die lokale Datenquelle als verknüpften Dienst zur Data Factory hinzufügen, indem Sie entweder das Azure-Verwaltungsportal oder Azure PowerShell verwenden.
 
Um eine lokale Datenquelle als verknüpften Dienst zur einer Data Factory hinzuzufügen, müssen Sie zunächst das Microsoft Datenverwaltungsgateway auf einen lokalen Computer herunterladen und installieren und den verknüpften Dienst für die lokale Datenquelle zur Verwendung des Gateways konfigurieren.


## <a href="DMG"></a> Datenverwaltungsgateway

Das **Datenverwaltungsgateway** ist eine Software, die lokale Datenquellen auf sichere, verwaltete Weise mit Clouddiensten verbindet. Mit dem Datenverwaltungsgateway können Sie

- **Eine Verbindung mit lokalen Daten für den hybriden Datenzugriff herstellen** – Sie können lokale Daten mit Clouddiensten verbinden, um von Clouddiensten zu profitieren, während Sie gleichzeitig den Geschäftsbetrieb mit lokalen Daten durchführen.
- **Einen sicheren Proxy definieren** – Sie können definieren, welche lokalen Datenquellen für das Datenverwaltungsgateway zur Verfügung gestellt werden, damit dieses Gateway die Datenanforderung von Clouddiensten authentifiziert und die lokalen Datenquellen schützt.
- **Ihr Gateway zur Erzielung einer vollständigen Governance verwalten** – Sie erhalten zum Sicherstellen der Verwaltung und Governance eine vollständige Überwachung und Protokollierung aller Aktivitäten innerhalb des Datenverwaltungsgateways.
- **Daten effizient verschieben** – Daten werden parallel verschoben, und zwar mit Ausfallsicherheit bei vorübergehenden Netzwerkproblemen dank automatischer Wiederholungslogik.


Das Datenverwaltungsgateway verfügt über eine Vielzahl von lokalen Datenverbindungsfunktionen, die Folgendes bieten:

- **Keine Auswirkungen auf die Unternehmensfirewall** – Das Datenverwaltungsgateway funktioniert nach der Installation, ohne dass Sie eine Firewallverbindung öffnen oder tiefgreifende Veränderungen an der Infrastruktur Ihres Unternehmensnetzwerks vornehmen müssen. 
- **Anmeldeinformationen mit Ihrem Zertifikat verschlüsseln** – Anmeldeinformationen zum Verbinden mit Datenquellen werden mit einem Zertifikat verschlüsselt,dass sich im vollständigen Besitz eines Benutzers befindet. Ohne das Zertifikat kann niemand die Anmeldeinformationen in Klartext entschlüsseln, einschließlich Microsoft.

### Überlegungen zur Verwendung des Datenverwaltungsgateways
1.	Eine einzelne Instanz des Datenverwaltungsgateways kann für mehrere lokale Datenquellen verwendet werden. Denken Sie aber daran, dass ein **Gateway an eine Azure Data Factory gebunden ist** und nicht mit einer anderen Data Factory gemeinsam genutzt werden kann.
2.	Es darf **nur eine Instanz des Datenverwaltungsgateways** auf Ihrem Computer installiert sein. Angenommen, Sie haben zwei Data Factorys, die Zugriff auf lokale Datenquellen benötigen, dann müssen Sie die Gateways auf zwei lokalen Computern installieren, wobei jedes Gateway an eine separate Daten Factory gebunden ist.
3.	Das **Gateway muss sich nicht auf dem gleichen Computer wie die Datenquelle befinden**, aber die Nähe zur Datenquelle verkürzt die Verbindungszeit des Gateways mit der Datenquelle. Es wird empfohlen, das Gateway auf einem anderen Computer als dem Computer zu installieren, auf dem die lokale Datenquelle gehostet wird. So konkurriert das Gateway nicht mit der Datenquelle um Ressourcen.
4.	Sie können über **mehrere Gateways auf verschiedenen Computern verfügen, die eine Verbindung mit der gleichen lokalen Datenquelle herstellen**. Sie können z. B. über zwei Gateways verfügen, die zwei Data Factorys mit Daten versorgen, wobei jedoch die lokale Datenquelle für die beiden Data Factorys registriert ist. 
5.	Wenn Sie bereits ein Gateway auf dem Computer installiert haben, das ein **Power BI**-Szenario unterstützt, installieren Sie auf einem anderen Computer ein **separates Gateway für die Azure Data Factory**.

### Ports und Sicherheitsaspekte 
- Das Installationsprogramm für das Datenverwaltungsgateway öffnet auf dem Gatewaycomputer die Ports **8050** und **8051**. Diese Ports werden vom **Anmeldeinformations-Manager** \(ClickOnce-Anwendung\) verwendet. Dadurch können Sie Anmeldeinformationen für einen lokal verknüpften Dienst festlegen und die Verbindung mit der Datenquelle testen. Diese Ports können nicht aus dem Internet erreicht werden, und Sie müssen sie nicht in der Unternehmensfirewall öffnen.
- Stellen Sie beim Kopieren von Daten zwischen einer lokalen SQL Server-Datenbank und einer Azure SQL-Datenbank Folgendes sicher:
 
	- Die Firewall auf dem Gatewaycomputer lässt die ausgehende TCP-Kommunikation über **TCP**-Port **1433** zu.
	- Die [Azure SQL-Firewalleinstellungen](https://msdn.microsoft.com/library/azure/jj553530.aspx) müssen so konfiguriert sein, dass die **IP-Adresse des Gatewaycomputers** den **zulässigen IP-Adressen** hinzugefügt wird.

- Wenn Sie Daten zwischen einer lokalen SQL Server-Datenbank und beliebigen Zielen kopieren und der Gateway- und die SQL Server-Computer sich unterscheiden, gehen Sie folgendermaßen vor: [Konfigurieren Sie die Windows-Firewall](https://msdn.microsoft.com/library/ms175043.aspx) auf dem SQL Server-Computer so, dass das Gateway auf die Datenbank über Ports zugreifen kann, die von der SQL Server-Instanz überwacht werden. Für die Standardinstanz ist dies Port 1433.

- Sie müssen die Anwendung **Anmeldeinformations-Manager** auf einem Computer starten, der sich mit dem Datenverwaltungsgateway mit dem Ziel verbinden kann, Anmeldeinformationen für die Datenquelle festzulegen und die Verbindung mit der Datenquelle zu testen.

### Gatewayinstallation – Voraussetzungen 

1.	Die unterstützten **Betriebssystemversionen** sind Windows 7, Windows 8/8.1, Windows Server 2008 R2 und Windows Server 2012.
2.	Die empfohlene **Konfiguration** für den Gatewaycomputer lautet: mindestens 2 GHz, 4 Kerne, 8 GB RAM und 80 GB Festplattenspeicher.
3.	Wenn der Hostcomputer in den Ruhezustand wechselt, kann das Gateway nicht auf Datenanforderungen reagieren. Aus diesem Grund sollten Sie vor der Installation des Gateways einen entsprechenden **Energiesparplan** auf dem Computer konfigurieren. Bei der Gatewayinstallation wird eine Meldung angezeigt, wenn der Computer für den Ruhezustand konfiguriert ist.  

Das Datenverwaltungsgateway serialisiert und deserialisiert die Quell- und Senkendaten auf dem Computer, auf dem sie gehostet werden. Es führt außerdem Typkonvertierungen für die Daten während des Kopiervorgangs aus. Während des Kopiervorgangs liest das Gateway Daten aus der Quelle in einen Arbeitsspeicherpuffer. Gleichzeitig schreibt ein anderer Schreibthread den Puffer in die Senke. Da auf dem Gatewayhostcomputer während der Spitzenzeiten mehrere Aufträge für Kopieraktivitäten gleichzeitig ausgeführt werden können, ist die Nutzung von Arbeitsspeicher- und CPU-Ressourcen wesentlich höher als in Ruhezeiten. Daher benötigt ein Hostcomputer mit einem Datenverwaltungsgateway möglicherweise mehr Ressourcen, als oben in der vorgeschlagenen Mindestkonfiguration für den Computer angegeben ist. Während Ruhezeiten werden unter Umständen weniger Ressourcen benötigt.


## Exemplarische Vorgehensweise

In dieser exemplarischen Vorgehensweise erstellen Sie eine Data Factory mit einer Pipeline, die Daten aus einer lokalen SQL Server-Datenbank in einen Azure-Blob kopiert.

## Schritt 1: Erstellen einer Azure Data Factory
In diesem Schritt verwenden Sie das Azure-Verwaltungsportal zum Erstellen einer Azure Data Factory-Instanz mit dem Namen **ADFTutorialOnPremDF**. Sie können auch eine Data Factory mithilfe von Azure Data Factory-Cmdlets erstellen.

1.	Nach der Anmeldung beim [Azure-Vorschauportal][azure-preview-portal] klicken Sie links unten auf **NEU**, dann auf **Datenanalyse** auf dem Blatt **Erstellen** und anschließend auf **Data Factory** auf dem Blatt **Datenanalyse**.

	![Neu-\>DataFactory][image-data-factory-new-datafactory-menu]
  
6. Gehen Sie auf dem Blatt **Neue Data Factory** wie folgt vor:
	1. Geben Sie **ADFTutorialOnPremDF** als **Namen** ein.
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und wählen Sie **ADFTutorialResourceGroup** aus \(wenn Sie das Lernprogramm unter [Erste Schritte mit Azure Data Factory][adfgetstarted] ausgeführt haben\). Sie können eine vorhandene Ressourcengruppe auswählen oder eine neue erstellen. So erstellen Sie eine neue Ressourcengruppe:
		1. Klicken Sie auf **Neue Ressourcengruppe erstellen**.
		2. Geben Sie auf dem Blatt **Ressourcengruppe erstellen** unter **Name** einen Namen für die Ressourcengruppe ein, und klicken Sie auf **OK**.

7. Auf dem Blatt **Neue Data Factory** ist **Zum Startmenü hinzufügen** aktiviert.

	![Zum Startmenü hinzufügen][image-data-factory-add-to-startboard]

8. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

	Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data factory name "ADFTutorialOnPremDF" is not available** ändern Sie den Namen der Data Factory \(z. B.in "IhrNameADFTutorialOnPremDF"\) und wiederholen den Vorgang. Verwenden Sie diesen Namen beim Ausführen der restlichen Schritte in diesem Lernprogramm anstelle von "ADFTutorialOnPremDF".

9. Suchen Sie im Hub **BENACHRICHTIGUNGEN** auf der linken Seite nach Benachrichtigungen zum Erstellungsvorgang. Klicken Sie auf **X**, um das Blatt **BENACHRICHTIGUNGEN** zu schließen, falls es geöffnet ist.

	![Hub BENACHRICHTIGUNGEN][image-data-factory-notifications-hub]

11. Nach Abschluss der Erstellung wird das Blatt **DATA FACTORY** wie unten dargestellt angezeigt:

	![Data Factory-Startseite][image-data-factory-datafactory-homepage]

## Schritt 2: Erstellen eines Datenverwaltungsgateways
5.	Klicken Sie auf dem Blatt **Data Factory** für **ADFTutorialOnPremDF** auf **Verknüpfte Dienste**. 

	![Data Factory-Startseite][image-data-factory-home-age]

2.	Klicken Sie auf dem Blatt **Verknüpfte Dienste** auf **Neues Datengateway**.

	![Verknüpfte Dienste – Schaltfläche "Gateway hinzufügen"][image-data-factory-linkedservices-add-gateway-button]

2. Geben Sie auf dem Blatt **Erstellen** den Namen **adftutorialgateway** unter **Name** ein, und klicken Sie auf **OK**.

	![Blatt "Gateway erstellen"][image-data-factory-create-gateway-blade]

3. Klicken Sie auf dem Blatt **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Daraufhin wird das Installationspaket für das Gateway heruntergeladen, installiert, konfiguriert und das Gateway auf dem Computer registriert.

	> [AZURE.NOTE]Hierfür ist Internet Explorer oder ein mit Microsoft ClickOnce kompatibler Webbrowser erforderlich.

	![Blatt "Gateway konfigurieren"][image-data-factory-gateway-configure-blade]

	Dies ist die einfachste Möglichkeit \(One-Click\) zum Herunterladen, Installieren, Konfigurieren und Registrieren des Gateways in einem einzigen Schritt. Sie können sehen, dass die Anwendung **Microsoft Datenverwaltungsgateway – Konfigurations-Manager** auf Ihrem Computer installiert ist. Die ausführbare Datei **ConfigManager.exe** befindet sich im Ordner **C:\\Programme\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Sie können das Gateway auch manuell über die Links in diesem Fenster herunterladen und installieren und mit dem Schlüssel im Textfeld **MIT SCHLÜSSEL REGISTRIEREN** registrieren.
	
	Im Abschnitt [Datenverwaltungsgateway](#DMG) finden Sie ausführliche Informationen zum Gateway mit bewährten Methoden und wichtigen Hinweisen.

	>[AZURE.NOTE]Sie müssen ein Administrator auf dem lokalen Computer sein, um das Datenverwaltungsgateway erfolgreich installieren und konfigurieren zu können. Sie können der lokalen Windows-Gruppe "Datenverwaltungsgateway-Benutzer" zusätzliche Benutzer hinzufügen. Die Mitglieder dieser Gruppe können den Datenverwaltungsgateway-Konfigurations-Manager verwenden, um das Gateway zu konfigurieren.

4. Klicken Sie auf der linken Seite auf den Hub **BENACHRICHTIGUNGEN**. Warten Sie, bis die Meldung **Express-Setup für "adftutorialgateway" erfolgreich** auf dem Blatt **Benachrichtigungen** angezeigt wird.

	![Express-Installation erfolgreich][express-setup-succeeded]
5. Klicken Sie auf dem Blatt **Erstellen** auf **OK** und dann auf das Blatt **Neues Datenverwaltungsgateway**.
6. Schließen Sie das Blatt **Verknüpfte Dienste** \(durch Klicken auf **X** rechts oben\), und öffnen Sie erneut das Blatt **Verknüpfte Dienste**, um den aktuellen Status des Gateways anzuzeigen. 
7. Überprüfen Sie, ob der **Status** des Gateways **Online** lautet. 

	![Gatewaystatus][gateway-status]
5. Starten Sie den **Microsoft Datenverwaltungsgateway-Konfigurations-Manager** auf Ihrem Computer.

	![Gatewaykonfigurations-Manager][image-data-factory-gateway-configuration-manager]

6. Warten Sie, bis die Werte wie folgt festgelegt sind:
	1. Wenn der **Status** des Diensts nicht auf **Gestartet** festgelegt ist, klicken Sie auf **Dienst starten**, um den Dienst zu starten. Warten Sie eine Minute, bis die anderen Felder aktualisiert wurden.
	2. **Gatewayname** wird auf **adftutorialgateway** festgelegt.
	3. **Instanzname** wird auf **adftutorialgateway** festgelegt.
	4. **Gateway-Schlüsselstatus** wird auf **Registriert** festgelegt.
	5. Die Statusleiste im unteren Bereich zeigt **Mit Datenverwaltungsgateway-Clouddienst verbunden** sowie ein **grünes Häkchen** an.
	
7. Vergewissern Sie sich auf dem Blatt **Verknüpfte Dienste**, dass der **Status** des Gateways **Gut** lautet.
8. Schließen Sie alle Blätter, bis Sie zur Startseite **Data Factory** gelangen. 

## Schritt 2: Erstellen von verknüpften Diensten 
In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **SqlServerLinkedService**. Der **SqlServerLinkedService** verknüpft eine lokale SQL Server-Datenbank, und der verknüpfte Dienst **StorageLinkedService** verknüpft einen Azure-Blobspeicher mit der **ADFTutorialDataFactory**. Sie erstellen eine Pipeline weiter unten in dieser exemplarischen Vorgehensweise, die Daten aus der lokalen SQL Server-Datenbank in den Azure-Blobspeicher kopiert.

### Hinzufügen eines verknüpften Diensts zu einer lokalen SQL Server-Datenbank
1.	Klicken Sie auf dem Blatt **DATA FACTORY** auf die Kachel **Erstellen und bereitstellen**, um den **Editor** für die Data Factory zu starten.

	![Kachel "Erstellen und bereitstellen"][image-author-deploy-tile]

	Im Thema [Data Factory-Editor][data-factory-editor] finden Sie eine ausführliche Übersicht über den Data Factory-Editor.

2.	Klicken Sie im **Editor** auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü **Lokale SQL Server-Datenbank** aus.

	![Editor – Schaltfläche "Neuer Datenspeicher"][image-editor-newdatastore-onpremsql-button]
    
3.	Die JSON-Vorlage zum Erstellen eines mit einer lokalen SQL Server-Datenbank verknüpften Diensts sollte im rechten Bereich angezeigt werden. ![Mit lokalem SQL Server verknüpfter Dienst – Einstellungen][image-editor-newdatastore-onpremsql-settings]

4.	Führen Sie im JSON-Bereich folgende Schritte aus:
	1.	Geben Sie **adftutorialgateway** für die **gatewayName**-Eigenschaft ein, um den gesamten Text in doppelten Anführungszeichen zu ersetzen.  
	2.	Bei Verwendung der **SQL Server-Authentifizierung**: 
		1.	Ersetzen Sie in der **connectionString**-Eigenschaft **\<servername\>**, **\<databasename\>**, **\<username\>** und **\<password\>** durch die Angaben für "Server", "Datenbank", "Benutzerkonto" und "Kennwort" Ihres lokalen SQL Server-Computers. Verwenden Sie zum Angeben eines Instanznamens Escapezeichen: . Beispiel: **Server\\Instanzname**. 	
		2.	Entfernen Sie die letzten beiden Eigenschaften \(**username** und **password**\) aus der JSON-Datei und das **Komma \(,\)** am Ende der letzten Zeile des verbleibenden JSON-Skripts.
		
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
				      "gatewayName": "adftutorialgateway"
				    }
				  }
				}
	3.	Bei Verwendung der **Windows-Authentifizierung:**:
		1. Ersetzen Sie in der **connectionString**-Eigenschaft **\<servername\>** und **\<databasename\>** durch die Angaben für den lokalen SQL Server-Computer und die Datenbank. Legen Sie **Integrierte Sicherheit** auf **Wahr** fest. Entfernen Sie **ID** und **Kennwort** aus der Verbindungszeichenfolge.
			
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
				      "gatewayName": "adftutorialgateway",
				      "username": "<Specify user name if you are using Windows Authentication>",
				      "password": "<Specify password for the user account>"
				    }
				  }
				}		
		
6. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst "SqlServerLinkedService" bereitzustellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTER DIENST ERFOLGREICH ERSTELLT** auf der Titelleiste des Editors angezeigt wird. Außerdem sollte **SqlServerLinkedService** in der Strukturansicht auf der linken Seite angezeigt werden.
		   
	![SqlServerLinkedService erfolgreich bereitgestellt][image-editor-sql-linked-service-successful]
	
  
Sie können auch einen verknüpften SQL Server-Dienst erstellen, indem Sie auf dem Blatt **Verknüpfte Dienste** auf die Symbolleistenschaltfläche **Neuer Datenspeicher** klicken. Hierbei legen Sie die Anmeldeinformationen für die Datenquelle mithilfe der ClickOnce-Anwendung "Anmeldeinformations-Manager" fest, die auf dem Computer ausgeführt wird, mit dem auf das Portal zugegriffen wird. Wenn Sie auf das Portal auf einem Computer zugreifen, der sich vom Computer mit dem Gateway unterscheidet, müssen Sie sicherstellen, dass die Anwendung "Anmeldeinformations-Manager" eine Verbindung mit dem Gatewaycomputer herstellen kann. Wenn die Anwendung den Computer mit dem Gateway nicht erreichen kann, können Sie keine Anmeldeinformationen für die Datenquelle festlegen und die Verbindung mit der Datenquelle nicht testen.

#### Hinzufügen eines verknüpften Diensts für Azure-Speicherkonten
 
1. Klicken Sie im **Editor** auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü **Azure-Speicher** aus. Die JSON-Vorlage zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im rechten Bereich angezeigt werden. 

	![Editor – Schaltfläche "Neuer Datenspeicher"][image-editor-newdatastore-button]
    
6. Ersetzen Sie **\<accountname\>** und **\<accountkey\>** durch die Werte für den Kontonamen und -schlüssel Ihres Azure-Speicherkontos.

	![Editor – Blobspeicher – JSON][image-editor-blob-storage-json]
	
	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst „StorageLinkedService“ bereitzustellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTER DIENST ERFOLGREICH ERSTELLT** auf der Titelleiste des Editors angezeigt wird.

	![Editor – Blobspeicher bereitstellen][image-editor-blob-storage-deploy]

 
## Schritt 3: Erstellen von Eingabe- und Ausgabedatasets
In diesem Schritt erstellen Sie die Eingabe- und Ausgabedatasets, die ein- und ausgehende Daten für den Kopiervorgang darstellen \(lokale SQL Server-Datenbank = \> Azure-Blobspeicher\). Vor dem Erstellen von Datasets oder Tabellen \(rechteckige Datasets\) müssen Sie Folgendes ausführen \(detaillierte Schritte in der Liste\):

- Erstellen Sie eine Tabelle mit dem Namen **emp** in der SQL Server-Datenbank, die Sie der Data Factory als verknüpften Dienst hinzugefügt haben, und fügen Sie verschiedene Beispieleinträge in die Tabelle ein.
- - Wenn Sie das Lernprogramm [Erste Schritte mit Azure Data Factory][adfgetstarted] noch nicht durchgeführt haben, erstellen Sie einen Blobcontainer mit dem Namen **adftutorial** im Azure-Blobspeicherkonto, das Sie der Data Factory als verknüpften Dienst hinzugefügt haben.

### Vorbereitung des lokalen SQL Servers für das Lernprogramm

1. Verwenden Sie in der Datenbank, die Sie für den lokalen verknüpften SQL Server-Dienst \(**SqlServerLinkedService**\) angegeben haben, das folgende SQL-Skript zum Erstellen der Tabelle **emp** in der Datenbank.


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

1.	Klicken Sie im **Data Factory-Editor** auf der Symbolleiste auf **Neues Dataset**, und wählen Sie **Lokaler SQL Server**. 
2.	Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text:    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	Beachten Sie Folgendes:
	
	- **type** ist auf **SqlServerTable** festgelegt.
	- **tableName** ist auf **emp** festgelegt.
	- **linkedServiceName** ist auf **SqlServerLinkedService** festgelegt \(diesen verknüpften Dienst haben Sie in Schritt 2 erstellt\).
	- Für eine Eingabetabelle, die nicht durch eine andere Pipeline in Azure Data Factory generiert wird, müssen Sie **external** auf **true** festlegen. Optional können Sie im Abschnitt **externalData** Richtlinien angeben.   

	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen \(eine Tabelle ist ein rechteckiges Dataset\). Vergewissern Sie sich, dass auf der Titelleiste **TABELLE ERFOLGREICH BEREITGESTELLT** angezeigt wird.


### Erstellen der Ausgabetabelle

1.	Klicken Sie im **Data Factory-Editor** auf der Symbolleiste auf **Neues Dataset** und dann auf **Azure-Blobspeicher**.
2.	Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text: 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	Beachten Sie Folgendes:
	
	- **type** ist auf **AzureBlob** festgelegt.
	- **linkedServiceName** ist auf **StorageLinkedService** festgelegt \(diesen verknüpften Dienst haben Sie in Schritt 2 erstellt\).
	- **folderPath** ist auf **adftutorial/outfromonpremdf** festgelegt, wobei "outfromonpremdf" der Ordner im "adftutorial"-Container ist. Sie müssen lediglich den **adftutorial**-Container erstellen.
	- Die Verfügbarkeit \(**availability**\) ist auf **hourly** festgelegt \(**frequency** auf **hour** und **interval** auf **1**\). Der Data Factory-Dienst generiert in der Tabelle **emp** in der Azure SQL-Datenbank stündlich einen Ausgabedatenslice. 

	Wenn Sie keinen **fileName** für eine **Eingabetabelle** angeben, werden alle Dateien/Blobs aus dem Eingabeordner \(**folderPath**\) als Eingaben angesehen. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Beispiele finden Sie in den Beispieldateien des [Lernprogramms][adf-tutorial].
 
	Wenn Sie keinen **fileName** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt \(Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt\).

	Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die "partitionedBy"-Eigenschaft. Im folgenden Beispiel verwendet folderPath die Angaben für Jahr, Monat und Tag aus "SliceStart" \(Startzeit des zu verarbeitenden Slices\) und "fileName" die Angabe für Stunde aus "SliceStart". Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

2.	Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen \(eine Tabelle ist ein rechteckiges Dataset\). Vergewissern Sie sich, dass auf der Titelleiste **TABELLE ERFOLGREICH BEREITGESTELLT** angezeigt wird.
  

## Schritt 4: Erstellen und Ausführen einer Pipeline
In diesem Schritt erstellen Sie eine **Pipeline** mit einer **Kopieraktivität**, die **EmpOnPremSQLTable** als Eingabe und **OutputBlobTable** als Ausgabe verwendet.

1.	Klicken Sie auf der Befehlsleiste auf **Neue Pipeline**. Wenn die Schaltfläche nicht angezeigt wird, klicken Sie auf **... \(Auslassungspunkte\)**, um die Befehlsleiste zu erweitern.
2.	Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text:   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
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
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}
	Beachten Sie Folgendes:
 
	- Der Abschnitt "activities" enthält nur eine Aktivität, deren **type** auf **Copy** festgelegt ist.
	- **Input** für die Aktivität ist auf **EmpOnPremSQLTable** und **output** auf **OutputBlobTable** festgelegt.
	- Im Abschnitt **transformation** ist **SqlSource** als **Quelltyp** und **BlobSink** als **Senkentyp** angegeben.
- Die SQL-Abfrage **select * from emp** ist für die **sqlReaderQuery**-Eigenschaft von **SqlSource** angegeben.

	Ersetzen Sie den Wert der **start**-Eigenschaft durch den aktuellen Tag und den Wert der **end**-Eigenschaft durch den nächsten Tag. Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Angabe für **end** ist optional, wird aber in diesem Lernprogramm verwendet.
	
	Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als **start + 48 Stunden** berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9/9/9999** an.
	
	Durch Angabe des aktiven Zeitraums für eine Pipeline definieren Sie die Dauer, für die die Datenslices verarbeitet werden, basierend auf den **Verfügbarkeitseigenschaften**, die für jede Azure Data Factory-Tabelle definiert wurden.
	
	Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen \(eine Tabelle ist ein rechteckiges Dataset\). Vergewissern Sie sich, dass auf der Titelleiste **PIPELINE ERFOLGREICH BEREITGESTELLT** angezeigt wird.
5. Schließen Sie jetzt das Blatt **Editor**, indem Sie auf **X** klicken. Klicken Sie erneut auf **X**, um das Blatt "ADFTutorialDataFactory" mit der Symbolleiste und Strukturansicht zu schließen. Wenn die Meldung **Ihre nicht gespeicherten Änderungen werden verworfen** angezeigt wird, klicken Sie auf **OK**.
6. Sie sollten sich nun wieder auf dem Blatt **DATA FACTORY** für **ADFTutorialOnPremDF** befinden.

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.

### Anzeigen einer Diagrammansicht der Data Factory 
1. Klicken Sie im **Azure-Vorschauportal** auf der Startseite für die Data Factory **ADFTutorialOnPremDF** auf die Kachel **Diagramm**.

	![Link "Diagramm"][image-data-factory-diagram-link]

2. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden:

	![Diagrammansicht][image-data-factory-diagram-view]

	Sie können die Ansicht vergrößern, verkleinern, auf 100 % anpassen, an die Fenstergröße anpassen, Pipelines und Tabellen automatisch positionieren und Informationen zur Datenherkunft anzeigen \(d. h. vor- und nachgelagerte Elemente ausgewählter Elemente hervorheben\). Sie können auf ein Objekt \(in der Ein-/Ausgabetabelle oder Pipeline\) doppelklicken, um seine Eigenschaften anzuzeigen.

## Schritt 5: Überwachen der Datasets und Pipelines
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Einzelheiten zur Verwendung von Cmdlets für die Überwachung finden Sie unter [Überwachen und Verwalten von Azure Data Factory mit PowerShell][monitor-manage-powershell].

1. Navigieren Sie zum **Azure-Vorschauportal** \(falls Sie es geschlossen haben\).
2. Wenn das Blatt für **ADFTutorialOnPremDF** nicht geöffnet ist, klicken Sie im **Startmenü** zum Öffnen auf **ADFTutorialOnPremDF**.
3. Auf diesem Blatt sollten die **Anzahl** und **Namen** der erstellten Tabellen und der Pipeline angezeigt werden.

	![Data Factory-Startseite][image-data-factory-homepage-2]
4. Klicken Sie nun auf die Kachel **Datasets**.
5. Klicken Sie auf dem Blatt **Datasets** auf **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable-Slices][image-data-factory-onprem-sqltable-slices]

6. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Das liegt daran, dass Sie die Daten in die SQL Server-Datenbank eingefügt haben und sie immer vorhanden sind. Überprüfen Sie, ob keine Slices im Abschnitt **Problemslices** am unteren Rand angezeigt werden.


	Die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** werden anhand der **UHRZEIT DER LETZTEN AKTUALISIERUNG** sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.
    

	-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set AzureDataFactorySliceStatus** oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
	-  Der Status des Slices ändert sich aufgrund einer Ausführung \(z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.\).
 
	Klicken Sie auf den Titel der Listen oder auf **...** \(Auslassungspunkte\), um eine umfangreichere Liste mit Slices anzuzeigen. Klicken Sie auf der Symbolleiste auf **Filter**, um die Slices zu filtern.
	
	Zum Anzeigen der nach Start-/Endzeit sortierten Datenslices klicken Sie auf die Kachel **Datenslices \(nach Slicezeit\)**.

7. Klicken Sie jetzt auf dem Blatt **Datasets** auf **OutputBlobTable**.

	![OutputBlobTable-Slices][image-data-factory-output-blobtable-slices]
8. Überprüfen Sie, ob die Slices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Warten Sie, bis der Status der Slices bis zum aktuellen Zeitpunkt auf **Bereit** festgelegt ist.
9. Klicken Sie auf einen beliebigen Datenslice in der Liste. Das Blatt **DATENSLICE** sollte angezeigt werden.

	![Blatt "Datenslice"][image-data-factory-dataslice-blade]

	Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slices blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**.

10. Klicken Sie in der Liste unten auf die **Aktivitätsausführung**, um **Aktivitätsausführung – Details** anzuzeigen.

	![Blatt "Aktivitätsausführung – Details"][image-data-factory-activity-run-details]

11. Klicken Sie auf **X**, um alle Fenster zu schließen, bis Sie sich wieder im Startfenster für **ADFTutorialOnPremDF** befinden.
14. \(Optional\) Klicken Sie auf **Pipelines**, dann auf **ADFTutorialOnPremDF**, und zeigen Sie Details zu den Eingabetabellen \(**Genutzt**\) oder Ausgabetabellen \(**Erstellt**\) an.
15. Verwenden Sie Tools wie z. B. **Azure Storage-Explorer**, um die Ausgabe zu überprüfen.

	![Azure Storage-Explorer][image-data-factory-stroage-explorer]


## Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets 
Dieser Abschnitt beschreibt das Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets.

1. Starten Sie **Azure PowerShell** im Administratormodus. 
2. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager**-Modus verfügbar. Führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln.     

        switch-azuremode AzureResourceManager


2. Verwenden Sie das **New-AzureDataFactoryGateway**-Cmdlet, um wie folgt ein logisches Gateway zu erstellen:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Beispiel für eine Befehlszeile und Ausgabe**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. Verwenden Sie das **New-AzureDataFactoryGatewayKey**-Cmdlet, um einen Registrierungsschlüssel für das neu erstellte Gateway zu generieren, und speichern Sie den Schlüssel in der lokalen Variablen **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Beispiel für eine Befehlszeilenausgabe:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Wechseln Sie in Azure PowerShell zum Ordner **C:\\Programme\\Microsoft Data Management Gateway\\1.0\\PowerShellScript\\**, und führen Sie das Skript **RegisterGateway.ps1** aus, das mit der lokalen Variablen **$Key** verknüpft ist, wie im folgenden Befehl dargestellt. Sie registrieren damit den Client-Agent auf dem Computer mit dem logischen Gateway, das Sie zuvor erstellt haben.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Mit dem **Get-AzureDataFactoryGateway**-Cmdlet können Sie die Liste mit Gateways in Ihrer Data Factory abrufen. Wenn **online** als **Status** angezeigt wird, bedeutet dies, dass Ihr Gateway einsatzbereit ist.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Sie können ein Gateway mit dem **Remove-AzureDataFactoryGateway**-Cmdlet entfernen und die Beschreibung für das Gateway mithilfe der **Set-AzureDataFactoryGateway**-Cmdlets aktualisieren. Syntax und andere Details zu diesen Cmdlets finden Sie in der Data Factory-Cmdlet-Referenz.



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

<!---HONumber=August15_HO7-->