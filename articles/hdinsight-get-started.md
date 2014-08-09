<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using HDInsight | Azure" metaKeywords="" description="Get started with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Erste Schritte mit Azure HDInsight
==================================

HDInsight stellt [Apache Hadoop](http://hadoop.apache.org/) als Dienst in der Cloud bereit. Das Software-Framework MapReduce wird somit in einer einfacheren, skalierbareren und kosteneffizienten Azure-Umgebung verfügbar. HDInsight bietet zudem einen kosteneffizienten Ansatz für die Verwaltung und Speicherung von Daten mit dem Azure Blob-Speicher.

In diesem Lernprogramm werden Sie mithilfe des Azure Verwaltungsportals einen HDInsight-Cluster bereitstellen, einen Hadoop MapReduce-Job mithilfe von PowerShell übermitteln, und anschließend die ausgegebenen Daten des MapReduce-Jobs für eine Untersuchung in Excel importieren.

> [WACOM.NOTE] In diesem Lernprogramm wird die Verwendung von Hadoop 1.2-Clustern auf HDInsight behandelt. Das Lernprogramm zur Verwendung von Hadoop 2.2-Clustern auf HDInsight finden Sie unter [Get started using Hadoop 2.2 clusters with HDInsight](/de-de/documentation/articles/hdinsight-get-started-30/) (Erste Schritte mit Hadoop 2.2-Clustern mit HDInsight, in englischer Sprache).

In Verbindung mit der generellen Verfügbarkeit von Azure HDInsight hat Microsoft zudem HDInsight Emulator für Azure gestartet, das zuvor als Microsoft HDInsight Developer Preview bekannt war. Dieses Produkt wurde für Entwicklungsszenarien erstellt, und unterstützt dementsprechend nur Umgebungen mit einem Knoten. Informationen zur Verwendung von HDInsight Emulator finden Sie unter [Get Started with the HDInsight Emulator](/de-de/documentation/articles/hdinsight-get-started-emulator/) (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Spezielle Angebote](https://www.windowsazure.com/en-us/pricing/member-offers/) oder [Kostenlose einmonatige Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/).
-   Einen Computer, auf dem Windows 8, Windows 7, Windows Server 2012 oder Windows Server 2008 R2 läuft. Dieser Computer wird verwendet, um MapReduce-Jobs zu übermitteln.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

**Geschätzter Zeitaufwand:** 30 Minuten

Dieses Lernprogramm umfasst folgende Punkte:
--------------------------------------------

-   [Einrichten einer lokalen Umgebung für die Ausführung von PowerShell](#setup)
-   [Bereitstellen eines HDInsight-Clusters](#provision)
-   [Ausführen eines WordCount-MapReduce-Programms](#sample)
-   [Verbinden mit Microsoft Business Intelligence-Tools](#powerquery)
-   [Nächste Schritte](#nextsteps)

Einrichten einer lokalen Umgebung für die Ausführung von PowerShell
-------------------------------------------------------------------

Es gibt verschiedene Möglichkeiten, um MapReduce-Jobs an HDInsight zu übermitteln. In diesem Lernprogramm werden Sie Azure PowerShell verwenden. Um Azure PowerShell zu installieren, führen Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) aus. Klicken Sie bei der entsprechenden Aufforderung auf **Ausführen**, klicken Sie auf **Installieren**, und folgen Sie dann den Anweisungen. Weitere Informationen finden Sie unter [Install and configure Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/) (Installieren und Konfigurieren von Azure PowerShell, in englischer Sprache).

Für die PowerShell-Cmdlets werden Ihre Abonnementinformationen benötigt, damit Sie Ihre Dienste über das Abonnement verwalten können.

**So verbinden Sie sich über Azure AD mit Ihrem Abonnement**

1.  Öffnen Sie die Azure PowerShell-Konsole, wie unter [Gewusst wie: Installieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/#install) beschrieben.
2.  Führen Sie den folgenden Befehl aus:

         Add-AzureAccount

3.  Geben Sie in dem Fenster die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

Eine andere Methode zum Verbinden mit dem Abonnement ist die Verwendung der Zertifikatmethode. Weitere Informationen finden Sie unter [Install and configure Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/) (Installieren und Konfigurieren von Azure PowerShell, in englischer Sprache).

Bereitstellen eines HDInsight-Clusters
--------------------------------------

Der Bereitstellungsprozess für HDInsight erfordert, dass ein Azure-Speicherkonto als Standarddateisystem verwendet wird. Das Speicherkonto muss sich im selben Rechenzentrum wie die Rechenressourcen von HDInsight befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

-   Südostasien
-   Nordeuropa
-   Westeuropa
-   Ost-USA
-   West-USA

Sie müssen eines dieser fünf Rechenzentren für Ihr Azure-Speicherkonto auswählen.

**So erstellen Sie ein Azure-Speicherkonto**

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2.  Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **SPEICHERN**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png)

3.  Geben Sie **URL**, **SPEICHERORT** und **REPLIZIERUNG** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. Affinitätsgruppen werden nicht unterstützt. Sie werden das neue Speicherkonto in der Speicherliste sehen.
4.  Warten Sie, bis sich der **STATUS** des neuen Speicherkontos auf **Online** geändert hat.
5.  Klicken Sie in der Liste auf das neue Speicherkonto, und wählen Sie es aus.
6.  Klicken Sie am Seitenende auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
7.  Notieren Sie sich **SPEICHERKONTONAME** und **PRIMÄRER ZUGRIFFSSCHLÜSSEL**. Sie werden diese später im Lernprogramm benötigen.

Genauere Anweisungen erhalten Sie unter [How to Create a Storage Account](/de-de/documentation/articles/storage-create-storage-account/) (So erstellen Sie ein Speicherkonto, in englischer Sprache) und [Use Azure Blob Storage with HDInsight](/de-de/documentation/articles/hdinsight-use-blob-storage/) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache).

**So stellen Sie HDInsight-Cluster bereit**

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.

2.  Klicken Sie links auf **HDInsight**, um den Status der Cluster in Ihrem Konto anzuzeigen. Im folgenden Screenshot ist kein HDInsight-Cluster vorhanden.

    ![HDI.ClusterStatus](./media/hdinsight-get-started/HDI.ClusterStatus.png)

3.  Klicken Sie links unten auf **NEU**, klicken Sie auf **Data Services**, klicken Sie auf **HDInsight**, und klicken Sie dann auf **Schnellerfassung**.

    ![HDI.QuickCreateCluster](./media/hdinsight-get-started/HDI.QuickCreateCluster.png)

4.  Geben Sie folgende Werte ein bzw. wählen diese aus:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Name
    Wert</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Clustername
    Der Name des Clusters</td>
    <td align="left">Clustergröße
    Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Der Standardwert ist 4. Es sind jedoch auch Cluster mit 8, 16 und 32 Datenknoten im Dropdownmenü verfügbar. Wenn Sie die Option <strong>Custom Create</strong> verwenden, kann eine beliebige Anzahl an Datenknoten angegeben werden. Es sind Preisdetails zur Abrechnung für verschiedene Cluster-Größen verfügbar. Klicken Sie auf das Symbol <strong>?</strong> direkt über dem Dropdownfeld, und folgen Sie dem Link im Pop-up-Fenster.</td>
    </tr>
    </tbody>
    </table>

5.  Klicken Sie links unten auf **HDInsight-Cluster erstellen**. Wenn der Bereitstellungsprozess abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

Informationen zur Verwendung der Option **CUSTOM CREATE** finden Sie unter [Provision HDInsight Clusters](/de-de/documentation/articles/hdinsight-provision-clusters/) (Bereitstellen von HDInsight-Clustern, in englischer Sprache).

Ausführen eines WordCount-MapReduce-Jobs
----------------------------------------

Sie haben jetzt ein HDInsight-Cluster bereitgestellt. Der nächste Schritt ist es, einen MapReduce-Job auszuführen, um Wörter in einer Textdatei zu zählen.

Für die Ausführung eines MapReduce-Jobs sind folgende Elemente erforderlich:

-   Ein MapReduce-Programm. In diesem Lernprogramm werden Sie das WordCount-Beispiel verwenden, das in der HDInsight-Clusterbereitstellung enthalten ist, sodass Sie kein eigenes schreiben müssen. Es befindet sich auf */example/jars/hadoop-examples.jar*. Anweisungen zum Schreiben eines eigenen MapReduce-Jobs finden Sie unter [Develop Java MapReduce programs for HDInsight](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/) (Entwickeln von Java MapReduce-Programmen für HDInsight, in englischer Sprache).

-   Eine Eingabedatei. Sie werden */example/data/gutenberg/davinci.txt* als Eingabedatei verwenden. Informationen zum Hochladen von Dateien finden Sie unter [Upload Data to HDInsight](/de-de/documentation/articles/hdinsight-upload-data/) (Hochladen von Daten zu HDInsight, in englischer Sprache).
-   Einen Ordner für die Ausgabedatei. Sie werden */example/data/WordCountOutput* als Ordner für die Ausgabedatei verwenden. Das System erstellt den Ordner, falls dieser nicht vorhanden ist.

Das URI-Schema für den Dateizugriff im Blob-Speicher ist:

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] Standardmäßig hat der für das Standarddateisystem verwendete Blob-Container denselben Namen wie der HDInsight-Cluster.

Das URI-Schema bietet sowohl unverschlüsselten Zugriff mit dem Präfix *wasb:* als auch SSL-verschlüsselten Zugriff mit WASBS. Wir empfehlen die Verwendung von Wasbs, selbst für den Zugriff auf Daten, die sich im selben Azure-Rechenzentrum befinden.

Da HDInsight einen Blob-Speichercontainer als Standarddateisystem verwendet, können Sie auf Dateien und Verzeichnisse innerhalb des Standarddateisystems über relative oder absolute Pfade verweisen.

Um zum Beispiel auf hadoop-examples.jar zuzugreifen, können Sie eine der folgenden Optionen verwenden:

    â— wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-examples.jar
    â— wasb:///example/jars/hadoop-examples.jar
    â— /example/jars/hadoop-examples.jar

Die Verwendung des Präfix *wasb://* in den Pfaden dieser Datei. Dies ist erforderlich, um anzuzeigen, dass Azure Blob-Speicher für Eingabe- und Ausgabedateien verwendet wird. Das Ausgabeverzeichnis geht von einem relativen Standardpfad zum Ordner *wasb:///user/&lt;username\>* aus.

Weitere Informationen erhalten Sie unter [Use Azure Blob Storage with HDInsight](/de-de/documentation/articles/hdinsight-use-blob-storage/) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache).

**So führen Sie das WordCount-Beispiel aus**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Install and configure Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/) (Installieren und Konfigurieren von Azure PowerShell, in englischer Sprache).

2.  Führen Sie die folgenden Befehle aus, um die Variablen festzulegen:

         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  Führen Sie die folgenden Befehle aus, um eine MapReduce-Jobdefinition zu erstellen:

         # Define the MapReduce job
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    Die Datei hadoop-examples.jar enthält die HDInsight-Clusterbereitstellung. Es gibt zwei Argumente für den MapReduce-Job. Das erste ist der Name der Ausgangsdatei, das zweite ist der Pfad der Ausgabedatei. Die Ausgangsdatei enthält die HDInsight-Clusterbereitstellung, und der Pfad der Ausgabedatei wird während der Laufzeit erstellt.

4.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job auszuführen:

         # Submit the job
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten.

    *Start-AzureHDInsightJob* ist ein asynchronisierter Aufruf. Verwenden Sie das Cmdlet *Wait-AzureHDInsightJob*, um zu überprüfen, ob der Job abgeschlossen ist.

5.  Führen Sie den folgenden Befehl aus, um den Abschluss des MapReduce-Jobs zu überprüfen:

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Jobs aufgetreten sind:

         # Get the job output
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

    Der folgende Screenshot zeigt die Ausgabe bei erfolgreicher Ausführung. Andernfalls werden Fehlermeldungen angezeigt.

    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png)

**So erhalten Sie die Ergebnisse des MapReduce-Jobs**

1.  Öffnen Sie **Azure PowerShell**.
2.  Führen Sie folgende Befehle aus, um einen Ordner C:\\Tutorials zu erstellen, und ändern Sie das Verzeichnis zum Ordner:

         mkdir \Tutorials
         cd \Tutorials

    Das Standardverzeichnis für Azure Powershell ist *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. Sie haben standardmäßig keinen Schreibzugriff für diesen Ordner. Sie müssen das Verzeichnis zu einem Ordner ändern, für den Sie Schreibzugriff haben.

3.  Legen Sie die drei Variablen in den folgenden Befehlen fest, und führen Sie diese aus:

         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"   
         $containerName = "<ContainerName>"              

    Das Azure-Speicherkonto ist das Konto, das Sie zuvor im Lernprogramm erstellt haben. Das Speicherkonto wird verwendet, um den Blob-Container zu hosten, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Name des Blob-Speichercontainers trägt normalerweise denselben Namen wie der HDInsight-Cluster, außer, Sie haben einen anderen Namen bei der Bereitstellung des Clusters verwendet.

4.  Führen Sie folgende Befehle aus, um ein Azure-Speicherkontextobjekt zu erstellen:

         # Create the storage account context object
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* wird verwendet, um das aktuelle Abonnement festzulegen, falls sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll.

5.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe vom Blob-Container zur Arbeitstation herunterzuladen:

         # Download the job output to the workstation
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Der Ordner *example/data/WordCountOutput* ist der angegebene Ausgabeordner für die Ausführung des MapReduce-Jobs. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird in dieselbe Ordnerstruktur auf dem lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C. Die Datei wird in den Ordner *C:\\example\\data\\WordCountOutput\\* heruntergeladen.

6.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabedatei auszudrucken:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png)

    Der MapReduce-Job produziert eine Datei namens *part-r-00000*, welche die Wörter und deren Anzahl enthält. Das Skript verwendet den Befehl findstr, um alle Wörter aufzulisten, die *"there"* enthalten.

