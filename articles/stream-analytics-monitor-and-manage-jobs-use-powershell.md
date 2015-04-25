<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="Überwachen und Verwalten von Stream Analytics-Aufträgen mithilfe von PowerShell | Azure" description="Enthält Informationen zum Verwenden von Azure PowerShell-Cmdlets zum Überwachen und Verwalten von Streamanalyseaufträgen" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Überwachen und Verwalten von Stream Analytics-Aufträgen mithilfe von PowerShell

Erfahren Sie, wie Sie Azure Stream Analytics-Ressourcen mithilfe von Azure PowerShell verwalten können.

##Themen in diesem Artikel

- [Voraussetzungen](#prerequisites)
- [PowerShell-Cmdlets für Stream Analytics](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [Siehe auch](#seealso)

## <a name="prerequisites"></a>Voraussetzungen für das Ausführen von PowerShell-Cmdlets für Stream Analytics

1.	Installieren und Konfigurieren von Azure PowerShell

	Befolgen Sie die Anleitungen unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install], um Azure PowerShell zu installieren.

2.	Konfigurieren des Azure-Modus

	Führen Sie nach dem Installieren von Azure PowerShell das Cmdlet [Switch-AzureMode][msdn-switch-azuremode] zum Festlegen des geeigneten Azure-Modus für den Zugriff auf die Stream Analytics-Cmdlets aus:

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Es besteht eine vorübergehende Einschränkung: Für Stream Analytics-Aufträge, die über Azure PowerShell erstellt wurden, ist keine Überwachung aktiviert.  Zur Umgehung dieses Problems navigieren Sie zur Seite "Überwachung" des Auftrags im Azure-Verwaltungsportal, und klicken Sie dann auf die Schaltfläche "Aktivieren".  

##<a name="cmdlets"></a>PowerShell-Cmdlets für Stream Analytics
Die folgende Tabelle enthält die Cmdlets, die Sie zum Überwachen und Verwalten von Azure Stream Analytics mithilfe von Azure PowerShell verwenden können.

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Listet alle Stream Analytics-Aufträge auf, die im Azure-Abonnement oder in der Ressourcengruppe definiert sind, oder ruft Auftragsinformationen zu einem bestimmten Auftrag innerhalb einer Ressourcengruppe ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsJob

Dieser Befehl gibt Informationen zu allen Stream Analytics-Aufträgen im Azure-Abonnement zurück.

**Beispiel 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
Dieser Befehl gibt Informationen zu allen Stream Analytics-Aufträgen in der Ressourcengruppe "StreamAnalytics-Default-West-US" zurück.

**Beispiel 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
Dieser Befehl gibt Informationen zum Stream Analytics-Auftrag "StreamingJob" in der Ressourcengruppe "StreamAnalytics-Default-West-US" zurück.

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
Listet alle Eingaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Eingabe ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

Dieser Befehl gibt Informationen zu allen Eingaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Dieser Befehl gibt Informationen zu der Eingabe namens "EntryStream" zurück, die für den Auftrag "StreamingJob" definiert ist.

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
Listet alle Ausgaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Ausgabe ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
Dieser Befehl gibt Informationen zu den Ausgaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Dieser Befehl gibt Informationen zu der Ausgabe namens "Output" zurück, die für den Auftrag "StreamingJob" definiert ist.

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
Ruft Informationen zum Streamingeinheitkontingent einer angegebenen Region ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
Dieser Befehl gibt Informationen zum Streamingeinheitkontingent und zur Nutzung in der Region "Westen USA" zurück.

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
Ruft Informationen zu einer bestimmten Transformation ab, die für den Stream Analytics-Auftrag definiert ist.

**Beispiel 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
Dieser Befehl gibt Informationen zu der Transformation namens "StreamingJob" für den Auftrag "StreamingJob" zurück.

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
Erstellt eine neue Eingabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene angegebene Eingabe.
  
Der Name der Eingabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden.  Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Eingabe angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Eingabe ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Eingabenamen angeben , wird die Eingabe ohne Bestätigung ersetzt.

**Beispiel 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
Dieser Befehl erstellt eine neue Eingabe aus der Datei "Input.json".  Wenn eine vorhandene Eingabe mit dem in der Eingabedefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
Dieser Befehl erstellt eine neue Eingabe für den Auftrag namens "EntryStream".  Wenn eine vorhandene Eingabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
Dieser Befehl ersetzt die Definition der vorhandenen Eingabequelle namens "EntryStream" durch die Definition aus der Datei.

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Erstellt einen neuen Stream Analytics-Auftrag in Microsoft Azure oder aktualisiert die Definition eines vorhandenen angegebenen Auftrags.

Der Name des Auftrags kann in der JSON-Datei oder in der Befehlszeile angegeben werden.  Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie einen Auftragsnamen angeben, der bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob der vorhandene Auftrag ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Auftragsnamen angeben , wird die Auftragsdefinition ohne Bestätigung ersetzt.

**Beispiel 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
Dieser Befehl erstellt einen neuen Auftrag aus der Definition in der Datei "JobDefinition.json".  Wenn ein vorhandener Auftrag mit dem in der Auftragsdefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob dieser ersetzt werden soll.

**Beispiel 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
Dieser Befehl ersetzt die Auftragsdefinition für "StreamingJob".

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
Erstellt eine neue Ausgabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene Ausgabe.  

Der Name der Ausgabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden.  Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Ausgabe angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Ausgabe ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Ausgabenamen angeben , wird die Ausgabe ohne Bestätigung ersetzt.

**Beispiel 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
Dieser Befehl erstellt eine neue Ausgabe namens "output" im Auftrag "StreamingJob".  Wenn eine vorhandene Ausgabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
Dieser Befehl ersetzt die Definition für "output" im Auftrag "StreamingJob".

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
Erstellt eine neue Transformation in einem Stream Analytics-Auftrag oder aktualisiert die vorhandene Transformation.
  
Der Name der Transformation kann in der JSON-Datei oder in der Befehlszeile angegeben werden.  Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Transformation angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Transformation ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Transformationsnamen angeben , wird die Transformation ohne Bestätigung ersetzt.

**Beispiel 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
Dieser Befehl erstellt eine neue Transformation namens "StreamingJobTransform" im Auftrag "StreamingJob".  Wenn eine vorhandene Transformation mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 Dieser Befehl ersetzt die Definition von "StreamingJobTransform" im Auftrag "StreamingJob".

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Löscht asynchron eine bestimmte Eingabe aus einem Stream Analytics-Auftrag in Microsoft Azure.  
Wenn Sie den Parameter "-Force" angeben, wird die Eingabe ohne Bestätigung gelöscht.

**Beispiel 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
Dieser Befehl entfernt die Eingabe "EventStream" im Auftrag "StreamingJob".  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Löscht asynchron einen bestimmten Stream Analytics-Auftrag in Microsoft Azure.  
Wenn Sie den Parameter "-Force" angeben, wird der Auftrag ohne Bestätigung gelöscht.

**Beispiel 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Dieser Befehl entfernt den Auftrag "StreamingJob".  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Löscht asynchron eine bestimmte Ausgabe aus einem Stream Analytics-Auftrag in Microsoft Azure.  
Wenn Sie den Parameter "-Force" angeben, wird die Ausgabe ohne Bestätigung gelöscht.

**Beispiel 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Dieser Befehl entfernt die Ausgabe "Output" im Auftrag "StreamingJob".  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
Stellt asynchron einen Stream Analytics-Auftrag in Microsoft Azure bereit und startet diesen.

**Beispiel 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Dieser Befehl startet den Auftrag "StreamingJob".  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Beendet die Ausführung eines Stream Analytics-Auftrags in Microsoft Azure asynchron und hebt die Zuordnung der Ressourcen auf, die verwendet wurden. Die Auftragsdefinition und die Metadaten bleiben in Ihrem Abonnement über das Azure-Portal und die Verwaltungs-APIs verfügbar, damit der Auftrag bearbeitet und neu gestartet werden kann. Es entstehen keine Kosten für einen Auftrag mit dem Zustand "Beendet".

**Beispiel 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Dieser Befehl beendet den Auftrag "StreamingJob".  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Eingabe herstellen kann.

**Beispiel 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Hier wird der Verbindungsstatus der Eingabe "EntryStream" in "StreamingJob" getestet.  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Ausgabe herstellen kann.

**Beispiel 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Hier wird der Verbindungsstatus der Ausgabe "Output" in "StreamingJob" getestet.  


##<a name="seealso"></a>Siehe auch

- [Einführung in Azure Stream Analytics][stream.analytics.introduction]
- [Erste Schritte mit Stream Analytics][stream.analytics.get.started]
- [Einschränkungen in der Preview-Version von Stream Analytics][stream.analytics.limitations]
- [Entwicklerhandbuch für Stream Analytics][stream.analytics.developer.guide]
- [Abfragesprachenreferenz für Stream Analytics][stream.analytics.query.language.reference]
- [REST-API-Referenz für Stream Analytics][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/de-de/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
