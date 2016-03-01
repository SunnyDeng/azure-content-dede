<properties 
	pageTitle="Überwachen und Verwalten von Stream Analytics-Aufträgen mit von PowerShell | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Stream Analytics-Aufträge mithilfe von PowerShell und Cmdlets überwachen und verwalten können." 
	keywords="Azure PowerShell, Azure PowerShell-Cmdlets, PowerShell-Befehl, PowerShell-Skripts"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/16/2016" 
	ms.author="jeffstok"/>


# Überwachen und Verwalten von Stream Analytics-Aufträgen mit PowerShell-Cmdlets

Erfahren Sie, wie Sie Stream Analytics-Ressourcen mit Azure PowerShell-Cmdlets und -Skripts, die grundlegende Stream Analytics-Aufgaben ausführen, überwachen und verwalten.

## Voraussetzungen für das Ausführen von Azure PowerShell-Cmdlets für Stream Analytics

 - Erstellen Sie in Ihrem Abonnement eine Azure-Ressourcengruppe. Nachfolgend ist ein Azure PowerShell-Beispielskript aufgeführt. Informationen zu Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).  

Azure PowerShell 0.9.8:

 		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
		Select-AzureSubscription -SubscriptionName <subscription name>
 
		# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
		#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

		# Create an Azure resource group
		New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:

 		# Log in to your Azure account
		Login-AzureRmAccount

		# Select the Azure subscription you want to use to create the resource group.
		Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

		# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
		#Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
		
		# Create an Azure resource group
		New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
		


> [AZURE.NOTE] Für programmgesteuert erstellte Stream Analytics-Aufträge ist die Überwachung in der Standardeinstellung nicht aktiviert. Sie können die Überwachung manuell im Azure-Portal aktivieren, indem Sie zur Überwachungsseite des Auftrags wechseln und auf die Schaltfläche „Aktivieren“ klicken. Zudem können Sie programmgesteuert vorgehen, indem Sie die Schritte unter [Azure Stream Analytics – programmgesteuerte Überwachung von Stream Analytics-Aufträgen](stream-analytics-monitor-jobs.md) durchführen.

## Azure PowerShell-Cmdlets für Stream Analytics
Mit den folgenden PowerShell-Cmdlets können Azure Stream Analytics-Aufträge überwacht und verwaltet werden. Beachten Sie, dass es verschiedene Versionen von Azure PowerShell gibt. **In den aufgeführten Beispielen gilt der erste Befehl für Azure PowerShell 0.9.8 und der zweite Befehl für Azure PowerShell 1.0.** Die Azure PowerShell-1.0-Befehle müssen immer „AzureRM“ im Befehl enthalten.

### Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Listet alle Stream Analytics-Aufträge auf, die im Azure-Abonnement oder in der Ressourcengruppe definiert sind, oder ruft Auftragsinformationen zu einem bestimmten Auftrag innerhalb einer Ressourcengruppe ab.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsJob

Dieser PowerShell-Befehl gibt Informationen zu allen Stream Analytics-Aufträgen im Azure-Abonnement zurück.

**Beispiel 2**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Dieser PowerShell-Befehl gibt Informationen zu allen Stream Analytics-Aufträgen in der Ressourcengruppe "StreamAnalytics-Default-Central-US" zurück.

**Beispiel 3**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Dieser PowerShell-Befehl gibt Informationen zum Stream Analytics-Auftrag "StreamingJob" in der Ressourcengruppe "StreamAnalytics-Default-Central-US" zurück.

### Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Listet alle Eingaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Eingabe ab.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Dieser PowerShell-Befehl gibt Informationen zu allen Eingaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Dieser PowerShell-Befehl gibt Informationen zu der Eingabe "EntryStream" zurück, die für den Auftrag "StreamingJob" definiert ist.

### Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Listet alle Ausgaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Ausgabe ab.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Dieser PowerShell-Befehl gibt Informationen zu Ausgaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Dieser PowerShell-Befehl gibt Informationen zu der Ausgabe "Output" zurück, die für den Auftrag "StreamingJob" definiert ist.

### Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Ruft Informationen zum Streamingeinheitenkontingent einer angegebenen Region ab.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Dieser PowerShell-Befehl gibt Informationen zum Streamingeinheitenkontingent und zur Nutzung in der Region "Central USA" zurück.

### Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Ruft Informationen zu einer bestimmten Transformation ab, die im Stream Analytics-Auftrag definiert ist.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:

	Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Dieser PowerShell-Befehl gibt Informationen zu der Transformation "StreamingJob" im Auftrag "StreamingJob" zurück.

### New-AzureStreamAnalyticsInput | New-AzureRMStreamAnalyticsInput
Erstellt eine neue Eingabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene angegebene Eingabe.
  
