<properties title="Tutorial: Move and process log files using Azure Data Factory" pageTitle="Verschieben und Verarbeiten von Protokolldateien mit Azure Data Factory" description="This advanced tutorial describes a near real-world scenario and implements the scenario using Azure Data Factory service." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory
Dieser Artikel bietet eine umfassende exemplarische Vorgehensweise eines kanonischen Szenarios der Protokollverarbeitung mithilfe von Azure Data Factory zum Transformieren von Daten aus Protokolldateien in Einblicke. 

## Szenario
Contoso ist ein Spiele-Unternehmen, das Spiele für mehrere Plattformen erstellt: Spielkonsolen, tragbare Geräte und Computer (PCs). Jedes dieser Spiele erzeugt Unmengen von Protokollen. Ziel von Contoso ist es, die von diesen Spielen produzieren Protokolle zu erfassen und zu analysieren, um Nutzungsinformationen zu erhalten, Up-Selling- und Cross-Selling-Möglichkeiten zu identifizieren, neue spannende Features zu entwickeln usw., um das Unternehmen zu verbessern und Kunden eine bessere Erfahrung zu bieten.
 
In dieser exemplarischen Vorgehensweise werden Beispielprotokolle gesammelt, verarbeitet und mit Verweisdaten angereichert; die Daten werden zur Bewertung der Wirksamkeit einer Marketingkampagne, die Contoso vor kurzem gestartet hat, transformiert.

