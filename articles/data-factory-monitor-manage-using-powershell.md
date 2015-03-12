<properties 
	pageTitle="Überwachen und Verwalten von Azure Data Factory mit Azure PowerShell" 
	description="Erfahren Sie, wie Sie von Ihnen erstellte Azure Data Factories mithilfe von Azure PowerShell überwachen und verwalten." 
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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Überwachen und Verwalten von Azure Data Factory mit Azure PowerShell
Die folgende Tabelle enthält die Cmdlets, die Sie zum Überwachen und Verwalten von Azure Data Factory mithilfe von Azure PowerShell verwenden können. 

> [WACOM.NOTE] In der [Data Factory-Cmdlet-Referenz][cmdlet-reference] finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets. 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
Ruft die Informationen zu einer bestimmten Data Factory oder alle Data Factorys in einem Azure-Abonnement innerhalb der angegebenen Ressourcengruppe ab.
 
###Beispiel 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

Dieser Befehl gibt alle Data Factorys in der Ressourcengruppe "ADFTutorialResourceGroup" zurück.
 
###Beispiel 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

Dieser Befehl gibt Details zur Data Factory "ADFTutorialDataFactory" in der Ressourcengruppe "ADFTutorialResourceGroup" zurück. 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService
Das Get-AzureDataFactoryLinkedService-Cmdlet ruft Informationen zu einem bestimmten verknüpften Dienst oder allen verknüpften Dienste in einer Azure Data Factory ab.

### Beispiel 1

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
Dieser Befehl gibt Informationen über alle verknüpften Dienste in der Azure Data Factory "ADFTutorialDataFactory" zurück.


Sie können den Parameter "-DataFactory" anstelle der Parameter "DataFactoryName" und "ResourceGroupName" verwenden. Auf diese Weise müssen Sie den Namen der Ressourcengruppe und der Factory nur einmal eingeben und können das Data Factory-Objekt als Parameter für alle Cmdlets verwenden, die sowohl "ResourceGroupName" als auch "DataFactoryName" als Parameter akzeptieren.

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### Beispiel 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

Mit diesem Befehl werden Informationen zu dem verknüpften Dienst "MyBlobStore" in der Azure Data Factory "ADFTutorialDataFactory" zurückgegeben. 

Sie können den Parameter "-DataFactory" anstelle der Parameter "-ResourceGroup" und "-DataFactoryName" verwenden, wie nachfolgend dargestellt: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
Das Get-AzureDataFactoryTable-Cmdlet ruft Informationen zu einer bestimmten Tabelle oder allen Tabellen in einer Azure Data Factory ab. 

### Beispiel 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Dieser Befehl gibt Informationen über alle Tabellen in der Azure Data Factory "ADFTutorialDataFactory" zurück.

Sie können den Parameter "-DataFactory" anstelle der Parameter "-ResourceGroup" und "-DataFactoryName" verwenden, wie nachfolgend dargestellt: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### Beispiel 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

Dieser Befehl gibt Informationen über die Tabelle "EmpTableFromBlob" in der Azure Data Factory "ADFTutorialDataFactory" zurück.



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
Das Get-AzureDataFactoryPipeline-Cmdlet ruft Informationen zu einer bestimmten Pipeline oder allen Pipelines in einer Azure Data Factory ab.

### Beispiel 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Dieser Befehl gibt Informationen über alle Pipelines in der Azure Data Factory "ADFTutorialDataFactory" zurück.

### Beispiel 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

Ruft Informationen über die Pipeline "ADFTutorialPipeline" in der Azure Data Factory "ADFTutorialDataFactory" ab.

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
Das Get-AzureDataFactorySlice-Cmdlet ruft alle Slices für eine Tabelle in einer Azure Data Factory ab, die nach dem StartDateTime und vor der EndDateTime produziert werden. Der Datenslice mit dem Status "Bereit" ist für eine Nutzung durch abhängige Slices bereit.

In der folgenden Tabelle sind alle Status eines Slices und ihre Beschreibungen aufgeführt.

