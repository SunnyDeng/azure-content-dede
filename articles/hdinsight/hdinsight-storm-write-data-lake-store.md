<properties
pageTitle="Verwenden von Azure Data Lake-Speicher mit Apache Storm in HDInsight"
description="Erfahren Sie, wie Daten aus einer Apache Storm-Topologie in HDInsight in Azure Data Lake-Speicher geschrieben werden. In diesem Dokument und dem dazugehörigen Beispiel wird veranschaulicht, wie die Komponente HdfsBolt zum Schreiben von Daten in Data Lake-Speicher verwendet werden kann."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/28/2016"
ms.author="larryfr"/>

#Verwenden von Azure Data Lake-Speicher mit Apache Storm und HDInsight

Azure Data Lake-Speicher ist ein mit HDFS-kompatibler Cloudspeicherdienst, der für Ihre Daten hohe Durchsatzraten, Verfügbarkeit, Stabilität und Zuverlässigkeit bietet. In diesem Dokument erfahren Sie, wie Sie eine Java-basierte Storm-Topologie verwenden, um Daten mithilfe der Komponente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) aus dem Funktionsumfang von Apache Storm in den Azure Data Lake-Speicher zu schreiben.

> [AZURE.IMPORTANT] Die in diesem Dokument verwendete Beispieltopologie basiert auf Komponenten im Funktionsumfang von Storm in HDInsight-Clustern, die ggf. überarbeitet werden müssen, um bei Verwenden mit anderen Apache Storm-Clustern mit dem Azure Data Lake-Speicher zusammenarbeiten zu können.

##Voraussetzungen

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) oder höher
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Ein Azure-Abonnement
* Ein Storm-in-HDInsight-Cluster In diesem Dokument sind Informationen zum Erstellen eines Clusters enthalten, der Azure Data Lake-Speicher verwenden kann.

###Konfigurieren von Umgebungsvariablen

Bei der Installation von Java und dem JDK auf Ihrer Entwicklungsworkstation können die folgenden Umgebungsvariablen festgelegt werden. Sie sollten dennoch prüfen, ob die Variablen vorhanden sind und korrekte Werte für Ihr System enthalten.

* __JAVA\_HOME__ – sollte auf das Verzeichnis verweisen, in dem die Java-Laufzeitumgebung (Java Runtime Environment, JRE) installiert ist. Für eine Unix- oder Linux-Distribution sollte z. B. ein Wert wie `/usr/lib/jvm/java-7-oracle` verwendet werden. Unter Windows sollte der Wert so ähnlich sein wie `c:\Program Files (x86)\Java\jre1.7`.

* __PATH__ – sollte die folgenden Pfade enthalten:

    * __JAVA\_HOME__ (oder den entsprechenden Pfad)
    
    * __JAVA\_HOME\\bin__ (oder den entsprechenden Pfad)
    
    * Das Verzeichnis, in dem Maven installiert ist.

##Implementierung der Topologie

Das Beispiel in diesem Dokument wurde in Java geschrieben und verwendet die folgenden Komponenten:

* __TickSpout__: Generiert die Daten, die von anderen Komponenten in der Topologie verwendet werden.

* __PartialCount__: Zählt vom TickSpout generierte Ereignisse.

* __FinalCount__: Aggregiert Zählerdaten von PartialCount.

* __ADLStoreBolt__: Schreibt Daten mit der Komponente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) in den Azure Data Lake-Speicher.

Das Projekt mit dieser Topologie steht unter [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) als Download bereit.

###Grundlegendes zu ADLStoreBolt

ADLStoreBolt ist der Name für die HdfsBolt-Instanz in der Topologie, die Daten in Azure Data Lake schreibt. Dies ist keine spezielle von Microsoft erstellte HdfsBolt-Version. Sie basiert stattdessen auf Konfigurationswerten in „core-site.xml“ sowie auf Hadoop-Komponenten, die in Azure HDInsight für die Kommunikation mit Data Lake enthalten sind.

Wenn Sie einen HDInsight-Cluster erstellen, können Sie ihn einem Azure Data Lake-Speicher zuordnen. Einträge werden dann für den ausgewählten Data Lake-Speicher in „core-site.xml“ geschrieben, die anschließend von Komponenten wie „hadoop-client“ und „hadoop-hdfs“ zum Ermöglichen der Kommunikation mit dem Data Lake-Speicher genutzt werden.

> [AZURE.NOTE] Microsoft hat Code zu Apache Hadoop- und Storm-Projekten beigetragen, der die Kommunikation mit Azure Data Lake-Speicher und Azure Blob Storage ermöglicht. Doch diese Funktionalität ist ggf. nicht standardmäßig in anderen Hadoop- und Storm-Distributionen enthalten.

