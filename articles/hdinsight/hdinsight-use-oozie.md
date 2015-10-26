<properties
	pageTitle="Verwenden von Hadoop Oozie in HDInsight | Microsoft Azure"
	description="Verwenden von Hadoop Oozie in HDInsight, einer Big Data-Lösung. Erfahren Sie, wie Sie einen Oozie-Workflow definieren und einen Oozie-Auftrag übermitteln können."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>


# Verwenden von Oozie mit Hadoop zum Definieren und Ausführen eines Workflows in HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

##Übersicht
Erfahren Sie, wie Sie Apache Oozie verwenden, um einen Workflow zu definieren und diesen in HDInsight auszuführen. Informationen zum Oozie-Koordinator finden Sie unter [Verwenden des zeitbasierten Oozie-Koordinators mit Hadoop in HDInsight][hdinsight-oozie-coordinator-time]. Informationen zu Azure Data Factory finden Sie unter [Verwenden von Pig und Hive mit Data Factory][azure-data-factory-pig-hive].

##Was ist Oozie?

Apache Oozie ist ein Workflow-/Koordinationssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stapel integriert und unterstützt Hadoop-Aufträge für Apache MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Es kann auch dazu verwendet werden, bestimmte Aufträge für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

Der Workflow, den Sie mithilfe der Anweisungen in diesem Lernprogramm implementieren, enthält zwei Aktionen:

![Workflowdiagramm][img-workflow-diagram]

1. Eine Hive-Aktion führt ein HiveQL-Skript aus, das die Vorkommen der verschiedenen Protokollierungsebenen in einer log4j-Datei zählt. Jede log4j-Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens [LOG LEVEL] befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt, z. B.

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

	Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

2.  Mit einer Sqoop-Aktion wird die HiveQL-Ausgabe in eine Tabelle in einer Azure SQL-Datenbank exportiert. Weitere Informationen über Sqoop finden Sie unter [Verwenden von Hadoop Sqoop mit HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE]Informationen zu den unterstützten Oozie-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][hdinsight-versions].

> [AZURE.NOTE]Dieses Lernprogramm funktioniert mit HDInsight der Versionen 3.0 und 2.1. Dieses Lernprogramm wurde nicht im HDInsight-Emulator getestet.


##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Um Windows PowerShell-Skripts ausführen zu können, müssen Sie PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Weitere Informationen finden Sie unter [Ausführen von Windows PowerShell-Skripts][powershell-script].
- **Einen HDInsight-Cluster**. Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Benutzerdefiniertes Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision] oder [Erste Schritte bei der Nutzung von Hadoop mit Hive in HDInsight zur Analyse der Verwendung von Mobiltelefonen][hdinsight-get-started]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

	<table border = "1">
<tr><th>Clustereigenschaft</th><th>Windows PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
<tr><td>HDInsight-Clustername</td><td>$clusterName</td><td></td><td>Der HDInsight-Cluster, auf dem dieses Lernprogramm ausgeführt wird.</td></tr>
<tr><td>Benutzername für den HDInsight-Cluster</td><td>$clusterUsername</td><td></td><td>Der Benutzername für den HDInsight-Cluster. </td></tr>
<tr><td>Das Benutzerkennwort für den HDInsight-Cluster. </td><td>$clusterPassword</td><td></td><td>Das Benutzerkennwort für den HDInsight-Cluster.</td></tr>
<tr><td>Azure-Speicherkontoname</td><td>$storageAccountName</td><td></td><td>Ein im HDInsight-Cluster verfügbares Azure-Speicherkonto. Verwenden Sie für dieses Lernprogramm das Standard-Speicherkonto, das Sie während der Bereitstellung des Clusters angegeben haben.</td></tr>
<tr><td>Azure-Blob-Containername</td><td>$containerName</td><td></td><td>Verwenden Sie in diesem Beispiel den Namen des Blobcontainers, der für das Standarddateisystem des HDInsight-Clusters verwendet wird. Standardmäßig hat dieser denselben Namen wie der HDInsight-Cluster.</td></tr>
</table>

