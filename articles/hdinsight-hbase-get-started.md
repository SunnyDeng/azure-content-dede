<properties
	pageTitle="Erstellen und Abfragen von HBase-Tabellen mit Hive in HDInsight | Azure"
	description="Erste Schritte mit HBase mit Hadoop in HDInsight. Erfahren Sie, wie Sie HBase Tabellen erstellen und sie mithilfe von Hive abfragen."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# Erste Schritte mit Apache HBase in HDInsight

Erfahren Sie, wie Sie HBase-Tabellen in HDInsight erstellen und mit Hive abfragen.

HBase ist eine NoSQL-Datenbank mit niedriger Latenz und ermöglicht die Onlinetransaktionsverarbeitung großer Datenmengen. HBase ist als verwalteter, in die Azure-Umgebung integrierter Cluster verfügbar. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blob-Speicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden. Weitere Informationen zu HBase und den Szenarios, für die es verwendet werden kann, finden Sie unter [Überblick über HBase in HDInsight][hdinsight-hbase-overview].

> [AZURE.NOTE]HBase (Version 0.98.0) ist nur für HDInsight-Cluster der Version 3.1 in HDInsight verfügbar (basiert auf Apache Hadoop und YARN 2.4.0). Versionshinweise finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?][hdinsight-versions]

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**: Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Angebote für Mitglieder][azure-member-offers] oder [Einen Monat kostenlos testen][azure-free-trial].
- **Ein Azure-Speicherkonto**: Anweisungen finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].
- **Eine Arbeitsstation**, auf der Visual Studio 2013 installiert ist: Anweisungen hierzu finden Sie unter [Installieren von Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Bereitstellen von HBase-Clustern

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**So stellen Sie einen HBase-Cluster im Azure-Portal bereit**


1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie unten links auf **NEU** und dann auf **DATA SERVICES** > **HDINSIGHT** > **HBASE**.

	Sie können auch die Option BENUTZERDEFINIERT ERSTELLEN verwenden.
3. Geben Sie Werte für **CLUSTERNAME**, **CLUSTERGRÖSSE**, "CLUSTER-BENUTZERKENNWORT" und **SPEICHERKONTO** ein.

	![Auswählen eines HBase-Clustertyps und Eingeben von Clusteranmeldeinformationen.][img-hdinsight-hbase-cluster-quick-create]

	Der Standard-HTTP-Benutzername ist "admin". Sie können den Namen mithilfe der Option BENUTZERDEFINIERT ERSTELLEN anpassen.

	> [AZURE.WARNING]Für eine hohe Verfügbarkeit der HBase-Dienste sollten Sie einen Cluster mit mindestens **drei** Knoten bereitstellen. Dadurch wird sichergestellt, dass die HBase-Datenbereiche auf anderen Knoten verfügbar sind, wenn ein Knoten ausfällt.

4. Klicken Sie unten rechts auf das Häkchensymbol, um den HBase-Cluster zu erstellen.

>[AZURE.NOTE]Nach dem Löschen eines HBase-Clusters können Sie unter Verwendung des gleichen Standard-Blobs einen anderen HBase-Cluster erstellen. Der neue Cluster übernimmt die HBase-Tabellen, die Sie im ursprünglichen Cluster erstellt haben.

## Erstellen einer HBase-Beispieltabelle in der HBase-Befehlszeile
In diesem Abschnitt wird beschrieben, wie Sie mit der HBase-Shell HBase-Tabellen erstellen sowie Zeilen hinzufügen und auflisten können. Für den Zugriff auf die HBase-Shell müssen Sie zunächst das Remotedesktopprotokoll (RDP) aktivieren und dann eine RDP-Verbindung mit dem HBase-Cluster herstellen. Anweisungen hierzu finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal][hdinsight-manage-portal].


**So verwenden Sie die HBase-Shell**

1. Klicken Sie in der RDP-Sitzung auf die Verknüpfung **Hadoop-Befehlszeile** auf dem Desktop.
2. Wechseln Sie in das HBase-Stammverzeichnis:

		cd %HBASE_HOME%\bin
3. Öffnen Sie die HBase-Befehlszeile:

		hbase shell

