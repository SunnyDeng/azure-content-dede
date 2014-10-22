<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze realt-time Twitter sentiment with Hbase in HDInsight" pageTitle="Analyze real-time Twitter sentiment with HBase in HDInsight | Azure" metaKeywords="" description="Learn how to do real-time analysis of big data using HBase in an HDInsight (Hadoop) cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze real-time Twitter sentiment with HBase in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Analysieren Sie Twitter-Stimmungen mit HBase in HDInsight

Erfahren Sie, wie Sie Echtzeit-[Stimmungsanalysen][Stimmungsanalysen] von Big Data mit HBase in einem HDInsight (Hadoop)-Cluster durchführen.

Soziale Netzwerke sind einer der Haupt-Antriebsfaktoren für die Einführung von Big Data. Öffentliche APIs von Websites wie Twitter sind eine nützliche Datenquelle für Analyse und Verständnis beliebter Trends. In diesem Lernprogramm entwickeln Sie eine Konsolenanwendung für Streamingdienste und eine ASP.NET-Webanwendung für folgende Zwecke:

![][]

-   Erhalten Sie örtlich lokalisierte Tweets über die Twitter-Streaming-API.
-   Analysieren Sie die Stimmung dieser Tweets.
-   Speichern Sie die Stimmungsinformationen in HBase mithilfe des Microsoft HBase SDK.
-   Stellen Sie die Echtzeit-Statistikergebnisse auf Bing Maps mithilfe einer ASP.NET-Webanwendung grafisch dar. Eine Visualisierung der Tweets sieht etwa so aus:

    ![hdinsight.hbase.twitter.sentiment.bing.map][hdinsight.hbase.twitter.sentiment.bing.map]

    Sie können Tweets mit bestimmten Schlüsselwörtern abfragen, um abzuschätzen, ob die geäußerten Meinungen in Tweets positiv, negativ oder neutral sind.

Ein vollständiges Visual Studio-Lösungsbeispiel finden Sie unter [][]<https://github.com/maxluk/tweet-sentiment></a>.

## Themen in diesem Artikel

-   [Voraussetzungen][Voraussetzungen]
-   [Erstellen einer Twitter-Anwendung][Erstellen einer Twitter-Anwendung]
-   [Erstellen eines einfachen Twitter-Streamingdiensts][Erstellen eines einfachen Twitter-Streamingdiensts]
-   [Erstellen einer Azure-Website zur Visualisierung der Twitter-Stimmung][Erstellen einer Azure-Website zur Visualisierung der Twitter-Stimmung]
-   [Nächste Schritte][Nächste Schritte]

## <span id="prerequisites"></span></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Ein HBase-Cluster in HDInsight**. Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HBase mit Hadoop in HDInsight][Erste Schritte mit HBase mit Hadoop in HDInsight]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

    | Clustereigenschaft       | Beschreibung                                                                                    |
    |--------------------------|-------------------------------------------------------------------------------------------------|
    | HBase-Clustername        | Dies ist der Name Ihres HDInsight HBase-Clusters. Beispiel: https://myhbase.azurehdinsight.net/ |
    | Clusterbenutzer-Name     | Der Name des Hadoop-Benutzerkontos. Der Standard-Benutzername für Hadoop ist **admin**.         |
    | Clusterbenutzer-Kennwort | Das Benutzerkennwort für den Hadoop-Cluster.                                                    |

-   **Eine Workstation** mit installiertem Visual Studio 2013. [Installieren von Visual Studio][Installieren von Visual Studio]

## <span id="twitter"></span></a>Erstellen einer Twitter-Anwendungs-ID und von Geheimschlüsseln

Die Streaming-APIs von Twitter verwenden [OAuth][OAuth], um Anforderungen zu autorisieren.

Um OAuth zu verwenden, müssen Sie zunächst auf der Twitter-Entwicklerwebsite eine neue Anwendung erstellen.

**Erstellen einer Twitter-Anwendungs-ID und von Geheimschlüsseln:**

1.  Melden Sie sich bei [][1]<https://apps.twitter.com/></a> an. Klicken Sie auf den Link **Registriere dich jetzt**, wenn Sie noch kein Twitter-Konto haben.
2.  Klicken Sie auf **Create New App**.
3.  Geben Sie **Name**, **Description** und **Website** ein. Das Feld "Website" ist nicht zwingend erforderlich. Es muss keine gültige URL enthalten. Die folgende Tabelle zeigt einige mögliche Beispielwerte:

    | Feld         | Wert                               |
    |--------------|------------------------------------|
    | Name         | MyHDInsightHBaseApp                |
    | Beschreibung | MyHDInsightHBaseApp                |
    | Website      | http://www.myhdinsighthbaseapp.com |

