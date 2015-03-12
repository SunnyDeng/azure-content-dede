<properties 
	pageTitle="Analysieren von Twitterdaten mit Hadoop in HDInsight | Azure" 
	description="Hier erfahren Sie, wie Sie mit Hive Twitter-Daten auf Hadoop in HDInsight analysieren können, um die Verwendungshäufigkeit eines bestimmten Wortes herauszufinden." 
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
	ms.topic="article" 
	ms.date="1/29/2015" 
	ms.author="jgao"/>

# Analysieren von Twitter-Daten mit Hadoop in HDInsight

Soziale Netzwerke sind einer der Haupt-Antriebsfaktoren für die Einführung von Big Data. Öffentliche APIs von Websites wie Twitter sind eine nützliche Datenquelle für Analyse und Verständnis beliebter Trends. In diesem Lernprogramm werden Sie mit der Twitter-Streaming-API Tweets abrufen und dann mithilfe von Apache Hive in HDInsight eine Liste der Twitter-Benutzer abrufen, die die meisten Tweets gesendet haben, die ein bestimmtes Wort enthalten.

> [WACOM.NOTE] Ein ähnliches Beispiel wird der HDInsight Sample Gallery hinzugefügt. Das folgende Channel 9-Video zeigt, wie das Beispiel ausgeführt wird:

<center><iframe width="854" height="510" src="https://www.youtube.com/embed/7ePbHot1SN4" frameborder="0" allowfullscreen></iframe></center>

##Themen in diesem Artikel

