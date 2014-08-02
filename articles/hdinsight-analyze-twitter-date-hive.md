<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Data with HDInsight" pageTitle="Analyzing Twitter data with Hive - Windows Azure Services" metaKeywords="" description="In this tutorial you will query, explore, and analyze data from Twitter using the HDInsight Service for Windows Azure and a complex Hive example." metaCanonical="" services="" documentationCenter="" title="Analyzing Twitter Data with Hive" authors="" solutions="" writer="wenming" manager="" editor="" />

Analysieren von Twitter-Daten mit Hive
======================================

In diesem Lernprogramm werden Sie Daten aus Twitter mithilfe des Apache Hadoop-basierten HDInsight-Diensts für Azure und anhand eines komplexen Hive-Beispiels abfragen, anzeigen und analysieren. Soziale Netzwerke sind einer der Haupt-Antriebsfaktoren für die Einführung von Big Data. Öffentliche APIs von Websites wie Twitter sind eine nützliche Datenquelle für Analyse und Verständnis beliebter Trends. Dieses Lernprogramm geht davon aus, dass Sie bereits ein HDInsight-Cluster im [Azure-Portal erstellt haben](http://www.windowsazure.com).

Dieses Lernprogramm umfasst die folgenden Abschnitte:

1.  [Twitter-Feed mit cURL und der Twitter Streaming API abrufen](#segment1)
2.  [Twitter-Daten mit Hive interaktiv verarbeiten](#segment2)

## Twitter-Feed mit cURL und der Twitter Streaming API abrufen

1.  Für diesen Schritt benötigen Sie curl.exe. Sie können die entsprechende curl-Datei für Ihr BS (z. B. die Win64 Binary SSL) unter <http://curl.haxx.se/download.html> herunterladen.

    ![Figure3](./media/hdinsight-analyze-twitter-date-hive/Figure3.png)

2.  Entzippen Sie **curl.exe** am gewünschten Ort (z. B. **C:\\twitterdata**)

3.  Erstellen Sie in Notepad die beiden Dateien **get\_twitter\_stream.cmd** und **twitter\_params.txt** im gleichen Ordner wie **curl.exe** mit dem folgenden Inhalt:

    ![Figure4](./media/hdinsight-analyze-twitter-date-hive/Figure4.png)

4.  Bearbeiten Sie die Datei **twitter\_params.txt** wie folgt, um Ihren Tweets zu folgen:

         track=weather,Azure,WindowsAzure,cloud

    Sie können beliebige Themen oder Hashtags verwenden.

5.  Bearbeiten Sie das Windows-Befehlsskript **get\_twitter\_stream.cmd** und fügen Sie Ihren Twitter-Benutzernamen anstelle von **USER** und Ihr Kennwort anstelle von **PASSWORD** in der folgenden Zeile ein:

         curl -d @twitter_parameters.txt -k https://stream.twitter.com/1/statuses/filter.json -u user:{password} >>twitter_stream_seq2.txt

6.  Führen Sie das get\_twitter\_stream.cmd-Skript wie folgt in einer Eingabeaufforderung aus:

    ![Figure5](./media/hdinsight-analyze-twitter-date-hive/Figure5.png)

    Die Ausgabe sollte folgendermaßen aussehen:

    ![Figure6](./media/hdinsight-analyze-twitter-date-hive/Figure6.png)

7.  Sie können jederzeit **Ctrl+C** drücken, um die Verarbeitung zu beenden. Anschließend können Sie die Datei umbenennen und das Skript erneut starten. Sie können den Prozess über einen beliebigen Zeitraum ausführen, von 10 Minuten bis hin zu Stunden. Für dieses Lernprogramm sollten Sie die Datenmenge auf wenige Hundert Megabyte beschränken.

    Die Twitterdaten werden im JSON-Format in einer komplexen verschachtelten Struktur gespeichert. Im nächsten Schritt werden Sie diese verschachtelte Struktur in eine Hive-Tabelle transformieren, die anschließend interaktiv mit einer SQL-ähnlichen Sprache namens HQL abgefragt werden kann, anstatt viele Zeilen Code in herkömmlichen Programmiersprachen zu schreiben.

## Twitter-Daten mit Hive interaktiv verarbeiten

1.  Während die Tweets gesammelt werden, öffnen Sie eine RDP-Sitzung zum HDInsight-Collections, das Sie im Azure-Portal erstellt haben.

    **Navigieren** Sie zu HDInsight und **wählen** Sie Ihr erstelltes Cluster aus. Klicken Sie auf das **Connect RDP**-Symbol im unteren Bildschirmbereich. Geben Sie Ihr Kennwort ein, um sich an der RDP-Sitzung anzumelden. **Öffnen** Sie nach der Anmeldung ein Explorerfenster und navigieren Sie zu C:.

    ![Verbinden über das RDP-Symbol](./media/hdinsight-analyze-twitter-date-hive/twitter-RDPconnect.PNG)

2.  Drücken Sie **Strg+C**, um den Curl-Sammelprozess zu beenden und navigieren Sie im Windows-Explorer zum Speicherort der Twitter-Datei. **Klicken Sie mit der rechten Maustaste** um einen komprimierten (gezippten) Ordner zu verschicken. Auf diese Weise sparen Sie Uploadzeit.

3.  Kopieren Sie die Zip-Datei auf Ihrem lokalen Computer mit **Strg-C** und navigieren Sie zu c:\\ im C:-Laufwerk der Remotedesktop-Sitzung. **Fügen Sie die Datei mit Strg-V** ein, nachdem Sie auf das C:-Explorerfenster geklickt haben, um die Zip-Datei über die RDP-Sitzung hochzuladen.

    ![Hochladen von Tweets über die RDP-Sitzung](./media/hdinsight-analyze-twitter-date-hive/twitter-uploadingzip.PNG)

4.  Klicken Sie nach dem Upload **mit der rechten Maustaste** auf die Datei und **wählen Sie** Alle extrahieren nach c:\\ aus, um die Ausgangs-Textdatei wiederherzustellen. **Öffnen** Sie ein Hadoop-Befehlszeilenfenster, um mit HIVE- und Hadoop-Befehlen arbeiten zu können.

    ![Figure26](./media/hdinsight-analyze-twitter-date-hive/Figure26.png)

5.  **Geben Sie zunächst** C:\\ ein und **drücken Sie die Eingabetaste**. Daraufhin wechseln Sie zum C:\\-Ordner, in dem die Twitterdaten liegen.

6.  Erstellen Sie anschließend einen Ordner im HDFS (Hadoop Distributed File System) und kopieren Sie die Twitterdaten mit dem -copyFromLocal-Parameter in den soeben erstellten Ordner:

         hadoop fs -mkdir /example/data
         hadoop fs -copyFromLocal c:\twitter_stream_seq2.txt /example/data/

7.  Sie haben die Twitter-Rohdaten nun in das HDFS auf Ihrem HDInsight-Cluster kopiert. Anschließend werden Sie eine einfache Tabellenstruktur für die geladenen Daten erstellen. Diese temporäre strukturierte Hive-Tabelle dient zum Aufbewahren der Daten für die weitere ETL-Verarbeitung. Öffnen Sie Notepad, geben Sie den folgenden Code ein und speichern Sie die Datei als: "c:\\load\_twitter\_raw.hql"

         drop table twitter_raw;

         create table twitter_raw (
             json_response string
         ) 
         partitioned by (filesequence int);

         load data inpath '/example/data/twitter_stream_seq2.txt'
         into table twitter_raw
         partition (filesequence = 1);

8.  Führen Sie anschließend Hive im Befehlsfenster aus:

         C:\apps\dist\hive-0.9.0\bin\hive -f load_twitter_raw.hql

9.  Dieser Prozess kann einige Sekunden in Anspruch nehmen:

    ![Rohergebnisse aus Twitter laden](./media/hdinsight-analyze-twitter-date-hive/twitter-load-raw-results.PNG)

10. Der große Twitter-Datensatz im verschachtelten JSON-Format wurde nun in eine temporäre Hive-Tabellenstruktur transformiert.

11. Bevor wir den Twitter-Datensatz mit Hive abfragen können, müssen wir einen weiteren ETL-Prozess ausführen. Wir werden ein detaillierteres Tabellenschema für die Daten definieren, die wir in der "twitter\_raw"-Tabelle gespeichert haben. Dieser komplexere ETL-Prozess dauert etwas länger. Öffnen Sie Notepad erneut, fügen Sie den folgenden Hive-Abfragecode ein und speichern Sie die Datei als "c:\\twitter\_etl.hql.txt".

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode=nonstrict;

        drop table twitter_temp;

        create table twitter_temp
        (
            id bigint,
            created_at string,
            created_at_date string,
            created_at_year string,
            created_at_month string,
            created_at_day string,
            created_at_time string,
            in_reply_to_user_id_str string,
            text string,
            contributors string,
            retweeted string,
            truncated string,
            coordinates string,
            source string,
            retweet_count int,
            url string,
            hashtags array<string>,
            user_mentions array<string>,
            first_hashtag string,
            first_user_mention string,
            screen_name string,
            name string,
            followers_count int,
            listed_count int,
            friends_count int,
            lang string,
            user_location string,
            time_zone string,
            profile_image_url string,
            json_response string
        )
        partitioned by (filesequence int);

        from twitter_raw
        insert overwrite table twitter_temp
        partition (filesequence)
        select
            cast(get_json_object(json_response, '$.id_str') as bigint),

            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
                substr (get_json_object(json_response, '$.created_at'),27,4)),

            substr (get_json_object(json_response, '$.created_at'),27,4),

            case substr (get_json_object(json_response,     '$.created_at'),5,3)
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
            cast (get_json_object(json_response, '$.retweet_count') as int),
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
            cast (get_json_object(json_response, '$.user.followers_count') as int),
            cast (get_json_object(json_response, '$.user.listed_count') as int),
            cast (get_json_object(json_response, '$.user.friends_count') as int),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response,
            filesequence
        where (length(json_response) > 500);

12. Geben Sie nun Folgendes ein:

        C:\apps\dist\hive-0.9.0\bin\hive -f twitter_etl.hql.txt

13. Dieses komplexe Hive-Skript löst eine Reihe langer MapReduce-Jobs im Hadoop-Cluster aus. Je nach Datensatz und Größe Ihres Clusters sollte dieser Vorgang ca. 10 Minuten dauern.

    ![twitter complex etl](./media/hdinsight-analyze-twitter-date-hive/twitter-etl-complex.PNG)

14. Sie können den Fortschritt Ihres Jobs auf der Job-Verfolgungsseite im Hauptknoten verfolgen, indem Sie auf die Verknüpfung "Hadoop MapReduce Status" auf Ihrem Desktop doppelklicken.

    ![Überwachung und Verfolgung von Jobs](./media/hdinsight-analyze-twitter-date-hive/twitter_longjob_browser.PNG)

15. Starten Sie Hive nach Abschluss des ETL-Prozesses:

        C:\apps\dist\hive-0.9.0\bin\hive

16. Probieren Sie verschiedene Hive-Abfragen aus, wie z. B.:

		select name, screen\_name, count(1) as cc from twitter\_temp where text like "%Azure%" group by name,screen\_name order by cc desc limit 10;

17. Diese Abfrage gibt eine Liste von Twitter-Benutzern zurück, deren Tweets das Wort "Azure" besonders häufig enthielten.

    ![Endergebnis](./media/hdinsight-analyze-twitter-date-hive/twitter_final_query_result.PNG)

## Zusammenfassung

Sie sind nun in der Lage, unstrukturierte JSON-Datensätze in strukturierte Hive-Tabellen umzuwandeln und Daten aus Twitter mit Azure HDInsight abzufragen, anzuzeigen und zu analysieren. Updates und Support-Dateien finden Sie [hier](https://github.com/wenming/BigDataSamples/tree/master/twittersample) im GitHub-Repository.

Nächste Schritte
----------------

In diesem Artikel wird die Verwendung der Hadoop-Befehlszeile beschrieben, Sie können die Aufgaben jedoch auch mit der interaktiven HDInsight-Konsole ausführen. Weitere Informationen finden Sie unter [Guidance: HDInsight Interactive JavaScript and Hive Consoles][interactive-console].

