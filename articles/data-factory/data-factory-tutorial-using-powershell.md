<properties 
	pageTitle="Verschieben und Verarbeiten von Protokolldateien mit Azure Data Factory (Azure PowerShell)" 
	description="In diesem fortgeschrittenen Tutorial wird ein sehr praxisnahes Szenario beschrieben und mithilfe des Azure Data Factory-Diensts und Azure PowerShell implementiert." 
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

# Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory [PowerShell]
Dieser Artikel bietet eine umfassende exemplarische Vorgehensweise eines kanonischen Szenarios der Protokollverarbeitung mithilfe von Azure Data Factory zum Transformieren von Daten aus Protokolldateien in Einblicke.

## Szenario
Contoso ist ein Spielehersteller, der Spiele für mehrere Plattformen entwickelt. Hierzu zählen Spielekonsolen, mobile Geräte und PCs. Jedes dieser Spiele erzeugt Unmengen von Protokollen. Ziel von Contoso ist es, die von diesen Spielen produzieren Protokolle zu erfassen und zu analysieren, um Nutzungsinformationen zu erhalten, Up-Selling- und Cross-Selling-Möglichkeiten zu identifizieren, neue spannende Features zu entwickeln usw., um das Unternehmen zu verbessern und Kunden eine bessere Erfahrung zu bieten.
 
In dieser exemplarischen Vorgehensweise werden Beispielprotokolle gesammelt, verarbeitet und mit Verweisdaten angereichert; die Daten werden zur Bewertung der Wirksamkeit einer Marketingkampagne, die Contoso vor kurzem gestartet hat, transformiert.