- [Voraussetzungen](#prerequisites)
- [Abrufen des Twitter-Feeds](#feed)
- [Erstellen eines HiveQL-Skripts](#script)
- [Verarbeiten der Daten mit Hive](#process)
- [Nächste Schritte](#nextsteps)

##<a id="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install]. Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* festlegen. Weitere Informationen finden Sie unter [Ausführen von Windows PowerShell-Skripts][powershell-script].

	Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

		Add-AzureAccount

	Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

		Select-AzureSubscription <AzureSubscriptionName>



- **Ein Azure HDInsight-Cluster**. Anleitungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HDInsight][Hdinsight-erste-Schritte] oder [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. Später in diesem Lernprogramm benötigen Sie den Namen des Clusters.

**Verstehen der HDInsight-Speicherung**

HDInsight verwendet Azure Blob-Speicher zur Datenspeicherung.  Dies wird *WASB* oder *Azure Storage - Blob* genannt. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage]. 

Wenn Sie einen HDInsight-Cluster bereitstellen, wird ebenso wie in HDFS ein Blob-Speichercontainer als Standarddateisystem festgelegt. Zusätzlich zu diesem Container können Sie während des Bereitstellungsprozesses weitere Container aus demselben Azure-Speicherkonto oder anderen Azure-Speicherkonten hinzufügen. Anleitungen zum Hinzufügen von zusätzlichen Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. 

> [AZURE.NOTE] Um das in diesem Lernprogramm verwendete PowerShell-Skript zu vereinfachen, werden alle Dateien im Container des Standarddateisystems unter */tutorials/twitter* gespeichert. Dieser Container hat standardmäßig denselben Namen wie der HDInsight-Cluster. Falls Sie einen anderen Container zum Speichern dieser Dateien verwenden, müssen Sie das Skript entsprechend anpassen.

Die WASB-Syntax lautet:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Nur die *wasb://*-Syntax wird in HDInsight-Clustern der Version 3.0 unterstützt. Die ältere  *asv://*-Syntax wird in HDInsight 2.1- und 1.6-Clustern unterstützt, nicht aber in HDInsight 3.0-Clustern; sie wird auch in späteren Versionen nicht unterstützt.

> Der WASB-Pfad ist ein virtueller Pfad.  Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage]. 

Auf Dateien, die im Standarddateisystem-Container gespeichert sind, kann in HDInsight über einen der folgenden URIs zugegriffen werden (als Beispiel wird hier tweets.txt verwendet):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

	tutorials/twitter/tweets.txt

In der folgenden Tabelle sind die in diesem Lernprogramm verwendeten Dateien aufgelistet:

<table border="1">
<tr><th>Dateien</th><th>Beschreibung</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Die Quelldaten für den Hive-Job.</td></tr>
<tr><td>/tutorials/twitter/output</td><td>Der Ausgabeordner für den Hive-Job. Der Standardname der Ausgabedatei des Hive-Jobs lautet <strong>000000_0</strong>. </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>Die HiveQL-Skriptdatei.</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>Der Status des Hadoop-Jobs.</td></tr>
</table>

##<a id="feed"></a>Abrufen des Twitter-Feeds

In diesem Lernprogramm verwenden Sie die [Twitter-Streaming-APIs][twitter-streaming-api]. Die spezielle Twitter-Streaming-API, die Sie verwenden werden, ist [statuses/filter][twitter-statuses-filter].

>[WACOM.NOTE] Eine Datei mit 10.000 Tweets und die Hive-Skriptdatei (siehe nächster Abschnitt) wurden in einen öffentlichen Blob-Container hochgeladen. Wenn Sie die hochgeladenen Dateien verwenden möchten, können Sie diesen Abschnitt überspringen. 

[Tweets-Daten](https://dev.twitter.com/docs/platform-objects/tweets) werden im JSON-Format gespeichert, das eine komplexe geschachtelte Struktur enthält. Anstatt viele Codezeilen in einer herkömmlichen Programmiersprache zu schreiben, können Sie diese verschachtelte Struktur in eine Hive-Tabelle transformieren, die anschließend mit einer SQL-ähnlichen Sprache namens HiveQL abgefragt werden kann. 

Twitter ermöglicht über OAuth den autorisierten Zugriff auf seine API. OAuth ist ein Authentifizierungsprotokoll, mit dem Benutzer einer Anwendung gestatten können, in ihrem Namen zu handeln, ohne ihr Kennwort weitergeben zu müssen. Weitere Informationen finden Sie unter [oauth.net](http://oauth.net/) oder im ausgezeichneten [Beginner's Guide to OAuth](http://hueniverse.com/oauth/) von Hueniverse.

Um OAuth zu verwenden, müssen Sie zunächst auf der Twitter-Entwicklerwebsite eine neue Anwendung erstellen.

**So erstellen Sie eine Twitter-Anwendung**

1. Melden Sie sich bei [https://apps.twitter.com/](https://apps.twitter.com/) an. Wenn Sie über kein Twitter-Konto verfügen, klicken Sie auf **Registriere Dich bei Twitter**.
2. Klicken Sie auf **Neue App erstellen**.
3. Geben Sie **Name**, **Beschreibung**, **Website** ein. Für das Feld "Website" können Sie eine URL erfinden. Die folgende Tabelle zeigt einige mögliche Beispielwerte:

	<table border="1">
	<tr><th>Feld</th><th>Wert</th></tr>
	<tr><td>Name</td><td>MyHDInsightApp</td></tr>
	<tr><td>Beschreibung</td><td>MyHDInsightApp</td></tr>
	<tr><td>Website</td><td>http://www.myhdinsightapp.com</td></tr>
	</table>
4. Aktivieren Sie das Kontrollkästchen **Ja, stimme zu**, und klicken Sie dann auf **Twitter-Anwendung erstellen**.
5. Klicken Sie auf die Registerkarte **Berechtigungen**. Die Standardberechtigung ist **Schreibgeschützt**. Diese Berechtigung reicht für dieses Lernprogramm aus. 
6. Klicken Sie auf die Registerkarte **Schlüssel und Zugriffstoken**.
7. Klicken Sie auf **Meinen Zugriffstoken erstellen**.
8. Klicken Sie rechts oben auf der Seite auf **OAuth testen**.
9. Notieren Sie sich **Kundenschlüssel**, **Geheimwort des Kunden**, **Zugriffstoken** und **Geheimwort für Zugriffstoken**. Sie benötigen diese Werte später im Lernprogramm.

In diesem Lernprogramm werden Sie PowerShell verwenden, um den Webdienstaufruf zu tätigen. Ein .NET C#-Beispiel finden Sie unter [Analysieren der Twitter-Stimmung in Echtzeit mit HBase in HDInsight ][hdinsight-hbase-twitter-sentiment]. Das andere beliebte Tool für Webdienstaufrufe ist [*Curl*][curl]. Curl kann [hier][curl-download] heruntergeladen werden.

>[AZURE.NOTE] Wenn Sie den Curl-Befehl in Windows verwenden, geben Sie für die Optionswerte doppelte anstelle einfacher Anführungszeichen ein.

**So rufen Sie Tweets ab**

1. Öffnen Sie Windows PowerShell ISE (geben Sie im Windows 8-Startbildschirm **PowerShell_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**. Siehe [Starten von Windows PowerShell unter Windows 8 und Windows][powershell-start]).

2. Kopieren Sie das folgende Skript in den Skriptbereich:

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name
		
		# Enter the Oauth information for your Twitter application
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		$destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob. 
		
		$trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
		#endregion
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writting Tweets into Blob storage
		Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		#end region
				
		# region - Format oauth strings	
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();
				
		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);
				
		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);
				
		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));
				
		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';
				
		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
		#endregion 
					
		#region - Read tweets	
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();
				
		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;
				
		Write-Host "Start stream reading ..." -ForegroundColor Green
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
				
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
				
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
			if ($inrec -ne "")
			{
				Write-Host "`n`t $count tweets received." -ForegroundColor Yellow
				
				$writeStream.WriteLine($inrec)
				$count ++
			}
				
			$inrec=$sReader.ReadLine()
		}
		#endregion
				
		#region - Write tweets to Blob storage
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
				
		$sReader.close()
		#endregion
		
		Write-Host "Completed!" -ForegroundColor Green

3. Legen Sie die ersten fünf bis acht Variablen im Skript fest:	

	<table border="1">
	<tr><th>Variable</th><th>Beschreibung</th></tr>
	<tr><td>$clusterName</td><td>Der Name des HDInsight-Clusters, auf dem die Anwendung ausgeführt werden soll.</td></tr><tr><td>$oauth_consumer_key</td><td>Dies ist der <strong>Consumer key</strong> der Twitter-Anwendung, den Sie beim Erstellen der Twitter-Anwendung notiert haben.</td></tr>
	<tr><td>$oauth_consumer_secret</td><td>Dies ist das <strong>Consumer secret</strong> , das Sie zuvor notiert haben.</td></tr>
	<tr><td>$oauth_token</td><td>Dies ist das <strong>Access token</strong> , das Sie zuvor notiert haben.</td></tr>
	<tr><td>$oauth_token_secret</td><td>Dies ist das <strong>Access token secret</strong> , das Sie zuvor notiert haben.</td></tr>	
	<tr><td>$destBlobName</td><td>Dies ist der Name des Ausgabe-Blobs.  Der Standardwert ist <strong>tutorials/twitter/data/tweets.txt</strong>. Wenn Sie den Standardwert ändern, müssen Sie die PowerShell-Skripts entsprechend aktualisieren.</td></tr>
	<tr><td>$trackString</td><td>Der Webdienst gibt Tweets zurück, die mit diesen Schlüsselwörtern verknüpft sind.  Der Standardwert ist <strong>Azure, Cloud, HDInsight</strong>. Wenn Sie den Standardwert ändern, müssen Sie die PowerShell-Skripts entsprechend aktualisieren.</td></tr>
	<tr><td>$lineMax</td><td>Der Wert bestimmt, wie viele Tweets das Skript liest. Das Lesen von 100 Tweets dauert etwa drei Minuten. Sie können einen größeren Wert festlegen, dann nimmt das Herunterladen jedoch mehr Zeit in Anspruch.</td></tr>

	</table>

5. Drücken Sie **F5**, um das Skript auszuführen. Wenn Sie auf Probleme stoßen, wählen Sie zur Problemumgehung alle Zeilen aus, und drücken Sie dann **F8**.
6. Am Ende der Ausgabe wird "Complete!" angezeigt. Eventuelle Fehlermeldungen werden rot dargestellt.

Für Validierungszwecke können Sie die Ausgabedatei **/tutorials/twitter/data/tweets.txt** in Ihrem Azure-Blob-Speicher mit einem Azure-Speicher-Explorer oder mit Azure PowerShell überprüfen.  Ein PowerShell-Beispielskript zum Auflisten von Dateien finden Sie unter [Verwenden von Blob-Speicher mit HDInsight][hdinsight-storage-powershell]. 



##<a id="script"></a>Erstellen eines HiveQL-Skripts

Mit Azure PowerShell können Sie mehrere HiveQL-Anweisungen gleichzeitig ausführen oder die HiveQL-Anweisung in eine Skriptdatei einfügen. In diesem Lernprogramm erstellen Sie ein HiveQL-Skript. Die Skriptdatei muss auf WASB hochgeladen werden. Im nächsten Abschnitt wird die Skriptdatei mit Azure PowerShell ausgeführt.

>[WACOM.NOTE] Die Hive-Skriptdatei und eine Datei mit 10.000 Tweets wurden in einen öffentlichen Blob-Container hochgeladen. Wenn Sie die hochgeladenen Dateien verwenden möchten, können Sie diesen Abschnitt überspringen.

Das HiveQL-Skript führt folgende Schritte aus:

1. **Löschen der Tabelle "tweets_raw"**, falls die Tabelle bereits vorhanden ist.
2. **Erstellen der Hive-Tabelle "tweets_raw"**. Diese temporäre strukturierte Hive-Tabelle dient zum Aufbewahren der Daten für die weitere ETL-Verarbeitung.  Informationen zur Partitionierung finden Sie im [Hive-Lernprogramm][apache-hive-tutorial].  
3. **Laden der Daten** vom Quellordner /tutorials/twitter/data. Der große Tweets-Datensatz im verschachtelten JSon-Format wurde nun in eine temporäre Hive-Tabellenstruktur transformiert.
3. **Löschen der Tabelle "tweets"**, falls die Tabelle bereits vorhanden ist.
4. **Erstellen der Tabelle "tweets"**. Bevor Sie den Tweets-Datensatz mit Hive abfragen können, müssen Sie einen weiteren ETL-Prozess ausführen. In diesem ETL-Prozess wird ein detaillierteres Tabellenschema für die Daten definiert, die Sie in der Tabelle "twitter_raw" gespeichert haben.  
5. **Einfügen der Overwrite-Tabelle**. Dieses komplexe Hive-Skript löst eine Reihe langer MapReduce-Jobs im Hadoop-Cluster aus.  Je nach Datensatz und Größe Ihres Clusters sollte dieser Vorgang ca. 10 Minuten dauern.
6. **Einfügen des Overwrite-Verzeichnisses**. Führen Sie eine Abfrage aus, und geben Sie den Datensatz in einer Datei aus. Diese Abfrage gibt eine Liste von Twitter-Benutzern zurück, deren Tweets das Wort "Azure" besonders häufig enthielten.

**So erstellen Sie ein Hive-Skript und laden es in Azure hoch**

1. Öffnen Sie Windows PowerShell ISE.
2. Kopieren Sie das folgende Skript in den Skriptbereich:

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter your HDInsight cluster name
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
				
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
				
		DROP TABLE tweets_raw;
		CREATE EXTERNAL TABLE tweets_raw (
			json_response STRING
		) 
		STORED AS TEXTFILE LOCATION '$sourceDataPath';
				
		DROP TABLE tweets;
		CREATE TABLE tweets
		(
			id BIGINT,
			created_at STRING,
			created_at_date STRING,
			created_at_year STRING,
			created_at_month STRING,
			created_at_day STRING,
			created_at_time STRING,
			in_reply_to_user_id_str STRING,
			text STRING,
			contributors STRING,
			retweeted STRING,
			truncated STRING,
			coordinates STRING,
			source STRING,
			retweet_count INT,
			url STRING,
			hashtags array<STRING>,
			user_mentions array<STRING>,
			first_hashtag STRING,
			first_user_mention STRING,
			screen_name STRING,
			name STRING,
			followers_count INT,
			listed_count INT,
			friends_count INT,
			lang STRING,
			user_location STRING,
			time_zone STRING,
			profile_image_url STRING,
			json_response STRING
		);
				
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		SELECT
			cast(get_json_object(json_response, '$.id_str') as BIGINT),
			get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
				when "Jan" then "01"
				when "Feb" then "02"
				when "Mar" then "03"
				when "Apr" then "04"
				when "May" then "05"
				when "Jun" then "06"
				when "Jul" then "07"
				when "Aug" then "08"
				when "Sep" then "09"
				when "Oct" then "10"
				when "Nov" then "11"
				when "Dec" then "12" end,
			substr (get_json_object(json_response, '$.created_at'),9,2),
			substr (get_json_object(json_response, '$.created_at'),12,8),
			get_json_object(json_response, '$.in_reply_to_user_id_str'),
			get_json_object(json_response, '$.text'),
			get_json_object(json_response, '$.contributors'),
			get_json_object(json_response, '$.retweeted'),
			get_json_object(json_response, '$.truncated'),
			get_json_object(json_response, '$.coordinates'),
			get_json_object(json_response, '$.source'),
			cast (get_json_object(json_response, '$.retweet_count') as INT),
			get_json_object(json_response, '$.entities.display_url'),
			array(	
				trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
			array(
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
			trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
			trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
			get_json_object(json_response, '$.user.screen_name'),
			get_json_object(json_response, '$.user.name'),
			cast (get_json_object(json_response, '$.user.followers_count') as INT),
			cast (get_json_object(json_response, '$.user.listed_count') as INT),
			cast (get_json_object(json_response, '$.user.friends_count') as INT),
			get_json_object(json_response, '$.user.lang'),
			get_json_object(json_response, '$.user.location'),
			get_json_object(json_response, '$.user.time_zone'),
			get_json_object(json_response, '$.user.profile_image_url'),
			json_response
		WHERE (length(json_response) > 500);
				
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		#endregion		
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writting the Hive script file
		Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
				
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
				
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
				
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		#endregion
		
		#region - Write the Hive script file to Blob storage		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		#endregion
				
		Write-Host "Completed!" -ForegroundColor Green

4. Legen Sie die ersten zwei Variablen in dem Skript fest:	

	<table border="1">
	<tr><th>Variable</th><th>Beschreibung</th></tr>
	<tr><td>$clusterName</td><td>Geben Sie den Namen des HDInsight-Clusters ein, in dem Sie die Anwendung ausführen möchten.</td></tr>
	<tr><td>$sourceDataPath</td><td>Der WASB-Speicherort, aus dem die Hive-Abfragen die Daten lesen. Sie müssen diese Variable nicht ändern.</td></tr>
	<tr><td>$outputPath</td><td>Der WASB-Speicherort, in den die Hive-Abfragen die Daten ausgeben. Sie müssen diese Variable nicht ändern.</td></tr>
	<tr><td>$hqlScriptFile</td><td>Der Speicherort und der Dateinamen der HiveQL-Skriptdatei. Sie müssen diese Variable nicht ändern.</td></tr>
	</table>

5. Drücken Sie **F5**, um das Skript auszuführen. Wenn Sie auf Probleme stoßen, wählen Sie zur Problemumgehung alle Zeilen aus, und drücken Sie dann **F8**.
6. Am Ende der Ausgabe wird "Complete!" angezeigt. Eventuelle Fehlermeldungen werden rot dargestellt.

Für Validierungszwecke können Sie die Ausgabedatei **/tutorials/twitter/data/tweets.hql** in Ihrem Azure-Blob-Speicher mit einem Azure-Speicher-Explorer oder mit Azure PowerShell überprüfen.  Ein PowerShell-Beispielskript zum Auflisten von Dateien finden Sie unter [Verwenden von Blob-Speicher mit HDInsight][hdinsight-storage-powershell].  


##<a name="process"></a> Verarbeiten der Twitter-Daten mit Hive

Damit haben Sie sämtliche Vorbereitungen abgeschlossen. Jetzt können Sie das Hive-Skript aufrufen und die Ergebnisse prüfen.

### Übermitteln eines Hive-Jobs

Verwenden Sie das folgende PowerShell-Skript, um das Hive-Skript auszuführen. Sie müssen die erste Variable festlegen.

>[WACOM.NOTE] Um die Tweets und das HiveQL-Skript, die Sie in den letzten beiden Abschnitten hochgeladen haben, zu verwenden, legen Sie "$hqlScriptFile" auf "/tutorials/twitter/twitter.hql" fest; und um die Daten zu verwenden, die für Sie in einen öffentlichen Blob hochgeladen wurden, legen Sie "$hqlScriptFile" auf "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql" fest.

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	#use one of the following
	$hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"

	$statusFolder = "/tutorials/twitter/jobstatus"
	#endregion
	
	#region - Invoke Hive
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace '\s*$' -replace '.*\s'
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 
	#endregion

### Prüfen der Ergebnisse

Verwenden Sie das folgende PowerShell-Skript, um die Ausgabe des Hive-Jobs zu prüfen. Sie müssen die ersten beiden Variablen festlegen.

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	#engregion
	
	#region - Create an azure storage context object
	Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
	$myCluster = Get-AzureHDInsightCluster -Name $clusterName
	$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
	$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
	Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
	Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	#endregion 
	
	#region - Download blob and display blob
	Write-Host "Download the blob ..." -ForegroundColor Green
	cd $HOME
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	Write-Host "==================================" -ForegroundColor Green
	cat "./$blob"
	Write-Host "==================================" -ForegroundColor Green
	#end region

> [AZURE.NOTE] In der Hive-Tabelle wird \001 als Feldtrennzeichen verwendet. Das Trennzeichen ist in der Ausgabe nicht sichtbar. 

Nachdem die Analyseergebnisse auf WASB abgelegt wurden, können Sie die Daten in eine Azure-SQL-Datenbank/auf einen SQL-Server exportieren, Sie können die Daten mit Power Query nach Excel exportieren oder Ihre Anwendung mit dem Hive-ODBC-Treiber mit den Daten verbinden.  Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop] ,[Analysieren von Daten zu Flugverspätungen mithilfe von HDInsight][hdinsight-analyze-flight-delay-data], [Verbinden von Excel mit HDInsight über Power Query][hdinsight-power-query] sowie [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][hdinsight-hive-odbc].

##<a id="nextsteps"></a>Nächste Schritte

Sie sind nun in der Lage, unstrukturierte JSon-Datensätze in strukturierte Hive-Tabellen umzuwandeln und Daten aus Twitter mit Azure HDInsight abzufragen, anzuzeigen und zu analysieren. Weitere Informationen finden Sie unter:

- [Erste Schritte mit HDInsight][hdinsight-get-started]
- [Analysieren der Twitter-Stimmung in Echtzeit mit HBase in HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analysieren von Daten zu Flugverspätungen mithilfe von HDInsight][hdinsight-analyze-flight-delay-data]
- [Verbinden von Excel mit HDInsight über Power Query][hdinsight-power-query]
- [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][hdinsight-hive-odbc]
- [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
<!--HONumber=42-->
