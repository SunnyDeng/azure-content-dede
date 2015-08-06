<properties 
   pageTitle="Bereitstellung von Apache Spark-Clustern in HDInsight | Azure" 
   description="Erfahren Sie, wie Sie Spark-Cluster für Azure HDInsight mithilfe des Azure-Portals, von Azure PowerShell, einer Befehlszeile oder das HDInsight .NET-SDK bereitstellen." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# Benutzerdefinierte Bereitstellung eines Apache Spark-Clusters in HDInsight

In den meisten Fällen können Sie einen Spark-Cluster mithilfe der Schnellerfassung gemäß der Beschreibung in [Erste Schritte mit Apache Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) bereitstellen. In bestimmten Szenarien empfiehlt es sich, einen angepassten Cluster bereitstellen. Beispielsweise empfiehlt sich das Anfügen eines externen Metadatenspeichers, um die Hive-Metadaten nach Ablauf der Lebensdauer eines Clusters beizubehalten, oder das Verwenden von zusätzlichem Speicher mit dem Cluster.

Für diese und andere Szenarien enthält dieser Artikel Anweisungen zur Verwendung des Azure-Portals, von Azure PowerShell oder des HDInsight .NET-SDKs, um einen benutzerdefinierten Spark-Cluster in HDInsight bereitzustellen.


**Voraussetzungen:**

Zum Ausführen der Anweisungen in diesem Artikel benötigen Sie ein Azure-Abonnement: Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Was sind die verschiedenen Konfigurationsoptionen?

###Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure-Blobspeicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional ein zusätzliches Azure-Speicherkonto angeben, das ebenfalls dem Cluster zugeordnet wird.

>[AZURE.NOTE] Geben Sie keinen Blob-Speichercontainer für mehrere Cluster frei. Dies wird nicht unterstützt.

Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](hdinsight-use-blob-storage.md).

###Metastore

Mit Spark können Sie über Rohdaten Schemas und Hive-Tabellen definieren. Sie können diese Schemas und Tabellenmetadaten in externen Metastores speichern. Mit dem Metastore können Sie Ihre Hive-Metadaten beibehalten, damit Sie Hive-Tabellen nicht neu erstellen müssen, wenn Sie einen neuen Cluster bereitstellen. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank. Die eingebettete Datenbank kann die Metadaten nicht beibehalten, wenn der Cluster gelöscht wird.

### Clusteranpassung

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Bereitstellung anpassen. Diese Skripts werden mithilfe der Konfigurationsoption **Skriptaktion** aufgerufen, die vom Azure-Verwaltungsportal, von HDInsight Windows PowerShell-Cmdlets oder dem HDInsight .NET SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen][hdinsight-customize-cluster].


###Virtuelle Netzwerke

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, beständigen Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mithilfe eines virtuellen privaten Netzwerks (VPN) mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort).

	In einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum über ein Hardware-VPN oder den Routing- und RAS-Dienst mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	In einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE] Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen Cluster bereitstellen. Weitere Informationen finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

##<a id="portal"></a>Verwenden des Azure-Portals

Spark-Cluster in HDInsight verwenden als Standarddateisystem einen Azure-BLOB-Speichercontainer. Sie benötigen ein Azure-Speicherkonto im entsprechenden Rechenzentrum, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](hdinsight-hadoop-use-blob-storage.md). Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].

