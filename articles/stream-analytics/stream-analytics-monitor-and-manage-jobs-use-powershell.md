<properties 
	pageTitle="Überwachen und Verwalten von Stream Analytics-Aufträgen mithilfe von PowerShell | Azure" 
	description="Erfahren Sie, wie Sie Stream Analytics-Aufträge mithilfe von PowerShell-Cmdlets überwachen und verwalten können." 
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
	ms.date="05/07/2015" 
	ms.author="jeffstok"/>


# Überwachen und Verwalten von Stream Analytics-Aufträgen mithilfe von Azure PowerShell

Erfahren Sie, wie Sie Azure Stream Analytics-Ressourcen mithilfe von Azure PowerShell verwalten können.

## Voraussetzungen für das Ausführen von Azure PowerShell-Cmdlets für Stream Analytics

1.	Installieren und konfigurieren Sie Azure PowerShell.

	Befolgen Sie die Anweisungen unter [How to install and configure Azure PowerShell][powershell-install] (in englischer Sprache), um Azure PowerShell zu installieren.

	So verbinden Sie Ihr Azure-Abonnement mit der Azure Active Directory-Methode

		Add-AzureAccount

	So wählen Sie Ihr Azure-Abonnement bei aktiviertem Azure Stream Analytics-Dienst aus

		Select-AzureSubscription

	>[AZURE.NOTE]Die folgende Fehlermeldung gibt an, dass Azure Stream Analytics für das Abonnement nicht aktiviert ist:
	>
		Error Code: InvalidResourceType.  Error Message: The resource type 'streamingjobs' could not be found in the namespace 'Microsoft.StreamAnalytics'.  
	
	>Um dieses Problem zu beheben, aktivieren Sie die Stream Analytics-Vorschau für das Abonnement, und führen Sie die folgenden Cmdlets aus, um das Abonnement zu wechseln:
	>
		Select-AzureSubscription –SubscriptionId xxxxxxxx

2.	Konfigurieren Sie den Azure-Modus.

	Führen Sie nach dem Installieren von Azure PowerShell das Cmdlet [Switch-AzureMode][msdn-switch-azuremode] zum Festlegen des geeigneten Azure-Modus für den Zugriff auf Stream Analytics-Cmdlets aus:

		Switch-AzureMode AzureResourceManager

>[AZURE.NOTE]Es besteht eine vorübergehende Einschränkung: Für Stream Analytics-Aufträge, die über Azure PowerShell erstellt wurden, ist keine Überwachung aktiviert. Zur Umgehung dieses Problems navigieren Sie zur Seite **Überwachung** des Auftrags im Azure-Verwaltungsportal, und klicken Sie dann auf die Schaltfläche **Aktivieren**.

## Azure PowerShell-Cmdlets für Stream Analytics
Mit den folgenden PowerShell-Cmdlets können Azure Stream Analytics-Aufträge überwacht und verwaltet werden.

### Get-AzureStreamAnalyticsJob
Listet alle Stream Analytics-Aufträge auf, die im Azure-Abonnement oder in der Ressourcengruppe definiert sind, oder ruft Auftragsinformationen zu einem bestimmten Auftrag innerhalb einer Ressourcengruppe ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsJob

Dieser Befehl gibt Informationen zu allen Stream Analytics-Aufträgen im Azure-Abonnement zurück.

**Beispiel 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
Dieser Befehl gibt Informationen zu allen Stream Analytics-Aufträgen in der Ressourcengruppe "StreamAnalytics-Default-Central-US" zurück.

**Beispiel 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
Dieser Befehl gibt Informationen zum Stream Analytics-Auftrag "StreamingJob" in der Ressourcengruppe "StreamAnalytics-Default-Central-US" zurück.

### Get-AzureStreamAnalyticsInput
Listet alle Eingaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Eingabe ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Dieser Befehl gibt Informationen zu allen Eingaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
Dieser Befehl gibt Informationen zu der Eingabe "EntryStream" zurück, die für den Auftrag "StreamingJob" definiert ist.

### Get-AzureStreamAnalyticsOutput
Listet alle Ausgaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Ausgabe ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
Dieser Befehl gibt Informationen zu den Ausgaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
Dieser Befehl gibt Informationen zu der Ausgabe "Output" zurück, die für den Auftrag "StreamingJob" definiert ist.

### Get-AzureStreamAnalyticsQuota
Ruft Informationen zum Streamingeinheitenkontingent einer angegebenen Region ab.

**Beispiel 1**

	Get-AzureStreamAnalyticsQuota –Location "Central US" 
Dieser Befehl gibt Informationen zum Streamingeinheitenkontingent und zur Nutzung in der Region "Central USA" zurück.

### Get-AzureStreamAnalyticsTransformation
Ruft Informationen zu einer bestimmten Transformation ab, die im Stream Analytics-Auftrag definiert ist.

**Beispiel 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
Dieser Befehl gibt Informationen zu der Transformation "StreamingJob" im Auftrag "StreamingJob" zurück.

### New-AzureStreamAnalyticsInput
Erstellt eine neue Eingabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene angegebene Eingabe.
  
