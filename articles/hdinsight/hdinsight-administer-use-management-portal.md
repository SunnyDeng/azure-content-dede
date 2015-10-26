<properties
	pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Vorschauportal | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie den HDInsight-Dienst verwalten. Erstellen Sie ein HDInsight-Cluster, öffnen Sie die interaktive JavaScript-Konsole, und öffnen Sie die Hadoop-Befehlszeile."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Vorschauportal

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]

Mit dem [Azure-Vorschauportal][azure-portal] können Sie Hadoop-Cluster in Azure HDInsight bereitstellen, das Hadoop-Benutzerkennwort ändern und RDP (Remotedesktopprotokoll) aktivieren, sodass Sie auf die Hadoop-Befehlskonsole im Cluster zugreifen können.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-management-portal-v1.md)

> [AZURE.NOTE]Die Schritte in diesem Dokument gelten für Windows-basierte Hadoop-Cluster. Informationen zur Verwendung von Linux-basierten Clustern finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Vorschauportal](hdinsight-administer-use-portal-linux.md).



> [AZURE.NOTE]Neben dem Azure-Vorschauportal stehen auch andere Tools zum Verwalten von HDInsight zur Verfügung.
>
> - [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)
> - [Verwalten von HDInsight mit der Azure-CLI](hdinsight-administer-use-command-line.md)

**Voraussetzungen**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure-Speicherkonto** – Ein HDInsight-Cluster verwendet einen Azure-Blobspeichercontainer als Standarddateisystem. Weitere Informationen über Azure-Blobspeicher und dessen nahtlose Integration in HDInsight-Cluster finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md). Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).


##Bereitstellen von HDInsight-Clustern

Informationen zur Bereitstellung mithilfe des Vorschauportals finden Sie unter [Bereitstellen von HDInsight-Clustern](hdinsight-provision-clusters.md#portal).

##Anpassen der Bereitstellung von HDInsight-Clustern

In HDInsight kann eine Vielzahl von Hadoop-Komponenten verwendet werden. Eine Liste der überprüften und unterstützten Komponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](hdinsight-component-versioning.md). Sie können HDInsight mit einer der folgenden Optionen anpassen:

- Verwenden Sie Skriptaktion, um benutzerdefinierte Skripts auszuführen, mit denen ein Cluster angepasst werden kann, um eine Clusterkonfiguration zu ändern oder um benutzerdefinierte Komponenten wie Giraph oder Solr zu installieren. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktion](hdinsight-hadoop-customize-cluster.md).
- Verwenden Sie die Clusteranpassungsparameter im HDInsight .NET-SDK oder in Azure PowerShell während der Clusterbereitstellung. Diese Konfigurationsänderungen bleiben während der gesamten Lebensdauer des Clusters erhalten und werden nicht vom Reimaging von Clusterknoten beeinträchtigt, das die Azure-Plattform regelmäßig zu Wartungszwecken durchführt. Weitere Informationen zur Verwendung der Clusteranpassungsparameter finden Sie unter [Bereitstellen eines HDInsight-Clusters](hdinsight-provision-clusters.md).
- Einige systemeigene Java-Komponenten wie Mahout und Cascading können auf dem Cluster als JAR-Dateien ausgeführt werden. Diese JAR-Dateien können an Azure-Blobspeicher verteilt und mit den Verfahren zur Übermittlung von Hadoop-Aufträgen an HDInsight-Cluster gesendet werden. Weitere Informationen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE]Wenn bei der Bereitstellung von JAR-Dateien für HDInsight-Cluster oder beim Aufrufen von JAR-Dateien für HDInsight-Cluster Probleme auftreten, wenden Sie sich an den [Microsoft-Support](http://azure.microsoft.com/support/options/).

	> Cascading wird von HDInsight nicht unterstützt, und es steht kein Microsoft-Support dafür zur Verfügung. Listen der unterstützten Komponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](hdinsight-component-versioning.md).


Die Installation von benutzerdefinierter Software auf dem Cluster über eine Remotedesktopverbindung wird nicht unterstützt. Speichern Sie nach Möglichkeit keine Dateien auf den Laufwerken des Hauptknotens, da diese verloren gehen, wenn Sie die Cluster neu erstellen müssen. Wir empfehlen die Speicherung von Dateien im Azure-Blobspeicher. Blobspeicher ist persistent.

## Einführung in die Portaloberfläche für Cluster

**So greifen Sie auf den Cluster zu**

1. Melden Sie sich beim [Vorschauportal][azure-portal] an.
2. Klicken Sie im linken Menü auf **Alle durchsuchen**, um das Blatt **Durchsuchen** zu öffnen.
3. Klicken Sie auf **HDInsight-Cluster**, um das Blatt **HDInsight-Cluster** zu öffnen.
4. Klicken Sie auf einen der Cluster in der Liste, um das Blatt für den Cluster zu öffnen:

	![HDInsight-Portal](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.blade.png)

