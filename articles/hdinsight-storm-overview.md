<properties 
	pageTitle="Informationen zu Apache Storm in HDInsight (Hadoop)" 
	description="Erfahren Sie, wie Sie Apache Storm in HDInsight (Hadoop) verwenden können" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Apache Storm in HDInsight: Übersicht

## Was ist Storm?

[Apache Storm][apachestorm] ist ein verteiltes, fehlertolerantes, Open-Source-Berechnungssystem für die Verarbeitung von Daten in Echtzeit. Storm-Lösungen sind außerdem in der Lage, die Verarbeitung von Daten zu garantieren und Daten erneut abzuspielen, die beim ersten Versuch nicht erfolgreich verarbeitet wurden.

## Was ist Storm in HDInsight?

Storm ist als verwalteter Cluster in die Azure-Umgebung integriert und kann als Teil einer größeren Azure-Lösung verwendet werden. Storm kann z. B. Daten von Diensten wie Servicebus-Warteschlangen oder Ereignis-Hubs konsumieren und die Daten in Websites oder Cloud-Diensten visuell aufbereiten. Storm-Cluster können außerdem in einem virtuellen Azure-Netzwerk konfiguriert werden, um die Latenz bei der Kommunikation mit anderen Ressourcen im gleichen Netzwerk zu reduzieren und um auf sichere Weise mit anderen Ressourcen in einem privaten Rechenzentrum zu kommunizieren.

Eine Einführung zu Storm finden Sie unter [Erste Schritte mit Storm in HDInsight][gettingstarted].

## Wie werden die Daten in Storm in HDInsight verarbeitet?

Storm-Cluster verarbeiten **Topologien** anstelle der MapReduce-Aufträge, die Sie möglicherweise aus HDInsight oder Hadoop kennen. Storm-Cluster enthalten zwei Arten von Knoten: Hauptknoten, auf denen **Nimbus** ausgeführt wird, und Workerknoten, auf denen **Supervisor** ausgeführt wird.

* **Nimbus** ähnelt dem JobTracker in Hadoop und verteilt den Code über das Cluster, weist Aufgaben an Computer zu und überwacht das System auf Fehler. HDInsight stellt zwei Nimbus-Knoten bereit, um einzelne Fehlerquellen für Storm-Cluster auszuschließen

* **Supervisor** - Die Supervisor der einzelnen Workerknoten sind dafür verantwortlich, die **Arbeitsprozesse** im Knoten zu starten und zu beenden.

* **Arbeitsprozess** - Ein Arbeitsprozess führt eine Teilmenge einer **Topologie** aus. Eine laufende Topologie ist über viele Arbeitsprozesse im gesamten Cluster verteilt.

* **Topologie** definiert einen Berechnungsgraphen zur Verarbeitung von **Datenströmen**. Im Gegensatz zu MapReduce-Jobs laufen Topologien so lange, bis sie beendet werden

* **Datenstrom** - eine ungebundene Sammlung von **Tupeln**. Datenströme werden von **Spouts** und **Bolts** generiert und von **Bolts** konsumiert.

* **Tupel** - eine benannte Liste dynamisch typisierter Werte

* **Spout** - Konsumiert Daten aus einer Datenquelle und gibt einen oder mehrere **Datenströme** aus.

	> [AZURE.NOTE] Oft werden die Daten aus einer Warteschlange wie z. B. Kafka, Azure ServiceBus-Warteschlangen oder Ereignis-Hubs gelesen. Die Warteschlange sorgt dafür, dass die Daten bei einem Ausfall in persistenter Form vorliegen.

* **Bolt** - Konsumiert **Datenströme**, verarbeitet **Tupel** und kann **Datenströme** ausgeben. Bolt sind außerdem dafür verantwortlich, Daten in externe Speicher zu schreiben, z. B. eine Warteschlange, HDInsight HBase, Blobs oder andere Datenspeicher

* **Thrift** - Apache Thrift ist ein Software-Framework für die Entwicklung skalierbarer, sprachenübergreifender Dienste. Mit Thrift können Sie Dienste entwickeln, die zwischen C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk und anderen Sprachen funktionieren.

	* **Nimbus** ist ein Thrift-Dienst, und eine **Topologie** ist eine Thrift-Definition. Aus diesem Grund ist es möglich, Topologien in einer Vielzahl von Programmiersprachen zu entwickeln. 

