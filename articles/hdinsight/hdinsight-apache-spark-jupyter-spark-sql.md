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
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="nitinme"/>


# Erste Schritte: Erstellen eines Apache Spark-Clusters für Azure HDInsight (Linux) und Ausführen von interaktiven Abfragen per Spark-SQL

Hier erfahren Sie, wie Sie einen Apache Spark-Cluster in HDInsight erstellen und dann ein [Jupyter](https://jupyter.org)-Notebook verwenden, um interaktive Spark-SQL-Abfragen für den Spark-Cluster auszuführen.

   ![Erste Schritte mit Apache Spark in HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Lernprogramm zu den ersten Schritten mit Apache Spark in HDInsight Dargestellte Schritte: Erstellen eines Speicherkontos, Erstellen eines Clusters, Ausführen von Spark-SQL-Anweisungen")

**Voraussetzungen:**

- **Ein Azure-Abonnement**. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie über ein Azure-Abonnement verfügen. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Ein Secure Shell-Client (SSH)**: Linux-, Unix und OS X-Systeme stellen einen SSH-Client über den Befehl `ssh` bereit. Für Windows-Systeme wird [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) empfohlen.
    
- **Secure Shell-Schlüssel (SSH) (optional)**: Sie können das SSH-Konto, mit dem die Verbindung zum Cluster hergestellt wird, mit einem Kennwort oder einem öffentlichen Schlüssel sichern. Die Verwendung eines Kennworts ermöglicht Ihnen einen schnellen Einstieg. Verwenden Sie diese Option, wenn Sie ein Cluster schnell erstellen und einige Testaufträge ausführen möchten. Die Verwendung eines Schlüssels bietet höhere Sicherheit. Allerdings sind hierfür zusätzliche Schritte zur Einrichtung erforderlich. Sie sollten diesen Ansatz verwenden, wenn Sie einen Produktionscluster erstellen. In diesem Artikel verwenden wir ein Kennwort. Anweisungen zum Erstellen und Verwenden von SSH-Schlüsseln mit HDInsight finden Sie in den folgenden Artikeln:

	-  Auf einem Linux-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
	-  Auf einem Windows-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).


## Erstellen eines Spark-Clusters unter HDInsight (Linux).

In diesem Abschnitt erstellen Sie einen HDInsight-Cluster der Version 3.3, der auf der Spark-Version 1.5.1 basiert. Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

>[AZURE.NOTE]Mit den Schritten in diesem Artikel wird ein Apache Spark-Cluster in HDInsight unter Verwendung grundlegender Konfigurationseinstellungen erstellt. Informationen zu anderen Clusterkonfigurationseinstellungen (z. B. zur Verwendung von zusätzlichem Speicher, einem virtuellen Azure-Netzwerk oder einem Metastore für Hive) finden Sie unter [Erstellen von HDInsight Spark-Clustern mit benutzerdefinierten Optionen](hdinsight-hadoop-provision-linux-clusters.md).


**Erstellen eines Spark-Clusters**

1. Melden Sie sich beim [Azure-Vorschauportal](https://ms.portal.azure.com/) an.

2. Klicken Sie auf **NEU**, auf **Datenanalyse** und anschließend auf **HDInsight**.

    ![Erstellen eines neuen Clusters im Azure-Vorschauportal](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.1.png "Erstellen eines neuen Clusters im Azure-Vorschauportal")

3. Geben Sie einen **Clusternamen** ein, wählen Sie **Hadoop** als **Clustertyp** aus, und wählen Sie im Dropdownmenü **Clusterbetriebssystem** den Eintrag **Ubuntu** aus. Geben Sie dann die Spark-Version an. Wenn der Clustername verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	![Clusternamen und -typ eingeben](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.2.png "Clusternamen und -typ eingeben")

4. Falls Sie mehrere Abonnements besitzen, klicken Sie auf den Eintrag **Abonnement**, um das Azure-Abonnement für den Cluster auszuwählen.

5. Klicken Sie auf **Ressourcengruppe**, um eine Liste der vorhandenen Ressourcengruppen anzuzeigen, und wählen Sie dann die Gruppe aus, in der der Cluster erstellt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf **Neu erstellen** klicken und den Namen der neuen Gruppe eingeben. Wenn der neue Gruppenname verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	> [AZURE.NOTE]Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).

6. Klicken Sie auf **Anmeldeinformationen**, und geben Sie ein Kennwort für den Administrator ein. Sie müssen außerdem einen **SSH-Benutzernamen** eingeben. Klicken Sie für **SSH-Authentifizierungstyp** auf **KENNWORT**, und geben Sie ein Kennwort für den SSH-Benutzer an. Klicken Sie unten auf **Auswählen**, um die Konfiguration der Anmeldeinformationen zu speichern.

	![Clusteranmeldeinformationen eingeben](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.3.png "Clusteranmeldeinformationen eingeben")

    > [AZURE.NOTE]SSH wird verwendet, um remote über eine Befehlszeile auf den HDInsight-Cluster zuzugreifen. Der Benutzername und das Kennwort, die Sie hier verwenden, dienen zum Herstellen der Verbindung mit dem Cluster über SSH. Außerdem muss der SSH-Benutzername eindeutig sein, da mit ihm auf allen HDInsight-Clusterknoten ein Benutzerkonto erstellt wird. Die folgenden Namen sind einige der Namen, die für die Verwendung durch Dienste auf dem Cluster reserviert sind und daher nicht als SSH-Benutzername verwendet werden können:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Klicken Sie auf **Datenquelle**, um eine vorhandene Datenquelle für den Cluster auszuwählen, oder erstellen Sie eine neue Datenquelle. Bei der Erstellung eines Hadoop-Clusters in HDInsight geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blobs-Speicher-Container aus diesem Konto wird genau wie in HDFS (Hadoop Distributed File System) als Standarddateisystem festgelegt. Der HDInsight-Cluster wird standardmäßig in demselben Rechenzentrum erstellt wie das angegebene Speicherkonto. Weitere Informationen finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight][hdinsight-storage].

	![Blatt „Datenquelle“](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.4.png "Datenquellenkonfiguration angeben")

	Zurzeit kann ein Azure-Speicherkonto als Datenquelle für einen HDInsight-Cluster ausgewählt werden. Die Einträge auf dem Blatt **Datenquelle** werden im Folgenden erläutert.

	- **Auswahlmethode**: Wählen Sie **Aus allen Abonnements** aus, damit Speicherkonten aus all Ihren Abonnements durchsucht werden können. Wählen Sie **Zugriffsschlüssel** aus, wenn Sie den **Speichernamen** und **Zugriffsschlüssel** eines vorhandenen Speicherkontos eingeben möchten.

	- **Speicherkonto auswählen/Neu erstellen**: Klicken Sie auf **Speicherkonto auswählen**, um ein vorhandenes Speicherkonto auszuwählen, das Sie dem Cluster zuordnen möchten. Alternativ können Sie auf **Neu erstellen** klicken, um ein neues Speicherkonto zu erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.

	- **Standardcontainer auswählen**: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.

	- **Standort**: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird.

		> [AZURE.IMPORTANT]Durch die Auswahl des Standorts für die Standarddatenquelle wird auch der Standort des HDInsight-Clusters festgelegt. Der Cluster und die Standarddatenquelle müssen sich in der gleichen Region befinden.

	Klicken Sie auf **Auswählen**, um die Datenquellenkonfiguration zu speichern.

