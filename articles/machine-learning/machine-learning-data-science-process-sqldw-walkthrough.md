<properties
	pageTitle="Der Cortana Analytics-Prozess in Aktion: Verwenden von SQL Data Warehouse | Microsoft Azure"
	description="Advanced Analytics Process and Technology in Aktion"  
	services="machine-learning"
	documentationCenter=""
	authors="hangzh,weig,bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/11/2016" 
	ms.author="bradsev"/>


# Der Cortana Analytics-Prozess in Aktion: Verwenden von SQL Data Warehouse


In diesem Tutorial führen wir Sie durch die Erstellung und Bereitstellung eines Machine Learning-Modells mit SQL Data Warehouse (SQL DW) für ein öffentlich zugängliches Dataset: das Dataset [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/). Das erstellte binäre Klassifizierungsmodell sagt voraus, ob ein Trinkgeld für eine Fahrt bezahlt wird. Zudem werden Modelle für Multiklassenklassifizierung und Regression behandelt.

Die Prozedur entspricht dem Workflow des [Cortana Analytics-Prozesses (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/). Wir zeigen das Einrichten einer Data Science-Umgebung, das Laden der Daten in SQL Data Warehouse sowie das Untersuchen der Daten und das Entwickeln von Features in SQL Data Warehouse und in IPython Notebook. Anschließend zeigen wir das Erstellen und Bereitstellen des Modells in Azure Machine Learning.


## <a name="dataset"></a>Das Dataset „NYC Taxi Trips“

Die „NYC Taxi Trips“-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (~48 GB unkomprimiert) mit Aufzeichnungen von mehr als 173 Millionen einzelner Fahrten mit den zugehörigen Preisen. Jeder Fahrtendatensatz enthält den Start- und Zielort, jeweils mit Uhrzeit, die anonymisierte Lizenznummer des Fahrers (Hack) und die eindeutige ID des Taxis (Medallion). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden DataSets bereitgestellt:

1. Die CSV-Datei "trip\_data" enthält Fahrtendetails wie die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Die CSV-Datei "trip\_fare" enthält Details für jede bezahlte Gebühr, wie Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den bezahlten Gesamtbetrag für den Fahrpreis. Es folgen einige Beispieleinträge:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Der eindeutige Schlüssel für die Zusammenführung von „trip\_data“ und „trip\_fare“ besteht aus den folgenden drei Feldern: „medallion“, „hack\_licence“ und „pickup\_datetime“.

## <a name="mltasks"></a>Beispiele für Vorhersageaufgaben

Wir werden drei Vorhersageprobleme formulieren, die auf *tip\_amount* basieren, nämlich:

1. **Binäre Klassifizierung**: Vorhersage, ob ein Trinkgeld bezahlt wurde. Hierbei repräsentiert ein *tip\_amount* von mehr als 0 $ eine positive Probe, während ein *tip\_amount* mit dem Wert 0 $ eine negative Probe darstellt.

2. **Multiklassenklassifizierung**: Vorhersage des Trinkgeldbereichs für die Fahrt. Wir teilen *tip\_amount* in fünf Container oder Klassen auf:

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für eine Fahrt.


## <a name="setup"></a>Einrichten der Azure Data Science-Umgebung für die erweiterte Analyse

Zum Einrichten Ihrer Azure Data Science-Umgebung führen Sie die folgenden Schritte durch:

Erstellen Sie Ihr eigenes **Azure-Blobspeicherkonto**. Die in dieser exemplarischen Vorgehensweise verwendeten NYC Taxi-Daten werden in einem öffentlichen Blobspeichercontainer in Azure in einem CSV-Format gemeinsam genutzt. In dieser exemplarischen Vorgehensweise werden die Daten in Ihren eigenen Azure-Blobspeicher kopiert, bevor sie in Azure SQL Data Warehouse hochgeladen werden. Der **öffentliche Blobspeicher** befindet sich unter ***USA, Süden-Mitte***.

- Wenn Sie Ihren eigenen Azure-Blobspeicher bereitstellen, wählen Sie einen geografischen Standort für Ihren Azure-Blobspeicher aus, der so nah wie möglich bei „USA, Süden-Mitte“ liegt. Die Daten werden aus dem öffentlichen Blobspeichercontainer in einen Container in Ihrem eigenen Speicherkonto kopiert. Je näher Ihr Azure-Blobspeicher bei „USA, Süden-Mitte“ liegt, desto schneller wird diese Aufgabe (Schritt 4) abgeschlossen. 
- Um Ihr eigenes Azure-Speicherkonto zu erstellen, befolgen Sie die Schritte unter [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md). Notieren Sie sich unbedingt die Werte für die folgenden Anmeldeinformationen für das Speicherkonto, da sie später in der exemplarischen Vorgehensweise benötigt werden. 

  - **Speicherkontoname**
  - **Speicherkontoschlüssel**
  - **Containername** (in dem die Daten im Azure-Blobspeicher gespeichert werden sollen)

Stellen Sie Ihre Azure SQL Data Warehouse-Instanz bereit. Befolgen Sie die Dokumentation unter [Erstellen eines SQL Data Warehouse](sql-data-warehouse-get-started-provision.md), um eine SQL Data Warehouse-Instanz bereitzustellen. Notieren Sie sich unbedingt die folgenden Anmeldeinformationen für SQL Data Warehouse, die in späteren Schritten verwendet werden.
 
  - **Servername**
  - **SQLDW-Name (Datenbank)**
  - **Benutzername**
  - **Kennwort**

Installieren Sie Visual Studio 2015 und SQL Server Data Tools. Eine Anleitung hierzu finden Sie unter [Installieren von Visual Studio 2015 und/oder SSDT (SQL Server Data Tools) für SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md).

Stellen Sie sicher, dass Sie mit Visual Studio eine Verbindung mit Ihrem Azure SQL Data Warehouse herstellen können. Eine Anleitung hierzu finden Sie unter [Herstellen einer Verbindung mit Azure SQL Data Warehouse über Visual Studio](sql-data-warehouse-get-started-connect.md).

Erstellen Sie einen Machine Learning-Arbeitsbereich unter Ihrem Azure-Abonnement. Eine Anleitung hierzu finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](machine-learning-create-workspace.md).

