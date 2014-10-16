<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="nitinme" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="nitinme"></tags>

# Erste Schritte mit Hadoop 2.4 in HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Erste Schritte mit Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Erste Schritte mit Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a-->
</div>

HDInsight stellt Apache Hadoop, ein MapReduce-Software-Framework, in einer einfacheren, skalierbareren und kosteneffizienten Azure-Umgebung zur Verfügung. HDInsight bietet zudem einen kostengünstigen Ansatz für die Verwaltung und Speicherung von Daten mit dem Azure Blob-Speicher.

> [WACOM.NOTE] Wenn Sie noch keine Erfahrung mit Hadoop und Big Data haben, machen Sie sich mit den Begriffen [Apache Hadoop][], [MapReduce][], [HDFS][] und [Hive][] vertraut. Wie HDInsight Hadoop in Azure bereitstellt, erfahren Sie unter [Einführung in Hadoop in HDInsight][].

Im Rahmen der generellen Verfügbarkeit von Azure HDInsight hat Microsoft zudem den HDInsight-Emulator für Azure veröffentlicht, der zuvor als *Microsoft HDInsight Developer Preview* bekannt war. Dieser Emulator wurde für Entwicklungsszenarien erstellt und unterstützt nur Umgebungen mit einem Knoten. Informationen zur Verwendung des HDInsight-Emulators finden Sie unter [Erste Schritte mit dem HDInsight-Emulator][].

> [WACOM.NOTE] Anweisungen zur Bereitstellung eines HBase-Clusters finden Sie unter [Bereitstellen von HBase-Clustern in HDInsight][]. Unter [Worin besteht der Unterschied zwischen Hadoop und HBase?][] (What's the difference between Hadoop and HBase?, in englischer Sprache) finden Sie einen Vergleich der beiden Technologien.

## Welchen Zweck verfolgt dieses Lernprogramm?

Angenommen, Sie verfügen über einen großen unstrukturierten Datensatz und möchten Abfragen für diesen ausführen, um aussagekräftige Informationen zu extrahieren. Genau darum geht es in diesem Lernprogramm. Und so geht es:

![HDI.GetStartedFlow][]

Sie können sich auch ein Demovideo dieses Lernprogramms ansehen:

<center>
<iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen>
</iframe>
</center>
<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][], [Spezielle Angebote][] oder [Kostenlose Testversion][].
-   Einen Computer mit Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

**Geschätzter Zeitaufwand:** 30 Minuten

## Dieses Lernprogramm umfasst folgende Punkte

-   [Erstellen eines Azure-Speicherkontos][]
-   [Bereitstellen eines HDInsight-Clusters][]
-   [Ausführen eines Hive-Jobs][]
-   [Verbinden mit Microsoft Business Intelligence-Tools][]
-   [Nächste Schritte][]

## <a name="storage"></a>Erstellen eines Azure-Speicherkontos

HDInsight verwendet Azure Blob-Speicher zum Speichern von Daten. Der Speicher wird *WASB* oder *Azure Storage-Blob* genannt. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][].

Bei der Bereitstellung eines HDInsight-Clusters geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blob-Speichercontainer aus diesem Konto wird als Standarddateisystem festgelegt, genau wie in HDFS. Der HDInsight-Cluster wird standardmäßig in demselben Rechenzentrum bereitgestellt wie das angegebene Speicherkonto.

Zusätzlich zu diesem Speicherkonto können Sie weitere Speicherkonten hinzufügen, wenn Sie einen HDInsight-Cluster benutzerdefiniert konfigurieren. Diese zusätzlichen Speicherkonten können entweder aus demselben Azure-Abonnement oder anderen Azure-Abonnements stammen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][].

Zur Vereinfachung des Lernprogramms werden nur der standardmäßige Blob-Container und das Standardspeicherkonto verwendet. In der Praxis werden die Datendateien in der Regel in einem festgelegten Speicherkonto gespeichert.

**So erstellen Sie ein Azure-Speicherkonto**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.
2.  Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **SPEICHERN**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

    ![HDI.StorageAccount.QuickCreate][]

3.  Geben Sie **URL**, **SPEICHERORT** und **REPLIZIERUNG** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. Affinitätsgruppen werden nicht unterstützt. Sie werden das neue Speicherkonto in der Speicherliste sehen.

    > [WACOM.NOTE] Die Schnellerfassungsoption zur Bereitstellung eines HDInsight-Clusters wie die in diesem Lernprogramm verwendete fordert beim Bereitstellen des Clusters nicht zur Angabe eines Standorts auf. Stattdessen wird der Cluster standardmäßig in demselben Rechenzentrum wie das Speicherkonto bereitgestellt. Achten Sie daher darauf, das Speicherkonto an den Standorten zu erstellen, die für den Cluster unterstützt werden; dies sind: **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Mitte/Norden)**, **USA (Mitte/Süden)**.

