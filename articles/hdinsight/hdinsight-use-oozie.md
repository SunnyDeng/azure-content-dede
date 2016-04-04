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
	ms.date="03/04/2016"
	ms.author="jgao"/>


# Verwenden von Oozie mit Hadoop zum Definieren und Ausführen eines Workflows in HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Erfahren Sie, wie Sie Apache Oozie verwenden, um einen Workflow zu definieren und diesen in HDInsight auszuführen. Informationen zum Oozie-Koordinator finden Sie unter [Verwenden des zeitbasierten Oozie-Koordinators mit Hadoop in HDInsight][hdinsight-oozie-coordinator-time]. Informationen zu Azure Data Factory finden Sie unter [Verwenden von Pig und Hive mit Data Factory][azure-data-factory-pig-hive].

Apache Oozie ist ein Workflow-/Koordinationssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stapel integriert und unterstützt Hadoop-Aufträge für Apache MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Es kann auch dazu verwendet werden, bestimmte Aufträge für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

Der Workflow, den Sie mithilfe der Anweisungen in diesem Lernprogramm implementieren, enthält zwei Aktionen:

![Workflowdiagramm][img-workflow-diagram]

1. Eine Hive-Aktion führt ein HiveQL-Skript aus, das die Vorkommen der verschiedenen Protokollierungsebenen in einer log4j-Datei zählt. Jede log4j-Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens [LOG LEVEL] befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt, z. B.

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

> [AZURE.NOTE] Informationen zu den unterstützten Oozie-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][hdinsight-versions].

###Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Um Windows PowerShell-Skripts ausführen zu können, müssen Sie PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Weitere Informationen finden Sie unter [Ausführen von Windows PowerShell-Skripts][powershell-script].

##Definieren des Oozie-Workflows und des zugehörigen HiveQL-Skripts

Definitionen von Oozie-Workflows werden in hPDL (eine XML-Prozessdefinitionssprache) geschrieben. Der Standardname der Workflow-Datei lautet *workflow.xml*. Es folgt die Workflowdatei, die Sie in diesem Tutorial verwenden werden.

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

Das RunHiveScript enthält mehrere Variablen. Die Werte werden übergeben, wenn Sie den Oozie-Auftrag mit Azure PowerShell von Ihrer Arbeitsstation senden.

<table border = "1">
<tr><th>Workflow-Variablen</th><th>Beschreibung</th></tr>
<tr><td>${jobTracker}</td><td>Gibt die URL des Hadoop-JobTrackers an. Verwenden Sie in HDInsight der Versionen 3.0 und 2.1 <strong>jobtrackerhost:9010</strong>.</td></tr>
<tr><td>${nameNode}</td><td>Gibt die URL des Hadoop-NameNode an. Verwenden Sie die Standarddateisystemadresse, zum Beispiel <i>wasb://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>.</td></tr>
<tr><td>${queueName}</td><td>Gibt den Namen der Warteschlange an, an die der Auftrag gesendet wird. Verwenden Sie den <strong>Standardwert</strong>.</td></tr>
</table>

<table border = "1">
<tr><th>Hive-Aktionsvariable</th><th>Beschreibung</th></tr>
<tr><td>${hiveDataFolder}</td><td>Gibt das Quellverzeichnis für den Befehl zum Erstellen der Hive-Tabelle an.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Gibt den Ausgabeordner für die Anweisung INSERT OVERWRITE an.</td></tr>
<tr><td>${hiveTableName}</td><td>Gibt den Namen der Hive-Tabelle an, die auf die log4j-Datendateien verweist.</td></tr>
</table>

<table border = "1">
<tr><th>Sqoop-Aktionsvariable</th><th>Beschreibung</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Gibt die Verbindungszeichenfolge für die Azure SQL-Datenbank an.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Gibt die Azure SQL-Datenbanktabelle an, in die die Daten exportiert werden.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Gibt den Ausgabeordner für die Hive-Anweisung INSERT OVERWRITE an. Dieser entspricht dem für den Sqoop-Export angegebenen Ordner (export-dir).</td></tr>
</table>

Weitere Informationen über den Oozie-Workflow und die Verwendung von Workflowaktionen finden Sie in der [Apache Oozie 4.0-Dokumentation][apache-oozie-400] (für HDInsight der Version 3.0) oder in der [Apache Oozie 3.3.2-Dokumentation][apache-oozie-332] (für HDInsight der Version 2.1).


