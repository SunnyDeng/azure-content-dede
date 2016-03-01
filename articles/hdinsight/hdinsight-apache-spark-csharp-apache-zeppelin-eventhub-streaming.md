<properties 
	pageTitle="Verwenden von Azure Event Hubs mit Apache Spark in HDInsight zum Verarbeiten von Streamingdaten | Microsoft Azure" 
	description="Schritt-für-Schritt-Anleitung zum Senden eines Datenstroms an einen Azure Event Hub und zum Empfangen dieser Ereignisse in Spark mit einem Zeppelin Notebook" 
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
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Spark-Streaming: Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Spark für HDInsight (Windows)

> [AZURE.NOTE] HDInsight bietet jetzt Spark-Cluster unter Linux. Informationen zum Ausführen einer Streaminganwendung auf HDInsight Spark-Clustern unter Linux finden Sie unter [Spark-Streaming: Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Spark für HDInsight (Linux)](hdinsight-apache-spark-eventhub-streaming.md).

Das Spark-Streaming ist eine Erweiterung der Spark-Kern-API zum Erstellen von skalierbaren, fehlertoleranten Anwendungen für die Datenstromverarbeitung mit hohem Durchsatz. Daten können aus vielen Quellen erfasst werden. In diesem Artikel verwenden wir Event Hubs zum Erfassen von Daten. Bei Event Hubs handelt es sich um ein hochskalierbares Erfassungssystem, mit dem Millionen von Ereignissen pro Sekunde verarbeitet werden können.

In diesem Lernprogramm erfahren Sie, wie Sie einen Azure Event Hub erstellen, Nachrichten mit einer Konsolenanwendung in C# für einen Event Hub erfassen und mit einem Zeppelin Notebook parallel abrufen, das für Apache Spark unter HDInsight konfiguriert ist.

