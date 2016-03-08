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
	ms.topic="get-started-article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>

# Übersicht: Apache Spark für Azure HDInsight (Linux)
 
<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von großen Datenmengen zu steigern. Das Spark-Verarbeitungsmodul ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank seiner arbeitsspeicherinternen Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen. Spark ist auch mit Azure-BLOB-Speicher (WASB) kompatibel, sodass Ihre vorhandenen Daten, die in Azure gespeichert sind, leicht per Spark verarbeitet werden können.

Wenn Sie einen Spark-Cluster in HDInsight erstellen, erstellen Sie damit Azure-Serverressourcen, für die Spark installiert und konfiguriert ist. Es dauert nur ungefähr zehn Minuten, einen Spark-Cluster in HDInsight zu erstellen. Die zu verarbeitenden Daten werden im Azure-BLOB-Speicher gespeichert. Weitere Informationen finden Sie unter [Verwenden von Azure-BLOB-Speicher mit HDInsight][hdinsight-storage].

![Apache Spark für Azure HDInsight](./media/hdinsight-apache-spark-overview/hdispark.architecture.png "Apache Spark für Azure HDInsight")


**Möchten Sie mit der Verwendung von Apache Spark für Azure HDInsight beginnen?** Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Spark-Clusters unter HDInsight (Linux) und Ausführen von Beispielanwendungen mit Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Eine Liste der bekannten Probleme und Einschränkungen in der aktuellen Version finden Sie unter [Bekannte Probleme von Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).


## Warum sollten Sie Spark für Azure HDInsight verwenden? 

Azure HDInsight umfasst einen vollständig verwalteten Spark-Dienst. Vorteile der Verwendung von Spark für HDInsight:

| Funktion | Beschreibung |
|-------------------------------------|-------------------|
| Einfache Erstellung | Sie können einen neuen Spark-Cluster unter HDInsight in wenigen Minuten erstellen, indem Sie das Azure-Verwaltungsportal, Azure PowerShell oder das HDInsight .NET SDK verwenden. Weitere Informationen finden Sie unter [Erste Schritte mit Spark-Clustern in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Einfache Bedienung | Spark in HDInsight-Clustern enthält vorkonfigurierte Jupyter Notebooks. Diese können Sie für die interaktive Datenverarbeitung und -visualisierung verwenden. Die URL dafür lautet https://CLUSTERNAME.azurehdinsight.net/jupyter. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres Spark HDInsight-Clusters.|
| REST-APIs | Spark in HDInsight umfasst [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), einen auf der REST-API basierenden Spark-Auftragsserver, mit dem Benutzer Aufträge per Remotezugriff senden und überwachen können. |
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

[Lernprogramm anzeigen](hdinsight-apache-spark-use-bi-tools.md)

Bei Apache Spark in HDInsight werden Daten in Azure-BLOBs gespeichert. Experten und Entscheidungsträger in Unternehmen können diese Daten analysieren und Berichte damit erstellen und Microsoft Power BI verwenden, um aus den analysierten Daten interaktive Berichte anzufertigen. Analysten können mit unstrukturierten oder teilweise stukturierten Daten in Azure Storage beginnen, mit Notebooks ein Schema für die Daten definieren und dann mit Microsoft Power BI Datenmodelle erstellen. Spark in HDInsight unterstützt auch eine Reihe von BI-Drittanbietertools, z. B. Tableau, Qlikview und SAP Lumira. Daher ist es eine ideale Plattform für Datenanalysten, Experten und Entscheidungsträger.

### Iteratives Machine Learning

[Tutorial ansehen: Vorhersage von Gebäudetemperaturen mithilfe von HVAC-Daten](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Tutorial ansehen: Vorhersage von Lebensmittelüberwachungsergebnissen](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark verfügt über [MLlib](http://spark.apache.org/mllib/), eine Machine Learning-Bibliothek, die auf Spark aufbaut. Außerdem umfasst Spark für HDInsight Anaconda, eine Python-Distribution mit vielen verschiedenen Paketen für Machine Learning. Wenn Sie dies mit der integrierten Unterstützung für Jupyter Notebooks kombinieren, verfügen Sie über eine hochmoderne Umgebung zur Erstellung von Machine Learning-Anwendungen.

### Streaming und Echtzeit-Datenanalysen

[Lernprogramm anzeigen](hdinsight-apache-spark-eventhub-streaming.md)

Die Echtzeit-Datenanalyse wird für Szenarien eingesetzt, die von der Reduzierung des Zeitaufwands für Einblicke in Daten (durch die sofortige Verarbeitung von Daten nach dem Eintreffen) bis zur Erstellung einer echten Streaminglösung reichen. Spark in HDInsight bietet umfassende Unterstützung für die Erstellung von Echtzeit-Analyselösungen. Spark verfügt zwar bereits über Connectors zum Erfassen von Daten aus den unterschiedlichsten Quellen, z. B. Kafka, Flume, Twitter, ZeroMQ oder TCP-Sockets, aber mit Spark in HDInsight wird zusätzlich noch die erstklassige Unterstützung für das Erfassen von Daten aus Azure Event Hubs hinzugefügt. Event Hubs sind der Warteschlangendienst, der in Azure am häufigsten verwendet wird. Da die Unterstützung von Event Hubs im Lieferumfang enthalten ist, ist Spark in HDInsight eine ideale Plattform zum Erstellen einer Echtzeit-Analysepipeline.

##<a name="next-steps"></a>Welche Komponenten sind Bestandteile eines Spark-Clusters?

Spark in HDInsight enthält die folgenden Komponenten, die standardmäßig in den Clustern verfügbar sind.

- [Spark Core](https://spark.apache.org/docs/1.5.1/). Umfasst Spark Core, Spark SQL, Spark-Streaming-APIs, GraphX und MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Jupyter Notebook](https://jupyter.org)

Spark in HDInsight verfügt auch über einen [ODBC-Treiber](http://go.microsoft.com/fwlink/?LinkId=616229) für Verbindungen mit Spark-Clustern in HDInsight über BI-Tools wie Microsoft Power BI und Tableau.

## Wo beginne ich?

Beginnen Sie mit der Erstellung eines Spark-Clusters unter HDInsight (Linux). Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Spark-Clusters unter HDInsight (Linux) und Ausführen von Beispielanwendungen mit Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

## Nächste Schritte

### Szenarios

* [Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools.md)

* [Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark-Streaming: Erstellen von Echtzeitstreaminganwendungen mithilfe von Spark in HDInsight](hdinsight-apache-spark-eventhub-streaming.md)

* [Websiteprotokollanalyse mithilfe von Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Erstellen und Ausführen von Anwendungen

* [Erstellen einer eigenständigen Anwendung mit Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Remoteausführung von Aufträgen in einem Spark-Cluster mithilfe von Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Tools und Erweiterungen

* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Verwenden von Zeppelin-Notebooks mit einem Spark-Cluster in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Verfügbare Kernels für Jupyter-Notebooks im Spark-Cluster für HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Verwalten von Ressourcen

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

<!---HONumber=AcomDC_0302_2016-->