<table border="1">	
	<tr>
		<th align="left">Status</th>
		<th align="left">Beschreibung</th>
	</tr>	

	<tr>
		<td>Ausstehende Ausführung</td>
		<td>Die Datenverarbeitung wurde noch nicht gestartet.</td>
	</tr>	

	<tr>
		<td>In Bearbeitung</td>
		<td>Die Datenverarbeitung wird ausgeführt.</td>
	</tr>

	<tr>
		<td>Bereit</td>
		<td>Die Datenverarbeitung ist abgeschlossen, und der Datenslice ist bereit.</td>
	</tr>

	<tr>
		<td>Fehler</td>
		<td>Fehler bei der Ausführung, die den Slice erstellt.</td>
	</tr>

	<tr>
		<td>Überspringen</td>
		<td>Die Verarbeitung des Slices wird übersprungen.</td>
	</tr>

	<tr>
		<td>Wiederholen</td>
		<td>Erneuter Versuch der Ausführung, die den Slice erstellt.</td>
	</tr>

	<tr>
		<td>Zeitüberschreitung</td>
		<td>Zeitüberschreitung bei der Datenverarbeitung des Slices.</td>
	</tr>

	<tr>
		<td>Ausstehende Überprüfung</td>
		<td>Der Datenslice wartet auf Überprüfung im Hinblick auf Überprüfungsrichtlinien, bevor es verarbeitet wird.</td>
	</tr>

	<tr>
		<td>Überprüfung wiederholen</td>
		<td>Die Überprüfung des Slices wird wiederholt.</td>
	</tr>

	<tr>
		<td>Fehler bei der Überprüfung</td>
		<td>Fehler beim Überprüfen des Slices.</td>
	</tr>

	<tr>
		<td>Lange Wiederholung</td>
		<td>Ein Slice weist diesen Status auf, wenn "Lange Wiederholung" in der JSON-Tabelle angegeben wird und reguläre Wiederholungen für den Slice fehlgeschlagen sind.</td>
	</tr>

	<tr>
		<td>Überprüfung wird ausgeführt</td>
		<td>Überprüfung des Slices (basierend auf den in der JSON-Tabelle definierten Richtlinien) wird ausgeführt.</td>
	</tr>

</table>

Sie können für die einzelnen Slices einen Drilldown ausführen und weitere Informationen zu der Ausführung anzeigen, die den Slice erstellt, indem Sie die Cmdlets "Get-AzureDataFactoryRun" und "Save-AzureDataFactoryLog" verwenden.

### Beispiel

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

Dieser Befehl ruft alle Slices, für die Tabelle "EmpSQLTable" in der Azure Data Factory "ADFTutorialDataFactory" ab, die nach 2014-05-20T10:00:00 (GMT) erstellt wurde. Ersetzen Sie Datum und Uhrzeit mit dem Startdatum und der Uhrzeit, das bzw. die Sie bei der Ausführung von "Set-AzureDataFactoryPipelineActivePeriod" angegeben haben.

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

Das "Get-AzureDataFactoryRun"-Cmdlet ruft alle Ausführungen für einen Datenslice einer Tabelle in einer Azure Data Factory ab.  Eine Tabelle in einer Azure Data Factory besteht aus Slices auf der Zeitachse. Die Breite eines Slices wird durch den Zeitplan (stündlich/täglich) bestimmt. Die Ausführung ist eine Verarbeitungseinheit für einen Slice. Im Falle von Wiederholungen, oder wenn Sie Ihren Slice im Falle von Fehlern erneut ausführen, kann es eine oder mehrere Ausführungen für einen Slice geben. Ein Slice wird durch seine Startzeit identifiziert. Aus diesem Grund müssen Sie für das "Get-AzureDataFactoryRun"-Cmdlet die Startzeit des Slices aus den Ergebnissen des "Get-AzureDataFactorySlice"-Cmdlets übergeben.

Um beispielsweise eine Ausführung für den folgenden Slice abzurufen, verwenden Sie 2015-04-02T20:00:00. 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### Beispiel

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

Dieser Befehl ruft alle Ausführungen für Slices der Tabelle "EmpSQLTable" in der Azure Data Factory "ADFTutorialDataFactory" beginnend mit 16 Uhr Uhr GMT am 05/21/2014 ab.

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
Das Save-AzureDataFactoryLog-Cmdlet lädt Protokolldateien im Zusammenhang mit der Azure HDInsight-Verarbeitung von Pig- oder Hive-Projekten oder für benutzerdefinierte Aktivitäten auf Ihre lokale Festplatte herunter. Sie führen zunächst das "Get-AzureDataFactoryRun"-Cmdlet aus, um eine ID für eine Aktivitätsausführung für einen Datenslice zu erhalten, und verwenden dann diese ID, um Protokolldateien aus dem BLOB-Speicher (Binary Large Object) abzurufen, der mit dem HDInsight-Cluster verbunden ist. 