> [AZURE.NOTE] Für die Anweisungen in diesem Artikel benötigen Sie beide Versionen des Azure-Portals. Zum Erstellen eines Event Hubs verwenden Sie das [Azure-Portal](https://manage.windowsazure.com). Für das Arbeiten mit dem HDInsight Spark-Cluster nutzen Sie das [Azure-Vorschauportal](https://ms.portal.azure.com/).

**Voraussetzungen:**

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Einen Apache Spark-Cluster. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Einen [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Eine Arbeitsstation mit Microsoft Visual Studio 2013. Anweisungen finden Sie unter [Installieren von Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Erstellen eines Azure Event Hubs

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU** > **Service Bus** > **Event Hub** > **Benutzerdefiniert erstellen**.

2. Geben Sie im Bildschirm **Neuen Event Hub hinzufügen** einen Wert für **Event Hub-Name** ein, wählen Sie die **Region** aus, in der der Hub erstellt werden soll, und erstellen Sie einen neuen Namespace, oder wählen Sie einen vorhandenen Namespace aus. Klicken Sie auf den **Pfeil**, um fortzufahren.

	![Seite 1 des Assistenten](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub.png "Erstellen eines Azure Event Hubs")

	> [AZURE.NOTE] Wählen Sie den gleichen **Speicherort** wie für Ihren Apache Spark-Cluster unter HDInsightStorm aus, um die Latenz und die Kosten zu reduzieren.

3. Geben Sie im Bildschirm **Event Hub konfigurieren** die Werte für **Partitionsanzahl** und **Nachrichtenaufbewahrung** ein, und klicken Sie auf das Häkchen. Verwenden Sie für dieses Beispiel eine Anzahl von 10 Partitionen und eine Nachrichtenaufbewahrung von 1. Notieren Sie sich die Partitionsanzahl, da Sie diesen Wert später benötigen werden.

	![Seite 2 des Assistenten](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Angeben der Partitionsgröße und der Aufbewahrung in Tagen für Event Hub")

4. Klicken Sie auf den erstellten Event Hub und dann auf **Konfigurieren**, und erstellen Sie zwei Zugriffsrichtlinien für den Event Hub.

	<table>
<tr><th>Name</th><th>Berechtigungen</th></tr>
<tr><td>mysendpolicy</td><td>Send</td></tr>
<tr><td>myreceivepolicy</td><td>Empfangen</td></tr>
</table>Klicken Sie nach dem Erstellen der Berechtigungen auf das Symbol **Speichern** am unteren Seitenrand. Hiermit werden die freigegebenen Zugriffsrichtlinien erstellt, die zum Senden (**mysendpolicy**) und Überwachen (**myreceivepolicy**) dieses Event Hubs verwendet werden.

	![Richtlinien](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Erstellen von Event Hub-Richtlinien")

	
5. Beachten Sie auf derselben Seite die Richtlinienschlüssel, die für die beiden Richtlinien generiert werden. Speichern Sie diese Schlüssel für den späteren Gebrauch.

	![Richtlinienschlüssel](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Speichern von Richtlinienschlüsseln")

6. Klicken Sie auf der Seite **Dashboard** unten auf **Verbindungsinformationen**, um die Verbindungszeichenfolgen für den Event Hub mit den beiden Richtlinien abzurufen und zu speichern.

	![Richtlinienschlüssel](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Speichern von Verbindungszeichenfolgen für Richtlinien")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Empfangen von Nachrichten in Spark für HDInsight per Zeppelin

In diesem Abschnitt erstellen Sie ein [Zeppelin](https://zeppelin.incubator.apache.org) Notebook zum Empfangen von Nachrichten aus dem Event Hub im Spark-Cluster unter HDInsight.

### Zuordnen von Ressourcen zu Zeppelin für eine Streaminganwendung

Beachten Sie die folgenden Punkte beim Erstellen einer Streaminganwendung mithilfe von Zeppelin:

* **Event Hub-Partitionen und Zeppelin zugeordnete Kerne**. In den vorherigen Schritten haben Sie einen Event Hub mit einigen Partitionen erstellt. In der Zeppelin-Streaminganwendung, die Sie nachfolgend erstellen, müssen Sie die gleiche Anzahl an Partitionen angeben. Für ein erfolgreiches Streaming von Event Hub-Daten mithilfe von Zeppelin muss die Zahl der Zeppelin zugeordneten Kerne doppelt so groß sein wie die Anzahl der Event Hub-Partitionen.
* **Minimale Anzahl an Kernen, die Zeppelin zugeordnet werden**. In der Streaminganwendung, die Sie nachfolgend erstellen, erstellen Sie eine temporäre Tabelle, in der die von Ihrer Anwendung gestreamten Nachrichten gespeichert werden. Anschließend verwenden Sie eine Spark-SQL-Anweisung zum Lesen der Nachrichten aus dieser temporären Tabelle. Damit die Spark-SQL-Anweisung erfolgreich ausgeführt wird, müssen Sie sicherstellen, dass Zeppelin mindestens zwei Kerne zugeordnet sind.

Aus den beiden oben genannten Anforderungen ergibt sich folgende Voraussetzung:

* Die minimale Anzahl von Kernen, die Zeppelin zugeordnet werden müssen, ist 2.
* Die Zahl der zugeordneten Kerne muss doppelt so groß sein wie die Zahl der Event Hub-Partitionen. 

Informationen zum Zuordnen von Ressourcen in einem Spark-Cluster finden Sie unter [Verwalten von Ressourcen für den Apache Spark-Cluster in HDInsight](hdinsight-apache-spark-resource-manager-v1.md).

### Erstellen einer Streaminganwendung mit Zeppelin

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.   

2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Quicklinks** und anschließend auf dem Blatt **Cluster Dashboard** auf **Zeppelin Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

	> [AZURE.NOTE] Sie können auch das Zeppelin Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres Clusters:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Erstellen Sie ein neues Notebook. Klicken Sie im Headerbereich auf **Notebook**, und wählen Sie in der Dropdownliste die Option **Neue Notiz erstellen**.

	![Erstellen eines neuen Zeppelin Notebooks](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.createnewnote.png "Erstellen eines neuen Zeppelin Notebooks")

	Auf derselben Seite sollte unter der Überschrift **Notebook** ein neues Notebook angezeigt werden, dessen Name mit **Notiz XXXXXXXXX** beginnt. Klicken Sie auf das neue Notebook.

3. Klicken Sie auf der Webseite für das neue Notebook auf die Überschrift, und ändern Sie den Namen des Notebooks, wenn Sie dies möchten. Drücken Sie die EINGABETASTE, um die Namensänderung zu speichern. Stellen Sie außerdem sicher, dass im Header des Notebooks in der oberen rechten Ecke der Status **Verbunden** angezeigt wird.

	![Zeppelin Notebook-Status](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.newnote.connected.png "Zeppelin Notebook-Status")

4. Fügen Sie in den leeren Absatz, der im neuen Notebook standardmäßig erstellt wird, den folgenden Codeausschnitt ein, und ersetzen Sie die Platzhalter, um Ihre Event Hub-Konfiguration zu verwenden. In diesem Codeausschnitt empfangen Sie den Datenstrom vom Event Hub und registrieren ihn in einer temporären Tabelle mit dem Namen **mytemptable**. Im nächsten Abschnitt starten wir die Absenderanwendung. Anschließend können Sie die Daten direkt aus der Tabelle auslesen.

	> [AZURE.NOTE] Der folgende Codeausschnitt (**eventhubs.checkpoint.dir**) muss auf ein Verzeichnis in Ihrem Standardspeichercontainer festgelegt werden. Wenn das Verzeichnis nicht vorhanden ist, wird es von der Streaminganwendung erstellt. Sie können entweder den vollständigen Pfad zum Verzeichnis angeben, z. B. "**wasb://container@storageaccount.blob.core.windows.net/mycheckpointdir/**", oder nur den relativen Verzeichnispfad, z. B. "**/mycheckpointdir**".

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>Ausführen der Anwendungen

1. Führen Sie den Absatz mit dem Codeausschnitt aus dem Zeppelin Notebook aus. Drücken Sie **UMSCHALT+EINGABE**, oder wählen Sie oben rechts die Wiedergabeschaltfläche.

	Der Status in der rechten Ecke des Absatzes sollte sich entsprechend ändern: BEREIT, AUSSTEHEND, WIRD AUSGEFÜHRT bis zu BEENDET. Die Ausgabe wird im unteren Teil des gleichen Absatzes angezeigt. Der Screenshot sieht folgendermaßen aus:

	![Ausgeben des Codeausschnitts](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.code.output.png "Ausgeben des Codeausschnitts")

2. Führen Sie das Projekt **Sender** aus, und drücken Sie im Konsolenfenster die **EINGABETASTE**, um mit dem Senden von Nachrichten an den Event Hub zu beginnen.

3. Geben Sie im Zeppelin Notebook in einem neuen Absatz den folgenden Codeausschnitt ein, um die in Spark empfangenen Nachrichten zu lesen.

		%sql 
		select * from mytemptable limit 10

	Der folgende Screenshot zeigt die Nachrichten, die in **mytemptable** empfangen werden.

	![Empfangen von Nachrichten in Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.output.png "Empfangen von Nachrichten im Zeppelin Notebook")

4. Starten Sie den Spark-SQL-Interpreter neu, um die Anwendung zu beenden. Klicken Sie oben auf die Registerkarte **Interpreter**, und klicken Sie für den Spark-Interpreter auf **Neu starten**.

	![Neustarten des Zeppelin-Interpreters](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.zeppelin.restart.interpreter.png "Neustarten des Zeppelin-Interpreters")

##<a name="sparkstreamingha"></a>Ausführen der Streaminganwendung mit hoher Verfügbarkeit

Die Verwendung von Zeppelin zum Empfangen von Streamingdaten im Spark-Cluster unter HDInsight ist ein guter Ansatz für die Prototyperstellung für Ihre Anwendung. Sie müssen aber folgende Schritte ausführen, um Ihre Streaminganwendung in einer Produktionsumgebung mit hoher Verfügbarkeit und Zuverlässigkeit auszuführen:

1. Kopieren Sie die Abhängigkeits-JAR-Datei in das Speicherkonto, das dem Cluster zugeordnet ist.
2. Erstellen Sie eine Streaminganwendung.
3. Greifen Sie per RDP auf den Cluster zu, und kopieren Sie die Anwendungs-JAR-Datei in den obersten Knoten des Clusters.
3. Greifen Sie per RDP auf den Cluster zu, und führen Sie die Anwendung im Clusterknoten aus.

Eine Anleitung zum Ausführen dieser Schritte und eine Streaming-Beispielanwendung können Sie von GitHub unter [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples) herunterladen.


##<a name="seealso"></a>Weitere Informationen


* [Übersicht: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Schnellstart: Erstellen von Apache Spark für HDInsight und Ausführen von interaktiven Abfragen per Spark-SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Verwenden von Spark in HDInsight zum Erstellen von Machine Learning-Anwendungen](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->