4.  Aktivieren Sie **Yes, I agree**, und klicken Sie dann auf **Create your Twitter application**.
5.  Klicken Sie auf die Registerkarte **Permissions**. Die Standardberechtigung ist **Read only**. Diese Berechtigung reicht für dieses Lernprogramm aus.
6.  Klicken Sie auf die Registerkarte **API Keys**.
7.  Klicken Sie auf **Create my access token**.
8.  Klicken Sie oben rechts auf der Seite auf **Test OAuth**.
9.  Notieren Sie **API key**, **API secret**, **Access token** und **Access token secret**. Sie benötigen diese Werte später im Lernprogramm.

    ![hdi.hbase.twitter.sentiment.twitter.app][hdi.hbase.twitter.sentiment.twitter.app]

## <span id="streaming"></span></a> Erstellen eines einfachen Twitter-Streamingdiensts

Erstellen Sie eine Konsolenanwendung, um Tweets zu erhalten, einen Tweet-Stimmungswert zu berechnen und die verarbeiteten Tweet-Wörter an HBase zu senden.

**Erstellen der Visual Studio-Lösung:**

1.  Öffnen Sie **Visual Studio**.
2.  Klicken Sie im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.
3.  Geben Sie folgende Werte ein bzw. wählen diese aus:

    -   Vorlagen: **Visual C#**
    -   Vorlage: **Konsolenanwendung**
    -   Name: **TweetSentimentStreaming**
    -   Location: **C:\\Tutorials**
    -   Projektmappenname: **TweetSentimentStreaming**

4.  Klicken Sie auf **OK**, um fortzufahren.

**Nuget-Pakete installieren und SDK-Referenzen hinzufügen:**

1.  Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Die Konsole wird am unteren Ende der Seite geöffnet.
2.  Verwenden Sie die folgenden Befehle, um das zum Zugriff auf die Twitter-API verwendete [Tweetinvi][Tweetinvi]-Paket sowie das zum Serialisieren und Deserialisieren von Objekten verwendete [Protobuf-net][Protobuf-net]-Paket zu installieren.

        Install-Package TweetinviAPI
        Install-Package protobuf-net 

    > [WACOM.NOTE] Das Microsoft Hbase SDK Nuget-Paket ist mit Stand vom 26. August 2014 nicht verfügbar. Das Github-Repository lautet [][2]<https://github.com/hdinsight/hbase-sdk-for-net></a>. Bis das SDK verfügbar ist, müssen Sie die dll-Datei selbst erstellen. Anweisungen finden Sie unter [Erste Schritte mit HBase mit Hadoop in HDInsight][Erste Schritte mit HBase mit Hadoop in HDInsight].

3.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Referenzen** und anschließend auf **Verweis hinzufügen**.
4.  Erweitern Sie im linken Bereich das Feld **Assemblys**, und klicken Sie dann auf **Framework**.
5.  Aktivieren Sie im rechten Bereich das Kontrollkästchen vor **System.Configuration**, und klicken Sie anschließend auf **OK**.

**Definieren der Twitter-Streamingdienst-Klasse:**

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **TweetSentimentStreaming**. Klicken Sie auf **Hinzufügen** und anschließend auf **Klasse**.
2.  Geben Sie in **Name** **HBaseWriter** ein, und klicken Sie dann auf **Hinzufügen**.
3.  Fügen Sie in **HbaseWriter.cs** die folgenden using-Anweisungen am Anfang der Datei ein:

        using System.IO;        
        using System.Threading;
        using System.Globalization;
        using Microsoft.HBase.Client;
        using Tweetinvi.Core.Interfaces;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

4.  Fügen Sie innerhalb von **HbaseWriter.cs** einen neuen Klassenaufruf **DictionaryItem** hinzu:

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

5.  Definieren Sie innerhalb der Klasse **HBaseWriter** die folgenden Konstanten und Variablen:

        // HDinsight HBase cluster and HBase table information
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Sentiment dictionary file and the punctuation characters
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // For writting to HBase
        HBaseClient client;

        // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
        Dictionary<string, DictionaryItem> dictionary;

        // use multithread write
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6.  Legen Sie die Werte der Konstanten fest, darunter **\<HBaseClusterName\>**, **\<HadoopUserName\>** und **\<HaddopUserPassword\>**. Falls Sie den HBase-Tabellennamen ändern möchten, müssen Sie den Tabellennamen in der Webanwendung ebenfalls entsprechend ändern.

    Später im Lernprogramm laden Sie die Datei "dictionary.tsv" herunter und verschieben diese in einen bestimmten Ordner.

