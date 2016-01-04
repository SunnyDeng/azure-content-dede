<properties
	pageTitle="Fehlermeldung „Nicht genügend Arbeitsspeicher“ (Out Of Memory, OOM) – Hive-Einstellungen | Microsoft Azure"
	description="Beheben eines Fehlers „Nicht genügend Arbeitsspeicher“ (Out Of Memory, OOM) bei einer Hive-Abfrage in Hadoop in HDInsight. Das Kundenszenario ist eine viele große Tabellen übergreifende Abfrage."
	keywords="Fehler „Nicht genügend Arbeitsspeicher“, OOM, Hive-Einstellungen"
	services="hdinsight"
	documentationCenter=""
	authors="rashimg"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="12/10/2015"
	ms.author="rashimg;cgronlun"/>

# Beheben eines „Nicht genügend Arbeitsspeicher“-Fehlers (Out of Memory, OOM) mit Hive-Arbeitsspeichereinstellungen in Hadoop in Azure HDInsight

Ein häufiges Problem unserer Kunden beim Verwenden von Hive ist, eine OOM-Fehlermeldung zu erhalten. Dieser Artikel beschreibt ein Kundenszenario und die Hive-Einstellungen, die wir empfohlen haben, um das Problem zu beheben.

## Szenario: große Tabellen übergreifende Hive-Abfrage

Ein Kunde führte die Abfrage unten mit Hive aus.

	SELECT
		COUNT (T1.COLUMN1) as DisplayColumn1,
		…
		…
		….
	FROM
		TABLE1 T1,
		TABLE2 T2,
		TABLE3 T3,
		TABLE5 T4,
		TABLE6 T5,
		TABLE7 T6
	where (T1.KEY1 = T2.KEY1….
		…
		…

Bestimmte Aspekte dieser Abfrage:

* T1 ist ein Alias für eine große Tabelle, TABLE1, die viele STRING-Spaltentypen enthält.
* Andere Tabellen sind nicht so groß, enthalten aber eine große Anzahl von Spalten.
* Alle Tabellen sind miteinander verbunden, in einigen Fällen mit mehreren Spalten in TABLE1 und anderen.

Wenn der Kunde die Abfrage mit Hive unter MapReduce auf einem 24-Knoten-A3-Cluster ausführte, dauerte die Abfrage etwa 26 Minuten. Beim Ausführen der Abfrage mit Hive unter MapReduce erhielt der Kunde folgende Warnmeldungen:

	Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
	Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Da die Ausführung der Abfrage nach etwa 26 Minuten endete, ignorierte der Kunde diese Warnungen und konzentrierte sich stattdessen auf die weitere Verbesserung der Abfrageleistung.

Der Kunde konsultierte [Optimieren von Hive-Abfragen für Hadoop in HDInsight](hdinsight-hadoop-optimize-hive-query.md) und entschied sich für die Verwendung des Tez-Ausführungsmoduls. Sobald die gleiche Abfrage mit aktivierter Tez-Einstellung ausgeführt wurde, wurde die Abfrage 15 Minuten ausgeführt, und dann wurde folgende Fehlermeldung ausgegeben:

	Status: Failed
	Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
	org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
	org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
	Caused by: java.lang.OutOfMemoryError: Java heap space

Der Kunde entschied sich dann für die Verwendung eines größeren virtuellen Computers (d. h. D12), in der Annahme, ein größerer virtueller Computer böte mehr Heapspeicher. Der Kunde erhielt weiterhin die Fehlermeldung. Der Kunde wandte sich an das HDInsight-Team, um Hilfe bei der Fehlerbehebung zu erhalten.

## Debuggen des OOM-Fehlers

Unsere Support- und Engineering-Teams fanden heraus, dass eines der Probleme, das den OOM-Fehler verursacht hatte, ein [in der Apache JIRA beschriebenes bekanntes Problem](https://issues.apache.org/jira/browse/HIVE-8306) war. Aus der Beschreibung in der JIRA:

	When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Wir fanden bei einer Untersuchung bestätigt, dass **hive.auto.convert.join.noconditionaltask** in der Datei „hive-site.xml“ tatsächlich auf **true** gesetzt war:

	<property>
    	<name>hive.auto.convert.join.noconditionaltask</name>
    	<value>true</value>
    	<description>
      		Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
      		If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
      		specified size, the join is directly converted to a mapjoin (there is no conditional task).
    	</description>
  	</property>

Basierend auf der Warnung und der JIRA lautete unser Hypothese, dass „Map Join“ die Ursache des Java-Heapspeicher-OOM-Fehlers war. Also betrachteten wir dieses Problem genauer.

Wie im Blogbeitrag [Hadoop Yarn memory settings in HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx) erläutert, gehört der bei Einsatz des Tez-Ausführungsmoduls verwendete Heapspeicher tatsächlich zum Tez-Container. Die Abbildung unten stellt den Speicher des Tez-Containers dar.

![Diagramm des Tez-Containerspeichers: Fehler – nicht genügend Hive-Speicher, OOM](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Wie im Blogbeitrag schon sagt, definieren die folgenden zwei Einstellungen den Containerspeicher für den Heap: **hive.tez.container.size** und **hive.tez.java.opts**. Nach unserer Erfahrung bedeutet die OOM-Ausnahme nicht, dass der Container zu klein ist. Es bedeutet, dass der Java-Heap (hive.tez.java.opts) zu klein ist. Wenn Sie also eine OOM-Fehlermeldung erhalten, können Sie versuchen, **hive.tez.java.opts** heraufzusetzen. Ggf. können Sie **hive.tez.container.size** heraufsetzen. Die Einstellung **java.opts** sollte ungefähr 80 % von **container.size** betragen.

> [AZURE.NOTE]Die Einstellung **hive.tez.java.opts** muss stets kleiner sein als **hive.tez.container.size**.

Da ein D12-Computer 28 GB Arbeitsspeicher hat, haben wir beschlossen, eine Containergröße von 10 GB (10.240 MB) zu verwenden und java.opts 80 % zuzuweisen. Dies erfolgte in der Hive-Konsole mit der folgenden Einstellung:

	SET hive.tez.container.size=10240
	SET hive.tez.java.opts=-Xmx8192m

Basierend auf diesen Einstellungen wurde die Abfrage in weniger als zehn Minuten erfolgreich ausgeführt.

## Schlussfolgerung: OOM-Fehler und Containergröße

Eine OOM-Fehlermeldung zu erhalten, bedeutet nicht unbedingt, dass der Container zu klein ist. Konfigurieren Sie stattdessen die Einstellungen für den Arbeitsspeicher, sodass die Heapgröße erhöht wird und mindestens 80 % der Größe des Containerspeichers entspricht.

<!---HONumber=AcomDC_1217_2015-->