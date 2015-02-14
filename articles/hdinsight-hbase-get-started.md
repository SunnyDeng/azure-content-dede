<properties 
	pageTitle="Erstellen und Abfragen von HBase-Tabellen mit Hive in HDInsight | Azure" 
	description="Erste Schritte mit HBase mit Hadoop in HDInsight. Erfahren Sie, wie Sie HBase Tabellen erstellen und sie mithilfe von Hive abfragen." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



# Einrichten von HBase-Clustern und deren Abfrage mit Hive für Hadoop in HDInsight

Erfahren Sie, wie Sie HBase-Tabellen mit Hive für Hadoop in HDInsight erstellen und abfragen. 

##Themen in diesem Artikel

* [Was ist HBase?](#hbaseintro)
* [Voraussetzungen](#prerequisites)
* [Bereitstellen von HBase-Clustern mithilfe des Azure-Verwaltungsportals](#create-hbase-cluster)
* [Verwalten von HBase-Tabellen mit der HBase-Shell](#create-sample-table)
* [Abfragen von HBase-Tabellen mit HiveQL](#hive-query)
* [Verwalten von HBase-Tabellen mit der Microsoft HBase REST-Clientbibliothek](#hbase-powershell)
* [Siehe auch](#seealso)

##<a name="hbaseintro"></a>Was ist HBase?

HBase ist eine NoSQL-Datenbank mit niedriger Latenz und ermöglicht die Onlinetransaktionsverarbeitung großer Datenmengen. HBase ist als verwalteter Cluster integriert in die Azure-Umgebung verfügbar. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blob-Speicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden. Weitere Informationen zu HBase und den Szenarien, für die es verwendet werden kann, finden Sie unter [Überblick zu HDInsight HBase][hdinsight-hbase-overview].

> [AZURE.NOTE] HBase (Version 0.98.0) ist nur für HDInsight-Cluster der Version 3.1 in HDInsight verfügbar (basiert auf Apache Hadoop und YARN 2.4.0). Versionshinweise finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][hdinsight-versions].

##<a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement** Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Angebote für Mitglieder][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].
- **Ein Azure-Speicherkonto** Anweisungen finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].
- **Eine Workstation** mit installiertem Visual Studio 2013 Anweisungen finden Sie unter [Installieren von Visual Studio](http://msdn.microsoft.com/de-de/library/e2h7fzkw.aspx)

##<a name="create-hbase-cluster"></a>Bereitstellen eines HBase-Clusters im Azure-Portal

Dieser Abschnitt beschreibt die Bereitstellung eines HBase-Clusters im Azure-Verwaltungsportal.


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**So stellen Sie einen HDInsight-Cluster im Azure-Portal bereit** 


1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an. 
2. Klicken Sie unten links auf **NEU** und dann auf **DATENDIENSTE**, **HDINSIGHT** und **HBASE**. 
3. Geben Sie **CLUSTERNAME**, **CLUSTERGRÖSSE**, CLUSTER-BENUTZERKENNWORT und **SPEICHERKONTO** ein.
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. Klicken Sie unten links auf das Häkchensymbol, um das HBase-Cluster zu erstellen.


##<a name="create-sample-table"></a>Erstellen einer HBase-Beispieltabelle in der HBase-Befehlszeile
Dieser Abschnitt beschreibt, wie Sie RDP für den Zugriff auf die HBase-Befehlszeile aktivieren und verwenden können und wie Sie eine HBase-Beispieltabelle erstellen, Zeilen einfügen und anschließend die Tabellenzeilen über die Befehlszeile auflisten können.

Es wird davon ausgegangen, dass Sie die im ersten Abschnitt beschriebene Prozedur ausgeführt haben und bereits über ein erfolgreich erstelltes HBase-Cluster verfügen.

**So aktivieren Sie die RDP-Verbindung zum HBase-Cluster**

1. Klicken Sie im Verwaltungsportal links auf **HDINSIGHT**, um die Liste der bestehenden Cluster anzuzeigen.
2. Klicken Sie auf den HBase-Cluster, in dem Sie HBase-Shell öffnen möchten.
3. Klicken Sie oben auf **KONFIGURATION**.
4. Klicken Sie unten auf **REMOTE AKTIVIEREN**.
5. Geben Sie den RDP-Benutzernamen und das Kennwort ein.  Der Benutzername muss sich von dem Cluster-Benutzernamen unterscheiden, den Sie beim Bereitstellen des Clusters verwendet haben. Für **GÜLTIG BIS** kann ein Wert ausgewählt werden, der bis zu sieben Tage nach dem heutigen Datum liegt.
6. Klicken Sie auf das Häkchen unten rechts, um Remotedesktop zu aktivieren.
7. Nachdem RPD aktiviert ist, klicken Sie auf **VERBINDEN** am unteren Rand der Registerkarte **KONFIGURATION**, und folgen Sie den Anweisungen.

 
**So öffnen Sie die HBase-Shell**

1. Klicken Sie in der RDP-Sitzung auf die **Hadoop-Befehlszeile**-Verknüpfung auf dem Desktop.

2. Wechseln Sie in das HBase-Stammverzeichnis:
		
		cd %HBASE_HOME%\bin

3. Öffnen Sie die HBase-Befehlszeile:

		hbase shell


**So erstellen Sie eine Beispieltabelle, fügen Daten hinzu und rufen Daten ab**

1. Erstellen einer Beispieltabelle:

		create 'sampletable', 'cf1'

2. Hinzufügen einer Zeile zur Beispieltabelle:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Auflisten der Zeilen in der Beispieltabelle:
	
		scan 'sampletable'

**Überprüfen des Clusterstatus in der HBase WebUI**
	
HBase bietet auch eine WebUI, die bei der Überwachung des Clusters hilft, z. B. durch die Bereitstellung von Abfragestatistiken oder von Informationen zu Regionen. Im HBase-Cluster finden Sie die WebUI unter der Adresse des ZooKeeperNode.


	http://zookeepernode:60010/master-status
	
Bei einem HighAvailability (HA)-Cluster gibt es einen Link zum aktuellen aktiven HBase-Masterknoten, auf dem die WebUI gehostet wird.

**Massenladen einer Beispieltabelle**

1. Erstellen Sie "samplefile1.txt" mit den folgenden Daten, und laden Sie sie im Azure-BLOB-Speicher in das Verzeichnis "/tmp/samplefile1.txt":

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. Wechseln Sie in das HBase-Stammverzeichnis:
		
		cd %HBASE_HOME%\bin

3. Führen Sie "ImportTsv" aus:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. Laden Sie die Ausgabe aus dem vorherigen Befehl in HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>Abfragen einer HBase-Tabelle mit Hive

Nachdem Sie einen HBase-Cluster bereitgestellt und eine HBase-Tabelle erstellt haben, können Sie sie mit Hive abfragen. In diesem Abschnitt erstellen Sie eine Hive-Tabelle, die zu Ihrer HBase-Tabelle zugeordnet ist und verwenden diese Tabelle, um die Daten in Ihrer HBase-Tabelle abzufragen.

**So öffnen Sie das Cluster-Dashboard**

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an. 
2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Sie sehen eine Liste der erstellten Cluster, inklusive des Clusters, das Sie im vorigen Abschnitt erstellt haben.
3. Klicken Sie auf den Namen des Clusters, in dem Sie den Hive-Job ausführen möchten.
4. Klicken Sie unten auf der Seite auf **ABFRAGEKONSOLE**, um das Cluster-Dashboard zu öffnen. Daraufhin wird eine Webseite in einer neuen Browser-Registerkarte geöffnet.   
5. Geben Sie den Benutzernamen und das Kennwort des Hadoop-Benutzers ein.  Der Standard-Benutzername ist **admin**, und das Kennwort haben Sie bei der Bereitstellung ausgewählt. Es wird eine neue Browserregisterkarte geöffnet. 
6. Klicken Sie oben auf **Hive-Editor**. Der Hive-Editor sieht folgendermaßen aus:

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**So führen Sie Hive-Abfragen aus**

1. Geben Sie das HiveQL-Skript in Hive-Editor ein, und klicken Sie auf **Senden**, um eine Hive-Tabellenzuordnung zur HBase-Tabelle zu erstellen. Stellen Sie sicher, dass Sie die Tabelle "sampletable" mithilfe der HBase-Shell erstellt haben, auf die hier in HBase verwiesen wird, bevor Sie diese Anweisung ausführen.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	Warten Sie, bis der **Status** in **Abgeschlossen** aktualisiert wurde.
 
2. Geben Sie das folgende HiveQL-Skript in den Hive-Editor ein, und klicken Sie dann auf die Schaltfläche **SENDEN**. Die Hive-Abfrage fragt die Daten in der HBase-Tabelle ab:

     	SELECT count(*) FROM hbasesampletable;
 
4. Um die Ergebnisse der Hive-Abfrage abzurufen, klicken Sie im Fenster **Auftragssitzung** auf den Link **Details anzeigen**, nachdem der Auftrag ausgeführt wurde. Die Auftragsausgabe muss 1 sein, da Sie nur einen Datensatz in die HBase-Tabelle eingefügt haben.




**So durchsuchen Sie die Ausgabedatei**

1. Klicken Sie ob in der Abfragekonsole auf **Dateibrowser**.
2. Klicken Sie auf das Azure-Speicherkonto, das als Standarddateisystem für den HBase-Cluster verwendet wird.
3. Klicken Sie auf den HBase-Clusternamen. Der Standardcontainer für das Azure-Speicherkonto verwendet den Namen des Clusters.
4. Klicken Sie auf **user**.
5. Klicken Sie auf **admin**. Dies ist der Hadoop-Benutzername.
6. Klicken Sie auf den Namen des Auftrags, dessen Zeit unter **Zuletzt geändert** mit der Zeit der Ausführung der SELECT-Hive-Abfrage übereinstimmt.
4. Klicken Sie auf **stdout**. Speichern Sie die Datei, und öffnen Sie die Datei mit dem Editor. Die Ausgabe muss 1 sein.

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>Erstellen einer HBase-Tabelle und Abrufen von Daten aus der Tabelle mit der Microsoft-HBase-REST-Clientbibliothek für .NET C#-APIs

Sie müssen die Microsoft-HBase-REST-Clientbibliothek für das .NET-Projekt von GitHub herunterladen, und das Projekt muss für die Verwendung des HBase .NET SDK erstellt werden. Das folgende Verfahren bietet Anweisungen für diese Aufgabe.

1. Erstellen Sie eine neue C#-Visual Studio Windows Desktop-Konsolenanwendung.
2. Öffnen Sie die NuGet-Paket-Manager-Konsole, indem Sie im Menü **EXTRAS** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole** klicken.
3. Führen Sie den folgenden NuGet-Befehl in der Konsole aus:

	Install-Package Microsoft.HBase.Client

5. Fügen Sie am Anfang der Datei die folgenden "Using"-Anweisungen ein:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Ersetzen Sie die Main-Funktion durch Folgendes:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35. 
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Legen Sie die ersten drei Variablen in der Main-Funktion fest.
8. Drücken Sie **F5**, um die Anwendung auszuführen.



##<a name="next"></a> Nächste Schritte
In diesem Lernprogramm haben Sie erfahren, wie Sie ein HBase-Cluster bereitstellen, Tabellen erstellen und die Daten in diesen Tabellen in der HBase-Befehlszeile anzeigen können. Außerdem haben Sie gelernt, wie Sie die Daten in HBase-Tabellen mit Hive abfragen und mit den HBase C#-APIs HBase-Tabellen erstellen und Daten aus der Tabelle abrufen können. 

Weitere Informationen finden Sie unter:

- [Überblick über HDInsight HBase][hdinsight-hbase-overview]:
HBase ist eine Open-Source-NoSQL-Datenbank, basierend auf Hadoop, die wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.
- [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet]:
Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können.
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment]:
Erfahren Sie, wie Sie in Echtzeit [Stimmungsanalysen](http://en.wikipedia.org/wiki/Sentiment_analysis) bei Big Data mithilfe von HBase in einem Hadoop-Cluster in HDInsight durchführen können.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/de-de/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png









<!--HONumber=42-->
