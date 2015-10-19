<properties
   	pageTitle="Bereitstellen von Hadoop-, HBse- oder Storm-Clustern unter Linux in HDInsight | Microsoft Azure"
   	description="Erfahren Sie, wie Sie Hadoop-Cluster unter Linux für HDInsight über das Verwaltungsportal, die Befehlszeile und das .NET SDK bereitstellen."
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
   	ms.date="09/21/2015"
   	ms.author="nitinme"/>


#Benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in HDInsight

Dieser Artikel beschreibt die verschiedenen Möglichkeiten für die benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in Azure HDInsight: Azure-Portal, Azure PowerShell, Befehlszeilenschnittstelle von Azure (Azure-CLI) oder HDInsight .NET SDK.

## Was ist ein HDInsight-Cluster?

Vielleicht haben Sie sich schon gefragt, warum wir jedes Mal Cluster erwähnen, wenn wir über Hadoop oder Big Data sprechen. Das liegt daran, dass Hadoop die verteilte Verarbeitung großer Datenmengen auf mehreren Knoten eines Clusters ermöglicht. Cluster haben eine Master/Worker-Architektur mit einem Masterknoten (oder Haupt- bzw. Namensknoten) und einer beliebigen Anzahl von Workerknoten (oder Datenknoten). Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![HDInsight-Cluster][img-hdi-cluster]


HDInsight-Cluster abstrahieren die Hadoop-Implementierungsdetails, sodass Sie sich keine Gedanken über die Kommunikation zwischen den einzelnen Clusterknoten machen müssen. Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. Weitere Informationen finden Sie unter [Einführung in Hadoop in HDInsight](hdinsight-hadoop-introduction.md). Die zu verarbeitenden Daten werden in Azure-Blobspeicher gespeichert. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).


Dieser Artikel beschreibt die verschiedenen Möglichkeiten bei der Einrichtung eines Clusters. Wenn Sie schnell in der Lage sein möchten, einen Cluster bereitzustellen, lesen Sie [Erste Schritte mit Azure HDInsight unter Linux](../hdinsight-hadoop-linux-get-started.md).

**Voraussetzungen**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Abrufen der kostenlosen Testversion von Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Secure Shell (SSH)-Schlüssel**. Wenn Sie mit SSH remote auf einen Linux-Cluster zugreifen und dabei anstelle eines Kennworts einen Schlüssel verwenden möchten. Bei Verwendung eines Schlüssels wird diese Methode empfohlen, da sie sicherer ist. Anweisungen zum Generieren von SSH-Schlüsseln finden Sie in den folgenden Artikeln:
	-  Auf einem Linux-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Auf einem Windows-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Konfigurationsoptionen

### Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure-Blobspeicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional zusätzliche Blobs angeben, die ebenfalls Ihrem Cluster zugeordnet werden.


Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).


### Metastore

Der Metastore enthält Informationen zu Tabellen, Partitionen, Schemas, Spalten usw. in Hive. Diese Informationen werden von Hive verwendet, um zu bestimmen, wo Daten in Hadoop Distributed File System (HDFS) oder Azure-Blobspeicher für HDInsight gespeichert sind. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank.

Bei der Bereitstellung eines HDInsight-Clusters können Sie eine SQL-Datenbank angeben, die als Metastore für Hive verwendet werden soll. Auf diese Weise bleiben die Metadateninformationen erhalten, wenn Sie einen Cluster löschen, da diese extern in der SQL-Datenbank gespeichert sind. Informationen zum Erstellen einer SQL-Datenbank finden Sie unter [Erstellen Ihrer ersten Azure SQL-Datenbank](sql-database-get-started.md).

### Anpassen von Clustern mithilfe von Skriptaktionen

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Bereitstellung anpassen. Diese Skripts werden per **Skriptaktion** aufgerufen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT]Das Hinzufügen zusätzlicher Komponenten nach der Bereitstellung eines Clusters wird nicht unterstützt, da diese Komponenten nach dem Reimaging eines Clusterknotens nicht verfügbar sind. Durch Skriptaktionen installierte Komponenten werden im Rahmen des Reimagingvorgangs neu installiert.

