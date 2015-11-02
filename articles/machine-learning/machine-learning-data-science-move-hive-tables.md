<properties 
	pageTitle="Erstellen und Laden von Daten in Hive-Tabellen aus Azure-Blobspeicher | Microsoft Azure" 
	description="Erstellen von Hive-Tabellen und Laden von Daten aus Blobs in Hive-Tabellen" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2015" 
	ms.author="hangzh;bradsev" />

 
#Erstellen und Laden von Daten in Hive-Tabellen aus Azure-Blob-Speicher

Dieses **Menü** bietet Links zu Themen, in denen beschrieben wird, wie Daten in Zielumgebungen erfasst werden, in denen die Daten während des Cortana-Analyseprozesses (CAP) gespeichert und verarbeitet werden.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

 
## Einführung
In **diesem Dokument** werden allgemeine Hive-Abfragen beschrieben, mit denen Hive-Tabellen erstellt und Daten aus dem Azure-Blobspeicher geladen werden. Es werden auch einige Hinweise zur Partitionierung der Hive-Tabellen und zur Verwendung des ORC-Formats (Optimized Row Columnar) zur Verbesserung der Abfrageleistung bereitgestellt.

## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:
 
* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../hdinsight-get-started.md#storage) 
* Sie haben einen angepassten Hadoop-Cluster mit dem HDInsight-Dienst bereitgestellt. Anweisungen dazu finden Sie unter [Anpassen von Azure HDInsight Hadoop-Clustern für die erweiterte Analyse](machine-learning-data-science-customize-hadoop-cluster.md).
* Sie haben den Remotezugriff auf den Cluster aktiviert, sich angemeldet und die Hadoop-Befehlszeile geöffnet. Anweisungen finden Sie unter [Zugreifen auf den Hauptknoten von Hadoop-Clustern](machine-learning-data-science-customize-hadoop-cluster.md#headnode). 

## Hochladen von Daten in Azure-Blobspeicher
Wenn Sie einen virtuellen Azure-Computer mithilfe der Anweisungen in [Einrichten eines virtuellen Azure-Computers für die erweiterte Analyse](machine-learning-data-science-setup-virtual-machine.md) erstellt haben, wurde diese Skriptdatei bereits in das Verzeichnis *C:\\Users<Benutzername>\\Documents\\Data Science Scripts* auf dem virtuellen Computer heruntergeladen. Sie müssen Ihr eigenes Datenschema implementieren und die Konfiguration des Azure-Blob-Speichers in den entsprechenden Feldern dieser Abfragen vornehmen. Danach sollten diese Hive-Abfragen für die Übermittlung bereit sein.

Es wird davon ausgegangen, dass die Daten für die Hive-Tabellen in einem **unkomprimierten** Tabellenformat vorliegen und dass die Daten in den Standardcontainer (oder einen zusätzlichen Container) des Speicherkontos hochgeladen wurden, das vom Hadoop-Cluster verwendet wird.

Wenn Sie mit den _NYC Taxi Trip-Daten_ üben möchten, müssen Sie zuerst alle 24 <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">NYC Taxi Trip-Daten</a>-Dateien herunterladen (12 Fahrtendateien und 12 Preisdateien), alle Dateien als CSV-Dateien **entzippen** und diese dann in den Standardcontainer (oder einen geeigneten Container) des Azure-Speicherkontos hochladen, das für das Verfahren im Thema [Anpassen des Azure HDInsight Hadoop-Clusters für den erweiterten Analyseprozess](machine-learning-data-science-customize-hadoop-cluster.md) erstellt wurde. Den Prozess zum Hochladen der CSV-Dateien in den Standardcontainer für das Speicherkonto finden Sie auf dieser [Seite](machine-learning-data-science-process-hive-walkthrough/#upload).

## <a name="submit"></a>Übermitteln von Hive-Abfragen
Hive-Abfragen können folgendermaßen übermittelt werden:

* an der Hadoop-Befehlszeile auf dem Hauptknoten des Clusters
* in einem IPython Notebook
* im Hive-Editor
* mit Azure PowerShell-Skripts

Hive-Abfragen sind ähnlich wie SQL-Abfragen. Mit SQL vertraute Benutzer finden möglicherweise das <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">Cheat Sheet „Hive for SQL Users“</a> nützlich.

Beim Übermitteln von Hive-Abfragen können Sie auch das Ziel der Ausgabe der Hive-Abfragen steuern. Diese kann auf den Bildschirm, in eine lokale Datei auf dem Hauptknoten oder in ein Azure-Blob erfolgen.

### Über die Hadoop-Befehlszeile im Hauptknoten des Hadoop-Clusters

Wenn die Abfrage komplex ist, führen Hive-Abfragen direkt am Hauptknoten des Hadoop-Clusters i. d. R. schneller zu Ergebnissen als das Senden im Hive-Editor oder über Azure PowerShell-Skripts.

Melden Sie sich am Hauptknoten des Hadoop-Clusters an, öffnen Sie die Hadoop-Befehlszeile auf dem Desktop des Hauptknotens, und geben Sie den folgenden Befehl ein:

    cd %hive_home%\bin

Es gibt drei Möglichkeiten zum Übermitteln von Hive-Abfragen an der Hadoop-Befehlszeile.

* direkt über die Hadoop-Befehlszeile
* mithilfe von HQL-Dateien
* an der Hive-Befehlskonsole

#### Übermitteln der Hive-Abfragen direkt an der Hadoop-Befehlszeile

Sie können z. B. folgende Befehle ausführen:

	hive -e "<your hive query>;

Damit übermitteln sie die Hive-Abfragen direkt an der Hadoop-Befehlszeile. Hier ist ein Beispiel. Das rote Kästchen enthält den Befehl, der die Hive-Abfrage übermittelt, und das grüne Kästchen umschließt die Ausgabe der Hive-Abfrage.

![Arbeitsbereich erstellen](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### Übermitteln der Hive-Abfragen in HQL-Dateien

Wenn die Hive-Abfrage sehr komplex ist und aus mehreren Zeilen besteht, ist das direkte Bearbeiten der Abfragen an der Hadoop-Befehlszeile oder an der Hive-Konsole nicht sehr praktikabel. Alternativ können die Hive-Abfragen mit einem Text-Editor auf dem Hauptknoten des Hadoop-Clusters in einer HQL-Datei in einem lokalen Verzeichnis auf dem Hauptknoten gespeichert werden. Anschließend kann die Hive-Abfrage in der HQL-Datei mithilfe des `-f`-Arguments im `hive`-Befehl übermittelt werden:

	`hive -f "<path to the .hql file>"`


#### Unterdrücken der Fortschrittsanzeige bei Hive-Abfragen

Standardmäßig wird bei Hive-Abfragen, die über die Hadoop-Befehlszeile übermittelt werden, der Fortschritt des Map/Reduce-Auftrags auf dem Bildschirm angezeigt. Um diese Fortschrittsanzeige zum Map/Reduce-Auftrag zu unterdrücken, können Sie das Argument `-S` (mit Unterscheidung von Groß-/Kleinschreibung) wie folgt an der Befehlszeile angeben:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Übermitteln von Hive-Abfragen an der Hive-Befehlskonsole

Sie können auch die Hive-Befehlskonsole starten, indem Sie den `hive`-Befehl an der Hadoop-Befehlszeile eingeben und dann Hive-Abfragen über die Hive-Befehlskonsole an der Eingabeaufforderung **hive>** übermitteln. Beispiel:

![Arbeitsbereich erstellen](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

In diesem Beispiel kennzeichnen die beiden roten Kästchen die Befehle, die zum Starten der Hive-Befehlskonsole verwendet werden, und die Hive-Abfrage, die über die Hive-Befehlskonsole übermittelt wird. Das grüne Kästchen markiert die Ausgabe der Hive-Abfrage.

In den vorherigen Beispielen wurden die Ergebnisse der Hive-Abfrage direkt auf den Bildschirm ausgeben. Sie können die Ausgabe aber auch in eine lokale Datei auf dem Hauptknoten oder in ein Azure-Blob schreiben. Sie können dann andere Tools verwenden, um die Ausgabe der Hive-Abfragen weiter zu analysieren.

#### Ausgeben der Hive-Abfrageergebnisse in eine lokale Datei

Für die Ausgabe der Hive-Abfrageergebnisse in ein lokales Verzeichnis auf dem Hauptknoten müssen Sie die Hive-Abfrage an der Hadoop-Befehlszeile wie folgt übermitteln:

	`hive -e "<hive query>" > <local path in the head node>`


#### Ausgeben der Hive-Abfrageergebnisse in ein Azure-Blob

Sie können die Hive-Abfrageergebnisse auch in ein Azure-Blob im Standardcontainer des Hadoop-Clusters ausgeben. Die Hive-Abfrage hierfür sieht folgendermaßen aus:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Im folgenden Beispiel wird die Ausgabe der Hive-Abfrage in das Blob-Verzeichnis `queryoutputdir` innerhalb des Standardcontainer des Hadoop-Cluster geschrieben. Hier müssen Sie nur den Namen des Verzeichnisses ohne den Namen des Blobs angeben. Es wird ein Fehler ausgelöst, wenn Sie sowohl den Verzeichnis- als auch den Blobnamen angeben, z. B. **wasb:///queryoutputdir/queryoutput.txt*.

![Arbeitsbereich erstellen](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

Sie können die Ausgabe der Hive-Abfrage im Blob-Speicher anzeigen, indem Sie den Standardcontainer des Hadoop-Clusters mithilfe des Azure-Speicher-Explorers (oder eines ähnlichen Tools) öffnen. Sie können Filter (markiert durch den roten Kasten) anwenden, um nur das Blob mit den angegebenen Buchstaben im Namen abzurufen.

![Arbeitsbereich erstellen](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### Über den Hive-Editor oder Azure PowerShell-Befehle

Sie können auch die Abfrage-Konsole (Hive-Editor) verwenden, indem Sie die URL

*https://&#60;Hadoop Clustername>.azurehdinsight.net/Home/HiveEditor*

in einem Webbrowser eingeben. Beachten Sie, dass Sie zur Eingabe der Anmeldeinformationen für den Hadoop-Cluster aufgefordert werden. Alternativ können Sie [Hive-Aufträge mit PowerShell übermitteln](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## Übermitteln von Hive-Abfragen (alt)

In diesem Dokument werden die verschiedenen Möglichkeiten zum Übermitteln von Hive-Abfragen an Hadoop-Cluster beschrieben, die durch einen HDInsight-Dienst in Azure verwaltet werden. (old intro – TBD incorporate)


Hive-Abfragen können an der Hadoop-Befehlszeile auf dem Hauptknoten des Hadoop-Clusters übermittelt werden. Dazu melden Sie sich auf dem Hauptknoten des Hadoop-Clusters an, öffnen die Hadoop-Befehlszeile und übermitteln die Hive-Abfragen von dort aus. Anweisungen hierzu finden Sie unter [Übermitteln von Hive-Abfragen an HDInsight Hadoop-Cluster im erweiterten Analyseprozess](machine-learning-data-science-process-hive-tables.md).

Sie können auch die Abfrage-Konsole (Hive-Editor) verwenden, indem Sie die URL

https://&#60;Hadoop Clustername>.azurehdinsight.net/Home/HiveEditor

in einem Webbrowser eingeben. Beachten Sie, dass Sie zur Eingabe der Anmeldeinformationen für den Hadoop-Cluster aufgefordert werden. Sie sollten diese Anmeldeinformationen daher bereithalten.

Alternativ können Sie [Hive-Aufträge mit PowerShell übermitteln](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a>Erstellen von Hive-Datenbanken und -Tabellen

Die Hive-Abfragen wurden im [GitHub-Repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) freigegeben und können von dort heruntergeladen werden.

Mit dieser Hive-Abfrage erstellen Sie eine Hive-Tabelle.

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Im Folgenden werden die Felder beschrieben, mit denen Sie die Implementierung und andere Konfigurationen vornehmen:

- **&#60;Datenbankname>**: Name der zu erstellenden Datenbank. Wenn Sie die Standarddatenbank verwenden möchten, kann die Abfrage *create database...* ausgelassen werden. 
- **&#60;Tabellenname>**: Name der zu erstellenden Tabelle in der angegebenen Datenbank. Wenn Benutzer die Standarddatenbank verwenden möchten, kann auf die Tabelle direkt mit *&#60;Tabellenname>* ohne &#60;Datenbankname> verwiesen werden.
- **&#60;Feldtrennzeichen>**: Trennzeichen für die Felder in der Datendatei, die in die Hive-Tabelle hochgeladen werden soll. 
- **&#60;Zeilentrennzeichen>**: Trennzeichen für die Zeilen in der Datendatei. 
- **&#60;Speicherort>**: Azure-Speicherort zum Speichern der Daten der Hive-Tabellen. Wenn Sie *LOCATION &#60;Speicherort>* nicht angeben, werden die Datenbank und die Tabellen im Verzeichnis *hive/warehouse/* im Standardcontainer des Hive-Clusters gespeichert. Wenn Sie den Speicherort angeben möchten, muss sich dieser im Standardcontainer für die Datenbank und die Tabellen befinden. Auf diesen Speicherort muss als relativer Speicherort zum Standardcontainer des Clusters im Format *'wasb:///&#60;Verzeichnis 1>/'* oder *'wasb:///&#60;Verzeichnis 1>/&#60;Verzeichnis 2>/'* usw. verwiesen werden. Nachdem die Abfrage ausgeführt wurde, werden die relativen Verzeichnisse innerhalb des Standardcontainers erstellt. 
- **TBLPROPERTIES("skip.header.line.count"="1")**: Wenn die Datendatei einen Header enthält, müssen Sie diese Eigenschaft **am Ende** der *create table*-Abfrage einfügen. Andernfalls wird der Header als ein Datensatz in die Tabelle geladen. Wenn die Datendatei keinen Header enthält, kann dieser Konfigurationsschritt in der Abfrage ausgelassen werden. 

## <a name="load-data"></a>Laden von Daten in Hive-Tabellen
Mit dieser Hive-Abfrage laden Sie Daten eine Hive-Tabelle.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;Pfad zu Blobdaten>**: Wenn sich die in die Hive-Tabelle hochzuladende Blobdatei im Standardcontainer des HDInsight Hadoop-Clusters befindet, sollte *&#60;Pfad zu Blobdaten>* das Format *'wasb:///&#60;Verzeichnis in diesem Container>/&#60;Blobdateiname>'* haben. Die Blobdatei kann sich auch in einem zusätzlichen Container des HDInsight Hadoop-Clusters befinden. In diesem Fall muss *& #60;Pfad zu Blobdaten>* das Format *'wasb://&#60;Containername>@&#60;Speicherkontoname>.blob.core.windows.net/&#60;Blobdateiname>'* haben.

	>[AZURE.NOTE]Die Blobdaten, die in die Hive-Tabelle hochgeladen werden sollen, müssen sich im Standard- oder einem zusätzlichen Container des Speicherkontos für den Hadoop-Cluster befinden. Andernfalls misslingt die Abfrage *LOAD DATA*, weil sie keinen Zugriff auf die Daten hat.


## <a name="partition-orc"></a>Weiterführende Themen: Partitionieren von Tabellen und Speichern von Hive-Daten im ORC-Format

Wenn die Daten sehr umfangreich sind, ist ein Partitionieren der Tabelle für Abfragen, die nur wenige Partitionen der Tabelle durchsuchen müssen, sehr hilfreich. Es ist z. B. sinnvoll, die Protokolldaten einer Website nach dem Datum zu partitionieren.

Neben der Partitionierung der Hive-Tabellen ist es auch vorteilhaft, die Hive-Daten im ORC-Format (Optimized Row Columnar) zu speichern. Weitere Informationen zur ORC-Formatierung finden Sie unter <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Das Verwenden von ORC-Dateien verbessert die Leistung, wenn Hive Daten liest, schreibt und verarbeitet</a>.

### Partitionierte Tabelle
Mit dieser Hive-Abfrage erstellen Sie eine Hive-Tabelle und laden Daten in diese Tabelle.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Beim Abfragen partitionierter Tabellen wird empfohlen, die Partitionierungsbedingung **am Anfang** der `where`-Klausel einzufügen, um die Effizienz der Suche zu verbessern.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Speichern von Hive-Daten im ORC-Format

Sie können nicht direkt Daten im ORC-Speicherformat aus dem Blob in Hive-Tabellen laden. Mit den folgenden Schritten können Sie Daten aus Azure-Blobs, die im ORC-Format gespeichert sind, in Hive-Tabellen laden.

1. Erstellen Sie die externe Tabelle **STORED AS TEXTFILE**, und laden Sie Daten aus dem Blobspeicher in die Tabelle.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. Erstellen Sie eine interne Tabelle mit demselben Schema und demselben Feldtrennzeichen wie die in Schritt 1 erstellte externe Tabelle, und speichern Sie die Hive-Daten im ORC-Format.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Wählen Sie Daten aus der in Schritt 1 erstellten externen Tabelle aus, und fügen Sie sie in die ORC-Tabelle ein:

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	>[AZURE.NOTE]Wenn die TEXTFILE-Tabelle *&#60;Datenbankname>.&#60;Name der externen TEXTFILE-Tabelle>* Partitionen aufweist, fügt der `SELECT * FROM <database name>.<external textfile table name>`-Befehl in Schritt 3 die Partitionsvariable als ein Feld in das zurückgegebene Dataset ein. Das Einfügen in *&#60;Datenbankname>.&#60;ORC-Tabellenname>* misslingt, da *&#60;Datenbankname>.&#60;ORC-Tabellenname>* nicht die Partitionsvariable als Feld im Tabellenschema enthält. In diesem Fall müssen Sie die in *&#60;Datenbankname>.&#60;ORC-Tabellenname>* einzufügenden Felder explizit wie folgt auswählen:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. Sie können *&#60;Name der externen TEXTFILE-Tabelle>* gefahrlos mithilfe der folgenden Abfrage löschen, nachdem alle Daten in *&#60;Datenbankname>.&#60;ORC-Tabellenname>* eingefügt wurden:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Sie besitzen nun eine einsatzbereite Tabelle mit Daten im ORC-Format.


##Abschnitte zur Optimierung hier einfügen.

Im letzten Abschnitt werden Parameter beschrieben, mit denen Sie die Leistung der Hive-Abfragen optimieren können.

<!---HONumber=Oct15_HO4-->