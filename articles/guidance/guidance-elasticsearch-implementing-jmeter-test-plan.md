<properties
   pageTitle="Implementieren eines JMeter-Testplans für Elasticsearch | Microsoft Azure"
   description="Durchführen von Leistungstests für Elasticsearch mit JMeter."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Implementieren eines JMeter-Testplans für Elasticsearch

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch.md).

Die anhand von Elasticsearch durchgeführten Leistungstests wurden mithilfe von JMeter-Testplänen zusammen mit integriertem Java-Code als JUnit-Test implementiert, um Aufgaben wie das Hochladen von Daten in den Cluster durchzuführen. Die Testpläne und der JUnit-Code werden unter [Optimierung der Datenerfassungsleistung für Elasticsearch in Azure][] und [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure][] (Optimierung der Datenaggregations- und Abfrageleistung für Elasticsearch in Azure) beschrieben.

In diesem Dokument werden die wichtigsten Erfahrungen zusammengefasst, die beim Erstellen und Ausführen dieser Testpläne gewonnen wurden. Die Seite [JMeter Best Practices](http://jmeter.apache.org/usermanual/best-practices.html) auf der Apache JMeter-Website enthält allgemeinere Ratschläge zur effektiven Verwendung von JMeter.

## Implementieren eines JMeter-Testplans

Die folgende Liste enthält die Elemente, die Sie beim Erstellen eines JMeter-Testplans berücksichtigen sollten:

- Erstellen Sie eine separate Threadgruppe für jeden Test, den Sie durchführen möchten. Ein Test kann mehrere Schritte umfassen, die logische Controller, Zeitgeber, Pre- und Postprozessoren, Sampler und Listener beinhalten.

- Vermeiden Sie zu viele Threads in einer Threadgruppe. Eine übermäßige Anzahl von Threads führt dazu, dass JMeter aufgrund von „Nicht genügend Arbeitsspeicher“-Ausnahmen einen Fehler verursacht. Es ist besser, mehr untergeordnete JMeter-Server mit jeweils einer kleineren Anzahl von Threads hinzuzufügen, als zu versuchen, eine große Anzahl von Threads auf einem einzigen JMeter-Server auszuführen.

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- Um die Leistung des Clusters zu bewerten, integrieren Sie das Plug-In [Perfmon Metrics Collector](http://jmeter-plugins.org/wiki/PerfMon/) in den Testplan. Hierbei handelt es sich um einen JMeter-Listener, der als eines der JMeter-Standard-Plug-Ins verfügbar ist. Speichern Sie die unformatierten Leistungsdaten in einem Satz von Dateien im CSV-Format, und verarbeiten Sie sie, wenn der Test abgeschlossen ist. Dies ist effizienter und stellt eine geringere Belastung für JMeter dar als der Versuch, die Daten gleich während der Erfassung zu verarbeiten. 

Mit einem Tool wie Excel können Sie die Daten importieren und zu Analysezwecken eine Reihe von Diagrammen generieren.

![](./media/guidance-elasticsearch/jmeter-testing2.png)

Sie sollten die folgende Informationen erfassen:

- Die CPU-Auslastung für jeden Knoten im Elasticsearch-Cluster.

- Die Anzahl der pro Sekunde vom Datenträger gelesenen Bytes für jeden Knoten.

- Wenn möglich, den Prozentsatz der CPU-Zeit, die mit Warten auf das Durchführen von E/A auf jedem Knoten verbracht wird. Dies ist für virtuelle Windows-Computer nicht immer möglich, aber für Linux können Sie eine benutzerdefinierte Metrik (eine EXEC-Metrik) erstellen, die den folgenden Shellbefehl zum Aufrufen von *vmstat* auf einem Knoten ausführt:

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

Feld 16 in der Ausgabe von *vmstat* enthält die CPU-Wartezeit für E/A. Weitere Informationen zur Funktionsweise dieser Anweisung finden Sie unter dem [Befehl „vmstat“](http://linuxcommand.org/man_pages/vmstat8.html).

- Die Anzahl von Bytes, die für jeden Knoten über das Netzwerk gesendet und empfangen wird.

- Verwenden Sie separate Aggregatberichtslistener, um die Leistung und die Häufigkeit der erfolgreichen und der fehlerhaften Vorgänge aufzuzeichnen. Erfassen Sie Erfolgs- und Fehlerdaten in verschiedenen Dateien.

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- Halten Sie jeden JMeter-Testfall so einfach wie möglich, damit Sie die Leistung direkt mit bestimmten Testaktionen in Bezug setzen können. Für Testfälle, die komplexe Logik benötigen, sollten Sie diese Logik in einem JUnit-Test kapseln und den JUnit Request-Sampler in JMeter zum Ausführen des Tests verwenden.

- Verwenden Sie den HTTP Request-Sampler zum Durchführen von HTTP-Vorgängen wie GET, POST, PUT oder DELETE. Sie können z. B. Elasticsearch-Suchvorgänge mithilfe einer POST-Abfrage ausführen und die Abfragedetails im Feld *Body Data* angeben:

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- Für einfache Wiederholbarkeit und Wiederverwendung versehen Sie JMeter-Testpläne mit Parametern. Anschließend können Sie die Ausführung von Testplänen mit Skripts automatisieren.

## Implementieren einen JUnit-Tests

Sie können komplexen Code in einen JMeter-Testplan integrieren, indem Sie einen oder mehrere JUnit-Tests erstellen. Sie können einen JUnit-Test mit einer Java-IDE wie Eclipse schreiben. Der Artikel [Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Leistung von Elasticsearch][] enthält Informationen zum Einrichten einer geeigneten Entwicklungsumgebung.

In der folgende Liste werden einige bewährte Methoden zusammengefasst, die Sie beim Schreiben des Codes für einen JUnit-Tests befolgen sollten:

- Verwenden Sie den Testklassenkonstruktor, um Initialisierungsparameter an den Test zu übergeben. JMeter kann einen Konstruktor verwenden, der ein einziges Zeichenfolgenargument akzeptiert. Im Konstruktor wird dieses Argument bis in die einzelnen Elemente analysiert, wie im folgenden Codebeispiel gezeigt:

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticsearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- Vermeiden Sie kostspielige Vorgänge oder E/A-Vorgänge in der Konstruktor- oder in der Setuptestklasse, da sie bei jeder Ausführung des JUnit-Tests ausgeführt werden. (Der gleiche JUnit-Test kann für jeden von JMeter aus ausgeführten Leistungstest mehrere tausend Mal ausgeführt werden).

- Erwägen Sie ein einmaliges Setup für die Initialisierung kostspieliger Testfälle.

- Wenn für den Test eine große Anzahl von Eingabeparametern erforderlich ist, speichern Sie Testkonfigurationsinformationen in einer separaten Konfigurationsdatei, und übergeben Sie den Speicherort dieser Datei an den Konstruktor.

- Vermeiden Sie die Hartcodierung von Dateipfaden im Code des Auslastungstests. Diese können aufgrund der Unterschiede zwischen Betriebssystemen wie Windows und Linux zu Fehlern führen.

- Verwenden Sie Assertionen zur Anzeige von Fehlern in JUnit-Testmethoden, damit Sie sie mit JMeter nachverfolgen und als Geschäftsmetrik verwenden können. Geben Sie nach Möglichkeit Informationen zur Ursache des Fehlers zurück (im folgenden Codebeispiel fett formatiert):

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Optimierung der Datenerfassungsleistung für Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Leistung von Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md

<!---HONumber=AcomDC_0224_2016-->