7.  Definieren Sie innerhalb der Klasse **HBaseWriter** die folgenden Funktionen:

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
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
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

    Der Code bietet die folgenden Funktionen:

    -   **Mit HBase verbinden [ HBaseWriter() ]**: Erstellen Sie mithilfe des HBase SDK ein *ClusterCredentials*-Objekt mit der Cluster-URL und den Hadoop-Anmeldeinformationen. Erstellen Sie dann unter Verwendung des ClusterCredentials-Objekts ein *HBaseClient*-Objekt.
    -   **HBase-Tabelle erstellen [ HBaseWriter() ]**: Der Methodenaufruf lautet *HBaseClient.CreateTable()*.
    -   **In HBase-Tabelle schreiben [ WriterThreadFunction() ]**: Der Methodenaufruf lautet *HBaseClient.StoreCells()*.

**Fertigstellen von Program.cs:**

1.  Doppelklicken Sie im **Projektmappen-Explorer** auf **Program.cs**, um die Datei zu öffnen.
2.  Fügen Sie am Anfang der Datei die folgenden using-Anweisungen hinzu:

        using System.Configuration;
        using System.Diagnostics;
        using Tweetinvi;

3.  Definieren Sie innerhalb der Klasse **Program** die folgenden Konstanten:

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4.  Legen Sie die Werte der Konstanten übereinstimmend mit den Werten Ihrer Twitter-Anwendung fest.

5.  Modifizieren Sie die Funktion **Main()** so, dass sie Folgendem entspricht:

        static void Main(string[] args)
        {
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
        }

6.  Fügen Sie der Klasse die folgende Funktion hinzu:

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

**Herunterladen der Stimmungsverzeichnis-Datei:**

1.  Navigieren Sie zur Webseite [][]<https://github.com/maxluk/tweet-sentiment></a>.
2.  Klicken Sie auf **Download ZIP**.
3.  Entpacken Sie die Datei lokal.
4.  Kopieren Sie die Datei von **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv**.
5.  Fügen Sie die Datei in Ihre Lösung unter **TweetSentimentStreaming/TweetSentimentStreaming/data/dictionary/dictionary.tsv** ein.

**Starten des Streamingdiensts:**

1.  Drücken Sie in Visual Studio die Taste **F5**. Es folgt ein Screenshot der Konsolenanwendung:

    ![hdinsight.hbase.twitter.sentiment.streaming.service][hdinsight.hbase.twitter.sentiment.streaming.service]

2.  Lassen Sie die Streaming-Konsolenanwendung ausführen, während Sie die Webanwendung entwickeln. Auf diese Weise verfügen Sie über mehr Daten zur Verwendung.

## <span id="web"></span></a> Erstellen einer Azure-Website zur Visualisierung der Twitter-Stimmung

In diesem Abschnitt erstellen Sie eine ASP.NET MVC-Webanwendung, die die Stimmungs-Echtzeitdaten aus HBase liest und die Daten auf Bing Maps grafisch darstellt.

**Erstellen einer ASP.NET MVC-Webanwendung:**

1.  Öffnen Sie Visual Studio.
2.  Klicken Sie auf **Datei**, auf **Neu** und anschließend auf **Projekt**.
3.  Geben Sie Folgendes ein:

    -   Vorlagenkategorie: **Visual C#/Web**
    -   Vorlage: **ASP.NET-Webanwendung**
    -   Name: **TweetSentimentWeb**
    -   Location: **C:\\Tutorials**

4.  Klicken Sie auf **OK**.
5.  Klicken Sie unter **Vorlage auswählen** auf **MVC**.
6.  Klicken Sie in **Windows Azure** auf **Abonnements verwalten**.
7.  Klicken Sie unter **Windows Azure-Abonnements verwalten** auf **Anmelden**.
8.  Geben Sie Ihre Azure-Anmeldeinformationen ein. Auf der Registerkarte "Konten" werden die Informationen zu Ihrem Azure-Abonnement angezeigt.
9.  Klicken Sie auf **Schließen**, um das Fenster "Windows Azure-Abonnements verwalten" zu schließen.
10. Klicken Sie unter **Neues ASP.NET-Projekt – TweetSentimentWeb** auf **OK**.
11. Wählen Sie unter **Windows Azure-Site-Einstellungen konfigurieren** die Ihnen am nächsten gelegene **Region** aus. Sie müssen keinen Datenbankserver angeben.
12. Klicken Sie auf **OK**.