4.  Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.
5.  Wählen Sie das neue Speicherkonto in der Liste aus, und klicken Sie unten auf der Seite auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
6.  Notieren Sie sich den **NAMEN DES SPEICHERKONTOS** und den **PRIMÄREN ZUGRIFFSSCHLÜSSEL** (oder den **SEKUNDÄREN ZUGRIFFSSCHLÜSSEL**. Beide können verwendet werden.) Sie werden diese später im Lernprogramm benötigen.

Weitere Informationen finden Sie unter
[Erstellen eines Speicherkontos][] und [Verwenden von Azure Blob-Speicher mit HDInsight][].

## <a name="provision"></a>Bereitstellen eines HDInsight-Clusters

Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. In diesen Abschnitt stellen Sie einen HDInsight-Cluster der Version 3.1 bereit, der auf Hadoop Version 2.4 basiert. Wenn Sie einen HDInsight-Cluster mit Hadoop Version 2.2 bereitstellen möchten, klicken Sie am Anfang dieses Artikels auf die Registerkarte für diese Version. Mit HDInsight PowerShell-Cmdlets oder dem HDInsight .NET SDK können Sie auch Hadoop-Cluster für andere Versionen erstellen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][]. Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie auf der Seite zu den [HDInsight-Komponentenversionen][].

**So stellen Sie HDInsight-Cluster bereit**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.

2.  Klicken Sie links auf **HDInsight**, um den Status der Cluster in Ihrem Konto anzuzeigen. Im folgenden Screenshot sind keine HDInsight-Cluster vorhanden.

    ![HDI.ClusterStatus][]

3.  Klicken Sie links unten auf **NEU**, klicken Sie auf **Datendienste**, klicken Sie auf **HDInsight**, und klicken Sie dann auf **Hadoop**.

    ![HDI.QuickCreateCluster][]

4.  Geben Sie folgende Werte ein, bzw. wählen Sie diese aus:

    <table border="1">
	<tr><th> Name</th><th> Wert </th></tr>
    <tr><td>Clustername   </td><td> Der Name des Clusters  </td></tr>
    <tr><td> Clustergröße </td><td> Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Der Standardwert ist 4. Die Option zur Verwendung von 1 oder 2 Datenknoten ist jedoch ebenfalls im Dropdownmenü verfügbar. Wenn Sie die Option <strong>Benutzerdefiniert erstellen</strong> verwenden, kann eine beliebige Anzahl an Datenknoten angegeben werden. Es sind Preisdetails zur Abrechnung für verschiedene Cluster-Größen verfügbar. Klicken Sie auf das Symbol <strong>?</strong> direkt über dem Dropdownfeld, und folgen Sie dem Link im Pop-up-Fenster. </td></tr>
    <tr><td> Kennwort  </td><td>Das Kennwort für das Konto <i>Admin</i>. Der Cluster-Benutzername "admin" wird angegeben, wenn Sie die Option "Benutzerdefiniert erstellen" nicht verwenden. Es handelt sich hierbei NICHT um das Windows-Administratorkonto für die virtuellen Computer, auf denen die Cluster bereitgestellt werden Der Kontoname kann mit dem Assistenten <strong>Benutzerdefiniert erstellen</strong> geändert werden.                                                                                                                  </td></tr>
    <tr><td> Speicherkonto </td><td> Wählen Sie das erstellte Speicherkonto im Dropdownfeld aus.                                                                                                                                                                                                                                                                                                                                                                                                                                               
                      Sobald ein Speicherkonto ausgewählt wurde, kann es nicht mehr geändert werden. Wird das Speicherkonto entfernt, kann der Cluster nicht mehr verwendet werden. 
	Der HDInsight-Cluster befindet sich in demselben Rechenzentrum wie das Speicherkonto.                                                                                                                                                                                                                                                       </td></tr>
	</table>

    Notieren Sie sich den Clusternamen. Sie werden ihn später im Lernprogramm benötigen.

5.  Klicken Sie auf **HDInsight-Cluster erstellen**. Wenn die Bereitstellung abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

    > [WACOM.NOTE] Mit dem vorstehenden Verfahren wird ein Cluster mit HDInsight-Clusterversion 3.1 erstellt. Zur Erstellung anderer Clusterversionen verwenden Sie die Methode "Benutzerdefiniert erstellen" im Verwaltungsportal oder aber Azure PowerShell. Informationen zu den Unterschieden zwischen den einzelnen Clusterversionen finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][]. Informationen zur Verwendung der Option **BENUTZERDEFINIERT ERSTELLEN** finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][].

