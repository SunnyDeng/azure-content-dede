<properties
	pageTitle="Aktivieren von Heapdumps für Hadoop-Dienste auf HDInsight | Microsoft Azure"
	description="Aktivieren Sie Heapdumps für Hadoop-Dienste von Linux-basierten HDInsight-Clustern zum Debuggen und für Analysen."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="larryfr"/>


#Aktivieren von Heapdumps für Hadoop-Dienste auf Linux-basierten HDInsight-Clustern (Vorschau)

[AZURE.INCLUDE [Heapdump-Auswahl](../../includes/hdinsight-selector-heap-dump.md)]

Heapdumps enthalten eine Momentaufnahme des Speichers der Anwendung, einschließlich der Werte von Variablen zum Zeitpunkt der Dumperstellung. Daher sind sie sehr nützlich zum Diagnostizieren von Problemen, die bei der Ausführung auftreten.

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten nur für Linux-basierte HDInsight-Cluster. Informationen zu Windows-basierten HDInsight-Clustern finden Sie unter [Aktivieren von Heapdumps für Hadoop-Dienste auf Windows-basierten HDInsight-Clustern](hdinsight-hadoop-collect-debug-heap-dumps.md)

## <a name="whichServices"></a>Dienste

Sie können Heapdumps für die folgenden Dienste aktivieren:

*  **hcatalog**: tempelton
*  **hive**: hiveserver2, metastore, derbyserver
*  **mapreduce**: jobhistoryserver
*  **yarn**: resourcemanager, nodemanager, timelineserver
*  **hdfs**: datanode, secondarynamenode, namenode

Sie können Heapdumps auch für die Mapper- und Reducer-Prozesse aktivieren, die von HDInsight ausgeführt wurden.

## <a name="configuration"></a>Grundlegendes zur Konfiguration von Heapdumps

Heapdumps werden aktiviert, indem Optionen (oder Parameter) an die JVM übergeben werden, wenn ein Dienst gestartet wird. Für die meisten Hadoop-Dienste kann dies erreicht werden, indem das Shellskript geändert wird, das zum Starten des Diensts verwendet wird.

In jedem Skript ist ein Export für ***\_OPTS** vorhanden, der die an die JVM übergebenen Optionen enthält. Beispiel: Im Skript **hadoop-env.sh** enthält die Zeile, die mit `export HADOOP_NAMENODE_OPTS=` beginnt, die Optionen für den NameNode-Dienst.

