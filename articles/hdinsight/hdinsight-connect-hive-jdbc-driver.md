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
 ms.date="03/04/2016"
 ms.author="larryfr"/>

#Herstellen einer Verbindung mit Hive unter Azure HDInsight per Hive-JDBC-Treiber

[AZURE.INCLUDE [ODBC-JDBC-Selektor](../../includes/hdinsight-selector-odbc-jdbc.md)]

In diesem Dokument erfahren Sie, wie Sie JDBC über eine Java-Anwendung nutzen, um Hive-Abfragen per Remoteverbindung an einen HDInsight-Cluster zu senden. Es wird erläutert, wie Sie eine Verbindung von SQuirreL SQL Client aus herstellen und wie Sie eine programmgesteuerte Verbindung von Java aus herstellen.

Weitere Informationen zur Hive-JDBC-Schnittstelle finden Sie unter [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Hadoop für einen HDInsight-Cluster. Linux-basierte oder Windows-basierte Cluster funktionieren.

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL ist eine JDBC-Clientanwendung.

Zum Erstellen und Ausführen der Beispiel-Java-Anwendung, die Sie über einen Link in diesem Artikel aufrufen können, benötigen Sie Folgendes:

* [Java Developer Kit (JDK) Version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) oder höher.

* [Apache Maven](https://maven.apache.org). Maven ist ein Projekterstellungssystem für Java-Projekte, das für das diesem Artikel zugeordnete Projekt verwendet wird.

##Verbindungszeichenfolge

JDBC-Verbindungen mit einem HDInsight-Cluster unter Azure werden über 443 hergestellt, und der Datenverkehr wird per SSL geschützt. Das öffentliche Gateway, hinter dem sich die Cluster befinden, leitet den Datenverkehr an den Port um, auf dem von HiveServer2 gelauscht wird. Eine typische Verbindungszeichenfolge sieht wie folgt aus:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres HDInsight-Clusters.

##Authentifizierung

Beim Herstellen der Verbindung müssen Sie den Administratornamen und das Kennwort für den HDInsight-Cluster verwenden, um sich beim Clustergateway zu authentifizieren. Beim Herstellen der Verbindung in JDBC-Clients wie SQuirreL SQL müssen Sie den Administratornamen und das Kennwort in den Clienteinstellungen eingeben.

In einer Java-Anwendung müssen Sie den Namen und das Kennwort beim Herstellen einer Verbindung eingeben. Beispielsweise wird mit dem folgenden Java-Code eine neue Verbindung geöffnet, indem die Verbindungszeichenfolge, der Administratorname und das Kennwort verwendet werden:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Herstellen einer Verbindung mit dem SQuirreL SQL-Client

SQuirreL SQL ist ein JDBC-Client, der für die Remoteausführung von Hive-Abfragen mit Ihrem HDInsight-Cluster verwendet werden kann. Die folgenden Schritte führen Sie durch das Herunterladen und Konfigurieren der Treiber für Hive. Dabei wird davon ausgegangen, dass Sie SQuirreL SQL bereits installiert haben.

1. Kopieren Sie die Hive-JDBC-Treiber aus Ihrem HDInsight-Cluster.

    * Bei __Linux-basiertem HDInsight__ gehen Sie folgendermaßen vor, um die erforderlichen JAR-Dateien herunterzuladen.

        1. Erstellen Sie ein neues Verzeichnis, das die Dateien enthalten soll. Beispiel: `mkdir hivedriver`.

        2. Ändern Sie die Verzeichnisse an einer Eingabeaufforderung, Bash, PowerShell oder einer anderen Befehlszeile in das neue Verzeichnis, und verwenden Sie die folgenden Befehle, um die Dateien aus dem HDInsight-Cluster zu kopieren.

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            Ersetzen Sie __USERNAME__ durch den Namen des SSH-Benutzerkontos für den Cluster. Ersetzen Sie __CLUSTERNAME__ durch den Namen des HDInsight-Clusters.

            > [AZURE.NOTE] In Windows-Umgebungen müssen Sie anstelle von scp das Dienstprogramm PSCP verwenden. Sie können es unter [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) herunterladen.

    * Bei __Windows-basiertem HDInsight__ gehen Sie folgendermaßen vor, um die JAR-Dateien herunterzuladen.

        1. Wählen Sie im Azure-Portal den HDInsight-Cluster aus, und wählen Sie dann das Symbol __Remotedesktop__ aus.

            ![Symbol „Remotedesktop“](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. Stellen Sie auf dem Blatt „Remotedesktop“ mithilfe der Schaltfläche __Verbinden__ eine Verbindung mit dem Cluster her. Wenn Remotedesktop nicht aktiviert ist, geben Sie einen Benutzernamen und ein Kennwort in das Formular ein, und wählen Sie dann __Aktivieren__ aus, um Remotedesktop für den Cluster zu aktivieren.

            ![Blatt „Remotedesktop“](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            Nachdem Sie __Verbinden__ ausgewählt haben, wird eine RDP-Datei heruntergeladen. Verwenden Sie diese Datei, um den Remotedesktopclient zu starten. Wenn Sie dazu aufgefordert werden, verwenden Sie den Benutzernamen und das Kennwort, die Sie für den Remotedesktopzugriff eingegeben haben.

        3. Nachdem die Verbindung hergestellt wurde, kopieren Sie die folgenden Dateien aus der Remotedesktopsitzung auf Ihren lokalen Computer. Speichern Sie sie in einem lokalen Verzeichnis mit dem Namen `hivedriver`.

            * C:\\apps\\dist\\hive-0.14.0.2.2.9.1-7\\lib\\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\lib\\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] Die in den Pfaden und Dateinamen enthaltenen Versionsnummern können für Ihren Cluster anders lauten.

        4. Trennen Sie die Remotedesktopsitzung, sobald Sie mit dem Kopieren der Dateien fertig sind.

3. Starten Sie die SQuirreL SQL-Anwendung. Wählen Sie auf der linken Seite des Fensters __Drivers__ aus.

    ![Registerkarte „Drivers“ auf der linken Seite des Fensters](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. Wählen Sie aus den Symbolen oben im Dialogfeld __Drivers__ das Symbol __+__ aus, um einen neuen Treiber zu erstellen.

    ![Treibersymbole](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. Fügen Sie im Dialogfeld „Add Driver“ die folgenden Informationen hinzu:

    * __Name:__ Hive
    * __Example URL:__ jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * __Extra Class Path:__ Fügen Sie mit der Schaltfläche „Add“ die JAR-Dateien hinzu, die Sie zuvor heruntergeladen haben.
    * __Class Name:__ org.apache.hive.jdbc.HiveDriver

    ![Dialogfeld „Add Driver“](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    Klicken Sie auf __OK__, um die Einstellungen zu speichern.

6. Wählen Sie auf der linken Seite des Fensters „SQuirreL SQL“ die Option __Aliases__ aus. Klicken Sie dann auf das Symbol __+__, um einen neuen Verbindungsalias zu erstellen.

    ![Hinzufügen eines neuen Alias](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. Verwenden Sie für das Dialogfeld __Add Alias__ die folgenden Werte:

    * __Name:__ Hive on HDInsight
    * __Driver:__ Wählen Sie in der Dropdownliste den Treiber __Hive__ aus.
    * __URL:__ jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres HDInsight-Clusters.

    * __User Name:__ Der Name des Clusteranmeldekontos für den HDInsight-Cluster. Der Standardwert lautet `admin`.
    * __Password:__ Das Kennwort für das Clusteranmeldekonto. Dies ist ein Kennwort, das Sie beim Erstellen des HDInsight-Clusters angegeben haben.

    ![Dialogfeld „Add Alias“](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    Überprüfen Sie mithilfe der Schaltfläche __Test__, ob die Verbindung funktioniert. Wenn das Dialogfeld __Connect to: Hive on HDInsight__ angezeigt wird, wählen Sie __Connect__ aus, um den Test durchzuführen. Wenn der Test erfolgreich ist, wird das Dialogfeld __Connection successful__ angezeigt.

    Verwenden Sie die Schaltfläche __OK__ unten im Dialogfeld __Add Alias__, um den Verbindungsalias zu speichern.

8. Wählen Sie oben in SQuirreL SQL in der Dropdownliste __Connect to__ die Option __Hive on HDInsight__ aus. Wenn Sie dazu aufgefordert werden, wählen Sie __Connect__ aus.

    ![Verbindungsdialogfeld](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. Nachdem die Verbindung hergestellt wurde, geben Sie im Dialogfeld für die SQL-Abfrage die folgende Abfrage ein, und wählen Sie das Symbol __Run__ aus. Die Ergebnisse der Abfrage sollten im Ergebnisbereich angezeigt werden.

        select * from hivesampletable limit 10;

    ![Dialogfeld für die SQL-Abfrage einschließlich der Ergebnisse](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##Herstellen einer Verbindung von einer Beispiel-Java-Anwendung aus

Ein Beispiel für die Nutzung eines Java-Clients zum Abfragen von Hive in HDInsight ist unter [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) verfügbar. Befolgen Sie die Anleitung im Repository, um das Beispiel zu erstellen und auszuführen.

##Nächste Schritte

Nachdem Sie erfahren haben, wie Sie JDBC mit Hive verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Hochladen von Daten in HDInsight](hdinsight-upload-data.md)
* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0309_2016-->