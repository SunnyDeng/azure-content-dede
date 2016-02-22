<properties 
	pageTitle="Verwenden von BI-Tools mit Apache Spark für Azure HDInsight | Microsoft Azure" 
	description="Schritt-für-Schritt-Anleitung zur Verwendung von Notebooks mit Apache Spark zum Erstellen von Schemas für Rohdaten, Speichern als Hive-Tabellen und Verwenden von BI-Tools in der Hive-Tabelle zur Datenanalyse" 
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


# Verwenden von BI-Tools mit Apache Spark für Azure HDInsight (Linux)

Hier erfahren Sie, wie Sie Apache Spark in Azure HDInsight für folgende Aufgaben verwenden:

* Verwenden von Beispielrohdaten und Speichern als Hive-Tabelle
* Verwenden von BI-Tools wie Power BI und Tableau zum Analysieren und Visualisieren von Daten

> [AZURE.TIP] Dieses Tutorial steht auch als Jupyter-Notebook für einen Spark-Cluster (Linux) zur Verfügung, den Sie in HDInsight erstellen. In der Notebook-Umgebung können Sie die Python-Ausschnitte direkt im Notebook ausführen. Wenn Sie das Tutorial innerhalb eines Notebooks ausführen möchten, erstellen Sie einen Spark-Cluster, starten Sie ein Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), und führen Sie dann das Notebook **Use BI tools with Apache Spark on HDInsight.ipynb** im Ordner **Python** aus.

