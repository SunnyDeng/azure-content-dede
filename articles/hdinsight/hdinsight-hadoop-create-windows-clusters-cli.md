<properties
   pageTitle="Erstellen Windows-basierter Hadoop-Cluster in HDInsight mit der Azure-Befehlszeilenschnittstelle"
   	description="Erfahren Sie, wie Sie Cluster für Azure HDInsight mithilfe der Azure-Befehlszeilenschnittstelle erstellen."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/04/2016"
   ms.author="jgao"/>

# Erstellen Windows-basierter Hadoop-Cluster in HDInsight mit der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-selector-create-clusters.md)]

Erfahren Sie, wie Sie HDInsight-Cluster mit der Azure-Befehlszeilenschnittstelle erstellen. Andere Tools und Features zur Clustererstellung finden Sie, indem Sie oben auf dieser Seite auf die Registerkartenauswahl klicken, oder unter [Methoden zur Clustererstellung](hdinsight-provision-clusters.md#cluster-creation-methods).

###Voraussetzungen:

Bevor Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- **Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure-CLI** – Informationen zur Installation und Konfiguration finden Sie unter [Installieren und Konfigurieren der Azure-CLI](../xplat-cli-install.md).

##Herstellen einer Verbindung mit Azure

Stellen Sie über den folgenden Befehl eine Verbindung mit Azure her:

	azure login

Weitere Informationen zur Authentifizierung mit einem Geschäfts- oder Schulkonto finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](../xplat-cli-connect.md).
	
Wechseln Sie mit dem folgenden Befehl in den ARM-Modus:

	azure config mode arm

Um Hilfe zu erhalten, verwenden Sie die Option **-h**. Beispiel:

	azure hdinsight cluster create -h
	
##Erstellen von Clustern

Sie benötigen zum Erstellen eines HDInsight-Clusters ein ARM- (Azure-Ressourcen-Manager) und ein Azure-Blobspeicherkonto. Zum Erstellen eines HDInsight-Clusters müssen Sie Folgendes angeben:

- **Azure-Ressourcengruppe**: Ein Data Lake Analytics-Konto muss in einer Azure-Ressourcengruppe erstellt werden. Mit dem Azure-Ressourcen-Manager können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen. 

	So führen Sie die Ressourcengruppen in Ihrem Abonnement auf:
	
		azure group list 
	
	So erstellen Sie eine neue Ressourcengruppe:
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight-Clustername**

- **Standort**: Eines der Azure-Datencenter, das HDInsight-Cluster unterstützt. Eine Liste unterstützter Standorte finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Standardspeicherkonto**: HDInsight verwendet als Standarddateisystem einen Azure-Blobspeichercontainer. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können.

	So erstellen Sie ein neues Azure-Speicherkonto:
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE] Das Speicherkonto muss sich im selben Datencenter wie HDInsight befinden. Der Speicherkontotyp darf nicht ZRS sein, da ZRS keine Tabellen unterstützt.

	Informationen zum Erstellen von Azure-Speicherkonten im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].
	
	Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Ausführliche Informationen zum Abrufen dieser Informationen über das Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount] im Abschnitt „Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln“.

- **(Optional) Standardblobcontainer**: Der Befehl **azure hdinsight cluster create** erstellt den Container, sofern dieser noch nicht vorhanden ist. Falls Sie den Container zuvor erstellen möchten, können Sie den folgenden Befehl verwenden:

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

Sobald Sie Ihr Speicherkonto vorbereitet haben, können Sie einen Cluster erstellen.

    
    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##Erstellen von Clustern mithilfe von Konfigurationsdateien
Normalerweise erstellen Sie einen HDInsight-Cluster, führen Aufträge dafür aus und löschen den Cluster anschließend wieder, um die Kosten möglichst gering zu halten. Über die Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um sie bei der nächsten Clustererstellung erneut verwenden zu können.

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

Beispiel: Erstellen einer Konfigurationsdatei mit einer Skriptaktion, die beim Erstellen eines Clusters ausgeführt wird.

	azure hdinsight config create "C:\myFiles\configFile.config"
	azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
	azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##Erstellen von Clustern mithilfe von Skriptaktionen

Erstellen einer Konfigurationsdatei mit einer Skriptaktion, die beim Erstellen eines Clusters ausgeführt wird.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Erstellen eines Clusters mit einer Skriptaktion

	azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01
	
	
Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Linux)](hdinsight-hadoop-customize-cluster.md).


## Erstellen von Clustern mithilfe von ARM-Vorlagen

Sie können die Befehlszeilenschnittstelle zum Erstellen von Clustern verwenden, indem ARM-Vorlagen aufgerufen werden. Weitere Informationen finden Sie unter [Bereitstellen über die Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## Weitere Informationen

- [Erste Schritte mit Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) – Erfahren Sie, wie Sie die Arbeit mit Ihrem HDInsight-Cluster aufnehmen können.
- [Programmgesteuerte Übermittlung von Hadoop-Jobs](hdinsight-submit-hadoop-jobs-programmatically.md) – Erfahren Sie, wie Sie Aufträge programmgesteuert an HDInsight übermitteln können.
- [Verwalten von Hadoop-Clustern in HDInsight mit der Azure-Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Dienstverwaltung](../virtual-machines/virtual-machines-command-line-tools.md)

<!---HONumber=AcomDC_0224_2016-->