**So erstellen Sie einen HDInsight-Cluster mit der benutzerdefinierten Erstellungsoption**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie im unteren Seitenbereich auf **+ NEW**, anschließend auf **DATA SERVICES**, auf **HDINSIGHT**und zuletzt auf **CUSTOM CREATE**.
3. Geben Sie auf der Seite **Clusterdetails** die folgenden Daten ein:

	![Bereitstellen von Spark in HDInsight-Clustern – Details](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "Bereitstellen von Spark in HDInsight-Clustern – Details")

    <table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Clustername</td>
		<td><p>Der Name des Clusters. </p>
			<ul>
			<li>Der DNS-Name (Domain Name System) muss mit einem alphanumerischen Zeichen beginnen und enden und darf Bindestriche enthalten.</li>
			<li>Das Feld muss eine Zeichenfolge mit 3 bis 63 Zeichen enthalten.</li>
			</ul></td></tr>
	<tr><td>Clustertyp</td>
		<td>Wählen Sie <strong>Spark</strong> als Clustertyp aus.</td></tr>
	<tr><td>Betriebssystem</td>
		<td>Wählen Sie <b>Windows Server 2012 R2 Data Center</b> um einen Spark-Cluster unter Windows bereitzustellen. </td></tr>
	<tr><td>HDInsight-Version</td>
		<td>Wählen Sie die Version aus. Für Spark ist <b>HDInsight 3.2</b> die einzige verfügbare Versionsoption, die Spark&#160;1.3.1 verwendet.</td></tr>
	</table>

	Geben Sie die Werte wie in der Tabelle gezeigt ein und klicken Sie auf den Pfeil nach rechts.

4. Geben Sie auf der Seite **Cluster konfigurieren** die folgenden Daten ein:

	![Angeben der Knotenanzahl und der Knotentypen für den Cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "Angeben der Knotenanzahl und der Knotentypen für den Cluster")

	<table border="1">
<tr><th>Name</th><th>Wert</th></tr>
<tr><td>Datenknoten</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten. <br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td></tr>
<tr><td>Region/virtuelles Netzwerk:</td><td><p>Wählen Sie dieselbe Region wie für das Speicherkonto, das Sie im letzten Verfahren erstellt haben. HDInsight erfordert, dass sich das Speicherkonto in derselben Region befindet. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet.</p>.<br/>Wenn Sie ein virtuelles Azure-Netzwerk erstellt haben, können Sie das Netzwerk auswählen, für dessen Nutzung der HDInsight-Cluster konfiguriert wird.</p><p>Informationen zur Erstellung von virtuellen Azure-Netzwerken finden Sie unter <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Aufgaben bei der Konfiguration virtueller Netzwerke</a>.</p></td></tr>
<tr><td>Größe des Hauptknotens</td><td><p>Wählen Sie eine VM-Größe für den Hauptknoten aus.</p></td></tr>
<tr><td>Datenknotengröße</td><td><p>Wählen Sie eine VM-Größe für die Datenknoten aus.</p></td></tr>
</table>

	>[AZURE.NOTE] Je nach Wahl der VMs können Ihre Kosten variieren. HDInsight verwendet für Clusterknoten VMs mit Standardtarif. Informationen über die Auswirkungen der VM-Größe auf Ihre Kosten finden Sie unter <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight-Preise</a>.