## Vorbereitungen für das Lernprogramm
1.	Lesen Sie die [Einführung in Azure Data Factory][adfintroduction], um eine Übersicht über Azure Data Factory zu erhalten und ein Verständnis der wichtigsten Konzepte zu erlangen.
2.	Sie benötigen ein Azure-Abonnement, um dieses Lernprogramm durchzuführen. Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen] [azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
3.	Sie müssen [Azure PowerShell][download-azure-powershell] auf Ihren Computer herunterladen und installieren. 
2.	**(empfohlen)** Beschäftigen Sie sich mit dem Lernprogramm im Artikel [Erste Schritte mit Azure Data Factory][adfgetstarted], um sich mit dem Portal und den Cmdlets in einem einfachen Lernprogramm vertraut zu machen.
3.	**(empfohlen)** Beschäftigen Sie sich mit der exemplarischen Vorgehensweise im Artikel [Verwenden von Pig und Hive mit Azure Data Factory][usepigandhive], um eine exemplarische Vorgehensweise zum Erstellen einer Pipeline zu erhalten, mit der Daten von einer lokalen Datenquelle in einen Azure-BLOB-Speicher verschoben werden.
4.	Laden Sie die [ADFWalkthrough][adfwalkthrough-download]-Dateien in den Ordner **C:\ADFWalkthrough** herunter, und **behalten Sie dabei die Ordnerstruktur bei**:
	- **Pipelines:** Enthält  JSON-Dateien, die die Definition der Pipelines enthalten.
	- **Tabellen:** Enthält  JSON-Dateien, die die Definition der Tabellen enthalten.
	- **LinkedServices:** Enthält JSON-Dateien, die die Definition Ihres Speicher- und Serverclusters (HDInsight) enthalten. 
	- **Skripts:** Enthält Hive- und Pig-Skripts, die für die Verarbeitung der Daten verwendet und über die Pipelines aufgerufen werden.
	- **SampleData:** Enthält Beispieldaten für diese exemplarische Vorgehensweise.
	- **OnPremises:** Enthält JSON-Dateien und Skripts, die verwendet werden, um das Zugreifen auf Ihre lokalen Daten zu demonstrieren.
	- **AzureEnvironmentSetup.ps1:** Dies ist das PowerShell-Skript für das Einrichten der Azure-Umgebung.
	- **uploadSampleDataAndScripts.ps1:** Dieses Skript lädt die Beispieldaten und Skripts in Azure hoch.
5. Stellen Sie sicher, dass Sie die folgenden Azure-Ressourcen erstellt haben:			
	- Azure-Speicherkonto
	- Azure SQL-Datenbank
	- Azure HDInsight-Cluster, Version 3.1 oder höher	
7. Nachdem die Azure-Ressourcen erstellt wurden, stellen Sie sicher, dass Sie über die erforderlichen Informationen verfügen, um eine Verbindung zu den einzelnen Ressourcen herzustellen.
 	- **Azure-Speicherkonto** - Kontoname und Kontoschlüssel.  
	- **Azure SQL-Datenbank** - Server, Datenbank, Benutzername und Kennwort.
	- **Azure HDInsight-Cluster** - Name des HDInsight-Clusters, Benutzername, Kennwort und Kontoname sowie Kontoschlüssel für den mit diesem Cluster verbundenen Azure-Speicher.  
 8. Starten Sie **Azure PowerShell**, und navigieren Sie zu **C:\ADFWalkthrough**, und führen Sie das Vorbereitungsskript .**\AzureEnvironmentSetup.ps1** aus.
 
		Vor dem Ausführen des Skripts benötigen Sie Ihren Abonnementnamen und das dem Abonnement zugeordnete Microsoft-Konto.

		Sie müssen die Azure-Umgebung mit dem Skript oben jedes Mal einrichten, wenn Sie   Azure PowerShell starten. Die Konfiguration wird nicht sitzungsübergreifend beibehalten und muss separat ausgeführt werden, selbst wenn mehrere Sitzungen gleichzeitig verwendet werden.

		Alternativ können Sie das Cmdlet **Add-AzureAccount** verwenden, um sich bei Azure anzumelden, und mithilfe des Cmdlets **Select-AzureSubscription** ein Abonnement auswählen (wenn Sie mehrere Abonnements haben).
	

## Übersicht
Nachfolgend ist der End-to-End-Workflow dargestellt:
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. Die **PartitionGameLogsPipeline** liest die rohen Spielereignisse aus einem BLOB-Speicher (RawGameEventsTable) und erstellt basierend auf Jahr, Monat und Tag (PartitionedGameEventsTable) Partitionen.
2. The **EnrichGameLogsPipeline** verknüpft partitionierte Spielereignisse (PartitionedGameEvents-Tabelle, die eine Ausgabe von PartitionGameLogsPipeline ist) mit Geocode (RefGetoCodeDictionaryTable) und reichert die Daten durch Zuordnen einer IP-Adresse zu dem entsprechenden Geo-Standort (EnrichedGameEventsTable) an.
3. Die Pipeline **AnalyzeMarketingCampaignPipeline** nutzt die erweiterten Daten (EnrichedGameEventTable produziert von der EnrichGameLogsPipeline) und verarbeitet diese mit den Werbungsdaten (RefMarketingCampaignnTable), um die endgültige Ausgabe der Wirksamkeit der Marketingkampagne zu erstellen, die in die Azure SQL-Datenbank (MarketingCampainEffectivensessSQLTable) und einen Azure-BLOB-Speicher (MarketingCampaignEffectivenessBlobTable) für die Analyse kopiert wird.
    
## Exemplarische Vorgehensweise: Erstellen, Bereitstellen und Überwachen von Workflows
1. [Schritt 1: Hochladen von Beispieldaten und Skripts](#MainStep1). In diesem Schritt laden Sie alle Beispieldaten (einschließlich aller Protokolle und Verweisdaten) sowie Hive-/Pig-Skripts hoch, die von den Workflows ausgeführt werden. Die Skripts, die Sie ausführen, erstellen auch eine Azure SQL-Datenbank (mit dem Namen "MarketingCampaigns"), Tabellen, benutzerdefinierte Typen und gespeicherte Prozeduren.
2. [Schritt 2: Erstellen einer Azure Data Factory](#MainStep2). In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen "LogProcessingFactory".
3. [Schritt 3: Erstellen von verknüpften Diensten](#MainStep3). In diesem Schritt erstellen Sie die folgenden verknüpften Dienste: 
	
	- 	**StorageLinkedService**. Verknüpft den Azure-Speicherort, der die rohen Spielereignisse, die partitionierten Spielereignisse, erweiterte Spielereignisse, effektive Informationen für die Marketingkampagne, Verweis-Geocodedaten sowie Verweisdaten der Marketingkampagne enthält, mit der LogProcessingFactory   .
	- 	**AzureSqlLinkedService**. Verknüpft eine Azure SQL-Datenbank, die effektive Informationen für die Marketingkampagne enthält. 
	- 	**HDInsightStorageLinkedService**. Verknüpft einen Azure-BLOB-Speicher, der dem HDInsight-Cluster zugeordnet ist, auf den HDInsightLinkedService verweist. 
	- 	**HDInsightLinkedService**. Verknüpft einen Azure HDInsight-Cluster mit der LogProcessingFactory. Dieser Clusters wird verwendet, um die Pig/Hive-Verarbeitung für die Daten auszuführen. 
 		
4. [Schritt 4: Erstellen von Tabellen](#MainStep4). In diesem Schritt erstellen Sie die folgenden Tabellen:  	
	
	- **RawGameEventsTable**. Diese Tabelle gibt den Speicherort der Rohdaten der Spielereignisse im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/rawgameevents/) definiert werden. 
	- **PartitionedGameEventsTable**. Diese Tabelle gibt den Speicherort der partitionierten Spielereignisdaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/) definiert werden. 
	- **RefGeoCodeDictionaryTable**. Diese Tabelle gibt den Speicherort der Verweis-Geocodedaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/rawgameevents/) definiert werden.
	- **RefMarketingCampaignTable**. Diese Tabelle gibt den Speicherort der Verweisdaten der Marketingkampagne im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/refmarketingcampaign/) definiert werden.
	- **EnrichedGameEventsTable**. Diese Tabelle gibt den Speicherort der erweiterten Spielereignisdaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/) definiert werden.
	- **MarketingCampaignEffectivenessSQLTable**. Diese Tabelle gibt die SQL-Tabelle (MarketingCampaignEffectiveness) in the Azure SQL-Datenbank an, die durch AzureSqlLinkedService definiert wird, der die Wirksamkeitsdaten der Marketingkampagne enthält. 
	- **MarketingCampaignEffectivenessBlobTable**. Diese Tabelle gibt den Speicherort der Wirksamkeitsdaten der Marketingkampagne im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/marketingcampaigneffectiveness/) definiert werden. 

	
