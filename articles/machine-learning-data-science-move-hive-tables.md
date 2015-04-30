<properties 
	pageTitle="Erstellen und Laden von Daten in Hive-Tabellen aus Azure-Blob-Speicher | Azure" 
	description="Erstellen von Hive-Tabellen und Laden von Daten aus Blobs in Hive-Tabellen" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="hangzh;bradsev"/>

 
# Erstellen und Laden von Daten in Hive-Tabellen aus Azure-Blob-Speicher
 
In diesem Dokument werden generische Hive-Abfragen beschrieben, die Hive-Tabellen erstellen und Daten aus dem Azure-Blob-Speicher laden. Es werden auch einige Hinweise zur Partitionierung der Hive-Tabellen und zur Verwendung des ORC-Formats (Optimized Row Columnar) zur Verbesserung der Abfrageleistung bereitgestellt.

Die Hive-Abfragen wurden im [GitHub-Repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) freigegeben und können von dort heruntergeladen werden.

Wenn Sie einen virtuellen Azure-Computer mithilfe der Anweisungen in [Einrichten eines virtuellen Azure-Computers für Data Science](machine-learning-data-science-setup-virtual-machine.md) erstellen, wurde diese Skriptdatei bereits in das Verzeichnis *C:\Users\<user name>\Documents\Data Science Scripts* auf dem virtuellen Computer heruntergeladen. Sie müssen Ihr eigenes Datenschema implementieren und die Konfiguration des Azure-Blob-Speichers in den entsprechenden Feldern dieser Abfragen vornehmen. Danach sollten diese Hive-Abfragen für die Übermittlung bereit sein.

