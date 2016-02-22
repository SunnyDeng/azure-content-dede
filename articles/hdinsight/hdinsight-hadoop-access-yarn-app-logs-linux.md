<properties
	pageTitle="Zugreifen auf YARN-Anwendungsprotokolle unter Linux-basiertem HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie auf einem Linux-basierten HDInsight (Hadoop)-Cluster mithilfe der Befehlszeile und eines Webbrowsers auf YARN-Anwendungsprotokolle zugreifen."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="Blackmist" 
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

# Zugriff auf YARN-Anwendungsprotokolle unter Linux-basiertem HDInsight

Dieses Dokument erläutert den Zugriff auf Protokolle für YARN (Yet Another Resource Negotiator)-Anwendungen, die auf einem Hadoop-Cluster in Azure HDInsight abgeschlossen wurden.

> [AZURE.NOTE] Die Informationen in diesem Artikel gelten für Linux-basierte HDInsight-Cluster. Informationen zu Windows-basierten Clustern finden Sie unter [Zugriff auf YARN-Anwendungsprotokolle in Windows-basiertem HDInsight](hdinsight-hadoop-access-yarn-app-logs.md)

## Voraussetzungen

* Ein Linux-basierter HDInsight-Cluster.

* Sie müssen einen [SSH-Tunnel erstellen](hdinsight-linux-ambari-ssh-tunnel.md), bevor Sie auf die Webbenutzeroberfläche für ResourceManager-Protokolle zugreifen können.

## <a name="YARNTimelineServer"></a>YARN Timeline Server

Der [YARN Timeline Server](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) bietet über zwei Schnittstellen allgemeine Informationen zu abgeschlossenen Anwendungen sowie frameworkspezifische Anwendungsinformationen. Dies gilt insbesondere in folgenden Fällen:

* Speicherung und Abruf allgemeiner Anwendungsinformationen zu HDInsight-Clustern sind für Versionen ab 3.1.1.374 aktiviert.
* Die frameworkspezifische Anwendungsinformationskomponente des Timeline Servers steht für HDInsight-Cluster derzeit nicht zur Verfügung.

Zu den allgemeinen Informationen zu Anwendungen zählen die folgenden Datenarten:

* Die Anwendungs-ID, ein eindeutiger Bezeichner einer Anwendung
* Der Benutzer, der die Anwendung gestartet hat
* Informationen zu den erfolgten Versuchen, die Anwendung abzuschließen
* Die bei Anwendungsversuchen verwendeten Container

## <a name="YARNAppsAndLogs"></a>YARN-Anwendungen und -Protokolle

YARN unterstützt mehrere Programmierungsmodelle (u. a. MapReduce), indem die Ressourcenverwaltung von der Zeitplanung/Überwachung von Anwendungen getrennt wird. Dies erfolgt über einen globalen *ResourceManager* (RM), workerknotenbezogene *NodeManager* (NMs) und anwendungsbezogene *ApplicationMasters* (AMs). Der anwendungsbezogene AM handelt Ressourcen (CPU, Arbeitsspeicher, Datenträger, Netzwerk) für die Ausführung Ihrer Anwendung mit dem RM aus. Der RM arbeitet mit NMs zusammen, um diese Ressourcen zu gewähren, die als *Container* zugewiesen werden. Der AM ist zuständig für die Nachverfolgung des Status der Container, die ihm vom RM zugewiesen wurden. Je nach Art der Anwendung kann diese viele Container benötigen.

Darüber hinaus kann jede Anwendung aus mehreren *Anwendungsversuchen* mit dem Zweck bestehen, die Anwendung bei Auftreten von Abstürzen oder Unterbrechungen der Kommunikation zwischen einem AM und RM abzuschließen. Demzufolge werden Container einem bestimmten Versuch einer Anwendung zugewiesen. In gewisser Weise stellt ein Container den Kontext für eine Basiseinheit von Aufgaben bereit, die von einer YARN-Anwendung ausgeführt werden. Sämtliche Aufgaben, die im Kontext eines Containers ausgeführt werden, erfolgen auf dem einzelnen Arbeitsknoten, dem der Container zugeordnet wurde. Weitere Informationen finden Sie unter [YARN-Konzepte][YARN-concepts].

