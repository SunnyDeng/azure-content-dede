<properties 
	pageTitle="Analysieren von Daten zu Flugverspätungen mit Hive in Linux-basiertem HDInsight | Microsoft Azure" 
	description="Hier erfahren Sie, wie Sie Flugverspätungsdaten mithilfe von Hive in einem Linux-basierten HDInsight-Cluster analysieren und anschließend die Daten mithilfe von Sqoop in SQL-Datenbank exportieren." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="larryfr"/>

#Analysieren von Flugverspätungsdaten mit Hive in HDInsight

Hier erfahren Sie, wie Sie Flugverspätungsdaten mithilfe von Hive in einem Linux-basierten HDInsight-Cluster analysieren und anschließend die Daten mithilfe von Sqoop in die Azure SQL-Datenbank exportieren.

> [AZURE.NOTE]Einzelne Teile dieses Dokuments können zwar für Windows-basierte HDInsight-Cluster (beispielsweise Python und Hive) verwendet werden. Zahlreiche Schritte gelten jedoch spezifisch für Linux-basierte Cluster. Schritte für Windows-basierte Cluster finden Sie unter [Analysieren von Flugverspätungsdaten mit Hive in HDInsight](hdinsight-analyze-flight-delay-data.md).

###Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Einen HDInsight-Cluster__. Schritte zum Erstellen eines neuen Linux-basierten HDInsight-Clusters finden Sie unter [Erste Schritte bei der Verwendung von Hadoop mit Hive in HDInsight unter Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

- __Azure SQL-Datenbank__. Sie verwenden eine Azure SQL-Datenbank als Zieldatenspeicher. Falls Sie noch keine SQL-Datenbank besitzen, lesen Sie das Thema [Erstellen und Konfigurieren einer Azure SQL-Datenbank](../sql-database/sql-database-create-configure.md).

- __Azure-Befehlszeilenschnittstelle__ Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht installiert haben, finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) weitere Schritte.


##Herunterladen der Flugdaten

1. Rufen Sie die Website von [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA) auf.
2. Wählen Sie auf der Website die folgenden Werte aus:

	| Name | Wert | | Filterjahr | 2013 | | Filterzeitraum | Januar | | Felder | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Entfernen Sie die Häkchen bei allen anderen Feldern. |

3. Klicken Sie auf **Download**.

##Hochladen der Daten

