<properties urlDisplayName="Get Started" pageTitle="Erstellen und Abfragen von HBase-Tabellen mit Hive in HDInsight | Azure" metaKeywords="" description="Erste Schritte mit HBase mit Hadoop in HDInsight. Erfahren Sie, wie Sie HBase Tabellen erstellen und sie mithilfe von Hive abfragen." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# Einrichten von HBase-Clustern und deren Abfrage mit Hive für Hadoop in HDInsight

Erfahren Sie, wie Sie HBase-Tabellen mit Hive für Hadoop in HDInsight erstellen und abfragen. 

##Themen in diesem Artikel

* [Was ist HBase?](#hbaseintro)
* [Voraussetzungen](#prerequisites)
* [Bereitstellen von HBase-Clustern mithilfe des Azure-Verwaltungsportals](#create-hbase-cluster)
* [Verwalten von HBase-Tabellen mit der HBase-Shell](#create-sample-table)
* [Abfragen von HBase-Tabellen mit HiveQL](#hive-query)
* [Verwalten von HBase-Tabellen mit der Microsoft-HBase-REST-Clientbibliothek](#hbase-powershell)
* [Siehe auch](#seealso)

##<a name="hbaseintro"></a>Was ist HBase?

HBase ist eine NoSQL-Datenbank mit niedriger Latenz und ermöglicht die Onlinetransaktionsverarbeitung großer Datenmengen. HBase ist als verwalteter Cluster integriert in die Azure-Umgebung verfügbar. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blob-Speicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden. Weitere Informationen zu HBase und den Szenarien, für die es verwendet werden kann, finden Sie in der [Übersicht zu HDInsight HBase](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-hbase-overview/).

> [WACOM.NOTE] HBase (Version 0.98.0) ist nur für HDInsight-Cluster der Version 3.1 in HDInsight verfügbar (basiert auf Apache Hadoop und YARN 2.4.0). Versionshinweise finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?][hdinsight-versions]

##<a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement** Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
- **Ein Azure-Speicherkonto** Anweisungen finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].
- **Eine Workstation** mit installiertem Visual Studio 2013. Anweisungen finden Sie unter [Installieren von Visual Studio](http://msdn.microsoft.com/de-de/library/e2h7fzkw.aspx).
- Laden Sie die [Microsoft-HBase-REST-Clientbibliothek für .NET herunter](https://github.com/hdinsight/hbase-sdk-for-net). 


##<a name="create-hbase-cluster"></a>Bereitstellen eines HBase-Clusters im Azure-Portal

Dieser Abschnitt beschreibt die Bereitstellung eines HBase-Clusters im Azure-Portal.


[WACOM.INCLUDE [Hinweis zur Bereitstellung](../includes/hdinsight-provisioning.md)]

**So stellen Sie einen HDInsight-Cluster im Azure-Portal bereit** 


1. Melden Sie sich am [Azure-Verwaltungsportal ][azure-management-portal] an. 

2. Klicken Sie links auf **HDInsight**, um den Status der Cluster in Ihrem Konto anzuzeigen, und klicken Sie dann links unten auf das Symbol **+NEU**. 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. Klicken Sie auf das HDInsight-Symbol in der zweiten Spalte von links und anschließend auf die HBase-Optionen in der nächsten Spalte. Geben Sie die Werte für CLUSTERNAME und CLUSTERGRÖSSE den Namen des Speicherkontos und ein Kennwort für das neue HBase-Cluster ein.
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. Klicken Sie unten links auf das Häkchensymbol, um das HBase-Cluster zu erstellen.


##<a name="create-sample-table"></a>Erstellen einer HBase-Beispieltabelle in der HBase-Befehlszeile
Dieser Abschnitt beschreibt, wie Sie RDP für den Zugriff auf die HBase-Befehlszeile aktivieren und verwenden können und wie Sie eine HBase-Beispieltabelle erstellen, Zeilen einfügen und anschließend die Tabellenzeilen über die Befehlszeile auflisten können.

Es wird davon ausgegangen, dass Sie die im ersten Abschnitt beschriebene Prozedur ausgeführt haben und bereits über ein erfolgreich erstelltes HBase-Cluster verfügen.

**Aktivieren der RDP-Verbindung mit dem HBase-Cluster**

1. Um die Remotedesktopverbindung für das HDInsight-Cluster zu aktivieren, wählen Sie das soeben erstellte HBase-Cluster aus und klicken Sie auf die Remotedesktopverbindung **KONFIGURATION**. Klicken Sie auf die Schaltfläche **REMOTE AKTIVIEREN** am unteren Seitenrand, um die RDP-Verbindung zum Cluster zu aktivieren.
2. Geben Sie die Anmeldeinformationen und das Ablaufdatum im Assistenten **REMOTEDESKTOP KONFIGURIEREN** ein und klicken Sie auf das Häkchensymbol unten rechts. (Diese Operation kann einige Minuten in Anspruch nehmen.)
3. Um sich mit dem HDInsight-Cluster zu verbinden, klicken Sie auf die Schaltfläche **VERBINDEN** am unteren Rand der Registerkarte **KONFIGURATION**.

 
**Öffnen der HBase-Befehlszeile**

1. Klicken Sie innerhalb Ihrer RDP-Sitzung auf die Verknüpfung **Hadoop-Befehlszeile** auf dem Desktop.

2. Wechseln Sie in das HBase-Stammverzeichnis:
		
		cd %HBASE_HOME%\bin

3. Öffnen Sie die HBase-Befehlszeile:

		hbase shell


**Erstellen einer Beispieltabelle, Hinzufügen von Daten und Abrufen der Daten**

1. Erstellen einer Beispieltabelle:

		create 'sampletable', 'cf1'

2. Hinzufügen einer Zeile zur Beispieltabelle:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Auflisten der Zeilen in der Beispieltabelle:
	
		scan 'sampletable'

##<a name="hive-query"></a>Abfragen einer HBase-Tabelle mit Hive

Sie haben nun ein HBase-Cluster eingerichtet und eine Tabelle erstellt und können nun Abfragen mit Hive ausführen. In diesem Abschnitt erstellen Sie eine Hive-Tabelle, die zu Ihrer HBase-Tabelle zugeordnet ist und verwenden diese Tabelle, um die Daten in Ihrer HBase-Tabelle abzufragen.

**So öffnen Sie das Cluster-Dashboard**

1. Melden Sie sich am [Azure-Verwaltungsportal ][azure-management-portal] an. 
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste der erstellten Cluster, inklusive des Clusters, das Sie im vorigen Abschnitt erstellt haben.
3. Klicken Sie auf den Namen des Clusters, in dem Sie den Hive-Job ausführen möchten.
4. Klicken Sie unten in der Seite auf **CLUSTER VERWALTEN**, um das Cluster-Dashboard zu öffnen. Daraufhin wird eine Webseite in einer neuen Browser-Registerkarte geöffnet.   
5. Geben Sie den Benutzernamen und das Kennwort des Hadoop-Benutzers ein.  Der Standardbenutzername ist **admin**, und das Kennwort haben Sie bei der Einrichtung ausgewählt.  Das Dashboard sieht folgendermaßen aus:

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**So führen Sie eine Hive-Abfrage aus**

1. Um eine Hive-Tabelle mit einer Zuordnung zur HBase-Tabelle zu erstellen, geben Sie das folgende HiveQL-Skript in das Hive-Konsolenfenster ein und klicken Sie auf **SENDEN**. Stellen Sie sicher, dass Sie die hier beschriebene Beispieltabelle in HBase mit der HBase-Befehlszeile erstellt haben, bevor Sie diese Anweisung ausführen.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. Um eine Hive-Abfrage über die Daten in HBase auszuführen, geben Sie das folgende HiveQL-Skript in das Hive-Konsolenfenster ein und klicken Sie auf **SENDEN**.

     	SELECT count(*) FROM hbasesampletable;
 
4. Um die Ergebnisse der Hive-Abfrage anzuzeigen, klicken Sie auf den Link **Details anzeigen** im Fenster **Auftragssitzung**, nachdem der Job abgeschlossen wurde.


Hinweis: Mit dem Link zur HBase-Befehlszeile können Sie zur **HBase-Befehlszeile** wechseln.



**So durchsuchen Sie die Ausgabedatei**

1. Klicken Sie oben im Cluster-Dashboard auf **Dateien**.
2. Klicken Sie auf **Templeton-Job-Status**.
3. Klicken Sie auf die GUID-Nummer, deren letzte Änderung kurz nach der Startzeit des Jobs liegt, die Sie zuvor notiert haben. Notieren Sie sich die GUID.  Sie benötigen sie im nächsten Abschnitt.
4. Die **stdout**-Datei enthält die Daten, die Sie für den nächsten Abschnitt benötigen. Sie können auf **stdout** klicken, falls Sie eine Kopie der Daten herunterladen möchten.

	
##<a name="hbase-powershell"></a>Erstellen einer HBase-Tabelle und Abrufen von Daten aus der Tabelle mit der Microsoft-HBase-REST-Clientbibliothek für .NET C#-APIs

Sie müssen die Microsoft-HBase-REST-Clientbibliothek für das .NET-Projekt von GitHub herunterladen, und das Projekt muss für die Verwendung des HBase .NET SDK erstellt werden. Das folgende Verfahren bietet Anweisungen für diese Aufgabe.

1. Laden Sie die [Microsoft-HBase-REST-Clientbibliothek für .NET herunter],(https://github.com/hdinsight/hbase-sdk-for-net) sofern diese Vorbedingung noch nicht erfüllt ist.

2. Öffnen Sie Marlin.sln in Visual Studio (**Datei** -> **Öffnen** -> **Projekt/Projektmappe ...**) an dem Speicherort, an den die Datei heruntergeladen wurde. Wählen Sie **Ansicht** -> **Projektmappen-Explorer**, um die Projektmappe 'Marlin' und das dazugehörige Microsoft.HBase.Client-Projekt anzuzeigen. Erstellen Sie das **Marlin**-Projekt, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste darauf klicken und **Projektmappe erstellen** auswählen. 

4. Erstellen einer neuen Visual C#-Konsolenanwendung Suchen Sie die resultierenden erstellten Dateien Microsoft.HBase.Client.dll und protobuf.dll (im Verzeichnis ...\bin\debug\Microsoft.HBase.Client), und fügen Sie sie Ihrem C#-Projekt hinzu: Klicken Sie mit der rechten Maustaste auf **Verweise**, klicken Sie auf **Verweise hinzufügen...**, navigieren Sie zu den beiden Assemblys, und laden Sie sie hoch. [protobuf-net](http://code.google.com/p/protobuf-net/) ist eine .NET-Implementierung des binären Protokollpufferserialisierers von Google, der für die Datenkommunikation verwendet wird.

5. Fügen Sie am Anfang der Datei die folgenden "Using"-Anweisungen ein:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Erstellen Sie eine neue Instanz eines HBase-Clients unter Verwendung der Clusteranmeldeinformationen, und rufen Sie die Clusterversion ab:

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. Mit dem folgenden Code können Sie eine neue HBase-Tabelle erstellen:

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. Fügen Sie mithilfe dieses Codes Daten in eine Tabelle ein:

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. Mit dem folgenden Code können Sie eine eine Zelle samt ihrem Schlüssel abrufen. 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. Durchlaufen Sie mithilfe des folgenden Codes Zeilen in einer Tabelle:

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie ein HBase-Cluster bereitstellen, Tabellen erstellen und die Daten in diesen Tabellen in der HBase-Befehlszeile anzeigen können. Außerdem haben Sie gelernt, wie Sie die Daten in HBase-Tabellen mit Hive abfragen und mit den HBase C#-APIs HBase-Tabellen erstellen und Daten aus der Tabelle abrufen können.

##<a name="next"></a> Nächste Schritte

[Übersicht über HDInsight HBase][hdinsight-hbase-overview]:
HBase ist eine Open-Source-NoSQL-Datenbank, basierend auf Hadoop, die wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.

[Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet]:
Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können.

[Analysieren von Stimmungen in Twitter mit HBase in HDInsight][hbase-twitter-sentiment]:
Erfahren Sie, wie Sie [Stimmungsanalysen](http://en.wikipedia.org/wiki/Sentiment_analysis) von Big Data mithilfe von HBase in einem Hadoop-Cluster in HDInsight in Echtzeit ausführen.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/de-de/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35.1-->
