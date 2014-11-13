<properties urlDisplayName="HDInsight Administration" pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, Hadoop, administration, administer" description="Hier erfahren Sie, wie Sie administrative Aufgaben f&uuml;r Hadoop-Cluster in HDInsight mit Azure PowerShell ausf&uuml;hren." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. In diesem Artikel erfahren Sie, wie Sie Hadoop-Cluster in HDInsight mit einer lokalen Azure PowerShell-Konsole über Windows PowerShell verwalten. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][HDInsight-Cmdlet-Referenz].

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Die HDInsight PowerShell-Cmdlets führen Aufgaben für Ihr Abonnement aus. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][Kaufoptionen], [Spezielle Angebote][Spezielle Angebote] oder [Kostenlose Testversion][Kostenlose Testversion].

-   Eine Arbeitsstation mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

## Themen in diesem Artikel

-   [Bereitstellen eines Clusters][Bereitstellen eines Clusters]
-   [Auflisten und Anzeigen von Clustern][Auflisten und Anzeigen von Clustern]
-   [Löschen eines Clusters][Löschen eines Clusters]
-   [Gewähren/Widerrufen von Zugriff auf HTTP-Dienste][Gewähren/Widerrufen von Zugriff auf HTTP-Dienste]
-   [Übermitteln von MapReduce-Jobs][Übermitteln von MapReduce-Jobs]
-   [Übermitteln von Hive-Jobs][Übermitteln von Hive-Jobs]
-   [Hochladen von Daten in den Blob-Speicher][Hochladen von Daten in den Blob-Speicher]
-   [Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher][Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher]

## <span id="provision"></span></a>Bereitstellen eines HDInsight-Clusters

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können.

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**So erstellen Sie ein Azure-Speicherkonto**

Nachdem Sie die Datei mit Veröffentlichungseinstellungen importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

    # Create an Azure storage account
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # For example, "West US"

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] Das Speicherkonto muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

> -   Südostasien
> -   Nordeuropa
> -   Westeuropa
> -   USA (Ost)
> -   USA (West)

Informationen zum Erstellen eines Azure-Speicherkontos mit dem Azure-Verwaltungsportal finden Sie unter [Erstellen eines Speicherkontos][Erstellen eines Speicherkontos].

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

    # List storage accounts for the current subscription
    Get-AzureStorageAccount
    # List the keys for a storage account
    Get-AzureStorageKey <StorageAccountName>

Details zum Abrufen dieser Informationen im Verwaltungsportal finden Sie im Bereich *Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* des Artikels [Verwalten von Speicherkonten][Verwalten von Speicherkonten].

**So erstellen Sie einen Azure-Speichercontainer**

Während der HDInsight-Bereitstellung kann von PowerShell kein Blob-Container erstellt werden. Sie können einen Container mit dem folgenden Skript erstellen:

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Create a storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext

**So stellen Sie einen Cluster bereit**

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Get the storage account key
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Create a new HDInsight cluster
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Der folgende Screenshot zeigt die Skriptausführung:

![HDI.PS.Bereitstellen][HDI.PS.Bereitstellen]

## <span id="listshow"></span></a> Auflisten und Anzeigen von Clusterdetails

Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

**So listen Sie alle Cluster im aktuellen Abonnement auf**

    Get-AzureHDInsightCluster 

**So zeigen Sie Details eines bestimmten Clusters im aktuellen Abonnement an**

    Get-AzureHDInsightCluster -Name <ClusterName> 

## <span id="delete"></span></a> Löschen eines Clusters

Mit dem folgenden Befehl können Sie einen Cluster löschen:

    Remove-AzureHDInsightCluster -Name <ClusterName> 

## <span id="httpservice"></span></a> Gewähren/Widerrufen von Zugriff auf HTTP-Dienste

In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff widerrufen/gewähren. Hier ein Beispiel:

    Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

In diesem Beispiel ist *hdiv2* der Name eines HDInsight-Clusters.

> [WACOM.NOTE] Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

Diese Aufgabe kann auch im Azure-Verwaltungsportal ausgeführt werden. Weitere Informationen finden Sie unter [Verwalten von HDInsight mit dem Verwaltungsportal][Verwalten von HDInsight mit dem Verwaltungsportal].