5. Geben Sie auf der Seite **Clusterbenutzer konfigurieren** die folgenden Daten ein:

    ![Angeben der Admin- und RDP-Benutzerdetails](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "Angeben der Admin- und RDP-Benutzerdetails")

    <table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>HTTP-Benutzername</td>
		<td>Geben Sie den Benutzernamen für den HDInsight-Cluster an.</td></tr>
	<tr><td>HTTP-Kennwort/Kennwort bestätigen</td>
		<td>Geben Sie das Kennwort für den HDInsight-Cluster an.</td></tr>
	<tr><td>Remotedesktop für Cluster aktivieren</td>
		<td>Aktivieren Sie dieses Kontrollkästchen, um einen Benutzernamen, ein Kennwort und ein Ablaufdatum für einen Remotedesktopbenutzer einzugeben, der nach der Bereitstellung per Remotezugriff auf die Clusterknoten zugreifen kann. Sie können Remotedesktop auch noch nach der Bereitstellung des Clusters aktivieren. Anweisungen hierzu finden Sie unter <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung mit HDInsight-Clustern mit RDP</a>.</td></tr>
	<tr><td>Hive-/Oozie-Metastore eingeben</td>
		<td>Aktivieren Sie dieses Kontrollkästchen, um eine SQL-Datenbank im gleichen Datencenter wie der Cluster für die Verwendung als Hive-/Oozie-Metastore anzugeben. Wenn Sie dieses Kontrollkästchen aktivieren, müssen Sie auf den nachfolgenden Seiten des Assistenten Details zur Azure SQL-Datenbank angeben. Dies ist praktisch, wenn Sie die Metadaten von Hive-/Oozie-Jobs behalten möchten, nachdem der Cluster gelöscht wurde. Anleitungen zum Erstellen eines Metastore finden Sie unter <a href="https://azure.microsoft.com/de-de/documentation/articles/sql-database-get-started/" target="_blank">Erstellen Ihrer ersten Azure SQL-Datenbank</a>.</td></tr>
	</td></tr>
	</table>

	>[AZURE.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, klicken Sie für **Windows** **Azure-Dienste** auf **Ja**, und klicken Sie dann auf **Speichern**.

    Klicken Sie auf den Pfeil nach rechts.

6. Geben Sie auf der Seite **Hive/Oozie-Metastore konfigurieren** die folgenden Daten ein:

    ![Angeben der Hive/Oozie-Metastore-Datenbankdetails](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "Angeben der Hive/Oozie-Metastore-Datenbankdetails")

	Geben Sie eine Azure SQL-Datenbank an, die als Metastore für Hive/Oozie verwendet werden soll. Sie können dieselbe Datenbank für Hive- und Oozie-Metastores angeben. Die SQL-Datenbank muss sich im selben Rechenzentrum wie das HDInsight-Cluster befinden. In diesem Feld werden nur SQL-Datenbanken aufgelistet, die sich im gleichen Datencenter befinden, das Sie auf der Seite <strong>Clusterdetails</strong> angegeben haben. Geben Sie auch den Benutzernamen und das Kennwort zum Herstellen der Verbindung mit der Azure SQL-Datenbank an, die Sie ausgewählt haben.

    >[AZURE.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.

    Klicken Sie auf den Pfeil nach rechts.

7. Geben Sie auf der Seite **Speicherkonto** die folgenden Werte ein:

    ![Angeben der Speicherkontodetails](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "Angeben der Speicherkontodetails")

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Speicherkonto</td>
		<td>Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für das HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten:
		<ul>
			<li><strong>Vorhandenen Speicher verwenden</strong></li>
			<li><strong>Neuen Speicher erstellen</strong></li>
			<li><strong>Speicher aus anderem Abonnement verwenden</strong></li>
		</ul>
		</td></tr>
	<tr><td>Kontoname</td>
		<td><ul>
			<li>Falls Sie vorhandenen Speicher verwenden, wählen Sie unter <strong>Kontoname</strong> ein vorhandenes Speicherkonto aus. Die Dropdownliste enthält nur die Storage-Konten in dem Datencenter, in dem Sie auch den Cluster eingerichtet haben.</li>
			<li>Wenn Sie <strong>Neuen Speicher erstellen</strong> oder <strong>Speicher aus einem anderem Abonnement verwenden</strong> ausgewählt haben, müssen Sie den Namen des Speicherkontos angeben.</li>
		</ul></td></tr>
	<tr><td>Kontoschlüssel</td>
		<td>Geben Sie den Speicherschlüssel für das entsprechende Konto ein, falls Sie <strong>Speicher aus anderem Abonnement verwenden</strong> ausgewählt haben.</td></tr>
	<tr><td>Standardcontainer</td>
		<td><p>Gibt den Standardcontainer im Speicherkonto an, der als Standarddateisystem für den HDInsight-Cluster verwendet werden soll. Wenn Sie <strong>Vorhandenen Speicher verwenden</strong> für das Feld <strong>Speicherkonto</strong> wählen und in dem betreffenden Konto keine Container vorhanden sind, wird der Container standardmäßig mit demselben Namen wie der des Clusters erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt. Zum Beispiel mycontainer1, mycontainer2 und so weiter. Sie können jedoch auch Container im vorhandenen Speicherkonto verwenden, die einen anderen Namen als der Cluster haben.</p>
        <p>Falls Sie neuen Speicher erstellen oder einen Speicher aus einem anderen Azure-Abonnement verwenden, müssen Sie den Namen des Standardcontainers angeben.</p>
    </td></tr>
	<tr><td>Zusätzliche Speicherkonten</td>
		<td>HDInsight unterstützt mehrere Speicherkonten. Es gibt keine Beschränkung in Bezug auf die zusätzlichen Speicherkonten, die von einem Cluster verwendet werden können. Wenn Sie den Cluster jedoch im Azure-Portal erstellen, können Sie aufgrund von Einschränkungen der Benutzeroberfläche maximal sieben Speicherkonten einrichten. Für jedes angegebene Storage-Konto wird eine zusätzliche Seite **Speicherkonto** im Assistenten hinzugefügt, auf der Sie die Kontoinformationen angeben können. Im obigen Bildschirmfoto wurde z. B. ein zusätzliches Speicherkonto ausgewählt, weshalb Seite 5 dem Dialogfeld hinzugefügt wurde.</td></tr>
</table>

	Klicken Sie auf den Pfeil nach rechts.

8. Wenn Sie für den Cluster zusätzlichen Speicher konfigurieren möchten, geben Sie auf der Seite **Speicherkonto** die Kontoinformationen für das zusätzliche Speicherkonto ein:

	![Angeben von zusätzlichen Speicherkontodetails](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "Angeben von zusätzlichen Speicherkontodetails")

    Hier haben Sie erneut die Auswahl zwischen einem vorhandenen Speicher, einem neu erstellten Speicher und einem Speicher aus einem anderen Azure-Abonnement. Die Angabe der Werte erfolgt auf dieselbe Weise wie im vorherigen Schritt.

    > [AZURE.NOTE] Nachdem Sie ein Azure-Speicherkonto für Ihren HDInsight-Cluster ausgewählt haben, können Sie das Konto nicht mehr löschen und auch kein anderes Konto auswählen.

8. Klicken Sie auf der Seite **Skriptaktionen** auf **Skriptaktion hinzufügen**, und geben Sie Einzelheiten zum benutzerdefinierten Skript an, das Sie zum Anpassen eines Clusters bei seiner Erstellung ausführen möchten. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen][hdinsight-customize-cluster].

	Wenn Sie eine Skriptaktion verwenden müssen, müssen Sie die folgenden Eingaben bereitstellen.
	
	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Name</td>
		<td>Geben Sie einen Namen für die Skriptaktion an.</td></tr>
	<tr><td>Skript-URI</td>
		<td>Geben Sie den Uniform Resource Identifier (URI) für das Skript an, das aufgerufen wird, um den Cluster anzupassen.</td></tr>
	<tr><td>Knotentyp</td>
		<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
	<tr><td>Parameter</td>
		<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</td></tr>
</table>

	Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.



##<a id="powershell"></a> Verwenden von Azure PowerShell

Dieser Abschnitt enthält Anweisungen für die Bereitstellung eines Apache Spark-Clusters in Azure HDInsight mit Azure PowerShell. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure]. Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]. Eine Liste der HDInsight Windows PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].

