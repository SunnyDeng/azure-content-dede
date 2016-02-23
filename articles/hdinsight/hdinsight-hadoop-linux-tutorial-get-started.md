<properties
   	pageTitle="Linux-Lernprogramm: Erste Schritte mit Hadoop und Hive | Microsoft Azure"
   	description="Führen Sie dieses Linux-Lernprogramm aus, um sich mit Hadoop in HDInsight vertraut zu machen. Erfahren Sie, wie Sie Linux-Cluster bereitstellen und Daten mit Hive abfragen."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="02/11/2016"
   	ms.author="jgao"/>

# Hadoop-Tutorial: Erste Schritte bei der Verwendung von Linux-basiertem Hadoop in HDInsight

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Erfahren Sie, wie Sie Linux-basierte Hadoop-Cluster in HDInsight erstellen und Ambari Hive View zum Ausführen von Hive-Aufträgen verwenden.

Wenn Sie noch keine Erfahrung mit Hadoop und Big Data haben, erfahren Sie hier mehr zu: [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [Hadoop Distributed File System (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) und [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Wie HDInsight Hadoop in Azure bereitstellt, erfahren Sie unter [Einführung in Hadoop in HDInsight](hdinsight-hadoop-introduction.md).

### Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**: siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## Cluster erstellen

Die meisten Hadoop-Aufträge sind Batchaufträge. Erstellen Sie einen Cluster, und führen Sie Aufträge aus. In diesem Abschnitt erstellen Sie einen Linux-basierten Hadoop-Cluster in HDInsight mithilfe einer [Azure ARM-Vorlage](../resource-group-template-deploy.md). Für dieses Tutorial ist keine Erfahrung mit Azure-ARM-Vorlagen erforderlich. Andere Methoden zur Erstellung von Clustern und Informationen zu den Einstellungen finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Weitere Informationen zur Verwendung der ARM-Vorlage zum Erstellen von Hadoop-Clustern in HDInsight finden Sie unter [Erstellen Windows-basierter Hadoop-Cluster in HDInsight mithilfe von ARM-Vorlagen](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

1. Klicken Sie auf die folgende Abbildung, um eine ARM-Vorlage im Azure-Portal zu öffnen. Die ARM-Vorlage befindet sich in einem öffentlichen Blobcontainer. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/de-DE/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Geben Sie über das Blatt **Parameter** Folgendes ein:

    - **ClusterName**: Geben Sie einen Namen für den Hadoop-Cluster ein, den Sie erstellen möchten.
    - **ClusterStorageAccountName**: Jeder Cluster verfügt über eine Abhängigkeit von einem Azure Blob Storage-Konto. Nach dem Löschen eines Clusters bleiben die Daten im Speicherkonto erhalten.
    - **Cluster-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet **admin**.
    - **SSH-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet **sshuser**. Sie können auch einen anderen Namen festlegen. Andere Parameter sind optional. Ändern Sie sie nicht.  
3. Klicken Sie auf **OK**, um die Parameter zu speichern.
4. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf das Dropdownfeld **Ressourcengruppe**, und klicken Sie dann auf **Neu**, um eine neue Ressourcengruppe zu erstellen. Die Ressourcengruppe ist ein Container, in dem der Cluster, das abhängige Speicherkonto und andere verknüpfte Ressourcen gruppiert werden.
5. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.
6. Klicken Sie auf **Erstellen**. Sie sehen daraufhin eine neue Kachel mit der Bezeichnung **Bereitstellung für Vorlagenbereitstellung wird gesendet**. Das Erstellen eines Clusters dauert ca. 20 Minuten. Wenn der Cluster erstellt wurde, öffnen Sie ihn, indem Sie im Portal auf das Clusterblatt klicken.

Löschen Sie den Cluster, wenn Sie das Tutorial beendet haben. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen. Anweisungen zum Löschen eines Clusters finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-management-portal.md#delete-clusters).


##Ausführen von Hive-Abfragen

[Ambari](hdinsight-hadoop-manage-ambari.md)-Ansichten stellen verschiedene Dienstprogramme über eine Webseite bereit. Eines der Dienstprogramme ist die Hive-Ansicht. In diesem Abschnitt verwenden Sie die Hive-Ansicht, um Hive-Abfragen in Ihrem HDInsight-Cluster auszuführen. Andere Methoden zum Ausführen von Hive-Abfragen finden Sie unter [Verwenden von Hive und HiveQL mit Hadoop in HDInsight zum Analysieren einer Apache Log4j-Beispieldatei](hdinsight-use-hive.md).

1. Navigieren Sie zu **https://&lt;ClusterName.azurehdinsight.net**, wobei &lt;ClusterName> der Cluster ist, den Sie im letzten Abschnitt zum Öffnen von Ambari erstellt haben.
2. Geben Sie den Hadoop-Benutzernamen und das Kennwort wie im letzten Abschnitt festgelegt ein. Der Standard-Benutzername lautet **admin**.
3. Öffnen Sie die **Hive-Ansicht** wie auf dem folgenden Screenshot dargestellt:

    ![Ambari-Ansichten auswählen](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. Fügen Sie im Abschnitt __Abfrage-Editor__ der Seite die folgenden HiveQL-Anweisungen in das Arbeitsblatt ein:

		SHOW tables;
5. Klicken Sie auf __Ausführen__. Der Abschnitt __Abfrageprozessergebnisse__ sollte unterhalb des Abfrage-Editors angezeigt werden und Informationen über den Auftrag enthalten. 

    Nach Abschluss der Abfrage werden im Abschnitt __Abfrageprozessergebnisse__ die Ergebnisse des Vorgangs angezeigt. Sie sehen eine Tabelle mit dem Namen **hivesampletable**. Die ist eine Hive-Beispieltabelle mit allen HDInsight-Clustern.
    
    > [AZURE.TIP] Beachten Sie die Dropdownliste __Ergebnisse speichern__ in der oberen linken Ecke des Abschnitts __Abfrageprozessergebnisse__; hiermit können Sie entweder die Ergebnisse herunterladen oder auf HDInsight-Speicher als CSV-Datei speichern.
6. Wiederholen Sie die Schritte 4 und 5, um die folgende Abfrage ausführen:

        SELECT * FROM hivesampletable;

Wenn Sie einen Hive-Auftrag abgeschlossen haben, können Sie [die Ergebnisse in eine Azure SQL-Datenbank oder eine SQL Server-Datenbank exportieren](hdinsight-use-sqoop-mac-linux.md) und [die Abfrageergebnisse mithilfe von Excel visualisieren](hdinsight-connect-excel-power-query.md). Weitere Informationen zum Verwenden von Hive in HDInsight finden Sie unter [Verwenden von Hive und HiveQL mit Hadoop in HDInsight zum Analysieren einer Apache Log4j-Beispieldatei](hdinsight-use-hive.md).

## Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mithilfe einer ARM-Vorlage einen Linux-basierten HDInsight-Cluster erstellen und wie Sie einfache Hive-Abfragen ausführen.

Weitere Informationen zur Datenanalyse mit HDInsight finden Sie in den folgenden Artikeln:

- Weitere Informationen zum Verwenden von Hive mit HDInsight, z. B. zum Ausführen von Hive-Abfragen in Visual Studio, finden Sie im Artikel zum [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

- Informationen zu Pig, einer Sprache zum Transformieren von Daten, finden Sie im Artikel zum [Verwenden von Pig mit HDInsight][hdinsight-use-pig].

- Informationen zu MapReduce, einer Möglichkeit zum Schreiben von Programmen, die Daten in Hadoop verarbeiten, finden Sie im Artikel zu [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce].

- Informationen zur Verwendung der HDInsight-Tools für Visual Studio zum Analysieren von Daten in HDInsight finden Sie unter [Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Wenn Sie mit eigenen Daten arbeiten und mehr darüber wissen möchten, wie Daten in HDInsight gespeichert werden oder wie HDInsight auf Daten zugreift, finden Sie weitere Informationen in folgenden Artikeln:

- Informationen zur Verwendung von Azure-Blob-Speicher durch HDInsight finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Informationen zum Hochladen von Daten in HDInsight finden Sie im Artikel zum [Hochladen von Daten zu HDInsight][hdinsight-upload-data].

Weitere Informationen zum Erstellen und Verwalten von HDInsight-Clustern finden Sie in folgenden Artikeln:

- Informationen zum Verwalten eines Linux-basierten HDInsight-Clusters finden Sie unter [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md).

- Informationen zu den Optionen, die Sie beim Erstellen eines HDInsight-Clusters auswählen können, finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

- Wenn Sie mit Linux und Hadoop vertraut sind und detaillierte Informationen zu Hadoop in HDInsight benötigen, finden Sie diese unter [Informationen zur Verwendung von HDInsight unter Linux](hdinsight-hadoop-linux-information.md). Dieser Artikel enthält u. a. folgende Informationen:

	* URLs für im Cluster gehostete Dienste, z. B. Ambari und WebHCat
	* Speicherort von Hadoop-Dateien und -Beispielen im lokalen Dateisystem
	* Verwendung von Azure-Speicher (WASB) anstelle von HDFS als Standarddatenspeicher


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0218_2016-->