## <a name="getdata"></a>Laden der Daten in SQL Data Warehouse

Öffnen Sie eine Windows PowerShell-Befehlskonsole. Führen Sie die folgenden PowerShell-Befehle zum Herunterladen der Beispiel-SQL-Skriptdateien aus, die wir für Sie auf Github bereitstellen. Speichern Sie sie in einem lokalen Verzeichnis, das Sie mit dem Parameter *-DestDir* angeben. Sie können den Wert des Parameters *-DestDir* in ein beliebiges lokales Verzeichnis ändern. Wenn *-DestDir* nicht vorhanden ist, wird es vom PowerShell-Skript erstellt.

>[AZURE.NOTE]Möglicherweise müssen Sie beim Ausführen des folgenden PowerShell-Skripts die Option **Als Administrator ausführen** verwenden, wenn für das Erstellen oder für das Schreiben in Ihr *DestDir* Administratorrechte erforderlich sind.

	$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
	$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
	$wc = New-Object System.Net.WebClient
	$wc.DownloadFile($source, $ps1_dest) 
	.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Nach erfolgreicher Ausführung ändert sich Ihr aktuelles Arbeitsverzeichnis in *-DestDir*. Ihnen sollte in etwa der folgenden Bildschirm angezeigt werden:

![][19]

Führen Sie im *-DestDir* das folgende PowerShell-Skript im Administratormodus aus:

	./SQLDW_Data_Import.ps1

Diese PowerShell-Skriptdatei führt folgende Aufgaben aus:

- Herunterladen und Installieren von AzCopy, falls AzCopy noch nicht installiert ist
- Kopieren von Daten aus dem öffentlichen Blob in Ihr privates Blobspeicherkonto mit AzCopy
- Laden von Daten aus Ihrem privaten Blobspeicherkonto in Ihr Azure SQL Data Warehouse
	- Erstellen externer Tabellen für das „NYC Taxi“-Dataset im Blobspeicherkonto
	- Erstellen von Tabellen („trip“ und „fare“) in SQL Data Warehouse zum Speichern des „NYC Taxi“-Datasets
	- Importieren des „NYC Taxi“-Datasets aus externen Tabellen in SQL Data Warehouse-Tabellen
	- Erstellen einer Beispieldatentabelle (NYCTaxi\_Sample) und Einfügen von Daten durch Auswahl von SQL-Abfragen für die Tabellen „trip“ und „fare“. Einige Schritte in dieser exemplarischen Vorgehensweise müssen diese Beispieltabelle verwenden. 

Bei der ersten Ausführung des PowerShell-Skripts werden Sie aufgefordert, die Informationen aus Ihrem Azure SQL Data Warehouse und Ihrem Azure-Blobspeicherkonto einzugeben. Wenn das PowerShell-Skripts erstmals vollständig ausgeführt wurde, wurden die von Ihnen eingegebenen Anmeldeinformationen in eine Konfigurationsdatei „SQLDW.conf“ im aktuellen Arbeitsverzeichnis geschrieben. Bei der zukünftigen Ausführung dieser PowerShell-Skriptdatei können alle benötigten Parameter aus dieser Konfigurationsdatei gelesen werden. Wenn Sie einige Parameter ändern müssen, können Sie entweder die Parameter nach Aufforderung am Bildschirm eingeben, indem Sie diese Konfigurationsdatei löschen und die Parameterwerte wie abgefragt eingeben, oder die Parameterwerte ändern, indem Sie die Konfigurationsdatei bearbeiten.

