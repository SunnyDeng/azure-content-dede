<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Erste Schritte mit Hadoop 2.4 in HDInsight (Vorschau)

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Erste Schritte mit Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<a href="../hdinsight-get-started-31" title="Erste Schritte mit Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-21" title="Erste Schritte mit Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div>

HDInsight stellt [Apache Hadoop][Apache Hadoop] als Dienst in der Cloud bereit. Das Software-Framework MapReduce wird somit in einer einfacheren, skalierbareren und kosteneffizienten Azure-Umgebung verfügbar. HDInsight bietet zudem einen kostengünstigen Ansatz für die Verwaltung und Speicherung von Daten mit dem Azure Blob-Speicher.

In diesem Lernprogramm werden Sie mithilfe des Azure-Verwaltungsportals ein Hadoop-Cluster in HDInsight bereitstellen, einen Hive-Job zur Abfrage einer Hive-Beispieltabelle mithilfe des HDInsight-Dashboards übermitteln und anschließend die ausgegebenen Daten des Hive-Jobs für eine Untersuchung in Excel importieren.

> [WACOM.NOTE] In diesem Lernprogramm wird die Verwendung von Hadoop 2.4-Clustern auf HDInsight (Vorschau) behandelt. Klicken Sie das Auswahlfeld oben auf der Seite für andere unterstützte Versionen. Versionshinweise finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][Neuheiten in den von HDInsight bereitgestellten Clusterversionen]

Die Live-Demo dieses Artikels:

<center>
[![HDI.getstarted.video][img-hdi-getstarted-video]][]

</center>
Im Rahmen der generellen Verfügbarkeit von Azure HDInsight hat Microsoft zudem den HDInsight Emulator für Azure veröffentlicht, der zuvor als *Microsoft HDInsight Developer Preview* bekannt war. Dieses Produkt wurde für Entwicklungsszenarien erstellt und unterstützt dementsprechend nur Umgebungen mit einem Knoten. Informationen zur Verwendung des HDInsight-Emulators finden Sie unter [Erste Schritte mit dem HDInsight-Emulator][Erste Schritte mit dem HDInsight-Emulator].

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][Kaufoptionen], [Spezielle Angebote][Spezielle Angebote] oder [Kostenlose Testversion][Kostenlose Testversion].
-   Einen Computer mit Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.
-   Weitere Hinweise zu den neuesten HDInsight-Versionen finden Sie unter [HDInsight-HDInsight-HDInsight-Versionshinweise][HDInsight-HDInsight-HDInsight-Versionshinweise].

**Geschätzter Zeitaufwand:** 30 Minuten

## Dieses Lernprogramm umfasst folgende Punkte

-   [Bereitstellen eines HDInsight-Clusters][Bereitstellen eines HDInsight-Clusters]
-   [Ausführen eines Hive-Jobs][Ausführen eines Hive-Jobs]
-   [Verbinden mit Microsoft Business Intelligence-Tools][Verbinden mit Microsoft Business Intelligence-Tools]
-   [Nächste Schritte][Nächste Schritte]

## <a name="provision"></a>Bereitstellen eines HDInsight-Clusters

HDInsight verwendet Azure Blob-Speicher zum Speichern von Daten. Der Speicher wird *WASB* oder *Azure Storage-Blob* genannt. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight].

Bei der Bereitstellung eines HDInsight-Clusters wird ein Azure-Speicherkonto definiert, und ein bestimmter Blob-Speichercontainer aus diesem Konto wird als Standard-Dateisystem verwendet, genau wie in HDFS. Das Speicherkonto muss sich im selben Rechenzentrum wie die Rechenressourcen von HDInsight befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

-   Südostasien
-   Nordeuropa
-   Westeuropa
-   USA (Ost)
-   USA (West)

Zusätzlich zu diesem Speicherkonto können Sie weitere Speicherkonten aus demselben Azure-Abonnement oder anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern].

