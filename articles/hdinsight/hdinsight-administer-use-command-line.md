<properties
	pageTitle="Verwalten von Hadoop-Clustern mit der Azure-CLI | Microsoft Azure"
	description="Verwalten von Hadoop-Clustern in HDIsight mit der Azure-CLI"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/17/2015"
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit der Azure-CLI

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]

Erfahren Sie, wie Sie die Befehlszeilenschnittstelle von Azure (Azure-CLI) zur Verwaltung von Hadoop-Clustern in Azure HDInsight verwenden. Die Azure-CLI ist in Node.js implementiert. Sie kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux.

Die Azure-Befehlszeilenschnittstelle ist Open Source. Der Quellcode wird auf GitHub unter <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a> verwaltet.

In diesem Artikel wird lediglich die Verwendung der Azure-CLI mit HDInsight behandelt. Eine allgemeine Anleitung zur Verwendung der Azure-CLI finden Sie unter [Verwenden der Azure-CLI][azure-command-line-tools].


##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Azure-CLI** – Informationen zur Installation und Konfiguration finden Sie unter [Installieren und Konfigurieren der Azure-CLI](../xplat-cli.md).

##Installation

Sofern noch nicht geschehen, installieren und konfigurieren Sie die Befehlszeilenschnittstelle von Azure (Azure-CLI) nach den Anweisungen im Dokument [Installieren und Konfigurieren der Azure-CLI](../xplat-cli.md).

##Bereitstellen eines HDInsight-Clusters

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


HDInsight verwendet Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können.

Nachdem Sie die publishsettings-Datei importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]Das Speicherkonto muss sich im selben Datencenter wie HDInsight befinden.


Informationen zum Erstellen von Azure-Speicherkonten mit dem Azure-Vorschauportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Ausführliche Informationen zum Abrufen dieser Informationen über das Azure-Vorschauportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount] im Abschnitt „Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln“.


Der Befehl **azure hdinsight cluster create** erstellt den Container, falls dieser nicht existiert. Falls Sie den Container zuvor erstellen möchten, können Sie den folgenden Befehl verwenden:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Bereitstellung eines HDInsight-Clusters mit einer Konfigurationsdatei
Normalerweise stellen Sie ein HDInsight-Cluster bereit, führen die entsprechenden Aufgaben aus und löschen das Cluster anschließend, um die Kosten zu senken. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"

	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

	azure hdinsight cluster create --config <file>



![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##Auflisten und Anzeigen von Clusterdetails
Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Löschen eines Clusters
Mit dem folgenden Befehl können Sie ein Cluster löschen:

	azure hdinsight cluster delete <ClusterName>




##Nächste Schritte
Sie sind nun in der Lage, verschiedene Verwaltungsaufgaben für HDInsight-Cluster auszuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit dem Azure-Vorschauportal][hdinsight-admin-portal]
* [Verwalten von HDInsight mit Azure PowerShell][hdinsight-admin-powershell]
* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Verwenden der Azure-CLI][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Auflisten und Anzeigen von Clustern"

<!---HONumber=Sept15_HO4-->