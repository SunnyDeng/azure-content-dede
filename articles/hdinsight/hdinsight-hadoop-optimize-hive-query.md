<properties
   pageTitle="Optimieren Ihrer Hive-Abfragen zur schnelleren Ausführung in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie Ihre Hive-Abfragen für Hadoop in HDInsight optimieren."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# Optimieren von Hive-Abfragen für Hadoop in HDInsight

Hadoop-Cluster sind standardmäßig nicht leistungsoptimiert. In diesem Artikel werden einige der gängigsten Optimierungsmethoden für die Leistung von Hive vorgestellt, die Sie auf unsere Abfragen anwenden können.

[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Optimieren von Hive-Abfragen für Hadoop in HDInsight](hdinsight-hadoop-optimize-hive-query-v1.md)

##Horizontales Skalieren der Worker-Knoten

In einem Cluster, dem mehr Worker-Knoten zur Verfügung stehen, können mehr Mapper und Reducer parallel ausgeführt werden. In HDInsight können Sie die horizontale Skalierung auf zwei Weisen erhöhen:

- Bei der Bereitstellung können Sie die Anzahl der Workerknoten im Azure-Vorschauportal, mit Azure PowerShell oder mit der plattformübergreifenden Befehlszeilenschnittstelle angeben. Weitere Informationen finden Sie unter [Bereitstellen von HDInsight-Clustern](hdinsight-provision-clusters.md). Der folgende Screenshot zeigt die Konfiguration der Workerknoten im Azure-Vorschauportal:

	![scaleout\_1][image-hdi-optimize-hive-scaleout_1]

- Aber auch während der Laufzeit kann ein Cluster horizontal skaliert werden, ohne ihn neu zu erstellen. Dies wird nachfolgend gezeigt. ![scaleout\_1][image-hdi-optimize-hive-scaleout_2]