> [WACOM.NOTE] Wenn Sie *./example/data/WordCountOutput/part-r-00000*, eine mehrzeilige Ausgabe eines MapReduce-Jobs, im Editor öffnen, werden Sie feststellen, dass die Zeilenumbrüche nicht korrekt sind. Dies muss so sein.

Verbinden mit Microsoft Business Intelligence-Tools
---------------------------------------------------

Das Power Query-Add-in für Excel kann verwendet werden, um die Ausgabe von HDInsight in Excel zu exportieren, wo Microsoft Business Intelligence-Tools (BI) verwendet werden können, um die Ergebnisse weiter zu bearbeiten oder anzuzeigen. Wenn Sie einen HDInsight-Cluster erstellt haben, wurde ein Standardcontainer mit demselben Namen wie der Cluster im Speicherkonto erstellt, das bei dessen Erstellung damit verbunden wurde. Dieser wird automatisch mit einem Dateiensatz aufgefüllt. Eine dieser Dateien ist eine Beispiel-Hive-Tabelle. In diesem Abschnitt wird gezeigt, wie die in dieser Tabelle enthaltenen Daten in Excel importiert werden, um sie anzuzeigen und weiter zu bearbeiten.

Sie benötigen Excel 2010 oder 2013, um diesen Teil des Lernprogramms durchführen zu können. Es wird nun gezeigt, wie die Standard-Hive-Tabelle aus HDInsight importiert wird.

