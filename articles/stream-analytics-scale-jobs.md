<properties 
	pageTitle="Skalieren von Stream Analytics-Aufträgen | Azure" 
	description="Erfahren Sie mehr über die Skalierung von Stream Analytics-Aufträgen."
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
	ms.date="2/10/2015" 
	ms.author="jgao"/>

# Skalieren von Azure Stream Analytics-Aufträgen 

Erfahren Sie, wie Sie *Streaming-Einheiten* für einen Stream Analytics-Auftrag berechnen und Stream Analytics-Aufträge durch Konfiguration von Eingabepartitionen, Optimierung der Abfragedefinition und Einstellung von Auftrags-Streaming-Einheiten skalieren.

Eine Azure Stream Analytics-Auftragsdefinition umfasst Eingaben, Abfrage und Ausgabe. Bei der Eingabe liest der Auftrag den eingehenden Datenstrom, bei der Ausgabe sendet der Auftrag die Auftragsergebnisse und bei der Abfrage wird der Eingabestrom umgewandelt.  Für einen Auftrag wird mindestens eine Datenstrom-Eingabequelle benötigt. Die Datenstrom-Eingabequelle kann entweder ein Azure Service Bus Event Hub oder ein Azure-Blobspeicher sein. Weitere Informationen finden Sie unter [Einführung in Azure Stream Analytics][stream.analytics.introduction], [Erste Schritte mit Azure Stream Analytics][stream.analytics.get.started] und [Azure Stream Analytics-Entwicklerhandbuch][stream.analytics.developer.guide]. 

Die für die Verarbeitung von Stream Analytics-Aufträgen verfügbare Ressource wird durch eine Streaming-Einheit gemessen. Jede Streaming-Einheit kann einen Durchsatz von bis zu 1 MB/Sekunde bieten. Für jeden Auftrag wird mindestens eine Streaming-Einheit benötigt. Dies ist der Standardwert für sämtliche Aufträge. Über das Azure-Verwaltungsportal können Sie für einen Stream Analytics-Auftrag bis zu 12 Streaming-Einheiten einrichten. Für jedes Azure-Abonnement kann es höchstens 12 Streaming-Einheiten für alle Aufträge in einer bestimmten Region geben. Wenn Sie die Streaming-Einheiten für Ihr Abonnement auf bis zu 100 Einheiten erhöhen möchten, wenden Sie sich an den [Microsoft Support][microsoft.support].

Die Anzahl an Streaming-Einheiten, die ein Auftrag verwenden kann, hängt von der Partitionskonfiguration der Eingaben und der für den Auftrag definierten Abfrage ab. In diesem Artikel sehen Sie, wie Sie die Abfrage so berechnen und optimieren, dass der Durchsatz erhöht wird.