### Verwenden virtueller Azure-Netzwerke

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, beständigen Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mithilfe eines virtuellen privaten Netzwerks (VPN) mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort).

	In einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum über ein Hardware-VPN oder den Routing- und RAS-Dienst mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png)

	In einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png)

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Übersicht über virtuelle Azure-Netzwerke](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen Cluster bereitstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks](virtual-networks-create-vnet.md).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden. Verwenden Sie das Azure PowerShell-Cmdlet "Get-AzureVNetConfig", um zu prüfen, ob ein vorhandenes virtuelles Netzwerk in Azure standortbasiert ist. Wenn das virtuelle Netzwerk nicht standortbasiert ist, haben Sie die folgenden Optionen:
>
> - Exportieren Sie die vorhandene virtuelle Netzwerkkonfiguration, und erstellen Sie ein neues virtuelles Netzwerk. Alle neuen virtuellen Netzwerke sind standardmäßig standortbasiert.
> - Migrieren Sie zu einem standortbasierten virtuellen Netzwerk. Weitere Informationen finden Sie unter [Migrieren vorhandener Dienste zu einem Regionsbereich](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.
>
> Derzeit (25.8.2015) können Sie nur einen Linux-basierten Cluster in einem virtuellen Azure-Netzwerk bereitstellen.
>
> Sie können kein virtuelles Azure-Netzwerk der Version 1 (klassisch) mit Linux-basiertem HDInsight verwenden. Das virtuelle Netzwerk muss Version 2 aufweisen (Azure-Ressourcen-Manager), damit es beim Erstellen des Clusters im Azure-Vorschauportal als Option angezeigt wird bzw. beim Erstellen eines Clusters über die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwendet werden kann.
>
> Wenn Ressourcen in einem v1-Netzwerk vorhanden sind und diese Ressourcen über ein virtuelles Netzwerk direkt auf HDInsight zugreifen können sollen, finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md) Informationen zum Erstellen einer Verbindung zwischen einem virtuellen v2-Netzwerk und einem virtuellen v1-Netzwerk. Nachdem diese Verbindung eingerichtet wurde, können Sie den HDInsight-Cluster im virtuellen v2-Netzwerk erstellen.

## <a id="options"></a>Optionen für die Bereitstellung von HDInsight-Linux-Clustern

Sie können einen Hadoop-Linux-Cluster von HDInsight sowohl über einen Linux- als auch über einen Windows-Computer bereitstellen. Die folgende Tabelle enthält Informationen über die Optionen für die Bereitstellung, die für die verschiedenen Betriebssystemen verfügbar sind, sowie Links zu den entsprechenden Anweisungen.