4. Erstellen Sie eine HBase mit einer Spaltenfamilie, und fügen Sie eine Zeile ein:

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Weitere Informationen über das HBase-Tabellenschema finden Sie unter [Introduction to HBase Schema Design][hbase-schema] (Einführung in das HBase-Schemadesign, in englischer Sprache). Weitere HBase-Befehle finden Sie in der [Apache HBase-Referenz][hbase-quick-start] (in englischer Sprache).
5. Listen Sie die HBase-Tabellen auf:

		list

**Überprüfen des Clusterstatus in der HBase WebUI**

HBase wird auch mit einer WebUI geliefert, über die Sie Ihren Cluster überwachen können. Sie können z. B. Statistiken oder Informationen zu Regionen anfordern. Im HBase-Cluster finden Sie die WebUI unter der Adresse des ZooKeeperNode:


	http://zookeepernode:60010/master-status

Bei einem Hochverfügbarkeitscluster ist ein Link zum aktuellen aktiven HBase-Masterknoten vorhanden, auf dem die WebUI gehostet wird.

**Massenladen einer Beispieltabelle**

1. Erstellen Sie in der HBase-Shell eine HBase-Tabelle mit zwei Spaltenfamilien:

		create 'Contacts', 'Personal', 'Office'


3. Erstellen Sie eine Kontaktliste, die die folgenden Daten enthält, und laden Sie sie in den Ordner mit dem Namen "/tmp/contacts.txt" im Azure-Blob-Speicher hoch. Anweisungen hierzu finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight][hdinsight-upload-data].

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. Ändern Sie den Ordner in einer Hadoop-Befehlszeile in das HBase-Basisverzeichnis:

		cd %HBASE_HOME%\bin

3. Führen Sie ImportTsv aus. ImportTsv ist ein Tool, das Daten im TSV-Format in HBase lädt. Es dient zwei unterschiedlichen Zwecken: Laden von Daten aus dem TSV-Format in das Hadoop Distributed File System (HDFS) in HBase und Vorbereiten der zu ladenden Dateien. Weitere Informationen finden Sie in der [Apache HBase-Referenz][hbase-reference] (in englischer Sprache).

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. Laden Sie die Ausgabe aus dem vorherigen Befehl in HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Abfragen einer HBase-Tabelle mit Hive

Nachdem Sie einen HBase-Cluster bereitgestellt und eine HBase-Tabelle erstellt haben, können Sie nun Daten in der Tabelle mit Hive abfragen. In diesem Abschnitt erstellen Sie eine Hive-Tabelle, die der HBase-Tabelle zugeordnet ist, und verwenden diese Tabelle, um die Daten in der HBase-Tabelle abzufragen.

**So öffnen Sie das Cluster-Dashboard**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie im linken Bereich auf **HDInsight**. Sie sehen eine Liste mit Clustern, einschließlich des Clusters, den Sie im vorherigen Abschnitt erstellt haben.
3. Klicken Sie auf den Namen des Clusters, in dem Sie den Hive-Job ausführen möchten.
4. Klicken Sie unten auf der Seite auf **ABFRAGEKONSOLE**, um das Cluster-Dashboard zu öffnen. Dadurch wird eine Webseite in einer anderen Browserregisterkarte geöffnet.
5. Geben Sie den Benutzernamen und das Kennwort des Hadoop-Benutzers ein. Der Standardbenutzername lautet **admin**, das Kennwort haben Sie bei der Einrichtung eingegeben. Eine neue Browserregisterkarte wird geöffnet.
6. Klicken Sie oben auf der Seite auf **Hive-Editor**. Der Hive-Editor sieht folgendermaßen aus:

	![HDInsight-Cluster-Dashboard.][img-hdinsight-hbase-hive-editor]





























**So führen Sie Hive-Abfragen aus**

1. Geben Sie das folgende HiveQL-Skript im Hive-Editor ein, und klicken Sie auf **SENDEN**, um eine Hive-Tabelle zu erstellen, die der HBase-Tabelle zugeordnet ist. Stellen Sie sicher, dass Sie die zuvor in diesem Lernprogramm beschriebene Beispieltabelle in der HBase-Shell erstellt haben, bevor Sie diese Anweisung ausführen.

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Warten Sie, bis der **Status** in **Abgeschlossen** geändert wurde.

