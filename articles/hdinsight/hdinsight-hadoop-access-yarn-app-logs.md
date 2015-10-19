<properties
	pageTitle="Programmgesteuerter Zugriff auf YARN-Anwendungsprotokolle in Hadoop | Microsoft Azure"
	description="Greifen Sie programmgesteuert auf Anwendungsprotokolle auf einem Hadoop-Cluster in HDInsight zu."
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
	ms.date="10/02/2015"
	ms.author="jgao"/>

# Programmgesteuerter Zugriff auf YARN-Anwendungsprotokolle in Hadoop in HDInsight

In diesem Thema wird erläutert, wie Sie YARN-Anwendungen (Yet Another Resource Negotiator) aufzählen können, die in einem Hadoop-Cluster in Azure HDInsight abgeschlossen wurden. Außerdem wird erklärt, wie Sie programmgesteuert auf die Anwendungsprotokolle zugreifen können, ohne über das Remotedesktopprotokoll (RDP) eine Verbindung mit Ihren Clustern herstellen zu müssen. Insbesondere wurden eine neue Komponente und eine neue API hinzugefügt:

  1. Der allgemeine Anwendungsverlaufsserver für HDInsight-Cluster wurde aktiviert. Es handelt sich um eine Komponente im YARN Timeline Server, die für Speicherung und Abruf allgemeiner Informationen zu abgeschlossenen Anwendungen zuständig ist.
  2. Im Azure HDInsight .Net SDK stehen APIs zur Verfügung, um Ihnen das programmgesteuerte Aufzählen von Anwendungen zu ermöglichen, die in Ihren Clustern ausgeführt wurden. Es erlaubt zudem das Herunterladen der relevanten Informationen oder containerspezifischen Protokolle (als unformatierten Text), damit Sie auftretende Anwendungsprobleme besser beheben können.

> [AZURE.NOTE]Die Informationen in diesem Dokument gelten nur für Windows-basierte HDInsight-Cluster. Weitere Informationen zum Zugriff auf YARN-Protokolle auf Linux-basierten HDInsight-Clustern finden Sie unter [Zugriff auf YARN-Anwendungsprotokolle auf Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md).

## Voraussetzungen

Das Azure HDInsight SDK wird benötigt, um den in diesem Thema vorgestellten Code in einer .NET Framework-Anwendung nutzen zu können. Die zuletzt veröffentlichte Version des SDK finden Sie auf [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started).

Zum Installieren des HDInsight SDK in einer Visual Studio-Anwendung öffnen Sie das Menü **Extras**, klicken auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Führen Sie den folgenden Befehl in der Konsole aus, um die Pakete zu installieren:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken für HDInsight und Verweise darauf hinzugefügt.


## <a name="YARNTimelineServer"></a>YARN Timeline Server

Der <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> bietet mittels zweier Schnittstellen allgemeine Informationen zu abgeschlossenen Anwendungen sowie frameworkspezifische Anwendungsinformationen. Dies gilt insbesondere in folgenden Fällen:

* Speicherung und Abruf allgemeiner Anwendungsinformationen zu HDInsight-Clustern sind für Versionen ab 3.1.1.374 aktiviert.
* Die frameworkspezifische Anwendungsinformationskomponente des Timeline Servers steht für HDInsight-Cluster derzeit nicht zur Verfügung.


Zu den allgemeinen Informationen zu Anwendungen zählen die folgenden Datenarten:

* Die Anwendungs-ID, ein eindeutiger Bezeichner einer Anwendung
* Der Benutzer, der die Anwendung gestartet hat
* Informationen zu den erfolgten Versuchen, die Anwendung abzuschließen
* Die bei Anwendungsversuchen verwendeten Container

