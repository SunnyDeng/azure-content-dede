<properties
	pageTitle="Skalieren von Stream Analytics-Aufträgen | Azure"
	description="Erfahren Sie mehr über die Skalierung von Stream Analytics-Aufträgen."
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
	ms.date="04/28/2015"
	ms.author="jeffstok"/>

# Skalieren von Azure Stream Analytics-Aufträgen

Erfahren Sie, wie Sie *Streaming-Einheiten* für einen Stream Analytics-Auftrag berechnen und Stream Analytics-Aufträge durch Konfiguration von Eingabepartitionen, Optimierung der Abfragedefinition und Einstellung von Auftrags-Streaming-Einheiten skalieren.

Eine Azure Stream Analytics-Auftragsdefinition umfasst Eingaben, Abfrage und Ausgabe. Bei der Eingabe liest der Auftrag den eingehenden Datenstrom, bei der Ausgabe sendet der Auftrag die Auftragsergebnisse, und bei der Abfrage wird der Eingabestrom umgewandelt.

Für einen Auftrag wird mindestens eine Datenstrom-Eingabequelle benötigt. Die Datenstrom-Eingabequelle kann entweder ein Azure Service Bus Event Hub oder ein Azure-BLOB-Speicher sein. Weitere Informationen finden Sie unter [Einführung in Azure Stream Analytics](stream-analytics-introduction.md), [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md) und [Azure Stream Analytics-Entwicklerhandbuch](../stream-analytics-developer-guide.md).

