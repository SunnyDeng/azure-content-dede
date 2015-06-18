<properties 
	pageTitle="Hochladen von Daten für Hadoop-Aufträge in HDInsight | Azure" 
	description="Erfahren Sie, wie Sie Daten in HDInsight mithilfe von Azure-Speicher-Explorer, Azure PowerShell, der Hadoop-Befehlszeile oder Sqoop hochladen und abrufen können." 
	services="storage, hdinsight" 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#Hochladen von Daten für Hadoop-Aufträge in HDInsight

Azure HDInsight stellt über Azure-Blob-Speicher ein Hadoop Distributed File System (HDFS) mit vollem Funktionsumfang zur Verfügung. Es ist als eine HDFS-Erweiterung konzipiert, die eine nahtlose Benutzererfahrung bietet. Der vollständige Satz von Komponenten im Hadoop-System kann direkt mit den verwalteten Daten verwendet werden. Azure-Blob-Speicher und HDFS sind unterschiedliche Dateisysteme, die jeweils für die Datenspeicherung und Berechnungen dieser Daten optimiert sind. Die Vorteile der Verwendung von Azure-Blob-Speicher werden unter [Verwenden von Azure-Blob-Speicher mit HDInsight][hdinsight-storage] beschrieben.

Azure HDInsight-Cluster werden i. d. R. bereitgestellt, um MapReduce-Aufträge auszuführen. Die Cluster werden gelöscht, sobald diese Aufträge abgeschlossen sind. Nachdem die Berechnungen erfolgt sind, wäre es zu teuer, diese Daten weiterhin in den HDFS-Clustern zu speichern. Der Azure-Blob-Speicher ist eine hoch skalierbare und verfügbare, kostengünstige und freigabefähige Speicheroption mit hoher Kapazität für Daten, die mit HDInsight verarbeitet werden sollen. Das Speichern von Daten in einem Blob sorgt dafür, dass die für Berechnungen verwendeten HDInsight-Cluster sicher freigegeben werden können, ohne Daten zu verlieren.

Auf den Azure-Blob-Speicher kann über [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], [Azure-Speicherclientbibliothek für .NET][azure-storage-client-library], [Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows][xplatcli] oder über Explorer-Tools zugegriffen werden. Hier sind einige der verfügbaren Tools:

* [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

##Voraussetzungen

Beachten Sie die folgenden Voraussetzungen, bevor Sie beginnen:

* Ein Azure HDInsight-Cluster. Anweisungen finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started] or [Bereitstellen eines HDInsight-Clusters][hdinsight-provision].


##<a id="azcopy"></a>Hochladen von Daten in Azure-Blob-Speicher##

AzCopy ist ein Befehlszeilenprogramm, das das Übertragen von Daten in ein Azure-Speicherkonto und von einem Azure-Speicherkonto vereinfachen soll. Sie können dieses Dienstprogramm als eigenständiges Tool verwenden oder in eine vorhandene Anwendung integrieren. [AzCopy herunterladen][azure-azcopy-download].

Die AzCopy-Syntax lautet folgendermaßen:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Weitere Informationen finden Sie unter [AzCopy – Hochladen/Herunterladen von Dateien für Azure-Blobs][azure-azcopy].

##<a id="powershell"></a>Hochladen von Daten zum Azure-Blob-Speicher mit Azure PowerShell##

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Mit Azure PowerShell können Sie Daten zum Azure-Blob-Speicher hochladen, sodass die Daten von MapReduce-Aufträgen verarbeitet werden können. Informationen zum Konfigurieren der Arbeitsstation für die Ausführung von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

**So laden Sie eine lokale Datei zum Azure-Blob-Speicher hoch**

1. Öffnen Sie die Azure PowerShell-Konsole entsprechend den Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).
2. Legen Sie die Werte der ersten fünf Variablen im folgenden Skript fest:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. Fügen Sie das Skript in die Azure PowerShell-Konsole ein, um es auszuführen.

In Azure-Blob-Speichercontainern werden Daten als Schlüssel-Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen das Zeichen '/' verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines Blobs kann z. B. *input/log1.txt* heißen. Das Verzeichnis 'input' existiert zwar nicht, wegen des Zeichens '/' im Schlüsselnamen sieht es jedoch so aus, als gäbe es einen Dateipfad. Im obigen Skript können Sie der Datei eine Ordnerstruktur geben, indem Sie die Variable **$blobname** festlegen, z. B. *$blobname="meinOrdner/meineDatei.txt"*.

