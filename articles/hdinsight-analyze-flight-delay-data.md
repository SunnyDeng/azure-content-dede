<properties 
	pageTitle="Analysieren von Daten zu Flugverspätungen mit Hadoop in HDInsight | Azure" 
	description="Erfahren Sie, wie Sie mithilfe eines PowerShell-Skripts HDInsight-Cluster bereitstellen, einen Hive-Auftrag ausführen, einen Sqool-Auftrag ausführen und das Cluster löschen." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/04/2014" 
	ms.author="jgao"/>

#Analysieren von Daten zu Flugverspätungen mit Hadoop in HDInsight

Hive bietet die Möglichkeit, Hadoop MapReduce-Aufträge über eine SQL-ähnliche Skriptsprache namens *[HiveQL][hadoop-hiveql]* auszuführen. Sie kann für die Zusammenfassung, Abfrage und Analyse großer Datenmengen verwendet werden. 

Einer der größten Vorteile von HDInsight ist die Trennung von Datenspeicher und Server. HDInsight verwendet Azure-Blob-Speicher zur Datenspeicherung. Ein normaler MapReduce-Prozess besteht aus drei Teilen:

1. **Speichern von Daten im Azure Blob-Speicher.**  Dies kann ein fortlaufender Prozess sein. Es werden zum Beispiel Wetterdaten, Sensordaten, Webprotokolle und in diesem Fall Daten zu Flugverspätungen im Blob-Speicher gespeichert.
2. **Ausführen von Aufträgen.**  Für die Bearbeitung der Daten führen Sie ein PowerShell-Skript aus (oder eine Clientanwendung), um einen HDInsight-Cluster bereitzustellen, Aufträge auszuführen und den Cluster zu löschen.  Die Aufträge speichern Ausgabedaten im Azure-Blob-Speicher. Die Ausgabedaten werden auch nach dem Löschen des Clusters beibehalten. So bezahlen Sie nur für den tatsächlichen Verbrauch. 
3. **Rufen Sie die Ausgabe aus dem Blob-Speicher ab**, oder, wie in diesem Lernprogramm, exportieren Sie die Daten in eine Azure SQL-Datenbank.

Das folgende Diagramm veranschaulicht das Szenario und die Struktur dieses Artikels:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Hinweis**: Die Nummern im Diagramm stehen für die Abschnittstitel.

In diesem Lernprogramm geht es hauptsächlich um die Verwendung eines PowerShell-Skripts zu folgenden Zwecken:

- Bereitstellen eines HDInsight-Clusters
- Ausführen eines Hive-Jobs für das Cluster zur Berechnung der durchschnittlichen Verspätung an Flughäfen.  Die Daten zu den Flugverspätungen werden in einem Azure-Blobspeicherkonto gespeichert 
- Ausführen eines Sqoop-Jobs zum Exportieren der Hive-Job-Ausgabe in eine Azure SQL-Datenbank
- Löschen des HDInsight-Clusters 

In den Anhängen finden Sie Anweisungen zum Hochladen der Flugverspätungsdaten, zum Erstellen/Hochladen der Hive-Abfragezeichenfolge und zum Vorbereiten der Azure SQL-Datenbank für den Sqoop-Job.

##Dieses Lernprogramm umfasst folgende Punkte

