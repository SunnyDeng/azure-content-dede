<properties
	pageTitle="Analysieren von Daten zu Flugverspätungen mit Hadoop in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie mithilfe eines Windows PowerShell-Skripts einen HDInsight-Cluster erstellen, einen Hive-Auftrag ausführen, einen Sqoop-Auftrag ausführen und den Cluster löschen."
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
	ms.date="12/01/2015"
	ms.author="jgao"/>

#Analysieren von Flugverspätungsdaten mit Hive in HDInsight

Hive ermöglicht die Ausführung eines Hadoop MapReduce-Auftrags über eine SQL-ähnliche Skriptsprache namens *[HiveQL][hadoop-hiveql]*, die zur Zusammenfassung, Abfrage und Analyse großer Datenmengen verwendet werden kann.

> [AZURE.NOTE] Die Schritte in diesem Dokument erfordern einen Windows-basierten HDInsight-Cluster. Schritte für Linux-basierte Cluster finden Sie unter [Analysieren von Flugverspätungsdaten mit Hive in HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Einer der größten Vorteile von Azure HDInsight ist die Trennung von Datenspeicher und Server. HDInsight verwendet zur Datenspeicherung Azure Blob-Speicher. Ein typischer Auftrag besteht aus drei Teilen:

1. **Speichern von Daten im Azure-Blobspeicher** Dies kann ein fortlaufender Prozess sein. Es werden zum Beispiel Wetterdaten, Sensordaten, Webprotokolle und in diesem Fall Daten über Flugverspätungen im Azure-Blobspeicher gespeichert.
2. **Ausführen von Aufträgen.** Für die Bearbeitung der Daten führen Sie ein Windows PowerShell-Skript (oder eine Clientanwendung) aus, um einen HDInsight-Cluster zu erstellen, Aufträge auszuführen und den Cluster zu löschen. Die Aufträge speichern Ausgabedaten im Azure-Blobspeicher. Die Ausgabedaten bleiben selbst nach dem Löschen des Clusters erhalten. So bezahlen Sie nur für den tatsächlichen Verbrauch.
3. **Rufen Sie die Ausgabe aus dem Azure-Blobspeicher ab**, oder exportieren Sie für dieses Lernprogramm die Daten in eine Azure SQL-Datenbank.

Das folgende Diagramm veranschaulicht das Szenario und die Struktur dieses Lernprogramms:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Hinweis**: Die Nummern im Diagramm stehen für die Abschnittstitel. **M** steht für den Hauptprozess. **A** steht für den Inhalt im Anhang.

In diesem Lernprogramm geht es hauptsächlich um die Verwendung eines PowerShell-Skripts zu folgenden Zwecken:

- Erstellen eines HDInsight-Clusters
- Ausführen eines Hive-Jobs für das Cluster zur Berechnung der durchschnittlichen Verspätung an Flughäfen. Die Daten bezüglich Flugverspätungen werden in einem Azure-Blobspeicherkonto gespeichert
- Ausführen eines Sqoop-Jobs zum Exportieren der Hive-Job-Ausgabe in eine Azure SQL-Datenbank
- Löschen des HDInsight-Clusters

In den Anhängen finden Sie Anweisungen zum Hochladen der Flugverspätungsdaten, zum Erstellen/Hochladen der Hive-Abfragezeichenfolge und zum Vorbereiten der Azure SQL-Datenbank für den Sqoop-Auftrag.

> [AZURE.NOTE] Die Schritte in diesem Dokument gelten speziell für Windows-basierte HDInsight-Cluster. Schritte für Linux-basierte Cluster finden Sie unter [Analysieren von Flugverspätungsdaten mit Hive in HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

###Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren von Azure PowerShell 1.0 und höher](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

**In diesem Lernprogramm verwendete Dateien**

In diesem Lernprogramm werden Flugdaten hinsichtlich der termingerechten Durchführung von Fluggesellschaften des [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA) verwendet. Eine Kopie der Daten wurde in einen Azure-Blobspeichercontainer mit öffentlicher Blobzugriffsberechtigung hochgeladen. Ein Teil des PowerShell-Skripts kopiert die Daten aus dem öffentlichen Blobcontainer in den standardmäßigen Blobcontainer des Clusters. Das HiveQL-Skript wird ebenfalls in denselben Blobcontainer kopiert. Weitere Informationen darüber, wie Sie die Daten in Ihr eigenes Speicherkonto einfügen/hochladen und die HiveQL-Skriptdatei erstellen/hochladen können, finden Sie in [Anhang A](#appendix-a) und [Anhang B](#appendix-b).

In der folgenden Tabelle sind die in diesem Lernprogramm verwendeten Dateien aufgelistet:

<table border="1">
<tr><th>Dateien</th><th>Beschreibung</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Die HiveQL-Skriptdatei, die vom Hive-Auftrag verwendet wird, den Sie ausführen werden. Dieses Skript wurde in ein Azure-Blobspeicherkonto mit öffentlichem Zugriff hochgeladen. <a href="#appendix-b">Anhang&#160;B</a> enthält Anweisungen zum Vorbereiten und Hochladen dieser Datei in Ihr eigenes Azure-Blobspeicherkonto.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Die Eingabedaten für den Hive-Auftrag. Die Daten wurden in ein Azure-Blobspeicherkonto mit öffentlichem Zugriff hochgeladen. In <a href="#appendix-a">Anhang&#160;A</a> finden Sie Anweisungen zum Einfügen und Hochladen der Daten in Ihr eigenes Azure-Blobspeicherkonto.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Der Ausgabepfad für den Hive-Auftrag. Der Standardcontainer wird für die Speicherung der Ausgabedaten verwendet.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Der Ordner mit dem Hive-Job-Status im Standardcontainer.</td></tr>
</table>


##Erstellen eines Clusters und Ausführen der Hive-/Sqoop-Aufträge

Hadoop MapReduce verwendet Stapelbearbeitung. Die kosteneffizienteste Möglichkeit, einen Hive-Auftrag auszuführen, besteht darin, einen Cluster für den Auftrag zu erstellen und den Auftrag nach Abschluss zu löschen. Das folgende Skript erläutert den gesamten Vorgang. Weitere Informationen zur Erstellung eines HDInsight-Clusters und zur Ausführung von Hive-Aufträgen finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision] und [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

**So führen Sie Hive-Abfragen mit Azure PowerShell aus**

1. Erstellen Sie eine Azure SQL-Datenbank und die Tabelle für die Ausgabe des Sqoop-Auftrags mithilfe der Anweisungen in [Anhang C](#appendix-c).
3. Öffnen Sie Windows PowerShell ISE, und führen Sie folgendes Skript aus:

		$subscriptionID = "<Azure Subscription ID>"
		$nameToken = "<Enter an Alias>" 
		
		###########################################
		# You must configure the follwing variables
		# for an existing Azure SQL Database
		###########################################
		$existingSqlDatabaseServerName = "<Azure SQL Database Server>"
		$existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
		$existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
		$existingSqlDatabaseName = "<Azure SQL Database name>"
		
		$localFolder = "E:\Tutorials\Downloads" # A temp location for copying files. 
		$azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
		
		###########################################
		# (Optional) configure the following variables
		###########################################
		
		$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

		$resourceGroupName = $namePrefix + "rg"
		$location = "EAST US 2"
		
		$HDInsightClusterName = $namePrefix + "hdi"
		$httpUserName = "admin"
		$httpPassword = "<Enter the Password>"
		
		$defaultStorageAccountName = $namePrefix + "store"
		$defaultBlobContainerName = $HDInsightClusterName # use the cluster name
		
		$existingSqlDatabaseTableName = "AvgDelays"
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
		
		$hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
		
		$jobStatusFolder = "/tutorials/flightdelays/jobstatus"
		
		###########################################
		# Login
		###########################################
		try{
			$acct = Get-AzureRmSubscription
		}
		catch{
			Login-AzureRmAccount
		}
		Select-AzureRmSubscription -SubscriptionID $subscriptionID
		
		###########################################
		# Create a new HDInsight cluster
		###########################################
		
		# Create ARM group
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
		
		# Create the default storage account
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
		
		# Create the default Blob container
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
		$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
		New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
		
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
			-DefaultStorageContainer $existingDefaultBlobContainerName 
		
		
		###########################################
		# Prepare the HiveQL script and source data
		###########################################
		
		# Create the temp location  
		New-Item -Path $localFolder -ItemType Directory -Force 
		
		# Download the sample file from Azure Blob storage
		$context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
		$blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
		#$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
		
		# Upload data to default container
		
		$azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
		
		Invoke-Expression -Command:$azcopycmd
		
		###########################################
		# Submit the Hive job
		###########################################
		Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
		$response = Invoke-AzureRmHDInsightHiveJob `
						-Files $hqlScriptFile `
						-DefaultContainer $defaultBlobContainerName `
						-DefaultStorageAccountName $defaultStorageAccountName `
						-DefaultStorageAccountKey $defaultStorageAccountKey `
						-StatusFolder $jobStatusFolder 
		
		write-Host $response
		
		
		###########################################
		# Submit the Sqoop job
		###########################################
		$exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
						-Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureRmHDInsightJob `
						-ResourceGroupName $resourceGroupName `
						-ClusterName $hdinsightClusterName `
						-HttpCredential $httpCredential `
						-JobDefinition $sqoopDef #-Debug -Verbose
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-HttpCredential $httpCredential `
			-WaitTimeoutInSeconds 3600 `
			-Job $sqoopJob.JobId
		
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-HttpCredential $httpCredential `
			-DefaultContainer $existingDefaultBlobContainerName `
			-DefaultStorageAccountName $defaultStorageAccountName `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-JobId $sqoopJob.JobId `
			-DisplayOutputType StandardError
		
		###########################################
		# Delete the cluster
		###########################################
		Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Stellen Sie eine Verbindung zur SQL-Datenbank her, und zeigen Sie die durchschnittlichen Flugverspätungen nach Stadt geordnet in der Tabelle "AvgDelays" an:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Anhang A – Hochladen der Daten zu Flugverspätungen in den Azure-Blobspeicher
Das Hochladen der Datendatei und der HiveQL-Skriptdateien (siehe [Anhang B](#appendix-b)) erfordert eine gewisse Planung. Idealerweise sollten die Datendateien und die HiveQL-Datei vor dem Erstellen eines HDInsight-Clusters und vor dem Ausführen des Hive-Auftrags gespeichert werden. Sie haben zwei Möglichkeiten:

- **Verwenden Sie dasselbe Azure-Speicherkonto, das vom HDInsight-Cluster als Standarddateisystem verwendet wird.** Da der HDInsight-Cluster über den Zugriffsschlüssel für das Speicherkonto verfügt, müssen Sie keine weiteren Änderungen vornehmen.
- **Verwenden Sie ein anderes Azure-Speicherkonto als das Standarddateisystem des HDInsight-Clusters.** In diesem Fall müssen Sie den Erstellungsteil des Windows PowerShell-Skripts ändern, das Sie unter [Erstellen des HDInsight-Clusters und Ausführen von Hive/Sqoop-Aufträgen](#runjob) finden, um das Speicherkonto als zusätzliches Speicherkonto zu verknüpfen. Anweisungen hierzu finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight][hdinsight-provision]. Der HDInsight-Cluster kennt dann den Zugriffsschlüssel für das Speicherkonto.

>[AZURE.NOTE] Der Blobspeicherpfad für die Datendatei ist fest in der HiveQL-Skriptdatei programmiert. Sie müssen ihn entsprechend aktualisieren.

**So laden Sie die Flugdaten herunter**

1. Rufen Sie die Website von [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA) auf.
2. Wählen Sie auf der Website die folgenden Werte aus:

	<table border="1">
<tr><th>Name</th><th>Wert</th></tr>
<tr><td>Filter Year</td><td>2013 </td></tr>
<tr><td>Filter Period</td><td>January</td></tr>
<tr><td>Felder</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (Entfernen Sie die Häkchen bei allen anderen Feldern.)</td></tr>
</table>

3. Klicken Sie auf **Download**.
4. Entpacken Sie die Datei im Ordner **C:\\Tutorials\\FlightDelay\\2013Data**. Jede Datei ist eine CSV-Datei und hat eine Größe von ungefähr 60 GB.
5.	Geben Sie der Datei den Monat, für den sie Daten enthält, als neuen Namen. Die Datei mit Daten aus dem Monat Januar hieße dann beispielsweise *January.csv*.
6. Wiederholen Sie die Schritte 2 und 5, um eine Datei für jeden der 12 Monate des Jahres 2013 herunterzuladen. Für das Lernprogramm benötigen Sie mindestens eine Datei.  

**So laden Sie Daten zu Flugverspätungen in den Azure-Blobspeicher hoch**

1. Bereiten Sie die Parameter vor:

	<table border="1">
<tr><th>Variablenname</th><th>Hinweise</th></tr>
<tr><td>$storageAccountName</td><td>Das Azure-Speicherkonto, in das Sie die Daten hochladen möchten.</td></tr>
<tr><td>$blobContainerName</td><td>Der Blobcontainer, in den Sie die Daten hochladen möchten.</td></tr>
</table>
2. Öffnen Sie Azure PowerShell ISE.
3. Fügen Sie das folgende Skript in den Skriptbereich ein:

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
		$localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
		$destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
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
		
		# List the uploaded files on HDInsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion




4. Drücken Sie **F5**, um das Skript auszuführen.

Wenn Sie für das Hochladen der Dateien eine andere Methode verwenden möchten, stellen Sie sicher, dass der Dateipfad „tutorials/flightdelay/data“ lautet. Für den Zugriff auf die Dateien gilt folgende Syntax:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Bei „tutorials/flightdelay/data“ handelt es sich um den virtuellen Ordner, den Sie beim Hochladen der Dateien erstellt haben. Überprüfen Sie, dass 12 Dateien vorhanden sind, eine für jeden Monat.

>[AZURE.NOTE] Sie müssen die Hive-Abfrage aktualisieren, um vom neuen Speicherort lesen zu können.

> Sie müssen entweder die Zugriffsberechtigung für den Container in "Öffentlich" ändern oder das Speicherkonto an den HDInsight-Cluster binden. Andernfalls kann die Hive-Abfragezeichenfolge nicht auf die Datendateien zugreifen.

---
##<a id="appendix-b"></a>Anhang B – Erstellen und Hochladen eines HiveQL-Skripts

Mit Azure PowerShell können Sie mehrere HiveQL-Anweisungen gleichzeitig ausführen oder die HiveQL-Anweisung in eine Skriptdatei paketieren. Der Abschnitt zeigt, wie Sie ein HiveQL-Skript erstellen und das Skript mithilfe von Azure PowerShell in den Azure-Blobspeicher hochladen. Hive erfordert, dass HiveQL-Skripte im Azure-Blobspeicher gespeichert werden.

Das HiveQL-Skript führt Folgendes durch:

1. **Löschen der Tabelle delays\_raw**, wenn diese Tabelle bereits vorhanden ist.
2. **Erstellen der externen Hive-Tabelle "delays\_raw"**, die auf den Blobspeicherort mit den Dateien zu Flugverspätungen verweist. Diese Abfrage legt fest, dass Felder durch "," getrennt und Zeilen mit "\\n" beendet werden. Dies stellt ein Problem dar, wenn Feldwerte Kommas enthalten, da Hive nicht zwischen einem Komma als Trennzeichen für Felder und einem Komma als Teil eines Feldwerts unterscheiden kann. (Letzteres ist der Fall bei Feldwerten für ORIGIN\_CITY\_NAME und DEST\_CITY\_NAME.) Zur Behebung dieses Problems erstellt die Abfrage TEMP-Spalten zur Aufbewahrung von Daten, die fehlerhaft in Spalten aufgeteilt sind.  
3. **Löschen der Tabelle "delays"**, falls diese Tabelle bereits vorhanden ist.
4. **Erstellen der Tabelle delays**. Es ist hilfreich, die Daten vor der weiteren Verarbeitung zu bereinigen. Mit dieser Abfrage wird eine neue Tabelle *delays* aus der Tabelle "delays\_raw" erstellt. Beachten Sie, dass die TEMP-Spalten (wie zuvor erwähnt) nicht kopiert werden und dass die **substring**-Funktion verwendet wird, um Anführungszeichen aus den Daten zu entfernen.
5. **Berechnen der durchschnittlichen Verspätungen aufgrund des Wetters und Gruppieren der Ergebnisse nach Stadt.** Darüber hinaus werden die Ergebnisse in den Blobspeicher ausgegeben. Beachten Sie, dass bei der Abfrage Apostrophe aus den Daten entfernt werden und dass Zeilen ausgeschlossen werden, bei denen der Wert für **weather\_delay** Null ist. Dies ist erforderlich, da Sqoop, das Sie später in diesem Lernprogramm verwenden werden, diese Werte standardmäßig nicht ordnungsgemäß verarbeitet.

Eine vollständige Liste der HiveQL-Befehle finden Sie unter [Hive Data Definition Language][hadoop-hiveql] (Hive-Datendefinitionssprache, in englischer Sprache). Jeder HiveQL-Befehl muss mit einem Semikolon enden.

**So erstellen Sie eine HiveQL-Skriptdatei**

1. Bereiten Sie die Parameter vor:

	<table border="1">
<tr><th>Variablenname</th><th>Hinweise</th></tr>
<tr><td>$storageAccountName</td><td>Das Azure-Speicherkonto, in das Sie das HiveQL-Skript hochladen möchten.</td></tr>
<tr><td>$blobContainerName</td><td>Der Blobcontainer, in den Sie das HiveQL-Skript hochladen möchten.</td></tr>
</table>
2. Öffnen Sie Azure PowerShell ISE.

3. Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:

		[CmdletBinding()]
		Param(

		    # Azure Blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#region - Define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# The HiveQL script file is exported as this file before it's uploaded to Blob storage
		$hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
		
		# The HiveQL script file will be uploaded to Blob storage as this blob name
		$hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
		
		# These two constants are used by the HiveQL script file
		#$srcDataFolder = "tutorials/flightdelay/data"
		$dstDataFolder = "/tutorials/flightdelay/output"
		#endregion

		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#region - Validate the file and file path
		
		# Check if a file with the same file name already exists on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
			$isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
			if ($isDelete.ToLower() -ne "y")
			{
				Exit
			}
		}
		
		# Create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
			Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
			new-item $folder -ItemType directory  
		}
		#end region
		
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
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to Blob storage
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	Im Skript werden folgende Variablen verwendet:

	- **$hqlLocalFileName** – Das Skript speichert die HiveQL-Skriptdatei lokal, bevor sie in den Blobspeicher hochgeladen wird. Dies ist der Dateiname. Der Standardwert lautet <u>C:\\tutorials\\flightdelay\\flightdelays.hql</u>.
	- **$hqlBlobName** – Dies ist der Blobname der HiveQL-Skriptdatei, der im Azure-Blobspeicher verwendet wird. Der Standardwert lautet „tutorials/flightdelay/flightdelays.hql“. Da die Datei direkt zum Azure-Blobspeicher geschrieben wird, befindet sich KEIN "/" am Anfang des Blobnamens. Wenn Sie im Blobspeicher auf die Datei zugreifen möchten, müssen Sie "/" an den Anfang des Dateinamens einfügen.
	- **$srcDataFolder** und **$dstDataFolder** - = "tutorials/flightdelay/data" = "tutorials/flightdelay/output"


---
##<a id="appendix-c"></a>Anhang C: Vorbereiten der Azure SQL-Datenbank für die Ausgabe des Sqoop-Auftrags
**So bereiten Sie die SQL-Datenbank vor (Zusammenführen mit dem Sqoop-Skript)**

1. Bereiten Sie die Parameter vor:

	<table border="1">
<tr><th>Variablenname</th><th>Hinweise</th></tr>
<tr><td>$sqlDatabaseServerName</td><td>Der Name für den Azure SQL-Datenbankserver. Geben Sie nichts ein, um einen neuen Server zu erstellen.</td></tr>
<tr><td>$sqlDatabaseUsername</td><td>Der Anmeldename für den Azure SQL-Datenbankserver. Wenn $sqlDatabaseServerName ein vorhandener Server ist, werden der Anmeldename und das Anmeldekennwort für die Authentifizierung beim Server verwendet. Andernfalls werden sie zum Erstellen eines neuen Servers verwendet.</td></tr>
<tr><td>$sqlDatabasePassword</td><td>Das Anmeldekennwort für den Azure SQL-Datenbankserver.</td></tr>
<tr><td>$sqlDatabaseLocation</td><td>Dieser Wert wird nur verwendet, wenn Sie einen neuen Azure-Datenbankserver erstellen.</td></tr>
<tr><td>$sqlDatabaseName</td><td>Die zum Erstellen der Tabelle "AvgDelays" für den Sqoop-Auftrag verwendete SQL-Datenbank. Wenn Sie keinen Wert eingeben, wird eine Datenbank mit dem Namen "HDISqoop" erstellt. Der Tabellenname für die Sqoop-Auftragsausgabe ist "AvgDelays". </td></tr>
</table>
2. Öffnen Sie Azure PowerShell ISE.
3. Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:

		[CmdletBinding()]
		Param(
		
			# Azure Resource group variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
			[String]$resourceGroupName,
		
			# SQL database server variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseServer, 
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user.")]
			[String]$sqlDatabaseLogin,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user password.")]
			[String]$sqlDatabasePassword,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the region to create the Database in.")]
			[String]$sqlDatabaseLocation,   #For example, West US.
		
			# SQL database variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
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
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#region - Create and validate Azure resouce group
		try{
			Get-AzureRmResourceGroup -Name $resourceGroupName
		}
		catch{
			New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
		}
		
		#EndRegion
		
		#region - Create and validate Azure SQL database server
		try{
			Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
		catch{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
		
			$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
			$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
		
			$sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
			Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
			Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
			$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
		
			#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
		}
		
		#endregion
		
		#region - Create and validate Azure SQL database
		
		try {
			Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		}
		catch {
			Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
			New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
		}
		
		#endregion
		
		#region -  Execute an SQL command to create the AvgDelays table
		
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
		
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] Das Skript verwendet einen REST-Dienst, http://bot.whatismyipaddress.com, um Ihre externe IP-Adresse abzurufen. Die IP-Adresse wird zum Erstellen einer Firewallregel für den SQL-Datenbankserver verwendet.

	Im Skript werden u. a. folgende Variablen verwendet:

	- **$ipAddressRestService** – Der Standardwert ist http://bot.whatismyipaddress.com. Es handelt sich um einen REST-Dienst mit öffentlicher IP-Adresse, mit dem die externe IP-Adresse abgerufen wird. Bei Bedarf können Sie andere Dienst verwenden. Die externe IP-Adresse, die vom Dienst abgerufen wurde, wird verwendet, um eine Firewallregel für Ihren Azure SQL-Datenbankserver zu erstellen, sodass Sie von der Arbeitsstation aus (mithilfe eines PowerShell-Skripts) auf die Datenbank zugreifen können.
	- **$fireWallRuleName** – Dies ist der Name der Firewallregel für den Azure SQL-Datenbankserver. Der Standardname lautet <u>FlightDelay</u>. Bei Bedarf können Sie den Namen ändern.
	- **$sqlDatabaseMaxSizeGB** – Dieser Wert wird nur verwendet, wenn Sie einen neuen Azure SQL-Datenbankserver erstellen. Der Standardwert ist 10 GB. 10 GB reicht für dieses Lernprogramm aus.
	- **$sqlDatabaseName** – Dieser Wert wird nur verwendet, wenn Sie eine neue Azure SQL-Datenbank erstellen. Der Standardwert ist HDISqoop. Bei einer Umbenennung müssen Sie das Sqoop PowerShell-Skript entsprechend aktualisieren.

4. Drücken Sie **F5**, um das Skript auszuführen.
5. Validieren Sie die Skriptausgabe. Überprüfen Sie, ob das Skript erfolgreich ausgeführt wurde.

##<a id="nextsteps"></a> Nächste Schritte
Jetzt wissen Sie, wie Sie eine Datei in den Azure-Blobspeicher hochladen, eine Hive-Tabelle mit Daten aus dem Azure-Blobspeicher füllen, Hive-Abfragen ausführen und Sqoop zum Exportieren von Daten aus dem HDFS in eine Azure SQL-Datenbank verwenden können. Weitere Informationen finden Sie in den folgenden Artikeln:

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
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

<!---HONumber=AcomDC_0218_2016-->