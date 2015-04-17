<properties 
	pageTitle="Azure Data Science in Aktion: Verwenden von HDInsight Hadoop-Clustern | Azure" 
	description="Kompletter Azure Data Science-Ablauf mit einem HDInsight Hadoop-Cluster für das Erstellen und Bereitstellen eines Modells mithilfe eines öffentlich zugänglichen DataSets." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# Azure Data Science in Aktion: Verwenden von HDInsight Hadoop-Clustern

In diesem Lernprogramm führen Sie den vollständigen Ablaufplan für Azure Data Science mit einem Azure HDInsight Hadoop-Cluster durch, um ein Modell zu erstellen und bereitzustellen, das das öffentlich zugängliche DataSet [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) verwendet. 


## <a name="dataset"></a>Beschreibung des NYC Taxi Trips-DataSets

Die NYC Taxi Trips-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (~48 GB unkomprimiert) mit mehr als 173 Millionen einzelnen Fahrten mit den zugehörigen Preisen. Jedes Fahrten-DataSet enthält den Start- und Zielort mit der Uhrzeit, die anonymisierte Lizenznummer des Fahrers (Hack) und die eindeutige ID des Taxis (Medallion). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden DataSets bereitgestellt:

1. Die CSV-Dateien 'trip_data' enthalten Fahrtendetails wie die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Die CSV-Dateien 'trip_fare' enthalten Details für jede bezahlte Gebühr, wie Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den bezahlten Gesamtbetrag für den Fahrpreis. Es folgen einige Beispieleinträge:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Der eindeutige Schlüssel für die Zusammenführung von "trip\_data" und "trip\_fare" besteht aus den Feldern: "medallion", "hack\_licence" und "pickup\_datetime".

Es gibt jeweils 12 Fahrtendateien 'trip_data' und 'trip_fare'. In den Dateinamen stellen die Zahlen 1 bis 12 die Monate dar, in denen die Fahrten vorgenommen wurden. 

## <a name="mltasks"></a>Beispiele für Vorhersageaufgaben
Bei der Arbeit mit Daten ist es zunächst wichtig, die Art der Vorhersagen zu bestimmen, die Sie treffen möchten. Basierend auf der Analyse können Sie dann die Aufgaben ermitteln, die Sie durchführen müssen.
Hier sehen Sie drei Beispiele für Vorhersageprobleme, die in dieser exemplarischen Vorgehensweise behandelt werden. Die Formulierung basiert auf *tip\_amount*:

1. **Binäre Klassifizierung**: Vorhersagen, ob ein Trinkgeld bezahlt wurde, d. h. ein *tip\_amount* größer als 0 $ ist eine positive Probe, während ein *tip\_amount* gleich 0 $ eine negative Probe ist.

2. **Multi-Klassen-Klassifizierung**: Vorhersage des Trinkgeldbereichs für die Fahrt. Wir teilen *tip\_amount* in fünf Fächer oder Klassen auf:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für die Fahrt.  


## <a name="setup"></a>Einrichten der Azure Data Science-Umgebung

Wie Sie unter [Planen Ihrer Umgebung](machine-learning-data-science-plan-your-environment.md) sehen können, gibt es mehrere Möglichkeiten für die Arbeit mit dem DataSet "NYC Taxi Trips" in Azure:

- Verarbeiten der Daten in Azure-Blobs und anschließendes Modellieren in Azure Machine Learning
- Laden der Daten in eine SQL Server-Datenbank und anschließendes Modellieren in Azure Machine Learning
- Laden der Daten in HDInsight Hive-Tabellen und anschließendes Modellieren in Azure Machine Learning

