<properties 
	pageTitle="Verwenden von Apache Spark zum Erstellen von Machine Learning-Anwendungen für HDInsight | Azure" 
	description="Schritt-für-Schritt-Anleitung zur Verwendung von Notebooks mit Apache Spark zum Erstellen von Machine Learning-Anwendungen" 
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
	ms.topic="article" 
	ms.date="07/19/2015" 
	ms.author="nitinme"/>


# Erstellen von Machine Learning-Anwendungen mit Apache Spark für Azure HDInsight

Sie erfahren, wie Sie eine Machine Learning-Anwendung mit einem Apache Spark-Cluster in HDInsight erstellen. In diesem Artikel wird beschrieben, wie Sie das Jupyter Notebook für den Cluster zum Erstellen und Testen unserer Anwendung verwenden. Für die Anwendung werden die HVAC.csv-Beispieldaten genutzt, die standardmäßig auf allen Clustern verfügbar sind.

**Voraussetzungen:**

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Einen Apache Spark-Cluster. Eine Anleitung finden Sie unter [Bereitstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-provision-clusters.md). 

##<a name="data"></a>Anzeigen der Daten

Bevor wir mit dem Erstellen der Anwendung beginnen, müssen wir uns zuerst mit der Struktur der Daten und der Art der Analyse vertraut machen, die wir für die Daten durchführen.

In diesem Artikel verwenden wir die Beispieldatendatei **HVAC.csv**, die für alle HDInsight-Cluster standardmäßig unter **\\HdiSamples\\SensorSampleData\\hvac** verfügbar ist. Laden Sie die CSV-Datei herunter, und öffnen Sie sie, um eine Momentaufnahme der Daten zu erhalten.

![HVAC-Datenmomentaufnahmen](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.png "Momentaufnahme der HVAC-Daten")

Die Daten zeigen die Zieltemperatur und die Ist-Temperatur eines Gebäudes an, in dem HVAC-Systeme installiert sind. Angenommen, die Spalte **System** enthält die System-ID und die Spalte **SystemAge** enthält eine Angabe in Jahren, wie lange das HVAC-System im Gebäude bereits verwendet wird.

Wir sagen anhand dieser Daten vorher, ob es in einem Gebäude basierend auf der Zieltemperatur zu warm oder zu kalt ist. Hierfür werden die System-ID und das Alter des Systems verwendet.

##<a name="app"></a>Schreiben einer Machine Learning-Anwendung mit Spark MLlib