## Vorbereitungen für das Lernprogramm
1.	Lesen Sie die [Einführung in Azure Data Factory][adfintroduction], um sich einen Überblick über Azure Data Factory zu verschaffen und sich mit den wichtigsten Konzepten vertraut zu machen.
2.	Sie benötigen ein Azure-Abonnement, um dieses Lernprogramm durchzuführen. Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
3.	Sie müssen [Azure PowerShell][download-azure-powershell] auf Ihren Computer herunterladen und installieren.

	In diesem Artikel werden nicht alle Data Factory-Cmdlets behandelt. In der [Data Factory-Cmdlet-Referenz](https://msdn.microsoft.com/library/dn820234.aspx) finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.
    
	1. Führen Sie **Add-AzureAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben.
	2. Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	3. Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
	
	Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.


2.	**Empfohlen:** Beschäftigen Sie sich mit dem Lernprogramm im Artikel [Erste Schritte mit Azure Data Factory][adfgetstarted], um sich im Rahmen eines einfachen Lernprogramms mit dem Portal und den Cmdlets vertraut zu machen.
3.	**Empfohlen:** Beschäftigen Sie sich mit der exemplarischen Vorgehensweise im Artikel [Verwenden von Pig und Hive mit Azure Data Factory][usepigandhive], um sich mit der Erstellung einer Pipeline vertraut zu machen, mit der Daten aus einer lokalen Datenquelle in einen Azure-Blobspeicher verschoben werden.
4.	Laden Sie die Dateien für [ADFWalkthrough][adfwalkthrough-download] in den Ordner **C:\\ADFWalkthrough** herunter, und **behalten Sie dabei die Ordnerstruktur bei**:
	- **Pipelines:** Enthält JSON-Dateien mit der Definition der Pipelines.
	- **Tables:** Enthält JSON-Dateien mit der Definition der Tabellen.
	- **LinkedServices:** Enthält JSON-Dateien mit der Definition Ihres Speicher- und Datenverarbeitungsclusters (HDInsight). 
	- **Scripts:** Enthält Hive- und Pig-Skripts, die für die Verarbeitung der Daten verwendet und über die Pipelines aufgerufen werden.
	- **SampleData:** Enthält Beispieldaten für diese exemplarische Vorgehensweise.
	- **OnPremises:** Enthält JSON-Dateien und Skripts, mit denen der Zugriff auf Ihre lokalen Daten veranschaulicht wird.
	- **uploadSampleDataAndScripts.ps1:** Dieses Skript lädt die Beispieldaten und Skripts an Azure hoch.
5. Stellen Sie sicher, dass Sie die folgenden Azure-Ressourcen erstellt haben:			
	- Azure-Speicherkonto
	- Azure SQL-Datenbank
	- Azure HDInsight-Cluster, Version 3.1 oder höher (oder ein bedarfsgesteuerter HDInsight-Cluster, den der Data Factory-Dienst automatisch erstellt)	
7. Nachdem die Azure-Ressourcen erstellt wurden, stellen Sie sicher, dass Sie über die erforderlichen Informationen verfügen, um eine Verbindung zu den einzelnen Ressourcen herzustellen.
 	- **Azure Storage-Konto**: Kontoname und Kontoschlüssel.  
	- **Azure SQL-Datenbank**: Server, Datenbank, Benutzername und Kennwort.
	- **Azure HDInsight-Cluster**: Name des HDInsight-Clusters, Benutzername, Kennwort und Kontoname sowie Kontoschlüssel für den mit diesem Cluster verknüpften Azure-Speicher. Wenn Sie anstelle Ihres eigenen HDInsight-Clusters einen bedarfsgesteuerten HDInsight-Cluster verwenden möchten, können Sie diesen Schritt überspringen.  
8. Starten Sie **Azure PowerShell**, und führen Sie die folgenden Befehle aus. Lassen Sie Azure PowerShell geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
	- Führen Sie **Login-AzureRmAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie zur Anmeldung beim Azure-Portal verwenden.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
	

## Übersicht
Nachfolgend ist der End-to-End-Workflow dargestellt:![Ablauf des Lernprogramms][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** liest die Rohdaten der Spielereignisse aus einem Blobspeicher (RawGameEventsTable) und erstellt basierend auf Jahr, Monat und Tag Partitionen (PartitionedGameEventsTable).
2. **EnrichGameLogsPipeline** verknüpft partitionierte Spielereignisse („PartitionedGameEventsTable“, ausgegeben von „PartitionGameLogsPipeline“) mit Geocode (RefGetoCodeDictionaryTable) und erweitert die Daten mittels IP-Adresszuordnung zum entsprechenden geografischen Standort (EnrichedGameEventsTable).
3. **AnalyzeMarketingCampaignPipeline** nutzt die erweiterten Daten („EnrichedGameEventTable“ aus „EnrichGameLogsPipeline“) und verarbeitet diese mit den Werbedaten (RefMarketingCampaignnTable), um die Wirksamkeit der Marketingkampagne zu liefern, die zur Analyse in die Azure SQL-Datenbank (MarketingCampainEffectivensessSQLTable) und in einen Azure-Blobspeicher (MarketingCampaignEffectivenessBlobTable) kopiert wird.
    
## Exemplarische Vorgehensweise: Erstellen, Bereitstellen und Überwachen von Workflows
1. [Schritt 1: Hochladen von Beispieldaten und Skripts](#MainStep1). In diesem Schritt laden Sie alle Beispieldaten (einschließlich aller Protokolle und Verweisdaten) sowie Hive-/Pig-Skripts hoch, die von den Workflows ausgeführt werden. Die Skripts, die Sie ausführen, erstellen auch eine Azure SQL-Datenbank (mit dem Namen „MarketingCampaigns“), Tabellen, benutzerdefinierte Typen und gespeicherte Prozeduren.
2. [Schritt 2: Erstellen einer Azure Data Factory](#MainStep2). In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen „LogProcessingFactory“.
3. [Schritt 3: Erstellen von verknüpften Diensten](#MainStep3). In diesem Schritt erstellen Sie die folgenden verknüpften Dienste: 
	
	- 	**StorageLinkedService**. Verknüpft den Azure-Speicherort, der die rohen Spielereignisse, die partitionierten Spielereignisse, erweiterte Spielereignisse, effektive Informationen für die Marketingkampagne, Verweis-Geocodedaten sowie Verweisdaten der Marketingkampagne enthält, mit der LogProcessingFactory.   
	- 	**AzureSqlLinkedService**. Verknüpft eine Azure SQL-Datenbank, die effektive Informationen für die Marketingkampagne enthält. 
	- 	**HDInsightStorageLinkedService**. Verknüpft einen Azure-BLOB-Speicher, der dem HDInsight-Cluster zugeordnet ist, auf den HDInsightLinkedService verweist. 
	- 	**HDInsightLinkedService**. Verknüpft einen Azure HDInsight-Cluster mit der LogProcessingFactory. Dieser Clusters wird verwendet, um die Pig/Hive-Verarbeitung für die Daten auszuführen. 
 		
4. [Schritt 4: Erstellen von Tabellen](#MainStep4). In diesem Schritt erstellen Sie die folgenden Tabellen:
	
	- **RawGameEventsTable**. Diese Tabelle gibt den Speicherort der Rohdaten der Spielereignisse im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/rawgameevents/) definiert werden. 
	- **PartitionedGameEventsTable**. Diese Tabelle gibt den Speicherort der partitionierten Spielereignisdaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/) definiert werden. 
	- **RefGeoCodeDictionaryTable**. Diese Tabelle gibt den Speicherort der Verweis-Geocodedaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/rawgameevents/) definiert werden.
	- **RefMarketingCampaignTable**. Diese Tabelle gibt den Speicherort der Verweisdaten der Marketingkampagne im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/refmarketingcampaign/) definiert werden.
	- **EnrichedGameEventsTable**. Diese Tabelle gibt den Speicherort der erweiterten Spielereignisdaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/) definiert werden.
	- **MarketingCampaignEffectivenessSQLTable**. Diese Tabelle gibt die SQL-Tabelle (MarketingCampaignEffectiveness) in der von „AzureSqlLinkedService“ definierten Azure SQL-Datenbank an, die die Daten zur Wirksamkeit der Marketingkampagne enthält. 
	- **MarketingCampaignEffectivenessBlobTable**. Diese Tabelle gibt den Speicherort der Wirksamkeitsdaten der Marketingkampagne im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/marketingcampaigneffectiveness/) definiert werden. 

	
