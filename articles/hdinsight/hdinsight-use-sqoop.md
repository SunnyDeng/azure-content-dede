<properties
	pageTitle="Verwenden von Hadoop Sqoop in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie Azure PowerShell auf einer Arbeitsstation verwenden können, um Sqoop-Importe und -Exporte zwischen einem Hadoop-Cluster und einer Azure SQL-Datenbank auszuführen."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/11/2015"
	ms.author="jgao"/>

#Verwenden von Sqoop mit Hadoop in HDInsight (Windows)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Sie Azure PowerShell und das HDInsight .NET SDK auf einer Arbeitsstation verwenden können, um Sqoop-Importe und -Exporte zwischen einem HDInsight-Cluster und einer Azure SQL-Datenbank oder einer SQL Server-Datenbank auszuführen.

> [AZURE.NOTE]Die Schritte in diesem Artikel können in Windows- oder Linux-basierten HDInsight-Clustern angewendet werden, sie funktionieren jedoch nur von einem Windows-Client aus.
>
> Wenn Sie einen Linux-, OS X- oder Unix-Client und einen Linux-basierten HDInsight-Server verwenden, lesen Sie unter [Verwenden von Sqoop mit Hadoop in HDInsight (SSH)](hdinsight-use-sqoop-mac-linux.md) nach.

##Was ist Sqoop?

Während Hadoop die beste Wahl für die Verarbeitung unstrukturierter und halbstrukturierter Daten wie z. B. Protokolle und Dateien ist, besteht oft auch Bedarf für die Verarbeitung strukturierter Daten, die in relationalen Datenbanken gespeichert werden.

[Sqoop][sqoop-user-guide-1.4.4] ist ein Tool zum Übertragen von Daten zwischen Hadoop-Clustern und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle in das verteilte Dateisystem von Hadoop (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und sie anschließend wieder in ein RDBMS exportieren. In diesem Beispiel verwenden Sie eine SQL-Serverdatenbank als relationale Datenbank.

Informationen zu den unterstützten Sqoop-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions].

##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Um Azure PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Ausführen von Windows PowerShell-Skripts][powershell-script].

- **Azure HDInsight-Cluster**: Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] oder unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

	<table border="1">
	<tr><th>Clustereigenschaft</th><th>Azure PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>HDInsight-Clustername</td><td>$clusterName</td><td></td><td>Ihr HDInsight-Clustername.</td></tr>
	<tr><td>Azure-Speicherkontoname</td><td>$storageAccountName</td><td></td><td>Ein im HDInsight-Cluster verfügbares Azure-Speicherkonto. Verwenden Sie für dieses Lernprogramm das Standard-Speicherkonto, das Sie während der Bereitstellung des Clusters angegeben haben.</td></tr>
	<tr><td>Azure-Blob-Containername</td><td>$containerName</td><td></td><td>Verwenden Sie in diesem Beispiel den Namen des Blobs, der für das Standarddateisystem des HDInsight-Clusters verwendet wird. Standardmäßig hat dieser denselben Namen wie der HDInsight-Cluster.</td></tr>
	</table>

- **Azure SQL-Datenbank**: Sie müssen eine Firewall-Regel für den Azure SQL-Datenbankserver konfigurieren, um Zugriff auf Ihre Arbeitsstation zu erlauben. Anweisungen zur Erstellung einer Azure SQL-Datenbank und zur Konfiguration der Firewall erhalten Sie unter [Erste Schritte mit Azure SQL-Datenbanken][sqldatabase-get-started]. Dieser Artikel beschreibt ein Windows PowerShell-Skript zur Erstellung der Azure SQL-Datenbanktabelle, die Sie für dieses Lernprogramm benötigen.

	<table border="1">
	<tr><th>Azure SQL-Datenbankeigenschaft</th><th>Azure PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>Servername der Azure SQL-Datenbank</td><td>$sqlDatabaseServer</td><td></td><td>Der Azure SQL-Datenbankserver, auf den Sqoop Daten exportiert oder von dem Sqoop Daten importiert. </td></tr>
	<tr><td>Anmeldename der Azure SQL-Datenbank</td><td>$sqlDatabaseLogin</td><td></td><td>Ihr Anmeldename für die Azure SQL-Datenbank.</td></tr>
	<tr><td>Anmeldekennwort der Azure SQL-Datenbank</td><td>$sqlDatabasePassword</td><td></td><td>Ihr Anmeldekennwort für die Azure SQL-Datenbank.</td></tr>
	<tr><td>Azure SQL-Datenbankname</td><td>$sqlDatabaseName</td><td></td><td>Die Azure SQL-Datenbank, auf den Sqoop Daten exportiert oder von dem Sqoop Daten importiert. </td></tr>
	</table>
	> [AZURE.NOTE]Eine Azure SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn die Firewall-Einstellung deaktiviert ist, müssen Sie sie im Azure-Portal aktivieren. Anweisungen zum Erstellen einer Azure SQL-Datenbank und zum Konfigurieren von Firewall-Regeln finden Sie unter [Erstellen und Konfigurieren einer SQL-Datenbank][sqldatabase-create-configue].

