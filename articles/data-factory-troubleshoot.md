<properties title="Troubleshoot Azure Data Factory issues" pageTitle="Problembehandlung bei Azure Data Factory" description="Erfahren Sie, wie Sie Probleme mithilfe von Azure Data Factory beheben." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Problembehandlung bei Data Factory
Sie können Azure Data Factory-Probleme mithilfe vom Azure-Portal (oder) Azure PowerShell-Cmdlets beheben. Dieses Thema enthält exemplarische Vorgehensweisen, die Ihnen zeigen, wie Sie das Azure-Portal verwenden, um schnell Fehler zu beheben, die bei Data Factory auftreten. 

## Themen in diesem Artikel

- [Exemplarische Vorgehensweise: Beheben eines Fehlers beim Kopieren von Daten](#copywalkthrough)
- [Exemplarische Vorgehensweise: Beheben eines Fehlers mit Hive/Pig-Verarbeitung](#pighivewalkthrough)

## <a name="copywalkthrough"></a>Exemplarische Vorgehensweise: Beheben eines Fehlers beim Kopieren von Daten
In dieser exemplarischen Vorgehensweise führen Sie einen Fehler in das Lernprogramm aus dem Artikel "Erste Schritte mit Data Factory" und erfahren, wie Sie das Azure-Portal verwenden können, um den Fehler zu beheben.

### Voraussetzungen
1. Schließen Sie das Lernprogramm im Artikel [Erste Schritte mit Data Factory][adfgetstarted] ab.
2. Vergewissern Sie sich, dass die **ADFTutorialDataFactory** Daten in der Tabelle **emp** in der Azure SQL-Datenbank erzeugt.  
3. Löschen Sie jetzt die Tabelle **emp** (**drop table emp**) aus der Azure SQL-Datenbank. Dadurch wird ein Fehler eingeführt.
4. Führen Sie den folgenden Befehl in **Azure PowerShell** aus, um die aktive Periode für die Pipeline zu aktualisieren, sodass sie versucht, Daten in die Tabelle **emp** zu schreiben, die nicht mehr existiert.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] Ersetzen Sie den <b>StartDateTime</b>-Wert durch den aktuellen Tag und <b>EndDateTime</b> durch den nächsten Tag. 


### Verwenden des Azure-Vorschauportals, um den Fehler zu beheben

1.	Melden Sie sich beim [Azure-Vorschauportal][azure-preview-portal] an. 
2.	Klicken Sie im Startboard auf **ADFTutorialDataFactory******. Wenn Sie den Data Factory-Link nicht auf dem **Startboard** sehen, klicken Sie auf **DURCHSUCHEN** und dann auf **Alles**. Klicken Sie im Bereich **Durchsuchen** auf **Data Factorys...** und dann auf **ADFTutorialDataFactory**.
3.	Sie werden feststellen, dass **Mit Fehlern** im Tile **Datasets** angezeigt wird. Klicken Sie auf **Mit Fehlern**. Der Bereich **Datasets mit Fehlern** sollte nun angezeigt werden.

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. Klicken Sie im Bereich **Datasets mit Fehlern** auf **EmpSQLTable**, um das Fenster **TABELLE** anzuzeigen.	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Im Fenster **TABELLE** sollten die Problemslices, also die Slices mit Fehlern, unten in der Liste **Problemslices** angezeigt werden. Sie können auch alle aktuellen Slices mit Fehlern in der Liste **Zuletzt verwendete Slices** sehen. Klicken Sie in der Liste **Problemslices** auf einen Slice. 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Wenn Sie auf **Problemslices** (nicht auf ein bestimmtes Problem) klicken, sehen Sie das Fenster **DATENSLICES**. Klicken Sie dann auf einen **bestimmtes Problemslice**, um das Fenster **DATENSLICE** für den ausgewählten Datenslice anzuzeigen.

6. Im Fenster **DATENSLICE** für **EmpSQLTable** sehen Sie alle **ausgeführten Aktivitäten** für den Slice unten in der Liste. Klicken Sie auf eine **Aktivitätsausführung** aus der Liste, die fehlgeschlagen ist.

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Im Fenster **Aktivitätsausführungsdetails** für die von Ihnen ausgewählte ausgeführte Aktivität sollten Fehlerdetails angezeigt werden. In diesem Szenario wird Folgendes angezeigt: **Invalid object name 'emp'**.

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

Um dieses Problem zu beheben, erstellen Sie die Tabelle **emp** mit dem SQL-Skript aus dem Artikel [Erste Schritte mit Data Factory][adfgetstarted].


### Verwenden von Azure PowerShell-Cmdlets, um den Fehler zu beheben
1.	Starten Sie **Azure PowerShell**. 
2.	Wechseln Sie zum **AzureResourceManager**-Modus, da Data Factory-Cmdlets nur in diesem Modus verfügbar sind.

         
		switch-azuremode AzureResourceManager

3. Führen Sie den Befehl Get-AzureDataFactorySlice aus, um die Slices und deren Status anzuzeigen. Ein Slice mit folgendem Status sollte angezeigt werden: Fehler.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] Ersetzen Sie **StartDateTime** durch den StartDateTime-Wert, den Sie für **Set-AzureDataFactoryPipelineActivePeriod** festgelegt haben. 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Beachten Sie die **Startzeit** für den Problemslice (der Slice mit dem **Status** **Fehler**) in der Ausgabe. 
4. Führen Sie nun das Cmdlet **Get-AzureDataFactoryRun** zum Abrufen von Details zur ausgeführten Aktivität für den Slice aus.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	Der Wert von **StartDateTime** ist die Startzeit für den Fehler-/Problemslice, den Sie im vorherigen Schritt notiert haben. Datum und Uhrzeit sollte in doppelte Anführungszeichen eingeschlossen werden.
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

 

