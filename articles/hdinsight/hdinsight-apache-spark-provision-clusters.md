<properties
   pageTitle="Bereitstellung von Apache Spark-Clustern in HDInsight | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie Spark-Cluster für Azure HDInsight mit dem Azure-Portal, Azure PowerShell, einer Befehlszeile oder dem HDInsight .NET-SDK bereitstellen."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="12/08/2015"
    ms.author="nitinme"/>

# Benutzerdefiniertes Erstellen eines Apache Spark-Clusters in HDInsight (Windows)

> [AZURE.NOTE]HDInsight bietet jetzt Spark-Cluster unter Linux. Informationen zum Erstellen benutzerdefinierter Spark-Cluster in HDInsight unter Linux finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

In den meisten Fällen können Sie einen Spark-Cluster mithilfe der Schnellerfassung gemäß der Beschreibung in [Erste Schritte mit Apache Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) erstellen. In bestimmten Szenarien empfiehlt es sich, einen angepassten Cluster zu erstellen. Beispielsweise empfiehlt sich das Anfügen eines externen Metadatenspeichers, um die Hive-Metadaten nach Ablauf der Lebensdauer eines Clusters beizubehalten, oder das Verwenden von zusätzlichem Speicher mit dem Cluster.

Für diese und andere Szenarien enthält dieser Artikel Anweisungen zur Verwendung des Azure-Portals, von Azure PowerShell oder des HDInsight .NET-SDKs, um einen benutzerdefinierten Spark-Cluster in HDInsight zu erstellen.


**Voraussetzungen:**

Zum Ausführen der Anweisungen in diesem Artikel benötigen Sie ein Azure-Abonnement: Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Was sind die verschiedenen Konfigurationsoptionen?

###Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure-Blobspeicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional ein zusätzliches Azure-Speicherkonto angeben, das ebenfalls dem Cluster zugeordnet wird.

>[AZURE.NOTE]Geben Sie keinen Blob-Speichercontainer für mehrere Cluster frei. Dies wird nicht unterstützt.

Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](hdinsight-use-blob-storage.md).

###Metastore

Mit Spark können Sie über Rohdaten Schemas und Hive-Tabellen definieren. Sie können diese Schemas und Tabellenmetadaten in externen Metastores speichern. Mit dem Metastore können Sie Ihre Hive-Metadaten beibehalten, damit Sie Hive-Tabellen nicht neu erstellen müssen, wenn Sie einen neuen Cluster erstellen. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank. Die eingebettete Datenbank kann die Metadaten nicht beibehalten, wenn der Cluster gelöscht wird.

Informationen zum Erstellen einer SQL-Datenbank finden Sie unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md).

### Clusteranpassung

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Erstellung anpassen. Diese Skripts werden mithilfe der Konfigurationsoption **Skriptaktion** aufgerufen, die vom Azure-Verwaltungsportal, von HDInsight Windows PowerShell-Cmdlets oder dem HDInsight .NET SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen][hdinsight-customize-cluster].


###Virtuelle Netzwerke

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, beständigen Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mithilfe eines virtuellen privaten Netzwerks (VPN) mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort).

	In einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum über ein Hardware-VPN oder den Routing- und RAS-Dienst mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	In einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen Cluster erstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks](virtual-networks-create-vnet.md).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

##<a id="portal"></a> Mit dem Azure-Vorschauportal

Spark-Cluster in HDInsight verwenden als Standarddateisystem einen Azure-BLOB-Speichercontainer. Sie benötigen ein Azure-Speicherkonto im entsprechenden Rechenzentrum, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](hdinsight-hadoop-use-blob-storage.md). Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].

**So erstellen Sie einen HDInsight-Cluster mit der benutzerdefinierten Erstellungsoption**

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.
2. Klicken Sie auf **NEU**, **Datenanalyse** und anschließend auf **HDInsight**.

    ![Erstellen eines neuen Clusters im Azure-Vorschauportal](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.1.png "Erstellen eines neuen Clusters im Azure-Vorschauportal")