Mapper- und Reducer-Prozesse unterscheiden sich geringfügig, da dies untergeordnete Prozesse des MapReduce-Diensts sind. Jeder Mapper- oder Reducer-Prozess wird in einem untergeordneten Container ausgeführt, und es gibt zwei Einträge, die die JVM-Optionen für diese Prozesse enthalten. Beide sind in **mapred-site.xml** enthalten:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [AZURE.NOTE]Wir empfehlen die Verwendung von Ambari zum Ändern der Skripts und der Einstellungen in "mapred-site.xml", da mit Ambari das Replizieren von Änderungen auf Knoten im Cluster verarbeitet wird. Unter [Verwenden von Ambari](#using-ambari) finden Sie die detaillierten Schritte.

###Aktivieren von Heapdumps

Die folgende Option aktiviert Heapdumps bei einem "OutOfMemoryError":

    -XX:+HeapDumpOnOutOfMemoryError

Das **+**-Zeichen gibt an, dass diese Option aktiviert ist. Der Standardwert ist deaktiviert.

> [AZURE.WARNING]Heapdumps sind für Hadoop-Dienste auf HDInsight-Clustern nicht standardmäßig aktiviert, da die Dumpdateien groß sein können. Wenn Sie sie zur Problembehandlung aktivieren, denken Sie daran, sie zu deaktivieren, sobald Sie das Problem reproduziert und die Dumpdateien gesammelt haben.

###Dumpspeicherort

Der Standardspeicherort für die Dumpdatei ist das aktuelle Arbeitsverzeichnis. Sie können steuern, wo die Datei gespeichert wird, indem Sie die folgende Option verwenden:

    -XX:HeapDumpPath=/path

Beispiel: `-XX:HeapDumpPath=/tmp` bewirkt, dass die Dumps im Verzeichnis "/temp" gespeichert werden.

###Skripts

Sie können auch ein Skript auslösen, wenn ein **OutOfMemoryError** auftritt. So können Sie beispielsweise eine Benachrichtigung auslösen, damit Sie wissen, dass der Fehler aufgetreten ist. Dies wird mit der folgenden Option gesteuert:

    -XX:OnOutOfMemoryError=/path/to/script

> [AZURE.NOTE]Da Hadoop ein verteiltes System ist, muss jedes verwendete Skript auf allen Knoten im Cluster vorhanden sein, auf denen der Dienst ausgeführt wird.
>
> Das Skript muss sich zudem an einem Speicherort befinden, auf den das Konto zugreifen kann, mit dem der Dienst ausgeführt wird, und Ausführungsberechtigungen müssen gewährt werden. Sie könnten z. B. Skripts in `/usr/local/bin` speichern und `chmod go+rx /usr/local/bin/filename.sh` zum Gewähren von Lese- und Ausführungsberechtigungen verwenden.

##Verwenden von Ambari

Gehen Sie folgendermaßen vor, um die Konfiguration für einen Dienst zu ändern:

1. Öffnen Sie die Ambari-Webbenutzeroberfläche für den Cluster. Die URL ist https://YOURCLUSTERNAME.azurehdinsight.net.

    Authentifizieren Sie bei der entsprechenden Aufforderung auf der Website mithilfe des HTTP-Kontonamens (Standard: admin) und dem Kennwort für den Cluster.

    > [AZURE.NOTE]Sie könnten ein zweites Mal von Ambari zur Eingabe von Benutzername und Kennwort aufgefordert werden. Geben Sie in diesem Fall einfach den gleichen Kontonamen und das Kennwort erneut ein.

2. Wählen Sie mithilfe der Liste auf der linken Seite den Dienstbereich aus, den Sie ändern möchten. Beispielsweise **HDFS**. Wählen Sie im mittleren Bereich die Registerkarte **Configs** aus.

    ![Bild der Ambari-Webbenutzeroberfläche mit ausgewählter Registerkarte für HDFS-Konfigurationen](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Geben Sie im Feld **Filter** den Text **opts** ein. Dadurch wird die Liste der Konfigurationselemente so gefiltert, dass nur die angezeigt werden, die diesen Text enthalten. Dies ist eine schnelle Möglichkeit, das Shellskript oder eine **Vorlage** zu finden, die verwendet werden kann, um diese Optionen festzulegen.

    ![Gefilterte Liste](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Suchen Sie nach dem Eintrag ***\_OPTS** für den Dienst, für den Sie Heapdumps aktivieren möchten, und fügen die Optionen hinzu, die Sie aktivieren möchten. In der folgenden Abbildung wurde `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` dem Eintrag **HADOOP\_NAMENODE\_OPTS** hinzugefügt:

    ![HADOOP\_NAMENODE\_OPTS mit -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

	> [AZURE.NOTE]Beim Aktivieren von Heapdumps für den untergeordneten Mapper- oder Reducer-Prozess suchen Sie stattdessen nach den Feldern mit der Bezeichnung **mapreduce.admin.map.child.java.opts** und **mapreduce.admin.reduce.child.java.opts**.

    Verwenden Sie die Schaltfläche **Save**, um die Änderungen zu speichern. Sie können einen kurzen Hinweise zur Beschreibung der Änderungen eingeben.

5. Nachdem die Änderungen angewendet wurden, wird das Symbol **Restart required** neben einem oder mehreren Diensten angezeigt.

    ![Symbol "Restart required" und Schaltfläche "Restart"](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Wählen Sie jeden Dienst aus, für den ein Neustart erforderlich ist, und verwenden Sie die Schaltfläche **Service Actions**, um **Turn On Maintenance Mode** auszuwählen. Dadurch wird verhindert, dass Warnungen von diesem Dienst generiert werden, wenn Sie ihn neu starten.

    ![Menü "Turn on maintenance mode"](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Nachdem Sie den Wartungsmodus aktiviert haben, verwenden Sie die Schaltfläche **Restart** für den Dienst, um **Restart All Effected** auszuwählen.

    ![Eintrag "Restart All Affected"](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

    > [AZURE.NOTE]Die Einträge für die Schaltfläche **Restart** können für andere Dienste anders lauten.

8. Verwenden Sie nach dem Neustart der Dienste die Schaltfläche **Service Actions**, um **Turn Off Maintenance Mode** auszuwählen. Dadurch setzt Ambari das Überwachen auf Warnungen für den Dienst fort.

<!---HONumber=Oct15_HO3-->