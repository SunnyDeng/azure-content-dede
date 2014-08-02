<properties linkid="manage-services-hdinsight-use-sqoop" urlDisplayName="Use Sqoop with HDInsight Samples" pageTitle="Use Sqoop with HDInsight | Azure" metaKeywords="" description="Learn how to use Azure PowerShell from a workstation to run Sqoop import and export between an HDInsight cluster and an Azure SQL database." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Use Sqoop with HDInsight" authors="jgao" />

Verwenden von Sqoop mit HDInsight
=================================

Erfahren Sie, wie Sie Azure PowerShell und HDInsight .NET SDK auf einer Arbeitsstation verwenden können, um Sqoop-Importe und -Exporte zwischen einem HDInsight-Cluster und einer Azure SQL-Datenbank auszuführen.

**Geschätzter Zeitaufwand:** 30 Minuten

Themen in diesem Artikel
------------------------

-   [Was ist Sqoop?](#whatissqoop)
-   [Voraussetzungen](#prerequisites)
-   [Grundlagen zum Lernprogramm-Szenario](#scenario)
-   [Vorbereiten des Lernprogramms](#prepare)
-   [Verwenden von PowerShell zur Ausführung von Sqoop-Exporten](#export)
-   [Verwenden des HDInsight SDKs zur Ausführung von Sqoop-Exporten](#export-sdk)
-   [Verwenden von PowerShell zur Ausführung von Sqoop-Importen](#import)
-   [Nächste Schritte](#nextsteps)

Was ist Sqoop?
--------------

Obwohl Hadoop die beste Wahl für die Verarbeitung von unstrukturierten und halbstrukturierten Daten wie z. B. Protokollen und Dateien ist, besteht oft auch Bedarf für die Verarbeitung strukturierter Dateien in relationalen Datenbanken.

[Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) ist ein Tool zum Übertragen von Daten zwischen Hadoop-Clustern und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL, MySQL oder Oracle in das Hadoop Distributed File System (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und sie anschließend wieder in ein RDBMS exportieren. In diesem Beispiel verwenden Sie eine SQL-Datenbank als relationale Datenbank.

Informationen zu den unterstützten Sqoop-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](../hdinsight-component-versioning/).

Voraussetzungen
---------------

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell). Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Ausführen von Windows PowerShell-Skripts](http://technet.microsoft.com/en-us/library/ee176949.aspx).

-   **Ein Azure HDInsight-Cluster**. Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HDInsight](../hdinsight-get-started/) oder unter [Bereitstellen von HDInsight-Clustern](../hdinsight-provision-clusters/). Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

	<table border="1">
	<tr><th>Clustereigenschaft</th><th>PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>HDInsight-Clustername</td><td>$clusterName</td><td></td><td>Dies ist der Name Ihres HDInsight-Clusters.</td></tr>
	<tr><td>Azure-Speicherkontoname</td><td>$storageAccountName</td><td></td><td>Ein im HDInsight-Cluster verfügbares Azure-Speicherkonto. Verwenden Sie für dieses Lernprogramm das Standard-Speicherkonto, das Sie während der Bereitstellung des Clusters angegeben haben.</td></tr>
	<tr><td>Azure-Blob-Containername</td><td>$containerName</td><td></td><td>Verwenden Sie in diesem Beispiel den Azure-Blob-Speichercontainer, der für das Standarddateisystem des HDInsight-Clusters verwendet wird. Standardmäßig hat dieser denselben Namen wie der HDInsight-Cluster.</td></tr>
	</table>

-   **Eine Azure SQL-Datenbank**. Sie müssen eine Firewall-Regel für den SQL-Datenbankserver konfigurieren, um Zugriff auf Ihre Arbeitsstation zu erlauben. Anweisungen zur Erstellung einer SQL-Datenbank und zur Konfiguration einer Firewall erhalten Sie unter [Erste Schritte mit Azure SQL-Datenbanken](../sql-database-get-started/). Dieser Artikel beschreibt ein PowerShell-Skript zur Erstellung der SQL-Datenbanktabelle, die für dieses Lernprogramm benötigt wird.

	<table border="1">
	<tr><th>SQL-Datenbankeigenschaft</th><th>PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>Servername der SQL-Datenbank</td><td>$sqlDatabaseServer</td><td></td><td>TDer SQL Database-Server, auf den Sqoop Daten exportiert oder von dem Sqoop Daten importiert. </td></tr>
	<tr><td>Anmeldename der SQL-Datenbank</td><td>$sqlDatabaseLogin</td><td></td><td>Anmeldename der SQL-Datenbank.</td></tr>
	<tr><td>Anmeldekennwort der SQL-Datenbank</td><td>$sqlDatabasePassword</td><td></td><td>Anmeldekennwort der SQL-Datenbank.</td></tr>
	<tr><td>SQL-Datenbankname</td><td>$sqlDatabaseName</td><td></td><td>Der Azure SQL Database-Server, auf den Sqoop Daten exportiert oder von dem Sqoop Daten importiert. </td></tr>
	</table>

    > [WACOM.NOTE] Eine Azure SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn die Firewall-Einstellung deaktiviert ist, müssen Sie sie im Azure-Verwaltungsportal aktivieren. Anweisungen zum Erstellen einer SQL-Datenbank und zum Konfigurieren von Firewall-Regeln finden Sie unter [Erstellen und Konfigurieren einer SQL-Datenbank](../sql-database-create-configure/).

> [WACOM.NOTE] Tragen Sie die Werte in die Tabellen ein. Dies wird Ihnen helfen, wenn Sie dieses Lernprogramm durcharbeiten.

Das Szenario
------------

Der HDInsight-Cluster wird mit einigen Beispieldaten geliefert. Sie verwenden die folgenden zwei:

-   Eine Protokolldatei namens log4j, die sich unter */example/data/sample.log* befindet. Die folgenden Protokolle werden aus der Datei extrahiert:

          2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
          2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
          2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
          ...

-   Eine Hive-Tabelle namens *hivesampletable*, die auf die Datendatei unter */hive/warehouse/hivesampletable* verweist. Die Tabelle enthält einige Mobilgerätedaten. Die Hive-Tabelle hat folgendes Schema:

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

Zunächst exportieren Sie sample.log und hivesampletable in die SQL-Datenbank. Anschließend importieren Sie die Tabelle mit den Mobilgerätedaten über den folgenden Pfad zurück in HDInsight:

    /tutorials/usesqoop/importeddata

### Grundlagen der HDInsight-Speicherung

HDInsight verwendet Azure Blob-Speicher zur Datenspeicherung. Der Speicher wird *WASB* oder *Azure Storage-Blob* genannt. WASB ist Microsofts Implementierung von HDFS in Azure-Blobspeicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage/).

Während des Prozesses zur Bereitstellung eines HDInsight-Clusters werden genau wie in HDFS ein Azure-Speicherkonto und ein bestimmter Blob-Speichercontainer aus diesem Konto als Standard-Dateisystem festgelegt. Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern](../hdinsight-provision-clusters/). Um das in diesem Lernprogramm verwendete PowerShell-Skript zu vereinfachen, werden alle Dateien im Container des Standarddateisystems gespeichert. Dieser befindet sich unter */tutorials/usesqoop*. Dieser Container hat standardmäßig denselben Namen wie der HDInsight-Cluster. Die WASB-Syntax lautet:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] In der HDInsight-Clusterversion 3.0 wird nur die *wasb://*-Syntax unterstützt. Die ältere *asv://*-Syntax wird in HDInsight-Clustern der Versionen 2.1 und 1.6 unterstützt, aber nicht in den Clusterversionen 3.0 und höher.

> [WACOM.NOTE] Der WASB-Pfad ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage/).

Auf eine im Standarddateisystem-Container gespeicherte Datei kann in HDInsight über jeden der folgenden URIs zugegriffen werden (in diesem Beispiel mit der sample.log):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

    example/data/sample.log

Vorbereiten des Lernprogramms
-----------------------------

Sie erstellen zwei SQL-Datenbanktabellen, die später in diesem Lernprogramm für den Scoop-Export verwendet werden. Sie müssen außerdem die Datei sample.log bearbeiten, bevor sie von Sqoop bearbeitet werden kann.

**Erstellen einer SQL-Datenbanktabelle**

1.  Öffnen Sie Windows PowerShell ISE. (Geben Sie auf dem Startbildschirm von Windows 8 **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**.) Siehe [Start Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx) (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).

2.  Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten vier Variablen ein.

         # SQL-Datenbankvariablen
         $sqlDatabaseServer = "<SQLDatabaseServerName>" 
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
         $sqlDatabaseName = "<SQLDatabaseName>" 

         $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

3.  Fügen Sie das folgende Skript in den Skriptbereich ein. Dies sind die SQL-Anweisungen, die die beiden Tabellen und ihre gruppierten Indizes definieren. Die SQL-Datenbank erfordert einen gruppierten Index.

         # SQL-Abfragezeichenfolgen zur Erstellung von Tabellen und gruppierten Indizes
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

4.  Fügen Sie das folgende Skript in den Skriptbereich ein, um die SQL-Befehle auszuführen:

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

5.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6.  Untersuchen Sie die Tabellen und die gruppierten Indizes mithilfe des [Azure-Verwaltungsportals](https://manage.windowsazure.com/).

In diesem Lernprogramm exportieren Sie die Protokolldatei log4j (eine getrennte Datei) und eine Hive-Tabelle in SQL Database. Der Name der getrennten Datei lautet */example/data/sample.log*. In diesem Lernprogramm haben wir Ihnen bereits einige Bespiele für log4j-Protokolle gezeigt. In der Protokolldatei existieren einige Leerzeilen und einige Zeilen, die ungefähr wie folgt aussehen:

    java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
        at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Sie können *java.lang.Exception:* aus der Zeile entfernen, damit der Datensatz korrekt analysiert wird.

Der Sqoop-Export schlägt fehl, wenn es eine leere Zeichenfolge oder eine Zeile mit weniger Elementen gibt, als in der SQL-Datenbanktabelle Felder definiert sind. Die log4jlogs hat 7 Felder mit Zeichenfolgen.

**Vorverarbeitung der Datei sample.log**

1.  Öffnen Sie Windows PowerShell ISE.
2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Diese Methode zum Hinzufügen einer Abonnementverbindung ist zeitlich begrenzt: Nach 12 Stunden müssen Sie sich erneut anmelden.

    > [WACOM.NOTE] Wenn Sie über mehrere Azure-Abonnements verfügen und nicht das Standard-Abonnement verwenden möchten, wählen Sie das aktuelle Abonnement mithilfe des Cmdlets **Select-AzureSubscription** aus.

3.  Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten zwei Variablen ein.

         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<BlobContainerName>"
            
         $sourceBlobName = "example/data/sample.log"
         $destBlobName = "tutorials/usesqoop/data/sample.log"

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

4.  Fügen Sie das folgende Skript in den Skriptbereich ein:

         # Verbindungszeichenfolge festlegen
         $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
            
         # Block-Blob-Objekte erstellen, die auf den Quell- und den Ziel-Blob verweisen.
         $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
         $storageClient = $storageAccount.CreateCloudBlobClient();
         $storageContainer = $storageClient.GetContainerReference($containerName)
         $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
         $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
            
         # Einen MemoryStream und einen StreamReader zum Lesen in der Quelldatei definieren
         $stream = New-Object System.IO.MemoryStream
         $stream = $sourceBlob.OpenRead()
         $sReader = New-Object System.IO.StreamReader($stream)
            
         # Einen MemoryStream und einen StreamWriter zum Schreiben in die Zieldatei definieren
         $memStream = New-Object System.IO.MemoryStream
         $writeStream = New-Object System.IO.StreamWriter $memStream
            
         # Den Quell-Blob verarbeiten
         $exString = "java.lang.Exception:"
         while(-Not $sReader.EndOfStream){
             $line = $sReader.ReadLine()
             $split = $line.Split(" ")
            
             # Die "java.lang.Exception" aus dem ersten Element des Arrays entfernen
             # Beispiel: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
             if ($split[0] -eq $exString){
                 #create a new ArrayList to remove $split[0]
                 $newArray = [System.Collections.ArrayList] $split
                 $newArray.Remove($exString)
            
                 # $split und $line aktualisieren
                 $split = $newArray
                 $line = $newArray -join(" ")
             }
            
             # Zeilen mit weniger als 7 Elementen entfernen
             if ($split.count -ge 7){
                 write-host $line
                 $writeStream.WriteLine($line)
             }
         }
            
         # In den Ziel-Blob schreiben
         $writeStream.Flush()
         $memStream.Seek(0, "Begin")
         $destBlob.UploadFromStream($memStream)

5.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6.  Zur Untersuchung der geänderten Datendatei können Sie das Azure-Verwaltungsportal, ein Azure-Speicher-Explorer-Tool oder Azure Powershell verwenden. In [Erste Schritte mit HDInsight](../hdinsight-get-started/) finden Sie einen Beispielcode für die Verwendung von PowerShell zum Herunterladen einer Datei und zum Anzeigen von deren Inhalt.

Verwenden von PowerShell zur Ausführung von Sqoop-Exporten
----------------------------------------------------------

In diesem Abschnitt verwenden Sie Azure PowerShell zur Ausführung des Sqoop-Exportbefehls, um eine Hive-Tabelle und eine Datendatei in eine Azure SQL-Datenbank zu exportieren. Im nächsten Abschnitt stehen Sie ein HDInsight .NET-Beispiel.

**Exportieren der Protokolldatei log4j**

1.  Öffnen Sie Windows PowerShell ISE.
2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten sieben Variablen ein.

         # Die Clustervariablen definieren
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Die SQL-Datenbankvariablen definieren
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "<SQLDatabaseName>"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_log4j = "/tutorials/usesqoop/data"

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

    Bitte beachten Sie, dass in \$exportDir\_log4j der Name der Datei sample.log nicht angegeben ist. Sqoop exportiert die Daten aus allen Dateien in diesem Ordner.

4.  Fügen Sie das folgende Skript in den Skriptbereich ein:

         # Einen Sqoop-Job übermitteln
         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by  # Einen Sqoop-Job übermitteln
         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput
        x20 -m 1"
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

    Bitte beachten Sie, dass das Trennzeichen **\\0x20** (Leerzeichen) ist. Das Trennzeichen ist im PowerShell-Skript zur Vorverarbeitung der Datei sample.log definiert. Weitere Informationen über **-m 1** finden Sie in der [Sqoop-Benutzeranleitung](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html).

5.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6.  Untersuchen Sie die exportierten Daten im [Azure-Verwaltungsportal](https://manage.windowsazure.com/).

**Exportieren der Hive-Tabelle hivesampletable**

1.  Öffnen Sie Windows PowerShell ISE.
2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten sieben Variablen ein.

         # Die Clustervariablen definieren
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Die SQL-Datenbankvariablen definieren
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_mobile = "mobiledata"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_mobile = "/hive/warehouse/hivesampletable"

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

4.  Fügen Sie das folgende Skript in den Skriptbereich ein:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
            
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6.  Untersuchen Sie die exportierten Daten im [Azure-Verwaltungsportal](https://manage.windowsazure.com/).

Verwenden des HDInsight .NET SDKs zur Ausführung von Sqoop-Exporten
-------------------------------------------------------------------

Im Folgenden sehen Sie ein C\#-Beispiel für die Verwendung des HDInsight .NET SDKs zur Ausführung eines Sqoop-Exports. Allgemeine Informationen zur Verwendung des HDInsight .NET SDKs finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Jobs](../hdinsight-submit-hadoop-jobs-programmatically/).

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
                // Die Variablen festlegen
                string subscriptionID = "<WindowsAzureSubscriptionID>";
                string clusterName = "<HDInsightClusterName>";
                string certFriendlyName = "<WindowsAzureCertificateFriendlyName>";
                string sqlDatabaseServerName = "<SQLDatabaseServerName>";
                string sqlDatabaseLogin = "<SQLDatabaseLogin>" + "@" + sqlDatabaseServerName;
                string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                string sqlDatabaseDatabaseName = "hdisqoop";
                string sqlDatabaseTableName = "log4jlogs";

                cmdExport = @"export";
                cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
                cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
                cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";

                SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
                {
                    Command = cmdExport,
                    StatusFolder = "/tutorials/usesqoop/jobStatus"
                };

                // Das Zertifikatobjekt mit dem Anzeigenamen zur Identifikation aus dem Zertifikatspeicher abrufen
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Den Hive-Job übermitteln
                var jobClient = JobSubmissionClientFactory.Connect(creds);
                JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);

                // Auf Abschluss des Jobs warten
                WaitForJobCompletion(jobResults, jobClient);

                // Die Ausgabe des Hive-Jobs drucken
                System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);

                StreamReader reader = new StreamReader(stream);
                Console.WriteLine(reader.ReadToEnd());

                Console.WriteLine("Eingabetaste drücken, um fortzufahren.");
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

Die Skriptdatei muss sich im WASB befinden.

Verwenden von PowerShell zur Ausführung von Sqoop-Importen
----------------------------------------------------------

In diesem Abschnitt importieren Sie die log4j-Protokolle (die Sie in die SQL-Datenbank exportiert haben) zurück in HDInsight.

1.  Öffnen Sie Windows PowerShell ISE.
2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Kopieren Sie das folgende Skript in den Skriptbereich, und stellen Sie dann die ersten sieben Variablen ein.

         # Die Clustervariablen definieren
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Die SQL-Datenbankvariablen definieren
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $tableName_mobile = "mobiledata"
         $targetDir_mobile = "/tutorials/usesqoop/importeddata/"

    Weitere Beschreibungen der Variablen finden Sie im Abschnitt [Voraussetzungen](#prerequisites) dieses Lernprogramms.

4.  Fügen Sie das folgende Skript in den Skriptbereich ein:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.
6.  Zur Untersuchung der geänderten Datendatei können Sie das Azure-Verwaltungsportal, ein Azure-Speicher-Explorer-Tool oder Azure Powershell verwenden. In [Erste Schritte mit HDInsight](../hdinsight-get-started/) finden Sie einen Beispielcode für die Verwendung von PowerShell zum Herunterladen einer Datei und zum Anzeigen von deren Inhalt.

Nächste Schritte
----------------

Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

-   [Verwenden von Oozie mit HDInsight](../hdinsight-use-oozie/): Anwenden von Sqoop-Aktionen im Oozie-Workflow.
-   [Analyze flight delay data using HDInsight (Analysieren von Daten zu Flugverspätungen mithilfe von HDInsight, in englischer Sprache)](../hdinsight-analyze-flight-delay-data/): Verwenden von Hive zur Analyse von Daten zu Flugverspätungen und Verwenden von Sqoop zum Exportieren von Daten in die SQL-Datenbank.
-   [Hochladen von Daten zu HDInsight](../hdinsight-upload-data/): Andere Methoden zum Hochladen von Daten in HDInsight/Azure-Blob-Speicher.