3. Geben Sie einen **Clusternamen** ein, wählen Sie **Spark** als **Clustertyp** aus, und wählen Sie in der Dropdownliste **Clusterbetriebssystem** den Eintrag **Windows Server 2012 R2 Datacenter** aus. Wenn der Clustername verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	![Clusternamen und -typ eingeben](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.2.png "Clusternamen und -typ eingeben")

4. Falls Sie mehrere Abonnements besitzen, klicken Sie auf den Eintrag **Abonnement**, um das Azure-Abonnement für den Cluster auszuwählen.

5. Klicken Sie auf **Ressourcengruppe**, um eine Liste der vorhandenen Ressourcengruppen anzuzeigen, und wählen Sie dann die Gruppe aus, in der der Cluster erstellt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf **Neu erstellen** klicken und den Namen der neuen Gruppe eingeben. Wenn der neue Gruppenname verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	> [AZURE.NOTE]Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).

6. Klicken Sie auf **Anmeldeinformationen**, und geben Sie dann ein **Clusteranmeldekennwort** und einen **Clusterbenutzernamen** ein. Wenn Sie Remotedesktop für den Clusterknoten aktivieren möchten, klicken Sie für **Remotedesktop aktivieren** auf **Ja**. Wählen Sie ein Ablaufdatum für den Remotedesktopzugriff auf den Cluster aus, und geben Sie den Benutzernamen und das Kennwort für den Remotedesktopbenutzer ein. Klicken Sie unten auf **Auswählen**, um die Konfiguration der Anmeldeinformationen zu speichern.

	![Clusteranmeldeinformationen eingeben](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.3.png "Clusteranmeldeinformationen eingeben")

7. Klicken Sie auf **Datenquelle**, um eine vorhandene Datenquelle für den Cluster auszuwählen, oder erstellen Sie eine neue Datenquelle.

	![Blatt „Datenquelle“](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.4.png "Datenquellenkonfiguration angeben")

	Zurzeit kann ein Azure-Speicherkonto als Datenquelle für einen HDInsight-Cluster ausgewählt werden. Die Einträge auf dem Blatt **Datenquelle** werden im Folgenden erläutert.

	- **Auswahlmethode**: Wählen Sie **Aus allen Abonnements** aus, damit Speicherkonten aus all Ihren Abonnements durchsucht werden können. Wählen Sie **Zugriffsschlüssel** aus, wenn Sie den **Speichernamen** und **Zugriffsschlüssel** eines vorhandenen Speicherkontos eingeben möchten.

	- **Speicherkonto auswählen/Neu erstellen**: Klicken Sie auf **Speicherkonto auswählen**, um ein vorhandenes Speicherkonto auszuwählen, das Sie dem Cluster zuordnen möchten. Alternativ können Sie auf **Neu erstellen** klicken, um ein neues Speicherkonto zu erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.

	- **Standardcontainer auswählen**: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.

	- **Standort**: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird.

		> [AZURE.IMPORTANT]Durch die Auswahl des Standorts für die Standarddatenquelle wird auch der Standort des HDInsight-Clusters festgelegt. Der Cluster und die Standarddatenquelle müssen sich in der gleichen Region befinden.

	Klicken Sie auf **Auswählen**, um die Datenquellenkonfiguration zu speichern.

8. Klicken Sie auf **Knotenpreistarife**, um Informationen zu den Knoten anzuzeigen, die für diesen Cluster erstellt werden. Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.

	![Blatt „Knotenpreistarife“](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.5.png "Anzahl von Clusterknoten angeben")

	Klicken Sie auf **Auswählen**, um die Konfiguration der Knotenpreise zu speichern.

