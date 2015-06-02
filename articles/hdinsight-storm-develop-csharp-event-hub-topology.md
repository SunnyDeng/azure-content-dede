<properties
   pageTitle="Verarbeiten von Ereignissen aus Event Hubs mit Storm in HDInsight | Azure"
   description="Erfahren Sie, wie Sie Event Hubs-Daten mit einer C#-Storm-Topologie verarbeiten, die mithilfe der HDInsight-Tools für Visual Studio in Visual Studio erstellt wurde."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="larryfr"/>

# Verarbeiten von Ereignissen von Azure Event Hubs mit Storm auf HDInsight (C#)

Azure Event Hubs ermöglichen die Verarbeitung sehr großer Datenmengen von Websites, Apps und Geräten. Der Event Hubs-Spout vereinfacht die Verwendung von Apache Storm in HDInsight, um diese Daten in Echtzeit zu analysieren. Sie können auch Daten aus Storm mithilfe des Event Hubs-Bolts in Event Hubs schreiben.

In diesem Lernprogramm erfahren Sie, wie Sie die HDInsight-Tools für Visual Studio sowie Event Hubs-Spouts und -Bolts verwenden, um zwei hybride C#-/Java-Topologien zu erstellen:

* **EventHubWriter**: generiert Daten nach dem Zufallsprinzip und schreibt sie in Event Hubs.

* **EventHubReader**: liest Daten aus Event Hubs und speichert sie im Azure-Tabellenspeicher.

## Voraussetzungen

* Ein <a href="../hdinsight-storm-getting-started/" target="_blank">Apache Storm in HDInsight-Cluster</a>

* Ein <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Azure Event Hub</a>

* Das <a href="http://azure.microsoft.com/downloads/" target="_blank">Azure .NET SDK</a>

* Die <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight-Tools für Visual Studio</a> (Version vom 17.02.2015 oder neuer)

## Abgeschlossenes Projekt

Sie können eine vollständige Version des Projekts von GitHub herunterladen, das in diesem Lernprogramm erstellt wurde: [eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid) (in englischer Sprache). Sie müssen jedoch weiterhin Konfigurationseinstellungen angeben, indem Sie die Schritte in diesem Lernprogramm durchführen.

> [AZURE.NOTE]Wenn Sie das abgeschlossene Projekt verwenden, müssen Sie die für diese Lösung erforderlichen Pakete mit dem **NuGet-Paket-Manager** wiederherstellen.

## Event Hubs-Spout und -Bolt

Event Hubs-Spout und -Bolt sind Java-Komponenten, die Ihnen die Arbeit mit Event Hubs aus Apache Storm vereinfachen. Während diese Komponenten in Java geschrieben sind, ermöglichen die HDInsight-Tools für Visual Studio das Erstellen von hybriden Topologien, in denen C#- und Java-Komponenten gemeinsam verwendet werden.

Spout und Bolt werden als einzelne Java-Archivdatei (JAR) mit dem Namen **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** bereitgestellt.

### Herunterladen der JAR-Datei

Die neueste Version der Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ist im Projekt <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a> im Ordner **lib** enthalten. Laden Sie die Datei mithilfe einer der folgenden Methoden herunter.

> [AZURE.NOTE]Spout und Bolt wurden zur Verwendung im Apache Storm-Projekt übermittelt. Weitere Informationen finden Sie unter <a href="https://github.com/apache/storm/pull/336/files">STORM-583: Initial check-in for storm-event hubs</a> (in englischer Sprache) in GitHub.

