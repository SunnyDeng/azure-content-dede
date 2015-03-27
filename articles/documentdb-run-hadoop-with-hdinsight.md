<properties 
	pageTitle="Ausführen eines Hadoop-Jobs mit DocumentDB und HDInsight | Azure" 
	description="Erfahren Sie, wie Sie einen einfachen Hive-, Pig- und MapReduce-Job mit DocumentDB und Azure HDInsight ausführen können."
	Services = "DocumentDB" 
	authors="andrewhoh" 
	manager="jhubbard" 
	editor="mimig"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/02/2015" 
	ms.author="anhoh"/>

# <a name="DocumentDB-HDInsight"></a>Ausführen eines Hadoop-Jobs mit DocumentDB und HDInsight

In diesem Lernprogramm wird gezeigt, wie Sie [Apache Hive][apache-hive]-, [Apache Pig][apache-pig]- und [Apache Hadoop][apache-hadoop]-MapReduce-Jobs in Azure HDInsight mit dem Hadoop-Connector der DocumentDB ausführen können. Der Hadoop-Connector der DocumentDB ermöglicht es dieser, sowohl als Quelle als auch als Senke für Hive-, Pig- und MapReduce-Jobs zu fungieren. In diesem Lernprogramm wird DocumentDB sowohl als Datenquelle als auch als Ziel für Hadoop-Jobs verwendet. 

Nach Abschluss dieses Lernprogramms können Sie die folgenden Fragen beantworten:

- Wie werden Daten von DocumentDB mithilfe eines Hive-, Pig- oder MapReduce-Jobs geladen?
- Wie werden Daten in DocumentDB mit einem Hive-, Pig- oder MapReduce-Job gespeichert?

Es wird empfohlen, dass Sie sich zunächst das folgende Video anschauen, in dem die Bearbeitung eines Hive-Jobs mit DocumentDB und HDInsight beschrieben wird.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Kehren Sie anschließend zu diesem Artikel zurück. Hier erhalten Sie umfassende Informationen darüber, wie Sie Analysejobs für die DocumentDB-Daten ausführen können.

> [AZURE.TIP] In diesem Lernprogramm wird davon ausgegangen, dass Sie bereits über Erfahrungen im Umgang mit Apache Hadoop, Hive, und/oder Pig verfügen. Wenn Sie noch nicht mit Apache Hadoop, Hive und Pig vertraut sind, ist es zu empfehlen, dass Sie die [Apache Hadoop-Dokumentation][apache-hadoop-doc] lesen. In diesem Lernprogramm wird außerdem davon ausgegangen, dass Sie bereits über Erfahrungen mit DocumentDB und über ein DocumentDB-Konto verfügen. Wenn Sie noch nicht mit DocumentDB vertraut sind oder noch nicht über ein DocumentDB-Konto verfügen, erhalten Sie weitergehende Informationen auf der Seite [Erste Schritte][getting-started].

Sie haben nicht die Zeit, das gesamte Lernprogramm zu bearbeiten und möchten lediglich sämtliche PowerShell-Beispielskripts zu Hive, Pig und MapReduce abrufen? Kein Problem! Sie können diese [hier][documentdb-hdinsight-samples] abrufen. Der Download umfasst außerdem die Hql-, Pig- und Java-Dateien zu diesen Beispielen.

## <a name="Prerequisites"></a>Voraussetzungen
Vor dem Ausführen der Anweisungen zu diesem Lernprogramm, müssen Sie sicherstellen, dass Sie über Folgendes verfügen:

- Ein DocumentDB-Konto, eine Datenbank und eine Auflistung mit Dokumenten. Weitere Informationen finden Sie unter [Erste Schritte mit DocumentDB][getting-started].
- Durchsatz. Lese- und Schreibvorgänge aus HDInsight werden gegen die zugeteilten Anforderungseinheiten der Kapazitätseinheit (CU, Capacity Unit) aufgerechnet. Weitere Informationen finden Sie unter [vorgesehener Durchsatz, Anforderungseinheiten und Datenbankvorgänge][documentdb-manage-throughput].
- Kapazität für eine zusätzliche gespeicherte Prozedur innerhalb einer jeden Ausgabeauflistung. Die gespeicherten Prozeduren werden für die Übertragung von resultierenden Dokumenten verwendet. Weitere Informationen finden Sie unter [Auflistungen und vorgesehener Durchsatz][documentdb-manage-document-storage].
- Kapazität für die resultierenden Dokumente aus den Hive-, Pig- oder MapReduce-Jobs. Weitere Informationen finden Sie unter [Verwalten der DocumentDB-Kapazität und -Performance][documentdb-manage-collections].
- [*Optional*] Kapazität für eine zusätzliche Auflistung. Weitere Informationen finden Sie unter [vorgesehener Dokumentspeicher und Index-Overhead][documentdb-manage-document-storage].
	
> [AZURE.WARNING] Um die Erstellung einer neuen Auflistung während eines der Jobs zu vermeiden, können Sie entweder die Ergebnisse über "stdout" drucken, die Ausgabe im WASB-Container speichern oder eine bereits vorhandene Auflistung angeben. Bei Angabe einer vorhandenen Auflistung werden neue Dokumente innerhalb der Auflistung erstellt. Bereits vorhandene Dokumente sind nur dann betroffen, wenn ein Konflikt in *ids* vorliegt. **Der Connector überschreibt vorhandene Dokumente automatisch mit ID-Konflikten**. Sie können dieses Feature deaktivieren, indem Sie die Upsert-Option auf "false" (falsch) festlegen. Wenn Upsert falsch ist und ein Konflikt auftritt, schlägt der Hadoop-Job fehl. Daraufhin wird ein Bericht über den ID-Konfliktfehler gesendet.

## <a name="CreateStorage"></a>Schritt 1: Erstellen eines Azure-Speicherkontos

> [AZURE.IMPORTANT] Wenn Sie **bereits** über ein Azure-Speicherkonto verfügen und Sie einen neuen Blob-Container innerhalb dieses Kontos erstellen möchten, können Sie fortfahren mit [Schritt 2: Erstellen eines benutzerdefinierten HDInsight-Clusters](#ProvisionHDInsight).

Azure HDInsight verwendet den Azure Blob-Speicher zum Speichern von Daten. Dieser wird als *WASB* oder *Azure-Speicher-Blob* bezeichnet. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden des Azure Blob-Speichers mit HDInsight][hdinsight-storage].

Bei der Bereitstellung eines HDInsight-Clusters geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blob-Speichercontainer aus diesem Konto wird als Standarddateisystem festgelegt, genau wie in HDFS. Der HDInsight-Cluster wird standardmäßig in demselben Rechenzentrum bereitgestellt wie das angegebene Speicherkonto.

**So erstellen Sie ein Azure-Speicherkonto**

1. Melden Sie sich am [Azure-Verwaltungsportal][azure-management-portal] an.
	
	> [AZURE.NOTE] Azure HDInsight wird derzeit im Azure-Verwaltungsportal unterstützt, während Azure DocumentDB lediglich im Azure-Vorschauportal vorhanden ist.

2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATENDIENSTE**, auf **SPEICHER**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.
	![Azure portal where you can use Quick Create to set up a new storage account.][image-storageaccount-quickcreate]

3. Geben Sie die **URL** ein, wählen Sie die **SPEICHERORT**- und **REPLIKATION**-Werte und klicken Sie anschließend auf **SPEICHERKONTO ERSTELLEN**. Affinitätsgruppen werden nicht unterstützt. 
	
	Sie werden das neue Speicherkonto in der Speicherliste sehen.

	> [AZURE.IMPORTANT] Um eine optimale Leistung zu erzielen, müssen Sie sicherstellen, dass sich Ihr Speicherkonto, der HDInsight-Cluster und das DocumentDB-Konto in derselben Azure-Region befinden. Die folgenden Azure-Regionen unterstützen alle drei Dienste:  **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **östliche USA** und **westliche USA**.

4. Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.

## <a name="ProvisionHDInsight"></a>Schritt 2: Erstellen eines benutzerdefinierten HDInsight-Clusters
Dieses Lernprogramm verwendet Skriptaktionen aus dem Azure-Verwaltungsportal zum Anpassen des HDInsight-Clusters. In diesem Lernprogramm wird das Azure-Verwaltungsportal zum Erstellen des angepassten Clusters verwendet. Anweisungen bezüglich der Verwendung von PowerShell-Cmdlets oder von HDInsight .NET SDK finden Sie im Artikel [Anpassen der HDInsight-Cluster mit Skriptaktionen][hdinsight-custom-provision] article.

1. Melden Sie sich am [Azure-Verwaltungsportal][azure-management-portal] an. Sie sind möglicherweise bereits seit dem vorherigen Schritt angemeldet.

2. Klicken Sie im unteren Seitenbereich auf **+ NEU**, und dann auf **DATENDIENSTE** und auf **HDINSIGHT**, und wählen Sie zuletzt **BENUTZERDEFINIERT ERSTELLEN**.

3. Auf der Seite mit den **Cluster-Details** können Sie die folgenden Werte auswählen bzw. eingeben:

	![Provide Hadoop HDInsight initial cluster details][image-customprovision-page1]

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Clustername</td><td>Der Name des Clusters.<br/>
			Der DNS-Name muss mit einem alphanumerischen Zeichen beginnen und enden. Er darf zudem Bindestriche enthalten.<br/>
			Das Feld muss eine Zeichenfolge darstellen und zwischen 3 und 63 Zeichen aufweisen.</td></tr>
		<tr><td>Abonnementname</td>
			<td>Wenn Sie über mehr als ein Azure-Abonnement verfügen, wählen Sie das Abonnement, das dem Speicherkonto aus <strong>Schritt 1</strong> entspricht. </td></tr>
		<tr><td>Clustertyp</td>
			<td>Wählen Sie <strong>Hadoop</strong> als Clustertyp.</td></tr>
		<tr><td>Betriebssystem</td>
			<td>Wählen Sie <strong>Windows Server 2012 R2 Datacenter</strong> als Betriebssystem.</td></tr>
		<tr><td>HDInsight-Version</td>
			<td>Wählen Sie die Version aus. </br>Wählen Sie <Strong>HDInsight Version 3.1</strong>.</td></tr>
		</table>

	<p>Geben Sie die Werte wie in der Tabelle gezeigt ein und klicken Sie auf den Pfeil nach rechts.</p>

4. Auf der Seite **Cluster konfigurieren** können Sie die folgenden Werte auswählen bzw. eingeben:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Datenknoten</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. </br>Beachten Sie, dass die HDInsight-Datenknoten sowohl mit Leistungen als auch mit Preisen verknüpft sind.</td></tr>
	<tr><td>Region/virtuelles Netzwerk</td><td>Wählen Sie dieselbe Region wie die des neu erstellten <strong>Speicherkontos</strong> und des <strong>DocumentDB-Kontos</strong>. </br> HDInsight verlangt, dass sich das Speicherkonto in derselben Region befindet. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet.</td></tr>
	</table>
	
    Klicken Sie auf den Pfeil nach rechts.

5. Auf der Seite **Clusterbenutzer konfigurieren** können Sie die folgenden Werte auswählen bzw. eingeben:

    <table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Benutzername</td>
			<td>Geben Sie den Benutzernamen für den HDInsight-Cluster an.</td></tr>
		<tr><td>Kennwort/Kennwort bestätigen</td>
			<td>Geben Sie das Kennwort für den HDInsight-Cluster an.</td></tr>
	</table>
	
    Klicken Sie auf den Pfeil nach rechts.
    