> [AZURE.NOTE] Die Skripts in diesem Abschnitt dienen zum Konfigurieren eines HDInsight-Clusters in einem virtuellen Azure-Netzwerk, jedoch nicht zur Erstellung des virtuellen Azure-Netzwerks. Informationen zur Erstellung von virtuellen Azure-Netzwerken finden Sie unter [Aufgaben bei der Konfiguration virtueller Netzwerke](http://msdn.microsoft.com/library/azure/jj156206.aspx).

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe von Azure PowerShell ausgeführt werden:

- Erstellen eines Azure-Speicherkontos
- Erstellen eines Azure-Blobcontainers
- Erstellen eines HDInsight-Clusters

Sie können die Windows PowerShell-Konsole oder Windows PowerShell Integrated Scripting Environment (ISE) zum Ausführen der Skripts verwenden.
 
HDInsight verwendet Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Datencenter wie der HDInsight-Cluster befinden.

**Verbinden mit Ihrem Azure-Konto**

		Add-AzureAccount 

Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

**Erstellen eines Azure-Speicherkontos**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Windows PowerShell-Befehlen abrufen:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Erstellen eines Azure-Blobspeichercontainers**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie HDInsight-Cluster bereit**

- Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE] Die von Ihnen angegebenen Anmeldeinformationen werden verwendet, um das Hadoop-Benutzerkonto für den Cluster zu erstellen. Sie verwenden dieses Konto, um die Verbindung mit dem Cluster herzustellen und Aufträge auszuführen.. Wenn Sie im Azure-Portal die Option "Schnellerfassung" verwenden, um einen Cluster bereitzustellen, lautet der Hadoop-Standardbenutzername "admin". Sie dürfen dieses Konto nicht mit dem Benutzerkonto für das Remotedesktopprotokoll (RDP) verwechseln. Das RDP-Benutzerkonto muss sich vom Hadoop-Benutzerkonto unterscheiden. Weitere Informationen finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][hdinsight-admin-portal].

	Die Bereitstellung des Clusters kann einige Minuten dauern.

	![HDI.PS.Bereitstellen](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "Bereitstellen eines Spark-Clusters mithilfe der grundlegenden Konfiguration in PowerShell")


