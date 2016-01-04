<properties
   pageTitle="Verwenden von MapReduce und PowerShell mit Hadoop | Microsoft Azure"
   description="Erfahren Sie, wie MapReduce-Aufträge mithilfe von PowerShell mit Hadoop in HDInsight remote ausgeführt werden."
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
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#Ausführen von Hive-Abfragen mit Hadoop in HDInsight mithilfe von PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dieses Dokument enthält ein Beispiel zur Verwendung von Azure PowerShell zum Ausführen eines MapReduce-Auftrags in einem Hadoop-Cluster in HDInsight.

##<a id="prereq"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- **Einen Azure HDInsight-Cluster (Hadoop in HDInsight), der auf Windows oder Linux basiert**

- **Eine Arbeitsstation mit Azure PowerShell**. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

##<a id="powershell"></a>Ausführen eines MapReduce-Auftrags mithilfe von Azure PowerShell

Azure PowerShell stellt *Cmdlets* bereit, mit denen Sie MapReduce-Aufträge in HDInsight remote ausführen können. Intern wird dies mithilfe von REST-Aufrufen an [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (früher Templeton) auf dem HDInsight-Cluster realisiert.

Die folgenden Cmdlets werden zum Ausführen der MapReduce-Aufträge in einem HDInsight-Remotecluster verwendet.

* **Login-AzureRmAccount**: Authentifiziert Azure PowerShell für Ihr Azure-Abonnement

* **New-AzureRmHDInsightMapReduceJobDefinition**: Erstellt aus den angegebenen MapReduce-Informationen eine neue *Auftragsdefinition*.

* **Start-AzureRmHDInsightJob**: sendet die Auftragsdefinition an HDInsight, startet den Auftrag und gibt ein *Auftrags*-Objekt zurück, mit dem der Status des Auftrags geprüft werden kann

* **Wait-AzureRmHDInsightJob**: verwendet das Auftragsobjekt, um den Status des Auftrags zu prüfen Es wird gewartet, bis der Auftrag abgeschlossen oder die Wartezeit überschritten ist.

* **Get-AzureRmHDInsightJobOutput**: wird zum Abrufen der Ausgabe des Auftrags verwendet

Die folgenden Schritte veranschaulichen, wie diese Cmdlets zum Ausführen eines Auftrags in einem HDInsight-Cluster verwendet werden.

1. Speichern Sie den folgenden Code in einem Editor in einer Datei namens **mapreducejob.ps1**. Ersetzen Sie **CLUSTERNAME** durch den Namen des HDInsight-Clusters.

		#Specify the values
		$clusterName = "CLUSTERNAME"
        		
		# Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Login-AzureRmAccount
		}
        
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasb:///example/data/gutenberg/davinci.txt", `
                "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
		# Print the output
		Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Öffnen Sie eine neue **Azure PowerShell**-Befehlsaufforderung. Navigieren Sie zum Speicherort der Datei **mapreducejob.ps1**, und verwenden Sie folgenden Befehl zum Ausführen des Skripts.

		.\mapreducejob.ps1
    
    Wenn Sie das Skript ausführen, werden Sie möglicherweise zur Authentifizierung des Azure-Abonnements aufgefordert Sie werden außerdem aufgefordert, den HTTPS-/Administratorkontonamen und das Kennwort für den HDInsight-Cluster bereitzustellen.

3. Nach Abschluss des Auftrags sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	Diese Ausgabe gibt an, dass der Auftrag erfolgreich abgeschlossen wurde.

	> [AZURE.NOTE]Wenn sich für **ExitCode** ein anderer Wert als 0 ergibt, finden Sie weitere Informationen unter [Troubleshooting](#troubleshooting).

    In diesem Beispiel werden außerdem die heruntergeladenen Dateien in Ordner **example/data/WordCountOutput** in dem Verzeichnis gespeichert, von dem aus Sie das Skript ausführen.

##Anzeigen der Ausgabe

Die Ausgabe des MapReduce-Auftrags wird in Dateien mit der Bezeichnung *part-r-#####* gespeichert. Öffnen Sie die Datei **example/data/WordCountOutput/part-r-00000** in einem Texteditor, um die durch den Auftrag erzeugten Wörter und Zählerstände anzuzeigen.

> [AZURE.NOTE]Die Ausgabedateien eines MapReduce-Auftrags sind unveränderlich. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

##<a id="troubleshooting"></a>Problembehandlung

Wenn nach Abschluss des Auftrags keine Informationen zurückgegeben werden, ist während der Verarbeitung möglicherweise ein Fehler aufgetreten. Um Fehlerinformationen für diesen Auftrag anzuzeigen, fügen Sie folgenden Befehl am Ende der Datei **mapreducejob.ps1** hinzu. Anschließend speichern Sie die Datei und führen sie erneut aus.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            -DisplayOutputType StandardError

Dadurch werden die beim Ausführen des Auftrags an STDERR auf dem Server geschriebenen Informationen zurückgegeben, die möglicherweise hilfreich sind, um festzustellen, warum der Auftrag fehlgeschlagen ist.

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet Azure PowerShell eine einfache Möglichkeit, um MapReduce-Aufträge auf einem HDInsight-Cluster auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu MapReduce-Aufträgen in HDInsight:

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_1210_2015-->