<properties 
	pageTitle="Sammeln von Heapdumps für Debugging und Analyse | Azure" 
	description="Sammeln von Heapdumps für Debugging und Analyse" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# Sammeln von Heapdumps für Debugging und Analyse

Heapdumps können für Hadoop-Dienste automatisch gesammelt und im Blob-Speicherkonto eines Benutzers unter "HDInsightHeapDumps/" abgelegt werden.  Dumpdateien für einen Dienst mit Heaps enthalten eine Momentaufnahme des Arbeitsspeichers der Anwendung. Dazu zählen die Werte von Variablen zum Zeitpunkt der Erstellung des Dumps.

Die Sammlung von Heapdumps für verschiedene Dienste muss für Dienste in einzelnen Clustern aktiviert werden. Standardmäßig ist dieses Feature für einen Cluster deaktiviert. Diese Heapdumps können groß sein, sodass es ratsam ist, das Blob-Speicherkonto zu überwachen, in dem sie gespeichert werden, nachdem die Sammlung aktiviert wurde.

## Themen in diesem Artikel

- [Für welche Dienste können Heapdumps aktiviert werden?](#whichServices)
- [Die Konfigurationselemente, mit denen Heapdumps aktiviert werden](#configuration)
- [Aktivieren von Heapdumps mit Azure HDInsight PowerShell](#powershell)
- [Aktivieren von Heapdumps mit dem HDInsight .NET SDK](#sdk)


## <a name="whichServices"></a>Für welche Dienste können Heapdumps aktiviert werden?

Für die folgenden Dienste können Heapdumps auf Anforderung aktiviert werden: 

*  **hcatalog**: tempelton
*  **hive**: hiveserver2, metastore, derbyserver 
*  **mapreduce**: jobhistoryserver 
*  **yarn**: resourcemanager, nodemanager, timelineserver 
*  **hdfs**: datanode, secondarynamenode, namenode

## <a name="configuration"></a>Die Konfigurationselemente, mit denen Heapdumps aktiviert werden

Um Heapdumps für einen Dienst zu aktivieren, muss der Benutzer die entsprechenden Konfigurationselemente im Abschnitt des jeweiligen Diensts festlegen, der von "service_name" angegeben wird.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

Der Wert von <service_name> kann einer der oben angegebenen Dienste sein: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode oder namenode.

## <a name="powershell"></a>Aktivieren von Heapdumps mit Azure HDInsight PowerShell

Um Heapdumps mit PowerShell z. B. für den Dienst "jobhistoryserver" zu aktivieren, muss der Benutzer die folgenden Schritte ausführen:

Mit dem Powershell SDK:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Aktivieren von Heapdumps mit dem Azure HDInsight .NET SDK

Um Heapdumps mit dem .NET SDK z. B. für den Dienst "jobhistoryserver" zu aktivieren, muss der Benutzer die folgenden Schritte ausführen:

Mit dem C# SDK:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));




<!--HONumber=42-->
