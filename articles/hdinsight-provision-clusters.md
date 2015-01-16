<properties urlDisplayName="HDInsight Administration" pageTitle="Bereitstellen von Hadoop-Clustern in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Erfahren Sie, wie Sie Cluster für Azure HDInsight im Verwaltungsportal oder mithilfe von PowerShell oder der Befehlszeile bereitstellen können." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="jgao" />

#Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight

Dieser Artikel beschreibt die verschiedenen Möglichkeiten für die benutzerdefinierte Bereitstellung eines Hadoop-Clusters in Azure HDInsight: Azure-Verwaltungsportal, PowerShell, Befehlszeilentools oder HDInsight .NET SDK. Dieser Artikel behandelt die Bereitstellung eines Hadoop-Clusters. Informationen zur Bereitstellung eines HBase-Clusters finden Sie unter [Bereitstellen eines HBase-Clusters in HDInsight][hdinsight-hbase-custom-provision]. Siehe <a href="http://go.microsoft.com/fwlink/?LinkId=510237">"Was ist der Unterschied zwischen den Hadoop und HBase?",</a> um zu verstehen, warum Sie einer dieser Lösungen den Vorzug geben sollten.

## Was ist ein HDInsight-Cluster?

Vielleicht haben Sie sich schon gefragt, warum wir jedesmal Cluster erwähnen, wenn wir über Hadoop oder Big Data sprechen. Das liegt daran, dass Hadoop die verteilte Verarbeitung großer Datenmengen auf mehreren Knoten eines Clusters ermöglicht. Cluster haben eine Master/Detail-Architektur mit einem Primärknoten (oder Stamm- bzw. Namensknoten) und einer beliebigen Anzahl von Sekundärknoten (oder Datenknoten). Weitere Informationen finden Sie unter [Apache Hadoop][apache-hadoop].

![HDInsight Cluster][img-hdi-cluster]

HDInsight-Cluster abstrahieren die Hadoop-Implementierungsdetails, sodass Sie sich keine Gedanken über die Kommunikation zwischen den einzelnen Clusterknoten machen müssen. Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. Weitere Informationen finden Sie unter [Einführung in Hadoop in HDInsight][hadoop-hdinsight-intro]. Die zu verarbeitenden Daten liegen im Azure-Blob-Speicher, auch bezeichnet als "Azure-Speicher-Blob" (oder WASB) im Kontext von HDInsight. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage].

Dieser Artikel beschreibt die verschiedenen Möglichkeiten bei der Einrichtung eines Clusters. Falls Sie auf der Suche nach einem Schnelleinstieg in die Clusterbereitstellung sind, lesen Sie [Erste Schritte mit Azure HDInsight][hdinsight-get-started].

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Die HDInsight PowerShell-Cmdlets führen Aufgaben für Ihr Abonnement aus. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].

##Themen in diesem Artikel

