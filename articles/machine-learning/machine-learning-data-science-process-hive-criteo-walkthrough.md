<properties 
	pageTitle="Erweiterte Analyseprozesse und -technologien in Aktion: Verwenden von Hadoop-Clustern in einem 1-TB-Criteo-DataSet | Microsoft Azure" 
	description="Verwenden der erweiterten Analyseprozesse und -technologien (Advanced Analytics Process and Technology, ADAPT) für ein End-to-End-Szenario mit einem HDInsight Hadoop-Cluster zum Erstellen und Bereitstellen eines Modells unter Verwendung eines großen (1 TB) öffentlich zugänglichen DataSets" 
	services="machine-learning,hdinsight"  
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# Erweiterte Analyseprozesse und -technologien in Aktion – mithilfe von Azure HDInsight-Hadoop-Clustern in einem 1-TB-DataSet

In dieser exemplarischen Vorgehensweise wird die End-to-End-Verwendung der erweiterten Analyseprozesse und -technologien (Advanced Analytics Process and Technology, ADAPT) mit einem [Azure HDInsight Hadoop-Cluster](http://azure.microsoft.com/services/hdinsight/) zum Speichern, Durchsuchen, Entwickeln von Funktionen sowie zum Komprimieren von Daten aus einem der öffentlich zugänglichen [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/)-DataSets veranschaulicht. Mithilfe von Azure Machine Learning erstellen wir anhand dieser Daten binäre Klassifizierungs- und Regressionsmodelle. Zudem zeigen wir auf, wie eines dieser Modelle als Webdienst veröffentlicht werden kann.

Die in dieser exemplarischen Vorgehensweise vorgestellten Aufgaben können auch mit einem iPython-Notizbuch umgesetzt werden. Benutzer, die diesem Ansatz folgen möchten, sollten das Thema [Criteo walkthrough using a Hive ODBC connection](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) (in englischer Sprache) beachten.


## <a name="dataset"></a>Beschreibung des Criteo-DataSets

Bei den Criteo-Daten handelt es sich um ein Klickvorhersage-DataSet mit etwa 370 GB an gzip-komprimierten TSV-Dateien (unkomprimiert ca. 1,3 TB), das aus über 4,3 Milliarden Datensätzen besteht. Es beruht auf den Klickdaten für 24 Tage, die von [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) bereitgestellt werden. Unseren Datenanalysten stehen zudem unkomprimierte Daten für Experimente zur Verfügung.

Alle Datensätze im DataSet enthalten je 40 Spalten:

- Die erste Spalte ist eine Bezeichnungsspalte, die angibt, ob ein Benutzer auf eine Anzeige klickt (Wert "1") oder nicht (Wert "0"). 
- Die nächsten 13 Spalten sind numerische und 
- die letzten 26 nach Kategorien sortierte Spalten. 

Die Spalten werden anonymisiert und mit Aufzählungsnamen versehen: "Col1" (für die Bezeichnungsspalte) bis "Col40" (für die letzte Kategoriespalte).

Hier finden Sie einen Auszug der ersten 20 Spalten der zwei Beobachtungen (Zeilen) aus diesem DataSet:

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Sowohl in den numerischen als auch den Kategoriespalten dieses DataSets fehlen Werte. Im Folgenden wird eine einfache Methode zum Umgang mit den fehlenden Werten beschrieben. Später werden weitere Details der Daten untersucht, wenn wir diese in Hive-Tabellen speichern.

**Definition:** *Durchklickrate (Clickthrough Rate, CTR):* Dies ist der Prozentsatz an Klicks in den Daten. In diesem Criteo-DataSet beträgt die CTR etwa 3,3 % oder 0,033.

## <a name="mltasks"></a>Beispiele für Vorhersageaufgaben
In dieser exemplarischen Vorgehensweise werden zwei beispielhafte Vorhersageprobleme behandelt:

1. **Binäre Klassifikation:** sagt vorher, ob ein Benutzer auf eine Werbung geklickt hat oder nicht:
	- Klasse 0: Kein Klick
	- Klasse 1: Klick

2. **Regression:** sagt anhand der Benutzerfunktionen die Wahrscheinlichkeit eines Anzeigenklicks vorher.


## <a name="setup"></a>Einrichten eines HDInsight Hadoop-Clusters für Data Science

**Hinweis:** Dies ist normalerweise eine **Admin**-Aufgabe.

Richten Sie Ihre Azure Data Science-Umgebung ein, um in drei Schritten Lösungen für Vorhersageanalysen mit HDInsight-Clustern zu erstellen:

1. [Erstellen eines Speicherkontos:](../storage-whatis-account.md) Mit diesem Speicherkonto werden Daten im Azure-Blob-Speicher gespeichert. Die in HDInsight-Clustern verwendeten Daten werden hier gespeichert.

2. [Anpassen von Azure HDInsight Hadoop-Clustern für Data Science:](machine-learning-data-science-customize-hadoop-cluster.md) Mit diesem Schritt wird ein Azure HDInsight Hadoop-Cluster mit 64-Bit-Anaconda Python 2.7 auf allen Knoten erstellt. Beim Anpassen des HDInsight-Clusters müssen zwei (in diesem Thema beschriebene) wichtige Schritte durchgeführt werden.

	* Verknüpfen Sie das in Schritt 1 erstellte Speicherkonto mit dem HDInsight-Cluster. Mit diesem Speicherkonto wird auf Daten zugegriffen, die innerhalb des Clusters verarbeitet werden können.

	* Sie müssen nach dem Erstellen den Remotezugriff auf den Hauptknoten des Clusters aktivieren. Merken Sie sich die hier angegebenen RAS-Anmeldeinformationen (die sich von denen beim Erstellen des Clusters unterscheiden). Sie werden diese später benötigen.

3. [Erstellen eines Azure ML-Arbeitsbereichs:](machine-learning-create-workspace.md) Mit diesem Azure Machine Learning-Arbeitsbereich werden nach dem erstmaligen Untersuchen der Daten und der Komprimierung im HDInsight-Cluster Machine Learning-Modelle erstellt.

## <a name="getdata"></a>Abrufen und Verwenden von Daten aus einer öffentlichen Quelle

Um auf das [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/)-DataSet zuzugreifen, klicken Sie auf den Link, akzeptieren Sie die Nutzungsbedingungen, und geben Sie einen Namen an. Im Folgenden finden Sie eine Beispielmomentaufnahme:

![Criteo-Bestimmungen akzeptieren](http://i.imgur.com/hLxfI2E.png)

Klicken Sie auf **Download fortsetzen**, um weitere Informationen über das DataSet und seine Verfügbarkeit zu erhalten.

Die Daten befinden sich an einem öffentlichen Speicherort für [Azure-Blob-Speicher](../storage-dotnet-how-to-use-blobs.md): wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" bezieht sich auf den Azure-Blob-Speicherort.

1. Die Daten in diesem öffentlichen Blob-Speicher bestehen aus drei untergeordneten Ordnern mit extrahierten Daten.
		
	1. Der Unterordner *raw/count/* enthält die Daten der ersten 21 Tage – von "day_00" bis "day_20".
	2. Der Unterordner *raw/train/* enthält nur die Daten des Tages "day_21".
	3. Der Unterordner *raw/test/* enthält die Daten der beiden Tage "day_22" und "day_23".

2. Wenn Sie mit den gzip-Rohdaten beginnen möchten, finden Sie diese im Hauptordner */raw * als "day_NN.gz", wobei "NN" von 00 bis 23 reicht.

Ein alternativer Ansatz für das Zugreifen, Untersuchen und Modellieren, der keine lokalen Downloads erfordert, wird später in dieser exemplarischen Vorgehensweise erläutert, wenn wir die Hive-Tabellen erstellen.

## <a name="login"></a>Anmeldung beim Cluster-Hauptknoten

Um sich beim Hauptknoten des Clusters anzumelden, suchen Sie im [Azure-Verwaltungsportal](manage.windowsazure.com) den Cluster. Klicken Sie links auf das HDInsight-Elefantensymbol, und doppelklicken Sie anschließend auf den Namen des Clusters. Navigieren Sie zur Registerkarte **Konfiguration**, doppelklicken Sie unten auf der Seite auf das Verbindungssymbol, und geben Sie Ihre RAS-Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden. Dadurch gelangen Sie zum Hauptknoten des Clusters.

Im Folgenden wird eine typische Erstanmeldung am Cluster-Hauptknoten dargestellt:

![Am Cluster anmelden](http://i.imgur.com/Yys9Vvm.png)

Auf der linken Seite sehen Sie die "Hadoop-Befehlszeile", mit der wir die Daten untersuchen werden. Zudem finden Sie zwei nützliche URLs: "Hadoop Yarn Status" und "Hadoop Name Node". Erstere führt zum Auftragsstatus, während Sie über die zweite zu Einzelheiten über die Clusterkonfiguration gelangen.

Nun sind wir bereit für den ersten Teil der exemplarischen Vorgehensweise: Das Untersuchen der Daten mithilfe von Hive und das Vorbereiten der Daten für Azure Machine Learning.

## <a name="hive-db-tables"></a> Erstellen von Hive-Datenbanken und -Tabellen

Öffnen Sie zum Erstellen von Hive-Tabellen für das Criteo-DataSet auf dem Desktopcomputer des Hauptknotens die ***Hadoop-Befehlszeile***, und wechseln Sie mithilfe des folgenden Befehls zum Hive-Verzeichnis.

    cd %hive_home%\bin

**WICHTIGER HINWEIS:** **Führen Sie alle Hive-Befehle dieser exemplarischen Vorgehensweise über Eingabeaufforderung im oben angeführten Hive-Verzeichnis "bin/" aus. So werden alle Pfadprobleme automatisch behoben. Die hier verwendeten Begriffe "Eingabeaufforderung im Hive-Verzeichnis" und "Hadoop-Befehlszeile" sind austauschbar.**

**WICHTIGER HINWEIS 2:** **Zum Ausführen von Hive-Abfragen haben Sie stets folgende Möglichkeit:** cd %hive_home%\bin hive

Wenn Hive REPL mit dem Symbol "hive >" angezeigt wird, können Sie die Abfrage einfach ausschneiden und einfügen, um sie auszuführen.

Mit folgendem Code werden die Datenbank "criteo" und anschließend vier Tabellen erstellt:


* eine *Zahlentabelle* anhand der Tage "day_00" bis "day_20", 
* eine auf "day_21" beruhende *Trainingstabelle* und 
* zwei *Testtabellen* für "day_22" und "day_23". 

Wir teilen die Test-DataSets in zwei verschiedene Tabellen auf, da ein Tag ein Feiertag ist und wir anhand der Klickrate feststellen möchten, ob das Modell Unterschiede zwischen einem Feier- und einem Arbeitstag erkennen kann.

Im Folgenden wird als Referenz das Skript [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) angeführt:

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Wir weisen darauf hin, dass all diese Tabellen extern sind, da wir einfach auf Azure-Blob-Speicher (wasb) verweisen.

**ALLE der im Folgenden erwähnten Hive-Abfragen können auf zwei Arten ausgeführt werden.**

1. **Über die Hive REPL-Befehlszeile:** Zunächst wird ein "hive"-Befehl ausgegeben und die oben angeführte Abfrage kopiert und in die Hive REPL-Befehlszeile kopiert. Gehen Sie hierfür folgendermaßen vor:

		cd %hive_home%\bin
		hive

 	Wenn Sie die Abfrage nun in der REPL-Befehlszeile ausschneiden und einfügen, wird sie ausgeführt.

2. **Speichern von Abfragen in einer Datei und Ausführen des Befehls:** Die zweiten Methode besteht im Speichern der Abfragen in einer HQL-Datei ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)). Anschließend wird zum Ausführen der Abfrage folgender Befehl ausgegeben:

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### Bestätigen des Erstellens der Datenbank und der Tabellen

Anschließend bestätigen wir, dass die Datenbank erstellt wurde, indem wir den folgenden Befehl an der Eingabeaufforderung im Hive-Verzeichnis "/bin" eingeben:

		hive -e "show databases;"

Dies ergibt:

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

Hiermit wird das Erstellen der neuen Datenbank "criteo" bestätigt.

Um zu ermitteln, welche Tabellen erstellt wurden, geben wir einfach den folgenden Befehl an der Eingabeaufforderung im Hive-Verzeichnis "/bin" aus:

		hive -e "show tables in criteo;"

Dies ergibt die folgende Ausgabe:

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a> Untersuchen von Daten in Hive

Nun können wir in Hive einige grundlegende Datenuntersuchungen vornehmen. Zunächst zählen wir die Anzahl der Beispiele in den Trainings- und Testdatentabellen.

### Anzahl der Trainingsbeispiele

Im Folgenden werden die Inhalte für [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) dargestellt:

		SELECT COUNT(*) FROM criteo.criteo_train;

Dies ergibt:

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

Sie können auch den folgenden Befehl an der Eingabeaufforderung im Hive-Verzeichnis "/bin" ausgeben:

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### Anzahl der Testbeispiele in den beiden Test-DataSets

Nun zählen wir die Anzahl der Beispiele in den beiden Test-DataSets. Im Folgenden werden die Inhalte für [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) dargestellt:

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Dies ergibt:
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

Wie immer können wir das Skript zudem über die Hive-Eingabeaufforderung "/bin" aufrufen, indem wir folgenden Befehl ausgeben:

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Abschließend untersuchen wir die Anzahl der Testbeispiele im Test-DataSet für "day_23".

Der hierfür verwendete Befehl ähnelt dem oben angeführten (siehe [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Dies ergibt:
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### Bezeichnungsverteilung im Trainings-DataSet

Die Bezeichnungsverteilung im Trainings-DataSet ist von Interesse. Hierzu führen wir die Inhalte von [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql) an:

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Dies ergibt die Bezeichnungsverteilung:

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

Beachten Sie, dass der Prozentsatz der positiven Bezeichnungen etwa 3,3 % beträgt (in Übereinstimmung mit dem ursprünglichen DataSet).
		
### Histogrammverteilungen für einige numerische Variablen des Trainings-DataSets

Mit der systemeigenen Funktion "histogram_numeric" von Hive können wir herausfinden, wie die Verteilung numerischer Variablen aussieht. Im Folgenden finden Sie die Inhalte von [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Dadurch ergibt sich Folgendes:

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

Die Kombination von Seitenansicht und Auflösung in Hive ermöglicht anstelle der üblichen Liste eine SQL-ähnliche Ausgabe. Beachten Sie, dass in der obigen Tabelle die erste Spalte dem Lagerplatzmittelwert und die zweite der Lagerplatzhäufigkeit entspricht.

### Ungefähre Prozentwerte für einige numerische Variablen des Trainings-DataSets

Im Zusammenhang mit numerischen Variablen ist auch die Berechnung der ungefähren Prozentwerte interessant. Dies wird von der systemeigenen Hive-Funktion "percentile_approx" übernommen. Die Inhalte von [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) lauten:

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Dies ergibt:

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

Wir weisen darauf hin, dass die Verteilung der Prozentwerte in der Regel eng mit der Histogrammverteilung beliebiger numerischer Variablen zusammenhängt.

### Ermitteln der Anzahl der eindeutigen Werte für bestimmte Kategoriespalten im Trainings-DataSet

Im weiteren Verlauf unserer Datenuntersuchung ermitteln wir nun für einige Kategoriespalten die Anzahl der übernommenen eindeutigen Werte. Hierzu führen wir die Inhalte von [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql) an:

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Dies ergibt:

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Beachten Sie, dass "Col15" über 19 Mio. eindeutige Werte verfügt! Mit systemeigenen Techniken wie z. B. "one-hot-encoding" können derartige Mengen an Kategorievariablen nicht sinnvoll codiert werden. Wir erläutern und veranschaulichen zum effizienten Umgang mit diesem Problem insbesondere die leistungsfähige, zuverlässige Technik [Lernen durch Anzahl](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx).

Zum Abschluss dieses Unterabschnitts betrachten wir die Anzahl an eindeutigen Werten für einige weitere Kategoriespalten. Die Inhalte von [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) lauten:

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

Dies ergibt:

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

Wieder erkennen wir, dass mit Ausnahme von "Col20" alle Spalten viele eindeutige Werte enthalten.

### Zusammentreffende Anzahlen von Kategorievariablenpaaren im Trainings-DataSet

Auch zusammentreffende Anzahlen von Kategorievariablenpaaren sind von Interesse. Dies kann mit dem Code in [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql) ermittelt werden:

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Wir kehren die Anzahlwerte anhand des Auftretens um und betrachten in diesem Fall die ersten 15. Dies ergibt:

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Komprimieren von DataSets für Azure Machine Learning

Nachdem wir die DataSets untersucht und aufgezeigt haben, wie wir diese Art der Untersuchung für beliebige Variablen (einschließlich Kombinationen) durchführen können, komprimieren wir nun die DataSets, damit wir in Azure Machine Learning Modelle erstellen können. Denken Sie an unser Hauptthema: Wir sagen anhand von Beispielattributen (Funktionswerte von "Col2" bis "Col40") vorher, ob "Col1" den Wert "0" (kein Klick) oder "1" (Klick) ergibt.

Um unsere Trainings- und Test-DataSets auf 1 % der ursprünglichen Größe zu komprimieren, verwenden wir die systemeigene Hive-Funktion "RAND()". Dies wird vom folgenden Skript für das Trainings-DataSet übernommen: [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql).

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Dies ergibt:

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

Das Skript [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) gilt für die Testdaten für "day_22":

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Dies ergibt:

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


Und abschließend verfügen wir für die Testdaten für "day_23" über das Skript [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql):

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Dies ergibt:

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

Hiermit können wir unsere komprimierten Trainings- und Test-DataSets nun zum Erstellen von Modellen in Azure Machine Learning einsetzen.

Bevor wir in Azure Machine Learning fortfahren, gilt es, eine letzte wichtige Komponente in Bezug auf die Zahlentabelle zu beachten. Im nächsten Unterabschnitt wird diese ausführlich beschrieben.

##<a name="count"></a> Eine kurze Erläuterung der Zahlentabelle

Wie wir gesehen haben, sind mehrere Kategorievariablen äußerst umfangreich. In dieser exemplarischen Vorgehensweise stellen wir die leistungsfähige Technik [Lernen durch Anzahl](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) vor, mit der diese Variablen effizient und stabile Weise codiert werden. Weitere Informationen zu dieser Technik finden Sie über den angegebenen Link.

**Hinweis:** In dieser exemplarischen Vorgehensweise konzentrieren wir uns auf die Verwendung von Zahlentabellen zum Erstellen kompakter Darstellungen umfangreicher Kategoriefunktionen. Dies ist sicher nicht die einzige Möglichkeit, Kategoriefunktionen zu codieren. Interessierte Benutzer können sich über anderen Verfahren wie [One-hot-encoding](http://en.wikipedia.org/wiki/One-hot) und [Feature hashing](http://en.wikipedia.org/wiki/Feature_hashing) (beide Artikel in englischer Sprache) infomieren.

Um anhand der Zahlendaten Zahlentabellen zu erstellen, verwenden wir die Daten im Ordner "raw/count". Im Abschnitt "Modellierung" erfahren Sie, wie diese Zahlentabellen für Kategoriefunktionen von Grund auf neu erstellt, oder wie optional vorgefertigte Zahlentabellen für Untersuchungen eingesetzt werden. Im folgenden bedeutet "vordefinierte Zahlentabellen", dass wir die von uns bereitgestellten Zahlentabellen verwenden. Im Folgenden finden Sie ausführliche Anweisungen für den Zugriff auf diese Tabellen.

## <a name="aml"></a> Erstellen eines Modells mit Azure Machine Learning

Beim Erstellen von Modellen in Azure Machine Learning führen wir folgende Schritte aus:

1. [Abrufen der Daten aus Hive-Tabellen in Azure Machine Learning](#step1)
2. [Erstellen des Experiments: Bereinigen der Daten, Auswählen eines Lerners und Ausstatten mit Funktionen mithilfe von Zahlentabellen](#step2)
3. [Modelltraining](#step3)
4. [Abstimmen des Modells anhand von Testdaten](#step4)
5. [Bewerten des Modells](#step5)
6. [Veröffentlichen des Modells als zu verwendender Webdienst](#step6)

Wir sind nun bereit, in Azure Machine Learning Studio Modelle zu erstellen. Unsere komprimierten Daten wurden als Hive-Tabellen im Cluster gespeichert. Zum Lesen dieser Daten verwenden wir das Azure Machine Learning Reader-Modul. Im Folgenden finden Sie die Anmeldeinformationen für den Zugriff auf das Speicherkonto für diesen Cluster.

### <a name="step1"></a> Schritt 1: Abrufen von Daten aus den Hive-Tabellen in Azure Machine Learning mit dem Reader-Modul und Auswählen für ein Computerexperiment

Die für das **Reader**-Modul in der Grafik festgelegten Parameterwerte sind lediglich Beispiele. Im Folgenden finden Sie eine allgemeine Anleitung zum "Ausfüllen" der für das **Reader**-Modul festgelegten Parameter.

1. Auswählen von "Hive-Abfrage" als Datenquelle
2. In der "Hive-Abfrage" reicht ein einfaches "SELECT * FROM <ihr_datenbankname.ihr_tabellenname>" aus.
3. Hcatalog-Server-URI: Wenn Ihr Cluster "abc" ist, dann gilt: https://abc.azurehdinsight.net
4. Hadoop-Benutzerkontoname: Der bei der Bereitstellung des Clusters ausgewählte Benutzername (NICHT der RAS-Benutzername)
5. Hadoop-Benutzerkonto-Kennwort: Das bei der Bereitstellung des Clusters ausgewählte Kennwort (NICHT das RAS-Kennwort)
6. Speicherort der Ausgabedaten: Wählen Sie "Azure" aus.
7. Azure-Speicherkontoname: Das dem Cluster zugeordnete Speicherkonto
8. Azure-Speicherkontoschlüssel: Der dem Cluster zugeordnete Speicherschlüssel
9. Azure-Containername: Wenn der Clustername "abc" ist, gilt in der Regel einfach "abc" 

So sieht der **Reader** beim Abrufen von Daten aus der Hive-Tabelle aus:

![Reader erhält Daten](http://i.imgur.com/i3zRaoj.png)

Sobald der **Reader** das Abrufen von Daten beendet (das grüne Häkchen für das Modul wird angezeigt), speichern Sie diese Daten als DataSet (mit einem Namen Ihrer Wahl). Dies sieht folgendermaßen aus:

![Reader speichert Daten](http://i.imgur.com/oxM73Np.png)


Klicken Sie mit der rechten Maustaste auf den Ausgabeport des **Reader**-Moduls. Es werden die Optionen **Als Dataset speichern** und **Visualisieren** angezeigt. Wenn die Option **Visualisieren** ausgewählt wurde, werden 100 Datenzeilen sowie rechts eine für einige zusammenfassende Statistiken nützliche Leiste angezeigt. Um Daten zu speichern, wählen Sie einfach **Als Dataset speichern** aus, und befolgen Sie die Anweisungen.

Um das gespeicherte DataSet für die Verwendung in einem Machine Learning-Experiment zu verwenden, suchen Sie die DataSets mithilfe der unten dargestellten **Suche**. Geben Sie dann einfach teilweise den Namen des DataSets ein, um darauf zuzugreifen, und ziehen es in den Hauptbereich. Durch das Ziehen in den Hauptbereich wird es zur Verwendung für die Machine Learning-Modellierung ausgewählt.

![DataSet suchen](http://i.imgur.com/rx4nnUH.png)

Dies gilt sowohl für Trainings- als auch Test-DataSets.

### <a name="step2"></a> Schritt 2: Erstellen eines einfachen Experiments in Azure Machine Learning, um Klicks/keine Klicks vorherzusagen

Zunächst stellen wir eine einfache Experimentarchitektur vor, um anschließend etwas spezifischer zu werden. Zu Beginn bereinigen wir die Daten. Anschließend wählen wir einen Lerner aus und zeigen auf, wie wir diesen mit Funktionen ausstatten. Hierzu werden entweder vorgefertigte oder vom Benutzer von Grund auf neu erstellte Zahlentabellen verwendet.

![Experimentarchitektur](http://i.imgur.com/R4iTLYi.png)

Um einen Drilldown auszuführen, beginnen wir wie dargestellt mit unseren gespeicherten DataSets.

Das anpassbare **Clean Missing Data**-Modul bereinigt fehlende Daten. In diesem Modul sehen wir dies:

![Fehlende Daten bereinigen](http://i.imgur.com/0ycXod6.png)

Hier haben wir alle fehlenden Werte durch "0" ersetzt. Es gibt weitere Optionen, die mithilfe der Dropdownlisten im Modul angezeigt werden.

Als Nächstes müssen wir einen Lerner auswählen. Als Lerner verwenden wir eine verstärkte Entscheidungsstruktur mit zwei Klassen. Wir möchten insbesondere aufzeigen, wie Zahlenfunktionen für umfangreiche Kategoriefunktionen zum Erstellen von kompakten Darstellungen unseres Modell sowie für effiziente Schulungen und Tests eingesetzt werden können.

An dieser Stelle sollte geklärt werden, dass es sich bei den Zahlentabellen tatsächlich um klassenbedingte Zahlen (manchmal nennen wir sie einfach nur "bedingte Zahlen") handelt. Letztlich handelt es sich um eine Möglichkeit, die Werte einer Funktion für die einzelnen Klassen zu zählen und diese Zählungen zum Berechnen von Protokollvorhersagen zu verwenden.


#### Zugreifen auf die vorgefertigten Zahlentabellen für die Modellierung

Um auf unsere vorgefertigten Zahlentabellen zuzugreifen, klicken Sie wie unten dargestellt auf **Katalog**:

![Katalog](http://i.imgur.com/TsWkig3.png)

Durch Klicken auf **Katalog** gelangen Sie zu einer Seite, die folgendermaßen aussieht:

![Katalogstartseite](http://i.imgur.com/dmXo0KR.png)

Suchen sie hier nach dem Begriff "criteo counts", und scrollen Sie in der Ergebnisliste nach unten. Folgendes sollte angezeigt werden:

![Criteo-Zahlen](http://i.imgur.com/JZ119Jf.png)

Durch Klicken auf dieses Experiment gelangen Sie zu einer Seite, die folgendermaßen aussieht:

![Zahlen](http://i.imgur.com/dxdjMjh.png)

Klicken Sie hier auf **Open in Studio**, um das Experiment in Ihren Arbeitsbereich zu kopieren. Hierbei werden auch die DataSets automatisch kopiert. In diesem Fall sind die beiden interessanten DataSets die Zahlenltabelle und die Zahlenmetadaten, auf die wir noch ausführlicher eingehen.

#### Zählfunktionen im DataSet

Die nächsten Module des Experiments umfassen die Verwendung von vorgefertigten Zahlentabellen. Um diese vordefinierten Zahlentabellen zu verwenden, suchen Sie auf der Registerkarte "Suchen" eines neuen Experiments nach "Cr_count_". Es sollten zwei DataSets angezeigt werden: "Cr_count_cleanednulls_metadata" und "Cr_count_table_cleanednulls". Verschieben Sie beide per Drag&Drop nach rechts in den Experimentbereich. Wenn wir mit der rechten Maustaste auf die Ausgangsports klicken, können wir diese wie immer visuell darstellen.

Die Visualisierung der Zahlentabellen-Metadaten sieht folgendermaßen aus:

![Zahlentabellen-Metadaten](http://i.imgur.com/A39PIe7.png)

Beachten Sie, dass die Metadaten Informationen darüber enthalten, auf welchen Spalten die bedingten Zahlen beruhen, ob zum Erstellen ein Wörterbuch verwendet wurde (eine Alternative ist die Skizze "count-min"), welcher Hashstartwert verwendet wurde, welche Anzahl von Hashsbits zum Hashen der Funktionen verwendet wurde, welche Menge von Klassen vorliegt und dergleichen mehr.

Die Visualisierung der Zahlentabelle sieht folgendermaßen aus:

![Zahlentabelle](http://i.imgur.com/NJn1EQO.png)

Wir erkennen, dass die Zahlentabelle Informationen zu den klassenbedingten Zählungen enthält. Der Wert der Kategoriefunktionen befindet sich im "Hashwert", sodass die Funktionen selbst gehasht werden.

Wie werden die Zahlenfunktionen in die DataSets integriert? Hierzu verwenden wir das **Featurizer**-Zählmodul (siehe unten):

!["Count Featurizer"-Modul](http://i.imgur.com/dnMdrR1.png)

Nach dem Erstellen der Zahlentabelle (denken Sie daran, dass hier klassenbedingte Zahlen von Kategoriefunktionen erstellt werden) erstellen wir mit dem oben dargestellten Modul **Count Featurizer** diese Zählfunktionen in unserem DataSet. Wie wir sehen, können wir mit dem **Featurizer**-Modul auswählen, über welche Funktionen gezählt werden soll oder ob wir nur die Protokollvorhersagen oder zudem die Zählungen benötigen. Zudem gibt es andere erweiterte Optionen.

#### Zahlentabelle von Grund auf neu erstellen

Denken Sie daran, dass in der Kurzerläuterung zu den Zahlentabellen erwähnt wurde, dass neben vorgefertigten Zahlentabellen (die in den vorherigen Abschnitten ausführlich besprochen wurden), die Benutzer auch eigene Zahlentabellen von Grund auf neu erstellen können.

In diesem Abschnitt wird aufgezeigt, wie Sie eine Zahlentabelle von Grund auf neu erstellen. Hierzu verwenden wir das unten dargestellte Modul **Build Count Table** mit den entsprechenden Einstellungen:

!["Build Count Table"-Modul](http://i.imgur.com/r7pP8Qq.png)

Der letzte Teil der Einstellungen für dieses Modul folgt:

![Einstellungen für das "Build Count Table"-Modul](http://i.imgur.com/PvmSh3C.png)


**Wichtiger Hinweis:** Verwenden Sie für die Cluster- und Speicherkontoeinstellungen die für Sie geltenden Werte!

Wenn Sie auf **Ausführen** klicken, werden die Zahlentabellen im ausgewählten Cluster erstellt. Bei der Ausgabe handelt es sich wie bereits erwähnt um die Zahlentabelle und die zugeordneten Metadaten. Mit diesen Tabellen können wir nun das Experiment erstellen.


### <a name="step3"></a> Schritt 3: Modelltraining

Um diese Option auszuwählen, geben Sie einfach "two class boosted" in das Suchfeld ein, und ziehen Sie das Modul hinüber. Wir verwenden die unten angeführten Standardwerte des Lerners mit verstärkter Entscheidungsstruktur:

![BDT-Lerner](http://i.imgur.com/dDk0Jtv.png)

Vor dem Ausführen des ML-Experiments benötigen wir drei abschließende Komponenten.

Die erste ist das "Train Modell"-Modul. Dessen erster Anschluss ist der Lernereingang, und der zweite gilt für das Trainings-DataSet zum Lernen. Die wird im Folgenden ebenso dargestellt wie die im Modul festzulegenden Parameter.

![Verbindungen des "BDT Train Model"-Moduls](http://i.imgur.com/szS2xBb.png)

![Einstellungen des "BDT Train Model"-Moduls](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> Schritt 4: Bewerten des Modells anhand eines Test-DataSets

Bei der zweiten Komponente handelt es sich um eine Möglichkeit, das Test-DataSet abzustimmen. Dies geschieht einfach mithilfe des **Score Model**-Moduls, das als Eingabe das anhand der Trainingsdaten erlernte Modell aufweist und beruhend auf dem Test-DataSet Vorhersagen trifft. Im Folgenden wird gezeigt, wie dies aussieht.

![Verbindungen des Abstimmungs-BDT-Modells](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a> Schritt 5: Bewerten des Modells

Abschließend möchten wir die Leistung des Modells ermitteln. Wenn bei zwei Klassen (binäre) Klassifizierungsprobleme auftreten, eignet sich AUC gewöhnlich als Maßstab. Um dies zu veranschaulichen, ordnen wir das Modul "Score Model" dem Modul "Evaluate Model" zu. Wenn Sie im Modul **Evaluate Model** auf **Visualisieren** klicken, ergibt dies ungefähr folgende Grafik:

!["Evaluate"-Modul für BDT-Modell](http://i.imgur.com/0Tl0cdg.png)

Bei binären (oder zweiklassigen) Klassifizierungsproblemen, eignet sich AUC als guter Maßstab für die Vorhersagegenauigkeit. Im Folgenden finden Sie unsere Ergebnisse dieses Modells für unser Test-DataSet. Um diese zu erhalten, klicken mit der rechten Maustaste auf den Ausgangsport des Moduls **Evaluate Model** und anschließend auf **Visualisieren**.

!["Visualize Evaluate Model"-Modul](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> Schritt 6: Veröffentlichen des Modells als Webdienst
Es ist von besonderem Interesse, Machine Learning-Modelle als Webdienste veröffentlichen zu können. Sobald dies erfolgt ist, können wir mit den Daten, für die wir Vorhersagen benötigen, Aufrufe an den Webdienst durchführen. Das Modell gibt dann im Idealfall eine wie auch immer geartete Vorhersage zurück.

Zu diesem Zweck speichern wir unser trainiertes Modell zunächst als "Trained Model"-Objekt. Hierzu klicken wir mit der rechten Maustaste auf das Modul **Train Model**. Anschließend wählen wir die Option "Save as Trained Model" aus.

Als Nächstes möchten wir einen E/A-Port für den Webdienst erstellen. Hierbei empfängt der Eingangsport Daten in der gleichen Form wie die Daten, die wir für Vorhersagen benötigen, während der Ausgangsport die bewerteten Bezeichnungen und die zugehörigen Wahrscheinlichkeiten zurückgibt.

#### Auswählen einiger Datenzeilen für den Eingangsanschluss

Wir zeigen nun auf, wie nur einige wenige Datenzeilen für den Eingangsanschluss ausgewählt werden.

![Eingangsportdaten](http://i.imgur.com/XqVtSxu.png)

Bequemerweise verwenden wir eine "Apply SQL"-Transformation, um nur 10 Zeilen auszuwählen, die als Daten für den Eingangsport fungieren.

#### Webdienst
Jetzt können wir ein kleines Experiment ausführen, das wir in unserem Webdienst veröffentlichen möchten.

#### Generieren von Eingabedaten für den Webdienst

Als nullten Schritt wählen wir, da die Zahlentabelle groß ist, einige wenige Zeilen mit Testdaten aus und generieren aus diesen mit den Zählfunktionen Ausgabedaten. Diese dienen als Eingabedatenformat für unseren Webdienst. Dies wird nachfolgend gezeigt:

![BDT-Eingangsdaten erstellen](http://i.imgur.com/OEJMmst.png)

Hinweis: Für das Eingabedatenformat verwenden wir nun die AUSGABE des **Count Featurizer**-Moduls. Nach Abschluss der Ausführung dieses Experiments speichern Sie die Ausgabe des **Count Featurizer**-Moduls als DataSet. **Wichtiger Hinweis:** Dieses DataSet wird für die Eingabedaten des Webdiensts verwendet.

#### Abstimmung des Experiments für den Veröffentlichungswebdienst

Zunächst wird im Folgenden gezeigt, wie dies aussieht. Die grundlegende Struktur ist ein "Score Model"-Modul, das unsere trainierten Modellobjekte und einige Zeilen der in den vorherigen Schritten mit dem **Count Featurizer**-Modul generierten Eingabedaten akzeptiert. Wir verwenden "Project Colums" um die bewerteten Bezeichnungen sowie die Bewertungswahrscheinlichkeiten auszublenden.

![Project Columns](http://i.imgur.com/kRHrIbe.png)

Es ist interessant zu sehen, wie mit dem "Project Colums"-Modul Daten aus einem DataSet "gefiltert" werden können. Im Folgenden finden Sie die Inhalte:

![Filterung mit dem "Project Columns"-Modul](http://i.imgur.com/oVUJC9K.png)

Um die blauen E/A-Ports zu erhalten, klicken Sie einfach unten rechts auf "Prepare Webservice". Wenn wir dieses Experiment ausführen, können wir zudem den Webdienst veröffentlichen, indem wir unten rechts auf das im Folgenden dargestellte Symbol **PREPARE WEBSERVICE** klicken.

![Webdienst veröffentlichen](http://i.imgur.com/WO0nens.png)

Sobald der Webdienst veröffentlicht wurde, werden wir auf eine Seite umgeleitet, die folgendermaßen aussieht:

![](http://i.imgur.com/YKzxAA5.png)

Auf der linken Seite befinden sich zwei Links zu Webdiensten:

* Der **REQUEST/RESPONSE**-Dienst (RRS) für einzelne Vorhersagen, den wir in diesem Workshop verwenden. 
* Der **BATCH EXECUTION**-Dienst (BES) für Stapelvorhersagen, der erfordert, dass die Daten für Vorhersagen in einem Azure-Blob-Speicher gespeichert wurden.

Wenn Sie auf den Link **REQUEST/RESPONSE** klicken, gelangen Sie zu einer Seite mit vorprogrammiertem Code in C#, Python und R. Dieser Code kann problemlos für Aufrufe an den Webdienst verwendet werden. Beachten Sie, dass der API-Schlüssel auf dieser Seite für die Authentifizierung verwendet werden muss.

Es empfiehlt sich, diesen Python-Code in eine neue Zelle des iPython-Notizbuchs zu kopieren.

Im Folgenden finden Sie einen Python-Codeabschnitt mit dem richtigen API-Schlüssel.

![Python-Code](http://i.imgur.com/f8N4L4g.png)

Beachten Sie, dass wir den Standard-API-Schlüssel durch unseren Webdienst-API-Schlüssel ersetzt haben. Wenn Sie in dieser Zelle eines iPython-Notizbuchs auf "Ausführen" klicken, ergibt dies die folgende Antwort:

![iPython-Antwort](http://i.imgur.com/KSxmia2.png)

Wir sehen, dass wir für die beiden angefragten Testbeispiele (im JSON-Framework oder Python-Skript) Antworten im Format "Bewertete Bezeichnungen, bewertete Wahrscheinlichkeiten" erhalten. Beachten Sie, dass wir in diesem Fall die Standardwerte des vorab erstellten Codes ausgewählt haben (Nullen für alle numerischen Spalten, die Zeichenfolge "value" für alle Kategoriespalten).

Dies bildet den Abschluss unserer Abhandlung über die End-to-End-Handhabung umfangreicher DataSets mit Azure ML. Aus einem Terabyte an Daten haben wir ein als Webdienst in der Cloud bereitgestelltes Vorhersagemodell entwickelt.

 

<!---HONumber=July15_HO2-->