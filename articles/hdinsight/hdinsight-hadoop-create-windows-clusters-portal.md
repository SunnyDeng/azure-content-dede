<properties
   pageTitle="Erstellen von Hadoop-Clustern in HDInsight | Microsoft Azure"
   	description="Erfahren Sie, wie Sie Cluster für Azure HDInsight mithilfe des Azure-Portals erstellen."
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
   ms.date="03/08/2016"
   ms.author="jgao"/>

# Erstellen Windows-basierter Hadoop-Cluster in HDInsight über das Azure-Portal

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-selector-create-clusters.md)]

Erfahren Sie, wie Sie einen Hadoop-Cluster in HDInsight über das Azure-Portal erstellen. Das Microsoft [Azure-Portal](../azure-portal-overview.md) ist eine zentrale Anlaufstelle zum Bereitstellen und Verwalten von Azure-Ressourcen. Das Azure-Portal ist eines der Tools, die Sie zum Erstellen Linux-basierter oder Windows-basierter Hadoop-Cluster in HDInsight verwenden können. Andere Tools und Features zur Clustererstellung finden Sie, indem Sie oben auf dieser Seite auf die Registerkartenauswahl klicken, oder unter [Methoden zur Clustererstellung](hdinsight-provision-clusters.md#cluster-creation-methods).

##Voraussetzungen:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Bevor Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (in englischer Sprache).

## Erstellen von Clustern


**So erstellen Sie einen HDInsight-Cluster**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **NEU**, auf **Datenanalyse** und anschließend auf **HDInsight**.

    ![Erstellen eines neuen Clusters im Azure-Portal](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Erstellen eines neuen Clusters im Azure-Portal")

3. Geben Sie folgende Werte ein bzw. wählen diese aus:

  * **Clustername**: Geben Sie einen Namen für den Cluster ein. Wenn der Clustername verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.
  * **Clustertyp**: Wählen Sie **Hadoop** aus. Weitere Optionen sind **HBase**, **Storm** und **Spark**.
  * **Clusterbetriebssystem**: Wählen Sie **Windows** aus. Wählen Sie zum Erstellen eines Linux-basierten Clusters **Linux** aus.
  * **Version**: Informationen hierzu finden Sie unter [HDInsight-Versionen](hdinsight-component-versioning.md)
  * **Abonnement**: Wählen Sie das Azure-Abonnement aus, das für die Erstellung des Clusters verwendet werden soll.
  * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).
  * **Anmeldeinformationen**: Konfigurieren Sie den Benutzernamen und das Kennwort für den Hadoop-Benutzer (HTTP-Benutzer). Wenn Sie Remotedesktop für den Cluster aktivieren, müssen Sie den Benutzernamen und das Kennwort für den Remotedesktopbenutzer konfigurieren und ein Ablaufdatum für das Konto festlegen. Klicken Sie unten auf **Auswählen**, um die Änderungen zu speichern.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Datenquelle**: Erstellen Sie ein neues Azure-Speicherkonto, oder wählen Sie ein vorhandenes Azure-Speicherkonto aus, das als Standarddateisystem für den Cluster verwendet werden soll.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Knotenpreistarife**: Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * Klicken Sie auf **Optionale Konfiguration**, um die Clusterversion auszuwählen und andere optionale Einstellungen zu konfigurieren. Sie können den Cluster z. B. einem **virtuellen Netzwerk** hinzufügen, einen **externen Metastore** zum Speichern von Daten für Hive und Oozie einrichten, einen Cluster mithilfe von Skriptaktionen zum Installieren von benutzerdefinierten Komponenten anpassen oder zusätzliche Speicherkonten für einen Cluster angeben.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Klicken Sie auf **Erstellen**. Wenn Sie **An Startmenü anheften** auswählen, wird dem Startmenü Ihres Portals eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster erstellt wird. Sobald die Erstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.
	
    Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Bereitstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter **Benachrichtigungen** überprüfen.
	

5. Klicken Sie nach Abschluss der Erstellung im Startmenü auf die Kachel für den Cluster, um das Clusterblatt zu öffnen. Auf dem Clusterblatt werden grundlegende Informationen zum Cluster angezeigt, z. B. der Name, die zugehörige Ressourcengruppe, der Standort, das Betriebssystem, die URL für das Cluster-Dashboard usw.


	![Clusterblatt](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Clustereigenschaften")


	Im Folgenden werden die Symbole oben auf diesem Blatt und im Abschnitt **Zusammenfassung** erläutert:


	* **Einstellungen** und **Alle Einstellungen**: Zeigt das Blatt **Einstellungen** für den Cluster an, über das Sie auf detaillierte Konfigurationsinformationen für den Cluster zugreifen können.
	* **Dashboard**, **Cluster-Dashboard** und **URL**: Über diese Optionen können Sie auf das Cluster-Dashboard zugreifen, ein Webportal für die Ausführung von Aufträgen im Cluster.
	* **Remotedesktop**: Dient zum Aktivieren/Deaktivieren von Remotedesktop für den Clusterknoten.
	* **Cluster skalieren**: Dient zum Ändern der Anzahl von Workerknoten für den Cluster.
	* **Löscht**: Löscht den HDInsight-Cluster.
	* **Schnellstart** (![Cloud- und Blitzsymbol = Schnellstart](./media/hdinsight-provision-clusters/quickstart.png)): Zeigt hilfreiche Informationen für die ersten Schritte mit HDInsight an.
	* **Benutzer** (![Benutzersymbol](./media/hdinsight-provision-clusters/users.png)): Dient zum Festlegen der Berechtigungen für die _Portalverwaltung_ dieses Clusters für andere Benutzer in Ihrem Azure-Abonnement.
	

		> [AZURE.IMPORTANT] Die hier vorgenommenen Einstellungen betreffen _nur_ den Zugriff und die Berechtigungen für diesen Cluster im Portal und haben keine Auswirkung darauf, wer eine Verbindung mit dem HDInsight-Cluster herstellen oder Aufträge übermitteln kann.
		
	* **Tags** (![Tagsymbol](./media/hdinsight-provision-clusters/tags.png)): Mithilfe von Tags können Sie Schlüssel-Wert-Paare festlegen, um eine benutzerdefinierte Taxonomie für Ihre Clouddienste zu definieren. Sie können z. B. einen Schlüssel mit dem Namen __Projekt__ erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.

##Anpassen von Clustern

- Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Weitere Informationen finden Sie unter [Anpassen Windows-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).

##Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten zum Erstellen von HDInsight-Clustern kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) – Erfahren Sie, wie Sie die Arbeit mit Ihrem HDInsight-Cluster aufnehmen können.
* [Programmgesteuerte Übermittlung von Hadoop-Jobs](hdinsight-submit-hadoop-jobs-programmatically.md) – Erfahren Sie, wie Sie Aufträge programmgesteuert an HDInsight übermitteln können.
* [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0309_2016-->