**So laden Sie Microsoft PowerQuery für Excel herunter**

-   Laden Sie Microsoft Power Query für Excel von [Microsoft Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=39379) herunter, und installieren Sie es.

**So importieren Sie HDInsight-Daten**

1.  Öffnen Sie Excel, und erstellen Sie eine neue leere Arbeitsmappe.
2.  Klicken Sie auf das Menü **Power Query**, klicken Sie auf **Von anderen Quellen**, und klicken Sie dann auf **Von Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  Geben Sie den **Kontonamen** des mit Ihrem Cluster verbundenen Azure Blob-Speicherkontos ein, und klicken Sie dann auf **OK**. Dies ist das Speicherkonto, das Sie zu Beginn des Lernprogramms erstellt haben.
4.  Geben Sie den **Kontoschlüssel** für das Azure Blob-Speicherkonto ein, und klicken Sie dann auf **Speichern**.
5.  Doppelklicken Sie im Navigationsbereich rechts auf den Namen des Blob-Speichercontainers. Der Containername ist standardmäßig derselbe Name wie der Clustername.

6.  Navigieren Sie zu **part-r-00000** in der Spalte **Name** (der Pfad ist *.../example/data/WordCountOutput*), und klicken Sie dann auf **Binär** links von **part-r-00000**.

    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Klicken Sie mit der rechten Maustaste auf **Column1.1**, und wählen Sie **Umbenennen**.
