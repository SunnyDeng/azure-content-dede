<properties
	pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie den HDInsight-Dienst verwalten. Erstellen Sie ein HDInsight-Cluster, öffnen Sie die interaktive JavaScript-Konsole, und öffnen Sie die Hadoop-Befehlszeile."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal

Mit dem [Azure-Portal](https://manage.windowsazure.com) können Sie Hadoop-Cluster in Azure HDInsight bereitstellen, das Hadoop-Benutzerkennwort ändern und RDP (Remotedesktopprotokoll) aktivieren, sodass Sie auf die Hadoop-Befehlskonsole im Cluster zugreifen können.

## Andere Tools zum Verwalten von HDInsight
Neben dem Azure-Portal stehen auch andere Tools zum Verwalten von HDInsight zur Verfügung.

- Weitere Informationen zum Verwalten von HDInsight mit Azure PowerShell finden Sie unter [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md).

- Weitere Informationen zum Verwalten von HDInsight mit der Azure-CLI finden Sie unter [Verwalten von HDInsight mit der Azure-CLI](hdinsight-administer-use-command-line.md).

##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure-Speicherkonto** – Ein HDInsight-Cluster verwendet einen Azure-Blobspeichercontainer als Standarddateisystem. Weitere Informationen über Azure-Blobspeicher und dessen nahtlose Integration in HDInsight-Cluster finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md). Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).


##Bereitstellen von HDInsight-Clustern

Mithilfe der Option "Schnellerfassung" oder "Benutzerdefiniert erstellen" können Sie HDInsight-Cluster über das Azure-Portal bereitstellen. Anweisungen finden Sie unter den folgenden Links:

- [Bereitstellen eines Clusters mithilfe von "Schnellerfassung"](../hdinsight-get-started.md#provision)
- [Bereitstellen eines Clusters mithilfe von "Benutzerdefiniert erstellen"](hdinsight-provision-clusters.md#portal)

[AZURE.INCLUDE [Datencenter-Liste](../../includes/hdinsight-pricing-data-centers-clusters.md)]


##Anpassen von HDInsight-Clustern

In HDInsight kann eine Vielzahl von Hadoop-Komponenten verwendet werden. Eine Liste der überprüften und unterstützten Komponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](hdinsight-component-versioning.md). Sie können HDInsight mit einer der folgenden Optionen anpassen:

- Verwenden Sie Skriptaktion, um benutzerdefinierte Skripts auszuführen, mit denen ein Cluster angepasst werden kann, um eine Clusterkonfiguration zu ändern oder um benutzerdefinierte Komponenten wie Giraph oder Solr zu installieren. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktion](hdinsight-hadoop-customize-cluster.md).
- Verwenden Sie die Clusteranpassungsparameter im HDInsight .NET-SDK oder in Azure PowerShell während der Clusterbereitstellung. Diese Konfigurationsänderungen bleiben während der gesamten Lebensdauer des Clusters erhalten und werden nicht vom Reimaging von Clusterknoten beeinträchtigt, das die Azure-Plattform regelmäßig zu Wartungszwecken durchführt. Weitere Informationen zur Verwendung der Clusteranpassungsparameter finden Sie unter [Bereitstellen eines HDInsight-Clusters](hdinsight-provision-clusters.md).
- Einige systemeigene Java-Komponenten wie Mahout und Cascading können auf dem Cluster als JAR-Dateien ausgeführt werden. Diese JAR-Dateien können an Azure-Blobspeicher verteilt und mit den Verfahren zur Übermittlung von Hadoop-Aufträgen an HDInsight-Cluster gesendet werden. Weitere Informationen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE]Wenn bei der Bereitstellung von JAR-Dateien für HDInsight-Cluster oder beim Aufrufen von JAR-Dateien für HDInsight-Cluster Probleme auftreten, wenden Sie sich an den [Microsoft-Support](http://azure.microsoft.com/support/options/).

	> Cascading wird von HDInsight nicht unterstützt, und es steht kein Microsoft-Support dafür zur Verfügung. Listen der unterstützten Komponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](hdinsight-component-versioning.md).