5. [Schritt 5: Erstellen und Planen von Pipelines](#MainStep5). In diesem Schritt erstellen Sie die folgenden Pipelines:
	- **PartitionGameLogsPipeline**. Diese Pipeline liest die rohen Spielereignisse aus einem BLOB-Speicher (RawGameEventsTable) und erstellt basierend auf Jahr, Monat und Tag (PartitionedGameEventsTable) Partitionen. 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Diese Pipeline verknüpft partitionierte Spielereignisse (PartitionedGameEvents-Tabelle, die eine Ausgabe von PartitionGameLogsPipeline ist) mit Geocode (RefGetoCodeDictionaryTable) und reichert die Daten durch Zuordnen einer IP-Adresse zu dem entsprechenden Geo-Standort (EnrichedGameEventsTable) an. 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Diese Pipeline nutzt die erweiterten Daten (EnrichedGameEventTable produziert von der EnrichGameLogsPipeline) und verarbeitet diese mit den Werbungsdaten (RefMarketingCampaignnTable), um die endgültige Ausgabe der Wirksamkeit der Marketingkampagne zu erstellen, die in die Azure SQL-Datenbank (MarketingCampainEffectivensessSQLTable) und einen Azure-BLOB-Speicher (MarketingCampaignEffectivenessBlobTable) für die Analyse kopiert wird.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Schritt 6: Überwachen von Pipelines und Datenslices](#MainStep6). In diesem Schritt überwachen Sie die Pipelines, Tabellen und Datenslices mithilfe des Azure-Portals.

### <a name="MainStep1"></a>Schritt 1: Hochladen von Beispieldaten und Skripts.
In diesem Schritt laden Sie alle Beispieldaten (einschließlich aller Protokolle und Verweisdaten) sowie Hive-/Pig-Skripts hoch, die von den Workflows ausgeführt werden. Die Skripts, die Sie ausführen, erstellen auch eine Azure SQL-Datenbank mit dem Namen **MarketingCampaigns**, Tabellen, benutzerdefinierte Typen und gespeicherte Prozeduren. 

Die Tabellen, benutzerdefinierten Typen und gespeicherten Prozeduren werden beim Verschieben der Wirksamkeitsergebnisse der Marketingkampagne aus dem Azure-BLOB-Speicher in die Azure SQL-Datenbank verschoben.

1. Öffnen Sie **uploadSampleDataAndScripts.ps1** aus dem Ordner **C:\ADFWalkthrough** (oder dem Ordner, der die extrahierten Dateien enthält) in Ihrem bevorzugten Editor, ersetzen Sie die markierten Informationen durch Ihre Cluster-Informationen, und speichern Sie die Datei.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] Dieses Skript erfordert, dass Sie das sqlcmd-Dienstprogramm auf dem Computer installiert haben. Wenn Sie SQL Server installiert haben, verfügen Sie bereits über das Dienstprogramm. Andernfalls [laden Sie das Dienstprogramm herunter][sqlcmd-install] und installieren es. 
	> Alternativ können Sie die Dateien im folgenden Ordner verwenden: C:\ADFWalkthrough\Scripts, um Pig-/Hive-Skripts und Beispieldateien in den Adfwalkthrough-Container im Blob-Speicher hochzuladen und die Tabelle "MarketingCampaignEffectiveness" in der MarketingCamapaigns-Azure-SQL-Datenbank zu erstellen.   
2. Bestätigen Sie, dass der lokale Computer auf die Azure SQL-Datenbank zugreifen darf. Um den Zugriff zu aktivieren, verwenden Sie das **Azure-Verwaltungsportal** oder **sp_set_firewall_rule** in der Masterdatenbank, um eine Firewallregel für die IP-Adresse auf dem Computer zu erstellen. Es kann bis zu fünf Minuten dauert, bis diese Änderung wirksam wird. Unter [Festlegen von Firewallregeln für Azure SQL][azure-sql-firewall] finden Sie weitere Informationen.
3. Starten Sie **Azure PowerShell**. 
4. Navigieren Sie zu dem Speicherort, an den Sie die Beispiele extrahiert haben (z. B.: **C:\ADFWalkthrough**)
5. Führen Sie **uploadSampleDataAndScripts.ps1**  aus.
6. Wenn das Skript erfolgreich ausgeführt wird, wird Folgendes angezeigt:

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>Schritt 2: Erstellen einer Azure Data Factory.
In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen **LogProcessingFactory**.

1.	Klicken Sie nach dem Anmelden beim [Azure-Vorschauportal][azure-preview-portal] in der Ecke links unten auf **NEU**, und klicken Sie auf **Data Factory** im Fenster **Neu**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Wenn **Data Factory** im Fenster **Neu** nicht angezeigt wird, führen Sie einen Bildlauf nach unten aus. 
	
5. Geben Sie im Fenster **Neue Data Factory** **LogProcessingFactory** als **Name** ein.

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6.  Wenn Sie noch keine Azure-Ressourcengruppe mit dem Namen **ADF** erstellt haben, gehen Sie folgendermaßen vor:
	1. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und klicken Sie auf **Neue Ressourcengruppe erstellen**.
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. Geben Sie im Fenster **Ressourcengruppe erstellen** **ADF** für den Namen der Ressourcengruppe ein, und klicken Sie auf **OK**.
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. Wählen Sie **ADF** für den **RESSOURCENGRUPPENNAMEN**.  
8.	Beachten Sie im Fenster **Neue Data Factory**, dass **Zum Startmenü hinzufügen** standardmäßig ausgewählt ist. Dadurch wird eine Verknüpfung zur Data Factory im Startmenü (was Sie sehen, wenn Sie sich beim Azure-Vorschauportal anmelden) hinzugefügt.

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	Klicken Sie im Fenster **Neue Data Factory** auf **Erstellen**, um die Data Factory zu erstellen.
10.	Nach der Erstellung der Data Factory sollte das Fenster**DATA FACTORY** mit dem Titel **LogProcessingFactory** angezeigt werden.

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	Wenn dies nicht angezeigt werden, führen Sie einen der folgenden Schritte aus:

	- Klicken Sie auf **LogProcessingFactory** (im **Startmenü** (Startseite)).
	- Klicken Sie links auf **DURCHSUCHEN**, klicken Sie auf **Alles**, auf **Data Factorys** und dann auf die Data Factory.
 

 
