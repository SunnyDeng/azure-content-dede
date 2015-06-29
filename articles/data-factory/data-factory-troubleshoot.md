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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Problembehandlung bei Data Factory
Sie können Azure Data Factory-Probleme mithilfe vom Azure-Portal (oder) Azure PowerShell-Cmdlets beheben. Dieses Thema enthält exemplarische Vorgehensweisen, die Ihnen zeigen, wie Sie das Azure-Portal verwenden, um schnell Fehler zu beheben, die bei Data Factory auftreten.

## Problem: Data Factory-Cmdlets können nicht ausgeführt werden.
Wechseln Sie zum Beheben des Problems in den Azure-Modus **AzureResourceManager**:

Starten Sie **Azure PowerShell**, und führen Sie den folgenden Befehl aus, um in den Modus **AzureResourceManager** zu wechseln. Die Azure Data Factory-Cmdlets sind im Modus **AzureResourceManager** verfügbar.

         switch-azuremode AzureResourceManager

## Problem: Autorisierungsfehler beim Ausführen eines Data Factory-Cmdlets
Sie verwenden wahrscheinlich nicht das richtige Azure-Konto oder -Abonnement für Azure-PowerShell. Wählen Sie mithilfe der folgenden Cmdlets das richtige Azure-Konto und -Abonnement für die Verwendung mit Azure-PowerShell.

1. Add-AzureAccount: Verwenden Sie die richtige Benutzer-ID und das richtige Kennwort.
2. Get-AzureSubscription: Zeigen Sie alle Abonnements für das Konto an. 
3. Select-AzureSubscription<subscription name>: Wählen Sie das richtige Abonnement. Verwenden Sie dasselbe Abonnement wie zum Erstellen einer Data Factory im Azure-Vorschauportal.

## Problem: Das Express-Setup für das Datengateway kann vom Azure-Portal aus nicht gestartet werden.
Für das Express-Setup des Datengateways ist Internet Explorer oder ein mit Microsoft ClickOnce kompatibler Webbrowser erforderlich. Wenn das Express-Setup nicht startet, haben Sie folgende Möglichkeiten:

1. Wechseln Sie zu Internet Explorer, wenn andere Browser nicht funktionieren. Oder
2. Verwenden Sie die auf dem gleichen Blatt im Portal angezeigten Links "Manuelles Setup", um die Installation durchzuführen. Kopieren Sie den auf dem Bildschirm angezeigten Schlüssel, und fügen Sie ihn ein, sobald die Konfiguration des Datenverwaltungsgateways bereit ist. Falls das Gateway nicht gestartet wird, überprüfen Sie das Startmenü auf "Microsoft-Datenverwaltungsgateway", und fügen Sie beim Start den Schlüssel ein. 


## Problem: Fehler beim Starten des Anmeldeinformations-Managers im Azure-Portal
Beim Einrichten oder Aktualisieren eines mit SQL Server verknüpften Diensts über das Azure-Portal wird der Anmeldeinformations-Manager gestartet, um die Zugriffssicherheit zu gewährleisten. Hierfür ist Internet Explorer oder ein mit Microsoft ClickOnce kompatibler Webbrowser erforderlich. Falls andere Browser nicht funktionieren, können Sie zu Internet Explorer wechseln.

## Problem: Fehler beim Herstellen einer Verbindung mit der lokalen SQL Server-Datenbank 
Stellen Sie sicher, dass SQL Server von dem Computer, auf dem das Gateway installiert ist, erreichbar ist. Sie haben auf dem Computer mit dem installierten Gateway folgende Möglichkeiten:

1. Pingen Sie den Computer, auf dem SQL Server installiert ist. Oder
2. Versuchen Sie unter Verwendung der Anmeldeinformationen, die Sie im Azure-Portal mithilfe von SQL Server Management Studio (SSMS) angegeben haben, eine Verbindung mit der SQL Server-Instanz herzustellen.


## Problem: Eingabeslices haben permanent den Status "PendingExecution" oder "PendingValidation".