In den Azure Explorer-Tools sehen Sie möglicherweise einige Dateien mit 0 Byte. Diese Dateien dienen zwei Zwecken:

- Bei leeren Ordnern markieren sie das Vorhandensein der Ordner. Bei der Azure-Blob-Speicherung wird erkannt, dass es bei Vorhandensein eines Blobs mit dem Namen "foo/bar" einen Ordner namens **foo** gibt. Soll es jedoch einen leeren Ordner namens **foo** geben, ist dies nur mit der speziellen 0-Byte-Datei möglich.
- Diese Dateien enthalten spezielle vom Hadoop-Dateisystem benötigte Metadaten, insbesondere die Berechtigungen und Eigentümer der Ordner.


##<a id="xplatcli"></a>Hochladen von Daten in Azure-Blob-Speicher mit der Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows ist ein plattformübergreifendes Tool zur Verwaltung von Azure-Diensten. Verwenden Sie die folgenden Schritte zum Hochladen von Daten in Azure-Blob-Speicher:

1. [Installieren und konfigurieren Sie die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows](../xplat-cli.md).

2. Öffnen Sie eine Eingabeaufforderung, Bash oder eine andere Shell, und authentifizieren Sie Ihr Azure-Abonnement wie folgt.

		azure login

	Geben Sie nach Aufforderung den Benutzernamen und das Kennwort für Ihr Abonnement ein.

3. Geben Sie den folgenden Befehl ein, um die Speicherkonten für Ihr Abonnement aufzulisten:

		azure storage account list

4. Wählen Sie das Speicherkonto mit dem zu verwendenden Blob aus, und rufen Sie mit dem folgenden Befehl den Schlüssel für dieses Konto ab:

		azure storage account keys list <storage-account-name>

	Damit sollten der **primäre** und der **sekundäre** Schlüssel zurückgegeben werden. Kopieren Sie den Wert des **primären** Schlüssels für die Verwendung in den nächsten Schritten.

5. Rufen Sie mit dem folgenden Befehl eine Liste der Blob-Container im Speicherkonto ab:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Verwenden Sie die folgenden Befehle zum Hoch- und Herunterladen von Dateien im Blob:

	* So laden Sie eine Datei hoch:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* So laden Sie eine Datei herunter:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Wenn Sie immer mit demselben Speicherkonto arbeiten, können die folgenden Umgebungsvariablen festlegen, anstatt das Konto und den Schlüssel für jeden Befehl anzugeben:
> 
> * **AZURE_STORAGE_ACCOUNT:** der Name des Speicherkontos
> 
> * **AZURE_STORAGE_ACCOUNT:** der Speicherkontoschlüssel

##<a id="storageexplorer"></a>Hochladen von Daten zum Blob-Speicher mit Azure-Speicher-Explorer

*Azure-Speicher-Explorer* ist ein hilfreiches Tool zum Prüfen und Ändern der Daten in Azure Storage. Dieses kostenlose Tool kann von CodePlex heruntergeladen werden: [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/ "Azure-Speicher-Explorer").

Bevor Sie das Tool verwenden können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen. Anleitungen zum Abrufen dieser Informationen finden Sie unter [Erstellen, Verwalten oder Löschen von Speicherkonten][azure-create-storage-account] im Abschnitt "Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln".

1. Führen Sie den Azure-Speicher-Explorer aus.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Klicken Sie auf **Konto hinzufügen**. Nachdem Sie Azure-Speicher-Explorer ein Konto hinzugefügt haben, müssen Sie diesen Schritt nicht noch einmal durchführen.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Geben Sie den **Speicherkontonamen** und den **Speicherkontoschlüssel** ein, und klicken Sie dann auf **Speicherkonto hinzufügen**. Sie können mehrere Speicherkonten hinzufügen. Jedes Konto wird auf einer Registerkarte angezeigt.
4. Klicken Sie unter **Speichertyp** auf **Blobs**.

	![HDI.ASEBlob][image-ase-blob]

