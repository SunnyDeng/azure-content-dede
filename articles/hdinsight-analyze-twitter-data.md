<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Analyze Twitter data with Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive to analyze Twitter data on Hadoop in HDInsight to find the usage frequency of a particular word." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Analysieren von Twitter-Daten mit Hadoop in HDInsight

Sie erfahren, wie Twitter-Daten mit Hadoop unter Verwendung von Hive in HDInsight analysiert werden.

Soziale Netzwerke sind einer der Haupt-Antriebsfaktoren für die Einführung von Big Data. Öffentliche APIs von Websites wie Twitter sind eine nützliche Datenquelle für Analyse und Verständnis beliebter Trends. In diesem Lernprogramm stellen Sie eine Verbindung mit dem Webdienst Twitter her, um einige Tweets mit der Twitter-Streaming-API abzurufen. Danach verwenden Sie Hive, um eine Liste von Twitter-Benutzern zu erstellen, die die meisten Tweets mit einem bestimmten Wort gesendet haben.

**Geschätzter Zeitaufwand:** 30 Minuten

## Themen in diesem Artikel

-   [Voraussetzungen][]
-   [Abrufen des Twitter-Feeds][]
-   [Erstellen eines HiveQL-Skripts][]
-   [Verarbeiten der Daten mit Hive][]
-   [Lernprogramm-Bereinigung][]
-   [Nächste Schritte][]

## <span id="prerequisites"></span></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][]. Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Ausführen von Windows PowerShell-Skripts][].

    Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

        Add-AzureAccount

    Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

        Select-AzureSubscription <AzureSubscriptionName>

-   **Einen Azure HDInsight-Cluster**. Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HDInsight][] oder unter [Bereitstellen von HDInsight-Clustern][]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Clustereigenschaft</th>
    <th align="left">PowerShell-Variablenname</th>
    <th align="left">Wert</th>
    <th align="left">Beschreibung</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight-Clustername</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Dies ist der Name Ihres HDInsight-Clusters.</td>
    </tr>
    <tr class="even">
    <td align="left">Azure-Speicherkontoname</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">Ein im HDInsight-Cluster verfügbares Azure-Speicherkonto. Verwenden Sie für dieses Lernprogramm das Standard-Speicherkonto, das Sie während der Bereitstellung des Clusters angegeben haben.</td>
    </tr>
    <tr class="odd">
    <td align="left">Azure-Blob-Containername</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">Verwenden Sie in diesem Beispiel den Azure-Blob-Speichercontainer, der für das Standarddateisystem des HDInsight-Clusters verwendet wird. Standardmäßig hat dieser denselben Namen wie der HDInsight-Cluster.</td>
    </tr>
    </tbody>
    </table>

**Grundlagen der HDInsight-Speicherung**

HDInsight verwendet Azure Blob-Speicher zur Datenspeicherung. Der Speicher wird *WASB* oder *Azure Storage-Blob* genannt. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][].

Wenn Sie einen HDInsight-Cluster bereitstellen, wird ebenso wie in HDFS ein Blob-Speichercontainer als Standarddateisystem festgelegt. Zusätzlich zu diesem Container können Sie während des Bereitstellungsprozesses weitere Container aus demselben Azure-Speicherkonto oder anderen Azure-Speicherkonten hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][].

Um das in diesem Lernprogramm verwendete PowerShell-Skript zu vereinfachen, werden alle Dateien im Standarddateisystem-Container unter */tutorials/twitter* gespeichert. Dieser Container hat standardmäßig denselben Namen wie der HDInsight-Cluster.

Die WASB-Syntax lautet:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] In der HDInsight-Clusterversion 3.0 wird nur die *wasb://*-Syntax unterstützt. Die ältere *asv://*-Syntax wird in HDInsight-Clustern der Versionen 2.1 und 1.6 unterstützt, aber nicht in den Clusterversionen 3.0 und höher.

> Der WASB-Pfad ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][].

Auf Dateien, die im Standarddateisystem-Container gespeichert sind, kann in HDInsight über einen der folgenden URIs zugegriffen werden (als Beispiel wird hier tweets.txt verwendet):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
    wasb:///tutorials/twitter/tweets.txt
    /tutorials/twitter/tweets.txt

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

    tutorials/twitter/tweets.txt

In der folgenden Tabelle sind die in diesem Lernprogramm verwendeten Dateien aufgelistet:

<table border="1"><tr><th> Dateien</th><th>                             Beschreibung </th></tr>                                                                                            <tr><td>/tutorials/twitter/data/tweets.txt </td><td> Die Quelldaten für den Hive-Job. </td></tr> 
<tr><td> /tutorials/twitter/output </td><td> Der Ausgabeordner für den Hive-Job. Der Standardname der Ausgabedatei des Hive-Jobs lautet <strong>000000\_0</strong>.  </td></tr>
<tr><td> tutorials/twitter/twitter.hql   </td><td>Die HiveQL-Skriptdatei.  </td></tr>
<tr><td> /tutorials/twitter/jobstatus  </td><td> Der Status des Hadoop-Jobs.</td></tr>
</table>

## <span id="feed"></span></a>Abrufen des Twitter-Feeds

In diesem Lernprogramm verwenden Sie die [Twitter-Streaming-APIs][]. Die spezielle Twitter-Streaming-API, die Sie verwenden, heißt [statuses/filter][].

Die [Tweets-Daten][] werden im JSon-Format in einer komplex verschachtelten Struktur gespeichert. Anstatt viele Codezeilen in einer herkömmlichen Programmiersprache zu schreiben, können Sie diese verschachtelte Struktur in eine Hive-Tabelle transformieren, die anschließend mit einer SQL-ähnlichen Sprache namens HiveQL abgefragt werden kann.

Twitter ermöglicht über OAuth den autorisierten Zugriff auf seine API. OAuth ist ein Authentifizierungsprotokoll, mit dem Benutzer einer Anwendung gestatten können, in ihrem Namen zu handeln, ohne ihr Kennwort weitergeben zu müssen. Weitere Informationen finden Sie unter [oauth.net][] oder im ausgezeichneten [Beginner's Guide to OAuth][] (OAuth-Einführungshandbuch, in englischer Sprache) von Hueniverse.

Um OAuth zu verwenden, müssen Sie zunächst auf der Twitter-Entwicklerwebsite eine neue Anwendung erstellen.

**So erstellen Sie eine Twitter-Anwendung**

1.  Melden Sie sich bei <https://apps.twitter.com/></a> an. Klicken Sie auf den Link **Registriere dich jetzt**, wenn Sie noch kein Twitter-Konto haben.
2.  Klicken Sie auf **Create New App**.
3.  Geben Sie **Name**, **Description** und **Website** ein. Für das Feld "Website" können Sie eine URL erfinden. Die folgende Tabelle zeigt einige mögliche Beispielwerte:

   	<table border="1">
	<tr><th> Feld </th><th>   Wert </th></tr>
    <tr><td> Name   </td><td> MyHDInsightApp </td></tr>
    <tr><td> Beschreibung </td><td> MyHDInsightApp</td></tr>
	<tr><td> Website </td>
	<td>http://www.myhdinsightapp.com </td></tr>
   	</table>
4.  Aktivieren Sie **Yes, I agree**, und klicken Sie dann auf **Create your Twitter application**.
5.  Klicken Sie auf die Registerkarte **Permissions**. Die Standardberechtigung ist **Read only**. Diese Berechtigung reicht für dieses Lernprogramm aus.
6.  Klicken Sie auf die Registerkarte **API Keys**.
7.  Klicken Sie auf **Create my access token**.
8.  Klicken Sie oben rechts auf der Seite auf **Test OAuth**.
9.  Notieren Sie **consumer key**, **Consumer secret**, **Access token** und **Access token secret**. Sie benötigen diese Werte später im Lernprogramm.

In diesem Lernprogramm erstellen Sie mit PowerShell einen Webdienstaufruf. Ein weiteres beliebtes Tool zum Erstellen von Webdienstaufrufen ist [*Curl*][]. Curl können Sie [hier][] herunterladen.

> [WACOM.NOTE] Wenn Sie den Curl-Befehl in Windows verwenden, geben Sie für die Optionswerte doppelte anstelle einfacher Anführungszeichen ein.

**So rufen Sie Tweets ab**

1.  Öffnen Sie Windows PowerShell ISE. (Geben Sie auf dem Startbildschirm von Windows 8 **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**.) Siehe [Start Windows PowerShell on Windows 8 and Windows][] (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).

2.  Kopieren Sie das folgende Skript in den Skriptbereich:

        Write-Host "Set variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $destBlobName = "tutorials/twitter/data/tweets.txt"

        $trackString = "Azure, Cloud, HDInsight"
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 100  #about 3 minutes every 100 lines

        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

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

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
                if ($count%10 -eq 0){
                    write-host " --- " -NoNewline
                    Get-Date -Format hh:mm:sstt
                }

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }

        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream) 

        $sReader.close()
        Write-Host "Complete!" -ForegroundColor Green