## <a name="sample"></a>Ausführen eines Hive-Jobs

Nachdem Sie einen HDInsight-Cluster bereitgestellt haben, führen Sie im nächsten Schritt einen Hive-Job aus, um die Hive-Beispieltabelle *hivesampletable* abzufragen, die in HDInsight-Clustern enthalten ist. Die Tabelle enthält Daten zu Mobilgeräteherstellern, -plattformen und -modellen. Wir fragen diese Tabelle ab, um Daten für Mobilgeräte eines bestimmten Herstellers abzurufen.

**So führen Sie einen Hive-Job über das Cluster-Dashboard aus**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.
2.  Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste der erstellten Cluster, inklusive des Clusters, den Sie im vorigen Abschnitt erstellt haben.
3.  Klicken Sie auf den Namen des Clusters, für den Sie den Hive-Job ausführen möchten, und klicken Sie dann unten auf der Seite auf **CLUSTER VERWALTEN**.
4.  Daraufhin wird eine Webseite in einer anderen Browser-Registerkarte geöffnet. Geben Sie das Hadoop-Benutzerkonto und das zugehörige Kennwort ein. Der Standardbenutzername ist **admin**, und das Kennwort haben Sie bei der Bereitstellung des Clusters angegeben. Das Dashboard sieht folgendermaßen aus:

    ![hdi.dashboard][]

    Am oberen Rand befinden sich mehrere Registerkarten. Die Standardregisterkarte ist **Hive-Editor**, die anderen Registerkarten heißen **Job-Verlauf** und **Dateibrowser**. Mithilfe des Dashboards können Sie Hive-Abfragen senden, Protokolle von Hadoop-Jobs überprüfen und WASB-Dateien durchsuchen.

    > [WACOM.NOTE] Beachten Sie, dass die URL der Webseite *\<Clustername\>.azurehdinsight.net* lautet. Anstatt über das Verwaltungsportal können Sie das Dashboard also auch unter Angabe der URL über einen Webbrowser öffnen.

5.  Geben Sie auf der Registerkarte **Hive-Editor** in das Feld **Abfragename** den Text **HTC20** ein. Der Abfragename ist gleichzeitig auch der Job-Titel.

6.  Geben Sie im Abfragefenster die folgende Abfrage ein:

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][]

7.  Klicken Sie auf **Senden**. Die Ausgabe der Ergebnisse dauert einen Moment. Der Bildschirm wird alle 30 Sekunden aktualisiert. Sie können auch auf **Aktualisieren** klicken.

    Nach Abschluss des Vorgangs sieht der Bildschirm folgendermaßen aus:

    ![hdi.dashboard.query.select.result][]

8.  Klicken Sie auf den Abfragenamen, um die Ausgabe anzuzeigen. Notieren Sie sich die **Job-Startzeit (UTC)**. Sie benötigen sie später.

    ![hdi.dashboard.query.select.result.output][]

    Diese Seite enthält außerdem die **Job-Ausgabe** und das **Job-Protokoll**. Außerdem haben Sie die Möglichkeit, die Ausgabedatei (\_stdout) und die Protokolldatei (\_stderr) herunterzuladen.

    > [WACOM.NOTE] In der Tabelle **Job-Sitzung** auf der Registerkarte **Hive-Editor** werden abgeschlossene oder laufende Jobs angezeigt, solange Sie auf dieser Registerkarte bleiben. Wenn Sie die Seite verlassen, werden in der Tabelle keine Jobs aufgeführt. Die Registerkarte **Job-Verlauf** enthält eine Liste aller abgeschlossenen und laufenden Jobs.

**So navigieren Sie zur Ausgabedatei**

1.  Klicken Sie oben im Cluster-Dashboard auf **Dateibrowser**.
2.  Klicken Sie auf den Namen Ihres Speicherkontos, auf den Namen des Containers (identisch mit dem Clusternamen) und dann auf **Benutzer**.
3.  Klicken Sie auf "admin" und dann auf die GUID-Nummer, deren Uhrzeit der letzten Änderung kurz nach der früher notierten Job-Startzeit liegt. Notieren Sie sich die GUID. Sie benötigen sie im nächsten Abschnitt.

    ![hdi.dashboard.query.browse.output][]

## <a name="powerquery"></a>Verbinden mit Microsoft Business Intelligence-Tools