- **Eine Azure SQL-Datenbank**. Sie müssen eine Firewallregel für die Azure SQL-Datenbank konfigurieren, um Zugriff von Ihrer Arbeitsstation zu erlauben. Anweisungen zur Erstellung einer Azure SQL-Datenbank und zur Konfiguration der Firewall erhalten Sie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank][sqldatabase-get-started]. Dieser Artikel beschreibt ein Windows PowerShell-Skript zur Erstellung der Azure SQL-Datenbanktabelle, die Sie für dieses Lernprogramm benötigen.

	<table border = "1">
<tr><th>SQL-Datenbankeigenschaft</th><th>Windows PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
<tr><td>Servername der SQL-Datenbank</td><td>$sqlDatabaseServer</td><td></td><td>Azure SQL-Datenbank, in die Sqoop Daten exportiert. </td></tr>
<tr><td>Anmeldename der SQL-Datenbank</td><td>$sqlDatabaseLogin</td><td></td><td>Anmeldename der Azure SQL-Datenbank.</td></tr>
<tr><td>Anmeldekennwort der SQL-Datenbank</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Anmeldekennwort der Azure SQL-Datenbank.</td></tr>
<tr><td>SQL-Datenbankname</td><td>$sqlDatabaseName</td><td></td><td>Azure SQL-Datenbank, in die Sqoop Daten exportiert. </td></tr>
</table>> [AZURE.NOTE]Eine Azure SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn die Firewalleinstellung deaktiviert ist, müssen Sie sie im Azure-Vorschauportal aktivieren. Anweisungen zum Erstellen einer SQL-Datenbank und zum Konfigurieren von Firewallregeln finden Sie unter [Erstellen und Konfigurieren einer Azure SQL-Datenbank][sqldatabase-create-configue].


> [AZURE.NOTE]Tragen Sie die Werte in die Tabellen ein. Das wird Ihnen helfen, wenn Sie dieses Lernprogramm durcharbeiten.


##Definieren des Oozie-Workflows und des zugehörigen HiveQL-Skripts

Definitionen von Oozie-Workflows werden in hPDL (eine XML-Prozessdefinitionssprache) geschrieben. Der Standardname der Workflow-Datei lautet *workflow.xml*. Sie speichern die Workflowdatei lokal und stellen sie später im Lernprogramm im HDInsight-Cluster mit Windows PowerShell bereit.

Von der Hive-Aktion im Workflow wird eine HiveQL-Skriptdatei aufgerufen. Die Skriptdatei enthält drei HiveQL-Anweisungen:

1. **Die Anweisung DROP TABLE** löscht die log4j-Hive-Tabelle, falls sie vorhanden ist.
2. **Die Anweisung CREATE TABLE** erstellt eine externe log4j-Hive-Tabelle, die auf den Speicherort der log4j-Protokolldatei zeigt. Das Feldtrennzeichen ist ",". Das Standard-Zeilentrennzeichen ist "\\n". Mit einer externen Hive-Tabelle wird vermieden, dass die Datendatei aus dem ursprünglichen Speicherort entfernt wird, falls Sie den Oozie-Workflow mehrmals ausführen möchten.
3. **Die Anweisung INSERT OVERWRITE** zählt die Vorkommen der verschiedenen Protokollierungsebenen in der log4j-Hive-Tabelle und speichert die Ausgabe in einem Blob in Azure Storage.

Es ist bekannt, dass es ein Problem mit dem Hive-Pfad gibt. Dieses Problem tritt auf, wenn Sie einen Oozie-Auftrag übermitteln. Die Anweisungen zu dessen Behebung finden Sie im TechNet-Wiki: [HDInsight Hive error: Unable to rename][technetwiki-hive-error] (in englischer Sprache).

**So definieren Sie die HiveQL-Skriptdatei, die vom Workflow aufgerufen werden soll**

1. Erstellen Sie eine Textdatei mit folgendem Inhalt:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Im Skript werden drei Variablen verwendet:

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}

	Die Workflowdefinitionsdatei ("workflow.xml" in diesem Lernprogramm) übergibt diese Werte zur Laufzeit an das HiveQL-Skript.

2. Speichern Sie die Datei als **C:\\Tutorials\\UseOozie\\useooziewf.hql** mit **ANSI (ASCII)**-Codierung. (Verwenden Sie Editor, falls Ihr Texteditor diese Option nicht zur Verfügung stellt.) Die Skriptdatei wird später im Lernprogramm im HDInsight-Cluster bereitgestellt.



