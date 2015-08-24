<properties 
	pageTitle="Bereitstellen eines Spark-Clusters unter HDInsight und Verwenden von Spark-SQL über Zeppelin und Jupyter für interaktive Analysen | Microsoft Azure" 
	description="Schritt-für-Schritt-Anleitung zur schnellen Bereitstellung eines Apache Spark-Clusters in HDInsight und Verwendung von Spark-SQL über Zeppelin und Jupyter Notebooks zum Ausführen von interaktiven Abfragen" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="07/19/2015" 
	ms.author="nitinme"/>


# Schnellstart: Bereitstellen von Apache Spark für HDInsight und Ausführen von interaktiven Abfragen per Spark-SQL

Sie erfahren, wie Sie einen Apache Spark-Cluster in HDInsight mit der Option „Quick Create“ bereitstellen und dann die webbasierten [Zeppelin](https://zeppelin.incubator.apache.org) und [Jupyter](https://jupyter.org) Notebooks verwenden, um interaktive Spark-SQL-Abfragen für den Spark-Cluster auszuführen.


   ![Erste Schritte mit Apache Spark in HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.GetStartedFlow.Spark.png "Lernprogramm zu den ersten Schritten mit Apache Spark in HDInsight Dargestellte Schritte: Erstellen eines Speicherkontos, Bereitstellen eines Clusters, Ausführen von Spark-SQL-Anweisungen")

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie über ein Azure-Abonnement verfügen. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="storage"></a>Erstellen eines Azure-Speicherkontos

Bei der Bereitstellung eines HDInsight-Clusters in HDInsight geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blobspeichercontainer aus diesem Konto wird als Standarddateisystem festgelegt. Der HDInsight-Cluster wird standardmäßig in demselben Rechenzentrum bereitgestellt wie das angegebene Speicherkonto. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].


**So erstellen Sie ein Azure-Speicherkonto**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie links unten auf **NEU**, und geben Sie dann die Werte wie in der Abbildung dargestellt ein.

	![Azure-Portal, in dem Sie per „Quick Create“ ein neues Speicherkonto einrichten können](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.StorageAccount.QuickCreate.png "Azure-Portal, in dem Sie per „Quick Create“ ein neues Speicherkonto einrichten können")

>[AZURE.NOTE]Achten Sie darauf, das Speicherkonto an einem Standort zu erstellen, der für den Cluster unterstützt wird.

Wählen Sie das neue Speicherkonto in der Liste aus, und klicken Sie unten auf der Seite auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**. Notieren Sie sich den **PRIMÄREN ZUGRIFFSSCHLÜSSEL** \(oder den **SEKUNDÄREN ZUGRIFFSSCHLÜSSEL** – beide Schlüssel funktionieren\). Sie benötigen diese später im Lernprogramm. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].
	
##<a name="provision"></a>Bereitstellen eines HDInsight Spark-Clusters

In diesem Abschnitt stellen Sie einen HDInsight-Cluster der Version 3.2 bereit, der auf der Spark-Version 1.3.1 basiert. Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

>[AZURE.NOTE]Mit den Schritten in diesem Artikel wird ein Apache Spark-Cluster in HDInsight unter Verwendung grundlegender Konfigurationseinstellungen erstellt. Informationen zu anderen Cluster-Konfigurationseinstellungen \(z. B. die Verwendung von zusätzlichem Speicher, von Azure Virtual Network oder eines Metastore für Hive\) finden Sie unter [Bereitstellen von HDInsight-Clustern mit benutzerdefinierten Optionen](hdinsight-apache-spark-provision-clusters.md).


**So stellen Sie einen Spark-Cluster bereit**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an. 

2. Klicken Sie links unten auf **NEU**, und geben Sie dann die Werte wie in der Abbildung dargestellt ein.

	![Erstellen eines Spark-Clusters in HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.QuickCreateCluster.png "Erstellen eines Spark-Clusters in HDInsight")


##<a name="zeppelin"></a>Ausführen von interaktiven Spark-SQL-Abfragen mit einem Zeppelin Notebook

Nachdem Sie einen Cluster bereitgestellt haben, können Sie ein webbasiertes Zeppelin Notebook verwenden, um interaktive Spark-SQL-Abfragen für den Spark HDInsight-Cluster auszuführen. In diesem Abschnitt verwenden wir eine Beispieldatendatei \(hvac.csv\), die im Cluster standardmäßig verfügbar ist, um interaktive Spark-SQL-Abfragen auszuführen.

>[AZURE.NOTE]Das Notebook, das Sie gemäß der unten angegebenen Anleitung erstellen, ist standardmäßig auch im Cluster verfügbar. Nach dem Starten von Zeppelin wird dieses Notebook als **Zeppelin HVAC-Lernprogramm** angezeigt.