5. Klicken Sie unter **Container** auf den Container, der Ihrem HDInsight-Cluster zugeordnet ist. Beim Erstellen eines HDInsight-Clusters müssen Sie einen Container angeben. Andernfalls wird der Container beim Erstellen des Clusters automatisch angelegt.
6. Klicken Sie unter **Blob** auf **Hochladen**.
7. Geben Sie eine hochzuladende Datei an, und klicken Sie auf **Öffnen**.








































































##<a id="commandline"></a>Hochladen von Daten zum Azure-Blob-Speicher mit der Hadoop-Befehlszeile

Bevor Sie die Hadoop-Befehlszeile verwenden können, müssen Sie über Remotedesktop eine Verbindung mit dem Cluster herstellen.



1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie auf **HDINSIGHT**. Sie sehen eine Liste bereitgestellter Hadoop-Cluster.
3. Klicken Sie auf den HDInsight-Cluster, zu dem Sie Daten hochladen möchten.
4. Klicken Sie oben auf der Seite auf **CONFIGURATION**.
5. Klicken Sie auf **ENABLE REMOTE**, wenn Sie Remotedesktop noch nicht aktiviert haben, und folgen Sie den Anweisungen. Gehen Sie andernfalls zum nächsten Schritt.
4. Klicken Sie unten auf der Seite auf **CONNECT**.
7. Klicken Sie auf **Öffnen**.
8. Geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **OK**.
9. Klicken Sie auf **Ja**.
10. Klicken Sie auf dem Desktop auf **Hadoop Command Line**.
12. Das folgende Codebeispiel zeigt, wie die Datei "davinci.txt" aus dem lokalen Dateisystem auf dem HDInsight-Hauptknoten in das Verzeichnis "/example/data" kopiert wird.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Da sich das Standarddateisystem im Azure-Blob-Speicher befindet, befindet sich "/example/data/davinci.txt" tatsächlich im Azure-Blob-Speicher. Sie können auch folgendermaßen auf die Datei verweisen:

		wasb:///example/data/davinci.txt 

	oder

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	Wenn Sie *wasbs* verwenden, müssen Sie den vollqualifizierten Domänennamen angeben.

13. Verwenden Sie den folgenden Befehl, um die hochgeladenen Dateien aufzulisten:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Importieren von Daten nach HDFS aus einer Azure-SQL-Datenbank oder SQL Server mithilfe von Sqoop

Sqoop ist ein Tool zum Übertragen von Daten zwischen Hadoop und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle in das verteilte Dateisystem von Hadoop (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und sie anschließend wieder in ein RDBMS exportieren. Weitere Informationen finden Sie unter [Sqoop-Benutzeranleitung][apache-sqoop-guide].

Bevor Sie Daten importieren, müssen Sie den Namen des Servers mit der Azure-SQL-Datenbank, den Namen des Datenbankkontos, das Kontokennwort und den Datenbanknamen kennen.

Eine Azure-SQL-Datenbank erlaubt standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn diese Firewall-Einstellung deaktiviert ist, müssen Sie sie im Azure-Portal aktivieren. Anweisungen zum Erstellen einer Azure-SQL-Datenbank und zum Konfigurieren von Firewallregeln finden Sie unter [Erstellen und Konfigurieren von SQL-Datenbanken][sqldatabase-create-configure].

In den folgenden Verfahren wird ein Sqoop-Auftrag mithilfe von Azure PowerShell übermittelt.

**So importieren Sie Daten mit Sqoop und Azure PowerShell nach HDInsight**

1. Öffnen Sie die Azure PowerShell-Konsole entsprechend den Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).
2. Legen Sie die Werte der ersten acht Variablen im folgenden Skript fest:

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. Fügen Sie das Skript in die Azure PowerShell-Konsole ein, um es auszuführen. Unter [Erste Schritte mit HDInsight][hdinsight-get-started] finden Sie ein Beispiel für das Abrufen der Datendatei aus dem Azure-Blob-Speicher.

Weitere Informationen zur Verwendung von Sqoop finden Sie unter [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop].

## Nächste Schritte
Jetzt wissen Sie, wie Sie Daten in HDInsight importieren. Lesen Sie in den folgenden Artikel, wie Sie die Daten analysieren:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight/hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight/hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight/hdinsight-use-pig.md
[hdinsight-provision]: hdinsight/hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database/sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[xplatcli]: ../xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54--> 