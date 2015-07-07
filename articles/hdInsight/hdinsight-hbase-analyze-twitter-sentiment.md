<properties 
	pageTitle="Analysieren der Twitter-Stimmung in Echtzeit mit HBase | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Echtzeit-Stimmungsanalysen von Big Data aus Twitter mit HBase in einem HDInsight (Hadoop)-Cluster durchführen können." 
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
	ms.date="01/27/2015" 
	ms.author="jgao"/>

# Analysieren Sie Twitter-Stimmungen mit HBase in HDInsight

Erfahren Sie, wie Sie Echtzeit-[Stimmungsanalysen](http://en.wikipedia.org/wiki/Sentiment_analysis) von Big Data aus Twitter mit HBase in einem HDInsight (Hadoop)-Cluster durchführen können.


Soziale Netzwerke sind einer der Hauptantriebsfaktoren für die Einführung von Big Data. Öffentliche APIs von Websites wie Twitter sind eine nützliche Datenquelle für Analyse und Verständnis beliebter Trends. In diesem Lernprogramm entwickeln Sie eine Konsolenanwendung für Streamingdienste und eine ASP.NET-Webanwendung für folgende Zwecke:

![][img-app-arch]

- Die Streaminganwendung
	- Rufen Sie Tweets mit Geo-Tags in Echtzeit über die Twitter-Streaming-API ab.
	- Analysieren Sie die Stimmung dieser Tweets.
	- Speichern Sie die Stimmungsinformationen in HBase mithilfe des Microsoft HBase SDK.
- Die Azure-Websites-Anwendung
	- Stellen Sie die Echtzeit-Statistikergebnisse mithilfe einer ASP.NET-Webanwendung auf Bing Maps grafisch dar. Eine Visualisierung der Tweets sieht etwa so aus:

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	Sie können Tweets mit bestimmten Schlüsselwörtern abfragen, um abzuschätzen, ob die geäußerten Meinungen in Tweets positiv, negativ oder neutral sind.

Ein vollständiges Visual Studio-Projektmappenbeispiel finden Sie auf GitHub: [Realtime social sentiment analysis app](https://github.com/maxluk/tweet-sentiment).































##<a id="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein HBase-Cluster in HDInsight**. Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HBase mit Hadoop in HDInsight][hbase-get-started]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:


<table border="1">
<tr><th>Clustereigenschaft</th><th>Beschreibung</th></tr>
<tr><td>HBase-Clustername</td><td>Der Name Ihres HDInsight HBase-Clusters. Beispiel: https://myhbase.azurehdinsight.net/</td></tr>
<tr><td>Clusterbenutzer-Name</td><td>Der Name des Hadoop-Benutzerkontos. Der Standardbenutzername für Hadoop lautet <strong>admin</strong>.</td></tr>
<tr><td>Clusterbenutzer-Kennwort</td><td>Das Benutzerkennwort für den Hadoop-Cluster.</td></tr>
</table>

- **Eine Workstation** mit installiertem Visual Studio 2013. [Installieren von Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx)





##<a id="twitter"></a>Erstellen einer Twitter-Anwendungs-ID und von Geheimschlüsseln

Die Streaming-APIs von Twitter verwenden [OAuth](http://oauth.net/), um Anforderungen zu autorisieren. Um OAuth zu verwenden, müssen Sie zunächst auf der Twitter-Entwicklerwebsite eine neue Anwendung erstellen.

**So erstellen Sie eine Twitter-Anwendungs-ID und Geheimschlüssel**

1. Melden Sie sich bei [Twitter Apps](https://apps.twitter.com/) an. Klicken Sie auf den Link **Registriere Dich jetzt**, wenn Sie noch kein Twitter-Konto haben.
2. Klicken Sie auf **Create New App**.
3. Geben Sie Werte für **Name**, **Description** und **Website** ein. Das Feld "Website" ist nicht zwingend erforderlich. Es muss keine gültige URL enthalten. Die folgende Tabelle zeigt einige mögliche Beispielwerte:

	<table border="1">
<tr><th>Feld</th><th>Wert</th></tr>
<tr><td>Name</td><td>MyHDInsightHBaseApp</td></tr>
<tr><td>Beschreibung</td><td>MyHDInsightHBaseApp</td></tr>
<tr><td>Website</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
</table>
> [AZURE.NOTE]Der Name der Twitter-Anwendung muss ein eindeutiger Name sein.

4. Aktivieren Sie **Yes, I agree**, und klicken Sie dann auf **Create your Twitter application**.
5. Klicken Sie auf die Registerkarte **Permissions**. Die Standardberechtigung ist **Read only**. Diese Berechtigung reicht für dieses Lernprogramm aus. 
6. Klicken Sie auf die Registerkarte **Keys and Access Tokens**.
7. Klicken Sie auf **Create my access token**.
8. Klicken Sie oben rechts auf der Seite auf **Test OAuth**.
9. Notieren Sie sich die Werte für **Consumer key**, **Consumer secret**, **Access token** und **Access token secret**. Sie benötigen diese Werte später im Lernprogramm.

	![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























##<a id="streaming"></a> Erstellen eines einfachen Twitter-Streamingdiensts

Erstellen Sie eine Konsolenanwendung, um Tweets abzurufen, einen Tweet-Stimmungswert zu berechnen und die verarbeiteten Tweet-Wörter an HBase zu senden.

**So laden Sie die Stimmungswörterbuchdatei herunter**

1. Navigieren Sie zu [Realtime social sentiment analysis app](https://github.com/maxluk/tweet-sentiment).
2. Klicken Sie auf **Download ZIP**.
3. Entpacken Sie die Datei lokal.
4. Notieren Sie sich den Pfad der Datei **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv**. Sie benötigen ihn später in der Anwendung.

**So erstellen Sie die Visual Studio-Projektmappe**

1. Öffnen Sie **Visual Studio**.
2. Klicken Sie im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.
3. Geben Sie folgende Werte ein bzw. wählen diese aus:

	- Vorlage: **Visual C# / Windows Desktop / Konsolenanwendung**
	- Name: **TweetSentimentStreaming** 
	- Speicherort: **C:\\Tutorials**
	- Projektmappenname: **TweetSentimentStreaming**

4. Klicken Sie auf **OK**, um fortzufahren.
 


**So installieren Sie NuGet-Pakete und fügen SDK-Verweise hinzu**

1. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Die Konsole wird am unteren Ende der Seite geöffnet.
2. Installieren Sie mit den folgenden Befehlen das [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/), das als Clientbibliothek für den Zugriff auf den HBase-Cluster verwendet wird, und das Paket [Tweetinvi API](https://www.nuget.org/packages/TweetinviAPI/), das für den Zugriff auf die Twitter-API verwendet wird.

		Install-Package Microsoft.HBase.Client
		Install-Package TweetinviAPI
	
3. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Referenzen** und anschließend auf **Verweis hinzufügen**.
4. Erweitern Sie im linken Bereich das Feld **Assemblys**, und klicken Sie dann auf **Framework**.
5. Aktivieren Sie im rechten Bereich das Kontrollkästchen vor **System.Configuration**, und klicken Sie anschließend auf **OK**.



**So definieren Sie die Tweeter-Streamingdienst-Klasse**

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **TweetSentimentStreaming**. Zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Klasse**.
2. Geben Sie im Feld **Name** den Namen **HBaseWriter** ein, und klicken Sie dann auf **Hinzufügen**.
3. Fügen Sie in **HBaseWriter.cs** die folgenden **using**-Anweisungen am Anfang der Datei ein:

		using System.IO;		
		using System.Threading;
		using System.Globalization;
		using Microsoft.HBase.Client;
		using Tweetinvi.Core.Interfaces;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

4. Fügen Sie innerhalb von **HbaseWriter.cs** einen neuen Klassenaufruf **DictionaryItem** hinzu:

	    public class DictionaryItem
	    {
	        public string Type { get; set; }
	        public int Length { get; set; }
	        public string Word { get; set; }
	        public string Pos { get; set; }
	        public string Stemmed { get; set; }
	        public string Polarity { get; set; }
	    }

	Diese Klassenstruktur wird zum Analysieren der Stimmungsverzeichnis-Datei verwendet. Die Daten werden zur Berechnung des Stimmungswerts jedes Tweets verwendet.

5. Definieren Sie innerhalb der Klasse **HBaseWriter** die folgenden Konstanten und Variablen:

        // HDinsight HBase cluster and HBase table information
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Sentiment dictionary file and the punctuation characters
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '"', '#', '$', '%', '&', ''', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // For writting to HBase
        HBaseClient client;

        // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
        Dictionary<string, DictionaryItem> dictionary;
        
        // use multithread write
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. Legen Sie die Werte der Konstanten fest, darunter **&lt;HBaseClusterName>**, **&lt;HadoopUserName>** und **&lt;HaddopUserPassword>**. Wenn Sie den HBase-Tabellennamen ändern möchten, müssen Sie den Tabellennamen in der Webanwendung ebenfalls entsprechend ändern.

	Später im Lernprogramm laden Sie die Datei "dictionary.tsv" herunter und verschieben diese in einen bestimmten Ordner.

7. Definieren Sie innerhalb der Klasse **HBaseWriter** die folgenden Funktionen:

		// This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // create the HBase table if it doesn't exist
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table "{0}" is created.", HBASETABLENAME);
            }

            // Load sentiment dictionary from a file
            LoadDictionary();

			// Start a thread for writting to HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Enqueue the Tweets received
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Load sentiment dictionary from a file
        private void LoadDictionary()
        {
            List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
            var items = lines.Select(line =>
            {
                var fields = line.Split('\t');
                var pos = 0;
                return new DictionaryItem
                {
                    Type = fields[pos++],
                    Length = Convert.ToInt32(fields[pos++]),
                    Word = fields[pos++],
                    Pos = fields[pos++],
                    Stemmed = fields[pos++],
                    Polarity = fields[pos++]
                };
            });

            dictionary = new Dictionary<string, DictionaryItem>();
            foreach (var item in items)
            {
                if (!dictionary.Keys.Contains(item.Word))
                {
                    dictionary.Add(item.Word, item);
                }
            }
        }

        // Calculate sentiment score
        private int CalcSentimentScore(string[] words)
        {
            Int32 total = 0;
            foreach (string word in words)
            {
                if (dictionary.Keys.Contains(word))
                {
                    switch (dictionary[word].Polarity)
                    {
                        case "negative": total -= 1; break;
                        case "positive": total += 1; break;
                    }
                }
            }
            if (total > 0)
            {
                return 1;
            }
            else if (total < 0)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }

		// Popular a CellSet object to be written into HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Split the Tweet into words
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Calculate sentiment score base on the words
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // For each word in the Tweet add a row to the HBase table
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Create a row
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment 
                var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                row.values.Add(value);
                
                value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                row.values.Add(value);
                
                if (tweet.Coordinates != null)
                {
                    var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                    value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                    row.values.Add(value);
                }

                value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                row.values.Add(value);

                set.rows.Add(row);
            }
        }

        // Write a Tweet (CellSet) to HBase
        public void WriterThreadFunction()
        {
            try
            {
                while (threadRunning)
                {
                    if (queue.Count > 0)
                    {
                        CellSet set = new CellSet();
                        lock (queue)
                        {
                            do
                            {
                                ITweet tweet = queue.Dequeue();
                                CreateTweetByWordsCells(set, tweet);
                            } while (queue.Count > 0);
                        }

                        // Write the Tweet by words cell set to the HBase table
                        client.StoreCells(HBASETABLENAME, set);
                        Console.WriteLine("\tRows written: {0}", set.rows.Count);
                    }
                    Thread.Sleep(100);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception: " + ex.Message);
            }
        }

	Der Code umfasst die folgenden Funktionen:

	- **Mit HBase verbinden [ HBaseWriter() ]**: Erstellen Sie mithilfe des HBase SDK ein *ClusterCredentials*-Objekt mit der Cluster-URL und den Hadoop-Anmeldeinformationen. Erstellen Sie dann unter Verwendung des "ClusterCredentials"-Objekts ein *HBaseClient*-Objekt.
	- **HBase-Tabelle erstellen [ HBaseWriter() ]**: Der Methodenaufruf lautet *HBaseClient.CreateTable()*.
	- **In HBase-Tabelle schreiben [ WriterThreadFunction() ]**: Der Methodenaufruf lautet *HBaseClient.StoreCells()*.

**So stellen Sie "Program.cs" fertig**

1. Doppelklicken Sie im **Projektmappen-Explorer** auf **Program.cs**, um die Datei zu öffnen.
2. Fügen Sie am Anfang der Datei die folgenden **using**-Anweisungen hinzu:

		using System.Configuration;
		using System.Diagnostics;
		using Tweetinvi;

3. Definieren Sie innerhalb der Klasse **Program** die folgenden Konstanten:

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. Legen Sie die Werte der Konstanten übereinstimmend mit den Werten Ihrer Twitter-Anwendung fest.

3. Ändern Sie die **Main()**-Funktion so, dass sie wie folgt aussieht:

		static void Main(string[] args)
		{
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
		}

4. Fügen Sie der Klasse die folgende Funktion hinzu:

        private static void Stream_FilteredStreamExample()
        {
            for (; ; )
            {
                try
                {
                    HBaseWriter hbase = new HBaseWriter();
                    var stream = Stream.CreateFilteredStream();
                    stream.AddLocation(Geo.GenerateLocation(-180, -90, 180, 90));

                    var tweetCount = 0;
                    var timer = Stopwatch.StartNew();

                    stream.MatchingTweetReceived += (sender, args) =>
                    {
                        tweetCount++;
                        var tweet = args.Tweet;

                        // Write Tweets to HBase
                        hbase.WriteTweet(tweet);

                        if (timer.ElapsedMilliseconds > 1000)
                        {
                            if (tweet.Coordinates != null)
                            {
                                Console.ForegroundColor = ConsoleColor.Green;
                                Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                Console.ForegroundColor = ConsoleColor.White;
                                Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                            }

                            timer.Restart();
                            Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                            tweetCount = 0;
                        }
                    };

                    stream.StartStreamMatchingAllConditions();
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Exception: {0}", ex.Message);
                }
            }
        }



**So führen Sie den Streamingdienst aus**

1. Drücken Sie in Visual Studio die Taste **F5**. Nachfolgend sehen Sie einen Screenshot der Konsolenanwendung:

	![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. Führen Sie die Streaming-Konsolenanwendung weiter aus, während Sie die Webanwendung entwickeln. Auf diese Weise verfügen Sie über mehr zu verwendende Daten.



























##<a id="web"></a> Erstellen einer Website mit Azure-Websites zur Visualisierung der Twitter-Stimmung

In diesem Abschnitt erstellen Sie eine ASP.NET MVC-Webanwendung, die die Echtzeitdaten zur Stimmung aus HBase liest und die Daten auf Bing Maps grafisch darstellt.

**So erstellen Sie eine ASP.NET MVC-Webanwendung**

1. Öffnen Sie Visual Studio.
2. Klicken Sie auf **Datei**, auf **Neu** und anschließend auf **Projekt**.
3. Geben Sie Folgendes ein:

	- Vorlagenkategorie: **Visual C#/Web**
	- Vorlage: **ASP.NET-Webanwendung**
	- Name: **TweetSentimentWeb**
	- Speicherort: **C:\\Tutorials** 
4. Klicken Sie auf **OK**.
5. Klicken Sie unter **Vorlage auswählen** auf **MVC**. 
6. Klicken Sie in **Microsoft Azure** auf **Abonnements verwalten**.
7. Klicken Sie unter **Microsoft Azure-Abonnements verwalten** auf **Anmelden**.
8. Geben Sie Ihre Azure-Anmeldeinformationen ein. Auf der Registerkarte **Konten** werden die Informationen zu Ihrem Azure-Abonnement angezeigt.
9. Klicken Sie auf **Schließen**, um das Fenster **Microsoft Azure-Abonnements verwalten** zu schließen.
10. Klicken Sie unter **Neues ASP.NET-Projekt – TweetSentimentWeb** auf **OK**.
11. Wählen Sie unter **Microsoft Azure-Site-Einstellungen konfigurieren** die Ihnen am nächsten gelegene **Region** aus. Sie müssen keinen Datenbankserver angeben. 
12. Klicken Sie auf **OK**.

**So installieren Sie NuGet-Pakete**

1. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Die Konsole wird am unteren Ende der Seite geöffnet.
2. Verwenden Sie den folgenden Befehl zum Installieren des Pakets [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/), der Clientbibliothek für den Zugriff auf den HBase-Cluster:

		Install-Package Microsoft.HBase.Client 

**So fügen Sie die HBaseReader-Klasse hinzu**

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentiment**.
2. Klicken Sie mit der rechten Maustaste auf **Modelle** und anschließend auf **Hinzufügen** und **Klasse**.
3. Geben Sie im Feld **Name** den Namen **HBaseReader.cs** ein, und klicken Sie dann auf **Hinzufügen**.
4. Ersetzen Sie den Code durch den folgenden Code:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		
		using System.Configuration;
		using System.Threading.Tasks;
		using System.Text;
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;
		
		namespace TweetSentimentWeb.Models
		{
		    public class HBaseReader
		    {
		        // For reading Tweet sentiment data from HDInsight HBase
		        HBaseClient client;
		
		        // HDinsight HBase cluster and HBase table information
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // The constructor
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // Query Tweets sentiment data from the HBase table asynchronously 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // Demonstrate Filtering the data from the past 6 hours the row key
		            string timeIndex = (ulong.MaxValue -
		                (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
		            string startRow = keyword + "_" + timeIndex;
		            string endRow = keyword + "|";
		            Scanner scanSettings = new Scanner
		            {
		                batch = 100000,
		                startRow = Encoding.UTF8.GetBytes(startRow),
		                endRow = Encoding.UTF8.GetBytes(endRow)
		            };
		
		            // Make async scan call
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // find the cell with string pattern "d:coor" 
		                    var coordinates =
		                        row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		
		                        var sentimentField =
		                            row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
		                        Int32 sentiment = 0;
		                        if (sentimentField != null)
		                        {
		                            sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
		                        }
		
		                        list.Add(new Tweet
		                        {
		                            Longtitude = Convert.ToDouble(lonlat[0]),
		                            Latitude = Convert.ToDouble(lonlat[1]),
		                            Sentiment = sentiment
		                        });
		                    }
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		                    }
		                }
		            }
		
		            return list;
		        }
		    }
		
		    public class Tweet
		    {
		        public string IdStr { get; set; }
		        public string Text { get; set; }
		        public string Lang { get; set; }
		        public double Longtitude { get; set; }
		        public double Latitude { get; set; }
		        public int Sentiment { get; set; }
		    }
		}

4. Ändern Sie in der Klasse **HBaseReader** die Konstantenwerte:

	- **CLUSTERNAME**: Der Name des HBase-Clusters, z. B. *https://<HBaseClusterName>.azurehdinsight.net/*. 
    - **HADOOPUSERNAME**: Der Hadoop-Benutzername des HBase-Clusters. Der Standardname lautet *admin*.
    - **HADOOPUSERPASSWORD**: Das Hadoop-Kennwort des HBase-Clusters.
    - **HBASETABLENAME** = "tweets_by_words";

	Der HBase-Tabellenname lautet **"tweets_by_words";**. Die Werte müssen mit denen übereinstimmen, die Sie im Streamingdienst gesendet haben, damit die Webanwendung die Daten aus der gleichen HBase-Tabelle liest.




**So fügen Sie den "TweetsController"-Controller hinzu**

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**.
2. Klicken Sie mit der rechten Maustaste auf **Controller** und anschließend auf **Hinzufügen** und **Controller**.
3. Klicken Sie auf **Web-API-2-Controller -- Leer** und dann auf **Hinzufügen**.
4. Geben Sie im Feld **Controllername** den Namen **TweetsController** ein, und klicken Sie dann auf **Hinzufügen**.
5. Doppelklicken Sie im **Projektmappen-Explorer** auf "TweetsController.cs", um die Datei zu öffnen.
5. Ändern Sie die Datei so, dass sie wie folgt aussieht:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		
		using System.Threading.Tasks;
		using TweetSentimentWeb.Models;
		
		namespace TweetSentimentWeb.Controllers
		{
		    public class TweetsController : ApiController
		    {
		        HBaseReader hbase = new HBaseReader();
		
		        public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
		        {
		            return await hbase.QueryTweetsByKeywordAsync(query);
		        }
		    }
		}

**Hinzufügen von heatmap.js**

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**.
2. Klicken Sie mit der rechten Maustaste auf **Skripts** und anschließend auf **Hinzufügen** und **JavaScript-Datei**.
3. Geben Sie im Feld **Elementname** den Namen **heatmap.js** ein.
4. Fügen Sie den folgenden Code in die Datei ein. Der Code wurde von Alastair Aitchison geschrieben. Weitere Informationen finden Sie unter [Bing Maps AJAX v7 HeatMap Library](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/) (in englischer Sprache).

		/*******************************************************************************
		* Author: Alastair Aitchison
		* Website: http://alastaira.wordpress.com
		* Date: 15th April 2011
		* 
		* Description: 
		* This JavaScript file provides an algorithm that can be used to add a heatmap
		* overlay on a Bing Maps v7 control. The intensity and temperature palette
		* of the heatmap are designed to be easily customisable.
		*
		* Requirements:
		* The heatmap layer itself is created dynamically on the client-side using
		* the HTML5 <canvas> element, and therefore requires a browser that supports
		* this element. It has been tested on IE9, Firefox 3.6/4 and 
		* Chrome 10 browsers. If you can confirm whether it works on other browsers or
		* not, I'd love to hear from you!
		
		* Usage:
		* The HeatMapLayer constructor requires:
		* - A reference to a map object
		* - An array or Microsoft.Maps.Location items
		* - Optional parameters to customise the appearance of the layer
		*  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
		*
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* Private Properties */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // Set default options
		    var _options = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 1000,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'meters',
		
		        // Colour temperature gradient of the map
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)',  // Magenta
		            "0.25": 'rgba(0,0,255,40)',    // Blue
		            "0.50": 'rgba(0,255,0,80)',    // Green
		            "0.75": 'rgba(255,255,0,120)', // Yellow
		            "1.00": 'rgba(255,0,0,150)'    // Red
		        },
		
		        // Callback function to be fired after heatmap layer has been redrawn 
		        callback: null
		    };
		
		    /* Private Methods */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // Create the canvas element
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // Override defaults with any options passed in the constructor
		            _setOptions(options);
		
		            // Load array of location data
		            _setPoints(locations);
		
		            // Create a colour gradient from the suppied colourstops
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // Wire up the event handler to redraw heatmap canvas
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // Resets the heat map
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // Creates a colour gradient from supplied colour stops on initialisation
		    function _createColourGradient(colourstops) {
		        var ctx = document.createElement('canvas').getContext('2d');
		        var grd = ctx.createLinearGradient(0, 0, 256, 0);
		        for (var c in colourstops) {
		            grd.addColorStop(c, colourstops[c]);
		        }
		        ctx.fillStyle = grd;
		        ctx.fillRect(0, 0, 256, 1);
		        return ctx.getImageData(0, 0, 256, 1).data;
		    }
		
		    // Applies a colour gradient to the intensity map
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // get the alpha of this pixel
		            if (a != 0) { // If there is any data to plot
		                pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
		                pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
		                pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
		            }
		            p += 4; // Move on to the next pixel
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // Sets any options passed in
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Sets the heatmap points from an array of Microsoft.Maps.Locations  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // Main method to draw the heatmap
		    function _createHeatMap() {
		        // Ensure the canvas matches the current dimensions of the map
		        // This also has the effect of resetting the canvas
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // Calculate the pixel radius of each heatpoint at the current map zoom
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // Convert lat/long to pixel location
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // Create the Intensity Map by looping through each location
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // Create radial gradient centred on this point
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // Draw the heatpoint onto the canvas
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // Apply the specified colour gradient to the intensity map
		        _colouriseHeatMap();
		
		        // Call the callback function, if specified
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* Public Methods */
		
		    this.Show = function () {
		        if (_canvas) {
		            _canvas.style.display = '';
		        }
		    };
		
		    this.Hide = function () {
		        if (_canvas) {
		            _canvas.style.display = 'none';
		        }
		    };
		
		    // Sets options for intensity, radius, colourgradient etc.
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
		    this.SetPoints = function (locations) {
		        // Reset the existing heatmap layer
		        _clearHeatMap();
		        // Pass in the new set of locations
		        _setPoints(locations);
		        // Recreate the layer
		        _createHeatMap();
		    }
		
		    // Removes the heatmap layer from the DOM
		    this.Remove = function () {
		        _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);
		
		        if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
		        if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }
		
		        _locations = null;
		        _temperaturemap = null;
		        _canvas = null;
		        _options = null;
		        _viewchangestarthandler = null;
		        _viewchangeendhandler = null;
		    }
		
		    // Call the initialisation routine
		    _init();
		};
		
		// Call the Module Loaded method
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**So fügen Sie die Datei "twitterStream.js" hinzu**

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**.
2. Klicken Sie mit der rechten Maustaste auf **Skripts** und anschließend auf **Hinzufügen** und **JavaScript-Datei**.
3. Geben Sie im Feld **Elementname** den Namen **twitterStream.js** ein.
4. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei ein:

		var liveTweetsPos = [];
		var liveTweets = [];
		var liveTweetsNeg = [];
		var map;
		var heatmap;
		var heatmapNeg;
		var heatmapPos;
		
		function initialize() {
		    // Initialize the map
		    var options = {
		        credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        center: new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2.5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // Heatmap options for positive, neutral and negative layers
		
		    var heatmapOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 255, 191, 1)',
		            0.3: 'rgba(0, 255, 127, 1)',
		            0.4: 'rgba(0, 255, 63, 1)',
		            0.5: 'rgba(0, 127, 0, 1)',
		            0.7: 'rgba(0, 159, 0, 1)',
		            0.8: 'rgba(0, 191, 0, 1)',
		            0.9: 'rgba(0, 223, 0, 1)',
		            1.0: 'rgba(0, 255, 0, 1)'
		        }
		    };
		
		    var heatmapNegOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 191, 255, 1)',
		            0.3: 'rgba(0, 127, 255, 1)',
		            0.4: 'rgba(0, 63, 255, 1)',
		            0.5: 'rgba(0, 0, 127, 1)',
		            0.7: 'rgba(0, 0, 159, 1)',
		            0.8: 'rgba(0, 0, 191, 1)',
		            0.9: 'rgba(0, 0, 223, 1)',
		            1.0: 'rgba(0, 0, 255, 1)'
		        }
		    };
		
		    // Register and load the Client Side HeatMap Module
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback: function () {
		            // Create heatmap layers for positive, neutral and negative tweets
		            heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
		            heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
		            heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
		        }
		    });
		
		    $("#searchbox").val("xbox");
		    $("#searchBtn").click(onsearch);
		    $("#positiveBtn").click(onPositiveBtn);
		    $("#negativeBtn").click(onNegativeBtn);
		    $("#neutralBtn").click(onNeutralBtn);
		    $("#neutralBtn").button("toggle");
		}
		
		function onsearch() {
		    var uri = 'api/tweets?query=';
		    var query = $('#searchbox').val();
		    $.getJSON(uri + query)
		        .done(function (data) {
		            liveTweetsPos = [];
		            liveTweets = [];
		            liveTweetsNeg = [];
		
		            // On success, 'data' contains a list of tweets.
		            $.each(data, function (key, item) {
		                addTweet(item);
		            });
		
		            if (!$("#neutralBtn").hasClass('active')) {
		                $("#neutralBtn").button("toggle");
		            }
		            onNeutralBtn();
		        })
		        .fail(function (jqXHR, textStatus, err) {
		            $('#statustext').text('Error: ' + err);
		        });
		}
		
		function addTweet(item) {
		    //Add tweet to the heat map arrays.
		    var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
		    if (item.Sentiment > 0) {
		        liveTweetsPos.push(tweetLocation);
		    } else if (item.Sentiment < 0) {
		        liveTweetsNeg.push(tweetLocation);
		    } else {
		        liveTweets.push(tweetLocation);
		    }
		}
		
		function onPositiveBtn() {
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmapPos.SetPoints(liveTweetsPos);
		    heatmapPos.Show();
		    heatmapNeg.Hide();
		    heatmap.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
		}
		
		function onNeutralBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmap.SetPoints(liveTweets);
		    heatmap.Show();
		    heatmapNeg.Hide();
		    heatmapPos.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
		}
		
		function onNegativeBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		
		    heatmapNeg.SetPoints(liveTweetsNeg);
		    heatmapNeg.Show();
		    heatmap.Hide();;
		    heatmapPos.Hide();;
		
		    $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
		}
		
		function getPosNegRatio() {
		    if (liveTweetsNeg.length == 0) {
		        return "";
		    }
		    else {
		        var ratio = liveTweetsPos.length / liveTweetsNeg.length;
		        var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
		        return "Positive/Negative Ratio: " + str;
		    }
		}


**So ändern Sie die Datei "layout.cshtml"**

1. Erweitern Sie im **Projektmappen-Explorer** nacheinander **TweetSentimentWeb**, **Ansichten**, **Freigegeben**, und doppelklicken Sie abschließend auf _**Layout.cshtml**.2. Ersetzen Sie den Inhalt durch Folgendes:

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		    <!-- Bing Maps -->
		    <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
		    <!-- Spatial Dashboard JavaScript -->
		    <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
		</head>
		<body onload="initialize()">
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		            </div>
		            <div class="navbar-collapse collapse">
		                <div class="row">
		                    <ul class="nav navbar-nav col-lg-5">
		                        <li class="col-lg-12">
		                            <div class="navbar-form">
		                                <input id="searchbox" type="search" class="form-control">
		                                <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
		                            </div>
		                        </li>
		                    </ul>
		                    <ul class="nav navbar-nav col-lg-7">
		                        <li>
		                            <div class="navbar-form">
		                                <div class="btn-group" data-toggle="buttons-radio">
		                                    <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
		                                    <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
		                                    <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
		                                </div>
		                            </div>
		                        </li>
		                        <li><span id="statustext" class="navbar-text"></span></li>
		                    </ul>
		                </div>
		            </div>
		        </div>
		    </div>
		    <div class="map_container">
		        @RenderBody()
		    </div>
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>



**So ändern Sie die Datei "Index.cshtml"**

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**, **Ansichten**, **Home**, und doppelklicken Sie abschließend auf **Layout.cshtml**.
2. Ersetzen Sie den Inhalt durch Folgendes:

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**So ändern Sie die Datei "site.css"**

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**, dann **Inhalt** und doppelklicken Sie abschließend auf **Site.css**.
2. Fügen Sie den folgenden Code in die Datei ein:
		
		/* make container, and thus map, 100% width */
		.map_container {
			width: 100%;
			height: 100%;
		}
		
		#map_canvas{
		  height:100%;
		}
		
		#tweets{
		  position: absolute;
		  top: 60px;
		  left: 75px;
		  z-index:1000;
		  font-size: 30px;
		}

**So ändern Sie die Datei "global.asax"**

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**, und doppelklicken Sie dann auf **Global.asax**.
2. Fügen Sie die folgende **using**-Anweisung hinzu:

		using System.Web.Http;

2. Fügen Sie der Funktion **Application_Start()** die folgenden Zeilen hinzu:

		// Register API routes
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	Ändern Sie die Registrierung der API-Routen, damit der Web API-Controller in der MVC-Anwendung funktionsfähig ist.

**So führen Sie die Webanwendung aus**

1. Stellen Sie sicher, dass die Streaming-Konsolenanwendung noch ausgeführt wird, damit Sie die Änderungen in Echtzeit sehen können.
2. Drücken Sie **F5**, um die Webanwendung auszuführen.

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. Geben Sie ein Schlüsselwort in das Textfeld ein, und klicken Sie dann auf **Los**. Je nach den in der HBase-Tabelle gesammelten Daten werden einige Schlüsselwörter ggf. nicht gefunden. Probieren Sie einige gebräuchliche Schlüsselwörter aus, beispielsweise "liebe", "xbox" oder "playstation". 
3. Wechseln Sie zwischen **Positiv**, **Neutral** und **Negativ**, um die Stimmungen zum Thema zu vergleichen.
4. Führen Sie den Streamingdienst eine weitere Stunde lang aus, suchen Sie dann nach dem gleichen Schlüsselwort, und vergleichen Sie die Ergebnisse.

 
Optional können Sie die Anwendung auf Azure-Websites bereitstellen. Anweisungen finden Sie unter [Erste Schritte mit Azure-Websites und ASP.NET][website-get-started].
 
##<a id="nextsteps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Sie Tweets abrufen, die Stimmung von Tweets analysieren, die Stimmungsdaten in HBase speichern und die Echtzeit-Stimmungsdaten von Twitter in Bing Maps darstellen. Weitere Informationen finden Sie unter:

- [Erste Schritte mit HDInsight][hdinsight-get-started]
- [Konfigurieren von HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md) 
- [Analysieren von Twitter-Daten mit Hadoop in HDInsight][hdinsight-analyze-twitter-data]
- [Analysieren von Daten zu Flugverspätungen mit HDInsight][hdinsight-analyze-flight-delay-data]
- [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][hdinsight-develop-streaming]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]


[hbase-get-started]: ../hdinsight-hbase-get-started.md
[website-get-started]: ../web-sites-dotnet-get-started.md



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-analyze-twitter-data]: hdinsight-analyze-twitter-data.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md


<!--HONumber=54--> 