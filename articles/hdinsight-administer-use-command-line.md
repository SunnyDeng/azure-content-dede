<properties 
	pageTitle="Verwalten von Hadoop-Clustern mit plattformübergreifender Befehlszeile | Azure" 
	description="Erfahren Sie, wie Sie die plattformübergreifende Befehlszeilenschnittstelle verwenden, um Hadoop-Cluster in HDInsight auf jeder Plattform zu verwalten, die Node.js unterstützt, einschließlich Windows, Mac und Linux.." 
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
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit der plattformübergreifenden Befehlszeilenschnittstelle

In diesem Artikel erfahren Sie, wie Sie Hadoop-Cluster in HDInsight mit der plattformübergreifenden Befehlszeilenschnittstelle verwalten können. Das Befehlszeilentool ist in Node.js implementiert. Dieses Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux. 

Das Befehlszeilentool ist Open Source.  Der Quellcode wird auf GitHub unter <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>. 

Dieser Artikel behandelt nur die Befehlszeilenschnittstelle unter Windows. Allgemeine Anleitungen zum Verwenden der Befehlszeilenschnittstelle finden Sie unter [Gewusst wie: Verwenden der Azure-Befehlszeilentools für Mac und Linux][azure-command-line-tools]. Umfassendes Referenzmaterial finden Sie unter [Azure-Befehlszeilentool für Mac und Linux][azure-command-line-tool].


**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement**. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].

##Themen in diesem Artikel

* [Installation](#installation)
* [Herunterladen und Importieren der Veröffentlichungseinstellungen für das Azure-Konto](#importsettings)
* [Bereitstellen eines Clusters](#provision)
* [Bereitstellen eines Clusters mithilfe der Konfigurationsdatei](#provisionconfigfile)
* [Auflisten und Anzeigen von Clustern](#listshow)
* [Löschen eines Clusters](#delete)
* [Nächste Schritte](#nextsteps)

##<a id="installation"></a> Installation
Die Befehlszeilenschnittstelle kann mithilfe von *Node.js Package Manager (NPM)* oder Windows Installer installiert werden.

**So installieren Sie die Befehlszeilenschnittstelle mit NPM**

1.	Navigieren Sie zu **www.nodejs.org**.
2.	Klicken Sie auf **Installieren**, und befolgen die Anweisungen mit den Standardeinstellungen.
3.	Öffnen Sie die **Eingabeaufforderung** (oder *Azure Command Prompt* oder *Developer Command Prompt for VS2012*) von Ihrer Arbeitsstation aus.
4.	Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein.

		npm install -g azure-cli

	> [AZURE.NOTE] Falls Sie einen Fehler erhalten und der NPM-Befehl nicht gefunden wird, vergewissern Sie sich, dass die PATH-Umgebungsvariable die folgenden Pfade enthält: <i>C:\Programme (x86)\nodejs;C:\Benutzer[Benutzername]\AppData\Roaming\npm</i> oder <i>C:\Programme\nodejs;C:\Benutzer[Benutzername]\AppData\Roaming\npm</i>

5.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den *-h*-Schalter auf unterschiedlichen Ebenen verwenden, um die Hilfeinformationen anzuzeigen.  Beispiel:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So installieren Sie die Befehlszeilenschnittstelle mit Windows Installer**

1.	Navigieren Sie zu **http://azure.microsoft.com/downloads/**.
2.	Führen Sie einen Bildlauf nach unten bis zum Abschnitt **Befehlszeilentools** durch, klicken Sie auf **Plattformübergreifende Befehlszeilenschnittstelle**, und befolgen Sie die Anweisungen des Webplattform-Installers.

##<a id="importsettings"></a> Herunterladen und Importieren der Veröffentlichungseinstellungen für das Azure-Konto

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [AZURE.NOTE] Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Sie sollten die Datei entweder löschen oder zusätzliche Maßnahmen ergreifen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.


**So laden Sie die Veröffentlichungseinstellungen herunter und importieren diese**

1.	Öffnen Sie eine **Eingabeaufforderung**.
2.	Führen Sie den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei herunterzuladen.

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Der Befehl zeigt die Anweisungen für den Download der Datei an, inklusive einer URL.

3.	Öffnen Sie **Internet Explorer**, und navigieren Sie zu der URL, die im Eingabeaufforderungsfenster aufgeführt ist.
4.	Klicken Sie auf **Speichern**, um die Datei auf der Arbeitsstation zu speichern.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <file>

	Für den vorigen Screenshot wurde die Einstellungsveröffentlichungsdatei im Ordner C:\HDInsight auf der Arbeitsstation gespeichert.


##<a id="provision"></a> Bereitstellen eines HDInsight-Clusters

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können. 

Nachdem Sie die Datei mit Veröffentlichungseinstellungen importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE] Das Speicherkonto muss sich im selben Rechenzentrum befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

><ul>
<li>Südostasien</li>
<li>Nordeuropa</li>
<li>Westeuropa</li>
<li>USA (Ost)</li>
<li>USA (West)</li>
</ul>


Informationen zum Erstellen von Azure-Speicherkonten mit dem Azure-Verwaltungsportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

Weitere Informationen zum Abrufen dieser Informationen mithilfe des Verwaltungsportals finden Sie im Abschnitt *Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].


Der Befehl  *azure hdinsight cluster create* erstellt den Container, wenn er nicht vorhanden ist. Falls Sie den Container zuvor erstellen möchten, können Sie den folgenden Befehl verwenden:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen: 

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##<a id="provisionconfigfile"></a> Bereitstellen eines HDInsight-Clusters mit einer Konfigurationsdatei
Normalerweise stellen Sie einen HDInsight-Cluster bereit, führen die entsprechenden Aufgaben aus und löschen das Cluster anschließend, um die Kosten zu senken. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.  
 
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


##<a id="listshow"></a> Auflisten und Anzeigen von Clusterdetails
Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:
	
	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>
	
![HDI.CLIListCluster][image-cli-clusterlisting]


##<a id="delete"></a> Löschen eines Clusters
Mit dem folgenden Befehl können Sie einen Cluster löschen:

	azure hdinsight cluster delete <ClusterName>




##<a id="nextsteps"></a> Nächste Schritte
Sie sind nun in der Lage, verschiedene Verwaltungsaufgaben für HDInsight-Cluster auszuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit dem Verwaltungsportal][hdinsight-admin-portal]
* [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Gewusst wie: Verwenden der Azure-Befehlszeilentools für Mac und Linux][azure-command-line-tools]
* [Azure-Befehlszeilentool für Mac und Linux][azure-command-line-tool]


[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png 
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "List and show clusters"


<!--HONumber=42-->