* **SQL Server**: Falls sich Ihr HDInsight-Cluster im gleichen virtuellen Netzwerk in Azure wie ein SQL Server befindet, können Sie mit den hier beschriebenen Schritten Daten in einer SQL Server-Datenbank importieren und exportieren.

	> [AZURE.NOTE]HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.

	* Weitere Informationen zur Erstellung und Konfiguration von virtuellen Netzwerken finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](http://msdn.microsoft.com/library/azure/jj156206.aspx).

		* Wenn Sie SQL Server in Ihrem Rechenzentrum verwenden, müssen Sie das virtuelle Netzwerk entweder als *Standort-zu-Standort* oder als *Punkt-zu-Standort* konfigurieren.

			> [AZURE.NOTE]Für virtuelle Netzwerke im **Punkt-zu-Standort-Modus** muss die Anwendung zum Konfigurieren des VPN-Clients auf dem SQL Server ausgeführt werden. Diese Anwendung ist im **Dashboard** der Konfiguration Ihres virtuellen Azure-Netzwerks verfügbar.

		* Wenn Sie SQL Server auf einem virtuellen Azure-Computer verwenden, können Sie eine beliebige Konfiguration für das virtuelle Netzwerk nehmen, sofern sich der virtuelle Computer, auf dem SQL Server läuft, im gleichen virtuellen Netzwerk befindet wie HDInsight.

	* Hinweise zum Bereitstellen eines HDInsight-Clusters in einem virtuellen Netzwerk finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight](hdinsight-provision-clusters.md)

	> [AZURE.NOTE]Der SQL Server muss eine Authentifizierung zulassen. Sie benötigen eine SQL-Serveranmeldung, um die Schritte in diesem Artikel abzuschließen.

	<table border="1">
	<tr><th>SQL Server-Datenbankeigenschaft</th><th>Azure PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>SQL Server-Name</td><td>$sqlDatabaseServer</td><td></td><td>Der SQL Server, in dem Sqoop Daten importiert bzw. exportiert. </td></tr>
	<tr><td>SQL Server-Anmeldename</td><td>$sqlDatabaseLogin</td><td></td><td>Ihr Anmeldename für den SQL Server.</td></tr>
	<tr><td>Kennwort für die SQL-Serveranmeldung</td><td>$sqlDatabasePassword</td><td></td><td>Ihr Anmeldekennwort für den SQL Server.</td></tr>
	<tr><td>SQL Server-Datenbankname</td><td>$sqlDatabaseName</td><td></td><td>Die SQL-Serverdatenbank, auf die Sqoop Daten exportiert oder von der Sqoop Daten importiert. </td></tr>
	</table>


> [AZURE.NOTE]Eingeben der Werte in die vorherigen Tabellen. Dies wird Ihnen helfen, wenn Sie dieses Lernprogramm durcharbeiten.

##Das Szenario
Ein HDInsight-Cluster wird mit einigen Beispieldaten geliefert. Sie verwenden die folgenden zwei Beispiele:

- Eine Protokolldatei namens log4j, die sich unter */example/data/sample.log* befindet. Die folgenden Protokolle werden aus der Datei extrahiert:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Eine Hive-Tabelle namens *hivesampletable*, die auf die Datendatei unter */hive/warehouse/hivesampletable* verweist. Die Tabelle enthält einige Mobilgerätedaten. Die Hive-Tabelle hat folgendes Schema:

	<table border="1">
	<tr><th>Feld</th><th>Datentyp</th></tr>
	<tr><td>clientid</td><td>string</td></tr>
	<tr><td>querytime</td><td>string</td></tr>
	<tr><td>market</td><td>string</td></tr>
	<tr><td>deviceplatform</td><td>string</td></tr>
	<tr><td>devicemake</td><td>string</td></tr>
	<tr><td>devicemodel</td><td>string</td></tr>
	<tr><td>state</td><td>string</td></tr>
	<tr><td>country</td><td>string</td></tr>
	<tr><td>querydwelltime</td><td>double</td></tr>
	<tr><td>sessionid</td><td>bigint</td></tr>
	<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
	</table>

