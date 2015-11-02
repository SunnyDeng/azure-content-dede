<properties
   pageTitle="Verwenden von Hadoop Pig mit PowerShell in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit Azure PowerShell Pig-Aufträge an einen Hadoop-Cluster in HDInsight übermitteln."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/16/2015"
   ms.author="larryfr"/>

#Ausführen von Pig-Aufträgen mit PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dieses Dokument enthält ein Beispiel zur Verwendung von Azure PowerShell zum Übermitteln von Pig-Aufträgen an einen Hadoop-Cluster in HDInsight. Mithilfe von Pig können Sie MapReduce-Aufträge in einer Sprache (Pig Latin) verfassen, die Datentransformationen anstelle von Map- und Reduce-Funktionen modelliert.

> [AZURE.NOTE]Dieses Dokument bietet keine detaillierten Beschreibungen zu den in diesem Beispiel verwendeten Pig Latin-Anweisungen. Informationen zu den in diesem Beispiel verwendeten Pig Latin-Anweisungen finden Sie unter [Verwenden von Pig Latin mit Hadoop in HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##<a id="powershell"></a>Ausführen von Pig-Aufträgen mit PowerShell

Azure PowerShell stellt *Cmdlets* bereit, mit denen Sie Pig-Aufträge in HDInsight remote ausführen können. Intern wird dies mithilfe von REST-Aufrufen an [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (früher Templeton) im HDInsight-Cluster realisiert.

Die folgenden Cmdlets werden zum Ausführen der Pig-Aufträge in einem HDInsight-Remotecluster verwendet:

* **Login-AzureRmAccount**: authentifiziert Azure PowerShell für Ihr Azure-Abonnement

* **New-AzureRmHDInsightPigJobDefinition**: erstellt mithilfe der angegebenen Pig Latin-Anweisungen eine neue *Auftragsdefinition*

* **Start-AzureRmHDInsightJob**: sendet die Auftragsdefinition an HDInsight, startet den Auftrag und gibt ein *Auftrags*-Objekt zurück, mit dem der Status des Auftrags geprüft werden kann

* **Wait-AzureRmHDInsightJob**: verwendet das Auftragsobjekt, um den Status des Auftrags zu prüfen Es wird gewartet, bis der Auftrag abgeschlossen oder die Wartezeit überschritten ist.

* **Get-AzureRmHDInsightJobOutput**: wird zum Abrufen der Ausgabe des Auftrags verwendet

Die folgenden Schritte veranschaulichen, wie diese Cmdlets zum Ausführen eines Auftrags auf dem HDInsight-Cluster verwendet werden.

1. Speichern Sie den folgenden Code mithilfe eines Editors als **pigjob.ps1**. Ersetzen Sie **CLUSTERNAME** durch den Namen des HDInsight-Clusters.

		#Login to your Azure subscription
		Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds=Get-Credential

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"
		#Where the output will be saved
		$statusFolder = "/tutorial/pig/status"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }

		#Store the Pig Latin into $QueryString
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		"LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		"FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		"GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		"FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		"RESULT = order FREQUENCIES by COUNT desc;" +
		"DUMP RESULT;"

		#Create a new HDInsight Pig Job definition
		$pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `

		# Start the Pig job on the HDInsight cluster
		Write-Host "Start the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -ClusterCredential $creds

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

		# Display the output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Öffnen Sie eine neue Azure PowerShell-Eingabeaufforderung. Navigieren Sie zum Speicherort der Datei **pigjob.ps1**, und verwenden Sie folgenden Befehl zum Ausführen des Skripts:

		.\pigjob.ps1
        
    Sie werden zunächst dazu aufgefordert, sich bei Ihrem Azure-Abonnement anzumelden. Außerdem werden Sie aufgefordert, den HTTPS-/Administratorkontonamen und das Kennwort für den HDInsight-Cluster bereitzustellen.

7. Nachdem der Auftrag abgeschlossen ist, muss er Informationen zurückgeben, die den folgenden ähneln:

		Start the Pig job ...
		Wait for the Pig job to complete ...

		Cluster         : CLUSTERNAME.
        HttpEndpoint    : CLUSTERNAME.azurehdinsight.net
        State           : SUCCEEDED
        JobId           : job_1444852971289_0018
        ParentId        :
        PercentComplete : 100% complete
        ExitValue       : 0
        User            : admin
        Callback        :
        Completed       : done
        
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Problembehandlung

Wenn nach Abschluss des Auftrags keine Informationen zurückgegeben werden, ist während der Verarbeitung möglicherweise ein Fehler aufgetreten. Um Fehlerinformationen für diesen Auftrag anzuzeigen, fügen Sie folgenden Befehl am Ende der Datei **pigjob.ps1** hinzu. Anschließend speichern Sie die Datei und führen sie erneut aus.

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            -DisplayOutputType StandardError

Dadurch werden die beim Ausführen des Auftrags an STDERR auf dem Server geschriebenen Informationen zurückgegeben, die möglicherweise hilfreich sind, um festzustellen, warum der Auftrag fehlgeschlagen ist.

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet Azure PowerShell eine einfache Möglichkeit, um Pig-Aufträge auf einem HDInsight-Cluster auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Oct15_HO4-->