Um diese Anleitung einfach zu halten, werden nur der BLOB-Container und das standardmäßige Speicherkonto verwendet und alle Dateien sind im standardmäßigen Dateisystemcontainer gespeichert, der sich unter */tutorials/getstarted/* befindet. In der Praxis werden die Datendateien in der Regel in einem festgelegten Speicherkonto gespeichert.

**So erstellen Sie ein Azure-Speicherkonto**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **SPEICHERN**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Geben Sie **URL**, **SPEICHERORT** und **REPLIZIERUNG** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. Affinitätsgruppen werden nicht unterstützt. Sie werden das neue Speicherkonto in der Speicherliste sehen.
4.  Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.
5.  Klicken Sie in der Liste auf das neue Speicherkonto, und wählen Sie es aus.
6.  Klicken Sie am Seitenende auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
7.  Notieren Sie sich den **NAMEN DES SPEICHERKONTOS** und den **PRIMÄREN ZUGRIFFSSCHLÜSSEL** (oder den **SEKUNDÄREN ZUGRIFFSSCHLÜSSEL**. Beide können verwendet werden). Sie werden diese später im Lernprogramm benötigen.

Weitere Informationen finden Sie unter
[Erstellen eines Speicherkontos][Erstellen eines Speicherkontos] und [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight].

**So stellen Sie HDInsight-Cluster bereit**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie auf der linken Seite auf **HDINSIGHT**, um die HDInsight-Cluster unter Ihrem Konto aufzulisten. Im folgenden Screenshot ist kein HDInsight-Cluster vorhanden.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Klicken Sie links unten auf **NEU**, klicken Sie auf **DATA SERVICES**, auf **HDInsight** und auf **BENUTZERDEFINIERT ERSTELLEN**.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

4.  Geben Sie auf der Registerkarte "Clusterdetails" die folgenden Werte ein, oder wählen Sie sie aus:

    | Name                  | Wert                                                                                                                                                                                                                                                                                                |
    |-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Clustername**       | Der Name des Clusters.                                                                                                                                                                                                                                                                              |
    | **Datenknoten**       | Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten.                                                                                                                                                                         
                             Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.                                                                                                                                             |
    | **HDInsight-Version** | Wählen Sie **3,1**, um einen Hadoop 2,4-Cluster in HDInsight zu erstellen.                                                                                                                                                                                                                          |
    | **Region**            | Wählen Sie dieselbe Region wie für das Speicherkonto, das Sie im letzten Verfahren erstellt haben. In HDInsight muss das Speicherkonto sich in derselben Region befinden. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet. |

5.  Klicken Sie in der rechten unteren Ecke auf den Pfeil nach rechts, um den Cluster-Benutzer zu konfigurieren.
6.  Geben Sie auf der Registerkarte "Clusterbenutzer konfigurieren" den **Benutzernamen** und das **Kennwort** für das Benutzerkonto im HDInsight-Cluster an. Zusätzlich zu diesem Konto können Sie nach der Bereitstellung des Clusters ein RDP-Benutzerkonto erstellen, sodass Sie über Remotedesktop auf den Cluster zugreifen können. Eine Anleitung finden Sie unter [Administer HDInsight using Management portal][hdinsight-admin-portal]
7.  Klicken Sie in der rechten unteren Ecke auf den Pfeil nach rechts, um das Speicherkonto zu konfigurieren.
8.  Geben Sie auf der Registerkarte "Speicherkonto" die folgenden Werte ein, oder wählen Sie sie aus:

    | Name                       | Wert                                                                                                                                                                                                   |
    |----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | STORAGE ACCOUNT            | Wählen Sie **Existierenden Speicher verwenden**. Sie haben auch die Möglichkeit, über das Verwaltungsportal ein neues Speicherkonto zu erstellen, wenn Sie dies nicht bereits getan haben.             |
    | ACCOUNT NAME               | Geben Sie das Speicherkonto an, das Sie im letzten Verfahren dieses Lernprogramms erstellt haben. Bitte beachten Sie, dass im Listenfeld nur die Speicherkonten aus derselben Region angezeigt werden. |
    | DEFAULT CONTAINER          | Wählen Sie **Standardcontainer erstellen**. Wenn diese Option ausgewählt ist, hat der Standardcontainer denselben Namen wie der Cluster.                                                               |
    | ADDITIONAL STORAGE ACCOUNT | Wählen Sie **0** aus. Sie haben die Möglichkeit, den Cluster mit bis zu 7 zusätzlichen Speicherkonten zu verbinden.                                                                                    |

9.  Klicken Sie auf das Häkchensymbol in der rechten unteren Ecke, um den Cluster zu erstellen. Wenn der Bereitstellungsprozess abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

## <a name="sample"></a>Ausführen eines Hive-Jobs

Sie haben jetzt einen HDInsight-Cluster bereitgestellt. Der nächste Schritt besteht darin, einen Hive-Job zur Abfrage einer Hive-Beispieltabelle ausführen, die mit HDInsight-Clustern kommt. Der Name der Tabelle lautet *hivesampletable*.

**So öffnen Sie das HDInsight-Dashboard**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste der erstellten Cluster, inklusive des Clusters, das Sie im vorigen Abschnitt erstellt haben.
3.  Klicken Sie auf den Namen des Clusters, in dem Sie den Hive-Job ausführen möchten.
4.  Klicken Sie unten auf der Seite auf **CLUSTER VERWALTEN**, um das HDInsight-Dashboard zu öffnen. Daraufhin wird eine Webseite in einer neuen Browser-Registerkarte geöffnet.
5.  Geben Sie den Benutzernamen und das Kennwort des Hadoop-Benutzers ein. Der Standard-Benutzername ist **admin**, und das Kennwort haben Sie bei der Einrichtung ausgewählt. Das Dashboard sieht folgendermaßen aus:

    ![hdi.dashboard][hdi.dashboard]

    Am oberen Rand befinden sich mehrere Registerkarten. Die standardmäßige Registerkarte ist *Hive-Editor*, andere sind unter anderem „Jobs“ und „Dateien“. Mithilfe des Dashboards können Sie Hive-Abfragen senden, Protokolle von Hadoop-Jobs überprüfen und WASB-Dateien durchsuchen.

> [wacom.note] Hinweis: Die URL ist *\<ClusterName\>.azurehdinsight.net*. Anstatt über das Verwaltungsportal können Sie das Dashboard auch unter Angabe der URL über einen Webbrowser öffnen.

**So führen Sie eine Hive-Abfrage aus**

1.  Klicken Sie oben im HDInsight-Dashboard auf **Hive-Editor**.
2.  Geben Sie in **Abfragename** **HTC20** ein. Der Abfragename ist gleichzeitig auch der Jobtitel.
3.  Geben Sie im Abfragefenster die folgende Abfrage ein:

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4.  Klicken Sie auf **Senden**. Die Ausgabe der Ergebnisse dauert einen Moment. Der Bildschirm wird alle 30 Sekunden aktualisiert. Sie können auch auf **Aktualisieren** klicken.

    Nach Abschluss des Vorgangs sieht der Bildschirm folgendermaßen aus:

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    Notieren Sie sich die **Job-Startzeit (UTC)**. Sie benötigen sie später.

    Scrollen Sie etwas nach unten. Dort sehen Sie **Job-Protokoll**. Die Job-Ausgabe ist stdout, das Job-Protokoll ist stderr.

5.  Wenn Sie die Protokolldatei künftig noch einmal öffnen möchten, können Sie oben im Fenster auf **Jobs** klicken und dann auf den Jobtitel (Abfragename). In diesem Fall zum Beispiel **HTC20**.

**So durchsuchen Sie die Ausgabedatei**

1.  Klicken Sie oben im HDInsight-Cluster-Dashboard auf **Dateien**.
2.  Klicken Sie auf **Templeton-Job-Status**.
3.  Klicken Sie auf die GUID-Nummer, deren letzte Änderung kurz nach der Startzeit des Jobs liegt, die Sie zuvor notiert haben. Notieren Sie sich die GUID. Sie benötigen sie im nächsten Abschnitt.
4.  Die **stdout**-Datei enthält die Daten, die Sie für den nächsten Abschnitt benötigen. Sie können **stdout** anklicken, falls Sie eine Kopie der Daten herunterladen möchten.

## <a name="powerquery"></a>Verbinden mit Microsoft Business Intelligence-Tools

Das Power Query-Add-in für Excel kann verwendet werden, um die Ausgabe von HDInsight in Excel zu exportieren, wo Microsoft Business Intelligence-Tools (BI) verwendet werden können, um die Ergebnisse weiter zu bearbeiten oder anzuzeigen.

Sie benötigen Excel 2010 oder 2013, um diesen Teil des Lernprogramms durchführen zu können.

**So laden Sie Microsoft Power Query für Excel herunter**

-   Laden Sie Microsoft Power Query für Excel aus dem [Microsoft Download Center][Microsoft Download Center] herunter, und installieren Sie es.

**So importieren Sie HDInsight-Daten**

1.  Öffnen Sie Excel, und erstellen Sie eine neue leere Arbeitsmappe.
2.  Klicken Sie auf das Menü **Power Query**, auf **Von anderen Quellen** und anschließend auf **Von Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  Geben Sie den **Kontonamen** des Ihrem Cluster zugeordneten Azure Blob-Speicherkontos ein, und klicken Sie dann auf **OK**. Dies ist das Speicherkonto, das Sie zu Beginn des Lernprogramms erstellt haben.
4.  Geben Sie den **Kontoschlüssel** für das Azure Blob-Speicherkonto ein, und klicken Sie dann auf **Speichern**.
5.  Doppelklicken Sie im Navigationsbereich rechts auf den Namen des Blob-Speichercontainers. Der Container hat standardmäßig denselben Name wie der Cluster.

6.  Lokalisieren Sie **stdout** in der Spalte **Name** (der Pfad ist *.../Templeton-Job-Status/<guid>*) und klicken Sie anschließend **Binary** links von **stdout**. Die <guid> muss derjenigen entsprechen, die Sie in der letzten Auswahl aufgeschrieben haben.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Klicken Sie in der oberen linken Ecke auf **Übernehmen & Schließen**. Die Abfrage importiert die Hive-Jobausgabe anschließend in Excel.

## <a name="nextsteps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie ein Cluster mit HDInsight bereitgestellt wird, ein MapReduce-Job darauf ausgeführt wird und die Ergebnisse in Excel importiert werden, wo sie mit BI-Tools weiter bearbeitet und grafisch angezeigt werden können. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit dem HDInsight-Emulator][Erste Schritte mit dem HDInsight-Emulator]
-   [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight]
-   [Verwalten von HDInsight mit PowerShell][Verwalten von HDInsight mit PowerShell]
-   [Hochladen von Daten zu HDInsight][Hochladen von Daten zu HDInsight]
-   [Verwenden von MapReduce mit HDInsight][Verwenden von MapReduce mit HDInsight]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]
-   [Verwenden von Oozie mit HDInsight][Verwenden von Oozie mit HDInsight]
-   [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight]
-   [Entwickeln von Java MapReduce-Programmen für HDInsight][Entwickeln von Java MapReduce-Programmen für HDInsight]

  [Apache Hadoop]: http://hadoop.apache.org/
  [Neuheiten in den von HDInsight bereitgestellten Clusterversionen]: ../hdinsight-component-versioning/
  [![HDI.getstarted.video][img-hdi-getstarted-video]]: https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS
  [Erste Schritte mit dem HDInsight-Emulator]: ../hdinsight-get-started-emulator/
  [Kaufoptionen]: http://azure.microsoft.com/de-de/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/de-de/pricing/member-offers/
  [Kostenlose Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [HDInsight-HDInsight-HDInsight-Versionshinweise]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-release-notes/
  [Bereitstellen eines HDInsight-Clusters]: #provision
  [Ausführen eines Hive-Jobs]: #sample
  [Verbinden mit Microsoft Business Intelligence-Tools]: #powerquery
  [Nächste Schritte]: #nextsteps
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Bereitstellen von HDInsight-Clustern]: ../hdinsight-provision-clusters/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Erstellen eines Speicherkontos]: ../storage-create-storage-account/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Microsoft Download Center]: http://www.microsoft.com/de-de/download/details.aspx?id=39379
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