Die für die Verarbeitung von Stream Analytics-Aufträgen verfügbare Ressource wird durch eine Streaming-Einheit gemessen. Jede Streaming-Einheit kann einen Durchsatz von bis zu 1 MB/Sekunde bieten. Für jeden Auftrag wird mindestens eine Streaming-Einheit benötigt. Dies ist der Standardwert für sämtliche Aufträge. Über das Azure-Verwaltungsportal können Sie für einen Stream Analytics-Auftrag bis zu 50 Streaming-Einheiten einrichten. Für jedes Azure-Abonnement kann es höchstens 50 Streaming-Einheiten für alle Aufträge in einer bestimmten Region geben. Wenn Sie die Streaming-Einheiten für Ihr Abonnement auf bis zu 100 Einheiten erhöhen möchten, wenden Sie sich an den [Microsoft Support](http://support.microsoft.com).

Die Anzahl an Streaming-Einheiten, die ein Auftrag verwenden kann, hängt von der Partitionskonfiguration der Eingaben und der für den Auftrag definierten Abfrage ab. In diesem Artikel sehen Sie, wie Sie die Abfrage so berechnen und optimieren, dass der Durchsatz erhöht wird.


## Berechnen der maximal möglichen Streaming-Einheiten für einen Auftrag
Die Gesamtzahl der von einem Stream Analytics-Auftrag verwendbaren Streaming-Einheiten hängt von der Anzahl an Schritten in der für den Auftrag definierten Abfrage und der Anzahl an Partitionen für die einzelnen Schritte ab.

### Schritte einer Abfrage
Eine Abfrage kann einen oder mehrere Schritte umfassen. Jeder Schritt besteht aus einer Unterabfrage, die anhand des WITH-Schlüsselworts definiert ist. Die einzige Abfrage, die sich außerhalb des WITH-Schlüsselworts befindet, wird ebenfalls als Schritt gezählt, beispielsweise die SELECT-Anweisung in der folgenden Abfrage:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId, PartitionId

Die vorherige Abfrage umfasst zwei Schritte.

> [AZURE.NOTE]Diese Beispielabfrage wird im Laufe des Artikels erläutert.

### Partitionieren eines Schritts

Für die Partitionierung eines Schrittes gelten die folgenden Voraussetzungen:

- Die Eingabequelle muss partitioniert sein. Siehe [Azure Stream Analytics-Entwicklerhandbuch](../stream-analytics-developer-guide.md) und [Azure Event Hubs-Entwicklerhandbuch](../azure-event-hubs-developer-guide.md).
- Die SELECT-Anweisung der Abfrage muss aus einer partitionierten Eingabequelle lesen.
- Die Abfrage im Schritt muss das Schlüsselwort **Partition By** aufweisen.

Wenn eine Abfrage partitioniert ist, werden die Eingabeereignisse verarbeitet und in separaten Partitionsgruppen aggregiert, und für jede einzelne Gruppe werden Ausgabeereignisse generiert. Wenn ein kombiniertes Aggregat erwünscht ist, müssen Sie einen zweiten, nicht partitionierten Schritt zum Aggregieren erstellen.

### Berechnen der maximal möglichen Streaming-Einheiten für einen Auftrag

Alle nicht partitionierten Schritte zusammen können bis zu sechs Streaming-Einheiten für einen Stream Analytics-Auftrag skalieren. Um zusätzliche Streaming-Einheiten hinzuzufügen, muss ein Schritt partitioniert werden. Jede Partition kann über sechs Streaming-Einheiten verfügen.

<table border="1">
<tr><th>Abfrage eines Auftrags</th><th>Maximal mögliche Streaming-Einheiten für den Auftrag</th></td>

<tr><td>
<ul>
<li>Die Abfrage umfasst einen Schritt.</li>
<li>Der Schritt ist nicht partitioniert.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Der Eingabedatenstrom ist in 3 partitioniert.</li>
<li>Die Abfrage umfasst einen Schritt.</li>
<li>Der Schritt ist partitioniert.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>Die Abfrage umfasst zwei Schritte.</li>
<li>Keiner der Schritte ist partitioniert.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>Die Datenstromeingabe ist in 3 partitioniert.</li>
<li>Die Abfrage umfasst zwei Schritte. Der Eingabeschritt ist partitioniert, nicht jedoch der zweite.</li>
<li>Die SELECT-Anweisung liest aus der partitionierten Eingabe.</li>
</ul>
</td>
<td>24 (18 für partitionierte Schritte + 6 für nicht partitionierte Schritte)</td></tr>
</table>

### Beispiel für Skalierung
Mit der folgenden Abfrage wird die Anzahl der Fahrzeuge berechnet, die innerhalb von drei Minuten eine gebührenpflichtige Station mit drei Mautstationen passieren. Diese Abfrage kann auf bis zu sechs Streaming-Einheiten skaliert werden.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Um weitere Streaming-Einheiten für die Abfrage zu verwenden, müssen sowohl die Datenstromeingabe als auch die Abfrage partitioniert werden. Da die Datenstrompartition auf "3" festgelegt ist, kann die folgende geänderte Abfrage auf bis zu 18 Streaming-Einheiten skaliert werden:

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Wenn eine Abfrage partitioniert ist, werden die Eingabeereignisse verarbeitet und in separaten Partitionsgruppen aggregiert. Zudem werden für die einzelnen Gruppen Ausgabeereignisse generiert. Das Partitionieren kann unerwartete Ergebnisse erbringen, wenn der Partitionsschlüssel in der Datenstromeingabe nicht das Feld **Group-by** ist. Beispielsweise ist das Feld "TollBoothId" in der vorherigen Beispielabfrage nicht der Partitionsschlüssel von Input1. Die Daten aus der Mautstation 1 können auf mehrere Partitionen verteilt werden.

Jede Input1-Partition wird separat von Stream Analytics verarbeitet, und es werden mehrere Datensätze für die Anzahl der passierenden Autos für dieselbe Mautstation im selben rollierenden Fenster erstellt. Für den Fall, dass der Eingabepartitionsschlüssel nicht geändert werden kann, kann dieses Problem durch Hinzufügen eines zusätzlichen, nicht partitionierten Schritts behoben werden, z. B.:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, ParititonId

Diese Abfrage kann auf bis zu 24 Streaming-Einheiten skaliert werden.

>[AZURE.NOTE]Wenn Sie zwei Datenströmen verknüpfen, stellen Sie sicher, dass die Ströme anhand des Partitionsschlüssels der Spalte partitioniert werden, in der Sie die Verknüpfungen vornehmen, und in beiden Strömen dieselbe Anzahl an Partitionen vorhanden ist.


## Konfigurieren der Stream Analytics-Auftragspartition

**So passen Sie die Streaming-Einheit für einen Auftrag an**

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie links auf **Stream Analytics**.
3. Klicken Sie auf den Stream Analytics-Auftrag, den Sie skalieren möchten.
4. Klicken Sie oben auf der Seite auf **SCALE** (Skalieren).

![Konfigurieren der Auftragsskalierung in Azure Stream Analytics][img.stream.analytics.configure.scale]


## Überwachen der Auftragsleistung

Über das Verwaltungsportal können Sie den Durchsatz eines Auftrags in Ereignissen/Sekunde nachverfolgen:

![Überwachen von Aufträgen in Azure Stream Analytics][img.stream.analytics.monitor.job]

Berechnen Sie den erwarteten Durchsatz der Arbeitsauslastung in Ereignissen/Sekunde. Für den Fall, dass der Durchsatz kleiner als erwartet ist, optimieren Sie die Eingabepartition und die Abfrage, und fügen Sie dem Auftrag zusätzliche Streaming-Einheiten hinzu.

## Skalierter ASA-Durchsatz – Raspberry Pi-Szenario


Um zu verstehen, wie ASA in einem typischen Szenario die Verarbeitung des Durchsatzes über mehrere Streaming-Einheiten skaliert, führen wir hier ein Experiment an, bei dem die Sensordaten (Clients) an einen Event Hub gesendet und von ASA verarbeitet werden, um als Ausgabe Warnungen oder Statistiken an einen anderen Event Hub zu senden.

Der Client sendet neu bestimmte Sensordaten im JSON-Format an Event Hubs und ASA. Auch die Datenausgabe erfolgt im JSON-Format. Hier wird dargestellt, wie die Beispieldaten ungefähr aussehen würden.

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Abfrage: "Warnung senden, wenn das Licht ausgeschaltet wird"

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

Messen des Durchsatzes: Beim Durchsatz handelt es sich in diesem Kontext um die Menge der von ASA über einen festen Zeitraum (10 Minuten) verarbeiteten Eingabedaten. Um einen optimalen Verarbeitungsdurchsatz für die Eingabedaten zu erzielen, müssen sowohl die Datenstromeingabe als auch die Abfrage partitioniert werden. Zudem umfasst die Abfrage "COUNT()", um zu messen, wie viele Eingabeereignisse verarbeitet wurden. Um sicherzustellen, dass ASA nicht einfach auf eingehende Eingabeereignisse wartet, wurden die einzelnen Eingabe-Event Hubs vorab mit ausreichend Eingabedaten (ca. 300 MB) geladen.

Im Folgenden finden Sie die Ergebnisse mit einer wachsenden Anzahl an Streaming-Einheiten sowie der entsprechenden Partitionsmenge in den Event Hubs.

<table border="1">
<tr><th>Eingabepartitionen</th><th>Ausgabepartitionen</th><th>Streaming-Einheiten</th><th>Anhaltender Durchsatz
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06&#160;MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06&#160;MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32&#160;MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67&#160;MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27&#160;MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69&#160;MB/s</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54--> 