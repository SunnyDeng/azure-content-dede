<properties linkid="hdinsight-use-oozie-with-hdinsight" urlDisplayName="Use Oozie with HDInsight" pageTitle="Use Oozie with HDInsight | Azure" metaKeywords="" description="Use Oozie with HDInsight, a big data solution. Learn how to define an Oozie workflow, and submit an Oozie job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Oozie with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden von Oozie mit HDInsight
=================================

Hier lernen Sie, wie Sie einen Workflow definieren und diesen in HDInsight ausführen. Informationen zum Oozie-Koordinator finden Sie unter [Use time-based Oozie Coordinator with HDInsight](../hdinsight-use-oozie-coordinator-time/) (Verwenden des zeitbasierten Oozie-Koordinators mit HDInsight, in englischer Sprache).

**Geschätzter Zeitaufwand:** 40 Minuten

Themen in diesem Artikel
------------------------

1.  [Was ist Oozie?](#whatisoozie)
2.  [Voraussetzungen](#prerequisites)
3.  [Definition einer Oozie-Workflowdatei](#defineworkflow)
4.  [Bereitstellen des Oozie-Projekts und Vorbereiten des Lernprogramms](#deploy)
5.  [Ausführen des Workflows](#run)
6.  [Nächste Schritte](#nextsteps)

Was ist Oozie?
--------------

Apache Oozie ist ein Workflow-/Koordinierungssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stack integriert und unterstützt Hadoop-Jobs für Apache MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Es kann auch zur Planung von systemspezifischen Jobs wie Java-Programmen oder Shell-Skripts verwendet werden.

Der Workflow, den Sie implementieren, besteht aus zwei Aktionen:

![Workflow-Diagramm](./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png)

1.  Eine Hive-Aktion führt ein HiveQL-Skript aus, um zu zählen, wie oft jeder Protokollebenentyp in einer log4j-Protokolldatei auftritt. Jedes log4j-Protokoll besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens [LOG LEVEL] befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt. Beispiel:

         2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
         2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
         2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
         ...

    Das ausgegebene Hive-Skript sieht etwa so aus:

         [DEBUG] 434
         [ERROR] 3
         [FATAL] 1
         [INFO]  96
         [TRACE] 816
         [WARN]  4

    Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight](/de-de/documentation/articles/hdinsight-use-hive/).

2.  Eine Sqoop-Aktion exportiert die ausgegebene HiveQL-Aktion in eine Tabelle in der Azure SQL-Datenbank. Weitere Informationen über Sqoop finden Sie unter [Verwenden von Sqoop mit HDInsight](../hdinsight-use-sqoop/).

> [WACOM.NOTE] Informationen ztu den unterstützten Oozie-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](/de-de/documentation/articles/hdinsight-component-versioning/).

> [WACOM.NOTE] Dieses Lernprogramm ist für die HDInsight-Clusterversionen 2.1 und 3.0 gültig. Dieser Artikel wurde nicht auf dem HDInsight-Emulator getestet.

Voraussetzungen
---------------

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/manage/install-and-configure-windows-powershell/). Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Ausführen von Windows PowerShell-Skripts](http://technet.microsoft.com/en-us/library/ee176949.aspx).
-   **Einen HDInsight-Cluster**. Informationen zur Erstellung eines HDInsight-Clusters finden Sie unter [Bereitstellen von HDInsight-Clustern](/de-de/documentation/articles/hdinsight-provision-clusters/) oder [Erste Schritte mit HDInsight](/de-de/documentation/articles/hdinsight-get-started/). Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

	<table border = "1">
	<tr><th>Clustereigenschaft</th><th>PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>HDInsight-Clustername</td><td>$clusterName</td><td></td><td>Das HDInsight-Cluster, auf dem Sie dieses Lernprogramm ausführen.</td></tr>
	<tr><td>HDInsight-Cluster-Benutzername</td><td>$clusterUsername</td><td></td><td>Der Benutzername des HDInsight-Clusterbenutzers. </td></tr>
	<tr><td>HDInsight-Cluster-Benutzerkennwort </td><td>$clusterPassword</td><td></td><td>Das Benutzerkennwort für den HDInsight-Cluster. </td></tr>
	<tr><td>Azure-Speicherkontoname</td><td>$storageAccountName</td><td></td><td>AEin im HDInsight-Cluster verfügbares Azure-Speicherkonto. Verwenden Sie für dieses Lernprogramm das Standard-Speicherkonto, das Sie während der Bereitstellung des Clusters angegeben haben.</td></tr>
	<tr><td>Azure-Blob-Containername</td><td>$containerName</td><td></td><td>Verwenden Sie in diesem Beispiel den Azure-Blob-Speichercontainer, der für das Standarddateisystem des HDInsight-Clusters verwendet wird. Standardmäßig hat dieser denselben Namen wie der HDInsight-Cluster.</td></tr>
	</table>

-   **Eine Azure SQL-Datenbank**. Sie müssen eine Firewall-Regel für den SQL-Datenbankserver konfigurieren, um Zugriff auf Ihre Arbeitsstation zu erlauben. Anweisungen zur Erstellung einer SQL-Datenbank und zur Konfiguration einer Firewall erhalten Sie unter [Erste Schritte mit Azure SQL-Datenbanken](../sql-database-get-started/). Dieser Artikel beschreibt ein PowerShell-Skript zur Erstellung der SQL-Datenbanktabelle, die für dieses Lernprogramm benötigt wird.

	<table border = "1">
	<tr><th>SQL-Datenbankeigenschaft</th><th>PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>Servername der SQL-Datenbank</td><td>$sqlDatabaseServer</td><td></td><td>Der SQL-Datenbankserver, auf den Sqoop die Daten exportiert. </td></tr>
	<tr><td>SAnmeldename der SQL-Datenbank</td><td>$sqlDatabaseLogin</td><td></td><td>Anmeldename der SQL-Datenbank.</td></tr>
	<tr><td>Anmeldekennwort der SQL-Datenbank</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Anmeldekennwort der SQL-Datenbank.</td></tr>
	<tr><td>SQL-Datenbankname</td><td>$sqlDatabaseName</td><td></td><td>Die Azure SQL-Datenbank, in die Sqoop die Daten exportiert. </td></tr>
	</table>

	> [WACOM.NOTE] Eine Azure SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn die Firewall-Einstellung deaktiviert ist, müssen Sie sie im Azure-Verwaltungsportal aktivieren. Anweisungen zum Erstellen einer SQL-Datenbank und zum Konfigurieren von Firewall-Regeln finden Sie unter [Erstellen und Konfigurieren einer SQL-Datenbank](../sql-database-create-configure/).

> [WACOM.NOTE] Tragen Sie die Werte in die Tabellen ein. Dies wird Ihnen helfen, wenn Sie dieses Lernprogramm durcharbeiten.

Definition des Oozie-Workflows und des entsprechenden HiveQL-Skripts
--------------------------------------------------------------------

Oozie-Workflowdefinitionen sind in hPDL (einer XML-Prozessdefinitionssprache) geschrieben. Der Standarddateiname für den Workflow lautet *workflow.xml*. Sie speichern die Worflowdatei lokal und stellen sie später in diesem Lernprogramm mit Azure PowerShell im HDInsight-Cluster bereit.

Mit der Hive-Aktion in diesem Workflow wird eine HiveQL-Skriptdatei aufgerufen. Diese Skriptdatei enthält drei HiveQL-Anweisungen:

1.  **Die Anweisung DROP TABLE** löscht die log4j-Hive-Tabelle, falls eine solche existiert.
2.  **Die Anweisung CREATE TABLE** erstellt eine externe log4j-Hive-Tabelle, die auf den
3.  Speicherort der log4j-Protokolldatei verweist. Das Feld-Trennzeichen ist ",". Das Standard-Trennzeichen ist "\\n". Die externe Hive-Tabelle wird verwendet, damit die Datendatei nicht aus dem ursprünglichen Speicherort entfernt wird, falls Sie den Oozie.-Workflow mehrmals ausführen möchten.
4.  **Die Anweisung INSERT OVERWRITE** zählt, wie oft jeder Protokollebenentyp in der log4j-Hive-Tabelle vorkommt, und speichert das Ergebnis an einem Azure Storage-Blob (WASB)-Speicherort.

Es gibt ein bekanntes Problem mit dem Hive-Pfad. Dieses Problem tritt auf, wenn Sie einen Oozie-Job übermitteln. Anweisungen zur Behebung dieses Problems finden Sie im [TechNet-Wiki](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx).

**Definieren der vom Workflow aufzurufenden HiveQL-Skriptdatei**

1.  Erstellen Sie eine Textdatei mit dem folgenden Inhalt:

         DROP TABLE ${hiveTableName};
         CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
         INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    In diesem Skript werden drei Variablen verwendet:

    -   ${hiveTableName}
    -   ${hiveDataFolder}
    -   ${hiveOutputFolder}

    Die Workflow-Definitionsdatei (in diesem Lernprogramm workflow.xml) leitet diese Werte in der Laufzeit an dieses HiveQL-Skript weiter.

2.  Speichern Sie die Datei unter **C:\\Tutorials\\UseOozie\\useooziewf.hql** mit der Kodierung **ANSI (ASCII)**. (Verwenden Sie Notepad, wenn Ihr Texteditor diese Option nicht unterstützt.) Diese Skriptdatei wird später in diesem Lernprogramm im HDInsight-Cluster bereitgestellt.

**Definieren eines Workflows**

1.  Erstellen Sie eine Textdatei mit dem folgenden Inhalt:

         <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
             <start to = "RunHiveScript"/> 
                
             <action name="RunHiveScript">
                 <hive xmlns="uri:oozie:hive-action:0.2">
                     <job-tracker>${jobTracker}</job-tracker>
                     <name-node>${nameNode}</name-node>
                     <configuration>
                         <property>
                             <name>mapred.job.queue.name</name>
                             <value>${queueName}</value>
                         </property>
                     </configuration>
                     <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                     <param>hiveDataFolder=${hiveDataFolder}</param>
                     <param>hiveOutputFolder=${hiveOutputFolder}</param>
                 </hive>
                 <ok to="RunSqoopExport"/>
                 <error to="fail"/>
             </action>
            
             <action name="RunSqoopExport">
                 <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                     <job-tracker>${jobTracker}</job-tracker>
                     <name-node>${nameNode}</name-node>
                     <configuration>
                         <property>
                             <name>mapred.compress.map.output</name>
                             <value>true</value>
                         </property>
                     </configuration>
                 <arg>export</arg>
                 <arg>--connect</arg> 
                 <arg>${sqlDatabaseConnectionString}</arg> 
                 <arg>--table</arg>
                 <arg>${sqlDatabaseTableName}</arg> 
                 <arg>--export-dir</arg> 
                 <arg>${hiveOutputFolder}</arg> 
                 <arg>-m</arg> 
                 <arg>1</arg>
                 <arg>--input-fields-terminated-by</arg>
                 <arg>"\001"</arg>
                 </sqoop>
                 <ok to="end"/>
                 <error to="fail"/>
             </action>
            
             <kill name="fail">
                 <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
             </kill>
            
            <end name="end"/>
         </workflow-app>

    In diesem Workflow werden zwei Aktionen definiert. Die erste Aktion ist *RunHiveScript*. Wenn die Aktion *erfolgreich* verläuft, ist die nächste Aktion *RunSqoopExport*.

    Das RunHiveScript weist mehrere Variablen auf. Sie leiten die Werte weiter, wenn Sie den Oozie-Job von Ihrer Arbeitsstation mit Azure PowerShell übermitteln.

	<table border = "1">
	<tr><th>Workflow-Variablen</th><th>Beschreibung</th></tr>
	<tr><td>${jobTracker}</td><td>Gibt die URL des Hadoop-JobTrackers an. Verwenden Sie in den HDInsight-Clusterversionen 2.0 und 3.0 <strong>jobtrackerhost:9010</strong></td></tr>
	<tr><td>${nameNode}</td><td>Gibt die URL des Hadoop-NameNodes an. Verwenden Sie die WASB-Adresse des Standarddateisystems. Beispiel: <i>wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>.</td></tr>
	<tr><td>${queueName}</td><td>Gibt den Namen der Warteschlange an, an die der Job übermittelt wird. Verwenden Sie <strong>default</strong>.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Hive-Aktionsvariable</th><th>Beschreibung</th></tr>
	<tr><td>${hiveDataFolder}</td><td>Das Quellverzeichnis für den Befehl zur Erstellung einer Hive-Tabelle.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Der Ausgabeordner für die Anweisung INSERT OVERWRITE.</td></tr>
	<tr><td>${hiveTableName}</td><td>Der Name der Hive-Tabelle, die auf die log4j-Datendateien verweist.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Sqoop-Aktionsvariable</th><th>Beschreibung</th></tr>
	<tr><td>${sqlDatabaseConnectionString}</td><td>SQL-Datenbank-Verbindungszeichenfolge.</td></tr>
	<tr><td>${sqlDatabaseTableName}</td><td>Die SQL-Datenbanktabelle, in die die Daten exportiert werden.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Der Ausgabeordner für die Hive-Anweisung INSERT OVERWRITE. Dies ist derselbe Ordner wie für den Sqoop-Export (export-dir).</td></tr>
	</table>

    Weitere Informationen zum Oozie-Workflow und zur Anwendung von Workflow-Aktionen finden Sie in der [Dokumentation zu Apache Oozie 4.0](http://oozie.apache.org/docs/4.0.0/) (für HDInsight-Clusterversion 3.0) oder in der [Dokumentation zu Apache Oozie 3.3.2](http://oozie.apache.org/docs/3.3.2/) (für HDInsight-Clusterversion 2.1).

2.  Speichern Sie die Datei unter **C:\\Tutorials\\UseOozie\\workflow.xml** mit der Kodierung ANSI (ASCII). (Verwenden Sie Notepad, wenn Ihr Texteditor diese Option nicht unterstützt.)

Bereitstellen des Oozie-Projekts und Vorbereiten des Lernprogramms
------------------------------------------------------------------

Führen Sie ein Azure PowerShell-Skript aus, um Folgendes durchzuführen:

-   Kopieren des HiveQL-Skripts (useoozie.hql) in den Azure-Blob-Speicher, wasb:///tutorials/useoozie/useoozie.hql.
-   Kopieren der workflow.xml in wasb:///tutorials/useoozie/workflow.xml.
-   Kopieren der Datendatei (/example/data/sample.log) in wasb:///tutorials/useoozie/data/sample.log.
-   Erstellen einer SQL-Datenbanktabelle zur Speicherung der Sqoop-Exportdaten. Der Tabellenname ist *log4jLogCount*.

**Grundlagen der HDInsight-Speicherung**

HDInsight verwendet Azure Blob-Speicher zur Datenspeicherung. Der Speicher wird *WASB* oder *Azure Storage-Blob* genannt. WASB ist Microsofts Implementierung von HDFS in Azure-Blobspeicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/de-de/documentation/articles/hdinsight-use-blob-storage/).

Während des Prozesses zur Bereitstellung eines HDInsight-Clusters werden genau wie in HDFS ein Azure-Speicherkonto und ein bestimmter Blob-Speichercontainer aus diesem Konto als Standard-Dateisystem festgelegt. Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern](/de-de/documentation/articles/hdinsight-provision-clusters/). Um das in diesem Lernprogramm verwendete PowerShell-Skript zu vereinfachen, werden alle Dateien im Container des Standarddateisystems gespeichert. Dieser befindet sich unter */tutorials/useoozie*. Dieser Container hat standardmäßig denselben Namen wie der HDInsight-Cluster. Die WASB-Syntax lautet:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] In der HDInsight-Clusterversion 3.0 wird nur die *wasb://*-Syntax unterstützt. Die ältere *asv://*-Syntax wird in HDInsight-Clustern der Versionen 2.1 und 1.6 unterstützt, aber nicht in den Clusterversionen 3.0 und höher.

> [WACOM.NOTE] Der WASB-Pfad ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/de-de/documentation/articles/hdinsight-use-blob-storage/).