Anwendungsprotokolle (und dazugehörige Containerprotokolle) sind für das Beheben von Problemen bei Hadoop-Anwendungen besonders wichtig. YARN bietet mit seinem Feature [Log Aggregation][log-aggregation] ein nützliches Gerüst für das Sammeln, Zusammenführen und Speichern von Anwendungsprotokollen. Durch "Log Aggregation" lässt sich der Zugriff auf Anwendungsprotokolle besser steuern, denn Protokolle für alle Container auf einem Arbeitsknoten werden zusammengeführt und als zentrale Protokolldatei pro Workerknoten im Standarddateisystem gespeichert, nachdem eine Anwendung abgeschlossen wurde. Ihre Anwendung mag Hunderte oder Tausende von Containern verwenden, doch Protokolle für alle auf einem einzelnen Arbeitsknoten vorhandenen Container werden zu einer zentralen Datei zusammengeführt, was in einer Protokolldatei pro Arbeitsknoten mündet, die von Ihrer Anwendung genutzt werden. Log Aggregation ist für HDInsight-Cluster (ab Version 3.0) standardmäßig aktiviert. Zusammengeführte Protokolle befinden sich im Standardcontainer Ihres Clusters am folgenden Speicherort:

	wasb:///app-logs/<user>/logs/<applicationId>

An diesem Speicherort ist *user* der Name des Benutzers, der die Anwendung gestartet hat. *applicationId* ist der eindeutige Bezeichner einer Anwendung gemäß der Zuweisung durch den YARN Resource Manager (RM).

Die zusammengeführten Protokolle sind nicht unmittelbar lesbar, da sie in [TFile][T-file] geschrieben werden, einem nach Container indiziertem [Binärformat][binary-format]. Sie müssen die YARN-ResourceManager-Protokolle oder CLI-Tools verwenden, um diese Protokolle für relevante Anwendungen oder Container im Nur-Text-Format anzuzeigen.

##YARN-CLI-Tools

Zur Verwendung der YARN-CLI-Tools müssen Sie zuerst über SSH eine Verbindung mit dem HDInsight-Cluster herstellen. Verwenden Sie eines der folgenden Dokumente, um Informationen zur Verwendung von SSH mit HDInsight zu erhalten:

- [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

- [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
	
Sie können diese Protokolle im Nur-Text-Format anzeigen, indem Sie einen der folgenden Befehle ausführen:

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
	
Beim Ausführen dieser Befehle müssen Sie &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> und &ltworker-node-address> angeben.

##YARN-ResourceManager-Benutzeroberfläche

Die YARN-ResourceManager-Benutzeroberfläche wird im Hauptknoten des Clusters ausgeführt, und der Zugriff erfolgt über die Ambari-Webbenutzeroberfläche; Sie müssen jedoch erst einen [SSH-Tunnel erstellen](hdinsight-linux-ambari-ssh-tunnel.md), bevor Sie auf die ResourceManager-Benutzeroberfläche zugreifen können.

Nachdem Sie einen SSH-Tunnel erstellt haben, zeigen Sie die YARN-Protokolle mithilfe folgender Schritte an:

1. Navigieren Sie in Ihrem Webbrowser zu https://CLUSTERNAME.azurehdinsight.net. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.

2. Wählen Sie aus der Liste der Dienste auf der linken Seite den Dienst __YARN__ aus.

	![Ausgewählter YARN-Dienst](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. Wählen Sie aus der Dropdownliste __QuickLinks__ einen der Clusterhauptknoten und dann __ResourceManager-Protokoll__ aus.

	![YARN-QuickLinks](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
	
	Es wird eine Liste mit Links zu YARN-Protokollen angezeigt.

[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=AcomDC_0211_2016-->