Slices können den Status **PendingExecution** oder **PendingValidation** aus unterschiedlichen Gründen aufweisen. Zu den häufigsten Gründen zählt, dass die Eigenschaft **waitOnExternal** nicht im Abschnitt **availability** der ersten Tabelle bzw. des ersten Datasets in der Pipeline angegeben ist. Ein Dataset, das außerhalb des Gültigkeitsbereichs von Azure Data Factory erstellt wird, sollte im Abschnitt **availability** mit der Eigenschaft **waitOnExternal** gekennzeichnet sein. Dies bedeutet, dass es sich um externe Daten handelt, die nicht von Pipelines innerhalb der Data Factory unterstützt werden. Die Datenslices werden als **Ready** gekennzeichnet, sobald die Daten im entsprechenden Speicher verfügbar sind.

Das folgende Beispiel zeigt die Verwendung der Eigenschaft **waitOnExternal**. Sie können **waitOnExternal {}** angeben, ohne Werte für Eigenschaften im Abschnitt festzulegen, sodass die Standardwerte verwendet werden.

Im Thema "Tabellen" in der [Referenz zur JSON-Skripterstellung][json-scripting-reference] finden Sie weitere Informationen zu dieser Eigenschaft.
	
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

 Um den Fehler zu beheben, fügen Sie den Abschnitt **waitOnExternal** der JSON-Definition für die Eingabetabelle hinzu und erstellen die Tabelle erneut.

## Problem: Fehler beim Hybridkopiervorgang
So erhalten Sie weitere Details:

1. Starten Sie den Konfigurations-Manager für das Datenverwaltungsgateway auf dem Computer, auf dem das Gateway installiert wurde. Stellen Sie Folgendes sicher: Als **Gatewayname** ist der logische Gatewayname im **Azure-Portal** festgelegt, der **Schlüsselstatus des Gateways** ist **registriert** und der **Dienststatus** ist **gestartet**. 
2. Starten Sie die **Ereignisanzeige**. Erweitern Sie **Anwendungs- und Dienstprotokolle**, und klicken Sie auf **Datenverwaltungsgateway**. Überprüfen Sie, ob es Fehler im Zusammenhang mit Data Management Gateway gibt. 

## Problem: Fehler bei der bedarfsgesteuerten HDInsight-Bereitstellung

Wenn Sie einen verknüpften Dienst vom Typ "HDInsightOnDemandLinkedService" verwenden, sollten Sie einen "linkedServiceName" angeben, der auf den Azure-Blobspeicher verweist. Dieses Speicherkonto wird verwendet, um sämtliche Protokolle und unterstützenden Dateien für Ihren bedarfsgesteuerten HDInsight-Cluster zu kopieren. Mitunter kann bei der Aktivität, die für die bedarfsgesteuerte HDInsight-Bereitstellung sorgt, folgender Fehler auftreten:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Dieser Fehler gibt normalerweise an, dass der Speicherort des Speicherkontos, das in "linkedServiceName" angegeben ist, nicht dem Speicherort des Datencenters entspricht, in dem die HDInsight-Bereitstellung erfolgt. Beispiel: Wenn der Speicherort von Azure Data Factory "USA (Westen)" ist und die bedarfsgesteuerte HDInsight-Bereitstellung in "USA (Westen)" erfolgt, aber der Speicherort des Azure-Blobspeicherkontos auf "USA (Osten)" festgelegt ist, schlägt die bedarfsgesteuerte Bereitstellung fehl.

Darüber hinaus können in der weiteren JSON-Eigenschaft "additionalLinkedServiceNames" zusätzliche Speicherkonten in bedarfsgesteuertem HDInsight angegeben werden. Diese zusätzlichen verknüpften Speicherkonten müssen sich am gleichen Speicherort wie der HDInsight-Cluster befinden. Andernfalls tritt derselbe Fehler auf.



## Problem: Fehler bei benutzerdefinierter Aktivität
Bei Verwendung einer benutzerdefinierten Aktivität in Azure Data Factory (Pipeline-Aktivitätstyp "CustomActivity") wird die benutzerdefinierte Anwendung im festgelegten Dienst, der mit HDInsight verknüpft ist, als auf "Map" beschränkter MapReduce-Streamingauftrag ausgeführt.

Wenn die benutzerdefinierte Aktivität ausgeführt wird, kann Azure Data Factory diese Ausgabe aus dem HDInsight-Cluster erfassen und im Speichercontainer *adfjobs* in Ihrem Azure-Blobspeicherkonto speichern. Bei einem Fehler finden Sie Einzelheiten in der Ausgabetextdatei **stderr**. Die Dateien sind im Azure-Portal zugänglich und können von dort in einem Webbrowser gelesen werden. Zudem ist es möglich, die im Speichercontainer enthaltenen Dateien mithilfe von Speicher-Explorer-Tools direkt im Azure-Blobspeicher aufzurufen.

