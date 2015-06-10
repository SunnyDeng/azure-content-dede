<properties 
	pageTitle="Erfahren Sie mehr über die Schlüsselkonzepte von Stream Analytics | Microsoft Azure" 
	description="Erfahren Sie mehr über die Schlüsselkonzepte von Azure Stream Analytics: Komponenten von Stream Analytics-Aufträgen, einschließlich unterstützte Eingaben und Ausgaben, Auftragskonfiguration und Metriken." 
	keywords="event processing,data stream,key concepts,serialization"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/28/2015" 
	ms.author="jeffstok" />


# Schlüsselkonzepte von Stream Analytics: Anleitung für die Grundlagen eines Stream Analytics-Auftrags 

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, hohe Verfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Stream Analytics ermöglicht Kunden die Einrichtung von Streaming-Aufträgen, um Datenströme zu analysieren und um eine Analyse nahezu in Echtzeit durchzuführen. Dieser Artikel beschreibt die wichtigsten Konzepte eines Stream Analytics -Auftrags.

## Welche Möglichkeiten bietet Stream Analytics?
Stream Analytics ermöglicht Folgendes:

- Durchführen einer komplexen Ereignisverarbeitung bei hohem Datenvolumen und hoher Datengeschwindigkeit   
- Sammeln von Ereignisdaten von global verteilten Ressourcen oder Geräten, wie z. B. verbundenen Fahrzeugen oder Stromnetzen 
- Verarbeiten von Telemetriedaten für die Überwachung und Diagnose nahezu in Echtzeit 
- Erfassen und Archivieren von Echtzeitereignissen für die zukünftige Verarbeitung

Weitere Informationen finden Sie unter [Einführung in Azure Stream Analytics](stream.analytics.introduction).

Ein Stream Analytics-Auftrag umfasst alle der folgenden Angaben: * Ein oder mehrere Eingabequellen * Eine Abfrage über einen eingehender Datenstrom * Ein Ausgabeziel.


## Eingaben

### Datenstrom

Jede Stream Analytics-Auftragsdefinition muss mindestens eine Datenstrom-Eingabequelle enthalten, die vom Auftrag genutzt und umgewandelt werden soll. [Azure-Blob-Speicher](azure.blob.storage) und [Azure Event Hubs](azure.event.hubs) werden als Datenstrom-Eingabequellen unterstützt. Event Hub-Eingabequellen dienen zum Sammeln von Ereignisströmen von mehreren verschiedenen Geräten und Diensten, während Blob-Speicher als Eingabequelle für die Erfassung von großen Datenmengen verwendet werden können. Da Blobs keine Daten per Stream übertragen, sind Stream Analytics-Aufträge über Blobs nicht zeitbezogen, es sei denn, die Datensätze im Blob enthalten Zeitstempel.

### Verweisdaten
Stream Analytics unterstützt auch einen zweiten Eingabequellentyp: Verweisdaten. Dies sind zusätzliche Daten zum Durchführen von Korrelationen und Suchvorgängen, und die Daten hier sind in der Regel statisch oder ändern sich selten. [Azure-Blob-Speicher](azure.blob.storage) sind die einzige unterstützte Eingabequelle für Verweisdaten. Blobs für Verweisdatenquellen sind auf eine Größe von 50 MB beschränkt.

Um die Unterstützung für das Aktualisieren von Verweisdaten zu aktivieren, muss der Benutzer in der Eingabekonfiguration eine Liste von Blobs angeben. Dies erfolgt im Pfadmuster mithilfe der Tokens {date} und {time}. Der Auftrag lädt das entsprechende Blob anhand des im Blob-Namen codierten Datums und der Uhrzeit in der UTC-Zeitzone.

Wenn für den Auftrag im Portal z. B. eine Verweiseingabe mit dem Pfadmuster "/beispiel/{date}/{time}/products.csv" konfiguriert wurde, deren Datumsformat "JJJJ-MM-TT" und deren Uhrzeitformat "HH:mm" lautet, erfasst der Auftrag am 16. April 2015 um 17 Uhr 30 (UTC-Zeitzone) die Datei "/beispiel/2015-04-16/17:30/products.csv" (dies entspricht in der PST-Zeitzone 10 Uhr 30 am 16. April 2015).


