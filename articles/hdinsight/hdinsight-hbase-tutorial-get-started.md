<properties
	pageTitle="HBase-Lernprogramm: Erste Schritte mit HBase in Hadoop | Microsoft Azure"
	description="Führen Sie dieses HBase-Lernprogramm aus, um sich in Apache HBase mit Hadoop in HDInsight einzuarbeiten. Erstellen Sie über die HBase-Shell Tabellen und fragen Sie diese mit Hive ab."
	keywords="apache hbase,hbase,hbase shell,hbase tutorial"
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
	ms.topic="get-started-article"
	ms.date="07/28/2015"
	ms.author="jgao"/>



# HBase-Lernprogramm: Erste Schritte mit Apache HBase mit Hadoop in HDInsight

Erfahren Sie, wie Sie einen HBase-Cluster in HDInsight bereitstellen, HBase-Tabellen erstellen und die Tabellen mit Hive abfragen. Allgemeine Informationen zu HBase finden Sie unter [Überblick zu HDInsight HBase][hdinsight-hbase-overview].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [HBase-Lernprogramm: Erste Schritte mit Apache HBase mit Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started-v1.md)

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten für Windows-basierte HDInsight-Cluster. Informationen zur Verwendung von Linux-basierten Clustern finden Sie unter [hdinsight-hbase-tutorial-get-started-linux.md).
>
> HBase (Version 0.98.0) für Windows-basiertes HDInsight ist nur für HDInsight-Cluster der Version 3.1 verfügbar (basiert auf Apache Hadoop und YARN 2.4.0). Versionshinweise finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?][hdinsight-versions]

##Voraussetzungen

Bevor Sie mit diesem Lernprogramm zu HBase beginnen können, benötigen Sie Folgendes:

- **Ein Microsoft Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Eine Arbeitsstation** mit Visual Studio 2013. Anweisungen hierzu finden Sie unter [Installieren von Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Bereitstellen eines HBase-Clusters

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**So stellen Sie einen HBase-Cluster im Azure-Portal bereit**


1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie in der Ecke oben rechts auf **Neu**, und klicken Sie dann auf **Daten und Analysen** und **HDInsight**.
3. Geben Sie die folgenden Werte ein:

	- **Clustername**: Geben Sie einen Namen zur Identifizierung dieses Clusters ein.
	- **Clustertyp**: HBase
	- **Clusterbetriebssystem**: Der HDInsight HBase-Cluster ist derzeit nur für Windows-Betriebssysteme verfügbar.
	- **Abonnement**: Wählen Sie das Azure-Abonnement aus, das für die Bereitstellung des Clusters verwendet werden soll.
	- **Ressourcengruppe**: Hier können Sie eine Azure-Ressourcengruppe hinzufügen oder auswählen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).
	- **Anmeldeinformationen konfigurieren**: Bei Windows-basierten Clustern können Sie einen Clusterbenutzer (auch als HTTP- oder HTTP-Webdienstbenutzer bezeichnet) und einen Remotedesktopbenutzer erstellen.
	- **Datenquelle**: Erstellen Sie ein neues Azure-Speicherkonto, oder wählen Sie ein vorhandenes Azure-Speicherkonto aus, das als Standarddateisystem für den Cluster verwendet werden soll. Dieses Azure-Speicherkonto muss sich am gleichen Standort befinden wie der HDInsight HBase-Cluster.
	- **Hinweis zu Tarifen:** Wählen Sie die Anzahl der Regionsserver für den HBase-Cluster aus.

		> [AZURE.WARNING]Um die Hochverfügbarkeit der HBase-Dienste sicherzustellen, müssen Sie einen Cluster mit mindestens **drei** Knoten bereitstellen. Sollte in diesem Fall ein Knoten ausfallen, stehen die Datenbereiche von HBase noch auf den anderen Knoten zur Verfügung.

		> Solange Sie sich noch in HBase einarbeiten, sollten Sie als Clustergröße 1 festlegen und den Cluster nach jeder Verwendung löschen. Auf diese Weise halten Sie die Kosten für den Cluster auf ein Minimum.

	- **Optionale Konfiguration**: Hier können Sie die Clusterversion auswählen, Azure Virtual Network und den Hive-/Oozie-Metastore sowie Skriptaktionen konfigurieren und zusätzliche Speicherkonten hinzufügen.

4. Klicken Sie auf **Erstellen**.

>[AZURE.NOTE]Nachdem Sie den HBase-Cluster gelöscht haben, können Sie im gleichen Standardblobcontainer einen neuen HBase-Cluster erstellen. Der neue Cluster übernimmt die im vorherigen Cluster erstellten HBase-Tabellen.