Auf eine im Standarddateisystem-Container gespeicherte Datei kann in HDINsight über jeden der folgenden URIs zugegriffen werden (in diesem Beispiel mit der workflow.xml):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

    tutorials/useoozie/workflow.xml

**Grundlagen zu internen und externen Hive-Tabellen**

Es gibt ein paar Dinge, die Sie über interne und externe Hive-Tabellen wissen müssen:

-   Mit dem Befehl CREATE TABLE erstellen Sie eine interne Tabelle, auch "verwaltete Tabelle" genannt. Die Datendatei muss sich im Standardcontainer befinden.
-   Mit dem Befehl CREATE TABLE wird die Datendatei in den Ordner /hive/warehouse/ im Standardcontainer kopiert.
-   Mit dem Befehl CREATE EXTERNAL TABLE wird eine externe Tabelle erstellt. Die Datendatei kann sich außerhalb des Standardcontainers befinden.
-   Mit dem Befehl CREATE EXTERNAL TABLE wird das Verschieben der Datendatei verhindert.
-   Mit dem Befehl CREATE EXTERNAL TABLE sind keine Unterordner unter dem in der Klausel LOCATION angegebenen Ordner erlaubt. Dies ist der Grund, aus dem das Lernprogramm eine Kopie der Datei sample.log erstellt.