**So definieren Sie einen Workflow**

1. Erstellen Sie eine Textdatei mit folgendem Inhalt:

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

	Im Workflow sind zwei Aktionen definiert. Die Startaktion lautet *RunHiveScript*. Wenn die Aktion erfolgreich war, wird die nächste Aktion *RunSqoopExport* ausgeführt.

	Das RunHiveScript enthält mehrere Variablen. Die Werte werden übergeben, wenn Sie den Oozie-Auftrag mit Windows PowerShell von Ihrer Arbeitsstation senden.

	<table border = "1">
<tr><th>Workflow-Variablen</th><th>Beschreibung</th></tr>
<tr><td>${jobTracker}</td><td>Gibt die URL des Hadoop-JobTrackers an. Verwenden Sie in HDInsight der Versionen 3.0 und 2.1 <strong>jobtrackerhost:9010</strong>.</td></tr>
<tr><td>${nameNode}</td><td>Gibt die URL des Hadoop-NameNode an. Verwenden Sie die Standarddateisystemadresse, zum Beispiel <i>wasb://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>.</td></tr>
<tr><td>${queueName}</td><td>Gibt den Namen der Warteschlange an, an die der Auftrag gesendet wird. Verwenden Sie den <strong>Standardwert</strong>.</td></tr>
</table><table border = "1">
<tr><th>Hive-Aktionsvariable</th><th>Beschreibung</th></tr>
<tr><td>${hiveDataFolder}</td><td>Gibt das Quellverzeichnis für den Befehl zum Erstellen der Hive-Tabelle an.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Gibt den Ausgabeordner für die Anweisung INSERT OVERWRITE an.</td></tr>
<tr><td>${hiveTableName}</td><td>Gibt den Namen der Hive-Tabelle an, die auf die log4j-Datendateien verweist.</td></tr>
</table><table border = "1">
<tr><th>Sqoop-Aktionsvariable</th><th>Beschreibung</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Gibt die Verbindungszeichenfolge für die Azure SQL-Datenbank an.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Gibt die Azure SQL-Datenbanktabelle an, in die die Daten exportiert werden.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Gibt den Ausgabeordner für die Hive-Anweisung INSERT OVERWRITE an. Dieser entspricht dem für den Sqoop-Export angegebenen Ordner (export-dir).</td></tr>
</table>Weitere Informationen über den Oozie-Workflow und die Verwendung von Workflowaktionen finden Sie in der [Apache Oozie 4.0-Dokumentation][apache-oozie-400] (für HDInsight der Version 3.0) oder in der [Apache Oozie 3.3.2-Dokumentation][apache-oozie-332] (für HDInsight der Version 2.1).

2. Speichern Sie die Datei al **C:\\Tutorials\\UseOozie\\workflow.xml** mit ANSI (ASCII)-Codierung. (Verwenden Sie Editor, falls Ihr Texteditor diese Option nicht zur Verfügung stellt.)

##Bereitstellen des Oozie-Projekts und Vorbereiten des Lernprogramms

Führen Sie ein Windows PowerShell-Skript aus, um Folgendes durchzuführen:

- Kopieren des HiveQL-Skripts ("useoozie.hql") in Azure Storage (wasb:///tutorials/useoozie/useoozie.hql).
- Kopieren der Datei "workflow.xml" nach wasb:///tutorials/useoozie/workflow.xml.
- Kopieren der Datendatei ("/example/data/sample.log") nach wasb:///tutorials/useoozie/data/sample.log.
- Erstellen einer Azure SQL-Datenbanktabelle zum Speichern der Sqoop-Exportdaten. Der Tabellenname lautet *log4jLogCount*.

**Grundlagen des HDInsight-Speichers**

HDInsight verwendet Blobs in Azure Storage zur Datenspeicherung. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].

Während des Prozesses zur Bereitstellung eines HDInsight-Clusters werden wie in HDFS ein Azure-Speicherkonto und ein bestimmter Blobcontainer aus diesem Konto als Standarddateisystem festgelegt. Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder aus anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision]. Um das in diesem Lernprogramm verwendete Windows PowerShell-Skript zu vereinfachen, werden alle Dateien im Standard-Dateispeichercontainer gespeichert, der sich unter */tutorials/useoozie* befindet. Dieser Container hat standardmäßig denselben Namen wie der HDInsight-Cluster. Die Syntax ist:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]In HDInsight-Clustern der Version 3.0 wird nur die **wasb://*-Syntax unterstützt. Die ältere **asv://*-Syntax wird in HDInsight 2.1- und 1.6-Clustern unterstützt, nicht aber in HDInsight 3.0-Clustern.