1. Starten Sie das Zeppelin Notebook. Wählen Sie Ihren neu erstellten Spark-Cluster im Azure-Portal, und klicken Sie unten in der Taskleiste des Portals auf **Zeppelin Notebook**. Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Folgen Sie den Anweisungen auf der angezeigten Seite, um das Notebook zu starten.

2. Erstellen Sie ein neues Notebook. Klicken Sie im Headerbereich auf **Notebook**, und wählen Sie die Option **Neue Notiz erstellen**.

	![Erstellen eines neuen Zeppelin Notebooks](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.CreateNewNote.png "Erstellen eines neuen Zeppelin Notebooks")

	Auf derselben Seite sollte unter der Überschrift **Notebook** ein neues Notebook angezeigt werden, dessen Name mit **Notiz XXXXXXXXX** beginnt. Klicken Sie auf das neue Notebook.

3. Klicken Sie auf der Webseite für das neue Notebook auf die Überschrift, und ändern Sie den Namen des Notebooks, wenn Sie dies möchten. Drücken Sie die EINGABETASTE, um die Namensänderung zu speichern. Stellen Sie außerdem sicher, dass im Header des Notebooks in der oberen rechten Ecke der Status **Verbunden** angezeigt wird.

	![Zeppelin Notebook-Status](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.NewNote.Connected.png "Zeppelin Notebook-Status")

4. Laden Sie Beispieldaten in eine temporäre Tabelle. Wenn Sie einen Spark-Cluster in HDInsight bereitstellen, wird die Beispieldatei **hvac.csv** in das zugeordnete Speicherkonto unter **\\HdiSamples\\SensorSampleData\\hvac** kopiert.

	Fügen Sie in den leeren Absatz, der im neuen Notebook standardmäßig erstellt wird, den folgenden Codeausschnitt ein.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Drücken Sie **UMSCHALT+EINGABETASTE**, oder klicken Sie auf die Wiedergabeschaltfläche \(**Play**\) für den Absatz, um den Codeausschnitt auszuführen. Der Status in der rechten Ecke des Absatzes sollte sich entsprechend ändern: BEREIT, AUSSTEHEND, WIRD AUSGEFÜHRT bis zu BEENDET. Die Ausgabe wird unten im Absatz angezeigt. Der Screenshot sieht folgendermaßen aus:

	![Erstellen einer temporären Tabelle aus Rohdaten](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.LoadDataIntoTable.png "Erstellen einer temporären Tabelle aus Rohdaten")

	Sie können auch einen Titel für jeden Absatz angeben. Klicken Sie in der rechten Ecke auf das Symbol **Einstellungen** und dann auf **Titel anzeigen**.

5. Sie können jetzt Spark-SQL-Anweisungen für die **hvac**-Tabelle ausführen. Fügen Sie die folgende Abfrage in einen neuen Absatz ein. Mit der Abfrage werden die Gebäude-ID und der Unterschied zwischen den Ziel- und Ist-Temperaturen für jedes Gebäude an einem bestimmten Datum abgerufen. Drücken Sie **UMSCHALT+EINGABETASTE**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	Mit der **%sql**-Anweisung am Anfang wird das Notebook angewiesen, den Spark-SQL-Interpreter zu verwenden. Sie können die definierten Interpreter im Notebook-Header auf der Registerkarte **Interpreter** anzeigen.

	Im folgenden Screenshot ist die Ausgabe dargestellt.

	![Ausführen einer Spark-SQL-Anweisung mit dem Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery1.png "Ausführen einer Spark-SQL-Anweisung mit dem Notebook")

	 Klicken Sie auf die Anzeigeoptionen \(im Rechteck hervorgehoben\), um für eine Ausgabe zwischen unterschiedlichen Darstellungen zu wechseln. Klicken Sie auf **Einstellungen**, um auszuwählen, wobei es sich in der Ausgabe um den Schlüssel und die Werte handelt. Im obigen Screenshot werden **buildingID** als Schlüssel und der Mittelwert von **temp\_diff** als Wert verwendet.

	