## <a name="pighavewalkthrough"></a>Exemplarische Vorgehensweise: Beheben eines Fehlers mit Hive/Pig-Verarbeitung
Diese exemplarische Vorgehensweise enthält Schritte zum Beheben der Fehler mit Hive/Pig-Verarbeitung mithilfe des Azure-Vorschauportals und Azure PowerShell. 


### Exemplarische Vorgehensweise: Verwenden des Azure-Portals zur Problembehandlung mit Pig/Hive-Verarbeitung
In diesem Szenario ist Datensatz aufgrund eines Fehlers in der Hive-Verarbeitung auf einen HDInsight-Cluster in einem Fehlerzustand.

1. Klicken Sie auf **Mit Fehlern** im Tile **Datasets** auf der Startseite **DATA FACTORY**.

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Klicken Sie im Fenster **Datasets mit Fehlern** auf die **Tabelle**, an der Sie interessiert sind.

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Klicken Sie im Fenster **TABELLE** auf den **Problemsllice** mit dem **STATUS** **Fehler**.

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Klicken Sie im Fenster **DATA SLICE** auf die fehlgeschlagene **Aktivitätsausführung**.

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Im Fenster **AKTIVITÄTSAUSFÜHRUNGSDETAILS** können Sie die mit der Verarbeitung von HDInsight verknüpften Dateien herunterladen. Klicken Sie auf **Download** für **Status/stderr**, um die Fehlerprotokolldatei herunterzuladen, die Einzelheiten zu dem Fehler enthält.

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### Exemplarische Vorgehensweise: Verwenden von Azure PowerShell zur Problembehandlung mit Pig/Hive-Verarbeitung
1.	Starten Sie **Azure PowerShell**. 
2.	Wechseln Sie zum **AzureResourceManager**-Modus, da Data Factory-Cmdlets nur in diesem Modus verfügbar sind.

         
		switch-azuremode AzureResourceManager