Weitere Informationen zu Storm-Komponenten finden Sie im [Storm-Lernprogramm][apachetutorial] auf apache.org.

## Szenarien: Was sind die typischen Anwendungsfälle für Storm?

Hier finden Sie eine Liste der typischen Szenarien, in denen Apache Storm eingesetzt wird. Weitere Informationen zu realen Szenarien finden Sie unter [Companies Using Apache Storm][poweredby] (Unternehmen, die Apache Storm verwenden, in englischer Sprache).

### Echtzeit-Analyse

Storm verarbeitet Datenströme in Echtzeit und eignet sich daher perfekt für Datenanalysen, bei denen direkt beim Eingang der Datenströme nach bestimmten Ereignissen oder Mustern gesucht und auf deren Auftreten reagiert wird. Eine Storm-Topologie kann z. B. Sensordaten überwachen, um die Systemintegrität zu ermitteln, und Benutzer anhand von SMS-Nachrichten warnen, wenn ein bestimmtes Muster auftritt.

### Extraktions-, Umwandlungs- und Ladeprozesse (Extract, Transform and Load, ETL)

ETL ist beinahe ein Nebeneffekt der Storm-Verarbeitung. Wenn Sie z. B. eine Betrugserkennung anhand einer Echtzeit-Analyse durchführen, werden die Daten in Ihrem System bereits erfasst und transformiert. Sie können die Daten außerdem über einen Bolt in HBase, Hive oder einem anderen Datenspeicher für die zukünftige Nutzung speichern.

### Verteiltes RPC

Verteiltes RPC ist ein Muster, das mit Storm implementiert werden kann. Eine Anfrage wird an Storm übergeben, das die Berechnung anschließend über mehrere Knoten verteilt und zuletzt einen Stream mit dem Ergebnis an den wartenden Client zurückgibt.

