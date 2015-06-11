<properties 
	pageTitle="Problembehandlung bei Azure Data Factory" 
	description="Erfahren Sie, wie Sie Probleme mithilfe von Azure Data Factory beheben." 
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

# Problembehandlung bei Data Factory
Sie können Azure Data Factory-Probleme mithilfe vom Azure-Portal (oder) Azure PowerShell-Cmdlets beheben. Dieses Thema enthält exemplarische Vorgehensweisen, die Ihnen zeigen, wie Sie das Azure-Portal verwenden, um schnell Fehler zu beheben, die bei Data Factory auftreten.

## Problem: Kann nicht ausgeführt "Data Factory"-Cmdlets
Wechseln Sie zum Beheben dieses Problems auf Azure-Modus **AzureResourceManager**:

Starten Sie **Azure PowerShell** und führen Sie den folgenden Befehl zum Wechseln der **AzureResourceManager** Modus. Die Azure Data Factory-Cmdlets stehen in der **AzureResourceManager** Modus.

         switch-azuremode AzureResourceManager

## Problem: Nicht autorisierter Fehler beim Ausführen eines Daten-Factory-Cmdlets
Sie verwenden wahrscheinlich nicht die Rechte Azure-Konto oder das Abonnement mit der Azure-PowerShell. Verwenden Sie die folgenden Cmdlets, um die Rechte Azure-Konto und das Abonnement mit der Azure-PowerShell auszuwählen.

1. Hinzufügen-AzureAccount - verwenden Sie die richtige Benutzer-ID und Kennwort
2. Get-AzureSubscription - alle Abonnements für das Konto anzeigen. 
3. SELECT-AzureSubscription <subscription name> -Wählen Sie das richtige Abonnement. Verwenden Sie den gleichen, die Sie verwenden, um eine Factory für die Daten auf das Azure Preview Portal erstellen.

## Problem: Nicht Daten Gateway Express-Installation von Azure-Portal gestartet
Die Express-Installation für das Gateway Daten erfordert Internet Explorer oder einem kompatiblen Webbrowser Microsoft ClickOnce. Wenn Sie die Express-Installation zu starten, können Sie

1. Wechseln Sie zu Internet Explorer, schlägt mit anderen Browsern. Oder
2. Verwenden Sie "Manuelle Installation" Links auf der gleichen Blade im Portal angezeigt, um die Installation durchzuführen, und kopieren Sie den Schlüssel, der auf dem Bildschirm bereitgestellt wird, und fügen Sie Sie bei die Konfiguration von Data Management Gateway bereit ist. Wenn es gestartet wird, überprüfen Sie im Startmenü 'Microsoft-Datenverwaltungsgateway', und fügen Sie in den Schlüssel, wenn er startet. 


## Problem: Nicht Anmeldeinformations-Manager von Azure-Portal gestartet
Beim Einrichten oder aktualisieren eine verknüpfte SQL Server-Dienst über den Azure-Portal den Anmeldeinformations-Manager, die Anwendung gestartet wird, um Sicherheit zu gewährleisten. Internet Explorer oder einem kompatiblen Webbrowser Microsoft ClickOnce benötigt. Sie können in Internet Explorer wechseln, schlägt mit anderen Browsern.

## Problem: Bei lokalen SQL Server-Verbindung herstellen 
Stellen Sie sicher, dass SQL Server von dem Computer, auf dem das Gateway installiert ist, erreichbar ist. Auf dem Computer, auf dem das Gateway installiert ist, können Sie

1. Pingen Sie den Computer, auf dem SQL Server installiert ist. Oder
2. Versuchen Sie die Verbindung mit den Anmeldeinformationen der Azure-Verwaltungsportal mithilfe von SQL Server Management Studio (SSMS) angegebene SQL Server-Instanz.


## Problem: Eingabe Slices sind PendingExecution oder PendingValidation zum jemals

Segmente werden konnte, **PendingExecution** oder **PendingValidation** aufgrund einer Reihe von Gründen und eine der häufigsten Gründe Zustand befindet, der **WaitOnExternal** Eigenschaft nicht angegeben ist die **Verfügbarkeit** Abschnitt der ersten Tabelle/Dataset in der Pipeline. Ein Dataset, das außerhalb des Bereichs der Azure Data Factory erzeugt wird gekennzeichnet werden soll, mit **WaitOnExternal** -Eigenschaft unter **Verfügbarkeit** Abschnitt. Dies bedeutet, dass die Daten nicht gesicherten von Pipelines innerhalb der Factory Daten und externen. Die Datensegmente werden als **bereit** sobald die Daten im entsprechenden Speicher verfügbar ist.