* **Herunterladen einer ZIP-Datei**: Wählen Sie auf der Website <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a> (in englischer Sprache) auf der rechten Seite **Download ZIP** aus, um eine ZIP-Datei mit dem Projekt herunterzuladen.

	![Schaltfläche "Download ZIP"](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Nach dem Herunterladen können Sie das Archiv extrahieren – die Datei befindet sich im Verzeichnis **lib**.

* **Klonen des Projekts**: Wenn <a href="http://git-scm.com/" target="_blank">Git</a> installiert ist, verwenden Sie folgenden Befehl, um das Repository lokal zu klonen – die Datei befindet sich anschließend im Verzeichnis **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Konfigurieren des Ereignis-Hubs

Event Hubs sind die Datenquelle für dieses Beispiel. Führen Sie die folgenden Schritte aus, um ein neues Ereignis-Hub zu erstellen.

1. Klicken Sie im [Azure-Portal](https://manage.windowsazure.com) auf **NEU** > **Servicebus** > **Event Hub** > **Benutzerdefiniert erstellen**.

2. Geben Sie auf dem Bildschirm **Neues Event Hub hinzufügen** einen **Event Hub-Namen** ein, wählen Sie die **Region** aus, in der das Hub erstellt werden soll, und erstellen Sie einen neuen Namespace, oder wählen Sie einen vorhandenen Namespace aus. Klicken Sie auf den **Pfeil**, um fortzufahren.

	![Seite 1 des Assistenten](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE]Wählen Sie den gleichen **Speicherort** wie für Ihren Storm auf HDInsight-Server, um Latenz und Kosten zu verringern.

2. Füllen Sie auf dem Bildschirm **Event Hub konfigurieren** die Felder **Anzahl der Partitionen** und **Aufbewahrung der Nachrichten** aus. Verwenden Sie für dieses Beispiel eine Anzahl von 10 Partitionen und eine Nachrichtenaufbewahrung von 1. Notieren Sie sich die Partitionsanzahl, da Sie diesen Wert später benötigen werden.

	![Seite 2 des Assistenten](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Nachdem das Event Hub erstellt wurde, wählen Sie den Namespace, wählen Sie **Event Hubs**, und wählen Sie dann das Event Hub, das Sie zuvor erstellt haben.

4. Klicken Sie auf **Konfigurieren**, und erstellen Sie zwei Zugriffsrichtlinien mit den folgenden Informationen.

	<table>
<tr><th>Name</th><th>Berechtigungen</th></tr>
<tr><td>Writer</td><td>Senden</td></tr>
<tr><td>Leser</td><td>Abhören</td></tr>
</table>Klicken Sie nach der Erstellung der Berechtigungen auf das **Speichern**-Symbol am unteren Seitenrand. Auf diese Weise werden die Richtlinien für den gemeinsamen Zugriff erstellt, die zum Senden (writer) und Empfangen (reader) von Daten an bzw. von diesem Event Hub verwendet werden.

	![Richtlinien](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Verwenden Sie nach dem Speichern der Richtlinien den **Generator für freigegebene Zugriffsschlüssel** am unteren Seitenrand, um den Schlüssel für die Richtlinien **writer** und **reader** abzurufen. Speichern Sie diese Schlüssel für den späteren Gebrauch.

## Konfigurieren des Tabellenspeichers

Im Tabellenspeicher werden aus Event Hubs abgerufene Werte gespeichert, und Sie können den Tabellenspeicher einfach über den **Server-Explorer** in Visual Studio anzeigen. Gehen Sie wie folgt vor, um einen neuen Tabellenspeicher zu erstellen:

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU** > **Data Services** > **Storage** > **Schnellerfassung**.

	![Schnelle Speichererstellung](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Geben Sie einen **Namen** für das Speicherkonto ein, wählen Sie einen **Speicherort**, und klicken Sie auf das **Häkchen**, um das Speicherkonto zu erstellen.

	> [AZURE.NOTE]Wählen Sie den gleichen **Speicherort** wie für Ihren Event Hub und Storm auf HDInsight-Server, um Latenz und Kosten zu verringern.

3. Sobald das neue Speicherkonto bereitgestellt ist, wählen Sie das Konto aus, und verwenden Sie anschließend den Link **Zugriffsschlüssel verwalten** am unteren Seitenrand, um den **Speicherkontonamen ** und den **Primären Zugriffsschlüssel ** abzurufen. Speichern Sie diese Informationen, da sie später benötigt werden.

	![Zugriffsschlüssel](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Erstellen von EventHubWriter

In diesem Abschnitt erstellen Sie eine Topologie, die Daten mithilfe des Event Hub-Bolts in Event Hubs schreibt.

1. Wenn Sie die neueste Version der HDInsight-Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Erste Schritte mit den HDInsight-Tools für Visual Studio</a>.

2. Öffnen Sie Visual Studio, wählen Sie **Datei** > **Neu** und dann **Projekt**.

3. Erweitern Sie auf dem Bildschirm **Neues Projekt** die Option **Installiert**, **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung** aus. Geben Sie auf dem Bildschirm unten **EventHubWriter** als Namen der Anwendung ein.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Nachdem das Projekt erstellt wurde, sollten die folgenden Dateien vorliegen:

	* **Program.cs**: definiert die Topologie für Ihr Projekt. Beachten Sie, dass standardmäßig eine Topologie erstellt wird, die aus einem Spout und einem Bolt besteht.

	* **Spout.cs**: ein Beispiel-Spout.

	* **Bolt.cs**: ein Beispiel-Bolt. Dies wird gelöscht, da Sie den Event Hubs-Bolt verwenden, um in das Event Hub zu schreiben.

### Konfiguration

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **EventHubWriter**, und wählen Sie **Eigenschaften**.

2. Wählen Sie in den Projekteigenschaften die Option **Einstellungen** und anschließend die Option **Dieses Projekt enthält keine Standardeinstellungsdatei. Klicken Sie hier, um eine zu erstellen.**

3. Geben Sie folgende Einstellungen ein. Verwenden Sie die Informationen für das Event Hub, das Sie zuvor in der Spalte **Wert** erstellt haben.

	<table>
<tr><th style="text-align:left">Name</th><th style="text-align:left">Typ</th><th style="text-align:left">Bereich</th></tr>
<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Anwendung</td></tr>
</table>

4. Speichern Sie Ihre Änderungen, und schließen Sie die Seite **Eigenschaften**.

### Definieren der Topologie

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Bolt.cs**, und wählen Sie **Löschen**. Da Sie den Java-Event Hubs-Bolt verwenden, benötigen Sie diese Datei nicht.

2. Öffnen Sie die Datei **Program.cs**, und fügen Sie direkt unterhalb der Zeile `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` den folgenden Code ein.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	Die erste Zeile liest die Anzahl der Partitionen aus den zuvor definierten Eigenschaften. Die zweite Zeile definiert ein Deserialisierungsprogramm, mit dem die durch den Spout erzeugten JSON-Daten in eine `java.lang.String`-Zeichenfolge deserialisiert werden, sodass die Java-Komponenten die Daten nutzen können.

4. Suchen Sie den folgenden Code:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Ersetzen Sie ihn durch Folgendes:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Dieser Code erstellt einen Spout und verwendet die Event Hubs-Partitionsanzahl als Parallelitätshinweis für diese Komponente. Damit wird eine Instanz des Spouts für jede Partition erstellt.

	Außerdem wird dadurch der zuvor erstellte Deserialisierer dem Ausgabedatenstrom dieser Komponente zugeordnet. Dadurch kann die Downstream-EventHubSpout-Komponente die durch den C#-Spout erzeugten Daten nutzen.

5. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu.

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName,
			"true"));

	Dieser Code erstellt einen neuen Konstruktor für den Java-Bolt, mit dem zur Laufzeit eine neue Instanz des Bolts konfiguriert wird. In diesem Fall verwenden Sie die <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>, um den Spout mit den zuvor hinzugefügten Event Hubs-Konfigurationsinformationen zu konfigurieren. Genauer gesagt wird dieser Code von HDInsight zur Laufzeit für Folgendes verwendet:

	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.bolt.EventHubBoltConfig** mithilfe der von Ihnen bereitgestellten Event Hubs-Informationen.
	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.bolt.EventHubBolt** und Übergabe der **EventHubBoltConfig**-Instanz.

6. Suchen Sie den folgenden Code:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Ersetzen Sie ihn durch Folgendes:

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Dieser Code weist die Topologie an, den **JavaComponentConstructor** aus dem vorherigen Schritt als Bolt zu verwenden. Auf die Komponente kann in dieser Topologie mit dem Anzeigenamen "EventHubBolt" verwiesen werden. Der Parallelitätshinweis ist auf die Anzahl der Partitionen für den Event Hub festgelegt, und die Komponente abonniert Daten, die durch den Spout ("Spout") erzeugt werden.

An dieser Stelle ist die Arbeit mit **Program.cs** abgeschlossen. Die Topologie wurde definiert, nun müssen Sie **Spout.cs** so ändern, dass Daten in einem Format erzeugt werden, das der Event Hubs-Bolt verwenden kann.

> [AZURE.NOTE]Diese Topologie erstellt standardmäßig einen Arbeitsprozess, der zu Beispielzwecken ausreicht. Bei der Übernahme für einen Produktionscluster sollten Sie Folgendes hinzufügen und an die Anzahl von Arbeitsthreads anpassen, die Sie erstellen möchten.
>
> ```topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
                {
                    {"topology.workers", "1"}  //Change to set the number of workers to create
                });```

### Ändern des Spouts

Der Event Hubs-Bolt erwartet einen einzelnen Zeichenfolgenwert, den er an Event Hub weiterleitet. Im folgenden Beispiel ändern Sie die Standarddatei **Spout.cs**, um eine JSON-Zeichenfolge zu erzeugen.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubWriter**, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie nach dem **Json.NET**-Paket, und fügen Sie es der Projektmappe hinzu. So lassen sich ganz einfach JSON-Daten erstellen, die mithilfe des Bolts an Event Hubs gesendet werden.

1. Öffnen Sie **Spout.cs**, und fügen Sie am Anfang der Datei folgenden Code ein.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Dies vereinfacht die Arbeit mit JSON-Daten.

3. Suchen Sie den folgenden Code:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Ersetzen Sie ihn durch Folgendes:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Dies ändert die Definition der durch den Spout erstellten Daten, sodass **Zeichenfolgedaten** sowie der zuvor in der Topologie (in der Datei "Program.cs") deklarierte **CustomizedInteropJSONSerializer** verwendet werden.

2. Ersetzen Sie die **NextTuple**-Methode durch Folgendes.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Dadurch werden nach dem Zufallsprinzip eine Geräte-ID und ein Wert erstellt, und anschließend wird Json.net verwendet, um mithilfe dieser Werte ein JSON-Objekt auszugeben.

3. Speichern Sie die Datei **Spout.cs**.

Nun verfügen Sie über eine grundlegende Topologie, die Daten nach dem Zufallsprinzip erzeugt und sie mithilfe des Event Hubs-Bolts in Event Hubs speichert. Als Nächstes erstellen Sie das reader-Element.

## Erstellen von EventHubReader

In diesem Abschnitt erstellen Sie eine Topologie, die Daten mithilfe des Event Hubs-Spouts aus Event Hubs liest.

2. Wählen Sie in Visual Studio **Datei** > **Neu** > **Projekt** aus.

3. Erweitern Sie auf dem Bildschirm **Neues Projekt** die Option **Installiert**, **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung** aus. Geben Sie unten auf dem Bildschirm **EventHubReader** als Namen der Anwendung ein.

### Konfiguration

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **EventHubReader**, und wählen Sie **Eigenschaften**.

2. Wählen Sie in den Projekteigenschaften die Option **Einstellungen** und anschließend die Option **Dieses Projekt enthält keine Standardeinstellungsdatei. Klicken Sie hier, um eine zu erstellen.**

3. Geben Sie folgende Einstellungen ein. Verwenden Sie die Informationen für den Event Hub und das Speicherkonto, die Sie zuvor in der Spalte **Wert** erstellt haben.

	<table>
<tr><th style="text-align:left">Name</th><th style="text-align:left">Typ</th><th style="text-align:left">Bereich</th></tr>
<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Anwendung</th></tr>
<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(Verbindungszeichenfolge)</th><th style="text-align:left">Anwendung</th></tr>
<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
</table>Geben Sie als **TableName** den Namen der Tabelle ein, in der Ereignisse gespeichert werden sollen.

  Für **StorageConnection** geben Sie den Wert `DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;` ein. Ersetzen Sie **MyAccount** und **MyKey** mit dem Namen des Speicherkontos und dem zuvor abgerufenen Schlüssel.

	These values will be used by the topology to communicate with Event Hubs and Table Storage.

4. Speichern Sie Ihre Änderungen, und schließen Sie die Seite **Eigenschaften**.

### Definieren der Topologie

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Spout.cs**, und wählen Sie **Löschen**. Da Sie den Java-Event Hubs-Spout verwenden, benötigen Sie diese Datei nicht.

2. Öffnen Sie die Datei **Program.cs**, und fügen Sie direkt unterhalb der Zeile `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` den folgenden Code ein:

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	Die Anzahl der Partitionen wird gelesen und einer lokalen Variable zugewiesen. Sie wird mehrmals verwendet.

	Der `JavaComponentConstructor` definiert, wie der Java-Spout zur Laufzeit konstruiert wird. In diesem Fall verwenden Sie die <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>, um den Spout mit den zuvor hinzugefügten Event Hubs-Konfigurationsinformationen zu konfigurieren. Genauer gesagt wird dieser Code von HDInsight zur Laufzeit für Folgendes verwendet:

	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.spout.EventHubSpoutConfig** mithilfe der von Ihnen bereitgestellten Event Hubs-Informationen.

	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.spout.EventHubSpout** und Übergabe der **EventHubSpoutConfig**-Instanz.

5. Suchen Sie den folgenden Code:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Ersetzen Sie ihn durch Folgendes:

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	Dieser Code weist die Topologie an, den **JavaComponentConstructor** aus dem vorherigen Schritt als Spout zu verwenden und mit "EventHubSpout" zu benennen. Der Code legt ebenfalls den Parallelitätshinweis für diese Komponente auf die Anzahl der Partitionen in Event Hub fest.

2. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu:

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Damit erstellen Sie ein benutzerdefiniertes Serialisierungsprogramm, das zum Serialisieren von mittels Java-Komponenten (z. B. dem "EventHubSpout") erzeugten Informationen in ein JSON-Format verwendet wird, das C#-Downstreamkomponenten nutzen können.

3. Suchen Sie den folgenden Code:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Ersetzen Sie ihn durch Folgendes:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount,
            true).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Dieser Code weist die Topologie an, einen (in "Bolt.cs" definierten) Bolt zu verwenden. Die oben definierte benutzerdefinierte Serialisierung wird hier verwendet, sodass dieser Bolt von Upstream-Java-Komponenten erzeugte Daten verwenden kann. In diesem Fall ist dies "EventHubSpout".

    > [AZURE.IMPORTANT]Der letzte Parameter für "SetBolt" (mit einem Wert von `true`) ermöglicht ACK-Funktionen für diesen Bolt. Dieser Parameter ist erforderlich, da die "EventHubSpout"-Komponente ein ACK als Datenausgabe erwartet. Wenn keine ACKs von Downstreamkomponenten zurückgegeben werden, beendet der Spout nach der Verarbeitung von ca. 1000 Nachrichten den Empfang.

An dieser Stelle ist die Arbeit mit **Program.cs** abgeschlossen. Die Topologie wurde definiert, nun müssen Sie eine Hilfsklasse erstellen, um Daten in den Tabellenspeicher zu schreiben. Anschließend müssen Sie **Bolt.cs** so ändern, dass die durch den Spout erzeugten Daten interpretiert werden können.

> [AZURE.NOTE]Diese Topologie erstellt standardmäßig einen Arbeitsprozess, der zu Beispielzwecken ausreicht. Bei der Übernahme für einen Produktionscluster sollten Sie Folgendes hinzufügen und an die Anzahl von Arbeitsthreads anpassen, die Sie erstellen möchten.
>
> ```topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
                {
                    {"topology.workers", "1"}  //Change to set the number of workers to create
                });```

### Erstellen einer Hilfsklasse

Damit Daten in den Tabellenspeicher geschrieben werden können, müssen Sie eine Klasse erstellen, die die zu schreibenden Daten beschreibt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **Hinzufügen** und dann **Neue Klasse** aus. Geben Sie der neuen Klasse den Namen **Devices.cs**.

2. Öffnen Sie die Datei **Devices.cs**, und ersetzen Sie den vorhandenen Code durch Folgendes.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;

		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }

		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	Dieser Code erstellt Entitäten im Tabellenspeicher, die aus einem Partitionsschlüssel (dieser wird auf die aus Event Hub gelesene Geräte-ID festgelegt), einem eindeutigen Zeilenschlüssel und einem aus Event Hub gelesenen Wert bestehen. Jede Entität verfügt außerdem über einen Zeitstempel, der beim Einfügen der Entität in die Tabelle automatisch erstellt wird.

### Ändern des Bolts

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie nach dem **Json.NET**-Paket, und fügen Sie es der Projektmappe hinzu. Auf diese Weise lassen sich die aus dem Spout übermittelten JSON-Daten einfach verarbeiten. Fügen Sie auch ein **WindowsAzure.Storage**-Paket hinzu, das das Schreiben von Daten in den Tabellenspeicher ermöglicht.

1. Öffnen Sie **Bolt.cs**, und fügen Sie am Anfang der Datei Folgendes ein:

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Dies vereinfacht die Arbeit mit JSON-Daten aus dem Bolt und das Schreiben von Daten in den Tabellenspeicher.

2. Suchen Sie die `private int count;`-Anweisung, und ersetzen Sie sie durch Folgendes:

        private CloudTable table;

	Diese Anweisung wird beim Herstellen einer Verbindung mit der Tabelle verwendet.

4. Suchen Sie den folgenden Code:

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Ersetzen Sie ihn durch Folgendes:

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Dieser Code weist den Bolt an, dass ein **string**-Wert anstelle eines **int**-Werts gesendet wird und die Daten mithilfe von **CustomizedInteropJSONDeserialzer** deserialisiert werden sollen, der zuvor in der Topologie (in der Datei "program.cs") deklariert wurde.

3. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Dieser Code stellt unter Verwendung der zuvor konfigurierten Verbindungszeichenfolge eine Verbindung mit der Tabelle **events** her. Wenn die Tabelle nicht vorhanden ist, wird sie erstellt.

2. Suchen Sie die **Execute**-Methode, und ersetzen Sie sie durch Folgendes:

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
                this.ctx.Ack(tuple);
            }
        }

	Diese Methode verwendet Json.NET, um die JSON-Daten aus dem Spout zu analysieren, und wählt anschließend die Felder **deviceId** und **deviceValue** aus. Anschließend wird ein neues **Device**-Objekt erstellt, wobei während der Initialisierung die **deviceId** verwendet wird, um den Partitionsschlüssel für die Tabelle festzulegen. Danach wird der Wert auf **deviceValue** festgelegt, und die Entität wird in die Tabelle eingefügt.

    Nachdem die Entität in die Tabelle eingefügt wurde, wird `Ack()` für das Tupel aufgerufen wird, um den Spout zu informieren, dass die Daten erfolgreich verarbeitet wurden.

    > [AZURE.IMPORTANT]Die "EventHubSpout"-Komponente erwartet ein ACK für jedes Tupel von Downstreamkomponenten, wie z. B. diesen Spout. Wenn keine ACKs empfangen werden, nimmt "EventHubSpout" an, dass die Verarbeitung für das Tupel fehlgeschlagen ist.