### <a name="MainStep3"></a>Schritt 3: Erstellen von verknüpften Diensten.

In diesem Schritt erstellen Sie die folgenden verknüpften Dienste: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService und HDInsightLinkedService.

1.	Klicken Sie im Fenster **LogProcessingFactory** auf die Kachel **Verknüpfte Dienste**.

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. Klicken Sie im Fenster **Verknüpfte Dienste** in der Befehlszeile auf **+ Datenspeicher**.	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. Geben Sie im Fenster **Neuer Datenspeicher** **StorageLinkedService** für den **Namen** ein, klicken Sie auf **TYP (Einstellungen erforderlich)**, und wählen Sie **Azure-Speicherkonto** aus.

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. Im Fenster **Neuer Datenspeicher** werden zwei neue Felder angezeigt: **Kontoname** und **Kontoschlüssel**. Geben Sie den Kontonamen und Kontoschlüssel für Ihr **Azure-Speicherkonto** ein.

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	Den Kontonamen und Kontoschlüssel für Ihr Azure-Speicherkonto können Sie wie folgt dargestellt aus dem Portal abrufen:

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. Nachdem Sie im Fenster des neuen Datenspeichers auf **OK** geklickt haben, wird **StorageLinkedService** in der Liste der **DATENSPEICHER** im Fenster **Verknüpfte Dienste** angezeigt. Im Hub **BENACHRICHTIGUNGEN** (links) werden Nachrichten angezeigt.

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Wiederholen Sie die **Schritte 2-5**, um einen weiteren verknüpften Dienst mit dem folgenden Namen zu erstellen: **HDInsightStorageLinkedService**. Dies ist der Speicher, der von Ihrem HDInsight-Cluster verwendet wird.
7. Vergewissern Sie sich, dass sowohl **StorageLinkedService** als auch **HDInsightStorageLinkedService** in der Liste im Fenster der verknüpften Dienste angezeigt wird.
8. Klicken Sie im Fenster **Verknüpfte Dienste** in der Befehlszeile auf **(+) Datenspeicher hinzufügen**.
9. Geben Sie **AzureSqlLinkedService** als Name ein.
10. Klicken Sie auf **TYPE (Einstellungen erforderlich)**, und wählen Sie **Azure SQL-Datenbank** aus.
11. Nun sollten die folgenden zusätzlichen Felder im Fenster **Neuer Datenspeicher** angezeigt werden. Geben Sie den Namen des Azure SQL-Datenbank**servers**, den **Datenbank**namen, den **Benutzernamen** und das **Kennwort** ein, und klicken Sie auf **OK**.
	1. Geben Sie **MarketingCampaigns** für die **Datenbank** ein. Dies ist die Azure SQL-Datenbank, die von den Skripts erstellt wurde, die Sie in Schritt 1 ausgeführt haben. Vergewissern Sie sich, dass diese Datenbank tatsächlich von den Skripts erstellt wurde (falls Fehler aufgetreten sind).
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		So rufen Sie diese Werte aus dem Azure-Verwaltungsportal ab: Klicken Sie auf "Verbindungszeichenfolgen für MarketingCampaigns-Datenbank anzeigen".

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. Vergewissern Sie sich, dass alle drei Datenspeicher angezeigt werden, die Sie erstellt haben: **StorageLinkedService**, **HDInsightStorageLinkedService** und **AzureSqlLinkedService**.
13. Sie müssen einen weiteren verknüpften Dienst erstellen, dies ist jedoch ein Serverdienst, und zwar **Azure HDInsight-Cluster**. Das Erstellen eines verknüpften Serverdiensts wird vom Portal noch nicht unterstützt. Aus diesem Grund müssen Sie Azure PowerShell verwenden, um diesen verknüpften Dienst zu erstellen. 
14. Wechseln Sie zu **Azure PowerShell**, wenn es bereits geöffnet ist, oder starten Sie **Azure PowerShell**.
15. Wechseln Sie zum Modus **AzureResourceManager**, da die Azure Data Factory-Cmdlets in diesem Modus verfügbar sind.

		Switch-AzureMode AzureResourceManager

16. Navigieren Sie zum Unterordner **LinkedServices** **C:\ADFWalkthrough** (oder) von dem Ordner an dem Speicherort aus, an dem Sie die Dateien extrahiert haben.
17. Öffnen Sie **HDInsightLinkedService.json** in Ihrem bevorzugten Editor, ersetzen Sie die markierten Informationen mit Ihren Clusterinformationen, und speichern Sie die Datei.

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. Verwenden Sie den folgenden Befehl aus, um die $df-Variable auf den Namen der Data Factory festzulegen.

		$df = "LogProcessingFactory"
19. Verwenden Sie das Cmdlet **New-AzureDataFactoryLinkedService**, um einen verknüpften Dienst wie folgt zu erstellen. Beginnen Sie mit dem Speicherkonto:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Wenn Sie einen anderen Namen für ResourceGroupName, DataFactoryName oder LinkedService verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad des verknüpften Diensts der JSON-Datei an, wenn die Datei nicht gefunden wird.
20. Alle vier verknüpften Dienste sollten im Fenster **Verknüpfte Dienste** wie unten dargestellt angezeigt werden. Wenn das Fenster der verknüpften Dienste nicht geöffnet ist, klicken Sie auf der Seite **DATA FACTORY** für **LogProcessingFactory** auf "Verknüpfte Dienste". Es dauert möglicherweise ein paar Sekunden, bis das Fenster "Verknüpfte Dienste" aktualisiert wird.

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>Schritt 4: Erstellen von Tabellen .
In diesem Schritt erstellen Sie die folgenden Tabellen: 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
In der Abbildung oben sind die Pipelines in der mittleren Zeile und die Tabellen in der oberen und unteren Zeile dargestellt. 

