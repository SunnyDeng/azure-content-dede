<properties 
	pageTitle="Verschieben und Verarbeiten von Protokolldateien mit Azure Data Factory" 
	description="Dieses Lernprogramm beschreibt ein nahezu realen Szenario und das Szenario mit Azure Data Factory-Dienst und Daten Factory-Editor implementiert." 
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

# Lernprogramm: Messen der Effektivität von Marketingkampagnen  
Contoso ist ein Spiele-Unternehmen, das Spielen für mehrere Plattformen erstellt: game-Konsolen, tragbare Geräte und Computer (PCs). Diese Spiele viele Protokolle und Contoso Ziel ist es, erfassen und analysieren diese Protokolle, um gewinnen Sie Einsichten in die Customer-Voreinstellungen, demografische Daten, Benutzerverhaltens usw. Ermitteln der Up-Selling und Cross-Selling-Möglichkeiten, neue überzeugende Features für Laufwerk Geschäftswachstum entwickeln und Kunden eine bessere Erfahrung bieten.

In diesem Lernprogramm erstellen Sie Data Factory-Pipelines, um die Wirksamkeit einer Marketingkampagne zu evaluieren, die Contoso von Beispielprotokolle sammeln, Verarbeitung und erweitern sie mit Verweisdaten und Transformieren der Daten zuletzt gestartet wurde. Es hat die folgenden drei Pipelines:

1.	Die **PartitionGameLogsPipeline** die rohen game-Ereignisse aus dem blobspeicher liest und Partitionen basierend auf dem Jahr, Monat und Tag erstellt.
2.	Die **EnrichGameLogsPipeline** verknüpft partitionierte Spiel Ereignisse mit geografischen Code Verweisdaten und wertet die Daten durch die Zuordnung von IP-Adressen zu den entsprechenden Standorten.
3.	Die **AnalyzeMarketingCampaignPipeline** Pipeline erweiterte Daten nutzt und verarbeitet sie mit den Daten Werbung, um die endgültige Ausgabe zu erstellen, marketing Kampagneneffektivität enthält.