Weitere Informationen zu den von HDInsight unterstützten virtuellen Computern finden Sie unter [HDInsight-Preise](http://azure.microsoft.com/pricing/details/hdinsight/).

##Aktivieren von Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) ist eine Alternative zum Ausführungsmodul MapReduce:

![tez\_1][image-hdi-optimize-hive-tez_1]


Tez ist jedoch aus folgenden Gründen schneller:

- Ausführung gerichteter azyklischer Diagramme (DAG; Directed Acyclic Graph) im MapReduce-Modul als Einzelauftrag. Für das darzustellende DAG muss auf jeden Mapper-Satz ein Reducer-Satz folgen. Dadurch müssen für jede Hive-Abfrage mehrere MapReduce-Aufträge gestartet werden. Für Tez gilt diese Einschränkung nicht. Es kann auch ein komplexes DAG in einem Auftrag verarbeiten, so dass weniger Aufträge gestartet werden müssen.
- **Vermeidung unnötiger Schreibvorgänge** Aufgrund der Eröffnung mehrerer Aufträge für eine Hive-Abfrage im MapReduce-Modul wird die Ausgabe eines jeden Auftrags in den HDFS-Zwischenspeicher geschrieben. Tez hingegen minimiert die Anzahl der Aufträge für jede Hive-Abfrage und vermeidet so unnötige Schreibvorgänge.
- **Minimierung der Startverzögerungen** Tez minimiert Startverzögerungen durch Reduzierung der Anzahl der für den Start erforderlichen Mapper sowie durch eine insgesamt bessere Optimierung.
- **Wiederverwendung von Containern** Tez versucht Container möglichst wiederzuverwenden und verringert so Latenzzeiten aufgrund von Containerstarts.
- **Fortlaufende Optimierung** Bislang fand die Optimierung in der Regel bei der Kompilierung statt. Nun aber stehen mehr Informationen zu den Eingaben zur Verfügung, die auch während der Laufzeit eine Optimierung ermöglichen. Tez verwendet fortlaufende Optimierungstechniken, durch die der Plan noch weit in der Laufzeitphase optimiert werden kann.

Weitere Informationen zu diesen Konzepten finden Sie [hier](http://hortonworks.com/hadoop/tez/).

Sie können jede von Tez ermöglichte Hive-Abfrage ausführen, indem Sie der Abfrage die folgende Einstellung voranstellen:

	set hive.execution.engine=tez;

Tez muss zum Zeitpunkt der Bereitstellung aktiviert sein. Nachfolgend sehen Sie ein Azure PowerShell-Beispielskript für die Bereitstellung eines Hadoop-Clusters mit aktiviertem Tez:


	$clusterName = "[HDInsightClusterName]"
	$location = "[AzureDataCenter]" #i.e. West US
	$dataNodes = 32 # number of worker nodes in the cluster

	$defaultStorageAccountName = "[DefaultStorageAccountName]"
	$defaultStorageContainerName = "[DefaultBlobContainerName]"
	$defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

	$hdiUserName = "[HTTPUserName]"
	$hdiPassword = "[HTTPUserPassword]"

	$hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
	$hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

	$hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

	New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
	Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
	Add-AzureHDInsightConfigValues -Hive $hiveConfig |
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

## Partitionierung in Hive

E/A-Vorgänge sind bei der Ausführung von Hive-Abfragen der größte Leistungsengpass. Die Leistung lässt sich durch Reduzierung der einzulesenden Daten verbessern. Hive-Abfragen durchsuchen standardmäßig gesamte Hive-Tabellen. Dies ist für Tabellenscans durchaus adäquat, bei Abfragen, die nur einen Auszug der Daten durchsuchen müssen (z. B. bei Abfragen mit Filtern), bedeutet dies hingegen einen unnötigen Aufwand. Durch die Partitionierung in Hive beschränkt sich der Zugriff von Hive-Abfragen auf die erforderlichen Teilmengen von Hive-Tabellen.

Die Partitionierung in Hive erfolgt durch Neuorganisation der Rohdaten in neuen Verzeichnissen, wobei jede Partition ein eigenes, vom Benutzer definiertes Verzeichnis erhält. Das folgende Diagramm veranschaulicht die Partitionierung einer Hive-Tabelle nach der Spalte *Jahr*. Für jedes Jahr wird ein neues Verzeichnis erstellt.

![Partitionierung][image-hdi-optimize-hive-partitioning_1]

Überlegungen zur Partitionierung:

- **Partitionieren Sie großzügig** – Wenn Sie die Partitionierung für Spalten mit nur wenigen Werte durchführen, erhalten Sie nur sehr wenige Partitionen. Partitionieren Sie zum Beispiel nach dem Geschlecht, so erhalten Sie nur zwei Partitionen (männlich, weiblich), die Latenzzeit wird sich also höchstens halbieren.

- **Aber nicht zu großzügig** – Partitionieren Sie hingegen nach einer Spalte mit eindeutigen Werten (z. B. der Benutzer-ID), belasten Sie den Namensknoten des Clusters durch die unzähligen Partitionsverzeichnisse nur unnötig.

- **Vermeiden Sie zu unterschiedliche Partitionsgrößen** – Wählen Sie Ihren Partitionsschlüssel so, dass sich etwa gleich große Partitionen ergeben. Partitionieren Sie zum Beispiel nach *Bundesland*, so wird die Partition für Nordrhein-Westfalen aufgrund der Einwohnerzahl etwa 17 mal so viele Einträge enthalten wie diejenige des Saarlands.

Zum Erstellen einer Partitionstabelle verwenden Sie die Klausel *Partitioned By*:

    CREATE TABLE lineitem_part
    	(L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE 	  	 STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
    	 L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Für die erstellte Partitionstabelle können Sie eine statische oder eine dynamische Partitionierung auswählen.

- **Statische Partitionierung** bedeutet, dass die entsprechenden Verzeichnisse bereits freigegebene Daten enthalten und Sie Hive-Partitionen manuell auf Basis des Verzeichnispfads abfragen können. Dies veranschaulicht der folgende Codeausschnitt:

	    INSERT OVERWRITE TABLE lineitem_part
	    PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
	    SELECT * FROM lineitem 
	    WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

	    ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
	    LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Dynamische Partitionierung** bedeutet, dass Hive die Partitionen automatisch erstellen und anpassen soll. Da wir die Partitionstabelle bereits aus der Stagingtabelle erstellt haben, müssen wir die Tabelle nun nur noch mit Daten füllen, wie nachfolgend gezeigt:

	    SET hive.exec.dynamic.partition = true;
	    SET hive.exec.dynamic.partition.mode = nonstrict;
	    INSERT INTO TABLE lineitem_part
	    PARTITION (L_SHIPDATE)
	    SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
	    	 L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
	     	 L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
	    	 L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as 	 	 L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as 	 	 L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as 	 L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as 	 L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Weitere Informationen finden Sie unter [Partitionierte Tabellen](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##Verwenden des Dateiformats ORC

Hive unterstützt verschiedene Dateiformate. Zum Beispiel:

- **Text**: Dies ist das Standarddateiformat. Es funktioniert in den meisten Szenarien.
- **Avro**: Dieses Dateiformat eignet sich besonders für Interoperabilitätsszenarien.
- **ORC/Parquet**: Dieses Dateiformat ist leistungsorientiert.

Das ORC-Format (Optimized Row Columnar) ist eine sehr effiziente Speichermethode für Hive-Daten. Gegenüber anderen Formaten hat ORC die folgenden Vorteile:

- Unterstützung für komplexe Typen, einschließlich DateTime, und komplexe und teilstrukturierte Typen
- Komprimierung bis zu 70 %
- Indizierung alle 10.000 Zeilen, wodurch das Überspringen von Zeilen möglich wird
- Wesentlich schnellere Laufzeitausführung

Zum Aktivieren von ORC erstellen Sie zunächst eine Tabelle mit der Klausel *Stored as ORC*:

    CREATE TABLE lineitem_orc_part
    	(L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
		 L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT 	 STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Danach fügen Sie der ORC-Tabelle Daten aus der Stagingtabelle hinzu. Zum Beispiel:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
    	   L_SUPPKEY as L_SUPPKEY,
		   L_LINENUMBER as L_LINENUMBER,
     	   L_QUANTITY as L_QUANTITY, 
		   L_EXTENDEDPRICE as L_EXTENDEDPRICE,
    	   L_DISCOUNT as L_DISCOUNT,
		   L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
		   L_LINESTATUS as L_LINESTATUS,
		   L_SHIPDATE as L_SHIPDATE,
		   L_COMMITDATE as L_COMMITDATE,
		   L_RECEIPTDATE as L_RECEIPTDATE, 
		   L_SHIPINSTRUCT as L_SHIPINSTRUCT,
		   L_SHIPMODE as L_SHIPMODE,
		   L_COMMENT as L_COMMENT
    FROM lineitem;

Weitere Informationen über das ORC-Format erhalten Sie [hier](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##Vektorisierung

Durch Vektorisierung kann Hive anstatt einzelnen Zeilen Batches mit jeweils 1024 Zeilen gleichzeitig verarbeiten. Einfache Vorgänge gehen so schneller, da weniger interner Code ausgeführt werden muss.

Zum Aktivieren der Vektorisierung stellen Sie Ihrer Hive-Abfrage folgende Einstellung voran:

    set hive.vectorized.execution.enabled = true;

Weitere Informationen finden Sie unter [Vektorisierte Abfrageausführung](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##Weitere Optimierungsmethoden

Es gibt noch weitere Optimierungsmethoden, die durchaus erwägenswert sind, zum Beispiel die folgenden:

- **Hive-Bucketing**: Ein Verfahren, mit dem große Datenmengen zur Optimierung der Abfrageleistung zusammengefasst bzw. segmentiert werden.
- **Join-Optimierung**: Optimierung des Hive-Abfrageausführungsplans zur Steigerung der Effizienz von Joins. Außerdem sollen dadurch Benutzerhinweise weitgehend unnötig werden. Weitere Informationen finden Sie unter [Join-Optimierung](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **Reducer erhöhen**

##<a id="nextsteps"></a> Nächste Schritte
In diesem Artikel haben Sie mehrere allgemeine Hive-Methoden zur Optimierung von Abfragen kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwenden von Apache Hive in HDInsight](../hdinsight-using-hive.md)
- [Analysieren von Flugverspätungsdaten mit Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analysieren von Twitter-Daten mit Hive in HDInsight](hdinsight-analyze-twitter-data.md)
- [Analysieren von Sensordaten mit der Hive-Abfragekonsole für Hadoop in HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Verwenden von Hive in HDInsight zum Analysieren von Website-Protokollen](../hdinsight-hive-analyze-web-site-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png

<!---HONumber=August15_HO8-->