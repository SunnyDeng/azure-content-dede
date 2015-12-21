<properties
	pageTitle="Analysieren von Twitter-Daten mit Apache Hive in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Python Tweets speichern, die bestimmte Schlüsselwörter enthalten, und dann mit Hive und Hadoop in HDInsight die Twitter-Rohdaten in eine durchsuchbare Hive-Tabelle umwandeln."
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
	ms.date="12/04/2015"
	ms.author="larryfr"/>

# Analysieren von Twitter-Daten mit Hive in HDInsight

In diesem Dokument erhalten Sie Tweets mithilfe einer Twitter-Streaming-API und nutzen dann Apache Hive auf einem Linux-basierten Cluster von HDInsight zum Verarbeiten der Daten im JSON-Format. Als Ergebnis erhalten Sie eine Liste der Twitter-Benutzer, die die meisten Tweets mit einem bestimmten Begriff gesendet haben.

> [AZURE.NOTE]Einzelne Teile dieses Dokuments können zwar für Windows-basierte HDInsight-Cluster (beispielsweise Python und Hive) verwendet werden. Zahlreiche Schritte basieren jedoch auf der Verwendung eines Linux-basierten HDInsight-Clusters. Die Schritte für einen Windows-basierten Cluster finden Sie unter [Analysieren von Twitter-Daten mit Hive in HDInsight](hdinsight-analyze-twitter-data.md).

###Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- Einen __Linux-basierten Azure HDInsight-Cluster__. Informationen zum Erstellen eines Clusters finden Sie unter [Erste Schritte mit Linux-basiertem HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

- Einen __SSH-Client__. Weitere Informationen zum Verwenden von SSH mit Linux-basierten HDInsight-Clustern finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ und [pip](https://pypi.python.org/pypi/pip)

- Die __Azure-Befehlszeilenschnittstelle__. Weitere Informationen finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).

##Abrufen eines Twitter-Feeds

