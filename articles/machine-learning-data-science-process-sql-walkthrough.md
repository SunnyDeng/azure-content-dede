<properties 
	pageTitle="Azure Data Science in Aktion: Verwenden von SQL Server | Azure" 
	description="Azure Data Science in Aktion" metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mohabib;fashah"/> 

                
# Azure Data Science in Aktion: Verwenden von SQL Server

In diesem Lernprogramm bearbeiten Sie den Azure Data Science-Ablaufplan vom Anfang bis zum Ende, um dabei ein Modell mithilfe eines öffentlich zugänglichen DataSets - dem [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/)-DataSets zu erstellen und bereitzustellen. 


## <a name="dataset"></a>Beschreibung des NYC Taxi Trips-DataSets

Die NYC Taxi Trips-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (~48 GB unkomprimiert) mit mehr als 173 Millionen einzelnen Fahrten mit den zugehörigen Preisen. Jedes Fahrten-DataSets enthält den Start- und Zielort mit der Uhrzeit, die anonymisierte Lizenznummer des Fahrers (Hack) und die eindeutige ID des Taxis (Medallion). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden DataSets bereitgestellt:

1. Die CSV-Datei 'trip_data' enthält Fahrtendetails wie die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Die CSV-Datei 'trip_fare' enthält Details für jede bezahlte Gebühr, wie Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den bezahlten Gesamtbetrag für den Fahrpreis. Es folgen einige Beispieleinträge:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Der eindeutige Schlüssel für die Zusammenführung von "trip_data" und "trip_fare" besteht aus den Feldern: "medallion", "hack_licence" und "pickup_datetime".

## <a name="mltasks"></a>Beispiele für Vorhersageaufgaben

Wir werden drei Vorhersageprobleme formulieren, die auf *tip_amount* basieren, nämlich:

1. Binäre Klassifikation: Vorhersagen, ob ein Trinkgeld bezahlt wurde, d. h. ein *tip_amount* größer als 0 $ ist eine positive Probe, während ein *tip_amount* gleich 0 $ eine negative Probe ist.

2. Multi-Klassen-Klassifizierung: Vorhersage des Trinkgeldbereichs für die Fahrt. Wir teilen *tip_amount* in fünf Fächer oder Klassen auf:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. Regressionsaufgabe: Vorhersage des Trinkgeldbetrags für die Fahrt.  


## <a name="setup"></a>Einrichten der Azure Data Science-Umgebung

Wie Sie unter [Planen Ihrer Umgebung](machine-learning-data-science-plan-your-environment.md) sehen können, gibt es mehrere Möglichkeiten für die Arbeit mit dem DataSet "NYC Taxi Trips" in Azure:

- Verarbeiten der Daten in Azure-Blobs und anschließendes Modellieren in Azure Machine Learning
- Laden der Daten in eine SQL Server-Datenbank und anschließendes Modellieren in Azure Machine Learning

In diesem Lernprogramm wird der parallele Massenimport von Daten in SQL Server gezeigt sowie das Durchsuchen von Daten, die Verarbeitung von Funktion und das Downsampling mithilfe von SQL Server Management Studio und IPython Notebook. [Die Beispielskripts](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) und [IPython Notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) werden in GitHub freigegeben. Ein Beispiel-IPython Notebook zum Arbeiten mit den Daten in Azure-Blobs ist am gleichen Speicherort verfügbar.

So richten Sie Ihre Azure Data Science-Umgebung ein:

1. [Erstellen Sie ein Speicherkonto.](storage-create-storage-account.md)

2. [Erstellen Sie einen Azure ML-Arbeitsbereich.](machine-learning-create-workspace.md)