In Ihren HDInsight-Clustern werden diese Informationen vom Azure-Ressourcen-Manager in einem Verlaufsspeicher im Standardcontainer Ihres Azure-Standardspeicherkontos gespeichert. Diese allgemeinen Daten zu abgeschlossenen Anwendungen können über eine REST-API abgerufen werden:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Wir haben dem HDInsight .Net SDK neue APIs hinzugefügt, um den programmgesteuerten Abruf dieser Daten zu vereinfachen. Diese allgemeinen Daten können jedoch auch abgerufen werden, indem Sie über die YARN-Befehlszeilenschnittstelle Befehle unmittelbar auf Ihre Clusterknoten anwenden (nachdem über RDP eine Verbindung mit dem Cluster hergestellt wurde).

## <a name="YARNAppsAndLogs"></a>YARN-Anwendungen und -Protokolle

YARN unterstützt mehrere Programmierungsmodelle (u. a. MapReduce), indem die Ressourcenverwaltung von der Zeitplanung/Überwachung von Anwendungen getrennt wird. Dies erfolgt über einen globalen *Resource Manager* (RM), workerknotenbezogene *Node Manager* (NMs) und anwendungsbezogene *Application Master* (AMs). Der anwendungsbezogene AM handelt Ressourcen (CPU, Arbeitsspeicher, Datenträger, Netzwerk) für die Ausführung Ihrer Anwendung mit dem RM aus. Der RM arbeitet mit NMs zusammen, um diese Ressourcen zu gewähren, die als *Container* zugewiesen werden. Der AM ist zuständig für die Nachverfolgung des Status der Container, die ihm vom RM zugewiesen wurden. Je nach Art der Anwendung kann diese viele Container benötigen.

Darüber hinaus kann jede Anwendung aus mehreren *Anwendungsversuchen* mit dem Zweck bestehen, die Anwendung bei Auftreten von Abstürzen oder Unterbrechungen der Kommunikation zwischen einem AM und RM abzuschließen. Demzufolge werden Container einem bestimmten Versuch einer Anwendung zugewiesen. In gewisser Weise stellt ein Container den Kontext für eine Basiseinheit von Aufgaben bereit, die von einer YARN-Anwendung ausgeführt werden. Sämtliche Aufgaben, die im Kontext eines Containers ausgeführt werden, erfolgen auf dem einzelnen Arbeitsknoten, dem der Container zugeordnet wurde. Weitere Informationen finden Sie unter [YARN-Konzepte][YARN-concepts].

Anwendungsprotokolle (und dazugehörige Containerprotokolle) sind für das Beheben von Problemen bei Hadoop-Anwendungen besonders wichtig. YARN bietet mit seinem Feature [Log Aggregation][log-aggregation] ein nützliches Gerüst für das Sammeln, Zusammenführen und Speichern von Anwendungsprotokollen. Durch "Log Aggregation" lässt sich der Zugriff auf Anwendungsprotokolle besser steuern, denn Protokolle für alle Container auf einem Arbeitsknoten werden zusammengeführt und als zentrale Protokolldatei pro Workerknoten im Standarddateisystem gespeichert, nachdem eine Anwendung abgeschlossen wurde. Ihre Anwendung mag Hunderte oder Tausende von Containern verwenden, doch Protokolle für alle auf einem einzelnen Arbeitsknoten vorhandenen Container werden zu einer zentralen Datei zusammengeführt, was in einer Protokolldatei pro Arbeitsknoten mündet, die von Ihrer Anwendung genutzt werden. Log Aggregation ist für HDInsight-Cluster (ab Version 3.0) standardmäßig aktiviert. Zusammengeführte Protokolle befinden sich im Standardcontainer Ihres Clusters am folgenden Speicherort:

	wasb:///app-logs/<user>/logs/<applicationId>

An diesem Speicherort ist *user* der Name des Benutzers, der die Anwendung gestartet hat. *applicationId* ist der eindeutige Bezeichner einer Anwendung gemäß der Zuweisung durch den YARN Resource Manager (RM).