Mit dem Power Query-Add-in für Microsoft Excel können Sie die Job-Ausgabe von HDInsight in Excel importieren. Dort können Sie die Ergebnisse mithilfe der Microsoft Business Intelligence (BI)-Tools weiter analysieren.

Sie benötigen Excel 2010 oder 2013, um diesen Teil des Lernprogramms durchführen zu können.

**So laden Sie Microsoft Power Query für Excel herunter**

-   Laden Sie Microsoft Power Query für Microsoft Excel aus dem [Microsoft Download Center][] herunter, und installieren Sie es.

**So importieren Sie HDInsight-Daten**

1.  Öffnen Sie Excel, und erstellen Sie eine neue leere Arbeitsmappe.
2.  Klicken Sie auf das Menü **Power Query**, auf **Von anderen Quellen** und anschließend auf **Von Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][]

3.  Geben Sie den **Kontonamen** des Ihrem Cluster zugeordneten Azure Blob-Speicherkontos ein, und klicken Sie dann auf **OK**. Dies ist das Speicherkonto, das Sie zu Beginn des Lernprogramms erstellt haben.
4.  Geben Sie den **Kontoschlüssel** für das Azure Blob-Speicherkonto ein, und klicken Sie dann auf **Speichern**.
5.  Doppelklicken Sie im Navigationsbereich rechts auf den Namen des Blob-Speichercontainers. Der Container hat standardmäßig denselben Name wie der Cluster.

6.  Suchen Sie **stdou** in der Spalte **Name**. Überprüfen Sie, ob die GUID in der entsprechenden Ordnerpfadspalte der zuvor notierten GUID entspricht. Klicken Sie links von **stdout** auf **Binär**.

    ![HDI.GettingStarted.PowerQuery.ImportData2][]

7.  Klicken Sie in der oberen linken Ecke auf **Schließen und laden**, um die Ausgabe des Hive-Jobs in Excel zu importieren.

## <a name="nextsteps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie ein Cluster mit HDInsight bereitgestellt wird, ein MapReduce-Job darauf ausgeführt wird und die Ergebnisse in Excel importiert werden, wo sie mit BI-Tools weiter bearbeitet und grafisch angezeigt werden können. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit dem HDInsight-Emulator][]
-   [Verwenden von Azure Blob-Speicher mit HDInsight][]
-   [Verwalten von HDInsight mit PowerShell][]
-   [Hochladen von Daten zu HDInsight][]
-   [Verwenden von MapReduce mit HDInsight][]
-   [Verwenden von Hive mit HDInsight][]
-   [Verwenden von Pig mit HDInsight][]
-   [Verwenden von Oozie mit HDInsight][]
-   [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][]
-   [Entwickeln von Java MapReduce-Programmen für HDInsight][]

  [Hadoop 2.4]: ../hdinsight-get-started "Erste Schritte mit Hadoop 2.4 in HDInsight"
  [Hadoop 2.2]: ../hdinsight-get-started-30 "Erste Schritte mit Hadoop 2.2 in HDInsight"
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [Einführung in Hadoop in HDInsight]: ../hdinsight-introduction/
  [Erste Schritte mit dem HDInsight-Emulator]: ../hdinsight-get-started-emulator/
  [Bereitstellen von HBase-Clustern in HDInsight]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-hbase-get-started/
  [Worin besteht der Unterschied zwischen Hadoop und HBase?]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [Kaufoptionen]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Kostenlose Testversion]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Erstellen eines Azure-Speicherkontos]: #storage
  [Bereitstellen eines HDInsight-Clusters]: #provision
  [Ausführen eines Hive-Jobs]: #sample
  [Verbinden mit Microsoft Business Intelligence-Tools]: #powerquery
  [Nächste Schritte]: #nextsteps
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight]: ../hdinsight-provision-clusters/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Erstellen eines Speicherkontos]: ../storage-create-storage-account/
  [HDInsight-Komponentenversionen]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.QuickCreateCluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
  [Neuheiten in den von HDInsight bereitgestellten Clusterversionen]: ../hdinsight-component-versioning/
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Microsoft Download Center]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Verwalten von HDInsight mit PowerShell]: ../hdinsight-administer-use-powershell/
  [Hochladen von Daten zu HDInsight]: ../hdinsight-upload-data/
  [Verwenden von MapReduce mit HDInsight]: ../hdinsight-use-mapreduce
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
  [Verwenden von Oozie mit HDInsight]: ../hdinsight-use-oozie/
  [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Entwickeln von Java MapReduce-Programmen für HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