Weitere Informationen finden Sie unter [HDInsight: Hive Internal and External Tables Intro](http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx) (Einführung zu internen und externen Hive-Tabellen, in englischer Sprache).

**Vorbereiten des Lernprogramms**

1.  Öffnen Sie Windows PowerShell ISE. (Geben Sie auf dem Startbildschirm von Windows 8 **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**.) Siehe [Start Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx) (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).
2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Diese Methode zum Hinzufügen einer Abonnementsverbindung ist zeitlich begrenzt: Nach 12 Stunden muss das Cmdlet erneut ausgeführt werden.

    > [WACOM.NOTE] Wenn Sie über mehrere Azure-Abonnements verfügen und nicht das Standard-Abonnement verwenden möchten, wählen Sie das aktuelle Abonnement mithilfe des Cmdlets **Select-AzureSubscription** aus.

3.  Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten sechs Variablen ein.

         # WASB variables
         $storageAccountName = "<StorageAccountName>"
         $containerName = "<BlobStorageContainerName>"
            
         # SQL-Datenbankvariablen
         $sqlDatabaseServer = "<SQLDatabaseServerName>"  
         $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
         $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
         $sqlDatabaseName = "<SQLDatabaseName>"  
         $sqlDatabaseTableName = "log4jLogsCount"
            
         # Oozie-Dateien für das Lernprogramm  
         $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
         $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
            
         # WASB-Ordner zur Speicherung der Oozie-Lernprogrammdateien.
         $destFolder = "tutorials/useoozie"  # Hier NICHT den langen Pfad verwenden

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