Der Name der Eingabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Eingabe angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Eingabe ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Eingabenamen angeben, wird die Eingabe ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Eingabe erstellen (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
Dieser Befehl erstellt eine neue Eingabe aus der Datei "Input.json". Wenn eine vorhandene Eingabe mit dem in der Eingabedefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
Dieser Befehl erstellt eine neue Eingabe für den Auftrag "EntryStream". Wenn eine vorhandene Eingabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
Dieser Befehl ersetzt die Definition der vorhandenen Eingabequelle "EntryStream" durch die Definition aus der Datei.

### New-AzureStreamAnalyticsJob
Erstellt einen neuen Stream Analytics-Auftrag in Microsoft Azure oder aktualisiert die Definition eines vorhandenen angegebenen Auftrags.

Der Name des Auftrags kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie einen Auftragsnamen angeben, der bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob der vorhandene Auftrag ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Auftragsnamen angeben , wird die Auftragsdefinition ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Erstellen eines Stream Analytics-Auftrags][msdn-rest-api-create-stream-analytics-job] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
Dieser Befehl erstellt einen neuen Auftrag aus der Definition in der Datei "JobDefinition.json". Wenn ein vorhandener Auftrag mit dem in der Auftragsdefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob dieser ersetzt werden soll.

**Beispiel 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
Dieser Befehl ersetzt die Auftragsdefinition für "StreamingJob".

### New-AzureStreamAnalyticsOutput
Erstellt eine neue Ausgabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene Ausgabe.

Der Name der Ausgabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Ausgabe angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Ausgabe ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Ausgabenamen angeben , wird die Ausgabe ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Ausgabe erstellen (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
Dieser Befehl erstellt eine neue Ausgabe namens "output" im Auftrag "StreamingJob". Wenn eine vorhandene Ausgabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
Dieser Befehl ersetzt die Definition für "output" im Auftrag "StreamingJob".

### New-AzureStreamAnalyticsTransformation
Erstellt eine neue Transformation in einem Stream Analytics-Auftrag oder aktualisiert die vorhandene Transformation.
  
Der Name der Transformation kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine Transformation angeben, die bereits vorhanden ist, und Sie geben den Parameter "-Force" nicht an, fragt das Cmdlet, ob die vorhandene Transformation ersetzt werden soll.

Wenn Sie den Parameter "-Force" und einen vorhandenen Transformationsnamen angeben, wird die Transformation ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Create Transformation (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
Dieser Befehl erstellt eine neue Transformation namens "StreamingJobTransform" im Auftrag "StreamingJob". Wenn eine vorhandene Transformation mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
 Dieser Befehl ersetzt die Definition von "StreamingJobTransform" im Auftrag "StreamingJob".

### Remove-AzureStreamAnalyticsInput
Löscht asynchron eine bestimmte Eingabe aus einem Stream Analytics-Auftrag in Microsoft Azure. Wenn Sie den Parameter "-Force" angeben, wird die Eingabe ohne Bestätigung gelöscht.

**Beispiel 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
Dieser Befehl entfernt die Eingabe "EventStream" im Auftrag "StreamingJob".

### Remove-AzureStreamAnalyticsJob
Löscht asynchron einen bestimmten Stream Analytics-Auftrag in Microsoft Azure. Wenn Sie den Parameter "-Force" angeben, wird der Auftrag ohne Bestätigung gelöscht.

**Beispiel 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Dieser Befehl entfernt den Auftrag "StreamingJob".

### Remove-AzureStreamAnalyticsOutput
Löscht asynchron eine bestimmte Ausgabe aus einem Stream Analytics-Auftrag in Microsoft Azure. Wenn Sie den Parameter "-Force" angeben, wird die Ausgabe ohne Bestätigung gelöscht.

**Beispiel 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Dieser Befehl entfernt die Ausgabe "Output" im Auftrag "StreamingJob".

### Start-AzureStreamAnalyticsJob
Stellt asynchron einen Stream Analytics-Auftrag in Microsoft Azure bereit und startet diesen.

**Beispiel 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Dieser Befehl startet den Auftrag "StreamingJob" am 12. Dezember 2012 mit der benutzerdefinierten Ausgabestartzeit 12:12:12 UTC.


### Stop-AzureStreamAnalyticsJob
Beendet die Ausführung eines Stream Analytics-Auftrags in Microsoft Azure asynchron und hebt die Zuordnung der Ressourcen auf, die verwendet wurden. Die Auftragsdefinition und die Metadaten bleiben in Ihrem Abonnement über das Azure-Portal und die Verwaltungs-APIs verfügbar, damit der Auftrag bearbeitet und neu gestartet werden kann. Es entstehen keine Kosten für einen Auftrag mit dem Zustand "Beendet".

**Beispiel 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Dieser Befehl beendet den Auftrag "StreamingJob".

### Test-AzureStreamAnalyticsInput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Eingabe herstellen kann.

**Beispiel 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
Hier wird der Verbindungsstatus der Eingabe "EntryStream" in "StreamingJob" getestet.

### Test-AzureStreamAnalyticsOutput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Ausgabe herstellen kann.

**Beispiel 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Hier wird der Verbindungsstatus der Ausgabe "Output" in "StreamingJob" getestet.

> [AZURE.NOTE]Für programmgesteuert erstellte Stream Analytics-Aufträge ist die Überwachung in der Standardeinstellung nicht aktiviert. Sie können die Überwachung manuell im Azure-Portal aktivieren, indem Sie zur Überwachungsseite des Auftrags wechseln und auf die Schaltfläche "Aktivieren" klicken. Zudem können Sie programmgesteuert vorgehen, indem Sie die Schritte unter [Azure Stream Analytics – programmgesteuerte Überwachung von Stream Analytics-Aufträgen](stream-analytics-monitor-jobs.md) durchführen.

## Support
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54--> 