Zum Auflisten und Lesen der Protokolle einer bestimmten benutzerdefinierten Aktivität können Sie eine der anschaulichen exemplarischen Vorgehensweisen weiter unten auf dieser Seite befolgen. Zusammenfassung:

1.  Klicken Sie im Azure-Portal auf **Durchsuchen**, um zu Ihrer Data Factory zu navigieren.
2.  Klicken Sie auf die Schaltfläche **Diagramm**, um das Data Factory-Diagramm anzuzeigen. Klicken Sie dann auf die Tabelle **Dataset**, die der spezifischen **Pipeline** mit der benutzerdefinierten Aktivität folgt. 
3.  Klicken Sie auf dem Blatt **Tabelle** in **Problemslices** auf den gewünschten Slice für den zu untersuchenden Zeitraum.
4.  Das detaillierte Blatt **Datenslice** wird angezeigt. Es kann mehrere **Aktivitätsausführungen** für den Slice beinhalten. Klicken Sie in der Liste auf eine **Aktivität**. 
5.  Das Blatt **Aktivitätsausführung – Details** wird angezeigt. In der Mitte des Blatts sehen Sie die **Fehlermeldung** und darunter verschiedene mit dieser Aktivitätsausführung verbundene **Protokolldateien**.
	- Logs/system-0.log
	- Status
	- Status/exit
	- Status/stderr
	- Status/stdout

6. Klicken Sie auf die erste **Protokolldatei** in der Liste. Das Protokoll wird in einem neuen Blatt mit dem vollständigen Text angezeigt. Überprüfen Sie den Inhalt der einzelnen Protokolle, indem Sie darauf klicken. Das Blatt "Text-Viewer" wird geöffnet. Klicken Sie auf die Schaltfläche **Herunterladen**, um die Textdatei für die optionale Offlineanzeige herunterzuladen.

Ein **häufiger Fehler** bei benutzerdefinierten Aktivitäten erfolgt bei der Paketausführung mit Exitcode "1". Weitere Details finden Sie unter "wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr".

Um weitere Details zu dieser Art von Fehler anzuzeigen, öffnen Sie die Datei **stderr**. Ein häufiger Fehler ist hier eine Timeoutbedingung wie diese: INFO mapreduce.Job: Task Id : attempt_1424212573646_0168_m_000000_0, Status : FAILED AttemptID:attempt_1424212573646_0168_m_000000_0 Timed out after 600 secs

Derselbe Fehler wird möglicherweise mehrmals angezeigt, wenn der Auftrag z. B. über einen Zeitraum von mindestens 30 Minuten dreimal wiederholt wurde.

Dieser Timeoutfehler weist darauf hin, dass ein Timeout von 600 Sekunden (10 Minuten) aufgetreten ist. In der Regel bedeutet dies, dass die benutzerdefinierte .NET-Anwendung 10 Minuten lang keine Statusaktualisierung ausgelöst hat. Falls sich die Anwendung durch zu langes Warten aufhängt, ist das zehnminütige Timeout ein Sicherheitsmechanismus, der verhindert, dass Ihre Azure Data Factory-Pipeline aufgrund eines unbegrenzten Wartezustands der Anwendung verzögert wird.

Dieses Timeout stammt aus der Konfiguration des HDInsight-Clusters, der mit der benutzerdefinierten Aktivität verknüpft ist. Als Einstellung wird **mapred.task.timeout** verwendet. Der Standardwert beträgt 600000 Millisekunden, wie hier in den Standardeinstellungen für Apache dokumentiert ist: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Sie können diese Standardeinstellung überschreiben, indem Sie die Standardwerte zum Zeitpunkt der Bereitstellung Ihres HDInsight-Clusters ändern. Bei Verwendung von Azure Data Factory und des mit **bedarfsgesteuertem HDInsight** verknüpften Diensts kann die JSON-Eigenschaft den JSON-Eigenschaften für "HDInsightOnDemandLinkedService" hinzugefügt werden. Sie können beispielsweise den Wert mithilfe dieser JSON-Eigenschaft auf 20 Minuten erhöhen.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Mehr Kontext und ein vollständiges Beispiel des JSON-Codes zum Bearbeiten dieser MapReduce-Konfigurationseigenschaften finden Sie in Beispiel 3 in der MSDN-Dokumentation unter https://msdn.microsoft.com/library/azure/dn893526.aspx.