## Vorbereitungen für das Lernprogramm
1.	Lesen [Einführung in Azure Data Factory][adfintroduction] um einen Überblick über Azure Data Factory und Kenntnisse über die Konzepte der obersten Ebene abzurufen.
2.	Sie benötigen ein Azure-Abonnement, um dieses Lernprogramm durchzuführen. Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](http://azure.microsoft.com/pricing/purchase-options/), [Spezielle Angebote](http://azure.microsoft.com/pricing/member-offers/) oder [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).
3.	Sie müssen auch herunterladen und installieren [Azure PowerShell][download-azure-powershell] auf Ihrem Computer. Sie führen "Data Factory"-Cmdlets zum Hochladen von Beispieldaten und Pig/Struktur Skripts an, die im Blob-Speicher. 
2.	**(empfohlen)** überprüfen und üben Sie das Lernprogramm in der [Erste Schritte mit Azure Data Factory][adfgetstarted] Artikel ein einfaches Lernprogramm mit dem Portal und -Cmdlets vertraut zu machen.
3.	**(empfohlen)** überprüfen und üben Sie in dieser exemplarischen Vorgehensweise die [verwenden Pig und Hive Azure Data Factory][usepigandhive] Artikel eine Anleitung zum Erstellen einer Pipeline, um Daten aus einer lokalen Datenquelle ein Azure-Blob-Speicher zu verschieben.
4.	Herunterladen [ADFWalkthrough][adfwalkthrough-download] -Dateien **C:\ADFWalkthrough** Ordner **Beibehalten der Ordnerstruktur**:
	- **Pipelines:** sie mit der Definition von Pipelines JSON-Dateien enthält.
	- **Tabellen:** sie JSON-Dateien, die mit der Definition der Tabellen enthält.
	- **LinkedServices:** darüber, dass JSON-Dateien, die mit der Definition der Speicher- und Rechenressourcen (HDInsight) Cluster 
	- **Skripts:** darüber Hive und Pig-Skripts, die für die Verarbeitung der Daten verwendet und über die Pipelines aufgerufen werden
	- **SampleData:** sie Beispieldaten für diese exemplarische Vorgehensweise enthält
	- **Verbindungspfad:** es enthält JSON-Dateien und Skripts, die verwendet werden, um zu veranschaulichen, Zugriff auf Ihre lokalen Daten
	- **uploadSampleDataAndScripts.ps1:** dieses Skript die Beispieldaten und Skripts in Azure hochgeladen.
5. Stellen Sie sicher, dass Sie die folgenden Azure-Ressourcen erstellt haben:			
	- Azure-Speicherkonto
	- Azure SQL-Datenbank
	- Azure HDInsight-Cluster der Version 3.1 oder höher (oder verwenden Sie on-Demand-HDInsight-Cluster, die die Service Factory von Daten automatisch erstellt)	
7. Nachdem die Azure-Ressourcen erstellt wurden, stellen Sie sicher, dass Sie über die erforderlichen Informationen verfügen, um eine Verbindung zu den einzelnen Ressourcen herzustellen.
 	- **Azure Storage-Konto** - Kontonamen und Kennwort.  
	- **Azure SQL-Datenbank** -Server, Datenbank, Benutzername und Kennwort.
	- **Azure HDInsight-Cluster**.-Name des HDInsight-Clusters, Benutzername, Kennwort und Kontonamen und Kontoschlüssel für den Azure-Speicher mit diesem Cluster verknüpften. Wenn Sie einen HDInsight-Cluster bei Bedarf statt eigene HDInsight-Cluster verwenden möchten, können Sie diesen Schritt überspringen.  
8. Starten Sie **Azure PowerShell** und führen Sie die folgenden Befehle aus. Lassen Sie die Azure PowerShell geöffnet. Wenn Sie zu schließen und erneut öffnen, müssen Sie diese Befehle erneut ausführen.
	- Führen Sie **Add-AzureAccount** und geben Sie den Benutzernamen und das Kennwort, das Sie verwenden, um das Azure Preview Portal anmelden.  
	- Führen Sie **Get-AzureSubscription** alle Abonnements für dieses Konto anzeigen.
	- Führen Sie **Select-AzureSubscription** das Abonnement aus, die Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Vorschauportal verwendet haben.	

## Übersicht
Nachfolgend ist der End-to-End-Workflow dargestellt:

![Lernprogramm End-to-End-Nachrichtenfluss][image-data-factory-tutorial-end-to-end-flow]

1. Die **PartitionGameLogsPipeline** liest die rohen Spiel Ereignisse aus einem Blob-Speicher (RawGameEventsTable) und erstellt Partitionen basierend auf dem Jahr, Monat und Tag (PartitionedGameEventsTable).
2. Die **EnrichGameLogsPipeline** partitionierten game-Ereignisse (PartitionedGameEvents Tabelle, eine Ausgabe der PartitionGameLogsPipeline) mit geografischen Code (RefGetoCodeDictionaryTable) verknüpft und ergänzt die Daten durch die Zuordnung von IP-Adresse zu den entsprechenden geografischen Standort (EnrichedGameEventsTable).
3. Die **AnalyzeMarketingCampaignPipeline** Pipeline nutzt die erweiterte Daten (EnrichedGameEventTable, die EnrichGameLogsPipeline erzeugt) und verarbeitet sie mit den Daten für Werbung (RefMarketingCampaignnTable) zum Erstellen der endgültigen Ausgabe von marketing-Kampagneneffektivität, der in der Azure SQL-Datenbank (MarketingCampainEffectivensessSQLTable) und ein Azure-Blob-Speicher (MarketingCampaignEffectivenessBlobTable) für die Geschäftsanalyse kopiert wird.
    
## Exemplarische Vorgehensweise: Erstellen, bereitstellen und Überwachen von workflows
1. [Schritt 1: Hochladen von Beispieldaten und Skripts](#MainStep1). In diesem Schritt laden Sie alle Beispieldaten (einschließlich aller Protokolle und Verweisdaten) sowie Hive-/Pig-Skripts hoch, die von den Workflows ausgeführt werden. Die Skripts, die Sie ausführen, erstellen auch eine Azure SQL-Datenbank (mit dem Namen „MarketingCampaigns“), Tabellen, benutzerdefinierte Typen und gespeicherte Prozeduren.
2. [Schritt2: erstellen eine Azure Data-Factory](#MainStep2). In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen „LogProcessingFactory“.
3. [Schritt 3: Erstellen von verknüpften Dienste](#MainStep3). In diesem Schritt erstellen Sie die folgenden verknüpften Dienste: 
	
	- 	**StorageLinkedService**. Verknüpft den Azure-Speicherort, der die rohen Spielereignisse, die partitionierten Spielereignisse, erweiterte Spielereignisse, effektive Informationen für die Marketingkampagne, Verweis-Geocodedaten sowie Verweisdaten der Marketingkampagne enthält, mit der LogProcessingFactory.   
	- 	**AzureSqlLinkedService**. Verknüpft eine Azure SQL-Datenbank, die effektive Informationen für die Marketingkampagne enthält. 
	- 	**HDInsightStorageLinkedService**. Verknüpft einen Azure-BLOB-Speicher, der dem HDInsight-Cluster zugeordnet ist, auf den HDInsightLinkedService verweist. 
	- 	**HDInsightLinkedService**. Verknüpft einen Azure HDInsight-Cluster mit der LogProcessingFactory. Dieser Clusters wird verwendet, um die Pig/Hive-Verarbeitung für die Daten auszuführen. 
 		
4. [Schritt 4: Erstellen von Tabellen](#MainStep4). In diesem Schritt erstellen Sie die folgenden Tabellen:
	
	- **RawGameEventsTable**. Diese Tabelle gibt den Speicherort der Rohdaten der Spielereignisse im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/rawgameevents/) definiert werden. 
	- **PartitionedGameEventsTable**. Diese Tabelle gibt den Speicherort der partitionierten Spielereignisdaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/) definiert werden. 
	- **RefGeoCodeDictionaryTable**. Diese Tabelle gibt den Speicherort der Verweis-Geocodedaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/rawgameevents/) definiert werden.
	- **RefMarketingCampaignTable**. Diese Tabelle gibt den Speicherort der Verweisdaten der Marketingkampagne im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/refmarketingcampaign/) definiert werden.
	- **EnrichedGameEventsTable**. Diese Tabelle gibt den Speicherort der erweiterten Spielereignisdaten im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/) definiert werden.
	- **MarketingCampaignEffectivenessSQLTable**. Diese Tabelle gibt die SQL-Tabelle (MarketingCampaignEffectiveness) in der Azure SQL-Datenbank, die durch AzureSqlLinkedService mit den marketing Kampagne Effektivität Daten definiert. 
	- **MarketingCampaignEffectivenessBlobTable**. Diese Tabelle gibt den Speicherort der Wirksamkeitsdaten der Marketingkampagne im Azure Blob-Speicher an, die durch StorageLinkedService (adfwalkthrough/logs/marketingcampaigneffectiveness/) definiert werden. 

	
