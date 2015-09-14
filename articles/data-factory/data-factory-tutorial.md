<properties 
	pageTitle="Verschieben und Verarbeiten von Protokolldateien mit Azure Data Factory (Azure-Portal)"
	description="In diesem fortgeschrittenen Tutorial wird ein sehr praxisnahes Szenario beschrieben und mithilfe des Azure Data Factory-Diensts und des Data Factory-Editors im Azure-Portal implementiert."
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
	ms.date="08/25/2015"
	ms.author="spelluru"/>

# Lernprogramm: Messen der Wirksamkeit von Marketingkampagnen  
Contoso ist ein Spielehersteller, der Spiele für mehrere Plattformen entwickelt. Hierzu zählen Spielekonsolen, mobile Geräte und PCs. Diese Spiele generieren zahlreiche Protokolle. Contoso möchte diese Protokolle sammeln und analysieren, um Einblicke in die Kundenvorlieben, die Altersverteilung, das Nutzungsverhalten usw. zu gewinnen. Der Zweck ist, Up- und Cross-Selling-Möglichkeiten zu finden, neue spannende Features zu entwickeln sowie das Wachstum des Unternehmens und die Zufriedenheit der Kunden zu steigern.

In diesem Lernprogramm erstellen Sie Data Factory-Pipelines, um die Wirksamkeit einer vor Kurzem von Contoso gestarteten Marketingkampagne zu bewerten. Dazu werden Beispielprotokolle gesammelt, verarbeitet, mit Verweisdaten angereichert und anschließend umgewandelt. Folgende Pipelines werden verwendet:

1.	Die **PartitionGameLogsPipeline** liest die Rohdaten der Spielereignisse aus dem Blobspeicher und erstellt Partitionen basierend auf Jahr, Monat und Tag.
2.	Die **EnrichGameLogsPipeline** verknüpft partitionierte Spielereignisse mit geografisch codierten Verweisdaten und reichert die Daten an, indem sie die IP-Adressen entsprechenden geografischen Standorten zuordnet.
3.	Die **AnalyzeMarketingCampaignPipeline** verarbeitet die angereichten Daten zusammen mit den Werbedaten, um die endgültige Ausgabe zu erstellen, die Aufschluss über die Wirksamkeit der Marketingkampagne gibt.

