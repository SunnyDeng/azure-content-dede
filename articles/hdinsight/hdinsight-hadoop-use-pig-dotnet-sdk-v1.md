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
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Ausführen von Pig-Aufträgen mithilfe des .NET SDK für Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dieses Dokument enthält ein Beispiel zur Verwendung von .NET SDK für Hadoop zur Übermittlung von Pig-Aufträgen an einen Hadoop-Cluster in HDInsight.

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen. Mithilfe von Pig können Sie MapReduce-Vorgänge erstellen, indem Sie eine Reihe von Datentransformationen modellieren. Sie werden erfahren, wie Sie mit einer einfachen C#-Anwendung einen Pig-Auftrag an einen HDInsight-Cluster übermitteln.

[AZURE.INCLUDE [azure-preview-portal](../../includes/hdinsight-azure-portal.md)]

* [Ausführen von Pig-Aufträgen mithilfe des .NET SDK für Hadoop in HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk.md)

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

3. Suchen Sie in den auf der rechten Seite angezeigten Informationen das zu verwendende Abonnement, und notieren Sie den Wert in der Spalte **Abonnement-ID**.

Speichern Sie die Abonnement-ID, da sie später verwendet wird.

##<a id="create"></a>Erstellen der Anwendung

1. Öffnen Sie Visual Studio 2012, 2013 oder 2015.

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

5. Wählen Sie im Menü **Extras** die Option **Bibliothekspaket-Manager** oder **Nuget-Paket-Manager** und dann **Paket-Manager-Konsole** aus.

6. Führen Sie den folgenden Befehl in der Konsole aus, um die .NET SDK-Pakete zu installieren.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen. Ersetzen Sie den vorhandenen Code durch den folgenden Code:

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace HDInsightSubmitPigJobsDotNet
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					var ExistingClusterName = "<HDInsightClusterName>";
					var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
					var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
					var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		
		            HDInsightJobManagementClient _hdiJobManagementClient;
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            // Define the Pig job
		            var parameters = new PigJobSubmissionParameters()
		            {
		                UserName = ExistingClusterUsername,
		                Query = queryString,
		            };
		
		            System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            Console.WriteLine("Press ENTER to continue ...");
		            Console.ReadLine();
		        }
		    }
		}

7. Drücken Sie **F5**, um die Anwendung zu starten.
8. Drücken Sie die **EINGABETASTE**, um die Anwendung zu beenden.

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen, können Sie mit dem .NET SDK für Hadoop .NET-Anwendungen erstellen, die Pig-Aufträge an einen HDInsight-Cluster übermitteln, den Auftragsstatus überwachen und die Ausgabe abrufen.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Oct15_HO4-->