<properties
	pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie den HDInsight-Dienst verwalten. Erstellen Sie ein HDInsight-Cluster, öffnen Sie die interaktive JavaScript-Konsole, und öffnen Sie die Hadoop-Befehlszeile."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/06/2015"
	ms.author="larryfr"/>

# Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Vorschauportal

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]


Mithilfe des [Azure-Vorschauportals][preview-portal] können Sie Linux-basierte Hadoop-Cluster in Azure HDInsight bereitstellen und verwalten.

> [AZURE.NOTE]Die Schritte in diesem Dokument gelten für Linux-basierte Hadoop-Cluster. Informationen zur Verwendung von Windows-basierten Clustern finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Vorschauportal](hdinsight-administer-use-management-portal.md).


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Andere Tools zum Verwalten von HDInsight
Neben dem Azure-Portal stehen auch andere Tools zum Verwalten von HDInsight zur Verfügung.

- [Verwalten von HDInsight mithilfe der Azure-Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md): Die Azure-Befehlszeilenschnittstelle ist ein plattformübergreifendes Befehlszeilentool, mit dem Sie Azure-Dienste verwalten können.

- [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md): Azure PowerShell stellt PowerShell-Cmdlets zum Verwalten von Azure-Diensten bereit.

## Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Bereitstellen von HDInsight-Clustern

HDInsight-Cluster können wie folgt im Azure-Portal bereitgestellt werden:

1. Melden Sie sich beim [Azure-Vorschauportal][preview-portal] an.

2. Wählen Sie **NEU**, __Datenanalyse__ und anschließend __HDInsight__ aus.

	![Erstellen eines neuen Clusters im Azure-Vorschauportal](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. Geben Sie einen __Clusternamen__ ein, und wählen Sie dann den gewünschten __Clustertyp__ aus. Wenn der __Clustername__ verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	![Clustername, Clustertyp und Betriebssystemtyp](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. Falls Sie mehrere Abonnements besitzen, wählen Sie den Eintrag __Abonnement__ aus, um das Azure-Abonnement für den Cluster auszuwählen.

5. Wählen Sie den Eintrag __Ressourcengruppe__ aus, um eine Liste der vorhandenen Ressourcengruppen anzuzeigen, und wählen Sie dann die Gruppe aus, in der der Cluster erstellt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf __Neu erstellen__ klicken und den Namen der neuen Gruppe eingeben. Wenn der neue Gruppenname verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	> [AZURE.NOTE]Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).

6. Wählen Sie __Anmeldeinformationen__ aus, und geben Sie dann ein __Clusteranmeldekennwort__ für den __Clusterbenutzernamen__ ein. Außerdem müssen Sie einen __SSH-Benutzernamen__ und entweder ein __Kennwort__ oder einen __öffentlichen Schlüssel__ zum Authentifizieren des SSH-Benutzers eingeben. Klicken Sie abschließend auf die Schaltfläche __Auswählen__, um die Anmeldeinformationen festzulegen. Remotedesktop wird in diesem Dokument nicht verwendet und muss daher nicht aktiviert werden.

	![Blatt „Clusteranmeldeinformationen“](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE]SSH wird verwendet, um remote über eine Befehlszeile auf den HDInsight-Cluster zuzugreifen. Der hier verwendete Benutzername und das Kennwort oder der öffentliche Schlüssel werden zur Herstellung einer Verbindung mit dem Cluster über SSH verwendet. Der SSH-Benutzername muss eindeutig sein, da mit ihm auf allen HDInsight-Clusterknoten ein Benutzerkonto erstellt wird. Folgende Kontonamen sind für die Verwendung durch Dienste auf dem Cluster reserviert und __sollten nicht__ als SSH-Benutzername verwendet werden:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows)

7. Wählen Sie den Eintrag __Datenquelle__ aus, um eine vorhandene Datenquelle auszuwählen, oder erstellen Sie eine neue Datenquelle.

	![Blatt „Datenquelle“](./media/hdinsight-administer-use-portal-linux/datasource.png)

	Zurzeit kann ein Azure-Speicherkonto als Datenquelle für einen HDInsight-Cluster ausgewählt werden. Die Einträge auf dem Blatt __Datenquelle__ werden im Folgenden erläutert.

	- __Auswahlmethode__: Wählen Sie __Aus allen Abonnements__ aus, damit die Speicherkonten in Ihren Abonnements durchsucht werden können. Wählen Sie __Zugriffsschlüssel__ aus, wenn Sie den __Speichernamen__ und __Zugriffsschlüssel__ eines vorhandenen Speicherkontos eingeben möchten.

	- __Neu erstellen__: Hiermit können Sie ein neues Speicherkonto erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.

	- __Standardcontainer auswählen__: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.

	- __Standort__: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird.

		> [AZURE.IMPORTANT]Durch die Auswahl des Standorts für die Standarddatenquelle wird auch der Standort des HDInsight-Clusters festgelegt. Der Cluster und die Standarddatenquelle müssen sich in der gleichen Region befinden.

	- __Auswählen__: Hiermit speichern Sie die Datenquellenkonfiguration.

	