### Serialisierung
Um das korrekte Verhalten von Abfragen sicherzustellen, muss Stream Analytics das Serialisierungsformat kennen, das für eingehende Datenströme verwendet wird. Derzeit unterstützte Formate sind JSON, CSV und AVRO für Streaming-Daten und CSV oder JSON für Verweisdaten.

### Generierte Eigenschaften
Je nach Eingabetyp im Auftrag werden einige zusätzliche Felder mit Ereignismetadaten generiert. Diese Felder können genau wie andere Eingabespalten abgefragt werden. Wenn ein vorhandenes Ereignis ein Feld mit dem gleichen Namen wie eine der folgenden Eigenschaften besitzt, wird sie mit den Eingabemetadaten überschrieben.

<table border="1">
	<tr>
		<th></th>
		<th>Eigenschaft</th>
		<th>Beschreibung</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>Der Name des Eingabe-Blobs, aus dem das Ereignis stammt.</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Das Datum und die Uhrzeit der Verarbeitung des Blob-Datensatzes.</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Das Datum und die Uhrzeit der letzten Änderung des Blobs.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>Die nullbasierte Partitions-ID für den Eingabeadapter.</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Event Hub</b></td>
		<td>EventProcessedUtcTime</td>
		<td>Das Datum und die Uhrzeit der Verarbeitung des Ereignisses.</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>Das Datum und die Uhrzeit des Ereignisempfangs durch die Event Hubs.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>Die nullbasierte Partitions-ID für den Eingabeadapter.</td>
	</tr>
</table>

###Partitionen mit langsamem oder gar keinen Eingabedaten
Beim Lesen von Eingabequellen mit mehreren Partitionen, bei denen mindestens eine Partition verlangsamt ist oder über keine Daten verfügt, muss der Streaming-Auftrag entscheiden, wie mit der Situation umgegangen wird, damit die Ereignisse weiter durch das System fließen. Die Eingabeeinstellung "Maximum allowed arrival delay" steuert dieses Verhalten und wartet in der Standardeinstellung über einen unbegrenzten Zeitraum auf die Daten. Daher werden zwar einerseits die Ereigniszeitstempel nicht verändert, andererseits fließen die Ereignisse jedoch beruhend auf der langsamsten Eingabepartition, wobei der Fluss unterbrochen wird, wenn mindestens eine Eingabepartition über keine Daten verfügt. Dies ist hilfreich, wenn die Daten gleichmäßig auf alle Eingabepartitionen verteilt sind und wenn die zeitliche Konsistenz unter den Ereignissen entscheidend ist.

Sie können jedoch auch festlegen, nur eine begrenzte Zeit zu warten. Mit "Maximum allowed arrival delay" wird die Verzögerung angegeben, nach der der Auftrag weitergeleitet wird und die nachhinkenden Eingabepartitionen hinter sich lässt. Hierbei wird der Umgang mit verzögerten Ereignissen über die Einstellung "Action for late events" festgelegt, wobei Ereignisse verworfen werden oder deren Zeitstempel angepasst wird, wenn Daten später ankommen. Dies ist hilfreich, wenn die Latenzzeit wichtig und eine Veränderung der Zeitstempel zulässig ist. Die Eingabe wird jedoch möglicherweise nicht gleichmäßig verteilt.

###Partitionen mit Ereignissen in der falschen Reihenfolge
Wenn für die Streaming-Auftragsabfrage das Schlüsselwort "TIMESTAMP BY" verwendet wird, kann die Reihenfolge nicht gewährleistet werden, in der die Ereignisse an der Eingabe ankommen. Einige Ereignisse derselben Eingabepartition sind möglicherweise verzögert, sodass der Parameter "Maximum allowed disorder within an input" dazu führt, dass der Streaming-Auftrag auf Ereignisse außerhalb der Toleranz anhand der Einstellung "Action for late events" reagiert und diese verwirft oder deren Zeitstempel anpasst.

### Zusätzliche Ressourcen
Weitere Informationen zum Erstellen von Eingabequellen finden Sie im [Azure Event Hubs-Entwicklerhandbuch](azure.event.hubs.developer.guide) und unter [Verwenden von Azure Blob-Speicher](azure.blob.storage.use).