4.  Hängen Sie im Skriptbereich den folgenden Code an das Skript an:

         # Ein Speicherkontextobjekt erstellen
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         function uploadOozieFiles()
         {      
             Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
             Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
             Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
         }
                    
         function prepareHiveDataFile()
         {
             Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
             Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
         }
                    
         function prepareSQLDatabase()
         {
             # SQL-Abfragezeichenfolge zur Erstellung der Tabelle log4jLogsCount
             $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                     [Level] [nvarchar](10) NOT NULL,
                     [Total] float,
                 CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                 (
                 [Level] ASC
                 )
                 )"
                    
             #Tabelle log4jLogsCount erstellen
             Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
             $conn = New-Object System.Data.SqlClient.SqlConnection
             $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
             $conn.open()
             $cmd = New-Object System.Data.SqlClient.SqlCommand
             $cmd.connection = $conn
             $cmd.commandtext = $cmdCreateLog4jCountTable
             $cmd.executenonquery()
                    
             $conn.close()
         }
                    
         # upload workflow.xml, coordinator.xml, and ooziewf.hql
         uploadOozieFiles;
                    
         # make a copy of example/data/sample.log to example/data/log4j/sample.log
         prepareHiveDataFile;
            
         # Tabelle log4jlogsCount in der SQL-Datenbank erstellen
         prepareSQLDatabase;