Das Erstellen von Datasets/Tabellen wird vom Azure-Verwaltungsportal noch nicht unterstützt, Sie müssen daher Azure PowerShell verwenden, um in dieser Version Tabellen zu erstellen.

#### So erstellen Sie die Tabellen

1.	Navigieren Sie in Azure PowerShell zum Ordner **Tabellen** (**C:\ADFWalkthrough\Tables\**) von dem Speicherort aus, an dem Sie die Beispiele extrahiert haben. 
2.	Verwenden Sie das Cmdlet **New-AzureDataFactoryTable**, um die Tabellen für **RawGameEventsTable**.json wie folgt zu erstellen.	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	Wenn Sie einen anderen Namen für ResourceGroupName und DataFactoryName verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad der Tabellen-JSON-Datei an, wenn die Datei vom Cmdlet nicht gefunden wird.

3. Wiederholen Sie den vorherigen Schritt, um die folgenden Tabellen zu erstellen:	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. Klicken Sie im **Azure-Vorschauportal** auf **Datasets** im Fenster **DATA FACTORY** für **LogProcessingFactory**, und vergewissern Sie sich, dass alle Datasets angezeigt werden (Tabellen sind rechteckige Datasets). 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	Sie können auch den folgenden Befehl in Azure PowerShell verwenden:
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>Schritt 5: Erstellen und Planen von Pipelines.
In diesem Schritt erstellen Sie die folgenden Pipelines: PartitionGameLogsPipeline, EnrichGameLogsPipeline und AnalyzeMarketingCampaignPipeline.

1. Navigieren Sie in **Windows-Explorer** zum Unterordner **Pipelines** im Ordner **C:\ADFWalkthrough** _(oder von dem Speicherort aus, an dem Sie die Beispiele extrahiert haben).
2.	Öffnen Sie **PartitionGameLogsPipeline.json** in Ihrem bevorzugten Editor, ersetzen Sie die markierten Informationen durch Ihr Speicherkonto für die Informationen des Datenspeicherkontos, und speichern Sie die Datei.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Wiederholen Sie den Schritt, um die folgenden Pipelines zu erstellen:
	1. **EnrichGameLogsPipeline**.json (3 Vorkommnisse)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 Vorkommnisse)

	**WICHTIG:** Vergewissern Sie sich, dass Sie alle <Speicherkontonamen> durch den Namen Ihres Speicherkontos ersetzt haben. 
 
4.  Navigieren Sie in **Azure PowerShell** zum Unterordner **Pipelines** im Ordner **C:\ADFWalkthrough** _(oder von dem Speicherort aus, an dem Sie die Beispiele extrahiert haben).
5.  Verwenden Sie das Cmdlet **New-AzureDataFactoryPipeline**, um die Pipelines für **PartitionGameLogspeline**.json wie folgt zu erstellen.	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	Wenn Sie einen anderen Namen für ResourceGroupName, DataFactoryName oder PipelineName verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad der Pipeline-JSON-Datei an.
6. Wiederholen Sie den vorherigen Schritt, um die folgenden Pipelines zu erstellen:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. Verwenden Sie das Cmdlet **Get-AzureDataFactoryPipeline**, um die Auflistung der Pipelines zu erhalten.
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. Sobald die Pipelines erstellt wurden, können Sie die Dauer angeben, in der die Datenverarbeitung von Daten erfolgt. Durch Angeben des aktiven Zeitraums für eine Pipeline definieren Sie die Zeitdauer, in der die Datenslices basierend auf den Verfügbarkeitseigenschaften, die für jede ADF-Tabelle definiert wurden, verarbeitet werden.

Um den aktiven Zeitraum für die Pipeline anzugeben, können Sie das Cmdlet "Set-AzureDataFactoryPipelineActivePeriod" verwenden. In dieser exemplarischen Vorgehensweise reichen die Beispieldaten von 05/01 bis 05/05. Verwenden Sie 2014-05-01 als StartDateTime. EndDateTime ist optional.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. Vergessen Sie nicht, den aktiven Zeitraum für die Pipeline festzulegen.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Wiederholen Sie die vorherigen beiden Schritte, um den aktiven Zeitraum für die folgenden Pipelines festzulegen.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. Klicken Sie im **Azure-Vorschauportal** auf die Kachel **Pipelines** (nicht auf die Namen der Pipelines) im Fenster **DATA FACTORY** für **LogProcessingFactory**. Die von Ihnen erstellten Pipelines werden angezeigt.

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. Klicken Sie im Fenster **DATA FACTORY** für **LogProcessingFactory** auf **Diagramm**.

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. Sie können das angezeigte Diagramm neu anordnen; hier sehen Sie ein neu angeordnetes Diagramm, in dem direkte Eingabe oben und Ausgaben unten angezeigt werden. Sie werden sehen, dass die Ausgabe von **PartitionGameLogsPipeline** als Eingabe für die EnrichGameLogsPipeline übernommen wird und dass die Ausgabe von **EnrichGameLogsPipeline** an die **AnalyzeMarketingCampaignPipeline** übergeben wird. Doppelklicken Sie auf einen Titel, um Details über das Artefakt anzuzeigen, das das Fenster darstellt.

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**Glückwunsch!** Sie haben die Azure Data Factory, die verknüpften Dienste, Pipelines und Tabellen erfolgreich erstellt und den Workflow gestartet. 


