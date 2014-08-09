<properties linkid="manage-services-hdinsight-administer-hdinsight-using-powershell" urlDisplayName="HDInsight Administration" pageTitle="Administer HDInsight using PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to perform administrative tasks for the HDInsight clusters using PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer HDInsight using PowerShell" authors="bradsev" />

Verwalten von HDInsight mit PowerShell
======================================

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und die Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. In diesem Artikel erfahren Sie, wie Sie HDInsight-Cluster mit einer lokalen Azure PowerShell-Konsole über Windows PowerShell verwalten. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie in der [HDInsight-Cmdlet-Referenz](http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx).

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Die HDInsight PowerShell-Cmdlets führen Aufgaben für Ihr Abonnement aus. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Spezielle Angebote](https://www.windowsazure.com/en-us/pricing/member-offers/) oder [Kostenlose Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/).

-   Eine Arbeitsstation mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).

Themen in diesem Artikel
------------------------

-   [Bereitstellen eines Clusters](#provision)
-   [Auflisten und Anzeigen von Clustern](#listshow)
-   [Löschen eines Clusters](#delete)
-   [Gewähren/Widerrufen von Zugriff auf HTTP-Dienste](#httpservices)
-   [Übermitteln von MapReduce-Jobs](#mapreduce)
-   [Übermitteln von Hive-Jobs](#hive)
-   [Hochladen von Daten in den Blob-Speicher](#upload)
-   [Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher](#download)

Bereitstellen eines HDInsight-Clusters
--------------------------------------

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können.

**So erstellen Sie ein Azure-Speicherkonto**

Nachdem Sie die Datei mit Veröffentlichungseinstellungen importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

    # Azure-Speicherkonto erstellen
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # Beispielsweise "West US"

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] Das Speicherkonto muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

><ul>
<li>Südostasien</li>
<li>Nordeuropa</li>
<li>Westeuropa</li>
<li>USA (Osten)</li>
<li>USA (Westen)</li>
</ul>




Informationen zum Erstellen eines Azure-Speicherkontos mit dem Azure-Verwaltungsportal finden Sie unter [Erstellen eines Speicherkontos](/de-de/manage/services/storage/how-to-create-a-storage-account/).

Falls Sie bereits ein Speicherkonto haben, aber den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

    # Speicherkonten für das aktuelle Abonnement auflisten
    Get-AzureStorageAccount
    # Schlüssel für ein Speicherkonto auflisten
    Get-AzureStorageKey <StorageAccountName>

Details zum Abrufen dieser Informationen im Verwaltungsportal finden Sie im Abschnitt *Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* des Artikels [Verwalten von Speicherkonten](/de-de/manage/services/storage/how-to-manage-a-storage-account/).

**So erstellen Sie einen Azure-Speichercontainer**

Während der HDInsight-Bereitstellung kann von PowerShell kein Blob-Container erstellt werden. Sie können einen Container mit dem folgenden Skript erstellen:

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Ein Speicherkontextobjekt erstellen
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Einen Blob-Speichercontainer erstellen
    New-AzureStorageContainer -Name $containerName -Context $destContext

**So stellen Sie einen Cluster bereit**

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Speicherkontoschlüssel abrufen
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Neuen HDInsight-Cluster erstellen
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Der folgende Screenshot zeigt die Skriptausführung:

![HDI.PS.Bereitstellen](./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png)

Auflisten und Anzeigen von Clusterdetails
-----------------------------------------

Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

**So listen Sie alle Cluster im aktuellen Abonnement auf**

    Get-AzureHDInsightCluster 

**So zeigen Sie Details eines bestimmten Clusters im aktuellen Abonnement an**

    Get-AzureHDInsightCluster -Name <ClusterName> 

Löschen eines Clusters
----------------------

Mit dem folgenden Befehl können Sie einen Cluster löschen:

    Remove-AzureHDInsightCluster -Name <ClusterName> 

Gewähren/Widerrufen von Zugriff auf HTTP-Dienste
------------------------------------------------

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

Diese Aufgabe kann auch im Azure-Verwaltungsportal ausgeführt werden. Weitere Informationen finden Sie unter [Verwalten von HDInsight mit dem Verwaltungsportal](/de-de/documentation/articles/hdinsight-administer-use-management-portal/).

Übermitteln von MapReduce-Jobs
------------------------------

In der HDInsight-Clusterbereitstellung sind einige MapReduce-Beispiele enthalten. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien.

**So übermitteln Sie einen MapReduce-Job**

Mit dem folgenden PowerShell-Skript wird der Beispieljob zur Wortzählung übermittelt:

    $clusterName = "<HDInsightClusterName>"            

    # MapReduce-Job definieren
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Job ausführen und Standardfehler anzeigen 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] *hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Für HDInsight-Cluster der Version 3.0 wurde die Datei in *hadoop-mapreduce.jar* umbenannt.

Informationen zum WASB-Präfix finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight- storage].

**So laden Sie die Ausgabe des MapReduce-Jobs herunter**

Mit dem folgenden PowerShell-Skript wird die Ausgabe des MapReduce-Jobs aus dem letzten Beispiel abgerufen:

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Speicherkonto-Kontextobjekt erstellen
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Ausgabe auf lokalen Computer herunterladen
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Ausgabe anzeigen
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight](/de-de/documentation/articles/hdinsight-use-mapreduce/).

Übermitteln von Hive-Jobs
-------------------------

Die HDInsight-Clusterbereitstellung enthält eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. Mit der HiveQL-Abfrage "show tables;" können Sie die Hive-Tabellen in einem Cluster auflisten.

**So übermitteln Sie einen Hive-Job**

Mit dem folgenden Skript wird ein Hive-Job zum Auflisten der Hive-Tabellen übermittelt:

    $clusterName = "<HDInsightClusterName>"               

    # HiveQL-Abfrage
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
    "@

    Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

Der Hive-Job zeigt zuerst die im Cluster erstellten Hive-Tabellen und dann die aus der hivesampletable zurückgegebenen Daten an.

Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight](/de-de/documentation/articles/hdinsight-use-hive/).

Hochladen von Daten in den Blob-Speicher
----------------------------------------

Siehe [Hochladen von Daten zu HDInsight](/de-de/documentation/articles/hdinsight-upload-data/).

Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher
---------------------------------------------------------

Informationen finden Sie im Abschnitt [Übermitteln von MapReduce-Jobs](#mapreduce) in diesem Artikel.

Weitere Informationen
---------------------

-   [HDInsight-Cmdlet-Referenzdokumentation](http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx)
-   [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal](/de-de/documentation/articles/hdinsight-administer-use-management-portal/)
-   [Verwalten von HDInsight mit der plattformübergreifenden Befehlszeile](/de-de/documentation/articles/hdinsight-administer-use-command-line/)
-   [Bereitstellen eines HDInsight-Clusters](/de-de/documentation/articles/hdinsight-provision-clusters/)
-   [Hochladen von Daten zu HDInsight](/de-de/documentation/articles/hdinsight-upload-data/)
-   [Programmgesteuerte Übermittlung von Hadoop-Jobs](/de-de/documentation/articles/hdinsight-submit-hadoop-jobs-programmatically/)
-   [Erste Schritte mit Azure HDInsight](/de-de/documentation/articles/hdinsight-get-started/)

