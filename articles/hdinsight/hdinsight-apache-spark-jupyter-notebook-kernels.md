<properties 
	pageTitle="Verfügbare Kernel für Jupyter Notebooks in HDInsight Spark-Clustern unter Linux | Microsoft Azure" 
	description="Hier erhalten Sie Informationen zu zusätzlichen Jupyter Notebook-Kerneln, die in Spark-Clustern unter HDInsight (Linux) zur Verfügung stehen." 
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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Verfügbare Kernels für Jupyter Notebooks mit Spark-Clustern unter HDInsight (Linux)

Apache Spark-Cluster unter HDInsight (Linux) beinhalten Jupyter Notebooks, die Sie zum Testen Ihrer Anwendungen verwenden können. Standardmäßig verfügt ein Jupyter Notebook über einen Kernel vom Typ **Python2**. HDInsight Spark-Cluster bieten zwei zusätzliche Kernel, die Sie für das Jupyter Notebook verwenden können. Dies sind:

1. **Spark** (für in Scala geschriebene Anwendungen)
2. **PySpark** (für in Python geschriebene Anwendungen)

In diesem Artikel erfahren Sie, wie Sie diese Kernel verwenden und welche Vorteile sie Ihnen bringen.

**Voraussetzungen:**

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (in englischer Sprache).
- Einen Apache Spark-Cluster unter HDInsight (Linux). Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Vorgehensweise zum Verwenden der Kernel 

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.   

2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Quicklinks** und anschließend auf dem Blatt **Cluster Dashboard** auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

	> [AZURE.NOTE] Sie können auch das Jupyter Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres Clusters:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Erstellen Sie ein neues Notebook mit dem neuen Kernel. Klicken Sie auf **Neu** und dann auf **Pyspark** oder **Spark**. Der Spark-Kernel ist für Scala-Anwendungen vorgesehen, der PySpark-Kernel für Python-Anwendung.

	![Erstellen eines neuen Jupyter Notebooks](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Erstellen eines neuen Jupyter Notebooks")

3. Dadurch öffnet sich ein neues Notebook mit dem ausgewählten Kernel.

## Argumente für die Verwendung der neuen Kernels

Die Verwendung der neuen Kernel bringt einige Vorteile mit sich.

1. Bei Verwendung des standardmäßigen Kernels vom Typ **Python2** müssen Sie die Spark-, SQL-, oder Hive-Kontexte festlegen, um mit der Anwendung, die Sie entwickeln, arbeiten zu können. Bei Verwendung der neuen Kernel (**Spark** oder **PySpark**) stehen Ihnen diese Kontexte dagegen standardmäßig zur Verfügung. Diese Kontexte sind:

	* **sc** (Spark-Kontext)
	* **sqlContext** (SQL-Kontext)
	* **hiveContext** (Hive-Kontext)


	Sie müssen also keine Anweisungen wie die folgenden ausführen, um die Kontexte festzulegen:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	Stattdessen können Sie in Ihrer Anwendung direkt die vordefinierten Kontexte verwenden.
	
2. Die Elemente **%sql** und **%hive** können direkt für SQL- bzw. Hive-Abfragen verwendet werden. Dadurch können Sie also beispielsweise direkt (sprich: ohne einleitende Codeanweisungen) einen Ansatz wie den folgenden verwenden:

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## Überlegungen bei der Verwendung der neuen Kernel

Unabhängig vom verwendeten Kernel (Python2, PySpark oder Spark) werden von ausgeführten Notebooks immer Clusterressourcen beansprucht. Da die Kontexte bei einem Python2-Notebook explizit erstellt werden müssen, kann beim Verlassen der Anwendung auch die Beendigung dieser Kontexte erzwungen werden.

Bei PySpark- und Spark-Kernels sind die Kontexte dagegen vorkonfiguriert. Eine erzwungene Beendigung des Kontexts ist daher nicht möglich. Wenn Sie also nur das Notebook verlassen, wird der Kontext unter Umständen weiter ausgeführt und beansprucht Clusterressourcen. Bei PySpark- und Spark-Kernels empfiehlt sich daher die Verwendung der Option zum Schließen und Anhalten aus dem Menü **Datei** des Notebooks. Dadurch wird die Beendigung des Kontexts erzwungen und das Notebook beendet.


## Beispiele

Wenn Sie ein Jupyter Notebook öffnen, sind auf der Stammebene zwei Ordner verfügbar.

* Der Ordner **Python** enthält Beispiele für Notebooks, die den standardmäßigen Kernel vom Typ **Python2** verwenden.
* Der Ordner **Scala** enthält Beispiele für Notebooks, die den neuen Kernel vom Typ **Spark** verwenden.

Wenn Sie in beiden Ordnern das gleiche Notebook (etwa **READ ME FIRST – Learn the Basics of Spark on HDInsight**) öffnen, sehen Sie, dass das Python2-Notebook immer zuerst die erforderlichen Kontexte festlegt, während das Spark-Notebook einfach die vorkonfigurierten Kontexte verwendet.

## Feedback

Die neuen Kernel sind noch ziemlich neu und werden im Laufe der Zeit weiterentwickelt. Im Zuge dieser Entwicklung ändern sich unter Umständen auch APIs. Wir freuen uns über Ihr Feedback zur Verwendung der neuen Kernel. Dies hilft uns bei der Gestaltung der endgültigen Kernelversion. Kommentare/Feedback können Sie im Kommentarbereich am Ende dieses Artikels hinterlassen.


## <a name="seealso"></a>Weitere Informationen


* [Übersicht: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md)

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

### Verwalten von Ressourcen

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->