9. Klicken Sie auf **Optionale Konfiguration**, um die Clusterversion auszuwählen und andere optionale Einstellungen zu konfigurieren. Sie können den Cluster z. B. einem **virtuellen Netzwerk** hinzufügen, einen **externen Metastore** zum Speichern von Daten für Hive und Oozie einrichten, einen Cluster mithilfe von Skriptaktionen zum Installieren von benutzerdefinierten Komponenten anpassen oder zusätzliche Speicherkonten für einen Cluster angeben.

	* Klicken Sie auf die Dropdownliste **HDInsight-Version**, und wählen Sie die Version aus, die Sie für den Cluster verwenden möchten. Weitere Informationen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).

	* Klicken Sie auf **Virtuelles Netzwerk**, um den Cluster als Teil eines virtuellen Netzwerks zu konfigurieren und die entsprechenden Konfigurationsdetails anzugeben. Klicken Sie auf dem Blatt **Virtuelles Netzwerk** auf **Virtuelles Netzwerk** und dann auf ein Netzwerk, das Sie verwenden möchten. Wählen Sie ein **Subnetz** für das Netzwerk aus, und klicken Sie dann auf **Auswählen**, um die Konfiguration des virtuellen Netzwerks zu speichern.

		![Blatt „Virtuelles Netzwerk“](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.6.png "Details zum virtuellen Netzwerk angeben")

	* Klicken Sie auf **Externe Metastores**, um die SQL-Datenbank anzugeben, die zum Speichern von Hive- und Oozie-Metadaten für den Cluster verwendet werden soll.

		![Blatt „Benutzerdefinierte Metastores“](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.7.png "Externe Metastores angeben")

		Klicken Sie für **Vorhandene SQL-Datenbank für Hive-Metadaten verwenden ** auf **Ja**, wählen Sie eine SQL-­Datenbank aus, und geben Sie dann den Benutzernamen und das Kennwort für die Datenbank ein. Wiederholen Sie diese Schritte ggf. für **Vorhandene SQL-Datenbank für Oozie-Metadaten verwenden**. Klicken Sie auf **Auswählen**, bis wieder das Blatt **Optionale Konfiguration** angezeigt wird.

		>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.

	* Klicken Sie auf **Skriptaktionen**, wenn Sie einen Cluster bei seiner Erstellung mit einem benutzerdefinierten Skript anpassen möchten. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md). Geben Sie auf dem Blatt „Skriptaktionen“ die Details wie im Screenshot dargestellt ein.

		![Blatt „Skriptaktionen“](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.8.png "Skriptaktion angeben")

		Klicken Sie auf **Auswählen**, um die Konfiguration der Skriptaktionen zu speichern.

	* Klicken Sie auf **Azure-Speicherschlüssel**, um dem Cluster weitere Speicherkonten zuzuordnen. Klicken Sie auf dem Blatt **Azure-Speicherschlüssel** auf **Speicherschlüssel hinzufügen**, und wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues Konto.

		![Blatt „Zusätzlicher Speicher“](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.9.png "Zusätzliche Speicherkonten angeben")

		Klicken Sie auf **Auswählen**, bis wieder das Blatt **Neues HDInsight-Cluster** angezeigt wird.