3. [Stellen Sie einen virtuellen Computer für Data Science bereit](machine-learning-data-science-setup-sql-server-virtual-machine.md), der als Server für SQL Server und IPython Notebook fungiert.

	> [AZURE.NOTE] Die Beispielskripts und IPython Notebooks werden während der Einrichtung auf den virtuellen Computer für Data Science heruntergeladen. Nach Abschluss der VM-Nachinstallationsskripts finden Sie die Beispiele in der Dokumentbibliothek auf Ihrem virtuellen Computer:  
	> - Beispielskripts: `C:\Users<user_name>\Documents\Data Science Scripts`  
	> - Beispiel-IPython Notebooks: `C:\Users<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
	> Dabei ist `<user_name>` der Windows-Anmeldename für Ihre VM. Wir bezeichnen die Beispielordner mit **Sample Scripts** und **Sample IPython Notebooks**.


Basierend auf der Größe des DataSets, dem Speicherort der Datenquelle und der ausgewählten Azure-Zielumgebung ähnelt dieses Szenario dem [Szenario 5: Large dataset in a local files, target SQL Server in Azure VM (in englischer Sprache)](machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Abrufen der Daten aus der öffentlichen Quelle

Sie können zum Abrufen des DataSets [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) von seinem öffentlichen Speicherort eine der in [Verschieben von Daten zu und von Azure Blob-Speichern](machine-learning-data-science-move-azure-blob.md) beschriebenen Methoden zum Kopieren der Daten auf den neuen virtuellen Computer verwenden.

So kopieren Sie die Daten mit AzCopy:

1. Melden Sie sich an dem virtuellen Computer an.

2. Erstellen Sie ein neues Verzeichnis auf dem VM-Datenträger (Hinweis: Verwenden Sie nicht den temporären Datenträger, der als Datenträger auf dem virtuellen Computer enthalten ist).

3. Führen Sie in einem Eingabeaufforderungsfenster die folgende AzCopy-Befehlszeile aus, und ersetzen Sie <path_to_data_folder> durch den in (2) erstellten Datenordner:

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

	Nach Abschluss von AzCopy sollten insgesamt 24 komprimierte CSV-Dateien (12 für "trip_data" und 12 für "trip_fare") im Datenordner enthalten sein.

4. Extrahieren Sie die heruntergeladenen Dateien. Notieren Sie sich den Ordner mit den extrahierten Dateien. Sie verweisen auf diesen Ordner mit <path_to_data_files>.

## <a name="dbload"></a>Massenimport von Daten in eine SQL Server-Datenbank

Die Leistung beim Laden/Übertragen großer Datenmengen in eine SQL-Datenbank und den nachfolgenden Abfragen kann mithilfe von _partitionierten Tabellen und Sichten_ verbessert werden. In diesem Abschnitt führen wir die Anweisungen unter [Paralleler Massenimport mithilfe von partitionierten SQL-Tabellen](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) durch, um eine neue Datenbank zu erstellen und die Daten parallel in partitionierte Tabellen zu laden.

1. Starten Sie, während sie am virtuellen Computer angemeldet sind, **SQL Server Management Studio**.

2. Stellen Sie mithilfe der Windows-Authentifizierung eine Verbindung her.

	![SSMS Connect][12]

3. Wenn Sie noch nicht den SQL Server-Authentifizierungsmodus geändert und einen neuen SQL-Benutzer erstellt haben, öffnen Sie die Skriptdatei **change_auth.sql** im Ordner **Sample Scripts**. Ändern Sie den Standardbenutzernamen und das Kennwort. Klicken Sie auf der Symbolleiste auf **!Ausführen**, um das Skript auszuführen.

	![Execute Script][13]

4. Überprüfen und/oder ändern Sie die Standardordner für die SQL Server-Datenbank und die Protokolldateien, um sicherzustellen, dass die neu erstellten Datenbanken auf einem Datenträger gespeichert werden. Das für das Laden von Data Warehousing optimierte SQL Server-VM-Image wurde mit den Datenträgern für Daten- und Protokolldateien vorkonfiguriert. Wenn Ihr virtueller Computer keinen Datenträger enthält und Sie während der Einrichtung des virtuellen Computers neue virtuelle Festplatten hinzugefügt haben, ändern Sie die Standardordner wie folgt:

	- Klicken Sie im linken Bereich mit der rechten Maustaste auf den SQL Server-Namen, und klicken Sie dann auf **Eigenschaften**.

		![SQL Server Properties][14]

	- Wählen Sie aus der Liste **Seite auswählen** auf der linken Seite **Datenbankeinstellungen** aus.

	- Überprüfen und/oder ändern Sie die **Standardspeicherorte für Datenbank** in die Speicherorte Ihrer **Datenträger**. Hier werden die neuen Datenbanken angelegt, die mit den Standardeinstellungen für den Speicherort erstellt werden.
	
		![SQL Database Defaults][15]  

5. Um eine neue Datenbank und einen Satz von Dateigruppen zum Speichern der partitionierten Tabellen zu erstellen, öffnen Sie das Beispielskript **create_db_default.sql**. Das Skript erstellt eine neue Datenbank namens **TaxiNYC** und 12 Dateigruppen am Standardspeicherort für Daten. Jede Dateigruppe enthält die Daten aus "trip_data" und "trip_fare" eines Monats. Ändern Sie bei Bedarf den Datenbanknamen. Klicken Sie auf **!Ausführen**, um das Skript auszuführen.

6. Erstellen Sie als Nächstes zwei Partitionstabellen für "trip_data" und "trip_fare". Öffnen Sie das Beispielskript **create_partitioned_table.sql**, dadurch wird Folgendes ausgeführt:

	- Erstellen einer Partitionsfunktion zum Aufteilen der Daten nach Monaten
	- Erstellen eines Partitionsschemas für die Zuordnung der Daten der einzelnen Monate zu einer anderen Dateigruppe
	- Erstellen von zwei partitionierten Tabellen, die dem Partitionsschema zugeordnet sind: **nyctaxi_trip** enthält "trip_data", und **nyctaxi_fare** enthält "trip_fare".

	Klicken Sie auf **!Ausführen**, um das Skript auszuführen und die partitionierten Tabellen zu erstellen.

7. Im Ordner **Sample Scripts** befinden sich zwei PowerShell-Beispielskripts zur Veranschaulichung des parallelen Massenimports von Daten in SQL Server-Tabellen.

	- **bcp_parallel_generic.ps1** ist ein allgemeines Skript für den parallelen Massenimport von Daten in eine Tabelle. Ändern Sie dieses Skript, um die Eingabe- und Zielvariablen wie in den Kommentarzeilen im Skript angegeben festzulegen.
	- **bcp_parallel_nyctaxi.ps1** ist eine vorkonfigurierte Version des generischen Skripts, das verwendet werden kann, um beide Tabellen für die "NYC Taxi Trips"-Daten zu laden.

8. Klicken Sie mit der rechten Maustaste auf den Namen des Skripts **bcp_parallel_nyctaxi.ps1**, und klicken Sie dann auf **Bearbeiten**, um es in PowerShell zu öffnen. Überprüfen und ändern Sie die vordefinierten Variablen entsprechend Ihren gewählten Namen für Datenbank, Eingabedatenordner, Zielprotokollordner und die Pfade zu den Beispielformatdateien **nyctaxi_trip.xml** und **nyctaxi_fare.xml** (im Ordner **Sample Scripts**). 

	![Bulk Import Data][16]

	Sie können auch den Authentifizierungsmodus auswählen, in der Voreinstellung wird die Windows-Authentifizierung verwendet. Klicken Sie für die Ausführung auf den grünen Pfeil auf der Symbolleiste. Das Skript startet 24 Massenimportvorgänge parallel - 12 für jede partitionierte Tabelle. Sie können den Fortschritt des Datenimports überwachen, indem Sie den oben festgelegten Standardordner mit den SQL Server-Daten öffnen.

9. Das PowerShell-Skript meldet die Start- und Endzeiten. Wenn alle Massenimportvorgänge abgeschlossen wurden, wird die Endzeit gemeldet. Überprüfen Sie den Zielprotokollordner, um zu überprüfen, ob die Massenimportvorgänge erfolgreich ausgeführt wurden, d. h., ob keine Fehler im Zielprotokollordner gemeldet wurden.

10. Die Datenbank kann jetzt zum Durchsuchen, zur Funktionsverarbeitung und für andere Vorgänge verwendet werden. Da die Tabellen nach dem Feld **pickup_datetime** partitioniert sind, profitieren Abfragen mit **pickup_datetime**-Bedingungen in der **WHERE**-Klausel vom Partitionsschema.

11. Untersuchen Sie in **SQL Server Management Studio** das bereitgestellte Beispielskript **sample_queries.sql**. Um eine der Beispielabfragen auszuführen, markieren Sie die Abfragezeilen, und klicken Sie auf der Symbolleiste auf **!Ausführen**.

12. Die "NYC Taxi Trips"-Daten werden in zwei separate Tabellen geladen. Zur Verbesserung der Join-Vorgänge empfiehlt es sich dringend, die Tabellen zu indizieren. Das Beispielskript **create_partitioned_index.sql** erstellt partitionierte Indizes für den zusammengesetzten Verknüpfungsschlüssel **"medallion", "hack_license" und "pickup_datetime"**.

## <a name="dbexplore"></a>Durchsuchen von Daten und Verarbeiten von Funktionen in SQL Server

In diesem Abschnitt durchsuchen wir Daten und generieren Funktionen durch Ausführen von SQL-Abfragen direkt in **SQL Server Management Studio**. Wir verwenden dazu die zuvor erstellte SQL Server-Datenbank. Das Beispielskript **sample_queries.sql** ist im Ordner **Sample Scripts** enthalten. Ändern Sie das Skript zum Ändern des Datenbanknamens, wenn er vom Standardwert abweicht: **TaxiNYC**.

In dieser Übung führen Sie die folgenden Aktionen durch:

- Herstellen einer Verbindung mit **SQL Server Management Studio** mithilfe der Windows-Authentifizierung oder der SQL-Authentifizierung mit SQL-Anmeldenamen und Kennwort
- Untersuchen der Datenverteilungen einiger Felder in unterschiedlichen Zeitfenstern
- Überprüfen der Datenqualität der Felder "longitude" und "latitude"
- Generieren von binären und Multi-Klassen-Klassifizierungsbezeichnern auf der Grundlage von **tip_amount**
- Generieren von Funktionen und Berechnen/Vergleichen der Fahrtentfernungen
- Zusammenführen der beiden Tabellen und Extrahieren einer zufälligen Stichprobe zum Erstellen von Modellen

Wenn Sie bereit sind, mit Azure Machine Learning fortzufahren, können Sie:  

1. die letzte SQL-Abfrage zum Extrahieren und Erstellen von Stichprobendaten speichern und per Kopieren und Einfügen direkt in ein Reader-Modul in Azure Machine Learning einfügen; 
2. die extrahierten und verarbeiteten Daten, die Sie für Ihr Modell verwenden möchten, in einer neuen Datenbanktabelle speichern und dann die neue Tabelle im Reader-Modul in Azure Machine Learning verwenden.

In diesem Abschnitt speichern wir die endgültige Abfrage zum Extrahieren der Daten und zum Erstellen von Proben. Die zweite Methode wird im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in IPython Notebook](#ipnb) beschrieben.

Für eine schnelle Überprüfung der Anzahl von Zeilen und Spalten in den Tabellen, die zuvor mithilfe des parallelen Massenimports aufgefüllt wurden, verwenden Sie folgenden Code:

	-- Melden der Anzahl von Zeilen in der Tabelle "nyctaxi_trip" ohne Tabellenscan
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

	-- Melden der Anzahl von Spalten in der Tabelle "nyctaxi_trip"
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip' 

#### Durchsuchen: Verteilung der Fahrten nach "medallion"

In diesem Beispiel wird die Taxinummer ("medallion") mit mehr als 100 Fahrten innerhalb eines bestimmten Zeitraums ermittelt. Die Abfrage profitiert vom Zugriff auf die partitionierte Tabelle, da sie vom Partitionsschema **pickup_datetime** abhängig ist. Abfragen an das vollständige DataSet nutzen ebenfalls die partitionierte Tabelle und/oder den Indexscan.

	SELECT medallion, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

#### Durchsuchen: Verteilung der Fahrten nach "medallion" und "hack_license"

	SELECT medallion, hack_license, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

#### Bewertung der Datenqualität: Überprüfen der Einträge auf falsche Werte für "longitude" und/oder "latitude"

In diesem Beispiel wird untersucht, ob die Felder "longitude" und/oder "latitude" entweder einen ungültigen Wert enthalten (die Gradzahl sollte zwischen -90 und 90 liegen) oder als Koordinaten (0,0) aufweisen.

	SELECT COUNT(*) FROM nyctaxi_trip
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### Durchsuchen: Vergleich von Fahrten mit und ohne Trinkgeld

Dieses Beispiel ermittelt die Anzahl von Fahrten mit und ohne Trinkgeld in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird). Diese Verteilung spiegelt die binäre Bezeichnerverteilung wider, die später für die Modellierung der binären Klassifizierung verwendet wird.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM nyctaxi_fare
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

#### Durchsuchen: Verteilung von Trinkgeld nach Klasse/Bereich

In diesem Beispiel wird die Verteilung von Trinkgeldbereichen in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird) berechnet. Dies ist die Verteilung der Bezeichnerklassen, die später für die Modellierung der Multi-Klassen-Klassifizierung verwendet wird.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE 
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4 
		END AS tip_class
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

#### Durchsuchen: Berechnen und Vergleichen der Fahrtlängen

In diesem Beispiel werden die Werte von "longitude" und "latitude" für Start- und Zielort in SQL-Geografiepunkte konvertiert. Anschließend werden anhand dieser SQL-Geografiepunkte die Fahrtentfernung berechnet und eine zufällige Stichprobe der Ergebnisse für den Vergleich ausgegeben. Im Beispiel werden die Ergebnisse anhand der zuvor durchgeführten Bewertung der Datenqualität auf gültige Koordinaten begrenzt.

	SELECT 
	pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
	,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
	,trip_distance
	,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
	FROM nyctaxi_trip
	tablesample(0.01 percent)
	WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### Verarbeiten von Funktionen in SQL-Abfragen

Die Suchabfragen zur Generierung von Bezeichnern und zum Konvertieren der Geografiepunkte können auch zum Erstellen von Bezeichnern/Funktionen verwendet werden, indem der Zählaspekt entfernt wird. Weitere SQL-Beispiele zum Verarbeiten von Funktionen finden Sie im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in IPython Notebook](#ipnb). Es ist effizienter, die Abfragen zum Generieren von Funktionen für das komplette DataSet oder einen großen Teil davon auszuführen und dazu SQL-Abfragen zu verwenden, die direkt auf der SQL Server-Datenbankinstanz ausgeführt werden. Die Abfragen können in **SQL Server Management Studio**, IPython Notebook oder einem Entwicklungstool bzw. einer Entwicklungsumgebung ausgeführt werden, sofern lokaler oder Remotezugriff auf die Datenbank besteht.

#### Vorbereiten von Daten für die Modellerstellung

Die folgende Abfrage führt die Tabellen **nyctaxi_trip** und **nyctaxi_fare** zusammen, generiert der binäre Klassifikationsbezeichner **tipped**, den Bezeichner **tip_class** für die Multi-Klassen-Klassifizierung und extrahiert eine zufällige 1-%-Stichprobe aus dem vollständig verbundenen DataSet. Diese Abfrage kann kopiert und dann direkt in das Reader-Modul in [Azure Machine Learning Studio](https://studio.azureml.net) eingefügt werden, um eine direkte Datenerfassung aus der SQL Server-Datenbankinstanz in Azure zu erreichen. Die Abfrage schließt DataSets mit falschen Koordinaten (0, 0) aus.

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM nyctaxi_trip t, nyctaxi_fare f
	TABLESAMPLE (1 percent)
	WHERE t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Durchsuchen von Daten und Verarbeiten von Funktionen in IPython Notebook

In diesem Abschnitt werden wir Daten durchsuchen und Funktionen generieren,
und zwar sowohl mit Python als auch mit SQL-Abfragen in der zuvor erstellten SQL Server-Datenbank. Ein Beispiel-IPython Notebook namens **machine-Learning-data-science-process-sql-story.ipynb** ist im Ordner **Sample IPython Notebooks** enthalten. Dieses Notebook ist auch auf [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) verfügbar.

Die empfohlene Reihenfolge beim Arbeiten mit großen Datenmengen lautet wie folgt:

- Einlesen eines kleinen Teils der Daten in ein DataFrame im Speicher
- Durchführen von Visualisierungen und Suchvorgängen mit den Beispieldaten
- Experimentieren mit der Funktionsverarbeitung anhand der Beispieldaten 
- Bei größeren DataSet-Suchvorgängen, Datenbearbeitungsschritten und Funktionsverarbeitungen sollten Sie mithilfe von Python SQL-Abfragen direkt in der SQL Server-Datenbank in der Azure-VM ausführen.
- Treffen von Entscheidungen zur Größe der Stichproben für die Modellerstellung in Azure Machine Learning

Wenn Sie bereit für die Weiterarbeit in Azure Machine Learning sind, können Sie Folgendes durchführen:  

1. die letzte SQL-Abfrage zum Extrahieren und Erstellen von Stichprobendaten speichern und per Kopieren und Einfügen direkt in ein Reader-Modul in Azure Machine Learning einfügen. Dieses Verfahren wird im Abschnitt [Erstellen von Modellen in Azure Machine Learning](#mlmodel) veranschaulicht.    
2. die extrahierten und verarbeiteten Daten, die Sie für Ihr Modell verwenden möchten, in einer neuen Datenbanktabelle speichern und dann die neue Tabelle im Reader-Modul verwenden.

Es folgen einige Beispiele für das Durchsuchen von Daten, die Datenvisualisierung und das Verarbeiten von Funktionen. Weitere Beispiele finden Sie im Beispiel-SQL-IPython Notebook im Ordner **Sample IPython Notebooks**.

#### Initialisieren der Datenbank-Anmeldeinformationen

Initialisieren Sie die Datenbank-Verbindungseinstellungen in den folgenden Variablen:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### Erstellen der Datenbankverbindung

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### Melden der Anzahl von Zeilen und Spalten in der Tabelle "nyctaxi_trip"

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions 
		WHERE object_id = OBJECT_ID('nyctaxi_trip')
	''', conn)
    
	print 'Total number of rows = %d' % nrows.iloc[0,0]
    
    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns 
		WHERE table_name = ('nyctaxi_trip')
	''', conn)
    
	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Gesamtanzahl von Zeilen = 173.179.759  
- Gesamtanzahl von Spalten = 14
    
#### Einlesen einer kleinen Datenprobe aus der SQL Server-Datenbank

    t0 = time.time()
    
	query = '''
		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, 
			f.tolls_amount, f.total_amount, f.tip_amount 
		FROM nyctaxi_trip t, nyctaxi_fare f 
		TABLESAMPLE (0.05 PERCENT)
		WHERE t.medallion = f.medallion 
		AND   t.hack_license = f.hack_license 
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)
    
    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)
    
    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Zeit für das Einlesen der Beispieltabelle = 6,492000 Sekunden  
Anzahl der abgerufenen Zeilen und Spalten = (84.952, 21)
    
#### Deskriptive Statistik

Jetzt können die erfassten Daten durchsucht werden. Wir beginnen mit
einem Blick auf die deskriptive Statistik für das Feld **trip_distance** (oder andere Felder):

    df1['trip_distance'].describe()

#### Visualisierung: Boxplot-Beispiel

Als Nächstes zeigen wir das Boxplot-Diagramm für die Fahrtentfernungen an, um die Quantile zu visualisieren.

    df1.boxplot(column='trip_distance',return_type='dict')

![Plot #1][1]

#### Visualisierung: Verteilungsdiagramm-Beispiel

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plot #2][2]

#### Visualisierung: Balken- und Liniendiagramme

In diesem Beispiel teilen wir die Fahrtentfernungen in fünf Klassifizierungen auf und visualisieren die Klassifizierungsergebnisse.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Die oben genannte Klassifizierungsverteilung können wir wie unten gezeigt in einem Balken- oder Liniendiagramm darstellen.

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plot #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plot #4][4]

#### Visualisierung: Punktdiagramm-Beispiel

Wir zeigen ein Punktdiagramm zwischen **trip_time_in_secs** und **trip_distance**, um zu ermitteln, ob es
Korrelationen gibt.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plot #6][6]

Auf ähnliche Weise können wir die Beziehung zwischen **rate_code** und **trip_distance** überprüfen.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plot #8][8]

### Generieren von Stichproben aus den Daten in SQL

Bei der Vorbereitung von Daten für die Modellerstellung in [Azure Machine Learning Studio](https://studio.azureml.net) entscheiden Sie sich entweder für eine **direkte Verwendung von SQL-Abfragen im Reader-Modul** oder für das Beibehalten der bearbeiteten und erfassten Daten in einer neuen Tabelle, die Sie im Reader-Modul mit einer einfachen SELECT-Abfrage wie **SELECT * FROM <Ihre_neue_Tabelle>** verwenden können.

In diesem Abschnitt erstellen Sie eine neue Tabelle zum Speichern der erfassten und verarbeiteten Daten. Ein Beispiel für eine direkte SQL-Abfrage für die Modellerstellung finden Sie im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in SQL Server](#dbexplore).

#### Erstellen einer Beispieltabelle und Auffüllen dieser mit 1 % der verknüpften Tabellen. Löschen Sie zunächst die Tabelle, falls sie bereits vorhanden ist.

In diesem Abschnitt führen wir die Tabellen **nyctaxi_trip** und **nyctaxi_fare** zusammen, extrahieren 1 % zufälliger Stichproben und speichern die erfassten Daten in der neuen Tabelle **nyctaxi_one_percent**:

    cursor = conn.cursor()
    
    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''
    
    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
		INTO nyctaxi_one_percent 
		FROM nyctaxi_trip t, nyctaxi_fare f
		TABLESAMPLE (1 PERCENT)
		WHERE t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
		AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''
    
    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()
    
### Durchsuchen von Daten und Verwenden von SQL-Abfragen in IPython Notebook

In diesem Abschnitt untersuchen wir die Datenverteilungen anhand der 1 % Stichprobendaten in der zuvor neu erstellten Tabelle. Beachten Sie, dass ähnliche Suchvorgänge anhand der ursprünglichen Tabellen ausgeführt werden können, wobei die Suchvorgänge optional mit **TABLESAMPLE** auf Stichproben begrenzt werden können, oder dass die Ergebnisse durch die Begrenzung mithilfe von **pickup_datetime**-Partitionen auf einen bestimmten Zeitraum eingeschränkt werden können. Dies wird im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in SQL Server](#dbexplore) beschrieben.

#### Durchsuchen: Tägliche Verteilung der Fahrten

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### Durchsuchen: Verteilung der Fahrten nach "medallion"

    query = '''
		SELECT medallion,count(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY medallion
	'''
    
	pd.read_sql(query,conn)

### Generieren von Funktionen mithilfe von SQL-Abfragen in IPython Notebook

In diesem Abschnitt erzeugen Sie neue Bezeichner und Funktionen direkt mithilfe von SQL-Abfragen für die Tabelle mit den 1 % Stichproben, die Sie im vorherigen Abschnitt erstellt haben.

#### Bezeichner-Generierung: Generieren von Klassenbezeichnern

Im folgenden Beispiel erstellen Sie zwei Sätze von Bezeichnern für die Modellierung:

1. Binäre Klassenbezeichner **tipped** (Vorhersagen, ob ein Trinkgeld gegeben wird)
2. Multi-Klassenbezeichner **tip_class** (Vorhersage des Trinkgeldbereichs)

		nyctaxi_one_percent_add_col = '''
			ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
		'''
		
		cursor.execute(nyctaxi_one_percent_add_col)
		cursor.commit()
    
    	nyctaxi_one_percent_update_col = '''
        	UPDATE nyctaxi_one_percent 
            SET 
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

    	cursor.execute(nyctaxi_one_percent_update_col)
		cursor.commit()

#### Funktionsverarbeitung: Zählen von Funktionen für kategorische Spalten

In diesem Beispiel wird ein kategorisches Feld in ein numerisches Feld umgewandelt, indem jede Kategorie durch die Anzahl ihrer Vorkommen in den Daten ersetzt wird.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B AS 
		(
			SELECT medallion, hack_license, 
				SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
				SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
			FROM nyctaxi_one_percent 
			GROUP BY medallion, hack_license
		) 
    
		UPDATE nyctaxi_one_percent 
		SET nyctaxi_one_percent.cmt_count = B.cmt_count,
			nyctaxi_one_percent.vts_count = B.vts_count
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion AND A.hack_license = B.hack_license
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Funktionsverarbeitung: Einteilen von Funktionen für numerische Spalten

In diesem Beispiel wird ein kontinuierliches numerisches Feld in vordefinierte Kategoriebereiche umgewandelt, d. h., das numerische Feld wird in ein kategorisches Feld konvertiert.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS 
		(
			SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs, 
			NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
		)
    
		UPDATE nyctaxi_one_percent 
		SET trip_time_bin = B.BinNumber
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion
		AND A.hack_license = B.hack_license
		AND A.pickup_datetime = B.pickup_datetime
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Funktionsverarbeitung: Extrahieren von Orts-Funktionen aus den Dezimalwerten für "latitude" und "longitude"

In diesem Beispiel wird die dezimale Darstellung eines Felds "latitude" und/oder "longitude" in mehrere Regionsfelder unterschiedlicher Granularität aufgeteilt, wie z. B. Land, Stadt, Stadtteil, Straße usw. Beachten Sie, dass die neuen Geografie-Felder keinen tatsächlichen Positionen zugeordnet sind. Informationen über die Zuordnung von Geocode-Positionen finden Sie in den [REST-Diensten für Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx). 

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent 
		ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
			l5 varchar(3), l6 varchar(3), l7 varchar(3)
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		UPDATE nyctaxi_one_percent
		SET l1=round(pickup_longitude,0) 
			, l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
			, l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
			, l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
			, l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
			, l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
			, l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END 
	'''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Überprüfen der endgültigen Form der Funktionstabelle

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Wir können nun mit der Modellerstellung und -bereitstellung in [Azure Machine Learning](https://studio.azureml.net) fortfahren. Die Daten sind für die oben beschriebenen Vorhersageprobleme vorbereitet:

1. Binäre Klassifizierung: Zur Vorhersage, ob ein Trinkgeld für eine Fahrt bezahlt wird.

2. Multi-Klassen-Klassifizierung: Zur Vorhersage des Trinkgeldbereichs gemäß den zuvor definierten Klassen.

3. Regressionsaufgabe: Vorhersage des Trinkgeldbetrags für die Fahrt.  


## <a name="mlmodel"></a>Erstellen von Modellen in Azure Machine Learning

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

Sie haben in dieser Übung bereits die Daten in SQL Server untersucht und bearbeitet und sich für eine Stichprobengröße für die Erfassung in Azure ML entschieden. Für das Erstellen einer oder mehrerer Vorhersagemodelle gehen Sie folgendermaßen vor:

1. Rufen Sie die Daten in Azure ML mithilfe des **Reader**-Moduls im Bereich **Data Input and Output** ab. Weitere Informationen finden Sie auf der Referenzseite zum [Reader](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004)-Modul.

	![Azure ML Reader][17]

2. Wählen Sie die **Azure SQL-Datenbank** als **Datenquelle** im **Eigenschaftenbereich** aus.

3. Geben Sie den DNS-Namen für die Datenbank im Feld **Datenbankservername** ein. Format: `tcp:<DNS_Name_Ihres_virtuellen_Computers>,1433`

4. Geben Sie den **Datenbanknamen** in das entsprechende Feld ein.

5. Geben Sie den **SQL-Benutzernamen** in **Server user account name** und das Kennwort in **Server user account password** ein.

6. Aktivieren Sie die Option **Accept any server certificate**.

7. Fügen Sie im Textbereich für die **Datenbankabfrage** die Abfrage ein, die die erforderlichen Datenbankfelder (einschließlich berechneter Felder wie die Bezeichner) extrahiert und die Daten auf die gewünschte Stichprobengröße reduziert.

Ein Beispiel für ein binäres Klassifizierungsexperiment zum Lesen von Daten direkt aus der SQL Server-Datenbank finden Sie in der folgenden Abbildung. Ähnliche Experimente können für Multi-Klassen-Klassifizierungen und Regressionsprobleme erstellt werden.

![Azure ML Train][10]

> [AZURE.IMPORTANT] In den Modellierungsbeispielen für Datenextraktion und Stichprobengenerierung in den vorherigen Abschnitten sind **alle Bezeichner für die drei Modellierungsübungen in der Abfrage enthalten**. Ein wichtiger (erforderlicher) Schritt in den einzelnen Modellierungsübungen ist das **Ausschließen** unnötiger Bezeichner für die anderen beiden Probleme und alle anderen **Zielverluste**. Wenn Sie z. B. eine binäre Klassifizierung anwenden, verwenden Sie den Bezeichner **tipped** und schließen die Felder **tip_class**, **tip_amount** und **total_amount** aus. Letztere sind Zielverluste, da sie das bezahlte Trinkgeld beinhalten.
>
> Um nicht benötigte Spalten und/oder Zielverluste auszuschließen, können Sie das Modul **Project Columns** oder den **Metadaten-Editor** verwenden. Weitere Informationen finden Sie auf den Referenzseiten zu [Project Columns](https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223) und [Metadaten-Editor](https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66).

## <a name="mldeploy"></a>Bereitstellen von Modellen in Azure Machine Learning

Wenn das Modell fertig ist, können Sie es problemlos als Webdienst direkt aus dem Experiment heraus bereitstellen. Weitere Informationen zum Veröffentlichen von Azure ML-Webdiensten finden Sie unter [Veröffentlichen von Azure Machine Learning-Webdiensten](machine-learning-publish-a-machine-learning-web-service.md).

So stellen Sie einen neuen Webdienst bereit:

1. Erstellen Sie ein Bewertungsexperiment.
2. Veröffentlichen Sie den Webdienst.

Zum Erstellen eines Bewertungsexperiments aus einem **beendeten** Trainingsexperiment klicken Sie auf der unteren Aktionsleiste auf **CREATE SCORING EXPERIMENT**.

![Azure Scoring][18]

Azure Machine Learning versucht, ein Bewertungsexperiment basierend auf den Komponenten des Trainingsexperiments zu erstellen. Insbesondere werden dabei folgende Schritte durchgeführt:

1. Speichern des trainierten Modells und Entfernen der Modelltrainings-Module
2. Ermitteln eines logischen **Eingabeports** für das erwartete Eingabedatenschema
3. Ermitteln eines logischen **Ausgabeports** für das erwartete Ausgabeschema für den Webdienst

Wenn das Bewertungsexperiment erstellt wurde, überprüfen Sie es und passen es bei Bedarf an. Eine typische Anpassung besteht darin, das Eingabe-DataSet und/oder die Abfrage durch ausgeschlossene Bezeichnerfelder zu ersetzen, da diese nicht verfügbar sein werden, wenn der Dienst aufgerufen wird. Es empfiehlt sich möglicherweise auch, die Größe des Eingabe-DataSets und/oder der Abfrage auf so wenige DataSets zu reduzieren, dass gerade das Eingabeschema ermittelt werden kann. Für den Ausgabeport ist es üblich, alle Eingabefelder auszuschließen und nur die **bewerteten Bezeichner** und die **bewerteten Wahrscheinlichkeiten** mit dem Modul **Project Columns** in die Ausgabe einzuschließen.

In der folgenden Abbildung finden Sie ein Beispiel für ein Bewertungsexperiment. Wenn Sie die Veröffentlichung fertig vorbereitet haben, klicken Sie auf der unteren Aktionsleiste auf die Schaltfläche **PUBLISH WEB SERVICE**.

![Azure ML Publish][11]

Zusammenfassend haben Sie in diesem Lernprogramm eine Azure Data Science-Umgebung erstellt und mit einem großen öffentlichen DataSet gearbeitet und dabei alle Schritte von der Datenerfassung bis zum Modelltraining und zur Veröffentlichung eines Azure Machine Learning-Webdienstes durchlaufen.

### Lizenzinformationen

Diese exemplarische Vorgehensweise und die zugehörigen Skripts und IPython Notebook(s) werden von Microsoft unter MIT-Lizenz bereitgestellt. Weitere Informationen finden Sie in der Datei LICENSE.txt im Verzeichnis mit dem Beispielcode auf GitHub.

### Referenzen

*	[Downloadseite von Andrés Monroy mit den New Yorker Taxidaten](http://www.andresmh.com/nyctaxitrips/)  
*	[FOILing NYC's Taxi Trip Data (in englischer Sprache) von Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[NYC Taxi and Limousine Commission Research and Statistics (in englischer Sprache)](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png

<!--HONumber=49-->