* [Konfigurationsoptionen](#configuration)
* [Verwenden des Azure-Verwaltungsportals](#portal)
* [Verwenden von Azure PowerShell](#powershell)
* [Die plattformübergreifende Befehlszeile](#cli)
* [Verwenden des HDInsight .NET SDK](#sdk)
* [Nächste Schritte](#nextsteps)

##<a id="configuration"></a>Konfigurationsoptionen

###Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure-Blobspeicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional zusätzliche Blobs angeben, die ebenfalls Ihrem Cluster zugeordnet werden.

Weitere Informationen zu sekundären Blob-Speichern finden Sie unter [Verwenden des Azure-Blob-Speichers mit HDInsight](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-use-blob-storage/).

###Metastore

Der Metastore enthält Informationen über Hive-Tabellen, Partitionen, Schemas, Spalten usw.. Hive verwendet diese Informationen, um die Daten im HDFS zu lokalisieren (bzw. WASB für HDInsight). Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank.

Bei der Bereitstellung eines HDInsight-Clusters können Sie eine SQL-Datenbank angeben, die als Metastore für Hive verwendet werden soll. Auf diese Weise bleiben die Metadaten-Informationen erhalten, wenn Sie ein Cluster löschen, da diese extern in einer SQL-Datenbank liegen.

### Clusteranpassung

Sie können weitere Komponenten installieren oder die Konfiguration des Clusters anpassen, während ein HDInsight-Cluster erstellt wird. Cluster können angepasst werden, indem Skripts erstellt werden, die während der Erstellung des Clusters ausgeführt werden. Diese Skripts werden mithilfe der Option **Skriptaktion** aufgerufen. Dies ist eine Konfigurationsoption, die von HDInsight PowerShell-Cmdlets oder dem HDInsight .NET SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mit einer Skriptaktion][hdinsight-customize-cluster].


###Virtuelle Netzwerke

Ein [virtuelles Azure-Netzwerk](http://azure.microsoft.com/de-de/documentation/services/virtual-network/) bietet ein sicheres, persistentes Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerke ermöglichen Folgendes:

* Verbinden von Cloud-Ressourcen in einem privaten Netzwerk (Nur-Cloud)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

* Verbinden Ihrer Cloud-Ressourcen mit dem Netzwerk in Ihrem lokalen Rechenzentrum (Standort-zu-Standort oder Punkt-zu-Standort) mithilfe eines virtuellen privaten Netzwerks (VPN)

	Mit einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum mit dem virtuellen Azure-Netzwerk über ein Hardware-VPN oder den Routing- und RAS-Dienst verbinden

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	Mit einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen HDInsight-Cluster bereitstellen. Weitere Informationen finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](http://msdn.microsoft.com/de-de/library/azure/jj156206.aspx).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

##<a id="portal"></a> Verwenden des Azure-Verwaltungsportals

HDInsight-Cluster verwenden einen Azure-Blobspeicher-Container als Standard-Dateisystem. Sie benötigen ein Speicherkonto im entsprechenden Rechenzentrum, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage]. Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].


> [WACOM.NOTE] Derzeit können nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Nord, Mitte)**, **USA (Süd, Mitte)** HDInsight-Cluster gehostet werden.

**So erstellen Sie einen HDInsight-Cluster über die benutzerdefinierte Erstellungsoption**

1. Melden Sie sich am [Azure-Verwaltungsportal ][azure-management-portal] an.
2. Klicken Sie links unten auf **NEU**, dann auf auf **DATA SERVICES**, danach auf **HDINSIGHT** und schließlich auf **BENUTZERDEFINIERT ERSTELLEN**.
3. Auf der Seite **Clusterdetails** können Sie die folgenden Werte auswählen bzw. eingeben:

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]

    <table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Clustername</td>
			<td><p>Der Name des Clusters. </p>
				<ul>
				<li>Der DNS-Name muss mit einem alphanumerischen Zeichen beginnen und enden und darf Bindestriche enthalten.</li>
				<li>Der Wert in diesem Feld muss zwischen 3 und 63 Zeichen lang sein.</li>
				</ul></td></tr>
		<tr><td>Clustertyp</td>
			<td>Wählen Sie unter "Clustertyp" <strong>den Eintrag "Hadoop" aus</strong>.</td></tr>
		<tr><td>HDInsight-Version</td>
			<td>Wählen Sie die Version aus. HDInsight Version 3.1 ist die Standardversion für Hadoop und verwendet Hadoop Version 2.4.</td></tr>
		</table>

	Geben Sie die Werte wie in der Tabelle gezeigt ein und klicken Sie auf den Pfeil nach rechts.

4. Geben Sie auf der Seite **Cluster konfigurieren** die folgenden Daten ein:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Datenknoten</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten. <br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td></tr>
	<tr><td>Region/virtuelles Netzwerk</td><td><p>Wählen Sie dieselbe Region wie für das Speicherkonto, das Sie im letzten Verfahren erstellt haben. In HDInsight muss das Speicherkonto sich in derselben Region befinden. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet.</p><p>Die verfügbaren Regionen sind: <strong>Ostasien</strong>, <strong>Südostasien</strong>, <strong>Nordeuropa</strong>, <strong>Westeuropa</strong>, <strong>USA (Ost)</strong>, <strong>USA (West)</strong>, <strong>USA (Mitte/Norden)</strong>, <strong>USA (Mitte/Süden)</strong><br/>Wenn Sie ein virtuelles Azure-Netzwerk erstellt haben, können Sie das Netzwerk auswählen, für dessen Nutzung der HDInsight-Cluster konfiguriert wird.</p><p>Weitere Informationen zur Erstellung virtueller Azure-Netzwerke finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/azure/jj156206.aspx">Konfigurationsaufgaben für virtuelle Netzwerke</a>.</p></td></tr>
	</table>


5. Geben Sie auf der Seite **Clusterbenutzer konfigurieren** die folgenden Daten ein:

    ![HDI.CustomCreateCluster.ClusterUser][image-hdi-customcreatecluster-clusteruser]

    <table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Benutzername</td>
			<td>Geben Sie den Benutzernamen für den HDInsight-Cluster an.</td></tr>
		<tr><td>Kennwort/Kennwort bestätigen</td>
			<td>Geben Sie das Kennwort für den HDInsight-Cluster an.</td></tr>
		<tr><td>Enter Hive/Oozie Metastore</td>
			<td>Markieren Sie dieses Kontrollkästchen, um eine SQL-Datenbank im gleichen Rechenzentrum wie der Cluster für die Verwendung als Hive-/Oozie-Metastore anzugeben. Dies ist praktisch, wenn Sie die Metadaten von Hive-/Oozie-Jobs behalten möchten, nachdem der Cluster gelöscht wurde.</td></tr>
		<tr><td>Metastore-Datenbank</td>
			<td>Geben Sie die Azure SQL-Datenbank an, die als Metastore für Hive/Oozie verwendet werden soll. Die SQL-Datenbank muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden. In diesem Feld werden nur SQL-Datenbanken aufgelistet, die sich im gleichen Rechenzentrum befinden, das Sie auf der Seite <strong>"Clusterdetails" angegeben haben</strong> .</td></tr>
		<tr><td>Datenbankbenutzer</td>
			<td>Geben Sie den SQL-Datenbankbenutzer an, der für die Verbindung zur Datenbank verwendet wird.</td></tr>
		<tr><td>Datenbank-Benutzerkennwort</td>
			<td>Geben Sie das Kennwort des SQL-Datenbankbenutzers an.</td></tr>
	</table>

	>[WACOM.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Azure SQL-Datenbank-Dashboard mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Windows Azure-Dienste** den Wert **Ja** aus, und klicken Sie auf **Speichern**..

    Klicken Sie auf den Pfeil nach rechts.


6. Geben Sie auf der Seite **Speicherkonto** den folgenden Wert ein:

    ![HDI.CustomCreateCluster.StorageAccount][image-hdi-customcreatecluster-storageaccount]

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Speicherkonto</td>
			<td>Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten:
			<ul>
				<li>Vorhandenen Speicher verwenden</li>
				<li>Neuen Speicher erstellen</li>
				<li>Speicher aus anderem Abonnement verwenden</li>
			</ul>
			</td></tr>
		<tr><td>Kontoname</td>
			<td><ul>
				<li>Falls Sie einen vorhandenen Speicher verwenden, wählen Sie unter <strong>"Kontoname"</strong>ein vorhandenes Speicherkonto aus. Die Dropdownliste enthält nur die Speicherkonten im gleichen Rechenzentrum, in dem Sie auch den Cluster eingerichtet haben.</li>
				<li>Wenn Sie <strong>"Neuen Speicher erstellen"</strong> oder <strong>"Speicher aus anderem Abonnement verwenden"</strong> wählen, müssen Sie den Speicherkontonamen angeben.</li>
			</ul></td></tr>
		<tr><td>Kontoschlüssel</td>
			<td>Wenn Sie <strong>"Speicher aus anderem Abonnement verwenden"</strong> wählen, geben Sie den Kontoschlüssel für dieses Speicherkonto an.</td></tr>
		<tr><td>Standardcontainer</td>
			<td><p>Gibt den Standardcontainer im Speicherkonto an, der als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Wenn Sie <strong>"Vorhandenen Speicher verwenden"</strong> für das Feld <strong>"Speicherkonto"</strong> ausgewählt haben und dieses Konto keine Container enthält, wird der Container standardmäßig mit demselben Namen wie der Cluster erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt. Zum Beispiel mycontainer1, mycontainer2 und so weiter. Sie können jedoch auch Container im vorhandenen Speicherkonto verwenden, die einen anderen Namen als der Cluster haben.</p>
            <p>Falls Sie einen neuen Speicher erstellen oder einen Speicher aus einem anderen Azure-Abonnement verwenden, müssen Sie den Namen des Standardcontainers angeben</p>
        </td></tr>
		<tr><td>Zusätzliche Speicherkonten</td>
			<td>HDInsight unterstützt mehrere Speicherkonten. Ein Cluster kann beliebig viele Speicherkonten verwenden. Wenn Sie den Cluster jedoch im Verwaltungsportal erstellen, können Sie aufgrund von Einschränkungen der Benutzeroberfläche maximal sieben Speicherkonten einrichten. Für jedes angegebene Speicherkonto wird eine zusätzliche Seite im Assistenten hinzugefügt, in der Sie die Kontoinformationen angeben können. Im obigen Screenshot wurde z. B. ein zusätzliches Speicherkonto ausgewählt, und Seite 5 wurde zum Dialog hinzugefügt.</td></tr>
	</table>

	Klicken Sie auf den Pfeil nach rechts.

7. Geben Sie auf der Seite **Speicherkonto** die Kontoinformationen für das zusätzliche Speicherkonto ein:

	![HDI.CustomCreateCluster.AddOnStorage][image-hdi-customcreatecluster-addonstorage]

    Hier haben Sie erneut die Auswahl zwischen einem vorhandenen Speicher, einem neu erstellten Speicher und einem Speicher aus einem anderen Azure-Abonnement. Die Angabe der Werte erfolgt auf dieselbe Weise wie im vorherigen Schritt.

    Klicken Sie auf das Häkchen, um mit der Bereitstellung des Clusters zu beginnen. Wenn die Bereitstellung abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

	> [WACOM.NOTE] Nachdem Sie ein Azure-Speicherkonto für Ihren HDInsight-Cluster ausgewählt haben, können Sie das Konto nicht mehr löschen und auch kein anderes Konto auswählen.

##<a id="powershell"></a> Verwenden der Azure PowerShell
Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Dieser Abschnitt bietet Anweisungen zum Bereitstellen eines HDInsight-Clusters. Informationen zur Konfiguration einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure-PowerShell][powershell-install-configure]. Weitere Informationen zum Verwenden von PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].

> [WACOM.NOTE] Die Skripts in diesem Abschnitt dienen zum Konfigurieren eines HDInsight-Clusters in einem virtuellen Azure-Netzwerk, jedoch nicht zur Erstellung des virtuellen Azure-Netzwerks. Informationen zur Erstellung von virtuellen Azure-Netzwerken finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](http://msdn.microsoft.com/de-de/library/azure/jj156206.aspx).

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe der PowerShell ausgeführt werden:

- Erstellen eines Azure-Speicherkontos
- Erstellen eines Azure-Blob-Containers
- Erstellen eines HDInsight-Clusters

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden.


**So erstellen Sie ein Azure-Speicherkonto**

- Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

		$storageAccountName = "<StorageAcccountName>"	# Provide a storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

	Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden PowerShell-Befehlen abrufen:

		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"

**So erstellen Sie einen Azure-Blob-Speichercontainer**

- Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

		$storageAccountName = "<StorageAccountName>"	# Provide the storage account name
		$storageAccountKey = "<StorageAccountKey>"		# Provide either primary or secondary key
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie HDInsight-Cluster bereit**

> [WACOM.NOTE] Die PowerShell-Cmdlets sind der einzige empfohlene Weg zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster.  Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden.  Die per PowerShell gesetzten Konfigurationswerte bleiben erhalten, wenn das Cluster gepatcht wird.

- Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container.
		$containerName = "<ContainerName>"				# Azure Blob container that is used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Bereitstellung des Clusters kann einige Minuten in Anspruch nehmen.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**So stellen Sie einen HDInsight-Cluster mit benutzerdefinierten Optionen bereit**

Bei der Bereitstellung eines Clusters können Sie erweiterte Optionen verwenden, wie z. B. die Verbindung mit mehr als einem Azure-Blobspeicher, Verwendung eines virtuellen Netzwerks oder einer Azure SQL-Datenbank für Hive- und Oozie-Metastores. Auf diese Weise können Sie die Lebensdauer Ihrer Daten und Metadaten von der Lebensdauer des Clusters trennen.

> [WACOM.NOTE] Die PowerShell-Cmdlets sind der einzige empfohlene Weg zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster.  Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden.  Die per PowerShell gesetzten Konfigurationswerte bleiben erhalten, wenn das Cluster gepatcht wird.

- Führen Sie die folgenden Befehle in einem Windows PowerShell-Fenster aus:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[WACOM.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Azure SQL-Datenbank-Dashboard mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Windows Azure-Dienste** den Wert **Ja** aus, und klicken Sie auf **Speichern**..

**So listen Sie HDInsight-Cluster auf**

- Führen Sie die folgenden Befehle in einer Azure PowerShell-Konsole aus, um den HDInsight-Cluster aufzulisten und zu überprüfen, ob der Cluster erfolgreich eingerichtet wurde:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Die plattformübergreifende Befehlszeile

> [WACOM.NOTE] Ab dem 29.8.2014 kann die plattformübergreifende Befehlszeilenschnittstelle nicht verwendet werden, um ein Cluster zu einem virtuellen Azure-Netzwerk zuzuordnen.

Eine weitere Möglichkeit zur Bereitstellung von HDInsight-Clustern ist die plattformübergreifende Befehlszeile. Das Befehlszeilentool ist in Node.js implementiert. Dieses Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux. Das Befehlszeilentool ist Open Source.  Der Quellcode wird auf GitHub unter <a href= "https://github.com/Azure/azure-sdk-tools-xplat">"https://github.com/Azure/azure-sdk-tools-xplat" verwaltet</a>. Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux][azure-command-line-tools]. Umfassendes Referenzmaterial finden Sie unter [Azure-Befehlszeilentool für Mac und Linux][azure-command-line-tool]. Dieser Artikel behandelt nur die Befehlszeilenschnittstelle unter Windows.

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters in der plattformübergreifenden Befehlszeile ausgeführt werden:

- Installieren der plattformübergreifenden Befehlszeile
- Download und Import der Veröffentlichungseinstellungen für das Azure-Konto
- Erstellen eines Azure-Speicherkontos
- Bereitstellen eines Clusters

Sie können die Befehlszeilenschnittstelle entweder über den Node.js-Paketmanager (NPM) oder über den Windows Installer installieren. Microsoft empfiehlt, dass Sie nur eine der beiden Optionen für die Installation verwenden.

**So installieren Sie die Befehlszeilenschnittstelle mit dem NPM**

1.	Öffnen Sie die Webseite **www.nodejs.org**.
2.	Klicken Sie auf **INSTALL** und folgen Sie den Anweisungen mit den Standardeinstellungen.
3.	Öffnen Sie die **Eingabeaufforderung** (bzw. "Azure-Eingabeaufforderung" oder "Developer-Eingabeaufforderung für VS2012") auf Ihrer Arbeitsstation.
4.	Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein.

		npm install -g azure-cli

	> [WACOM.NOTE] Falls Sie einen Fehler erhalten und der NPM-Befehl nicht gefunden wird, vergewissern Sie sich, dass die PATH-Umgebungsvariable die folgenden Pfade enthält: <i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> oder <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter "-h" auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So installieren Sie die Befehlszeilenschnittstelle mit dem Windows Installer**

1.	Navigieren Sie zur Webseite **http://azure.microsoft.com/de-de/downloads/**.
2.	Wechseln Sie zum Abschnitt **Befehlszeilentools**, und klicken Sie dann auf **Plattformübergreifende Befehlszeilenschnittstelle**, und führen Sie den Assistenten für den Webplattform-Installer aus.

**So laden Sie Veröffentlichungseinstellungen herunter und importieren sie**

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [WACOM.NOTE] Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.


1.	Öffnen Sie eine **Eingabeaufforderung**.
2.	Führen Sie den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei herunterzuladen.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können.

3.	Klicken Sie in der Aufforderung auf **Speichern** und geben Sie einen Speicherort für die Datei an.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <file>

	Für den vorigen Screenshot wurde die Einstellungsveröffentlichungsdatei im Ordner C:\HDInsight auf der Arbeitsstation gespeichert.

**So erstellen Sie ein Azure-Speicherkonto**

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum befinden.

- Führen Sie den folgenden Befehl in einem Eingabeaufforderungsfenster aus, um ein Azure-Speicherkonto zu erstellen:

		azure storage account create [options] <StorageAccountName>

	Wählen Sie einen geeigneten Ort für die Bereitstellung des HDInsight-Clusters aus, wenn Sie dazu aufgefordert werden. Der Speicher muss sich am gleichen Standort befinden wie der HDInsight-Cluster. Derzeit können nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Nord, Mitte)**, **USA (Süd, Mitte)** HDInsight-Cluster gehostet werden.  