**Bereitstellen eines HDInsight-Clusters mit benutzerdefinierten Konfigurationsoptionen**

Bei der Bereitstellung eines Clusters können Sie erweiterte Optionen verwenden, wie z. B. die Verbindung mit mehr als einem Azure-Blobspeichercontainer oder die Verwendung einer Azure SQL-Datenbank für Hive- und Oozie-Metastores. Auf diese Weise können Sie die Lebensdauer Ihrer Daten und Metadaten von der Lebensdauer des Clusters trennen.

> [AZURE.NOTE] Die Windows PowerShell-Cmdlets sind die einzige empfohlene Methode zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster. Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden. Die per Azure PowerShell festgelegten Konfigurationswerte bleiben erhalten, wenn das Cluster gepatcht wird.

- Führen Sie die folgenden Befehle in einem Windows PowerShell-Fenster aus:

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, klicken Sie für **Windows** **Azure-Dienste** auf **Ja**, und klicken Sie dann auf **Speichern**.

	Die Bereitstellung des Clusters kann einige Minuten dauern.

**So listen Sie HDInsight-Cluster auf**

- Führen Sie den folgenden Befehl in einer Azure PowerShell-Konsole aus, um den HDInsight-Cluster aufzulisten und zu überprüfen, ob der Cluster erfolgreich bereitgestellt wurde:

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a>Verwenden des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in einer .NET Framework-Anwendung.

Führen Sie die folgenden Schritte aus, um einen HDInsight-Cluster mithilfe des SDK bereitzustellen:

- Installieren des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**Installieren des HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) herunterladen und installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


**Erstellen einer Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013.

2. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3. Unter **Neues Projekt** können Sie die folgenden Werte eingeben bzw. auswählen:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie den folgenden Befehl in der Konsole aus, um die Pakete zu installieren:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. Fügen Sie in der Main()-Funktion den folgenden Code ein:

        string thumbprint = "<CertificateFriendlyName>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. Ersetzen Sie die Variablen am Anfang der main()-Funktion.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.


##<a id="nextsteps"></a> Nächste Schritte

* Unter [Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools.md) finden Sie Informationen zur Verwendung von Apache Spark in HDInsight mit BI-Tools wie Power BI und Tableau. 
* Unter [Verwenden von Spark in HDInsight zum Erstellen von Machine Learning-Anwendungen](hdinsight-apache-spark-ipython-notebook-machine-learning.md) finden Sie Informationen zum Erstellen von Machine Learning-Anwendungen mit Apache Spark in HDInsight.
* Unter [Verwenden von Spark in HDInsight zum Erstellen von Echtzeit-Streaminganwendungen](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) finden Sie Informationen zum Erstellen von Streaminganwendungen mit Apache Spark in HDInsight.
* Unter [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md) finden Sie Informationen zur Verwendung des Ressourcen-Managers zum Verwalten von Ressourcen, die dem Spark-Cluster zugeteilt sind.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Verwenden von Sqoop mit HDInsight"

<!----HONumber=July15_HO4-->