Zunächst exportieren Sie *sample.log* und *hivesampletable* in die Azure SQL-Datenbank bzw. den SQL Server. Anschließend importieren Sie die Tabelle mit den Mobilgerätedaten über den folgenden Pfad zurück in HDInsight:

	/tutorials/usesqoop/importeddata

###Grundlagen der HDInsight-Speicherung

HDInsight verwendet zur Datenspeicherung Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].

Während des Prozesses zur Bereitstellung eines HDInsight-Clusters werden wie in HDFS ein Azure-Speicherkonto und ein bestimmter Blob-Speichercontainer aus diesem Konto als Standard-Dateisystem festgelegt. Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder aus anderen Azure-Abonnements hinzufügen.

Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. Um das in diesem Lernprogramm verwendete Windows PowerShell-Skript zu vereinfachen, werden alle Dateien im Container des Standarddateisystems gespeichert, der sich unter */tutorials/usesqoop* befindet. Dieser Container hat standardmäßig denselben Namen wie der HDInsight-Cluster. Die Syntax ist:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]In der HDInsight-Clusterversion 3.0 wird nur die *wasb://*-Syntax unterstützt. Die ältere *asv://*-Syntax wird in HDInsight 2.1- und 1.6-Clustern unterstützt, nicht aber in HDInsight 3.0-Clustern.

> [AZURE.NOTE]Der *wasb://*-Pfad ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight][hdinsight-storage].

Auf eine im Standarddateisystem-Blob gespeicherte Datei kann in HDInsight über jeden der folgenden URIs zugegriffen werden (in diesem Beispiel mit der Datei sample.log):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

	example/data/sample.log


##Vorbereiten des Lernprogramms

Sie werden zwei Tabellen in der Azure SQL-Datenbank bzw. im SQL Server erstellen. Diese werden im weiteren Verlauf dieses Lernprogramms vom Sqoop-Export verwendet. Sie müssen außerdem die sample.log-Dateien bearbeiten, bevor sie von Sqoop bearbeitet werden können.

###Erstellen von SQL-Tabellen

**Für eine Azure SQL-Datenbank**

1. Öffnen Sie die Windows PowerShell ISE (geben Sie auf dem Startbildschirm von Windows 8 **PowerShell_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**). Siehe [Start Windows PowerShell on Windows 8 and Windows][powershell-start] (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).

2. Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten vier Variablen ein.

		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

3. Fügen Sie das folgende Skript in den Skriptbereich ein. Dies sind die SQL-Anweisungen, die die beiden Tabellen und ihre gruppierten Indizes definieren. Die Azure SQL-Datenbank erfordert einen gruppierten Index.

		# SQL query strings for creating tables and clustered indexes
		$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		    [t1] [nvarchar](50),
		    [t2] [nvarchar](50),
		    [t3] [nvarchar](50),
		    [t4] [nvarchar](50),
		    [t5] [nvarchar](50),
		    [t6] [nvarchar](50),
		    [t7] [nvarchar](50))"

		$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

		$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
		[clientid] [nvarchar](50),
		[querytime] [nvarchar](50),
		[market] [nvarchar](50),
		[deviceplatform] [nvarchar](50),
		[devicemake] [nvarchar](50),
		[devicemodel] [nvarchar](50),
		[state] [nvarchar](50),
		[country] [nvarchar](50),
		[querydwelltime] [float],
		[sessionid] [bigint],
		[sessionpagevieworder][bigint])"

		$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

4. Fügen Sie das folgende Skript in den Skriptbereich ein, um die SQL-Befehle auszuführen:

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()

		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()

		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()

		Write-Host "Close connection ..." -ForegroundColor Green
		$conn.close()

		Write-Host "Done" -ForegroundColor Green

5. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6. Untersuchen Sie die Tabellen und die gruppierten Indizes mithilfe des [Azure-Portals][azure-management-portal].

**Für SQL Server**

1. Öffnen Sie **SQL Server Management Studio** und verbinden Sie sich mit dem SQL Server.

2. Erstellen Sie eine neue Datenbank mit dem Namen **sqoopdb**.

3. Wählen Sie die Datenbank **sqoopdb** aus und klicken Sie in der Multifunktionsleiste oben im SQL Server Management Studio auf **Neue Abfrage**.