Von der Hive-Aktion im Workflow wird eine HiveQL-Skriptdatei aufgerufen. Die Skriptdatei enthält drei HiveQL-Anweisungen:

	DROP TABLE ${hiveTableName};
	CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
	INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **Die Anweisung DROP TABLE** löscht die log4j-Hive-Tabelle, falls sie vorhanden ist.
2. **Die Anweisung CREATE TABLE** erstellt eine externe log4j-Hive-Tabelle, die auf den Speicherort der log4j-Protokolldatei zeigt. Das Feldtrennzeichen ist ",". Das Standard-Zeilentrennzeichen ist "\\n". Mit einer externen Hive-Tabelle wird vermieden, dass die Datendatei aus dem ursprünglichen Speicherort entfernt wird, falls Sie den Oozie-Workflow mehrmals ausführen möchten.
3. **Die Anweisung INSERT OVERWRITE** zählt die Vorkommen der verschiedenen Protokollierungsebenen in der log4j-Hive-Tabelle und speichert die Ausgabe in einem Blob in Azure Storage.


Im Skript werden drei Variablen verwendet:

- ${hiveTableName}
- ${hiveDataFolder}
- ${hiveOutputFolder}

Die Workflowdefinitionsdatei ("workflow.xml" in diesem Lernprogramm) übergibt diese Werte zur Laufzeit an das HiveQL-Skript.

Sowohl die Workflowdatei als auch die HiveQL-Datei werden in einem Blobcontainer gespeichert. Das PowerShell-Skript, das Sie später in diesem Tutorial verwenden, kopiert beide Dateien in das standardmäßige Speicherkonto.

##Übermitteln von Oozie-Jobs mit PowerShell

Azure PowerShell stellt derzeit keine Cmdlets zum Definieren von Oozie-Jobs bereit. Sie können das Cmdlet **Invoke-RestMethod** verwenden, um Oozie-Webdienste aufzurufen. Die Oozie-Webdienste-API ist eine HTTP REST JSON-API. Weitere Informationen über die Oozie-Webdienste-API finden Sie in der [Apache Oozie 4.0-Dokumentation][apache-oozie-400] (für HDInsight der Version 3.0) oder in der [Apache Oozie 3.3.2-Dokumentation][apache-oozie-332] (für HDInsight der Version 2.1).

Das PowerShell-Skript in diesem Abschnitt führt die folgenden Schritte aus:

1. Herstellen einer Verbindung zu Azure.
2. Erstellen einer Azure-Ressourcengruppe. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).
3. Erstellen Sie einen Azure SQL-Datenbank-Server, eine Azure SQL-Datenbank und zwei Tabellen. Diese werden von der Sqoop-Aktion im Workflow verwendet.

	Der Tabellenname lautet *log4jLogCount*.

4. Erstellen Sie ein HDInsight-Cluster, um Oozie-Jobs auszuführen.

	Um das Cluster zu untersuchen, können Sie das Azure-Portal oder Azure PowerShell verwenden.