1. Verwenden Sie den folgenden Befehl, um die ZIP-Datei in den Hauptknoten des HDInsight-Clusters hochzuladen:

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Ersetzen Sie __FILENAME__ durch den Namen der ZIP-Datei. Ersetzen Sie __USERNAME__ durch die SSH-Anmeldedaten für den HDInsight-Cluster. Ersetzen Sie CLUSTERNAME durch den Namen des HDInsight-Clusters.
	
	> [AZURE.NOTE]Wenn Sie für die Authentifizierung Ihrer SSH-Anmeldung ein Kennwort verwenden, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben. Beispiel: `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Wenn der Upload abgeschlossen ist, stellen Sie über SSH eine Verbindung mit dem Cluster her:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Weitere Informationen zum Verwenden von SSH mit Linux-basierten HDInsight-Clustern finden Sie in den folgenden Artikeln:
	
	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows)
	
3. Extrahieren Sie nach dem Herstellen einer Verbindung die ZIP-Datei wie folgt:

		unzip FILENAME.zip
	
	Dadurch wird eine CSV-Datei extrahiert, die ungefähr 60 MB groß ist.
	
4. Erstellen Sie folgendermaßen ein neues Verzeichnis im WASB (dem von HDInsight verwendeten verteilten Datenspeicher), und kopieren Sie die Datei:

	hadoop fs -mkdir -p /tutorials/flightdelays/data hadoop fs -copyFromLocal FILENAME.csv /tutorials/flightdelays/data/FILENAME.csv
	
##Erstellen und Ausführen von HiveQL

Importieren Sie mit den folgenden Schritten Daten aus der CSV-Datei in eine Hive-Tabelle namens __delays__.

1. Erstellen und bearbeiten Sie folgendermaßen eine neue Datei mit dem Namen __flightdelays.hql__:

		nano flightdelays.hql
		
	Fügen Sie Folgendes als Inhalt der Datei hinzu:
	
		DROP TABLE delays_raw;
		-- Creates an external table over the csv file
		CREATE EXTERNAL TABLE delays_raw (
			YEAR string,
			FL_DATE string,
			UNIQUE_CARRIER string,
			CARRIER string,
			FL_NUM string,
			ORIGIN_AIRPORT_ID string,
			ORIGIN string,
			ORIGIN_CITY_NAME string,
			ORIGIN_CITY_NAME_TEMP string,
			ORIGIN_STATE_ABR string,
			DEST_AIRPORT_ID string,
			DEST string,
			DEST_CITY_NAME string,
			DEST_CITY_NAME_TEMP string,
			DEST_STATE_ABR string,
			DEP_DELAY_NEW float,
			ARR_DELAY_NEW float,
			CARRIER_DELAY float,
			WEATHER_DELAY float,
			NAS_DELAY float,
			SECURITY_DELAY float,
			LATE_AIRCRAFT_DELAY float)
		-- The following lines describe the format and location of the file
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE
		LOCATION '/tutorials/flightdelays/data';
		
		-- Drop the delays table if it exists
		DROP TABLE delays;
		-- Create the delays table and populate it with data
		-- pulled in from the CSV file (via the external table defined previously)
		CREATE TABLE delays AS
		SELECT YEAR AS year,
		    FL_DATE AS flight_date,
		    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
		    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
		    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
		    ORIGIN_AIRPORT_ID AS origin_airport_id,
		    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
		    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
		    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
		    DEST_AIRPORT_ID AS dest_airport_id,
		    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
		    substring(DEST_CITY_NAME,2) AS dest_city_name,
		    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
		    DEP_DELAY_NEW AS dep_delay_new,
		    ARR_DELAY_NEW AS arr_delay_new,
		    CARRIER_DELAY AS carrier_delay,
		    WEATHER_DELAY AS weather_delay,
		    NAS_DELAY AS nas_delay,
		    SECURITY_DELAY AS security_delay,
		    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
		FROM delays_raw;
		
2. Drücken Sie zum Speichern der Datei __STRG+X__ und anschließend __Y__

3. Starten Sie Hive folgendermaßen, und führen Sie die Datei __flightdelays.hql__ aus:

		hive -i flightdelays.hql
		
	Dadurch wird die Datei ausgeführt und anschließend eine `hive>`-Eingabeaufforderung geöffnet.

4. Wenn Sie die `hive>`-Eingabeaufforderung erhalten, rufen Sie die Daten wie folgt aus den importierten Flugverspätungsdaten ab.

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	Dadurch wird eine Liste von Orten, in denen Verspätungen infolge des Wetters auftraten, sowie die durchschnittliche Verspätung abgerufen. Die Liste wird anschließend in `/tutorials/flightdelays/output` gespeichert. Sqoop liest später die Daten an diesem Speicherort und exportiert sie in die Azure SQL-Datenbank.

##Erstellen einer SQL-Datenbank

Führen Sie folgenden Schritte aus, um eine Azure SQL-Datenbank zu erstellen: Diese wird zum Speichern von Daten verwendet, die über Sqoop aus HDInsight exportiert wurden.

1. Verwenden Sie in der Entwicklungsumgebung, in der die Azure-Befehlszeilenschnittstelle installiert ist, den folgenden Befehl zum Erstellen einer neuen Azure SQL-Datenbank:

		azure sql server create <adminLogin> <adminPassword> <region>

	Beispiel: `azure sql server create admin password "West US"`.

	Wenn der Befehl abgeschlossen ist, erhalten Sie eine Antwort ähnlich der folgenden:

		info:    Executing command sql server create
		+ Creating SQL Server
		data:    Server Name i1qwc540ts
		info:    sql server create command OK

> [AZURE.IMPORTANT]Beachten Sie den Servernamen, der von diesem Befehl zurückgegeben wird. Dies ist der kurze Name des erstellten SQL-Datenbankservers. Der vollqualifizierte Domänenname (FQDN) lautet `<shortname>.database.windows.net`.

2. Verwenden Sie den folgenden Befehl zum Erstellen einer Datenbank mit dem Namen **sqooptest** auf dem SQL-Datenbankserver:

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Nach seinem Abschluss wird die Meldung "OK" zurückgegeben.

	> [AZURE.NOTE]Wenn Sie eine Fehlermeldung über fehlenden Zugriff erhalten, müssen Sie die IP-Adresse der Client-Arbeitsstation der Firewall der SQL-Datenbank mit dem folgenden Befehl hinzufügen:
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Erstellen einer SQL-Datenbanktabelle

> [AZURE.NOTE]Es gibt viele Möglichkeiten, zum Erstellen einer Tabelle eine Verbindung mit SQL Database herzustellen. Die folgenden Schritte verwenden [FreeTDS](http://www.freetds.org/) aus dem HDInsight-Cluster.

1. Verwenden Sie SSH, um eine Verbindung mit dem Linux-basierten HDInsight-Cluster herzustellen, und führen Sie die folgenden Schritte in der SSH-Sitzung aus.

3. Geben Sie den folgenden Befehl für die Installation von FreeTDS ein:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Sobald FreeTDS installiert wurde, verwenden Sie folgenden Befehl, um sich mit dem zuvor erstellten SQL-Datenbank-Server zu verbinden.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Eine Ausgabe ähnlich der folgenden wird angezeigt:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Geben Sie bei der Eingabeaufforderung `1>` folgende Zeilen ein:

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Dadurch wird eine neue Tabelle namens __delays__ mit einem gruppierten Index (erforderlich für die SQL-Datenbank) erstellt.

    Stellen Sie wie folgt sicher, dass die Tabelle erstellt wurde:

        SELECT * FROM information_schema.tables
        GO

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     delays      BASE TABLE

8. Geben Sie zum Beenden des Dienstprogramms tsql an der Eingabeaufforderung `1>` die Zeichenfolge `exit` ein.
	
##Exportieren von Daten mit Sqoop

1. Verwenden Sie den folgenden Befehl, um einen Link zum SQL Server JDBC-Treiber aus dem Sqoop-Verzeichnis "lib" zu erstellen. Dies ermöglicht Sqoop die Nutzung dieses Treibers zur Kommunikation mit der SQL-Datenbank.

		sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

2. Verwenden Sie den folgenden Befehl, um zu überprüfen, ob Sqoop Ihre SQL-Datenbank erreichen kann:

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	Dadurch sollte eine Liste der Datenbanken zurückgegeben werden, einschließlich der zuvor erstellten Datenbank „sqooptest“.

3. Verwenden Sie den folgenden Befehl, um Daten aus „hivesampletable“ in die Tabelle „mobiledata“ zu exportieren:

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasb:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	Dadurch wird Sqoop angewiesen, eine Verbindung mit der SQL-Datenbank bzw. der Datenbank „sqooptest“ herzustellen und Daten aus dem wasb:///tutorials/flightdelays/output (in dem wir zuvor die Ausgabe der Hive-Abfrage gespeichert haben) in die Tabelle „delays“ zu exportieren.

4. Nach Abschluss des Befehls stellen Sie wie folgt über TSQL eine Verbindung mit der Datenbank her:

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	Nachdem die Verbindung hergestellt wurde, überprüfen Sie mithilfe der folgenden Anweisungen, ob die Daten in die Tabelle „mobiledata“ exportiert wurden:
	
		SELECT * FROM delays
		GO

	Es sollte eine Liste der Tabellendaten angezeigt werden. Geben Sie `exit` ein, um das Dienstprogramm tsql zu beenden.

##<a id="nextsteps"></a> Nächste Schritte
Jetzt wissen Sie, wie Sie eine Datei in den Azure-Blobspeicher hochladen, eine Hive-Tabelle mit Daten aus dem Azure-Blobspeicher füllen, Hive-Abfragen ausführen und Sqoop zum Exportieren von Daten aus dem HDFS in eine Azure SQL-Datenbank verwenden können. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit HDInsight][hdinsight-get-started]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]
* [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]
* [Entwickeln von Python Hadoop-Streamingprogrammen für HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 

<!---HONumber=Oct15_HO3-->