8. Klicken Sie auf **Knotenpreistarife**, um Informationen zu den Knoten anzuzeigen, die für diesen Cluster erstellt werden. Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.

	![Blatt „Knotenpreistarife“](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.5.png "Anzahl von Clusterknoten angeben")

	Klicken Sie auf **Auswählen**, um die Konfiguration der Knotenpreise zu speichern.

9. Vergewissern Sie sich auf dem Blatt **Neues HDInsight-Cluster**, dass **An Startmenü anheften** ausgewählt ist, und klicken Sie dann auf **Erstellen**. Der Cluster wird erstellt, und dem Startmenü Ihres Azure-Portals wird eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster erstellt wird. Sobald die Erstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.

	| Während der Erstellung | Erstellung abgeschlossen |
	| ------------------ | --------------------- |
	| ![Erstellungsanzeige im Startmenü](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioning.png) | ![Kachel für einen bereitgestellten Cluster](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE]Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Erstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter **Benachrichtigungen** überprüfen.

10. Klicken Sie nach Abschluss der Erstellung im Startmenü auf die Kachel für den Spark-Cluster, um das Clusterblatt zu öffnen.



## <a name="jupyter"></a>Ausführen von Spark-SQL-Abfragen mit einem Jupyter Notebook

In diesem Abschnitt verwenden Sie ein Jupyter Notebook, um Spark-SQL-Abfragen für einen Spark-Cluster auszuführen.

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.   