**Nuget-Pakete installieren:**

1.  Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Die Konsole wird am unteren Ende der Seite geöffnet.
2.  Verwenden Sie den folgenden Befehl, um das zum Serialisieren und Deserialisieren von Objekten verwendete [Protobuf-net][Protobuf-net]-Paket zu installieren.

        Install-Package protobuf-net 

    > [WACOM.NOTE] Das Microsoft Hbase SDK Nuget-Paket ist mit Stand vom 20. August 2014 nicht verfügbar. Das Github-Repository lautet [][2]<https://github.com/hdinsight/hbase-sdk-for-net></a>. Bis das SDK verfügbar ist, müssen Sie die dll-Datei selbst erstellen. Anweisungen finden Sie unter [Erste Schritte mit HBase mit Hadoop in HDInsight][Erste Schritte mit HBase mit Hadoop in HDInsight].

**Hinzufügen der Klasse HBaseReader:**

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentiment**.
2.  Klicken Sie mit der rechten Maustaste auf **Modelle** und anschließend auf **Hinzufügen** und **Klasse**.
3.  Geben Sie in "Name" **HBaseReader.cs** ein, und klicken Sie dann auf **Hinzufügen**.
4.  Ersetzen Sie den Code durch den folgenden Code:

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

5.  Ändern Sie innerhalb der Klasse **HBaseReader** die Konstantenwerte:

    -   **CLUSTERNAME**: Der HBase-Clustername. Beispiel: *<https://><hbaseclustername>.azurehdinsight.net/*.
    -   **HADOOPUSERNAME**: Der Hadoop-Benutzername des HBase-Clusters. Der Standardname lautet *admin*.
    -   **HADOOPUSERPASSWORD**: Das Hadoop-Benutzerkennwort des HBase-Clusters.
    -   **HBASETABLENAME** = "tweets\_by\_words";

    Der HBase-Tabellenname lautet "tweets\_by\_words". Die Werte müssen mit denen übereinstimmen, die Sie im Streamingdienst gesendet haben, damit die Webanwendung die Daten aus der gleichen HBase-Tabelle liest.

**Hinzufügen des Controllers "TweetsController":**

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**.
2.  Klicken Sie mit der rechten Maustaste auf **Controller** und anschließend auf **Hinzufügen** und **Controller**.
3.  Klicken Sie auf **Web-API-2-Controller -- Leer** und dann auf **Hinzufügen**.
4.  Geben Sie als Controllernamen **TweetsController** ein, und klicken Sie dann auf **Hinzufügen**.
5.  Doppelklicken Sie im **Projektmappen-Explorer** auf "TweetsController.cs", um die Datei zu öffnen.
6.  Modifizieren Sie die Datei so, dass sie Folgendem entspricht:

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

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**.
2.  Klicken Sie mit der rechten Maustaste auf **Skripts** und anschließend auf **Hinzufügen** und **JavaScript-Datei**.
3.  Geben Sie unter "Item Name" **heatmap.js** ein.
4.  Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei ein. Der Code wurde von Alastair Aitchison geschrieben. Weitere Informationen finden Sie unter [][3]<http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/></a>.

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

**Hinzufügen von tweetStream.js:**

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**.
2.  Klicken Sie mit der rechten Maustaste auf **Skripts** und anschließend auf **Hinzufügen** und **JavaScript-Datei**.
3.  Geben Sie unter "Item Name" **tweetStream.js** ein.
4.  Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei ein:

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

**Ändern der Datei "layout.cshtml":**

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**, **Ansichten**, **Freigegeben**, und doppelklicken Sie abschließend auf **Layout.cshtml**.
2.  Ersetzen Sie den Inhalt durch Folgendes:

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

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**, **Ansichten**, **Home**, und doppelklicken Sie abschließend auf **Layout.cshtml**.
2.  Ersetzen Sie den Inhalt durch Folgendes:

        @{
            ViewBag.Title = "Tweet Sentiment";
        }

        <div class="map_container">
            <div id="map_canvas"/>
        </div>

**Ändern der Datei "site.css":**

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**, dann **Inhalt** und doppelklicken Sie abschließend auf **Site.css**.
2.  Ergänzen Sie den folgenden Code in der Datei.

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

