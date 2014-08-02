<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using Hadoop 2.2 clusters with HDInsight | Azure" metaKeywords="" description="Get started using Hadoop 2.2 clusters with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Erste Schritte mit Hadoop 2.2-Clustern in HDInsight
===================================================

HDInsight stellt [Apache Hadoop](http://hadoop.apache.org/) als Dienst in der Cloud bereit. Das Software-Framework MapReduce wird somit in einer einfacheren, skalierbareren und kosteneffizienten Azure-Umgebung verfügbar. HDInsight bietet zudem einen kostengünstigen Ansatz für die Verwaltung und Speicherung von Daten mit dem Azure Blob-Speicher.

In diesem Lernprogramm werden Sie mithilfe des Azure-Verwaltungsportals einen HDInsight-Cluster bereitstellen, einen Hadoop MapReduce-Job mithilfe von PowerShell übermitteln und anschließend die ausgegebenen Daten des MapReduce-Jobs für eine Untersuchung in Excel importieren.

> [WACOM.NOTE] In diesem Lernprogramm wird die Verwendung von Hadoop 2,2-Clustern auf HDInsight behandelt. Das Lernprogramm zur Verwendung von Hadoop 1.2-Clustern auf HDInsight finden Sie unter [Erste Schritte mit Azure HDInsight](/en-us/documentation/articles/hdinsight-get-started/).

> [WACOM.NOTE] Die *asv://*-Syntax wird in HDInsight-Clustern der Version 3.0 und auch in späteren Versionen nicht unterstützt. Stattdessen sollte die *wasb://*-Syntax verwendet werden.

Im Rahmen der generellen Verfügbarkeit von Azure HDInsight hat Microsoft zudem den HDInsight Emulator für Azure veröffentlicht, der zuvor als Microsoft HDInsight Developer Preview bekannt war. Dieses Produkt wurde für Entwicklungsszenarien erstellt und unterstützt dementsprechend nur Umgebungen mit einem Knoten. Informationen zur Verwendung von HDInsight Emulator finden Sie unter [Get Started with the HDInsight Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/) (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Spezielle Angebote](https://www.windowsazure.com/en-us/pricing/member-offers/) oder [Kostenlose Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/).
-   Einen Computer, auf dem Windows 8, Windows 7, Windows Server 2012 oder Windows Server 2008 R2 läuft. Dieser Computer wird verwendet, um MapReduce-Jobs zu übermitteln.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

**Geschätzter Zeitaufwand:** 30 Minuten

Dieses Lernprogramm umfasst folgende Punkte
-------------------------------------------

-   [Einrichten einer lokalen Umgebung für die Ausführung von PowerShell](#setup)
-   [Bereitstellen eines HDInsight-Clusters](#provision)
-   [Ausführen eines WordCount-MapReduce-Programms](#sample)
-   [Verbinden mit Microsoft Business Intelligence-Tools](#powerquery)
-   [Nächste Schritte](#nextsteps)

Einrichten einer lokalen Umgebung für die Ausführung von PowerShell
-------------------------------------------------------------------

Es gibt verschiedene Möglichkeiten, um MapReduce-Jobs an HDInsight zu übermitteln. In diesem Lernprogramm werden Sie Azure PowerShell verwenden. Um Azure PowerShell zu installieren, führen Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) aus. Klicken Sie bei der entsprechenden Aufforderung auf **Ausführen** sowie auf **Installieren**, und folgen Sie dann den Anweisungen. Weitere Informationen finden Sie unter [Install and configure Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/) (Installieren und Konfigurieren von Azure PowerShell, in englischer Sprache).

Für die PowerShell-Cmdlets werden Ihre Abonnementinformationen benötigt, damit Sie Ihre Dienste über das Abonnement verwalten können.

**Herstellen einer Verbindung mit Ihrem Abonnement über Azure AD**

1.  Öffnen Sie die Azure PowerShell-Konsole, wie unter [Gewusst wie: Installieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/#install) beschrieben.
2.  Führen Sie den folgenden Befehl aus:

         Add-AzureAccount

3.  Geben Sie in dem Fenster die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

Eine andere Methode zum Verbinden mit dem Abonnement ist die Verwendung der Zertifikatmethode. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Bereitstellen eines HDInsight-Clusters
--------------------------------------

Der Bereitstellungsprozess für HDInsight erfordert, dass ein Azure-Speicherkonto als Standarddateisystem verwendet wird. Das Speicherkonto muss sich im selben Rechenzentrum wie die Rechenressourcen von HDInsight befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

-   Südostasien
-   Nordeuropa
-   Westeuropa
-   USA (Ost)
-   USA (West)

Sie müssen eines dieser fünf Rechenzentren für Ihr Azure-Speicherkonto auswählen.

**Erstellen eines Azure-Speicherkontos**

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2.  Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **SPEICHERN**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started-3.0/HDI.StorageAccount.QuickCreate.png)

3.  Geben Sie **URL**, **SPEICHERORT** und **REPLIZIERUNG** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. Affinitätsgruppen werden nicht unterstützt. Sie werden das neue Speicherkonto in der Speicherliste sehen.
4.  Warten Sie, bis sich der **STATUS** des neuen Speicherkontos auf **Online** geändert hat.
5.  Klicken Sie in der Liste auf das neue Speicherkonto, und wählen Sie es aus.
6.  Klicken Sie am Seitenende auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
7.  Notieren Sie sich **SPEICHERKONTONAME** und **PRIMÄRER ZUGRIFFSSCHLÜSSEL**. Sie werden diese später im Lernprogramm benötigen.

Genauere Anweisungen erhalten Sie unter [How to Create a Storage Account](/en-us/documentation/articles/storage-create-storage-account/) (Erstellen eines Speicherkontos, in englischer Sprache) und [Use Azure Blob Storage with HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache).

Die Bereitstellung von HDInsight 3.0-Clustern wird aktuell nur durch die Option zur benutzerdefinierten Erstellung.unterstützt.

**Bereitstellen eines HDInsight-Clusters**

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.

2.  Klicken Sie auf der linken Seite auf **HDINSIGHT**, um die HDInsight-Cluster unter Ihrem Konto aufzulisten. Im folgenden Screenshot ist kein HDInsight-Cluster vorhanden.

    ![HDI.ClusterStatus](./media/hdinsight-get-started-3.0/HDI.ClusterStatus.png)

3.  Klicken Sie links unten auf **NEU**, klicken Sie auf **DATA SERVICES**, auf **HDInsight** und auf **BENUTZERDEFINIERT ERSTELLEN**.

    ![HDI.CustomCreateCluster](./media/hdinsight-get-started-3.0/HDI.CustomCreateCluster.png)

4.  Geben Sie auf der Registerkarte "Clusterdetails" die folgenden Werte ein, oder wählen Sie sie aus:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td><strong>Clustername</strong></td><td>Der Name des Clusters.</td></tr>
	<tr><td><strong>Datenknoten</strong></td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten. <br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td></tr>
	<tr><td><strong>HDInsight-Version</strong></td><td>Wählen Sie <strong>3.0</strong> um einen Hadoop 2.2-Cluster in HDInsight zu erstellen.</td></tr>
	<tr><td><strong>Region</strong></td><td>Wählen Sie dieselbe Region wie für das Speicherkonto, das Sie im letzten Verfahren erstellt haben. In HDInsight muss das Speicherkonto sich in derselben Region befinden. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet. 
	</td></tr>
	</table>

5.  Klicken Sie in der rechten unteren Ecke auf den Pfeil nach rechts, um den Cluster-Benutzer zu konfigurieren.
6.  Geben Sie auf der Registerkarte "Clusterbenutzer konfigurieren" den **Benutzernamen** und das **Kennwort** für das Benutzerkonto im HDInsight-Cluster an. Zusätzlich zu diesem Konto können Sie nach der Bereitstellung des Clusters ein RDP-Benutzerkonto erstellen, sodass Sie über Remotedesktop auf den Cluster zugreifen können. Anweisungen dazu finden Sie unter [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal](/en-us/documentation/articles/hdinsight-administer-use-management-portal/)
7.  Klicken Sie in der rechten unteren Ecke auf den Pfeil nach rechts, um das Speicherkonto zu konfigurieren.
8.  Geben Sie auf der Registerkarte "Speicherkonto" die folgenden Werte ein, oder wählen Sie sie aus:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>STORAGE ACCOUNT</td><td>Wählen Sie <strong>Existierenden Speicher verwenden</strong>. Sie haben auch die Möglichkeit, über das Verwaltungsportal ein neues Speicherkonto zu erstellen, wenn Sie dies nicht bereits getan haben.</td></tr>
	<tr><td>ACCOUNT NAME</td><td>Geben Sie das Speicherkonto an, das Sie im letzten Verfahren dieses Lernprogramms erstellt haben. Bitte beachten Sie, dass im Listenfeld nur die Speicherkonten aus derselben Region angezeigt werden.</td></tr>
	<tr><td>DEFAULT CONTAINER</td><td>Wählen Sie <strong>Standardcontainer erstellen</strong>. Wenn diese Option ausgewählt ist, hat der Standardcontainer denselben Namen wie der Cluster.</td></tr>
	<tr><td>ADDITIONAL STORATGE ACCOUNT</td><td>Wählen Sie <strong>0</strong>. aus. Sie haben die Möglichkeit, den Cluster mit bis zu 7 zusätzlichen Speicherkonten zu verbinden.</td></tr>
	</table>

9.  Klicken Sie auf das Häkchensymbol in der rechten unteren Ecke, um den Cluster zu erstellen. Wenn der Bereitstellungsprozess abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

Ausführen eines WordCount-MapReduce-Jobs
----------------------------------------

Sie haben jetzt einen HDInsight-Cluster bereitgestellt. Der nächste Schritt ist es, einen MapReduce-Job auszuführen, um Wörter in einer Textdatei zu zählen.

Für die Ausführung eines MapReduce-Jobs sind folgende Elemente erforderlich:

-   Ein MapReduce-Programm. In diesem Lernprogramm werden Sie das WordCount-Beispiel verwenden, das in der HDInsight-Clusterbereitstellung enthalten ist, sodass Sie kein eigenes schreiben müssen. Es befindet sich in */example/jars/hadoop-mapreduce-examples.jar*. Anweisungen zum Schreiben eines eigenen MapReduce-Jobs finden Sie unter [Entwickeln von Java MapReduce-Programmen für HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).

-   Eine Eingabedatei. Sie verwenden */example/data/gutenberg/davinci.txt* als Eingabedatei. Informationen zum Hochladen von Dateien finden Sie unter [Hochladen von Daten zu HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).
-   Einen Ordner für die Ausgabedatei. Sie verwenden */example/data/WordCountOutput* als Ordner für die Ausgabedatei. Das System erstellt den Ordner, falls dieser nicht vorhanden ist.

Das URI-Schema für den Dateizugriff im Blob-Speicher ist:

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] Standardmäßig hat der für das Standarddateisystem verwendete Blob-Container denselben Namen wie der HDInsight-Cluster.

Das URI-Schema bietet sowohl unverschlüsselten Zugriff mit dem Präfix *wasb:* als auch SSL-verschlüsselten Zugriff mit WASBS. Wir empfehlen die Verwendung von Wasbs, selbst für den Zugriff auf Daten, die sich im selben Azure-Rechenzentrum befinden.

Da HDInsight einen Blob-Speichercontainer als Standarddateisystem verwendet, können Sie auf Dateien und Verzeichnisse innerhalb des Standarddateisystems über relative oder absolute Pfade verweisen.

Um zum Beispiel auf hadoop-mapreduce-examples.jar zuzugreifen, können Sie eine der folgenden Optionen verwenden:

	● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	● wasb:///example/jars/hadoop-mapreduce-examples.jar
	● /example/jars/hadoop-mapreduce-examples.jar

Die Verwendung des Präfix *wasb://* in den Pfaden dieser Datei. Dies ist erforderlich, um anzuzeigen, dass Azure Blob-Speicher für Eingabe- und Ausgabedateien verwendet wird. Das Ausgabeverzeichnis geht von einem relativen Standardpfad zum Ordner *wasb:///user/&lt;username\>* aus.

Weitere Informationen finden Sie unter [Use Azure Blob Storage with HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache).

**Ausführen des WordCount-Beispiels**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Führen Sie die folgenden Befehle aus, um die Variablen festzulegen:

         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  Führen Sie die folgenden Befehle aus, um eine MapReduce-Jobdefinition zu erstellen:

         # MapReduce-Job definieren
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    Die Datei "hadoop-mapreduce-examples.jar" ist in der HDInsight-Clusterbereitstellung enthalten. Es gibt zwei Argumente für den MapReduce-Job. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Die Quelldatei ist in der HDInsight-Clusterbereitstellung enthalten, und der Pfad der Ausgabedatei wird während der Laufzeit erstellt.

4.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu übermitteln:

         # Job übermitteln
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten.

    *Start-AzureHDInsightJob* ist ein asynchronisierter Aufruf. Verwenden Sie das Cmdlet *Wait-AzureHDInsightJob*, um zu überprüfen, ob der Job abgeschlossen ist.

5.  Führen Sie den folgenden Befehl aus, um den Abschluss des MapReduce-Jobs zu überprüfen:

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Jobs aufgetreten sind:

         # Jobausgabe abrufen
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

    Der folgende Screenshot zeigt die Ausgabe bei erfolgreicher Ausführung. Andernfalls werden Fehlermeldungen angezeigt.

    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started-3.0/HDI.GettingStarted.RunMRJob.png)

**So rufen Sie die Ergebnisse des MapReduce-Jobs ab**

1.  Öffnen Sie **Azure PowerShell**.
2.  Führen Sie folgende Befehle aus, um einen Ordner C:\\Tutorials zu erstellen, und ändern Sie das Verzeichnis zum Ordner:

         mkdir \Tutorials
         cd \Tutorials

    Das Standardverzeichnis für Azure PowerShell ist *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. Sie haben standardmäßig keinen Schreibzugriff für diesen Ordner. Sie müssen das Verzeichnis in einen Ordner ändern, für den Sie Schreibzugriff haben.

3.  Legen Sie die drei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"   
         $containerName = "<ContainerName>"              

    Das Azure-Speicherkonto ist das Konto, das Sie zuvor im Lernprogramm erstellt haben. Das Speicherkonto wird verwendet, um den Blob-Container zu hosten, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Blob-Speichercontainer trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

4.  Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:

         # Speicherkonto-Kontextobjekt erstellen
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* wird verwendet, um das aktuelle Abonnement festzulegen, falls sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll.

5.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe aus dem Blob-Container auf die Arbeitsstation herunterzuladen:

         # Jobausgabe auf die Arbeitsstation herunterladen
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Der Ordner *example/data/WordCountOutput* ist der angegebene Ausgabeordner für die Ausführung des MapReduce-Jobs. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird in dieselbe Ordnerstruktur auf dem lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C. Die Datei wird in den Ordner *C:\\example\\data\\WordCountOutput\\* heruntergeladen.

6.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabedatei auszudrucken:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started-3.0/HDI.GettingStarted.MRJobOutput.png)

    Der MapReduce-Job produziert eine Datei namens *part-r-00000*, die die Wörter und deren Anzahl enthält. Das Skript verwendet den Befehl "findstr", um alle Wörter aufzulisten, die *there* enthalten.

> [WACOM.NOTE] Wenn Sie *./example/data/WordCountOutput/part-r-00000*, eine mehrzeilige Ausgabe eines MapReduce-Jobs, im Editor öffnen, werden Sie feststellen, dass die Zeilenumbrüche nicht korrekt sind. Dies entspricht dem erwarteten Verhalten.

Verbinden mit Microsoft Business Intelligence-Tools
---------------------------------------------------

Das Power Query-Add-in für Excel kann verwendet werden, um die Ausgabe von HDInsight in Excel zu exportieren, wo Microsoft Business Intelligence-Tools (BI) verwendet werden können, um die Ergebnisse weiter zu bearbeiten oder anzuzeigen. Wenn Sie einen HDInsight-Cluster erstellt haben, wurde ein Standardcontainer mit demselben Namen wie der Cluster im Speicherkonto erstellt, das bei dessen Erstellung damit verbunden wurde. Dieser Container wird automatisch mit einem Dateiensatz aufgefüllt. Eine dieser Dateien ist eine Beispiel-Hive-Tabelle. In diesem Abschnitt wird gezeigt, wie die in dieser Tabelle enthaltenen Daten in Excel importiert werden, um sie anzuzeigen und weiter zu bearbeiten.

Sie benötigen Excel 2010 oder 2013, um diesen Teil des Lernprogramms durchführen zu können. Es wird nun gezeigt, wie die Standard-Hive-Tabelle aus HDInsight importiert wird.

**Herunterladen von Microsoft PowerQuery für Excel**

-   Laden Sie Microsoft Power Query für Excel aus dem [Microsoft Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=39379) herunter, und installieren Sie es.

**Importieren von HDInsight-Daten**

1.  Öffnen Sie Excel, und erstellen Sie eine neue leere Arbeitsmappe.
2.  Klicken Sie auf das Menü **Power Query**, auf **Von anderen Quellen** und anschließend auf **Von Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  Geben Sie den **Kontonamen** des mit Ihrem Cluster verbundenen Azure Blob-Speicherkontos ein, und klicken Sie dann auf **OK**. Dies ist das Speicherkonto, das Sie zu Beginn des Lernprogramms erstellt haben.
4.  Geben Sie den **Kontoschlüssel** für das Azure Blob-Speicherkonto ein, und klicken Sie dann auf **Speichern**.
5.  Doppelklicken Sie im Navigationsbereich rechts auf den Namen des Blob-Speichercontainers. Der Container hat standardmäßig denselben Name wie der Cluster.

6.  Navigieren Sie zu **part-r-00000** in der Spalte **Name** (der Pfad ist *.../example/data/WordCountOutput*), und klicken Sie dann auf **Binär** links von **part-r-00000**.

    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Klicken Sie mit der rechten Maustaste auf **Column1.1**, und wählen Sie **Umbenennen**.
8.  Ändern Sie den Namen in **Word**.
9.  Wiederholen Sie den Vorgang, um **Column1.2** in **Count** umzubenennen.

    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Klicken Sie in der oberen linken Ecke auf **Übernehmen & Schließen**. Die Abfrage importiert die Hive-Tabelle anschließend in Excel.

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie erfahren, wie ein Cluster mit HDInsight bereitgestellt wird, ein MapReduce-Job darauf ausgeführt wird und die Ergebnisse in Excel importiert werden, wo sie mit BI-Tools weiter bearbeitet und grafisch angezeigt werden können. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit HDInsight](/en-us/documentation/articles/hdinsight-get-started/)
-   [Erste Schritte mit dem HDInsight Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)
-   [Verwenden von Azure Blob-Speicher mit HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/)
-   [Verwalten von HDInsight mit PowerShell](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
-   [Hochladen von Daten zu HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [Verwenden von Hive mit HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
-   [Verwenden von Pig mit HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
-   [Verwenden von Oozie mit HDInsight](/en-us/documentation/articles/hdinsight-use-oozie/)
-   [Entwickeln von Hadoop-Streaming-MapReduce-Programmen in C\# für HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Entwickeln von Java MapReduce-Programmen für HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

