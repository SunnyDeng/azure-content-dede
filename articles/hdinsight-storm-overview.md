<properties
	pageTitle="Einführung in Apache Storm in HDInsight | Microsoft Azure"
	description="Erhalten Sie eine allgemeine Einführung in Apache Storm, und erfahren Sie, wie Sie Apache Storm in HDInsight verwenden, um Lösungen für Echtzeit-Datenanalyse in der Cloud zu erstellen."
	keywords="apache storm,real time analytics"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="larryfr"/>

#Einführung in Apache Storm in HDInsight: Echtzeitanalysen für Hadoop

Mit Apache Storm in HDInsight können Sie verteilte Echtzeitanalyselösungen in der Azure-Umgebung mithilfe von [Apache Hadoop](http://hadoop.apache.org) erstellen.

##Was ist Apache Storm?

Apache Storm ist ein verteiltes, fehlertolerantes, Open Source-Berechnungssystem für die Verarbeitung von Daten in Echtzeit mit Hadoop. Storm-Lösungen sind außerdem in der Lage, die Verarbeitung von Daten zu garantieren und Daten erneut abzuspielen, die beim ersten Versuch nicht erfolgreich verarbeitet wurden.

##Warum Storm in HDInsight verwenden?

Apache Storm in HDInsight wird als verwalteter Cluster angeboten, der in die Azure-Umgebung integriert ist. Die Lösung bietet folgende wichtige Vorteile:

* Ausführung als verwalteter Dienst mit einer SLA von 99,9 % Betriebszeit ausgeführt

* Verwendung der Sprache Ihrer Wahl: bietet Unterstützung für Storm-Komponenten in **Java**, **C#** und **Python**

	* Unterstützung für gemischte Programmiersprachen: Daten mit Java lesen und dann mit C# verarbeiten

	* Verwenden Sie die **Trident**-Java-Schnittstelle, um Storm-Topologien zu erstellen, die eine "exakt einmalige" Verarbeitung von Nachrichten, "transaktionale" Datenspeicherpersistenz sowie eine Reihe von allgemeinen Datenstrom-Analysevorgängen unterstützen

* Verfügt über integrierte Funktionen für Skalierbarkeit nach oben und unten: Skalieren eines HDInsight-Clusters ohne Auswirkungen auf die Ausführung von Storm-Topologien

* Integration mit anderen Azure-Diensten, einschließlich Event Hub, Azure Virtual Network, SQL-Datenbank, Blob-Speicher und DocumentDB

	* Kombinieren Sie die Funktionen mehrerer HDInsight-Cluster mithilfe von Azure Virtual Network: Erstellen Sie analytische Pipelines, die HDInsight-, HBase- oder Hadoop-Cluster nutzen.

Eine Liste der Unternehmen, die Apache Storm als Echtzeitanalyselösung verwenden, finden Sie unter [Unternehmen, die Apache Storm verwenden](https://storm.apache.org/documentation/Powered-By.html).

Eine Einführung in Storm finden Sie unter [Erste Schritte mit Storm in HDInsight][gettingstarted].

###Einfache Bereitstellung

Sie können einen neuen Storm in HDInsight-Cluster in wenigen Minuten bereitstellen. Geben Sie den Clusternamen, die Größe, das Administratorkonto und das Speicherkonto an. Azure erstellt den Cluster, einschließlich Beispieltopologien und Webverwaltungs-Dashboard.

Im Folgenden finden Sie ein Beispiel für die Bereitstellung eines Storm in HDInsight-Clusters mithilfe des Azure-Portals. Sie können einen Storm-Cluster auch über [Azure PowerShell](install-configure-powershell.md) bereitstellen.

![Ein Beispiel des Clusterformulars zur Schnellerfassung im Portal](./media/hdinsight-storm-overview/quick-create.png)

Innerhalb von 15 Minuten nach Übermittlung der Anforderung verfügen Sie über einen neuen Storm-Cluster, der bereit ist für die erste Echtzeitanalysepipeline.

###Einfache Bedienung

Wenn Sie Visual Studio verwenden, haben Sie mit den HDInsight-Tools für Visual Studio die Möglichkeit, hybride C#-/Java-Topologien zu erstellen, die Sie dann an Ihren Storm in HDInsight-Cluster senden können.

![Storm-Projekterstellung](./media/hdinsight-storm-overview/createproject.png)

Die HDInsight-Tools für Visual Studio bieten außerdem eine Schnittstelle, die die Überwachung und Verwaltung von Storm-Topologien in einem Cluster ermöglicht.

![Storm-Verwaltung](./media/hdinsight-storm-overview/stormview.png)

Ein Beispiel für die Verwendung der HDInsight-Tools zum Erstellen einer Storm-Anwendung finden Sie unter [Entwickeln von C#-Storm-Topologien mit HDInsight-Tools für Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Informationen zum Installieren der HDInsight-Tools für Visual Studio finden Sie unter [Erste Schritte mit den HDInsight-Tools für Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

Jedes Storm in HDInsight-Cluster bietet auch ein webbasiertes Storm-Dashboard, mit dem Sie im Cluster ausgeführte Storm-Topologien übermitteln, überwachen und verwalten können.

![Storm-Dashboard](./media/hdinsight-storm-overview/dashboard.png)

Weitere Informationen über das Storm-Dashboard finden Sie unter [Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Storm in HDInsight bietet außerdem durch den **Event Hub-Spout** eine einfache Integration in Azure Event Hubs. Dies steht auf jedem Storm-Cluster unter **%STORM_HOME%\\examples\\eventhubspout\\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** zur Verfügung. Beispiele für die Verwendung dieses Spouts in einer Storm-Topologie finden Sie unter [Erste Schritte mit Event Hubs](service-bus-event-hubs-c-storm-getstarted.MD) und [Analysieren von Sensordaten mit Storm und HBase](hdinsight-storm-sensor-data-analysis.MD).

###Zuverlässigkeit

Mit Apache Storm ist garantiert, dass jede eingehende Nachricht vollständig verarbeitet wird, auch wenn die Datenanalyse über Hunderte von Knoten verteilt ist.

Der **Nimbus-Knoten** stellt dem Hadoop-JobTracker ähnliche Funktionen zur Verfügung und weist über **Zookeeper** Aufgaben anderen Knoten im Cluster zu. Zookeeper-Knoten stellen Koordination für den Cluster bereit und erleichtern die Kommunikation zwischen Nimbus und dem **Supervisor**-Prozess der Workerknoten. Wenn ein Verarbeitungsknoten ausfällt, wird der Nimbus-Knoten darüber informiert, und die Aufgabe sowie zugehörige Daten werden einem anderen Knoten zugewiesen.

In der Standardkonfiguration verfügt Apache Storm über einen einzelnen Nimbus-Knoten. Storm in HDInsight betreibt zwei Nimbus-Knoten. Wenn der primäre Knoten fehlschlägt, wechselt der HDInsight-Cluster auf den sekundären Knoten, während der primäre Knoten wiederhergestellt wird.

![Nimbus-, Zookeeper- und Supervisor-Diagramm](./media/hdinsight-storm-overview/nimbus.png)

###Skalieren

Obwohl Sie die Anzahl der Knoten im Cluster bei der Erstellung angeben können, sollten Sie den Cluster je nach Arbeitslast vergrößern oder verkleinern. Alle HDInsight-Cluster bieten die Möglichkeit, die Anzahl der Knoten im Cluster zu ändern, sogar während der Verarbeitung von Daten.

![Datenknoten-Instanzanzahl im Portal](./media/hdinsight-storm-overview/scale.png)

###Support

Für Storm in HDInsight steht umfassender Support auf Unternehmensebene rund um die Uhr zur Verfügung. Storm in HDInsight bietet eine SLA von 99,9 %. Das bedeutet, dass der Cluster garantiert mindestens 99,9 % der Zeit über externe Konnektivität verfügt.

##Allgemeine Szenarios für Echtzeitanalysen

Hier finden Sie eine Liste der typischen Szenarios, in denen Apache Storm in HDInsight eingesetzt wird. Weitere Informationen zu realen Szenarios finden Sie unter [How companies are using Storm](https://storm.incubator.apache.org/documentation/Powered-By.html) (Einsatz von Storm in Unternehmen, in englischer Sprache).

* Internet der Dinge (IoT, Internet of Things)
* Betrugserkennung
* Soziale Analysen
* Extraktions-, Umwandlungs- und Ladeprozesse (Extract, Transform and Load, ETL)
* Netzwerküberwachung
* Suchen
* Mobile Engagement

##Wie werden die Daten in HDInsight Storm verarbeitet?

Apache Storm verarbeitet **Topologien** anstelle der MapReduce-Aufträge, die Sie möglicherweise aus HDInsight oder Hadoop kennen. Ein Storm in HDInsight-Cluster enthält zwei Arten von Knoten: Hauptknoten, auf denen **Nimbus** ausgeführt wird, und Workerknoten, auf denen **Supervisor** ausgeführt wird.

* **Nimbus:** ähnelt dem JobTracker in Hadoop und verteilt den Code über das Cluster, weist Aufgaben an virtuelle Computer zu und überwacht das System auf Fehler. HDInsight stellt zwei Nimbus-Knoten bereit, um einzelne Fehlerquellen für Storm in HDInsight auszuschließen.

* **Supervisor:** Die Supervisoren der einzelnen Workerknoten sind dafür verantwortlich, die **Arbeitsprozesse** im Knoten zu starten und zu beenden

* **Arbeitsprozess:** Ein Arbeitsprozess führt eine Teilmenge einer **Topologie** aus. Eine laufende Topologie ist über viele Arbeitsprozesse im gesamten Cluster verteilt.

* **Topologie:** definiert einen Berechnungsgraphen zur Verarbeitung von **Datenströmen**. Im Gegensatz zu MapReduce-Aufträgen laufen Topologien so lange, bis sie beendet werden.

* **Datenstrom:** eine ungebundene Sammlung von **Tupeln**. Datenströme werden von **Spouts** und **Bolts** erzeugt und von **Bolts** konsumiert.

* **Tupel:** eine benannte Liste dynamisch typisierter Werte.

* **Spout:** konsumiert Daten aus einer Datenquelle und gibt einen oder mehrere **Datenströme** aus

	> [AZURE.NOTE]Oft werden die Daten aus einer Warteschlange wie z. B. Kafka, Azure ServiceBus-Warteschlangen oder Event Hubs gelesen. Die Warteschlange sorgt dafür, dass die Daten bei einem Ausfall in persistenter Form vorliegen.

* **Bolt:** konsumiert **Datenströme**, verarbeitet **Tupel** und kann **Datenströme** ausgeben. Bolts sind außerdem dafür verantwortlich, Daten in externe Speicher zu schreiben, z. B. eine Warteschlange, HDInsight, HBase, Blobs oder andere Datenspeicher.

* **Thrift:** Apache Thrift ist ein Software-Framework für die Entwicklung skalierbarer, sprachübergreifender Dienste. Mit Thrift können Sie Dienste entwickeln, die zwischen C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk und anderen Sprachen funktionieren.

	* **Nimbus** ist ein Thrift-Dienst, und eine **Topologie** ist eine Thrift-Definition. Aus diesem Grund ist es möglich, Topologien in einer Vielzahl von Programmiersprachen zu entwickeln.

Weitere Informationen zu Storm-Komponenten finden Sie im [Storm Tutorial][apachetutorial] (in englischer Sprache) auf apache.org.


##Welche Programmiersprachen kann ich verwenden?

Der Storm in HDInsight-Cluster unterstützt C#, Java und Python.

### C&#35;

Mit den HDInsight-Tools für Visual Studio können .NET-Entwickler eine Topologie in C# entwerfen und implementieren. Sie können auch Hybridtopologien erstellen, die Java- und C#-Komponenten verwenden.

Weitere Informationen finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

###Java

Bei den meisten Java-Beispielen handelt es sich entweder um reines Java oder um Trident. Trident ist eine Abstraktion auf einer hohen Ebene zur Erleichterung von Aufgaben wie z. B. Joins, Aggregation, Gruppierung und Filterung. Trident arbeitet jedoch mit Stapeln von Tupeln, wohingegen Datenströme in reinen Java-Lösungen tupelweise verarbeitet werden.

Weitere Informationen zu Trident finden Sie im [Trident Tutorial](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) (in englischer Sprache) auf apache.org.

Beispiele für reine Java- und Trident-Topologien finden Sie im Verzeichnis **%storm_home%\\contrib\\storm-starter** in Ihrem HDInsight Storm-Cluster.

##Welche Entwicklungsmuster werden häufig verwendet?

###Garantierte Nachrichtenverarbeitung

Mit Storm lassen sich verschiedene Stufen der garantierten Nachrichtenverarbeitung implementieren. Eine einfache Storm-Anwendung kann z. B. die Verarbeitung nach dem "At-Least-Once"-Prinzip garantieren, während Trident Nachrichten nach dem "Exactly-Once"-Prinzip garantiert.

Weitere Informationen finden Sie unter [Guarantees on data processing](https://storm.apache.org/about/guarantees-data-processing.html) (Garantien zur Datenverarbeitung, in englischer Sprache) auf apache.org

###IBasicBolt

Bei diesem Muster werden ein Eingangstupel gelesen, null oder mehr Tupel ausgegeben und Ack für das Eingangstupel direkt am Ende der Verarbeitungsmethode aufgerufen. Dieses Muster wird häufig verwendet, und Storm bietet die [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html)-Schnittstelle zu dessen Automatisierung.

###Joins

Joins zwischen zwei Datenströmen unterscheiden sich je nach Anwendung. Sie können z. B. alle Tupel aus mehreren Datenströmen in einem neuen Datenstrom zusammenführen, oder Sie führen nur Stapel von Tupeln für ein bestimmtes Intervall zusammen. Die Zusammenführung erfolgt in beiden Fällen über [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), um zu definieren, wie Tupel an Bolts weitergeleitet werden.

Das folgende Java-Beispiel verwendet fieldsGrouping, um Tupel von den Komponenten "1", "2" und "3" an den **MyJoiner**-Bolt weiterzuleiten.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###Batchverarbeitung

Batchverarbeitung kann auf unterschiedliche Arten implementiert werden. In einer einfachen Storm-Java-Topologie können Sie einen einfachen Zähler verwenden, um X Tupel vor der Ausgabe zu einem Batch zusammenzufassen, oder einen internen Timer-Mechanismus verwenden, der auch als "Tick-Tupel" bezeichnet wird, um alle X Sekunden einen Batch auszugeben.

Ein Beispiel für Tick-Tupel finden Sie unter [Analysieren von Sensordaten mit Storm und HBase in HDInsight](hdinsight-storm-sensor-data-analysis.md)

Falls Sie Trident verwenden, erfolgt die Verarbeitung immer als Batches von Tupeln.

###Zwischenspeicherung

Speicherinterne Zwischenspeicherung wird oft verwendet, um die Verarbeitung zu beschleunigen, da häufig verwendete Elemente im Speicher erhalten bleiben. Da eine Topologie über mehrere Knoten verteilt ist und jeder Knoten mehrere Prozesse enthält, können Sie mit [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) sicherstellen, dass Tupel mit Feldern, die für die Cache-Suche verwendet werden, immer an denselben Prozess weitergeleitet werden. Auf diese Weise können Sie Duplikate von Cache-Einträgen zwischen Prozessen vermeiden.

###Streaming Top N

Wenn Ihre Topologie darauf abzielt, einen "Top N"-Wert zu ermitteln, z. B. die Top-5-Trends auf Twitter, dann sollten Sie den "Top N"-Wert parallel berechnen und die Ausgabe dieser Berechnungen in einen globalen Wert zusammenführen. Hierzu können Sie z. B. [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) verwenden, um nach Feldern an parallele Bolts weiterzuleiten, die die Daten nach Feldwerten partitionieren, und dann an einen Bolt weiterleiten, der den "Top N"-Wert global ermittelt.

Ein Beispiel hierfür finden Sie im [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java)-Beispiel.

##Nächste Schritte

Weitere Informationen zu Lösungen für Echtzeitanalysen mit Apache Storm in HDInsight:

* [Erste Schritte mit Storm in HDInsight][gettingstarted]

* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-storm-getting-started.md

<!--HONumber=54-->