Weitere Informationen zum Erstellen eines Azure-Speicherkontos im Azure-Verwaltungsportal finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

Details zum Abrufen dieser Informationen im Verwaltungsportal finden Sie im Bereich "Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln unter [Verwalten von Speicherkonten][azure-create-storageaccount].

Für Ihren HDInsight-Cluster benötigen Sie außerdem einen Container innerhalb des Speicherkontos. Falls das angegebene Speicherkonto noch keinen Container enthält, werden Sie von "azure hdinsight cluster create" zur Eingabe eines Containernamens aufgefordert, und der Container wird erstellt. Sie können den folgenden Befehl verwenden, um den Container vorab zu erstellen:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie HDInsight-Cluster bereit**

- Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**So stellen Sie einen HDInsight-Cluster mit einer Konfigurationsdatei bereit**

Normalerweise erstellen Sie einen HDInsight-Cluster, führen Ihre Jobs aus und löschen den Cluster anschließend, um Kosten zu sparen. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

- Führen Sie im Eingabeaufforderungsfenster die folgenden Befehle aus:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Azure SQL-Datenbank-Dashboard mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Windows Azure-Dienste** den Wert **Ja** aus, und klicken Sie auf **Speichern**..


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**So listen Sie Clusterdetails auf und zeigen sie an**

- Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**So löschen Sie Cluster**

- Mit dem folgenden Befehl können Sie einen Cluster löschen:

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Verwenden des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen.

Führen Sie die folgenden Schritte aus, um einen HDInsight-Cluster mithilfe des SDK bereitzustellen:

- Installation des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet] installieren(http://nuget.codeplex.com/wikipage?title=Getting%20Started). Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013.

2. Klicken Sie im Menü Datei auf **Neu** und anschließend auf **Projekt**.

3. Unter Neues Projekt können Sie die folgenden Werte eingeben bzw. auswählen:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **Nuget Package Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Fügen Sie in der Main()-Funktion den folgenden Code ein:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Ersetzen Sie die Variablen am Anfang der main()-Funktion.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.



##<a id="nextsteps"></a> Nächste Schritte
In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started] - Erfahren Sie, wie Sie die Arbeit mit Ihrem HDInsight-Cluster aufnehmen
* [Verwenden von Sqoop mit HDInsight][89e2276a] - Erfahren Sie, wie Sie Daten zwischen HDInsight und der SQL-Datenbank oder SQL Server kopieren
* [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell] - Erfahren Sie, wie Sie mit HDInsight unter Verwendung von PowerShell arbeiten
* [Programmgesteuerte Übermittlung von Hadoop-Jobs][hdinsight-submit-jobs] - Erfahren Sie, wie Sie Aufträge programmgesteuert an HDInsight übermitteln
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation] - Machen Sie sich mit dem HDInsight SDK vertraut

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/de-de/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /de-de/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=35.1-->
