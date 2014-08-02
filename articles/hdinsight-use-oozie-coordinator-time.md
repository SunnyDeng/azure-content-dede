<properties linkid="hdinsight-use-time-based-oozie-coordinator-with-hdinsight" urlDisplayName="Use time-based Oozie Coordinator with HDInsight" pageTitle="Use time-based Oozie Coordinator with HDInsight | Microsoft Azure" metaKeywords="" description="Use time-based Oozie Coordinator with HDInsight, a big data solution. Learn how to define Oozie workflows and coordinators, and submit Oozie coordinator jobs." metaCanonical="" services="hdinsight" documentationCenter="" title="Use time-based Oozie Coordinator with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden von zeitbasierten Oozie-Koordinatoren mit HDInsight
=============================================================

Sie erfahren, wie Workflows und Koordinatoren definiert werden und wie die Koordinatorjobs zeitabhängig ausgelöst werden. Vor der Lektüre dieses Artikels empfiehlt es sich, das Thema [Use Oozie with Hadoop in HDInsight](../hdinsight-use-oozie/) (Verwenden von Oozie mit Hadoop in HDInsight, in englischer Sprache) durchzuarbeiten.

**Geschätzter Zeitaufwand:** 40 Minuten

Themen in diesem Artikel
------------------------

1.  [Was ist Oozie?](#whatisoozie)
2.  [Voraussetzungen](#prerequisites)
3.  [Definieren der Oozie-Workflowdatei](#defineworkflow)
4.  [Bereitstellen des Oozie-Projekts und Vorbereiten des Lernprogramms](#deploy)
5.  [Ausführen des Workflows](#run)
6.  [Nächste Schritte](#nextsteps)

Was ist Oozie?
--------------

Apache Oozie ist ein Workflow-/Koordinationssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stapel integriert und unterstützt Hadoop-Jobs für Apache MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Es kann auch dazu verwendet werden, bestimmte Jobs für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

Der Workflow, den Sie implementieren, enthält zwei Aktionen:

![Workflowdiagramm](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png)

1.  Eine Hive-Aktion führt ein HiveQL-Skript aus, das die Vorkommen der verschiedenen Protokollierungsebenen in einer log4j-Protokolldatei zählt. Jedes log4j-Protokoll besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens [LOG LEVEL] befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt. Beispiel:

         2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
         2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
         2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
         ...

    Das Hive-Skript erzeugt eine Ausgabe ähnlich der folgenden:

         [DEBUG] 434
         [ERROR] 3
         [FATAL] 1
         [INFO]  96
         [TRACE] 816
         [WARN]  4

    Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight](/en-us/documentation/articles/hdinsight-use-hive/).

2.  Mit einer Sqoop-Aktion wird die Ausgabe der HiveQL-Aktion in eine Tabelle der Azure-SQL-Datenbank exportiert. Weitere Informationen über Sqoop finden Sie unter [Verwenden von Sqoop mit HDInsight](../hdinsight-use-sqoop/).

> [WACOM.NOTE] Informationen über unterstützte Oozie-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](/en-us/documentation/articles/hdinsight-component-versioning/).

> [WACOM.NOTE] Dieses Lernprogramm arbeitet mit HDInsight-Clustern der Versionen 2.1 und 3.0. Dieser Artikel wurde nicht im HDInsight-Emulator getestet.

Voraussetzungen
---------------

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/manage/install-and-configure-windows-powershell/). Um PowerShell-Skripts auszuführen, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* festlegen. Weitere Informationen finden Sie unter [Running Windows PowerShell Scripts](http://technet.microsoft.com/en-us/library/ee176949.aspx) (Ausführen von Windows PowerShell-Skripts, in englischer Sprache).
-   **Einen HDInsight-Cluster**. Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Bereitstellen von Clustern in HDInsight](/en-us/documentation/articles/hdinsight-provision-clusters/) oder unter [Erste Schritte mit HDInsight](/en-us/documentation/articles/hdinsight-get-started/). Zum Durcharbeiten des Lernprogramms benötigen Sie die folgenden Daten:

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Clustereigenschaft</th><th data-morhtml="true">PowerShell-Variablenname</th><th data-morhtml="true">Wert</th><th data-morhtml="true">Beschreibung</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight-Clustername</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">Der HDInsight-Cluster, auf dem dieses Lernprogramm ausgeführt wird.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Benutzername für den HDInsight-Cluster</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">Der Benutzername für den HDInsight-Cluster. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Das Benutzerkennwort für den HDInsight-Cluster. </td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">Das Benutzerkennwort für den HDInsight-Cluster.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure-Speicherkontoname</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">Ein Azure-Speicherkonto, das dem HDInsight-Cluster zur Verfügung steht. Verwenden Sie für dieses Lernprogramm das Standard-Speicherkonto, das während der Bereitstellung des Clusters angegeben wurde.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure-Blob-Containername</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">Verwenden Sie für dieses Beispiel den Azure-Blob-Speichercontainer, der für das standardmäßige HDInsight-Clusterdateisystem verwendet wird. Der Container hat normalerweise denselben Namen wie der HDInsight-Cluster.</td></tr>
  </table>

-   **Eine Azure SQL-Datenbank**. Sie müssen eine Firewallregel für den SQL-Datenbankserver konfigurieren, um den Zugriff von Ihrer Arbeitsstation aus zu gestatten. Anweisungen zum Erstellen einer SQL-Datenbank und zum Konfigurieren einer Firewall finden Sie unter [Erste Schritte mit Azure SQL Database](../sql-database-get-started/). Dieser Artikel enthält ein PowerShell-Skript zum Erstellen der SQL-Datenbanktabelle, die für dieses Lernprogramm benötigt wird.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">SQL-Datenbankeigenschaft</th><th data-morhtml="true">PowerShell-Variablenname</th><th data-morhtml="true">Wert</th><th data-morhtml="true">Beschreibung</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Name des SQL-Datenbankservers</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">Der SQL-Datenbankserver, auf den Sqoop Daten exportiert. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Anmeldename für die SQL-Datenbank</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">Anmeldename für die SQL-Datenbank.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Anmeldekennwort für die SQL-Datenbank</td><td data-morhtml="true">$sqlDatabaseLoginPassword</td><td data-morhtml="true"></td><td data-morhtml="true">Anmeldekennwort für die SQL-Datenbank.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Name der SQL-Datenbank</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Die Azure SQL-Datenbank, in die Sqoop Daten exportiert. </td></tr>
  </table>

    > [WACOM.NOTE] Eine Azure-SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn die Firewall-Einstellung deaktiviert ist, müssen Sie sie im Azure-Verwaltungsportal aktivieren. Anweisungen zum Erstellen einer SQL-Datenbank und zum Konfigurieren von Firewall-Regeln finden Sie unter [Erstellen und Konfigurieren einer SQL-Datenbank](../sql-database-create-configure/).

> [WACOM.NOTE] Tragen Sie die Werte in die Tabellen ein. Dies ist beim Durcharbeiten dieses Lernprogramms hilfreich.

Definieren des Oozie-Workflows und des zugehörigen HiveQL-Skripts
-----------------------------------------------------------------

Definitionen von Oozie-Workflows werden in hPDL (eine XML-Prozessdefinitionssprache) geschrieben. Der Standardname der Workflow-Datei lautet *workflow.xml*. Sie speichern die Workflow-Datei lokal und stellen sie später im Lernprogramm im HDInsight-Cluster mit Azure PowerShell bereit.

Von der Hive-Aktion im Workflow wird eine HiveQL-Skriptdatei aufgerufen. Die Skriptdatei enthält drei HiveQL-Anweisungen:

1.  **Die Anweisung DROP TABLE** löscht die log4j-Hive-Tabelle, falls sie vorhanden ist.
2.  **Die Anweisung CREATE TABLE** erstellt eine externe log4j-Hive-Tabelle, die auf den
3.  Speicherort der log4j-Protokolldatei verweist. Das Feldtrennzeichen ist ",". Das Standard-Zeilentrennzeichen is "\\n". Mit einer externen Hive-Tabelle wird vermieden, dass die Datendatei aus dem ursprünglichen Speicherort entfernt wird, falls Sie den Oozie-Workflow mehrmals ausführen möchten.
4.  **Die Anweisung INSERT OVERWRITE** zählt die Vorkommen der verschiedenen Protokollierungsebenen in der log4j-Hive-Tabelle und speichert die Ausgabe in einem Azure Storage - Blob (WASB)-Speicherort.

Es ist bekannt, dass es ein Problem mit dem Hive-Pfad gibt. Dieses Problem tritt auf, wenn Sie einen Oozie-Job senden. Anweisungen zur Behebung des Problems finden Sie im [TechNet Wiki](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx).

**So definieren Sie die HiveQL-Skriptdatei, die vom Workflow aufgerufen werden soll:**

1.  Erstellen Sie eine Textdatei mit folgendem Inhalt:

         DROP TABLE ${hiveTableName};
         CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
         INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Im Skript werden drei Variablen verwendet:

    -   \${hiveTableName}
    -   \${hiveDataFolder}
    -   \${hiveOutputFolder}

    Die Workflow-Definitionsdatei (workflow.xml in diesem Lernprogramm) übergibt diese Werte zur Laufzeit an das HiveQL-Skript.

2.  Speichern Sie die Datei unter **C:\\Tutorials\\UseOozie\\useooziewf.hql**, und verwenden Sie dabei die **ANSI (ASCII)**-Codierung (Verwenden Sie Editor, falls Ihr Texteditor diese Möglichkeit nicht bietet). Die Skriptdatei wird später im Lernprogramm im HDInsight-Cluster bereitgestellt.

**So definieren Sie einen Workflow**

1.  Erstellen Sie eine Textdatei mit folgendem Inhalt:

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
                 <arg>" <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
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
        01"</arg>
                 </sqoop>
                 <ok to="end"/>
                 <error to="fail"/>
             </action>
            
             <kill name="fail">
                 <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
             </kill>
            
            <end name="end"/>
         </workflow-app>

    Im Workflow sind zwei Aktionen definiert. Die Startaktion lautet *RunHiveScript*. Wenn die Ausführung der Aktion *ok* ist, wird die nächste Aktion *RunSqoopExport* ausgeführt.

    Das RunHiveScript enthält mehrere Variablen. Die Werte werden übergeben, wenn Sie den Oozie-Job mit Azure PowerShell über Ihre Arbeitsstation senden.

   <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Workflow-Variablen</th><th data-morhtml="true">Beschreibung</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${jobTracker}</td><td data-morhtml="true">Geben Sie die URL des Hadoop-JobTrackers an. Verwenden Sie <strong data-morhtml="true">jobtrackerhost:9010</strong> bei Clustern der HDInsight-Versionen 2.0 und 3.0.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${nameNode}</td><td data-morhtml="true">Geben Sie die URL des Hadoop-NameNode an. Verwenden Sie die WASB-Adresse des Standarddateisystems. Beispiel: <i data-morhtml="true">wasb://&lt;ContainerName&gt;@&lt;SpeicherkontoName&gt;.blob.core.windows.net</i>.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${queueName}</td><td data-morhtml="true">Gibt den Namen der Warteschlange an, an die der Job gesendet wird. Verwenden Sie <strong data-morhtml="true">default</strong>.</td></tr>
 </table>

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Hive-Aktionsvariable</th><th data-morhtml="true">Beschreibung</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveDataFolder}</td><td data-morhtml="true">Das Quellverzeichnis für den Befehl zum Erstellen der Hive-Tabelle.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">Der Ausgabeordner für die Anweisung INSERT OVERWRITE.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveTableName}</td><td data-morhtml="true">Der Name der Hive-Tabelle, die auf die log4j-Datendateien verweist.</td></tr>
 </table>

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Sqoop-Aktionsvariable</th><th data-morhtml="true">Beschreibung</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseConnectionString}</td><td data-morhtml="true">Verbindungszeichenfolge für die SQL-Datenbank.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseTableName}</td><td data-morhtml="true">Die SQL-Datenbanktabelle, in die die Daten exportiert werden.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">Der Ausgabeordner für die Hive-Anweisung INSERT OVERWRITE. Dieser entspricht dem mit export-dir angegebenen Ordner für den Sqoop-Export.</td></tr>
 </table>

    Weitere Informationen über den Oozie-Workflow und die Verwendung von Workflow-Aktionen finden Sie in der [Apache Oozie 4.0-Dokumentation](http://oozie.apache.org/docs/4.0.0/) (für HDInsight-Cluster der Version 3.0) oder in der [Apache Oozie 3.3.2-Dokumentation](http://oozie.apache.org/docs/3.3.2/) (für HDInsight-Cluster der Version 2.1).

2.  Speichern Sie die Datei unter **C:\\Tutorials\\UseOozie\\workflow.xml**, und verwenden Sie dabei die ANSI (ASCII)-Codierung (Verwenden Sie Editor, falls Ihr Texteditor diese Möglichkeit nicht bietet).

**So definieren Sie einen Koordinator**

1.  Erstellen Sie eine Textdatei mit folgendem Inhalt:

         <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
            <action>
               <workflow>
                  <app-path>${wfPath}</app-path>
               </workflow>
            </action>
         </coordinator-app>

    In der Definitionsdatei werden fünf Variablen verwendet:

    Variable | Beschreibung ---------------|------------ \${coordFrequency} | Job-Anhaltezeit. Die Frequenz wird stets in Minuten ausgedrückt. \${coordStart} | Startzeit für Job. \${coordEnd} | Endzeit für Job. \${coordTimezone} | Oozie verarbeitet Koordinator-Jobs in einer festen Zeitzone ohne Sommerzeit (in der Regel UTC). Diese Zeitzone wird als 'Oozie-Verarbeitungszeitzone' bezeichnet. \${wfPath} | Der Pfad für die Datei workflow.xml. Wenn Sie nicht den standardmäßigen Workflow-Dateinamen (workflow.xml) verwenden, müssen Sie den Namen angeben.

2.  Speichern Sie die Datei unter **C:\\Tutorials\\UseOozie\\coordinator.xml**, und verwenden Sie dabei die ANSI (ASCII)-Codierung (Verwenden Sie Editor, falls Ihr Texteditor diese Möglichkeit nicht bietet).

Bereitstellen des Oozie-Projekts und Vorbereiten des Lernprogramms
------------------------------------------------------------------

Sie führen ein Azure PowerShell-Skript aus, um folgende Aktionen durchzuführen:

-   Kopieren des HiveQL-Skripts (useoozie.hql) in den Azure Blob-Speicher, wasb:///tutorials/useoozie/useoozie.hql.
-   Kopieren der Datei workflow.xml nach wasb:///tutorials/useoozie/workflow.xml.
-   Kopieren der Datei coordinator.xml nach wasb:///tutorials/useoozie/coordinator.xml.
-   Kopieren der Datendatei (/example/data/sample.log) nach wasb:///tutorials/useoozie/data/sample.log.
-   Erstellen einer SQL-Datenbanktabelle zum Speichern der Sqoop-Exportdaten. Der Tabellenname lautet *log4jLogCount*.

**Verstehen von HDInsight-Speicher**

HDInsight verwendet zur Datenspeicherung Azure Blob-Speicher. Dieser wird als *WASB* oder *Windows Azure Storage - Blob* bezeichnet. WASB ist die Microsoft-Implementierung von HDFS auf Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

Wenn Sie einen HDInsight-Cluster bereitstellen, werden ebenso wie in HDFS ein Azure-Speicherkonto und ein bestimmter Blob-Speichercontainer aus diesem Konto als Standarddateisystem festgelegt. Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern](/en-us/documentation/articles/hdinsight-provision-clusters/). Um das in diesem Lernprogramm verwendete PowerShell-Skript zu vereinfachen, werden alle Dateien im Standarddateisystem-Container unter */tutorials/useoozie* gespeichert. Dieser Container hat normalerweise denselben Namen wie das HDInsight-Cluster. Die WASB-Syntax lautet folgendermaßen:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] In HDInsight-Clustern der Version 3.0 wird nur die Syntax *wasb://* unterstützt. Die ältere Syntax *asv://* wird in HDInsight-Clustern der Versionen 2.1 und 1.6 unterstützt, jedoch nicht in HDInsight-Clustern der Version 3.0 und höher.