## <span id="mapreduce"></span></a> Übermitteln von MapReduce-Jobs

In der HDInsight-Clusterbereitstellung sind einige MapReduce-Beispiele enthalten. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien.

**So übermitteln Sie einen MapReduce-Job**

Mit dem folgenden PowerShell-Skript wird der Beispieljob zur Wortzählung übermittelt:

    $clusterName = "<HDInsightClusterName>"            

    # Define the MapReduce job
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Run the job and show the standard error 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] *hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Für HDInsight-Cluster der Version 3.0 wurde die Datei in *hadoop-mapreduce.jar* umbenannt.

Informationen zum WASB-Präfix finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-
storage].

**So laden Sie die Ausgabe des MapReduce-Jobs herunter**

Mit dem folgenden PowerShell-Skript wird die Ausgabe des MapReduce-Jobs aus dem letzten Beispiel abgerufen:

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Create the storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Download the output to local computer
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight][Verwenden von MapReduce mit HDInsight].

## <span id="hive"></span></a> Übermitteln von Hive-Jobs

Die HDInsight-Clusterbereitstellung enthält eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. Mit der HiveQL-Abfrage "show tables;" können Sie die Hive-Tabellen in einem Cluster auflisten.

**So übermitteln Sie einen Hive-Job**

Mit dem folgenden Skript wird ein Hive-Job zum Auflisten der Hive-Tabellen übermittelt:

    $clusterName = "<HDInsightClusterName>"               

    # HiveQL query
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
    "@

    Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

Der Hive-Job zeigt zuerst die im Cluster erstellten Hive-Tabellen und dann die aus der hivesampletable zurückgegebenen Daten an.

Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight].

## <span id="upload"></span></a>Hochladen von Daten in den Blob-Speicher

Siehe [Hochladen von Daten in HDInsight][Hochladen von Daten in HDInsight].

## <span id="download"></span></a>Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher

Informationen finden Sie im Abschnitt [Übermitteln von MapReduce-Jobs][Übermitteln von MapReduce-Jobs] in diesem Artikel.

## Weitere Informationen

-   [HDInsight-Cmdlet-Referenzdokumentation][HDInsight-Cmdlet-Referenz]
-   [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal][Verwalten von HDInsight mit dem Verwaltungsportal]
-   [Verwalten von HDInsight mit der plattformübergreifenden Befehlszeile][Verwalten von HDInsight mit der plattformübergreifenden Befehlszeile]
-   [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern]
-   [Hochladen von Daten zu HDInsight][Hochladen von Daten in HDInsight]
-   [Programmgesteuerte Übermittlung von Hadoop-Jobs][Programmgesteuerte Übermittlung von Hadoop-Jobs]
-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight]

  [HDInsight-Cmdlet-Referenz]: http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx
  [Kaufoptionen]: http://azure.microsoft.com/de-de/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/de-de/pricing/member-offers/
  [Kostenlose Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Bereitstellen eines Clusters]: #provision
  [Auflisten und Anzeigen von Clustern]: #listshow
  [Löschen eines Clusters]: #delete
  [Gewähren/Widerrufen von Zugriff auf HTTP-Dienste]: #httpservices
  [Übermitteln von MapReduce-Jobs]: #mapreduce
  [Übermitteln von Hive-Jobs]: #hive
  [Hochladen von Daten in den Blob-Speicher]: #upload
  [Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher]: #download
  [Erstellen eines Speicherkontos]: ../storage-create-storage-account/
  [Verwalten von Speicherkonten]: ../storage-manage-storage-account/
  [HDI.PS.Bereitstellen]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
  [Verwalten von HDInsight mit dem Verwaltungsportal]: ../hdinsight-administer-use-management-portal/
  [Verwenden von MapReduce mit HDInsight]: ../hdinsight-use-mapreduce/
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Hochladen von Daten in HDInsight]: ../hdinsight-upload-data/
  [Verwalten von HDInsight mit der plattformübergreifenden Befehlszeile]: ../hdinsight-administer-use-command-line/
  [Bereitstellen von HDInsight-Clustern]: ../hdinsight-provision-clusters/
  [Programmgesteuerte Übermittlung von Hadoop-Jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