Basierend auf der Größe des DataSets, dem Speicherort der Datenquelle und der ausgewählten Azure-Zielumgebung ähnelt dieses Szenario dem [Szenario 7: Big data in local files, target Hive database in Azure HDInsight Hadoop clusters (in englischer Sprache)](machine-learning-data-science-plan-sample-scenarios.md#largedbtohive).

Führen Sie folgende Schritte aus, um Ihre Azure Data Science-Umgebung für diesen Ansatz einzurichten. Dabei werden Azure HDInsight Hadoop-Cluster und speziell Hive-Tabellen und -Abfragen zum Speichern und Verarbeiten der Daten verwendet.

1. [Erstellen Sie ein Speicherkonto.](storage-whatis-account.md)

2. [Erstellen Sie einen Azure ML-Arbeitsbereich.](machine-learning-create-workspace.md)

3. [Stellen Sie einen virtuellen Computer unter **Windows** für Data Science bereit](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] Die Beispielskripts werden während der Einrichtung auf den virtuellen Computer für Data Science heruntergeladen. Nach Abschluss der VM-Nachinstallationsskripts finden Sie die Beispiele in der Dokumentbibliothek auf Ihrem virtuellen Computer unter *C:\Users\<user_name>\Documents\Data Science Scripts*.  Dieser Ordner wird im Folgenden mit **Beispielskripts** bezeichnet. Die Hive-Beispielabfragen befinden sich in Dateien mit der Erweiterung ".hql" im Ordner **Beispielskripts**. Beachten Sie, dass mit *<user_name>* in der Pfadangabe zu diesem Ordner Ihr Windows-Anmeldename für die VM gemeint ist, nicht Ihr Azure-Benutzername.

4. [Anpassen von Azure HDInsight Hadoop-Clustern für Data Science](machine-learning-data-science-customize-hadoop-cluster.md). In diesem Schritt erstellen Sie ein Azure HDInsight Hadoop-Cluster, bei dem auf allen Knoten 64-Bit-Anaconda Python 2.7 installiert ist. Nachdem der benutzerdefinierte Hadoop-Cluster erstellt wurde, aktivieren Sie im Azure-Portal mithilfe der in diesem Thema zur Anpassung beschriebenen Vorgehensweise den Remotezugriff auf den obersten Knoten des Hadoop-Clusters.


## <a name="getdata"></a>Abrufen der Daten aus der öffentlichen Quelle

Sie können zum Abrufen des DataSets [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) von seinem öffentlichen Speicherort eine der in [Verschieben von Daten zu und von Azure Blob-Speichern](machine-learning-data-science-move-azure-blob.md) beschriebenen Methoden zum Kopieren der Daten auf den neuen virtuellen Computer verwenden.

So kopieren Sie die Daten mit AzCopy:

1. Melden Sie sich an dem virtuellen Computer an.

2. Erstellen Sie ein neues Verzeichnis auf dem VM-Datenträger (Hinweis: Verwenden Sie nicht den temporären Datenträger, der als Datenträger auf dem virtuellen Computer enthalten ist).

3. Führen Sie in einem Eingabeaufforderungsfenster die folgenden AzCopy-Befehle aus, und ersetzen Sie <path_to_data_folder> durch den Pfad zu dem in (2) erstellten Datenordner:

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	Nach Abschluss von AzCopy sollten insgesamt 24 komprimierte CSV-Dateien (12 für "trip_data" und 12 für "trip_fare") im Datenordner enthalten sein.

	>[AZURE.NOTE] *Source:https://getgoing.blob.core.windows.net/nyctaxitrip* und *Source:https://getgoing.blob.core.windows.net/nyctaxifare* geben zwei öffentliche Azure-Container an, die zum Freigeben der extrahierten NYC Taxi-Daten für Benutzer verwendet werden. Für das Lesen aus diesen beiden öffentlichen Azure-Containern ist kein Zugriffsschlüssel erforderlich. 

## <a name="upload"></a>Hochladen der Daten in den Standardcontainer des Azure HDInsight Hadoop-Clusters

Führen Sie den folgenden AzCopy-Befehl an einer Eingabeaufforderung oder in einem Windows PowerShell-Fenster auf Ihrem virtuellen Computer aus, und ersetzen Sie die folgenden Parameter durch die Werte, die Sie beim Erstellen des Hadoop-Clusters angegeben haben. Ersetzen Sie Folgendes:

* ***&#60;path_to_data_folder>*** durch den im vorhergehenden Abschnitt erstellten Datenordner 
* ***&#60;storage account name of Hadoop cluster>*** durch das von Ihrem Cluster verwendete Speicherkonto
* ***&#60;default container of Hadoop cluster>*** durch den von Ihrem Cluster verwendeten Standardcontainer
* ***&#60;storage account key>*** durch den Schlüssel für das von Ihrem Cluster verwendete Speicherkonto

Beachten Sie, dass die Verzeichnisse ***nyctaxitripraw*** und ***nyctaxifareraw***, auf die in diesem Befehl verwiesen wird, im Container erstellt werden, wenn sie noch nicht vorhanden sind. 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>Erstellen der Hive-Datenbank und der Tabellen partitioniert nach Monat

Melden Sie sich zum Zugreifen auf und Ausführen von Hive-Abfragen in dieser exemplarischen Vorgehensweise am Hauptknoten des Hadoop-Clusters an, öffnen Sie die Hadoop-Befehlszeile auf dem Desktop des Hauptknotens, und geben Sie das Hive-Verzeichnis durch Eingabe des folgenden Befehls ein.

    cd %hive_home%\bin

Wenn Sie weitere Hilfe bei diesen oder anderen Verfahren benötigen, lesen Sie im Abschnitt [Übermitteln von Hive-Abfragen direkt von der Hadoop-Befehlszeile](machine-learning-data-science-process-hive-tables.md#submit) nach. 

Wenn Sie anhand der Anweisungen in [Einrichten der Azure Data Science-Umgebung](#setup) einen virtuellen Azure-Computer erstellt haben, sollten die Hive-Beispielabfragen bereits auf den virtuellen Computer in den Ordner **Beispielskripts** heruntergeladen worden sein. Benutzer können die HQL-Dateien auch aus [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) auschecken. 

Um die Hive-Abfragen in HQL-Dateien zu übermitteln, müssen Sie die HQL-Dateien in ein lokales Verzeichnis auf dem Hauptknoten des Hadoop-Clusters übertragen. In den folgenden Beispielen wird angenommen, dass die HQL-Dateien in das Verzeichnis ***C:\temp&#92;*** auf dem Hauptknoten übertragen wurden.

Dies ist der Inhalt der Datei ***C:\temp\sample_hive_create_db_and_tables.hql***, die die Hive-Datenbank ***nyctaxidb*** und die Tabellen ***trip*** und ***fare*** erstellt.

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

Geben Sie den folgenden Befehl in die Hadoop-Befehlszeile des Hauptknotens ein, um diese Hive-Abfragen zu übermitteln:
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>Laden von Daten in Tabellen nach Partitionen
Führen Sie die folgenden PowerShell-Befehle in der Hadoop-Befehlszeile aus, um Daten partitioniert nach Monaten in die Hive-Tabellen zu laden:

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Dies ist der Inhalt der Datei *sample_hive_load_data_by_partitions.hql* zur Überprüfung.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>Anzeigen von Datenbanken im HDInsight Hadoop-Cluster

Um die in HDInsight Hadoop-Clustern erstellten Datenbanken im Hadoop-Befehlszeilenfenster anzuzeigen, führen Sie folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -e "show databases;"

### <a name="#show-tables"></a>Anzeigen der Tabellen in der Datenbank "nyctaxidb" 
	
Führen Sie zum Anzeigen der Tabellen in der Datenbank "nyctaxidb" folgenden Befehl an der Hadoop-Befehlszeile aus:
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Durchsuchen von Daten und Verarbeiten von Funktionen in Hive
Das Durchsuchen von Daten und das Verarbeiten von Funktionen für die in die Hive-Tabellen geladenen Daten kann mithilfe von Hive-Abfragen durchgeführt werden. Dies sind Beispiele für solche Aufgaben, die wir in diesem Abschnitt bearbeiten:

- Anzeigen der ersten 10 Datensätze in beiden Tabellen
- Untersuchen der Datenverteilungen einiger Felder in unterschiedlichen Zeitfenstern
- Überprüfen der Datenqualität der Felder "longitude" und "latitude"
- Generieren von binären und Multi-Klassen-Klassifizierungsbezeichnern auf der Grundlage von **tip\_amount**
- Generieren von Funktionen durch Berechnung der direkten Fahrtentfernungen
- Zusammenführen der beiden Tabellen und Extrahieren einer zufälligen Stichprobe zum Erstellen von Modellen

Nachdem Sie die Datensuche und Funktionsverarbeitung in Hive abgeschlossen haben, können Sie mit Azure Machine Learning fortfahren. Sie können die letzte Hive-Abfrage zum Extrahieren und Erstellen von Stichprobendaten speichern und per Kopieren und Einfügen direkt in ein Reader-Modul in Azure Machine Learning einfügen. Sie können dann ein Modell für diese Daten erstellen.

### Durchsuchen: Anzeigen der ersten 10 Datensätze in der Tabelle "trip"

Um die Art des Datenschemas und der Daten zu erfassen, können Benutzer die obersten 10 Datensätze aus den einzelnen Tabellen extrahieren. Führen Sie die folgenden beiden Abfragen einzeln an der Hadoop-Befehlszeile aus, um die Datensätze zu überprüfen.

So rufen Sie die obersten 10 Datensätze in *table _trip_* ab:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
So rufen Sie die obersten 10 Datensätze in *table _fare_* ab:
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### Durchsuchen: Anzeigen der Anzahl von Datensätzen in den einzelnen 12 Partitionen

Führen Sie den folgenden Befehl an der Hadoop-Befehlszeile aus, um die Anzahl der Datensätze in jeder der 12 monatlichen Partitionen anzuzeigen.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### Durchsuchen: Verteilung der Fahrten nach "medallion"

In diesem Beispiel wird die Taxinummer ("medallion") mit mehr als 100 Fahrten innerhalb eines bestimmten Zeitraums ermittelt. Die Abfrage profitiert vom Zugriff auf die partitionierte Tabelle, da sie von der Partitionsvariable **month** abhängig ist. 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

Dies ist der Inhalt der Datei *sample_hive_trip_count_by_medallion.hql* zur Überprüfung.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### Durchsuchen: Verteilung der Fahrten nach "medallion" und "hack_license"

Dies ist ein Beispiel für die Gruppierung nach mehreren Spalten der Tabelle. In diesem Fall anhand der Spalten "medallion" und "hack_license". Führen Sie den folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

Dies ist der Inhalt der Datei *sample_hive_trip_count_by_medallion_license.hql* zur Überprüfung.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### Bewertung der Datenqualität: Überprüfen der Einträge auf ungültige Werte für "longitude" und/oder "latitude"

In diesem Beispiel wird untersucht, ob die Felder "longitude" und/oder "latitude" entweder einen ungültigen Wert enthalten (die Gradzahl sollte zwischen -90 und 90 liegen) oder als Koordinaten (0,0) aufweisen.

Führen Sie den folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Das *-S*-Argument in diesem Befehl unterdrückt die Ausgabe der Hive Map/Reduce-Aufträge an den Statusbildschirm. Dies ist hilfreich, da so die Bildschirmausgabe der Hive-Abfrage besser lesbar ist. 

Dies ist der Inhalt der Datei *sample_hive_quality_assessment.hql* zur Überprüfung.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### Durchsuchen: Häufigkeit von Fahrten mit und ohne Trinkgeld

Dieses Beispiel ermittelt die Anzahl von Fahrten mit und ohne Trinkgeld in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird). Diese Verteilung spiegelt die binäre Bezeichnerverteilung wider, die später für die Modellierung der binären Klassifizierung verwendet wird.

Führen Sie den folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

Dies ist der Inhalt der Datei *sample_hive_tipped_frequencies.hql* zur Überprüfung.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### Durchsuchen: Häufigkeit von Trinkgeldbereichen

In diesem Beispiel wird die Verteilung von Trinkgeldbereichen in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird) berechnet. Dies ist die Verteilung der Bezeichnerklassen, die später für die Modellierung der Multi-Klassen-Klassifizierung verwendet wird.

Führen Sie den folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

Dies ist der Inhalt der Datei *sample_hive_tip_range_frequencies.hql* zur Überprüfung.

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

### Durchsuchen: Berechnen der direkten Entfernung und Vergleichen mit der Fahrtentfernung

In diesem Beispiel wird die direkte Fahrtentfernung (in Meilen) berechnet. Im Beispiel werden die Ergebnisse anhand der zuvor durchgeführten Bewertung der Datenqualität auf gültige Koordinaten begrenzt.

Führen Sie den folgenden Befehl an der Hadoop-Befehlszeile aus:

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

Dies ist der Inhalt der Datei *sample_hive_trip_direct_distance.hql* zur Überprüfung.

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

Nach dem Abschluss der Abfrage werden die Ergebnisse in das Azure-Blob ***queryoutputdir/000000_0*** im Standardcontainer des Hadoop-Clusters geschrieben. Wenn Sie den Azure-Speicher-Explorer verwenden, sollte dieses Blob im Standardcontainer des Hadoop-Clusters wie in der folgenden Abbildung dargestellt angezeigt werden.

![Create workspace][2]

Beachten Sie, dass Sie den Filter (markiert durch den roten Kasten) anwenden können, um nur das Blob mit den angegebenen Buchstaben im Namen abzurufen. 

Nach dem Abschluss der Abfrage können Sie mit dem Azure SDK die Ausgabe der Abfrage vom Azure-Blob in einen Pandas-DataFrame lesen, um weiterführende Suchvorgänge und Verarbeitungsschritte durchzuführen. Anweisungen zum Laden von Azure-Blobs in Pandas-DataFrames finden Sie unter [Verarbeiten von Azure-Blob-Daten in Ihrer Data Science-Umgebung](machine-learning-data-science-process-data-blob.md). 
	
### Vorbereiten von Daten für die Modellerstellung

Die in diesem Abschnitt bereitgestellte Abfrage führt die Tabellen **nyctaxidb.trip** und **nyctaxidb.fare** zusammen, und generiert den binären Klassifizierungsbezeichner **tipped** und den Multi-Klasse Klassifizierungsbezeichner **tip\_class**. Diese Abfrage kann kopiert und dann direkt in das Reader-Modul in [Azure Machine Learning Studio](https://studio.azureml.net) eingefügt werden, um eine direkte Datenerfassung aus der **Hive-Abfrage** in Azure zu erreichen. Diese Abfrage schließt auch Datensätze mit falschen Koordinaten in "longitude" und "latitude" aus.

Diese Abfrage wendet in Hive eingebettete UDFs direkt an, um verschiedene Funktionen aus Hive-Datensätzen zu generieren. Dazu gehören die Stunde, die Kalenderwoche und der Wochentag (1 steht für Montag und 7 für Sonntag) aus dem Feld "_pickup_datetime_". Eine vollständige Liste der eingebetteten Hive-UDFs finden Sie in [LanguageManual UDF (in englischer Sprache)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Diese Abfrage führt auch ein Downsampling der Daten durch, sodass die Abfrageergebnisse in Azure Machine Learning Studio aufgenommen werden können. Nur etwa 1 % wird in Studio importiert.

Führen Sie an der Hadoop-Befehlszeile den folgenden Befehl aus, um die Abfrage zu übermitteln:

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

Dies ist der Inhalt der Datei *sample_hive_prepare_for_aml.hql* zur Überprüfung.
	
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
        select medallion, 
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
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
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

## <a name="mlmodel"></a>Erstellen von Modellen in Azure Machine Learning

Wir können nun mit der Modellerstellung und -bereitstellung in [Azure Machine Learning](https://studio.azureml.net) fortfahren. Die Daten sind jetzt für die oben festgelegten Vorhersageprobleme vorbereitet:

1. **Binäre Klassifizierung**: Zur Vorhersage, ob ein Trinkgeld für eine Fahrt bezahlt wird.

2. **Multi-Klassen-Klassifizierung**: Zur Vorhersage des Trinkgeldbereichs für die Fahrt gemäß den zuvor definierten Klassen. 

3. **Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für die Fahrt.  

Melden Sie sich zum Starten der Modellierungsübung im Azure Machine Learning-Arbeitsbereich an. Wenn Sie noch keinen Machine Learning-Arbeitsbereich erstellt haben, lesen Sie unter [Erstellen eines Azure ML-Arbeitsbereichs](machine-learning-create-workspace.md) nach.

1. Informationen zu den ersten Schritten in Azure Machine Learning finden Sie unter [Was ist Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

2. Melden Sie sich in [Azure Machine Learning Studio](https://studio.azureml.net) an.

3. Die Startseite von Studio enthält eine Vielzahl an Informationen, Videos, Lernprogrammen, Links zu Modulreferenzen und andere Ressourcen. Weitere Informationen zu Azure Machine Learning finden Sie im [Azure Machine Learning Center](http://azure.microsoft.com/documentation/services/machine-learning/).

Ein typisches Trainingsexperiment umfasst Folgendes:

1. Erstellen eines neuen Experiments über **+NEW**
2. Abrufen der Daten in Azure ML
3. Vorverarbeiten, Transformieren und Ändern der Daten nach Bedarf
4. Generieren von Funktionen nach Bedarf
5. Aufteilen der Daten in DataSets für Training/Überprüfung/Tests (oder Verwenden verschiedener DataSets für alles)
6. Auswählen eines oder mehrerer Algorithmen für das maschinelle Lernen in Abhängigkeit vom zu lösenden Lernproblem wie binäre Klassifizierung, Multi-Klassen-Klassifizierung, Regression
7. Trainieren eines oder mehrerer Modelle mit dem Trainings-DataSet
8. Bewerten des Validierungs-DataSets mithilfe der trainierten Modelle
9. Evaluieren der Modelle zur Berechnung der relevanten Kennzahlen für das Lernproblem
10. Optimieren der Modelle und Auswählen des geeignetsten Modells für die Bereitstellung

Sie haben in dieser Übung bereits die Daten in Hive untersucht und bearbeitet (Schritte 1-4) und sich für eine Stichprobengröße für die Erfassung in Azure ML entschieden. Für das Erstellen einer oder mehrerer Vorhersagemodelle gehen Sie folgendermaßen vor:

1. Rufen Sie die Daten in Azure ML mithilfe des **Reader**-Moduls im Bereich **Data Input and Output** ab. Weitere Informationen finden Sie auf der Referenzseite zum [Reader](http://msdn.microsoft.com/library/dn784775)-Modul.

	![Create workspace][15]

2. Wählen Sie die **Hive-Abfrage** als **Datenquelle** im **Eigenschaftenbereich** aus.

3. Geben Sie die Informationen zum HDInsight Hadoop-Cluster wie folgt ein. Der **Azure-Speicherkontoname** muss das Speicherkonto sein, das zum Erstellen des HDInsight Hadoop-Clusters verwendet wird, und der **Azure-Containername** muss der Standardcontainer des Hadoop-Clusters sein. 

	![Create workspace][11]

4. Fügen Sie im Textbereich für die **Hive-Datenbankabfrage** die Abfrage ein, die die erforderlichen Datenbankfelder (einschließlich berechneter Felder wie die Label) extrahiert und die Daten auf die gewünschte Stichprobengröße reduziert.

Ein Beispiel für ein binäres Klassifizierungsexperiment zum Lesen von Daten direkt aus der Hive-Abfrage finden Sie in der folgenden Abbildung. Ähnliche Experimente können für Multi-Klassen-Klassifizierungen und Regressionsprobleme erstellt werden.

![Create workspace][12]

> [AZURE.IMPORTANT] In den Modellierungsbeispielen für Datenextraktion und Stichprobengenerierung in den vorherigen Abschnitten sind **alle Bezeichner für die drei Modellierungsübungen in der Abfrage enthalten**. Ein wichtiger (erforderlicher) Schritt in den einzelnen Modellierungsübungen ist das **Ausschließen** unnötiger Bezeichner für die anderen beiden Probleme und alle anderen **Zielverluste**. Wenn Sie z. B. eine binäre Klassifizierung anwenden, verwenden Sie den Bezeichner **tipped** und schließen die Felder **tip\_class**, **tip\_amount** und **total\_amount** aus. Letztere sind Zielverluste, da sie das bezahlte Trinkgeld beinhalten.

> In diesem Experiment ergänzt das erste **Metadaten-Editor**-Modul die Spaltennamen in den Ausgabedaten vom Reader-Modul. Dieses Modul ist erforderlich, da das Reader-Modul, das die Daten aus der Hive-Abfrage liest, keine Spaltennamen für die Ausgabedaten erstellt. 

> Um nicht benötigte Spalten und/oder Zielverluste auszuschließen, können Sie das Modul **Project Columns** oder den **Metadaten-Editor** verwenden. Weitere Informationen finden Sie auf den Referenzseiten zu [Project Columns](http://msdn.microsoft.com/library/dn784740) und [Metadaten-Editor](http://msdn.microsoft.com/library/dn784761).

## <a name="mldeploy"></a>Bereitstellen von Modellen in Azure Machine Learning

Wenn das Modell fertig ist, können Sie es als Webdienst direkt aus dem Experiment heraus bereitstellen. Weitere Informationen zum Veröffentlichen von Azure ML-Webdiensten finden Sie unter [Azure Machine Learning API service operations (in englischer Sprache)](machine-learning-overview-of-azure-ml-process.md).

So stellen Sie einen neuen Webdienst bereit:

1. Erstellen Sie ein Bewertungsexperiment.
2. Veröffentlichen Sie den Webdienst.

Zum Erstellen eines Bewertungsexperiments aus einem **beendeten** Trainingsexperiment klicken Sie auf der unteren Aktionsleiste auf **CREATE SCORING EXPERIMENT**.

![Azure Scoring][13]

Azure Machine Learning versucht, ein Bewertungsexperiment basierend auf den Komponenten des Trainingsexperiments zu erstellen. Insbesondere werden dabei folgende Schritte durchgeführt:

1. Speichern des trainierten Modells und Entfernen der Modelltrainings-Module
2. Ermitteln eines logischen **Eingabeports** für das erwartete Eingabedatenschema
3. Ermitteln eines logischen **Ausgabeports** für das erwartete Ausgabeschema für den Webdienst

Wenn das Bewertungsexperiment erstellt wurde, überprüfen Sie es und passen es bei Bedarf an. Eine typische Anpassung besteht darin, das Eingabe-DataSet und/oder die Abfrage durch ausgeschlossene Bezeichnerfelder zu ersetzen, da diese nicht verfügbar sein werden, wenn der Dienst aufgerufen wird. Es empfiehlt sich möglicherweise auch, die Größe des Eingabe-DataSets und/oder der Abfrage auf so wenige DataSets zu reduzieren, dass gerade das Eingabeschema ermittelt werden kann. Für den Ausgabeport ist es üblich, alle Eingabefelder auszuschließen und nur die **bewerteten Bezeichner** und die **bewerteten Wahrscheinlichkeiten** mit dem Modul **Project Columns** in die Ausgabe einzuschließen.

In der folgenden Abbildung finden Sie ein Beispiel für ein Bewertungsexperiment. Wenn Sie die Veröffentlichung fertig vorbereitet haben, klicken Sie auf der unteren Aktionsleiste auf die Schaltfläche **PUBLISH WEB SERVICE**.

![Create workspace][14]

Zusammenfassend haben Sie in diesem Lernprogramm eine Azure Data Science-Umgebung erstellt, die auch mit einem großen öffentlichen DataSet arbeiten kann. Sie haben mit der Datenerfassung begonnen und dann sämtliche Schritte im Data Science-Ablauf von der Datensuche bis zur Funktionsverarbeitung durchgeführt, um ein Modell zu trainieren und einen Azure Machine Learning-Webdienst zu veröffentlichen.

## Lizenzinformationen

Diese exemplarische Vorgehensweise und die zugehörigen Skripts werden von Microsoft unter MIT-Lizenz bereitgestellt. Weitere Informationen finden Sie in der Datei LICENSE.txt im Verzeichnis mit dem Beispielcode auf GitHub.

## Referenzen

*	[Downloadseite von Andrés Monroy mit den New Yorker Taxidaten](http://www.andresmh.com/nyctaxitrips/)  
*	[FOILing NYC's Taxi Trip Data (in englischer Sprache) von Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[NYC Taxi and Limousine Commission Research and Statistics (in englischer Sprache)](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49-->