Die Konfiguration für HdfsBolt in der Topologie ist wie folgt:

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
		.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
      	.withRecordFormat(recordFormat)
      	.withFileNameFormat(fileNameFormat)
      	.withRotationPolicy(rotationPolicy)
      	.withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
Wenn Sie mit HdfsBolt vertraut sind, sehen Sie, dass dies mit Ausnahme der URL eine Standardkonfiguration ist. Die URL enthält den Pfad zum Stammverzeichnis Ihres Azure Data Lake-Speichers.

Da HdfsBolt zum Schreiben in Azure Data Lake-Speicher verwendet wird und nur eine URL-Änderung vorliegt, sollte Sie eine vorhandene Topologie verwenden, die mithilfe von HdfsBolt in HDFS oder WASB schreibt, und eine einfache Änderung zur Verwendung von Azure Data Lake-Speicher vornehmen können.

##Erstellen eines HDInsight-Clusters und von Data Lake-Speicher

Erstellen Sie einen neuen Storm-in-HDInsight-Cluster, indem Sie die Schritte im Dokument [Bereitstellen eines HDInsight-Clusters mit Data Lake-Speicher mithilfe des Azure-Portals](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) ausführen. Anhand der Schritte in diesem Dokument erstellen Sie einen neuen HDInsight-Cluster und Azure Data Lake-Speicher.

> [AZURE.IMPORTANT] Wenn Sie den HDInsight-Cluster erstellen, müssen Sie __Storm__ als Clustertyp auswählen. Das Betriebssystem kann Windows oder Linux sein.

##Erstellen und Packen der Topologie

1. Laden Sie das Beispielprojekt von [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) in Ihre Entwicklungsumgebung herunter.