Die Installation von benutzerdefinierter Software auf dem Cluster über eine Remotedesktopverbindung wird nicht unterstützt. Speichern Sie nach Möglichkeit keine Dateien auf den Laufwerken des Hauptknotens, da diese verloren gehen, wenn Sie die Cluster neu erstellen müssen. Wir empfehlen die Speicherung von Dateien im Azure-Blobspeicher. Blobspeicher ist persistent.

##Ändern des Benutzernamens und Kennworts für einen HDInsight-Cluster
Ein HDInsight-Cluster kann über zwei Benutzerkonten verfügen. Das Benutzerkonto des HDInsight-Clusters wird während der Bereitstellung erstellt. Sie können auch ein RDP-Benutzerkonto erstellen, um über das RDP auf den Cluster zuzugreifen. Weitere Informationen finden Sie unter [So aktivieren Sie den Remotedesktopzugriff](#connect-to-hdinsight-clusters-by-using-rdp).

**So ändern Sie den Benutzernamen und das Kennwort für einen HDInsight-Cluster**

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, dessen Benutzernamen und Kennwort Sie zurücksetzen möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie neben **HADOOP-DIENSTE** auf **AUS**.
6. Klicken Sie unten auf der Seite auf **SPEICHERN**, und warten Sie, bis die Deaktivierung abgeschlossen ist.
7. Nach der Deaktivierung des Diensts klicken Sie neben **HADOOP-DIENSTE** auf **EIN**.
8. Geben Sie unter **BENUTZERNAME** und **NEUES KENNWORT** den neuen Benutzernamen bzw. das Kennwort für den Cluster ein.
8. Klicken Sie auf **SPEICHERN**.


##Herstellen einer Verbindung zu HDInsight-Clustern mit RDP

Mit den Anmeldeinformationen, die Sie bei der Erstellung des Clusters für dieses festgelegt haben, können Sie auf die Dienste im Cluster zugreifen, nicht jedoch über eine Remotedesktopverbindung auf den Cluster selbst. Der Remotedesktopzugriff ist standardmäßig deaktiviert, daher müssen nach der Erstellung einige zusätzliche Konfigurationsschritte durchgeführt werden, um den direkten Zugriff auf den Cluster mit dieser Methode zu ermöglichen.

**So aktivieren Sie den Remotedesktopzugriff**

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, zu dem Sie eine Verbindung herstellen möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie unten auf der Seite auf **REMOTE AKTIVIEREN**.
6. Geben Sie im Assistenten **Remotedesktop konfigurieren** einen Benutzernamen und ein Kennwort für den Remotedesktop ein. Der Benutzername darf nicht mit dem Namen identisch sein, mit dem Sie den Cluster erstellt haben (bei Verwendung der Option "Schnellerfassung" standardmäßig **admin**). Geben Sie ein Ablaufdatum in das Feld **GÜLTIG BIS** ein. Beachten Sie, dass das Ablaufdatum in der Zukunft und maximal 90 Tage nach dem heutigen Tag liegen muss. Als Ablaufuhrzeit wird standardmäßig Mitternacht des angegebenen Datums angenommen. Klicken Sie dann auf das Häkchen.

	![HDI.RDPBenutzerErstellen][image-hdi-create-rpd-user]


> [AZURE.NOTE]Mit dem HDInsight .NET SDK können Sie auch Remotedesktop auf einem Cluster aktivieren. Verwenden Sie die **EnableRdp**-Methode folgendermaßen für das HDInsight-Client-Objekt: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Um Remotedesktop auf dem Cluster zu deaktivieren, können Sie auch **client.DisableRdp(clustername, location)** verwenden. Weitere Informationen zu diesen Methoden finden Sie unter [HDInsight .NET SDK-Referenz](http://go.microsoft.com/fwlink/?LinkId=529017). Dies gilt nur für HDInsight-Cluster unter Windows.



> [AZURE.NOTE]Nach der Aktivierung von RDP für einen Cluster müssen Sie die Seite aktualisieren, bevor Sie eine Verbindung zum Cluster herstellen können.

**So stellen Sie eine Verbindung über RDP mit dem Cluster her**

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, zu dem Sie eine Verbindung herstellen möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie auf **VERBINDEN**, und befolgen Sie die Anweisungen.

##Erstellen eines selbstsignierten Zertifikats

Wenn Sie mithilfe des .NET-SDK Vorgänge auf dem Cluster ausführen möchten, müssen Sie ein selbstsigniertes Zertifikat auf der Arbeitsstation erstellen und das Zertifikat auch in Ihr Azure-Abonnement hochladen. Dies ist eine einmalige Aufgabe. Sie können dasselbe Zertifikat auf anderen Computern installieren, solange es gültig ist.

**So erstellen Sie ein selbstsigniertes Zertifikat**

1. Erstellen Sie ein selbstsigniertes Zertifikat für die Authentifizierung der Anfragen. Zum Erstellen des Zertifikats können Sie Internet Information Services (IIS) oder [Makecert](http://go.microsoft.com/fwlink/?LinkId=534000) verwenden.

2. Navigieren Sie zum Speicherort des Zertifikats, klicken Sie mit der rechten Maustaste auf das Zertifikat, klicken Sie auf **Zertifikat installieren** und installieren Sie das Zertifikat im Zertifikatspeicher Ihres Computers. Bearbeiten Sie die Zertifikateigenschaften und geben Sie dem Zertifikat einen Anzeigenamen.

3. Importieren Sie das Zertifikat in das Azure-Portal. Klicken Sie unten links im Portal auf **EINSTELLUNGEN** und anschließend auf **VERWALTUNGSZERTIFIKATE**. Klicken Sie am unteren Rand der Seite auf **HOCHLADEN**, und befolgen Sie die Anweisungen, um die CER-Datei hochzuladen, die Sie im vorigen Schritt erstellt haben.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


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

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, den Sie konfigurieren möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie neben **HADOOP-DIENSTE** auf **EIN** oder **AUS**.  
6. Geben Sie unter **BENUTZERNAME** und **NEUES KENNWORT** den neuen Benutzernamen bzw. das Kennwort für den Cluster ein.
7. Klicken Sie auf **SPEICHERN**.

Diese Aufgabe kann auch mithilfe der folgenden Azure PowerShell-Cmdlets ausgeführt werden:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Siehe [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)

##Öffnen Sie eine Hadoop-Befehlszeile.

Wenn Sie die Verbindung zum Cluster über den Remotedesktop herstellen und die Hadoop-Befehlszeile verwenden möchten, müssen Sie zuerst den Remotedesktopzugriff auf dem Cluster aktivieren, wie im vorherigen Abschnitt beschrieben.

**So öffnen Sie eine Hadoop-Befehlszeile**

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste bereitgestellter Hadoop-Cluster.
3. Klicken Sie auf den HDInsight-Cluster, zu dem Sie eine Verbindung herstellen möchten.
3. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
4. Klicken Sie unten auf der Seite auf **CONNECT**.
5. Klicken Sie auf **Öffnen**.
6. Geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **OK**. Verwenden Sie den Benutzernamen und das Kennwort, den bzw. das Sie bei der Erstellung des Clusters konfiguriert haben.
7. Klicken Sie auf **Ja**.
8. Doppelklicken Sie auf dem Desktop auf **Hadoop Command Line**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Weitere Informationen zu Hadoop-Befehlen finden Sie in der [Hadoop-Befehlsreferenz](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html) (in englischer Sprache).

Im vorherigen Screenshot ist die Hadoop-Versionsnummer im Ordnernamen enthalten. Die Versionsnummer kann sich je nach den im Cluster installierten Hadoop-Komponenten ändern. Sie können mithilfe von Hadoop-Umgebungsvariablen auf diese Ordner verweisen. Zum Beispiel:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%

##Skalieren von Clustern
Siehe [Skalieren von Hadoop-Clustern in HDInsight](hdinsight-hadoop-cluster-scaling.md).

##Nächste Schritte
In diesem Artikel haben Sie erfahren, wie ein HDInsight-Cluster mit dem Azure-Portal erstellt und wie das Hadoop-Befehlszeilentool geöffnet wird. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Verwalten von HDInsight mit der Azure-CLI](hdinsight-administer-use-command-line.md)
* [Bereitstellen von HDInsight-Clustern](hdinsight-provision-clusters.md)
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Erste Schritte mit Azure HDInsight](../hdinsight-get-started.md)
* [Welche Hadoop-Version wird in Azure HDInsight verwendet?](hdinsight-component-versioning.md)

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster-Angebotsseite"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop-Befehlszeile"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png
 

<!---HONumber=July15_HO4-->