Bereitstellen eines Linux-Clusters über einen Computer mit diesem Betriebssystem | Verwenden des Azure-Portals | Verwenden der Azure-Befehlszeilenschnittstelle | Verwenden von Azure PowerShell | Verwenden des .NET-SDK
-----------------| --------| ------------------------| -------------------| ---------- | ---------
Linux| Klicken Sie [hier](#portal) | Klicken Sie [hier](#cli) | Nicht zutreffend | Nicht zutreffend
Mac OS X | Klicken Sie [hier](#portal) | Klicken Sie [hier](#cli) | Nicht zutreffend | Nicht zutreffend
Windows | Klicken Sie [hier](#portal) | Klicken Sie [hier](#cli) | Klicken Sie [hier](#powershell) | Klicken Sie [hier](#sdk)

### <a id="portal"></a>Verwenden des Azure-Portals

HDInsight-Cluster verwenden als Standarddateisystem einen Azure-Blobspeichercontainer. Sie benötigen ein Azure-Speicherkonto im entsprechenden Datencenter, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md). Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).


> [AZURE.NOTE]HDInsight-Cluster können derzeit nur in den Regionen **Südostasien**, **Nordeuropa**, **USA (Osten)** und **USA (Süden-Mitte)** gehostet werden.

**So erstellen Sie einen HDInsight-Cluster**

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.
2. Klicken Sie auf **NEU**, auf **Datenanalyse** und anschließend auf **HDInsight**.

    ![Erstellen eines neuen Clusters im Azure-Vorschauportal](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Erstellen eines neuen Clusters im Azure-Vorschauportal")

3. Geben Sie einen **Clusternamen** ein, wählen Sie **Hadoop** als **Clustertyp** aus, und wählen Sie in der Dropdownliste **Clusterbetriebssystem** den Eintrag **Ubuntu** aus. Wenn der Clustername verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.


	> [AZURE.NOTE]Wählen Sie zum Bereitstellen von HBase- oder Storm-Clustern in der Dropdownliste **Clustertyp** den entsprechenden Wert aus.


	![Clusternamen und -typ eingeben](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "Clusternamen und -typ eingeben")

4. Falls Sie mehrere Abonnements besitzen, klicken Sie auf den Eintrag **Abonnement**, um das Azure-Abonnement für den Cluster auszuwählen.

5. Klicken Sie auf **Ressourcengruppe**, um eine Liste der vorhandenen Ressourcengruppen anzuzeigen, und wählen Sie dann die Gruppe aus, in der der Cluster erstellt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf **Neu erstellen** klicken und den Namen der neuen Gruppe eingeben. Wenn der neue Gruppenname verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	> [AZURE.NOTE]Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).

6. Klicken Sie auf **Anmeldeinformationen**, und geben Sie ein Kennwort für den Administrator ein. Außerdem müssen Sie einen **SSH-Benutzernamen** und entweder ein **Kennwort** oder einen **öffentlichen Schlüssel** zum Authentifizieren des SSH-Benutzers eingeben. Es wird empfohlen, einen öffentlichen Schlüssel zu verwenden. Klicken Sie unten auf **Auswählen**, um die Konfiguration der Anmeldeinformationen zu speichern.

	![Clusteranmeldeinformationen eingeben](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "Clusteranmeldeinformationen eingeben")

	Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Klicken Sie auf **Datenquelle**, um eine vorhandene Datenquelle für den Cluster auszuwählen, oder erstellen Sie eine neue Datenquelle.

	![Blatt „Datenquelle“](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "Datenquellenkonfiguration angeben")

	Zurzeit kann ein Azure-Speicherkonto als Datenquelle für einen HDInsight-Cluster ausgewählt werden. Die Einträge auf dem Blatt **Datenquelle** werden im Folgenden erläutert.

	- **Auswahlmethode**: Wählen Sie **Aus allen Abonnements** aus, damit Speicherkonten aus all Ihren Abonnements durchsucht werden können. Wählen Sie **Zugriffsschlüssel** aus, wenn Sie den **Speichernamen** und **Zugriffsschlüssel** eines vorhandenen Speicherkontos eingeben möchten.

	- **Speicherkonto auswählen/Neu erstellen**: Klicken Sie auf **Speicherkonto auswählen**, um ein vorhandenes Speicherkonto auszuwählen, das Sie dem Cluster zuordnen möchten. Alternativ können Sie auf **Neu erstellen** klicken, um ein neues Speicherkonto zu erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.

	- **Standardcontainer auswählen**: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.

	- **Standort**: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird.

		> [AZURE.IMPORTANT]Durch die Auswahl des Standorts für die Standarddatenquelle wird auch der Standort des HDInsight-Clusters festgelegt. Der Cluster und die Standarddatenquelle müssen sich in der gleichen Region befinden.

	Klicken Sie auf **Auswählen**, um die Datenquellenkonfiguration zu speichern.

8. Klicken Sie auf **Knotentarife**, um Informationen zu den Knoten anzuzeigen, die für diesen Cluster erstellt werden. Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.

	![Blatt „Knotentarife“](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "Anzahl von Clusterknoten angeben")

	Klicken Sie auf **Auswählen**, um die Konfiguration der Knotenpreise zu speichern.

9. Klicken Sie auf **Optionale Konfiguration**, um die Clusterversion auszuwählen und andere optionale Einstellungen zu konfigurieren. Sie können den Cluster z. B. einem **virtuellen Netzwerk** hinzufügen, einen **externen Metastore** zum Speichern von Daten für Hive und Oozie einrichten, einen Cluster mithilfe von Skriptaktionen zum Installieren von benutzerdefinierten Komponenten anpassen oder zusätzliche Speicherkonten für einen Cluster angeben.

	* Klicken Sie auf die Dropdownliste **HDInsight-Version**, und wählen Sie die Version aus, die Sie für den Cluster verwenden möchten. Weitere Informationen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).


	* **Virtuelles Netzwerk**: Wählen Sie ein virtuelles Azure-Netzwerk und das Subnetz aus, wenn Sie den Cluster in einem virtuellen Netzwerk platzieren möchten.

		![Blatt „Virtuelles Netzwerk“](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.6.png "Details zum virtuellen Netzwerk angeben")

    	>[AZURE.NOTE]Windows-basierte HDInsight-Cluster können nur in einem klassischen virtuellen Netzwerk bereitgestellt werden.


	* Klicken Sie auf **Externe Metastores**, um die SQL-Datenbank anzugeben, die zum Speichern von Hive- und Oozie-Metadaten für den Cluster verwendet werden soll.

		![Blatt „Benutzerdefinierte Metastores“](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "Externe Metastores angeben")

		Klicken Sie für **Vorhandene SQL-Datenbank für Hive-Metadaten verwenden** auf **Ja**, wählen Sie eine SQL-Datenbank aus, und geben Sie dann den Benutzernamen und das Kennwort für die Datenbank ein. Wiederholen Sie diese Schritte ggf. für **Vorhandene SQL-Datenbank für Oozie-Metadaten verwenden**. Klicken Sie auf **Auswählen**, bis wieder das Blatt **Optionale Konfiguration** angezeigt wird.

		>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.


	* Klicken Sie auf **Skriptaktionen**, wenn Sie einen Cluster bei seiner Erstellung mit einem benutzerdefinierten Skript anpassen möchten. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md). Geben Sie auf dem Blatt „Skriptaktionen“ die Details wie im Screenshot dargestellt ein.

		![Blatt „Skriptaktionen“](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.8.png "Skriptaktion angeben")


	* Klicken Sie auf **Azure-Speicherschlüssel**, um dem Cluster weitere Speicherkonten zuzuordnen. Klicken Sie auf dem Blatt **Azure-Speicherschlüssel** auf **Speicherschlüssel hinzufügen**, und wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues Konto.

		![Blatt „Zusätzlicher Speicher“](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "Zusätzliche Speicherkonten angeben")

		Klicken Sie auf **Auswählen**, bis wieder das Blatt **Neues HDInsight-Cluster** angezeigt wird.

