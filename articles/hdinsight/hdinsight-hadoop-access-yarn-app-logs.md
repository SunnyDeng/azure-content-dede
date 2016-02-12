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
	ms.date="01/29/2016"
	ms.author="jgao"/>

# Zugriff auf YARN-Anwendungsprotokolle unter Windows-basiertem HDInsight

Dieses Thema erläutert den Zugriff auf Protokolle für YARN-Anwendungen (Yet Another Resource Negotiator), die in einem Hadoop-Cluster in Azure HDInsight abgeschlossen wurden.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten nur für Windows-basierte HDInsight-Cluster. Weitere Informationen zum Zugriff auf YARN-Protokolle auf Linux-basierten HDInsight-Clustern finden Sie unter [Zugriff auf YARN-Anwendungsprotokolle auf Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md).

### Voraussetzungen

- Ein Windows-basierter HDInsight-Cluster Siehe [Erstellen Windows-basierter Hadoop-Cluster in HDInsight](hdinsight-provision-clusters.md).


## YARN Timeline Server

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


## YARN-Anwendungen und -Protokolle

YARN unterstützt mehrere Programmierungsmodelle (u. a. MapReduce), indem die Ressourcenverwaltung von der Zeitplanung/Überwachung von Anwendungen getrennt wird. Dies erfolgt über einen globalen *ResourceManager* (RM), workerknotenbezogene *NodeManager* (NMs) und anwendungsbezogene *ApplicationMasters* (AMs). Der anwendungsbezogene AM handelt Ressourcen (CPU, Arbeitsspeicher, Datenträger, Netzwerk) für die Ausführung Ihrer Anwendung mit dem RM aus. Der RM arbeitet mit NMs zusammen, um diese Ressourcen zu gewähren, die als *Container* zugewiesen werden. Der AM ist zuständig für die Nachverfolgung des Status der Container, die ihm vom RM zugewiesen wurden. Je nach Art der Anwendung kann diese viele Container benötigen.

Darüber hinaus kann jede Anwendung aus mehreren *Anwendungsversuchen* mit dem Zweck bestehen, die Anwendung bei Auftreten von Abstürzen oder Unterbrechungen der Kommunikation zwischen einem AM und RM abzuschließen. Demzufolge werden Container einem bestimmten Versuch einer Anwendung zugewiesen. In gewisser Weise stellt ein Container den Kontext für eine Basiseinheit von Aufgaben bereit, die von einer YARN-Anwendung ausgeführt werden. Sämtliche Aufgaben, die im Kontext eines Containers ausgeführt werden, erfolgen auf dem einzelnen Arbeitsknoten, dem der Container zugeordnet wurde. Weitere Informationen finden Sie unter [YARN-Konzepte][YARN-concepts].

Anwendungsprotokolle (und dazugehörige Containerprotokolle) sind für das Beheben von Problemen bei Hadoop-Anwendungen besonders wichtig. YARN bietet mit seinem Feature [Log Aggregation][log-aggregation] ein nützliches Gerüst für das Sammeln, Zusammenführen und Speichern von Anwendungsprotokollen. Durch "Log Aggregation" lässt sich der Zugriff auf Anwendungsprotokolle besser steuern, denn Protokolle für alle Container auf einem Arbeitsknoten werden zusammengeführt und als zentrale Protokolldatei pro Workerknoten im Standarddateisystem gespeichert, nachdem eine Anwendung abgeschlossen wurde. Ihre Anwendung mag Hunderte oder Tausende von Containern verwenden, doch Protokolle für alle auf einem einzelnen Arbeitsknoten vorhandenen Container werden zu einer zentralen Datei zusammengeführt, was in einer Protokolldatei pro Arbeitsknoten mündet, die von Ihrer Anwendung genutzt werden. Log Aggregation ist für HDInsight-Cluster (ab Version 3.0) standardmäßig aktiviert. Zusammengeführte Protokolle befinden sich im Standardcontainer Ihres Clusters am folgenden Speicherort:

	wasb:///app-logs/<user>/logs/<applicationId>

An diesem Speicherort ist *user* der Name des Benutzers, der die Anwendung gestartet hat. *applicationId* ist der eindeutige Bezeichner einer Anwendung gemäß der Zuweisung durch den YARN Resource Manager (RM).

Die zusammengeführten Protokolle sind nicht unmittelbar lesbar, da sie in [TFile][T-file] geschrieben werden, einem nach Container indiziertem [Binärformat][binary-format]. YARN bietet CLI-Tools zum Ausgeben dieser Protokolle mit unformatiertem Text für Anwendungen und Container von Interesse. Sie können diese Protokolle als unformatierten Text anzeigen, indem Sie einen der folgenden YARN-Befehle direkt auf die Clusterknoten anwenden (nachdem über RDP eine Verbindung hergestellt wurde):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## YARN-ResourceManager-Benutzeroberfläche

Die YARN-ResourceManager-Benutzeroberfläche wird auf dem Hauptknoten des Clusters ausgeführt. Der Zugriff darauf erfolgt über das Dashboard im Azure-Portal:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. 
2. Klicken Sie im linken Menü auf **Durchsuchen**, dann auf **HDInsight-Cluster** und danach auf einen Windows-basierten Cluster, auf dessen YARN-Anwendungsprotokolle Sie zugreifen möchten.
3. Klicken Sie im oberen Menü auf **Dashboard**. Eine auf einer neuen Browserregisterkarte geöffnete Seite namens **HDInsight-Abfragekonsole** wird angezeigt.
4. Klicken Sie in der **HDInsight-Abfragekonsole** auf **Yarn-Benutzeroberfläche**.




[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=AcomDC_0204_2016-->