**Ändern der Datei "global.asax":**

1.  Erweitern Sie im **Projektmappen-Explorer** den Eintrag **TweetSentimentWeb**, und doppelklicken Sie dann auf **Global.asax**.
2.  Fügen Sie die folgende using-Anweisung hinzu:

        using System.Web.Http;

3.  Fügen Sie der Funktion **Application\_Start()** die folgenden Zeilen hinzu:

        // Register API routes
        GlobalConfiguration.Configure(WebApiConfig.Register);

    Ändern sie die Registrierung der API-Routen, damit Web API Controller innerhalb der MVC-Anwendung funktionsfähig ist.

**Ausführen der Webanwendung:**

1.  Prüfen Sie, ob die Konsolenanwendung für den Streamingdienst noch ausgeführt wird. Auf diese Weise sehen Sie die Echtzeitänderungen.
2.  Drücken Sie **F5**, um die Webanwendung auszuführen.

    ![hdinsight.hbase.twitter.sentiment.bing.map][hdinsight.hbase.twitter.sentiment.bing.map]

3.  Geben Sie ein Schlüsselwort in das Textfeld ein, und klicken Sie dann auf **Los**. Je nach den in der HBase-Tabelle gesammelten Daten werden einige Schlüsselwörter ggf. nicht gefunden. Probieren Sie einige gebräuchliche Schlüsselwörter aus, beispielsweise "liebe", "xbox", "playstation" usw.
4.  Wechseln Sie zwischen **Positiv**, **Neutral** und **Negativ**, um die Stimmungen zum Thema zu vergleichen.
5.  Führen Sie den Streamingdienst eine weitere Stunde lang aus, suchen Sie dann nach dem gleichen Schlüsselwort, und vergleichen Sie die Ergebnisse.

Optional können Sie die Anwendung auf einer Azure-Website bereitstellen. Anweisungen finden Sie unter [Erste Schritte mit Azure-Websites und ASP.NET][Erste Schritte mit Azure-Websites und ASP.NET].

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Tweets abrufen, die Stimmung von Tweets analysieren, die Stimmungsdaten in HBase speichern und die Echtzeit-Stimmungsdaten von Twitter in Bing Maps präsentieren. Weitere Informationen finden Sie unter:

-   [Erste Schritte mit HDInsight][Erste Schritte mit HDInsight]
-   [Analysieren von Twitter-Daten mit Hadoop in HDInsight][Analysieren von Twitter-Daten mit Hadoop in HDInsight]
-   [Analysieren von Daten zu Flugverspätungen mit HDInsight][Analysieren von Daten zu Flugverspätungen mit HDInsight]
-   [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight]
-   [Entwickeln von Java MapReduce-Programmen für HDInsight][Entwickeln von Java MapReduce-Programmen für HDInsight]

  [Stimmungsanalysen]: http://en.wikipedia.org/wiki/Sentiment_analysis
  []: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
  [hdinsight.hbase.twitter.sentiment.bing.map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png
  []: https://github.com/maxluk/tweet-sentiment
  [Voraussetzungen]: #prerequisites
  [Erstellen einer Twitter-Anwendung]: #twitter
  [Erstellen eines einfachen Twitter-Streamingdiensts]: #streaming
  [Erstellen einer Azure-Website zur Visualisierung der Twitter-Stimmung]: #web
  [Nächste Schritte]: #nextsteps
  [Erste Schritte mit HBase mit Hadoop in HDInsight]: ../hdinsight-hbase-get-started/
  [Installieren von Visual Studio]: http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx
  [OAuth]: http://oauth.net/
  [1]: https://apps.twitter.com/
  [hdi.hbase.twitter.sentiment.twitter.app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
  [Tweetinvi]: https://www.nuget.org/packages/TweetinviAPI/
  [Protobuf-net]: https://www.nuget.org/packages/protobuf-net/
  [2]: https://github.com/hdinsight/hbase-sdk-for-net
  [hdinsight.hbase.twitter.sentiment.streaming.service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
  [3]: http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/
  [Erste Schritte mit Azure-Websites und ASP.NET]: ../web-sites-dotnet-get-started/
  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Analysieren von Twitter-Daten mit Hadoop in HDInsight]: ../hdinsight-analyze-twitter-data/
  [Analysieren von Daten zu Flugverspätungen mit HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Entwickeln von Java MapReduce-Programmen für HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
