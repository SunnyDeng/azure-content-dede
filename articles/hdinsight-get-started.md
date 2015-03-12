<properties 
	pageTitle="Erste Schritte bei der Nutzung von mit Hive in HDInsight | Azure" 
	description="Erste Schritte mit Hadoop in HDInsight, einer Big Data-Lösung in der Cloud. Erfahren Sie, wie Sie Cluster bereitstellen und Daten mit Hive abrufen und zur Analyse an Excel ausgeben." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="nitinme"/>


# Erste Schritte bei der Nutzung von mit Hive in HDInsight zur Analyse der Verwendung von Mobiltelefonen

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

Für Ihren Schnelleinstieg in die Nutzung von HDInsight veranschaulicht dieses Lernprogramm die Ausführung einer Hive-Abfrage zum Extrahieren aussagekräftiger Informationen aus unstrukturierten Daten in einem Hadoop-Cluster. Anschließend analysieren Sie die Ergebnisse in Microsoft Excel.


> [AZURE.NOTE] Wenn Sie noch keine Erfahrung mit Hadoop und Big Data haben, erfahren Sie mehr zu den Begriffen [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs] und [Hive][apache-hive]. Wie HDInsight Hadoop in Azure bereitstellt, erfahren Sie unter [Einführung in Hadoop in HDInsight][hadoop-hdinsight-intro].

Im Rahmen der generellen Verfügbarkeit von Azure HDInsight hat Microsoft zudem den HDInsight Emulator für Azure veröffentlicht, der zuvor unter dem Namen *Microsoft HDInsight Developer Preview* bekannt war. Dieser Emulator wurde für Entwicklungsszenarien erstellt und unterstützt nur Umgebungen mit einem Knoten. Informationen zur Verwendung des HDInsight Emulators finden Sie unter [Erste Schritte mit dem HDInsight Emulator][hdinsight-emulator].

> [AZURE.NOTE] Informationen zur Bereitstellung eines HBase-Clusters finden Sie unter [Bereitstellen eines HBase-Clusters in HDInsight][hdinsight-hbase-custom-provision]. Siehe <a href="http://go.microsoft.com/fwlink/?LinkId=510237">"Was ist der Unterschied zwischen den Hadoop und HBase?",</a> um zu verstehen, warum Sie einer dieser Lösungen den Vorzug geben sollten.   

## Welchen Zweck verfolgt dieses Lernprogramm? ##

Angenommen, Sie verfügen über einen großen unstrukturierten Datensatz und möchten Abfragen für diesen ausführen, um aussagekräftige Informationen zu extrahieren. Genau darum geht es in diesem Lernprogramm. Und so geht es:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

Sie können sich auch ein Demovideo dieses Lernprogramms ansehen:

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:


- Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
- Einen Computer mit Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

**Geschätzter Zeitaufwand:** 30 Minuten

##Dieses Lernprogramm umfasst folgende Punkte