Im folgenden Beispiel für die Verwendung der **WaitOnExternal** Eigenschaft. Sie können angeben, **WaitOnExternal {}** ohne Werte für Eigenschaften im Abschnitt festlegen, sodass die Standardwerte verwendet werden.

Tabellen finden Sie in [JSON Scripting Reference][json-scripting-reference] für Weitere Informationen zu dieser Eigenschaft.
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 Um den Fehler zu beheben, fügen die **WaitOnExternal** im Abschnitt zur Definition der Eingabetabelle JSON und die Tabelle erneut erstellen.

## Problem: Hybrid-Kopiervorgang schlägt fehl
Um weitere Details zu erfahren:

1. Starten Sie Data Management Gateway-Konfigurations-Manager auf dem Computer, auf dem Gateway installiert wurde. Überprüfen Sie, ob die **gatewayname** auf den Namen des logischen Gateway festgelegt ist, auf die **Azure Portal**, **Schlüssel des Gateways** ist **registriert** und **Service Status** ist **gestartet**. 
2. Starten Sie **Ereignisanzeige**. Erweitern Sie **Anwendungs- und Dienstprotokolle** und klicken Sie auf **Datenverwaltungsgateway**. Überprüfen Sie, ob es Fehler im Zusammenhang mit Data Management Gateway gibt. 

## Problem: Auf Anforderung HDInsight Bereitstellung schlägt fehl mit Fehler

Wenn Sie einen verknüpften Dienst vom Typ HDInsightOnDemandLinkedService zu verwenden, sollten Sie eine LinkedServiceName angeben, Azure Blob-Speicher verweist. Dieses Speicherkonto wird verwendet, um die Protokolle und die unterstützenden Dateien für Ihr HDInsight-Cluster bei Bedarf kopieren. Manchmal kann die Aktivität, die die Bereitstellung nach Bedarf auf HDInsight mit folgendem Fehler fehlschlagen:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Dieser Fehler gibt normalerweise an, dass der Speicherort des Speicherkontos angegeben, in der LinkedServiceName nicht in demselben Standort des Rechenzentrums als, in dem die Bereitstellung des HDInsight geschieht. Z. B. Wenn Ihre Azure-Daten-Factory Location (Westen) und erfolgt die Bereitstellung nach Bedarf HDInsight, in die auf Osten der USA (Westen), aber der Azure-Blob-kontospeicherort festgelegt, die die bedarfsgesteuerte Bereitstellung fehl.

Darüber hinaus steht eine zweite JSON-Eigenschaft AdditionalLinkedServiceNames wobei zusätzliche Speicherkonten in einer bedarfsgesteuerten HDInsight angegeben werden kann. Diese zusätzlichen verknüpften Speicherkonten sollten am gleichen Speicherort wie der HDInsight-Cluster, oder sie schlägt mit dem gleichen Fehler.



## Problem: Benutzerdefinierte Aktivität ein Fehler auftritt
Bei Verwendung einer benutzerdefinierten Aktivität in Azure Data Factory (Pipeline Aktivitätstyp CustomActivity) führt die benutzerdefinierte Anwendung in den angegebenen verknüpften Dienst zu HDInsight als Zuordnung nur streaming-MapReduce-Job.

Wenn die benutzerdefinierte Aktivität ausgeführt wird, Azure Data Factory werden diese Ausgabe aus dem HDInsight-Cluster zu erfassen und speichern Sie es in der *Adfjobs* Speichercontainer in Ihrem Konto Azure Blob-Speicher. Im Falle eines Fehlers können Sie den Text aus lesen **"stderr"** Output-Textdatei, nachdem ein Fehler aufgetreten ist. Die Dateien sind verfügbar und lesbar aus dem Azure-Portal selbst in einem Webbrowser oder mit Speicher-Explorer-Tools Zugriff auf die Dateien direkt in den Speichercontainer in Azure Blob-Speicher aufbewahrt.

Zum Auflisten und lesen Sie die Protokolle für eine benutzerdefinierte Aktivität, können Sie eine illustrierte exemplarischen Vorgehensweisen weiter unten auf dieser Seite folgen. Zusammenfassung:

1.  In der Azure-Portal **Durchsuchen** Ihrer Daten Factory zu lokalisieren.
2.  Verwenden der **Diagramm** um die Factory-Diagramm anzuzeigen, und klicken Sie auf die **Dataset** -Tabelle die spezifische **Pipeline** hat die benutzerdefinierte Aktivität. 
3.  In der **Tabelle** Blade, klicken Sie auf das Segment Interesse an der **Problem Segmente** für den Zeitrahmen untersucht werden.
4.  Die ausführliche **Datenslice** Blade wird angezeigt, und es kann mehrere Listen **Aktivität ausgeführt wird** für das Segment. Klicken Sie auf ein **Aktivität** aus der Liste. 
5.  Die **Aktivitätsdetails ausführen** Blade wird angezeigt. Listet die **Fehlermeldung** in der Mitte der Blade und mehrere **Protokolldateien** unten im Fenster zugeordnet, mit die Aktivität ausgeführt.
	- Protokolle, System-0.log
	- Status
	- Status/beenden
	- Status / "stderr"
	- Status/stdout

6. Klicken Sie auf der ersten **Protokolldatei** Element in der Liste aus, und das Protokoll wird in einem neuen Fenster geöffnet, mit dem vollständigen Text angezeigt wird, zu lesen. Überprüfen Sie den Text des jeweiligen Protokolls jeweils auf. Das Text-Viewer-Fenster wird geöffnet. Klicken Sie auf die **herunterladen** Schaltfläche zum Herunterladen der Textdatei für die Offlineanzeige optional.

Ein **häufiger Fehler** aus einer benutzerdefinierten Aktivität wird Fehler bei der Paketausführung mit Exitcode "1". Finden Sie unter "wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status /" stderr "" Weitere Informationen.

Um weitere Details für diese Art von Fehler anzuzeigen, öffnen Sie die **"stderr"** Datei. Ein häufiger Fehler angezeigt, es wird eine Timeoutbedingung wie dieser: INFO Mapreduce. Auftrag: Task-Id: attempt_1424212573646_0168_m_000000_0, Status: Fehler AttemptID:attempt_1424212573646_0168_m_000000_0 Zeitlimit von 600 Sekunden

Diese denselben Fehler möglicherweise mehrmals angezeigt, wenn der Auftrag dreimal z. B. über den Zeitraum von 30 oder mehr Minuten wiederholt wurde.

Dieses Timeout-Fehler weist darauf hin, eine 600 Sekunden (10 Minuten) Zeitüberschreitung aufgetreten. In der Regel bedeutet dies, dass die benutzerdefinierte Anwendung .net eine Statusaktualisierung für 10 Minuten nicht ausgestellt hat. Wenn die Anwendung hängen bleibt oder angehalten, warten auf etwas zu lang, Timeout einen Sicherheitsmechanismus, ohne dass diese warten und verzögern der Pipeline Azure Data Factory ist 10 Minuten.

Dieses Timeout stammt in der Konfiguration des HDInsight-Cluster, die in der benutzerdefinierten Aktivität verknüpft ist. Die Einstellung ist **mapred.task.timeout**, die den Standardwert 600000 Millisekunden an, wie in den Apache-Standardeinstellungen dokumentiert: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Sie können diese Standardeinstellung override aufgenommen, durch Ändern der Standardwerte zum Zeitpunkt der Bereitstellung Ihrer Bereitstellung HDInsight-Clusters. Bei Verwendung von Azure Data Factory und **HDInsight On-Demand** Service, verknüpft die JSON-Eigenschaft in der Nähe Ihrer HDInsightOnDemandLinkedService JSON-Eigenschaften hinzugefügt werden kann. Beispielsweise können Sie den Wert auf 20 Minuten, die Verwendung dieser JSON-Eigenschaft erhöhen.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Reduzieren Sie für weitere Kontext und ein vollständiges Beispiel für JSON Zuordnung bearbeiten Konfiguration Eigenschaften Siehe Beispiel 3 in der MSDN-Dokumentation https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problem: PowerShell-Anforderung schlägt fehl mit Fehler 400 Bad Request "Keine registrierten Ressourcenanbieter gefunden..."

Zum Zeitpunkt der Erstellung 10 März 2015 werden die Azure Data Factory PowerShell frühen private Vorschauversionen 2014-05-01-Vorschau 2014-07-01-Vorschau und 2014-08-01-Vorschau eingestellt. Es wird empfohlen, dass Sie die neueste Version der ADF-Cmdlets verwenden, die jetzt Teil der Azure PowerShell herunterladen, z. B. das Herunterladen von folgender URL http://go.microsoft.com/?linkid=9811175&clcid=0x409