> [WACOM.NOTE] Der WASB-Pfad ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

Auf Dateien, die im Standarddateisystem-Container gespeichert sind, kann in HDInsight über einen der folgenden URIs zugegriffen werden (als Beispiel wird hier workflow.xml verwendet):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Wenn Sie auf die Datei direkt über das Speicherkonto zugreifen möchten, lautet der Blob-Name der Datei folgendermaßen:

    tutorials/useoozie/workflow.xml

**Verstehen von internen und externen Hive-Tabellen**

Folgendes sollten Sie über interne und externe Hive-Tabellen wissen:

-   Der Befehl CREATE TABLE erstellt eine interne Tabelle, die auch als verwaltete Tabelle bezeichnet wird. Die Datendatei muss sich im Standardcontainer befinden.
-   Der Befehl CREATE TABLE verschiebt die Datendatei in den Ordner /hive/warehouse/ im Standardcontainer.
-   Der Befehl CREATE EXTERNAL TABLE erstellt eine externe Tabelle. Die Datendatei kann sich außerhalb des Standardcontainers befinden.
-   Der Befehl CREATE EXTERNAL TABLE verschiebt die Datendatei nicht.
-   Der Befehl CREATE EXTERNAL TABLE lässt keine Unterordner unter dem in der LOCATION-Klausel angegebenen Ordner zu. Aus diesem Grund wird im Lernprogramm eine Kopie der Datei sample.log erstellt.