## Verwenden der HBase-Shell
Auf HBase können Sie zurzeit auf zwei Weisen zugreifen. In diesem Abschnitt wird der Zugriff über die HBase-Shell beschrieben. Im nächsten Abschnitt erfahren Sie, wie Sie über das NET SDK zugreifen.

Den meisten Benutzern werden die Daten im Tabellenformat angezeigt:

![HDInsight HBase-Tabellendaten][img-hbase-sample-data-tabular]

In HBase, das eine Implementierung von BigTable ist, sehen die gleichen Daten so aus:

![HDInsight HBase-BigTable-Daten][img-hbase-sample-data-bigtable]

Diese Darstellung ergibt nach Abschluss der nächsten Prozedur mehr Sinn.


**So verwenden Sie die HBase-Shell**

1. Zum Verbindungsaufbau zu Ihrem HBase-Cluster in HDInsight verwenden Sie RDP. Anweisungen zu RDP finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][hdinsight-manage-portal].
2. Klicken Sie in Ihrer RDP-Sitzung auf die Desktopverknüpfung für die **Hadoop-Befehlszeile**.
3. Öffnen Sie die HBase-Befehlszeile:

		cd %HBASE_HOME%\bin
		hbase shell

4. Erstellen Sie eine HBase-Tabelle mit zwei Spaltenfamilien:

		create 'Contacts', 'Personal', 'Office'
		list
5. Fügen Sie Daten ein:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![HDInsight Hadoop HBase-Shell][img-hbase-shell]

6. Rufen Sie eine einzelne Zeile ab:

		get 'Contacts', '1000'

	Sie erhalten das gleiche Ergebnis wie mit dem Scanbefehl, da nur eine Zeile vorhanden ist.

	Weitere Informationen über das HBase-Tabellenschema finden Sie unter [Einführung in das HBase-Schemadesign][hbase-schema]. Beschreibungen weiterer HBase-Befehle finden Sie im [Apache HBase-Referenzhandbuch][hbase-quick-start].


6. Beenden Sie die Shell:

		exit

**So laden Sie Massendaten in die HBase-Kontakttabelle hoch**

HBase bietet mehrere Methoden zum Laden von Daten in Tabellen. Weitere Informationen finden Sie unter [Laden von Massendaten](http://hbase.apache.org/book.html#arch.bulk.load).


Eine Beispieldatendatei befindet sich in folgendem öffentlichen Blobcontainer: wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. Diese Datendatei hat folgenden Inhalt:

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

Wenn Sie möchten, können Sie eine Textdatei erstellen und die Datei in Ihr eigenes Speicherkonto hochladen. Anweisungen hierzu finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight][hdinsight-upload-data].

> [AZURE.NOTE]In der folgenden Prozedur wird die soeben erstellte HBase-Kontakttabelle verwendet.

1. Klicken Sie in Ihrer RDP-Sitzung auf die Desktopverknüpfung für die **Hadoop-Befehlszeile**.
2. Wechseln Sie das Verzeichnis:

		cd %HBASE_HOME%\bin

3. Führen Sie den folgenden Befehl aus, um die Datendatei in das StoreFiles-Format zu konvertieren und unter dem durch "Dimporttsv.bulk.output" angegebenen relativen Pfad zu speichern:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Führen Sie den folgenden Befehl aus, um die Daten aus "/example/data/storeDataFileOutput" in die HBase-Tabelle hochzuladen:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Sie können die HBase-Shell öffnen und den Tabelleninhalt mit dem Scanbefehl auflisten.

## Überprüfen des Clusterstatus

HBase in HDInsight wird mit einer Web-Benutzeroberfläche ausgeliefert, über die Cluster überwacht werden können. In dieser Web-Benutzeroberfläche können Sie Statistiken und Informationen zu Regionen anfordern.

Zum Öffnen der Web-Benutzeroberfläche müssen Sie eine RDP-Sitzung zum Cluster herstellen und dann auf die Desktopverknüpfung "HMaster Info Web UI" klicken. Alternativ können Sie auch die folgende URL in einem Webbrowser eingeben:

	http://zookeeper[0-2]:60010/master-status

In einem Cluster mit hoher Verfügbarkeit gibt es einen Link zum aktuellen aktiven HBase-Masterknoten, unter dem die Web-Benutzeroberfläche gehostet wird.



## Abfragen von HBase-Tabellen mit Hive

Sie können Daten in HBase-Tabellen mit Hive abfragen. In diesem Abschnitt erstellen Sie eine Ihrer HBase-Tabelle zugeordnete Hive-Tabelle, mit der Sie die Daten Ihrer HBase-Tabelle abfragen.

**So öffnen Sie das Cluster-Dashboard**

