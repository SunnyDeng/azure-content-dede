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
   	ms.date="03/16/2016"
   	ms.author="jgao"/>

# Hadoop-Tutorial: Erste Schritte bei der Verwendung von Linux-basiertem Hadoop in HDInsight

> [AZURE.SELECTOR]
- [Linux-basiert](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Windows-basiert](hdinsight-hadoop-tutorial-get-started-windows.md)

Erfahren Sie, wie Sie Linux-basierte [Hadoop](http://hadoop.apache.org/)-Cluster in HDInsight erstellen und Hive-Aufträge in HDInsight ausführen. [Apache Hive](https://hive.apache.org/) ist die beliebteste Komponente im Hadoop-Ökosystem. Derzeit verfügt HDInsight über vier verschiedene Clustertypen: [Hadoop](hdinsight-hadoop-introduction), [Spark](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) und [Storm](hdinsight-storm-overview.md). Jeder Clustertyp unterstützt eine andere Gruppe von Komponenten. Alle vier Clustertypen unterstützen Hive. Eine Liste mit den unterstützten Komponenten in HDInsight finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen](hdinsight-component-versioning.md).

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

### Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement:** Um ein kostenloses Testkonto für die Dauer eines Monats zu erstellen, navigieren Sie zu [azure.microsoft.com/free](https://azure.microsoft.com/free).

## Cluster erstellen

Die meisten Hadoop-Aufträge sind Batchaufträge. Sie erstellen einen Cluster, führen einige Aufträge aus und löschen dann den Cluster. In diesem Abschnitt erstellen Sie einen Linux-basierten Hadoop-Cluster in HDInsight mithilfe einer [Azure ARM-Vorlage](../resource-group-template-deploy.md). ARM-Vorlagen sind vollständig anpassbar, sodass es sehr einfach ist, Azure-Ressourcen wie HDInsight zu erstellen. Für dieses Tutorial sind keine Erfahrungen mit Azure-ARM-Vorlagen erforderlich. Andere Methoden zur Erstellung von Clustern und Informationen zu den in diesem Tutorial verwendeten Eigenschaften finden Sie unter [Erstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md). Die in diesem Tutorial verwendete ARM-Vorlage befindet sich in einem öffentlichen Blob-Container ([https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json)).

1. Klicken Sie auf die folgende Abbildung, um sich an Azure anzumelden und die ARM-Vorlage im Azure-Portal zu öffnen. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/de-DE/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Geben Sie auf dem Blatt **Parameter** Folgendes ein:

    ![HDInsight Linux, Erste Schritte mit der ARM-Vorlage im Portal](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).

    - **ClusterName**: Geben Sie einen Namen für den Hadoop-Cluster ein, den Sie erstellen möchten.
    - **Cluster-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet **admin**.
    - **SSH-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet **sshuser**. Sie können auch einen anderen Namen festlegen. 
    
    Andere Parameter sind zum Durcharbeiten dieses Tutorials optional. Ändern Sie sie nicht.
    
    Jeder Cluster verfügt über eine Abhängigkeit von einem Azure Blob Storage-Konto. Es wird in der Regel als Standardspeicherkonto bezeichnet. Der HDInsight-Cluster und das dazugehörige Speicherkonto müssen sich in derselben Azure-Region befinden. Beim Löschen von Clustern wird das Speicherkonto nicht gelöscht. In der Vorlage wird der standardmäßige Speicherkontoname als Clustername mit dem Zusatz „store“ definiert.
    
3. Klicken Sie auf **OK**, um die Parameter zu speichern.
4. Geben Sie im Blatt **Benutzerdefinierte Bereitstellung** den Text **Neuer Ressourcengruppenname** ein, um eine neue Ressourcengruppe zu erstellen. Die Ressourcengruppe ist ein Container, in dem der Cluster, das abhängige Speicherkonto und andere Elemente gruppiert werden. Der Speicherort der Ressourcengruppe kann vom Clusterspeicherort abweichen.
5. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.
6. Vergewissern Sie sich, dass das Kontrollkästchen **An Dashboard anheften** aktiviert ist, und klicken Sie auf **Erstellen**. Es wird eine neue Kachel mit dem Titel **Vorlagenbereitstellung bereitstellen** angezeigt. Das Erstellen eines Clusters dauert ca. 20 Minuten. 
7.  Nachdem der Cluster erstellt wurde, wird die Beschriftung der Kachel in den von Ihnen angegebenen Ressourcengruppennamen geändert. Im Portal werden außerdem automatisch zwei Blätter mit dem Cluster und den Clustereinstellungen geöffnet. 

    ![HDInsight Linux, Erste Schritte mit Clustereinstellungen](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).

    Es werden zwei Ressourcen aufgeführt: der Cluster und das Standardspeicherkonto.

##Ausführen von Hive-Abfragen

[Apache Hive](hdinsight-use-hive.md) ist die am häufigsten in HDInsight verwendete Komponente. Es gibt viele Verfahren zum Ausführen von Hive-Aufträgen in HDInsight. In diesem Tutorial verwenden Sie die Ambari Hive-Ansicht aus dem Portal, um einige Hive-Aufträge auszuführen. Andere Methoden zum Übermitteln von Hive-Aufträgen finden Sie unter [Verwenden von Hive in HDInsight](hdinsight-use-hive.md).

1. Navigieren Sie zu **https://&lt;ClusterName>.azurehdinsight.net**, wobei &lt;ClusterName> der Cluster ist, den Sie im letzten Abschnitt zum Öffnen von Ambari erstellt haben.
2. Geben Sie den Hadoop-Benutzernamen und das Kennwort wie im letzten Abschnitt festgelegt ein. Der Standard-Benutzername lautet **admin**.
3. Öffnen Sie die **Hive-Ansicht** wie im folgenden Screenshot dargestellt:

    ![Ambari-Ansichten auswählen](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. Fügen Sie im Abschnitt __Abfrage-Editor__ der Seite die folgenden HiveQL-Anweisungen in das Arbeitsblatt ein:

		SHOW TABLES;

    >[AZURE.NOTE] Für Hive ist ein Semikolon erforderlich.
        
5. Klicken Sie auf __Ausführen__. Der Abschnitt __Abfrageprozessergebnisse__ sollte unterhalb des Abfrage-Editors angezeigt werden und Informationen über den Auftrag enthalten.

    Nach Abschluss der Abfrage werden im Abschnitt __Abfrageprozessergebnisse__ die Ergebnisse des Vorgangs angezeigt. Sie sehen eine Tabelle mit dem Namen **hivesampletable**. Die ist eine Hive-Beispieltabelle mit allen HDInsight-Clustern.

    ![HDInsight Hive-Ansichten](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).

6. Wiederholen Sie die Schritte 4 und 5, um die folgende Abfrage ausführen:

        SELECT * FROM hivesampletable;

    > [AZURE.TIP] Beachten Sie die Dropdownliste __Ergebnisse speichern__ in der oberen linken Ecke des Abschnitts __Abfrageprozessergebnisse__; hiermit können Sie entweder die Ergebnisse herunterladen oder auf HDInsight-Speicher als CSV-Datei speichern.

7. Klicken Sie auf **Verlauf**, um eine Liste mit den Aufträgen zu erhalten.

Nachdem Sie einen Hive-Auftrag abgeschlossen haben, können Sie [die Ergebnisse in eine Azure SQL-Datenbank oder eine SQL Server-Datenbank exportieren](hdinsight-use-sqoop-mac-linux.md) und [die Abfrageergebnisse mithilfe von Excel visualisieren](hdinsight-connect-excel-power-query.md). Weitere Informationen zum Verwenden von Hive in HDInsight finden Sie unter [Verwenden von Hive und HiveQL mit Hadoop in HDInsight zum Analysieren einer Apache Log4j-Beispieldatei](hdinsight-use-hive.md).

##Bereinigen des Tutorials

Nach Abschluss des Tutorials kann es ratsam sein, den Cluster zu löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

>[AZURE.NOTE] Mit der [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) können Sie HDInsight-Cluster bedarfsgesteuert erstellen und eine TimeToLive-Einstellung konfigurieren, um die Cluster automatisch zu löschen.

**So löschen Sie den Cluster bzw. das Standardspeicherkonto**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im Dashboard des Portals auf die Kachel mit dem Ressourcengruppennamen, den Sie beim Erstellen des Clusters verwendet haben.
3. Klicken Sie auf dem Blatt mit den Ressourcen auf **Löschen**, um die Ressourcengruppe zu löschen, die den Cluster und das Standardspeicherkonto enthält. Oder klicken Sie auf der Kachel **Ressourcen** auf den Clusternamen und dann auf dem Clusterblatt auf **Löschen**. Beachten Sie, dass das Speicherkonto beim Löschen der Ressourcengruppe ebenfalls gelöscht wird. Wenn Sie das Speicherkonto beibehalten möchten, müssen Sie auswählen, dass nur der Cluster gelöscht werden soll.

## Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mithilfe einer ARM-Vorlage einen Linux-basierten HDInsight-Cluster erstellen und wie Sie einfache Hive-Abfragen ausführen.

Weitere Informationen zur Datenanalyse mit HDInsight finden Sie in den folgenden Artikeln:

- Weitere Informationen zum Verwenden von Hive mit HDInsight, z. B. zum Ausführen von Hive-Abfragen in Visual Studio, finden Sie im Artikel zum [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

- Informationen zu Pig, einer Sprache zum Transformieren von Daten, finden Sie im Artikel zum [Verwenden von Pig mit HDInsight][hdinsight-use-pig].

- Informationen zu MapReduce, einer Möglichkeit zum Schreiben von Programmen, die Daten in Hadoop verarbeiten, finden Sie im Artikel zu [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce].

- Informationen zur Verwendung der HDInsight-Tools für Visual Studio zum Analysieren von Daten in HDInsight finden Sie unter [Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Wenn Sie mit eigenen Daten arbeiten und mehr darüber wissen möchten, wie Daten in HDInsight gespeichert werden oder wie HDInsight auf Daten zugreift, finden Sie weitere Informationen in folgenden Artikeln:

- Informationen zur Verwendung von Azure-Blob-Speicher durch HDInsight finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Informationen zum Hochladen von Daten in HDInsight finden Sie im Artikel zum [Hochladen von Daten zu HDInsight][hdinsight-upload-data].

Weitere Informationen zum Erstellen und Verwalten von HDInsight-Clustern finden Sie in folgenden Artikeln:

- Informationen zum Verwalten eines Linux-basierten HDInsight-Clusters finden Sie unter [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md).

- Informationen zu den Optionen, die Sie beim Erstellen eines HDInsight-Clusters auswählen können, finden Sie unter [Erstellen von HDInsight unter Linux mit benutzerdefinierten Optionen](hdinsight-hadoop-provision-linux-clusters.md).

- Wenn Sie mit Linux und Hadoop vertraut sind und detaillierte Informationen zu Hadoop in HDInsight benötigen, finden Sie diese unter [Informationen zur Verwendung von HDInsight unter Linux](hdinsight-hadoop-linux-information.md). Dieser Artikel enthält u. a. folgende Informationen:

	* URLs für im Cluster gehostete Dienste, z. B. Ambari und WebHCat
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

<!---HONumber=AcomDC_0330_2016-->