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
	ms.date="01/04/2016"
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

Informationen hierzu finden Sie unter [Erstellen Windows-basierter Hadoop-Cluster in HDInsight mit der Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-windows-clusters-cli.md).

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

<!---HONumber=AcomDC_0107_2016-->