## Abfragen
Die Logik zum Filtern, Bearbeiten und Verarbeiten von eingehenden Daten ist in der Abfrage von Stream Analytics-Aufträgen definiert. Abfragen werden in der Stream Analytics-Abfragesprache geschrieben, einer SQL-ähnlichen Sprache, die zum größten Teil eine Teilmenge der Transact-SQL-Standardsyntax ist und über einige spezielle Erweiterungen für zeitliche Abfragen verfügt.

### Windowing
Windowing-Erweiterungen ermöglichen die Durchführung von Aggregationen und Berechnungen für Teilmengen von Ereignissen, die innerhalb einer gewissen Zeitspanne liegen. Windowing-Funktionen werden mit der **GROUP BY**-Anweisung aufgerufen. Die folgende Abfrage zählt beispielsweise die Ereignisse, die pro Sekunde empfangen werden:

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

### Ausführungsschritte
Für komplexere Abfragen kann die SQL-Standardklausel **WITH** verwendet werden, um einen temporär benannten Ergebnissatz anzugeben. Die folgende Abfrage verwendet **WITH** beispielsweise, um eine Transformation mit zwei Ausführungsschritten durchzuführen:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Weitere Informationen zur Abfragesprache finden Sie in der [Azure Stream Analytics-Abfragesprachreferenz](stream.analytics.query.language.reference).

## Ausgabe
Die Ausgabequelle ist der Ort, an den die Ergebnisse des Stream Analytics-Auftrags geschrieben werden. Die Ergebnisse werden kontinuierlich in die Ausgabequelle geschrieben, während der Auftrag Eingabeereignisse verarbeitet. Die folgenden Ausgabequellen werden unterstützt:

- Azure Event Hubs – Wählen Sie Event Hubs als Ausgabequelle für Szenarios aus, in denen mehrere Streaming-Pipelines zusammengeführt werden müssen, z. B. die Rückgabe von Befehlen an Geräte.
- Azure-Blob-Speicher – Verwenden Sie Blob-Speicher für die langfristige Archivierung der Ausgabe oder zum Speichern von Daten für die spätere Verarbeitung.
- Azure-Tabellenspeicher – der Azure-Tabellenspeicher ist ein strukturierter Datenspeicher mit weniger Schemaeinschränkungen. Entitäten mit unterschiedlichen Schemas und Typen können in der gleichen Azure-Tabelle gespeichert werden. Azure-Tabellenspeicher kann zum Speichern von Daten für dauerhafte Archivierung und effizienten Abruf verwendet werden. Weitere Informationen finden Sie unter [Einführung zum Azure-Speicher](storage.introduction.md) und [Entwerfen einer Strategie für die skalierbare Partitionierung des Azure-Tabellenspeichers](https://msdn.microsoft.com/library/azure/hh508997.aspx).
- Azure-SQL-Datenbank – diese Ausgabequelle eignet sich für relationale Daten oder für Anwendungen, die von dem in einer Datenbank gehosteten Inhalt abhängig sind.


## Skalieren von Aufträgen

Ein Stream Analytics-Auftrag kann durch Konfigurieren von Streaming-Einheiten skaliert werden, die die Größe der Verarbeitungsleistung für einen Auftrag bestimmen. Jede Streaming-Einheit entspricht etwa einem Durchsatz von 1 MB/s. Jedes Abonnement verfügt über ein Kontingent von 12 Streaming-Einheiten pro Region für Aufträge in dieser Region.

Einzelheiten finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen](stream.analytics.scale.jobs).


## Überwachung und Problembehandlung von Aufträgen

### Regionales Überwachungskonto für Speicher

Zum Aktivieren der Auftragsüberwachung erfordert Stream Analytics, dass Sie ein Azure-Speicherkonto für die Überwachung von Daten in jeder Region mit Stream Analytics-Aufträgen festlegen. Dies wird zum Zeitpunkt der Auftragserstellung konfiguriert.

### Metriken
Die folgenden Metriken sind für die Überwachung der Nutzung und Leistung von Stream Analytics-Aufträgen verfügbar:

- Fehler – Anzahl von Fehlermeldungen, die durch einen Stream Analytics-Auftrag entstehen.
- Eingangsereignisse – Vom Stream Analytics-Auftrag erhaltene Datenmenge im Hinblick auf die 
-  Ereignisanzahl.
- Ausgangsereignisse – vom Stream Analytics-Auftrag gesendete Datenmenge im Hinblick auf die Ereignisanzahl.
- Ereignisse mit falscher Reihenfolge – Anzahl der Ereignisse, die in falscher Reihenfolge empfangen und anhand der entsprechenden Richtlinie entweder verworfen oder mit einem angepassten Zeitstempel versehen wurden.
- Datenkonvertierungsfehler – Anzahl der Datenkonvertierungsfehler im Zusammenhang mit einem Stream Analytics-Auftrag.

### Vorgangsprotokolle
Das beste Verfahren zum Debuggen oder zur Fehlerbehebung eines Stream Analytics-Auftrags ist die Verwendung von Azure-Vorgangsprotokollen. Vorgangsprotokolle stehen im Bereich **Management Services** des Portals zur Verfügung. Legen Sie zum Überprüfen von Protokollen **Diensttyp** auf **Stream Analytics** und **Dienstname** auf den Namen des Auftrags fest.


## Verwalten von Aufträgen 

### Starten und Beenden von Aufträgen
Beim Starten eines Auftrags werden Sie aufgefordert, einen Wert für **Start Output** einzugeben, der bestimmt, wann dieser Auftrag die Ergebnisausgabe beginnt. Wenn die zugeordnete Abfrage ein Fenster enthält, beginnt der Auftrag mit dem Erfassen von Eingaben aus den Eingabequellen zu Beginn der erforderlichen Fensterdauer, um das erste Ausgabeereignis zum angegebenen Zeitpunkt zu erzeugen. Es sind drei Optionen verfügbar: **Auftragsstartzeit**, **Benutzerdefiniert** und **Last Stopped Time**. Die Standardeinstellung ist **Auftragsstartzeit**. Wenn ein Auftrag vorübergehend angehalten wurde, sollte **Last Stopped Time** als Wert für **Start Output** ausgewählt werden, um den Auftrag vom letzten Ausgabezeitpunkt aus zu starten und Datenverluste zu vermeiden. Für die Option **Benutzerdefiniert** müssen Sie ein Datum und eine Uhrzeit angeben. Diese Einstellung ist nützlich für die Angabe, wie viele historische Daten in den Eingabequellen genutzt werden sollen, oder für die Erfassung der Datenaufnahme von einem bestimmten Zeitpunkt.

### Konfigurieren von Aufträgen
Sie können die folgenden Einstellungen auf der obersten Ebene für einen Stream Analytics-Auftrag anpassen:

