<properties 
	pageTitle="Stream Analytics-Entwicklerhandbuch | Azure" 
	description="Erfahren Sie mehr über die Entwicklung von Azure Stream Analytics-Anwendungen."
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/17/2015" 
	ms.author="jgao"/>


# Azure Stream Analytics-Entwicklerhandbuch 

[Dies ist die Vorabdokumentation und kann in zukünftigen Versionen geändert werden.] 

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, Hochverfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Die Vorabversion von Stream Analytics ermöglicht Kunden die Einrichtung von Streaming-Aufträgen, um Datenströme zu analysieren und um eine Analyse in nahezu Echtzeit durchzuführen.  

Zielszenarien von Stream Analytics:

- Durchführen einer komplexen Ereignisverarbeitung bei hohem Datenvolumen und hoher Datengeschwindigkeit   
- Sammeln von Ereignisdaten von global verteilten Ressourcen oder Geräten, wie z. B. verbundenen Fahrzeugen oder Stromnetzen 
- Verarbeiten von Telemetriedaten für die Überwachung und Diagnose in nahezu Echtzeit 
- Erfassen und Archivieren von Echtzeitereignissen für die zukünftige Verarbeitung

Weitere Informationen finden Sie unter [Einführung in Azure Stream Analytics][stream.analytics.introduction]. 

Stream Analytics-Aufträge werden als eine oder mehrere Eingabequellen, eine Abfrage der eingehenden Streaming-Daten und als ein Ausgabeziel definiert.  


##Themen in diesem Artikel