1. Starten Sie das [Jupyter](https://jupyter.org) Notebook. Wählen Sie Ihren Spark-Cluster im Azure-Portal aus, und klicken Sie in der Taskleiste des Portals auf **Jupyter Notebook**. Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.

2. Erstellen Sie ein neues Notebook. Klicken Sie auf **Neu** und dann auf **Python2**.

	![Erstellen eines neuen Jupyter Notebooks](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.CreateNotebook.png "Erstellen eines neuen Jupyter Notebooks")

3. Ein neues Notebook mit dem Namen „Untitled.pynb“ wird erstellt und geöffnet. Klicken Sie oben auf den Namen des Notebooks, und geben Sie einen Anzeigenamen ein.

	![Angeben eines neuen Namens für das Notebook](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.Notebook.Name.png "Angeben eines neuen Namens für das Notebook")

3. Beginnen Sie mit der Erstellung Ihrer Machine Learning-Anwendung. In dieser Anwendung verwenden wir eine Spark ML-Pipeline, um eine Dokumentklassifizierung durchzuführen. In der Pipeline teilen wir das Dokument in Wörter auf, konvertieren die Wörter in einen numerischen Featurevektor und erstellen dann mit den Featurevektoren und Beschriftungen ein Vorhersagemodell.

	Um mit der Erstellung der Anwendung zu beginnen, importieren Sie zuerst die erforderlichen Module und weisen der Anwendung Ressourcen zu. Fügen Sie in die leere Zelle im neuen Notebook den folgenden Codeausschnitt ein, und drücken Sie **UMSCHALT+EINGABETASTE**.


		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row, SQLContext
		
		import os
		import sys
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		from pyspark.mllib.classification import LogisticRegressionWithSGD
		from pyspark.mllib.regression import LabeledPoint
		from numpy import array
		
		# Assign resources to the application
		conf = SparkConf()
		conf.setMaster('spark://headnodehost:7077')
		conf.setAppName('pysparkregression')
		conf.set("spark.cores.max", "4")
		conf.set("spark.executor.memory", "4g")
		
		sc = SparkContext(conf=conf)
		sqlContext = SQLContext(sc)

	Bei jedem Ausführen eines Auftrags in Jupyter wird in der Titelleiste Ihres Webbrowserfensters neben dem Notebook-Titel der Status **(Busy)** (Beschäftigt) angezeigt. Außerdem sehen Sie in der oberen rechten Ecke einen ausgefüllten Kreis neben dem Text **Python 2**. Wenn der Auftrag abgeschlossen ist, wird ein Kreis ohne Füllung angezeigt.

	 ![Status eines Jupyter Notebook-Auftrags](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Job.Status.png "Status eines Jupyter Notebook-Auftrags")
 
4. Sie müssen die Daten (hvac.csv) jetzt laden, analysieren und zum Trainieren des Modells verwenden. Definieren Sie hierzu eine Funktion, mit der überprüft wird, ob die Ist-Temperatur des Gebäudes höher als die Zieltemperatur ist. Wenn die Ist-Temperatur höher ist, wird angegeben, dass es in dem Gebäude zu warm ist (Wert**1,0**). Wenn die Ist-Temperatur niedriger ist, wird angegeben, dass es in dem Gebäude zu kalt ist (Wert **0,0**).

	Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.

		
		# List the structure of data for better understanding. Becuase the data will be
		# loaded as an array, this structure makes it easy to understand what each element
		# in the array corresponds to

		# 0 Date
		# 1 Time
		# 2 TargetTemp
		# 3 ActualTemp
		# 4 System
		# 5 SystemAge
		# 6 BuildingID

		LabeledDocument = Row("BuildingID", "SystemInfo", "label")

		# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
		
		def parseDocument(line):
    		values = [str(x) for x in line.split(',')]
    		if (values[3] > values[2]):
        		hot = 1.0
    		else:
        		hot = 0.0        
    
    		textValue = str(values[4]) + " " + str(values[5])
    
    		return LabeledDocument((values[6]), textValue, hot)

		# Load the raw HVAC.csv file, parse it using the function
		data = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

		documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
		training = documents.toDF()


5. Konfigurieren Sie die Spark Machine Learning-Pipeline, die drei Phasen umfasst: Tokenizer, hashingTF und lr. Weitere Informationen dazu, was eine Pipeline ist und wie sie funktioniert, finden Sie unter <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark Machine Learning-Pipeline</a>.

	Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.

		tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
		hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
		lr = LogisticRegression(maxIter=10, regParam=0.01)
		pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Passen Sie die Pipeline an das Schulungsdokument an. Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.

		model = pipeline.fit(training)

7. Überprüfen Sie das Schulungsdokument, um Ihre Fortschritte in Bezug auf die Anwendung zu ermitteln. Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.

		training.show()

	Sie sollten eine Ausgabe erhalten, die Folgendem ähnelt:

		BuildingID SystemInfo label
		4          13 20      0.0  
		17         3 20       0.0  
		18         17 20      1.0  
		15         2 23       0.0  
		3          16 9       1.0  
		4          13 28      0.0  
		2          12 24      0.0  
		16         20 26      1.0  
		9          16 9       1.0  
		12         6 5        0.0  
		15         10 17      1.0  
		7          2 11       0.0  
		15         14 2       1.0  
		6          3 2        0.0  
		20         19 22      0.0  
		8          19 11      0.0  
		6          15 7       0.0  
		13         12 5       0.0  
		4          8 22       0.0  
		7          17 5       0.0

	Wechseln Sie zurück, und überprüfen Sie die Ausgabe gegenüber der CSV-Rohdatei. Die erste Zeile der CSV-Datei enthält beispielsweise folgende Daten:

	![HVAC-Datenmomentaufnahmen](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.First.Row.png "Momentaufnahme der HVAC-Daten")

	Beachten Sie, dass die Ist-Temperatur unterhalb der Zieltemperatur liegt. Im Gebäude ist es also zu kalt. In der Schulungsausgabe lautet der Wert **label** in der ersten Zeile daher **0,0**. Dies bedeutet, dass es im Gebäude nicht zu warm ist.

8.  Bereiten Sie ein Dataset vor, für das das Schulungsmodell ausgeführt werden kann. Hierzu übergeben wir eine System-ID und ein Systemalter (in der Schulungsausgabe als **SystemInfo** bezeichnet). Mit dem Modell wird dann vorhergesagt, ob es im Gebäude mit der jeweiligen System-ID und dem Systemalter wärmer (1,0) oder kälter (0,0) wäre.

	Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.
		
		# SystemInfo here is a combination of system ID followed by system age
		Document = Row("id", "SystemInfo")
		test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
    		.map(lambda x: Document(*x)).toDF() 

9. Treffen Sie als Letztes die Vorhersagen für die Testdaten. Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.

		# Make predictions on test documents and print columns of interest
		prediction = model.transform(test)
		selected = prediction.select("SystemInfo", "prediction", "probability")
		for row in selected.collect():
		    print row

10. Es sollte eine Ausgabe angezeigt werden, die Folgendem ähnelt:

		Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
		Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
		Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
		Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
		Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
		Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

	In der ersten Zeile der Vorhersage können Sie sehen, dass das Gebäude für ein HVAC-System mit ID 20 und einem Systemalter von 25 Jahren zu warm ist (**prediction=1.0**). Der erste Wert für DenseVector (0.49999) entspricht der Vorhersage 0,0, und der zweite Wert (0.5001) entspricht der Vorhersage 1,0. Obwohl der zweite Wert in der Ausgabe nur unwesentlich höher ist, zeigt das Modell **prediction=1.0** an.

11. Sie können das Notebook jetzt beenden, indem Sie den Kernel neu starten. Klicken Sie in der oberen Menüleiste auf **Kernel** und **Neu starten**, und klicken Sie in der Aufforderung dann noch einmal auf **Neu starten**.

	![Neustarten des Jupyter-Kernels](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Restart.Kernel.png "Neustarten des Jupyter-Kernels")
	  	   

##<a name="anaconda"></a>Verwenden der Anaconda-scikit-learn-Bibliothek für Machine Learning

Apache Spark-Cluster unter HDInsight enthalten Anaconda-Bibliotheken. Dazu gehört auch die **scikit-learn**-Bibliothek für Machine Learning. Außerdem enthält die Bibliothek verschiedene Datasets, mit denen Sie Beispielanwendungen direkt über ein Jupyter Notebook erstellen können. Beispiele zur Verwendung der scikit-learn-Bibliothek finden Sie unter [http://scikit-learn.org/stable/auto\_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Weitere Informationen

* [Übersicht: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Bereitstellen von Spark in einem HDInsight-Cluster](hdinsight-apache-spark-provision-clusters.md)
* [Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools.md)
* [Verwenden von Spark in HDInsight zum Erstellen von Echtzeit-Streaminganwendungen](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-storage]: ../hdinsight-use-blob-storage/


[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=August15_HO6-->