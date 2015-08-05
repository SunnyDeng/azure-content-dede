<properties
   pageTitle="Verwenden von Hadoop Pig mit .NET in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie mithilfe des .NET SDK für Hadoop Pig-Aufträge an Hadoop in HDInsight übermitteln."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Ausführen von Pig-Aufträgen mithilfe des .NET SDK für Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dieses Dokument enthält ein Beispiel zur Verwendung von .NET SDK für Hadoop zur Übermittlung von Pig-Aufträgen an einen Hadoop-Cluster in HDInsight.

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen. Mithilfe von Pig können Sie MapReduce-Vorgänge erstellen, indem Sie eine Reihe von Datentransformationen modellieren. Sie werden erfahren, wie Sie mit einer einfachen C#-Anwendung einen Pig-Auftrag an einen HDInsight-Cluster übermitteln.

##<a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Azure HDInsight-Cluster (Hadoop in HDInsight), der auf Windows oder Linux basiert

* Visual Studio 2012 oder 2013

##<a id="certificate"></a>Erstellen eines Verwaltungszertifikats

Um die Anwendung für Azure HDInsight zu authentifizieren, müssen Sie ein selbstsigniertes Zertifikat erstellen, es auf Ihrer Entwicklungsarbeitsstation installieren und es auch in Ihrem Azure-Abonnement hochladen.

Anweisungen hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE]Stellen Sie beim Erstellen des Zertifikats sicher, dass Sie sich den verwendeten Anzeigenamen notieren, da dieser später verwendet wird.

##<a id="subscriptionid"></a>Ermitteln Ihrer Abonnement-ID

Jedes Azure-Abonnement wird durch einen GUID-Wert gekennzeichnet, der als Abonnement-ID bezeichnet wird. Ermitteln Sie diesen Wert mithilfe der folgenden Schritte.

1. Öffnen Sie die [Azure-Verwaltungskonsole](https://manage.windowsazure.com/).

2. Wählen Sie im Portal auf der linken Leiste die Option **Einstellungen** aus.

3. Suchen Sie in den auf der rechten Seite angezeigten Informationen das zu verwendende Abonnement, und beachten Sie den Wert in der Spalte **Abonnement-ID**.

Speichern Sie die Abonnement-ID, da sie später verwendet wird.

##<a id="create"></a>Erstellen der Anwendung

1. Öffnen Sie Visual Studio 2012 oder 2013.

2. Wählen Sie im Menü **Datei** die Option **Neu** und anschließend **Projekt** aus.

3. Geben Sie für das neue Projekt die folgenden Werte ein, oder wählen Sie sie aus.

	<table>
<tr>
<th>Eigenschaft</th>
<th>Wert</th>
</tr>
<tr>
<th>Kategorie</th>
<th>Vorlagen/Visual C#/Windows</th>
</tr>
<tr>
<th>Vorlage</th>
<th>Konsolenanwendung</th>
</tr>
<tr>
<th>Name</th>
<th>SubmitPigJob</th>
</tr>
</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Wählen Sie im Menü **Extras** die Option **Library Package Manager** oder **Nuget Package Manager** und dann **Paket-Manager-Konsole** aus.

6. Führen Sie den folgenden Befehl in der Konsole aus, um die .NET SDK-Pakete zu installieren.

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen. Ersetzen Sie den vorhandenen Code durch den folgenden Code:

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

		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");

		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");

		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";

		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";

		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };

		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);

		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);

		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);

		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }

		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
		            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }

		            Console.WriteLine("----- Press ENTER to continue.");
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

		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. Speichern Sie die Datei.

##<a id="run"></a>Ausführen der Anwendung

Drücken Sie **F5**, um die Anwendung zu starten. Wenn Sie dazu aufgefordert werden, geben Sie **Abonnement-ID**, **Angezeigter Zertifikatname** und **HDInsight-Clustername** ein. Die Anwendung erzeugt während der Ausführung mehrere Zeilen mit Informationen, die ähnlich dem Folgenden enden.

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

Drücken Sie die **EINGABETASTE**, um die Anwendung zu beenden.

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen, können Sie mit dem .NET SDK für Hadoop .NET-Anwendungen erstellen, die Pig-Aufträge an einen HDInsight-Cluster übermitteln, den Auftragsstatus überwachen und die Ausgabe abrufen.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO4-->