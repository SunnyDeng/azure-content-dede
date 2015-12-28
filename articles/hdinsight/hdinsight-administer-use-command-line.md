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
	ms.date="12/16/2015"
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]

Hier erfahren Sie, wie Sie mit der [Azure-Befehlszeilenschnittstelle](xplat-cli-install.md) Hadoop-Cluster in Azure HDInsight verwalten. Die Azure-CLI ist in Node.js implementiert. Sie kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux.

In diesem Artikel wird lediglich die Verwendung der Azure-CLI mit HDInsight behandelt. Eine allgemeine Anleitung zur Verwendung der Azure-CLI finden Sie unter [Installieren und Konfigurieren der Azure-CLI][azure-command-line-tools].

##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure-CLI** – Informationen zur Installation und Konfiguration finden Sie unter [Installieren und Konfigurieren der Azure-CLI](../xplat-cli-install.md).
- Stellen Sie über den folgenden Befehl **eine Verbindung mit Azure** her:

		azure login

	Weitere Informationen zur Authentifizierung mit einem Geschäfts- oder Schulkonto finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](xplat-cli-connect.md).
	
- Wechseln Sie mit dem folgenden Befehl in den **Azure-Ressourcen-Manager-Modus**:

		azure config mode arm

Um Hilfe zu erhalten, verwenden Sie die Option **-h**. Beispiel:

	azure hdinsight cluster create -h
	
##Erstellen von Clustern

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

Sie benötigen zum Erstellen eines HDInsight-Clusters ein Azure-Ressourcen-Manager- und Azure-Blobspeicherkonto. Zum Erstellen eines HDInsight-Clusters müssen Sie Folgendes angeben:

- **Azure-Ressourcengruppe**: Ein Data Lake Analytics-Konto muss in einer Azure-Ressourcengruppe erstellt werden. Mit dem Azure-Ressourcen-Manager können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen. 

	So führen Sie die Ressourcengruppen in Ihrem Abonnement auf:
	
		azure group list 
	
	So erstellen Sie eine neue Ressourcengruppe:
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight-Clustername**

- **Standort**: Eines der Azure-Datencenter, das HDInsight-Cluster unterstützt. Eine Liste unterstützter Standorte finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Standardspeicherkonto**: HDInsight verwendet als Standarddateisystem einen Azure-Blobspeichercontainer. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können.

	So erstellen Sie ein neues Azure-Speicherkonto:
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE]Das Speicherkonto muss sich im selben Datencenter wie HDInsight befinden. Der Speicherkontotyp darf nicht ZRS sein, da ZRS keine Tabellen unterstützt.

	Informationen zum Erstellen von Azure-Speicherkonten im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].
	
	Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Ausführliche Informationen zum Abrufen dieser Informationen über das Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount] im Abschnitt "Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln".

- **(Optional) Standardblobcontainer**: Der Befehl **azure hdinsight cluster create** erstellt den Container, sofern dieser noch nicht vorhanden ist. Falls Sie den Container zuvor erstellen möchten, können Sie den folgenden Befehl verwenden:

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

Sobald Sie Ihr Speicherkonto vorbereitet haben, können Sie einen Cluster erstellen.

	azure hdinsight cluster create --resource-group <Resource Group Name> --clusterName <Cluster Name> --location <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --storageAccountName <Default Storage Account Name> --storageAccountKey <Storage Account Key> --storageContainer <Default Storage Container> --username <HDInsight Cluster Username> --password <HDInsight Cluster Password> --sshUserName <SSH Username> --sshPassword <SSH User Password> --workerNodeCount <Number of Worker Nodes>


##Erstellen von Clustern mithilfe von Konfigurationsdateien
Normalerweise erstellen Sie einen HDInsight-Cluster, führen Aufträge dafür aus und löschen den Cluster anschließend wieder, um die Kosten möglichst gering zu halten. Über die Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um sie bei der nächsten Clustererstellung erneut verwenden zu können.

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

Beispiel: Erstellen einer Konfigurationsdatei mit einer Skriptaktion, die beim Erstellen eines Clusters ausgeführt wird.

	hdinsight config create "C:\myFiles\configFile.config"
	hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"

##Erstellen von Clustern mithilfe von Skriptaktionen

Beispiel:

	azure hdinsight cluster create -g mahirg001 -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01
	
Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

##Auflisten und Anzeigen von Clusterdetails
Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Löschen von Clustern
Mit dem folgenden Befehl können Sie ein Cluster löschen:

	azure hdinsight cluster delete <Cluster Name>

##Skalieren von Clustern

So ändern Sie die Hadoop-Clustergröße:

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## Aktivieren/Deaktivieren des HTTP-Zugriffs für einen Cluster

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## Aktivieren/Deaktivieren des RDP-Zugriffs für einen Cluster

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##Nächste Schritte
Sie sind nun in der Lage, verschiedene Verwaltungsaufgaben für HDInsight-Cluster auszuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit dem Azure-Portal][hdinsight-admin-portal]
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

<!---HONumber=AcomDC_1217_2015-->