4. Geben Sie die folgenden Informationen in das Abfragefenster ein.

		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50),
		 [t2] [nvarchar](50),
		 [t3] [nvarchar](50),
		 [t4] [nvarchar](50),
		 [t5] [nvarchar](50),
		 [t6] [nvarchar](50),
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50),
		 [querytime] [nvarchar](50),
		 [market] [nvarchar](50),
		 [deviceplatform] [nvarchar](50),
		 [devicemake] [nvarchar](50),
		 [devicemodel] [nvarchar](50),
		 [state] [nvarchar](50),
		 [country] [nvarchar](50),
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

5. Klicken Sie auf **F5** oder wählen Sie **! Ausführen** in der Multifunktionsleiste, um die Abfrage auszuführen. Die folgende Nachricht sollte unterhalb der Abfrage ausgegeben werden:

		Command(s) completed successfully.

6. Schließen Sie SQL Server Management Studio.

###Generieren von Daten

In diesem Lernprogramm exportieren Sie die Protokolldatei log4j (eine getrennte Datei) und eine Hive-Tabelle in eine Azure SQL-Datenbank. Der Name der getrennten Datei lautet */example/data/sample.log*. In diesem Lernprogramm haben wir Ihnen bereits einige Beispiele für log4j-Protokolle gezeigt. In der Protokolldatei existieren einige Leerzeilen und einige Zeilen, die ungefähr wie folgt aussehen:

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Dies ist kein Problem für andere Beispiele, die diese Daten verwenden. Wir müssen die Ausnahmen jedoch beheben, bevor wir die Daten in die Azure SQL-Datenbank oder in SQL Server importieren können. Der Sqoop-Export schlägt fehl, wenn es eine leere Zeichenfolge oder eine Zeile mit weniger Elementen gibt, als in der Azure SQL-Datenbanktabelle Felder definiert sind. Die log4jlogs hat 7 Felder mit Zeichenfolgen.

**Verarbeitung der Datei sample.log**

1. Öffnen Sie die Windows PowerShell ISE.
2. Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

		Add-AzureAccount

	Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Diese Methode zum Hinzufügen einer Abonnementverbindung ist zeitlich begrenzt: Nach 12 Stunden müssen Sie sich erneut anmelden.

	> [AZURE.NOTE]Wenn Sie über mehrere Azure-Abonnements verfügen und nicht das Standard-Abonnement verwenden möchten, wählen Sie das aktuelle Abonnement mithilfe des Cmdlets <strong>Select-AzureSubscription</strong> aus.

3. Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten zwei Variablen ein.

		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

4. Fügen Sie das folgende Skript in den Skriptbereich ein:

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)

		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream

		# process the source blob
		$exString = "java.lang.Exception:"
		while(-Not $sReader.EndOfStream){
		    $line = $sReader.ReadLine()
		    $split = $line.Split(" ")

		    # remove the "java.lang.Exception" from the first element of the array
		    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		    if ($split[0] -eq $exString){
		        #create a new ArrayList to remove $split[0]
		        $newArray = [System.Collections.ArrayList] $split
		        $newArray.Remove($exString)

		        # update $split and $line
		        $split = $newArray
		        $line = $newArray -join(" ")
		    }

		    # remove the lines that has less than 7 elements
		    if ($split.count -ge 7){
		        write-host $line
		        $writeStream.WriteLine($line)
		    }
		}

		# Write to the destination blob
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream)

5. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6. Zur Untersuchung der geänderten Datendatei können Sie das Azure-Portal, ein Azure-Speicher-Explorer-Tool oder Azure Powershell verwenden. In [Erste Schritte mit HDInsight][hdinsight-get-started] finden Sie einen Beispielcode für die Verwendung von Azure PowerShell zum Herunterladen einer Datei und zum Anzeigen von deren Inhalt.


##Verwenden von PowerShell zur Ausführung von Sqoop-Exporten

In diesem Abschnitt verwenden Sie Azure PowerShell zur Ausführung des Sqoop-Exportbefehls, um eine Hive-Tabelle und eine Datendatei in eine Azure SQL-Datenbank oder SQL Server zu exportieren. Im nächsten Abschnitt stehen Sie ein HDInsight .NET-Beispiel.

