<properties
	pageTitle="Erstellen eines Spark-Clusters unter HDInsight (Linux) und Verwenden von Spark-SQL über Jupyter für interaktive Analysen | Microsoft Azure"
	description="Schritt-für-Schritt-Anleitungen zur schnellen Erstellung eines Apache Spark-Clusters in HDInsight und Verwendung von Spark-SQL über Jupyter-Notebooks zum Ausführen von interaktiven Abfragen"
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
	ms.date="03/10/2016"
	ms.author="nitinme"/>


# Erste Schritte: Erstellen eines Apache Spark-Clusters für Azure HDInsight (Linux) und Ausführen von interaktiven Abfragen per Spark-SQL

Hier erfahren Sie, wie Sie einen Apache Spark-Cluster in HDInsight erstellen und dann ein [Jupyter](https://jupyter.org)-Notebook verwenden, um interaktive Spark-SQL-Abfragen für den Spark-Cluster auszuführen.

   ![Erste Schritte mit Apache Spark in HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Lernprogramm zu den ersten Schritten mit Apache Spark in HDInsight Dargestellte Schritte: Erstellen eines Speicherkontos, Erstellen eines Clusters, Ausführen von Spark-SQL-Anweisungen")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**Voraussetzungen:**

- **Ein Azure-Abonnement**. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie über ein Azure-Abonnement verfügen. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Ein Secure Shell-Client (SSH)**: Linux-, Unix und OS X-Systeme stellen einen SSH-Client über den Befehl `ssh` bereit. Für Windows-Systeme wird [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) empfohlen.
    
- **Secure Shell-Schlüssel (SSH) (optional)**: Sie können das SSH-Konto, mit dem die Verbindung zum Cluster hergestellt wird, mit einem Kennwort oder einem öffentlichen Schlüssel sichern. Die Verwendung eines Kennworts ermöglicht Ihnen einen schnellen Einstieg. Verwenden Sie diese Option, wenn Sie ein Cluster schnell erstellen und einige Testaufträge ausführen möchten. Die Verwendung eines Schlüssels bietet höhere Sicherheit. Allerdings sind hierfür zusätzliche Schritte zur Einrichtung erforderlich. Sie sollten diesen Ansatz verwenden, wenn Sie einen Produktionscluster erstellen. In diesem Artikel verwenden wir ein Kennwort. Anweisungen zum Erstellen und Verwenden von SSH-Schlüsseln mit HDInsight finden Sie in den folgenden Artikeln:

	-  Auf einem Linux-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
	-  Auf einem Windows-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).


## Erstellen von Spark-Clustern

In diesem Abschnitt erstellen Sie mithilfe einer Azure ARM-Vorlage einen HDInsight-Cluster der Version 3.3 (Spark-Version 1.5.1). Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md). Andere Methoden zur Erstellung von Clustern finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicken Sie auf die folgende Abbildung, um eine ARM-Vorlage im Azure-Portal zu öffnen.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/de-DE/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Die ARM-Vorlage befindet sich in einem öffentlichen Blobcontainer, **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*.
   
2. Geben Sie auf dem Blatt "Parameter" Folgendes ein:

    - **ClusterName**: Geben Sie einen Namen für den Hadoop-Cluster ein, den Sie erstellen möchten.
    - **Cluster-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet „admin“.
    - **SSH-Benutzername und -Kennwort**.
    
    Bitte notieren Sie diese Werte. Sie werden diese später im Lernprogramm benötigen.

    > [AZURE.NOTE] SSH wird verwendet, um remote über eine Befehlszeile auf den HDInsight-Cluster zuzugreifen. Der Benutzername und das Kennwort, die Sie hier verwenden, dienen zum Herstellen der Verbindung mit dem Cluster über SSH. Außerdem muss der SSH-Benutzername eindeutig sein, da mit ihm auf allen HDInsight-Clusterknoten ein Benutzerkonto erstellt wird. Die folgenden Namen sind einige der Namen, die für die Verwendung durch Dienste auf dem Cluster reserviert sind und daher nicht als SSH-Benutzername verwendet werden können:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	> Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	> * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. Klicken Sie auf **OK**, um die Parameter zu speichern.

4\. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf das Dropdownfeld **Ressourcengruppe** und dann auf **Neu**, um eine neue Ressourcengruppe zu erstellen. Die Ressourcengruppe ist ein Container, in dem der Cluster, das abhängige Speicherkonto und andere verknüpfte Ressourcen gruppiert werden.

5\. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.

6\. Klicken Sie auf **Erstellen**. Daraufhin wird eine neue Kachel mit der Bezeichnung "Bereitstellung für Vorlagenbereitstellung wird gesendet" angezeigt. Das Erstellen des Clusters und der SQL-Datenbank dauert ca. 20 Minuten.



## Ausführen von Spark-SQL-Abfragen mit einem Jupyter Notebook

In diesem Abschnitt verwenden Sie Jupyter Notebook, um Spark-SQL-Abfragen für einen Spark-Cluster auszuführen. Standardmäßig verfügt ein Jupyter Notebook über einen Kernel vom Typ **Python2**. HDInsight Spark-Cluster bieten zwei zusätzliche Kernel, die Sie für das Jupyter Notebook verwenden können. Dies sind:

* **PySpark** (für in Python geschriebene Anwendungen)
* **Spark** (für in Scala geschriebene Anwendungen)

In diesem Artikel verwenden Sie den PySpark-Kernel. Im Artikel [In Jupyter-Notebooks verfügbare Kernel mit Spark-HDInsight-Clustern](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) finden Sie ausführliche Informationen über die Vorteile der Verwendung des PySpark-Kernels. Zwei der wichtigsten Vorteile des PySpark-Kernels sind jedoch:

* Sie müssen keine Kontexte für Spark, SQL und Hive festlegen. Diese werden automatisch für Sie festgelegt.
* Sie können verschiedene Zellen-Magics (z. B. %%sql oder %%hive) verwenden, um die SQL- oder Hive-Abfragen ohne vorherige Codeausschnitte direkt auszuführen.
* Die Ausgabe für SQL- oder Hive-Abfragen wird automatisch angezeigt.

### Erstellen von Jupyter Notebook mit PySpark-Kernel 

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.   

2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Quicklinks** und anschließend auf dem Blatt **Cluster Dashboard** auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

	> [AZURE.NOTE] Sie können auch das Jupyter Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres Clusters:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Erstellen Sie ein neues Notebook. Klicken Sie auf **Neu** und dann auf **PySpark**.

	![Erstellen eines neuen Jupyter Notebooks](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Erstellen eines neuen Jupyter Notebooks")

3. Ein neues Notebook mit dem Namen „Untitled.pynb“ wird erstellt und geöffnet. Klicken Sie oben auf den Namen des Notebooks, und geben Sie einen Anzeigenamen ein.

	![Angeben eines neuen Namens für das Notebook](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Angeben eines neuen Namens für das Notebook")

4. Da Sie ein Notebook mit dem PySpark-Kernel erstellt haben, müssen Sie keine Kontexte explizit erstellen. Die Spark-, SQL- und Hive-Kontexte werden automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen. Sie können zunächst die Typen importieren, die für dieses Szenario erforderlich sind. Fügen Sie dazu den folgenden Codeausschnitt in eine Zelle ein, und drücken Sie **UMSCHALT+EINGABE**.

		from pyspark.sql.types import *
		
	Bei jedem Ausführen eines Auftrags in Jupyter wird in der Titelleiste Ihres Webbrowserfensters neben dem Notebooktitel der Status **(Beschäftigt)** angezeigt. Außerdem sehen Sie in der rechten oberen Ecke einen ausgefüllten Kreis neben dem Text **PySpark**. Wenn der Auftrag abgeschlossen ist, wird ein Kreis ohne Füllung angezeigt.

	 ![Status eines Jupyter Notebook-Auftrags](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Status eines Jupyter Notebook-Auftrags")

4. Laden Sie Beispieldaten in eine temporäre Tabelle. Wenn Sie einen Spark-Cluster in HDInsight erstellen, wird die Beispieldatendatei **hvac.csv** in das zugeordnete Speicherkonto unter **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac** kopiert.

	Fügen Sie den folgenden Beispielcode in eine leere Zelle ein, und drücken Sie UMSCHALT+EINGABETASTE. Mit diesem Beispielcode werden die Daten in einer temporären Tabelle mit dem Namen **hvac** registriert.

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. Da Sie einen PySpark-Kernel verwenden, können Sie jetzt direkt eine SQL-Abfrage für die temporäre Tabelle **hvac**, die Sie gerade mit der `%%sql`-Magic erstellt haben, ausführen. Weitere Informationen zur `%%sql`-Magic sowie anderen Magics in Verbindung mit dem PySpark-Kernel finden Sie unter [Verfügbare Kernels für Jupyter Notebooks mit Spark-Clustern unter HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13")

5. Nachdem der Auftrag erfolgreich abgeschlossen wurde, wird die folgende tabellarische Ausgabe standardmäßig angezeigt.

 	![Tabellenausgabe des Abfrageergebnisses](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Tabellenausgabe des Abfrageergebnisses")

	Sie können die Ergebnisse auch in anderen Visualisierungen anzeigen. Beispielsweise würde ein Bereichsdiagramm für dieselbe Ausgabe wie folgt aussehen.

	![Bereichsdiagramm des Abfrageergebnisses](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Bereichsdiagramm des Abfrageergebnisses")


6. Nach Ausführung der Anwendung empfiehlt es sich, das Notebook herunterzufahren, um die Ressourcen freizugeben. Klicken Sie hierzu im Menü **Datei** des Notebooks auf die Option zum Schließen und Anhalten. Dadurch wird das Notebook heruntergefahren und geschlossen.

##Löschen des Clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Weitere Informationen


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

* [Verfügbare Kernels für Jupyter-Notebooks im Spark-Cluster für HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Verwalten von Ressourcen

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

### Bekannte Probleme

* [Bekannte Probleme von Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-known-issues.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0316_2016-->