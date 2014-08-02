<properties  linkid="manage-services-hdinsight-howto-upload-data-to-hdinsight" urlDisplayName="Upload Data" pageTitle="Upload data to HDInsight | Azure" metaKeywords="" description="Learn how to upload and access data in HDInsight using Azure Storage Explorer, the interactive console, the Hadoop command line, or Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data to HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# Hochladen von Daten in HDInsight

Azure HDInsight stellt über Azure Blob-Speicher ein Hadoop Distributed File System (HDFS) mit vollem Funktionsumfang zur Verfügung. Es wurde als eine HDFS-Erweiterung entwickelt, die Kunden eine reibungslose Leistung bietet, indem alle Komponenten im Hadoop-System direkt mit den von ihm verwalteten Daten arbeiten kĂ¶nnen. Azure Blob-Speicher und HDFS sind unterschiedliche Dateisysteme, die jeweils für die Datenspeicherung und Berechnungen dieser Daten optimiert sind. Die Vorteile der Verwendung von Azure Blob-Speicher werden unter [Verwenden von Azure-Blob-Speicher mit HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/) erläutert.

Azure HDInsight-Clusters werden in der Regel bereitgestellt, um MapReduce-Jobs auszuführen, und werden gelöscht, sobald diese Jobs abgeschlossen sind. Nachdem die Berechnungen erfolgt sind, wäre es zu teuer, diese Daten weiterhin auf den HDFS-Clustern zu speichern. Der Azure-Blob-Speicher ist eine hoch skalierbare und verfügbare, kostengünstige und freigabefähige Speicheroption mit hoher Kapazität für Daten, die mit HDInsight verarbeitet werden sollen. Das Speichern von Daten in einem Blob sorgt dafür, dass die für Berechnungen verwendeten HDInsight-Cluster sicher freigegeben werden können, ohne Daten zu verlieren.

Auf den Azure Blob-Speicher kann über [AzCopy][1], [Azure PowerShell][2], [Azure-Speicherclientbibliothek für.NET](/en-us/develop/net/how-to-guides/blob-storage/) oder über Explorer-Tools zugegriffen werden. Hier sind einige der verfügbaren Tools:

* [Azure-Speicher-Explorer][3]
* [Cloud Storage Studio 2][4]
* [CloudXplorer][5]
* [Azure Explorer][6]
* [Azure Explorer PRO][7]

**Voraussetzungen**

Beachten Sie die folgenden Anforderungen, bevor Sie diesen Artikel lesen:

* Ein Azure-HDInsight-Cluster. Anweisungen hierzu finden Sie unter
  [Erste Schritte mit
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
  oder unter [Bereitstellen von
  HDInsight-Clustern](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

## Themen in diesem Artikel

* [Hochladen von Daten zum Blob-Speicher mit AzCopy](#azcopy)
* [Hochladen von Daten zum Blob-Speicher mit Azure
  PowerShell](#powershell)
* [Hochladen von Daten zum Blob-Speicher mit
  Azure-Speicher-Explorer](#storageexplorer)
* [Hochladen von Daten zum Blob-Speicher mit der Hadoop Command
  Line](#commandline)
* [Importieren von Daten aus der Azure-SQL-Datenbank in den
  Blob-Speicher mit Sqoop](#sqoop)

## <a id="azcopy" ></a>Hochladen von Daten zum Blob-Speicher mit AzCopy

AzCopy ist ein Befehlszeilenprogramm, das das Übertragen von Daten in ein Azure-Speicherkonto und von einem Azure-Speicherkonto vereinfachen soll. Sie können dieses Dienstprogramm als eigenständiges Tool verwenden oder in eine vorhandene Anwendung integrieren. [AzCopy herunterladen][8].

Die AzCopy-Syntax lautet folgendermaßen:

    AzCopy <Source> <Destination> [DateiMuster [DateiMuster...]] [Optionen]

Weitere Informationen finden Sie unter [AzCopy - Hochladen/Herunterladen von Dateien für Azure-Blobs][1].

## <a id="powershell" ></a>Hochladen von Daten zum Blob-Speicher mit Azure PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Mit Azure PowerShell können Sie Daten zum Blob-Speicher hochladen, sodass die Daten von MapReduce-Jobs verarbeitet werden können. Informationen zum Konfigurieren der Arbeitsstation für die Ausführung von Azure PowerShell finden Sie unter
[Installieren und Konfigurieren von Azure
PowerShell](/en-us/documentation/articles/install-configure-powershell/).

**So laden Sie eine lokale Datei zum Blob-Speicher hoch**

1.  Öffnen Sie das Azure PowerShell-Konsolenfenster entsprechend den
    Anweisungen unter [Installieren und Konfigurieren von Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).
2.  Legen Sie die Werte der ersten fünf Variablen im folgenden Skript
    fest:
    
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName = "<StorageAccountName>"
         $containerName = "<ContainerName>"
        	
         $fileName ="<LocalFileName>"
         $blobName = "<BlobName>"

         # Speicherkontoschlüssel abrufen
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}

         # Speicherkontextobjekt erstellen
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

         # Datei von der lokalen Arbeitsstation in den Blob-Container kopieren		
         Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3.  Fügen Sie das Skript in das Azure PowerShell-Konsolenfenster ein, um
    es auszuführen.

In Blob-Speichercontainern werden Daten als Schlüssel/Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen das Zeichen '/' verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines Blobs kann z. B. *input/log1.txt* heiĂźen. Das Verzeichnis 'input' existiert zwar nicht, wegen des Zeichens '/' im Schlüsselnamen sieht es jedoch so aus, als gäbe es einen Dateipfad. Im obigen Skript können Sie der Datei eine Ordnerstruktur geben, indem Sie die Variable $blobname festlegen. Beispiel:
*$blobname="myfolder/myfile.txt"*.

In den Azure Explorer-Tools sehen Sie möglicherweise einige Dateien mit 0 Byte. Diese Dateien dienen zwei Zwecken:

* Bei leeren Ordnern markieren sie das Vorhandensein der Ordner. Bei der
  Blob-Speicherung wird erkannt, dass es bei Vorhandensein eines Blobs
  mit dem Namen 'foo/bar' einen Ordner namens 'foo' gibt Soll es jedoch
  einen leeren Ordner namens 'foo' geben, kann dieser nur mit der
  speziellen 0-Byte-Datei bezeichnet werden.
* Diese Dateien enthalten einige spezielle vom Hadoop-Dateisystem
  benötigte Metadaten, insbesondere die Berechtigungen und Eigentümer
  der Ordner.

## <a id="storageexplorer" ></a>Hochladen von Daten zum Blob-Speicher mit Azure-Speicher-Explorer

*Azure-Speicher-Explorer* ist ein hilfreiches Tool zum Prüfen und Ändern
der Daten in Ihrem Azure-Speicher. Dieses kostenlose Tool kann von
[http://azurestorageexplorer.codeplex.com/][3] heruntergeladen werden.

Bevor Sie das Tool verwenden können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen. Anweisungen zum Abrufen dieser Informationen finden Sie im Abschnitt 'How to: View, copy and regenerate storage access keys' (Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln) unter [Verwalten von Speicherkonten](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

1.  Führen Sie den Azure-Speicher-Explorer aus.
    
    ![HDI.AzureStorageExplorer](./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png)

2.  Klicken Sie auf **Konto hinzufügen**. Nachdem Sie
    Azure-Speicher-Explorer ein Konto hinzugefügt haben, müssen Sie
    diesen Schritt nicht noch einmal durchführen.
    
    ![HDI.ASEAddAccount](./media/hdinsight-upload-data/HDI.ASEAddAccount.png)

3.  Geben Sie den **Speicherkontonamen** und den
    **Speicherkontoschlüssel** ein, und klicken Sie dann auf
    **Speicherkonto hinzufügen**. Sie können mehrere Speicherkonten
    hinzufügen. Jedes Konto wird auf einer Registerkarte angezeigt.
4.  Klicken Sie unter **Speichertyp** auf **Blobs**.
    
    ![HDI.ASEBlob](./media/hdinsight-upload-data/HDI.ASEBlob.png)

5.  Klicken Sie unter **Container** auf den Container, der mit Ihrem
    HDInsight-Cluster verknüpft ist. Beim Erstellen eines
    HDInsight-Clusters müssen Sie einen Container angeben. Andernfalls
    wird der Container beim Erstellen des Clusters automatisch angelegt.
6.  Klicken Sie unter **Blob** auf **Hochladen**. 
7.  Geben Sie eine hochzuladende Datei an, und klicken Sie auf
    **Öffnen**.

## <a id="commandline" ></a> Hochladen von Daten zum Blob-Speicher mit der Hadoop-Befehlszeile

Um die Hadoop-Befehlszeile verwenden zu können, müssen Sie zuerst über Remotedesktop eine Verbindung mit dem Cluster herstellen.

1.  Melden Sie sich beim [Verwaltungsportal][9] an. 
2.  Klicken Sie auf **HDINSIGHT**. Sie sehen eine Liste bereitgestellter
    Hadoop-Cluster.
3.  Klicken Sie auf das HDInsight-Cluster, zu dem Sie Daten hochladen
    möchten.
4.  Klicken Sie oben auf der Seite auf **CONFIGURATION**. 
5.  Klicken Sie auf **ENABLE REMOTE**, wenn Sie Remotedesktop noch nicht
    aktiviert haben, und folgen Sie den Anweisungen. Gehen Sie
    andernfalls zum nächsten Schritt.
6.  Klicken Sie unten auf der Seite auf **CONNECT**. 
7.  Klicken Sie auf **Öffnen**. 
8.  Geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **OK**. 
9.  Klicken Sie auf **Ja**. 
10. Klicken Sie auf dem Desktop auf **Hadoop-Befehlszeile**. 
11. Das folgende Beispiel zeigt, wie die Datei davinci.txt aus dem lokalen Dateisystem auf dem HDInsight-Hauptknoten in das Verzeichnis /example/data kopiert wird.
    
        hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt
    
    Da sich das Standarddateisystem im Azure-Blob-Speicher befindet,
    befindet sich /example/data/davinci.txt tatsächlich im
    Azure-Blob-Speicher. Sie können auch folgendermaßen auf die Datei
    verweisen:
    
        wasb:///example/data/davinci.txt 
    
    Oder
    
        wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt
    
    Wenn Sie *wasbs* verwenden, müssen Sie den vollqualifizierten
    Domänennamen angeben.

12. Verwenden Sie den folgenden Befehl, um die hochgeladenen Dateien
    aufzulisten:
    
        hadoop dfs -lsr /example/data

## <a id="sqoop" ></a> Importieren von Daten nach HDFS aus SQL-Datenbank/SQL Server mithilfe von Sqoop

Sqoop ist ein Tool zum Übertragen von Daten zwischen Hadoop und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL, MySQL oder Oracle in das Hadoop Distributed File System (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und die Daten anschließend wieder in ein RDBMS exportieren. Weitere Informationen finden Sie unter [Sqoop-Benutzeranleitung][10].

Bevor Sie Daten importieren, müssen Sie den Namen des Azure SQL-Datenbankservers, den Namen des Datenbankkontos, das Kontokennwort und den Datenbanknamen kennen.

Eine Azure SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn die Firewall-Einstellung deaktiviert ist, müssen Sie sie im Azure-Verwaltungsportal aktivieren. Anweisungen zum Erstellen einer SQL-Datenbank und zum Konfigurieren von Firewall-Regeln finden Sie unter [Erstellen und Konfigurieren einer SQL-Datenbank](../sql-database-create-configure/).

In den nachfolgend dargestellten Schritten wird ein Sqoop-Job mithilfe von PowerShell übermittelt.

**So importieren Sie Daten mit Sqoop und PowerShell nach HDInsight**

1.  Öffnen Sie das Azure PowerShell-Konsolenfenster entsprechend den
    Anweisungen unter [Installieren und Konfigurieren von Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/)
    .
2.  Legen Sie die Werte der ersten acht Variablen im folgenden Skript
    fest:
    
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $clusterName = "<HDInsightClusterName>"
        	
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
         $sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
         $tableName = "<SQLDatabaseTableName>"
        	
         $hdfsOutputDir = "<OutputPath>"  # Dies ist der HDFS-Pfad für die Ausgabedatei, z.B. "/lineItemData".
        	
         Select-AzureSubscription $subscriptionName		
         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 
        
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
        	
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3.  Fügen Sie das Skript in das Azure PowerShell-Konsolenfenster ein, um
    es auszuführen. Unter [Erste Schritte mit
    HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
    finden Sie ein PowerShell-Beispiel zum Abrufen der Datendatei aus
    dem Azure-Blob-Speicher.

Weitere Informationen über Sqoop finden Sie unter [Verwenden von Sqoop mit HDInsight](../hdinsight-use-sqoop/).

## Nächste Schritte

Jetzt wissen Sie, wie Sie Daten in HDInsight importieren. Lernen Sie anhand der folgenden Artikel, wie Sie die Daten analysieren:

* [Erste Schritte mit Azure
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
* [Programmgesteuerte Übermittlung von
  Hadoop-Jobs](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
* [Verwenden von Hive mit
  HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
* [Verwenden von Pig mit
  HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)



[1]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj152841.aspx
[3]: http://azurestorageexplorer.codeplex.com/
[4]: http://www.cerebrata.com/Products/CloudStorageStudio/
[5]: http://clumsyleaf.com/products/cloudxplorer
[6]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[7]: http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx
[8]: http://aka.ms/WaCopy
[9]: https://manage.windowsazure.com
[10]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html