Weitere Informationen zu verteiltem RPC und dem mit Storm ausgelieferten DRPCClient finden Sie unter [Distributed RPC](https://storm.incubator.apache.org/documentation/Distributed-RPC.html) (Verteiltes RPC, in englischer Sprache).

### Maschinelles Lernen online

Storm kann mit Lösungen für maschinelles Lernen verwendet werden, die zuvor per Stapelverarbeitung trainiert wurden, z. B. auf Mahout basierende Lösungen. Das generische, verteilte Berechnungsmodell eignet sich jedoch ebenso für streambasierte Lösungen für maschinelles Lernen. Ein Beispiel ist das [Scalable Advanced Massive Online Analysis (SAMOA)-Projekt][samoa], eine Bibliothek für maschinelles Lernen mit Streamverarbeitung, die mit Storm kompatibel ist.

## Welche Programmiersprachen kann ich verwenden?

Der HDInsight Storm-Cluster unterstützt standardmäßig .NET, Java und Python. Storm [unterstützt zwar weitere Sprachen](https://storm.incubator.apache.org/about/multi-language.html), allerdings muss für deren Verwendung neben weiteren Konfigurationsänderungen eine zusätzliche Programmiersprache im HDInsight-Cluster installiert werden. 

### .NET

SCP ist ein Projekt, mit dem .NET-Entwickler Topologien (inklusive Spouts und Bolts) entwickeln und implementieren können. Storm-Cluster unterstützen SCP standardmäßig.

Weitere Informationen zur Entwicklung mit SCP finden Sie unter [Entwickeln von Anwendungen zur Verarbeitung von Datenströmen in C# mit Stream Computing Platform und Storm in HDInsight](/de-de/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application).

### Java

Bei den meisten Java-Beispielen handelt es sich entweder um reines Java oder um Trident. Trident ist eine Abstraktion auf einer hohen Ebene zur Erleichterung von Aufgaben wie z. B. Joins, Aggregation, Gruppierung und Filterung. Trident arbeitet jedoch mit Stapeln von Tupeln, wohingegen Streams in Java-Lösungen tupelweise verarbeitet werden.

Weitere Informationen zu Trident finden Sie im [Trident-Lernprogramm](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) auf apache.org.

Beispiele für reine Java- und Trident-Topologien finden Sie im Verzeichnis **%storm_home%\contrib\storm-starter** in Ihrem Storm-Cluster.

## Welche Entwicklungsmuster werden häufig verwendet?

### Garantierte Nachrichtenverarbeitung

Mit Storm lassen sich verschiedene Stufen der garantierten Nachrichtenverarbeitung implementieren. Eine einfache Storm-Anwendung kann z. B. die Verarbeitung nach dem "At-Least-Once"-Prinzip garantieren, während Trident Nachrichten nach dem "Exactly-Once"-Prinzip garantiert.

Weitere Informationen finden Sie im Trident-Lernprogramm unter [Guarantees data processing](https://storm.apache.org/about/guarantees-data-processing.html) (Garantierte Datenverarbeitung, in englischer Sprache) auf apache.org

### BasicBolt

Bei diesem Muster wird ein Eingangstupel gelesen, null oder mehr Tupel ausgegeben und für das Eingangstupel direkt am Ende der Verarbeitungsmethode ein Ack durchgeführt. Dieses Muster wird so häufig verwendet, dass Storm eine [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html)-Schnittstelle zur Automatisierung bietet.

### Joins

Joins zwischen zwei Datenströmen unterscheiden sich je nach Anwendung. Sie können z. B. alle Tupel aus mehreren Streams in einen neuen Stream zusammenführen, oder auch nur Stapel von Tupeln für ein bestimmtes Intervall zusammenführen. Die Zusammenführung erfolgt in beiden Fällen über [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), um zu definieren, wie Tupel an Bolts weitergeleitet werden.

Das folgende Java-Beispiel verwendet "fieldsGrouping", um Tupel von den Komponenten "1", "2" und "3" an den **MyJoiner**-Bolt weiterzuleiten.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### Batchverarbeitung

Batchverarbeitung kann auf unterschiedliche Arten implementiert werden. In einer einfachen Storm-Java-Topologie können Sie einen einfachen Zähler verwenden, um X Tupel vor der Ausgabe zu einem Batch zusammenzufassen, oder einen internen Timer-Mechanismus verwenden, der auch als Tick-Tupel bezeichnet wird, um alle X Sekunden einen Batch auszugeben.

Ein Beispiel für Tick-Tupel finden Sie unter [Analysieren von Sensordaten mit Storm und HDInsight](/de-de/documentation/articles/hdinsight-storm-sensor-data-analysis.md).

Falls Sie Trident verwenden, erfolgt die Verarbeitung immer als Batches von Tupeln.

### Cachedienst

Speicherinterne Zwischenspeicherung wird oft verwendet, um die Verarbeitung zu beschleunigen, da häufig verwendete Elemente im Speicher erhalten bleiben. Da eine Topologie über mehrere Knoten verteilt ist und jeder Knoten mehrere Prozesse enthält, können Sie mit [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) sicherstellen, dass Tupel mit Feldern, die für die Cache-Suche verwendet werden, immer an denselben Prozess weitergeleitet werden. Auf diese Weise können Sie Duplikate von Cache-Einträgen zwischen Prozessen vermeiden.

### Streaming Top N

Wenn Ihre Topologie darauf abzielt, einen " 'top' N"-Wert zu ermitteln, z. B. die Top 5 Trends auf Twitter, dann sollten Sie den "Top N"-Wert parallel berechnen und die Ausgabe dieser Berechnungen in einen globalen Wert zusammenführen. Hierzu können Sie z. B. [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) verwenden, um nach Feldern an parallele Bolts weiterzuleiten, die die Daten nach Feldwerten partitionieren, und zuletzt an einen Bolt weiterleiten, der den "Top N"-Wert global ermittelt.

Ein Beispiel hierfür finden Sie im [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java)-Beispiel.

## Nächste Schritte

* [Erste Schritte mit Storm in HDInsight][gettingstarted]

* [Analysieren von Sensordaten mit Storm und HBase in HDInsight (Hadoop)](/de-de/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Entwickeln von Anwendungen zur Verarbeitung von Datenströmen in C# mit Stream Computing Platform und Storm in HDInsight](/de-de/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /de-de/documentation/articles/hdinsight-storm-getting-started

<!--HONumber=42-->