Es wird davon ausgegangen, dass die Daten für die Hive-Tabellen in einem **unkomprimierten** Tabellenformat vorliegen und dass die Daten in den Standardcontainer (oder einen zusätzlichen Container) des Speicherkontos hochgeladen wurden, das vom Hadoop-Cluster verwendet wird. Wenn Sie mit den _NYC Taxi Trip-Daten_ üben möchten, müssen Sie zuerst [alle 24 Dateien herunterladen](http://www.andresmh.com/nyctaxitrips/) (12 Fahrtendateien und 12 Preisdateien), alle Dateien als CSV-Dateien **entpacken** und diese dann in den Standardcontainer (oder einen geeigneten Container) des Azure-Speicherkontos hochladen, das für das [Anpassen des Azure HDInsight Hadoop-Clusters](machine-learning-data-science-customize-hadoop-cluster.md) verwendet wurde. 

Hive-Abfragen können an der Hadoop-Befehlszeile auf dem Hauptknoten des Hadoop-Clusters übermittelt werden. Dazu melden Sie sich auf dem Hauptknoten des Hadoop-Clusters an, öffnen die Hadoop-Befehlszeile und übermitteln die Hive-Abfragen von dort aus. Anweisungen dazu finden Sie unter [Übermitteln von Hive-Abfragen an HDInsight Hadoop-Cluster in Cloud Data Science](machine-learning-data-science-process-hive-tables.md).

Sie können auch die Abfrage-Konsole (Hive-Editor) verwenden, indem Sie die URL 

https://&#60;Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor

in einem Webbrowser eingeben. Sie werden aufgefordert die Anmeldeinformationen für den Hadoop-Cluster anzugeben. Sie können aber auch wie unter [Übermitteln von Hive-Aufträgen mit PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell) beschrieben vorgehen. 

## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:
 
* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](hdinsight-get-started.md#storage) 
* Sie haben einen angepassten Hadoop-Cluster mit dem HDInsight-Dienst bereitgestellt.  Anweisungen dazu finden Sie unter [Anpassen von Azure HDInsight Hadoop-Clustern für Data Science](machine-learning-data-science-customize-hadoop-cluster.md).
* Sie haben den Remotezugriff auf den Cluster aktiviert, sich angemeldet und die Hadoop-Befehlszeile geöffnet. Anweisungen finden Sie unter [Zugreifen auf den Hauptknoten von Hadoop-Clustern](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


## <a name="create-tables"></a>Erstellen von Hive-Datenbank und -Tabellen
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

- `<database name>`: der Name der zu erstellenden Datenbank. Wenn Sie die Standarddatenbank verwenden möchten, kann die Abfrage `create database...` ausgelassen werden. 
- `<table name>`: der Name der zu erstellenden Tabelle in der angegebenen Datenbank. Wenn Sie die Standarddatenbank verwenden, kann auf die Tabelle direkt mit `<table name>` verwiesen werden - ohne `<database name>`.
- `<field separator>`: das Trennzeichen für die Felder in der Datendatei, die in die Hive-Tabelle hochgeladen werden soll. 
- `<line separator>`: das Trennzeichen für die Zeilen in der Datendatei. 
- `<storage location>`: der Azure-Speicherort zum Speichern der Daten der Hive-Tabellen. Wenn Sie keine `LOCATION '<storage location>'` angeben, werden die Datenbank und die Tabellen im Verzeichnis `hive/warehouse/` im Standardcontainer des Hive-Clusters gespeichert. Wenn Sie den Speicherort angeben möchten, muss sich dieser im Standardcontainer für die Datenbank und die Tabellen befinden. Dieser Speicherort muss als relativer Pfad zum Standardcontainer des Clusters im Format `'wasb:///<Verzeichnis 1>/'` oder `'wasb:///<Verzeichnis 1>/<Verzeichnis 2>/'` usw. angegeben werden. Nach der Ausführung der Abfrage werden die relativen Verzeichnisse im Standardcontainer erstellt. 
- `TBLPROPERTIES("skip.header.line.count"="1")`: Wenn die Datendatei einen Header enthält, müssen Sie diese Eigenschaft am **Ende** der Abfrage `create table` einfügen. Andernfalls wird der Header als ein Datensatz in die Tabelle geladen. Wenn die Datendatei keinen Header enthält, kann dieser Konfigurationsschritt in der Abfrage ausgelassen werden. 

## <a name="load-data"></a>Laden von Daten in Hive-Tabellen
Mit dieser Hive-Abfrage laden Sie Daten eine Hive-Tabelle.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>`: Wenn sich die in die Hive-Tabelle hochzuladende Blob-Datei im Standardcontainer des HDInsight Hadoop-Clusters befindet, sollte `<path to blob data>` das Format `'wasb:///<Verzeichnis in diesem Container>/<Blob-Dateiname>'` haben. Die Blob-Datei kann sich auch in einem zusätzlichen Container des HDInsight Hadoop-Clusters befinden. In diesem Fall sollte `<path to blob data>` das Format `'wasb://<Containername>@<Speicherkontoname>.blob.windows.core.net/<Blob-Dateiname>'` haben.

>[AZURE.NOTE] Die Blob-Daten, die in die Hive-Tabelle hochgeladen werden sollen, müssen sich im Standard- oder einem zusätzlichen Container des Speicherkontos für den Hadoop-Cluster befinden. Andernfalls schlägt die Abfrage `LOAD DATa` fehl, weil sie keinen Zugriff auf die Daten hat. 


## <a name="partition-orc"></a>Weiterführende Themen: Partitionieren von Tabellen und Speichern von Hive-Daten im ORC-Format

Wenn die Daten sehr umfangreich sind, ist ein Partitionieren der Tabelle für Abfragen, die nur wenige Partitionen der Tabelle durchsuchen müssen, sehr hilfreich. Es ist z. B. sinnvoll, die Protokolldaten einer Website nach dem Datum zu partitionieren. 

Neben der Partitionierung der Hive-Tabellen ist es auch vorteilhaft, die Hive-Daten im ORC-Format (Optimized Row Columnar) zu speichern. Weitere Informationen zum ORC-Format finden Sie unter [Using ORC files improves performance when Hive is reading, writing, and processing data](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles) (in englischer Sprache).

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

Beim Abfragen partitionierter Tabellen wird empfohlen, die Partitionierungsbedingung am **Anfang** der `where`-Klausel einzufügen, um die Effizienz der Suche zu verbessern. 

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Speichern von Hive-Daten im ORC-Format

Sie können nicht direkt Daten im ORC-Speicherformat aus dem Blob in Hive-Tabellen laden. Mit den folgenden Schritten können Sie Daten aus Azure-Blobs, die im ORC-Format gespeichert sind, in Hive-Tabellen laden. 

1. Erstellen Sie die externe Tabelle **STORED AS TEXTFILE**, und laden Sie Daten aus dem Blob-Speicher in die Tabelle.

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

2. Erstellen Sie eine interne Tabelle mit demselben Schema und demselben Feldtrennzeichen wie die in Schritt 1 erstellte externe Tabelle, und speichern Sie die Hive-Daten im ORC-Format.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Wählen Sie Daten aus der in Schritt 1 erstellten externen Tabelle aus, und fügen Sie sie in die ORC-Tabelle ein:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

[AZURE.NOTE] Wenn die "TEXTFILE"-Tabelle `<database name>.<external textfile table name>` Partitionen aufweist, fügt `SELECT * FROM <database name>.<external textfile table name>` in Schritt 3 die Partitionsvariable als ein Feld in die zurückgegebenen Daten ein. Beim Einfügen in `<database name>.<ORC table name>` tritt ein Fehler auf, weil `<database name>.<ORC table name>` nicht die Partitionsvariable als Feld im Tabellenschema enthält. In diesem Fall müssen Sie die in `<database name>.<ORC table name>` einzufügenden Felder explizit wie folgt auswählen:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. Sie können `<external textfile table name>` gefahrlos mithilfe der folgenden Abfrage löschen, nachdem alle Daten in `<database name>.<ORC table name>` eingefügt wurden:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Sie besitzen nun eine einsatzbereite Tabelle mit Daten im ORC-Format. 

<!--HONumber=49-->