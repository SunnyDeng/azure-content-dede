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
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Erstellen Windows-basierter Hadoop-Cluster in HDInsight über das Azure-Portal

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-create-windows-cluster-selector.md)]

Erfahren Sie, wie Sie einen Hadoop-Cluster in HDInsight über das Azure-Portal erstellen. Das Microsoft [Azure-Portal](azure-portal-overview.md) ist eine zentrale Anlaufstelle zum Bereitstellen und Verwalten von Azure-Ressourcen. Das Azure-Portal ist eines der Tools, die Sie zum Erstellen Linux-basierter oder Windows-basierter Hadoop-Cluster in HDInsight verwenden können. Andere Tools und Features zur Clustererstellung finden Sie, indem Sie oben auf dieser Seite auf die Registerkartenauswahl klicken, oder unter [Methoden zur Clustererstellung](hdinsight-provision-clusters.md#cluster-creation-methods).

###Voraussetzungen:

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

	   	![Clusteranmeldeinformationen eingeben](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Datenquelle**: Erstellen Sie ein neues Azure-Speicherkonto, oder wählen Sie ein vorhandenes Azure-Speicherkonto aus, das als Standarddateisystem für den Cluster verwendet werden soll.

   		![Blatt „Datenquelle“](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Auswahlmethode**: Wählen Sie **Aus allen Abonnements** aus, damit Speicherkonten aus all Ihren Abonnements durchsucht werden können. Wählen Sie **Zugriffsschlüssel** aus, wenn Sie den **Speichernamen** und **Zugriffsschlüssel** eines vorhandenen Speicherkontos eingeben möchten.
  		* **Speicherkonto auswählen/Neu erstellen**: Klicken Sie auf **Speicherkonto auswählen**, um ein vorhandenes Speicherkonto auszuwählen, das Sie dem Cluster zuordnen möchten. Alternativ können Sie auf **Neu erstellen** klicken, um ein neues Speicherkonto zu erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.
  		* **Standardcontainer auswählen**: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.
  		* **Standort**: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird. Dieser Standort bestimmt auch den Clusterstandort.  Der Cluster und das zugeordnete Standardspeicherkonto müssen sich im selben Azure-Rechenzentrum befinden.
  	
  * **Knotenpreistarife**: Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.
  

		![Blatt „Knotenpreistarife“	](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * Klicken Sie auf **Optionale Konfiguration**, um die Clusterversion auszuwählen und andere optionale Einstellungen zu konfigurieren. Sie können den Cluster z. B. einem **virtuellen Netzwerk** hinzufügen, einen **externen Metastore** zum Speichern von Daten für Hive und Oozie einrichten, einen Cluster mithilfe von Skriptaktionen zum Installieren von benutzerdefinierten Komponenten anpassen oder zusätzliche Speicherkonten für einen Cluster angeben.

  		* **HDInsight-Version**: Wählen Sie die Version aus, die Sie für den Cluster verwenden möchten. Weitere Informationen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).
  		* **Virtuelles Netzwerk**: Wählen Sie ein virtuelles Azure-Netzwerk und das Subnetz aus, wenn Sie den Cluster in einem virtuellen Netzwerk platzieren möchten.  

			![Blatt „Virtuelles Netzwerk“](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Windows-basierte HDInsight-Cluster können nur in einem klassischen virtuellen Netzwerk bereitgestellt werden.
  

  		
		* **Externe Metastores**: Geben Sie eine Azure SQL-Datenbank an, die zum Speichern von Hive- und Oozie-Metadaten für den Cluster verwendet werden soll.
 
            > [AZURE.NOTE] Für HBase-Clustertypen ist keine Metastore-Konfiguration verfügbar.

			![Blatt „Benutzerdefinierte Metastores“](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			Klicken Sie für **Vorhandene SQL-Datenbank für Hive-Metadaten verwenden** auf **Ja**, wählen Sie eine SQL-­Datenbank aus, und geben Sie dann den Benutzernamen und das Kennwort für die Datenbank ein. Wiederholen Sie diese Schritte ggf. für **Vorhandene SQL-Datenbank für Oozie-Metadaten verwenden**. Klicken Sie auf **Auswählen**, bis wieder das Blatt **Optionale Konfiguration** angezeigt wird.


			>[AZURE.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.
		
  		* Klicken Sie auf **Skriptaktionen**, wenn Sie einen Cluster bei seiner Erstellung mit einem benutzerdefinierten Skript anpassen möchten. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md). Geben Sie auf dem Blatt „Skriptaktionen“ die Details wie im Screenshot dargestellt ein.
  	

			![Blatt „Skriptaktionen“](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure-Speicherschlüssel**: Geben Sie weitere Speicherkonten an, die dem Cluster zugeordnet werden sollen. Klicken Sie auf dem Blatt **Azure-Speicherschlüssel** auf **Speicherschlüssel hinzufügen**, und wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues Konto.
    

			![Blatt „Zusätzlicher Speicher“](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Klicken Sie auf **Erstellen**. Wenn Sie **An Startmenü anheften** auswählen, wird dem Startmenü Ihres Portals eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster erstellt wird. Sobald die Erstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.
	
    Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Bereitstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter **Benachrichtigungen** überprüfen.
	

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
		
	* **Tags** (![Tagsymbol](./media/hdinsight-provision-clusters/tags.png)): Mithilfe von Tags können Sie Schlüssel-Wert-Paare festlegen, um eine benutzerdefinierte Taxonomie für Ihre Clouddienste zu definieren. Sie können z. B. einen Schlüssel mit dem Namen __Projekt__ erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.

##Anpassen von Clustern

- Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Weitere Informationen finden Sie unter [Anpassen Windows-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).

##Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten zum Erstellen von HDInsight-Clustern kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight](hdinsight-get-started.md) – Erfahren Sie, wie Sie die Arbeit mit Ihrem HDInsight-Cluster aufnehmen können.
* [Programmgesteuerte Übermittlung von Hadoop-Jobs](hdinsight-submit-hadoop-jobs-programmatically.md) – Erfahren Sie, wie Sie Aufträge programmgesteuert an HDInsight übermitteln können.
* [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0128_2016-->