10. Vergewissern Sie sich auf dem Blatt **Neues HDInsight-Cluster**, dass **An Startmenü anheften** ausgewählt ist, und klicken Sie dann auf **Erstellen**. Der Cluster wird erstellt, und dem Startmenü Ihres Azure-Portals wird eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster bereitgestellt wird. Sobald die Bereitstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.

	| Während der Bereitstellung | Nach Abschluss der Bereitstellung |
	| ------------------ | --------------------- |
	| ![Bereitstellungsanzeige im Startmenü](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![Kachel für einen bereitgestellten Cluster](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Bereitstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter **Benachrichtigungen** überprüfen.

11. Klicken Sie nach Abschluss der Bereitstellung im Startmenü auf die Kachel für den Cluster, um das Clusterblatt zu öffnen. Auf dem Clusterblatt werden grundlegende Informationen zum Cluster angezeigt, z. B. der Name, die zugehörige Ressourcengruppe, der Standort, das Betriebssystem, die URL für das Cluster-Dashboard usw.

	![Clusterblatt](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "Clustereigenschaften")

	Im Folgenden werden die Symbole oben auf diesem Blatt und im Abschnitt **Zusammenfassung** erläutert:

	* **Einstellungen** und **Alle Einstellungen**: Zeigt das Blatt **Einstellungen** für den Cluster an, über das Sie auf detaillierte Konfigurationsinformationen für den Cluster zugreifen können.

	* **Dashboard**, **Cluster-Dashboard** und **URL**: Über diese Optionen können Sie auf das Cluster-Dashboard zugreifen, ein Webportal für die Ausführung von Aufträgen im Cluster.

	* **Secure Shell**: Für den Zugriff auf den Cluster über SSH erforderliche Informationen.

	* **Löschen**: Löscht den HDInsight-Cluster.

	* **Schnellstart** (![Cloud- und Blitzsymbol = Schnellstart](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)): Zeigt hilfreiche Informationen für die ersten Schritte mit HDInsight an.

	* **Benutzer** (![Benutzersymbol](./media/hdinsight-hadoop-provision-linux-clusters/users.png)): Dient zum Festlegen der Berechtigungen für die _Portalverwaltung_ dieses Clusters für andere Benutzer in Ihrem Azure-Abonnement.

		> [AZURE.IMPORTANT]Die hier vorgenommenen Einstellungen betreffen _nur_ den Zugriff und die Berechtigungen für diesen Cluster im Azure-Vorschauportal und haben keine Auswirkung darauf, wer eine Verbindung mit dem HDInsight-Cluster herstellen oder Aufträge übermitteln kann.

	* **Tags** (![Tagsymbol](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)): Mithilfe von Tags können Sie Schlüssel-Wert-Paare festlegen, um eine benutzerdefinierte Taxonomie für Ihre Clouddienste zu definieren. Sie können z. B. einen Schlüssel mit dem Namen __Projekt__ erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.

### <a id="cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle ist ein plattformübergreifendes Befehlszeilentool zur Verwaltung von Azure-Diensten. Es kann mit Azure-Ressourcen-Manager-Vorlagen zum Bereitstellen eines HDInsight-Clusters verwendet werden, zusammen mit zugeordneten Speicherkonten und anderen Diensten.

Bei Azure-Ressourcen-Manager-Vorlagen handelt es sich um JSON-Dokumente, mit denen eine __Ressourcengruppe__ und alle darin enthaltenen Ressourcen (z. B. HDInsight) beschrieben werden. Dieser vorlagenbasierte Ansatz ermöglicht Ihnen das Definieren aller Ressourcen, die Sie für HDInsight innerhalb einer Vorlage benötigen, und das Verwalten von Änderungen an der gesamten Gruppe durch __Bereitstellungen__, mit denen Änderungen an der Gruppe vorgenommen werden.

Mit den folgenden Schritten werden Sie durch die Erstellung eines neuen HDInsight-Clusters mit der Azure-Befehlszeilenschnittstelle und einer Vorlage geführt:

1. Führen Sie die Schritte im Dokument [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) aus, falls Sie die Installation noch nicht durchgeführt haben.

2. Verwenden Sie an einer Befehlszeile oder in einem Terminal oder einer Shell den folgenden Befehl, um Ihr Azure-Abonnement zu authentifizieren:

        azure login

    Sie werden aufgefordert, Ihren Namen und das Kennwort anzugeben. Wenn Sie über mehrere Azure-Abonnements verfügen, können Sie `azure account set <subscriptionname>` zum Festlegen des Abonnements verwenden, das von den Befehlen der Azure-Befehlszeilenschnittstelle genutzt werden soll.

3. Wechseln Sie mit dem folgenden Befehl in den Azure-Ressourcen-Manager-Modus:

        azure config mode arm

4. Erstellen Sie eine Vorlage für Ihren HDInsight-Cluster. Unten sind einige einfache Beispielvorlagen angegeben:

    * [Linux-basierter Cluster mit öffentlichem SSH-Schlüssel](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpublickey)
    * [Linux-basierter Cluster mit Kennwort für das SSH-Konto](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpassword)

    Mit beiden Vorlagen wird auch das von HDInsight verwendete Azure-Speicherkonto erstellt.

    Sie benötigen die Dateien __azuredeploy.json__ und __azuredeploy.parameters.json__.

5. Öffnen Sie die Datei __azuredeploy.parameters.json__ in einem Editor, und geben Sie Werte für die Elemente im Abschnitt `parameters` an:

    * __location__: Das Rechenzentrum, in dem die Ressourcen erstellt werden. Sie können den Abschnitt `location` in der Datei __azuredeploy.json__ anzeigen, um eine Liste der zulässigen Standorte zu erhalten.
    * __clusterName__: Der Name des HDInsight-Clusters. Dieser Name muss eindeutig sein, da für die Bereitstellung sonst ein Fehler auftritt.
    * __clusterStorageAccountName__: Der Name des Azure-Speicherkontos, das für den HDInsight-Cluster erstellt wird. Dieser Name muss eindeutig sein, da für die Bereitstellung sonst ein Fehler auftritt.
    * __clusterLoginPassword__: Das Kennwort für den Cluster-Administratorbenutzer. Dies sollte ein sicheres Kennwort sein, weil damit auf Websites und REST-Dienste im Cluster zugegriffen wird.
    * __sshUserName__: Der Name des ersten SSH-Benutzers, der für diesen Cluster erstellt wird. SSH wird verwendet, um mit diesem Konto per Remoteverbindung auf diesen Cluster zuzugreifen. Der Name muss eindeutig sein, und es darf sich nicht um den Namen eines Kontos handeln, das im Cluster bereits verwendet wird. Beispiele für ungültige Namen sind „root“, „storm“ und „hbase“.
    * __sshPublicKey__: Wenn Sie die Vorlage nutzen, die einen öffentlichen SSH-Schlüssel erfordert, müssen Sie den öffentlichen Schlüssel in dieser Zeile hinzufügen. Weitere Informationen zum Generieren von öffentlichen Schlüsseln und zu ihrer Verwendung finden Sie in den folgenden Artikeln:

        * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: Wenn Sie die Vorlage verwenden, die ein SSH-Kennwort erfordert, müssen Sie in dieser Zeile ein Kennwort hinzufügen.

    Speichern und schließen Sie die Datei, wenn Sie dies erledigt haben.

5. Gehen Sie wie folgt vor, um eine leere Ressourcengruppe zu erstellen. Ersetzen Sie __RESOURCEGROUPNAME__ durch den Namen, den Sie für diese Gruppe verwenden möchten. Ersetzen Sie __LOCATION__ durch das Rechenzentrum, in dem die Gruppe erstellt werden soll:

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]Wenn der Name des Standorts Leerzeichen enthält, sollten Sie ihn in doppelte Anführungszeichen setzen. Beispiel: „USA, Mitte/Süden“.

6. Verwenden Sie den folgenden Befehl, um die erste Bereitstellung für diese Ressourcengruppe zu erstellen. Ersetzen Sie __PATHTOTEMPLATE__ durch den Pfad zur Vorlagendatei __azuredeploy.json__. Ersetzen Sie __PATHTOPARAMETERSFILE__ durch den Pfad zur Datei __azuredeploy.parameters.json__. Ersetzen Sie __RESOURCEGROUPNAME__ durch den Namen der Gruppe, die Sie im vorherigen Schritt erstellt haben:

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Nachdem die Bereitstellung akzeptiert wurde, sollte eine Meldung der folgenden Art angezeigt werden: `group deployment create command ok`.

7. Es kann einige Zeit dauern, bis die Bereitstellung abgeschlossen ist (ca. 15 Minuten). Sie können Informationen zur Bereitstellung mit dem folgenden Befehl anzeigen. Ersetzen Sie __RESOURCEGROUPNAME__ durch den Namen der Ressourcengruppe aus dem vorherigen Schritt:

        azure group log show -l RESOURCEGROUPNAME

    Wenn während der Bereitstellung ein Fehler auftritt, erhalten Sie mit dem folgenden Befehl weitere Informationen zum Fehler:

        azure group log show -l -v RESOURCEGROUPNAME

### <a id="powershell"></a> Verwenden von Azure PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Azure steuern und automatisieren können. Dieser Abschnitt enthält Anweisungen für die Bereitstellung eines HDInsight-Clusters mit Azure PowerShell. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md). Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md). Eine Liste der HDInsight Windows PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe von Azure PowerShell ausgeführt werden:

- Erstellen einer Azure-Ressourcengruppe
- Erstellen eines Azure-Speicherkontos
- Erstellen eines Azure-Blobcontainers
- Erstellen eines HDInsight-Clusters

Die beiden wichtigsten Parameter, die für die Bereitstellung von Linux-Clustern festgelegt werden müssen, sind diejenigen zum Angeben des Typs des Betriebssystems und der Details zum SSH-Benutzer:

- Stellen Sie sicher, dass Sie den Parameter **- OSType** als **Linux** angeben.
- Um SSH für Remotesitzungen mit dem Cluster zu verwenden, können Sie entweder das SSH-Benutzerkennwort oder den öffentlichen SSH-Schlüssel angeben. Wenn Sie sowohl das SSH-Benutzerkennwort und als auch den öffentlichen SSH-Schlüssel angeben, wird der Schlüssel ignoriert. Wenn Sie den SSH-Schlüssel für Remotesitzungen verwenden möchten, müssen Sie bei der Aufforderung zur Eingabe ein leeres SSH-Kennwort angeben. Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


			# Use the new Azure Resource Manager mode
			Switch-AzureMode AzureResourceManager

			###########################################
			# Create required items, if none exist
			###########################################

			# Sign in
			Add-AzureAccount

			# Select the subscription to use
			$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
			Select-AzureSubscription -SubscriptionName $subscriptionName

			# Register your subscription to use HDInsight
			Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

			# Create an Azure Resource Group
			$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
			$location = "<Location>"                        # For example, "West US"
			New-AzureResourceGroup -Name $resourceGroupName -Location $location

			# Create an Azure Storage account
			$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
			New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

			# Create an Azure Blob Storage container
			$containerName = "<ContainerName>"              # Provide a container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
			$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
			New-AzureStorageContainer -Name $containerName -Context $destContext

			###########################################
			# Create an HDInsight Cluster
			###########################################

			# Skip these variables if you just created them
			$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
			$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
			$containerName = "<ContainerName>"              # Provide the container name
			$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

			# Set these variables
			$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
			$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
			$credentials = Get-Credential
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]Die Werte, die Sie für **$clusterCredentials** angeben, werden verwendet, um das Hadoop-Benutzerkonto für den Cluster zu erstellen. Sie verwenden dieses Konto für die Verbindung mit dem Cluster. Die Werte, die Sie für **$sshCredentials** angeben, werden verwendet, um den SSH-Benutzer für den Cluster zu erstellen. Sie verwenden dieses Konto zum Starten einer SSH-Remotesitzung mit dem Cluster und Ausführen von Aufträgen. Wenn Sie im Azure-Portal die Option "Schnellerfassung" verwenden, um einen Cluster bereitzustellen, ist der Hadoop-Standardbenutzername "admin" und der SSH-Standardbenutzername "ddiuser".

