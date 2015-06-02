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
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/16/2015"
   ms.author="nitinme"/>


# Erste Schritte bei der Verwendung von Hadoop mit Hive in HDInsight unter Windows

> [AZURE.SELECTOR]
- [Windows](hdinsight-get-started.md)
- [Linux](hdinsight-hadoop-linux-get-started.md)

Für Ihren Schnelleinstieg in die Nutzung von HDInsight veranschaulicht dieses Lernprogramm die Ausführung einer Hive-Abfrage zum Extrahieren aussagekräftiger Informationen aus unstrukturierten Daten in einem Hadoop-Cluster. Anschließend analysieren Sie die Ergebnisse in Microsoft Excel.


> [AZURE.NOTE]Wenn Sie noch keine Erfahrung mit Hadoop und Big Data haben, erfahren Sie mehr zu den Begriffen [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs] und [Hive][apache-hive]. Wie HDInsight Hadoop in Azure bereitstellt, erfahren Sie unter [Einführung in Hadoop in HDInsight][hadoop-hdinsight-intro].

Im Rahmen der generellen Verfügbarkeit von Azure HDInsight hat Microsoft zudem den HDInsight-Emulator für Azure veröffentlicht, der zuvor als *Microsoft HDInsight Developer Preview* bekannt war. Dieser Emulator wurde für Entwicklungsszenarien erstellt und unterstützt nur Umgebungen mit einem Knoten. Informationen zur Verwendung von des HDInsight-Emulators finden Sie unter [Erste Schritte mit dem HDInsight-Emulator][hdinsight-emulator].

> [AZURE.NOTE]Informationen zur Bereitstellung eines HBase-Clusters finden Sie unter [Bereitstellen eines HBase-Clusters in HDInsight][hdinsight-hbase-custom-provision]. Unter <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Worin besteht der Unterschied zwischen Hadoop und HBase?</a> finden Sie einen Vergleich der beiden Technologien.

## Welchen Zweck verfolgt dieses Lernprogramm? ##

Angenommen, Sie verfügen über einen großen unstrukturierten Datensatz und möchten Abfragen für diesen ausführen, um aussagekräftige Informationen zu extrahieren. Genau darum geht es in diesem Lernprogramm. Und so geht es:

   ![Veranschaulichung der Schritte im Lernprogramm: "Erste Schritte bei der Verwendung von Hadoop mit Hive in HDInsight": Erstellen eines Kontos, Bereitstellen eines Clusters, Abfragen von Daten und Analysieren in Excel.][image-hdi-getstarted-flow]

Sie können sich auch [ein Demovideo dieses Lernprogramms ansehen](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS).

![!HDI.getstarted.video][img-hdi-getstarted-video]


**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:


- Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
- Einen Computer mit Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

**Geschätzte Zeit zum Bearbeiten dieses Lernprogramms:** 30 Minuten

##Dieses Lernprogramm umfasst folgende Punkte

