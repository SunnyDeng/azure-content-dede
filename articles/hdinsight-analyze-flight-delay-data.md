<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with HDInsight" pageTitle="Analyze flight delay data using HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Analysieren von Daten zu Flugverspätungen mithilfe von HDInsight
================================================================

Hive ermöglicht die Ausführung eines MapReduce-Jobs über eine SQL-ähnliche Skriptsprache namens *[HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL)*, die zur Zusammenfassung, Abfrage und Analyse großer Datenmengen verwendet werden kann. In diesem Lernprogramm erfahren Sie, wie Sie mit Hive durchschnittliche Verspätungen für Flughäfen berechnen und dann mit Sqoop die Ergebnisse in SQL-Datenbank exportieren können.

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure HDInsight-Cluster. Informationen zur Bereitstellung eines HDInsight-Clusters finden Sie unter [Erste Schritte mit HDInsight](/de-de/documentation/articles/hdinsight-get-started/) oder [Bereitstellen von HDInsight-Clustern](/de-de/documentation/articles/hdinsight-provision-clusters/).
-   Eine Arbeitsstation, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).

**Geschätzter Zeitaufwand:** 30 Minuten

Dieses Lernprogramm umfasst folgende Punkte
-------------------------------------------

-   [Vorbereitung für das Lernprogramm](#prepare)
-   [Erstellen und Hochladen eines HiveQL-Skripts](#createscript)
-   [Ausführen eines HiveQL-Skripts](#executehqlscript)
-   [Exportieren der Ergebnisse in Azure-SQL-Datenbank](#exportdata)
-   [Nächste Schritte](#nextsteps)

Vorbereitung für das Lernprogramm
---------------------------------

In diesem Lernprogramm werden Daten zur termingemäßen Leistung von Airline-Flügen von [Research and Innovative Technology Administration, Bureau of Transportation Statistics](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (RITA) auf Ihrer Arbeitsstation verwendet. Sie führen Folgendes durch:

1.  Herunterladen der termingemäßen Leistungsdaten von der RITA-Website auf Ihre Arbeitsstation mit einem Webbrowser
2.  Hochladen der Daten in HDInsight mit Azure PowerShell
3.  Vorbereiten der SQL-Datenbank für Datenexport mit Azure PowerShell

**Verstehen des HDInsight-Speichers**

HDInsight verwendet zur Datenspeicherung den Azure-Blob-Speicher. Dieser heißt *WASB* oder *Azure-Speicher-Blob*. Der WASB ist die Implementierung von HDFS von Microsoft im Azure-Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/de-de/documentation/articles/hdinsight-use-blob-storage/).

Wenn Sie einen HDInsight-Cluster bereitstellen, wird ein Blob-Speichercontainer als Standarddateisystem festgelegt, wie in HDFS. Zusätzlich zu diesem Container können Sie während des Bereitstellungsprozesses weitere Container aus demselben Azure-Speicherkonto oder anderen Azure-Speicherkonten hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern](/de-de/documentation/articles/hdinsight-provision-clusters/).

Zur Vereinfachung des PowerShell-Skripts in diesem Lernprogramm sind alle Dateien im Standarddateisystem-Container gespeichert. Dieser liegt unter */tutorials/flightdelays*. Standardmäßig hat dieser Container den gleichen Namen wie das HDInsight-Cluster.

Die WASB-Syntax lautet folgendermaßen:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nur die Syntax *wasb://* wird für HDInsight-Cluster der Version 3.0 unterstützt. Die ältere Syntax *asv://* wird für HDInsight-Cluster der Versionen 2.1 und 1.6 unterstützt, jedoch nicht für HDInsight-Cluster der Version 3.0 oder höhere Versionen.

> Der WASB-Pfad ist der virtuelle Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/de-de/documentation/articles/hdinsight-use-blob-storage/).

Auf Dateien, die im Standarddateisystem-Container gespeichert sind, können Sie aus HDInsight zugreifen. Verwenden Sie dazu eine der folgenden URIs (verwenden Sie flightdelays.hql als Beispiel):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

    tutorials/flightdelays/flightdelays.hql

In der folgenden Tabelle sind die Dateien aufgeführt, die in diesem Lernprogramm verwendet werden:

<table border="1">
<tr><td><strong>Dateien</strong></td><td><strong>Beschreibung</strong></td></tr>
<tr><td>\tutotirals\flightdelays\data</td><td>Eingabeflugdaten für Hive</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Ergebnisse für Hive</td></tr>
<tr><td>\tutorials\flightdelays\flightdelays.hql</td><td>HiveQL-Skriptdatei</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Hadoop-Job-Status</td></tr>
</table>

**Verstehen der internen und externen Tabelle in Hive**

Sie benötigen die folgenden Informationen zu internen und externen Tabellen in Hive:

-   Mit dem Befehl CREATE TABLE erstellen Sie eine interne Tabelle. Die Datendatei muss sich im Standardcontainer befinden.
-   Der Befehl CREATE TABLE verschiebt die Datendatei in den Ordner /hive/warehouse/.
-   Mit dem Befehl CREATE EXTERNAL TABLE erstellen Sie eine externe Tabelle. Die Datendatei kann sich außerhalb des Standardcontainers befinden.
-   Der Befehl CREATE EXTERNAL TABLE verschiebt die Datendatei nicht.
-   Der Befehl CREATE EXTERNAL TABLE lässt keine Ordner im SPEICHERORT zu. Aus diesem Grund wird durch dieses Lernprogramm eine Kopie der Datei "sample.log" erstellt.

Weitere Informationen finden Sie unter [HDInsight: Hive Internal and External Tables Intro][cindygross-hive-tables].

> [WACOM.NOTE] Eine der HiveQL-Anweisungen erstellt eine externe Hive-Tabelle. Externe Hive-Tabellen erhalten die Datendatei am ursprünglichen Speicherort. Interne Hive-Tabellen bewegen die Datendate nach hive\\warehouse. Bei externen Hive-Tabellen muss die Datendatei im WASB-Container des Standarddateisystems enthalten sein. Wenn Sie die Flugdatendateien in einem anderen Container als dem standardmäßigen Blob-Container speichern möchten, müssen Sie die internen Hive-Tabellen verwenden.

**Herunterladen der Flugdaten**

1.  Rufen Sie die folgende Website auf: [Research and Innovative Technology Administration, Bureau of Transportation Statistics](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (RITA).
2.  Wählen Sie auf der Website die folgenden Werte aus:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Filter Year</td><td>2012</td></tr>
	<tr><td>Filter Period</td><td>January</td></tr>
	<tr><td>Fields:</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (Entfernen Sie die Häkchen bei allen anderen Feldern.)</td></tr>
	</table>

3.  Klicken Sie auf **Download**. Der Download jeder Datei kann bis zu 15 Minuten dauern.
4.  Entpacken Sie die Datei im Ordner **C:\\Tutorials\\FlightDelays\\Data**. Jede Datei ist eine CSV-Datei und hat eine Größe von ungefähr 60 GB.
5.  Geben Sie der Datei den Monat, für den sie Daten enthält, als neuen Namen. Die Datei mit Daten aus dem Monat Januar hieße dann beispielsweise *January.csv*.
6.  Wiederholen Sie Schritt 2 und 5, um eine Datei für jeden der 12 Monate des Jahres 2012 herunterzuladen. Für die Ausführung des Lernprogramms benötigen Sie mindestens eine Datei.

**Hochladen der Daten zu Flugverspätungen in den Azure-Blob-Speicher**

1.  Öffnen Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Legen Sie die ersten drei Variablen fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $ContainerName = "<BlobStorageContainerName>"
            
         $localFolder = "C:\Tutorials\FlightDelays\Data"
         $destFolder = "tutorials/flightdelays/data"
            
         $month = "January", "Feburary", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    Dies sind die Variablen und deren Beschreibung:
	<table border="1">
	<tr><td><strong>Variablenname</strong></td><td><strong>Beschreibung</strong></td></tr>
	<tr><td>$subscriptionName</td><td>Dies ist der Name Ihres Azure-Abonnements.</td></tr>
	<tr><td>$storageAccountName</td><td>TDies ist das Azure-Speicherkonto, mit dem die Flugdatendateien gespeichert werden. Es wird empfohlen, das Standard-Speicherkonto zu verwenden.</td></tr>
	<tr><td>$containerName</td><td>Dies ist der Azure-Blob-Speichercontainer, mit dem die Flugdatendateien gespeichert werden. Es wird empfohlen, den Blob-Container des Standarddateisystems für das HDInsight-Cluster zu verwenden. Er hat standardmäßig den gleichen Namen wie das HDInsight-Cluster.</td></tr>
	<tr><td>$localFolder</td><td>Dies ist auf Ihrer Arbeitsstation der Ordner, in dem die Dateien zu Flugverspätungen gespeichert sind.</td></tr>
	<tr><td>$destFolder</td><td>Dies ist der WASB-Pfad, unter dem die Daten zu Flugverspätungen hochgeladen werden. Beim Hadoop-Pfad (HDInsight) muss die Groß- und Kleinschreibung beachtet werden.</td></tr>
	<tr><td>$month</td><td>Wenn Sie nicht alle 12 Dateien heruntergeladen haben, müssen Sie diese Variable aktualisieren.</td></tr>
	</table>

4.  Führen Sie die folgenden Befehle aus, um die Dateien hochzuladen und aufzulisten:

         # Ein Speicherkontextobjekt erstellen
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         # Datei von der lokalen Arbeitsstation in den WASB kopieren        
         foreach ($item in $month){
             $fileName = "$localFolder\$item.csv"
             $blobName = "$destFolder/$item.csv"
            
             Write-Host "Copying $fileName to $blobName" -BackgroundColor Green
            
             Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
         }
            
         # In HDInsight hochgeladene Dateien auflisten
         Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

Wenn Sie für das Hochladen der Dateien eine andere Methode verwenden möchten, stellen Sie sicher, dass der Dateipfad *tutorials/flightdelays/data* lautet. Für den Zugriff auf die Dateien gilt folgende Syntax:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*Bei tutorials/flightdelays/data* handelt es sich um den virtuellen Ordner, den Sie beim Hochladen der Dateien erstellt haben. Überprüfen Sie, dass 12 Dateien vorhanden sind, eine für jeden Monat.

**Vorbereiten der SQL-Datenbank**

1.  Öffnen Sie Azure PowerShell.
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Legen Sie die ersten sechs Variablen im folgenden Skript fest, und führen Sie danach die Befehle aus:

         # Name des Azure-Abonnements
         $subscriptionName = "<WindowsAzureSubscriptionName>"
                    
         # SQL-Datenbank-Servervariablen
         $sqlDatabaseServer = ""  # Geben Sie den Namen des Azure-SQL-Datenbankservers an, wenn Sie einen erstellt haben. Verwenden Sie andernfalls "".
         $sqlDatabaseUsername = "<SQLDatabaseUserName>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
         $sqlDatabaseLocation = "<MicrosoftDataCenter>"   # Beispielsweise West US
            
         # SQL-Datenbankvariablen
         $sqlDatabaseName = ""  # Geben Sie den Namen der Datenbank an, wenn Sie eine erstellt haben.  Verwenden Sie andernfalls "". Dann nimmt das Skript die Erstellung vor.
         $sqlDatabaseMaxSizeGB = 10

         # SQL-Abfrageparameter für die Erstellung der Tabelle AvgDelays
         $sqlDatabaseTableName = "AvgDelays"
         $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                 [origin_city_name] [nvarchar](50) NOT NULL,
                 [weather_delay] float,
             CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
             (
                 [origin_city_name] ASC
             )
             )"

    Dies sind die Variablen und deren Beschreibung:
	<table border="1">
	<tr><td><strong>Variablenname</strong></td><td><strong>Beschreibung</strong></td></tr>
	<tr><td>$subscriptionName</td><td>Name Ihres Azure-Abonnements</td></tr>
	<tr><td>$sqlDatabaseServer</td><td>Dies ist der Name des SQL-Datenbankservers, auf den Sqoop Daten exportiert. Wenn Sie dies unverändert lassen, nimmt der Server die Erstellung vor. Geben Sie andernfalls eine vorhandene SQL-Datenbank oder einen SQL-Server an.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Benutzername für SQL-Datenbank/SQL-Server</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Benutzerkennwort für SQL-Datenbank/SQL-Server</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>Dies wird nur verwendet, wenn das Skript einen SQL-Datenbankserver erstellen soll.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Dies ist der Name der SQL-Datenbank, in die Sqoop Daten exportiert. Wenn Sie dies unverändert lassen, nimmt der Server die Erstellung vor. Geben Sie andernfalls eine vorhandene SQL-Datenbank oder einen SQL-Server an.</td></tr>
	<tr><td>$sqlDatabaseMaxSizeGB</td><td>Dies wird nur verwendet, wenn das Skript eine SQL-Datenbank erstellen soll.</td></tr>
	</table>

4.  Führen Sie die folgenden Befehle aus, um einen SQL-Datenbankserver/eine SQL-Datenbank/eine SQL-Tabelle zu erstellen.

         # Auswählen des aktuellen Azure-Abonnements, falls mehrere Abonnements vorhanden sind
         Select-AzureSubscription $subscriptionName

         # Erstellen einer neuen Azure-SQL-Datenbank, falls erforderlich
         if ([string]::IsNullOrEmpty($sqlDatabaseServer))
         {
             $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
             Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

         }
         else
         {
             Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
         }

         # Erstellen einer neuen SQL-Datenbank, falls erforderlich
         if ([string]::IsNullOrEmpty($sqlDatabaseName))
         {
             $sqlDatabaseName = "HDISqoop"
             $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
             $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

             $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
            
             Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

         }
         else
         {
             Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
         }

         # Erstellen der Tabelle AvgDelays
         $conn = New-Object System.Data.SqlClient.SqlConnection
         $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
         $conn.open()
         $cmd = New-Object System.Data.SqlClient.SqlCommand
         $cmd.connection = $conn
         $cmd.commandtext = $sqlCreateAvgDelaysTable
         $cmd.executenonquery()

         $conn.close()

Erstellen und Hochladen eines HiveQL-Skripts
--------------------------------------------

Mit Azure PowerShell können Sie mehrere HiveQL-Anweisungen gleichzeitig ausführen oder die HiveQL-Anweisung in eine Skriptdatei paketieren. In diesem Lernprogramm erstellen Sie ein HiveQL-Skript. Die Skriptdatei muss in den WASB hochgeladen werden. Im nächsten Abschnitt führen Sie das Skript unter Verwendung von Azure PowerShell aus.

Das HiveQL-Skript führt Folgendes durch:

1.  **Löschen der Tabelle delays\_raw**, wenn diese Tabelle bereits vorhanden ist.
2.  **Erstellen der externen Hive-Tabelle delays\_raw**, die sich auf den WASB-Speicherort mit den Daten zu Flugverspätungen bezieht. Diese Abfrage legt fest, dass Felder durch "," getrennt und Zeilen mit "\\n" beendet werden. Dies stellt ein Problem dar, wenn Feldwerte Kommas *enthalten*, da Hive nicht zwischen einem Komma als Trennzeichen für Felder und einem Komma als Teil eines Feldwerts unterscheiden kann. (Letzteres ist der Fall bei Feldwerten für ORIGIN\_CITY\_NAME und DEST\_CITY\_NAME.) Zur Behebung dieses Problems erstellt die Abfrage TEMP-Spalten zur Aufbewahrung von Daten, die fehlerhaft in Spalten aufgeteilt sind.
3.  **Löschen der Tabelle delays**, wenn diese Tabelle bereits vorhanden ist.
4.  **Erstellen der Tabelle delays**; Es ist hilfreich, die Daten vor der weiteren Verarbeitung zu bereinigen. Diese Abfrage erstellt eine neue Tabelle *delays* aus der Tabelle *delays\_raw*. Beachten Sie, dass die TEMP-Spalten (wie zuvor erwähnt) nicht kopiert werden und dass die *substring*-Funktion verwendet wird, um Anführungszeichen aus den Daten zu entfernen.
5.  **Berechnen der durchschnittlichen Verspätungen aufgrund des Wetters und Gruppieren der Ergebnisse nach Name der Stadt;** Außerdem werden die Ergebnisse in den WASB ausgegeben. Beachten Sie, dass die Anfrage Apostrophe aus den Daten entfernt und Zeilen ausschließt, in denen der Wert für *weather\_dela*y *null* beträgt. Dies ist erforderlich, da Sqoop, das Sie später in diesem Lernprogramm verwenden, diese Werte standardmäßig nicht ordnungsgemäß behandelt.

Eine vollständige Liste der HiveQL-Befehle finden Sie unter [Hive Data Definition Language](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL) (Hive-Datendefinitionssprache, in englischer Sprache). Jeder HiveQL-Befehl muss mit einem Semikolon enden.

**Erstellen einer HiveQL-Skriptdatei**

1.  Öffnen Sie Azure PowerShell.
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

         Add-AzureAccount

3.  Legen Sie die ersten zwei Variablen fest, und führen Sie dann die Befehle aus.

         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName ="<BlobStorageContainerName>"

         $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
         $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
            
         $srcDataFolder = "tutorials/flightdelays/data" 
         $dstDataFolder = "tutorials/flightdelays/output"

    Dies sind die Variablen und deren Beschreibung:
	<table border="1">
	<tr><td><strong>Variablenname</strong></td><td><strong>Beschreibung</strong></td></tr>
	<tr><td>$storageAccountName</td><td>Dies ist das Azure-Speicherkonto, mit dem die HiveQL-Skriptdatei gespeichert wird. Die in diesem Lernprogramm bereitgestellten PowerShell-Skripts erfordern sowohl die Flugdatendateien, als auch die Skriptdatei, die sich in demselben Azure-Speicherkonto und Blob-Speichercontainer befinden.</td></tr>
	<tr><td>$containerName</td><td>Dies ist der Azure-Blob-Speichercontainer, mit dem die HiveQL-Skriptdatei gespeichert wird. Die in diesem Lernprogramm bereitgestellten PowerShell-Skripts erfordern sowohl die Flugdatendateien, als auch die Skriptdatei, die sich in demselben Azure-Speicherkonto und Blob-Speichercontainer befinden.</td></tr>
	<tr><td>$hqlLocalFileName</td><td>Dies ist der lokale Dateiname für das HiveQL-Skript, bevor es in den WASB hochgeladen wird. Zur Vereinfachung des PowerShell-Skripts schreiben Sie die Datei lokal und verwenden dann das Cmdlet "Set-AzureStorageBlobContent", um die Skriptdatei in HDInsight hochzuladen.</td></tr>
	<tr><td>$hqlBlobName</td><td>Dies ist der Skriptdateiname mit dem Pfad im WASB</td></tr>
	<tr><td>$srcDataFolder</td><td>Dies ist der Ordner im WASB, aus dem das HiveQL-Skript Daten bezieht.</td></tr>
	<tr><td>$dstDataFolder </td><td>Dies ist der Ordner im WASB, zu dem das HiveQL-Skript die Ergebnisse sendet. Im Verlauf des Lernprogramms verwenden Sie Sqoop, um die Daten aus diesem Ordner in Azure-SQL-Datenbank zu exportieren.</td></tr>
	</table>

4.  Führen Sie die folgenden Befehle aus, um die HiveQL-Anweisungen zu definieren:

         $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
            
         $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                 "YEAR string, " +
                 "FL_DATE string, " +
                 "UNIQUE_CARRIER string, " +
                 "CARRIER string, " +
                 "FL_NUM string, " +
                 "ORIGIN_AIRPORT_ID string, " +
                 "ORIGIN string, " +
                 "ORIGIN_CITY_NAME string, " +
                 "ORIGIN_CITY_NAME_TEMP string, " +
                 "ORIGIN_STATE_ABR string, " +
                 "DEST_AIRPORT_ID string, " +
                 "DEST string, " +
                 "DEST_CITY_NAME string, " +
                 "DEST_CITY_NAME_TEMP string, " +
                 "DEST_STATE_ABR string, " +
                 "DEP_DELAY_NEW float, " +
                 "ARR_DELAY_NEW float, " +
                 "CARRIER_DELAY float, " +
                 "WEATHER_DELAY float, " +
                 "NAS_DELAY float, " +
                 "SECURITY_DELAY float, " +
                 "LATE_AIRCRAFT_DELAY float) " +
             "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
             "LINES TERMINATED BY '\n' " +
             "STORED AS TEXTFILE " +
             "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 
            
         $hqlDropDelays = "DROP TABLE delays;"
            
         $hqlCreateDelays = "CREATE TABLE delays AS " +
             "SELECT YEAR AS year, " +
                 "FL_DATE AS flight_date, " +
                 "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                 "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                 "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                 "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                 "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                 "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                 "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                 "DEST_AIRPORT_ID AS dest_airport_id, " +
                 "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                 "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                 "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                 "DEP_DELAY_NEW AS dep_delay_new, " +
                 "ARR_DELAY_NEW AS arr_delay_new, " +
                 "CARRIER_DELAY AS carrier_delay, " +
                 "WEATHER_DELAY AS weather_delay, " +
                 "NAS_DELAY AS nas_delay, " +
                 "SECURITY_DELAY AS security_delay, " +
                 "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
             "FROM delays_raw;" 
            
         $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
             "SELECT regexp_replace(origin_city_name, '''', ''), " +
                 "avg(weather_delay) " +
             "FROM delays " +
             "WHERE weather_delay IS NOT NULL " +
             "GROUP BY origin_city_name;"
            
         $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5.  Führen Sie die folgenden Befehle aus, um die Hive-Skriptdatei auf der Arbeitsstation zu schreiben und sie dann in den WASB hochzuladen:

         # Das Hive-Skript in eine lokale Datei schreiben
         $hqlScript | Out-File $hqlLocalFileName -Encoding ascii
            
         # Ein Speicherkontextobjekt erstellen
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         # Die Datei von der lokalen Arbeitsstation in den WASB kopieren
         Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext
            
         # Die Skriptdatei aus dem WASB auflisten
         Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    Die Ausgabe sollte der Folgenden ähneln:

         Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3
            
         Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
         ----                              --------   ------                            -----------                       ------------                      ------------
         tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

Ausführen eines HiveQL-Skripts
------------------------------

Sie können mehrere Azure PowerShell-Cmdlets für das Ausführen von Hive verwenden. In diesem Lernprogramm wird Invoke-Hive verwendet. Weitere Methoden finden Sie unter [Verwenden von Hive mit HDInsight](/de-de/documentation/articles/hdinsight-use-hive/). Mit Invoke-Hive können Sie entweder eine HiveQL-Anweisung oder ein HiveQL-Skript ausführen. Verwenden Sie das HiveQL-Skript, das Sie erstellt und in den Azure-Blob-Speicher hochgeladen haben.

Es existiert ein bekanntes Problem bezüglich des Hive-Pfades. Eine Anleitung zur Behebung des Problems finden Sie im [TechNet Wiki](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx).

**Ausführen der Hive-Abfragen mit PowerShell**

1.  Öffnen Sie Azure PowerShell.
2.  Führen Sie den folgenden Befehl aus, um das aktuelle Verzeichnis zu ändern:

    cd \\Tutorials\\FlightDelays\\

    Dieser Schritt ist erforderlich, da Sie eine Kopie der Hive-Ergebnisse auf Ihre Arbeitsstation herunterladen. Sie haben standardmäßig keinen Schreibzugriff für diesen PowerShell-Ordner.

3.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

         Add-AzureAccount

4.  Legen Sie die ersten drei Variablen fest, und führen Sie diese dann aus:

         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<WindowsAzureBlobStorageContainerName>"
         $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
         $outputBlobName = "tutorials/flightdelays/output/000000_0"

    Dies sind die Variablen und deren Beschreibung:
	<table border="1">
	<tr><td><strong>Variablenname</strong></td><td><strong>Beschreibung</strong></td></tr>
	<tr><td>$clusterName</td><td>Dies ist der HDInsight-Cluster, der das Hive-Skript und den Sqoop-Export ausführt.</td></tr>
	<tr><td>$storageAccountName</td><td>Dies ist das Azure-Speicherkonto, mit dem das HiveQL-Skript gespeichert wird. Siehe <a href = "#createScript">Erstellen und Hochladen eines HiveQL-Skripts.</a>.</td></tr>
	<tr><td>$containerName</td><td>Dies ist der Azure-Blob-Speichercontainer, mit dem das HiveQL-Skript gespeichert wird. Siehe <a href = "#createScript">Erstellen und Hochladen eines HiveQL-Skripts</a>.</td></tr>
	<tr><td>$hqlScriptFile</td><td>Dies ist die URI für die HiveQL-Skriptdatei.</td></tr>
	<tr><td>$outputBlobName</td><td>Dies ist die HiveQL-Skriptausgabedatei. Der Standardname lautet *000000_0*.</td></tr>
	</table>

5.  Führen Sie den folgenden Befehl aus, um Hive aufzurufen.

         Use-AzureHDInsightCluster $clusterName
                    
         Invoke-Hive -File $hqlScriptFile

6.  Führen Sie den folgenden Befehl aus, um die Ergebnisse zu überprüfen:

         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
            
         Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 
            
         cat ".\$outputBlobName" | findstr /B "Ch"

    Die Ausgabe sollte der Folgenden ähneln:

         Champaign/Urbana 19.023255813953487
         Charleston 24.93975903614458
         Charleston/Dunbar 16.954545454545453
         Charlotte 16.88616981831665
         Charlotte Amalie 12.88888888888889
         Charlottesville 25.444444444444443
         Chattanooga 19.883561643835616
         Cheyenne 9.875
         Chicago 16.77321649680922
         Chico 12.340909090909092
         Christiansted 12.318181818181818

    Zwischen jeder Stadt und Verzögerungszeit befindet sich ein Trennzeichen, das im PowerShell-Ausgabefenster nicht sichtbar ist. Es lautet "\\001". Sie verwenden dieses Trennzeichen, wenn Sie den Sqoop-Export ausführen.

Exportieren der Hive-Job-Ergebnisse in Azure-SQL-Datenbank
----------------------------------------------------------

Der letzte Schritt besteht darin, den Sqoop-Export auszuführen, um die Daten in die SQL-Datenbank zu exportieren. Zuvor in diesem Lernprogramm haben Sie die SQL-Datenbank und die Tabelle "AvgDelays" erstellt.

**Exportieren von Daten in die SQL-Datenbank**

1.  Öffnen Sie Azure PowerShell.
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

         Add-AzureAccount

3.  Legen Sie die ersten fünf Variablen fest, und führen Sie dann folgende Befehle aus:

         $clusterName = "<HDInsightClusterName>"
            
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseUserName = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
            
         $sqlDatabaseName = "<SQLDatabaseName>" # Der Standardname lautet "HDISqoop".
         $sqlDatabaseTableName = "AvgDelays" 

         $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
            
         $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Dies sind die Variablen und deren Beschreibung:
	<table border="1">
	<tr><td><strong>Variablenname</strong></td><td><strong>Hinweis</strong></td></tr>
	<tr><td>$clusterName</td><td>Dies ist der Name des HDInsight-Clusters.</td></tr>
	<tr><td>$sqlDatabaseServer</td><td>Dies ist der Name des SQL-Datenbankservers, auf den Sqoop Daten exportiert.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Dies ist der Benutzername für die SQL-Datenbank.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Dies ist das Benutzerkennwort für die SQL-Datenbank.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Dies ist der Name der SQL-Datenbank, in die Sqoop Daten exportiert. Der Standardname lautet *HDISqoop".  </td></tr>
	<tr><td>$sqlDatabaseTableName</td><td>Dies ist die SQL-Datenbank, in die Sqoop Daten exportiert. Der Standardname lautet AvgDelays. Dies ist die Tabelle, die Sie zuvor im Lernprogramm erstellt haben.</td></tr>
	<tr><td>$exportDir</td><td>Dies ist der Speicherort der Hive-Ausgabedatei. Sqoop exportiert die Dateien aus diesem Speicherort in die SQL-Datenbank.</td></tr>
	</table>

4.  Führen Sie den folgenden Befehl aus, um Daten zu exportieren:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by  $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput
        01 "
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Stellen Sie eine Verbindung zur SQL-Datenbank her, und zeigen Sie die durchschnittlichen Flugverspätungen nach Stadt geordnet in der Tabelle *AvgDelays* an:

    ![HDI.FlightDelays.AvgDelays.Dataset](./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png)

Nächste Schritte
----------------

Jetzt haben Sie erfahren, wie Sie Folgendes vornehmen können: Dateien in den Blob-Speicher hochladen, eine Hive-Tabelle mit Daten aus dem Blob-Speicher füllen, Hive-Abfragen ausführen und Sqoop verwenden, um Daten aus dem HDFS in Azure-SQL-Datenbank zu exportieren. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Getting Started with HDInsight](/de-de/documentation/articles/hdinsight-get-started/)
-   [Verwenden von Hive mit HDInsight](/de-de/documentation/articles/hdinsight-use-hive/)
-   [Verwenden von Oozie mit HDInsight](/de-de/documentation/articles/hdinsight-use-oozie/)
-   [Use Sqoop with HDInsight (Verwenden von Sqoop mit HDInsight, in englischer Sprache)](../hdinsight-use-sqoop/)
-   [Verwenden von Pig mit HDInsight](/de-de/documentation/articles/hdinsight-use-pig/)
-   [Entwickeln von Java MapReduce-Programmen für HDInsight](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [Entwickeln von C\# Hadoop-Streamingprogrammen für HDInsight](/de-de/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)