6. Sie können auch Spark-SQL-Anweisungen ausführen, indem Sie die Variablen in der Abfrage verwenden. Der nächste Codeausschnitt zeigt, wie Sie eine Variable \(**Temp**\) in der Abfrage mit den möglichen Werten definieren, die für die Abfrage verwendet werden sollen. Beim ersten Ausführen der Abfrage wird automatisch eine Dropdownliste mit den Werten aufgefüllt, die Sie für die Variable angegeben haben.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Fügen Sie diesen Codeausschnitt in einen neuen Absatz ein, und drücken Sie **UMSCHALT+EINGABETASTE**. Im folgenden Screenshot ist die Ausgabe dargestellt.

	![Ausführen einer Spark-SQL-Anweisung mit dem Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery2.png "Ausführen einer Spark-SQL-Anweisung mit dem Notebook")

	Für nachfolgende Abfragen können Sie einen neuen Wert aus der Dropdownliste auswählen und die Abfrage erneut ausführen. Klicken Sie auf **Einstellungen**, um auszuwählen, wobei es sich in der Ausgabe um den Schlüssel und die Werte handelt. Im obigen Screenshot werden **buildingID** als Schlüssel, der Mittelwert von **temp\_diff** als Wert und **targettemp** als Gruppe verwendet.

7. Starten Sie den Spark-SQL-Interpreter neu, um die Anwendung zu beenden. Klicken Sie oben auf die Registerkarte **Interpreter**, und klicken Sie für den Spark-Interpreter auf **Neu starten**.

	![Neustarten des Zeppelin-Interpreters](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Zeppelin.Restart.Interpreter.png "Neustarten des Zeppelin-Interpreters")

##<a name="jupyter"></a>Ausführen von Spark-SQL-Abfragen mit einem Jupyter Notebook

In diesem Abschnitt verwenden Sie ein Jupyter Notebook, um Spark-SQL-Abfragen für einen Spark-Cluster auszuführen.

>[AZURE.NOTE]Das Notebook, das Sie gemäß der unten angegebenen Anleitung erstellen, ist standardmäßig auch im Cluster verfügbar. Nach dem Starten von Jupyter wird dieses Notebook mit dem Namen **HVACTutorial.ipynb** angezeigt.

1. Starten Sie das Jupyter Notebook. Wählen Sie Ihren Spark-Cluster im Azure-Portal aus, und klicken Sie in der Taskleiste des Portals auf **Jupyter Notebook**. Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.
2. Erstellen Sie ein neues Notebook. Klicken Sie auf **Neu** und dann auf **Python2**.

	![Erstellen eines neuen Jupyter Notebooks](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.CreateNotebook.png "Erstellen eines neuen Jupyter Notebooks")

3. Ein neues Notebook mit dem Namen „Untitled.pynb“ wird erstellt und geöffnet. Klicken Sie oben auf den Namen des Notebooks, und geben Sie einen Anzeigenamen ein.

	![Angeben eines neuen Namens für das Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.Notebook.Name.png "Angeben eines neuen Namens für das Notebook")

4. Importieren Sie die erforderlichen Module, und erstellen Sie die Spark- und SQL-Kontexte. Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Bei jedem Ausführen eines Auftrags in Jupyter wird in der Titelleiste Ihres Webbrowserfensters neben dem Notebook-Titel der Status **\(Busy\)** \(Beschäftigt\) angezeigt. Außerdem sehen Sie in der oberen rechten Ecke einen ausgefüllten Kreis neben dem Text **Python 2**. Wenn der Auftrag abgeschlossen ist, wird ein Kreis ohne Füllung angezeigt.

	 ![Status eines Jupyter Notebook-Auftrags](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Job.Status.png "Status eines Jupyter Notebook-Auftrags")

4. Laden Sie Beispieldaten in eine temporäre Tabelle. Wenn Sie einen Spark-Cluster in HDInsight bereitstellen, wird die Beispieldatei **hvac.csv** in das zugeordnete Speicherkonto unter **\\HdiSamples\\SensorSampleData\\hvac** kopiert.

	Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**. Mit diesem Codeausschnitt werden die Daten in einer temporären Tabelle mit dem Namen **hvac** registriert.


		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = \"6/1/13\"")
		data.show()

5. Nachdem der Auftrag erfolgreich abgeschlossen wurde, wird die folgende Ausgabe angezeigt.

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. Starten Sie den Kernel neu, um die Anwendung zu beenden. Klicken Sie in der oberen Menüleiste auf **Kernel** und **Neu starten**, und klicken Sie in der Aufforderung dann noch einmal auf **Neu starten**.

	![Neustarten des Jupyter-Kernels](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Restart.Kernel.png "Neustarten des Jupyter-Kernels")


##<a name="seealso"></a>Weitere Informationen


* [Übersicht: Apache Spark für Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Bereitstellen von Spark in einem HDInsight-Cluster](hdinsight-apache-spark-provision-clusters.md)
* [Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools.md)
* [Verwenden von Spark in HDInsight zum Erstellen von Machine Learning-Anwendungen](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Verwenden von Spark in HDInsight zum Erstellen von Echtzeit-Streaminganwendungen](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=August15_HO7-->