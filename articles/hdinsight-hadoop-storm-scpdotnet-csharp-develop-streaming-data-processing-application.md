<properties 
	pageTitle="Entwickeln von Anwendungen zur Verarbeitung von Datenströmen mit SCP.NET und Storm | Azure" 
	description="Erfahren Sie, wie Sie Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm in HDInsight entwickeln können." 
	services="hdinsight" 
	documentationCenter="" 
	authors="weedqian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2014" 
	ms.author="qixia"/>

#Entwickeln von Anwendungen zur Verarbeitung von Datenströmen in C# mit Stream Computing Platform und Storm in HDInsight

Stream Computing Platform (SCP) ist eine Plattform zum Erstellen zuverlässiger, verteilter, einheitlicher und leistungsstarker Anwendungen für die Datenverarbeitung in Echtzeit mit .NET. Sie basiert auf [Apache Storm](http://storm.incubator.apache.org/), einem Open-Source-System zur Echtzeitverarbeitung, das mit HDInsight verfügbar ist.

In diesem Artikel lernen Sie Folgendes:

* Was SCP ist und wie die Zusammenarbeit mit Storm funktioniert

* Erstellen einer SCP-Lösung

* Testen einer SCP-Lösung

* Bereitstellen einer SCP-Lösung in einem Storm-Cluster auf HDInsight

##Voraussetzungen

* Ein Azure-Abonnement

* Ein Storm-Cluster auf HDInsight

* Visual Studio 2010 oder 2013

##Inhaltsverzeichnis

* [SCP und Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP und Storm

Apache Storm ist ein verteiltes Datenverarbeitungssystem, das in Hadoop-Clustern ausgeführt wird und Ihnen Datenverarbeitung in Echtzeit ermöglicht. Storm wird in der Java Virtual Machine (JVM) ausgeführt und ist so konzipiert, dass Lösungen(als **Topologien** bezeichnet) in einer Vielzahl von Programmiersprachen implementiert werden können. Sie können sogar eine Topologie erstellen, die aus verschiedenen in mehreren Sprachen geschriebenen Komponenten besteht.

SCP bietet die Bibliotheken, die das Erstellen von Storm-Lösungen mithilfe von .NET erleichtern. Storm-Cluster auf HDInsight umfassen die benötigten serverseitigen Komponenten zum Ausführen der von Ihnen erstellten SCP-Lösungen.

Weitere Informationen zu Storm auf HDInsight finden Sie unter [HDInsight Storm: Übersicht](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-storm-overview/).

###Entwerfen einer SCP-Lösung

SCP bietet Schnittstellen, die Ihnen das Erstellen der folgenden Storm-Komponenten ermöglichen:

* Spout - Nutzt Daten aus einer Quelle (Datei, Datenbank, API usw.) und gibt einen oder mehrere Datenströme von Tupeln (geordneten Listen von Elementen) aus
* Bolt - Nutzt einen oder mehrere Datenströme und gibt optional einen oder mehrere Datenströme aus
* Topologie - Definiert den Datenfluss zwischen Spouts und Bolts, die Parallelität von Spouts und Bolts und Konfigurationsinformationen.

> [AZURE.NOTE] Spouts und Bolts implementieren auch eine allgemeine Verarbeitung. Ein Spout kann beispielsweise eine eingehende Nachricht in mehrere Tupel aufteilen oder nur einen Tupel ausgeben und einen Bolt die benötigten Werte extrahieren lassen. Wenn Sie Daten in einen Datenspeicher schreiben müssen, implementieren Sie dies in ähnlicher Weise in einem Bolt.

Das Entwerfen einer Lösung umfasst das Beantworten der folgenden Fragen:

* Welche Datenquelle wollen Sie nutzen? Sie benötigen einen Spout, der dies implementiert.
* Welche Verarbeitung muss erfolgen? Sie müssen dies in den Spouts und/oder Bolts implementieren.
* Wenn die Verarbeitung auf mehrere Bolts aufgeteilt ist, wie ist der Datenfluss dazwischen? Dies müssen Sie mithilfe der Topologie beschreiben.
* Wie wird die Verarbeitung auf die Knoten im HDInsight-Cluster verteilt? Dies müssen Sie mithilfe der Topologie beschreiben.

##Installieren des SCP SDK

Das SCP SDK wird im Storm-Cluster auf HDInsight bereitgestellt. Führen Sie nach dem [Erstellen eines Storm-Clusters auf HDInsight](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-storm-getting-started/) die folgenden Schritte aus, um das SDK in Ihre lokale Entwicklungsumgebung herunterzuladen.

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.

2. Klicken Sie im linken Bereich auf **HDINSIGHT**. Eine Liste bereitgestellter HDInsight-Cluster wird angezeigt.

3. Klicken Sie auf den HDInsight-Cluster, mit dem Sie eine Verbindung herstellen möchten.

4. Klicken Sie oben auf der Seite auf **KONFIGURATION**.

5. Klicken Sie unten auf der Seite auf **REMOTE AKTIVIEREN**.

6. Geben Sie im Assistenten "Remotedesktop konfigurieren" einen **Benutzernamen** und ein **Kennwort** für den Remotedesktop ein. Geben Sie in das Feld **GÜLTIG BIS** ein Ablaufdatum ein, und klicken Sie dann auf das Häkchensymbol.

7. Klicken Sie nach dem Aktivieren des Remotedesktops unten auf der Seite auf **VERBINDEN**, und befolgen Sie die Anweisungen.

8. Öffnen Sie nach dem Herstellen der Verbindung mit dem Cluster über Remotedesktop den **Datei-Explorer**, und geben Sie in die Adressleiste **%storm_home%\examples** ein.

9. Klicken Sie mit der rechten Maustaste auf den Ordner **SDK**, und wählen Sie **Kopieren** aus.

10. Öffnen Sie in Ihrer lokalen Entwicklungsumgebung den **Datei-Explorer**, und navigieren Sie zum Speicherort, an dem Sie das SDK speichern möchten. Klicken Sie mit der rechten Maustaste, und wählen Sie **Einfügen**.

##Erstellen einer SCP-Projektmappe

1. Erstellen Sie in Visual Studio ein neues Projekt für eine **Konsolenanwendung**. Geben Sie dieser Projektmappe den Namen **WordCount**.

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und wählen Sie dann **Verweis hinzufügen** aus.

3. Klicken Sie unten im **Verweis-Manager** auf die Schaltfläche **Durchsuchen**, und wechseln Sie zum Ordner "SDK", den Sie zuvor heruntergeladen haben. Wählen Sie **Microsoft.SCP.dll** und **Microsoft.SCPLogger.dll** aus, und klicken Sie dann auf **Hinzufügen**. Klicken Sie auf **OK**, um das Verweis-Manager-Fenster zu schließen.

###Erstellen des Spouts

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **WordCount**, und wählen Sie dann **Hinzufügen | Neues Element** aus. Wählen Sie **Klasse** aus, und geben Sie **WordSpout.cs** als Namen ein. Klicken Sie abschließend auf **Hinzufügen**.

2. Öffnen Sie die Datei **WordSpout.cs**, und ersetzen Sie den vorhandenen Code durch Folgendes. Lesen Sie unbedingt die Kommentare, um zu verstehen, wie dieser Code funktioniert.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###Erstellen von Tests

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **WordCount**, und wählen Sie dann **Hinzufügen | Neues Element** aus. Wählen Sie **Klasse** aus, und geben Sie **LocalTest.cs** als Namen ein. Klicken Sie abschließend auf **Hinzufügen**.

2. Öffnen Sie die Datei **LocalTest.cs**, und ersetzen Sie den vorhandenen Code durch Folgendes. Der Code zum Testen des Spouts ähnelt sehr dem Code, der zum Testen von Bolts verwendet wird.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. Öffnen Sie die Datei **Program.cs**, und ersetzen Sie den vorhandenen Code durch Folgendes.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. Führen Sie die Anwendung aus. Öffnen Sie anschließend das Verzeichnis **WordCount\bin\debug** unter Ihrem Visual Studio-Projekt. Dort muss sich eine Datei mit dem Namen **spout.txt** befinden, die die Daten enthält, die der Spout während des Tests ausgegeben hat. Der Inhalt sollte in etwa wie der folgende Text aussehen:

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] In den obigen Zeilen ist "Data" die vom Spout ausgegebene Zeichenfolge, die allerdings als Bytearray gespeichert wird. Beispiel: `[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` steht für "an apple a day keeps the doctor away".

###Erstellen des Bolts

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **WordCount**, und wählen Sie dann **Hinzufügen | Neues Element** aus. Wählen Sie **Klasse** aus, und geben Sie **SplitterBolt.cs** als Namen ein. Klicken Sie abschließend auf **Hinzufügen**. Wiederholen Sie diesen Schritt, um eine Klasse namens **CounterBolt.cs** hinzuzufügen.

2. Öffnen Sie die Datei **SplitterBolt.cs**, und ersetzen Sie den vorhandenen Code durch Folgendes.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. Öffnen Sie die Datei **CounterBolt.cs**, und ersetzen Sie den Inhalt durch Folgendes.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] Dieser Bolt gibt einen Datenstrom aus, der für Tests nützlich ist. In der Praxis speichern Sie die Daten am Ende der Verarbeitung in einer Datenbank, Warteschlange oder einem anderen persistenten Speicher.