Wenn Sie den Parameter **-DownloadLogs** nicht angeben, gibt das Cmdlet nur den Speicherort der Protokolldateien zurück. 

Wenn Sie den Parameter **-DownloadLogs** ohne Angabe eines Ausgabeverzeichnisses angeben (**-Output**-Parameter), werden die Protokolldateien in den standardmäßigen Ordner **Dokumente** heruntergeladen. 

Wenn Sie den Parameter **-DownloadLogs** zusammen mit einem Ausgabeordner angeben (**-Output**), werden die Protokolldateien in den angegebenen Ordner heruntergeladen. 


### Beispiel 1
Dieser Befehl speichert die Protokolldateien für die Aktivitätsausführung mit der ID "841b77c9-d56c-48d1-99a3-8c16c3e77d39", wobei die Aktivität zu einer Pipeline in der Data Factory mit dem Namen "LogProcessingFactory" in der Ressourcengruppe mit dem Namen "ADF" gehört. Die Protokolldateien werden im Ordner "C:\Test" gespeichert. 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### Beispiel 2
Dieser Befehl speichert die Protokolldateien im Ordner "Dokumente" (Standard).


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### Beispiel 3
Dieser Befehl gibt den Speicherort der Protokolldateien zurück. Beachten Sie, dass der Parameter "-DownloadLogs" nicht angegeben wird. 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
Das Get-AzureDataFactoryGateway-Cmdlet ruft Informationen über ein bestimmtes Gateway oder alle Gateways in einer Azure Data Factory ab. Sie müssen ein Gateway auf dem lokalen Computer installieren, damit Sie einen lokalen SQL Server als verknüpften Dienst zu einer Data Factory hinzufügen können.

### Beispiel 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
Dieser Befehl gibt Informationen über alle Gateways in der Azure Data Factory "ADFTutorialDataFactory" zurück.

### Beispiel 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

Dieser Befehl gibt Informationen über das Gateway "ADFTutorialGateway" in der Azure Data Factory "ADFTutorialDataFactory" zurück.

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
Dieses Cmdlet legt den aktiven Zeitraum für die Datenslices fest, die von der Pipeline verarbeitet werden. Bei Verwendung von "Set-AzureDataFactorySliceStatus" müssen Sie sicherstellen, dass das Startdatum und das Enddatum des Slices im aktiven Zeitraum der Pipeline liegen.

Sobald die Pipelines erstellt wurden, können Sie die Dauer angeben, in der die Datenverarbeitung von Daten erfolgt. Durch Angeben des aktiven Zeitraums für eine Pipeline definieren Sie die Zeitdauer, in der die Datenslices basierend auf den Verfügbarkeitseigenschaften, die für jede ADF-Tabelle definiert wurden, verarbeitet werden.

### Beispiel

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

Mit diesem Befehl wird der aktive Zeitraum für die Datenslices, die von der Pipeline "ADFTutoiralPipeline" verarbeitet werden, von 5/21/2014 4 PM GMT bis 5/22/2014 4 PM GMT festgelegt.

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
Legt den Status eines Slices für eine Tabelle fest. Das Startdatum und das Enddatum des Slices muss in dem aktiven Zeitraum der Pipeline liegen.

### Unterstützte Werte für den Status
Jeder Datenslice für eine Tabelle durchläuft unterschiedliche Phasen. Diese Phasen unterscheiden sich geringfügig dahingehend, ob die Überprüfungsrichtlinien angegeben wurden.


- Wenn keine Überprüfungsrichtlinien angegeben wurden: Ausstehende Ausführung -> In Bearbeitung -> Bereit
- Wenn Überprüfungsrichtlinien angegeben wurden: Ausstehende Ausführung -> Ausstehende Überprüfung -> In Bearbeitung -> Bereit

Die folgende Tabelle enthält eine Beschreibung der möglichen Status eines Slices, und Sie erfahren, ob der Status mithilfe von "Set-AzureDataFactorySliceStatus" festgelegt werden kann oder nicht.