8. Wählen Sie __Knotenpreistarife__ aus, um Informationen zu den Knoten anzuzeigen, die für diesen Cluster erstellt werden. Die Anzahl von Workerknoten ist standardmäßig auf __4__ festgelegt.


	Die vorkalkulierten Kosten für den Cluster werden unten auf dem Blatt angezeigt.

	![Blatt „Knotenpreistarife“](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

	Klicken Sie auf die Schaltfläche __Auswählen__, um die Informationen zu den __Knotenpreistarifen__ zu speichern.

9. Wählen Sie __Optionale Konfiguration__ aus. Auf diesem Blatt können Sie die folgenden Elemente konfigurieren:

	* __HDInsight-Version__: Die für den Cluster verwendete HDInsight-Version. Weitere Informationen zu den HDInsight-Versionen finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

	* __Externe Metastores__: Hier können Sie eine SQL-Datenbank auswählen, die zum Speichern der Konfigurationsinformationen für Oozie und Hive verwendet wird. Dadurch können Sie die Konfiguration beim Löschen und Neuerstellen eines Clusters wiederverwenden, anstatt die Hive- und Oozie-Konfiguration jedes Mal neu erstellen zu müssen.

	* __Virtuelles Netzwerk__: Hiermit können Sie den HDInsight-Cluster im gleichen Netzwerk platzieren wie die anderen Ressourcen, z. B. eine SQL-Datenbank oder ein virtueller Azure-Computer. Wenn Ressourcen in einem virtuellen Netzwerk platziert werden, können sie direkt miteinander kommunizieren und umgehen so die öffentlichen Gateways, die eingehenden Datenverkehr aus dem Internet verarbeiten. Informationen zu den Vorteilen, die virtuelle Azure-Netzwerke für HDInsight bieten, finden Sie unter [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

		> [AZURE.IMPORTANT]Sie müssen das virtuelle Azure-Netzwerk vor dem HDInsight-Cluster erstellen, da Sie in einer HDInsight-Konfiguration kein neues Netzwerk erstellen können.
		>
		> Zurzeit (25.08.2015) besteht die Einschränkung, das in einem virtuellen Azure-Netzwerk nur ein einziger Linux-basierter HDInsight-Knoten vorhanden sein darf.
        >
        > Sie können kein virtuelles Azure-Netzwerk der Version 1 (klassisch) mit Linux-basiertem HDInsight verwenden. Das virtuelle Netzwerk muss Version 2 aufweisen (Azure-Ressourcen-Manager), damit es beim Erstellen des Clusters im Azure-Vorschauportal als Option angezeigt wird bzw. beim Erstellen eines Clusters über die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwendet werden kann.
        >
        > Wenn Ressourcen in einem v1-Netzwerk vorhanden sind und diese Ressourcen über ein virtuelles Netzwerk direkt auf HDInsight zugreifen können sollen, finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md) Informationen zum Erstellen einer Verbindung zwischen einem virtuellen v2-Netzwerk und einem virtuellen v1-Netzwerk. Nachdem diese Verbindung eingerichtet wurde, können Sie den HDInsight-Cluster im virtuellen v2-Netzwerk erstellen.

	* __Skriptaktionen__: Hiermit können Sie Bash-Skripts angeben, die den HDInsight-Cluster während der Bereitstellung anpassen. Beispielsweise gibt es ein [Skript zur Installation von Hue](hdinsight-hadoop-hue-linux.md) (einem grafischen Client für den Einsatz mit Hadoop). Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

	* __Azure-Speicherschlüssel__: Hiermit können Sie dem HDInsight-Server weitere Speicherkonten zuordnen.

		> [AZURE.NOTE]HDInsight kann nur auf Azure-Speicherkonten zugreifen, die als Standarddatenspeicher verwendet werden, in diesem Konfigurationsabschnitt hinzugefügt wurden oder öffentlich zugänglich sind.

	![Blatt „Optionale Konfiguration“](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. Stellen Sie sicher, dass __An Startmenü anheften__ ausgewählt ist, und klicken Sie dann auf __Erstellen__. Der Cluster wird erstellt, und dem Startmenü Ihres Azure-Portals wird eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster bereitgestellt wird. Sobald die Bereitstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.

	| Während der Bereitstellung | Nach Abschluss der Bereitstellung |
	| ------------------ | --------------------- |
	| ![Bereitstellungsanzeige im Startmenü](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![Kachel für einen bereitgestellten Cluster](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Bereitstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter __Benachrichtigungen__ überprüfen.

## Verwalten eines Clusters

Wenn Sie einen Cluster im Azure-Vorschauportal auswählen, werden grundlegende Informationen zum Cluster angezeigt, z. B. der Name, die Ressourcengruppe, das Betriebssystem und die URL für das Cluster-Dashboard (wird für den Zugriff auf Ambari Web für Linux-Cluster verwendet.)

![Clusterdetails](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Im Folgenden werden die Symbole oben auf diesem Blatt und in den Abschnitten __Zusammenfassung__ und __QuickLinks__ erläutert:

* __Einstellungen__ und __Alle Einstellungen__: Zeigt das Blatt __Einstellungen__ für den Cluster an, über das Sie auf detaillierte Konfigurationsinformationen für den Cluster zugreifen können.

* __Dashboard__, __Cluster-Dashboard__ und __URL__: Über diese Optionen können Sie auf das Cluster-Dashboard zugreifen (Ambari Web für Linux-basierte Cluster).

* __Secure Shell__: Für den Zugriff auf den Cluster über SSH erforderliche Informationen.

* __Cluster skalieren__: Dient zum Ändern der Anzahl von Workerknoten für den Cluster.

* __Löschen__: Löscht den HDInsight-Cluster.

* __Schnellstart__ (![Cloud- und Blitzsymbol = Schnellstart](./media/hdinsight-administer-use-portal-linux/quickstart.png)): Zeigt hilfreiche Informationen für die ersten Schritte mit HDInsight an.

* __Benutzer__ (![Benutzersymbol](./media/hdinsight-administer-use-portal-linux/users.png)): Dient zum Festlegen der Berechtigungen für die _Portalverwaltung_ dieses Clusters für andere Benutzer in Ihrem Azure-Abonnement.

	> [AZURE.IMPORTANT]Die hier vorgenommenen Einstellungen betreffen _nur_ den Zugriff und die Berechtigungen für diesen Cluster im Azure-Vorschauportal und haben keine Auswirkung darauf, wer eine Verbindung mit dem HDInsight-Cluster herstellen oder Aufträge übermitteln kann.

* __Tags__ (![Tagsymbol](./media/hdinsight-administer-use-portal-linux/tags.png)): Mithilfe von Tags können Sie Schlüssel-Wert-Paare festlegen, um eine benutzerdefinierte Taxonomie für Ihre Cloud Services zu definieren. Sie können z. B. einen Schlüssel mit dem Namen __Projekt__ erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.

* __Dokumentation__: Links zur Dokumentation für Azure HDInsight.

> [AZURE.IMPORTANT]Um die vom HDInsight-Cluster bereitgestellten Dienste zu verwalten, müssen Sie Ambari Web oder die Ambari-REST-API verwenden. Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="scaling"></a>Skalieren

Zum Skalieren eines Clusters über das Portal wählen Sie den HDInsight-Cluster und anschließend __Cluster skalieren__ aus. Geben Sie die __Anzahl von Workerknoten__ ein, die Sie für den Cluster festlegen möchten, und klicken Sie auf __Speichern__.

![Dialogfeld für die Skalierung](./media/hdinsight-administer-use-portal-linux/scaling.png)

Weitere Informationen zum Skalieren von Vorgängen finden Sie unter [Informationen zur Verwendung von HDInsight unter Linux](hdinsight-hadoop-linux-information.md#scaling).

## Überwachen eines Clusters

Der Abschnitt __Nutzung__ des Blatts für den HDInsight-Cluster enthält Informationen zur Anzahl von Kernen, die für Ihr Abonnement zur Verwendung mit HDInsight verfügbar sind, und zur Anzahl von Kernen, die diesem Cluster zugeordnet sind. Es wird auch angezeigt, wie die Kerne für die Knoten innerhalb dieses Clusters zugeordnet werden.

![Informationen zur Nutzung](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]Um die vom HDInsight-Cluster bereitgestellten Dienste zu überwachen, müssen Sie Ambari Web oder die Ambari-REST-API verwenden. Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md).

## Nächste Schritte
In diesem Artikel haben Sie erfahren, wie ein HDInsight-Cluster mit dem Azure-Portal erstellt und wie das Hadoop-Befehlszeilentool geöffnet wird. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Verwalten von HDInsight mit der Azure-CLI](hdinsight-administer-use-command-line.md)
* [Bereitstellen von HDInsight-Clustern](hdinsight-provision-clusters.md)
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Erste Schritte mit Azure HDInsight](../hdinsight-get-started.md)
* [Welche Hadoop-Version wird in Azure HDInsight verwendet?](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=Oct15_HO1-->