5. Klicken Sie auf **Einstellungen**, um die Konfigurationsdetails anzuzeigen und den Cluster zu konfigurieren:

	![HDInsight-Portal Clustereinstellungen](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	|Einstellung|Beschreibung|
	|-----|-----|
	|Eigenschaften| Clustereigenschaften anzeigen|
	|Azure-Speicherschlüssel| Informationen zum standardmäßigen Azure-Speicherkonto anzeigen |
	|Clusteranmeldung| Zugriff auf HTTP-Webdienste gewähren/widerrufen und Anmeldeinformationen für den Cluster konfigurieren |
	|Externe Metastores| Informationen zum Hive/Oozie-Metastore anzeigen|
	|Cluster skalieren| Anzahl der Workerknoten des Clusters erhöhen/verringern|
	|Remotedesktop| Remotedesktopverbindung aktivieren/deaktivieren, über Remotedesktop eine Verbindung mit dem Cluster herstellen|


##Gewähren/Widerrufen von Zugriff auf HTTP-Dienste

In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Der Zugriff auf diese Dienste wird standardmäßig gewährt. Über das Azure-Portal können Sie den Zugriff widerrufen/gewähren.

>[AZURE.NOTE]Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

**So gewähren/widerrufen Sie den Zugriff auf HTTP-Webdienste**

1. Melden Sie sich beim [Vorschauportal][azure-portal] an.
2. Klicken Sie im linken Menü auf **Alle durchsuchen**, klicken Sie auf **HDInsight-Cluster** und anschließend auf den Clusternamen.
3. Klicken Sie im oberen Menü auf **Einstellungen** und anschließend auf **Clusteranmeldung**.
5. Klicken Sie unter **Clusteranmeldezugriff** auf **EIN** oder **AUS**.  
6. Geben Sie für **Clusterbenutzername** und **Clusteranmeldekennwort** den neuen Benutzernamen bzw. das Kennwort für den Cluster ein.
7. Klicken Sie auf **SPEICHERN**.

	![HDInsight HTTP-Webdienstzugriff gewähren widerrufen](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)


Diese Aufgabe kann auch mithilfe der folgenden Azure PowerShell-Cmdlets ausgeführt werden:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Siehe [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)

##Ändern von Benutzername und Kennwort für einen HDInsight-Cluster
Ein HDInsight-Cluster kann über zwei Benutzerkonten verfügen. Das Benutzerkonto des HDInsight-Clusters wird während der Bereitstellung erstellt. Sie können auch ein RDP-Benutzerkonto erstellen, um über das RDP auf den Cluster zuzugreifen. Weitere Informationen finden Sie unter [So aktivieren Sie den Remotedesktopzugriff](#connect-to-hdinsight-clusters-by-using-rdp).

**So ändern Sie den Benutzernamen und das Kennwort für einen HDInsight-Cluster**

1. Melden Sie sich beim [Vorschauportal][azure-portal] an.
2. Klicken Sie im linken Menü auf **Alle durchsuchen**, klicken Sie auf **HDInsight-Cluster** und anschließend auf den Clusternamen.
3. Klicken Sie im oberen Menü auf **Einstellungen** und anschließend auf **Clusteranmeldung**.
4. Ändern Sie den **Clusterbenutzernamen** und/oder das **Clusteranmeldekennwort**, und klicken Sie dann auf **Speichern**.

	![HDInsight Clusterbenutzernamen ändern Clusterkennwort ändern HTTP-Benutzer](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##Skalieren von Clustern
Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in Azure HDInsight ausgeführten Cluster verwendeten Workerknoten ändern, ohne den Cluster neu erstellen zu müssen.

>[AZURE.NOTE]Es werden nur Cluster mit HDInsight-Versionen ab 3.1.3 unterstützt. Überprüfen Sie ggf. auf der Seite „Eigenschaften“ die Version Ihres Clusters. Siehe [Einführung in die Portaloberfläche für Cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

Auswirkungen der Änderung der Anzahl von Datenknoten für die von HDInsight unterstützten Clustertypen:

- Hadoop

	Sie können die Anzahl der Workerknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.

	Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten zentral herunterskaliert wird, werden einige der Dienste im Cluster neu gestartet. Dies führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.

- HBase

	Sie können Knoten reibungslos Ihrem HBase-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Allerdings können Sie die regionalen Server auch manuell ausgleichen, indem Sie sich am Hauptknoten des Clusters anmelden und in einem Eingabeaufforderungsfenster die folgenden Befehle ausführen:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Weitere Informationen zur Verwendung der HBase-Shell finden Sie unter
- Storm

	Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach erfolgreichen Abschluss des Skalierungsvorgangs müssen Sie die Topologie neu ausgleichen.

	Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:

	* Storm-Webbenutzeroberfläche
	* Befehlszeilenschnittstelle (CLI)

	Weitere Informationen finden Sie in der Dokumentation zu [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	Die Storm-Webbenutzeroberfläche ist für den HDInsight-Cluster verfügbar:

	![HDInsight Storm skalieren ausgleichen](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Es folgt ein Beispiel, wie die Storm-Topologie mithilfe des CLI-Befehls neu ausgeglichen werden kann:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**So skalieren Sie Cluster**

1. Melden Sie sich beim [Vorschauportal][azure-portal] an.
2. Klicken Sie im linken Menü auf **Alle durchsuchen**, klicken Sie auf **HDInsight-Cluster** und anschließend auf den Clusternamen.
3. Klicken Sie im oberen Menü auf **Einstellungen** und anschließend auf **Cluster skalieren**.
4. Geben Sie die **Anzahl von Workerknoten** ein. Die maximal Anzahl von Clusterknoten variiert abhängig vom Azure-Abonnement. Sie können sich an den Azure-Abrechnungssupport wenden, falls Sie diese Begrenzung erhöhen möchten. Die Änderungen, die Sie an der Anzahl von Knoten vorgenommen haben, werden in den Kosteninformationen widergespiegelt.


	![HDInsight Hadoop Hbase Storm Spark skalieren](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##Herstellen einer Verbindung zu HDInsight-Clustern mit RDP

Mit den Anmeldeinformationen, die Sie bei der Erstellung des Clusters für dieses festgelegt haben, können Sie auf die Dienste im Cluster zugreifen, nicht jedoch über eine Remotedesktopverbindung auf den Cluster selbst. Sie können beim oder nach dem Bereitstellen eines Clusters den Remotedesktopzugriff aktivieren. Informationen zum Aktivieren von Remotedesktop bei der Bereitstellung finden Sie unter [Bereitstellen eines HDInsight-Clusters](hdinsight-provision-clusters.md).

**So aktivieren Sie den Remotedesktopzugriff**

1. Melden Sie sich beim [Vorschauportal][azure-portal] an.
2. Klicken Sie im linken Menü auf **Alle durchsuchen**, klicken Sie auf **HDInsight-Cluster** und anschließend auf den Clusternamen.
3. Klicken Sie im oberen Menü auf **Einstellungen** und anschließend auf **Remotedesktop**.
4. Füllen Sie die Felder **Gültig bis**, **Remotedesktopbenutzername** und **Remotedesktopkennwort** aus, und klicken Sie dann auf **Aktivieren**.

	![HDInsight Remotedesktop aktivieren deaktivieren konfigurieren](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	Der Standardwert für „Gültig bis“ ist eine Woche.
> [AZURE.NOTE]Mit dem HDInsight .NET SDK können Sie auch Remotedesktop auf einem Cluster aktivieren. Verwenden Sie die **EnableRdp**-Methode folgendermaßen für das HDInsight-Client-Objekt: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Um Remotedesktop auf dem Cluster zu deaktivieren, können Sie auch **client.DisableRdp(clustername, location)** verwenden. Weitere Informationen zu diesen Methoden finden Sie unter [HDInsight .NET SDK-Referenz](http://go.microsoft.com/fwlink/?LinkId=529017). Dies gilt nur für HDInsight-Cluster unter Windows.

**So stellen Sie eine Verbindung über RDP mit dem Cluster her**

1. Melden Sie sich beim [Vorschauportal][azure-portal] an.
2. Klicken Sie im linken Menü auf **Alle durchsuchen**, klicken Sie auf **HDInsight-Cluster** und anschließend auf den Clusternamen.
3. Klicken Sie im oberen Menü auf **Einstellungen** und anschließend auf **Remotedesktop**.
4. Klicken Sie auf **Verbinden**, und folgen Sie den Anweisungen. Falls „Verbinden“ deaktiviert ist, müssen Sie diese Option zunächst aktivieren. Achten Sie darauf, dass Sie den Benutzernamen und das Kennwort für Remotedesktop verwenden. Es ist nicht möglich die Benutzeranmeldeinformationen des Clusters zu verwenden.

##Öffnen Sie eine Hadoop-Befehlszeile.

Wenn Sie die Verbindung zum Cluster über den Remotedesktop herstellen und die Hadoop-Befehlszeile verwenden möchten, müssen Sie zuerst den Remotedesktopzugriff auf dem Cluster aktivieren, wie im vorherigen Abschnitt beschrieben.

**So öffnen Sie eine Hadoop-Befehlszeile**

1. Stellen Sie per Remotedesktop eine Verbindung mit dem Cluster her.
8. Doppelklicken Sie auf dem Desktop auf **Hadoop Command Line**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Weitere Informationen zu Hadoop-Befehlen finden Sie in der [Hadoop-Befehlsreferenz](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html) (in englischer Sprache).

Im vorherigen Screenshot ist die Hadoop-Versionsnummer im Ordnernamen enthalten. Die Versionsnummer kann sich je nach den im Cluster installierten Hadoop-Komponenten ändern. Sie können mithilfe von Hadoop-Umgebungsvariablen auf diese Ordner verweisen. Beispiel:

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%


##Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie mit dem Azure-Vorschauportal einen HDInsight-Cluster erstellen und das Hadoop-Befehlszeilentool öffnen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Verwalten von HDInsight mit der Azure-CLI](hdinsight-administer-use-command-line.md)
* [Bereitstellen von HDInsight-Clustern](hdinsight-provision-clusters.md)
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Erste Schritte mit Azure HDInsight](../hdinsight-get-started.md)
* [Welche Hadoop-Version wird in Azure HDInsight verwendet?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster-Angebotsseite"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop-Befehlszeile"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=Oct15_HO3-->