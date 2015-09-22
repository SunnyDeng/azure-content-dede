<properties 
	pageTitle="Advanced Analytics Process and Technology (ADAPT) in Aktion: Verwenden von Hadoop-Clustern | Microsoft Azure"
	description="Verwenden von ADAPT (Advanced Analytics Process and Technology) für ein End-to-End-Szenario mit einem HDInsight Hadoop-Cluster zum Entwickeln und Bereitstellen eines Modells unter Verwendung eines öffentlich zugänglichen Datasets."
	services="machine-learning,hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="hangzh;bradsev"/>

                
# Advanced Analytics Process and Technology (ADAPT) in Aktion – Verwenden von HDInsight-Hadoop-Clustern

In dieser exemplarischen Vorgehensweise nutzen Sie ADAPT (Advanced Analytics Process and Technology) in einem End-to-End-Szenario mit einem [Azure HDInsight Hadoop-Cluster](http://azure.microsoft.com/services/hdinsight/), um Daten aus dem öffentlich zugänglichen [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/)-Dataset zu speichern und zu untersuchen, um Funktionen zu entwickeln und die Daten zu komprimieren. Modelle der Daten werden mit Azure Machine Learning entwickelt, um eine binäre Klassifizierung und eine Multiklassenklassifizierung sowie Regressionsvorhersageaufgaben durchzuführen.

Eine exemplarische Vorgehensweise, die zeigt, wie ein größeres Dataset (mit 1 TB) für ein ähnliches Szenario mit Verwendung von HDInsight Hadoop-Clustern für die Datenverarbeitung nutzt, finden Sie unter [Advanced Analytics Process and Technology (ADAPT) in Aktion – Verwenden von Azure HDInsight Hadoop-Clustern in einem 1-TB-Dataset](machine-learning-data-science-process-hive-criteo-walkthrough.md).

Es ist auch möglich, die in der exemplarischen Vorgehensweise mit 1-TB-Dataset vorgestellten Aufgaben mit einem IPython Notebook umzusetzen. Benutzer, die diesem Ansatz folgen möchten, sollten das Thema [Criteo walkthrough using a Hive ODBC connection](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) (in englischer Sprache) beachten.


## <a name="dataset"></a>Beschreibung des NYC Taxi Trips-Datasets

Die NYC Taxi Trips-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (~48 GB unkomprimiert) mit mehr als 173 Millionen einzelnen Fahrten mit den zugehörigen Preisen. Jeder Fahrtendatensatz enthält den Start- und Zielort mit der Uhrzeit, die anonymisierte Lizenznummer des Fahrers (Hack) und die eindeutige ID des Taxis (Medallion). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden Datasets bereitgestellt:

1. Die CSV-Dateien "trip\_data" enthalten Fahrtendetails, z. B. die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Die CSV-Dateien "trip\_fare" enthalten Details zu den Kosten für jede Fahrt, beispielsweise Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den entrichteten Gesamtbetrag. Es folgen einige Beispieleinträge:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Der eindeutige Schlüssel für die Zusammenführung von "trip\_data" und "trip\_fare" besteht aus den Feldern: "medallion", "hack\_licence" und "pickup\_datetime".

Um alle relevanten Detailinformationen zu einer bestimmten Fahrt abzurufen, reicht eine Zusammenführung mit drei Schlüsseln: "medallion", "hack\_license" und "pickup\_datetime".

Weitere Details der Daten werden beschrieben, wenn wir diese in Hive-Tabellen speichern.

## <a name="mltasks"></a>Beispiele für Vorhersageaufgaben
Bei der Arbeit mit Daten ist es zunächst wichtig, die Art der Vorhersagen zu bestimmen, die Sie treffen möchten. Basierend auf der Analyse können Sie dann die Aufgaben ermitteln, die Sie durchführen müssen. Hier sehen Sie drei Beispiele für Vorhersageprobleme, die in dieser exemplarischen Vorgehensweise behandelt werden. Die Formulierung basiert auf *tip\_amount*:

1. **Binäre Klassifizierung**: Vorhersage, ob ein Trinkgeld bezahlt wurde. Hierbei repräsentiert ein *tip\_amount* von mehr als 0 $ eine positive Probe, während ein *tip\_amount* mit dem Wert 0 $ eine negative Probe darstellt.

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0

2. **Multiklassenklassifizierung**: Vorhersage des Trinkgeldbereichs für die Fahrt. Wir teilen *tip\_amount* in fünf Container oder Klassen auf:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für die Fahrt.


## <a name="setup"></a>Einrichten eines HDInsight Hadoop-Clusters für die erweiterte Analyse

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Administrator** ausgeführt.

Sie können in drei Schritten eine Azure-Umgebung für die erweiterte Analyse einrichten, in der ein HDInsight-Cluster verwendet wird:

1. [Erstellen eines Speicherkontos:](../storage-whatis-account.md) Mit diesem Speicherkonto werden Daten im Azure-Blobspeicher gespeichert. Die in HDInsight-Clustern verwendeten Daten werden ebenfalls hier gespeichert.

2. [Anpassen von Azure HDInsight Hadoop-Clustern für erweiterte Analyseprozesse und -technologien](machine-learning-data-science-customize-hadoop-cluster.md) In diesem Schritt erstellen Sie einen Azure HDInsight Hadoop-Cluster, bei dem auf allen Knoten 64-Bit-Anaconda Python 2.7 installiert ist. Bei der Anpassung Ihres HDInsight-Clusters müssen Sie an zwei wichtige Schritte denken.

	* Verknüpfen Sie das in Schritt 1 erstellte Speicherkonto mit dem HDInsight-Cluster, wenn Sie diesen erstellen. Mit diesem Speicherkonto wird auf Daten zugegriffen, die innerhalb des Clusters verarbeitet werden.
	
	* Sie müssen nach dem Erstellen den Remotezugriff auf den Hauptknoten des Clusters aktivieren. Navigieren Sie zur Registerkarte **Konfiguration**, und klicken Sie auf **Remote aktivieren**. Mit diesem Schritt werden die Benutzeranmeldeinformationen für die Remoteanmeldung angegeben.

3. [Erstellen eines Azure Machine Learning-Arbeitsbereichs](machine-learning-create-workspace.md): Dieser Azure Machine Learning-Arbeitsbereich wird zum Entwickeln von Machine Learning-Modellen verwendet. Diese Aufgabe wird ausgeführt, nachdem Sie mit dem HDInsight-Cluster eine anfängliche Datenuntersuchung und -komprimierung durchgeführt haben.

## <a name="getdata"></a>Abrufen der Daten aus der öffentlichen Quelle

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Administrator** ausgeführt.

Sie können zum Abrufen des Datasets [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) von seinem öffentlichen Speicherort eine der in [Verschieben von Daten in und aus Azure-Blobspeichern](machine-learning-data-science-move-azure-blob.md) beschriebenen Methoden verwenden, um die Daten auf Ihren Computer zu kopieren.

Nachfolgend wird beschrieben, wie Sie AzCopy zum Übertragen der Dateien verwenden, die Daten enthalten. Folgen Sie den Anweisungen unter [Erste Schritte mit dem Befehlszeilenprogramm AzCopy](../storage-use-azcopy.md), um AzCopy herunterzuladen und zu installieren.

1. Führen Sie in einem Eingabeaufforderungsfenster die folgenden AzCopy-Befehle aus, und ersetzen Sie *<path_to_data_folder>* durch den gewünschten Speicherort:

		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. Nach Abschluss des Kopiervorgangs befinden sich insgesamt 24 gezippte Dateien im ausgewählten Datenordner. Entzippen Sie die heruntergeladenen Dateien in dasselbe Verzeichnis auf Ihrem lokalen Computer. Notieren Sie sich den Ordner mit den extrahierten Dateien. Sie verweisen nachfolgend mit *<path\_to\_unzipped\_data\_files>* auf diesen Ordner.


## <a name="upload"></a>Hochladen der Daten in den Standardcontainer des Azure HDInsight Hadoop-Clusters

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Administrator** ausgeführt.

Ersetzen Sie in den folgenden AzCopy-Befehlen die folgenden Parameter durch die tatsächlichen Werte, die Sie beim Erstellen des Hadoop-Clusters und beim Entzippen der Dateien angegeben haben.

* ***&#60;path\_to\_data\_folder>***: Das Verzeichnis (einschließlich Pfad) auf Ihrem Computer, das die extrahierten Datendateien enthält.  
* ***&#60;storage account name of Hadoop cluster>***: Das Ihrem HDInsight-Cluster zugeordnete Speicherkonto.
* ***&#60;default container of Hadoop cluster>***: Der von Ihrem Cluster verwendete Standardcontainer. Beachten Sie, dass der Name des Standardcontainers üblicherweise mit dem des Clusters übereinstimmt. Wenn der Cluster beispielsweise den Namen "abc123.azurehdinsight.net" verwendet, heißt der Standardcontainer "abc123".
* ***&#60;storage account key>***: Der Schlüssel für das von Ihrem Cluster verwendete Speicherkonto.

Führen Sie von einer Eingabeaufforderung oder einem Windows PowerShell-Fenster auf Ihrem Computer die folgenden AzCopy-Befehle aus.

Dieser Befehl lädt die Fahrtdaten in das Verzeichnis ***nyctaxitripraw*** im Standardcontainer des Hadoop-Clusters.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Dieser Befehl lädt die Fahrpreisdaten in das Verzeichnis ***nyctaxifareraw*** im Standardcontainer des Hadoop-Clusters.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Die Daten sollten jetzt im Azure-Blobspeicher vorliegen und zur Verarbeitung im HDInsight-Cluster genutzt werden können.

## <a name="#download-hql-files"></a>Melden Sie sich beim Hauptknoten des Hadoop-Clusters an, und bereiten Sie eine explorative Datenanalyse vor.

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Administrator** ausgeführt.

Um für eine explorative Datenanalyse und eine Komprimierung der Daten auf den Hauptknoten des Clusters zuzugreifen, folgen Sie den Anweisungen unter [Zugreifen auf den Hauptknoten des Hadoop-Clusters](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

In dieser exemplarischen Vorgehensweise werden vorwiegend in [Hive](https://hive.apache.org/) geschriebene Abfragen verwendet. Hive ist eine SQL-ähnliche Abfragesprache zur Durchführung von Vorabdatenuntersuchungen. Die Hive-Abfragen werden in HQL-Dateien gespeichert. Anschließend komprimieren wir diese Daten, um sie für die Modellentwicklung in Azure Machine Learning zu nutzen.

Um den Cluster auf die explorative Datenanalyse vorzubereiten, laden wird die HQL-Dateien mit den relevanten Hive-Skripts aus [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) in ein lokales Verzeichnis (C:\\temp) auf dem Hauptknoten herunter. Hierzu öffnen wir auf dem Hauptknoten im Cluster die **Eingabeaufforderung** und führen die folgenden zwei Befehle aus:

	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Mit diesen zwei Befehlen werden alle für diese exemplarische Vorgehensweise benötigten HQL-Dateien in das lokale Verzeichnis ***C:\\temp&#92;*** auf dem Hauptknoten heruntergeladen.

## <a name="#hive-db-tables"></a>Erstellen von Hive-Datenbank und Tabellen partitioniert nach Monat

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Administrator** ausgeführt.

Wir können jetzt Hive-Tabellen für das NYC Taxi-Dataset erstellen. Öffnen Sie auf dem Desktop des Hauptknotens die ***Hadoop-Befehlszeile***, und wechseln Sie mithilfe des folgenden Befehls zum Hive-Verzeichnis.

    cd %hive_home%\bin

>[AZURE.NOTE] **Führen Sie alle Hive-Befehle dieser exemplarischen Vorgehensweise über die Eingabeaufforderung im oben angeführten Hive-Verzeichnis "bin/" aus. So werden alle Pfadprobleme automatisch behoben. Die hier verwendeten Begriffe "Hive-Eingabeaufforderung", "Hive-Eingabeaufforderung im bin-Verzeichnis" und "Hadoop-Befehlszeile" sind austauschbar.**

Geben Sie auf dem Hauptknoten von der Hive-Eingabeaufforderung den folgenden Befehl in der Hadoop-Befehlszeile ein, um die Hive-Abfrage zum Erstellen von Hive-Datenbank und -Tabellen zu senden:
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Dies ist der Inhalt der Datei ***C:\\temp\\sample\_hive\_create\_db\_and\_tables.hql***, die die Hive-Datenbank ***nyctaxidb*** und die Tabellen ***trip*** und ***fare*** erstellt.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Dieses Hive-Skript erstellt zwei Tabellen:

* Die Tabelle "trip" enthält Details zu jeder Fahrt (Fahrerinformationen, Abholzeit, Fahrtstrecke und Zeiten). 
* Die Tabelle "fare" enthält Details zum Fahrpreis (Betrag, Trinkgeld, Mautgebühren und Zuschläge).

Wenn Sie weitere Hilfe bei diesen oder anderen Verfahren benötigen, oder wenn Sie alternative Verfahren untersuchen möchten, lesen Sie den Abschnitt [Übermitteln von Hive-Abfragen direkt von der Hadoop-Befehlszeile](machine-learning-data-science-process-hive-tables.md#submit).

## <a name="#load-data"></a>Laden von Daten in Hive-Tabellen nach Partitionen

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Administrator** ausgeführt.

Das NYC Taxi-Dataset ist bereits nach Monat partitioniert, was wir für eine schnellere Verarbeitung und Abfrage nutzen. Die nachstehenden PowerShell-Befehle (ausgegeben über das Hive-Verzeichnis mithilfe der **Hadoop-Befehlszeile**) laden Daten in die nach Monat partitionierten Hive-Tabellen "trip" und "fare".

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Die Datei *sample\_hive\_load\_data\_by\_partitions.hql* enthält die folgenden **LOAD**-Befehle.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Beachten Sie, dass verschiedene der im Analyseverfahren verwenden Hive-Abfragen nur eine einzelne Partition oder einige wenige Partitionen untersuchen. Diese Abfragen könnten jedoch auch über die gesamten Daten ausgeführt werden.

### <a name="#show-db"></a>Anzeigen von Datenbanken im HDInsight Hadoop-Cluster

Um die in HDInsight Hadoop-Clustern erstellten Datenbanken im Hadoop-Befehlszeilenfenster anzuzeigen, führen Sie folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -e "show databases;"

### <a name="#show-tables"></a>Anzeigen der Hive-Tabellen in der Datenbank "nyctaxidb" 
	
Führen Sie zum Anzeigen der Tabellen in der Datenbank "nyctaxidb" folgenden Befehl an der Hadoop-Befehlszeile aus:
	
	hive -e "show tables in nyctaxidb;"

Wir können mithilfe des folgenden Befehls bestätigen, dass die Tabellen partitioniert sind:

	hive -e "show partitions nyctaxidb.trip;"

Nachfolgend sehen Sie die erwartete Ausgabe:

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 2.075 seconds, Fetched: 12 row(s)
	
Auf ähnliche Weise können wir über den folgenden Befehl sicherstellen, dass die Fahrpreistabelle partitioniert ist:

	hive -e "show partitions nyctaxidb.fare;"

Nachfolgend sehen Sie die erwartete Ausgabe:

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 1.887 seconds, Fetched: 12 row(s)
   
## <a name="#explore-hive"></a>Untersuchen von Daten und Entwickeln von Features in Hive

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Das Untersuchen von Daten und das Entwickeln von Funktionen für die in die Hive-Tabellen geladenen Daten kann mithilfe von Hive-Abfragen durchgeführt werden. Dies sind einige Beispiele für solche Aufgaben, die wir in diesem Abschnitt bearbeiten:

- Anzeigen der ersten 10 Datensätze in beiden Tabellen
- Untersuchen der Datenverteilungen einiger Felder in unterschiedlichen Zeitfenstern
- Überprüfen der Datenqualität der Felder "longitude" und "latitude"
- Generieren Sie Bezeichner für die binäre und Multiklassenklassifizierung auf der Grundlage von **tip\_amount**.
- Generieren von Funktionen durch Berechnung der direkten Fahrtentfernungen

### Untersuchung: Anzeigen der ersten 10 Datensätze in der Tabelle "trip"

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Um einen Eindruck der Daten zu erhalten, untersuchen wir die ersten 10 Datensätze jeder Tabelle. Führen Sie die folgenden zwei Abfragen einzeln von der Hive-Eingabeaufforderung in der Hadoop-Befehlszeilenkonsole aus, um die Datensätze zu überprüfen.

Abrufen der ersten 10 Datensätze in der Tabelle "trip" für den ersten Monat:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
Abrufen der ersten 10 Datensätze in der Tabelle "fare" für den ersten Monat:
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Es ist häufig nützlich, die Datensätze zur bequemen Anzeige in einer Datei zu speichern. Eine kleine Änderung an der obigen Abfrage bewirkt Folgendes:

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### Untersuchung: Anzeigen der Anzahl von Datensätzen in jeder der 12 Partitionen

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Interessant ist, wie die Anzahl von Fahrten im Laufe des Kalenderjahrs variiert. Eine Gruppierung nach Monat ermöglicht es uns, die Verteilung der Fahrten anzuzeigen.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Wir erhalten die folgende Ausgabe:

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 283.406 seconds, Fetched: 12 row(s)

Hier steht die erste Spalte für den Monat, die zweite Spalte gibt die Anzahl von Fahrten für diesen Monat an.

Wir können außerdem die Gesamtzahl der Datensätze im trip-Dataset berechnen, indem wir den folgenden Befehl an der Hive-Eingabeaufforderung ausführen:

	hive -e "select count(*) from nyctaxidb.trip;"

Dies ergibt:

	173179759
	Time taken: 284.017 seconds, Fetched: 1 row(s)

Mithilfe ähnlicher Befehle wie denen für das trip-Dataset können wir Hive-Abfragen von der Eingabeaufforderung im Hive-Verzeichnis ausführen, um die Anzahl von Datensätzen im fare-Dataset zu ermitteln.

	hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Wir erhalten die folgende Ausgabe:

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 253.955 seconds, Fetched: 12 row(s)

Beachten Sie, dass die zurückgegebene Anzahl von Fahrten pro Monat für beide Datasets exakt übereinstimmt. Dies liefert den ersten Beweis, dass die Daten korrekt geladen wurden.

Die Gesamtzahl der Datensätze im fare-Dataset kann mithilfe des folgenden Befehls von der Hive-Eingabeaufforderung ermittelt werden:

	hive -e "select count(*) from nyctaxidb.fare;"

Dies ergibt:

	173179759
	Time taken: 186.683 seconds, Fetched: 1 row(s)

Die Gesamtzahl der Datensätze in beiden Tabellen stimmt ebenfalls überein. Dies liefert einen zweiten Beweis dafür, dass die Daten korrekt geladen wurden.

### Untersuchung: Verteilung der Fahrten nach "medallion"

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

In diesem Beispiel wird die Taxinummer ("medallion") mit mehr als 100 Fahrten innerhalb eines bestimmten Zeitraums ermittelt. Die Abfrage profitiert vom Zugriff auf die partitionierte Tabelle, da sie von der Partitionsvariable **month** abhängig ist. Die Abfrageergebnisse werden in eine lokale Datei namens "queryoutput.tsv" im Verzeichnis `C:\temp` auf dem Hauptknoten geschrieben.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Dies ist der Inhalt der Datei *sample\_hive\_trip\_count\_by\_medallion.hql*, der untersucht wird.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

Über "medallion" wird im NYC Taxi-Dataset ein Taxi eindeutig identifiziert. Wir können ermitteln, welche Taxis besonders häufig zum Einsatz kommen, indem wir abfragen, welche Taxis innerhalb einer bestimmten Zeit mehr als eine bestimmte Anzahl von Fahrten durchgeführt haben. Im folgenden Beispiel werden Taxis ermittelt, die in den ersten drei Monaten mehr als 100 Fahrten durchgeführt haben. Die Ergebnisse der Abfrage werden in einer lokalen Datei namens "C:\\temp\\queryoutput.tsv" gespeichert.

Dies ist der Inhalt der Datei *sample\_hive\_trip\_count\_by\_medallion.hql*, der untersucht wird.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### Untersuchung: Verteilung der Fahrten nach "medallion" und "hack\_license"

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Beim Untersuchen eines Datasets wird häufig das Zusammentreffen von Gruppen von Werten geprüft. Der vorliegende Abschnitt zeigt anhand eines Beispiels, wie diese Untersuchung für Taxis und Fahrer durchgeführt werden kann.

In der Datei *sample\_hive\_trip\_count\_by\_medallion\_license.hql* wird das fare-Dataset nach "medallion" und "hack\_license" gruppiert, und es wird die Anzahl jeder Kombination zurückgegeben. Nachfolgend werden die Inhalte gezeigt.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

Diese Abfrage gibt bestimmte Kombinationen aus Taxi und Fahrer zurück, geordnet nach der Anzahl von Fahrten (in absteigender Reihenfolge).

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Die Abfrageergebnisse werden in eine lokale Datei namens "C:\\temp\\queryoutput.tsv" geschrieben.

### Untersuchung: Bewerten der Datenqualität durch Prüfen auf Datensätze mit ungültigem Längen-/Breitengrad

>[AZURE.NOTE] Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Eine allgemeines Ziel bei der explorativen Datenanalyse besteht darin, ungültige oder falsche Datensätze auszusortieren. Im Beispiel in diesem Abschnitt wird ermittelt, ob Werte in den Feldern für Längen- oder Breitengrad außerhalb des Bereichs von New York liegen. Da es wahrscheinlich ist, dass solche Datensätze einen falschen Wert für den Längen-/Breitengrad enthalten, sollen diese Daten bei der Modellierung ausgeschlossen werden.

Dies ist der Inhalt der Datei *sample\_hive\_quality\_assessment.hql*, der untersucht wird.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Das *-S*-Argument in diesem Befehl unterdrückt die Ausgabe der Hive Map/Reduce-Aufträge an den Statusbildschirm. Dies ist hilfreich, da so die Bildschirmausgabe der Hive-Abfrage besser lesbar ist.

### Untersuchung: Binäre Klassenverteilung von Trinkgeldern für Fahrten

**Hinweis:** Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Für das im Abschnitt [Beispiele für Vorhersageaufgaben](machine-learning-data-science-process-hive-walkthrough.md#mltasks) beschriebene Problem der binären Klassifizierung ist es nützlich zu wissen, ob ein Trinkgeld gegeben wurde oder nicht. Die Verteilung der Trinkgelder ist binär:

* Trinkgeld erhalten (Class 1, tip\_amount > $0)  
* Kein Trinkgeld (Class 0, tip\_amount = $0) 

Dies wird in der nachstehenden Datei *sample\_hive\_tipped\_frequencies.hql* gezeigt.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### Untersuchung: Klassenverteilungen in der Multiklasseneinstellung

**Hinweis:** Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Für das im Abschnitt [Beispiele für Vorhersageaufgaben](machine-learning-data-science-process-hive-walkthrough.md#mltasks) beschriebene Problem der Multiklassenklassifizierung eignet sich dieses Dataset für eine natürliche Klassifizierung, wenn Sie den Betrag der Trinkgelder vorhersagen möchten. In der Abfrage können Trinkgeldbereiche mithilfe von Containern definiert werden. Zum Abrufen der Klassenverteilungen für die verschiedenen Trinkgeldbereiche verwenden wir die Datei *sample\_hive\_tip\_range\_frequencies.hql*. Nachfolgend werden die Inhalte gezeigt.

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Führen Sie den folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### Untersuchung: Berechnen der direkten Entfernung zwischen zwei Längengrad-/Breitengradpositionen

**Hinweis:** Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Eine Messung der direkten Entfernung ermöglicht es uns, die Abweichung zur tatsächlichen Fahrtstrecke zu ermitteln. Wir begründen diese Untersuchung damit, dass ein Kunde wahrscheinlich weniger geneigt ist, ein Trinkgeld zu geben, wenn er bemerkt, dass der Fahrer absichtlich eine längere Route genommen hat.

Um den Unterschied zwischen der tatsächlichen Fahrtstrecke und der Entfernung nach der [Haversine-Formel](http://en.wikipedia.org/wiki/Haversine_formula) zwischen zwei Längengrad-/Breitengradpositionen (die Großkreisentfernung) zu ermitteln, verwenden wir die in Hive verfügbaren trigonometrischen Funktionen:

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

In der oben gezeigten Abfrage steht R für den Erdradius in Meilen, Pi ist in das Bogenmaß konvertiert. Beachten Sie, dass die Längengrad-/Breitengradpositionen "gefiltert" werden, um vom Bereich New York City zu weit entfernte Werte zu entfernen.

In diesem Fall werden die Ergebnisse in ein Verzeichnis mit dem Namen "queryoutputdir" geschrieben. Die nachfolgend gezeigte Befehlssequenz erstellt zunächst das Ausgabeverzeichnis, anschließend wird der Hive-Befehl ausgeführt.

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

	hdfs dfs -mkdir wasb:///queryoutputdir

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Die Abfrageergebnisse werden in 9 Azure-Blobs von ***queryoutputdir/000000\_0*** bis ***queryoutputdir/000008\_0*** im Standardcontainer des Hadoop-Clusters geschrieben.

Um die Größe der einzelnen Blobs anzuzeigen, führen wir den folgenden Befehl von der Hive-Eingabeaufforderung aus:

	hdfs dfs -ls wasb:///queryoutputdir

Um die Inhalte einer bestimmten Datei anzuzeigen, beispielsweise "000000\_0", verwenden wird den Hadoop-Befehl `copyToLocal`.

	hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

**Warnung** `copyToLocal` kann für große Dateien sehr langsam sein, deshalb wird eine Verwendung für große Dateien nicht empfohlen.

Das Speichern dieser Daten in einem Azure-Blob bietet den Vorteil, dass die Daten in Azure Machine Learning mit dem [Reader][reader]-Modul untersucht werden können.


## <a name="#downsample"></a>Komprimieren von Daten und Entwickeln von Modellen in Azure Machine Learning

**Hinweis:** Diese Aufgabe wird typischerweise von einem **Datenanalyst** ausgeführt.

Im Anschluss an die explorative Datenanalyse können wir die Daten komprimieren, um in Azure Machine Learning Modelle zu entwickeln. In diesem Abschnitt wird gezeigt, wie Sie eine Hive-Abfrage zum Komprimieren der Daten verwenden und anschließend mit dem [Reader][reader]-Modul in Azure Machine Learning auf die Daten zugreifen.

### Komprimieren der Daten

Dieses Verfahren umfasst zwei Schritte. Zunächst werden die Tabellen **nyctaxidb.trip** und **nyctaxidb.fare** mithilfe von drei Schlüsseln zusammengeführt, die in allen Datensätzen vorhanden sind: "medallion", "hack\_license" und "pickup\_datetime". Anschließend wird der Bezeichner **tipped** für die binäre Klassifizierung und der Bezeichner **tip\_class** für die Multiklassenklassifizierung generiert.

Um die komprimierten Daten direkt aus dem [Reader][reader]-Modul in Azure Machine Learning verwenden zu können, müssen Sie die Ergebnisse der oben gezeigten Abfrage in einer internen Hive-Tabelle speichern. Im Folgenden erstellen wir eine interne Hive-Tabelle und füllen diese mit den zusammengeführten und komprimierten Daten.

Die Abfrage verwendet standardmäßige Hive-Funktionen, um aus dem Feld "pickup\_datetime" Stunde, Woche und Wochentag (hierbei steht 1 für Montag und 7 für Sonntag) sowie die direkte Entfernung zwischen dem Startpunkt und Ziel der Fahrt zu generieren. Eine vollständige Liste solcher Funktionen finden Sie unter [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (in englischer Sprache).

Die Abfrage komprimiert anschließend die Daten, damit die Ergebnisse in Azure Machine Learning Studio verwendet werden können. Es wird nur etwa 1 % des ursprünglichen Datasets in Studio importiert.

Nachfolgend werden die Inhalte der Datei *sample\_hive\_prepare\_for\_aml\_full.hql* gezeigt, mit der Daten für die Modellentwicklung in Azure Machine Learning vorbereitet werden.
		
		set R = 3959;
	    set pi=radians(180);

		create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
		)
		row format delimited fields terminated by ','
		lines terminated by '\n'
		stored as textfile;

		--- now insert contents of the join into the above internal table

    	insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
    	select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
		t.direct_distance,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

    	from
    	(
        select 
		medallion, 
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
		${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select 
        medallion, 
        hack_license, 
        vendor_id, 
        pickup_datetime, 
        payment_type, 
        fare_amount, 
        surcharge, 
        mta_tax, 
        tip_amount, 
        tolls_amount, 
        total_amount
        from nyctaxidb.fare 
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus, um diese Abfrage auszuführen:

	hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Wir verfügen jetzt über eine interne Tabelle namens "nyctaxidb.nyctaxi\_downsampled\_dataset", auf die über das [Reader][reader]-Modul in Azure Machine Learning zugegriffen werden kann. Darüber hinaus können wir dieses Dataset zum Entwickeln von Machine Learning-Modellen nutzen.

### Verwenden Sie das Reader-Modul in Azure Machine Learning, um auf die komprimierten Daten zuzugreifen.

Als Voraussetzung für die Ausführung von Hive-Abfragen im [Reader][reader]-Modul von Azure Machine Learning benötigen wir Zugriff auf einen Azure Machine Learning-Arbeitsbereich sowie Zugriff auf die Anmeldeinformationen des Clusters und das zugeordnete Speicherkonto.

Nachfolgend werden einige Details zum [Reader][reader]-Modul sowie die Parameter für die Eingabe gezeigt:

**HCatalog-Server-URI**: Wenn der Clustername "abc123" lautet, ist dies einfach: https://abc123.azurehdinsight.net

**Hadoop-Benutzerkontoname**: Der bei der Bereitstellung des Clusters ausgewählte Benutzername (**nicht** der Benutzername für den Remotezugriff).

**Hadoop-Kontokennwort**: Das gewählte Kennwort für den Cluster (**nicht** das Kennwort für den Remotezugriff).

**Speicherort der Ausgabedaten**: Wird von Azure festgelegt.

**Azure-Speicherkontoname**: Der Name des dem Cluster zugeordneten standardmäßigen Speicherkontos.

**Azure-Containername**: Dies ist der standardmäßige Containername für den Cluster. Dieser stimmt üblicherweise mit dem Clusternamen überein. Wenn der Cluster "abc123" heißt, lautet dieser Name "abc123".

**Wichtiger Hinweis:** **Jede Tabelle, die mit dem [Reader][reader]-Modul in Azure Machine Learning abgefragt werden soll, muss eine interne Tabelle sein.** Nachfolgend wird gezeigt, wie Sie ermitteln können, ob es sich bei einer Tabelle "T" in einer Datenbank "D.db" um eine interne Tabelle handelt.

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

	hdfs dfs -ls wasb:///D.db/T

Wenn es sich um eine interne Tabelle handelt und diese mit Daten gefüllt ist, müssen die Inhalte angezeigt werden. Alternativ können Sie auch mit dem Azure Storage-Explorer prüfen, ob es sich bei einer Tabelle um eine interne Tabelle handelt. Wechseln Sie im Azure Storage-Explorer zum standardmäßigen Containernamen des Clusters, und filtern Sie anschließend nach dem Tabellennamen. Wenn die Tabelle vorhanden ist und ihre Inhalte angezeigt werden, handelt es sich um eine interne Tabelle.

Nachfolgend sehen Sie einen Screenshot von Hive-Abfrage und [Reader][reader]-Modul:

![](http://i.imgur.com/1eTYf52.png)

Beachten Sie Folgendes: Da die komprimierten Daten im Standardcontainer vorliegen, ist die Hive-Abfrage aus Azure Machine Learning sehr einfach und besteht lediglich aus einem "SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data".

Das Dataset kann jetzt als Startpunkt für die Entwicklung von Machine Learning-Modellen verwendet werden.

### <a name="mlmodel"></a>Entwickeln von Modellen in Azure Machine Learning

Wir können nun mit der Modellentwicklung und -bereitstellung in [Azure Machine Learning](https://studio.azureml.net) fortfahren. Die Daten sind jetzt für die oben festgelegten Vorhersageprobleme vorbereitet:

**1. Binäre Klassifizierung**: Zur Vorhersage, ob ein Trinkgeld für eine Fahrt bezahlt wird.

**Verwendeter Lernansatz:** Logistische Regression mit zwei Klassen

a. Für dieses Problem lautet der Zielbezeichner (oder die Zielklasse) "tipped". Unser ursprüngliches komprimiertes Dataset weist einige Spalten auf, die Datenlecks für dieses Klassifizierungsexperiment darstellen. Dies sind insbesondere: "tip\_class", "tip\_amount" und "total\_amount". Diese Spalten enthalten Informationen zum Zielbezeichner, die zum Testzeitpunkt nicht zur Verfügung stehen. Wir nehmen diese Spalten mit dem [Project Columns][project-columns]-Modul von der Berücksichtigung aus.

Der nachstehende Screenshot zeigt unser Experiment zur Vorhersage, ob für eine bestimmte Fahrt ein Trinkgeld gezahlt wurde oder nicht.

![](http://i.imgur.com/QGxRz5A.png)

b. Bei diesem Experiment ergibt sich für die Zielbezeichner eine Verteilung von etwa 1:1.

Der nachstehende Screenshot zeigt die Verteilung der tip\_class-Bezeichner für das binäre Klassifizierungsproblem.

![](http://i.imgur.com/9mM4jlD.png)

Als Ergebnis erhalten wir einen AUC-Wert (Area Under Curve) von 0,987, wie in der nachstehenden Abbildung gezeigt.

![](http://i.imgur.com/8JDT0F8.png)

**2. Multiklassenklassifizierung**: Zur Vorhersage des Trinkgeldbereichs für die Fahrt, unter Verwendung der zuvor definierten Klassen.

**Verwendeter Lernansatz:** Logistische Regression mit mehreren Klassen

a. Für dieses Problem lautet unser Zielbezeichner (oder die Zielklasse) "tip\_class" und kann einen von fünf Werten annehmen (0,1,2,3,4). Wie bei der binären Klassifizierung sind Spalten vorhanden, die Datenlecks für dieses Experiment darstellen. Dies sind insbesondere: "tip\_class", "tip\_amount" und "total\_amount". Diese Spalten zeigen Informationen zum Zielbezeichner, die zum Testzeitpunkt nicht zur Verfügung stehen. Wir entfernen diese Spalten mit dem [Project Columns][project-columns]-Modul.

Der nachstehende Screenshot zeigt unser Experiment zur Vorhersage, wann ein Trinkgeld wahrscheinlich niedriger ausfällt ( Class 0: tip = $0, class 1 : tip > $0 and tip <= $5, Class 2 : tip > $5 and tip <= $10, Class 3 : tip > $10 and tip <= $20, Class 4 : tip > $20)

![](http://i.imgur.com/5ztv0n0.png)

Nachstehend wird gezeigt, wie unsere tatsächliche Testklassenverteilung aussieht. Während Klasse 0 und Klasse 1 sehr häufig vorkommen, sind die weiteren Klassen eher selten.

![](http://i.imgur.com/Vy1FUKa.png)

b. Für diese Experiment verwenden wir eine Konfusionsmatrix, um die Vorhersagegenauigkeit zu untersuchen. Dies wird nachfolgend gezeigt.

![](http://i.imgur.com/cxFmErM.png)

Beachten Sie Folgendes: Während die Klassengenauigkeit bei den häufig vorkommenden Klassen recht gut ist, zeigt das Modell keine gute "Lernkurve" bei den selteneren Klassen.


**3. Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für eine Fahrt.

**Verwendeter Lernansatz:** Gewichteter Entscheidungsbaum

a. Für dieses Problem lautet der Zielbezeichner (oder -klasse) "tipped". Die Datenlecks lauten in diesem Fall: "tipped", "tip\_class", "total\_amount". Sämtliche dieser Variablen enthalten Informationen zum Trinkgeldbetrag, die zum Testzeitpunkt normalerweise nicht zur Verfügung stehen. Wir entfernen diese Spalten mit dem [Project Columns][project-columns]-Modul.

Der nachstehende Screenshot zeigt unser Experiment zur Vorhersage des Trinkgeldbetrags.

![](http://i.imgur.com/11TZWgV.png)

b. Für Regressionsprobleme messen wir die Genauigkeit unserer Vorhersage, indem wir die quadratische Abweichung in den Vorhersagen, den Bestimmungskoeffizienten und ähnliche Faktoren betrachten. Dies wird nachfolgend gezeigt.

![](http://i.imgur.com/Jat9mrz.png)

Wir sehen, dass der Bestimmungskoeffizient 0,709 lautet, was impliziert, dass etwa 71 % der Varianz durch die Modellkoeffizienten erklärt werden.

**Wichtiger Hinweis:** Weitere Informationen zu Azure Machine Learning und dazu, wie Sie darauf zugreifen und es verwenden, finden Sie unter [Was ist maschinelles Lernen?](machine-learning-what-is-machine-learning.md). Sehr nützlich zum Ausprobieren verschiedenster Experimente aus dem Bereich des maschinellen Lernens in Azure Machine Learning ist der [Katalog](https://gallery.azureml.net/). Der Katalog umfasst eine große Palette an Experimenten und bietet eine umfassende Einführung in die Möglichkeiten von Azure Machine Learning.

## Lizenzinformationen

Diese exemplarische Vorgehensweise und die zugehörigen Skripts werden von Microsoft unter MIT-Lizenz bereitgestellt. Weitere Informationen finden Sie in der Datei LICENSE.txt im Verzeichnis mit dem Beispielcode auf GitHub.

## Referenzen

•	[Downloadseite von Andrés Monroy mit den New Yorker Taxidaten](http://www.andresmh.com/nyctaxitrips/)  
•	[FOILing NYC’s Taxi Trip Data (in englischer Sprache) von Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•	[NYC Taxi and Limousine Commission Research and Statistics (in englischer Sprache)](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!-----HONumber=September15_HO1-->