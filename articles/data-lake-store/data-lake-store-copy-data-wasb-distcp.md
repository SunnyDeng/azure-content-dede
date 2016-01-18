<properties 
   pageTitle="Kopieren von Daten in und aus WASB in Data Lake-Speicher mit Distcp| Microsoft Azure"
   description="Verwenden des Distcp-Tools zum Kopieren von Daten aus Azure Storage-Blobs in den Data Lake-Speicher und umgekehrt" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# Kopieren von Daten zwischen Azure Storage-Blobs und Data Lake-Speicher mithilfe von Distcp

Nachdem Sie einen HDInsight-Cluster erstellt haben, der Zugriff auf ein Data Lake-Speicherkonto besitzt, können Sie Hadoop-Ökosystemtools wie Distcp zum Kopieren von Daten aus einem HDInsight-Clusterspeicher (WASB) in ein Data Lake-Speicherkonto (und umgekehrt) verwenden. Dieser Artikel enthält Anweisungen zur Vorgehensweise.

##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktiviertes Azure-Abonnement** für die öffentliche Vorschauversion des Data Lake-Speichers. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup). 
- **HDInsight-Cluster** mit Zugriff auf ein Data Lake-Speicherkonto. Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.

## Verwenden von Distcp über Remotedesktop (Windows-Cluster) oder SSH (Linux-Cluster)

Ein HDInsight-Cluster enthält das Distcp-Dienstprogramm, das zum Kopieren von Daten aus verschiedenen Quellen in einen HDInsight-Cluster verwendet werden kann. Wenn Sie den HDInsight-Cluster so konfiguriert haben, dass er den Data Lake-Speicher als zusätzlichen Speicher verwendet, kann das Distcp-Dienstprogramm ebenfalls direkt zum Kopieren von Daten in ein bzw. aus einem Data Lake-Speicherkonto genutzt werden. In diesem Abschnitt wird die Verwendung des Distcp-Dienstprogramms erläutert.

1. Stellen Sie bei einem Windows-Cluster eine Remoteverbindung mit einem HDInsight-Cluster her, der Zugriff auf ein Data Lake-Speicherkonto besitzt. Anweisungen hierzu finden Sie unter [Herstellen einer Verbindung zu Clustern mit RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Öffnen Sie auf dem Cluster-Desktop die Hadoop-Befehlszeile.

	Stellen Sie bei einem Linux-Cluster die Verbindung mit dem Cluster über SSH her. Informationen finden Sie unter [Verbinden mit einem Linux-basierten HDInsight-Cluster](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Führen Sie die Befehle von der SSH-Eingabeaufforderung aus.

3. Überprüfen Sie, ob Sie auf die Azure Storage-Blobs (WASB) zugreifen können. Führen Sie den folgenden Befehl aus:

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	Dadurch sollte eine Liste der Inhalte im Speicherblob angezeigt werden.

4. Überprüfen Sie zudem, ob Sie vom Cluster auf das Data Lake-Speicherkonto zugreifen können. Führen Sie den folgenden Befehl aus:

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	Dadurch sollte eine Liste der Dateien bzw. Ordner im Data Lake-Speicherkonto bereitgestellt werden.

5. Verwenden Sie Distcp zum Kopieren von Daten aus WASB in ein Data Lake-Speicherkonto.

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	Dadurch wird der Inhalt des Ordners **/example/data/gutenberg/** in WASB in den Ordner **/myfolder** im Data Lake-Speicherkonto kopiert.

6. Verwenden Sie Distcp ebenso zum Kopieren von Daten aus einem Data Lake-Speicherkonto in WASB.

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	Dadurch wird der Inhalt des Ordners **/myfolder** im Data Lake-Speicherkonto in den Ordner **/example/data/gutenberg/** in WASB kopiert.

## Weitere Informationen

- [Kopieren von Daten aus Azure Storage-Blobs in den Data Lake-Speicher](data-lake-store-copy-data-azure-storage-blob.md)
- [Sichern von Daten in Data Lake-Speicher](data-lake-store-secure-data.md)
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0107_2016-->