+ [Eingaben](#inputs) 
+ [Query](#query)
+ [Ausgabe](#output)
+ [Skalieren von Aufträgen](#scale)
+ [Überwachung und Problembehandlung von Aufträgen](#monitor)
+ [Verwalten von Aufträgen](#manage)
+ [Nächste Schritte](#nextsteps)



##<a name="inputs"></a>Eingaben

### Datenstrom

Jede Stream Analytics-Auftragsdefinition muss mindestens eine Datenstrom-Eingabequelle enthalten, die vom Auftrag genutzt und umgewandelt werden soll.  [Azure BLOB-Speicher][azure.blob.storage] und [Azure Service Bus Event Hubs][azure.event.hubs] werden als Datenstrom-Eingabequellen unterstützt.  Event Hub-Eingabequellen dienen zum Sammeln von Ereignisströmen von mehreren verschiedenen Geräten und Diensten, während BLOB-Speicher als Eingabequelle für die Erfassung von großen Datenmengen verwendet werden können.  Da BLOBs keine Daten per Stream übertragen, sind Stream Analytics-Aufträge über BLOBs von Grund auf nicht zeitlicher Natur, es sei denn die Datensätze im BLOB enthalten Zeitstempel.

### Verweisdaten

Stream Analytics unterstützt auch einen zweiten Eingabequellentyp: Verweisdaten.  Dies sind zusätzliche Daten zum Durchführen von Korrelationen und Suchvorgängen, und die Daten hier sind in der Regel statisch oder ändern sich selten.  In der Vorabversion sind BLOB-Speicher die einzige unterstützte Eingabequelle für Verweisdaten.

### Serialisierung
Um das korrekte Verhalten von Abfragen sicherzustellen, muss Stream Analytics das Serialisierungsformat kennen, das für eingehende Datenströme verwendet wird. Derzeit unterstützte Formate sind JSON, CSV und Avro für Streaming-Daten und CSV für Verweisdaten.

### Generierte Eigenschaften
Je nach Eingabetyp im Auftrag werden einige zusätzliche Felder mit Ereignismetadaten generiert.  Diese Felder können genau wie andere Eingabespalten abgefragt werden.  Wenn ein vorhandenes Ereignis ein Feld mit dem gleichen Namen wie eine der folgenden Eigenschaften besitzt, wird sie mit den Eingabemetadaten überschrieben.

<table border="1">
	<tr>
		<th></th>
		<th>Eigenschaft</th>
		<th>Beschreibung</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>Der Name des Eingabe-BLOBs, aus dem das Ereignis stammt</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Das Datum und die Uhrzeit der Verarbeitung des BLOB-Datensatzes</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Das Datum und die Uhrzeit der letzten Änderung des BLOBs</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>Die nullbasierte Partitions-ID für den Eingabeadapter</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Event Hub</b></td>
		<td>EventProcessedUtcTime</td>
		<td>Das Datum und die Uhrzeit der Verarbeitung des Ereignisses</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>Das Datum und die Uhrzeit des Ereignisempfangs durch den Event Hub</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>Die nullbasierte Partitions-ID für den Eingabeadapter</td>
	</tr>
</table>



###Zusätzliche Ressourcen
Weitere Informationen zum Erstellen von Eingabequellen finden Sie im [Azure Event Hubs-Entwicklerhandbuch][azure.event.hubs.developer.guide] und unter [Verwenden von Azure BLOB-Speicher][azure.blob.storage.use].  



##<a name="query"></a>Query
Die Logik zum Filtern, Bearbeiten und Verarbeiten von eingehenden Daten ist in der Abfrage von Stream Analytics-Aufträgen definiert.  Abfragen werden mithilfe der Stream Analytics-Abfragesprache geschrieben, einer SQL-ähnlichen Sprache, die zum größten Teil eine Teilmenge der T-SQL-Standardsyntax ist und über einige spezielle Erweiterungen für zeitliche Abfragen verfügt.

###Windowing
Windowing-Erweiterungen ermöglichen die Durchführung von Aggregationen und Berechnungen für Teilmengen von Ereignissen, die innerhalb einer gewissen Zeitspanne liegen. Windowing-Funktionen werden mit der GROUP BY-Anweisung aufgerufen. Die folgende Abfrage zählt beispielsweise die Ereignisse, die pro Sekunde empfangen werden: 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###Ausführungsschritte
Für komplexere Abfragen kann die standardmäßige SQL-Klausel WITH verwendet werden, um einen temporär benannten Ergebnissatz anzugeben.  Die folgende Abfrage verwendet WITH beispielsweise, um eine Transformation mit zwei Ausführungsschritten durchzuführen:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Weitere Informationen zur Abfragesprache finden Sie in der [Azure Stream Analytics-Abfragesprechreferenz][stream.analytics.query.language.reference]. 

##<a name="output"></a>Ausgabe
Die Ausgabequelle ist der Ort, an den die Ergebnisse des Stream Analytics-Auftrags geschrieben werden. Ergebnisse werden kontinuierlich in die Ausgabequelle geschrieben, während der Auftrag Eingabeereignisse verarbeitet.  Die folgenden Ausgabequellen werden unterstützt:

- Azure Service Bus Event Hubs: Wählen Sie Event Hub als Ausgabequelle für Szenarien aus, wenn mehrere Streaming-Pipelines zusammengeführt werden müssen, z. B. die Rückgabe von Befehlen an Geräte.
- Azure BLOB-Speicher: Verwenden Sie BLOB-Speicher für die langfristige Archivierung der Ausgabe oder zum Speichern von Daten für die spätere Verarbeitung.
- Azure SQL-Datenbank: Diese Ausgabequelle eignet sich für Daten, die von Natur aus relational sind oder für Anwendungen, die von Inhalt abhängig sind, der in einer Datenbank gehostet wird.


##<a name="scale"></a>Skalieren von Aufträgen

Ein Stream Analytics-Auftrag kann durch Konfigurieren von Streaming-Einheiten skaliert werden, die die Größe der Verarbeitungsleistung für einen Auftrag bestimmen. Jede Streaming-Einheit entspricht etwa einem Durchsatz von 1 MB/s. Jedes Abonnement verfügt über ein Kontingent von 12 Streaming-Einheiten pro Region für Aufträge in dieser Region.

Einzelheiten finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs].


##<a name="monitor"></a>Überwachung und Problembehandlung von Aufträgen

###Regionales Überwachungskonto für Speicher

Zum Aktivieren der Auftragsüberwachung erfordert Stream Analytics, dass Sie ein Azure-Speicherkonto für die Überwachung von Daten in jeder Region mit Stream Analytics-Aufträgen festlegen.  Dies wird zum Zeitpunkt der Auftragserstellung konfiguriert.  

###Metriken
Die folgenden Metriken sind für die Überwachung der Nutzung und Leistung von Stream Analytics-Aufträgen verfügbar:

- Eingehender Durchsatz: vom Stream Analytics-Auftrag erhaltene Datenmenge im Hinblick auf die Ereignisanzahl
- Ausgehender Durchsatz: vom Stream Analytics-Auftrag an die Ausgabequelle gesendete Datenmenge im Hinblick auf die Ereignisanzahl
- Fehleranzahl: Anzahl von Fehlermeldungen, die durch einen Stream Analytics-Auftrag entstehen

###Vorgangsprotokolle
Das beste Verfahren zum Debuggen oder zur Fehlerbehebung eines Stream Analytics-Auftrags erfolgt über Azure-Vorgangsprotokolle.  Vorgangsprotokolle stehen im Management Services-Abschnitt des Portals zur Verfügung.  Legen Sie zum Überprüfen von Protokollen den Diensttyp auf "Stream Analytics" und den Dienstnamen auf den Namen des Auftrags fest.


##<a name="manage"></a>Verwalten von Aufträgen 

###Starten und Anhalten von Aufträgen
In der Vorabversion von Stream Analytics wird beim Anhalten eines Auftrags kein Status der letzten Ereignisse beibehalten, die vom Auftrag genutzt wurden.  Daher können beim Neustarten eines angehaltenen Auftrags gelöschte Ereignisse oder doppelte Daten auftreten.  Wenn ein Auftrag vorübergehend angehalten werden muss, besteht die bewährte Methode darin, die Ausgabe zu untersuchen und die Einfügezeit des letzten Datensatzes zu verwenden, um annäherungsweise herauszufinden, wann der Auftrag angehalten wurde.  Legen Sie dann diese Zeit in der Ausgabestart-Einstellung auf der Registerkarte "Konfigurieren" fest, wenn der Auftrag neu gestartet wird.
Dies ist eine temporäre Einschränkung, und die Aktivierung des Auftragsstarts und -stopps ohne Datenverlust hat eine hohe Priorität für zukünftige Versionen.  

###Konfigurieren von Aufträgen
Sie können die folgenden Einstellungen auf der obersten Ebene für einen Stream Analytics-Auftrag anpassen:

- Ausgabestart: Gibt an, wann dieser Auftrag mit der Ausgabe beginnt. Wenn die zugeordnete Abfrage ein Fenster enthält, beginnt der Auftrag mit dem Erfassen von Eingaben aus den Eingabequellen zu Beginn der erforderlichen Fensterdauer, um das erste Ausgabeereignis zum angegebenen Zeitpunkt zu erzeugen. Es gibt zwei Möglichkeiten: "Auftragsstartzeit" und "Benutzerdefiniert". Die Standardeinstellung ist "Auftragsstartzeit". Für die Option "Benutzerdefiniert" müssen Sie ein Datum und eine Uhrzeit angeben. Diese Einstellung ist nützlich für die Angabe, wie viele historische Daten in den Eingabequellen genutzt werden sollen oder für die Erfassung der Datenaufnahme von einem bestimmten Zeitpunkt, z. B. dem letzten Stopp des Auftrags. 
- Richtlinien für falsche Reihenfolge: Einstellungen für die Verarbeitung von Ereignissen, die nicht sequentiell beim Stream Analytics-Auftrag eingehen. Sie können einen Zeitschwellenwert angeben, um die enthaltenen Ereignisse neu zu ordnen, indem Sie ein Toleranzfenster festlegen. Sie können auch eine Aktion bestimmen, um Ereignisse außerhalb dieses Fensters zu bearbeiten: Löschen oder Anpassen.  Durch Löschen werden alle Ereignisse gelöscht, die in der falschen Reihenfolge empfangen wurden, und durch Anpassen wird der Systemzeitstempel (System.Timestamp) von Ereignissen in der falschen Reihenfolge in den Zeitstempel des zuletzt empfangenen sortierten Ereignisses geändert.  
- Gebietsschema: Verwenden Sie diese Einstellung, um die Voreinstellung für die Internationalisierung für den Stream Analytics-Auftrag anzugeben. Zeitstempel der Daten sind gebietsschemaneutral, die hier vorgenommenen Einstellungen haben Auswirkungen auf die Analyse, den Vergleich und die Sortierung der Daten. Für die Vorabversion wird nur en-US unterstützt.


##<a name="support"></a>Support
Zusätzlichen Support erhalten Sie im [Azure Stream Analytics-Forum][stream.analytics.forum]. 


##<a name="nextsteps"></a>Nächste Schritte

- [Einführung in Azure Stream Analytics][stream.analytics.introduction]
- [Erste Schritte mit Azure Stream Analytics][stream.analytics.get.started]
- [Skalieren von Azure Stream Analytics-Aufträgen][stream.analytics.scale.jobs]
- [Einschränkungen und bekannte Probleme von Azure Stream Analytics][stream.analytics.limitations]
- [Azure Stream Analytics-Abfragesprachreferenz][stream.analytics.query.language.reference]
- [Azure Stream Analytics Management REST API Referenz][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=46--> 