## Problem: PowerShell-Anforderung löst den Fehler "400/Ungültige Anforderung – Keine registrierten Ressourcenanbieter gefunden..." aus.

Seit dem 10. März 2015 werden die frühen privaten Vorschauversionen von Azure Data Factory PowerShell "2014-05-01-preview", "2014-07-01-preview" und "2014-08-01-preview" nicht mehr unterstützt. Es wird empfohlen, die neueste Version der ADF-Cmdlets zu verwenden, die jetzt Teil des Azure PowerShell-Downloads sind, der beispielsweise unter folgender URL abgerufen werden kann: http://go.microsoft.com/?linkid=9811175&clcid=0x409.

Wenn Sie die nicht mehr unterstützten Versionen von Azure PowerShell SDK verwenden, erhalten Sie ggf. folgende Fehler:

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
1. Schließen Sie das Lernprogramm im Artikel [Erste Schritte mit Azure Data Factory][adfgetstarted] ab.
2. Vergewissern Sie sich, dass **ADFTutorialDataFactory** Daten in der Tabelle **emp** in der Azure SQL-Datenbank generiert.  
3. Löschen Sie jetzt die Tabelle **emp** (**drop table emp**) aus der Azure SQL-Datenbank. Dadurch wird ein Fehler eingeführt.
4. Führen Sie den folgenden Befehl in **Azure PowerShell** aus, um den aktiven Zeitraum für die Pipeline zu aktualisieren, sodass sie versucht, Daten in die Tabelle **emp** zu schreiben, die nicht mehr existiert.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	Ersetzen Sie den Wert **StartDateTime** durch den aktuellen Tag und den Wert **EndDateTime** durch den nächsten Tag.


### Verwenden des Azure-Vorschauportals, um den Fehler zu beheben

1.	Melden Sie sich beim [Azure-Vorschauportal][azure-preview-portal] an. 
2.	Klicken Sie im **Startmenü** auf **ADFTutorialDataFactory**. Wenn Sie den Data Factory-Link nicht im **Startmenü** sehen, klicken Sie auf den Hub **DURCHSUCHEN** und dann auf **Alles**. Klicken Sie auf dem Blatt **Durchsuchen** auf **Data Factorys…** und dann auf **ADFTutorialDataFactory**.
3.	Wie Sie sehen, wird auf der Kachel **Datasets** die Option **Mit Fehlern** angezeigt. Klicken Sie auf **Mit Fehlern**. Das Blatt **Datasets mit Fehlern** sollte nun angezeigt werden.

	![Data Factory mit Link "Fehler"][image-data-factory-troubleshoot-with-error-link]