8.  Ändern Sie den Namen in **Word**.
9.  Wiederholen Sie den Vorgang, um **Column1.2** in **Count** umzubenennen.

    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Klicken Sie in der oberen linken Ecke auf **Übernehmen & Schließen**. Die Abfrage importiert die Hive-Tabelle anschließend in Excel.

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie erfahren, wie ein Cluster mit HDInsight bereitgestellt wird, ein MapReduce-Job darauf ausgeführt wird, und die Ergebnisse in Excel importiert werden, wo sie mit BI-Tools weiter bearbeitet und grafisch angezeigt werden können. Weitere Informationen erhalten Sie in folgenden Artikeln:

-   [Get started using Hadoop 2.2 clusters with HDInsight (Erste Schritte mit Hadoop 2.2-Clustern mit HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-get-started-30/)
-   [Get started with the HDInsight Emulator (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache)](/de-de/documentation/articles/hdinsight-get-started-emulator/)
-   [Use Azure Blob storage with HDInsight (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-use-blob-storage/)
-   [Administer HDInsight using PowerShell (Verwalten von HDInsight mit PowerShell, in englischer Sprache)](/de-de/documentation/articles/hdinsight-administer-use-powershell/)
-   [Upload data to HDInsight (Hochladen von Daten zu HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-upload-data/)
-   [Use MapReduce with HDInsight (Verwenden von MapReduce mit HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-use-mapreduce)
-   [Use Hive with HDInsight (Verwenden von Hive mit HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-use-hive/)
-   [Use Pig with HDInsight )Verwenden von Pig mit HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-use-pig/)
-   [Use Oozie with HDInsight (Verwenden von Oozie mit HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-use-oozie/)
-   [Develop C\# Hadoop streaming programs for HDInsight (Entwickeln von C\# Hadoop-Streamingprogrammen für HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Develop Java MapReduce programs for HDInsight (Entwickeln von Java MapReduce-Programmen für HDInsight, in englischer Sprache)](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