6. Geben Sie auf der Seite **Speicherkonto** die folgenden Werte ein:

	![Provide storage account for Hadoop HDInsight cluster][image-customprovision-page4]

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Speicherkonto</td>
			<td>Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten: Verwenden Sie den existierenden Speicher, erstellen Sie einen neuen Speicher oder verwenden Sie einen Speicher aus einem anderem Abonnement.</br></br>
			Wählen Sie <strong>Existierenden Speicher verwenden</strong>.
			</td>
			</td></tr>
		<tr><td>Kontoname</td>
			<td>
			Wählen Sie als <strong>Kontoname</strong> das im <strong>Schritt 1</strong> erstellte Konto. Das Dropdown-Menü listet unter demselben Azure-Abonnement ausschließlich jene Speicherkonten auf, die sich in dem Rechenzentrum befinden, in dem Sie die Auswahl zur Bereitstellung des entsprechenden Clusters treffen.
			</td></tr>
		<tr><td>Standardcontainer</td>
			<td>Gibt den Standardcontainer im Speicherkonto an, der als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Wenn Sie <strong>Existierenden Speicher verwenden</strong> für das Feld <strong>Speicherkonto</strong> wählen und in dem betreffenden Konto keine Container vorhanden sind, wird der Container standardgemäß mit demselben Namen wie der des Clusters erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt.
        </td></tr>
		<tr><td>Zusätzliche Speicherkonten</td>
			<td>HDInsight unterstützt mehrere Speicherkonten. Es gibt keine Beschränkung in Bezug auf die zusätzlichen Speicherkonten, die von einem Cluster verwendet werden können. Wenn Sie jedoch einen Cluster mit dem Verwaltungsportal erstellen, gibt es aufgrund der Benutzeroberflächeneinschränkungen eine Beschränkung auf eine Anzahl von sieben. Für jedes angegebene Speicherkonto wird eine zusätzliche Seite im Assistenten hinzugefügt, in der Sie die Kontoinformationen angeben können.</td></tr>
	</table>

	Klicken Sie auf den Pfeil nach rechts.

7. Klicken Sie auf der Seite **Skriptaktionen** auf **Skriptaktion hinzufügen**, um Informationen über das PowerShell-Skript bereitzustellen, welches Sie zum Anpassen des Clusters ausführen, wenn dieser erstellt wird. Über das PowerShell-Skript wird der DocumentDB-Hadoop-Connector auf den HDInsight-Clustern während der Erstellung des entsprechenden Clusters installiert.
	
	![Configure Script Action to customize an HDInsight cluster][image-customprovision-page5]

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Name</td>
			<td>Geben Sie einen Namen für die Skriptaktion an.</td></tr>
		<tr><td>Skript-URI</td>
			<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen.</br></br>
			Geben Sie Folgendes ein: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v01.ps1</strong>.</td></tr>
		<tr><td>Knotentyp</td>
			<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.</br></br>
			Wählen Sie <strong>Alle Knoten</strong>.</td></tr>
		<tr><td>Parameter</td>
			<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</br></br>
			<strong>Keine Parameter erforderlich</strong>.</td></tr>
	</table>

	Klicken Sie auf das Häkchen, um die Erstellung des Clusters abzuschließen.

## <a name="InstallCmdlets"></a>Schritt 3: Installieren und Konfigurieren von Azure PowerShell

1. Installieren Sie Azure PowerShell. Anweisungen finden Sie [hier][powershell-install-configure].

	> [AZURE.NOTE] Alternativ können Sie den Hive-Online-Editor von HDInsight ausschließlich für Hive-Abfragen verwenden. Melden Sie sich zu diesem Zweck am [Azure-Verwaltungsportal][azure-management-portal] an. Klicken Sie auf **HDInsight** im linken Bildschirmbereich, um eine Liste der HDInsight-Cluster anzusehen. Klicken Sie auf den Cluster, auf welchem Hive-Abfragen ausgeführt werden sollen. Klicken Sie anschließend auf **Query Console** (Abfragekonsole).