Weitere Informationen finden Sie unter [HDInsight: Hive Internal and External Tables Intro](http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx) (HDInsight: Einführung in interne und externe Hive-Tabellen, in englischer Sprache).

**So bereiten Sie das Lernprogramm vor**

1.  Öffnen Sie Windows PowerShell ISE (geben Sie auf dem Windows 8-Startbildschirm **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**. Weitere Informationen finden Sie unter [Starting Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx) (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).
2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Dieses Verfahren zum Hinzufügen einer Abonnementverbindung läuft nach einer bestimmten Zeit ab. Daher müssen Sie das Cmdlet nach 12 Stunden erneut ausführen.

    > [WACOM.NOTE] Wenn Sie mehrere Azure-Abonnements haben und Sie nicht das Standardabonnement verwenden möchten, wählen Sie das aktuelle Abonnement mit dem Cmdlet **Select-AzureSubscription** aus.

3.  Kopieren Sie das folgende Skript in den Skriptbereich, und legen Sie anschließend die ersten sechs Variablen fest.

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
         $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
         $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
         $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"
            
         # WASB-Ordner zum Speichern der Oozie-Lernprogrammdateien.
         $destFolder = "tutorials/useoozie"  # Hier NICHT langen Pfad verwenden

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) in diesem Lernprogramm.