3. Öffnen Sie **LocalTest.cs**, und fügen Sie die folgenden Tests für "SplitterBolt" und "CounterBolt" hinter dem vorherigen Spout-Testblock ein.

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. Führen Sie die Lösung aus. Nach Abschluss sollten die folgenden Dateien im Verzeichnis **WordCount\bin\debug** enthalten sein.

    * **spout.txt** - Die von WordSpout ausgegebenen Daten
    * **splitter.txt** - Die von SplitterBolt ausgegebenen Daten
    * **counter.txt** - Die von CounterBolt ausgegebenen Daten

###Hinzufügen von Code zur Ausführung in einem Cluster

1. Öffnen Sie **Program.cs**, und ersetzen Sie die Zeile `//Code to run on HDInsight cluster will go here` durch den folgenden Code, und erstellen Sie dann das Projekt neu.

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. Erstellen Sie eine neue Datei mit dem Namen **WordCount.spec**, und verwenden Sie den folgenden Inhalt für die Datei. Dies dient der Definition der Topologie, d. h. der Komponenten, der Anzahl der Instanzen, die auf den Knoten im Cluster erstellt werden, und des Datenflusses dazwischen. Diese Definition erfolgt in der [Topology Specification Language](#spec).

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. Stellen Sie per Remotedesktop eine Verbindung mit Ihrem Storm-Cluster her, und kopieren Sie den Ordner **bin\debug** Ihres lokalen WordCount-Projekts in Ihren Storm-Cluster. Kopieren Sie ihn beispielsweise in den Ordner **%storm_home%\examples**, und benennen Sie ihn in **WordCount** um.

3. Kopieren Sie auch die Datei **WordCount.spec** auf den HDInsight-Server. Legen Sie sie im Verzeichnis **%storm_home%\examples** ab.

4. Klicken Sie für den Storm-Cluster auf dem Desktop auf das Symbol **Storm-Befehlszeile**, um eine Befehlszeile zu öffnen. Geben Sie dann den folgenden Befehl ein, um die Topologie "WordCount" zu starten.

        bin\runspec examples\WordCount.spec temp examples\WordCount

    Hierdurch wird ein neuer Ordner mit der Bezeichnung **temp** erstellt. Verwenden Sie die Spezifikation und die Dateien für die WordCount-Lösung zum Erstellen der Datei **WordCount.jar**, die anschließend an Storm übermittelt wird.

5. Öffnen Sie nach dem Ausführen des Befehls auf dem Desktop die **Storm-Benutzeroberfläche**. Die Topologie **WordCount** sollte aufgeführt sein. Sie können die Topologie in der **Storm-Benutzeroberfläche** auswählen, um Statistiken anzuzeigen.

6. Wählen Sie zum Beenden der Topologie in der **Storm-Benutzeroberfläche** die Option **WordCount** und dann den Befehl **Beenden** im Abschnitt **Topologieaktionen** aus.

##Zusammenfassung

Zuvor haben Sie erfahren, wie Sie eine einfache Topologie zur Wortzählung bereitstellen, die mithilfe der Stream Computing Platform erstellt wurde. Weitere Beispiele finden Sie im Verzeichnis **%storm_home%\examples** Ihres HDInsight-Clusters. Weitere Informationen zu SCP finden Sie in der folgenden Referenz.

##SCP-Referenz

###SCP-Plug-In-Schnittstelle

SCP-Anwendungen (auch Plug-Ins genannt) werden mit der Storm-Pipeline verbunden. Ein Plug-In ist eine .NET-Konsolenanwendung, die eine oder mehrere der folgenden Schnittstellen implementiert.  

-	**ISCPSpout** - dient zum Implementieren eines nicht transaktionalen Spouts.
- 	**ISCPTxSpout** dient zum Implementieren eines transaktionalen Spouts.
-	**ISCPBolt** dient zum Implementieren eines nicht transaktionalen Bolts.
-	**ISCPBatchBolt** dient zum Implementieren eines transaktionalen Bolts.

####ISCPPlugin

ISCPPlugin ist die Basisschnittstelle, von der alle anderen SCP-Schnittstellen Informationen erben. Momentan ist dies noch eine Dummy-Schnittstelle.  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout ist die Schnittstelle für nicht transaktionale Spouts.  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Beim Aufrufen von `NextTuple()` kann der Code ein oder mehrere Tupel ausgeben. Wenn nichts ausgegeben werden kann, sollte diese Methode ohne Ausgabe zurückgegeben werden.

`Ack()` und `Fail()` werden nur aufgerufen, wenn "Ack" in der Spezifikationsdatei aktiviert ist.  `seqId` wird verwendet, um das Tupel zu identifizieren, für das "Ack" bzw. "Fail" aufgerufen wird. Wenn "Ack" in einer nicht transaktionalen Topologie verwendet wird, sollte die folgende Ausgabefunktion im Spout verwendet werden:  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

Wenn "Ack" in einer nicht transaktionalen Topologie nicht unterstützt wird, können `Ack()` und `Fail()` als leere Funktionen definiert werden.  

Der Parameter `parms` für diese Funktionen ist ein leeres Wörterbuchobjekt, das für die zukünftige Verwendung reserviert ist.  

> [AZURE.NOTE] `NextTuple()`, `Ack()` und `Fail()` werden in einer engen Schleife in einem einzelnen Thread aufgerufen. Wenn es keine auszugebenden Tupel gibt, erwägen Sie die Verwendung von `sleep` für einen kurzen Zeitraum (z. B. 10 ms), um CPU-Zyklen zu sparen.

####ISCPTxSpout

ISCPSpout ist die Schnittstelle für transaktionale Spouts.  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()` wird zum Starten einer neuen Transaktion aufgerufen.  `seqId` wird verwendet, um die Transaktion zu identifizieren, die auch in `Ack()` und `Fail()` verwendet wird. Von `NextTx()` ausgegebene Daten werden in ZooKeeper gespeichert, um die Wiedergabe zu unterstützen. Da ZooKeeper eine sehr eingeschränkte Speicherkapazität hat, sollten Sie nur Metadaten und keine großen Datenmengen in transaktionale Spouts ausgeben.  

Storm gibt eine Transaktion im Fehlerfall automatisch erneut wieder. "Fail()" muss daher bei der normalen Verarbeitung nicht aufgerufen werden. Falls SCP jedoch die vom transaktionalen Spout ausgegebenen Metadaten überprüft, kann "Fail()" für ungültige Metadaten aufgerufen werden.

Der Parameter `parms` für diese Funktionen ist ein leeres Wörterbuchobjekt, das für die zukünftige Verwendung reserviert ist.

> [AZURE.NOTE] `NextTx()`, `Ack()` und `Fail()` werden in einer engen Schleife in einem einzelnen Thread aufgerufen. Wenn es keine auszugebenden Tupel gibt, erwägen Sie die Verwendung von `sleep` für einen kurzen Zeitraum (z. B. 10 ms), um CPU-Zyklen zu sparen.

####ISCPBolt

ISCPBolt ist die Schnittstelle für nicht transaktionale Bolts.  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Wenn ein neues Tupel verfügbar ist, wird die Funktion `Execute()` aufgerufen, um es zu verarbeiten.

####ISCPBatchBolt

ISCPBatchBolt ist die Schnittstelle für transaktionale Bolts.  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Wenn ein neues Tupel verfügbar ist, wird die Funktion `Execute()` aufgerufen, um es zu verarbeiten. `FinishBatch()` wird aufgerufen, nachdem diese Transaktion beendet wurde.

Der Parameter  `parms` für diese Funktionen ist ein leeres Wörterbuchobjekt, das für die zukünftige Verwendung reserviert ist.

> [AZURE.NOTE] Bolts, die `ISCPBatchBolt` implementieren, können `StormTxAttempt` von `parms` abrufen. `StormTxAttempt` kann verwendet werden, um herauszufinden, ob ein Tupel das Original oder ein Wiedergabeversuch ist. Dies wird normalerweise im Commit-Bolt ausgeführt und im **HelloWorldTx**-Beispiel demonstriert.  

###Objektmodell

SCP.NET enthält außerdem einen einfachen Satz von Schlüsselobjekten für die Entwicklung.

* `Context` bietet Informationen zur Ausführungsumgebung der Anwendung.

* `StateStore` bietet Metadatendienste, monotone Sequenzgenerierung und Koordinierung ohne Wartezeiten. Parallelitätsabstraktionen auf höheren Ebenen können auf `StateStore` aufgebaut werden, inklusive verteilter Sperren, verteilter Warteschlangen, Schranken und Transaktionsdienste.

* `SCPRuntime` initialisiert die Laufzeitumgebung und startet Plug-Ins beim Ausführen einer Lösung in Storm.

* `LocalContext` bietet Methoden zum Serialisieren und Deserialisieren der ausgegebenen Tupel in lokale Dateien, wenn eine Anwendung lokal in Visual Studio getestet wird.

####Context

Jede ISCPPlugin-Instanz (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) hat eine entsprechende "Context"-Instanz. Die von "Context" bereitgestellten Funktionen sind in zwei Teile unterteilt:

* **Static** - im gesamten C#-Prozess verfügbar
* **Dynamic** - für die jeweilige Instanz verfügbar  

**Static Context**

* `public static ILogger Logger = null;` - zu Protokollierungszwecken bereitgestellt  

* `public static SCPPluginType pluginType;` ruft den Plug-In-Typ des C#-Prozesses ab. Falls der C#-Prozess im lokalen Testmodus (ohne Java) läuft, ist der Plug-In-Typ "SCP_NET_LOCAL".

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` ruft Konfigurationsparameter von der JVM ab. Die Parameter werden von der JVM beim Initialisieren des Plug-Ins übergeben. `Config` enthält zwei Wörterbücher.

    * `public Dictionary<string, Object> stormConf { get; set; }` enthält die von Storm definierten Parameter.

    * `public Dictionary<string, Object> pluginConf { get; set; }` enthält die von der SCP definierten Parameter.

* `public static TopologyContext TopologyContext { get; set; } `  ruft den Topologiekontext ab. Dieses Element ist am nützlichsten für Komponenten mit mehreren Parallelitäten. Nachstehend wird der Zugriff auf den Topologiekontext gezeigt.

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**Dynamic Context**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` deklariert das Ausgabe- und Eingabeschema für Datenströme. Das folgende Beispiel deklariert ein Eingabeschema, das aus einem einzelnen Zeichenfolgentupel besteht, und ein Ausgabeschema, das sowohl aus einem Zeichenfolgen- aus auch einem Ganzzahltupel besteht.

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` gibt ein oder mehrere Tupel an den Standarddatenstrom aus. Das folgende Beispiel gibt zwei Tupel in den Standarddatenstrom aus.

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` gibt ein oder mehrere Tupel an den angegebenen Datenstrom aus. Das folgende Beispiel gibt zwei Tupel an den Datenstrom 'mystream' aus.

        this.ctx.Emit("mystream", new Values(word, count));

Beim Arbeiten mit nicht transaktionalen Spouts und Bolts, bei denen "Ack" aktiviert ist, verwenden Sie Folgendes.

* `public abstract void Emit(string streamId, List<object> values, long seqId);` gibt ein oder mehrere Tupel sowie einen Sequenzbezeichner **aus einem Spout** an den angegebenen Datenstrom aus. Das folgende Beispiel gibt ein Tupel und einen Sequenzbezeichner in den Standarddatenstrom aus.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` gibt ein oder mehrere Tupel sowie einen Sequenzbezeichner **aus einem Bolt** an den angegebenen Datenstrom aus. Die ausgegebenen Tupel sind mit den eingehenden Tupeln verankert, die als die  `anchors` angegeben sind. Dies ermöglicht "Acks" das Zurücklaufen in der Pipeline durch Folgen der Kette ein-/ausgehender Tupel. Das folgende Beispiel gibt ein Tupel und einen Sequenzbezeichner an den Standarddatenstrom aus und verankert die ausgegebenen Tupel mit den in `tuple` enthaltenen eingehenden Tupel.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` führt ein Ack für ein Tupel durch. Ruft die `ISCPSpout.Ack`-Funktion des Spouts auf, der das Tupel ursprünglich erstellt hat.

* `public abstract void Fail(SCPTuple tuple);` lässt ein Tupel fehlschlagen. Ruft die `ISCPSpout.Fail`-Funktion des Spouts auf, der das Tupel ursprünglich erstellt hat.

####StateStore

SCP-Anwendungen können das `State`-Objekt verwenden, um bestimmte Daten in ZooKeeper dauerhaft zu speichern, insbesondere für transaktionale Topologien. Dies ermöglicht transaktionalen Spouts das Abrufen des Status aus ZooKeeper und das Neustarten der Pipeline im Falle eines Absturzes.  

Das `StateStore`-Objekt bietet die folgenden Methoden:

* `public static StateStore Get(string storePath, string connStr);` ruft ein `StateStore` für den angegebenen Pfad und die Verbindungszeichenfolge ab.

* `public State Create();` erstellt ein neues `State`-Objekt in dieser Statusspeicherinstanz.

* `public IEnumerable<State> GetUnCommitted();` ruft alle `State`-Objekte ohne Commit ab, mit Ausnahme von abgebrochenen Status.

* `public IEnumerable<State> States();` ruft alle 'State'-Objekte im `StateStore` ab.


* `public T Get<T>(string info = null);` ruft ein `State`- oder `Registry`-Objekt ab.

    * `info` - der abzurufende `Registry`-Name. Wird nur verwendet, wenn ein `Registry`-Objekt abgerufen wird.

    * `T` -  `State`- oder `Registry`-Typ

* `public IEnumerable<Registry> Commited();` ruft `Registry`-Objekte ab, die `State` mit Commit enthalten.

* `public IEnumerable<Registry> Aborted();` ruft `Registry`-Objekte ab, die abgebrochene `State` enthalten.

* `public State GetState(long stateId)` ruft das `State`-Objekt für die angegebene Status-ID ab.

Das **State**-Objekt bietet die folgenden Methoden.

* `public void Commit(bool simpleMode = true);` legt den Status des `State`-Objekts für den Commit fest.

    > [AZURE.NOTE] Wenn `simpleMode` auf "true" festgelegt ist, wird der entsprechende ZNode in ZooKeeper gelöscht. Andernfalls wird der aktuelle ZNode gelöscht und ein neuer Knoten im `COMMITTED_PATH` hinzugefügt.

* `public void Abort();` legt den Status des abzubrechenden `State`-Objekts fest.

* `public void PutAttribute<T>(string key, T attribute);` legt einen Attributwert für den angegebenen Schlüssel fest.

    * `key` - der für das Attribut festgelegte Schlüssel

    * `attribute` - der Attributwert

* `public T GetAttribute<T>(string key);` ruft den Attributwert für den angegebenen Schlüssel ab.

####SCPRuntime

SCPRuntime stellt die folgenden Methoden bereit.

* `public static void Initialize();` initialisiert die SCP-Laufzeitumgebung. Falls aufgerufen, verbindet sich der C#-Prozess mit der JVM und ruft Konfigurationsparameter und Topologiekontext ab

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` startet die Nachrichtenverarbeitungsschleife. In dieser Schleife empfängt das C#-Plug-In Nachrichten von der JVM (einschließlich Tupel und Steuerungssignale) und verarbeitet anschließend die Nachrichten.

    * `pluginDelegate` - ein Delegat, der ein Objekt zurückgegeben kann, das "ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt" implementiert

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` erhält Tupel aus der Warteschlange.

* `void WriteMsgQueueToFile(string filepath, bool append = false);` speichert Tupel dauerhaft in der Datei.

* `void ReadFromFileToMsgQueue(string filepath);` liest Tupel aus der Datei.


###<a id="spec"></a>Topology Specification Language

Die SCP Topology Specification ist eine domänenspezifische Sprache zum Beschreiben und Konfigurieren von SCP-Topologien. Sie basiert auf der [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html) von Storm.  

Nutzen Sie die folgenden Elemente, um eine Topologie zu definieren.

|Neue Funktionen|	Parameter|	Beschreibung
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 Definiert eine transaktionale Topologie mit dem angegebenen Namen, der Spouts-Definitions-Map und der Bolts-Definitions-Map
|**scp-tx-spout**|	exec-name<br> args<br> fields|	Definiert einen transaktionalen Spout. Die Anwendung wird mit ***exec-name*** mithilfe von ***args*** ausgeführt.<br><br>***fields*** sind die Ausgabefelder für Spout.
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	Definiert einen transaktionalen Stapel-Bolt. Die Anwendung wird mit ***exec-name*** mithilfe von ***args*** ausgeführt.<br><br>"fields" sind die Ausgabefelder für Bolt.
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	Definiert einen transaktionalen Committer-Bolt. Die Anwendung wird mit ***exec-name*** mithilfe von "args" ausgeführt.<br><br>***fields*** sind die Ausgabefelder für Bolt.
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	Definiert eine nicht transaktionale Topologie mit dem Topologienamen,  der Spouts-Definitionsmap und der Bolts-Definitionsmap.
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	Definiert einen nichttransaktionalen Spout. Die Anwendung wird mit ***exec-name*** mithilfe von ***args*** ausgeführt.<br><br>***fields*** sind die Ausgabefelder für Spout.<br><br>***parameters*** ist optional und kann zum Angeben von Parametern wie "nontransactional.ack.enabled" verwendet werden.
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	Definiert einen nichttransaktionalen Bolt. Die Anwendung wird mit ***exec-name*** mithilfe von ***args*** ausgeführt.<br><br>***fields*** sind die Ausgabefelder für Bolt.<br><br>***parameters*** ist optional und kann zum Angeben von Parametern wie "nontransactional.ack.enabled" verwendet werden.

Es folgen Schlüsselwörter, die beim Definieren einer Topologie verwendet werden können.

|Schlüsselwörter|	Beschreibung
|---------|------------
|**:name**|	Definiert den Namen der Topologie
|**:topology**|	Definiert die Topologie mit eingebauten und den oben genannten Funktionen.
|**:p**|	Definiert den Parallelitäts-Hinweis für die einzelnen Spouts bzw. Bolts.
|**:config**|	Definiert Konfigurationsparameter bzw. aktualisiert vorhandene Parameter
|**:schema**|	Definiert das Schema des Streams.

Häufig verwendete Parameter

|Parameter|	Beschreibung
|---------|------------
|**"plugin.name"**|	.exe-Dateiname des C#-Plug-Ins
|**"plugin.args"**|	Plug-In-Argumente
|**"output.schema"**|	Ausgabeschema
|**"nontransactional.ack.enabled"**|	Gibt an, ob Ack in einer nichttransaktionalen Topologie aktiviert ist

Topologiespezifikationen können über den Befehl ***runspec*** direkt an den Storm-Cluster übermittelt werden. Diesen Befehl finden Sie im Verzeichnis **%storm_home%\bin** des Storm-Clusters.  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] Der ***resource-dir***-Parameter ist optional und muss angegeben werden, wenn Sie eine C#-Anwendung ausführen möchten. Das angegebene Verzeichnis enthält die Anwendung sowie deren Abhängigkeiten und Konfigurationen.  

Der ***classpath***-Parameter ist ebenfalls optional. Mit ihm kann der Java-Classpath angegeben werden, wenn die Spezifikationsdatei Java-Spouts oder -Bolts enthält.  

###Verschiedene Features

####Deklarieren von Ein- und Ausgabeschema

Bei Aufrufen von `Emit()` serialisiert die Plattform das Tupel in ein Bytearray, das dann in die JVM übertragen wird. Storm überträgt dieses Tupel an die Ziele. Das Tupel wird von Bolts empfangen. Bei C#-Bolts wird das Tupel von der JVM empfangen und in den ursprünglichen Typ rückkonvertiert.

Um diese Serialisierung und Deserialisierung zu unterstützen, müssen Sie das Schema für Ein- und Ausgaben deklarieren. Diese werden als `Dictionary<string, List<Type>`-Objekte definiert, bei denen der Schlüssel die Datenstrom-ID und der Wert der `Types` der Tupel ist, die ausgegeben werden. Die Komponente kann mehrere Datenströme deklarieren.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

Das `Context`-Objekt bietet das `DeclareComponentSchema`, das zum Deklarieren des Schemas für die Serialisierung/Deserialisierung dient.

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] Sie müssen sicherstellen, dass die ausgegebenen Tupel dem für den Datenstrom definierten Schema folgen. Andernfalls löst das System zur Laufzeit eine Ausnahme aus.  

####Unterstützung mehrerer Datenströme

Sie können eine Ausgabe in mehrere Datenströme bewirken, indem Sie `Emit()` mehrmals aufrufen und den `streamId`-Parameter für jeden Datenstrom angeben, in den geschrieben werden soll.

> [AZURE.NOTE] Ausgaben an nicht vorhandene Datenströme lösen Ausnahmen zur Laufzeit aus.

####Gruppieren von Feldern

Das integrierte Gruppieren von Feldern in Storm funktioniert in SCP.NET nicht ordnungsgemäß. Wenn Daten per Proxyvorgang in die JVM übertragen werden, sind alle Felddatentypen auf `byte[]` festgelegt, und die Feldgruppierung nutzt den Hashcode des  `byte[]`-Objekts zum Durchführen des Gruppierens. Der Hashcode des `byte[]`-Objekts ist die Adresse dieses Objekts im Arbeitsspeicher, weshalb die Gruppierung zweier `byte[]`-Objekte misslingt, die sich zwar denselben Inhalt, aber nicht dieselbe Adresse teilen.

Verwenden Sie in Ihrer Spezifikation `scp-field-group`, um dieses Problem zu umgehen. Dadurch wird der Inhalt von `byte[]` zum Durchführen der Gruppierung verwendet. Es folgt ein Beispiel für die Verwendung in einer Spezifikation.  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

Dieses Beispiel bewirkt Folgendes.

* `scp-field-group` - Gibt an, dass eine benutzerdefinierte Gruppierung verwendet wird.  
* `:tx` oder `:non-tx` - Gibt an, ob dies transaktional oder nicht transaktional ist.
* `[0,1]` - Verwenden Sie ein Hashset mit Feld-IDs, beginnend bei 0.  

####Hybridtopologie

Die meisten nativen Spouts, Bolts und Topologien von Storm sind in Java implementiert. Zum Unterstützen dieser Komponenten in Lösungen, die C#-Komponenten verwenden, lässt SCP das Definieren von Hybridtopologien in der Spezifikation zu.

* **Java-Spout oder -Bolt** - In der Spezifikationsdatei können `scp-spout` und `scp-bolt` auch zum Angeben von Java-Spouts und -Bolts verwendet werden. Nachfolgend wird das Angeben eines Java-Spouts mit dem Klassennamen `microsoft.scp.example.HybridTopology.Generator` gezeigt.

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Java Classpath** - Bei Verwendung von Java-Spouts oder -Bolts müssen Sie zunächst den Spout oder Bolt in einer JAR-Datei kompilieren. Fügen Sie anschließend den Java Classpath mit dem `-cp`-Parameter hinzu, wenn Sie den Befehl **runSpec** verwenden. Das folgende Beispiel umfasst die JAR-Dateien im Verzeichnis **examples\HybridTopology\java\target**.  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Serialisierung und Deserialisierung zwischen Java und C#** - Verwenden Sie zum Serialisieren von Objekten zwischen Java und C# den `CustomizedInteropJSONSerializer`.

    > [AZURE.NOTE] Derzeit unterstützt `CustomizedInteropJSONSerializer` nur **Java-Spouts** und **C#-Bolts**.

    * Angeben des Serialisierungsprogramms für Java-Komponenten in der Spezifikationsdatei

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * Verwenden des Serialisierungsprogramms in Ihren C#-Komponenten

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    Die Standardimplementierung sollte die meisten Fälle verarbeiten können, wenn der Datentyp nicht zu komplex ist.  Wenn der Datentyp zu komplex ist oder die Leistung der Standardimplementierung nicht Ihre Anforderungen erfüllt, können Sie eine benutzerdefinierte Implementierung entsprechend Ihrem Bedarf mithilfe der folgenden Schnittstellen erstellen.

    **Serialisierungsschnittstelle in Java**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **Deserialisierungsschnittstelle in C#**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###SCP-Hostmodus

Der Hostmodus ermöglicht Ihnen das Kompilieren Ihres Projekts in einer DLL und deren Hosting mithilfe von **SCPHost.exe**. Dies ist die Empfehlung für das Hosting Ihrer Lösung in einer Produktionsumgebung. Beim Verwenden des Hostmodus listet die Spezifikationsdatei `SCPHost.exe` als Plug-In auf.

Es folgt ein Beispiel einer Spezifikationsdatei, die den Hostmodus für das Beispiel "HelloWorld" verwendet.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** - die Assembly mit den Spouts und Bolts
* **Scp.App.HelloWorld.Generator** - der Klassenname eines in **HelloWorld.dll** enthaltenen Spouts
* **Get** - die aufzurufende Methode zum Abrufen einer Spout-Instanz


##SCP-Programmierbeispiele

Die folgenden mit SCP geschriebenen Beispielanwendungen finden Sie in Ihrem Storm-Cluster auf HDInsight unter **%storm_home%\examples**.

* **HelloWorld** - HelloWorld ist ein sehr einfaches Beispiel von SCP.Net und ähnelt dem zuvor in diesem Artikel behandelten Wortzählungsbeispiel. Verwendet wird eine nicht transaktionale Topologie mit einem Spout namens **generator** und zwei Bolts namens **splitter** und **counter**. Der Spout **generator** erzeugt zufällige Sätze und gibt diese Sätze an **splitter** aus. Der Bolt **splitter** teilt die Sätze in Wörter auf und gibt diese Wörter an den Bolt **counter** aus. Der Bolt **counter** verwendet ein Wörterbuch, um die Vorkommnisse der einzelnen Wörter zu zählen.

    Für dieses Beispiel gibt es zwei Spezifikationsdateien: **HelloWorld.spec** und **HelloWorld_EnableAck.spec**. Bei Verwendung von **HelloWorld_EnableAck.spec** führt das Beispiel ein Ack für die Tupel aus, die durch die Bolts geleitet werden. **splitter** ist hingegen darauf auslegt, bei einigen Bolts Fehler zu bewirken, um die Fehlerbehandlung in nicht transaktionalen Topologien zu demonstrieren.

* **HelloWorldTx** - ein Beispiel für die Implementierung einer transaktionalen Topologie Sie enthält den transaktionalen Spout **generator**, einen Stapel-Bolt mit Namen **partial-count** und einen Commit-Bolt namens **count-sum**. Es gibt auch drei vorab erstellte TXT-Dateien für die Nutzung mit dieser Topologie. **DataSource0.txt**, **DataSource1.txt** und **DataSource2.txt**.

    Für jede Transaktion wählt der Spout nach dem Zufallsprinzip zwei Dateien aus und gibt die beiden Dateinamen an den Bolt **partial-count** aus. Der Bolt ruft den Dateinamen aus dem empfangenen Tupel ab, öffnet dann die Datei, zählt die Anzahl der Wörter in dieser Datei und übermittelt schließlich die Wortzahl an den Bolt **count-sum**. Der Bolt **count-sum** summiert die Gesamtzahlen.  

    Um das "Exactly-Once"-Prinzip umzusetzen, muss der Bolt **count-sum** beurteilen können, ob eine wiedergegebene Transaktion verarbeitet wird. In diesem Beispiel enthält der Bolt eine statische Membervariable:  

        public static long lastCommittedTxId = -1;  

    Beim Erstellen einer Instanz des Bolts wird der Transaktionsversuch (`txAttempt`) aus den Eingabeparametern gelesen:  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    Beim Aufrufen von `FinishBatch()` wird `lastCommittedTxId` aktualisiert, wenn es sich nicht um eine wiederholt abgespielte Transaktion handelt.  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** - Diese Topologie enthält einen Java-Spout und einen C#-Bolt. Sie verwendet die Standardimplementierungen von SCP für Serialisierung und Deserialisierung. Unter **%storm_home%examples\HybridTopology\HybridTopology.spec** finden Sie Details. Unter **SubmitTopology.bat** erfahren Sie, wie Sie den Java Classpath bei der Übermittlung der Topologie angeben können.  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png
<!--HONumber=42-->