<table border="1">	
	<tr>
		<th>Status</th>
		<th>Beschreibung</th>
		<th>Kann mithilfe des Cmdlets festgelegt werden ></th>
	</tr>	

	<tr>
		<td>Ausstehende Ausführung</td>
		<td>Die Datenverarbeitung wurde noch nicht gestartet.</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>In Bearbeitung</td>
		<td>Die Datenverarbeitung wird ausgeführt.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Bereit</td>
		<td>Die Datenverarbeitung ist abgeschlossen, und der Datenslice ist bereit.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Fehler</td>
		<td>Fehler bei der Ausführung, die den Slice erstellt.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Überspringen</td>
		<td>Die Verarbeitung des Slices wird übersprungen.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Wiederholen</td>
		<td>Erneuter Versuch der Ausführung, die den Slice erstellt.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Zeitüberschreitung</td>
		<td>Zeitüberschreitung bei der Datenverarbeitung.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ausstehende Überprüfung</td>
		<td>Der Datenslice wartet auf Überprüfung im Hinblick auf Überprüfungsrichtlinien, bevor es verarbeitet wird.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Überprüfung wiederholen</td>
		<td>Die Überprüfung des Slices wird wiederholt.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Fehler bei der Überprüfung</td>
		<td>Fehler beim Überprüfen des Slices.</td>
		<td>N</td>
	</tr>
	</table>


### Unterstützte Werte - Aktualisierungstyp
Für jede Tabelle in einer Azure Data Factory müssen Sie beim Festlegen des Status eines Slices angeben, ob die Statusaktualisierung für nur die Tabelle gilt oder ob die Statusaktualisierungen für alle betroffenen Slices übertragen werden sollen.

<table border="1">	
	<tr>
		<th>Aktualisierungstyp</th>
		<th>Beschreibung</th>
		<th>Kann mithilfe des Cmdlets festgelegt werden</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>Legt den Status der einzelnen Slices für die Tabelle in dem angegebenen Zeitraum fest</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>Legt den Status jedes Slices für die Tabelle und alle abhängigen Tabellen (upstream) fest, die als Eingabetabellen für Aktivitäten in der Pipeline verwendet werden.</td>
		<td>Y</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
Das Suspend-AzureDataFactoryPipeline-Cmdlet hält die angegebene Pipeline in einer Azure Data Factory an. Sie können die Pipeline später mithilfe des "Resume-AzureDataFactoryPipeline"-Cmdlets fortsetzen.

### Beispiel

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Dieser Befehl hält die Pipeline "ADFTutorialPipeline" in der Azure Data Factory "ADFTutorialDataFactory" an.

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
Durch das Resume-AzureDataFactoryPipeline-Cmdlet wird die angegebene Pipeline fortgesetzt, die sich derzeit im angehaltenen Zustand in einer Azure Data Factory befindet. 

### Beispiel

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Mit diesem Befehl wird die Pipeline "ADFTutorialPipeline" in der Azure Data Factory "ADFTutorialDataFactory" fortgesetzt, die zuvor durch Verwendung des Befehls "Suspend-AzureDataFactoryPipeline" angehalten wurde.

## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Überwachen und Verwalten der Azure Data Factory mit dem Azure-Vorschauportal][monitor-manage-using-portal] | In diesem Artikel wird beschrieben, wie eine Azure Data Factory mithilfe des Azure-Vorschauportals überwacht und verwaltet wird.
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-BLOB.
[Verwenden von Pig und Hive mit Data Factory][use-pig-and-hive-with-data-factory] | Dieser Artikel enthält eine exemplarische Vorgehensweise, in der gezeigt wird, wie eine HDInsight-Aktivität verwendet wird, um ein Hive-/Pig-Skript zur Verarbeitung von Eingabedaten auszuführen, um Ausgabedaten zu produzieren.
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und deren Verwendung in einer Pipeline.
[Problembehandlung für Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Probleme in Azure Data Factory behoben werden.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 
[Azure Data Factory-Cmdlet-Referenz][cmdlet-reference] | Diese Referenz enthält Einzelheiten zu allen **Data Factory-Cmdlets**.

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--HONumber=35.2-->

<!--HONumber=46--> 