10. Vergewissern Sie sich auf dem Blatt **Neues HDInsight-Cluster**, dass **An Startmenü anheften** ausgewählt ist, und klicken Sie dann auf **Erstellen**. Der Cluster wird erstellt, und dem Startmenü Ihres Azure-Portals wird eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster erstellt wird. Sobald die Erstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.

	| Während der Erstellung | Erstellung abgeschlossen |
	| ------------------ | --------------------- |
	| ![Erstellungsanzeige im Startmenü](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Kachel für einen erstellten Cluster](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Erstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter **Benachrichtigungen** überprüfen.

11. Klicken Sie nach Abschluss der Erstellung im Startmenü auf die Kachel für den Cluster, um das Clusterblatt zu öffnen. Auf dem Clusterblatt werden grundlegende Informationen zum Cluster angezeigt, z. B. der Name, die zugehörige Ressourcengruppe, der Standort, das Betriebssystem, die URL für das Cluster-Dashboard usw.

	![Clusterblatt](./media/hdinsight-apache-spark-provision-clusters/hdispark.cluster.blade.png "Clustereigenschaften")

	Im Folgenden werden die Symbole oben auf diesem Blatt und in den Abschnitten **Zusammenfassung** und **Quicklinks** erläutert:

	* **Einstellungen** und **Alle Einstellungen**: Zeigt das Blatt **Einstellungen** für den Cluster an, über das Sie auf detaillierte Konfigurationsinformationen für den Cluster zugreifen können.

	* **Dashboard** und **URL**: Über diese Optionen können Sie auf das Cluster-Dashboard zugreifen, ein Webportal für die Ausführung von Aufträgen im Cluster.

	* **Remotedesktop**: Dient zum Aktivieren/Deaktivieren von Remotedesktop für den Clusterknoten.

	* **Cluster skalieren**: Dient zum Ändern der Anzahl von Workerknoten für den Cluster.

	* **Löscht**: Löscht den HDInsight-Cluster.

	* **Schnellstart** (![Cloud- und Blitzsymbol = Schnellstart](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): Zeigt hilfreiche Informationen für die ersten Schritte mit HDInsight an.

	* **Benutzer** (![Benutzersymbol](./media/hdinsight-apache-spark-provision-clusters/users.png)): Dient zum Festlegen der Berechtigungen für die _Portalverwaltung_ dieses Clusters für andere Benutzer in Ihrem Azure-Abonnement.

		> [AZURE.IMPORTANT]Die hier vorgenommenen Einstellungen betreffen _nur_ den Zugriff und die Berechtigungen für diesen Cluster im Azure-Vorschauportal und haben keine Auswirkung darauf, wer eine Verbindung mit dem HDInsight-Cluster herstellen oder Aufträge übermitteln kann.

	* **Tags** (![Tagsymbol](./media/hdinsight-apache-spark-provision-clusters/tags.png)): Mithilfe von Tags können Sie Schlüssel-Wert-Paare festlegen, um eine benutzerdefinierte Taxonomie für Ihre Clouddienste zu definieren. Sie können z. B. einen Schlüssel mit dem Namen __Projekt__ erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.

	* **Cluster-Dashboard**: Öffnet das Blatt „Cluster-Dashboard“, auf dem Sie das Cluster-Dashboard selbst oder Zeppelin und Jupyter Notebooks starten können.


##<a id="powershell"></a> Verwenden von Azure PowerShell

Weitere Informationen finden Sie unter [Erstellen von HDInsight-Clustern](hdinsight-provision-clusters.md#create-using-azure-powershell).

Geben Sie den Switch des Spark-Clustertyps im switch in the Cmdlet „New-AzureRmHDInsightCluster“ an:

	-ClusterType Spark


## Verwenden des ARM-basierten HDInsight .NET-SDK

Weitere Informationen finden Sie unter [Erstellen von HDInsight-Clustern](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk).

Geben Sie den Spark-Clustertyp an:

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


##<a id="nextsteps"></a> Nächste Schritte

* Unter [Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools-v1.md) finden Sie Informationen zur Verwendung von Apache Spark in HDInsight mit BI-Tools wie Power BI und Tableau.
* Unter [Verwenden von Spark in HDInsight zum Erstellen von Machine Learning-Anwendungen](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md) finden Sie Informationen zum Erstellen von Machine Learning-Anwendungen mit Apache Spark in HDInsight.
* Unter [Verwenden von Spark in HDInsight zum Erstellen von Echtzeit-Streaminganwendungen](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) finden Sie Informationen zum Erstellen von Streaminganwendungen mit Apache Spark in HDInsight.
* Unter [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md) finden Sie Informationen zur Verwendung des Ressourcen-Managers zum Verwalten von Ressourcen, die dem Spark-Cluster zugeordnet sind.


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


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Verwenden von Sqoop mit HDInsight"

<!---HONumber=AcomDC_1223_2015-->