##Themen in diesem Artikel
+ [Berechnen der maximal möglichen Streaming-Einheiten für einen Auftrag](#calculate)
+ [Konfigurieren der Stream Analytics-Auftragspartition](#configure)
+ [Überwachen der Stream Analytics-Auftragsleistung](#monitor)
+ [Nächste Schritte](#nextstep)


##<a name="calculate"></a>Berechnen der maximal möglichen Streaming-Einheiten für einen Auftrag
Die Gesamtzahl der von einem Stream Analytics-Auftrag verwendbaren Streaming-Einheiten hängt von der Anzahl an Schritten in der für den Auftrag definierten Abfrage und der Anzahl an Partitionen für die einzelnen Schritte ab.

### Schritte einer Abfrage
Eine Abfrage kann einen oder mehrere Schritte umfassen. Jeder Schritt besteht aus einer Unterabfrage, die anhand des WITH-Schlüsselworts definiert ist. Die einzige Abfrage, die sich außerhalb des WITH-Schlüsselworts befindet, wird ebenfalls als Schritt gezählt. Beispielsweise die SELECT-Anweisung in der folgenden Abfrage:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

Die vorherige Abfrage umfasst zwei Schritte. 

> [WACOM.NOTE] Diese Beispielabfrage wird im Laufe des Artikels erläutert.

### Partitionieren eines Schritts

Für die Partitionierung eines Schrittes gelten die folgenden Voraussetzungen:

- Die Eingabequelle muss partitioniert sein. Siehe [Azure Stream Analytics-Entwicklerhandbuch][stream.analytics.developer.guide] und [Azure Event Hubs-Entwicklerhandbuch][azure.event.hubs.developer.guide].
- Die SELECT-Anweisung der Abfrage muss aus einer partitionierten Eingabequelle lesen. 
- Die Abfrage im Schritt muss das Schlüsselwort "Partition By" aufweisen. 

Wenn eine Abfrage partitioniert ist, werden die Eingabeereignisse verarbeitet und in separaten Partitionsgruppen aggregiert, und für jede einzelne Gruppe werden Ausgabeereignisse generiert. Wenn ein kombiniertes Aggregat erwünscht ist, müssen Sie einen zweiten, nicht partitionierten Schritt zum Aggregieren erstellen.

Die Vorschauversion von Azure Stream Analytics unterstützt keine Partitionierung nach Spaltennamen. Sie können lediglich nach dem in die Abfrage integrierten Feld "PartitionId" partitionieren. Das Feld "ParitionId" gibt an, aus welcher Partition des Quelldatenstroms das Ereignis stammt.  Einzelheiten finden Sie unter [Einschränkungen und bekannte Probleme von Azure Stream Analytics ][stream.analytics.limitations].

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
<li>Die Abfrage umfasst zwei Schritte.</li>
<li>Die SELECT-Anweisung liest aus der partitionierten Eingabe.</li>
</ul>
</td>
<td>24 (18 für partitionierten Schritt + 6 für nicht partitionierten Schritt)</td></tr>
</table>

###Beispiel für Skalierung
Mit der folgenden Abfrage wird die Anzahl der Fahrzeuge berechnet, die innerhalb von 3 Minuten eine gebührenpflichtige Station mit drei Mautstationen passieren. Diese Abfrage kann auf bis zu 6 Streaming-Einheiten skaliert werden.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Um weitere Streaming-Einheiten für die Abfrage zu verwenden, müssen sowohl die Datenstromeingabe als auch die Abfrage partitioniert werden. Da die Datenstrompartition auf 3 festgelegt ist, kann die folgende geänderte Abfrage auf bis zu 18 Streaming-Einheiten skaliert werden.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Wenn eine Abfrage partitioniert ist, werden die Eingabeereignisse verarbeitet und in separaten Partitionsgruppen aggregiert, und für jede einzelne Gruppe werden zudem Ausgabeereignisse generiert. Das Partitionieren kann unerwartete Ergebnisse erbringen, wenn der Partitionsschlüssel in der Datenstromeingabe nicht das Feld "Group-by" ist. Beispielsweise ist das Feld "TollBoothId" in der vorherigen Beispielabfrage nicht der Partitionsschlüssel von Input1. Die Daten aus der Mautstation 1 können auf mehrere Partitionen verteilt werden. Jede Input1-Partition wird separat von Stream Analytics verarbeitet, und es werden mehrere Datensätze für die Anzahl der passierenden Autos für dieselbe Mautstation im selben rollierenden Fenster erstellt. Für den Fall, dass der Eingabepartitionsschlüssel nicht geändert werden kann, kann dieses Problem durch Hinzufügen eines zusätzlichen, nicht partitionierten Schritts behoben werden. Beispiel:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Diese Abfrage kann auf bis zu 24 Streaming-Einheiten skaliert werden. 

>[WACOM.NOTE] Wenn Sie zwei Datenströmen verknüpfen, stellen Sie sicher, dass die Ströme anhand des Partitionsschlüssels der Spalte partitioniert werden, in der Sie die Verknüpfungen vornehmen, und in beiden Strömen dieselbe Anzahl an Partitionen vorhanden ist.


##<a name="configure"></a>Konfigurieren der Stream Analytics-Auftragspartition

**So passen Sie Streaming-Einheiten für einen Auftrag an**

1. Melden Sie sich beim [Verwaltungsportal][azure.management.portal] an.
2. Klicken Sie links auf **Stream Analytics**.
3. Klicken Sie auf den Stream Analytics-Auftrag, den Sie skalieren möchten.
4. Klicken Sie oben auf der Seite auf **SCALE** (Skalieren).

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>Überwachen der Auftragsleistung

Über das Verwaltungsportal können Sie den Durchsatz eines Auftrags in Ereignissen/Sekunde nachverfolgen:

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
Berechnen Sie den erwarteten Durchsatz der Arbeitsauslastung in Ereignissen/Sekunde. Für den Fall, dass der Durchsatz kleiner als erwartet ist, optimieren Sie die Eingabepartition, optimieren Sie die Abfrage und fügen Sie dem Auftrag zusätzliche Streaming-Einheiten hinzu.

##<a name="nextstep"></a> Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie Streaming-Einheiten berechnen und einen Stream Analytics-Auftrag skalieren. Weitere Informationen zu Stream Analytics finden Sie hier:

- [Einführung in Azure Stream Analytics][stream.analytics.introduction]
- [Erste Schritte mit Azure Stream Analytics][stream.analytics.get.started]
- [Azure Stream Analytics-Entwicklerhandbuch][stream.analytics.developer.guide]
- [Einschränkungen und bekannte Probleme von Azure Stream Analytics][stream.analytics.limitations]
- [Azure Stream Analytics-Abfragesprachreferenz][stream.analytics.query.language.reference]
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301



<!--HONumber=46--> 