2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Quicklinks** und anschließend auf dem Blatt **Cluster Dashboard** auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

	> [AZURE.NOTE]Sie können auch das Jupyter Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres Clusters:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Erstellen Sie ein neues Notebook. Klicken Sie auf **Neu** und dann auf **Python2**.

	![Erstellen eines neuen Jupyter Notebooks](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Erstellen eines neuen Jupyter Notebooks")

3. Ein neues Notebook mit dem Namen „Untitled.pynb“ wird erstellt und geöffnet. Klicken Sie oben auf den Namen des Notebooks, und geben Sie einen Anzeigenamen ein.

	![Angeben eines neuen Namens für das Notebook](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Angeben eines neuen Namens für das Notebook")

4. Importieren Sie die erforderlichen Module, und erstellen Sie die Spark- und SQL-Kontexte. Fügen Sie den folgenden Beispielcode in eine leere Zelle ein, und drücken Sie UMSCHALT+EINGABETASTE.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		# Create Spark and SQL contexts
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)

	Bei jedem Ausführen eines Auftrags in Jupyter wird in der Titelleiste Ihres Webbrowserfensters neben dem Notebooktitel der Status **(Beschäftigt)** angezeigt. Außerdem sehen Sie in der oberen rechten Ecke einen ausgefüllten Kreis neben dem Text **Python 2**. Wenn der Auftrag abgeschlossen ist, wird ein Kreis ohne Füllung angezeigt.

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
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. Nachdem der Auftrag erfolgreich abgeschlossen wurde, wird die folgende Ausgabe angezeigt.

		+----------+---------+------+
		|buildingID|temp_diff|  date|
		+----------+---------+------+
		|         4|        8|6/1/13|
		|         3|        2|6/1/13|
		|         7|      -10|6/1/13|
		|        12|        3|6/1/13|
		|         7|        9|6/1/13|
		|         7|        5|6/1/13|
		|         3|       11|6/1/13|
		|         8|       -7|6/1/13|
		|        17|       14|6/1/13|
		|        16|       -3|6/1/13|
		|         8|       -8|6/1/13|
		|         1|       -1|6/1/13|
		|        12|       11|6/1/13|
		|         3|       14|6/1/13|
		|         6|       -4|6/1/13|
		|         1|        4|6/1/13|
		|        19|        4|6/1/13|
		|        19|       12|6/1/13|
		|         9|       -9|6/1/13|
		|        15|      -10|6/1/13|
		+----------+---------+------+


6. Nach Ausführen der Anwendung empfiehlt es sich, das Notebook herunterzufahren, um die Ressourcen freizugeben. Klicken Sie hierzu im Menü **Datei** des Notebooks auf die Option zum Schließen und Anhalten. Dadurch wird das Notebook heruntergefahren und geschlossen.


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

### Erweiterungen

* [Verwenden von Zeppelin-Notebooks mit einem Spark-Cluster in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Verfügbare Kernels für Jupyter-Notebooks im Spark-Cluster für HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Verwalten von Ressourcen

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

### Bekannte Probleme

* [Bekannte Probleme von Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-known-issues.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0114_2016-->