5. [Schritt 5: Erstellen und Planen von Pipelines](#MainStep5). In diesem Schritt erstellen Sie die folgenden Pipelines:
	- **PartitionGameLogsPipeline**. Diese Pipeline liest die rohen Spielereignisse aus einem BLOB-Speicher (RawGameEventsTable) und erstellt basierend auf Jahr, Monat und Tag (PartitionedGameEventsTable) Partitionen. 


		![PartitionGamesLogsPipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Diese Pipeline verknüpft partitionierte Spielereignisse (PartitionedGameEvents-Tabelle, die eine Ausgabe von PartitionGameLogsPipeline ist) mit Geocode (RefGetoCodeDictionaryTable) und reichert die Daten durch Zuordnen einer IP-Adresse zu dem entsprechenden Geo-Standort (EnrichedGameEventsTable) an.

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Diese Pipeline nutzt die erweiterten Daten (EnrichedGameEventTable produziert von der EnrichGameLogsPipeline) und verarbeitet diese mit den Werbungsdaten (RefMarketingCampaignnTable), um die endgültige Ausgabe der Wirksamkeit der Marketingkampagne zu erstellen, die in die Azure SQL-Datenbank (MarketingCampainEffectivensessSQLTable) und einen Azure-BLOB-Speicher (MarketingCampaignEffectivenessBlobTable) für die Analyse kopiert wird.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Schritt 6: Überwachen von Pipelines und Datenslices](#MainStep6). In diesem Schritt überwachen Sie die Pipelines, Tabellen und Datenslices mithilfe des klassischen Azure-Portals.

## <a name="MainStep1"></a> Schritt 1: Hochladen von Beispieldaten und Skripts
In diesem Schritt laden Sie alle Beispieldaten (einschließlich aller Protokolle und Verweisdaten) sowie Hive-/Pig-Skripts hoch, die von den Workflows ausgeführt werden. Die Skripts, die Sie ausführen, erstellen auch eine Azure SQL-Datenbank namens **MarketingCampaigns** sowie Tabellen, benutzerdefinierte Typen und gespeicherte Prozeduren.

Die Tabellen, benutzerdefinierten Typen und gespeicherten Prozeduren werden beim Verschieben der Wirksamkeitsergebnisse der Marketingkampagne aus dem Azure-BLOB-Speicher in die Azure SQL-Datenbank verschoben.

1. Öffnen Sie **uploadSampleDataAndScripts.ps1** im Ordner **C:\\ADFWalkthrough** (oder in dem Ordner mit den extrahierten Dateien) in Ihrem bevorzugten Editor, ersetzen Sie die markierten Informationen durch Ihre Clusterinformationen, und speichern Sie die Datei.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	Dieses Skript erfordert, dass Sie das sqlcmd-Dienstprogramm auf dem Computer installiert haben. Wenn Sie SQL Server installiert haben, verfügen Sie bereits über das Dienstprogramm. Andernfalls [laden Sie das Dienstprogramm herunter][sqlcmd-install] und installieren es.
	
	Sie können auch die Dateien im Ordner "C:\\ADFWalkthrough\\Scripts" verwenden, um Pig-/Hive-Skripts und Beispieldateien in den ADFWalkthrough-Container im Blob-Speicher hochzuladen und die Tabelle "MarketingCampaignEffectiveness" in der Azure SQL-Datenbank "MarketingCamapaigns" zu erstellen.
   
2. Bestätigen Sie, dass der lokale Computer auf die Azure SQL-Datenbank zugreifen darf. Verwenden Sie das [klassische Azure-Portal](http://manage.windowsazure.com) oder **sp\_set\_firewall\_rule** in der Masterdatenbank, um eine Firewallregel für die IP-Adresse auf dem Computer zu erstellen und so den Zugriff zu aktivieren. Es kann bis zu fünf Minuten dauert, bis diese Änderung wirksam wird. Unter [Festlegen von Firewallregeln für Azure SQL][azure-sql-firewall] finden Sie weitere Informationen.
4. Navigieren Sie in Azure PowerShell zu dem Ordner, in dem Sie die Beispiele extrahiert haben (z. B. **C:\\ADFWalkthrough**).
5. Führen Sie **uploadSampleDataAndScripts.ps1** aus. 
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
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


## <a name="MainStep2"></a> Schritt 2: Erstellen einer Azure Data Factory
In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen **LogProcessingFactory**.

1. Wechseln Sie zu **Azure PowerShell**, falls bereits geöffnet, oder starten Sie **Azure PowerShell**. Wenn Sie Azure PowerShell geschlossen und erneut geöffnet haben, müssen Sie die folgenden Befehle ausführen: 
	- Führen Sie **Login-AzureRmAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie zur Anmeldung beim Azure-Portal verwenden.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben. 

2. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup** (falls Sie dies noch nicht getan haben), indem Sie den folgenden Befehl ausführen.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens ADFTutorialResourceGroup verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie diese anstelle der Ressourcengruppe ADFTutorialResourceGroup verwenden.
4. Führen Sie das Cmdlet **New-AzureRmDataFactory** zum Erstellen einer Data Factory mit dem Namen „DataFactoryMyFirstPipelinePSH“ aus.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name LogProcessingFactory –Location "West US"

	> [AZURE.IMPORTANT] Der Name der Azure Data Factory muss global eindeutig sein. Sollten Sie die Fehlermeldung **Data Factory-Name „LogProcessingFactory“ nicht verfügbar** erhalten, ändern Sie den Namen (z. B. in „IhrNameLogProcessingFactory“). Verwenden Sie diesen Namen bei den Schritten in diesem Lernprogramm anstelle von „LogProcessingFactory“. Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.
	> 
	> Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.

 
## <a name="MainStep3"></a> Schritt 3: Erstellen von verknüpften Diensten

> [AZURE.NOTE] In diesem Artikel wird Azure PowerShell zum Erstellen von verknüpften Diensten, Tabellen und Pipelines verwendet. Wenn Sie dieses Lernprogramm mit dem Azure-Portal (genauer: mit dem Data Factory-Editor) ausführen möchten, lesen Sie unter [Lernprogramm mit Data Factory-Editor][adftutorial-using-editor] weiter.

In diesem Schritt werden die folgenden verknüpften Dienste erstellt: „StorageLinkedService“, „AzureSqlLinkedService“, „HDInsightStorageLinkedService“ und „HDInsightLinkedService“.

16. Navigieren Sie in Azure PowerShell zum Unterordner **LinkedServices**. Dieser befindet sich unter **C:\\ADFWalkthrough** (oder) an dem Speicherort, an dem Sie die Dateien extrahiert haben.
17. Verwenden Sie den folgenden Befehl aus, um die $df-Variable auf den Namen der Data Factory festzulegen.

		$df = “LogProcessingFactory”
17. Öffnen Sie **StorageLinkedService.json** in Ihrem bevorzugten Editor, geben Sie die Werte **Kontoname** und **Kontoschlüssel** ein, und speichern Sie die Datei.
17. Verwenden Sie das Cmdlet **New-AzureRmDataFactoryLinkedService**, um wie folgt einen verknüpften Dienst zu erstellen. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\StorageLinkedService.json
	
18. Öffnen Sie **StorageLinkedService.json** in Ihrem bevorzugten Editor, geben Sie die Werte **Kontoname** und **Kontoschlüssel** ein, und speichern Sie die Datei.
19. Erstellen Sie **HDInsightStorageLinkedService**.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightStorageLinkedService.json
 
19. Öffnen Sie **AzureSqlLinkedService.json** in Ihrem bevorzugten Editor, geben Sie die Werte **Azure SQL Server**-Name, **Benutzername** und **Kennwort** ein, und speichern Sie die Datei.
19. Verwenden Sie das Cmdlet **New-AzureRmDataFactoryLinkedService**, um wie folgt einen verknüpften Dienst zu erstellen. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\AzureSqlLinkedService.json
19. Öffnen Sie **HDInsightLinkedService.json** in Ihrem bevorzugten Editor, und beachten Sie, dass der Typ auf **HDInsightOnDemandLinkedService** festgelegt ist.

	Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung Ihres eigenen HDInsight-Clusters kann der Cluster den Slice jedoch sofort verarbeiten. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters. Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster.
	
	„HDInsightLinkedService“ verknüpft einen bedarfsgesteuerten HDInsight-Cluster mit der Data Factory. Wenn Sie Ihren eigenen HDInsight-Cluster verwenden möchten, aktualisieren Sie den Abschnitt „Properties“ der Datei „HDInsightLinkedService.json“ wie folgt (ersetzen Sie „clustername“, „username“ und „password“ durch die entsprechenden Werte):
	
		"Properties": 
		{
    		"Type": "HDInsightBYOCLinkedService",
        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
	    	"UserName": "<username>",
	    	"Password": "<password>",
	    	"LinkedServiceName": "HDInsightStorageLinkedService"
		}
		


19. Verwenden Sie das Cmdlet **New-AzureRmDataFactoryLinkedService**, um wie folgt einen verknüpften Dienst zu erstellen. Beginnen Sie mit dem Speicherkonto:

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Wenn Sie einen anderen Namen für ResourceGroupName, DataFactoryName oder LinkedService verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad des verknüpften Diensts der JSON-Datei an, wenn die Datei nicht gefunden wird.

 

## <a name="MainStep4"></a> Schritt 4: Erstellen von Tabellen 
In diesem Schritt erstellen Sie die folgenden Tabellen:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Ablauf des Lernprogramms][image-data-factory-tutorial-end-to-end-flow]
 
In der Abbildung oben sind die Pipelines in der mittleren Zeile und die Tabellen in der oberen und unteren Zeile dargestellt.

Das Erstellen von Datasets/Tabellen wird vom klassischen Azure-Portal noch nicht unterstützt. Sie müssen daher Azure PowerShell verwenden, um in dieser Version Tabellen zu erstellen.

### So erstellen Sie die Tabellen

1.	Navigieren Sie in Azure PowerShell an dem Speicherort, an dem Sie die Beispiele extrahiert haben, zum Ordner **Tables** (**C:\\ADFWalkthrough\\Tables\\**).
2.	Verwenden Sie das Cmdlet **New-AzureRmDataFactoryDataset**, um die Datensätze für **RawGameEventsTable.json** wie folgt zu erstellen.	


		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Wenn Sie einen anderen Namen für ResourceGroupName und DataFactoryName verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad der Tabellen-JSON-Datei an, wenn die Datei vom Cmdlet nicht gefunden wird.

3. Wiederholen Sie den vorherigen Schritt, um die folgenden Tabellen zu erstellen:
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. Klicken Sie im **Azure-Portal** auf dem Blatt **DATA FACTORY** für **LogProcessingFactory** auf **Datasets**, und vergewissern Sie sich, dass alle Datasets angezeigt werden (Tabellen sind rechteckige Datasets).

	![Datasets – Alle][image-data-factory-tutorial-datasets-all]

	Sie können auch den folgenden Befehl in Azure PowerShell verwenden:
			
		Get-AzureRmDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Schritt 5: Erstellen und Planen von Pipelines
In diesem Schritt werden die folgenden Pipelines erstellt: „PartitionGameLogsPipeline“, „EnrichGameLogsPipeline“ und „AnalyzeMarketingCampaignPipeline“.

1. Navigieren Sie mithilfe von **Windows-Explorer** im Ordner **C:\\ADFWalkthrough** (oder an dem Speicherort, an dem Sie die Beispiele extrahiert haben) zum Unterordner **Pipelines**.
2.	Öffnen Sie **PartitionGameLogsPipeline.json** in Ihrem bevorzugten Editor, ersetzen Sie die markierten Informationen durch Ihr Speicherkonto für die Informationen des Datenspeicherkontos, und speichern Sie die Datei.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Wiederholen Sie den Schritt, um die folgenden Pipelines zu erstellen:
	1. **EnrichGameLogsPipeline.json** (3x vorhanden)
	2. **AnalyzeMarketingCampaignPipeline.json** (3x vorhanden)

	**WICHTIG:** Vergewissern Sie sich, dass Sie alle <storageaccountname> durch den Namen Ihres Speicherkontos ersetzt haben.
 
4.  Navigieren Sie in **Azure PowerShell** im Ordner **C:\\ADFWalkthrough** (oder an dem Speicherort, an dem Sie die Beispiele extrahiert haben) zum Unterordner **Pipelines**.
5.  Verwenden Sie das Cmdlet **New-AzureRmDataFactoryPipeline**, um die Pipelines für **PartitionGameLogspeline**.json wie folgt zu erstellen.	 
			
		New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Wenn Sie einen anderen Namen für ResourceGroupName, DataFactoryName oder PipelineName verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad der Pipeline-JSON-Datei an.
6. Wiederholen Sie den vorherigen Schritt, um die folgenden Pipelines zu erstellen:
	1. **EnrichGameLogsPipeline**
			
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Verwenden Sie das Cmdlet **Get-AzureRmDataFactoryPipeline**, um die Auflistung der Pipelines abzurufen.
			
		Get-AzureRmDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Sobald die Pipelines erstellt wurden, können Sie die Dauer angeben, in der die Datenverarbeitung von Daten erfolgt. Durch Angeben des aktiven Zeitraums für eine Pipeline definieren Sie die Zeitdauer, in der die Datenslices basierend auf den Verfügbarkeitseigenschaften, die für jede ADF-Tabelle definiert wurden, verarbeitet werden.

Um den aktiven Zeitraum für die Pipeline anzugeben, können Sie das Cmdlet „Set-AzureRmDataFactoryPipelineActivePeriod“ verwenden. In dieser exemplarischen Vorgehensweise reichen die Beispieldaten vom 01.05 bis 05.05. Geben Sie für „StartDateTime“ den Wert „2014-05-01“ an. EndDateTime ist optional.
			
		Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Vergessen Sie nicht, den aktiven Zeitraum für die Pipeline festzulegen.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Wiederholen Sie die vorherigen beiden Schritte, um den aktiven Zeitraum für die folgenden Pipelines festzulegen.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. Klicken Sie im **Azure-Portal** auf dem Blatt **DATA FACTORY** für **LogProcessingFactory** auf die Kachel **Pipelines** (nicht auf die Namen der Pipelines). Die von Ihnen erstellten Pipelines werden angezeigt.

	![Alle Pipelines][image-data-factory-tutorial-pipelines-all]

12. Klicken Sie auf dem Blatt **DATA FACTORY** für **LogProcessingFactory** auf **Diagramm**.

	![Link „Diagramm“][image-data-factory-tutorial-diagram-link]

13. Sie können das angezeigte Diagramm neu anordnen; hier sehen Sie ein neu angeordnetes Diagramm, in dem direkte Eingabe oben und Ausgaben unten angezeigt werden. Wie Sie sehen, wird die Ausgabe von **PartitionGameLogsPipeline** als Eingabe für **EnrichGameLogsPipeline** übernommen, und die Ausgabe von **EnrichGameLogsPipeline** wird an „AnalyzeMarketingCampaignPipeline“ übergeben. Doppelklicken Sie auf einen Titel, um Details über das Artefakt anzuzeigen, das das Fenster darstellt.

	![Diagrammansicht][image-data-factory-tutorial-diagram-view]

	**Glückwunsch!** Sie haben die Azure Data Factory, die verknüpften Dienste, Pipelines und Tabellen erfolgreich erstellt und den Workflow gestartet.


## <a name="MainStep6"></a> Schritt 6: Überwachen von Pipelines und Datenslices 

1.	Wenn das Fenster „DATA FACTORY“ für „LogProcessingFactory“ nicht geöffnet ist, können Sie einen der folgenden Schritte ausführen:
	1.	Klicken Sie im Startmenü auf **LogProcessingFactory**. Während der Erstellung der Data Factory wurde die Option **Zum Startmenü hinzufügen** automatisch aktiviert.

		![Überwachung – Startmenü][image-data-factory-monitoring-startboard]

	2. Klicken Sie auf den Hub **DURCHSUCHEN** und dann auf **Alles**.
	 	
		![Hub „Überwachung“ – Alles][image-data-factory-monitoring-hub-everything]

		Wählen Sie auf dem Blatt **Durchsuchen** die Option **Data Factorys** und auf dem Blatt **Data Factorys** die Option **LogProcessingFactory** aus.

		![Überwachung – Data Factorys durchsuchen][image-data-factory-monitoring-browse-datafactories]
2. Sie haben mehrere Möglichkeiten, um Ihre Data Factory zu überwachen. Sie können mit Pipelines oder Datasets beginnen. Wir beginnen mit Pipelines und arbeiten uns dann weiter vor. 
3.	Klicken Sie auf dem Blatt **DATA FACTORY** auf **Pipelines**. 
4.	Klicken Sie auf dem Blatt **Pipelines** auf „PartitionGameLogsPipeline“. 
5.	Auf dem Blatt **PIPELINE** für **PartitionGameLogsPipeline** sehen Sie, dass die Pipeline das Dataset **RawGameEventsTable** verwendet. Klicken Sie auf **RawGameEventsTable**.

	![Pipeline – Genutzt und Erstellt][image-data-factory-monitoring-pipeline-consumed-produced]

6. Auf dem Blatt „TABELLE“ für **RawGameEventsTable** werden alle Slices angezeigt. Im folgenden Screenshot besitzen alle Slices den Status **Bereit**, und es liegen keine Probleme mit Slices vor. Dies bedeutet, dass die Daten verarbeitet werden können.

	![Blatt der Tabelle „RawGameEventsTable“][image-data-factory-monitoring-raw-game-events-table]
 
7. Klicken Sie nun auf dem Blatt **PIPELINE** für **PartionGameLogsPipeline** auf **Erstellt**.
8. Daraufhin wird die Liste der Datasets angezeigt, die diese Pipeline produziert: 
9. Klicken Sie auf dem Blatt **Erstellte Datasets** auf die Tabelle **PartitionedGameEvents**. 
10.	Vergewissern Sie sich, dass der Status aller Slices **Bereit** lautet. 
11.	Klicken Sie auf einen der Slices mit dem Status **Bereit**, um das Blatt **DATENSLICE** für diesen Slice anzuzeigen.

	![Blatt des Datenslices „RawGameEventsTable“][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Im Falle eines Fehlers wird hier der Status **Fehler** angezeigt. Möglicherweise werden auch beide Slices mit dem Status **Bereit** oder beide Slices mit dem Status **Ausstehende Überprüfung** angezeigt – je nachdem, wie schnell die Slices verarbeitet werden.
 
	Die [Azure Data Factory-Entwicklerreferenz][developer-reference] enthält Informationen zu allen möglichen Slicestatusoptionen.

12.	Klicken Sie auf dem Blatt **DATENSLICE** in der Liste **Aktivitätsausführungen** auf die Ausführung. Daraufhin sollte das Fenster „Aktivitätsausführung“ für diesen Slice angezeigt werden. Das Blatt **AKTIVITÄTSAUSFÜHRUNG – DETAILS** wird angezeigt:

	![Blatt „Aktivitätsausführung – Details“][image-data-factory-monitoring-activity-run-details]

13.	Klicken Sie auf **Herunterladen**, um die Dateien herunterzuladen. Dieser Bildschirm ist besonders dann hilfreich, wenn Sie Fehler der HDInsight-Verarbeitung beheben.
	 
	
Wenn die Ausführung aller Pipelines abgeschlossen ist, können Sie sich die Ergebnisse in **MarketingCampaignEffectivenessTable** in der Azure SQL-Datenbank **MarketingCampaigns** ansehen.

**Glückwunsch!** Sie können jetzt die Workflows überwachen und Fehler beheben. Sie haben gelernt, wie Azure Data Factory zum Verarbeiten von Daten und Erstellen von Analysen verwendet wird.

## Erweitern des Lernprogramms mit lokalen Daten
Im letzten Schritt des Protokollverarbeitungsszenarios in der exemplarischen Vorgehensweise dieses Artikels wurde die Ausgabe zur Wirksamkeit der Marketingkampagne in eine Azure SQL-Datenbank kopiert. Sie können diese Daten auch in einen lokalen SQL Server für Analysen innerhalb Ihrer Organisation verschieben.
 
Um die Daten zur Wirksamkeit der Marketingkampagne aus dem Azure-Blob in eine lokale SQL Server-Datenbank zu kopieren, müssen Sie einen zusätzlichen lokalen verknüpften Dienst, eine Tabelle und eine Pipeline erstellen. Dies wurde in dieser exemplarischen Vorgehensweise erläutert.

In [Exemplarische Vorgehensweise: Verwenden einer lokalen Datenquelle][tutorial-onpremises-using-powershell] erfahren Sie, wie Sie eine Pipeline erstellen, die die Daten zur Wirksamkeit einer Marketingkampagne in eine lokale SQL Server-Datenbank kopiert.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

<!---HONumber=AcomDC_0204_2016-->