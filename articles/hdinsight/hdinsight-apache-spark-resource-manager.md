<properties 
	pageTitle="Verwenden des Ressourcen-Managers zum Zuteilen von Ressourcen an den Apache Spark-Cluster in HDInsight| Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Ressourcen-Manager für Spark-Cluster in HDInsight zum Verbessern der Leistung verwenden." 
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


# Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight (Linux)

Spark in Azure HDInsight (Linux) bietet die Ambari-Webbenutzeroberfläche zum Verwalten der Clusterressourcen und zum Überwachen der Integrität des Clusters. Sie können auch den Spark-Verlaufsserver nutzen, um Anwendungen zu verfolgen, deren Ausführung auf dem Cluster abgeschlossen wurde. Auf der YARN-Benutzeroberfläche können Sie die derzeit auf dem Cluster ausgeführten Anwendungen überwachen. Dieser Artikel enthält Anweisungen für den Zugriff auf diese Benutzeroberflächen sowie für das Ausführen einiger grundlegender Verwaltungsaufgaben mithilfe dieser Oberflächen.

**Voraussetzungen:**

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (in englischer Sprache).
- Einen Apache Spark-Cluster unter HDInsight (Linux). Anweisungen finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Wie wird die Ambari-Webbenutzeroberfläche gestartet?

1. Klicken Sie im [Azure-Vorschauportal](https://ms.portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren. 
 
2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Dashboard**. Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.

	![Ambari starten](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Ressourcen-Manager starten")

3. Damit sollte wie unten dargestellt die Ambari-Webbenutzeroberfläche gestartet werden.

	![Ambari-Webbenutzeroberfläche](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Ambari-Webbenutzeroberfläche")

## Wie starte ich den Spark-Verlaufsserver?

1. Klicken Sie im [Azure-Vorschauportal](https://ms.portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben).

2. Klicken Sie auf dem Clusterblatt unter **Quicklinks** auf **Clusterdashboard**. Klicken Sie auf dem Blatt **Clusterdashboard** auf **Spark-Verlaufsserver**.

	![Spark-Verlaufsserver](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Spark-Verlaufsserver")

	Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.


## Wie wird die Yarn-Benutzeroberfläche gestartet?

Auf der YARN-Benutzeroberfläche können Anwendungen überwachen, die derzeit auf dem Spark-Cluster ausgeführt werden. Vor dem Zugriff auf die YARN-Benutzeroberfläche sollten Sie SSH-Tunneling zum Cluster aktivieren. Eine Anleitung hierzu finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche](hdinsight-linux-ambari-ssh-tunnel.md).

1. Starten Sie wie im vorherigen Abschnitt gezeigt die Ambari-Webbenutzeroberfläche.

2. Wählen Sie auf der Ambari-Webbenutzeroberfläche aus der Liste links auf der Seite YARN aus.

3. 3\. Wenn die Dienstinformationen zu YARN angezeigt werden, wählen Sie **QuickLinks** aus. Es wird eine Liste der Clusterhauptknoten angezeigt. Wählen Sie einen der Hauptknoten und dann **ResourceManager UI** aus.

	![YARN-Benutzeroberfläche starten](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "YARN-Benutzeroberfläche starten")

4. Damit sollte die YARN-Benutzeroberfläche gestartet und eine Seite ähnlich der folgenden angezeigt werden:

	![YARN-Benutzeroberfläche](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "YARN-Benutzeroberfläche")

##<a name="scenariosrm"></a>Wie verwalte ich Ressourcen mithilfe der Ambari-Webbenutzeroberfläche?

Hier finden Sie einige gängige Szenarien, die bei Ihrem Spark Cluster ggf. auftreten, und Anweisungen dazu, wie sie diese mit der Ambari-Webbenutzeroberfläche behandeln.

### Ich verwende BI mit Spark-Cluster nicht. Wie nehme ich die Ressourcen zurück?

1. Starten Sie wie oben gezeigt die Ambari-Webbenutzeroberfläche. Klicken Sie im linken Navigationsbereich auf **Spark** und dann auf **Configs**.

2. Suchen Sie in der Liste der verfügbaren Konfigurationen nach **benutzerdefinierten spark-thrift-sparkconf-Elementen**, und ändern Sie die Werte für **spark.executor.memory** und **spark.drivers.core** zu **0**.

	![Ressourcen für BI](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "Ressourcen für BI")

3. Klicken Sie auf **Speichern**. Geben Sie eine Beschreibung für die Änderungen ein, und klicken Sie dann erneut auf **Speichern**.

4. Oben auf der Seite sollte eine Aufforderung zum Neustarten des Spark-Dienstes angezeigt werden. Klicken Sie auf **Neu starten**, damit die Änderungen wirksam werden.


### Meine Jupyter-Notebooks werden nicht wie erwartet ausgeführt. Wie kann ich den Dienst neu starten?

1. Starten Sie wie oben gezeigt die Ambari-Webbenutzeroberfläche. Klicken Sie im linken Navigationsbereich auf **Jupyter**, **Dienstaktionen** und dann auf **Alle neu starten**. Dadurch wird der Jupyter-Dienst auf allen Stammknoten gestartet.

	![Neustarten von Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Neustarten von Jupyter")

	


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

* [Verfügbare Kernels für Jupyter-Notebook im Spark-Cluster für HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0128_2016-->