1. Navigieren Sie zu **https://<HDInsightClusterName>.azurehdinsight.net/**.
5. Geben Sie den Benutzernamen und das Kennwort des Hadoop-Benutzerkontos ein. Der Standardbenutzername ist **admin**, und das Kennwort haben Sie bei der Bereitstellung ausgewählt. Im Browser wird eine neue Registerkarte geöffnet.
6. Klicken Sie oben auf der Seite auf **Hive-Editor**. Der Hive-Editor sieht wie folgt aus:

	![HDInsight-Cluster-Dashboard][img-hdinsight-hbase-hive-editor]

**So führen Sie Hive-Abfragen aus**

1. Geben Sie das folgende HiveQL-Skript in den Hive-Editor ein, und klicken Sie auf **Senden**, um eine der HBase-Tabelle zugeordnete Hive-Tabelle zu erstellen. Stellen Sie vor Ausführung dieser Anweisung sicher, dass Sie die zuvor in diesem Tutorial erwähnte Beispieltabelle über die HBase-Shell erstellt haben.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Warten Sie, bis **Status** den Status **Abgeschlossen** anzeigt.

2. Geben Sie das folgende HiveQL-Skript in den Hive-Editor ein, und klicken Sie dann auf **Senden**. Die Hive-Abfrage fragt die Daten in der HBase-Tabelle ab:

     	SELECT count(*) FROM hbasecontacts;

4. Klicken Sie nach Ausführung des Auftrags zum Abrufen der Ergebnisse der Hive-Abfrage im Fenster **Auftragssitzung** auf den Link **Details anzeigen**. Es wird nur eine Auftragsausgabedatei generiert, da die HBase-Tabelle nur einen Datensatz enthält.




**So durchsuchen Sie die Ausgabedatei**

1. Klicken Sie in der Abfragekonsole auf **Dateibrowser**.
2. Klicken Sie auf das Azure-Speicherkonto, das als Standarddateisystem für den HBase-Cluster verwendet wird.
3. Klicken Sie auf den HBase-Clusternamen. Der Standardcontainer für das Azure-Speicherkonto verwendet den Namen des Clusters.
4. Klicken Sie auf **Benutzer** und dann auf **Admin**. (Dies ist der Hadoop-Benutzername.)
6. Klicken Sie auf den Namen des Auftrags, dessen letztes Änderungsdatum mit der Zeit der Ausführung der Hive-SELECT-Abfrage übereinstimmt.
4. Klicken Sie auf **stdout**. Speichern Sie die Datei, und öffnen Sie die Datei mit dem Editor. Es gibt nur eine Ausgabedatei.

	![HDInsight HBase Hive-Editor, Dateibrowser][img-hdinsight-hbase-file-browser]

## Verwenden der Clientbibliothek der HBase-REST-API für .NET

Sie müssen die Clientbibliothek der HBase-REST-API für .NET von GitHub herunterladen und das Projekt so erstellen, dass Sie das HBase .NET SDK verwenden können. Das folgende Verfahren bietet Anweisungen für diese Aufgabe.

1. Erstellen Sie eine neue C#-Visual Studio Windows Desktop-Konsolenanwendung.
2. Öffnen Sie die NuGet-Paket-Manager-Konsole, indem Sie auf **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** klicken.
3. Führen Sie den folgenden NuGet-Befehl in der Konsole aus:

		Install-Package Microsoft.HBase.Client

5. Fügen Sie am Anfang der Datei die folgenden **using**-Anweisungen ein:

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Ersetzen Sie die **Main**-Funktion durch Folgendes:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
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
In diesem HBase-Lernprogramm für HDInsight haben Sie erfahren, wie Sie einen HBase-Cluster bereitstellen, Tabellen erstellen und die Daten in diesen Tabellen über die HBase-Shell anzeigen. Außerdem haben Sie gelernt, wie Sie an den Daten in HBase-Tabellen eine Hive-Abfrage ausführen und wie Sie mit den HBase C#-REST-APIs HBase-Tabellen erstellen und Daten aus diesen Tabellen abrufen.

Weitere Informationen finden Sie unter:

- [Überblick zu HDInsight HBase][hdinsight-hbase-overview] HBase ist eine Open Source-NoSQL-Datenbank von Apache, die auf Hadoop basiert und wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.
- [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet] Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können.
- [Konfigurieren der HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md) Erfahren Sie, wie Sie HBase-Replikation zwischen zwei Azure-Rechenzentren konfigurieren.
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment] Erfahren Sie, wie Sie in Echtzeit [Stimmungsanalysen](http://en.wikipedia.org/wiki/Sentiment_analysis) bei Big Data mithilfe von HBase in einem Hadoop-Cluster in HDInsight durchführen können.

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
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=Oct15_HO3-->