## Vorbereitungen für das Lernprogramm
1.	Lesen Sie die [Einführung in Azure Data Factory][adfintroduction], um sich einen Überblick über Azure Data Factory zu verschaffen und sich mit den wichtigsten Konzepten vertraut zu machen.
2.	Sie benötigen ein Azure-Abonnement, um dieses Lernprogramm durchzuführen. Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](http://azure.microsoft.com/pricing/purchase-options/), [Spezielle Angebote](http://azure.microsoft.com/pricing/member-offers/) oder [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).
3.	Sie müssen [Azure PowerShell][download-azure-powershell] auf Ihren Computer herunterladen und installieren. Beispieldaten und Pig-/Hive-Skripts werden mithilfe von Data Factory-Cmdlets an den Blobspeicher hochgeladen. 
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
	- Führen Sie **Add-AzureAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung für das Azure-Vorschauportal verwendet haben.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Vorschauportal verwendet haben.	

## Übersicht
Nachfolgend ist der End-to-End-Workflow dargestellt:

![Ablauf des Lernprogramms][image-data-factory-tutorial-end-to-end-flow]

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


6. [Schritt 6: Überwachen von Pipelines und Datenslices](#MainStep6). In diesem Schritt überwachen Sie die Pipelines, Tabellen und Datenslices mithilfe des Azure-Portals.

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
   
2. Bestätigen Sie, dass der lokale Computer auf die Azure SQL-Datenbank zugreifen darf. Verwenden Sie das Azure-Verwaltungsportal oder **sp\_set\_firewall\_rule** in der Masterdatenbank, um eine Firewallregel für die IP-Adresse auf dem Computer zu erstellen und so den Zugriff zu aktivieren. Es kann bis zu fünf Minuten dauert, bis diese Änderung wirksam wird. Unter [Festlegen von Firewallregeln für Azure SQL][azure-sql-firewall] finden Sie weitere Informationen.
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

1.	Klicken Sie nach der Anmeldung beim [Azure-Vorschauportal][azure-preview-portal] links unten auf **NEU**, auf dem Blatt **Erstellen** auf **Datenanalyse** und anschließend auf dem Blatt **Datenanalyse** auf **Data Factory**. 

	![Neu -> Data Factory][image-data-factory-new-datafactory-menu]

5. Geben Sie auf dem Blatt **Neue Data Factory** unter **Name** die Zeichenfolge **LogProcessingFactory** ein.

	![Blatt „Data Factory“][image-data-factory-tutorial-new-datafactory-blade]

6. Wenn Sie noch keine Azure-Ressourcengruppe mit dem Namen **ADF** erstellt haben, gehen Sie folgendermaßen vor:
	1. Klicken Sie auf **RESSOURCENGRUPPENNAME** und anschließend auf **Neue Ressourcengruppe erstellen**.
	
		![Blatt „Ressourcengruppe“][image-data-factory-tutorial-resourcegroup-blade]
	2. Geben Sie auf dem Blatt **Ressourcengruppe erstellen** als Name der Ressourcengruppe die Zeichenfolge **ADF** ein, und klicken Sie auf **OK**.
	
		![Ressourcengruppe erstellen][image-data-factory-tutorial-create-resourcegroup]
7. Wählen Sie **ADF** als **RESSOURCENGRUPPENNAME**.  
8.	Hinweis: Auf dem Blatt **Neue Data Factory** ist standardmäßig **Zum Startmenü hinzufügen** aktiviert. Dadurch wird eine Verknüpfung zur Data Factory im Startmenü (was Sie sehen, wenn Sie sich beim Azure-Vorschauportal anmelden) hinzugefügt.

	![Blatt „Data Factory erstellen“][image-data-factory-tutorial-create-datafactory]

9.	Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**, um die Data Factory zu erstellen.
10.	Nach der Erstellung der Data Factory wird das Blatt **DATA FACTORY** mit dem Titel **LogProcessingFactory** angezeigt.

	![Data Factory-Startseite][image-data-factory-tutorial-datafactory-homepage]

	
	Ist dies nicht der Fall, führen Sie einen der folgenden Schritte aus:

	- Klicken Sie im Startmenü (auf der Startseite) auf **LogProcessingFactory**.
	- Klicken Sie links auf **DURCHSUCHEN**, dann auf **Alles**, danach auf **Data Factorys** und schließlich auf die Data Factory.
 
	Der Name der Azure Data Factory muss global eindeutig sein. Sollte die Fehlermeldung **Data Factory-Name "LogProcessingFactory" nicht verfügbar** erscheinen, ändern Sie den Namen (z. B.in „IhrNameLogProcessingFactory“). Verwenden Sie diesen Namen bei den Schritten in diesem Lernprogramm anstelle von „LogProcessingFactory“.
 
## <a name="MainStep3"></a> Schritt 3: Erstellen von verknüpften Diensten

> [AZURE.NOTE]In diesem Artikel werden mithilfe des Azure-Portals und insbesondere des Data Factory-Editors verknüpfte Dienste, Tabellen und Pipelines erstellt. Wenn Sie dieses Lernprogramm mit Azure PowerShell ausführen möchten, lesen Sie unter [Lernprogramm mit Azure PowerShell][adftutorial-using-powershell] weiter.

In diesem Schritt erstellen Sie die folgenden verknüpften Dienste:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Erstellen von „StorageLinkedService“ und „HDInsightStorageLinkedService“

1.	Klicken Sie auf dem Blatt **DATA FACTORY** auf die Kachel **Erstellen und bereitstellen**, um den Editor für die Data Factory zu starten.

	![Kachel „Erstellen und bereitstellen“][image-author-deploy-tile]

	Im Thema [Data Factory-Editor][data-factory-editor] finden Sie eine ausführliche Übersicht über den Data Factory-Editor.

2.  Klicken Sie im **Editor** auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü **Azure-Speicher** aus. Die JSON-Vorlage zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im rechten Bereich angezeigt werden.
	
	![Editor – Schaltfläche „Neuer Datenspeicher“][image-editor-newdatastore-button]

3. Ersetzen Sie **accountname** und **accountkey** durch die Werte für den Kontonamen und -schlüssel Ihres Azure-Speicherkontos.

	![Editor – Blobspeicher – JSON][image-editor-blob-storage-json]
	
	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst „StorageLinkedService“ bereitzustellen. Vergewissern Sie sich, dass die Meldung **VERKNÜPFTER DIENST ERFOLGREICH ERSTELLT** auf der Titelleiste des Editors angezeigt wird.

	![Editor – Blobspeicher bereitstellen][image-editor-blob-storage-deploy]

5. Wiederholen Sie die Schritte, um für den Speicher, der Ihrem HDInsight-Cluster zugeordnet ist, einen weiteren, mit Azure Storage verknüpften Dienst namens **HDInsightStorageLinkedService** zu erstellen. Ändern Sie im JSON-Skript für den verknüpften Dienst den Wert der Eigenschaft **name** in **HDInsightStorageLinkedService**.

### Erstellen von „AzureSqlLinkedService“
1. Klicken Sie im Data Factory-Editor auf der Symbolleiste auf die Schaltfläche **Neuer Datenspeicher**, und wählen Sie im Dropdownmenü **Azure SQL-Datenbank** aus. Die JSON-Vorlage zum Erstellen eines mit der Azure SQL-Datenbank verknüpften Diensts sollte im rechten Bereich angezeigt werden.
2. Ersetzen Sie **servername**, ****username@servername** und **password** durch Namen von Azure SQL-Server, -Benutzerkonto und -Kennwort.
3. Ersetzen Sie **databasename** durch **MarketingCampaigns**. Dies ist die Azure SQL-Datenbank, die mithilfe der Skripts erstellt wurde, die Sie in Schritt 1 ausgeführt haben. Vergewissern Sie sich im Falle eines Fehlers, dass die Datenbank tatsächlich erstellt wurde. 
3. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst „AzureSqlLinkedService“ zu erstellen und bereitzustellen.

### Erstellen von „HDInsightLinkedService“
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung Ihres eigenen HDInsight-Clusters kann der Cluster den Slice jedoch sofort verarbeiten. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters. Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster.

#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster
1. Klicken Sie auf der Befehlsleiste auf **Neu -> Compute**, und wählen Sie im Menü **On-Demand-HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus: 
	1. Geben Sie für die Eigenschaft **clusterSize** die Größe des HDInsight-Clusters an.
	2. Geben Sie für die Eigenschaft **jobsContainer** den Namen des Standardcontainers an, in dem die Clusterprotokolle gespeichert werden sollen. Geben Sie im Rahmen dieses Lernprogramms **adfjobscontainer** an.
	3. Geben Sie für die **timeToLive**-Eigenschaft an, wie lange sich der Cluster im Leerlauf befinden darf, bevor er gelöscht wird. 
	4. Geben Sie für die **version**-Eigenschaft die HDInsight-Version an, die Sie verwenden möchten. Wenn Sie diese Eigenschaft ausschließen, wird die neueste Version verwendet.  
	5. Geben Sie für **linkedServiceName** die Zeichenfolge **HDInsightStorageLinkedService** an. Diesen Dienst haben Sie im Lernprogramm „Erste Schritte“ erstellt. 

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

		Achten Sie darauf, dass **type** für den verknüpften Dienst auf **HDInsightOnDemandLinkedService** festgelegt ist.

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.
   
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. Klicken Sie auf der Befehlsleiste auf **Neu -> Compute**, und wählen Sie im Menü **HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus: 
	1. Geben Sie für die **clusterUri**-Eigenschaft die URL für Ihr HDInsight ein. Beispiel: https://<clustername>.azurehdinsight.net/     
	2. Geben Sie für die **UserName**-Eigenschaft den Benutzernamen ein, der Zugriff auf den HDInsight-Cluster hat.
	3. Geben Sie für die **Password**-Eigenschaft das Kennwort für das Benutzerkonto ein. 
	4. Geben Sie für die **LinkedServiceName**-Eigenschaft das **StorageLinkedService**-Element ein. Dies ist der verknüpfte Dienst, den Sie im Lernprogramm „Erste Schritte“ erstellt haben. 

	Achten Sie darauf, dass **type** für den verknüpften Dienst auf **HDInsightBYOCLinkedService** festgelegt ist (BYOC steht für „Bring Your Own Cluster“).

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.


## <a name="MainStep4"></a> Schritt 4: Erstellen von Tabellen
 
In diesem Schritt werden die folgenden Data Factory-Tabellen erstellt:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Ablauf des Lernprogramms][image-data-factory-tutorial-end-to-end-flow]
 
In der Abbildung oben sind die Pipelines in der mittleren Zeile und die Tabellen in der oberen und unteren Zeile dargestellt.

### So erstellen Sie die Tabellen
	
1. Klicken Sie im Editor für die Data Factory auf der Symbolleiste auf **Neues Dataset** und anschließend im Dropdownmenü auf **Azure-Blobspeicher**. 
2. Ersetzen Sie JSON im rechten Bereich durch das JSON-Skript aus der Datei **RawGameEventsTable.json** im Ordner **C:\\ADFWalkthrough\\Tables**.
3. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um die Tabelle bereitzustellen. Vergewissern Sie sich auf der Titelleiste des Editors, dass die Meldung **TABELLE ERFOLGREICH ERSTELLT** angezeigt wird.
4. Wiederholen Sie die Schritte 1 bis 3 mit dem Inhalt der folgenden Dateien: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Wiederholen Sie die Schritte 1 bis 3 mit dem Inhalt der folgenden Datei. Wählen Sie dabei aber nach dem Klicken auf **Neues Dataset** die Option **Azure SQL** aus.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Schritt 5: Erstellen und Planen von Pipelines
In diesem Schritt erstellen Sie die folgenden Pipelines:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### So erstellen Sie Pipelines:

1. Klicken Sie in **Data Factory Editor** in der Symbolleiste auf die Schaltfläche **Neue Pipeline**. Klicken Sie in der Symbolleiste auf **... (drei Punkte)**, wenn die Schaltfläche nicht angezeigt wird. Alternativ können Sie in der Strukturansicht mit der rechten Maustaste auf **Pipelines** und dann auf **Neue Pipeline** klicken.
2. Ersetzen Sie JSON im rechten Bereich durch das JSON-Skript aus der Datei **PartitionGameLogsPipeline.json** im Ordner **C:\\ADFWalkthrough\\Pipelines**.
3. Fügen Sie ein **Komma (',')** am Ende der **schließenden eckigen Klammer ('] ')** in JSON hinzu, und fügen Sie hinter der schließenden eckigen Klammer die folgenden drei Zeilen hinzu. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Beachten Sie, dass die Start- und Endzeiten auf 05/01/2014 und 05/05/2014 festgelegt sind, da sich die Beispieldaten in dieser exemplarischen Vorgehensweise auf den Zeitraum von 05/01/2014 bis 05/05/2014 beziehen.
 
3. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um die Pipeline bereitzustellen. Vergewissern Sie sich, dass die Nachricht **PIPELINE ERFOLGREICH ERSTELLT** in der Titelleiste des Editors angezeigt wird.
4. Wiederholen Sie die Schritte 1 bis 3 mit dem Inhalt der folgenden Dateien: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Schließen Sie die Data Factory-Blätter durch Klicken auf **X** (rechts oben), um die Startseite (das Blatt **DATA FACTORY**) Ihrer Data Factory anzuzeigen.

### Diagrammansicht

1. Klicken Sie auf dem Blatt **DATA FACTORY** für **LogProcessingFactory** auf **Diagramm**. 

	![Link „Diagramm“][image-data-factory-tutorial-diagram-link]

2. Sie können das angezeigte Diagramm neu anordnen; hier sehen Sie ein neu angeordnetes Diagramm, in dem direkte Eingabe oben und Ausgaben unten angezeigt werden. Wie Sie sehen, wird die Ausgabe von **PartitionGameLogsPipeline** als Eingabe für **EnrichGameLogsPipeline** übernommen, und die Ausgabe von **EnrichGameLogsPipeline** wird an „AnalyzeMarketingCampaignPipeline“ übergeben. Doppelklicken Sie auf einen Titel, um Details über das Artefakt anzuzeigen, das das Fenster darstellt.

	![Diagrammansicht][image-data-factory-tutorial-diagram-view]

3. Klicken Sie mit der rechten Maustaste auf **AnalyzeMarketingCampaignPipeline**, und klicken Sie auf **Pipeline öffnen**. Alle Aktivitäten in der Pipeline sowie Ein- und Ausgabedatasets für die Aktivitäten werden angezeigt.
 
	![Pipeline öffnen](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Klicken Sie auf der Breadcrumb-Leiste links oben auf **Data Factory**, um zur Diagrammansicht mit allen Pipelines zurückzukehren.


**Glückwunsch!** Sie haben die Azure Data Factory, die verknüpften Dienste, Pipelines und Tabellen erfolgreich erstellt und den Workflow gestartet.


## <a name="MainStep6"></a> Schritt 6: Überwachen von Pipelines und Datenslices 

1.	Wenn das Blatt **DATA FACTORY** für **LogProcessingFactory** nicht geöffnet ist, können Sie einen der folgenden Schritte ausführen:
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

	Die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** werden nach **UHRZEIT DER LETZTEN AKTUALISIERUNG** sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.

	-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set AzureDataFactorySliceStatus** oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
	-  Der Status des Slices ändert sich aufgrund einer Ausführung (z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.).
 
	Klicken Sie auf den Titel der Listen oder auf **...** (Auslassungspunkte), um eine umfangreichere Liste mit Slices anzuzeigen. Klicken Sie auf der Symbolleiste auf **Filter**, um die Slices zu filtern.
	
	Zum Anzeigen der nach Start-/Endzeit sortierten Datenslices klicken Sie auf die Kachel **Datenslices (nach Slicezeit)**.

	![Datenslices nach Slicezeit][DataSlicesBySliceTime]
 
7. Klicken Sie nun auf dem Blatt **PIPELINE** für **PartionGameLogsPipeline** auf **Erstellt**.
8. Daraufhin wird die Liste der Datasets angezeigt, die diese Pipeline produziert: 
9. Klicken Sie auf dem Blatt **Erstellte Datasets** auf die Tabelle **PartitionedGameEvents**. 
10.	Vergewissern Sie sich, dass der Status aller Slices **Bereit** lautet. 
11.	Klicken Sie auf einen der Slices mit dem Status **Bereit**, um das Blatt **DATENSLICE** für diesen Slice anzuzeigen.

	![Blatt des Datenslices „RawGameEventsTable“][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Im Falle eines Fehlers wird hier der Status **Fehler** angezeigt. Möglicherweise werden auch beide Slices mit dem Status **Bereit** oder beide Slices mit dem Status **Ausstehende Überprüfung** angezeigt – je nachdem, wie schnell die Slices verarbeitet werden.

	Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slices blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**.
 
	Die [Azure Data Factory-Entwicklerreferenz][developer-reference] enthält Informationen zu allen möglichen Slicestatusoptionen.

12.	Klicken Sie auf dem Blatt **DATENSLICE** in der Liste **Aktivitätsausführungen** auf die Ausführung. Daraufhin sollte das Fenster „Aktivitätsausführung“ für diesen Slice angezeigt werden. Das Blatt **AKTIVITÄTSAUSFÜHRUNG – DETAILS** wird angezeigt:

	![Blatt „Aktivitätsausführung – Details“][image-data-factory-monitoring-activity-run-details]

13.	Klicken Sie auf **Herunterladen**, um die Dateien herunterzuladen. Dieser Bildschirm ist besonders dann hilfreich, wenn Sie Fehler der HDInsight-Verarbeitung beheben.
	 
	
Wenn die Ausführung aller Pipelines abgeschlossen ist, können Sie sich die Ergebnisse in **MarketingCampaignEffectivenessTable** in der Azure SQL-Datenbank **MarketingCampaigns** ansehen.

**Glückwunsch!** Sie können jetzt die Workflows überwachen und Fehler beheben. Sie haben gelernt, wie Azure Data Factory zum Verarbeiten von Daten und Erstellen von Analysen verwendet wird.

## Erweitern des Lernprogramms mit lokalen Daten
Im letzten Schritt des Protokollverarbeitungsszenarios in der exemplarischen Vorgehensweise dieses Artikels wurde die Ausgabe zur Wirksamkeit der Marketingkampagne in eine Azure SQL-Datenbank kopiert. Sie können diese Daten auch in einen lokalen SQL Server für Analysen innerhalb Ihrer Organisation verschieben.
 
Um die Daten zur Wirksamkeit der Marketingkampagne aus dem Azure-Blob in eine lokale SQL Server-Datenbank zu kopieren, müssen Sie einen zusätzlichen lokalen verknüpften Dienst, eine Tabelle und eine Pipeline erstellen. Dies wurde in dieser exemplarischen Vorgehensweise erläutert.

In [Exemplarische Vorgehensweise: Verwenden einer lokalen Datenquelle][tutorial-onpremises] erfahren Sie, wie Sie eine Pipeline erstellen, die die Daten zur Wirksamkeit einer Marketingkampagne in eine lokale SQL Server-Datenbank kopiert.

## Feedback senden
Über Ihr Feedback zu diesem Artikel würden wir uns sehr freuen. Bitte nehmen Sie sich einen Moment Zeit, und senden Sie uns Ihr Feedback per [E-Mail](mailto:adfdocfeedback@microsoft.com?subject=data-factory-tutorial.md).

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
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

<!---HONumber=September15_HO1-->