2. Öffnen Sie die integrierte Azure PowerShell-Skripting-Umgebung:
	- Sie können die integrierte Suche auf einem Computer verwenden, auf dem Windows 8 oder Windows Server 2012 oder höher ausgeführt wird. Geben Sie ausgehend vom Startbildschirm **Powershell Ise** ein. Drücken Sie anschließend die **Eingabetaste**. 
	- Verwenden Sie auf einem Computer, auf dem eine niedrigere Version als Windows 8 oder Windows Server 2012 ausgeführt wird, das Startmenü. Geben Sie ausgehend vom Startmenü **Eingabeaufforderung** in das Suchfeld ein. Klicken Sie anschließend in der Ergebnisliste auf **Eingabeaufforderung**. Geben Sie in der Eingabeaufforderung **powershell_ise** ein. Drücken Sie dann die **Eingabetaste**.

3. Fügen Sie Ihr Azure-Konto hinzu.
	1. Geben Sie im Konsolenbereich **Add-AzureAccount** (Azure-Konto hinzufügen) ein. Drücken Sie anschließend die **Eingabetaste**. 
	2. Geben Sie die mit Ihrem Azure-Abonnement verknüpfte E-Mail-Adresse ein. Klicken Sie auf **Weiter**. 
	3. Geben Sie das Kennwort für Ihr Azure-Abonnement ein. 
	4. Klicken Sie auf **Anmelden**.

