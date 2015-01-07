<properties urlDisplayName="Administration" pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal | Azure" metaKeywords="" description="Learn how to administer HDInsight Service. Create an HDInsight cluster, open the interactive JavaScript console, and open the Hadoop command console." metaCanonical="" services="hdinsight" documentationCenter="" title="Manage Hadoop clusters in HDInsight using the Azure Management Portal" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="jgao" />



# Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal

Mit dem Azure-Verwaltungsportal können Sie Hadoop-Cluster in HDInsight bereitstellen, das Hadoop-Benutzerkennwort ändern und RDP aktivieren, sodass Sie auf die Hadoop-Befehlszeile im Cluster zugreifen können. Außer dem Verwaltungsportal stehen auch andere Tools zum Verwalten von HDInsight zur Verfügung. 

- Weitere Informationen zum Verwalten von HDInsight mit Azure PowerShell finden Sie unter [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell].

- Weitere Informationen zum Verwalten von HDInsight mit den plattformübergreifenden Befehlszeilentools finden Sie unter [Verwalten von HDInsight mit der plattformübergreifenden Befehlszeile][hdinsight-admin-cross-platform]. 

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement**. Azure ist eine abonnementbasierte Plattform. Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].


##Themen in diesem Artikel

* [Bereitstellen von HDInsight-Clustern](#create)
* [Anpassen von HDInsight-Clustern](#customize)
* [Ändern von Benutzername und Kennwort für einen HDInsight-Cluster](#password)
* [Herstellen einer Verbindung zu HDInsight-Clustern mit RDP](#rdp)
* [Erstellen eines selbstsignierten Zertifikats](#cert)
* [Gewähren/Widerrufen von Zugriff auf HTTP-Dienste](#httpservice)
* [Öffnen der Hadoop-Befehlszeile](#hadoopcmd)
* [Nächste Schritte](#nextsteps)

##<a id="create"></a> Bereitstellen von HDInsight-Clustern

HDInsight-Cluster können mit verschiedenen Methoden erstellt werden, in diesem Artikel wird lediglich die Verwendung der Option "Schnellerfassung" im Azure-Verwaltungsportal behandelt. Weitere Methoden werden unter [Bereitstellen eines HDInsight-Clusters][hdinsight-provision] erläutert.

HDInsight-Cluster verwenden Azure-Blob-Speichercontainer als Standarddateisystem. Weitere Informationen über Azure-Blob-Speicher und dessen nahtlose Integration in HDInsight-Cluster finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage].

Ein Azure-Speicherkonto muss in demselben Rechenzentrum erstellt werden, in dem der HDInsight-Cluster bereitgestellt werden soll. Derzeit können HDInsight-Cluster in fünf Rechenzentren bereitgestellt werden: 

- Südostasien 
- Nordeuropa
- Westeuropa 
- USA (Ost) 
- USA (West) 

Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].


**So stellen Sie HDInsight-Cluster bereit**

1. Melden Sie sich auf dem [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie im unteren Seitenbereich auf **NEU**, anschließend auf **DATENDIENSTE**, **HDINSIGHT** und zuletzt auf **SCHNELLERFASSUNG**.

3. Geben Sie Werte für **Clustername**, **Clustergröße**, **Cluster-Administratorkennwort** sowie ein Azure-**Speicherkonto** an, und klicken Sie dann auf **HDInsight-Cluster erstellen**. Nachdem der Cluster erstellt wurde und ausgeführt wird, lautet sein Status *Wird ausgeführt*.

	![HDI.QuickCreate][image-cluster-quickcreate]

	Wenn Sie einen Cluster mit der Option "Schnellerfassung" erstellen, lautet der Standardbenutzername für das Hadoop-Benutzerkonto *admin*. Um dem Konto einen anderen Benutzernamen zuzuweisen, können Sie die Option "Benutzerdefiniert erstellen" anstelle der Option "Schnellerfassung" verwenden. Alternativ können Sie den Namen des Kontos nach dessen Bereitstellung zu ändern.

	Wenn Sie einen Cluster mit der Option "Schnellerfassung" erstellen, wird im angegebenen Speicherkonto automatisch ein neuer Container mit dem Namen des HDInsight-Clusters erstellt. Um den Namen des standardmäßig vom Cluster zu verwendenden Containers anzupassen, müssen Sie die Option "Benutzerdefiniert erstellen" verwenden. 

	> [WACOM.NOTE] Sobald Sie ein Azure-Speicherkonto für Ihren HDInsight-Cluster ausgewählt haben, können Sie das Speicherkonto nur noch ändern, indem Sie den Cluster löschen und einen neuen Cluster mit dem gewünschten Speicherkonto erstellen.

4. Klicken Sie auf den neu erstellten Cluster.  Die Landing Page wird angezeigt:

	![HDI.ClusterLanding][image-cluster-landing]


##<a id="customize"></a> Anpassen von HDInsight-Clustern

In HDInsight kann eine Vielzahl von Hadoop-Komponenten verwendet werden. Eine Liste der überprüften und unterstützten Komponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions]. Die HDInsight-Anpassung kann mit einem der folgenden Verfahren durchgeführt werden:

- Sie können die Clusteranpassungsparameter im HDInsight .NET-SDK oder in Azure PowerShell während der Clusterbereitstellung verwenden. Dies bewirkt, dass diese Konfigurationsänderungen während der gesamten Lebensdauer des Clusters erhalten bleiben und sich Reimagings von Clusterknoten, die die Azure-Plattform regelmäßig zu Wartungszwecken durchführt, nicht darauf auswirken. Weitere Informationen zur Verwendung der Clusteranpassungsparameter finden Sie unter [Bereitstellen eines HDInsight-Clusters][hdinsight-provision].
- Einige systemeigene Java-Komponenten wie Mahout und Cascading können als JAR-Dateien auf dem Cluster ausgeführt werden. Diese JAR-Dateien können an Azure-Blob-Speicher (WASB) verteilt und mit den Verfahren zur Übermittelung von Hadoop-Jobs an HDInsight-Cluster gesendet werden. Weitere Informationen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Jobs][hdinsight-submit-jobs]. 


	>[WACOM.NOTE] Wenn Sie Probleme bei der Bereitstellung von JAR-Dateien an HDInsight-Cluster oder beim Aufrufen von JAR-Dateien für HDInsight-Cluster haben, wenden Sie sich an den [Microsoft-Support][hdinsight-support].
	
	> Cascading wird von HDInsight nicht unterstützt, und es steht kein Microsoft-Support dafür zur Verfügung. Listen der unterstützten Komponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions].


Die Installation von benutzerdefinierter Software auf dem Cluster über eine Remotedesktopverbindung wird nicht unterstützt. Speichern Sie nach Möglichkeit keine Dateien auf den Laufwerken des Hauptknotens, da diese verloren gehen, wenn Sie die Cluster neu erstellen müssen. Wir empfehlen die Speicherung von Dateien im Azure-Blob-Speicher. Blob-Speicher ist persistent.

##<a id="password"></a> Ändern von Benutzername und Kennwort für einen HDInsight-Cluster
Ein HDInsight-Cluster kann über zwei Benutzerkonten verfügen.  Das Benutzerkonto des HDInsight-Clusters wird während der Bereitstellung erstellt.  Sie können auch ein RDP-Benutzerkonto erstellen, um über RDP auf den Cluster zuzugreifen. Weitere Informationen finden Sie unter [So aktivieren Sie den Remotedesktopzugriff](#enablerdp).

**So ändern Sie Benutzername und Kennwort für einen HDInsight-Cluster**

1. Melden Sie sich auf dem [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, dessen Benutzernamen und Kennwort Sie zurücksetzen möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie neben **HADOOP-DIENSTE** auf **AUS**.
6. Klicken Sie unten auf der Seite auf **SPEICHERN**, und warten Sie, bis die Deaktivierung abgeschlossen ist.
7. Nach der Deaktivierung des Diensts klicken Sie neben **HADOOP-DIENSTE** auf **EIN**.
8. Geben Sie Werte in die Felder **BENUTZERNAME** und **NEUES KENNWORT** ein.  Dies sind der neue Benutzername und das neue Kennwort für den Cluster.
8. Klicken Sie auf **SPEICHERN**.


##<a id="rdp"></a> Herstellen einer Verbindung zu HDInsight-Clustern mit RDP

Mit den Anmeldeinformationen, die Sie bei der Erstellung des Clusters für dieses festgelegt haben, können Sie auf die Dienste im Cluster zugreifen, nicht jedoch über eine Remotedesktopverbindung auf den Cluster selbst. Der Remotedesktopzugriff ist standardmäßig deaktiviert, daher müssen nach der Erstellung einige zusätzliche Konfigurationsschritte durchgeführt werden, um den direkten Zugriff auf den Cluster mit dieser Methode zu ermöglichen.

**So aktivieren Sie den Remotedesktopzugriff**

1. Melden Sie sich auf dem [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, zu dem Sie eine Verbindung herstellen möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie unten auf der Seite auf **REMOTE AKTIVIEREN**.
6. Geben Sie im Assistenten **Remotedesktop konfigurieren** einen Benutzernamen und ein Kennwort für den Remotedesktop ein. Der Benutzername darf nicht mit dem Namen identisch sein, mit dem Sie den Cluster erstellt haben (bei Verwendung der Option "Schnellerfassung" standardmäßig *admin*). Geben Sie ein Ablaufdatum in das Feld **GÜLTIG BIS** ein. Das Ablaufdatum muss in der Zukunft und maximal eine Woche nach dem aktuellen Tag liegen. Als Ablaufuhrzeit wird standardmäßig Mitternacht des angegebenen Datums angenommen. Klicken Sie dann auf das Häkchen.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]

	Das Ablaufdatum muss in der Zukunft und maximal sieben Tage nach dem aktuellen Tag liegen. Die Uhrzeit ist Mitternacht des ausgewählten Datums.

> [WACOM.NOTE] Nach der Aktivierung von RDP für einen Cluster müssen Sie die Seite aktualisieren, bevor Sie eine Verbindung zum Cluster herstellen können.
 
**So stellen Sie über RDP eine Verbindung zum Cluster her**

1. Melden Sie sich auf dem [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, zu dem Sie eine Verbindung herstellen möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie auf **VERBINDEN**, und befolgen Sie die Anweisungen.

##<a id="cert"></a>Erstellen eines selbstsignierten Zertifikats

Wenn Sie Vorgänge auf dem Cluster mithilfe des .NET-SDK ausführen möchten, müssen Sie ein selbstsigniertes Zertifikat auf der Arbeitsstation erstellen und das Zertifikat zu Ihrem Azure-Abonnement hochladen. Dies ist eine einmalige Aufgabe. Sie können dasselbe Zertifikat auf anderen Computern installieren, solange es gültig ist.

**So erstellen Sie ein selbstsigniertes Zertifikat**

1. Erstellen Sie ein selbstsigniertes Zertifikat für die Authentifizierung der Anfragen. Sie können das Zertifikat mithilfe von IIS oder [makecert][makecert-info] erstellen.
 
2. Navigieren Sie zum Speicherort des Zertifikats, klicken Sie mit der rechten Maustaste auf das Zertifikat, klicken Sie auf **Zertifikat installieren** und installieren Sie das Zertifikat im Zertifikatspeicher Ihres Computers. Bearbeiten Sie die Zertifikateigenschaften und geben Sie dem Zertifikat einen Anzeigenamen.

3. Importieren Sie das Zertifikat in das Azure-Verwaltungsportal. Klicken Sie unten links im Portal auf **Einstellungen** und anschließend auf **Verwaltungszertifikate**. Klicken Sie unten in der Seite auf **Hochladen**, und folgen Sie den Anweisungen, um die .cer-Datei hochzuladen, die Sie im vorigen Schritt erstellt haben.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##<a id="httpservice"></a> Gewähren/Widerrufen von Zugriff auf HTTP-Dienste

In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff über das Verwaltungsportal widerrufen/gewähren. 

>[WACOM.NOTE] Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

**So gewähren/widerrufen Sie den Zugriff auf HTTP-Dienste**

1. Melden Sie sich auf dem [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.
3. Klicken Sie auf den HDInsight-Cluster, den Sie konfigurieren möchten.
4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
5. Klicken Sie neben**HADOOP-DIENSTE** auf **EIN** oder **AUS**.  
6. Geben Sie Werte in die Felder **BENUTZERNAME** und **NEUES KENNWORT** ein.  Dies sind der neue Benutzername und das neue Kennwort für den Cluster.
7. Klicken Sie auf **SPEICHERN**.

Diese Aufgabe kann auch mithilfe der folgenden Azure PowerShell-Cmdlets ausgeführt werden:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Weitere Informationen finden Sie unter [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell].

##<a id="hadoopcmd"></a> Öffnen der Hadoop-Befehlszeile

Wenn Sie die Verbindung zum Cluster über den Remotedesktop herstellen und die Hadoop-Befehlszeile verwenden möchten, müssen Sie zuerst den Remotedesktopzugriff auf den Cluster wie im vorherigen Abschnitt beschrieben aktivieren. 

**So öffnen Sie die Hadoop-Befehlszeile**

1. Melden Sie sich auf dem [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste bereitgestellter Hadoop-Cluster.
3. Klicken Sie auf den HDInsight-Cluster, zu dem Sie eine Verbindung herstellen möchten.
3. Klicken Sie oben auf der Seite auf **KONFIGURATION**.
4. Klicken Sie unten auf der Seite auf **VERBINDEN**.
5. Klicken Sie auf **Öffnen**.
6. Geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **OK**.  Verwenden Sie den Benutzernamen und das Kennwort, den bzw. das Sie bei der Erstellung des Clusters konfiguriert haben.
7. Klicken Sie auf **Ja**.
8. Doppelklicken Sie auf dem Desktop auf **Hadoop Command Line**.
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Weitere Informationen zu Hadoop-Befehlen finden Sie in der [Hadoop-Befehlsreferenz][hadoop-command-reference] (in englischer Sprache).

Im vorherigen Screenshot ist die Hadoop-Versionsnummer im Ordnernamen angegeben. Die Versionsnummer kann sich je nach den im Cluster installierten Hadoop-Komponenten ändern. Sie können mithilfe von Hadoop-Umgebungsvariablen auf diese Ordner verweisen.  Beispiel:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%

##<a id="nextsteps"></a> Nächste Schritte
In diesem Artikel haben Sie erfahren, wie ein HDInsight-Cluster mit dem Verwaltungsportal erstellt und wie das Hadoop-Befehlszeilentool geöffnet wird. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
* [Verwalten von HDInsight mit der plattformübergreifenden Befehlszeile][hdinsight-admin-cross-platform]
* [Bereitstellen von HDInsight-Clustern][hdinsight-provision]
* [Programmgesteuerte Übermittlung von Hadoop-Jobs][hdinsight-submit-jobs]
* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions]

[hdinsight-admin-cross-platform]: ../hdinsight-administer-use-command-line/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-support]: http://azure.microsoft.com/de-de/support/options/
[makecert-info]: http://msdn.microsoft.com/de-de/library/bfsktky3(v=vs.110).aspx

[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/

[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/


[hadoop-command-reference]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!--HONumber=35.1-->