> [AZURE.NOTE]Mit Ausnahme der Verbindungszeichenfolgen sollten die Schritte in diesem Abschnitt sowohl für Azure SQL-Datenbanken als auch für SQL Server funktionieren. Diese Schritte wurden mithilfe der folgenden Konfiguration getestet:
>
> * **Virtuelles Azure-Netzwerk mit Punkt-zu-Standort-Konfiguration**: Ein virtuelles Netzwerk verbindet das HDInsight-Cluster mit einem SQL Server in einem privaten Rechenzentrum. Weitere Informationen finden Sie unter [Konfigurieren eines Punkt-zu-Standort-VPN im Verwaltungsportal](http://msdn.microsoft.com/library/azure/dn133792.aspx).
> * **Azure HDInsight 3.1**: Siehe [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight](hdinsight-provision-clusters.md) für weitere Informationen zur Erstellung eines Clusters in einem virtuellen Netzwerk.
> * **SQL Server 2014**: Konfiguriert für die Authentifizierung und mit dem Konfigurationspaket für VPN-Clients für eine sichere Verbindung zum virtuellen Netzwerk.

**So exportieren Sie die Protokolldatei log4j**

1. Öffnen Sie die Windows PowerShell ISE.
2. Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

		Add-AzureAccount

	Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3. Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten sieben Variablen ein.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_log4j = "/tutorials/usesqoop/data"

	Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

	Bitte beachten Sie, dass in $exportDir_log4j der Name der Datei sample.log nicht angegeben ist. Sqoop exportiert die Daten aus allen Dateien in diesem Ordner.

4. Fügen Sie das folgende Skript in den Skriptbereich ein:

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	Bitte beachten Sie, dass das Trennzeichen **\0x20** (Leerzeichen) ist. Das Trennzeichen ist im Azure PowerShell-Skript in der Datei sample.log definiert. Weitere Informationen über **-m 1** finden Sie in der [Sqoop-Benutzeranleitung][sqoop-user-guide-1.4.4].

5. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6. Untersuchen Sie die exportierten Daten im [Azure-Portal][azure-management-portal].

**Exportieren der Hive-Tabelle hivesampletable**

1. Öffnen Sie die Windows PowerShell ISE.
2. Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

		Add-AzureAccount

	Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3. Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten sieben Variablen ein.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_mobile = "/hive/warehouse/hivesampletable"

	Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

4. Fügen Sie das folgende Skript in den Skriptbereich ein:

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"


		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6. Untersuchen Sie die exportierten Daten im [Azure-Portal][azure-management-portal].



##Verwenden des HDInsight .NET SDKs zur Ausführung von Sqoop-Exporten

Im Folgenden sehen Sie ein C#-Beispiel, das das HDInsight .NET SDK zur Ausführung eines Sqoop-Exports verwendet. Allgemeine Informationen zur Verwendung des HDInsight .NET SDKs finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Jobs][hdinsight-submit-jobs].


	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;

	namespace sqoopSDKSample
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Set the variables
	            string subscriptionID = "<AzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<AzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>";
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";

	            cmdExport = @"export";
				// Connection string for using Azure SQL Database.
				// Comment if using SQL Server
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
				// Connection string for using SQL Server.
				// Uncomment if using SQL Server
				//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";

	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
	            {
	                Command = cmdExport,
	                StatusFolder = "/tutorials/usesqoop/jobStatus"
	            };

	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);

	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);

	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);

	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());

	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }

	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

Zur Ausführung einer Skriptdatei können Sie folgende Ersetzung durchführen:

	Command = cmdExport,

 durch:

	File = "/tutorials/usesqoop/sqoopexport.txt",

Die Skriptdatei muss im Azure-Blobspeicher lokalisiert werden.




##Verwenden von Azure PowerShell zur Ausführung des Sqoop-Imports

In diesem Abschnitt importieren Sie die log4j-Protokolle (die Sie in die Azure SQL-Datenbank exportiert haben) zurück in HDInsight.

1. Öffnen Sie die Windows PowerShell ISE.
2. Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

		Add-AzureAccount

	Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3. Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten sieben Variablen ein.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

	Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

4. Fügen Sie das folgende Skript in den Skriptbereich ein:

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6. Zur Untersuchung der geänderten Datendatei können Sie das Azure-Portal, ein Azure-Speicher-Explorer-Tool oder Azure Powershell verwenden. In [Erste Schritte mit HDInsight][hdinsight-get-started] finden Sie einen Beispielcode für die Verwendung von Azure PowerShell zum Herunterladen einer Datei und zum Anzeigen von deren Inhalt.

##Nächste Schritte

Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

- [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]: Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
- [Analysieren von Daten zu Flugverspätungen mit HDInsight][hdinsight-analyze-flight-data]: Verwenden von Hive zur Analyse von Daten zu Flugverspätungen und Verwenden von Sqoop zum Exportieren von Daten in die SQL-Datenbank.
- [Hochladen von Daten in HDInsight][hdinsight-upload-data]: Andere Methoden zum Hochladen von Daten in HDInsight/Azure Blob-Speicher.




[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
 

<!---HONumber=62-->