>[AZURE.NOTE]Um beim direkten Lesen von Parametern aus der CONF-Datei Namenskonflikte mit den Schemas zu vermeiden, die bereits in Ihrem Azure SQL Data Warehouse vorhanden sind, wird dem Schemanamen aus der CONF-Datei eine dreistellige Zufallszahl als Standardschemaname für jede Ausführung hinzugefügt.

>[AZURE.NOTE]Je nach dem geografischen Standort Ihres privaten Blobspeicherkontos dauert der Kopiervorgang der Daten aus einem öffentlichen Blob in Ihr privates Speicherkonto ca. 15 Minuten oder auch länger, und der Vorgang zum Laden von Daten aus Ihrem Speicherkonto in Ihr Azure SQL Data Warehouse kann 20 Minuten oder länger dauern.

>[Azure-Hinweis] Wenn die Dateien, die aus dem öffentlichen Blobspeicher in Ihr privates Blobspeicherkonto kopiert werden sollen, bereits in Ihrem privaten Blobspeicherkonto vorhanden sind, werden Sie von AzCopy gefragt, ob Sie sie überschreiben möchten. Wenn Sie sie nicht überschreiben möchten, geben Sie bei Aufforderung **n** ein. Wenn Sie **alle** Dateien überschreiben möchten, geben Sie bei Aufforderung **a** ein. Sie können auch **y** eingeben, um sie einzeln zu überschreiben.

![Grafik 21][21]

[Azure-Tipps]

- Wenn Ihre Daten auf Ihrem lokalen Computer in einer realen Anwendung gespeichert sind, können Sie AzCopy dennoch zum Hochladen lokaler Daten in Ihren privaten Azure-Blobspeicher verwenden. Sie müssen im AzCopy-Befehl nur den **Quellspeicherort** in der PowerShell-Skriptdatei in ein lokales Verzeichnis ändern.	
- Wenn Ihre Daten sich bereits in Ihrem privaten Azure-Blobspeicher in einer realen Anwendung befinden, können Sie den AzCopy-Schritt im PowerShell-Skript überspringen und die Daten direkt in Azure SQL Data Warehouse hochladen. 


Dieses Powershell-Skript bindet auch die Azure SQL Data Warehouse-Informationen in die Beispieldateien zum Durchsuchen von Daten („SQLDW\_Explorations.sql“, „SQLDW\_Explorations.ipynb“ und „SQLDW\_Explorations\_Scripts.py“) ein, sodass diese drei Dateien sofort nach Abschluss des PowerShell-Skripts getestet werden können.

Nach erfolgreicher Ausführung wird folgender Bildschirm angezeigt:

![][20]

## <a name="dbexplore"></a>Durchsuchen von Daten und Entwickeln von Features in Azure SQL Data Warehouse

In diesem Abschnitt durchsuchen wir Daten und generieren Features durch das direkte Ausführen von SQL-Abfragen für Azure SQL Data Warehouse mit **Visual Studio Data Tools**. Alle in diesem Abschnitt verwendeten SQL-Abfragen finden Sie im Beispielskript namens **SQLDW\_Explorations.sql**. Diese Datei wurde bereits vom PowerShell-Skript in das lokale Verzeichnis heruntergeladen. Sie können es auch aus [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql) abrufen. In die Datei in Github sind jedoch keine Azure SQL Data Warehouse-Informationen eingebunden.

Hier sind die Arten der in diesem Abschnitt durchgeführten Aufgaben zum Durchsuchen von Daten und Generieren von Features aufgeführt:

- Herstellen einer Verbindung mit Ihrem Azure SQL Data Warehouse mithilfe von Visual Studio anhand des SQL Data Warehouse-Anmeldenamens und des Kennworts
- Untersuchen der Datenverteilungen einiger Felder in unterschiedlichen Zeitfenstern
- Überprüfen der Datenqualität der Felder "longitude" und "latitude"
- Generieren von binären und Multiklassenklassifizierungs-Bezeichnern auf der Grundlage von **tip\_amount**
- Generieren von Funktionen und Berechnen/Vergleichen der Fahrtentfernungen
- Zusammenführen der beiden Tabellen und Extrahieren einer zufälligen Stichprobe zum Erstellen von Modellen