3. Führen Sie den Befehl Get-AzureDataFactorySlice aus, um die Slices und deren Status anzuzeigen. Ein Slice mit folgendem Status sollte angezeigt werden: Fehler.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] Ersetzen Sie **StartDateTime** durch den StartDateTime-Wert, den Sie für **Set-AzureDataFactoryPipelineActivePeriod** festgelegt haben. 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Beachten Sie die **Startzeit** für den Problemslice (der Slice mit dem **Status** **Fehler**) in der Ausgabe. 
4. Führen Sie nun das Cmdlet **Get-AzureDataFactoryRun** zum Abrufen von Details zur ausgeführten Aktivität für den Slice aus.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Der Wert von **StartDateTime** ist die Startzeit für den Fehler-/Problemslice, den Sie im vorherigen Schritt notiert haben. Datum und Uhrzeit sollte in doppelte Anführungszeichen eingeschlossen werden.
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

6. Sie können das Cmdlet **Save-AzureDataFactoryLog** mit dem ID-Wert, den Sie in der oben genannten Ausgabe finden, ausführen und die Protokolldateien mithilfe der Option **-DownloadLogs** für das Cmdlet herunterladen.

## Tipps zur Problembehandlung

### Fehler beim Herstellen einer Verbindung zum lokalen SQL Server 
Stellen Sie sicher, dass SQL Server von dem Computer, auf dem das Gateway installiert ist, erreichbar ist.


1. Pingen Sie den Computer, auf dem SQL Server installiert ist
2. Versuchen Sie auf dem Computer, auf dem das Gateway installiert ist, unter Verwendung der Anmeldeinformationen, die Sie im Azure-Verwaltungsportal mithilfe von SQL Server Management Studio (SSMS) angegeben haben, eine Verbindung mit der SQL Server-Instanz herzustellen.

### Kopiervorgang schlägt fehl
1. Starten Sie den Data Management Gateway-Konfigurations-Manager auf dem Computer, auf dem das Gateway installiert wurde. Stellen Sie sicher, dass der **Gateway-Name** auf den logischen Gateway-Namen im **Azure-Portal** festgelegt ist, **Gateway Schlüsselstatus** ist **registriert** und **Dienststatus** ist **Gestartet**. 
2. Starten Sie die **Ereignisanzeige**. Erweitern Sie **Anwendungs- und Dienstprotokolle**, und klicken Sie auf **Data Management Gateway**. Überprüfen Sie, ob es Fehler im Zusammenhang mit Data Management Gateway gibt. 



## Weitere Informationen

Artikel | Beschreibung
------ | ---------------
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] |  Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-Blob.
[Verwenden von Pig und Hive mit Data Factory][use-pig-and-hive-with-data-factory] | Dieser Artikel hat eine exemplarische Vorgehensweise, die zeigt, wie Sie HDInsight-Aktivitäten zum Ausführen eines Hive/Pig-Skripts zur Verarbeitung der Eingabedaten in Ausgabedaten zu erzeugen. 
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen
[Verwenden von benutzerdefinierte Aktivitäten in Data Factory][use-custom-activities] | Dieser Artikel bietet eine exemplarische Vorgehensweise mit einer schrittweisen Anleitung zum Erstellen einer benutzerdefinierten Aktivität und ihrer Verwendung in einer Pipeline. 
[Überwachen und Verwalten von Azure Data Factory mit PowerShell][monitor-manage-using-powershell] | In diesem Artikel wird beschrieben, wie eine Azure Data Factory mithilfe von Azure PowerShell-Cmdlets überwacht werden kann. 
[Problembehandlung bei Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Sie Azure Data Factory-Probleme beheben. Sie können die exemplarische Vorgehensweise in diesem Artikel mit ADFTutorialDataFactory ausprobieren, indem Sie einen Fehler einbauen (die Tabelle in der Azure SQL-Datenbank löschen). 
[Azure Data Factory Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassendes Referenzmaterial für Cmdlets, JSON-Skripts, Funktionen usw... 
[Azure Data Factory Cmdlet-Referenz][cmdlet-reference] | Diese Referenz enthält die Details zu allen **Data Factory-Cmdlets**.

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
