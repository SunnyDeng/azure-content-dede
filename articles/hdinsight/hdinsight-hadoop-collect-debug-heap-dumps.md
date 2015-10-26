<properties
	pageTitle="Debuggen und Analysieren von Hadoop-Diensten mit Heapdumps | Microsoft Azure"
	description="Lassen Sie Heapdumps für Hadoop-Dienste automatisch sammeln und zum Debuggen und Analysieren in das Azure-Blobspeicherkonto einfügen."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>


# Sammeln von Heapdumps im Blobspeicher zum Debuggen und Analysieren von Hadoop-Diensten

[AZURE.INCLUDE [Heapdump-Auswahl](../../includes/hdinsight-selector-heap-dump.md)]

Heapdumps können für Hadoop-Dienste automatisch gesammelt und im Azure-Blobspeicherkonto eines Benutzers unter "HDInsightHeapDumps/" abgelegt werden. Dumpdateien für einen Dienst mit Heaps enthalten eine Momentaufnahme des Arbeitsspeichers der Anwendung. Dazu zählen die Werte von Variablen zum Zeitpunkt der Erstellung des Dumps.

Die Sammlung von Heapdumps für verschiedene Dienste muss für Dienste in einzelnen Clustern aktiviert werden. Standardmäßig ist dieses Feature für einen Cluster deaktiviert. Diese Heapdumps können groß sein, sodass es ratsam ist, das Blobspeicherkonto zu überwachen, in dem sie gespeichert werden, nachdem die Sammlung aktiviert wurde.

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten nur für Windows-basierte HDInsight-Cluster. Informationen zu Linux-basierten HDInsight-Clustern finden Sie unter [Aktivieren von Heapdumps für Hadoop-Dienste auf Linux-basierten HDInsight-Clustern](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="whichServices"></a>Verfügbare Dienste für Heapdumps

Für die folgenden Dienste können Heapdumps auf Anforderung aktiviert werden:

*  **hcatalog**: tempelton
*  **hive**: hiveserver2, metastore, derbyserver
*  **mapreduce**: jobhistoryserver
*  **yarn**: resourcemanager, nodemanager, timelineserver
*  **hdfs**: datanode, secondarynamenode, namenode

## <a name="configuration"></a>Die Konfigurationselemente, mit denen Heapdumps aktiviert werden

Um Heapdumps für einen Dienst zu aktivieren, müssen Sie die entsprechenden Konfigurationselemente im Abschnitt des jeweiligen Diensts festlegen, der von **service\_name** angegeben wird.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Der Wert von **service\_name** kann einer der oben angegebenen Dienste sein: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode oder namenode.

## <a name="powershell"></a>Aktivieren von Heapdumps mithilfe von Azure PowerShell

Um Heapdumps mit Azure PowerShell z. B. für den Dienst "jobhistoryserver" zu aktivieren, müssen Sie die folgenden Schritte ausführen:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Aktivieren von Heapdumps mit dem Azure HDInsight .NET SDK

Um Heapdumps mit dem .NET SDK z. B. für "jobhistoryserver" zu aktivieren, müssen Sie die folgenden Schritte ausführen:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=Oct15_HO3-->