2. Öffnen Sie die Datei `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` in einem Editor, und suchen Sie die Zeile mit `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Ändern Sie __MYDATALAKE__ in den Namen des Azure Data Lake-Speichers, den Sie beim Erstellen Ihres HDInsight-Servers verwendet haben.

3. Ändern Sie über eine Eingabeaufforderung, ein Terminal oder eine Shellsitzung Verzeichnisse in das Stammverzeichnis des heruntergeladenen Projekts, und führen Sie die folgenden Befehle aus, um die Topologie zu erstellen und zu packen.

        mvn compile
        mvn package
    
    Nach Abschluss des Erstellungs- und Packvorgangs finden Sie ein neues Verzeichnis namens `target` vor, das die Datei `StormToDataLakeStore-1.0-SNAPSHOT.jar` enthält. Diese Datei enthält die kompilierte Topologie.

##Bereitstellen und Ausführen in auf Linux basierendem HDInsight

Wenn Sie einen auf Linux basierenden Storm-in-HDInsight-Cluster erstellt haben, führen Sie die folgenden Schritte aus, um die Topologie bereitzustellen und auszuführen.

1. Führen Sie den folgenden Befehl aus, um die Topologie in den HDInsight-Cluster zu kopieren. Ersetzen Sie __USER__ durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie __CLUSTERNAME__ durch den Namen des Clusters.

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    Geben Sie nach Aufforderung das Kennwort ein, das Sie beim Erstellen des SSH-Benutzers für den Cluster verwendet haben. Wenn Sie einen öffentlichen Schlüssel anstelle eines Kennworts verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben.
    
    > [AZURE.NOTE] Wenn Sie einen Windows-Client für die Entwicklung verwenden, steht der Befehl `scp` ggf. nicht zur Verfügung. In diesem Fall können Sie `pscp` verwenden, das unter [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) zur Verfügung steht.

2. Führen Sie nach Abschluss des Uploads den folgenden Befehl aus, um mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster herzustellen. Ersetzen Sie __USER__ durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie __CLUSTERNAME__ durch den Namen des Clusters.

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    Geben Sie nach Aufforderung das Kennwort ein, das Sie beim Erstellen des SSH-Benutzers für den Cluster verwendet haben. Wenn Sie einen öffentlichen Schlüssel anstelle eines Kennworts verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben.
    
    > [AZURE.NOTE] Wenn Sie einen Windows-Client für die Entwicklung verwenden, befolgen Sie die Informationen unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md) zur Verwendung des PuTTY-Clients zum Herstellen einer Verbindung mit dem Cluster.
    
3. Führen Sie nach dem Herstellen der Verbindung den folgenden Befehl aus, um die Topologie zu starten:

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    Dadurch wird die Topologie mit dem Anzeigenamen `datalakewriter` gestartet.

##Bereitstellen und Ausführen in auf Windows basierendem HDInsight

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu HTTPS://CLUSTERNAME.azurehdinsight.net, wobei __CLUSTERNAME__ der Name Ihres HDInsight-Clusters ist. Geben Sie bei Aufforderung den Benutzernamen „admin“ (`admin`) und das Kennwort ein, das Sie für dieses Konto verwendet haben, als der Cluster erstellt wurde.

2. Wählen Sie im Storm-Dashboard __Durchsuchen__ in der Dropdownliste __JAR-Datei__ aus, und wählen Sie im Verzeichnis `target` die Datei „StormToDataLakeStore-1.0-SNAPSHOT.jar“ aus. Verwenden Sie die folgenden Werte für die weiteren Einträge im Formular:

    * Klassenname: com.microsoft.example.StormToDataLakeStore
    * Zusätzliche Parameter: datalakewriter
    
    ![Abbildung des Storm-Dashboards](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Klicken Sie auf die Schaltfläche __Senden__, um die Topologie hochzuladen und zu starten. Im Ergebnisfeld unter der Schaltfläche __Senden__ sollten ähnliche Informationen enthalten sein, nachdem die Topologie gestartet wurde:

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##Anzeigen von Ausgabedaten

Es gibt mehrere Möglichkeiten zum Anzeigen der Daten. In diesem Abschnitt verwenden wir das Azure-Portal und den `hdfs`-Befehl, um die Daten anzuzeigen.

> [AZURE.NOTE] Sie sollten die Topologien vor der Prüfung der Ausgabedaten mehrere Minuten ausführen, damit Daten mit mehreren Dateien im Azure Data Lake-Speicher synchronisiert wurden.

* __Im [Azure-Portal](https://portal.azure.com)__: Wählen Sie im Portal, den Azure Data Lake-Speicher aus, den Sie mit HDInsight verwendet haben.

    > [AZURE.NOTE] Wenn Sie den Data Lake-Speicher nicht am Dashboard im Azure-Portal angeheftet haben, finden Sie ihn, indem Sie links unten in der Liste erst __Durchsuchen__, dann __Data Lake-Speicher__ und schließlich den Speicher auswählen.
    
    Wählen Sie aus den Symbolen oben im Data Lake-Speicher __Daten-Explorer__ aus.
    
    ![Symbol „Daten-Explorer“](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    Wählen Sie als Nächstes den Ordner __stormdata__ aus. Eine Liste mit Textdateien sollte angezeigt werden.
    
    ![Textdateien](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    Wählen Sie eine der Dateien aus, um ihren Inhalt anzuzeigen.

* __Im Cluster__: Wenn Sie mit dem HDInsight-Cluster über SSH (Linux-Cluster) verbunden oder Remotedesktop (Windows-Cluster) verbunden sind, können Sie die Daten mit folgendem Befehl anzeigen. Ersetzen Sie __DATALAKE__ durch den Namen des Data Lake-Speichers.

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    Dadurch werden die im Verzeichnis gespeicherten Textdateien verkettet und Informationen wie die folgenden angezeigt:
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##Beenden der Topologie

Storm-Topologien werden ausgeführt, bis sie beendet werden oder der Cluster gelöscht wird. Gehen Sie wie folgt vor, um die Topologien zu beenden.

__Für Linux-basiertes HDInsight__:

Führen Sie in einer SSH-Sitzung mit dem Cluster den folgenden Befehl aus:

    storm kill datalakewriter

__Für Windows-basiertes HDInsight__:

1. Wählen Sie im Storm-Dashboard (https://CLUSTERNAME.azurehdinsight.net,) oben auf der Seiten den Link __Storm-Benutzeroberfläche__ aus.

2. Nach dem die Storm-Benutzeroberfläche geladen wurde, wählen Sie den Link __datalakewriter__ aus.

    ![Link zu datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. Wählen Sie im Abschnitt __Topologieaktionen__ den Befehl __Beenden__ aus, und klicken Sie im eingeblendeten Dialogfeld auf „OK“.

    ![Topologieaktionen](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## Löschen des Clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Nächste Schritte

Nachdem Sie erfahren haben, wie Daten mithilfe von Storm in Azure Data Lake-Speicher geschrieben werden, können Sie sich mit anderen [Storm-Beispielen für HDInsight](hdinsight-storm-example-topology.md) beschäftigen.

<!---HONumber=AcomDC_0309_2016-->