2. Geben Sie das folgende HiveQL-Skript in den Hive-Editor ein, und klicken Sie dann auf **SENDEN**. Die Hive-Abfrage fragt die Daten in der HBase-Tabelle ab:

     	SELECT count(*) FROM hbasecontactstable;

4. Um die Ergebnisse der Hive-Abfrage abzurufen, klicken Sie auf den Link **Details anzeigen** im Fenster **Auftragssitzung**, nachdem der Auftrag abgeschlossen wurde. Da Sie einen Datensatz in der HBase-Tabelle eingefügt haben, wird nur eine Ausgabedatei für den Auftrag angezeigt.




**So durchsuchen Sie die Ausgabedatei**

1. Klicken Sie in der Abfragekonsole auf **Dateibrowser**.
2. Klicken Sie auf das Azure-Speicherkonto, das als Standarddateisystem für den HBase-Cluster verwendet wird.
3. Klicken Sie auf den HBase-Clusternamen. Der Standardcontainer für das Azure-Speicherkonto verwendet den Namen des Clusters.
4. Klicken Sie auf **Benutzer** und dann auf **Admin**. (Dies ist der Hadoop-Benutzername.)
6. Klicken Sie auf den Namen des Auftrags, dessen Zeitangabe unter **Zuletzt geändert** mit der Zeitangabe der Ausführung der SELECT-Hive-Abfrage übereinstimmt.
4. Klicken Sie auf **stdout**. Speichern Sie die Datei, und öffnen Sie die Datei mit dem Editor. Es wird nur eine Ausgabedatei angezeigt.

	![HDInsight HBase, Dateibrowser Hive-Editor][img-hdinsight-hbase-file-browser]

## Erstellen einer HBase-Tabelle und Abrufen von Daten aus der Tabelle mit der Clientbibliothek der HBase-REST-API für .NET C#

Sie müssen die Clientbibliothek der HBase-REST-API für .NET von GitHub herunterladen und das Projekt so erstellen, dass Sie das HBase .NET SDK verwenden können. Das folgende Verfahren bietet Anweisungen für diese Aufgabe.

1. Erstellen Sie eine neue C#-Visual Studio Windows Desktop-Konsolenanwendung.
2. Öffnen Sie die NuGet-Paket-Manager-Konsole, indem Sie im Menü **EXTRAS** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole** klicken.
3. Führen Sie den folgenden NuGet-Befehl in der Konsole aus:

		Install-Package Microsoft.HBase.Client

5. Fügen Sie die folgenden **using**-Anweisungen am Anfang der Datei ein:

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Ersetzen Sie die **Main**-Funktion durch den folgenden Code:

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

7. Legen Sie die ersten drei Variablen in der **Main**-Funktion fest.
8. Drücken Sie **F5**, um die Anwendung auszuführen.



## Wie geht es weiter?
In diesem Lernprogramm haben Sie erfahren, wie Sie einen HBase-Cluster bereitstellen sowie Tabellen erstellen und die Daten in diesen Tabellen in der HBase-Shell anzeigen können. Außerdem haben Sie gelernt, wie Sie die Daten in HBase-Tabellen mit Hive abfragen und mit den HBase C# REST-APIs HBase-Tabellen erstellen und Daten aus der Tabelle abrufen können.

Weitere Informationen finden Sie unter:

- [Überblick über HBase in HDInsight][hdinsight-hbase-overview]: HBase ist eine Open-Source-NoSQL-Datenbank von Apache, die auf Hadoop aufbaut und wahlfreien Zugriff sowie starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.
- [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet]: Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können.
- [Konfiguration von HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md): Erfahren Sie, wie Sie HBase-Replikation über zwei Azure-Datencenter konfigurieren. 
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment]: Erfahren Sie, wie Sie in Echtzeit [Stimmungsanalysen](http://en.wikipedia.org/wiki/Sentiment_analysis) bei Big Data mithilfe von HBase in einem Hadoop-Cluster in HDInsight durchführen können.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->