Die Bereitstellung des Clusters kann einige Minuten dauern.

![HDI.CLI.Provision][image-hdi-ps-provision]



###<a id="sdk"></a>Verwenden des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in einer .NET Framework-Anwendung. Gehen Sie wie im Folgenden beschrieben vor, um ein Visual Studio-Konsolenanwendungsprojekt zu erstellen und den Code zum Erstellen eines Clusters einzufügen.

**So erstellen Sie eine C#-Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013 oder 2015.
2. Erstellen Sie ein neues Visual Studio-Projekt mit den folgenden Einstellungen:

	|Eigenschaft|Wert|
	|--------|-----|
	|Vorlage|Vorlagen/Visual C#/Windows/Konsolenanwendung|
	|Name|CreateHDICluster|

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.
6. Führen Sie den folgenden Befehl in der Konsole aus, um die Pakete zu installieren:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Diese Befehle installieren .NET-Bibliotheken und fügen Verweise zum aktuellen Visual Studio-Projekt hinzu.

6. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Program.cs**, um sie zu öffnen, fügen Sie den folgenden Code ein, und geben Sie Werte für die Variablen an:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const string NewClusterVersion = "3.2";
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;

		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

		        private const string NewClusterSshUserName = "sshuser";
		        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
					Comment: ""rsa-key-20150731""
					AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
					gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
					yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
					WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
					pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
					zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
					---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Ersetzen Sie die Klassenmemberwerte.

7. Drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Sie werden auch zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.



## Nächste Schritte
In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters (Hadoop) unter Linux kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Arbeiten mit HDInsight unter Linux](hdinsight-hadoop-linux-information.md): Einführung in die Nuancen des Arbeitens mit einem HDInsight-Cluster unter Linux.
- [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md): Erfahren Sie, wie Sie Ihren Linux-basierten Hadoop-Cluster in HDInsight mithilfe von Ambari Web oder der Ambari-REST-API überwachen und verwalten.
- [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md): Informationen zu den verschiedenen Möglichkeiten zum Ausführen von MapReduce-Aufträgen in einem Cluster.
- [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md): Informationen zu den verschiedenen Möglichkeiten zum Anwenden einer Hive-Abfrage auf einen Cluster.
- [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md): Informationen zu den verschiedenen Möglichkeiten zum Ausführen eines Pig-Auftrags in einem Cluster.
- [Verwenden von Azure Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md): Erfahren Sie, wie HDInsight Azure-Blobspeicher zum Speichern von Daten für HDInsight-Cluster verwendet.
- [Hochladen von Daten in HDInsight](hdinsight-upload-data.md): Erfahren Sie, wie Sie mit Daten arbeiten, die in Azure-Blobspeicher für einen HDInsight-Cluster gespeichert sind.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Auflisten und Anzeigen von Clustern"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Verwenden von Sqoop mit HDInsight"

<!---HONumber=Oct15_HO2-->