4. Klicken Sie auf dem Blatt **Datasets mit Fehlern** auf **EmpSQLTable**, um das Blatt **TABELLE** anzuzeigen.

	![Blatt "Datasets mit Fehlern"][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Auf dem Blatt **TABELLE** sollten unten in der Liste **Problemslices** Slices mit Fehlern angezeigt werden. In der Liste **Zuletzt verwendete Slices** sehen Sie auch alle aktuellen Slices mit Fehlern. Klicken Sie in der Liste **Problemslices** auf einen Slice.

	![Blatt "Tabelle" mit Problemslices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Wenn Sie auf **Problemslices** (nicht auf ein bestimmtes Problem) klicken, sehen Sie das Blatt **DATENSLICES**. Klicken Sie auf einen bestimmten **Problemslice**, um das Blatt **DATENSLICE** für den ausgewählten Datenslice anzuzeigen.

6. Auf dem Blatt **DATENSLICE** für **EmpSQLTable** sehen Sie alle **Aktivitätsausführungen** für den unten in der Liste enthaltenen Slice. Klicken Sie in der Liste auf eine **Aktivitätsausführung**, bei der ein Fehler aufgetreten ist.

	![Blatt "Datenslice" mit Aktivitätsausführungen][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Auf dem Blatt **Aktivitätsausführung – Details** für die von Ihnen ausgewählte Aktivitätsausführung sollten Fehlerdetails angezeigt werden. In diesem Szenario wird Folgendes angezeigt: **Ungültiger Objektname 'emp'**.

	![Details zur Aktivitätsausführung mit einem Fehler][image-data-factory-troubleshoot-activity-run-with-error]

Um dieses Problem zu beheben, erstellen Sie die Tabelle **emp** mit dem SQL-Skript aus dem Artikel [Erste Schritte mit Data Factory][adfgetstarted].


### Verwenden von Azure PowerShell-Cmdlets, um den Fehler zu beheben
1.	Starten Sie **Azure PowerShell**. 
2.	Wechseln Sie in den Modus **AzureResourceManager**, da Data Factory-Cmdlets nur in diesem Modus verfügbar sind.

         
		switch-azuremode AzureResourceManager

3. Führen Sie den Befehl Get-AzureDataFactorySlice aus, um die Slices und deren Status anzuzeigen. Ein Slice mit dem Status "Fehler" sollte angezeigt werden.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	Ersetzen Sie **StartDateTime** durch den für **Set-AzureDataFactoryPipelineActivePeriod** festgelegten "StartDateTime"-Wert.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Beachten Sie in der Ausgabe die **Startzeit** für den Problemslice (den Slice mit dem **Status**: **Fehler**). 
4. Führen Sie nun das Cmdlet **Get-AzureDataFactoryRun** zum Abrufen von Details zur Aktivitätsausführung für den Slice aus.
         
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

 

## <a name="pighivewalkthrough"></a> Exemplarische Vorgehensweise: Beheben eines Fehlers mit Hive/Pig-Verarbeitung
Diese exemplarische Vorgehensweise enthält Schritte zum Beheben der Fehler mit Hive/Pig-Verarbeitung mithilfe des Azure-Vorschauportals und Azure PowerShell.


### Exemplarische Vorgehensweise: Verwenden des Azure-Portals zur Problembehandlung bei der Pig/Hive-Verarbeitung
In diesem Szenario ist Datensatz aufgrund eines Fehlers in der Hive-Verarbeitung auf einen HDInsight-Cluster in einem Fehlerzustand.

1. Klicken Sie auf der Startseite von **DATA FACTORY** auf der Kachel **Datasets** auf **Mit Fehlern**.

	![Link "Mit Fehlern" auf der Kachel "Datasets"][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Klicken Sie auf dem Blatt **Datasets mit Fehlern** auf die gewünschte **Tabelle**.

	![Blatt "Datasets mit Fehlern"][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Klicken Sie auf dem Blatt **TABELLE** auf den **Problemslice** mit dem **STATUS**: **Fehler**.

	![Tabelle mit Problemslices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Klicken Sie auf dem Blatt **DATA SLICE** auf die fehlerhafte **Aktivitätsausführung**.

	![Datenslice mit fehlerhaften Ausführungen][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Auf dem Blatt **AKTIVITÄTSAUSFÜHRUNG – DETAILS** können Sie die mit der Verarbeitung von HDInsight verknüpften Dateien herunterladen. Klicken Sie für **Status/stderr** auf **Herunterladen**, um die Fehlerprotokolldatei mit Einzelheiten zum Fehler herunterzuladen.

	![Details zur Aktivitätsausführung mit Downloadlink][image-data-factory-troubleshoot-activity-run-details]

    
### Exemplarische Vorgehensweise: Verwenden von Azure PowerShell zur Problembehandlung bei der Pig/Hive-Verarbeitung
1.	Starten Sie **Azure PowerShell**. 
2.	Wechseln Sie in den Modus **AzureResourceManager**, da Data Factory-Cmdlets nur in diesem Modus verfügbar sind.

         
		switch-azuremode AzureResourceManager

3. Führen Sie den Befehl Get-AzureDataFactorySlice aus, um die Slices und deren Status anzuzeigen. Ein Slice mit dem Status "Fehler" sollte angezeigt werden.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Ersetzen Sie **StartDateTime** durch den für **Set-AzureDataFactoryPipelineActivePeriod** festgelegten "StartDateTime"-Wert.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Beachten Sie in der Ausgabe die **Startzeit** für den Problemslice (den Slice mit dem **Status**: **Fehler**). 
4. Führen Sie nun das Cmdlet **Get-AzureDataFactoryRun** zum Abrufen von Details zur Aktivitätsausführung für den Slice aus.
         
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

6. Sie können das Cmdlet **Save-AzureDataFactoryLog** mit dem ID-Wert ausführen, den Sie in der oben genannten Ausgabe finden, und die Protokolldateien mithilfe der Option **-DownloadLogs** für das Cmdlet herunterladen.



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
 

<!---HONumber=58_postMigration-->