### <a name="MainStep6"></a>Schritt 6: Überwachen von Pipelines und Datenslices .

1.	Wenn das Fenster "DATA FACTORY" für "LogProcessingFactory" nicht geöffnet ist, können Sie einen der folgenden Schritte ausführen:
	1.	Klicken Sie auf **LogProcessingFactory** im **Startmenü**. Während der Erstellung der Data Factory wurde die Option **Zum Startmenü hinzufügen** automatisch aktiviert.

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. Klicken Sie auf den Hub**DURCHSUCHEN**, und klicken Sie auf **Alles**.
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. Wählen Sie im Fenster **Durchsuchen** **Data Factorys** und **LogProcessingFactory** im Fenster **Data Factorys** aus.

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. Sie haben mehrere Möglichkeiten, um Ihre Data Factory zu überwachen. Sie können mit Pipelines oder Datasets beginnen. Wir beginnen mit Pipelines und arbeiten uns dann weiter vor. 
3.	Klicken Sie im Fenster **DATA FACTORY** auf **Pipelines**. 
4.	Klicken Sie im Fenster "Pipelines " auf **PartitionGameLogsPipeline**. 
5.	Im Fenster **PIPELINE** für **PartitionGameLogsPipeline** sehen Sie, dass die Pipeline das Dataset **RawGameEventsTable** verbraucht.  Klicken Sie auf **RawGameEventsTable**.

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. Im Fenster "TABELLE" für **RawGameEventsTable** werden alle Slices angezeigt. Im folgenden Screenshot weisen alle Slices den Status **Bereit** auf, und es gibt keine Slices mit Problemen. Dies bedeutet, dass die Daten verarbeitet werden können.	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. Klicken Sie nun im Fenster **PIPELINE** für **PartiionGameLogsPipeline** auf **Erstellt**. 
8. Daraufhin wird die Liste der Datasets angezeigt, die diese Pipeline produziert: 
9. Klicken Sie auf die Tabelle **PartitionedGameEvents** im Fenster **Erstellte Datasets**. 
10.	Vergewissern Sie sich, dass der **Status** aller Slices auf **Bereit** festgelegt ist. 
11.	Klicken Sie auf einen der Slices mit dem Status **Bereit**, um das Fenster **DATENSLICE** für diesen Slice anzuzeigen.

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Wenn ein Fehler auftritt, wird hier der Status "Fehler" angezeigt.  Möglicherweise werden auch beide Slices mit dem Status **Bereit** oder beide Slices mit dem Status **Ausstehende Überprüfung** angezeigt, je nachdem, wie schnell die Slices verarbeitet werden.
 
	In der [Azure Data Factory-Entwicklerreferenz][developer-reference] erhalten Sie Informationen zu allen möglichen Slicestatus.

12.	Klicken Sie im Fenster **DATENSLICE** auf die Ausführung aus der Liste **Aktivitätsausführungen**. Daraufhin sollte das Fenster "Aktivitätsausführung" für diesen Slice angezeigt werden. Das folgende Fenster **DETAILS ZUR AKTIVITÄTSAUSFÜHRUNG** wird angezeigt.

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	Klicken Sie auf**Herunterladen**, um die Dateien herunterzuladen. Dieser Bildschirm ist besonders dann hilfreich, wenn Sie Fehler der HDInsight-Verarbeitung beheben. 
	 
	
Wenn die Ausführung aller Pipelines abgeschlossen ist, können Sie in der **MarketingCampaignEffectivenessTable** in der Azure SQL-Datenbank **MarketingCampaigns** die Ergebnisse sehen. 

**Glückwunsch!**Sie können jetzt die Workflows überwachen und Fehler beheben. Sie haben gelernt, wie Azure Data Factory zum Verarbeiten von Daten und Erstellen von Analysen verwendet wird.

	 

## Exemplarische Vorgehensweise: Verwenden von lokalen Daten

**(empfohlen)** Beschäftigen Sie sich mit der exemplarischen Vorgehensweise im Artikel [Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][useonpremisesdatasources], um eine exemplarische Vorgehensweise zum Erstellen einer Pipeline zum Verschieben von Daten von einem lokalen SQL Server zu einem Azure-BLOB-Speicher zu erhalten.


In dieser exemplarischen Vorgehensweise erfahren Sie, wie die Umgebung eingerichtet werden muss, damit die Pipeline mit lokalen Daten arbeiten kann.
 
Im letzten Schritt des Protokollverarbeitungszenarios aus der ersten exemplarischen Vorgehensweise mit dem Workflow "Partition -> Enrich -> Analyze" wurde die Ausgabe der Wirksamkeit der Marketingkampagne in eine Azure SQL-Datenbank kopiert. Sie können diese Daten auch in einen lokalen SQL Server für Analysen innerhalb Ihrer Organisation verschieben.
 
Um die Wirksamkeitsdaten der Marketingkampagne aus dem Azure-BLOB auf einen lokalen SQL Server zu kopieren, müssen Sie einen zusätzlichen lokalen verknüpften Dienst, eine Tabelle und eine Pipeline erstellen, die die gleichen Cmdlets wie in der ersten exemplarischen Vorgehensweise verwenden.

In diesem Lernprogramm führen Sie die folgenden Schritte aus: 