4. Das folgende Diagramm kennzeichnet die wichtigen Bestandteile Ihrer Azure PowerShell-Skripting-Umgebung. 

	![Diagram for Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Schritt 4: Ausführen eines Hive-Jobs mit DocumentDB und HDInsight

> [AZURE.IMPORTANT] Alle durch < > gekennzeichneten Variablen müssen mithilfe der entsprechenden Konfigurationseinstellungen eingetragen werden.

1. Legen Sie die folgenden Variablen im PowerShell-Skript-Bereich fest.

		# Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide the HDInsight cluster name where you want to run the Hive job.
		$clusterName = "<HDInsightClusterName>"

2. 
	<p>Beginnen Sie mit dem Erstellen der Abfragezeichenfolge. Eine Hive-Abfrage wird geschrieben, welche die systemgenerierten Zeitstempel (_ts) aller Dokumente und die eindeutige ids (_rid) aus einer DocumentDB-Auflistung aufnimmt, alle Dokumente minutengenau aufeinander abstimmt und die Ergebnisse dann in einer neuen DocumentDB-Auflistung abspeichert. </p>

    <p>Zunächst soll eine Hive-Tabelle aus der entsprechenden DocumentDB-Auflistung erstellt werden. Fügen Sie den folgenden Codeausschnitt zum PowerShell-Skript-Bereich <strong>nach</strong> dem Codeausschnitt von Nr. 1 hinzu. Sie müssen den optionalen DocumentDB.Abfrage-Parameter einbinden, um die entsprechenden Dokumente gemäß _ts und _rid zuzuschneiden. </p>

    > [AZURE.NOTE] **Bei der Benennung von DocumentDB.inputCollections handelt es sich nicht um einen Fehler.** Ja, das Hinzufügen mehrerer Auflistungen ist als Eingabe zulässig: </br>
    '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' </br> Die Auflistungsnamen werden ohne Leerzeichen von einander abgegrenzt. Es wird lediglich ein einzelnes Komma verwendet.

		# Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
		$queryStringPart1 = "drop table DocumentDB_timestamps; "  + 
                            "create external table DocumentDB_timestamps(id string, ts INT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " + 
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
 
3.  Als Nächstes wird eine Hive-Tabelle für die Ausgabeauflistung erstellt. Bei den Eigenschaften des Ausgabedokuments handelt es sich um Monat, Tag, Stunde, Minute und die Gesamtanzahl an Vorkommen.

	> [AZURE.NOTE] **Auch hier handelt es sich bei der Benennung von DocumentDB.outputCollections nicht um einen Fehler.** Ja, das Hinzufügen mehrerer Auflistungen ist als Ausgabe zulässig: </br>
    '*DocumentDB.outputCollections*' = '*\<DocumentDB Output Collection Name 1\>*,*\<DocumentDB Output Collection Name 2\>*' </br> Die Auflistungsnamen werden ohne Leerzeichen von einander abgegrenzt. Es wird lediglich ein einzelnes Komma verwendet. </br></br>
    Die Dokumente werden in Umlauf gebracht und über mehrere Auflistungen hinweg verteilt. Ein Batch von Dokumenten wird in einer Auflistung gespeichert. Ein zweiter Batch von Dokumenten wird dann in der nächsten Auflistung gespeichert usw.

		# Create a Hive table for the output data to DocumentDB.
	    $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " + 
                              "tblproperties ( " + 
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Zum Schluss werden die Dokumente nach Monat, Tag, Stunde und Minute berechnet. Die Ergebnisse werden zurück in die Hive-Ausgabetabelle eingefügt.

		# GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Fügen Sie den folgenden Skriptausschnitt hinzu, um eine Hive-Jobdefinition aus der vorherigen Abfrage zu erstellen.

		# Create a Hive job definition.
		$queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	Sie können auch den Schalter "-File" verwenden, um eine HiveQL-Skriptdatei auf HDFS anzugeben.

6. Fügen Sie den folgenden Ausschnitt hinzu, um die Startzeit zu speichern und den Hive-Job zu übermitteln.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Fügen Sie Folgendes hinzu, um auf das Abschließen des Hive-Jobs zu warten.

		# Wait for the Hive job to complete.
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Fügen Sie Folgendes hinzu, um die Standardausgabe sowie die Start- und Endzeiten auszudrucken.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Führen** Sie das neue Skript aus! **Klicken** Sie zum Ausführen auf die grüne Schaltfläche.

10. Überprüfen Sie die Ergebnisse. Melden Sie sich am [Azure-Vorschauportal][azure-preview-portal] an. 
	1. Klicken Sie im linken Bereich auf <strong>Durchsuchen</strong>. </br>
	2. Klicken Sie oben rechts im Suchbereich auf <strong>Alles</strong>. </br>
	3. Suchen Sie nach und klicken Sie auf <strong>DocumentDB-Konten</strong>. </br>
	4. Suchen Sie als Nächstes das <strong>DocumentDB-Konto</strong>, anschließend die <strong>DocumentDB-Datenbank</strong> und die <strong>DocumentDB-Auflistung</strong>, die mit der in der Hive-Abfrage angegebenen Ausgabeauflistung verknüpft ist.</br>
	5. Klicken Sie abschließend auf <strong>Document Explorer</strong> unter <strong>Entwicklertools</strong>.</br></p>

	Sie sehen die Ergebnisse der Hive-Abfrage.

	![Hive query results][image-hive-query-results]

## <a name="RunPig"></a>Schritt 5: Ausführen eines Pig-Jobs mit DocumentDB und HDInsight

> [AZURE.IMPORTANT] Alle durch < > gekennzeichneten Variablen müssen mithilfe der entsprechenden Konfigurationseinstellungen eingetragen werden.

1. Legen Sie die folgenden Variablen im PowerShell-Skript-Bereich fest.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Beginnen Sie mit dem Erstellen der Abfragezeichenfolge. Eine Pig-Abfrage wird geschrieben, welche die systemgenerierten Zeitstempel (_ts) aller Dokumente und die eindeutige ids (_rid) aus einer DocumentDB-Auflistung aufnimmt, alle Dokumente minutengenau aufeinander abstimmt und die Ergebnisse dann in einer neuen DocumentDB-Auflistung abspeichert.</p>
    <p>Laden Sie zunächst die Dokumente aus DocumentDB in HDInsight. Fügen Sie den folgenden Codeausschnitt zum PowerShell-Skript-Bereich <strong>nach</strong> dem Codeausschnitt von Nr. 1 hinzu. Achten Sie darauf, dass eine DocumentDB-Abfrage zum optionalen DocumentDB-Abfrageparameter hinzugefügt wird, um die Dokumente gemäß _ts und _rid zu verkürzen.</p>

    > [AZURE.NOTE] Ja, das Hinzufügen mehrerer Auflistungen ist als Eingabe zulässig: </br>
    '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*'</br> Die Auflistungsnamen werden ohne Leerzeichen von einander abgegrenzt. Es wird lediglich ein einzelnes Komma verwendet. </b>

	Die Dokumente werden in Umlauf gebracht und über mehrere Auflistungen hinweg verteilt. Ein Batch von Dokumenten wird in einer Auflistung gespeichert. Ein zweiter Batch von Dokumenten wird dann in der nächsten Auflistung gespeichert usw.

		# Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Als Nächstes werden die Dokumente nach Monat, Tag, Stunde, Minute und Gesamtzahl der Vorkommen zusammengerechnet.

		# GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Schließlich werden die Ergebnisse in der neuen Ausgabeauflistung abgespeichert.

    > [AZURE.NOTE] Ja, das Hinzufügen mehrerer Auflistungen ist als Ausgabe zulässig: </br>
    '\<DocumentDB Output Collection Name 1\>,\<DocumentDB Output Collection Name 2\>'</br> Die Auflistungsnamen werden ohne Leerzeichen von einander abgegrenzt. Es wird lediglich ein einzelnes Komma verwendet.</br>
    Die Dokumente werden in Umlauf gebracht und über die mehrfachen Auflistungen hinweg verteilt. Ein Batch von Dokumenten wird in einer Auflistung gespeichert. Ein zweiter Batch von Dokumenten wird dann in der nächsten Auflistung gespeichert usw.

		# Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Fügen Sie den folgenden Skriptausschnitt hinzu, um eine Pig-Jobdefinition aus der vorherigen Abfrage zu erstellen.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

	Mit dem Parameter -File können Sie eine Pig-Skriptdatei unter HDFS angeben.

6. Fügen Sie den folgenden Ausschnitt hinzu, um die Startzeit zu speichern und den Pig-Job zu übermitteln.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Fügen Sie Folgendes hinzu, um auf das Abschließen des Pig-Jobs zu warten.

		# Wait for the Pig job to complete.
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Fügen Sie Folgendes hinzu, um die Standardausgabe sowie die Start- und Endzeiten auszudrucken.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
		
9. **Führen** Sie das neue Skript aus! **Klicken** Sie zum Ausführen auf die grüne Schaltfläche.

10. Überprüfen Sie die Ergebnisse. Melden Sie sich am [Azure-Vorschauportal][azure-preview-portal] an. 
	1. Klicken Sie im linken Bereich auf <strong>Durchsuchen</strong>. </br>
	2. Klicken Sie oben rechts im Suchbereich auf <strong>Alles</strong>. </br>
	3. Suchen Sie nach und klicken Sie auf <strong>DocumentDB-Konten</strong>. </br>
	4. Suchen Sie als Nächstes das <strong>DocumentDB-Konto</strong>, anschließend die <strong>DocumentDB-Datenbank</strong> und die <strong>DocumentDB-Auflistung</strong>, die mit der in der Pig-Abfrage angegebenen Ausgabeauflistung verknüpft ist.</br>
	5. Klicken Sie abschließend auf <strong>Document Explorer</strong> unter <strong>Entwicklertools</strong>.</br></p>

	Sie sehen die Ergebnisse der Pig-Abfrage.

	![Pig query results][image-pig-query-results]

## <a name="RunMapReduce"></a>Schritt 6: Ausführen eines MapReduce-Jobs mit DocumentDB und HDInsight

1. Legen Sie die folgenden Variablen im PowerShell-Skript-Bereich fest.
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
2. Ein MapReduce-Job soll ausgeführt werden, der die Anzahl der Vorkommen für jede Dokumenteigenschaft aus der DocumentDB-Auflistung zusammenrechnet. Fügen Sie diesen Skriptausschnitt **nach** dem oberen Ausschnitt hinzu.

		# Define the MapReduce job.
		$TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

	> [AZURE.NOTE] TallyProperties-v01.jar geht mit der benutzerdefinierten Installation des DocumentDB-Hadoop-Connectors einher.

3. Fügen Sie den folgenden Befehl hinzu, um den MapReduce-Job zu übermitteln.

		# Save the start time and submit the job.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten, sowie die Anmeldeinformationen. "Start-AzureHDInsightJob" ist ein asynchronisierter Aufruf. Um den Abschluss des Jobs zu überprüfen, verwenden Sie das *Wait-AzureHDInsightJob*-Cmdlet.

4. Fügen Sie den folgenden Befehl hinzu, um bei der Ausführung des MapReduce-Jobs auf etwaige Fehler hin zu prüfen.	
	
		# Get the job output and print the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green 

5. **Führen** Sie das neue Skript aus! **Klicken** Sie zum Ausführen auf die grüne Schaltfläche.

6. Überprüfen Sie die Ergebnisse. Melden Sie sich am [Azure-Vorschauportal][azure-preview-portal] an. 
	1. Klicken Sie im linken Bereich auf <strong>Durchsuchen</strong>.
	2. Klicken Sie oben rechts im Suchbereich auf <strong>Alles</strong>.
	3. Suchen Sie nach und klicken Sie auf <strong>DocumentDB-Konten</strong>.
	4. Suchen Sie als Nächstes das <strong>DocumentDB-Konto</strong>, anschließend die <strong>DocumentDB-Datenbank</strong> und die <strong>DocumentDB-Auflistung</strong>, die mit der im MapReduce-Job angegebenen Ausgabeauflistung verknüpft ist.
	5. Klicken Sie abschließend auf <strong>Document Explorer</strong> unter <strong>Entwicklertools</strong>.

	Sie sehen die Ergebnisse des MapReduce-Jobs.

	![MapReduce query results][image-mapreduce-query-results]

## <a name="NextSteps"></a>Nächste Schritte

Glückwunsch! Sie haben soeben mithilfe von Azure-DocumentDB und HDInsight Ihre ersten Hive-, Pig- und MapReduce-Jobs ausgeführt. 

Der Hadoop-Connector steht im Rahmen von "Open Source" zur Verfügung. Wenn Sie möchten, können Sie unter [GitHub][documentdb-github] einen Beitrag leisten.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Entwickeln einer Java-Anwendung mit Documentdb][documentdb-java-application]
- [Entwickeln von Java-MapReduce-Programmen für Hadoop in HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Erste Schritte bei der Verwendung von Hadoop mit Hive in HDInsight zum Analysieren der Nutzung von Mobiltelefonen][hdinsight-get-started]
- [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce]
- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
- [Anpassen von HDInsight-Clustern unter Verwendung einer Skriptaktion][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: ../documentdb-get-started/

[azure-management-portal]: https://manage.windowsazure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png
[azure-preview-portal]: https://portal.azure.com/

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: ../documentdb-java-application/
[documentdb-manage-collections]: ../documentdb-manage/#Collections
[documentdb-manage-document-storage]: ../documentdb-manage/#IndexOverhead
[documentdb-manage-throughput]: ../documentdb-manage/#ProvThroughput

[hdinsight-custom-provision]: ../hdinsight-provision-clusters/#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-hadoop-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/ 
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-customprovision-page4]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page4.png
[image-customprovision-page5]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page5.png
[image-storageaccount-quickcreate]: ./media/documentdb-run-hadoop-with-hdinsight/storagequickcreate.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../install-configure-powershell/

<!--HONumber=47-->