3.  Legen Sie die ersten neun Variablen in dem Skript fest:

    <table border="1">
	<tr><th> Variable </th><th> Beschreibung </th></tr>                                                                                                                                                                                                                         
    <tr><td> $storageAccountName</td><td> Das Speicherkonto, das für das Standarddateisystem des HDInsight-Clusters verwendet wird. Es handelt sich um das bei der Bereitstellung angegebene Speicherkonto. Weitere Informationen finden Sie unter <a href="#prerequisites">Voraussetzungen</a>.          </td></tr>
    <tr><td> $containerName </td><td>Der für das Standarddateisystem des HDInsight-Clusters verwendete Blob-Container. Es handelt sich um den bei der Bereitstellung angegebenen Container. Weitere Informationen finden Sie unter <a href="#prerequisites">Voraussetzungen</a>.</td></tr>
    <tr><td> $destBlobName</td><td> Dies ist der Name des Ausgabe-Blobs. Der Standardwert lautet <strong>tutorials/twitter/data/tweets.txt</strong>. Wenn Sie den Standardwert ändern, müssen Sie die PowerShell-Skripts entsprechend aktualisieren.                                    </td></tr>
    <tr><td> $trackString</td><td> Der Webdienst gibt Tweets zurück, die mit diesen Schlüsselwörtern verknüpft sind. Der Standardwert lautet <strong>Azure, Cloud, HDInsight</strong>. Wenn Sie den Standardwert ändern, müssen Sie die PowerShell-Skripts entsprechend aktualisieren. </td></tr>
    <tr><td> $lineMax</td><td> Der Wert bestimmt, wie viele Tweets das Skript liest. Das Lesen von 100 Tweets dauert etwa drei Minuten. Sie können einen größeren Wert festlegen, dann nimmt das Herunterladen jedoch mehr Zeit in Anspruch.                          </td></tr>
    <tr><td> $oauth\_consumer\_key </td><td>Dies ist der <strong>consumer key</strong>, den Sie beim Erstellen der Twitter-Anwendung notiert haben.                                                                                                                                             </td></tr>
    <tr><td> $oauth\_consumer\_secret</td><td> Dies ist der zuvor notierte <strong>consumer secret</strong>-Schlüssel der Twitter-Anwendung.                                                                                                                                                       </td></tr>
    <tr><td> $oauth\_token</td><td>Dies ist das zuvor notierte <strong>access token</strong> der Twitter-Anwendung.                                                                                                                                                                    </td></tr>
    <tr><td> $oauth\_token\_secret</td><td> Dies ist der zuvor notierte <strong>access token secret</strong>-Schlüssel der Twitter-Anwendung.                                                                                                                                                   </td></tr>
	</table>

4.  Drücken Sie **F5**, um das Skript auszuführen. Falls Probleme auftreten, markieren Sie als Behelfslösung alle Zeilen, und drücken Sie anschließend **F8**.
5.  Am Ende der Ausgabe wird "Complete!" angezeigt. Eventuelle Fehlermeldungen werden rot dargestellt.

Zur Validierung können Sie die Ausgabedatei **/tutorials/twitter/data/tweets.txt** im Azure Blob-Speicher mit einem Azure-Speicher-Explorer oder mit Azure PowerShell prüfen. Ein PowerShell-Beispielskript zum Auflisten von Dateien finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight][].

## <span id="script"></span></a>Erstellen eines HiveQL-Skripts

Mit Azure PowerShell können Sie mehrere HiveQL-Anweisungen gleichzeitig ausführen oder die HiveQL-Anweisung in eine Skriptdatei einfügen. In diesem Lernprogramm erstellen Sie ein HiveQL-Skript. Die Skriptdatei muss auf WASB hochgeladen werden. Im nächsten Abschnitt wird die Skriptdatei mit Azure PowerShell ausgeführt.

Das HiveQL-Skript führt folgende Schritte aus:

1.  **Ablegen der Tabelle tweets\_raw**, falls die Tabelle bereits vorhanden ist.
2.  **Erstellen der Hive-Tabelle tweets\_raw**. Diese temporäre strukturierte Hive-Tabelle dient zum Aufbewahren der Daten für die weitere ETL-Verarbeitung. Informationen zur Partitionierung finden Sie im [Hive Tutorial][] (Hive-Lernprogramm, in englischer Sprache).
3.  **Laden der Daten** aus dem Quellordner /tutorials/twitter/data. Der große Tweets-Datensatz im verschachtelten JSon-Format wurde nun in eine temporäre Hive-Tabellenstruktur transformiert.
4.  **Ablegen der Tweets-Tabelle**, falls die Tabelle bereits vorhanden ist.
5.  **Erstellen der Tweets-Tabelle**. Bevor Sie den Tweets-Datensatz mit Hive abfragen können, müssen Sie einen weiteren ETL-Prozess ausführen. In diesem ETL-Prozess wird ein detaillierteres Tabellenschema für die Daten definiert, die Sie in der Tabelle "twitter\_raw" gespeichert haben.
6.  **Einfügen der overwrite-Tabelle**. Dieses komplexe Hive-Skript löst eine Reihe langer MapReduce-Jobs im Hadoop-Cluster aus. Je nach Datensatz und Größe Ihres Clusters sollte dieser Vorgang ca. 10 Minuten dauern.
7.  **Einfügen des overwrite-Verzeichnisses**. Führen Sie eine Abfrage aus, und geben Sie den Datensatz in einer Datei aus. Diese Abfrage gibt eine Liste von Twitter-Benutzern zurück, deren Tweets das Wort "Azure" besonders häufig enthielten.

**So erstellen Sie ein Hive-Skript und laden es nach Azure hoch**

1.  Öffnen Sie Windows PowerShell ISE.
2.  Kopieren Sie das folgende Skript in den Skriptbereich:

        Write-Host "Define variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"

        $hqlScriptFile = "tutorials/twitter/twitter.hql"

        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;

        DROP TABLE tweets_raw;
        CREATE TABLE tweets_raw (
            json_response STRING
        ) 
        PARTITIONED BY (filesequence INT);

        LOAD DATA INPATH '$sourceDataPath'
        INTO TABLE tweets_raw
        PARTITION (filesequence = 1);

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
        )
        PARTITIONED BY (filesequence INT);

        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        PARTITION (filesequence)
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
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
            json_response,
            filesequence
        WHERE (length(json_response) > 500);

        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc 
            FROM tweets 
            WHERE text like "%Azure%" 
            GROUP BY name,screen_name 
            ORDER BY cc DESC LIMIT 10;
        "@

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

        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)

        Write-Host "Complete!" -ForegroundColor Green

3.  Legen Sie die ersten zwei Variablen in dem Skript fest:

    <table border="1">
	<tr><th>Variable </th><th> Beschreibung</th></tr>    
    <tr><td> $storageAccountName </td><td> Das Speicherkonto, das für das Standarddateisystem des HDInsight-Clusters verwendet wird. Es handelt sich um das bei der Bereitstellung angegebene Speicherkonto. Weitere Informationen finden Sie unter <a href="#prerequisites">Voraussetzungen</a>.</td></tr>
    <tr><td> $containerName </td><td>Der für das Standarddateisystem des HDInsight-Clusters verwendete Blob-Container. Es handelt sich um den bei der Bereitstellung angegebenen Container. Weitere Informationen finden Sie unter <a href="#prerequisites">Voraussetzungen</a>. </td></tr>
    <tr><td> $sourceDataPath  </td><td> Der WASB-Speicherort, aus dem die Hive-Abfragen die Daten lesen. Sie müssen diese Variable nicht ändern.                                                                                                                      </td></tr>
    <tr><td> $outputPath </td><td> Der WASB-Speicherort, in den die Hive-Abfragen die Daten ausgeben. Sie müssen diese Variable nicht ändern.                                                                                                                    </td></tr>
    <tr><td> $hqlScriptFile </td><td> Der Speicherort und der Dateinamen der HiveQL-Skriptdatei.                                                                                                                                                                    </td></tr>
	</table>

4.  Drücken Sie **F5**, um das Skript auszuführen. Falls Probleme auftreten, markieren Sie als Behelfslösung alle Zeilen, und drücken Sie anschließend **F8**.
5.  Am Ende der Ausgabe wird "Complete!" angezeigt. Eventuelle Fehlermeldungen werden rot dargestellt.

Zur Validierung können Sie die Ausgabedatei **/tutorials/twitter/twitter.hql** im Azure Blob-Speicher mit einem Azure-Speicher-Explorer oder mit Azure PowerShell prüfen. Ein PowerShell-Beispielskript zum Auflisten von Dateien finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight][].

