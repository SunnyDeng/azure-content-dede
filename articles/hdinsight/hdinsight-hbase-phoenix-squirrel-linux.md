<properties 
   pageTitle="Verwenden von Apache Phoenix und SQuirreL in HDInsight | Microsoft Azure" 
   description="Erfahren Sie, wie Sie Apache Phoenix in HDInsight verwenden und wie Sie SQuirreL auf Ihrer Arbeitsstation für die Verbindung mit einem HBase-Cluster in HDInsight installieren und konfigurieren." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/04/2016"
   ms.author="jgao"/>

# Verwenden von Apache Phoenix mit Linux-basierten HBase-Clustern in HDInsight  

Erfahren Sie, wie Sie [Apache Phoenix](http://phoenix.apache.org/) in HDInsight und wie Sie SQLLine verwenden. Weitere Informationen zu Phoenix finden Sie unter [Phoenix innerhalb von 15 Minuten oder weniger](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Eine Beschreibung der Phoenix-Grammatik finden Sie unter [Phoenix-Grammatik](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Versionshinweise für Phoenix finden Sie in HDInsight unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?][hdinsight-versions]

##Verwendung von SQLLine
[SQLLine](http://sqlline.sourceforge.net/) ist ein Befehlszeilenprogramm zum Ausführen von SQL.

###Voraussetzungen
Bevor Sie SQLLine verwenden können, benötigen Sie Folgendes:

- **Ein HBase-Cluster in HDInsight**. Informationen zur Bereitstellung von HBase-Cluster finden Sie unter [Erste Schritte mit Apache in HDInsight][hdinsight-hbase-get-started].
- **Verbinden Sie sich per Remotedesktop-Protokoll mit dem HBase-Cluster**. Anweisungen finden Sie in [Verwalten von Hadoop-Clustern in HDInsight mit dem klassischen Azure-Portal][hdinsight-manage-portal].


Beim Verbinden mit einem HBase-Cluster müssen Sie die Verbindung zu einem der Zookeeper herstellen. Jeder HDInsight-Cluster verfügt über 3 Zookeeper.

**So finden Sie den Hostnamen des Zookeeper heraus:**

1. Öffnen Sie Ambari, indem Sie zu **https://<ClusterName>.azurehdinsight.net** navigieren.
2. Geben Sie zum Anmelden den HTTP-(Cluster-)Benutzernamen und das Kennwort ein.
3. Klicken Sie im linken Menü auf **ZooKeeper**. Sie sehen die 3 **ZooKeeper-Server** aufgeführt.
4. Klicken Sie auf einen der aufgeführten **ZooKeeper-Server**. Suchen Sie im Zusammenfassungsbereich den **Hostnamen**. Er ähnelt *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Verwendung von SQLLine**

1. Stellen Sie mithilfe von SSH eine Verbindung zum Cluster her. Weitere Anweisungen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md) oder [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows), abhängig vom Betriebssystem Ihres Clientcomputers.

2. Führen Sie unter SSH die folgenden Befehle aus, um SQLLine auszuführen:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Führen Sie die folgenden Befehle aus, um eine HBase-Tabelle zu erstellen, und fügen Sie Daten ein:

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
	
		!tables
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;
        
        !quit

Weitere Informationen finden Sie im [SQLLine-Handbuch](http://sqlline.sourceforge.net/#manual) sowie unter [Phoenix-Grammatik](http://phoenix.apache.org/language/index.html).


 
##Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie Apache Phoenix in HDInsight verwenden. Weitere Informationen finden Sie unter

- [Übersicht HBase mit HDInsight][hdinsight-hbase-overview]\: HBase ist eine Open-Source-NoSQL-Datenbank von Apache, die auf Hadoop aufbaut und wahlfreien Zugriff sowie starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.
- [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet]\: Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können.
- [Konfiguration von HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md): Erfahren Sie, wie Sie HBase-Replikation über zwei Azure-Rechenzentren konfigurieren. 
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment]\: Erfahren Sie, wie Sie in Echtzeit [Stimmungsanalysen](http://en.wikipedia.org/wiki/Sentiment_analysis) bei Big Data mithilfe von HBase in einem Hadoop-Cluster in HDInsight durchführen können.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0309_2016-->