5.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe sollte der Folgenden ähneln:

    ![Ergebnis der Vorbereitung des Lernprogramms](./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png)

Ausführen des Oozie-Projekts
----------------------------

Azure PowerShell bietet aktuell keine Cmdlets zur Definition von Oozie-Jobs. Sie können jedoch das PowerShell-Cmdlet Invoke-RestMethod verwenden, um Oozie-Webdienste aufzurufen. Die Oozie-Webdienste-API ist eine HTTP REST JSON-API. Weitere Informationen zur Oozie-Webdienste-API finden Sie in der [Dokumentation zu Apache Oozie 4.0](http://oozie.apache.org/docs/4.0.0/) (für HDInsight-Clusterversion 3.0) oder in der [Dokumentation zu Apache Oozie 3.3.2](http://oozie.apache.org/docs/3.3.2/) (für HDInsight-Clusterversion 2.1).

**Übermitteln eines Oozie-Jobs**

1.  Öffnen Sie Windows PowerShell ISE. (Geben Sie auf dem Startbildschirm von Windows 8 **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**.) Siehe [Start Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx) (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).

2.  Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten zehn Variablen ein. (Überspringen Sie dabei die 6. Variable \$storageUriI.)

         #HDInsight-Clustervariablen
         $clusterName = "<HDInsightClusterName>"
         $clusterUsername = "<HDInsightClusterUsername>"
         $clusterPassword = "<HDInsightClusterUserPassword>"
            
         #Azure Blob-Speicher (WASB)-Variablen
         $storageAccountName = "<StorageAccountName>"
         $storageContainerName = "<BlobContainerName>"
         $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
            
         #Azure SQL-Datenbankvariablen
         $sqlDatabaseServer = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
         $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
         $sqlDatabaseName = "<SQLDatabaseName>"  
            
         #Oozie WF-Variablen
         $oozieWFPath="$storageUri/tutorials/useoozie"  # Der Standardname ist workflow.xml. Sie müssen den Dateinamen nicht angeben.
         $waitTimeBetweenOozieJobStatusCheck=10
            
         #Hive-Aktionsvariablen
         $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
         $hiveTableName = "log4jlogs"
         $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
         $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
            
         #Sqoop-Aktionsvariablen
         $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
         $sqlDatabaseTableName = "log4jLogsCount"

         $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

3.  Hängen Sie Folgendes an das Skript an. Dieser Teil legt die Oozie-Nutzlast fest:

         #OoziePayload zur Übermittlung an den Oozie-Webdienst
         $OoziePayload =  @"
         <
         xml version="1.0" encoding="UTF-8"
         >
         <configuration>
            
            <property>
                <name>nameNode</name>
                <value>$storageUrI</value>
            </property>
            
            <property>
                <name>jobTracker</name>
                <value>jobtrackerhost:9010</value>
            </property>
            
            <property>
                <name>queueName</name>
                <value>default</value>
            </property>
            
            <property>
                <name>oozie.use.system.libpath</name>
                <value>true</value>
            </property>
            
            <property>
                <name>hiveScript</name>
                <value>$hiveScript</value>
            </property>
            
            <property>
                <name>hiveTableName</name>
                <value>$hiveTableName</value>
            </property>
            
            <property>
                <name>hiveDataFolder</name>
                <value>$hiveDataFolder</value>
            </property>
            
            <property>
                <name>hiveOutputFolder</name>
                <value>$hiveOutputFolder</value>
            </property>
            
            <property>
                <name>sqlDatabaseConnectionString</name>
                <value>"$sqlDatabaseConnectionString"</value>
            </property>
            
            <property>
                <name>sqlDatabaseTableName</name>
                <value>$SQLDatabaseTableName</value>
            </property>
            
            <property>
                <name>user.name</name>
                <value>admin</value>
            </property>
            
            <property>
                <name>oozie.wf.application.path</name>
                <value>$oozieWFPath</value>
            </property>
            
         </configuration>
         "@

4.  Hängen Sie Folgendes an das Skript an. Dieser Teil dient zur Statusüberprüfung des Oozie-Webdienstes:

         Write-Host "Checking Oozie server status..." -ForegroundColor Green
         $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
         $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
            
         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $oozieServerSatus = $jsonResponse[0].("systemMode")
         Write-Host "Oozie server status is $oozieServerSatus..."

5.  Hängen Sie Folgendes an das Skript an. Dieser Teil erstellt und startet einen Oozie-Job:

         # Oozie-Job erstellen
         Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
         Write-Host "`n--------`n$OoziePayload`n--------"
         $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
         $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $oozieJobId = $jsonResponse[0].("id")
         Write-Host "Oozie job id is $oozieJobId..."

         # Oozie-Job starten
         Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
         $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "
         action=start"
         $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6.  Hängen Sie Folgendes an das Skript an. Dieser Teil dient zur Statusüberprüfung des Oozie-Jobs:

         # Jobstatus abrufen
         Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
         Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

         Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
         $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "
         show=info"
         $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $JobStatus = $jsonResponse[0].("status")

         while($JobStatus -notmatch "SUCCEEDED|KILLED")
         {
             Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
             Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $JobStatus = $jsonResponse[0].("status")
         }

         Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7.  Wenn Ihr HDinsight-Cluster die Version 2.1 ist, ersetzen Sie "https://\$clusterName.azurehdinsight.net:443/oozie/v2/" durch "https://\$clusterName.azurehdinsight.net:443/oozie/v1/". Die HDInsight-Clusterversion 2.1 unterstützt nicht Version 2 der Webdienste nicht.

8.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe sieht etwa so aus:

    ![Workflow-Ausgabe des ausgeführten Lernprogramms](./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png)

9.  Stellen Sie eine Verbindung zur SQL-Datenbank her, um die exportierten Daten anzuzeigen.

**Prüfen des Job-Fehlerprotokolls**

Wenn Sie Probleme in einem Workflow beheben möchten, finden Sie die Oozie-Protokolldatei unter *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* oder *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* im Cluster-Hauptknoten. Informationen über RDP finden Sie unter [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal](/de-de/documentation/articles/hdinsight-administer-use-management-portal/).

**Erneutes Ausführen des Tutorials**

Führen Sie folgende Schritte aus, um den Workflow erneut auszuführen:

-   Löschen der Hive-Skript-Ausgabedatei
-   Löschen der Daten in der Datei log4jLogsCount

Hier sehen Sie ein Beispiel-PowerShell-Skript, das Sie verwenden können:

    $storageAccountName = "<WindowsAzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL-Datenbankvariablen
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie erfahren, wie ein Oozie-Workflow definiert wird und wie ein Oozie-Job mit der Azure-PowerShell ausgeführt wird. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Use time-based Oozie Coordinator with HDInsight (Verwenden des zeitbasierten Oozie-Koordinators mit HDInsight, in englischer Sprache)](../hdinsight-use-oozie-coordinator-time/)
-   [Erste Schritte mit HDInsight](/de-de/documentation/articles/hdinsight-get-started/)
-   [Erste Schritte mit dem HDInsight Emulator](/de-de/documentation/articles/hdinsight-get-started-emulator/)
-   [Verwenden von Azure Blob-Speicher mit HDInsight](/de-de/documentation/articles/hdinsight-use-blob-storage/)
-   [Verwalten von HDInsight mit PowerShell](/de-de/documentation/articles/hdinsight-administer-use-powershell/)
-   [Hochladen von Daten zu HDInsight](/de-de/documentation/articles/hdinsight-upload-data/)
-   [Verwenden von Sqoop mit HDInsight](../hdinsight-use-sqoop/)
-   [Verwenden von Hive mit HDInsight](/de-de/documentation/articles/hdinsight-use-hive/)
-   [Verwenden von Pig mit HDInsight](/de-de/documentation/articles/hdinsight-use-pig/)
-   [Entwickeln von C\# Hadoop-Streamingjobs für HDInsight](/de-de/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Entwickeln von Java MapReduce-Programmen für HDInsight](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