## <a name="process"></a>Verarbeiten der Twitter-Daten mit Hive

Damit haben Sie sämtliche Vorbereitungen abgeschlossen. Jetzt können Sie das Hive-Skript aufrufen und die Ergebnisse prüfen.

### Übermitteln eines Hive-Jobs

Verwenden Sie das folgende PowerShell-Skript, um das Hive-Skript auszuführen. Sie müssen die erste Variable festlegen.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $clusterName = "<HDInsightClusterName>"

    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    $statusFolder = "/tutorials/twitter/jobstatus"

    Write-Host "Invoke Hive ... " -ForegroundColor Green
    Use-AzureHDInsightCluster $clusterName
    $response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable

    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### Prüfen der Ergebnisse

Verwenden Sie das folgende PowerShell-Skript, um die Ausgabe des Hive-Jobs zu prüfen. Sie müssen die ersten beiden Variablen festlegen.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "Display the output ..." -ForegroundColor Green
    cat "./$blob"

> [WACOM.NOTE] In der Hive-Tabelle wird \\001 als Feldtrennzeichen verwendet. Das Trennzeichen ist in der Ausgabe nicht sichtbar.

Nachdem die Analyseergebnisse auf WASB abgelegt wurden, können Sie die Daten in eine Azure-SQL-Datenbank/auf einen SQL-Server exportieren, Sie können die Daten mit Power Query nach Excel exportieren oder Ihre Anwendung mit dem Hive-ODBC-Treiber mit den Daten verbinden. Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][], [Analysieren von Daten zu Flugverspätungen mithilfe von HDInsight][], [Verbinden von Excel mit HDInsight über Power Query][] und [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][].

## <span id="cleanup"></span></a>Lernprogramm-Bereinigung

Falls Sie das Lernprogramm erneut ausführen möchten, müssen Sie folgende Schritte ausführen:

-   **Erstellen Sie die Tweets-Datendatei neu**. Die Tweets-Quelldatendatei wurde vom Hive-Job entfernt. Sie müssen eine neue Datei generieren. Der Dateiname lautet **tutorials/twitter/data/tweets.txt**. Informationen hierzu finden Sie unter [Abrufen des Twitter-Feeds][].

## <span id="nextsteps"></span></a>Nächste Schritte

Sie sind nun in der Lage, unstrukturierte JSon-Datensätze in strukturierte Hive-Tabellen umzuwandeln und Daten aus Twitter mit Azure HDInsight abzufragen, anzuzeigen und zu analysieren. Weitere Informationen finden Sie unter:

-   [Erste Schritte mit HDInsight][]
-   [Analysieren von Daten zu Flugverspätungen mit HDInsight][Analysieren von Daten zu Flugverspätungen mithilfe von HDInsight]
-   [Verbinden von Excel mit HDInsight über Power Query][]
-   [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][]
-   [Verwenden von Sqoop mit HDInsight][]

  [Voraussetzungen]: #prerequisites
  [Abrufen des Twitter-Feeds]: #feed
  [Erstellen eines HiveQL-Skripts]: #script
  [Verarbeiten der Daten mit Hive]: #process
  [Lernprogramm-Bereinigung]: #cleanup
  [Nächste Schritte]: #nextsteps
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell
  [Ausführen von Windows PowerShell-Skripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Bereitstellen von HDInsight-Clustern]: ../hdinsight-provision-clusters/
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Twitter-Streaming-APIs]: https://dev.twitter.com/docs/streaming-apis
  [statuses/filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
  [Tweets-Daten]: https://dev.twitter.com/docs/platform-objects/tweets
  [oauth.net]: http://oauth.net/
  [Beginner's Guide to OAuth]: http://hueniverse.com/oauth/
  
  [*Curl*]: http://curl.haxx.se
  [hier]: http://curl.haxx.se/download.html
  [Start Windows PowerShell on Windows 8 and Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [Verwenden von Azure-Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/#powershell
  [Hive Tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial
  [Verwenden von Sqoop mit HDInsight]: ../hdinsight-use-sqoop/
  [Analysieren von Daten zu Flugverspätungen mithilfe von HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Verbinden von Excel mit HDInsight über Power Query]: ../hdinsight-connect-excel-power-query/
  [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight]: ../hdinsight-connect-excel-hive-ODBC-driver/
