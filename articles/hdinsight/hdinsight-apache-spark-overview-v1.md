<properties 
	pageTitle="Übersicht über Apache Spark in HDInsight | Microsoft Azure" 
	description="Eine Einführung in Apache Spark in HDInsight und Szenarien, in denen Sie Spark für HDInsight in Ihren Anwendungen verwenden können." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>

# Übersicht: Apache Spark für Azure HDInsight (Windows)
 
> [AZURE.NOTE]HDInsight bietet jetzt Spark-Cluster unter Linux. Informationen zu den Features, die mit HDInsight Spark unter Linux verfügbar sind, finden Sie unter [Übersicht: Apache Spark für Azure HDInsight (Linux)](hdinsight-apache-spark-overview.md).

<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von großen Datenmengen zu steigern. Das Spark-Verarbeitungsmodul ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank seiner arbeitsspeicherinternen Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen. Spark ist auch mit Azure-BLOB-Speicher (WASB) kompatibel, sodass Ihre vorhandenen Daten, die in Azure gespeichert sind, leicht per Spark verarbeitet werden können.

Wenn Sie einen Spark-Cluster in HDInsight erstellen, erstellen Sie damit Azure-Serverressourcen, für die Spark installiert und konfiguriert ist. Es dauert nur ungefähr zehn Minuten, einen Spark-Cluster in HDInsight zu erstellen. Die zu verarbeitenden Daten werden im Azure-BLOB-Speicher gespeichert. Weitere Informationen finden Sie unter [Verwenden von Azure-BLOB-Speicher mit HDInsight][hdinsight-storage].

![Apache Spark für Azure HDInsight](./media/hdinsight-apache-spark-overview-v1/hdispark.architecture.png "Apache Spark für Azure HDInsight")


**Möchten Sie mit der Verwendung von Apache Spark für Azure HDInsight beginnen?** Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Spark-Clusters unter HDInsight und Ausführen von Beispielanwendungen mit Jupyter und Zeppelin](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1.md).



Sehen Sie sich ein kurzes Video an, in dem Apache Spark für Azure HDInsight beschrieben wird, um einen Überblick zu erhalten.

> [AZURE.VIDEO announcing-apache-spark-on-azure-hdinsight]

## Warum sollten Sie Spark für Azure HDInsight verwenden? 

Azure HDInsight umfasst einen vollständig verwalteten Spark-Dienst. Vorteile der Verwendung von Spark für HDInsight:

| Funktion | Beschreibung |
|-------------------------------------|-------------------|
| Einfache Erstellung | Sie können einen neuen Spark-Cluster unter HDInsight in wenigen Minuten erstellen, indem Sie das Azure-Verwaltungsportal, Azure PowerShell oder das HDInsight .NET SDK verwenden. Siehe [Erstellen eines Spark-Clusters in HDInsight](hdinsight-apache-spark-provision-clusters.md). |
| Einfache Bedienung | Spark in HDInsight-Clustern enthält vorkonfigurierte Zeppelin und Jupyter Notebooks. Diese können Sie für die interaktive Datenverarbeitung und -visualisierung verwenden. Die URLs für diese Notebooks lauten https://CLUSTERNAME.azurehdinsight.net/zeppelin und https://CLUSTERNAME.azurehdinsight.net/jupyter. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres HDInsight-Clusters.|
| REST-APIs | Spark in HDInsight umfasst einen Spark-Auftragsserver. Hierbei handelt es sich um einen REST-API-Server, mit dem Benutzer Aufträge per Remotezugriff senden und überwachen können. |
| Gleichzeitige Abfragen | Spark in HDInsight unterstützt gleichzeitige Abfragen. So können mehrere Abfragen von einem Benutzer oder mehrere Abfragen von unterschiedlichen Benutzern und Anwendungen dieselben Clusterressourcen verwenden. |
| Zwischenspeicherung auf SSDs | Sie können Daten entweder im Arbeitsspeicher oder auf SSDs zwischenspeichern, die an die Clusterknoten angefügt sind. Das Zwischenspeichern im Arbeitsspeicher liefert die beste Abfrageleistung, kann aber teuer sein. Das Zwischenspeichern auf SSDs ist eine hervorragende Möglichkeit zur Verbesserung der Abfrageleistung, ohne dass ein Cluster mit einer Größe erstellt werden muss, die für das Einfügen des gesamten Datasets in den Arbeitsspeicher ausreicht.|
| Integration in Azure-Dienste | Spark unter HDInsight verfügt über einen Connector für Azure Event Hubs. Kunden können mit Event Hubs Streaminganwendungen erstellen. Dies ist eine Alternative zur Option [Kafka](http://kafka.apache.org/), die als Teil von Spark bereits verfügbar ist. |
| Integration in BI-Tools | Spark für HDInsight enthält Connectors für beliebte BI-Tools, z. B. [Power BI](http://www.powerbi.com/) und [Tableau](http://www.tableau.com/products/desktop) für die Datenanalyse.|
| Vorinstallierte Anaconda-Bibliotheken | Für Spark-Cluster unter HDInsight sind Anaconda-Bibliotheken vorinstalliert. [Anaconda](http://docs.continuum.io/anaconda/) umfasst ca. 200 Bibliotheken für Machine Learning, Datenanalyse, Visualisierung usw.|
| Skalierbarkeit | Obwohl Sie die Anzahl der Knoten im Cluster bei der Erstellung angeben können, sollten Sie den Cluster je nach Workload vergrößern oder verkleinern. Alle HDInsight-Cluster bieten die Möglichkeit, die Anzahl der Knoten im Cluster zu ändern. Darüber hinaus können Spark-Cluster ohne Datenverlust verworfen werden, da alle Daten im Azure-BLOB-Speicher gespeichert werden. |
| Support rund um die Uhr | Für Spark für HDInsight ist professioneller Support verfügbar, und im SLA ist eine Betriebszeit von 99,9 % angegeben.|



## Welche Anwendungsfälle gibt es für Spark für HDInsight?

Apache Spark in HDInsight ermöglicht die folgenden Anwendungsfälle.

### Interaktive Datenanalyse und BI

[Lernprogramm anzeigen](hdinsight-apache-spark-use-bi-tools-v1.md)

Bei Apache Spark in HDInsight werden Daten in Azure-BLOBs gespeichert. Experten und Entscheidungsträger in Unternehmen können diese Daten analysieren und Berichte damit erstellen und Microsoft Power BI verwenden, um aus den analysierten Daten interaktive Berichte anzufertigen. Analysten können mit unstrukturierten oder teilweise stukturierten Daten in Azure Storage beginnen, mit Notebooks ein Schema für die Daten definieren und dann mit Microsoft Power BI Datenmodelle erstellen. Spark in HDInsight unterstützt auch eine Reihe von BI-Drittanbietertools, z. B. Tableau, Qlikview und SAP Lumira. Daher ist es eine ideale Plattform für Datenanalysten, Experten und Entscheidungsträger.

### Iteratives Machine Learning

[Lernprogramm anzeigen](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)

Apache Spark verfügt über [MLlib](http://spark.apache.org/mllib/), eine Machine Learning-Bibliothek, die auf Spark aufbaut. Außerdem umfasst Spark für HDInsight Anaconda, eine Python-Distribution mit vielen verschiedenen Paketen für Machine Learning. Wenn Sie dies mit der integrierten Unterstützung für Jupyter Notebooks kombinieren, verfügen Sie über eine hochmoderne Umgebung zur Erstellung von Machine Learning-Anwendungen.

### Streaming und Echtzeit-Datenanalysen

[Lernprogramm anzeigen](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

Die Echtzeit-Datenanalyse wird für Szenarien eingesetzt, die von der Reduzierung des Zeitaufwands für Einblicke in Daten (durch die sofortige Verarbeitung von Daten nach dem Eintreffen) bis zur Erstellung einer echten Streaminglösung reichen. Spark in HDInsight bietet umfassende Unterstützung für die Erstellung von Echtzeit-Analyselösungen. Spark verfügt zwar bereits über Connectors zum Erfassen von Daten aus den unterschiedlichsten Quellen, z. B. Kafka, Flume, Twitter, ZeroMQ oder TCP-Sockets, aber mit Spark in HDInsight wird zusätzlich noch die erstklassige Unterstützung für das Erfassen von Daten aus Azure Event Hubs hinzugefügt. Event Hubs sind der Warteschlangendienst, der in Azure am häufigsten verwendet wird. Da die Unterstützung von Event Hubs im Lieferumfang enthalten ist, ist Spark in HDInsight eine ideale Plattform zum Erstellen einer Echtzeit-Analysepipeline.

##<a name="next-steps"></a>Welche Komponenten sind Bestandteile eines Spark-Clusters?

Spark in HDInsight enthält die folgenden Komponenten, die standardmäßig in den Clustern verfügbar sind.

- [Spark 1.3.1](https://spark.apache.org/docs/1.3.1/). Verfügt über Spark Core, Spark SQL, Spark-Streaming-APIs, GraphX und MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Spark Job Server](https://github.com/spark-jobserver/spark-jobserver)
- [Jupyter Notebook](https://jupyter.org)

Spark in HDInsight verfügt auch über einen [ODBC-Treiber](http://go.microsoft.com/fwlink/?LinkId=616229) für Verbindungen mit Spark-Clustern in HDInsight über BI-Tools wie Microsoft Power BI und Tableau.

##<a name="see-also"></a>Weitere Informationen

* [Schnellstart: Verwenden von Spark in HDInsight mit Zeppelin Notebook zum Durchführen interaktiver Datenanalysen](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Benutzerdefinierte Erstellung eines Apache Spark-Clusters in HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)
* [Übermitteln von Aufträgen an einen Apache Spark-Cluster in Azure HDInsight per Remoteverbindung](hdinsight-apache-spark-job-server.md)


[hdinsight-storage]: ../hdinsight-use-blob-storage/

<!---HONumber=AcomDC_1223_2015-->