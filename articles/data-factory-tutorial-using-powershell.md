<properties 
	pageTitle="Verschieben und Verarbeiten von Protokolldateien mit Azure Data Factory" 
	description="In diesem fortgeschrittenen Lernprogramm wird ein nahezu reales Szenario beschrieben und mithilfe des Azure Data Factory-Diensts implementiert." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Lernprogramm: Verschieben und Verarbeiten von Protokolldateien, die mit Daten Factory [PowerShell]
Dieser Artikel bietet eine umfassende exemplarische Vorgehensweise eines kanonischen Szenarios der Protokollverarbeitung mithilfe von Azure Data Factory zum Transformieren von Daten aus Protokolldateien in Einblicke.

## Szenario
Contoso ist ein Spiele-Unternehmen, das Spielen für mehrere Plattformen erstellt: game-Konsolen, tragbare Geräte und Computer (PCs). Jedes dieser Spiele erzeugt Unmengen von Protokollen. Ziel von Contoso ist es, die von diesen Spielen produzieren Protokolle zu erfassen und zu analysieren, um Nutzungsinformationen zu erhalten, Up-Selling- und Cross-Selling-Möglichkeiten zu identifizieren, neue spannende Features zu entwickeln usw., um das Unternehmen zu verbessern und Kunden eine bessere Erfahrung zu bieten.
 
In dieser exemplarischen Vorgehensweise werden Beispielprotokolle gesammelt, verarbeitet und mit Verweisdaten angereichert; die Daten werden zur Bewertung der Wirksamkeit einer Marketingkampagne, die Contoso vor kurzem gestartet hat, transformiert.

## Vorbereitungen für das Lernprogramm
1.	Lesen [Einführung in Azure Data Factory][adfintroduction] um einen Überblick über Azure Data Factory und Kenntnisse über die Konzepte der obersten Ebene abzurufen.
2.	Sie benötigen ein Azure-Abonnement, um dieses Lernprogramm durchzuführen. Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
3.	Sie müssen auch herunterladen und installieren [Azure PowerShell][download-azure-powershell] auf Ihrem Computer. 
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
Der End-to-End-Workflow wird im folgenden dargestellt: ![Lernprogramm End-to-End-Nachrichtenfluss][image-data-factory-tutorial-end-to-end-flow]

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