Twitter ermöglicht das Abrufen der [Daten für jeden Tweet](https://dev.twitter.com/docs/platform-objects/tweets) als JavaScript Object Notation (JSON)-Dokument über eine REST-API. [OAuth](http://oauth.net) ist für die Authentifizierung gegenüber der API erforderlich. Außerdem müssen Sie eine _Twitter-Anwendung_ mit den Einstellungen erstellen, die zum Zugreifen auf die API verwendet werden.

###Erstellen einer Twitter-Anwendung

1. Melden Sie sich in einem Webbrowser bei [https://apps.twitter.com/](https://apps.twitter.com/) an. Klicken Sie auf den Link **Registriere Dich jetzt**, wenn Sie noch kein Twitter-Konto haben.
2. Klicken Sie auf **Create New App**.
3. Geben Sie **Name**, **Description** und **Website** ein. Für das Feld **Website** können Sie eine URL erfinden. Die folgende Tabelle zeigt einige mögliche Beispielwerte:

	| Feld | Wert |
	|:----- |:----- |
	| Name | MyHDInsightApp |
	| Description | MyHDInsightApp |
	| Website | http://www.myhdinsightapp.com |
	
4. Aktivieren Sie **Yes, I agree**, und klicken Sie dann auf **Create your Twitter application**.
5. Klicken Sie auf die Registerkarte **Permissions**. Die Standardberechtigung ist **Read only**. Diese Berechtigung reicht für dieses Lernprogramm aus.
6. Klicken Sie auf die Registerkarte **Keys and Access Tokens**.
7. Klicken Sie auf **Create my access token**.
8. Klicken Sie oben rechts auf der Seite auf **Test OAuth**.
9. Notieren Sie **consumer key**, **Consumer secret**, **Access token** und **Access token secret**. Sie benötigen diese Werte später.

>[AZURE.NOTE]Wenn Sie den Curl-Befehl in Windows verwenden, geben Sie die Optionswerte in doppelten anstelle von einfachen Anführungszeichen ein.

###Herunterladen von Tweets

Mit dem folgenden Python-Code werden 10.000 Tweets von Twitter heruntergeladen und in einer Datei namens__tweets.txt__ gespeichert.

> [AZURE.NOTE]Die folgenden Schritte werden auf dem HDInsight-Cluster ausgeführt, da Python bereits installiert ist.

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Wenn Sie zum Schutz Ihres SSH-Benutzerkontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden, um den passenden privaten Schlüssel anzugeben. Beispiel: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Weitere Informationen zum Verwenden von SSH mit Linux-basierten HDInsight-Clustern finden Sie in den folgenden Artikeln:
	
	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows)
	
2. Das Hilfsprogramm __pip__ ist nicht standardmäßig auf dem HDInsight-Hauptknoten installiert. Installieren und aktualisieren Sie das Hilfsprogramm wie folgt:

		sudo apt-get install python-pip
		sudo pip install --upgrade pip

3. Der Code zum Herunterladen von Tweets basiert auf [Tweepy](http://www.tweepy.org/) und [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Verwenden Sie zum Installieren den folgenden Befehl:

		sudo apt-get install python-dev libffi-dev libssl-dev
		sudo apt-get remove python-openssl
		sudo pip install tweepy==3.2.0 progressbar pyOpenSSL requests[security]
		
	> [AZURE.NOTE]Achten Sie darauf, dass beim Entfernen von „python-openssl“ und Installieren von „python-dev“, „libffi-dev“, „libssl-dev“, „pyOpenSSL“ und „requests[security]“ muss eine InsecurePlatform-Warnung vermieden wird, wenn von Python über SSL eine Verbindung mit Twitter hergestellt wird.
	>
	> Mit Tweepy v3.2.0 wird [ein Fehler](https://github.com/tweepy/tweepy/issues/576) vermieden, der beim Verarbeiten von Tweets auftreten kann.

4. Verwenden Sie den folgenden Befehl, um eine neue Datei namens __gettweets.py__ zu erstellen:

		nano gettweets.py

5. Fügen Sie folgenden Inhalt in die Datei __gettweets.py__ ein: Ersetzen Sie die Platzhalterinformationen für __consumer/\_secret__, __consumer/\_key__, __access/\_token__ und __access/\_token/\_secret__ durch die Informationen aus Ihrer Twitter-Anwendung.

		#!/usr/bin/python
		
		from tweepy import Stream, OAuthHandler
		from tweepy.streaming import StreamListener
		from progressbar import ProgressBar, Percentage, Bar
		import json
		import sys
		
		#Twitter app information
		consumer_secret='Your consumer secret'
		consumer_key='Your consumer key'
		access_token='Your access token'
		access_token_secret='Your access token secret'
		
		#The number of tweets we want to get
		max_tweets=10000
		
		#Create the listener class that will receive and save tweets
		class listener(StreamListener):
		    #On init, set the counter to zero and create a progress bar
		    def __init__(self, api=None):
		        self.num_tweets = 0
		        self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()
		
		    #When data is received, do this
		    def on_data(self, data):
		        #Append the tweet to the 'tweets.txt' file
		        with open('tweets.txt', 'a') as tweet_file:
		            tweet_file.write(data)
		            #Increment the number of tweets
		            self.num_tweets += 1
		            #Check to see if we have hit max_tweets and exit if so
		            if self.num_tweets >= max_tweets:
		                self.pbar.finish()
		                sys.exit(0)
		            else:
		                #increment the progress bar
		                self.pbar.update(self.num_tweets)
		        return True
		
		    #Handle any errors that may occur
		    def on_error(self, status):
		        print status
		
		#Get the OAuth token
		auth = OAuthHandler(consumer_key, consumer_secret)
		auth.set_access_token(access_token, access_token_secret)
		#Use the listener class for stream processing
		twitterStream = Stream(auth, listener())
		#Filter for these topics
		twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Drücken Sie zum Speichern der Datei __STRG+X__ und anschließend __Y__

7. Verwenden Sie den folgenden Befehl, um die Datei auszuführen und Tweets herunterzuladen:

		python gettweets.py

	In der Regel wird eine Statusanzeige angezeigt, wenn die Tweets heruntergeladen und in einer Datei gespeichert werden.

###Hochladen der Daten

Verwenden Sie zum Hochladen der Daten in WASB (das von HDInsight verwendete Dateisystem) die folgenden Befehle:

	hadoop fs -mkdir -p /tutorials/twitter/data
	hadoop fs -copyFromLocal tweets.txt /tutorials/twitter/data/tweets.txt

Dabei werden die Daten an einem Speicherort gespeichert, auf den alle Knoten im Cluster zugreifen können.

##Ausführen des HiveQL-Auftrags


1. Verwenden Sie den folgenden Befehl, um eine Datei mit HiveQL-Anweisungen zu erstellen:

		nano twitter.hql
	
	Fügen Sie Folgendes als Inhalt der Datei hinzu:

		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
		-- Drop table, if it exists
		DROP TABLE tweets_raw;
		-- Create it, pointing toward the tweets logged from Twitter
		CREATE EXTERNAL TABLE tweets_raw (
			json_response STRING
		)
		STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
		-- Drop and recreate the destination table
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
		-- Select tweets from the imported data, parse the JSON,
		-- and insert into the tweets table
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
		
		
3. Drücken Sie zum Speichern der Datei __STRG+X__ und anschließend __Y__

4. Verwenden Sie den folgenden Befehl, um den in der Datei enthaltenen HiveQL-Auftrag auszuführen:

		hive -i twitter.hql		
		
	Dadurch wird die Hive-Shell geladen, der HiveQL-Auftrag in der Datei __twitter.hql__ ausgeführt und schließlich eine `hive >`-Eingabeaufforderung zurückgegeben.
	
5. Überprüfen Sie an der `hive >`-Eingabeaufforderung mit folgendem Befehl, ob Daten aus der Tabelle __tweets__ ausgewählt werden können, die vom HiveQL-Auftrag in der Datei __twitter.hql__ erstellt wurde:
		
		SELECT name, screen_name, count(1) as cc
			FROM tweets
			WHERE text like "%Azure%"
			GROUP BY name,screen_name
			ORDER BY cc DESC LIMIT 10;

	Dadurch werden maximal 10 Tweets zurückgegeben, die den Begriff __Azure__ im Meldungstext enthalten.

##Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Sie ein unstrukturiertes JSON-Dataset in eine strukturierte Hive-Tabelle umwandeln und Daten aus Twitter mithilfe von Azure HDInsight abfragen, anzeigen und analysieren können. Weitere Informationen finden Sie unter:

- [Erste Schritte mit HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analysieren von Daten zu Flugverspätungen mit HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

<!---HONumber=AcomDC_1210_2015-->