Wenn Sie die nicht mehr unterstützte Versionen von Azure PowerShell SDK verwenden, erhalten Sie folgende Fehler:

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> Exemplarische Vorgehensweise: Beheben eines Fehlers beim Kopieren von Daten
In dieser exemplarischen Vorgehensweise führen Sie einen Fehler in das Lernprogramm aus dem Artikel „Erste Schritte mit Data Factory“ und erfahren, wie Sie das Azure-Portal verwenden können, um den Fehler zu beheben.

### Voraussetzungen
1. Arbeiten Sie das Lernprogramm, in dem [Erste Schritte mit Azure Data Factory][adfgetstarted] Artikel.
2. Überprüfen Sie, ob die **ADFTutorialDataFactory** erzeugt der Daten in der **emp** Tabelle in Azure SQL-Datenbank.  
3. Löschen Sie nun die **emp** Tabelle (** Drop table emp **) von Azure SQL-Datenbank. Dadurch wird ein Fehler eingeführt.
4. Führen den folgenden Befehl die **Azure PowerShell** zum aktive Periode für die Pipeline zu aktualisieren, sodass er versucht, Daten zu schreiben, die **emp** Tabelle, die nicht mehr vorhanden ist.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]Ersetzen Sie <b>StartDateTime</b> -Wert mit dem aktuellen Tag und <b>EndDateTime</b> Wert mit am nächsten Tag.


### Verwenden des Azure-Vorschauportals, um den Fehler zu beheben

1.	Melden Sie sich [Azure Preview Portal][azure-preview-portal]. 
2.	Klicken Sie auf **ADFTutorialDataFactory** aus der **Startboard**. Wenn die Daten Factory verknüpfen, nicht angezeigt wird die **Startmenü**, klicken Sie auf **Durchsuchen** Hub, und klicken Sie auf **Alles**. Klicken Sie auf **Daten Factorys...** in den **Durchsuchen** Blade, und klicken Sie auf **ADFTutorialDataFactory**.
3.	Beachten Sie, dass die angezeigten **mit Fehlern** auf der **Datasets** Kachel. Klicken Sie auf **mit Fehlern**. Sollte eine **Datasets mit Fehlern** Blade.

	![Daten-Factory mit Fehlern link][image-data-factory-troubleshoot-with-error-link]

