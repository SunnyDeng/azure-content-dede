<properties
 pageTitle="Verwenden von JDBC zum Abfragen von Hive für Azure HDInsight"
 description="Sie erfahren, wie Sie JDBC zum Herstellen einer Verbindung mit Hive für Azure HDInsight verwenden und Abfragen für in der Cloud gespeicherte Daten per Remoteverbindung ausführen."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/23/2015"
 ms.author="larryfr"/>

#Herstellen einer Verbindung mit Hive unter Azure HDInsight per Hive-JDBC-Treiber

[AZURE.INCLUDE [ODBC-JDBC-Selektor](../../includes/hdinsight-selector-odbc-jdbc.md)]

In diesem Dokument erfahren Sie, wie Sie JDBC über eine Java-Anwendung nutzen, um Hive-Abfragen per Remoteverbindung an einen HDInsight-Cluster zu senden. Weitere Informationen zur Hive-JDBC-Schnittstelle finden Sie unter [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Hadoop für einen HDInsight-Cluster. Linux-basierte oder Windows-basierte Cluster funktionieren.

* [Java Developer Kit (JDK) Version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) oder höher.

* [Apache Maven](https://maven.apache.org). Maven ist ein Projekterstellungssystem für Java-Projekte, das für das diesem Artikel zugeordnete Projekt verwendet wird.

##Verbindungszeichenfolge

JDBC-Verbindungen mit einem HDInsight-Cluster unter Azure werden über 443 hergestellt, und der Datenverkehr wird per SSL geschützt. Das öffentliche Gateway, hinter dem sich die Cluster befinden, leitet den Datenverkehr an den Port um, auf dem von HiveServer2 gelauscht wird. Eine typische Verbindungszeichenfolge sieht wie folgt aus:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##Authentifizierung

Beim Herstellen der Verbindung müssen Sie den Administratornamen und das Kennwort für den HDInsight-Cluster angeben. Hiermit wird die Anforderung für das Gateway authentifiziert. Beispielsweise wird mit dem folgenden Java-Code eine neue Verbindung geöffnet, indem die Verbindungszeichenfolge, der Administratorname und das Kennwort verwendet werden:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Abfragen

Nachdem die Verbindung hergestellt wurde, können Sie Abfragen für Hive ausführen. Mit dem folgenden Java-Code wird __SELECT__ für eine Tabelle ausgeführt, und die Ergebnisse werden auf drei Zeilen beschränkt. Anschließend werden die Ergebnisse angezeigt:

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Java-Beispielprojekt

Ein Beispiel für die Nutzung eines Java-Clients zum Abfragen von Hive für HDInsight ist unter [https://github.com/Blackmist/hdinsight-hive-jdbc](https://github.com/Blackmist/hdinsight-hive-jdbc) verfügbar. Befolgen Sie die Anleitung im Repository, um das Beispiel zu erstellen und auszuführen.

##Nächste Schritte

Nachdem Sie erfahren haben, wie Sie JDBC mit Hive verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Hochladen von Daten in HDInsight](hdinsight-upload-data.md)
* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Oct15_HO3-->