**Voraussetzungen:**

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (in englischer Sprache).
- Einen Apache Spark-Cluster unter HDInsight (Linux). Anleitungen finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
- Ein Computer mit installiertem Microsoft Spark-ODBC-Treiber (erforderlich für die Zusammenarbeit von Spark auf HDInsight mit Tableau). Sie können den Treiber [hier](http://go.microsoft.com/fwlink/?LinkId=616229) installieren.
- BI-Tools wie [Power BI](http://www.powerbi.com/) oder [Tableau Desktop](http://www.tableau.com/products/desktop). Eine kostenlose Preview-Version von Power BI können Sie unter [http://www.powerbi.com/](http://www.powerbi.com/) abonnieren.

##<a name="hivetable"></a>Speichern von Rohdaten als Hive-Tabelle

In diesem Abschnitt verwenden wir das [Jupyter](https://jupyter.org) Notebook, das einem Apache Spark-Cluster in HDInsight zugeordnet ist, zum Ausführen von Aufträgen, bei denen Ihre Beispielrohdaten verarbeitet und dann als Hive-Tabelle gespeichert werden. Die Beispieldaten sind in einer CSV-Datei (hvac.csv) enthalten, die standardmäßig auf allen Clustern verfügbar ist.

Nachdem Ihre Daten als Hive-Tabelle gespeichert wurden, können wir im nächsten Abschnitt eine Verbindung mit der Hive-Tabelle herstellen. Hierzu verwenden wir BI-Tools wie Power BI und Tableau.

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.   

2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Quicklinks** und anschließend auf dem Blatt **Cluster Dashboard** auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

	> [AZURE.NOTE] Sie können auch das Jupyter Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres Clusters:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Erstellen Sie ein neues Notebook. Klicken Sie auf **Neu** und dann auf **Python 2**.

	![Erstellen eines neuen Jupyter Notebooks](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "Erstellen eines neuen Jupyter Notebooks")

3. Ein neues Notebook mit dem Namen „Untitled.pynb“ wird erstellt und geöffnet. Klicken Sie oben auf den Namen des Notebooks, und geben Sie einen Anzeigenamen ein.

	![Angeben eines neuen Namens für das Notebook](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Angeben eines neuen Namens für das Notebook")

4. Importieren Sie die erforderlichen Module, und erstellen Sie die Spark- und Hive-Kontexte. Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie UMSCHALT+EINGABETASTE.

		from pyspark import SparkContext
		from pyspark.sql import *
		from pyspark.sql import HiveContext
		from pyspark.sql import Row
		
		# Create Spark and Hive contexts
		sc = SparkContext('yarn-client')
		hiveCtx = HiveContext(sc)

	Bei jedem Ausführen eines Auftrags in Jupyter wird in der Titelleiste Ihres Webbrowserfensters neben dem Notebooktitel der Status **(Beschäftigt)** angezeigt. Außerdem sehen Sie in der oberen rechten Ecke einen ausgefüllten Kreis neben dem Text **Python 2**. Wenn der Auftrag abgeschlossen ist, wird ein Kreis ohne Füllung angezeigt.

	 ![Status eines Jupyter Notebook-Auftrags](./media/hdinsight-apache-spark-use-bi-tools/hdispark.jupyter.job.status.png "Status eines Jupyter Notebook-Auftrags")

4. Laden Sie Beispieldaten in eine temporäre Tabelle. Wenn Sie einen Spark-Cluster in HDInsight erstellen, wird die Beispieldatendatei **hvac.csv** in das zugeordnete Speicherkonto unter **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac** kopiert.

	Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie UMSCHALT+EINGABETASTE. Mit diesem Codeausschnitt werden die Daten in einer Hive-Tabelle mit dem Namen **hvac** registriert.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create a schema for our data
		Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
		hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
		
		# Infer the schema and create a table       
		hvacTable = hiveCtx.createDataFrame(hvac)
		hvacTable.registerTempTable('hvactemptable')
		dfw = DataFrameWriter(hvacTable)
		dfw.saveAsTable('hvac')

5. Vergewissern Sie sich, dass die Tabelle erstellt wurde. Kopieren Sie den folgenden Codeausschnitt in eine leere Zelle im Notebook, und drücken Sie UMSCHALT+EINGABETASTE.

		hiveCtx.sql("SHOW TABLES").show()

	Folgendes sollte angezeigt werden:

		+---------------+-----------+
		|      tableName|isTemporary|
		+---------------+-----------+
		|  hvactemptable|       true|
		|hivesampletable|      false|
		|           hvac|      false|
		+---------------+-----------+


	Nur die Tabellen, für die in der Spalte **isTemporary** die Option "false" angegeben ist, sind Hive-Tabellen, die im Metastore gespeichert werden und auf die mit den BI-Tools zugegriffen werden kann. In diesem Lernprogramm stellen wir eine Verbindung mit der gerade erstellten **hvac**-Tabelle her.

6. Stellen Sie sicher, dass die Tabelle die gewünschten Daten enthält. Kopieren Sie den folgenden Codeausschnitt in eine leere Zelle im Notebook, und drücken Sie UMSCHALT+EINGABETASTE.

		hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()
	
7. Sie können jetzt das Notebook herunterfahren, um die Ressourcen freizugeben. Klicken Sie hierzu im Menü **Datei** des Notebooks auf die Option zum Schließen und Anhalten. Dadurch wird das Notebook heruntergefahren und geschlossen.

##<a name="powerbi"></a>Verwenden von Power BI zum Analysieren von Daten in der Hive-Tabelle

Nachdem Sie die Daten als Hive-Tabelle gespeichert haben, können Sie Power BI verwenden, um eine Verbindung mit den Daten herzustellen und die Daten zu visualisieren. So können Sie Berichte, Dashboards usw. erstellen.

1. Melden Sie sich bei [Power BI](http://www.powerbi.com/) an.

2. Klicken Sie auf der Willkommensseite auf **Databases & More**.

	![Abrufen von Daten in Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Abrufen von Daten in Power BI")

3. Klicken Sie auf dem nächsten Bildschirm auf **Spark on Azure HDInsight** und dann auf **Verbinden**. Geben Sie nach Aufforderung die Cluster-URL (`mysparkcluster.azurehdinsight.net`) und die Anmeldeinformationen für die Verbindung mit dem Cluster ein.

	Nachdem die Verbindung hergestellt wurde, beginnt Power BI mit dem Importieren der Daten aus dem Spark-Cluster unter HDInsight.

5. Power BI importiert die Daten und fügt einen neuen Spark-Dataset unter der Überschrift **Datasets** hinzu. Klicken Sie auf das Dataset, um ein neues Arbeitsblatt zum Visualisieren der Daten zu öffnen. Sie können das Arbeitsblatt auch als Bericht speichern. Klicken Sie im Menü **Datei** auf **Speichern**, um ein Arbeitsblatt zu speichern.

	![Spark-Kachel im Power BI-Dashboard](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Spark-Kachel im Power BI-Dashboard")

6. Beachten Sie, dass in der Liste **Felder** auf der rechten Seite die **hvac**-Tabelle aufgeführt ist, die Sie erstellt haben. Erweitern Sie die Tabelle, um die Felder in der Tabelle anzuzeigen, die Sie im Notebook definiert haben.

	  ![Auflisten der Hive-Tabellen](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Auflisten der Hive-Tabellen")

7. Erstellen Sie eine Visualisierung, um die Abweichung zwischen Zieltemperatur und Ist-Temperatur für jedes Gebäude anzuzeigen. Wählen Sie **Area Chart** (rot umrandet dargestellt) aus, um Ihre Daten zu visualisieren. Zum Definieren der Achse legen Sie das Feld **BuildingID** unter **Achse** und die Felder **ActualTemp**/**TargetTemp** unter **Wert** ab.

	![Erstellen von Visualisierungen](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "Erstellen von Visualisierungen")


8. Standardmäßig werden in der Visualisierung die Summen für **ActualTemp** und **TargetTemp** angezeigt. Wählen Sie für beide Felder in der Dropdownliste die Option **Mittelwert** aus, um für beide Gebäude den Mittelwert der Ist- und Zieltemperaturen zu erhalten.

	![Erstellen von Visualisierungen](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
    
9. Ihre Datenvisualisierung sollte in etwa wie folgt aussehen. Bewegen Sie den Cursor über die Visualisierung, um QuickInfos mit relevanten Daten abzurufen.

	![Erstellen von Visualisierungen](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

10. Klicken Sie im oberen Menü auf **Speichern**, und geben Sie einen Berichtsnamen an. Sie können die Visualisierung auch anheften. Wenn Sie eine Visualisierung anheften, wird sie in Ihrem Dashboard gespeichert, damit Sie jeweils den aktuellen Wert im Blick haben.

	Sie können für ein Dataset beliebig viele Visualisierungen hinzufügen und im Dashboard anheften, um eine Momentaufnahme Ihrer Daten zu erhalten. Außerdem sind Spark-Cluster unter HDInsight per Direktverbindung mit Power BI verbunden. Dies bedeutet, dass Power BI immer über die aktuellsten Daten aus Ihrem Cluster verfügt, sodass Sie keine Aktualisierungen für das Dataset einplanen müssen.

##<a name="tableau"></a>Verwenden von Tableau Desktop zum Analysieren von Daten in der Hive-Tabelle
	
1. Starten Sie Tableau Desktop. Klicken Sie im linken Bereich in der Liste mit den verfügbaren Servern auf **Spark SQL**. Wenn Spark-SQL nicht standardmäßig im linken Bereich aufgeführt ist, finden Sie es durch Klicken auf **Mehr Server**. 

2. Geben Sie im Dialogfeld für die Spark-SQL-Verbindung die Werte wie unten dargestellt ein, und klicken Sie dann auf **OK**.

	![Herstellen einer Verbindung mit einem Spark-Cluster](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Herstellen einer Verbindung mit einem Spark-Cluster")

	In der Dropdownliste für die Authentifizierung wird der **Microsoft Azure HDInsight-Dienst** nur dann als Option aufgeführt, wenn Sie auf dem Computer den [Microsoft Spark-ODBC-Treiber](http://go.microsoft.com/fwlink/?LinkId=616229) installiert haben.

3. Klicken Sie auf dem nächsten Bildschirm in der Dropdownliste **Schema** auf das Symbol **Suchen** und dann auf **Standard**.

	![Suchen nach Schemas](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "Suchen nach Schemas")

4. Klicken Sie für das Feld **Tabelle** erneut auf das Symbol **Suchen**, um alle Hive-Tabellen aufzulisten, die im Cluster verfügbar sind. Daraufhin sollte die **hvac**-Tabelle angezeigt werden, die Sie zuvor mit dem Notebook erstellt haben.

	![Suchen nach Tabellen](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "Suchen nach Tabellen")

5. Legen Sie die Tabelle im oberen Feld auf der rechten Seite ab. Tableau importiert die Daten und zeigt das Schema wie in der Hervorhebung mit dem roten Kasten an.

	![Hinzufügen von Tabellen zu Tableau](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Hinzufügen von Tabellen zu Tableau")

6. Klicken Sie unten links auf die Registerkarte **Blatt1**. Erstellen Sie eine Visualisierung, in der die durchschnittlichen Ziel- und Ist-Temperaturen für alle Gebäude und jedes Datum angezeigt werden. Ziehen Sie **Date** und **Building ID** auf **Spalten** und **Actual Temp**/**Target Temp** auf **Zeilen**. Wählen Sie unter **Markierungen** die Option **Bereich**, um eine Bereichskartenvisualisierung zu verwenden.

	 ![Hinzufügen von Feldern für die Visualisierung](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "Hinzufügen von Feldern für die Visualisierung")

7. Standardmäßig werden die Temperaturfelder als Aggregatwerte angezeigt. Wenn Sie stattdessen die durchschnittlichen Temperaturen anzeigen möchten, können Sie die Dropdownliste verwenden, wie unten dargestellt.

	![Bestimmen des Mittelwerts für die Temperatur](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "Bestimmen des Mittelwerts für die Temperatur")

8. Sie können auch eine Temperaturkarte auf die andere Temperaturkarte legen, um ein besseres Gefühl für den Unterschied zwischen Ziel- und Ist-Temperaturen zu erhalten. Bewegen Sie den Mauszeiger in die Ecke der unteren Bereichskarte, bis das Griffsymbol mit einem roten Kreis markiert wird. Ziehen Sie die Karte in die andere Karte, die darüber liegt, und lassen Sie die Maustaste los, wenn die Form im roten Rechteck hervorgehoben wird.

	![Zusammenführen von Karten](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "Zusammenführen von Karten")

	 Ihre Datenvisualisierung sollte sich wie folgt ändern:

	![Visualisierung](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "Visualisierung")
	 
9. Klicken Sie auf **Speichern**, um das Arbeitsblatt zu speichern. Sie können Dashboards erstellen und ein oder mehrere Blätter hinzufügen.

##<a name="seealso"></a>Weitere Informationen

* [Übersicht: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md)

### Szenarios

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

[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0211_2016-->