5. [Schritt 5: Erstellen und Planen von Pipelines](#MainStep5). In diesem Schritt erstellen Sie die folgenden Pipelines:
	- **PartitionGameLogsPipeline**. Diese Pipeline liest die rohen Spielereignisse aus einem BLOB-Speicher (RawGameEventsTable) und erstellt basierend auf Jahr, Monat und Tag (PartitionedGameEventsTable) Partitionen. 


		![PartitionGamesLogs-pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Diese Pipeline verknüpft partitionierte Spielereignisse (PartitionedGameEvents-Tabelle, die eine Ausgabe von PartitionGameLogsPipeline ist) mit Geocode (RefGetoCodeDictionaryTable) und reichert die Daten durch Zuordnen einer IP-Adresse zu dem entsprechenden Geo-Standort (EnrichedGameEventsTable) an.

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Diese Pipeline nutzt die erweiterten Daten (EnrichedGameEventTable produziert von der EnrichGameLogsPipeline) und verarbeitet diese mit den Werbungsdaten (RefMarketingCampaignnTable), um die endgültige Ausgabe der Wirksamkeit der Marketingkampagne zu erstellen, die in die Azure SQL-Datenbank (MarketingCampainEffectivensessSQLTable) und einen Azure-BLOB-Speicher (MarketingCampaignEffectivenessBlobTable) für die Analyse kopiert wird.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Schritt 6: Überwachen von Pipelines und Daten Segmente](#MainStep6). In diesem Schritt überwachen Sie die Pipelines, Tabellen und Datenslices mithilfe des Azure-Portals.

## <a name="MainStep1"></a> Schritt 1: Hochladen von Beispieldaten und Skripts
In diesem Schritt laden Sie alle Beispieldaten (einschließlich aller Protokolle und Verweisdaten) sowie Hive-/Pig-Skripts hoch, die von den Workflows ausgeführt werden. Führen Sie auch Skripts erstellen eine Azure SQL-Datenbank namens **MarketingCampaigns**, gespeicherten Prozeduren und Tabellen, benutzerdefinierte Typen.

Die Tabellen, benutzerdefinierten Typen und gespeicherten Prozeduren werden beim Verschieben der Wirksamkeitsergebnisse der Marketingkampagne aus dem Azure-BLOB-Speicher in die Azure SQL-Datenbank verschoben.

1. Open **uploadSampleDataAndScripts.ps1** von **C:\ADFWalkthrough** Ordner (oder den Ordner, der die extrahierten Dateien enthält) in Ihrem bevorzugten Editor der Clusterinformationen ersetzen die hervorgehobene, und speichern Sie die Datei.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][Herunterladen][sqlcmd-install]   
2. Bestätigen Sie, dass der lokale Computer auf die Azure SQL-Datenbank zugreifen darf. Verwenden Sie zum Aktivieren des Zugriffs der **Azure-Verwaltungsportal** oder **Sp_set_firewall_rule** auf der master-Datenbank um eine Firewallregel für die IP-Adresse des Computers zu erstellen. Es kann bis zu fünf Minuten dauert, bis diese Änderung wirksam wird. Finden Sie unter [Festlegen von Firewallregeln für Azure SQL][azure-sql-firewall].
4. Wechseln Sie in Azure PowerShell, um den Speicherort, in dem Sie die Beispiele extrahiert haben (z. B.: **C:\ADFWalkthrough**)
5. Führen Sie **uploadSampleDataAndScripts.ps1** 
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

## <a name="MainStep2"></a> Schritt 2: Erstellen einer Azure Data-factory
In diesem Schritt erstellen Sie eine Azure Data-Factory, die mit dem Namen **LogProcessingFactory**.

1.	Nach der Anmeldung die [Azure Preview Portal][azure-preview-portal], klicken Sie auf **Neu** klicken Sie in der unteren linken Ecke auf **Datenanalyse** in den **Erstellen** Blade, und klicken Sie auf **Data Factory** auf der **Datenanalyse** Blade. 

	![Neu -> DataFactory][image-data-factory-new-datafactory-menu]

5. In der **neue Daten Factory** Blade, geben Sie **LogProcessingFactory** für den **Namen**.

	![Daten-Factory-Blade][image-data-factory-tutorial-new-datafactory-blade]

6. Wenn Sie eine Azure-Ressourcengruppe mit dem Namen erstellt haben **ADF** bereits, wie folgt vorgehen:
	1. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und klicken Sie auf **Erstellen Sie eine neue Ressourcengruppe**.
	
		![Ressourcengruppe Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. In der **Erstellen der Ressourcengruppe** Blade, geben Sie **ADF** für den Namen der Ressourcengruppe, und klicken Sie auf **OK**.
	
		![Ressourcengruppe erstellen][image-data-factory-tutorial-create-resourcegroup]
7. Wählen Sie **ADF** für die **NAME der RESSOURCENGRUPPE**.  
8.	In der **neue Daten Factory** Blade, beachten Sie, dass **zum Startmenü hinzufügen** ist standardmäßig aktiviert. Dadurch wird eine Verknüpfung zur Data Factory im Startmenü (was Sie sehen, wenn Sie sich beim Azure-Vorschauportal anmelden) hinzugefügt.

	![Erstellen von Daten-Factory-Blade][image-data-factory-tutorial-create-datafactory]

9.	In der **neue Daten Factory** Blade, klicken Sie auf **Erstellen** Werk Daten zu erstellen.
10.	Nachdem die Factory Daten erstellt wurde, sollte der **DATA FACTORY** Blade mit der Bezeichnung **LogProcessingFactory**.

	![Daten-Factory-Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	Wenn dies nicht angezeigt werden, führen Sie einen der folgenden Schritte aus:

	- Klicken Sie auf **LogProcessingFactory** auf der **Startboard** (Startseite)
	- Klicken Sie auf **Durchsuchen** klicken Sie auf der linken Seite auf **Alles**, klicken Sie auf **Daten Factorys**, und klicken Sie auf die Daten-Factory.
 
	> [AZURE.NOTE]Der Name der Azure Data Factory muss global eindeutig sein. Wenn Sie die Fehlermeldung: **Daten Factory Name "LogProcessingFactory" ist nicht verfügbar**, ändern Sie den Namen (z. B. YournameLogProcessingFactory). Verwenden Sie diesen Namen anstelle von LogProcessingFactory beim Ausführen der Schritte in diesem Lernprogramm.
 
## <a name="MainStep3"></a> Schritt 3: Erstellen von verknüpften Dienste

> [AZURE.NOTE]In diesem Artikel verwendet den Azure-Verwaltungsportal, insbesondere Data Factory-Editor, zum Erstellen von verknüpften Dienste, Tabellen und Pipelines. Finden Sie unter [Tutorial mit Azure PowerShell][adftutorial-using-powershell] Wenn Sie dieses Lernprogramm mit Azure PowerShell ausführen möchten.

In diesem Schritt erstellen Sie die folgenden verknüpften Dienste:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Erstellen von StorageLinkedService und HDInsightStorageLinkedService

1.	In der **DATA FACTORY** Blade, klicken Sie auf **Autor und Bereitstellen von** Kachel zu starten der **Editor** für die Daten-Factory.

	![Kachel "Erstellen und bereitstellen"][image-author-deploy-tile]

	> [AZURE.NOTE]Finden Sie unter [Data Factory-Editor][data-factory-editor] Thema ausführliche Übersicht über den Daten-Factory-Editor.

2.  In der **Editor**, klicken Sie auf **neuen Datenspeicher** Schaltfläche auf der Symbolleiste, und wählen **Azure-Speicher** aus dem Dropdown-Menü. Die JSON-Vorlage zum Erstellen einer Azure-Speicher verknüpft-Diensts im rechten Fensterbereich sollte angezeigt werden.
	
	![Neue Data Store Schaltfläche-Editor][image-editor-newdatastore-button]

3. Ersetzen Sie **Accountname** und **Accountkey** mit dem Kontonamen und Kontowerte für Azure-Speicherkonto.

	![Blob-Speicher JSON-Editor][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Finden Sie unter [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) ausführliche Informationen zu JSON-Eigenschaften.

4. Klicken Sie auf **Bereitstellen** auf der Symbolleiste auf die StorageLinkedService bereitstellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTE SERVICE erstellt erfolgreich** auf der Titelleiste.

	![Editor-Blob-Speicher bereitstellen.][image-editor-blob-storage-deploy]

5. Wiederholen Sie die Schritte zum Erstellen einer anderen Azure-Speicher verknüpft den Dienst mit dem Namen: **HDInsightStorageLinkedService** für den Speicher mit HDInsight-Cluster. In das JSON-Skript für den Dienst verknüpfte, ändern Sie den Wert von der **Name** -Eigenschaft **HDInsightStorageLinkedService**.

### Erstellen von AzureSqlLinkedService
1. In der **Data Factory-Editor** , klicken Sie auf **neuen Datenspeicher** Schaltfläche auf der Symbolleiste, und wählen **Azure SQL-Datenbank** aus dem Dropdown-Menü. Die JSON-Vorlage zum Erstellen des SQL Azure-verknüpfte Diensts im rechten Fensterbereich sollte angezeigt werden.
2. Ersetzen Sie **Servername**, **username@servername**, und **Kennwort** mit dem Namen Ihrer Azure SQL Server-Benutzerkonto und Kennwort. 3. Ersetzen Sie **Databasename** mit **MarketingCampaigns**. Dies ist der Azure SQL-Datenbank erstellt, die von den Skripts, die Sie in Schritt 1 ausgeführt haben. Sie sollten sicherstellen, dass diese Datenbank (für den Fall, dass der Fehler) tatsächlich von den Skripts erstellt wurde. 
3. Klicken Sie auf **Bereitstellen** auf der Symbolleiste erstellen und Bereitstellen der AzureSqlLinkedService.

### Erstellen von HDInsightLinkedService
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung Ihres eigenen HDInsight-Clusters kann der Cluster den Slice jedoch sofort verarbeiten. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters. Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster.

#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster
1. Klicken Sie auf **neue Compute** aus, und wählen Sie die Befehlsleiste **On-Demand-HDInsight-Cluster** aus dem Menü.
2. Gehen Sie in das JSON-Skript: 
	1. Für die **ClusterSize** -Eigenschaft, geben Sie die Größe des HDInsight-Clusters.
	2. Für die **JobsContainer** -Eigenschaft, geben Sie den Namen der Standardcontainer, in denen die Clusterprotokolle gespeichert werden. Geben Sie im Rahmen dieses Lernprogramms **Adfjobscontainer**.
	3. Für die **TimeToLive** -Eigenschaft angeben, wie lange der Kunde im Leerlauf befinden darf, bevor es gelöscht wird. 
	4. Für die **Version** -Eigenschaft, geben Sie die HDInsight-Version, die Sie verwenden möchten. Wenn Sie diese Eigenschaft ausschließen, wird die neueste Version verwendet.  
	5. Für die **LinkedServiceName**, geben Sie **HDInsightStorageLinkedService** dass Sie erhalten erstellt hätten-Schritte-Lernprogramm. 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		Beachten Sie, dass die **Typ** verknüpfte Service ist Satz von **HDInsightOnDemandLinkedService**.

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste, um den verknüpften Dienst bereitzustellen.
   
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. Klicken Sie auf **neue Compute** aus, und wählen Sie die Befehlsleiste **HDInsight-Cluster** aus dem Menü.
2. Gehen Sie in das JSON-Skript: 
	1. Für die **ClusterUri** -Eigenschaft, geben Sie die URL für Ihre HDInsight. Zum Beispiel: https://<clustername>.azurehdinsight.net/     
	2. Für die **Benutzername** -Eigenschaft, geben Sie den Benutzernamen, der Zugriff auf den HDInsight-Cluster verfügt.
	3. Für die **Kennwort** -Eigenschaft, geben Sie das Kennwort für den Benutzer. 
	4. Für die **LinkedServiceName** -Eigenschaft, geben Sie **StorageLinkedService**. Dies ist die verknüpfte Dienst, den Sie in Get-Schritte-Lernprogramm erstellt haben. 

	Norieren, die **Typ** verknüpfte Service ist Satz von **HDInsightBYOCLinkedService** (BYOC steht für Ihre eigenen Cluster zu bringen).

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste, um den verknüpften Dienst bereitzustellen.


## <a name="MainStep4"></a> Schritt 4: Erstellen von Tabellen
 
In diesem Schritt erstellen Sie die folgenden Daten Factory-Tabellen:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Lernprogramm End-to-End-Nachrichtenfluss][image-data-factory-tutorial-end-to-end-flow]
 
In der Abbildung oben sind die Pipelines in der mittleren Zeile und die Tabellen in der oberen und unteren Zeile dargestellt.

### So erstellen Sie die Tabellen
	
1. In der **Editor** finden Sie die Factory Daten **Neues Dataset** auf der Symbolleiste, und klicken Sie auf die Schaltfläche **Azure Blob-Speicher** aus dem Dropdown-Menü. 
2. Ersetzen von JSON im rechten Bereich mit der JSON-Skript aus der **RawGameEventsTable.json** Datei aus der **C:\ADFWalkthrough\Tables** Ordner.
3. Klicken Sie auf **Bereitstellen** auf der Symbolleiste erstellen und Bereitstellen der Tabelle. Vergewissern Sie sich, dass Sie sehen die **Tabelle erfolgreich erstellt** Nachricht in der Titelleiste des Editors.
4. Wiederholen Sie die Schritte 1 bis 3 mit dem Inhalt von den folgenden Dateien: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Wiederholen Sie die Schritte 1 bis 3 mit dem Inhalt aus der folgenden Datei. Auswählen, dabei jedoch **Azure Sql** nach Anklicken von **Neues Dataset**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Schritt 5: Erstellen und Planen von pipelines
In diesem Schritt erstellen Sie die folgenden Pipelines:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Pipelines erstellen

1. Klicken Sie in **Data Factory Editor** in der Symbolleiste auf die Schaltfläche **Neue Pipeline**. Klicken Sie in der Symbolleiste auf **... (drei Punkte)**, wenn die Schaltfläche nicht angezeigt wird. Alternativ können Sie in der Strukturansicht mit der rechten Maustaste auf **Pipelines** und dann auf **Neue Pipeline** klicken.
2. Ersetzen von JSON im rechten Bereich mit der JSON-Skript aus der **PartitionGameLogsPipeline.json** Datei aus der **C:\ADFWalkthrough\Pipelines** Ordner.
3. Fügen Sie ein **Komma (',')** am Ende der **schließenden eckigen Klammer ('] ')** in JSON hinzu, und fügen Sie hinter der schließenden eckigen Klammer die folgenden drei Zeilen hinzu. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]Beachten Sie, dass die Start- und Endzeiten auf 05/01/2014 und 05/05/2014 festgelegt sind, da sich die Beispieldaten in dieser exemplarischen Vorgehensweise auf den Zeitraum von 05/01/2014 bis 05/05/2014 beziehen.
 
3. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um die Pipeline bereitzustellen. Vergewissern Sie sich, dass die Nachricht **PIPELINE ERFOLGREICH ERSTELLT** in der Titelleiste des Editors angezeigt wird.
4. Wiederholen Sie die Schritte 1 bis 3 mit dem Inhalt von den folgenden Dateien: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Schließen Sie die Daten Factory Blades durch Drücken von **X** (oben rechts) auf der Startseite finden Sie unter (** DATA FACTORY **Blade) für Ihre Daten Factory. 
### Diagrammansicht

1. In der **DATA FACTORY** Blade für die **LogProcessingFactory**, klicken Sie auf **Diagramm**. 

	![Diagramm-Link][image-data-factory-tutorial-diagram-link]

2. Sie können das angezeigte Diagramm neu anordnen; hier sehen Sie ein neu angeordnetes Diagramm, in dem direkte Eingabe oben und Ausgaben unten angezeigt werden. Können Sie sehen, die Ausgabe von der **PartitionGameLogsPipeline** wird als Eingabe für die EnrichGameLogsPipeline und die Ausgabe der übergeben der **EnrichGameLogsPipeline** übergeben wird, um die **AnalyzeMarketingCampaignPipeline**. Doppelklicken Sie auf einen Titel, um Details über das Artefakt anzuzeigen, das das Fenster darstellt.

	![Diagrammansicht][image-data-factory-tutorial-diagram-view]

3. Mit der rechten Maustaste **AnalyzeMarketingCampaignPipeline**, und klicken Sie auf **offene Pipeline**. Alle Aktivitäten in der Pipeline sowie Eingabe- und Datasets für die Aktivitäten sollte angezeigt werden.
 
	![Offene pipeline](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Klicken Sie auf **Data Factory** in die Breadcrumb-Leiste in der oberen linken Ecke zu der Diagrammansicht mit allen Pipelines zurückzukehren.


**Glückwunsch!** Sie haben die Azure Data Factory, die verknüpften Dienste, Pipelines und Tabellen erfolgreich erstellt und den Workflow gestartet.


## <a name="MainStep6"></a> Schritt 6: Überwachen von Pipelines und Daten slices 

1.	Wenn Sie keinen der **DATA FACTORY** Blade für die **LogProcessingFactory** geöffnet ist, führen Sie einen der folgenden:
	1.	Klicken Sie auf **LogProcessingFactory** auf der **Startboard**. Beim Erstellen des Factory Daten der **zum Startmenü hinzufügen** Option wurde automatisch ausgecheckt.

		![Überwachung von Startmenü][image-data-factory-monitoring-startboard]

	2. Klicken Sie auf **Durchsuchen** Hub, und klicken Sie auf **Alles**.
	 	
		![Überwachen der Hub alles][image-data-factory-monitoring-hub-everything]

		In der **Durchsuchen** Blade, wählen **Daten Factorys** und wählen Sie **LogProcessingFactory** in den **Daten Factorys** Blade.

		![Überwachung durchsuchen Datafactories][image-data-factory-monitoring-browse-datafactories]
2. Sie haben mehrere Möglichkeiten, um Ihre Data Factory zu überwachen. Sie können mit Pipelines oder Datasets beginnen. Wir beginnen mit Pipelines und arbeiten uns dann weiter vor. 
3.	Klicken Sie auf **Pipelines** auf der **DATA FACTORY** Blade. 
4.	Klicken Sie auf **PartitionGameLogsPipeline** im Blade Pipelines. 
5.	In der **PIPELINE** Blade für **PartitionGameLogsPipeline**, sehen Sie, dass die Pipeline nutzt **RawGameEventsTable** Dataset. Klicken Sie auf **RawGameEventsTable**.

	![Pipeline und-Erstellung][image-data-factory-monitoring-pipeline-consumed-produced]

6. In Tabelle Blade auf **RawGameEventsTable**, alle Segmente angezeigt. In der folgenden Bildschirmabbildung werden alle Segmente im **bereit** Zustand, und es sind keine Segmente Problem. Dies bedeutet, dass die Daten verarbeitet werden können.

	![RawGameEventsTable Tabelle blade][image-data-factory-monitoring-raw-game-events-table]

	Beide **kürzlich aktualisierte Segmente** und **Slices einer vor kurzem fehlschlug** Listen sortiert die **Zeit der letzten Aktualisierung**. Der Zeitpunkt des Updates eines Segments wird in den folgenden Situationen geändert.

	-  Sie den Status eines Segments manuell aktualisieren, z. B. mit der **Set AzureDataFactorySliceStatus** (oder), indem Sie auf **Ausführen** auf der **SLICE** Blade für das Segment.
	-  Das Segment ändert sich der Status aufgrund einer Ausführung (z. B. eine Ausführung gestartet, eine Ausführung beendet wurde und fehlgeschlagen ist, eine Ausführung beendet wurde und erfolgreich war, usw.).
 
	Klicken Sie auf den Titel der Listen oder **... (Ellipsen)** die Liste die größere Segmente anzeigen zu können. Klicken Sie auf **Filter** auf der Symbolleiste, um Segmente zu filtern.
	
	Klicken Sie zum Anzeigen der Datenslices sortiert nach den Slice Start-/Endzeiten stattdessen **Datenslices (von Slice-Zeit)** Kachel.

	![Datenslices Slice Zeit][DataSlicesBySliceTime]
 
7. Fangen wir an der **PIPELINE** Blade für **PartiionGameLogsPipeline**, klicken Sie auf **produziert**.
8. Daraufhin wird die Liste der Datasets angezeigt, die diese Pipeline produziert: 
9. Klicken Sie auf **PartitionedGameEvents** -Tabelle in der **erstellten Datasets** Blade. 
10.	Überprüfen Sie, ob die **Status** alle Segmente minFreeThreads auf **bereit**. 
11.	Klicken Sie auf eines der Segmente, die **bereit** finden Sie unter der **DATENSLICE** Blade für dieses Segment.

	![RawGameEventsTable DATENSLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Wenn ein Fehler aufgetreten ist, sehen Sie eine **Fehler **Status ein. Darüber hinaus möglicherweise entweder beide Segmente mit dem Status **bereit**, oder beide mit dem Status **PendingValidation**, je nachdem wie schnell die Segmente verarbeitet werden.

	Wenn das Segment nicht in die **bereit** Status, sehen Sie die upstream Segmente, die nicht bereit sind, und blockieren das aktuelle Segment in Ausführen der **Upstream-Segmente, die nicht bereit sind** Liste.
 
	Finden Sie in der [Azure Data Factory Entwicklerreferenz][developer-reference] um einen Überblick über alle möglichen Slice Status abzurufen.

12.	In der **DATENSLICE** Blade, klicken Sie auf das Ausführen von der **Aktivität führt** Liste. Daraufhin sollte das Fenster „Aktivitätsausführung“ für diesen Slice angezeigt werden. Sollte eine die folgenden **AKTIVITÄTSDETAILS ausführen** Blade.

	![Aktivität Testlaufdetails blade][image-data-factory-monitoring-activity-run-details]

13.	Klicken Sie auf **herunterladen** zum Herunterladen der Dateien. Dieser Bildschirm ist besonders dann hilfreich, wenn Sie Fehler der HDInsight-Verarbeitung beheben.
	 
	
Wenn alle Pipeline Ausführung abgeschlossen haben, suchen Sie in der **MarketingCampaignEffectivenessTable** in den **MarketingCampaigns** Azure SQL-Datenbank, um die Ergebnisse anzuzeigen.

**Glückwunsch!** Sie können jetzt die Workflows überwachen und Fehler beheben. Sie haben gelernt, wie Azure Data Factory zum Verarbeiten von Daten und Erstellen von Analysen verwendet wird.

## Erweitern Sie das Lernprogramm zum Verwenden von lokalen Daten
Im letzten Schritt der Protokollverarbeitung Szenario aus der exemplarischen Vorgehensweise in diesem Artikel wurde die marketing Kampagne Effektivität Ausgabe in einer Azure SQL-Datenbank kopiert. Sie können diese Daten auch in einen lokalen SQL Server für Analysen innerhalb Ihrer Organisation verschieben.
 
Die Kampagne Marketingdaten der Effektivität von Azure-Blob, das Kopieren auf lokale SQL Server ist, müssen Sie zusätzliche lokale verknüpfte Dienst erstellen, Tabelle und der Pipeline, die in der exemplarischen Vorgehensweise in diesem Artikel eingeführt.

Praxis der [Exemplarische Vorgehensweise: verwenden lokale Datenquelle][tutorial-onpremises] Informationen zum Erstellen einer Pipeline um Marketingdaten Kampagne Effektivität in einer lokalen SQL Server-Datenbank zu kopieren.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

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

<!---HONumber=GIT-SubDir--> 