- **Start Output** – mit dieser Einstellung können Sie angeben, wann dieser Auftrag mit der Ergebnisausgabe beginnen soll. Wenn die zugeordnete Abfrage ein Fenster enthält, beginnt der Auftrag mit dem Erfassen von Eingaben aus den Eingabequellen zu Beginn der erforderlichen Fensterdauer, um das erste Ausgabeereignis zum angegebenen Zeitpunkt zu erzeugen. Es gibt zwei Optionen: **Auftragsstartzeit** und **Benutzerdefiniert**. Die Standardeinstellung ist **Auftragsstartzeit**. Für die Option **Benutzerdefiniert** müssen Sie ein Datum und eine Uhrzeit angeben. Diese Einstellung ist nützlich für die Angabe, wie viele historische Daten in den Eingabequellen genutzt werden sollen oder für die Erfassung der Datenaufnahme von einem bestimmten Zeitpunkt, z. B. dem letzten Stopp des Auftrags. 
- **Out of order policy** – Einstellungen für den Umgang mit Ereignissen, die nicht in der richtigen Reihenfolge beim Stream Analytics-Auftrag eingehen. Sie können einen Zeitschwellenwert angeben, um die enthaltenen Ereignisse neu zu ordnen, indem Sie ein Toleranzfenster festlegen. Sie können auch eine Aktion bestimmen, um Ereignisse außerhalb dieses Fensters zu bearbeiten: **Verwerfen** oder **Anpassen**. **Löschen** löscht alle Ereignisse, die in der falschen Reihenfolge empfangen wurden, und **Anpassen** bewirkt eine Änderung des Systems. Zeitstempel der in falscher Reihenfolge empfangenen Ereignisse zum Zeitstempel des zuletzt empfangenen sortierten Ereignisses. 
- **Late arrival policy** – beim Lesen von Eingabequellen mit mehreren Partitionen, bei denen mindestens eine Partition verlangsamt ist oder über keine Daten verfügt, muss der Streaming-Auftrag entscheiden, wie mit der Situation umgegangen wird, damit die Ereignisse weiter durch das System fließen. Die Eingabeeinstellung "Maximum allowed arrival delay" steuert dieses Verhalten und wartet in der Standardeinstellung über einen unbegrenzten Zeitraum auf die Daten. Daher werden zwar einerseits die Ereigniszeitstempel nicht verändert, andererseits fließen die Ereignisse jedoch beruhend auf der langsamsten Eingabepartition, wobei der Fluss unterbrochen wird, wenn mindestens eine Eingabepartition über keine Daten verfügt. Dies ist hilfreich, wenn die Daten gleichmäßig auf alle Eingabepartitionen verteilt sind und wenn die zeitliche Konsistenz unter den Ereignissen entscheidend ist. Der Benutzer kann jedoch auch festlegen, dass nur eine begrenzte Zeit gewartet wird. Mit "Maximum allowed arrival delay" wird die Verzögerung angegeben, nach der der Auftrag weitergeleitet wird und die nachhinkenden Partitionen hinter sich lässt. Hierbei wird der Umgang mit verzögerten Ereignissen über die Einstellung "Action for late events" festgelegt, wobei Ereignisse verworfen werden oder deren Zeitstempel angepasst wird, wenn Daten später ankommen. Dies ist hilfreich, wenn die Latenzzeit wichtig und eine Veränderung der Zeitstempel zulässig ist. Die Eingabe wird jedoch möglicherweise nicht gleichmäßig verteilt.
- **Gebietsschema** – Verwenden Sie diese Einstellung, um die Voreinstellung für die Internationalisierung für den Stream Analytics-Auftrag anzugeben. Zeitstempel der Daten sind gebietsschemaneutral, die hier vorgenommenen Einstellungen haben Auswirkungen auf die Analyse, den Vergleich und die Sortierung der Daten. Für die Vorabversion wird nur **en-US** unterstützt.

### Status

Der Status von Stream Analytics-Aufträgen kann im Azure-Portal angezeigt werden. Aufträge können in einem der drei folgenden Status durchgeführt werden: **Leerlauf**, **Wird verarbeitet** oder **Heruntergestuft**. Im Folgenden finden Sie die einzelnen Statusdefinitionen:

- **Leerlauf** – seit dem Erstellen des Auftrags oder in den letzten zwei Minuten wurden keine Eingabebytes erkannt. Wenn ein Auftrag länger den Status **Leerlauf** aufweist, ist es wahrscheinlich, dass zwar die Eingabe, jedoch keine zu verarbeitenden RAW-Bytes vorhanden sind.
- **Processing** – eine Menge gefilterter Eingabeereignisse, die nicht Null ist, wurde vom Stream Analytics-Auftrag erfolgreich verarbeitet. Wenn ein Auftrag im Status **Verarbeitung** festhängt, ohne eine Ausgabe zu erzeugen, ist es wahrscheinlich, dass das Zeitfenster für die Verarbeitung groß oder die Abfragelogik kompliziert ist.
- **Heruntergestuft** – dieser Status gibt an, dass bei einem Stream Analytics-Auftrag einer der folgenden Fehler vorliegt: Eingabe/Ausgabe-Kommunikationsfehler, Abfragefehler oder wiederholbare Laufzeitfehler. Um die Art der Fehler für Aufträge zu ermitteln, zeigen Sie die Vorgangsprotokolle an.


## Support
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Nächste Schritte

Nachdem Sie nun mit den Schlüsselkonzepten von Stream Analytics vertraut sind, können Sie Folgendes versuchen:

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)





<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=58-->