### Überprüfung des Datenimports

Für eine schnelle Überprüfung der Anzahl von Zeilen und Spalten in den Tabellen, die zuvor mithilfe des parallelen Massenimports aufgefüllt wurden, verwenden Sie folgenden Code:

	-- Report number of rows in table <nyctaxi_trip> without table scan
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

	-- Report number of columns in table <nyctaxi_trip>
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

### Durchsuchen: Verteilung der Fahrten nach "medallion"

In diesem Beispiel werden die Taxinummern („medallions“) mit mehr als 100 abgeschlossenen Fahrten innerhalb eines bestimmten Zeitraums ermittelt. Die Abfrage profitiert vom Zugriff auf die partitionierte Tabelle, da sie vom Partitionsschema **pickup\_datetime** abhängig ist. Abfragen an das vollständige DataSet nutzen ebenfalls die partitionierte Tabelle und/oder den Indexscan.

	SELECT medallion, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

### Durchsuchen: Verteilung der Fahrten nach "medallion" und "hack\_license"

In diesem Beispiel werden die Taxinummern („medallions“) und Fahrer („hack\_license“) mit mehr als 100 abgeschlossenen Fahrten innerhalb eines bestimmten Zeitraums ermittelt.

	SELECT medallion, hack_license, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

### Bewertung der Datenqualität: Überprüfen der Einträge auf falsche Werte für „longitude“ und/oder „latitude“

In diesem Beispiel wird untersucht, ob die Felder "longitude" und/oder "latitude" entweder einen ungültigen Wert enthalten (die Gradzahl sollte zwischen –90 und 90 liegen) oder als Koordinaten (0,0) aufweisen.

	SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

### Durchsuchen: Vergleich der Verteilung von Fahrten mit bzw. ohne Trinkgeld

Dieses Beispiel ermittelt die Anzahl von Fahrten mit und ohne Trinkgeld in einem bestimmten Zeitraum (oder im vollständigen Dataset, wenn das ganze Jahr verwendet wird). Diese Verteilung spiegelt die binäre Bezeichnerverteilung wider, die später für die Modellierung der binären Klassifizierung verwendet wird.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM <schemaname>.<nyctaxi_fare>
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

### Durchsuchen: Verteilung von Trinkgeld nach Klasse/Bereich

In diesem Beispiel wird die Verteilung von Trinkgeldbereichen in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird) berechnet. Dies ist die Verteilung der Bezeichnerklassen, die später für die Modellierung der Multiklassenklassifizierung verwendet wird.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4
		END AS tip_class
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

### Durchsuchen: Berechnen und Vergleichen der Fahrtlängen

In diesem Beispiel werden die Werte von "longitude" und "latitude" für Start- und Zielort in SQL-Geografiepunkte konvertiert. Anschließend werden anhand dieser SQL-Geografiepunkte die Fahrtentfernung berechnet und eine zufällige Stichprobe der Ergebnisse für den Vergleich ausgegeben. Im Beispiel werden die Ergebnisse anhand der zuvor durchgeführten Bewertung der Datenqualität auf gültige Koordinaten begrenzt.

	/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function calculate the direct distance between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)
	
	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO

	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude, 
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

### Featureentwicklung mit SQL-Funktionen

SQL-Funktionen können manchmal eine effiziente Option für die Featureentwicklung sein. In dieser exemplarischen Vorgehensweise haben wir eine SQL-Funktion definiert, um die direkte Entfernung zwischen dem Start- und dem Zielort zu berechnen. Sie können die folgenden SQL-Skripts in **Visual Studio Data Tools** ausführen.

Hier sehen Sie das SQL-Skript, in dem die Funktion „distance“ definiert wird.

	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function calculate the direct distance between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)
	
	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO 

Es folgt ein Beispiel zum Aufrufen dieser Funktion, um Features in Ihrer SQL-Abfrage zu generieren:

	-- Sample query to call the function to create features
	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude, 
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

### Vorbereiten von Daten für die Modellerstellung