* [Erstellen eines Azure-Speicherkontos](#storage)
* [Bereitstellen eines HDInsight-Clusters](#provision)
* [Ausführen von Beispielen im Portal](#sample)
* [Ausführen eines HIVE-Auftrags](#hivequery)
* [Nächste Schritte](#nextsteps)

##<a name="storage"></a>Erstellen eines Azure-Speicherkontos

HDInsight verwendet Azure Blob-Speicher zum Speichern von Daten. Dies wird als *WASB* oder *Azure Storage - Blob* bezeichnet. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure BLOB-Speicher mit HDInsight][hdinsight-storage].

Bei der Bereitstellung eines HDInsight-Clusters geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blob-Speichercontainer aus diesem Konto wird als Standarddateisystem festgelegt, genau wie in HDFS. Der HDInsight-Cluster wird standardmäßig in demselben Rechenzentrum bereitgestellt wie das angegebene Speicherkonto.

Zusätzlich zu diesem Speicherkonto können Sie weitere Speicherkonten hinzufügen, wenn Sie einen HDInsight-Cluster benutzerdefiniert konfigurieren. Diese zusätzlichen Speicherkonten können entweder aus demselben Azure-Abonnement oder anderen Azure-Abonnements stammen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision]. 

Zur Vereinfachung des Lernprogramms werden nur der standardmäßige Blob-Container und das Standardspeicherkonto verwendet. In der Praxis werden die Datendateien in der Regel in einem festgelegten Speicherkonto gespeichert.

**So erstellen Sie ein Azure-Speicherkonto**

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATENDIENSTE**, auf **SPEICHER**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. Geben Sie Daten in **URL**, **SPEICHERORT** und **REPLIKATION** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. Affinitätsgruppen werden nicht unterstützt. Sie werden das neue Speicherkonto in der Speicherliste sehen.

	>[AZURE.NOTE]  Die Schnellerfassungsoption zur Bereitstellung eines HDInsight-Clusters wie die in diesem Lernprogramm verwendete fordert beim Bereitstellen des Clusters nicht zur Angabe eines Standorts auf. Stattdessen wird der Cluster standardmäßig in demselben Rechenzentrum wie das Speicherkonto bereitgestellt. Achten Sie daher darauf, das Speicherkonto an den Standorten zu erstellen, die für den Cluster unterstützt werden; dies sind:  **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Mitte/Norden)**, **USA (Mitte/Süden)**.

4. Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.
5. Wählen Sie das neue Speicherkonto in der Liste aus, und klicken Sie unten auf der Seite auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
7. Notieren Sie sich die Werte unter **SPEICHERKONTONAME** und **PRIMÄRER ZUGRIFFSSCHLÜSSEL** (oder **SEKUNDÄRER ZUGRIFFSSCHLÜSSEL**. Beide können verwendet werden.)  Sie werden diese später im Lernprogramm benötigen.


Weitere Informationen finden Sie unter:
[Erstellen eines Speicherkontos][azure-create-storageaccount] und [Verwenden von Azure BLOB-Speicher mit HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Bereitstellen eines HDInsight-Clusters

Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. In diesen Abschnitt stellen Sie einen HDInsight-Cluster der Version 3.1 bereit, der auf Hadoop Version 2.4 basiert. Mit dem Azure-Portal, HDInsight PowerShell-Cmdlets oder dem HDInsight .NET SDK können Sie auch Hadoop-Cluster für andere Versionen erstellen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision]. Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie auf der Seite zu den [HDInsight-Komponentenversionen](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**So stellen Sie ein HDInsight-Cluster bereit** 

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an. 

2. Klicken Sie links auf **HDInsight**, um den Status der Cluster in Ihrem Konto anzuzeigen. Im folgenden Screenshot sind keine HDInsight-Cluster vorhanden.

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. Klicken Sie links unten auf **NEU** und dann auf **Datendienste**, **HDInsight** und **Hadoop**.

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. Geben Sie folgende Werte ein, bzw. wählen Sie diese aus:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Clustername</td><td>Der Name des Clusters</td></tr>
	<tr><td>Clustergröße</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Der Standardwert ist 4. Die Option zur Verwendung von einem oder zwei Datenknoten ist jedoch ebenfalls im Dropdownmenü verfügbar. Mit der Option <strong>Benutzerdefiniert erstellen</strong> können Sie eine beliebige Anzahl von Clusterknoten erstellen. Es sind Preisdetails zur Abrechnung für verschiedene Cluster-Größen verfügbar. Klicken Sie auf das Symbol <strong>?</strong> direkt über dem Dropdownfeld, und folgen Sie dem Link im Popupfenster.</td></tr>
	<tr><td>Kennwort</td><td>Das Kennwort für das Konto <i>admin</i> . Der Cluster-Benutzername "admin" wird angegeben, wenn Sie die Option <strong>Benutzerdefiniert erstellen</strong> nicht verwenden. Es handelt sich hierbei NICHT um das Windows-Administratorkonto für die virtuellen Computer, auf denen die Cluster bereitgestellt werden Der Kontoname kann mit dem Assistenten <strong>Benutzerdefiniert erstellen</strong> geändert werden.</td></tr>
	<tr><td>Speicherkonto</td><td>Wählen Sie das erstellte Speicherkonto im Dropdownfeld aus. <br/>

	Sobald ein Speicherkonto ausgewählt wurde, kann es nicht mehr geändert werden. Wird das Speicherkonto entfernt, kann der Cluster nicht mehr verwendet werden.

	Der HDInsight-Cluster befindet sich in demselben Rechenzentrum wie das Speicherkonto. 
	</td></tr>
	</table>

	Notieren Sie sich den Clusternamen.  Sie werden ihn später im Lernprogramm benötigen.
	
5. Klicken Sie auf **HDInsight-Cluster erstellen**. Wenn die Bereitstellung abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

	>[AZURE.NOTE] Mit dem vorstehenden Verfahren wird ein Cluster mit HDInsight-Clusterversion 3.1 erstellt. Zur Erstellung anderer Clusterversionen verwenden Sie die Methode "Benutzerdefiniert erstellen" im Verwaltungsportal oder aber Azure PowerShell. Informationen zu den Unterschieden zwischen den einzelnen Clusterversionen finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions]. Informationen zur Verwendung der Option **BENUTZERDEFINIERT ERSTELLEN** finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision].


##<a name="sample"></a>Ausführen von Beispielen im Portal

Ein erfolgreich bereitgestelltes HDInsight-Cluster enthält eine Abfragekonsole, in der Sie die Beispiele direkt im Portal ausführen können. Mit diesen Beispielen können Sie das Arbeiten mit HDInsight erlernen, indem Sie einige einfache Szenarien durcharbeiten. Die Beispiele enthalten alle benötigten Komponenten wie z. B. die zu analysierenden Daten und die entsprechenden Abfragen für die Daten. 

**Um das Beispiel auszuführen**, klicken Sie im Azure-Verwaltungsportal auf den Namen des entsprechenden Clusters, und klicken Sie anschließend unten in der Seite auf **Abfragekonsole**. Klicken Sie in der Webseite, die daraufhin geöffnet wird, auf die Registerkarte **Erste Schritte** und anschließend in der Kategorie **Beispiele** auf das Beispiel, das Sie ausführen möchten. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen. Klicken Sie auf die folgenden Links, um mehr über die einzelnen Beispiele zu erfahren.

Beispiel | Behandelte Themen
------ | ---------------
[Sensordatenanalyse][hdinsight-sensor-data-sample] | Erfahren Sie, wie Sie Verlaufsdaten von Heizungs-, Lüftungs- und Klimaanlagen (HLK) mithilfe von HDInsight verarbeiten können, um herauszufinden, welche Anlagen nicht in der Lage sind, eine eingestellte Temperatur zu halten
[Analyse von Websiteprotokollen][hdinsight-weblogs-sample] | Erfahren Sie, wie Sie Website-Protokolldateien mithilfe von HDInsight analysieren können, um Einblicke in die Häufigkeit von Besuchen pro Tag von externen Webseiten sowie eine Zusammenfassung von Websitefehlern zu erhalten, die den Benutzern angezeigt werden


##<a name="hivequery"></a>Ausführen einer Hive-Abfrage im Portal
Nachdem Sie einen HDInsight-Cluster bereitgestellt haben, führen Sie im nächsten Schritt einen Hive-Auftrag aus, um die Hive-Beispieltabelle  *hivesampletable* abzufragen, die in HDInsight-Clustern enthalten ist. Die Tabelle enthält Daten zu Mobilgeräteherstellern, -plattformen und -modellen. Wir fragen diese Tabelle ab, um Daten für Mobilgeräte eines bestimmten Herstellers abzurufen.

> [AZURE.NOTE] Die HDInsight-Tools für Visual Studio sind im Azure SDK für .NET, Version 2.5 oder höher, enthalten.  Mithilfe der Tools in Visual Studio können Sie eine Verbindung mit dem HDInsight-Cluster herstellen, Hive-Tabellen erstellen und Hive-Abfragen ausführen.  Weitere Informationen finden Sie unter [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio][1].

**So führen Sie einen Hive-Auftrag über das Cluster-Dashboard aus**

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an. 
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste der erstellten Cluster, inklusive des Clusters, den Sie im vorigen Abschnitt erstellt haben.
3. Klicken Sie auf den Namen des Clusters, in dem Sie den Hive-Auftrag ausführen möchten, und klicken Sie anschließend unten in der Seite auf **ABFRAGEKONSOLE**. 
4. Daraufhin wird eine Webseite in einer neuen Browser-Registerkarte geöffnet. Geben Sie das Hadoop-Benutzerkonto und das zugehörige Kennwort ein.  Der Standardbenutzername ist **admin**, und das Kennwort haben Sie bei der Bereitstellung des Clusters angegeben.  Das Dashboard sieht folgendermaßen aus:

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	Am oberen Rand befinden sich mehrere Registerkarten.  Die Standardregisterkarte ist **Hive-Editor**, die anderen Registerkarten heißen **Auftragsverlauf** und **Dateibrowser**.  Mithilfe des Dashboards können Sie Hive-Abfragen senden, Protokolle von Hadoop-Jobs überprüfen und WASB-Dateien durchsuchen.

	> [AZURE.NOTE] Beachten Sie, dass die URL der Webseite *&lt;Clustername&gt;.azurehdinsight.net* lautet. Anstatt über das Verwaltungsportal können Sie das Dashboard also auch unter Angabe der URL über einen Webbrowser öffnen.

6. Geben Sie auf der Registerkarte **Hive-Editor** in das Feld **Abfragename** den Text **HTC20** ein.  Der Abfragename ist gleichzeitig auch der Auftragstitel.

7. Geben Sie im Abfragefenster die folgende Abfrage ein: 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. Klicken Sie auf **Senden**. Die Ausgabe der Ergebnisse dauert einen Moment. Der Bildschirm wird alle 30 Sekunden aktualisiert. Sie können auch auf **Aktualisieren** klicken, um den Bildschirm zu aktualisieren.

    Nach Abschluss des Vorgangs sieht der Bildschirm folgendermaßen aus:

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. Klicken Sie auf den Abfragenamen, um die Ausgabe anzuzeigen. Notieren Sie sich die **Auftragsstartzeit (UTC)**. Sie benötigen sie später. 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    Die Seite zeigt auch die **Auftragsausgabe** und das **Auftragsprotokoll** an. Außerdem haben Sie die Möglichkeit, die Ausgabedatei (\_stdout) und die Protokolldatei (\_stderr) herunterzuladen.


	> [AZURE.NOTE] In der Tabelle **Auftragssitzung** auf der Registerkarte **Hive-Editor** werden abgeschlossene oder laufende Jobs angezeigt, solange Sie auf dieser Registerkarte bleiben. Wenn Sie die Seite verlassen, werden in der Tabelle keine Jobs aufgeführt. Die Registerkarte **Auftragsverlauf** enthält eine Liste aller abgeschlossenen und laufenden Aufträge.
 

**So navigieren Sie zur Ausgabedatei**

1. Klicken Sie oben im Cluster-Dashboard auf **Dateibrowser**. 
2. Klicken Sie auf den Namen Ihres Speicherkontos, auf den Namen des Containers (identisch mit dem Clusternamen) und dann auf **user**.
3. Klicken Sie auf **admin** und dann auf die GUID, deren letzte Änderungszeit kurz nach der zuvor notierten Auftragsstartzeit liegt. Notieren Sie sich die GUID. Sie benötigen sie im nächsten Abschnitt.


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Verbinden mit Microsoft Business Intelligence-Tools 

Mit dem Power Query-Add-in für Microsoft Excel können Sie die Job-Ausgabe von HDInsight in Excel importieren. Dort können Sie die Ergebnisse mithilfe der Microsoft Business Intelligence (BI)-Tools weiter analysieren. 

Sie benötigen Excel 2010 oder 2013, um diesen Teil des Lernprogramms durchführen zu können. 

**So laden Sie Microsoft Power Query für Excel herunter**

- Laden Sie Microsoft Power Query für Microsoft Excel aus dem [Microsoft Download Center](http://www.microsoft.com/de-de/download/details.aspx?id=39379) herunter, und installieren Sie es.

**So importieren Sie HDInsight-Daten**

1. Öffnen Sie Excel, und erstellen Sie eine neue leere Arbeitsmappe.
3. Klicken Sie auf das Menü **Power Query**, auf **Aus anderen Quellen** und anschließend auf **Von Azure HDInsight**.

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Geben Sie den **Kontonamen** des mit Ihrem Cluster verbundenen Azure BLOB-Speicherkontos ein, und klicken Sie dann auf **OK**. Dies ist das Speicherkonto, das Sie zu Beginn des Lernprogramms erstellt haben.
4. Geben Sie den **Kontoschlüssel** für das Azure BLOB-Speicherkonto ein, und klicken Sie dann auf **Speichern**. 
5. Doppelklicken Sie im Navigationsbereich rechts auf den Namen des Blob-Speichercontainers. Der Container hat standardmäßig denselben Name wie der Cluster. 

6. Suchen Sie **stdout** in der Spalte **Name**. Stellen Sie sicher, dass die GUID in der entsprechenden Spalte **Ordnerpfad** Spalte mit der zuvor notierten GUID übereinstimmt. Eine Übereinstimmung deutet darauf hin, dass die Ausgabedaten zu dem von Ihnen übermittelten Job gehören. Klicken Sie links von **stdout** auf **Binär**.

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. Klicken Sie in der oberen linken Ecke auf **Schließen und laden**, um die Hive-Auftragsausgabe in Excel zu importieren.


##<a name="nextsteps"></a>Nächste Schritte
In diesem Lernprogramm haben Sie erfahren, wie ein Cluster mit HDInsight bereitgestellt wird, ein MapReduce-Job darauf ausgeführt wird und die Ergebnisse in Excel importiert werden, wo sie mit BI-Tools weiter bearbeitet und grafisch angezeigt werden können. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio][1]
- [Erste Schritte mit dem HDInsight Emulator][hdinsight-emulator]
- [Verwenden von Azure BLOB-Speicher mit HDInsight][hdinsight-storage]
- [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
- [Hochladen von Daten in HDInsight][hdinsight-upload-data]
- [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce]
- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
- [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]
- [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][hdinsight-develop-streaming]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=42-->
