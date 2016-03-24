<properties
	pageTitle="Übermitteln von Hadoop-Aufträgen in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie Hadoop-Aufträge an Azure HDInsight Hadoop übermitteln können."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="jgao"/>

# Übermitteln von Hadoop-Aufträgen in HDInsight

Erfahren Sie, wie Sie mithilfe von Azure PowerShell MapReduce- und Hive-Aufträge übermitteln und das HDInsight .NET SDK verwenden, um MapReduce-, Hadoop-Streaming- und Hive-Aufträge zu übermitteln.

> [AZURE.NOTE] Die Schritte in diesem Artikel müssen auf einem Windows-Client ausgeführt werden. Informationen zur Verwendung eines Linux-, OS X- oder Unix-Clients für die Arbeit mit MapReduce, Hive oder Pig in HDInsight finden Sie in den folgenden Artikeln. Wählen Sie innerhalb der Artikel entweder die Links für **SSH** oder **Curl** aus:
>
> - [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
> - [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
> - [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure HDInsight-Cluster**. Anweisungen hierzu finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] und unter [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision].
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren von Azure PowerShell 1.0 und höher](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

##Übermitteln von MapReduce-Jobs mit PowerShell

Siehe [Ausführen von Hadoop MapReduce-Beispielen in Windows-basiertem HDInsight](hdinsight-run-samples.md).

##Übermitteln von Hive-Jobs mit PowerShell

Siehe [Ausführen von Hive-Abfragen mit PowerShell](hdinsight-hadoop-use-hive-powershell.md).

## Übermitteln von Hive-Aufträgen mithilfe von Visual Studio

Weitere Informationen finden Sie unter [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio][hdinsight-visual-studio-tools].

##Übermitteln von Sqoop-Jobs mit PowerShell

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop].

##Übermitteln von Hive-/Pig-/Sqoop-Aufträgen mit dem HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen.

**So übermitteln Sie Aufträge**

1. Erstellen Sie eine C#-Konsolenanwendung in Visual Studio.
2. Führen Sie in der NuGet-Paket-Manager-Konsole den folgenden Befehl aus.

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. Verwenden Sie den folgenden Code:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Security;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHDInsightJobDotNet
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
				private static HDInsightJobManagementClient _hdiJobManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
				private const string ResourceGroupName = "<Your Resource Group Name>";
		
				private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
				private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
				private const string ExistingClusterUsername = "<Cluster Username>";
				private const string ExistingClusterPassword = "<Cluster User Password>";
                
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Running");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

					var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);  
 					var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");  

					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
					var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
					_hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
					SubmitHiveJob();
					SubmitPigJob();
					SubmitSqoopJob();
				}
		
				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
				{
					var authFactory = new AuthenticationFactory();
		
					var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
					if (username != null && password != null)
						account.Id = username;
		
					var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
					var accessToken =
						authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
							.AccessToken;
		
					return new TokenCloudCredentials(accessToken);
				}
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
				}
		
				private static void SubmitPigJob()
				{
					var parameters = new PigJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Query = @"LOGS = LOAD 'wasb:///example/data/sample.log';
							LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
							FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
							GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
							FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
							RESULT = order FREQUENCIES by COUNT desc;
							DUMP RESULT;"
					};
		
					System.Console.WriteLine("Submitting the Pig job to the cluster...");
					var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
					System.Console.WriteLine("Validating that the response is as expected...");
					System.Console.WriteLine("Response status code is " + response.StatusCode);
					System.Console.WriteLine("Validating the response object...");
					System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
				}
		
				private static void SubmitHiveJob()
				{
					Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
					List<string> args = new List<string> { { "argA" }, { "argB" } };
					var parameters = new HiveJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Query = "SHOW TABLES",
						Defines = ConvertDefinesToString(defines),
						Arguments = ConvertArgsToString(args)
					};
		
                    Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    Console.WriteLine("Validating that the response is as expected...");
                    Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    Console.WriteLine("Validating the response object...");
                    Console.WriteLine("JobId is " + jobId);

                    Console.WriteLine("Waiting for the job completion ...");
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var outputResponse = _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
				}
		
				private static void SubmitSqoopJob()
				{
					var sqlDatabaseServerName = "<SQLDatabaseServerName>";
					var sqlDatabaseLogin = "<SQLDatabaseLogin>";
					var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
					var sqlDatabaseDatabaseName = "hdisqoop";
		
					var tableName = "log4jlogs";
					var exportDir = "/tutorials/usesqoop/data";
		
					// Connection string for using Azure SQL Database.
					// Comment if using SQL Server
					var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
					// Connection string for using SQL Server.
					// Uncomment if using SQL Server
					//var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		
					var parameters = new SqoopJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
					};
		
					System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
					var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
					System.Console.WriteLine("Validating that the response is as expected...");
					System.Console.WriteLine("Response status code is " + response.StatusCode);
					System.Console.WriteLine("Validating the response object...");
					System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
				}
		
				private static string ConvertDefinesToString(Dictionary<string, string> defines)
				{
					if (defines.Count == 0)
					{
						return null;
					}
		
					return "&define=" + string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
				}
				private static string ConvertArgsToString(List<string> args)
				{
					if (args.Count == 0)
					{
						return null;
					}
		
					return string.Join("&arg=", args.ToArray());
				}
			}
		}

5. Drücken Sie **F5**, um die Anwendung auszuführen.

##Übermitteln von Aufträgen mit den HDInsight-Tools für Visual Studio

Mithilfe der HDInsight-Tools für Visual Studio können Sie Hive-Abfragen und Pig-Skripts ausführen. Weitere Informationen finden Sie unter [Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

##Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten zum Erstellen von HDInsight-Clustern kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Erstellen von Hadoop-Clustern in HDInsight][hdinsight-provision]
* [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
* [Referenzdokumentation zum HDInsight-Cmdlet][hdinsight-powershell-reference]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=AcomDC_0309_2016-->