Nun verfügen Sie über eine vollständige Topologie, die Daten aus Event Hub liest und sie im Tabellenspeicher in einer Tabelle mit der Bezeichnung **events** speichert.

## Bereitstellen der Topologien

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **An Storm in HDInsight übermitteln** aus.

	![Senden an Storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Wählen Sie auf dem Bildschirm **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen**, wählen Sie **Java-Dateipfade** und **...**, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** enthält. Klicken Sie schließlich auf **Senden**.

	![Dialogfeld "Senden"](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Sobald die Topologie übermittelt wurde, wird der **Storm-Topologie-Viewer** angezeigt. Wählen Sie auf der linken Seite des Dialogfelds die Topologie **EventHubReader** aus, um die Statistiken für diese Topologie anzuzeigen. Aktuell sollte dort nichts angezeigt werden, da noch keine Ereignisse in Event Hubs geschrieben wurden.

	![Beispiel-Speicheransicht](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubWriter**, und wählen Sie **An Storm in HDInsight übermitteln** aus.

2. Wählen Sie auf dem Bildschirm **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen**, wählen Sie **Java-Dateipfade** und **...**, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** enthält. Klicken Sie schließlich auf **Senden**.

5. Sobald die Topologie übermittelt wurde, aktualisieren Sie die Topologieliste im **Storm-Topologie-Viewer**, um sicherzustellen, dass beide Topologien auf dem Cluster ausgeführt werden.

6. Sobald beide ausgeführt werden, wählen Sie **Server-Explorer**, erweitern Sie **Azure** > **Storage**, und wählen Sie das Speicherkonto, das Sie zuvor erstellt haben. Erweitern Sie im Speicherkonto den Eintrag **Tabellen**. Doppelklicken Sie abschließend auf die Tabelle **events**, um sie zu öffnen. Hier sollten die Daten aus der **EventHubReader**-Topologie angezeigt werden, die in der Tabelle gespeichert wurden.

	* Die Ereignisse werden durch die **EventHubWriter**-Topologie erzeugt, die sie in Event Hub schreibt.

	* **EventHubReader** liest die Ereignisse aus Event Hubs und speichert sie im Tabellenspeicher in der Tabelle **events**.

## Beenden der Topologien

Um die Topologien zu beenden, wählen Sie jede Topologie im **Storm-Topologie-Viewer**, und klicken Sie auf **Beenden**.

![Abbildung zum Beenden einer Topologie](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## Hinweise

### Konfiguration

Es gibt beim Erstellen von "EventHubSpoutConfig" mehrere überladene Methoden. Verwenden Sie die folgenden Informationen, um die Ihren Anforderungen am besten entsprechende Methode herauszufinden.

* EventHubSpoutConfig(String PolicyName, String PolicyKey, String Namespace, String HubName, Int PartitionCount)

    * PolicyName: Der Name der freigegebenen Zugriffsrichtlinie, die vom angegebenen Hub lesen kann.

    * PolicyKey: Der Schlüssel der Richtlinie für den freigegebenen Zugriff.

    * Namespace: Der Service Bus-Namespace, in dem der Hub vorhanden ist.

    * HubName: Der Name des Event Hubs, aus dem gelesen werden soll.

    * PartitionCount: Die Anzahl der Partitionen für den Hub.

* EventHubSpoutConfig(String PolicyName, String PolicyKey, String Namespace, String HubName, Int PartitionCount, String ZooKeeperConnection)

    Zusätzlich zu den zuvor beschriebenen Eigenschaften:

    * ZooKeeperConnection: Die Verbindungszeichenfolge für den ZooKeeper-Knoten. Lassen Sie diese für Storm auf HDInsight-Server leer.

* EventHubSpoutConfig(String PolicyName, String PolicyKey, String Namespace, String HubName, Int PartitionCount, String ZooKeeperConnection, Int CheckPointIntervalInSeconds,Int ReceiverCredits)

    Zusätzlich zu den zuvor beschriebenen Eigenschaften:

    * CheckPointIntervalInSeconds: Häufigkeit, mit der der Status "Zookeeper" beibehalten wird.

    * ReceiverCredits: Die Anzahl von Ereignissen, die vor der Freigabe in der Storm-Topologie zusammengefasst werden.

* EventHubSpoutConfig(String PolicyName, String PolicyKey, String Namespace, String HubName, Int PartitionCount, String ZooKeeperConnection, Int CheckPointIntervalInSeconds, Int ReceiverCredits, Int MaxPendingMsgsPerPartition, Long EnqueueTimeFilter)

    Zusätzlich zu den zuvor beschriebenen Eigenschaften:

    * MaxPendingMsgsPerPartition: Die maximale Anzahl von Ereignissen, die vom Hub abgerufen werden. Der Standardwert ist 1024.

    * EnqueueTimeFilter: Filtert Ereignisse basierend auf dem Zeitstempel, zu dem das Ereignis in die Warteschlange eingereiht wurde.

### Prüfpunkterstellung

Der EventHubSpout übermittelt in regelmäßigen Abständen Prüfpunkte an den Zookeeper-Knoten, der den aktuellen Offset für Nachrichten speichert, die aus der Warteschlange gelesen wurden. Dadurch kann die Komponente in den folgenden Szenarios mit dem Empfang von Nachrichten am gespeicherten Offset beginnen:

* Die Instanz der Komponente hat einen Fehler verursacht und wird neu gestartet.

* Der Cluster wird durch Hinzufügen oder Entfernen von Knoten vergrößert oder verkleinert.

* Die Topologie wird beendet und **mit dem gleichen Namen** neu gestartet.

Sie können die beibehaltenen Prüfpunkte auch aus dem und in den WASB (Azure-Speicher, der vom HDInsight-Cluster verwendet wird) exportieren und importieren. Die Skripts dazu befinden sich in Storm auf dem HDInsight-Cluster unter **C:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE]Die Versionsnummer im Pfad kann eine andere sein, da sich die installierte Storm-Version in Zukunft ändern kann.

In diesem Verzeichnis befinden sich die folgenden Skripts:

* **stormmeta_import.cmd**: importiert alle Storm-Metadaten aus dem Cluster-Standardcontainer in Zookeeper.

* **stormmeta_export.cmd**: exportiert alle Storm-Metadaten aus Zookeeper in den Cluster-Standardcontainer.

* **stormmeta_delete.cmd**: löscht alle Storm-Metadaten aus Zookeeper.

Durch das Exportieren von Importdaten können Sie Prüfpunktdaten beibehalten, wenn Sie den Cluster löschen müssen, die Verarbeitung jedoch vom aktuellen Offset im Hub fortsetzen möchten, wenn Sie einen neuen Cluster wieder online schalten.

> [AZURE.NOTE]Da die Daten im Standardspeichercontainer beibehalten werden **muss** der neue Cluster das gleiche Speicherkonto und den gleichen Container wie der vorherige Cluster verwenden.

## Zusammenfassung

In diesem Dokument haben Sie erfahren, wie Sie die Java-Event Hubs-Spouts und -Bolts aus einer C#-Topologie verwenden, um mit Daten in Azure Event Hub zu arbeiten. Weitere Informationen zum Erstellen von C#-Topologien finden Sie in folgenden Themen.

* [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

<!--HONumber=54-->