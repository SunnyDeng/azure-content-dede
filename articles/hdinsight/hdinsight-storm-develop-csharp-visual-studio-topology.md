<properties
   pageTitle="Apache-Storm-Topologien mit Visual Studio und C# | Microsoft Azure"
   description="Erfahren Sie, wie Sie Storm-Topologien in C# erstellen, indem Sie mit den HDInsight Tools für Visual Studio eine einfache Wortanzahl-Topologie in Visual Studio erstellen."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Hadoop-Tools für Visual Studio

Erfahren Sie, wie Sie eine C#-Storm-Topologie mithilfe der HDInsight-Tools für Visual Studio erstellen. In diesem Lernprogramm werden Sie durch die Erstellung eines neuen Storm-Projekts in Visual Studio, durch den zugehörigen lokalen Test und die Bereitstellung eines Apache Storm-Clusters in HDInsight geleitet.

Außerdem erfahren Sie, wie hybride Topologien erstellt werden, die C#- und Java-Komponenten verwenden.

##Voraussetzungen

-	Eine der folgenden Versionen von Visual Studio:

	-	Visual Studio 2012 mit [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

	-	Visual Studio 2013 mit [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) oder [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

	-	[Visual Studio 2015 CTP6](http://visualstudio.com/downloads/visual-studio-2015-ctp-vs)

-	Azure SDK 2.5.1 oder höher

-	HDInsight-Tools für Visual Studio: Informationen zum Installieren und Konfigurieren der HDInsight-Tools für Visual Studio finden Sie unter [Erste Schritte mit den HDInsight-Tools für Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

-	Apache Storm in HDInsight-Cluster: Informationen zum Erstellen eines Clusters finden Sie unter [Erste Schritte mit Apache Storm in HDInsight](hdinsight-storm-getting-started.md).

	> [AZURE.NOTE]Die HDInsight-Tools für Visual Studio unterstützen derzeit nur Storm auf HDInsight-Clustern der Version 3.2.

##Erstellen einer C#-Topologie

1.	Wenn Sie die neueste Version der HDInsight-Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter [Erste Schritte mit den HDInsight-Tools für Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2.	Öffnen Sie Visual Studio, wählen Sie **Datei** > **Neu** aus, und klicken Sie dann auf **Projekt**.

3.	Erweitern Sie im Bildschirm **Neues Projekt** die Option **Installiert** > **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung** aus. Geben Sie unten im Bildschirm **WordCount** als Namen der Anwendung ein.

	![image](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4.	Nachdem das Projekt erstellt wurde, sollten die folgenden Dateien vorliegen:

	-	**Program.cs:** definiert die Topologie für Ihr Projekt. Beachten Sie, dass standardmäßig eine Topologie erstellt wird, die aus einem Spout und einem Bolt besteht.

	-	**Spout.cs:** ein Beispiel-Spout, der Zufallszahlen ausgibt.

	-	**Bolt.cs:** ein Beispiel-Bolt, der eine Anzahl der vom Spout ausgegebenen Zahlen aufnimmt.

	Im Rahmen der Projekterstellung werden die neuesten [SCP.NET-Pakete](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) von NuGet heruntergeladen.

In den nächsten Abschnitten verwandeln Sie dieses Projekt in eine einfache Anwendung zur Wortzählung.

###Implementieren des Spouts

1.	Öffnen Sie **Spout.cs**. Mithilfe der Spouts werden Daten aus einer externen Quelle in eine Topologie eingelesen. Wichtigste Komponenten für einen Spout:

	-	**NextTuple:** wird von Storm aufgerufen, wenn der Spout neue Tupel ausgeben darf.

	-	**Ack** (nur transaktionale Topologie): verarbeitet Bestätigungen, die von anderen Komponenten in der Topologie initiiert wurden, für Tupel, die von diesem Spout gesendet wurden. Durch die Tupelbestätigung erfährt der Spout, dass die Verarbeitung durch nachgelagerte Komponenten erfolgreich ausgeführt wurde.

	-	**Fail** (nur transaktionale Topologie): verarbeitet Tupel, bei denen bei der Verarbeitung anderer Komponenten in der Topologie ein Fehler aufgetreten ist. Dies bietet die Möglichkeit, das Tupel neu auszugeben, damit es erneut verarbeitet werden kann.

2.	Ersetzen Sie den Inhalt der **Spout**-Klasse durch Folgendes. Dadurch wird ein Spout erstellt, der einen Satz willkürlich in die Topologie ausgibt.

	```
	private Context ctx;
	private Random r = new Random();
	string[] sentences = new string[] {
	    "the cow jumped over the moon",
	    "an apple a day keeps the doctor away",
	    "four score and seven years ago",
	    "snow white and the seven dwarfs",
	    "i am at two with nature"
	};


	public Spout(Context ctx)
	{
	    // Set the instance context
	    this.ctx = ctx;


	    Context.Logger.Info("Generator constructor called");


	    // Declare Output schema
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // The schema for the default output stream is
	    // a tuple that contains a string field
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
	}


	// Get an instance of the spout
	public static Spout Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Spout(ctx);
	}


	public void NextTuple(Dictionary<string, Object> parms)
	{
	    Context.Logger.Info("NextTuple enter");
	    // The sentence to be emitted
	    string sentence;


	    // Get a random sentence
	    sentence = sentences[r.Next(0, sentences.Length - 1)];
	    Context.Logger.Info("Emit: {0}", sentence);
	    // Emit it
	    this.ctx.Emit(new Values(sentence));


	    Context.Logger.Info("NextTuple exit");
	}


	public void Ack(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}


	public void Fail(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}
	```

	Lesen Sie die Kommentare in Ruhe durch, um die Funktionsweise dieses Codes zu verstehen.

###Implementieren der Bolts

1.	Löschen Sie die vorhandene Datei **Bolt.cs** aus dem Projekt.

2.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Neues Element** aus. Wählen Sie in der Liste die Option **Storm Bolt** aus, und geben Sie **Splitter.cs** als Namen ein. Wiederholen Sie diesen Vorgang, um einen zweiten Bolt namens **Counter.cs** zu erstellen.

	-	**Splitter.cs:** implementiert einen Bolt, der Sätze in einzelne Wörter unterteilt und einen neuen Datenstrom von Wörtern ausgibt.

	-	**Counter.cs:** implementiert einen Bolt, der jedes Wort zählt und einen neuen Datenstrom von Wörtern sowie die Anzahl der einzelnen Wörter ausgibt.

	> [AZURE.NOTE]Obwohl diese Bolts einfach in Datenströme schreiben und aus diesen lesen, können Sie mithilfe eines Bolts auch mit einer Datenbank oder einem Dienst kommunizieren.

3.	Öffnen Sie **Splitter.cs**. Beachten Sie, dass die Datei standardmäßig nur eine Methode enthält: **Execute**. Diese wird aufgerufen, wenn das Bolt ein zu verarbeitendes Tupel empfängt. Hier können Sie eingehende Tupel lesen und verarbeiten sowie ausgehende Tupel ausgeben.

4.	Ersetzen Sie den Inhalt der **Splitter**-Klasse durch folgenden Code:

	```
	private Context ctx;


	// Constructor
	public Splitter(Context ctx)
	{
	    Context.Logger.Info("Splitter constructor called");
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // Input contains a tuple with a string field (the sentence)
	    inputSchema.Add("default", new List<Type>() { typeof(string) });
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // Outbound contains a tuple with a string field (the word)
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance of the bolt
	public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Splitter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the sentence from the tuple
	    string sentence = tuple.GetString(0);
	    // Split at space characters
	    foreach (string word in sentence.Split(' '))
	    {
	        Context.Logger.Info("Emit: {0}", word);
	        //Emit each word
	        this.ctx.Emit(new Values(word));
	    }


	    Context.Logger.Info("Execute exit");
	}
	```

	Lesen Sie die Kommentare in Ruhe durch, um die Funktionsweise dieses Codes zu verstehen.

5.	Öffnen Sie **Counter.cs**, und ersetzen Sie die Klasseninhalte durch Folgendes:

	```
	private Context ctx;


	// Dictionary for holding words and counts
	private Dictionary<string, int> counts = new Dictionary<string, int>();


	// Constructor
	public Counter(Context ctx)
	{
	    Context.Logger.Info("Counter constructor called");
	    // Set instance context
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string field - the word
	    inputSchema.Add("default", new List<Type>() { typeof(string) });


	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string and integer field - the word and the word count
	    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance
	public static Counter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Counter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the word from the tuple
	    string word = tuple.GetString(0);
	    // Do we already have an entry for the word in the dictionary?
	    // If no, create one with a count of 0
	    int count = counts.ContainsKey(word) ? counts[word] : 0;
	    // Increment the count
	    count++;
	    // Update the count in the dictionary
	    counts[word] = count;


	    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
	    // Emit the word and count information
	    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));


	    Context.Logger.Info("Execute exit");
	}
	```

	Lesen Sie die Kommentare in Ruhe durch, um die Funktionsweise dieses Codes zu verstehen.

###Definieren der Topologie

Spouts und Bolts werden in einem Diagramm angeordnet, das definiert, wie die Daten zwischen den Komponenten verlaufen. Bei dieser Topologie sieht das Diagramm folgendermaßen aus:

![Bild der Anordnung von Komponenten](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Sätze werden vom Spout ausgegeben, die an Instanzen des Splitter-Bolts verteilt werden. Das Splitter-Bolt unterteilt Sätze in Wörter, die an das Counter-Bolt verteilt werden.

Da die Wortanzahl lokal in der Counter-Instanz gespeichert wird, möchten wir sicherstellen, dass bestimmte Wörter an dieselbe Instanz des Counter-Bolts geleitet werden, damit ein bestimmtes Wort nur von einer Instanz verfolgt wird. Für das Splitter-Bolt spielt es keine Rolle, welcher Bolt welchen Satz empfängt, daher werden die Sätze einfach auf diese Instanzen verteilt, um einen Lastenausgleich zu erreichen.

Öffnen Sie die Datei **Program.cs**. Die entscheidende Methode ist **ITopologyBuilder**, mit der die an Storm gesendete Topologie definiert wird. Ersetzen Sie den Inhalt von **ITopologyBuilder** durch Folgendes, um die zuvor beschriebene Topologie zu implementieren.

```
    // Create a new topology named 'WordCount'
    TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

    // Add the spout to the topology.
    // Name the component 'sentences'
    // Name the field that is emitted as 'sentence'
    topologyBuilder.SetSpout(
        "sentences",
        Spout.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
        },
        1);
    // Add the splitter bolt to the topology.
    // Name the component 'splitter'
    // Name the field that is emitted 'word'
    // Use suffleGrouping to distribute incoming tuples
    //   from the 'sentences' spout across instances
    //   of the splitter
    topologyBuilder.SetBolt(
        "splitter",
        Splitter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
        },
        1).shuffleGrouping("sentences");

    // Add the counter bolt to the topology.
    // Name the component 'counter'
    // Name the fields that are emitted 'word' and 'count'
    // Use fieldsGrouping to ensure that tuples are routed
    //   to counter instances based on the contents of field
    //   position 0 (the word). This could also have been
    //   List<string>(){"word"}.
    //   This ensures that the word 'jumped', for example, will always
    //   go to the same instance
    topologyBuilder.SetBolt(
        "counter",
        Counter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
        },
        1).fieldsGrouping("splitter", new List<int>() { 0 });

    // Add topology config
    topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
    {
        {"topology.kryo.register","["[B"]"}
    });

    return topologyBuilder;
```

Lesen Sie die Kommentare in Ruhe durch, um die Funktionsweise dieses Codes zu verstehen.

##Übermitteln der Topologie

1.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Submit to Storm on HDInsight** aus.

	> [AZURE.NOTE]Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen für Ihr Azure-Abonnement ein. Wenn Sie über mehrere Abonnements verfügen, melden Sie sich bei dem Abonnement an, das Ihren Storm-Cluster in HDInsight enthält.

2.	Wählen Sie in der Dropdownliste **Storm Cluster** Ihren Storm-Cluster in HDInsight und dann die Option **Übermitteln** aus. Sie können über das Fenster **Ausgabe** überwachen, ob die Übermittlung erfolgreich ausgeführt wurde.

3.	Sobald die Topologie erfolgreich übermittelt wurde, sollten die **Storm-Topologien** für den Cluster angezeigt werden. Wählen Sie die **WordCount**-Topologie in der Liste aus, um Informationen zur aktiven Topologie anzuzeigen.

	> [AZURE.NOTE]Sie können die **Storm-Topologien** auch im **Server-Explorer** anzeigen, indem Sie **Azure** > **HDInsight** erweitern und dann mit der rechten Maustaste auf einen Storm-Cluster in HDInsight klicken und **Storm-Topologien anzeigen** auswählen.

	Verwenden Sie die Links für die Spouts oder Bolts, um Informationen über diese Komponenten anzuzeigen. Für jedes ausgewählte Element wird ein neues Fenster geöffnet.

4.	Wählen Sie in der Ansicht **Topologiezusammenfassung** die Option **Beenden** aus, um die Topologie zu beenden.

	> [AZURE.NOTE]Storm-Topologien werden weiterhin ausgeführt, bis sie deaktiviert werden oder der Cluster gelöscht wird.

##Transaktionale Topologie

Die vorherige Topologie ist nicht transaktional. Die Komponenten in der Topologie implementieren keine Funktionen für die Wiedergabe von Nachrichten, wenn bei der Verarbeitung durch eine Komponente in der Topologie ein Fehler auftritt. Erstellen Sie für eine transaktionale Beispieltopologie ein neues Projekt, und wählen Sie **Storm-Beispiel** als Projekttyp aus.

Transaktionale Topologien implementieren Folgendes, um die Wiedergabe von Daten zu unterstützen:

-	**Metadaten-Zwischenspeicherung:** Der Spout muss Metadaten zu den ausgegebenen Daten speichern, damit die Daten bei einem auftretenden Fehler erneut abgerufen und ausgegeben werden können. Da die in diesem Beispiel ausgegebene Datenmenge klein ist, werden die Rohdaten der einzelnen Tupel zur Wiedergabe in einem Wörterbuch gespeichert.

-	**Ack:** Jeder Bolt in der Topologie kann `this.ctx.Ack(tuple)` aufrufen, um die erfolgreiche Verarbeitung eines Tupels zu bestätigen. Nachdem das Tupel von allen Bolts bestätigt wurde, wird die `Ack`-Methode des Spouts aufgerufen. Dadurch kann der Spout zur Wiedergabe zwischengespeicherte Daten entfernen, da die Daten vollständig verarbeitet wurden.

-	**Fail:** Jeder Bolt kann `this.ctx.Fail(tuple)` aufrufen, um anzuzeigen, dass bei der Verarbeitung eines Tupels ein Fehler aufgetreten ist. Der Fehler wird an die `Fail`-Methode des Spouts weitergegeben, wo das Tupel mithilfe der zwischengespeicherten Daten wiedergegeben werden kann.

-	**Sequence ID:** Beim Ausgeben eines Tupels kann eine Sequenz-ID angegeben werden. Hierbei sollte es sich um einen Wert handeln, der das Tupel für die Wiedergabeverarbeitung ("Ack" und "Fail") kennzeichnet. Der Spout im Projekt **Storm-Beispiel** verwendet z. B. Folgendes beim Ausgeben von Daten:

	```
	this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
	```

	Hierbei wird ein neues Tupel, das einen Satz enthält, im Standarddatenstrom ausgegeben, wobei der Sequenz-ID-Wert in **lastSeqId** enthalten ist. In diesem Beispiel wird **lastSeqId** für jedes ausgegebene Tupel einfach erhöht.

Wie im Projekt **Storm-Beispiel** veranschaulicht, kann zur Laufzeit auf Basis der Konfiguration angegeben werden, ob es sich um eine transaktionale Komponente handelt.

##Hybride Topologie

HDInsight-Tools für Visual Studio können auch zum Erstellen von hybriden Topologien verwendet werden, bei denen einige Komponenten aus C#- und andere aus Java-Code bestehen.

Erstellen Sie für eine hybride Beispieltopologie z. B. ein neues Projekt, und wählen Sie **Storm-Hybridbeispiel** aus. Dadurch wird eine vollständig kommentierte Beispieldatei mit mehreren Topologien erstellt, die Folgendes veranschaulichen:

-	**Java-Spout** und **C#-Bolt**: Definition in **HybridTopology_javaSpout_csharpBolt**

	-	Eine transaktionale Version ist in **HybridTopologyTx_javaSpout_csharpBolt** definiert.

-	**C#-Spout** und **Java-Bolt**: Definition in **HybridTopology_csharpSpout_javaBolt**

	-	Eine transaktionale Version ist in **HybridTopologyTx_csharpSpout_javaBolt** definiert.

		> [AZURE.NOTE]Diese Version veranschaulicht zudem, wie Sie Clojure-Code aus einer Textdatei als Java-Komponente verwenden.

Um zwischen den Topologien zu wechseln, die beim Übermitteln des Projekts verwendet werden, verschieben Sie einfach vor der Übermittlung an den Cluster die `[Active(true)]`-Anweisung in die Topologie, die Sie verwenden möchten.

> [AZURE.NOTE]Alle erforderliche Java-Dateien werden im Rahmen dieses Projekts im Ordner **JavaDependency** bereitgestellt.

Bedenken Sie beim Erstellen und Übermitteln einer hybriden Topologie Folgendes:

-	**JavaComponentConstructor** muss verwendet werden, um eine neue Instanz der Java-Klasse für einen Spout oder Bolt zu erstellen.

-	**microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** muss zum Serialisieren von Daten in bzw. aus Java-Komponenten von Java-Objekten zu JSON verwendet werden.

-	Beim Übermitteln der Topologie an den Server müssen Sie die Option **Zusätzliche Konfigurationen** verwenden, um die **Java-Dateipfade** anzugeben. Der angegebene Pfad muss sich in dem Verzeichnis befinden, das die JAR-Dateien mit den Java-Klassen enthält.

###Azure Event Hubs

SCP.Net Version 0.9.4.203 bietet eine neue Klasse und Methode speziell für die Arbeit mit dem Event Hub-Spout (ein Java-Spout, der von Event Hub liest). Verwenden Sie die folgenden Methoden beim Erstellen einer Topologie, die diesen Spout verwendet:

-	**EventHubSpoutConfig**-Klasse: erstellt ein Objekt, das die Konfiguration für die Spout-Komponente enthält

-	**TopologyBuilder.SetEventHubSpout**-Methode: fügt die Event Hub-Spout-Komponente der Topologie hinzu

> [AZURE.NOTE]Obwohl hierdurch die Arbeit mit dem Event Hub-Spout gegenüber anderen Java-Komponenten erleichtert wird, müssen Sie dennoch "CustomizedInteropJSONSerializer" zum Serialisieren der von dem Spout erzeugten Daten verwenden.

##Problembehandlung

###Aktualisieren von SCP.Net-Komponenten

Wenn Sie erstmalig eine C#-Storm-Topologie erstellen, wird die neueste Version von SCP.Net installiert. Allerdings müssen Sie manuelle Schritte zur Aktualisierung vorhandener Projekte auf die neueste Version ausführen.

1.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.	Wenn der Paket-Manager angezeigt wird, verwenden Sie das Suchfeld zum Suchen und Installieren der neuesten Version von SCP.Net.

	> [AZURE.IMPORTANT]Nach Abschluss der Installation können Sie die aktualisierte Version von SCP.Net in Ihrer Topologie verwenden. Möglicherweise erhalten Sie jedoch Fehlermeldungen bei der Bereitstellung der Topologie in einem HDInsight-Cluster. Grund hierfür ist der Umstand, dass die während der Bereitstellung verwendete Version ebenfalls aktualisiert werden muss.

3.	Wechseln Sie nach der Installation zu dem Verzeichnis, das Ihre Projektmappe enthält, und öffnen Sie das Verzeichnis **packages**. Dort sollte ein Unterverzeichnis mit dem Namen **Microsoft.SCP.Net.SDK.#.#.#.###** vorhanden sein, wobei die "#"-Zeichen die Versionsnummer darstellen.

4.	Öffnen Sie das Verzeichnis **Microsoft.SCP.Net.SDK.#.#.#.###**, und kopieren Sie den Inhalt.

5.	Öffnen Sie in dem Verzeichnis, das die Projektmappe enthält, das Verzeichnis mit dem C#-Storm-Topologieprojekt, und suchen Sie den Ordner **Microsoft.SCP.Net.SDK**. Dieser Ordner enthält die SCP.Net-Komponenten, die zum Verpacken und Bereitstellen Ihrer Anwendung auf dem HDInsight-Cluster verwendet werden.

6.	Löschen Sie den vorhandenen Inhalt des Ordners **Microsoft.SCP.Net.SDK**, und ersetzen Sie den Inhalt durch die Version aus **packages/Microsoft.SCP.Net.SDK.#.#.#.###**.

Ihr Projekt wurde jetzt auf die von NuGet installierte Version aktualisiert, die sowohl für lokale Entwicklungen als auch für die Bereitstellung auf dem HDInsight-Cluster verwendet wird.

###Lokales Testen einer Topologie

Obwohl es relativ einfach ist, eine Topologie in einem Cluster bereitzustellen, müssen Sie eine Topologie möglicherweise auch lokal testen. Gehen Sie wie folgt vor, um die Beispieltopologie in diesem Lernprogramm lokal in Ihrer Entwicklungsumgebung auszuführen und zu testen.

> [AZURE.WARNING]Lokale Tests funktionieren nur für einfache, ausschließlich in C# umgesetzte Topologien. Sie sollten lokale Tests nicht für hybride Topologien verwenden oder für Topologien, in denen mehrere Streams verwendt werden, da sonst Fehlermeldungen auftreten.

1.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus. Wählen Sie in den Projekteigenschaften für **Ausgabetyp** die Option **Konsolenanwendung** aus.

	![Ausgabetyp](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

	> [AZURE.NOTE]Denken Sie daran, für den **Ausgabetyp** später wieder **Klassenbibliothek** festzulegen, bevor die Topologie in einem Cluster bereitgestellt wird.

2.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Hinzufügen** > **Neues Element** aus. Wählen Sie **Klasse** aus, und geben Sie **LocalTest.cs** als Klassennamen ein. Klicken Sie abschließend auf **Hinzufügen**.

3.	Öffnen Sie die Datei **LocalTest.cs**, und fügen Sie die folgende **using**-Anweisung am Anfang hinzu:

	```
	using Microsoft.SCP;
	```

4.	Fügen Sie den folgenden Inhalt in die **LocalTest**-Klasse ein.

	```
	// Drives the topology components
	public void RunTestCase()
	{
	    // An empty dictionary for use when creating components
	    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();


	    #region Test the spout
	    {
	        Console.WriteLine("Starting spout");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext spoutCtx = LocalContext.Get();
	        // Get a new instance of the spout, using the local context
	        Spout sentences = Spout.Get(spoutCtx, emptyDictionary);


	        // Emit 10 tuples
	        for (int i = 0; i < 10; i++)
	        {
	            sentences.NextTuple(emptyDictionary);
	        }
	        // Use LocalContext to persist the data stream to file
	        spoutCtx.WriteMsgQueueToFile("sentences.txt");
	        Console.WriteLine("Spout finished");
	    }
	    #endregion


	    #region Test the splitter bolt
	    {
	        Console.WriteLine("Starting splitter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext splitterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


	        // Set the data stream to the data created by the spout
	        splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            splitter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        splitterCtx.WriteMsgQueueToFile("splitter.txt");
	        Console.WriteLine("Splitter bolt finished");
	    }
	    #endregion


	    #region Test the counter bolt
	    {
	        Console.WriteLine("Starting counter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext counterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Counter counter = Counter.Get(counterCtx, emptyDictionary);


	        // Set the data stream to the data created by splitter bolt
	        counterCtx.ReadFromFileToMsgQueue("splitter.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            counter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        counterCtx.WriteMsgQueueToFile("counter.txt");
	        Console.WriteLine("Counter bolt finished");
	    }
	    #endregion
	}
	```

	Nehmen Sie sich einen Moment Zeit, um die Codekommentare zu lesen. Dieser Code verwendet **LocalContext**, um die Komponenten in der Entwicklungsumgebung auszuführen, wobei der Datenstrom zwischen den Komponenten in Textdateien auf dem lokalen Laufwerk bestehen bleibt.

5.	Öffnen Sie die Datei **Program.cs**, und fügen Sie der Methode **Main** Folgendes hinzu:

	```
	Console.WriteLine("Starting tests");
	System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
	// Initialize the runtime
	SCPRuntime.Initialize();


	//If we are not running under the local context, throw an error
	if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
	{
	    throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
	}
	// Create test instance
	LocalTest tests = new LocalTest();
	// Run tests
	tests.RunTestCase();
	Console.WriteLine("Tests finished");
	Console.ReadKey();
	```

6.	Speichern Sie die Änderungen, und verwenden Sie dann **F5** oder **Debuggen** > **Debugging starten**, um das Projekt zu starten. Daraufhin sollte ein Konsolenfenster angezeigt und mit fortschreitendem Test der Status protokolliert werden. Nachdem **Tests abgeschlossen** angezeigt wurde, drücken Sie eine beliebige Taste, um das Fenster zu schließen.

7.	Suchen Sie mithilfe von **Windows Explorer** das Verzeichnis, in dem Ihr Projekt gespeichert ist, z. B. **C:\\Users\\<Benutzername>\\Documents\\Visual Studio 2013\\Projects\\WordCount\\WordCount**. Öffnen Sie in diesem Verzeichnis das Unterverzeichnis **Bin**, und klicken Sie dann auf **Debug**. Es sollten die beim Ausführen der Tests erstellten Textdateien vorhanden sein: "sentences.txt", "counter.txt" und "splitter.txt". Öffnen Sie die einzelnen Textdateien, und überprüfen Sie die Daten.

	> [AZURE.NOTE]Zeichenfolgendaten werden in diesen Dateien als Array von Dezimalwerten beibehalten. Beispiel: [[97,103,111]] in der Datei **splitter.txt** entspricht dem Wort "and".

Während das lokale Testen einer einfachen Anwendung zur Wortzählung relativ einfach erscheint, zeigt sich der wahre Wert erst bei komplexen Topologien, die mit externen Datenquellen kommunizieren oder komplexe Datenanalysen durchführen. Wenn Sie an einem solchen Projekt arbeiten, müssen Sie möglicherweise Haltepunkte festlegen und den Code in Ihren Komponenten schrittweise durchlaufen, um Problembereiche zu isolieren.

> [AZURE.NOTE]Vergewissern Sie sich, dass Sie für den **Projekttyp** vor der Bereitstellung in einem Storm-Cluster in HDInsight wieder **Klassenbibliothek** festlegen.

###Protokollieren von Informationen

Sie können Informationen aus den Topologiekomponenten problemlos mithilfe von `Context.Logger` protokollieren. Das folgende Beispiel erzeugt z. B. einen informativen Protokolleintrag:

```
Context.Logger.Info("Component started");
```

Protokollierte Informationen können über das **Hadoop-Dienstprotokoll** angezeigt werden, das sich in **Server-Explorer** befindet. Erweitern Sie den Eintrag für Ihren Storm-Cluster in HDInsight, und erweitern Sie dann das **Hadoop-Dienstprotokoll**. Wählen Sie abschließend die anzuzeigende Protokolldatei aus.

> [AZURE.NOTE]Die Protokolle werden in dem Azure-Speicherkonto gespeichert, das von Ihrem Cluster verwendet wird. Wenn es sich hierbei um ein anderes Abonnement als das handelt, mit dem Sie sich bei Visual Studio angemeldet haben, müssen Sie sich bei dem Abonnement anmelden, das dieses Speicherkonto enthält, um die entsprechenden Informationen anzeigen zu können.

###Anzeigen von Fehlerinformationen

Gehen Sie wie folgt vor, um die Fehler anzuzeigen, die in einer aktiven Topologie aufgetreten sind:

1.	Klicken Sie in **Server-Explorer** mit der rechten Maustaste auf den Storm-Cluster in HDInsight, und wählen Sie **Storm-Topologien anzeigen** aus.

2.	Für die **Spouts** und **Bolts** enthält die Spalte **Letzter Fehler** die Informationen zum zuletzt aufgetretenen Fehler.

3.	Wählen Sie die **Spout-ID** oder **Bolt-ID** für die Komponente aus, für die ein Fehler aufgeführt ist. Auf der angezeigten Detailseite werden am unteren Rand zusätzliche Fehlerinformationen im Abschnitt **Fehler** aufgeführt.

4.	Um weitere Informationen zu erhalten, wählen Sie einen **Port** im Abschnitt **Ausführer** aus, um das Storm-Arbeitsprotokoll für die letzten Minuten anzuzeigen.

##Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Storm-Topologien mithilfe der HDInsight-Tools für Visual Studio entwickeln und bereitstellen, lesen Sie jetzt Informationen zum [Verarbeiten von Ereignissen aus Azure Event Hub mit Storm in HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Ein Beispiel einer C#-Topologie, die Streamdaten in mehrere Datenströme aufteilt, finden Sie unter [C# Storm example](https://github.com/Blackmist/csharp-storm-example) (in englischer Sprache).

Weitere Informationen zum Erstellen von C#-Topologien finden Sie unter [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Weitere Möglichkeiten zum Arbeiten mit HDInsight sowie weitere Beispiele für Storm in HDInsight finden Sie in folgenden Themen:

**Apache Storm in HDInsight**

-	[Bereitstellen und Überwachen von Topologien mit Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology.md)

-	[Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop in HDInsight**

-	[Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

-	[Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

-	[Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase in HDInsight**

-	[Erste Schritte mit HBase in HDInsight](../hdinsight-hbase-get-started.md)

<!---HONumber=July15_HO2-->