1. [Schritt 1: Erstellen eines Datenverwaltungsgateways](#OnPremStep1). Das Datenverwaltungsgateway  ist ein Client-Agent, der Zugriff auf lokale Datenquellen in Ihrer Organisation aus der Cloud bietet. Das Gateway ermöglicht die Übertragung von Daten zwischen einem lokalen SQL Server und Azure-Datenspeichern.	

	Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses auch bei Azure Data Factory registrieren, bevor Sie eine lokale SQL Server-Datenbank als verknüpften Dienst für eine Azure Data Factory hinzufügen.

2. [Schritt 2: Erstellen eines verknüpften Diensts für den lokalen SQL Server](#OnPremStep2). In diesem Schritt erstellen Sie zuerst eine Datenbank und eine Tabelle auf dem lokalen SQL Server-Computer und dann den verknüpften Dienst: **OnPremSqlLinkedService**.  
3. [Schritt 3: Erstellen der Tabelle und der Pipeline](#OnPremStep3). In diesem Schritt erstellen Sie die Tabelle **MarketingCampaignEffectivenessOnPremSQLTable** und die Pipeline **EgressDataToOnPremPipeline**. 

4. [Schritt 4: Überwachen der Pipeline und Anzeigen des Ergebnisses](#OnPremStep4). In diesem Schritt überwachen Sie die Pipelines, Tabellen und Datenslices mithilfe des Azure-Portals.


### <a name="OnPremStep1"></a>Schritt 1: Erstellen eines Datenverwaltungsgateways.

Das Datenverwaltungsgateway ist ein Client-Agent, der Zugriff auf lokale Datenquellen in Ihrer Organisation aus der Cloud bietet. Das Gateway ermöglicht die Übertragung von Daten zwischen einem lokalen SQL Server und Azure-Datenspeichern.
  
Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses auch bei Azure Data Factory registrieren, bevor Sie eine lokale SQL Server-Datenbank als verknüpften Dienst für eine Azure Data Factory hinzufügen.

Wenn Sie ein vorhandenes Datengateway haben, das Sie verwenden können, überspringen Sie diesen Schritt.

1.	Erstellen eines logischen Datengateways. Klicken Sie im **Azure-Vorschauportal im Fenster **DATA FACTORY**** auf **Verknüpfte Dienste**.
2.	Klicken Sie auf der Befehlszeile auf **(+) Datengateway hinzufügen**.  
3.	Klicken Sie im Fenster **Neues Datengateway** auf **ERSTELLEN**.
4.	Geben Sie im Fenster **Erstellen** **MyGateway** für das Datengateway ein**name**.
5.	Klicken Sie auf **REGION AUSWÄHLEN**, und ändern Sie diese, falls erforderlich. 
6.	Klicken Sie im Fenster **Erstellen** auf **OK**. 
7.	Daraufhin wird das Fenster **Konfigurieren** angezeigt. 
8.	Klicken Sie im Fenster **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Daraufhin wird das Gateway auf Ihren Computer heruntergeladen, installiert und konfiguriert und dann bei dem Dienst registriert. Wenn Sie ein vorhandenes Gateway auf dem Computer installiert haben, das Sie mit diesem logischen Gateway im Portal verknüpfen möchten, verwenden Sie den Schlüssel in diesem Fenster, um das Gateway mithilfe des Tools "Datenverwaltungsgateway-Konfigurations-Managers (Vorschau)" erneut zu registrieren.

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Klicken Sie auf **OK**, um das Fenster **Konfigurieren** zu schließen und erneut auf **OK**, um das Fenster **Erstellen** zu schließen. Warten Sie, bis sich der Status von **MyGateway** im Fenster **Verknüpfte Dienste** in **GOOD** ändert. Sie können auch das Tool **Datenverwaltungsgateway-Konfigurations-Manager (Vorschau)** starten, um zu sich zu vergewissern, dass der Name des Gateways mit dem Namen im Portal übereinstimmt und dass der **Status** **Registriert** lautet. Sie müssen möglicherweise das Fenster "Verknüpfte Dienste" schließen und erneut öffnen, um den aktuellen Status anzuzeigen. Es kann ein paar Minuten dauern, bis der Bildschirm mit dem aktuellen Status aktualisiert wird.	

### <a name="OnPremStep2"></a>Schritt 2: Erstellen eines verknüpften Diensts für den lokalen SQL Server.

In diesem Schritt erstellen Sie zuerst die erforderliche Datenbank und Tabelle auf dem lokalen SQL Server-Computer und dann den verknüpften Dienst.

#### Vorbereiten der lokalen Datenbank und Tabelle

Zunächst müssen Sie die SQL Server-Datenbank, die Tabelle, benutzerdefinierte Typen und gespeicherte Prozeduren erstellen. Diese werden zum Verschieben der Ergebnisse von **MarketingCampaignEffectiveness** aus dem Azure-BLOB in die SQL Server-Datenbank verwendet.

1.	Navigieren Sie in **Windows-Explorer** zum Unterordner **OnPremises** in **C:\ADFWalkthrough** (oder von dem Speicherort aus, an dem Sie die Beispiele extrahiert haben).
2.	Öffnen Sie **prepareOnPremDatabase&Table.ps1** in Ihrem bevorzugten Editor, ersetzen Sie die markierten Informationen durch Ihre SQL Server-Informationen, und speichern Sie die Datei (geben Sie die Details der **SQL-Authentifizierung** ein). Aktivieren Sie für das Lernprogramm die SQL-Authentifizierung für die Datenbank. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. Navigieren Sie in **Azure PowerShell** zum Ordner **C:\ADFWalkthrough\OnPremises**.
4.	Führen Sie **prepareOnPremDatabase&Table.ps1** aus **(entweder & in doppelten Anführungszeichen oder wie unten dargestellt)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### Erstellen des verknüpften Diensts

1.	Klicken Sie im **Azure-Vorschauportal** auf die Kachel **Verknüpfte Dienste** im Fenster **DATA FACTORY** für **LogProcessingFactory**.
2.	Klicken Sie im Fenster **Verknüpfte Dienste** auf **(+) Datenspeicher hinzufügen**.
3.	Geben Sie im Fenster **Neuer Datenspeicher** **OnPremSqlLinkedService** als **Name** ein. 
4.	Klicken Sie auf **Typ (Einstellungen erforderlich)**, und wählen Sie **SQL Server** aus. Jetzt sollten die Einstellungen **DATENGATEWAY**, **Server**, **Datenbank** und **ANMELDEINFORMATIONEN** im Fenster **Neuer Datenspeicher** angezeigt werden. 
5.	Klicken Sie auf **DATENGATEWAY (erforderliche Einstellungen konfigurieren)**,und wählen Sie **MyGateway** aus, das Sie zuvor erstellt haben. 
6.	Geben Sie den **Namen** des Datenbankservers ein, auf dem die **MarketingCampaigns**-Datenbank gehostet wird. 
7.	Geben Sie **MarketingCampaigns** für die Datenbank ein. 
8.	Klicken Sie auf **ANMELDEINFORMATIONEN**. 
9.	Klicken Sie im Fenster **Anmeldeinformationen** auf **Klicken Sie hier, um Anmeldeinformationen auf sichere Weise festzulegen.**.
10.	Daraufhin wird eine 1-Klick-Anwendung zum ersten Mal installiert und das Dialogfeld "Anmeldeinformationen festlegen" geöffnet. 
11.	Geben Sie im Dialogfeld **Anmeldeinformationen festlegen** den **Benutzernamen** und das **Kennwort** ein, und klicken Sie auf **OK**. Warten Sie, bis das Dialogfeld geschlossen wird. 
12.	Klicken Sie im Fenster **Neuer Datenspeicher** auf **OK**. 
13.	Vergewissern Sie sich, dass im Fenster **Verknüpfte Dienste** in der Liste **OnPremSqlLinkedService** angezeigt wird und dass der **Status** des verknüpften Diensts **Gut** lautet.

### <a name="OnPremStep3"></a>Schritt 3: Erstellen der Tabelle und der Pipeline.

#### Erstellen der lokalen logischen Tabelle

1.	Wechseln Sie in**Azure PowerShell** zum Ordner **C:\ADFWalkthrough\OnPremises**. 
2.	Verwenden Sie das Cmdlet **New-AzureDataFactoryTable**, um die Tabellen für **MarketingCampaignEffectivenessOnPremSQLTable.json** wie folgt zu erstellen.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Erstellen der Pipeline zum Kopieren der Daten aus dem Azure-BLOB auf SQL Server

1.	Verwenden Sie das Cmdlet **New-AzureDataFactoryPipeline**, um die Pipeline für **EgressDataToOnPremPipeline.json** wie folgt zu erstellen.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. Verwenden Sie das Cmdlet **Set-AzureDataFactoryPipelineActivePeriod**, um den aktiven Zeitraum für **EgressDataToOnPremPipeline** zu anzugeben.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	Drücken Sie **J**, um fortzufahren.
	
### <a name="OnPremStep4"></a>Schritt 4: Überwachen der Pipeline und Anzeigen des Ergebnisses.

Jetzt können Sie die gleichen wie die in [Schritt 6: Überwachen von Tabellen und Pipelines](#MainStep6)   eingeführten Schritte verwenden, um die neue Pipeline und die Datenslices für die neue lokale ADF-Tabelle zu überwachen.
 
Wenn Sie sehen, dass sich der Status eines Slices der Tabelle **MarketingCampaignEffectivenessOnPremSQLTable** in "Bereit" ändert, bedeutet das, dass die Pipeline die Ausführung für den Slice abgeschlossen hat. Um die Ergebnisse anzuzeigen, fragen Sie die Tabelle **MarketingCampaignEffectiveness** in der **MarketingCampaigns**-Datenbank in Ihrem SQL Server ab.
 
Glückwunsch! Sie haben die exemplarische Vorgehensweise zur Verwendung Ihrer lokalen Daten erfolgreich abgeschlossen. 
 
## Weitere Informationen

Artikel | Beschreibung
------ | ---------------
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][useonpremisesdatasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-BLOB.
[Verwenden von Pig und Hive mit Data Factory][usepigandhive] | Dieser Artikel enthält eine exemplarische Vorgehensweise, in der gezeigt wird, wie eine HDInsight-Aktivität verwendet wird, um ein Hive-/Pig-Skript zur Verarbeitung von Eingabedaten auszuführen, um Ausgabedaten zu produzieren. 
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und deren Verwendung in einer Pipeline. 
[Überwachen und Verwalten von Azure Data Factory mithilfe von PowerShell][monitor-manage-using-powershell] | In diesem Artikel wird beschrieben, wie eine Azure Data Factory mithilfe von Azure PowerShell-Cmdlets überwacht wird. Sie können die Beispiele in diesem Artikel in der ADFTutorialDataFactory ausprobieren.
[Problembehandlung für Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Probleme in Azure Data Factory behoben werden. Sie können die exemplarische Vorgehensweise in diesem Artikel in der ADFTutorialDataFactory ausprobieren, indem Sie einen Fehler einführen (z. B. eine Tabelle in der Azure SQL-Datenbank löschen). 
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw.
[Azure Data Factory-Cmdlet-Referenz][cmdlet-reference] | Diese Referenz enthält Einzelheiten zu allen Data Factory-Cmdlets. 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/de-de/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/de-de/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png