Der Name der Eingabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Eingabe angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Eingabe ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Eingabenamen angeben, wird die Eingabe ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Eingabe erstellen (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Dieser PowerShell-Befehl erstellt eine neue Eingabe aus der Datei "Input.json". Wenn eine vorhandene Eingabe mit dem in der Eingabedefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Dieser PowerShell-Befehl erstellt eine neue Eingabe für den Auftrag "EntryStream". Wenn eine vorhandene Eingabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 3**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Dieser PowerShell-Befehl ersetzt die Definition der vorhandenen Eingabequelle "EntryStream" durch die Definition aus der Datei.

### New-AzureStreamAnalyticsJob | New-AzureRMStreamAnalyticsJob
Erstellt einen neuen Stream Analytics-Auftrag in Microsoft Azure oder aktualisiert die Definition eines vorhandenen angegebenen Auftrags.

Der Name des Auftrags kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie einen Auftragsnamen angeben, der bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob der vorhandene Auftrag ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Auftragsnamen angeben , wird die Auftragsdefinition ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Erstellen eines Stream Analytics-Auftrags][msdn-rest-api-create-stream-analytics-job] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Dieser PowerShell-Befehl erstellt einen neuen Auftrag aus der Definition in der Datei "JobDefinition.json". Wenn ein vorhandener Auftrag mit dem in der Auftragsdefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob dieser ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Dieser PowerShell-Befehl ersetzt die Auftragsdefinition für "StreamingJob".

### New-AzureStreamAnalyticsOutput | New-AzureRMStreamAnalyticsOutput
Erstellt eine neue Ausgabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene Ausgabe.

Der Name der Ausgabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Ausgabe angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Ausgabe ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Ausgabenamen angeben , wird die Ausgabe ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Ausgabe erstellen (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Dieser PowerShell-Befehl erstellt eine neue Ausgabe namens "output" im Auftrag "StreamingJob". Wenn eine vorhandene Ausgabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Dieser PowerShell-Befehl ersetzt die Definition für "output" im Auftrag "StreamingJob".

### New-AzureStreamAnalyticsTransformation | New-AzureRMStreamAnalyticsTransformation
Erstellt eine neue Transformation in einem Stream Analytics-Auftrag oder aktualisiert die vorhandene Transformation.
  
Der Name der Transformation kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Transformation angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Transformation ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Transformationsnamen angeben, wird die Transformation ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Create Transformation (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Dieser PowerShell-Befehl erstellt eine neue Transformation namens "StreamingJobTransform" im Auftrag "StreamingJob". Wenn eine vorhandene Transformation mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:

	New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Dieser PowerShell-Befehl ersetzt die Definition von "StreamingJobTransform" im Auftrag "StreamingJob".

### Remove-AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Löscht asynchron eine bestimmte Eingabe aus einem Stream Analytics-Auftrag in Microsoft Azure. Wenn Sie den Parameter "-Force" angeben, wird die Eingabe ohne Bestätigung gelöscht.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:

	Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Dieser PowerShell-Befehl entfernt die Eingabe "EventStream" im Auftrag "StreamingJob".

### Remove-AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Löscht asynchron einen bestimmten Stream Analytics-Auftrag in Microsoft Azure. Wenn Sie den Parameter "-Force" angeben, wird der Auftrag ohne Bestätigung gelöscht.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:

	Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Dieser PowerShell-Befehl entfernt den Auftrag "StreamingJob".

### Remove-AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
Löscht asynchron eine bestimmte Ausgabe aus einem Stream Analytics-Auftrag in Microsoft Azure. Wenn Sie den Parameter "-Force" angeben, wird die Ausgabe ohne Bestätigung gelöscht.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:

	Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Dieser PowerShell-Befehl entfernt die Ausgabe "Output" im Auftrag "StreamingJob".

### Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Stellt asynchron einen Stream Analytics-Auftrag in Microsoft Azure bereit und startet diesen.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:

	Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Dieser PowerShell-Befehl startet den Auftrag "StreamingJob" am 12. Dezember 2012 mit der benutzerdefinierten Ausgabestartzeit 12:12:12 UTC.


### Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Beendet die Ausführung eines Stream Analytics-Auftrags in Microsoft Azure asynchron und hebt die Zuordnung der Ressourcen auf, die verwendet wurden. Die Auftragsdefinition und die Metadaten bleiben in Ihrem Abonnement über das Azure-Portal und die Verwaltungs-APIs verfügbar, damit der Auftrag bearbeitet und neu gestartet werden kann. Es entstehen keine Kosten für einen Auftrag mit dem Zustand "Beendet".

**Beispiel 1**

Azure PowerShell 0.9.8:

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:

	Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Dieser PowerShell-Befehl beendet den Auftrag "StreamingJob".

### Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Eingabe herstellen kann.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:

	Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Dieser PowerShell-Befehl testet den Verbindungsstatus der Eingabe "EntryStream" in "StreamingJob".

###Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Ausgabe herstellen kann.

**Beispiel 1**

Azure PowerShell 0.9.8:

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:

	Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Dieser PowerShell-Befehl testet den Verbindungsstatus der Ausgabe "Output" in "StreamingJob".

## Support
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).


## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=AcomDC_0218_2016-->