4. In der **Datasets** Fehler Blade, und klicken Sie auf **EmpSQLTable** finden Sie unter der **Tabelle** Blade.

	![Datasets mit Fehlern blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. In der **Tabelle** Blade, sollte das Problem Slices verwendet, d. h. Segmente mit einem Fehler in der **Problem Segmente** Liste am unteren Rand. Sie sehen außerdem alle aktuellen Slices mit Fehlern in den **aktuelle Slices** Liste. Klicken Sie auf ein Segment in der **Problem Segmente** Liste.

	![Tabelle Blade mit Problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Wenn Sie auf **Problem Segmente** (nicht auf ein bestimmtes Problem), sehen Sie die **DATENSLICES** Blade und klicken Sie dann auf eine **Problems Slice** finden Sie unter der **DATENSLICE** Folie für den ausgewählten Datenslice.

6. In der **DATENSLICE** Blade für **EmpSQLTable**, sehen Sie all **Aktivität ausgeführt wird** für den Slice in der Liste unten. Klicken Sie auf ein **Aktivität ausführen** aus der Liste, die fehlgeschlagen.

	![Daten Slice-Blade mit Aktive Testläufe][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. In der **Aktivitätsdetails ausführen** Blade für die Aktivität führen Sie ausgewählt haben, sollte die Einzelheiten zu dem Fehler. In diesem Szenario finden Sie: **Ungültiger Objektname 'emp'**.

	![Aktivität, die Details zu einem Fehler führen][image-data-factory-troubleshoot-activity-run-with-error]

Um dieses Problem zu beheben, erstellen Sie die **emp** Tabelle mithilfe des SQL-Skript von [Erste Schritte mit Daten Factory][adfgetstarted] Artikel.


### Verwenden von Azure PowerShell-Cmdlets, um den Fehler zu beheben
1.	Starten Sie **Azure PowerShell**. 
2.	Wechseln Sie zu **AzureResourceManager** Modus als "Data Factory"-Cmdlets sind nur in diesem Modus verfügbar.

         
		switch-azuremode AzureResourceManager

3. Führen Sie den Befehl Get-AzureDataFactorySlice aus, um die Slices und deren Status anzuzeigen. Daraufhin sollte einen Slice mit dem Status: Fehler.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]Ersetzen Sie **StartDateTime** mit der StartDateTime-Wert für die **Set AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Beachten Sie die **Starten** für das Problem-Segment (mit **Status** festgelegt **fehlgeschlagen**) in der Ausgabe. 
4. Führen Sie nun die **Get-AzureDataFactoryRun** -Cmdlet zum Abrufen von Details zur Aktivität für das Segment ausführen.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	Der Wert der **StartDateTime** ist die Startzeit für die Fehler oder Probleme-Segment, das Sie im vorherigen Schritt notiert haben. Datum und Uhrzeit sollte in doppelte Anführungszeichen eingeschlossen werden.
5. Die Ausgabe mit Einzelheiten zu dem Fehler (ähnlich der folgenden) sollte angezeigt werden:

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> Exemplarische Vorgehensweise: Beheben eines Fehlers mit Hive/Pig-Verarbeitung
Diese exemplarische Vorgehensweise enthält Schritte zum Beheben der Fehler mit Hive/Pig-Verarbeitung mithilfe des Azure-Vorschauportals und Azure PowerShell.


### Exemplarische Vorgehensweise: Verwenden Azure-Portal zur Problembehandlung mit Pig-Hive-Verarbeitung
In diesem Szenario ist Datensatz aufgrund eines Fehlers in der Hive-Verarbeitung auf einen HDInsight-Cluster in einem Fehlerzustand.

1. Klicken Sie auf **mit Fehlern** auf **Datasets** Kachel auf die **DATA FACTORY** auf der Startseite.

	![Mit Fehlern Link auf Datasets-Kachel][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. In der **Datasets mit Fehlern** Blade, klicken Sie auf die **Tabelle** dass Sie interessiert sind.

	![Datasets mit Fehlern blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. In der **Tabelle** Blade, klicken Sie auf die **Problem Slice** mit **STATUS** festgelegt **Fehler**.

	![Tabelle mit Problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. In der **DATENSLICE** Blade, klicken Sie auf die **Aktivität ausführen** fehlgeschlagen.

	![Datenslice mit fehlgeschlagenen Ausführungen][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. In der **AKTIVITÄTSDETAILS ausführen** Blade, Sie können die Verarbeitung von HDInsight zugeordneten Dateien herunterladen. Klicken Sie auf **herunterladen** für **Status/Stderr** Fehlerprotokolldatei herunterladen, die Details über den Fehler enthält.

	![Aktivität ausführen Details mit Link zum Herunterladen][image-data-factory-troubleshoot-activity-run-details]

    
### Exemplarische Vorgehensweise: Verwenden von Azure PowerShell zur Problembehandlung mit Pig-Hive-Verarbeitung
1.	Starten Sie **Azure PowerShell**. 
2.	Wechseln Sie zu **AzureResourceManager** Modus als "Data Factory"-Cmdlets sind nur in diesem Modus verfügbar.

         
		switch-azuremode AzureResourceManager

3. Führen Sie den Befehl Get-AzureDataFactorySlice aus, um die Slices und deren Status anzuzeigen. Daraufhin sollte einen Slice mit dem Status: Fehler.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]Ersetzen Sie **StartDateTime** mit der StartDateTime-Wert für die **Set AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Beachten Sie die **Starten** für das Problem-Segment (mit **Status** festgelegt **fehlgeschlagen**) in der Ausgabe. 
4. Führen Sie nun die **Get-AzureDataFactoryRun** -Cmdlet zum Abrufen von Details zur Aktivität für das Segment ausführen.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Der Wert der **StartDateTime** ist die Startzeit für die Fehler oder Probleme-Segment, das Sie im vorherigen Schritt notiert haben. Datum und Uhrzeit sollte in doppelte Anführungszeichen eingeschlossen werden.
5. Die Ausgabe mit Einzelheiten zu dem Fehler (ähnlich der folgenden) sollte angezeigt werden:

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. Können Sie ausführen, **Speichern AzureDataFactoryLog** Cmdlet mit dem Id-Wert aus der obigen Ausgabe sehen und Protokoll herunterladen Dateien mit der **- DownloadLogs** -Option für das Cmdlet.



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!---HONumber=GIT-SubDir--> 