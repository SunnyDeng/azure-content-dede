<properties 
	pageTitle="Verfügbare Kernel für Jupyter Notebooks in HDInsight Spark-Clustern unter Linux | Microsoft Azure" 
	description="Hier erhalten Sie Informationen zu zusätzlichen Jupyter Notebook-Kerneln, die in Spark-Clustern unter HDInsight (Linux) zur Verfügung stehen." 
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
	ms.date="02/17/2016" 
	ms.author="nitinme"/>


# Verfügbare Kernels für Jupyter Notebooks mit Spark-Clustern unter HDInsight (Linux)

Apache Spark-Cluster unter HDInsight (Linux) beinhalten Jupyter Notebooks, die Sie zum Testen Ihrer Anwendungen verwenden können. Standardmäßig verfügt ein Jupyter Notebook über einen Kernel vom Typ **Python2**. Ein Kernel ist ein Programm, das ausgeführt wird und Ihren Code interpretiert. HDInsight Spark-Cluster bieten zwei zusätzliche Kernel, die Sie für das Jupyter Notebook verwenden können. Dies sind:

1. **PySpark** (für in Python geschriebene Anwendungen)
2. **Spark** (für in Scala geschriebene Anwendungen)

In diesem Artikel erfahren Sie, wie Sie diese Kernel verwenden und welche Vorteile sie Ihnen bringen.

**Voraussetzungen:**

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (in englischer Sprache).
- Einen Apache Spark-Cluster unter HDInsight (Linux). Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

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

1. **Voreingestellte Kontexte**. Bei Verwendung des standardmäßigen Kernels vom Typ **Python2**, der mit Jupyter-Notebooks verfügbar ist, müssen Sie die Spark-, SQL-, oder Hive-Kontexte festlegen, um mit der Anwendung, die Sie entwickeln, arbeiten zu können. Bei Verwendung der neuen Kernel (**PySpark** oder **Spark**) stehen Ihnen diese Kontexte dagegen standardmäßig zur Verfügung. Diese Kontexte sind:

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
	
2. **Zellen-Magics**. Der PySpark-Kernel bietet einige vordefinierte „Magics“, spezielle Befehle, die Sie mit `%%` (z. B. `%%MAGIC` <args>) aufrufen können. Der Magic-Befehl muss das erste Wort in einer Codezelle sein und ermöglicht mehrere Inhaltszeilen. Das Magic-Wort sollte das erste Wort in der Zelle sein. Beliebige Hinzufügungen vor dem Magic, auch Kommentare, verursachen einen Fehler. Weitere Informationen zu Magics finden Sie [hier](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

	In der folgenden Tabelle sind die verschiedenen über den Kernel verfügbaren Magics aufgeführt.

	| Magic | Beispiel | Beschreibung |
	|-----------|---------------------------------|--------------|
	| help | `%%help` | Generiert eine Tabelle mit allen verfügbaren Magics mit Beispiel und Beschreibung. |
	| info | `%%info` | Gibt Sitzungsinformationen für den aktuellen Livy-Endpunkt heraus. |
	| Konfigurieren | `%%configure -f {"executorMemory": "1000M", "executorCores": 4`} | Konfiguriert die Parameter für das Erstellen einer neuen Sitzung. Das Force-Flag (-f) ist obligatorisch, wenn bereits eine Sitzung erstellt wurde, und die Sitzung gelöscht und neu erstellt wird. Unter [„Request Body“ in „POST /sessions“ für Livy](https://github.com/cloudera/livy#request-body) finden Sie eine Liste der gültigen Parameter. Parameter müssen als JSON-Zeichenfolge übergeben werden. |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | Führt eine SQL-Abfrage für sqlContext aus. Wenn der Parameter `-o` übergeben wird, wird das Ergebnis der Abfrage im %%local-Python-Kontext als [Pandas](http://pandas.pydata.org/)-Datenrahmen beibehalten. |
	| hive | `%%hive -o <variable name>`<br> `SHOW TABLES` | Führt eine Hive-Abfrage für hivelContext aus. Wenn der Parameter -o übergeben wird, wird das Ergebnis der Abfrage im %%local-Python-Kontext als [Pandas](http://pandas.pydata.org/)-Datenrahmen beibehalten. |
	| local | `%%local`<br>`a=1` | Der gesamte Code in den folgenden Zeilen wird lokal ausgeführt. Der Code muss gültiger Python-Code sein. |
	| Protokolle | `%%logs` | Gibt die Protokolle für die aktuelle Livy-Sitzung aus. |
	| delete | `%%delete -f -s <session number>` | Löscht eine bestimmte Sitzung des aktuellen Livy-Endpunkts. Beachten Sie, dass Sie die Sitzung, die für den Kernel selbst initiiert wird, nicht löschen können. |
	| cleanup | `%%cleanup -f` | Löscht alle Sitzungen für den aktuellen Livy-Endpunkt, einschließlich dieser Notebook-Sitzung. Das Force-Flag „-f“ ist obligatorisch |

3. **Automatische Visualisierung**. Der **PySpark**-Kernel visualisiert automatisch die Ausgabe der Hive- und SQL-Abfragen. Sie können zwischen verschiedenen Arten von Visualisierungen wählen, inklusive Tabelle, Kreis-, Linie-, Flächen- und Balkendiagramm.


## Überlegungen bei der Verwendung der neuen Kernel

Unabhängig vom verwendeten Kernel (Python2, PySpark oder Spark) werden von ausgeführten Notebooks immer Clusterressourcen beansprucht. Da die Kontexte bei einem Python2-Notebook explizit erstellt werden müssen, kann beim Verlassen der Anwendung auch die Beendigung dieser Kontexte erzwungen werden.

Bei PySpark- und Spark-Kernels sind die Kontexte dagegen vorkonfiguriert. Eine erzwungene Beendigung des Kontexts ist daher nicht möglich. Wenn Sie also nur das Notebook verlassen, wird der Kontext unter Umständen weiter ausgeführt und beansprucht Clusterressourcen. Bei PySpark- und Spark-Kernels empfiehlt sich daher die Verwendung der Option zum Schließen und Anhalten aus dem Menü **Datei** des Notebooks. Dadurch wird die Beendigung des Kontexts erzwungen und das Notebook beendet.


## Beispiele

Wenn Sie ein Jupyter Notebook öffnen, sind auf der Stammebene zwei Ordner verfügbar.

* Der Ordner **PySpark** enthält Beispiele für Notebooks, die den neuen Kernel vom Typ **Python** verwenden.
* Der Ordner **Scala** enthält Beispiele für Notebooks, die den neuen Kernel vom Typ **Spark** verwenden.

Sie können das Notebook **00 - [READ ME FIRST] Spark Magic Kernel Features** im Ordner **PySpark** oder **Spark** öffnen, um Informationen zu den verschiedenen verfügbaren Magics zu erhalten. Sie können auch anhand der anderen in den beiden Ordnern verfügbaren Beispiel-Notebooks erfahren, wie Sie verschiedene Szenarien der Verwendung von Jupyter-Notebooks mit HDInsight Spark-Clustern realisieren können.

## Feedback

Die neuen Kernels befinden sich in der Entwicklungsphase und werden mit der Zeit ausreifen. Im Zuge dieser Entwicklung ändern sich unter Umständen auch APIs. Wir freuen uns über Ihr Feedback zur Verwendung der neuen Kernel. Dies hilft uns bei der Gestaltung der endgültigen Kernelversion. Kommentare/Feedback können Sie im Kommentarbereich am Ende dieses Artikels hinterlassen.


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

<!---HONumber=AcomDC_0224_2016-->