5. Kopieren Sie die Oozie-Workflowdatei und die HiveQL-Skriptdatei in das Standarddateisystem.

	Beide Dateien werden in einem öffentlichen Blobcontainer gespeichert.
	
	- Kopieren des HiveQL-Skripts ("useoozie.hql") in Azure Storage (wasb:///tutorials/useoozie/useoozie.hql).
	- Kopieren der Datei "workflow.xml" nach wasb:///tutorials/useoozie/workflow.xml.
	- Kopieren der Datendatei ("/example/data/sample.log") nach wasb:///tutorials/useoozie/data/sample.log.
	 
6. Übermitteln Sie einen Oozie-Auftrag.

	Um die Ergebnisse des Oozie-Jobs zu überprüfen, verwenden Sie Visual Studio oder andere Tools, um eine Verbindung zur Azure SQL-Datenbank herzustellen.

Hier ist das Skript. Sie können das Skript mit Windows PowerShell ISE ausführen. Sie müssen nur die ersten sieben Variablen konfigurieren.

	#region - provide the following values
	
	$subscriptionID = "<Enter your Azure subscription ID>"
	
	# SQL Database server login credentials used for creating and connecting
	$sqlDatabaseLogin = "<Enter SQL Database Login Name>"
	$sqlDatabasePassword = "<Enter SQL Database Login Password>"
	
	# HDInsight cluster HTTP user credential used for creating and connectin
	$httpUserName = "admin"  # The default name is "admin"
	$httpPassword = "<Enter HDInsight Cluster HTTP User Password>"
	
	# Used for creating Azure service names
	$nameToken = "<Enter an Alias>"
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	#endregion
	
	#region - variables
	
	# Resource group variables
	$resourceGroupName = $namePrefix + "rg"
	$location = "East US 2" # used by all Azure services defined in this tutorial
	
	# SQL database varialbes
	$sqlDatabaseServerName = $namePrefix + "sqldbserver"
	$sqlDatabaseName = $namePrefix + "sqldb"
	$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$sqlDatabaseMaxSizeGB = 10
	
	# Used for retrieving external IP address and creating firewall rules
	$ipAddressRestService = "http://bot.whatismyipaddress.com"
	$fireWallRuleName = "UseSqoop"
	
	# HDInsight variables
	$hdinsightClusterName = $namePrefix + "hdi"
	$defaultStorageAccountName = $namePrefix + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	#endregion
	
	# Treat all errors as terminating
	$ErrorActionPreference = "Stop"
	
	#region - Connect to Azure subscription
	Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
	try{Get-AzureRmContext}
	catch{
		Login-AzureRmAccount
		Select-AzureRmSubscription -SubscriptionId $subscriptionID
	}
	#endregion
	
	#region - Create Azure resouce group
	Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
	try{
		Get-AzureRmResourceGroup -Name $resourceGroupName
	}
	catch{
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	}
	#endregion
	
	#region - Create Azure SQL database server
	Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
	try{
		Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
	catch{
		Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
	
		$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
		$sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
	
		$sqlDatabaseServerName = (New-AzureRmSqlServer `
									-ResourceGroupName $resourceGroupName `
									-ServerName $sqlDatabaseServerName `
									-SqlAdministratorCredentials $sqlLoginCredentials `
									-Location $location).ServerName
		Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
	
		Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-workstation" `
			-StartIpAddress $workstationIPAddress `
			-EndIpAddress $workstationIPAddress
	
		#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
		#Note that this allows Azure traffic from any Azure subscription to access the server.
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-Azureservices" `
			-StartIpAddress "0.0.0.0" `
			-EndIpAddress "0.0.0.0"
	}
	#endregion
	
	#region - Create and validate Azure SQL database
	Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
	
	try {
		Get-AzureRmSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName
	}
	catch {
		New-AzureRMSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName `
			-Edition "Standard" `
			-RequestedServiceObjectiveName "S1"
	}
	#endregion
	
	#region - Create SQL database tables
	Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green
	
	$sqlDatabaseTableName = "log4jLogsCount"
	$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
			[Level] [nvarchar](10) NOT NULL,
			[Total] float,
		CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
		(
		[Level] ASC
		)
		)"
	
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = $sqlDatabaseConnectionString
	$conn.Open()
	
	# Create the log4jlogs table and index
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.Connection = $conn
	$cmd.CommandText = $cmdCreateLog4jCountTable
	$cmd.ExecuteNonQuery()
	
	$conn.close()
	#endregion
	
	#region - Create HDInsight cluster
	
	Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
	
	# Create the default storage account
	New-AzureRmStorageAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $defaultStorageAccountName `
		-Location $location `
		-Type Standard_LRS
	
	# Create the default Blob container
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
									-ResourceGroupName $resourceGroupName `
									-Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext `
										-StorageAccountName $defaultStorageAccountName `
										-StorageAccountKey $defaultStorageAccountKey 
	New-AzureStorageContainer `
		-Name $defaultBlobContainerName `
		-Context $defaultStorageAccountContext 
	
	# Create the HDInsight cluster
	$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
	$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $HDInsightClusterName `
		-Location $location `
		-ClusterType Hadoop `
		-OSType Windows `
		-ClusterSizeInNodes 2 `
		-HttpCredential $httpCredential `
		-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultStorageContainer $defaultBlobContainerName 
	
	# Validate the cluster
	Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
	#endregion
	
	#region - copy Oozie workflow and HiveQL files
	
	Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
	
	# Both files are stored in a public Blob
	$publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
	
	# WASB folder for storing the Oozie tutorial files.
	$destFolder = "tutorials/useoozie"  # Do NOT use the long path here
	
	Start-CopyAzureStorageBlob `
		-Context $publicBlobContext `
		-SrcContainer "useoozie" `
		-SrcBlob "useooziewf.hql"  `
		-DestContext $defaultStorageAccountContext `
		-DestContainer $defaultBlobContainerName `
		-DestBlob "$destFolder/useooziewf.hql" `
		-Force
	
	Start-CopyAzureStorageBlob `
		-Context $publicBlobContext `
		-SrcContainer "useoozie" `
		-SrcBlob "workflow.xml"  `
		-DestContext $defaultStorageAccountContext `
		-DestContainer $defaultBlobContainerName `
		-DestBlob "$destFolder/workflow.xml" `
		-Force
	
	#validate the copy
	Get-AzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-Container $defaultBlobContainerName `
		-Blob $destFolder/workflow.xml
	
	Get-AzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-Container $defaultBlobContainerName `
		-Blob $destFolder/useooziewf.hql
	
	#endregion
	
	#region - copy the sample.log file
	
	Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
	
	Start-CopyAzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-SrcContainer $defaultBlobContainerName `
		-SrcBlob "example/data/sample.log"  `
		-DestContext $defaultStorageAccountContext `
		-DestContainer $defaultBlobContainerName `
		-destBlob "$destFolder/data/sample.log" 
	
	#validate the copy
	Get-AzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-Container $defaultBlobContainerName `
		-Blob $destFolder/data/sample.log
	
	#endregion
	
	#region - submit Oozie job
	
	$storageUri="wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"
	
	$oozieJobName = $namePrefix + "OozieJob"
	
	#Oozie WF variables
	$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
	$waitTimeBetweenOozieJobStatusCheck=10
	
	#Hive action variables
	$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
	$hiveTableName = "log4jlogs"
	$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
	$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
	
	#Sqoop action variables
	$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	
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
		<value>$httpUserName</value>
	</property>
	
	<property>
		<name>oozie.wf.application.path</name>
		<value>$oozieWFPath</value>
	</property>
	
	</configuration>
	"@
	
	Write-Host "Checking Oozie server status..." -ForegroundColor Green
	$clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	$response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus
	
	$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	$oozieServerSatus = $jsonResponse[0].("systemMode")
	Write-Host "Oozie server status is $oozieServerSatus."
	
	# create Oozie job
	Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	Write-Host "`n--------`n$OoziePayload`n--------"
	$clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
	$response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
	
	$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	$oozieJobId = $jsonResponse[0].("id")
	Write-Host "Oozie job id is $oozieJobId..."
	
	# start Oozie job
	Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	$clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	$response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug
	
	# get job status
	Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
	Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
	
	Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
	$clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
	$response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
	$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	$JobStatus = $jsonResponse[0].("status")
	
	while($JobStatus -notmatch "SUCCEEDED|KILLED")
	{
		Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
		Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
		$response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
		$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		$JobStatus = $jsonResponse[0].("status")
		$jobStatus
	}
	
	Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green
	
	#endregion


**So führen Sie das Lernprogramm erneut aus**

Um den Workflow zu wiederholen, müssen Sie Folgendes löschen:

- Die Ausgabedatei des Hive-Skripts
- Die Daten in der log4jLogsCount-Tabelle

Sie können das folgende PowerShell-Beispielskript verwenden:

	$resourceGroupName = "<AzureResourceGroupName>"
	
	$defaultStorageAccountName = "<AzureStorageAccountName>"
	$defaultBlobContainerName = "<ContainerName>"

	#SQL database variables
	$sqlDatabaseServerName = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"

	Write-host "Delete the Hive script output file ..." -ForegroundColor Green
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

	Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()

	$conn.close()

##Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie ein Oozie-Workflow definiert und wie ein Oozie-Auftrag mithilfe von PowerShell ausgeführt wird. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwenden des zeitbasierten Oozie-Koordinators mit HDInsight][hdinsight-oozie-coordinator-time]
- [Erste Schritte bei der Nutzung von Hadoop mit Hive in HDInsight zur Analyse der Verwendung von Mobiltelefonen][hdinsight-get-started]
- [Erste Schritte mit dem HDInsight-Emulator][hdinsight-get-started-emulator]
- [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage]
- [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
- [Hochladen von Daten für Hadoop-Aufträge in HDInsight][hdinsight-upload-data]
- [Verwenden von Sqoop mit Hadoop in HDInsight][hdinsight-use-sqoop]
- [Verwenden von Hive mit Hadoop in HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit Hadoop in HDInsight][hdinsight-use-pig]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md

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

<!---HONumber=AcomDC_0323_2016-->