> [AZURE.NOTE]Der Pfad "wasb://" ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].

Auf Dateien, die im Standarddateisystemcontainer gespeichert sind, kann in HDInsight über einen der folgenden URIs zugegriffen werden (als Beispiel wird in folgendem Code "workflow.xml" verwendet):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

	tutorials/useoozie/workflow.xml

**Verstehen von internen und externen Hive-Tabellen**

Folgendes sollten Sie über interne und externe Hive-Tabellen wissen:

- Der Befehl CREATE TABLE erstellt eine interne Tabelle, die auch als verwaltete Tabelle bezeichnet wird. Die Datendatei muss sich im Standardcontainer befinden.
- Der Befehl CREATE TABLE verschiebt die Datendatei in den Ordner "/hive/warehouse/"<TableName> im Standardcontainer.
- Der Befehl CREATE EXTERNAL TABLE erstellt eine externe Tabelle. Die Datendatei kann sich außerhalb des Standardcontainers befinden.
- Der Befehl CREATE EXTERNAL TABLE verschiebt die Datendatei nicht.
- Der Befehl CREATE EXTERNAL TABLE lässt keine Unterordner unter dem in der LOCATION-Klausel angegebenen Ordner zu. Aus diesem Grund wird im Lernprogramm eine Kopie der Datei "sample.log" erstellt.

Weitere Informationen finden Sie unter [HDInsight: Hive Internal and External Tables Intro][cindygross-hive-tables].

**So bereiten Sie das Lernprogramm vor**

1. Öffnen Sie die Windows PowerShell ISE. (Geben Sie im Windows 8-Startbildschirm **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**. Weitere Informationen finden Sie unter [Starten von Windows PowerShell unter Windows 8 und Windows][powershell-start]).
2. Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

		Add-AzureAccount

	Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Dieses Verfahren zum Hinzufügen einer Abonnementverbindung läuft nach einer bestimmten Zeit ab. Daher müssen Sie das Cmdlet nach 12 Stunden erneut ausführen.

	> [AZURE.NOTE]Wenn Sie über mehrere Azure-Abonnements verfügen und nicht das Standard-Abonnement verwenden möchten, wählen Sie das aktuelle Abonnement mithilfe des Cmdlets <strong>Select-AzureSubscription</strong> aus.

3. Kopieren Sie das folgende Skript in den Skriptbereich, und legen Sie anschließend die ersten sechs Variablen fest:

		# WASB variables
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"

		# SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"

		# Oozie files for the tutorial
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"

		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

3. Hängen Sie die folgenden Zeilen an das Skript im Skriptbereich:

		# Create a storage context object
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
			# SQL query string for creating log4jLogsCount table
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Level] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
				(
				[Level] ASC
				)
				)"

			#Create the log4jLogsCount table
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

		# create log4jlogsCount table on SQL database
		prepareSQLDatabase;

4. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe ähnelt der folgenden:

	![Ausgabe für Lernprogrammvorbereitung][img-preparation-output]

##Ausführen des Oozie-Projekts

Azure PowerShell stellt derzeit keine Cmdlets zum Definieren von Oozie-Jobs bereit. Sie können das Cmdlet **Invoke-RestMethod** verwenden, um Oozie-Webdienste aufzurufen. Die Oozie-Webdienste-API ist eine HTTP REST JSON-API. Weitere Informationen über die Oozie-Webdienste-API finden Sie in der [Apache Oozie 4.0-Dokumentation][apache-oozie-400] (für HDInsight der Version 3.0) oder in der [Apache Oozie 3.3.2-Dokumentation][apache-oozie-332] (für HDInsight der Version 2.1).

**So übermitteln Sie einen Oozie-Auftrag**

1. Öffnen Sie die Windows PowerShell ISE. (Geben Sie im Windows 8-Startbildschirm **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**. Weitere Informationen finden Sie unter [Starten von Windows PowerShell unter Windows 8 und Windows][powershell-start]).