* [Erstellen eines Azure-Speicherkontos](#storage)
* [Bereitstellen eines HDInsight-Clusters](#provision)
* [Ausführen von Beispielen im Portal](#sample)
* [Ausführen eines Hive-Auftrags](#hivequery)
* [Nächste Schritte](#nextsteps)

##<a name="storage"></a>Erstellen eines Azure-Speicherkontos

HDInsight verwendet Azure-Blobspeicher zum Speichern von Daten. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].

Bei der Bereitstellung eines HDInsight-Clusters geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blobcontainer aus diesem Konto wird genau wie in HDFS (Hadoop Distribute File System) als Standarddateisystem festgelegt. Der HDInsight-Cluster wird standardmäßig im selben Datencenter bereitgestellt wie das angegebene Speicherkonto.

>[AZURE.NOTE]Geben Sie einen Blobspeichercontainer nicht für mehrere HDInsight-Cluster frei.

Zusätzlich zu diesem Speicherkonto können Sie weitere Speicherkonten hinzufügen, wenn Sie einen HDInsight-Cluster benutzerdefiniert konfigurieren. Diese zusätzlichen Speicherkonten können entweder aus demselben Azure-Abonnement oder anderen Azure-Abonnements stammen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision].

Zur Vereinfachung dieses Lernprogramms werden nur das Standardblob und Standardspeicherkonto verwendet. In der Praxis werden die Datendateien in der Regel in einem festgelegten Speicherkonto gespeichert.

**So erstellen Sie ein Azure-Speicherkonto**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **SPEICHER**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

	![Azure-Portal, in dem Sie die Schnellerfassungsfunktion nutzen können, um ein neues Speicherkonto einzurichten.][image-hdi-storageaccount-quickcreate]

3. Geben Sie **URL**, **SPEICHERORT** und **REPLIKATION** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. (Affinitätsgruppen werden nicht unterstützt.) Sie werden das neue Speicherkonto in der Speicherliste sehen.

	>[AZURE.NOTE]Die Option SCHNELLERFASSUNG zur Bereitstellung eines HDInsight-Clusters (wie der in diesem Lernprogramm verwendete) fordert beim Bereitstellen des Clusters nicht zur Angabe eines Standorts auf. Stattdessen wird der Cluster standardmäßig im selben Datencenter wie das Speicherkonto bereitgestellt. Achten Sie daher darauf, das Speicherkonto an einem Standort zu erstellen, der für den Cluster unterstützt wird. Dies sind: **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Mitte/Norden)**, **USA (Süden-Mitte)**.

4. Warten Sie, bis sich der **STATUS** des neuen Speicherkontos auf **Online** geändert hat.
5. Wählen Sie das neue Speicherkonto in der Liste aus, und klicken Sie unten auf der Seite auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
7. Notieren Sie sich den **NAMEN DES SPEICHERKONTOS** und den **PRIMÄREN ZUGRIFFSSCHLÜSSEL** (oder den **SEKUNDÄREN ZUGRIFFSSCHLÜSSEL** – beide Schlüssel funktionieren). Sie werden diese später im Lernprogramm benötigen.


Weitere Informationen finden Sie unter [￼Erstellen eines Speicherkontos][azure-create-storageaccount] und [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Bereitstellen eines HDInsight-Clusters

Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. In diesen Abschnitt stellen Sie einen HDInsight-Cluster der Version 3.1 bereit, der auf der Hadoop-Version 2.4 basiert. Mit dem Azure-Portal, HDInsight PowerShell-Cmdlets oder dem HDInsight .NET SDK können Sie auch Hadoop-Cluster für andere Versionen erstellen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision]. Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**So stellen Sie einen HDInsight-Cluster bereit**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an. 

2. Klicken Sie links auf **HDInsight**, um den Status der Cluster in Ihrem Konto anzuzeigen. Im folgenden Screenshot sind keine HDInsight-Cluster vorhanden.

	![Status von HDInsight-Clustern im Azure-Portal.][image-hdi-clusterstatus]

3. Klicken Sie links unten auf **NEU** und dann nacheinander auf **Data Services**, **HDInsight** und **Hadoop**.

	![Erstellung eines Hadoop-Clusters in HDInsight.][image-hdi-quickcreatecluster]

4. Geben Sie folgende Werte ein bzw. wählen diese aus:

	<table border="1">
<tr><th>Name</th><th>Wert</th></tr>
<tr><td>Clustername</td><td>Der Name des Clusters.</td></tr>
<tr><td>Clustergröße</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Der Standardwert ist 4. Die Option zur Verwendung von 1 oder 2 Datenknoten ist jedoch ebenfalls im Dropdownmenü verfügbar. Mit der Option <strong>Benutzerdefinierte Erstellung</strong> können Sie eine beliebige Anzahl von Clusterknoten erstellen. Preisdetails zu den Abrechnungstarifen verschiedener Clustergrößen sind verfügbar. Klicken Sie auf das Symbol <strong>?</strong> über der Dropdownliste, und folgen Sie dem Link, der angezeigt wird.</td></tr>
<tr><td>Kennwort</td><td>Das Kennwort für das Konto <i>Admin</i>. Der Cluster-Benutzername "admin" wird angegeben, wenn Sie die Option <strong>Benutzerdefinierte Erstellung</strong> nicht verwenden. Es handelt sich hierbei NICHT um das Windows-Administratorkonto für die virtuellen Computer, auf denen die Cluster bereitgestellt werden Der Kontoname kann mit dem Assistenten <strong>Benutzerdefiniert erstellen</strong> geändert werden.</td></tr>
<tr><td>Speicherkonto</td><td>Klicken Sie auf die Dropdownliste, und wählen Sie das Speicherkonto aus, das Sie erstellt haben. <br/>

Sobald ein Speicherkonto ausgewählt wurde, kann es nicht mehr geändert werden. Wird das Speicherkonto entfernt, kann der Cluster nicht mehr verwendet werden. Der HDInsight-Cluster befindet sich im selben Datencenter wie das Speicherkonto. 
</td></tr>
</table>Notieren Sie sich den Clusternamen. Sie werden ihn später im Lernprogramm benötigen.

	
5. Klicken Sie auf **HDInsight-Cluster erstellen**. Wenn die Bereitstellung abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

	>[AZURE.NOTE]Über das vorherige Verfahren wird einen Cluster mit HDInsight Version 3.1 erstellt. Zur Erstellung anderer Clusterversionen verwenden Sie die Methode **Benutzerdefiniert erstellen** im Portal oder Azure PowerShell. Informationen zu den Unterschieden zwischen den einzelnen Clusterversionen finden Sie unter [Neuerungen in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions]. Informationen zur Verwendung der Option **BENUTZERDEFINIERT ERSTELLEN** finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision].


##<a name="sample"></a>Ausführen von Beispielen im Portal

Ein erfolgreich bereitgestellter HDInsight-Cluster bietet eine Abfragekonsole mit einem Katalog für den Einstieg, um Beispiele direkt im Portal ausführen zu können. Mit diesen Beispielen können Sie das Arbeiten mit HDInsight erlernen, indem Sie einige einfache Szenarien durcharbeiten. Die Beispiele enthalten alle benötigten Komponenten wie z. B. die zu analysierenden Daten und die entsprechenden Abfragen für die Daten. Um weitere Informationen zu den Beispielen im Katalog für den Einstieg zu erhalten, lesen Sie [Erlernen von Hadoop in HDInsight mithilfe des HDInsight-Katalogs für den Einstieg](hdinsight-learn-hadoop-use-sample-gallery.md).

**Um das Beispiel auszuführen**, klicken Sie im Azure-Portal auf den Namen des Clusters, in dem Sie das Beispiel ausführen möchten, und anschließend unten in der Seite auf **Abfragekonsole**. Klicken Sie auf der Webseite, die sich daraufhin öffnet, auf die Registerkarte **Katalog für den Einstieg** und anschließend in der Kategorie **Beispiele** auf das Beispiel, das Sie ausführen möchten. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen. In der folgende Tabelle sind einige Beispiele aufgeführt und weitere Informationen zur Funktion jedes Beispiels enthalten.

Beispiel | Behandelte Themen
------ | ---------------
[Analysieren von Sensordaten][hdinsight-sensor-data-sample] | Erfahren Sie, wie Sie Verlaufsdaten von Klimaanlagen mithilfe von HDInsight verarbeiten können, um herauszufinden, welche Anlagen nicht in der Lage sind, eine eingestellte Temperatur zu halten.
[Analysieren von Websiteprotokollen][hdinsight-weblogs-sample] | Erfahren Sie, wie Sie Websiteprotokolle mithilfe von HDInsight analysieren können, um Einblicke in die Häufigkeit von Besuchen pro Tag von externen Webseiten sowie eine Zusammenfassung von Websitefehlern zu erhalten, die den Benutzern angezeigt werden.
[Twitter-Trendanalyse](hdinsight-analyze-twitter-data.md) | Erfahren Sie, wie Sie HDInsight verwenden, um Trends in Twitter zu analysieren.



##<a name="hivequery"></a>Ausführen einer Hive-Abfrage im Portal
Nun, da Sie einen HDInsight-Cluster bereitgestellt haben, ist der nächste Schritt die Ausführung eines Hive-Auftrags zum Abfragen einer Hive-Beispieltabelle. Wir verwenden die Tabelle *hivesampletable*, die zu HDInsight-Clustern gehört. Die Tabelle enthält Daten zu Mobilgeräteherstellern, -plattformen und -modellen. Wir fragen diese Tabelle ab, um Daten für Mobilgeräte eines bestimmten Herstellers abzurufen.

> [AZURE.NOTE]HDInsight Tools für Visual Studio ist Bestandteil des Azure SDK für .NET Version 2.5 oder höher. Mithilfe der Tools von Visual Studio können Sie Verbindungen zu HDInsight-Clustern herstellen, Hive-Tabellen erstellen und Hive-Abfragen ausführen. Weitere Informationen finden Sie unter [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio][1].



**So führen Sie einen Hive-Job über das Clusterdashboard aus**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an. 
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste der Cluster, einschließlich des Clusters, den Sie gerade im vorherigen Abschnitt erstellt haben.
3. Klicken Sie auf den Namen des Clusters, in dem Sie den Hive-Auftrag ausführen möchten, und klicken Sie anschließend unten auf der Seite auf **ABFRAGEKONSOLE**. 
4. Eine Webseite wird in einer anderen Browserregisterkarte geöffnet. Geben Sie das Hadoop-Benutzerkonto und das zugehörige Kennwort ein. Der Standardbenutzername ist **admin**, und das Kennwort haben Sie bei der Bereitstellung des Clusters angegeben. Das Dashboard sieht folgendermaßen aus:

	![Registerkarte "Hive-Editor" im HDInsight-Clusterdashboard.][img-hdi-dashboard]

	Am oberen Rand der Seite befinden sich mehrere Registerkarten. Die Standardregisterkarte ist **Hive-Editor**, die anderen Registerkarten heißen **Auftragsverlauf** und **Dateibrowser**. Mithilfe des Dashboards können Sie Hive-Abfragen senden, Protokolle von Hadoop-Aufträgen überprüfen und Dateien im Speicher durchsuchen.

	> [AZURE.NOTE]Beachten Sie, dass die URL der Webseite *&lt;ClusterName&gt;.azurehdinsight.net* lautet. Anstatt über das Portal können Sie das Dashboard also auch unter Angabe der URL in einem Webbrowser öffnen.

6. Geben Sie auf der Registerkarte **Hive-Editor** in das Feld **Abfragename** den Text **HTC20** ein. Der Abfragename ist gleichzeitig auch der Job-Titel.

7. Geben Sie im Abfragefenster die folgende Abfrage ein:

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![In den Abfragebereich des Hive-Editors eingegebene Abfrage.][img-hdi-dashboard-query-select]

4. Klicken Sie auf **Senden**. Die Ausgabe der Ergebnisse dauert einen Moment. Der Bildschirm wird alle 30 Sekunden aktualisiert. Sie können auch auf **Aktualisieren** klicken.

    Nach Abschluss des Auftrags sieht der Bildschirm folgendermaßen aus:

	![Ergebnisse einer Hive-Abfrage werden am unteren Rand des Clusterdashboards aufgeführt.][img-hdi-dashboard-query-select-result]

5. Klicken Sie auf den Abfragenamen, um die Ausgabe anzuzeigen. Notieren Sie sich die **Job-Startzeit (UTC)**. Sie benötigen sie später.

    ![Auf der Registerkarte "Auftragsverlauf" des HDInsight-Clusterdashboards aufgeführte Auftragsstartzeit.][img-hdi-dashboard-query-select-result-output]

    Diese Seite enthält außerdem die **Job-Ausgabe** und das **Job-Protokoll**. Außerdem haben Sie die Möglichkeit, die Ausgabedatei (_stdout) und die Protokolldatei (_stderr) herunterzuladen.


	> [AZURE.NOTE]In der Tabelle **Auftragssitzung** auf der Registerkarte **Hive-Editor** werden abgeschlossene oder laufende Aufträge angezeigt, solange Sie auf dieser Registerkarte bleiben. Wenn Sie die Seite verlassen, werden in der Tabelle keine Jobs aufgeführt. Die Registerkarte **Job-Verlauf** enthält eine Liste aller abgeschlossenen und laufenden Jobs.
 

**So navigieren Sie zur Ausgabedatei**

1. Klicken Sie oben im Clusterdashboard auf **Dateibrowser**. 
2. Klicken Sie auf den Namen Ihres Speicherkontos, auf den Namen des Containers (identisch mit dem Clusternamen) und dann auf **Benutzer**.
3. Klicken Sie auf **admin** und dann auf die GUID, deren Uhrzeit der letzten Änderung kurz nach der zuvor notierten Startzeit des Auftrags liegt. Kopieren Sie diese GUID. Sie benötigen sie im nächsten Abschnitt.


   	![Auf der Registerkarte "Datei-Browser" aufgeführte Ausgabedatei-GUID.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Verbinden mit Microsoft Business Intelligence-Tools 

Mit dem Power Query-Add-in für Microsoft Excel können Sie die Auftragsausgabe von HDInsight in Excel importieren. Dort können Sie die Ergebnisse mithilfe der Microsoft Business Intelligence (BI)-Tools weiter analysieren.

Sie benötigen Excel 2013 oder 2010, um diesen Teil des Lernprogramms durchführen zu können.

**So laden Sie Microsoft Power Query für Excel herunter**

- Laden Sie Microsoft Power Query für Microsoft Excel aus dem [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=39379) herunter, und installieren Sie es.

**So importieren Sie HDInsight-Daten**

1. Öffnen Sie Excel, und erstellen Sie eine neue Arbeitsmappe.
3. Klicken Sie auf das Menü **Power Query**, klicken Sie auf **Von anderen Quellen**, und klicken Sie dann auf **Von Azure HDInsight**.

	![Für Azure HDInsight geöffnetes Menü "Excel PowerQuery importieren".][image-hdi-gettingstarted-powerquery-importdata]

3. Geben Sie den **Kontonamen** des mit Ihrem Cluster verbundenen Azure-Blobspeicherkontos ein, und klicken Sie dann auf **OK**. (Dies ist das Speicherkonto, das Sie zu Beginn des Lernprogramms erstellt haben.)
4. Geben Sie den **Kontoschlüssel** für das Azure-Blobspeicherkonto ein, und klicken Sie dann auf **Speichern**. 
5. Doppelklicken Sie im rechten Bereich auf den Blobnamen. Standardmäßig entspricht der Blobname dem Namen des Clusters. 

6. Suchen Sie **stdou** in der Spalte **Name**. Prüfen Sie, ob die GUID in der entsprechenden Spalte **Ordnerpfad** mit der GUID übereinstimmt, die Sie zuvor kopiert haben. Eine Übereinstimmung deutet darauf hin, dass die Ausgabedaten zu dem von Ihnen übermittelten Job gehören. Klicken Sie in der linken Spalte von **stdout** auf **Binary**.

	![Suchen der Datenausgabe nach GUID in der Liste der Inhalte.][image-hdi-gettingstarted-powerquery-importdata2]

9. Klicken Sie in der oberen linken Ecke auf **Schließen und laden**, um die Ausgabe des Hive-Auftrags in Excel zu importieren.


##<a name="nextsteps"></a>Nächste Schritte
In diesem Lernprogramm haben Sie erfahren, wie ein Cluster mit HDInsight bereitgestellt wird, ein MapReduce-Auftrag darauf angewendet wird und die Ergebnisse in Excel importiert werden, wo sie mit BI-Tools weiter bearbeitet und grafisch angezeigt werden können. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio][1]
- [Erste Schritte mit dem HDInsight-Emulator][hdinsight-emulator]
- [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage]
- [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
- [Hochladen von Daten in HDInsight][hdinsight-upload-data]
- [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce]
- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
- [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]
- [Entwickeln von C#-Hadoop-Streamingprogrammen für HDInsight][hdinsight-develop-streaming]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]


[1]: hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md

[hdinsight-get-started-30]: hdinsight-get-started-30.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: install-configure-powershell.md
[powershell-open]: install-configure-powershell.md#Install


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

<!--HONumber=54-->