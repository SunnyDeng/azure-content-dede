<properties 
	pageTitle="Verwalten von Hadoop-Clustern über eine Befehlszeilenschnittstelle | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die plattformübergreifende Befehlszeilenschnittstelle für die Verwaltung von Hadoop-Clustern in HDInsight auf einer beliebigen Plattform verwenden können, die Node.js unterstützt, einschließlich Windows, Mac und Linux." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight über eine plattformübergreifende Befehlszeilenschnittstelle

Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows Hadoop-Cluster in Azure HDInsight verwalten können. Die Azure-Befehlszeilenschnittstelle ist in Node.js implementiert. Sie kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux.

Die Azure-Befehlszeilenschnittstelle ist Open Source. Der Quellcode liegt auf GitHub unter <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>.

Dieser Artikel beschreibt nur die Verwendung der Befehlszeilenschnittstelle unter Windows. Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux][azure-command-line-tools].


##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement** – Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].

##Installation
Sie können die Befehlszeilenschnittstelle entweder über den *Node.js-Paketmanager (NPM)* oder über den Windows Installer installieren.

**So installieren Sie die Befehlszeilenschnittstelle mit dem NPM**

1.	Öffnen Sie die Webseite **www.nodejs.org**.
2.	Klicken Sie auf **INSTALL**, und befolgen Sie die Anweisungen unter Beibehaltung der Standardeinstellungen.
3.	Öffnen Sie die **Eingabeaufforderung** (bzw. **Azure-Eingabeaufforderung** oder **Developer-Eingabeaufforderung für VS2012**) auf Ihrer Arbeitsstation.
4.	Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein.

		npm install -g azure-cli

	> [AZURE.NOTE]Wenn die Fehlermeldung angezeigt wird, dass der NPM-Befehl nicht gefunden wurde, überprüfen Sie, ob sich die folgenden Pfade in der Umgebungsvariablen **PATH** befinden: <i>C:\\Program Files (X 86) \\nodejs; C:\\Users[Benutzername]\\AppData\\Roaming\\npm</i> oder <i>C:\\Program Files\\nodejs; C:\\Users[Benutzername]\\AppData\\Roaming\\npm</i>


5.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter **-h** auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Zum Beispiel:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So installieren Sie die Befehlszeilenschnittstelle mit dem Windows Installer**

1.	Navigieren Sie zu **http://azure.microsoft.com/downloads/**.
2.	Scrollen Sie nach unten zum Bereich **Befehlszeilentools**, klicken Sie auf **Plattformübergreifende Befehlszeilenschnittstelle** und führen Sie den Assistenten des Webplattform-Installer aus.

##Herunterladen und Importieren der publishsettings-Datei für das Azure-Konto

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen die publishsettings-Datei mit den Veröffentlichungseinstellungen nur einmal importieren.

> [AZURE.NOTE]Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Sie sollten die Datei entweder löschen oder zusätzliche Maßnahmen ergreifen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Ändern Sie unter Windows die Ordnereigenschaften, oder verwenden Sie die BitLocker-Laufwerkverschlüsselung.


**So laden Sie die publishsettings-Datei herunter und importieren sie**

1.	Öffnen Sie eine Eingabeaufforderung.
2.	Führen Sie den folgenden Befehl aus, um die publishsettings-Datei herunterzuladen:

		azure account download
 
	![Befehlszeilenschnittstelle lädt Azure-Konto herunter.][image-cli-account-download-import]

	Der Befehl zeigt die Anweisungen für den Download der Datei an, inklusive einer URL.

3.	Öffnen Sie Internet Explorer, und navigieren Sie zu der URL, die im Eingabeaufforderungsfenster aufgeführt ist.
4.	Klicken Sie auf **Speichern** und speichern Sie die Datei auf Ihrer Arbeitsstation.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <file>

	Für den vorherigen Screenshot wurde die publishsettings-Datei im Ordner "C:\\HDInsight" auf der Arbeitsstation gespeichert.


##Bereitstellen eines HDInsight-Clusters

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


HDInsight verwendet Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können.

Nachdem Sie die publishsettings-Datei importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]Das Speicherkonto muss sich im selben Datencenter wie HDInsight befinden.


Informationen zum Erstellen von Azure-Speicherkonten im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Ausführliche Informationen zum Abrufen dieser Informationen im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount] im Abschnitt "Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln".


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

* [Verwalten von HDInsight mit dem Azure-Portal][hdinsight-admin-portal]
* [Verwalten von HDInsight mit Azure PowerShell][hdinsight-admin-powershell]
* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows][azure-command-line-tools].


[azure-command-line-tools]: xplat-cli.md
[azure-create-storageaccount]: storage/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Auflisten und Anzeigen von Clustern"

<!--HONumber=54-->