Die folgende Abfrage führt die Tabellen **nyctaxi\_trip** und **nyctaxi\_fare** zusammen, generiert einen Bezeichner **tipped** für die binäre Klassifizierung und einen Bezeichner **tip\_class** für die Multiklassenklassifizierung und extrahiert eine Stichprobe aus dem vollständig verbundenen Dataset. Das Sampling erfolgt durch das Abrufen einer Teilmenge der Fahrten basierend auf der Startzeit. Diese Abfrage kann kopiert und dann direkt in das [Reader][reader]-Modul in [Azure Machine Learning Studio](https://studio.azureml.net) eingefügt werden, um eine direkte Datenerfassung aus der SQL-Datenbankinstanz in Azure zu erzielen. Die Abfrage schließt DataSets mit falschen Koordinaten (0, 0) aus.

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
	WHERE datepart("mi",t.pickup_datetime) = 1
	AND   t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Wenn Sie bereit sind, mit Azure Machine Learning fortzufahren, können Sie:

1. die letzte SQL-Abfrage zum Extrahieren und Erstellen von Stichprobendaten speichern und per Kopieren und Einfügen direkt in ein [Reader][reader]-Modul in Azure Machine Learning einfügen;
2. die extrahierten und verarbeiteten Daten, die Sie für Ihr Modell verwenden möchten, in einer neuen SQL Data Warehouse-Tabelle speichern und dann die neue Tabelle im [Reader][reader]-Modul in Azure Machine Learning verwenden. Das PowerShell-Skript hat dies in einem früheren Schritt für Sie erledigt. Sie können Daten direkt aus dieser Tabelle in das Reader-Modul einlesen. 


## <a name="ipnb"></a>Durchsuchen von Daten und Entwickeln von Features in IPython Notebook

In diesem Abschnitt werden wir Daten durchsuchen und Features generieren, und zwar sowohl mit Python als auch mit SQL-Abfragen für das zuvor erstellte SQL Server Data Warehouse. Ein IPython Notebook-Beispiel namens **SQLDW\_Explorations.ipynb** und eine Python-Skriptdatei namens **SQLDW\_Explorations\_Scripts.py** wurden in Ihr lokales Verzeichnis heruntergeladen. Sie stehen auch auf [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW) zur Verfügung. Diese beiden Dateien sind in Python-Skripts identisch. Für den Fall, dass Sie keinen IPython Notebook-Server verwenden, wird die Python-Skriptdatei für Sie bereitgestellt. Diese beiden Python-Beispieldateien wurden unter **Python 2.7** entworfen.

Die erforderlichen Azure SQL Data Warehouse-Informationen im IPython Notebook-Beispiel und die auf Ihren lokalen Computer heruntergeladene Python-Skriptdatei wurden zuvor vom PowerShell-Skript eingebunden. Sie sind ohne Änderung ausführbar.

Wenn Sie bereits einen AzureML-Arbeitsbereich eingerichtet haben, können Sie das IPython Notebook-Beispiel direkt in den AzureML IPython Notebook-Dienst hochladen und dessen Ausführung starten. Im Folgenden werden die Schritte zum Hochladen in den AzureML IPython Notebook-Dienst aufgeführt:

1. Melden Sie sich bei Ihrem AzureML-Arbeitsbereich an, klicken Sie oben auf „Studio“, und klicken Sie auf der linken Seite der Webseite auf „NOTEBOOKS“. 

	![Grafik 22][22]

2. Klicken Sie in der linken unteren Ecke der Webseite auf „NEW“, und wählen Sie „Python 2“ aus. Geben Sie dann einen Namen für das Notebook ein, und klicken Sie auf das Häkchen, um das neue, leere IPython Notebook zu erstellen.

	![Grafik 23][23]

3. Klicken Sie auf das Symbol „Jupyter“ in der linken oberen Ecke des neuen IPython Notebook.

	![Grafik 24][24]

4. Ziehen Sie das IPython Notebook-Beispiel auf die **Strukturseite** Ihres AzureML IPython Notebook-Diensts, und klicken Sie auf **Upload**. Das IPython Notebook-Beispiel wird dann in den AzureML IPython Notebook-Dienst hochgeladen.

	![Grafik 25][25]

Zum Ausführen des IPython Notebook-Beispiels oder der Python-Skriptdatei sind die folgenden Python-Pakete erforderlich. Wenn Sie den AzureML IPython Notebook-Dienst verwenden, wurden diese Pakete bereits vorinstalliert.

	- pandas
	- numpy
	- matplotlib
	- pyodbc
	- PyTables

Die empfohlene Reihenfolge beim Erstellen fortgeschrittener Analyselösungen mit großen Datenmengen in AzureML lautet wie folgt:

- Einlesen eines kleinen Teils der Daten in ein DataFrame im Speicher
- Durchführen von Visualisierungen und Suchvorgängen mit den Beispieldaten
- Experimentieren mit der Featureentwicklung anhand der Beispieldaten
- Bei umfangreicherer Datendurchsuchung, Datenbearbeitung und Featureentwicklung direktes Ausführen von SQL-Abfragen für das SQL Data Warehouse mithilfe von Python
- Treffen von Entscheidungen zum Umfang von Stichproben für die Modellerstellung in Azure Machine Learning

Es folgen einige Beispiele für das Durchsuchen von Daten, die Datenvisualisierung und die Featureentwicklung. Weitere Datensuchvorgänge finden Sie im IPython Notebook-Beispiel und in der Python-Beispielskriptdatei.

### Initialisieren von Datenbank-Anmeldeinformationen

Initialisieren Sie die Datenbank-Verbindungseinstellungen in den folgenden Variablen:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### Erstellen der Datenbankverbindung

Hier ist die Verbindungszeichenfolge aufgeführt, die die Verbindung mit der Datenbank erstellt.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### Melden der Anzahl von Zeilen und Spalten in der Tabelle <nyctaxi_trip>

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Gesamtanzahl von Zeilen = 173.179.759  
- Gesamtanzahl von Spalten = 14

### Melden der Anzahl von Zeilen und Spalten in der Tabelle <nyctaxi_fare>

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Gesamtanzahl von Zeilen = 173.179.759  
- Gesamtanzahl von Spalten = 11

### Einlesen einer kleinen Datenprobe aus der SQL Data Warehouse-Datenbank

    t0 = time.time()

	query = '''
		SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
			f.tolls_amount, f.total_amount, f.tip_amount
		FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
		WHERE datepart("mi",t.pickup_datetime) = 1
		AND   t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Zeit für das Einlesen der Beispieltabelle = 14,096495 Sekunden Anzahl der abgerufenen Zeilen und Spalten = (1000, 21)

### Deskriptive Statistik

Jetzt können die erfassten Daten durchsucht werden. Wir beginnen mit einem Blick auf einige deskriptive Statistiken für das Feld **trip\_distance** (oder andere Felder, die Sie angeben möchten):

    df1['trip_distance'].describe()

### Visualisierung: Beispiel für ein Boxplotdiagramm

Als Nächstes sehen wir uns das Boxplotdiagramm für die Fahrtentfernungen an, um die Quantile zu visualisieren.

    df1.boxplot(column='trip_distance',return_type='dict')

![Grafik 1][1]

### Visualisierung: Beispiel für ein Verteilungsdiagramm

Diagramme, die die Verteilung und ein Histogramm für die Stichproben der Fahrtentfernungen visualisieren.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Grafik 2][2]

### Visualisierung: Balken- und Liniendiagramme

In diesem Beispiel teilen wir die Fahrtentfernungen in fünf Klassifizierungen auf und visualisieren die Klassifizierungsergebnisse.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Die oben genannte Klassifizierungsverteilung können wir wie folgt in einem Balken- oder Liniendiagramm darstellen.

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Grafik 3][3]

und

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Grafik 4][4]

### Visualisierung: Beispiele für ein Punktdiagramm

Wir zeigen ein Punktdiagramm zwischen **trip\_time\_in\_secs** und **trip\_distance**, um zu ermitteln, ob es Korrelationen gibt.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Grafik 6][6]

Auf ähnliche Weise können wir die Beziehung zwischen **rate\_code** und **trip\_distance** überprüfen.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Grafik 8][8]


### Durchsuchen von Stichprobendaten mit SQL-Abfragen in IPython Notebook

In diesem Abschnitt untersuchen wir die Datenverteilungen anhand der Stichprobendaten in der zuvor neu erstellten Tabelle. Beachten Sie, dass ähnliche Suchvorgänge anhand der ursprünglichen Tabellen ausgeführt werden können.

#### Durchsuchen: Melden der Anzahl von Zeilen und Spalten in der Stichprobentabelle

	nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
	print 'Number of rows in sample = %d' % nrows.iloc[0,0]

	ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
	print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### Durchsuchen: Verteilung von Fahrten mit/ohne Trinkgeld

	query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

	pd.read_sql(query, conn)

#### Durchsuchen: Verteilung von Trinkgeld nach Klasse

	query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
	'''

	tip_class_dist = pd.read_sql(query, conn)

#### Durchsuchen: Darstellen der Trinkgeldverteilung nach Klasse

	tip_class_dist['tip_freq'].plot(kind='bar')

![Grafik 26][26]


#### Durchsuchen: Tägliche Verteilung der Fahrten

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### Durchsuchen: Verteilung der Fahrten nach "medallion"

    query = '''
		SELECT medallion,count(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY medallion
	'''

	pd.read_sql(query,conn)

#### Durchsuchen: Verteilung der Fahrten nach „medallion“ und „hack\_license“

	query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
	pd.read_sql(query,conn)


#### Durchsuchen: Verteilung der Fahrtzeit

	query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
	pd.read_sql(query,conn)

#### Durchsuchen: Verteilung der Fahrtentfernungen

	query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
	pd.read_sql(query,conn)

#### Durchsuchen: Verteilung der Zahlungsart

	query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
	pd.read_sql(query,conn)

#### Überprüfen der endgültigen Form der Funktionstabelle

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Entwickeln von Modellen in Azure Machine Learning

Wir können nun mit der Modellerstellung und -bereitstellung in [Azure Machine Learning](https://studio.azureml.net) fortfahren. Die Daten können jetzt für die oben beschriebenen Vorhersageprobleme verwendet werden:

1. **Binäre Klassifizierung**: Zur Vorhersage, ob ein Trinkgeld für eine Fahrt bezahlt wird.

2. **Multiklassenklassifizierung**: Zur Vorhersage des Trinkgeldbereichs gemäß den zuvor definierten Klassen.

3. **Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für eine Fahrt.



Melden Sie sich zum Starten der Modellierungsübung im **Azure Machine Learning**-Arbeitsbereich an. Wenn Sie noch keinen Machine Learning-Arbeitsbereich erstellt haben, lesen Sie unter [Erstellen eines Azure ML-Arbeitsbereichs](machine-learning-create-workspace.md) nach.

1. Informationen zu den ersten Schritten in Azure Machine Learning finden Sie unter [Was ist Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md).

2. Melden Sie sich in [Azure Machine Learning Studio](https://studio.azureml.net) an.

3. Die Startseite von Studio enthält eine Vielzahl an Informationen, Videos, Tutorials, Links zu Modulreferenzen und andere Ressourcen. Weitere Informationen zu Azure Machine Learning finden Sie in der [Azure Machine Learning-Dokumentation](http://azure.microsoft.com/documentation/services/machine-learning/).

Ein typisches Trainingsexperiment umfasst die folgenden Schritte:

1. Erstellen eines **+NEW**-Experiments
2. Einbringen der Daten in Azure ML.
3. Vorverarbeiten, Transformieren und Ändern der Daten nach Bedarf
4. Generieren von Funktionen nach Bedarf
5. Aufteilen der Daten in DataSets für Training/Überprüfung/Tests (oder Verwenden verschiedener DataSets für alles)
6. Auswählen eines oder mehrerer Algorithmen für das maschinelle Lernen in Abhängigkeit vom zu lösenden Lernproblem wie binäre Klassifizierung, Multiklassenklassifizierung, Regression
7. Trainieren eines oder mehrerer Modelle mit dem Trainings-DataSet
8. Bewerten des Validierungs-DataSets mithilfe der trainierten Modelle
9. Evaluieren der Modelle zur Berechnung der relevanten Kennzahlen für das Lernproblem
10. Optimieren der Modelle und Auswählen des geeignetsten Modells für die Bereitstellung

Sie haben in dieser Übung bereits die Daten in SQL Data Warehouse untersucht und bearbeitet und sich für eine Stichprobengröße für die Erfassung in Azure ML entschieden. Im Folgenden ist das Verfahren zum Erstellen eines oder mehrerer der Vorhersagemodelle aufgeführt:

1. Erfassen Sie die Daten in Azure ML mithilfe des [Reader][reader]-Moduls im Bereich **Data Input and Output**. Weitere Informationen finden Sie auf der Referenzseite zum [Reader][reader]-Modul.

	![Azure ML-Reader][17]

2. Wählen Sie **Azure SQL-Datenbank** als **Datenquelle** im **Eigenschaften**bereich aus.

3. Geben Sie den DNS-Namen für die Datenbank im Feld **Datenbankservername** ein. Format: `tcp:<your_virtual_machine_DNS_name>,1433`

4. Geben Sie den **Datenbanknamen** in das entsprechende Feld ein.

5. Geben Sie den *SQL-Benutzernamen* unter **Server user account name** und das *Kennwort* unter **Server user account password** ein.

6. Aktivieren Sie die Option **Accept any server certificate**.

7. Fügen Sie im Textbereich für die **Datenbankabfrage** die Abfrage ein, die die erforderlichen Datenbankfelder (einschließlich berechneter Felder wie die Bezeichner) extrahiert und die Daten auf die gewünschte Stichprobengröße reduziert.

Ein Beispiel für ein binäres Klassifizierungsexperiment zum Lesen von Daten direkt aus der SQL Data Warehouse-Datenbank wird in der folgenden Abbildung gezeigt. (Ersetzen Sie die Tabellennamen „nyctaxi\_trip“ und „nyctaxi\_fare“ durch den Schemanamen und die Tabellennamen, die Sie in Ihrer exemplarischen Vorgehensweise verwendet haben.) Ähnliche Experimente können für Multiklassenklassifizierungen und Regressionsprobleme erstellt werden.

![Azure ML-Schulung][10]

> [AZURE.IMPORTANT]In den Modellierungsbeispielen für Datenextraktion und Stichprobengenerierung in den vorherigen Abschnitten sind **alle Bezeichner für die drei Modellierungsübungen in der Abfrage enthalten**. Ein wichtiger (erforderlicher) Schritt in den einzelnen Modellierungsübungen ist das **Ausschließen** unnötiger Bezeichner für die anderen beiden Probleme und alle anderen **Zielverluste**. Wenn Sie z. B. eine binäre Klassifizierung anwenden, verwenden Sie den Bezeichner **tipped** und schließen die Felder **tip\_class**, **tip\_amount** und **total\_amount** aus. Letztere sind Zielverluste, da sie das bezahlte Trinkgeld beinhalten.
>
> Um nicht benötigte Spalten oder Zielverluste auszuschließen, können Sie das Modul [Project Columns][project-columns] oder den [Metadaten-Editor][metadata-editor] verwenden. Weitere Informationen finden Sie auf den Referenzseiten [Project Columns][project-columns] und [Metadaten-Editor][metadata-editor].

## <a name="mldeploy"></a>Bereitstellen von Modellen in Azure Machine Learning

Wenn das Modell fertig ist, können Sie es problemlos als Webdienst direkt aus dem Experiment heraus bereitstellen. Weitere Informationen zum Bereitstellen von Azure ML-Webdiensten finden Sie unter [Bereitstellen von Azure Machine Learning-Webdiensten](machine-learning-publish-a-machine-learning-web-service.md).

So stellen Sie einen neuen Webdienst bereit:

1. Erstellen Sie ein Bewertungsexperiment.
2. Stellen Sie den Webdienst bereit.

Zum Erstellen eines Bewertungsexperiments aus einem **beendeten** Trainingsexperiment klicken Sie auf der unteren Aktionsleiste auf **CREATE SCORING EXPERIMENT**.

![Azure-Bewertung][18]

Azure Machine Learning versucht, ein Bewertungsexperiment basierend auf den Komponenten des Trainingsexperiments zu erstellen. Insbesondere werden dabei folgende Schritte durchgeführt:

1. Speichern des trainierten Modells und Entfernen der Modelltrainings-Module
2. Ermitteln eines logischen **Eingabeports** für das erwartete Eingabedatenschema
3. Ermitteln eines logischen **Ausgabeport**s für das erwartete Ausgabeschema für den Webdienst

Wenn das Bewertungsexperiment erstellt wurde, überprüfen Sie es und passen es bei Bedarf an. Eine typische Anpassung besteht darin, das Eingabe-DataSet und/oder die Abfrage durch ausgeschlossene Bezeichnerfelder zu ersetzen, da diese nicht verfügbar sein werden, wenn der Dienst aufgerufen wird. Es empfiehlt sich möglicherweise auch, die Größe des Eingabe-DataSets und/oder der Abfrage auf so wenige DataSets zu reduzieren, dass gerade das Eingabeschema ermittelt werden kann. Für den Ausgabeport ist es üblich, alle Eingabefelder auszuschließen und nur die **bewerteten Bezeichner** und die **bewerteten Wahrscheinlichkeiten** mit dem Modul [Project Column][project-columns]s in die Ausgabe einzuschließen.

In der folgenden Abbildung finden Sie ein Beispiel für ein Bewertungsexperiment. Wenn Sie die Bereitstellung fertig vorbereitet haben, klicken Sie auf der unteren Aktionsleiste auf die Schaltfläche **PUBLISH WEB SERVICE**.

![Azure ML-Veröffentlichung][11]


## Zusammenfassung
Zusammenfassend haben Sie in diesem Tutorial eine Azure Data Science-Umgebung erstellt und mit einem großen öffentlichen Dataset gearbeitet und dabei alle Schritte des Cortana Analytics-Prozesses durchlaufen: von der Datenerfassung über das Modelltraining bis hin zur Bereitstellung eines Azure Machine Learning-Webdiensts.

### Lizenzinformationen

Diese exemplarische Vorgehensweise und die zugehörigen Skripts und IPython Notebook(s) werden von Microsoft unter MIT-Lizenz bereitgestellt. Weitere Informationen finden Sie in der Datei LICENSE.txt im Verzeichnis mit dem Beispielcode auf GitHub.

## Referenzen

• [Downloadseite von Andrés Monroy mit den New Yorker Taxidaten](http://www.andresmh.com/nyctaxitrips/) • [FOILing NYC’s Taxi Trip Data (in englischer Sprache) von Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [NYC Taxi and Limousine Commission Research and Statistics (in englischer Sprache)](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0114_2016-->