4.  Hängen Sie die folgenden Zeilen an das Skript im Skriptbereich:

         # Ein Speicherkontextobjekt erstellen
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         function uploadOozieFiles()
         {      
             Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
             Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
             Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
             Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
         }
                    
         function prepareHiveDataFile()
         {
             Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
             Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
         }
                    
         function prepareSQLDatabase()
         {
             # SQL-Abfragezeichenfolge zum Erstellen der Tabelle log4jLogsCount
             $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                     [Level] [nvarchar](10) NOT NULL,
                     [Total] float,
                 CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                 (
                 [Level] ASC
                 )
                 )"
                    
             # Die Tabelle log4jLogsCount erstellen
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
                    
         # workflow.xml, coordinator.xml und ooziewf.hql hochladen
         uploadOozieFiles;
                    
         # Eine Kopie der Datei example/data/sample.log in example/data/log4j/sample.log erstellen
         prepareHiveDataFile;
            
         # Tabelle log4jlogsCount in SQL-Datenbank erstellen
         prepareSQLDatabase;

5.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe sollte der Folgenden ähneln:

    ![Ausgabe für Lernprogrammvorbereitung](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png)

Ausführen des Oozie-Projekts
----------------------------

Azure PowerShell stellt derzeit keine Cmdlets zum Definieren von Oozie-Jobs bereit. Sie können das PowerShell-Cmdlet Invoke-RestMethod verwenden, um Oozie-Webdienste aufzurufen. Die Oozie-Webdienste-API ist eine HTTP REST JSON-API. Weitere Informationen über die Oozie-Webdienste-API finden Sie in der [Apache Oozie 4.0-Dokumentation](http://oozie.apache.org/docs/4.0.0/) (für HDInsight-Cluster der Version 3.0) oder in der [Apache Oozie 3.3.2-Dokumentation](http://oozie.apache.org/docs/3.3.2/) (für HDInsight-Cluster der Version 2.1).

**So übermitteln Sie einen Oozie-Job**

1.  Öffnen Sie Windows PowerShell ISE (geben Sie auf dem Windows 8-Startbildschirm **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**. Weitere Informationen finden Sie unter [Starting Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx) (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).

2.  Kopieren Sie das folgende Skript in den Skriptbereich, und legen Sie anschließend die ersten 14 Variablen fest (überspringen Sie die sechste Variable \$storageUri).

         #HDInsight-Clustervariablen
         $clusterName = "<HDInsightClusterName>"
         $clusterUsername = "<HDInsightClusterUsername>"
         $clusterPassword = "<HDInsightClusterUserPassword>"
            
         #Variablen für Azure Blob-Speicher (WASB)
         $storageAccountName = "<StorageAccountName>"
         $storageContainerName = "<BlobContainerName>"
         $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
            
         #Azure SQL-Datenbankvariablen
         $sqlDatabaseServer = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
         $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
         $sqlDatabaseName = "<SQLDatabaseName>"  
            
         #Oozie WF-/Koordinatorvariablen
         $coordStart = "2014-03-21T13:45Z"
         $coordEnd = "2014-03-21T13:45Z"
         $coordFrequency = "1440"   # in minutes, 24h x 60m = 1440m
         $coordTimezone = "UTC" #UTC/GMT

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

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) in diesem Lernprogramm.

    \$coordstart und \$coordend geben Start- bzw. Endzeit des Workflows an. Um die UTC/GMT-Uhrzeit zu ermitteln, suchen Sie auf bing.com nach "utc time". \$coordFrequency gibt in Minuten an, wie oft der Workflow ausgeführt werden soll.

3.  Hängen Sie die folgenden Codezeilen an das Skript. In diesem Teil wird die Oozie-Nutzlast definiert:

         #Für Übermittlung von Oozie-Webdiensten verwendete OoziePayload
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
                <name>oozie.coord.application.path</name>
                <value>$oozieWFPath</value>
            </property>

            <property>
                <name>wfPath</name>
                <value>$oozieWFPath</value>
            </property>

            <property>
                <name>coordStart</name>
                <value>$coordStart</value>
            </property>

            <property>
                <name>coordEnd</name>
                <value>$coordEnd</value>
            </property>

            <property>
                <name>coordFrequency</name>
                <value>$coordFrequency</value>
            </property>

            <property>
                <name>coordTimezone</name>
                <value>$coordTimezone</value>
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
            
         </configuration>
         "@

    > [WACOM.NOTE] Der Hauptunterschied zur Nutzlastdatei für die Workflow-Übermittlung ist die Variable **oozie.coord.application.path**. Beim Übermitteln eines Workflow-Jobs wird stattdessen die Variable **oozie.wf.application.path** verwendet.

4.  Hängen Sie die folgenden Codezeilen an das Skript. In diesem Teil wird der Status des Oozie-Webdiensts geprüft:

         function checkOozieServerStatus()
         {
             Write-Host "Checking Oozie server status..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
                
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $oozieServerSatus = $jsonResponse[0].("systemMode")
             Write-Host "Oozie server status is $oozieServerSatus..."
            
             if($oozieServerSatus -notmatch "NORMAL")
             {
                 Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                 exit 1
             }
         }

5.  Hängen Sie die folgenden Codezeilen an das Skript. Mit diesem Teil wird ein Oozie-Job erstellt:

         function createOozieJob()
         {
             # Oozie-Job erstellen
             Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
             Write-Host "`n--------`n$OoziePayload`n--------"
             $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
             $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose
            
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $oozieJobId = $jsonResponse[0].("id")
             Write-Host "Oozie job id is $oozieJobId..."
            
             return $oozieJobId
         }

    > [WACOM.NOTE] Wenn Sie einen Workflow-Job übermitteln, müssen Sie einen weiteren Webdienstaufruf ausführen, um den Job nach dessen Erstellung auszuführen. In diesem Fall wird der Koordinator-Job durch die Uhrzeit ausgelöst. Der Job wird automatisch gestartet.

6.  Hängen Sie die folgenden Codezeilen an das Skript. In diesem Teil wird der Status des Oozie-Jobs geprüft:

         function checkOozieJobStatus($oozieJobId)
         {
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
            
             Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
             if($JobStatus -notmatch "SUCCEEDED")
             {
                 Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                 exit -1
             }
         }

7.  (Optional) Hängen Sie die folgenden Codezeilen an das Skript.

         function listOozieJobs()
         {
             Write-Host "Listing Oozie jobs..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
                
             write-host "Job ID                                   App Name        Status      Started                         Ended"
             write-host "----------------------------------------------------------------------------------------------------------------------------------"
             foreach($job in $response.workflows)
             {
                 Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
             }
         }

         function ShowOozieJobLog($oozieJobId)
         {
             Write-Host "Showing Oozie job info..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "
         show=log"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
             write-host $response
         }

         function killOozieJob($oozieJobId)
         {
             Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
             $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "
         action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
             $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
         }

8.  Hängen Sie die folgenden Codezeilen an das Skript:

         checkOozieServerStatus
         # listOozieJobs
         $oozieJobId = createOozieJob($oozieJobId)
         checkOozieJobStatus($oozieJobId)
         # ShowOozieJobLog($oozieJobId)
         # killOozieJob($oozieJobId)

    Entfernen Sie die \#-Zeichen, wenn die zusätzlichen Funktionen ausgeführt werden sollen.

9.  Wenn Sie einen HDinsight-Cluster der Version 2.1 haben, ersetzen Sie "https://\$clusterName.azurehdinsight.net:443/oozie/v2/" durch "https://\$clusterName.azurehdinsight.net:443/oozie/v1/". HDInsight-Cluster der Version 2.1 unterstützen nicht Version 2 der Webdienste.

10. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe ähnelt der folgenden:

    ![Lernprogramm – Ausgabe beim Ausführen des Workflows](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png)

11. Stellen Sie eine Verbindung mit der SQL-Datenbank her, um die exportierten Daten anzuzeigen.

**So prüfen Sie das Job-Fehlerprotokoll**

Um Probleme mit einem Workflow zu beheben, suchen Sie die Oozie-Protokolldatei unter C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log im Hauptknoten des Clusters. Informationen über RDP finden Sie unter [Verwalten von HDInsight-Clustern mit dem Management portal](/en-us/documentation/articles/hdinsight-administer-use-management-portal/).

**So führen Sie das Lernprogramm erneut aus**

Um den Workflow zu wiederholen, müssen Sie die folgenden Schritte ausführen:

-   Löschen der Ausgabedatei des Hive-Skripts
-   Löschen der Daten in der Tabelle log4jLogsCount

Sie können das folgende PowerShell-Beispielskript verwenden:

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

In diesem Tutorial haben Sie gelernt, wie ein Oozie-Workflow definiert und wie ein Oozie-Job mithilfe von Azure PowerShell ausgeführt wird. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit HDInsight](/en-us/documentation/articles/hdinsight-get-started/)
-   [Erste Schritte mit dem HDInsight-Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)
-   [Verwenden von Azure Blob-Speicher mit HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/)
-   [Verwalten von HDInsight mit PowerShell](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
-   [Hochladen von Daten zu HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [Verwenden von Sqoop mit HDInsight](../hdinsight-use-sqoop/)
-   [Verwenden von Hive mit HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
-   [Verwenden von Pig mit HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
-   [Entwickeln von C\# Hadoop-Streaming-Jobs für HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Entwickeln von Java MapReduce-Programmen für HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