1.	Nach der Anmeldung die [Azure Preview Portal][azure-preview-portal], klicken Sie auf **Neu** aus der linken unteren Ecke, und klicken Sie auf **Data Factory** auf der **Neu** Blade. 

	![Neu -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Wenn Sie nicht sehen, **Data Factory** auf der **Neu** Fenster einen Bildlauf nach unten.
	
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

> [AZURE.NOTE]In diesem Artikel verwendet die Azure PowerShell zum Erstellen von verknüpften Dienste, Tabellen und Pipelines. Finden Sie unter [Tutorial Factory mit][adftutorial-using-editor] wenn Sie dieses Lernprogramm mit der Azure-Verwaltungsportal, insbesondere Data Factory-Editor ausführen möchten.

In diesem Schritt erstellen Sie die folgenden verknüpften Dienste: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService und HDInsightLinkedService.


1.	In der **LogProcessingFactory** Blade, klicken Sie auf **verknüpften Dienste** Kachel.

	![Verknüpfte Dienste-Kachel][image-data-factory-tutorial-linkedservice-tile]

2. In der **verknüpften Dienste** Blade, klicken Sie auf **+ -Datenspeicher** in der Befehlsleiste.

	![Verknüpfte Dienste - hinzufügen Informationsspeicher][image-data-factory-tutorial-linkedservices-add-datstore]

3. In der **neuen Datenspeicher** Blade, geben Sie **StorageLinkedService** für die **Name**, klicken Sie auf **Typ (Einstellungen erforderlich)**, und wählen Sie **Azure-Speicherkonto**.

	![Typ der Datenspeicher - Azure-Speicher][image-data-factory-tutorial-datastoretype-azurestorage]

4. In der **neuen Datenspeicher** Blade, sehen Sie zwei neue Felder: **Kontoname** und **Kontoschlüssel**. Geben Sie Kontonamen und Kontoschlüssel für Ihre **Azure Storage-Konto**.

	![Azure-Speicher-Einstellungen][image-data-factory-tutorial-azurestorage-settings]

	Den Kontonamen und Kontoschlüssel für Ihr Azure-Speicherkonto können Sie wie folgt dargestellt aus dem Portal abrufen:

	![Speicherschlüssel][image-data-factory-tutorial-storage-key]
  
5. Nachdem Sie auf **OK** auf die neuen Daten zu speichern Blade, sollte eine **StorageLinkedService** in der Liste der **DATENSPEICHER** auf der **verknüpften Dienste** Blade. Überprüfen Sie **Benachrichtigungen** Hub (auf der linken Seite) alle Nachrichten anzeigen.

	![Verknüpfte Dienste Blade mit Speicher][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Wiederholen Sie die **die Schritte 2 bis 5** erstellen Sie einen anderen Dienst namens verknüpft: **HDInsightStorageLinkedService**. Dies ist der Speicher, der von Ihrem HDInsight-Cluster verwendet wird.
7. Vergewissern Sie sich, dass Sie beide finden Sie unter **StorageLinkedService** und **HDInsightStorageLinkedService** in der Liste im verknüpften Dienste Blade.
8. In der **verknüpften Dienste** Blade, klicken Sie auf **Hinzufügen (+) Datenspeicher** in der Befehlsleiste.
9. Geben Sie **AzureSqlLinkedService** für den Namen.
10. Klicken Sie auf **Typ (erforderlichen Einstellungen)**, auf **Azure SQL-Datenbank**.
11. Nun sollten Sie die folgenden zusätzlichen Felder auf der **neuen Datenspeicher** Blade. Geben Sie die Namen der SQL Azure-Datenbank **Server**, **Datenbank** Name **Benutzername**, und **Kennwort**, und klicken Sie auf **OK**.
	1. Geben Sie **MarketingCampaigns** für die **Datenbank**. Dies ist der Azure SQL-Datenbank erstellt, die von den Skripts, die Sie in Schritt 1 ausgeführt haben. Sie sollten sicherstellen, dass diese Datenbank (für den Fall, dass der Fehler) tatsächlich von den Skripts erstellt wurde.
		
 		![Azure SQL-Einstellungen][image-data-factory-tutorial-azuresql-settings]

		Zum Abrufen dieser Werte aus dem Azure-Verwaltungsportal: Klicken Sie auf die Ansicht SQL-Datenbank-Verbindungszeichenfolgen für MarketingCampaigns-Datenbank

		![Azure SQL-Datenbank-Verbindungszeichenfolge][image-data-factory-tutorial-azuresql-database-connection-string]

12. Vergewissern Sie sich, dass die drei Datenspeicher finden Sie unter erstellt haben: **StorageLinkedService**, **HDInsightStorageLinkedService**, und **AzureSqlLinkedService**.
13. Müssen Sie einen anderen verknüpften Dienst zu erstellen, aber dies ist ein Compute-Dienst speziell **Azure HDInsight-Cluster**. Das Erstellen eines verknüpften Serverdiensts wird vom Portal noch nicht unterstützt. Aus diesem Grund müssen Sie Azure PowerShell verwenden, um diesen verknüpften Dienst zu erstellen. 
14. Wechseln Sie zu **Azure PowerShell** ist bereits geöffnet (oder) starten **Azure PowerShell**. Wenn Sie geschlossen und erneut geöffnet Azure PowerShell, müssen Sie die folgenden Befehle ausführen: 
	- Führen Sie **Add-AzureAccount** und geben Sie den Benutzernamen und das Kennwort, das Sie verwenden, um das Azure Preview Portal anmelden.  
	- Führen Sie **Get-AzureSubscription** alle Abonnements für dieses Konto anzeigen.
	- Führen Sie **Select-AzureSubscription** das Abonnement aus, die Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Vorschauportal verwendet haben. 
15. Wechseln Sie zu **AzureResourceManager** Modus als "Data Factory" Azure-Cmdlets sind in diesem Modus verfügbar.

		Switch-AzureMode AzureResourceManager

16. Navigieren Sie zu den **LinkedServices** Unterordner im **C:\ADFWalkthrough** (oder) aus dem Ordner aus dem Speicherort, in dem Sie die Dateien extrahiert haben.
17. Open **HDInsightLinkedService.json** in Ihrem bevorzugten Editor und beachten Sie, dass der Typ ist an **HDInsightOnDemandLinkedService**.


	> [AZURE.NOTE]Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung Ihres eigenen HDInsight-Clusters kann der Cluster den Slice jedoch sofort verarbeiten. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters. Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster. Die HDInsightLinkedService verknüpft einen HDInsight-Cluster bei Bedarf mit der Factory Daten. Um HDInsight-Cluster verwenden, aktualisieren Sie im Eigenschaften-Abschnitt der Datei HDInsightLinkedService.json wie folgt (Replace Clustername, Benutzername und Kennwort mit den entsprechenden Werten):
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. Verwenden Sie den folgenden Befehl aus, um die $df-Variable auf den Namen der Data Factory festzulegen.

		$df = “LogProcessingFactory”
19. Verwenden Sie das Cmdlet **neu AzureDataFactoryLinkedService** wie folgt einen verknüpften Dienst zu erstellen. Beginnen Sie mit dem Speicherkonto:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Wenn Sie einen anderen Namen für ResourceGroupName, DataFactoryName oder LinkedService verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad des verknüpften Diensts der JSON-Datei an, wenn die Datei nicht gefunden wird.
20. Daraufhin sollte alle vier verknüpfte Dienste in der **verknüpften Dienste** Blade wie unten dargestellt. Wenn das verknüpfte Dienste Blade nicht geöffnet ist, klicken Sie auf Verknüpfte Dienste in der **DATA FACTORY** für Seite **LogProcessingFactory**. Es dauert möglicherweise ein paar Sekunden, bis das Fenster „Verknüpfte Dienste“ aktualisiert wird.

	![Services verknüpft alle][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Schritt 4: Erstellen von Tabellen 
In diesem Schritt erstellen Sie die folgenden Tabellen:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Lernprogramm End-to-End-Nachrichtenfluss][image-data-factory-tutorial-end-to-end-flow]
 
In der Abbildung oben sind die Pipelines in der mittleren Zeile und die Tabellen in der oberen und unteren Zeile dargestellt.

Das Erstellen von Datasets/Tabellen wird vom Azure-Verwaltungsportal noch nicht unterstützt, Sie müssen daher Azure PowerShell verwenden, um in dieser Version Tabellen zu erstellen.

### So erstellen Sie die Tabellen

1.	In der Azure-PowerShell, navigieren Sie zu den **Tabellen** Ordner (** C:\ADFWalkthrough\Tables**) aus dem Speicherort, in dem Sie die Beispiele extrahiert haben. 
2.	Verwenden Sie das Cmdlet **neu AzureDataFactoryTable** zum Erstellen von der Tabellen wie folgt für **RawGameEventsTable**JSON	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Wenn Sie einen anderen Namen für ResourceGroupName und DataFactoryName verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad der Tabellen-JSON-Datei an, wenn die Datei vom Cmdlet nicht gefunden wird.

3. Wiederholen Sie den vorherigen Schritt, um die folgenden Tabellen zu erstellen:
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. In der **Azure Preview Portal**, klicken Sie auf **Datasets** in den **DATA FACTORY** Blade für **LogProcessingFactory** und vergewissern Sie sich, dass Sie alle Datasets finden Sie unter (Tabellen sind rechteckig Datasets).

	![Alle Datensätze][image-data-factory-tutorial-datasets-all]

	Sie können auch den folgenden Befehl in Azure PowerShell verwenden:
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Schritt 5: Erstellen und Planen von pipelines
In diesem Schritt erstellen Sie die folgenden Pipelines: PartitionGameLogsPipeline, EnrichGameLogsPipeline und AnalyzeMarketingCampaignPipeline.

1. In **Windows Explorer**, navigieren Sie zu den **Pipelines** Unterordner in **C:\ADFWalkthrough** Ordner (oder von dem Speicherort, in dem Sie die Beispiele extrahiert haben).
2.	Open **PartitionGameLogsPipeline.json** in Ihrem bevorzugten Editor hervorgehobenen ersetzen, mit dem Speicherkonto für die Data Storage-Kontoinformationen, und speichern Sie die Datei.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Wiederholen Sie den Schritt, um die folgenden Pipelines zu erstellen:
	1. **EnrichGameLogsPipeline**JSON (3 Vorkommen)
	2. **AnalyzeMarketingCampaignPipeline**JSON (3 Vorkommen)

	**Wichtig:** bestätigen, dass Sie alle ersetzt haben <storageaccountname> mit den Namen des Speicherkontos.
 
4.  In **Azure PowerShell**, navigieren Sie zu den **Pipelines** Unterordner in **C:\ADFWalkthrough** Ordner (oder von dem Speicherort, in dem Sie die Beispiele extrahiert haben).
5.  Verwenden Sie das Cmdlet **neu AzureDataFactoryPipeline** die Pipelines für folgendermaßen erstellen **PartitionGameLogspeline**JSON	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Wenn Sie einen anderen Namen für ResourceGroupName, DataFactoryName oder PipelineName verwenden, müssen Sie im Cmdlet oben darauf verweisen. Geben Sie außerdem den vollständigen Dateipfad der Pipeline-JSON-Datei an.
6. Wiederholen Sie den vorherigen Schritt, um die folgenden Pipelines zu erstellen:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Verwenden Sie das Cmdlet **Get-AzureDataFactoryPipeline** die Auflistung der Pipelines abgerufen.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Sobald die Pipelines erstellt wurden, können Sie die Dauer angeben, in der die Datenverarbeitung von Daten erfolgt. Durch Angeben des aktiven Zeitraums für eine Pipeline definieren Sie die Zeitdauer, in der die Datenslices basierend auf den Verfügbarkeitseigenschaften, die für jede ADF-Tabelle definiert wurden, verarbeitet werden.

Um den aktiven Zeitraum für die Pipeline anzugeben, können Sie das Cmdlet „Set-AzureDataFactoryPipelineActivePeriod“ verwenden. In dieser exemplarischen Vorgehensweise werden die Beispieldaten aus 05/01-05/05. Verwendung 2014-05-01 als die StartDateTime. EndDateTime ist optional.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Vergessen Sie nicht, den aktiven Zeitraum für die Pipeline festzulegen.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Wiederholen Sie die vorherigen beiden Schritte, um den aktiven Zeitraum für die folgenden Pipelines festzulegen.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. In der **Azure Preview Portal**, klicken Sie auf **Pipelines** Kachel (nicht auf die Namen von Pipelines) in der **DATA FACTORY** Blade für die **LogProcessingFactory**, sollte die Pipelines, die Sie erstellt haben.

	![Alle Leitungen][image-data-factory-tutorial-pipelines-all]

12. In der **DATA FACTORY** Blade für die **LogProcessingFactory**, klicken Sie auf **Diagramm**.

	![Diagramm-Link][image-data-factory-tutorial-diagram-link]

13. Sie können das angezeigte Diagramm neu anordnen; hier sehen Sie ein neu angeordnetes Diagramm, in dem direkte Eingabe oben und Ausgaben unten angezeigt werden. Können Sie sehen, die Ausgabe von der **PartitionGameLogsPipeline** wird als Eingabe für die EnrichGameLogsPipeline und die Ausgabe der übergeben der **EnrichGameLogsPipeline** übergeben wird, um die **AnalyzeMarketingCampaignPipeline**. Doppelklicken Sie auf einen Titel, um Details über das Artefakt anzuzeigen, das das Fenster darstellt.

	![Diagrammansicht][image-data-factory-tutorial-diagram-view]

	**Glückwunsch!** Sie haben die Azure Data Factory, die verknüpften Dienste, Pipelines und Tabellen erfolgreich erstellt und den Workflow gestartet.


## <a name="MainStep6"></a> Schritt 6: Überwachen von Pipelines und Daten slices 

1.	Wenn das Fenster „DATA FACTORY“ für „LogProcessingFactory“ nicht geöffnet ist, können Sie einen der folgenden Schritte ausführen:
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
 
7. Fangen wir an der **PIPELINE** Blade für **PartiionGameLogsPipeline**, klicken Sie auf **produziert**.
8. Daraufhin wird die Liste der Datasets angezeigt, die diese Pipeline produziert: 
9. Klicken Sie auf **PartitionedGameEvents** -Tabelle in der **erstellten Datasets** Blade. 
10.	Überprüfen Sie, ob die **Status** alle Segmente minFreeThreads auf **bereit**. 
11.	Klicken Sie auf eines der Segmente, die **bereit** finden Sie unter der **DATENSLICE** Blade für dieses Segment.

	![RawGameEventsTable DATENSLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Wenn ein Fehler aufgetreten ist, sehen Sie eine **Fehler **Status ein. Darüber hinaus möglicherweise entweder beide Segmente mit dem Status **bereit**, oder beide mit dem Status **PendingValidation**, je nachdem wie schnell die Segmente verarbeitet werden.
 
	Finden Sie in der [Azure Data Factory Entwicklerreferenz][developer-reference] um einen Überblick über alle möglichen Slice Status abzurufen.

12.	In der **DATENSLICE** Blade, klicken Sie auf das Ausführen von der **Aktivität führt** Liste. Daraufhin sollte das Fenster „Aktivitätsausführung“ für diesen Slice angezeigt werden. Sollte eine die folgenden **AKTIVITÄTSDETAILS ausführen** Blade.

	![Aktivität Testlaufdetails blade][image-data-factory-monitoring-activity-run-details]

13.	Klicken Sie auf **herunterladen** zum Herunterladen der Dateien. Dieser Bildschirm ist besonders dann hilfreich, wenn Sie Fehler der HDInsight-Verarbeitung beheben.
	 
	
Wenn alle Pipeline Ausführung abgeschlossen haben, suchen Sie in der **MarketingCampaignEffectivenessTable** in den **MarketingCampaigns** Azure SQL-Datenbank, um die Ergebnisse anzuzeigen.

**Glückwunsch!** Sie können jetzt die Workflows überwachen und Fehler beheben. Sie haben gelernt, wie Azure Data Factory zum Verarbeiten von Daten und Erstellen von Analysen verwendet wird.

## Erweitern Sie das Lernprogramm zum Verwenden von lokalen Daten
Im letzten Schritt der Protokollverarbeitung Szenario aus der exemplarischen Vorgehensweise in diesem Artikel wurde die marketing Kampagne Effektivität Ausgabe in einer Azure SQL-Datenbank kopiert. Sie können diese Daten auch in einen lokalen SQL Server für Analysen innerhalb Ihrer Organisation verschieben.
 
Die Kampagne Marketingdaten der Effektivität von Azure-Blob, das Kopieren auf lokale SQL Server ist, müssen Sie zusätzliche lokale verknüpfte Dienst erstellen, Tabelle und der Pipeline, die in der exemplarischen Vorgehensweise in diesem Artikel eingeführt.

Praxis der [Exemplarische Vorgehensweise: verwenden lokale Datenquelle][tutorial-onpremises-using-powershell] Informationen zum Erstellen einer Pipeline um Marketingdaten Kampagne Effektivität in einer lokalen SQL Server-Datenbank zu kopieren.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

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

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->