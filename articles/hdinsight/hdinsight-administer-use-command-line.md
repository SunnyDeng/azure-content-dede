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
	ms.date="11/03/2015"
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]

Erfahren Sie, wie Sie mit der [Azure-Befehlszeilenschnittstelle](xplat-cli-install.md) Hadoop-Cluster in Azure HDInsight verwalten können. Die Azure-CLI ist in Node.js implementiert. Sie kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux.

Die Azure-Befehlszeilenschnittstelle ist Open Source. Der Quellcode wird auf GitHub unter <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a> verwaltet.

In diesem Artikel wird lediglich die Verwendung der Azure-CLI mit HDInsight behandelt. Eine allgemeine Anleitung zur Verwendung der Azure-CLI finden Sie unter [Installieren und Konfigurieren der Azure-CLI][azure-command-line-tools].


##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure-CLI** – Informationen zur Installation und Konfiguration finden Sie unter [Installieren und Konfigurieren der Azure-CLI](../xplat-cli-install.md).
- Erstellen Sie mit dem folgenden Befehl **eine Verbindung mit Azure**:

		azure login

	Weitere Informationen zur Authentifizierung mit einem Geschäfts- oder Schulkonto finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](xplat-cli-connect.md).
	
- **Wechseln Sie mit dem folgenden Befehl in den Azure-Ressourcen-Manager-Modus**:

		azure config mode arm


##Erstellen von Clustern

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

Sie benötigen zum Erstellen eines HDInsight-Clusters ein Azure-Ressourcen-Manager- und Azure-Blobspeicherkonto. Zum Erstellen eines HDInsight-Clusters müssen Sie Folgendes angeben:

- **Azure-Ressourcengruppe**: Ein Data Lake Analytics-Konto muss in einer Azure-Ressourcengruppe erstellt werden. Mit dem Azure-Ressourcen-Manager können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen. 

	So listen Sie die Ressourcengruppen in Ihrem Abonnement auf:
	
		azure group list 
	
	So erstellen Sie eine neue Ressourcengruppe:
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight-Clustername**

- **Standort**: Eines der Azure-Datencenter, die HDInsight-Cluster unterstützen. Eine Liste unterstützter Standorte finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Standardspeicherkonto**: HDInsight verwendet einen Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können.

	So erstellen Sie ein neues Azure-Speicherkonto
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE]Das Speicherkonto muss sich im selben Datencenter wie HDInsight befinden. Der Speicherkontotyp darf nicht ZRS sein, da ZRS keine Tabellen unterstützt.

	Informationen zum Erstellen von Azure-Speicherkonten im Azure-Vorschauportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].
	
	Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Ausführliche Informationen zum Abrufen dieser Informationen über das Azure-Vorschauportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount] im Abschnitt „Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln“.

- **(Optional) Standardblobcontainer**: Der Befehl **azure hdinsight cluster create** erstellt den Container, falls noch nicht vorhanden. Falls Sie den Container zuvor erstellen möchten, können Sie den folgenden Befehl verwenden:

	azure storage container create --account-name "<Storage Account Name>" --account-key <StorageAccountKey> [Containername]

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<Storage Account Name>" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Erstellen von Clustern mithilfe von Konfigurationsdateien
Normalerweise erstellen Sie einen HDInsight-Cluster, führen Aufträge in ihm aus und löschen den Cluster anschließend, um die Kosten zu senken. Über die Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, damit Sie sie wiederverwenden können, wenn Sie einen Cluster erstellen.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName ""<Storage Account Name>".blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName ""<Storage Account Name>".blob.core.windows.net"
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


##Löschen von Clustern
Mit dem folgenden Befehl können Sie ein Cluster löschen:

	azure hdinsight cluster delete <ClusterName>

##Skalieren von Clustern

Führen Sie den folgenden Befehl auf einem Clientcomputer aus, um die Hadoop-Clustergröße mithilfe von Azure PowerShell zu ändern:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>

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

<!---HONumber=Nov15_HO2-->