3. Kopieren Sie das folgende Skript in den Skriptbereich, und legen Sie dann die ersten zehn Variablen fest (überspringen Sie dabei die $storageUri-Variable).

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"

		#Azure Blob storage variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  

		#Oozie WF variables
		$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
		$waitTimeBetweenOozieJobStatusCheck=10

		#Hive action variables
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

		#Sqoop action variables
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)


	Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

3. Hängen Sie die folgenden Codezeilen an das Skript. In diesem Skript wird die Oozie-Nutzlast definiert.

		#OoziePayload used for Oozie web service submission
		$OoziePayload =  @"
		<?xml version="1.0" encoding="UTF-8"?>
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
		       <value>";$sqlDatabaseConnectionString";</value>
		   </property>

		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>

		   <property>
		       <name>user.name</name>
		       <value>$clusterUsername</value>
		   </property>

		   <property>
		       <name>oozie.wf.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		</configuration>
		"@

4. Hängen Sie die folgenden Codezeilen an das Skript. In diesem Skript wird der Status des Oozie-Webdiensts geprüft.

	    Write-Host "Checking Oozie server status..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "Oozie server status is $oozieServerSatus..."

5. Hängen Sie die folgenden Codezeilen an das Skript. Dieser Teil erstellt und startet einen Oozie-Job:

	    # create Oozie job
	    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	    Write-Host "`n--------`n$OoziePayload`n--------"
	    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
	    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieJobId = $jsonResponse[0].("id")
	    Write-Host "Oozie job id is $oozieJobId..."

	    # start Oozie job
	    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6. Hängen Sie die folgenden Codezeilen an das Skript. In diesem Skript wird der Status des Oozie-Auftrags geprüft.

	    # get job status
	    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
	    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

	    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
	    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
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

7. Wenn Sie einen HDinsight-Cluster der Version 2.1 haben, ersetzen Sie "https://$clusterName.azurehdinsight.net:443/oozie/v2/" durch "https://$clusterName.azurehdinsight.net:443/oozie/v1/". HDInsight-Cluster der Version 2.1 unterstützen nicht Version 2 der Webdienste.

8. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe ähnelt der folgenden:

	![Lernprogramm – Ausgabe beim Ausführen des Workflows][img-runworkflow-output]

8. Stellen Sie eine Verbindung mit der Azure SQL-Datenbank her, um die exportierten Daten anzuzeigen.

**So prüfen Sie das Auftragsfehlerprotokoll**

Wenn Sie Probleme in einem Workflow beheben möchten, finden Sie die Oozie-Protokolldatei unter *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* oder *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* im Clusterhauptknoten. Weitere Informationen zu RDP finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Vorschauportal][hdinsight-admin-portal].

**So führen Sie das Lernprogramm erneut aus**

Um den Workflow zu wiederholen, müssen Sie Folgendes löschen:

- Die Ausgabedatei des Hive-Skripts
- Die Daten in der log4jLogsCount-Tabelle

Sie können das folgende Windows PowerShell-Beispielskript verwenden:

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"

	#SQL database variables
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


##Nächste Schritte
In diesem Lernprogramm haben Sie gelernt, wie ein Oozie-Workflow definiert und wie ein Oozie-Auftrag mithilfe von Windows PowerShell ausgeführt wird. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwenden des zeitbasierten Oozie-Koordinators mit HDInsight][hdinsight-oozie-coordinator-time]
- [Erste Schritte bei der Nutzung von Hadoop mit Hive in HDInsight zur Analyse der Verwendung von Mobiltelefonen][hdinsight-get-started]
- [Erste Schritte mit dem HDInsight-Emulator][hdinsight-get-started-emulator]
- [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage]
- [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
- [Hochladen von Daten für Hadoop-Aufträge in HDInsight][hdinsight-upload-data]
- [Verwenden von Sqoop mit Hadoop in HDInsight][hdinsight-use-sqoop]
- [Verwenden von Hive mit Hadoop in HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit Hadoop in HDInsight][hdinsight-use-pig]
- [Entwickeln von C# Hadoop-Streamingaufträgen für HDInsight][hdinsight-develop-streaming-jobs]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/de-DE/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=Oct15_HO3-->