* [Voraussetzungen](#prerequisite)
* [Bereitstellen des HDInsight-Clusters und Ausführen von Hive/Sqoop-Aufträgen (M1)](#runjob)
* [Anhang A: Hochladen von Daten zu Flugverspätungen in den Azure Blob-Speicher (A1)](#appendix-a)
* [Anhang B: Erstellen und Hochladen eines HiveQL-Skripts (A2)](#appendix-b)
* [Anhang C: Vorbereiten der Azure SQL-Datenbank für die Ausgabe des Sqoop-Auftrags (A3)](#appendix-c)
* [Nächste Schritte](#nextsteps)

##<a id="prerequisite"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

* Eine Arbeitsstation, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].
* Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [kostenlose Testversion][azure-free-trial].

###Grundlagen der HDInsight-Speicherung

Hadoop-Cluster in HDInsight verwenden Azure-Blob-Speicher zur Datenspeicherung.  Dies wird *WASB* oder *Azure Storage - Blob* genannt. WASB ist die Implementierung von Microsoft von *HDFS* auf dem Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage]. 

Wenn Sie einen HDInsight-Cluster bereitstellen, wird ebenso wie in HDFS ein Blob-Speichercontainer eines Azure-Speicherkontos als Standarddateisystem festgelegt. Dieses Speicherkonto wird als das *default storage account* und der Blob-Container als  *default Blob container* oder *default container* bezeichnet. Das Standardspeicherkonto muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden. Durch das Löschen eines HDInsight-Clusters wird der Standardcontainer oder das Standardspeicherkonto nicht gelöscht.

Zusätzlich zum Standardspeicherkonto können andere Azure-Speicherkonten während des Bereitstellungsprozesses an einen HDInsight-Cluster gebunden werden. Die Bindung erfolgt dadurch, dass das Speicherkonto und der Speicherkontoschlüssel zur Konfigurationsdatei hinzugefügt werden. Somit kann der Cluster zur Laufzeit auf diese Speicherkonten zugreifen. Anleitungen zum Hinzufügen von zusätzlichen Speicherkonten finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision]. 

Die WASB-Syntax lautet:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

>[AZURE.NOTE] Der WASB-Pfad ist ein virtueller Pfad.  Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage]. 

Auf Dateien, die im Standardcontainer gespeichert sind, kann in HDInsight über einen der folgenden URIs zugegriffen werden (als Beispiel wird hier flightdelays.hql verwendet):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
	wasb:///tutorials/flightdelays/flightdelays.hql
	/tutorials/flightdelays/flightdelays.hql

Um direkt aus dem Speicherkonto auf die Datei zuzugreifen, lautet der Blob-Name für die Datei:

	tutorials/flightdelays/flightdelays.hql

Es befindet sich kein "/" vor dem Blob-Namen.

**In diesem Lernprogramm verwendete Dateien**

In diesem Lernprogramm wird die Leistung bezüglich Pünktlichkeit der Flugdaten von [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA) verwendet. Die Daten wurden in einen Azure-Blob-Speichercontainer mit öffentlicher Blob-Zugriffsberechtigung hochgeladen. Da es sich um einen öffentlichen Blob-Container handelt, müssen Sie dieses Speicherkonto nicht an den HDInsight-Cluster binden, in dem das Hive-Skript ausgeführt wird. Das HiveQL-Skript wird ebenfalls zum selben Blob-Container hochgeladen. Wenn Sie wissen möchten, wie Sie die Daten in Ihr eigenes Speicherkonto bekommen/hochladen und wie Sie die HiveQL-Skriptdatei erstellen/hochladen, lesen Sie [Anhang A](#appendix-a) und [Anhang B](#appendix-b).

In der folgenden Tabelle sind die in diesem Lernprogramm verwendeten Dateien aufgelistet:

<table border="1">
<tr><th>Dateien</th><th>Beschreibung</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Die HiveQL-Skriptdatei, die vom Hive-Auftrag verwendet wird, den Sie ausführen werden. Dieses Skript wurde in ein Azure-Blob-Speicherkonto mit öffentlichem Zugriff hochgeladen.  Im <a href="#appendix-b">Anhang B</a> finden Sie Anweisungen zum Vorbereiten und Hochladen dieser Datei in Ihr eigenes Azure-Blob-Speicherkonto.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Die Eingabedaten für die Hive-Aufträge. Die Daten wurden in ein Azure-Blob-Speicherkonto mit öffentlichem Zugriff hochgeladen.  Im <a href="#appendix-a">Anhang A</a> finden Sie Anweisungen zum Vorbereiten und Hochladen der Daten in Ihr eigenes Azure-Blob-Speicherkonto.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Der Ausgabepfad für den Hive-Auftrag. Der Standardcontainer wird für die Speicherung der Ausgabedaten verwendet.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Der Ordner mit dem Hive-Job-Status im Standardcontainer.</td></tr>
</table>



###Verstehen von internen und externen Hive-Tabellen

Folgendes sollten Sie über interne und externe Hive-Tabellen wissen:

- Mit dem Befehl CREATE TABLE erstellen Sie eine interne Tabelle. Die Datendatei muss sich im Standardcontainer befinden.
- Der Befehl CREATE TABLE verschiebt die Datendatei in den Ordner /hive/warehouse/<Tabellenname>.
- Der Befehl CREATE EXTERNAL TABLE erstellt eine externe Tabelle. Die Datendatei kann sich außerhalb des Standardcontainers befinden.
- Der Befehl CREATE EXTERNAL TABLE verschiebt die Datendatei nicht.
- Der Befehl CREATE EXTERNAL TABLE lässt keine Ordner im SPEICHERORT zu. Aus diesem Grund wird im Lernprogramm eine Kopie der Datei sample.log erstellt.

Weitere Informationen finden Sie unter [HDInsight: Einführung in interne und externe Hive-Tabellen][cindygross-hive-tables].

> [AZURE.NOTE] Eine der HiveQL-Anweisungen erstellt eine externe Hive-Tabelle. Externe Hive-Tabellen erhalten die Datendatei am ursprünglichen Speicherort. Interne Hive-Tabellen verschieben die Datendate nach hive\warehouse. Bei internen Hive-Tabellen muss die Datendatei im Standardcontainer enthalten sein. Bei Daten, die nicht im Standard-Blob-Container gespeichert sind, müssen Sie externe Hive-Tabellen verwenden.









##<a id="runjob"></a>Bereitstellen eines HDInsight-Clusters und Ausführen der Hive-/Sqoop-Jobs 

Hadoop MapReduce verwendet Stapelbearbeitung. Die kosteneffizienteste Möglichkeit, einen Hive-Auftrag auszuführen, ist, einen Cluster für den Auftrag bereitzustellen und den Auftrag nach Abschluss zu löschen. Das folgende Skript erläutert den gesamten Vorgang. Weitere Informationen zur Bereitstellung eines HDInsight-Clusters und zur Ausführung von Hive-Aufträgen finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision] und  [Verwenden von Hive mit HDInsight][hdinsight-use-hive]. 

**So führen Sie die Hive-Abfragen mithilfe von PowerShell aus**

1. Erstellen Sie eine Azure SQL-Datenbank und die Tabelle für die Ausgabe des Sqoop-Auftrags mithilfe der Anweisungen in [Anhang C](#appendix-c).
2. Bereiten Sie die Parameter vor:

	<table border="1">
	<tr><th>Variablenname</th><th>Hinweise</th></tr>
	<tr><td>$hdinsightClusterName</td><td>Dies ist der Name des HDInsight-Clusters. Wenn der Cluster nicht vorhanden ist, erstellt das Skript einen mit dem eingegebenen Namen.</td></tr>
	<tr><td>$storageAccountName</td><td>Das als Standardspeicherkonto verwendet Azure-Speicherkonto. Dieser Wert ist nur erforderlich, wenn das Skript einen HDInsight-Cluster erstellen muss. Nehmen Sie keine Angabe vor, wenn Sie einen vorhandenen HDInsight-Clusternamen für $hdinsightClusterName angegeben haben. Wenn das Speicherkonto mit dem eingegebenen Wert nicht vorhanden ist, erstellt das Skript eines mit dem Namen.</td></tr>
	<tr><td>$blobContainerName</td><td>Der für das Standarddateisystem verwendete Blob-Container. Wenn Sie nichts angeben, wird der Wert $hdinsightClusterName verwendet. </td></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Servername der Azure SQL-Datenbank. Dies muss ein vorhandener Server sein. Informationen zum Erstellen finden Sie in <a href="#appendix-c">Anhang C</a> .</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Anmeldename für den Azure SQL-Datenbankserver.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Anmeldekennwort für den Azure SQL-Datenbankserver.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Dies ist der Name der SQL-Datenbank, in die Sqoop Daten exportiert. Der Standardname lautet "HDISqoop". Der Tabellenname für die Sqooop-Job-Ausgabe ist "AvgDelays". </td></tr>
	</table>
2. Öffnen Sie PowerShell ISE.
2. Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:

		[CmdletBinding()]
		Param(
		
		    # HDInsight cluster variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the HDInsight cluster name. If the cluster doesn't exist, the script will create one.")]
		    [String]$hdinsightClusterName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [AllowEmptyString()]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [AllowEmptyString()]
		    [String]$blobContainerName,
		
		    #SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name where to export data.")]
		    [String]$sqlDatabaseServerName,  # specify the Azure SQL database server name where you want to export data to.
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login username.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name where data will be exported to.")]
		    [String]$sqlDatabaseName  # the default value is HDISqoop
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - HDinsight cluster variables
		[int]$clusterSize = 1                # One data node is sufficient for this tutorial.
		[String]$location = "Central US"     # For better performance, choose a data center near you.
		[String]$hadoopUserLogin = "admin"   # Use "admin" as the Hadoop login name
		[String]$hadoopUserpw = "Pass@word1" # Use "Pass@word1" as te the Hadoop login password
		
		[Bool]$isNewCluster = $false      # Indicates whether a new HDInsight cluster is created by the script.  
		                                  # If this variable is true, then the script can optionally delete the cluster after running the Hive and Sqoop jobs.
		
		[Bool]$isNewStorageAccount = $false
		
		$storageAccountName = $storageAccountName.ToLower() # Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only.
		#endregion
		
		#region - Hive job variables
		[String]$hqlScriptFile = "wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql" # The HiveQL script is located in a public Blob container. Update this URI if you want to use your own script file.
		
		[String]$jobStatusFolder = "/tutorials/flightdelays/jobstatus" # The script saves both the output data and the job status file to the default container.
		                                                               # The output data path is set in the HiveQL file.
		
		#[String]$jobOutputBlobName = "tutorials/flightdelays/output/000000_0" # This is the output file of the Hive job. The path is set in the HiveQL script.
		#endregion
		
		#region - Sqoop job variables
		[String]$sqlDatabaseTableName = "AvgDelays" 
		[String]$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
		#endregion Constants and variables
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#endregion
		
		#region - Validate user input, and provision HDInsight cluster if needed
		Write-Host "`nValidating user input ..." -ForegroundColor Green
		
		# Both the Azure SQL database server and database must exist.
		if (-not (Get-AzureSqlDatabaseServer|Where-Object{$_.ServerName -eq $sqlDatabaseServerName})){
		    Write-host "The Azure SQL database server, $sqlDatabaseServerName doesn't exist." -ForegroundColor Red
		    Exit
		}
		else
		{
		    if (-not ((Get-AzureSqlDatabase -ServerName $sqlDatabaseServerName)|Where-Object{$_.Name -eq $sqlDatabaseName})){
		        Write-host "The Azure SQL database, $sqlDatabaseName doesn't exist." -ForegroundColor Red
		        Exit
		    }
		}
		
		if (Test-AzureName -Service -Name $hdinsightClusterName)     # If it is an existing HDInsight cluster ...
		{
		    Write-Host "`tThe HDInsight cluster, $hdinsightClusterName, exists. This cluster will be used to run the Hive job." -ForegroundColor Cyan
		
		    #region - Retrieve the default storage account/container names of the cluster exists
		    # The Hive job output will be stored in the default container. The 
		    # information is used to download a copy of the output file from 
		    # Blob storage to workstation for the validation purpose.
		    Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
		                -ForegroundColor Green
		
		    $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName
		
		    # Use the default storage account and the default container even if the names are different from the user input
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
		                            -replace ".blob.core.windows.net"
		    $blobContainerName = $hdi.DefaultStorageAccount.StorageContainerName
		
		    Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
		                -ForegroundColor Cyan
		    Write-Host "`tThe default Blob container for the cluster is $blobContainerName." `
		                -ForegroundColor Cyan
		    #endregion
		}
		else     #If the cluster doesn't exist, a new one will be provisioned.
		{
		    if ([string]::IsNullOrEmpty($storageAccountName))
		    {
		        Write-Host "You must provide a storage account name" -ForegroundColor Red
		        EXit           
		    }
		    else
		    {
		        # If the container name is not specified, use the cluster name as the container name
		        if ([string]::IsNullOrEmpty($blobContainerName))
		        {
		            $blobContainerName = $hdinsightClusterName
		        }
		        $blobContainerName = $blobContainerName.ToLower()
		
		        #region - Provision HDInsigtht cluster
		        # Create an Azure storage account if it doesn't exist
		        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		        {
		            Write-Host "`nCreating the Azure storage account, $storageAccountName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageAccount -StorageAccountName $storageAccountName.ToLower() -Location $location)){
		                Write-Host "Error creating the storage account, $storageAccountName" -ForegroundColor Red
		                Exit
		            }
		            $isNewStorageAccount = $True
		        }
		
		        # Create a Blob container used as the default container
		        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		        {
		            Write-Host "`nCreating the Azure Blob container, $blobContainerName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageContainer -name $blobContainerName -Context $storageContext)){
		                Write-Host "Error creating the Blob container, $blobContainerName" -ForegroundColor Red
		                Exit
		            }
		        }
		
		        # Create a new HDInsight cluster
		        Write-Host "`nProvisioning the HDInsight cluster, $hdinsightClusterName ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		        if (-not $credential)
		        {
		            Write-Host "Error creating the PSCredential object" -ForegroundColor Red
		            Exit
		        }
		
		        if (-not (New-AzureHDInsightCluster -Name $hdinsightClusterName -Location $location -Credential $credential -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $blobContainerName -ClusterSizeInNodes $clusterSize)){
		            Write-Host "Error provisioning the cluster, $hdinsightClusterName." -ForegroundColor Red
		            Exit
		        }
		        Else
		        {
		            $isNewCluster = $True
		        }
		        #endregion
		    }
		}
		#endregion
		
		#region - Submit Hive job
		Write-Host "`nSubmitting the Hive job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		Use-AzureHDInsightCluster $HDInsightClusterName
		$response = Invoke-Hive -File $hqlScriptFile -StatusFolder $jobStatusFolder
		
		Write-Host "`nThe Hive job status" -ForegroundColor Cyan
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		write-Host $response
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		#endregion 
		
		#region - run Sqoop job
		Write-Host "`nSubmitting the Sqoop job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		[String]$exportDir = "wasb://$blobContainerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
				
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureHDInsightJob -Cluster $hdinsightClusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardOutput
		#endregion
		
		#region - Delete the HDInsight cluster
		if ($isNewCluster -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the HDInsight Hadoop cluster ' $hdinsightClusterName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the HDInsight cluster ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureHDInsightCluster -Name $hdinsightClusterName
		    }
		}
		#endregion
		
		#region - Delete the storage account
		if ($isNewStorageAccount -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the Azure storage account ' $storageAccountName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the Azure storage account ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureStorageAccount -StorageAccountName $storageAccountName
		    }
		}
		#endregion
		
		Write-Host "End of the PowerShell script" -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow

4. Drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe sollte der Folgenden ähneln:

	![HDI.FlightDelays.RunHiveJob.output][img-hdi-flightdelays-run-hive-job-output]
		
5. Stellen Sie eine Verbindung mit der SQL-Datenbank her, und suchen Sie die durchschnittlichen Flugverspätungen nach Stadt in der Tabelle *AvgDelays*:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Anhang A - Hochladen der Daten zu Flugverspätungen in den Azure-Blob-Speicher
Vor dem Hochladen der Datendatei und der HiveQL-Skriptdateien (siehe [Anhang B](#appendix-b)) sind einige Überlegungen erforderlich. Der Grundgedanke ist, die Datendatei und HiveQL-Datei vor der Bereitstellung eines HDInsight-Clusters und der Ausführung des Hive-Jobs zu speichern.  Sie haben zwei Möglichkeiten:

- **Verwenden Sie das gleiche Azure-Speicherkonto, das vom HDInsight-Cluster als Standarddateisystem verwendet wird.** Da der HDInsight-Cluster über den Speicherkonto-Zugriffsschlüssel verfügt, müssen Sie keine weiteren Änderungen vornehmen.
- **Verwenden Sie ein anderes Azure-Speicherkonto von dem Standarddateisystem des HDInsight-Clusters.** In diesem Fall müssen Sie den Bereitstellungsteil des PowerShell-Skripts ändern, das Sie unter [Bereitstellen des HDInsight-Clusters und Ausführen von Hive/Sqoop-Aufträgen] finden,(#runjob) um das Speicherkonto als zusätzliches Speicherkonto hinzuzufügen. Anweisungen hierzu finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision]. Auf diese Weise kennt der HDInsight-Cluster den Zugriffsschlüssel für das Speicherkonto.

>[AZURE.NOTE] Der WASB-Pfad für die Datendatei ist fest in der HiveQL-Skriptdatei programmiert. Sie müssen ihn entsprechend aktualisieren.

**So laden Sie die Flugdaten herunter**

1. Navigieren Sie zu [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA).
2. Wählen Sie auf der Website die folgenden Werte aus:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Filter Year</td><td>2013 </td></tr>
	<tr><td>Filter Period</td><td>January</td></tr>
	<tr><td>Fields:</td><td>*Jahr*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (alle anderen Felder löschen)</td></tr>
	</table>

3. Klicken Sie auf **Herunterladen**. 
4. Extrahieren Sie die Datei in den Ordner **C:\Tutorials\FlightDelays\Data**.  Jede Datei ist eine CSV-Datei und hat eine Größe von ungefähr 60 GB.
5.	Geben Sie der Datei den Monat, für den sie Daten enthält, als neuen Namen. Zum Beispiel würde die Datei, die die Daten für Januar enthält, den Namen *January.csv* erhalten.
6. Wiederholen Sie Schritt 2 und 5, um eine Datei für jeden der 12 Monate des Jahres 2013 herunterzuladen. Für die Ausführung des Lernprogramms benötigen Sie mindestens eine Datei.  

**So laden Sie die Daten zu Flugverspätungen in den Azure Blob-Speicher hoch**

1. Bereiten Sie die Parameter vor:

	<table border="1">
	<tr><th>Variablenname</th><th>Hinweise</th></tr>
	<tr><td>$storageAccountName</td><td>Das Azure-Speicherkonto, in das Sie die Daten hochladen möchten.</td></tr>
	<tr><td>$blobContainerName</td><td>Der Blob-Container, in den Sie die Daten hochladen möchten.</td></tr>
	</table>
2. Öffnen Sie PowerShell ISE.
2. Fügen Sie das folgende Skript in den Skriptbereich ein:

		[CmdletBinding()]
		Param(
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)
		
		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelays\Data"  # the source folder
		$destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#EndRegion
		
		#Region - Validate user inpute
		# Validate the storage account
		if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		{
		    Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
		    exit
		}
		
		# Validate the container
		$storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
		    Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
		    Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
		    foreach ($item in Get-ChildItem -Path $localFolder){
		        $fileName = "$localFolder\$item"
		        $blobName = "$destFolder/$item"
		
		        Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
		        Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
		    }
		}
		else
		{
		    Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDinsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion

3. Drücken Sie **F5**, um das Skript auszuführen.

Wenn Sie eine andere Methode zum Hochladen von Dateien verwenden möchten, stellen Sie sicher, dass der Dateipfad *tutorials/flightdelays/data* lautet. Für den Zugriff auf die Dateien gilt folgende Syntax:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* ist das virtuelle Verzeichnis, das Sie beim Hochladen der Dateien erstellt haben. Überprüfen Sie, dass 12 Dateien vorhanden sind, eine für jeden Monat.

>[AZURE.NOTE] Sie müssen die Hive-Abfrage aktualisieren, um vom neuen Speicherort lesen zu können.

> Sie müssen entweder die Zugriffsberechtigung für den Container auf "Öffentlich" ändern oder das Speicherkonto an den HDInsight-Cluster binden.  Andernfalls kann die Hive-Abfragezeichenfolge nicht auf die Datendateien zugreifen. 

---
##<a id="appendix-b"></a>Anhang B - Erstellen und Hochladen eines HiveQL-Skripts

Mit Azure PowerShell können Sie mehrere HiveQL-Anweisungen gleichzeitig ausführen oder die HiveQL-Anweisung in eine Skriptdatei einfügen. Der Abschnitt zeigt, wie Sie ein HiveQL-Skript erstellen und das Skript mithilfe von PowerShell in den Azure-Blob-Speicher hochladen. Hive erfordert, dass HiveQL-Skripte auf WASB gespeichert werden.

Das HiveQL-Skript führt folgende Schritte aus:

1. **Löschen der Tabelle "delays_raw"**für den Fall, dass die Tabelle bereits vorhanden ist.
2. **Erstellen der externen Hive-Tabelle "delays_raw"**, die auf den Speicherort WASB mit den Flugverspätungsdateien verweist. Diese Abfrage legt fest, dass Felder durch "," getrennt und Zeilen mit "\n" beendet werden. Dies stellt ein Problem dar, wenn Feldwerte Kommas *contain*, da Hive nicht zwischen einem Komma als Feldtrennzeichen und einem Komma als Bestandteil des Feldwerts unterscheiden kann (was bei Feldwerten für ORIGIN\_CITY\_NAME und DEST\_CITY\_NAME der Fall ist). Zur Behebung dieses Problems erstellt die Abfrage TEMP-Spalten zur Aufbewahrung von Daten, die fehlerhaft in Spalten aufgeteilt sind.  
3. **Löschen der Tabelle "delays"**für den Fall, dass die Tabelle bereits vorhanden ist.
4. **Erstellen der Tabelle "delays"**. Es ist hilfreich, die Daten vor der weiteren Verarbeitung zu bereinigen. Diese Abfrage erstellt eine neue Tabelle, *delays* aus der Tabelle *delays_raw*. Beachten Sie, dass die TEMP-Spalten (wie bereits erwähnt) nicht kopiert werden und dass die *substring*-Funktion verwendet wird, um Anführungszeichen aus den Daten zu entfernen. 
5. **Berechnet die durchschnittlichen wetterbedingten Verspätungen und gruppiert die Ergebnisse nach Name der Stadt.** Die Ergebnisse werden außerdem in WASB ausgegeben. Beachten Sie, dass die Abfrage Apostrophe aus den Daten entfernt und Zeilen ausschließt, bei denen der Wert für *weather_deal*y *null* ist. Dies ist erforderlich, da Sqoop, das später in diesem Lernprogramm verwendet wird, diese Werte standardmäßig nicht ordnungsgemäß verarbeiten kann.

Eine vollständige Liste der HiveQL-Befehle finden Sie unter [Hive-Datendefinitionssprache][hadoop-hiveql]. Jeder HiveQL-Befehl muss mit einem Semikolon enden.

**So erstellen Sie eine HiveQL-Skriptdatei**

1. Bereiten Sie die Parameter vor:

	<table border="1">
	<tr><th>Variablenname</th><th>Hinweise</th></tr>
	<tr><td>$storageAccountName</td><td>Das Azure-Speicherkonto, in das Sie das HiveQL-Skript hochladen möchten.</td></tr>
	<tr><td>$blobContainerName</td><td>Der Blob-Container, in den Sie das HiveQL-Skript hochladen möchten.</td></tr>
	</table>
2. Öffnen Sie Azure PowerShell ISE.

2. Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:

		[CmdletBinding()]
		Param(
		
		    # Azure blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		
		)
		
		#region - define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# the HQL script file is exported as this file before uploaded to WASB
		$hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
		
		# the HQL script file will be upload to WASB as this blob name
		$hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
		
		# this two constants are used by the HQL scrpit file
		#$srcDataFolder = "tutorials/flightdelays/data" 
		$dstDataFolder = "/tutorials/flightdelays/output"
		#endregion
		
		#region - Validate the file and file path
		
		# check if a file with the same file name already exist on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
		    $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
		    if ($isDelete.ToLower() -ne "y")
		    {
		        Exit
		    }
		}
		
		# create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
		    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
		    new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		    Add-AzureAccount
		}
		#endregion
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
		            -ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
		        "YEAR string, " +
		        "FL_DATE string, " +
		        "UNIQUE_CARRIER string, " +
		        "CARRIER string, " +
		        "FL_NUM string, " +
		        "ORIGIN_AIRPORT_ID string, " +
		        "ORIGIN string, " +
		        "ORIGIN_CITY_NAME string, " +
		        "ORIGIN_CITY_NAME_TEMP string, " +
		        "ORIGIN_STATE_ABR string, " +
		        "DEST_AIRPORT_ID string, " +
		        "DEST string, " +
		        "DEST_CITY_NAME string, " +
		        "DEST_CITY_NAME_TEMP string, " +
		        "DEST_STATE_ABR string, " +
		        "DEP_DELAY_NEW float, " +
		        "ARR_DELAY_NEW float, " +
		        "CARRIER_DELAY float, " +
		        "WEATHER_DELAY float, " +
		        "NAS_DELAY float, " +
		        "SECURITY_DELAY float, " +
		        "LATE_AIRCRAFT_DELAY float) " +
		    "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
		    "LINES TERMINATED BY '\n' " +
		    "STORED AS TEXTFILE " +
		    "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';" 
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
		    "SELECT YEAR AS year, " +
		        "FL_DATE AS flight_date, " +
		        "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
		        "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
		        "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
		        "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
		        "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
		        "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
		        "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
		        "DEST_AIRPORT_ID AS dest_airport_id, " +
		        "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
		        "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
		        "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
		        "DEP_DELAY_NEW AS dep_delay_new, " +
		        "ARR_DELAY_NEW AS arr_delay_new, " +
		        "CARRIER_DELAY AS carrier_delay, " +
		        "WEATHER_DELAY AS weather_delay, " +
		        "NAS_DELAY AS nas_delay, " +
		        "SECURITY_DELAY AS security_delay, " +
		        "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
		    "FROM delays_raw;" 
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
		    "SELECT regexp_replace(origin_city_name, '''', ''), " +
		        "avg(weather_delay) " +
		    "FROM delays " +
		    "WHERE weather_delay IS NOT NULL " +
		    "GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to WASB
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext 
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	Im Skript werden folgende Variablen verwendet:

	- **$hqlLocalFileName**: Das Skript speichert die HiveQL-Skriptdatei lokal, bevor diese zu WASB hochgeladen wird. Dies ist der Dateiname. Der Standardwert ist <u>C:\tutorials\flightdelays\flightdelays.hql</u>.
	- **$hqlBlobName**: Dies ist der Blob-Name der HiveQL-Skriptdatei, der im Azure-Blob-Speicher verwendet wird. Der Standardwert ist <u>tutorials/flightdelays/flightdelays.hql</u>. Da die Datei direkt zum Azure-Blob-Speicher geschrieben wird, befindet sich KEIN "/" am Anfang des Blob-Namens. Wenn Sie von WASB auf die Datei zugreifen möchten, müssen Sie "/" am Anfang des Dateinamens hinzufügen.
	- **$srcDataFolder** und **$dstDataFolder**:  = "tutorials/flightdelays/data" 
 = "tutorials/flightdelays/output"


---
##<a id="appendix-c"></a>Anfang A: Vorbereiten der Azure SQL-Datenbank für die Sqoop-Job-Ausgabe
**So bereiten Sie die SQL-Datenbank vor (mit dem Sqoop-Skript zusammenführen)**

1. Bereiten Sie die Parameter vor:

	<table border="1">
	<tr><th>Variablenname</th><th>Hinweise</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Name für den Azure SQL-Datenbankserver. Geben Sie nichts ein, um einen neuen Server zu erstellen.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Anmeldename für den Azure SQL-Datenbankserver. Wenn $sqlDatabaseServerName ein vorhandener Server ist, werden der Anmeldename und das Anmeldekennwort für die Authentifizierung beim Server verwendet.  Andernfalls werden sie zum Erstellen eines neuen Servers verwendet.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Anmeldekennwort für den Azure SQL-Datenbankserver.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>Dieser Wert wird nur verwendet, wenn ein neuer Azure-Datenbankserver erstellt wird.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Die zum Erstellen der Tabelle AvgDelays für den Sqoop-Job verwendete SQL-Datenbank. Wenn Sie nicht eingeben, wird eine Datenbank mit dem Namen "HDISqoop" erstellt. Der Tabellenname für die Sqooop-Job-Ausgabe ist "AvgDelays". </td></tr>
	</table>
2. Öffnen Sie PowerShell ISE. 
2. Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:
	
		[CmdletBinding()]
		Param(
		
		    # SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the region to create the Database in.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseLocation,   #For example, West US.
		
		    # SQL database variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
		            [origin_city_name] [nvarchar](50) NOT NULL,
		            [weather_delay] float,
		        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
		        (
		            [origin_city_name] ASC
		        )
		        )"
		#endregion

		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		Add-AzureAccount
		}
		#endregion
		
		#region - Create and validate Azure SQL Database server
		if ([string]::IsNullOrEmpty($sqlDatabaseServer))
		{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
			$sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
		    Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
		    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress	
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
		}
		else
		{
		    $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
		    if (! $dbServer)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
		    }
		}
		#endregion
		
		#region - Create and validate Azure SQL database
		if ([string]::IsNullOrEmpty($sqlDatabaseName))
		{
			Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green
		
			$sqlDatabaseName = "HDISqoop"
			$sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
		
		    $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 
			
			$sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
		}
		else
		{
		    $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		    if (! $db)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
		    }
		}
		#endregion
			
		#region -  Excute a SQL command to create the AvgDelays table
			
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
			
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] Das Skript verwendet einen REST-Dienst, http://bot.whatismyipaddress.com, um Ihre externe IP-Adresse abzurufen. Die IP-Adresse wird für das Erstellen einer Firewall-Regel für den SQL-Datenbankserver verwendet.  

	Im Skript werden u. a. folgende Variablen verwendet:

	- **$ipAddressRestService**: Der Standardwert ist <u>http://bot.whatismyipaddress.com</u>. Es handelt sich um einen REST-Dienst mit öffentlicher IP-Adresse, mit dem die externe IP-Adresse abgerufen wird. Bei Bedarf können Sie andere Dienst verwenden. Die externe IP-Adresse, die vom Dienst abgerufen wurde, wird verwendet, um eine Firewallregel für Ihren Azure SQL-Datenbankserver zu erstellen, sodass Sie von der Arbeitsstation aus auf die Datenbank zugreifen können (mit PowerShell-Skript).
	- **$fireWallRuleName**: Dies ist der Name der Firewall des Azure SQL-Datenbankservers. Der Standardname lautet <u>FlightDelay</u>. Bei Bedarf können Sie den Namen ändern.
	- **$sqlDatabaseMaxSizeGB**: Dieser Wert wird nur verwendet, wenn ein neuer Azure SQL-Datenbankserver erstellt wird. Der Standardwert ist <u>10 GB</u>. 10 GB reicht für dieses Lernprogramm aus.
	- **$sqlDatabaseName**: Dieser Wert wird nur verwendet, wenn eine neue Azure SQL-Datenbank erstellt wird. Der Standardwert ist <u>HDISqoop</u>. Wenn Sie diesen umbenennen, müssen Sie das Sqoop PowerShell-Skript entsprechend aktualisieren. 

4. Drücken Sie **F5**, um das Skript auszuführen. 
5. Validieren Sie die Skriptausgabe. Überprüfen Sie, ob das Skript erfolgreich ausgeführt wurde.	

##<a id="nextsteps"></a> Nächste Schritte
Jetzt haben Sie erfahren, wie Sie Folgendes vornehmen können: Dateien in den Blob-Speicher hochladen, eine Hive-Tabelle mit Daten aus dem Blob-Speicher füllen, Hive-Abfragen ausführen und Sqoop verwenden, um Daten aus dem HDFS in Azure-SQL-Datenbank zu exportieren. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit HDInsight][hdinsight-get-started]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]
* [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]
* [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png


<!--HONumber=42-->