Die zusammengeführten Protokolle sind nicht unmittelbar lesbar, da sie in [TFile][T-file] geschrieben werden, einem nach Container indiziertem [Binärformat][binary-format]. YARN bietet CLI-Tools zum Ausgeben dieser Protokolle mit unformatiertem Text für Anwendungen und Container von Interesse. Sie können diese Protokolle als unformatierten Text anzeigen, indem Sie einen der folgenden YARN-Befehle direkt auf die Clusterknoten anwenden (nachdem über RDP eine Verbindung hergestellt wurde):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Im nächsten Abschnitt wird erläutert, wie auf anwendungs- oder containerspezifische Protokolle programmgesteuert zugegriffen werden kann, ohne über RDP eine Verbindung mit Ihren HDInsight-Clustern herstellen zu müssen.

## <a name="enumerate-and-download"></a>Programmgesteuertes Aufzählen von Anwendungen und Herunterladen von Protokollen

Zum Verwenden der folgenden Codebeispiele müssen Sie die zuvor genannten Voraussetzungen erfüllen, indem Sie die neueste Version des HDInsight .NET SDK herunterladen. Lesen Sie die angegebenen Anweisungen.

Der nachfolgende Code veranschaulicht das Verwenden der neuen APIs zum Aufzählen von Anwendungen und Herunterladen der Protokolle für abgeschlossene Anwendungen.

> [AZURE.NOTE]Die nachstehenden APIs funktionieren nur für "ausgeführte" Hadoop-Cluster ab Version 3.1.1.374. Fügen Sie die folgenden Direktiven hinzu:

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

die auf die neu definierten APIs im folgenden Code verweisen. Mit dem folgenden Codeausschnitt wird ein "Application History"-Client für einen in Ihrem Abonnement "ausgeführten" Cluster erstellt:

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";

	// Create an HDInsight client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);

	HDInsightCertificateCredential creds =
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);

	IHDInsightClient client = HDInsightClient.Connect(creds);

	// Get the cluster on which your applications were run
	// The cluster needs to be in the "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);

	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient =
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


Mit dem "Application History"-Client können Sie abgeschlossene Anwendungen auflisten, Anwendungen basierend auf Ihren Kriterien filtern und relevante Anwendungsprotokolle herunterladen. Der folgende Codeausschnitt zeigt, wie dies programmgesteuert erfolgt:

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";

	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app =>
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed
	                    && app.Name.StartsWith(appNamePrefix));

	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

Der obige Code dient zum Auflisten/Finden von Anwendungen von Interesse mithilfe des "Application History"-Clients und anschließenden Herunterladen von Protokollen für diese Anwendungen in einen lokalen Ordner.

Alternativ werden mit dem folgenden Codeausschnitt Protokolle für eine Anwendung heruntergeladen, deren Anwendungs-ID bekannt ist. Die Anwendungs-ID ist der global eindeutige Bezeichner einer Anwendung gemäß der Zuweisung durch den Resource Manager. Sie wird anhand der Startzeit von Resource Manager sowie eines monoton ansteigenden Zählers für Anwendungen erstellt, die an diesen übermittelt werden. Die Anwendungs-ID hat das Format "Anwendung\_&lt;RM-Startzeit&gt;\_&lt;Zähler&gt". Bitte beachten Sie, dass die Anwendungs-ID und die Auftrags-ID eindeutig sind. Die Auftrags-ID ist ein für das MapReduce-Framework spezifisches Konzept, während die Anwendungs-ID ein von Frameworks unabhängiges YARN-Konzept ist. In YARN bestimmt eine Auftrags-ID einen spezifischen MapReduce-Auftrag entsprechend der Verarbeitung durch den Application Master einer MapReduce-Anwendung, die an den Resource Manager übermittelt wurde.

	// Download application logs for an application whose application ID is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

Bei Bedarf können Sie auch Protokolle für jeden Container (oder einen bestimmten Container) herunterladen, die wie unten dargestellt von einer Anwendung verwendet werden.

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);

	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);

	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);

	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);

	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=Oct15_HO2-->