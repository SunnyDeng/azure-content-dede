<properties
   pageTitle="Verarbeiten von Ereignissen aus Event Hubs mit Storm in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie Event Hubs-Daten mit einer C#-Storm-Topologie verarbeiten, die mithilfe der HDInsight-Tools für Visual Studio in Visual Studio erstellt wurde."
   services="hdinsight,notification hubs"
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
   ms.date="01/28/2016"
   ms.author="larryfr"/>

# Verarbeitung von Ereignissen von Azure-Event-Hubs mit Storm auf HDInsight (C#)

Azure Event Hubs ermöglichen Ihnen die Verarbeitung sehr großer Datenmengen aus Websites, Apps und Geräten. Der Event Hubs-Spout vereinfacht die Verwendung von Apache Storm in HDInsight, um diese Daten in Echtzeit zu analysieren. Sie können auch Daten aus Storm mithilfe des Event Hubs-Bolts in Event Hub schreiben.

In diesem Lernprogramm erfahren Sie, wie Sie die HDInsight-Tools für Visual Studio sowie den Spout und den Bolt für Event Hubs verwenden, um zwei hybride C#-/Java-Topologien zu erstellen:

* **EventHubWriter:** generiert Daten nach dem Zufallsprinzip und schreibt sie in Event Hubs

* **EventHubReader:** liest Daten aus Event Hubs und speichert sie im Azure-Tabellenspeicher

[AZURE.NOTE] Die Schritte in diesem Dokument funktionieren nur mit einem Windows-basierten HDInsight-Cluster. Eine Java-Version dieses Projekts, das mit einem Linux- oder Windows-basierten Cluster funktioniert, finden Sie unter [Verarbeitung von Ereignissen von Azure Event Hubs mit Storm auf HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## Voraussetzungen

* Ein [Apache Storm in HDInsight-Cluster](hdinsight-apache-storm-tutorial-get-started.md)

* Ein [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* Das [Azure .NET SDK](http://azure.microsoft.com/downloads/)

* Die [HDInsight-Tools für Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## Abgeschlossenes Projekt

Sie können eine vollständige Version des Projekts von GitHub herunterladen, die in folgendem Lernprogramm erstellt wurde: [eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid). Sie müssen jedoch weiterhin Konfigurationseinstellungen bereitstellen, indem Sie die Schritte in diesem Lernprogramm befolgen:

> [AZURE.NOTE] Wenn Sie das abgeschlossene Projekt verwenden, müssen Sie die für diese Lösung erforderlichen Pakete mit dem **NuGet-Paket-Manager** wiederherstellen.

## Spout und Bolt für Event Hubs

Der Spout und der Bolt für Event Hubs sind Java-Komponenten, die Ihnen die Arbeit mit Event Hubs aus Apache Storm vereinfachen. Obwohl diese Komponenten in Java geschrieben sind, ermöglichen die HDInsight-Tools für Visual Studio das Erstellen von hybriden Topologien, in denen C#- und Java-Komponenten gemeinsam verwendet werden.

Der Spout und der Bolt werden als einzelne Java-Archivdatei (.jar) mit dem Namen **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** bereitgestellt.

### Herunterladen der JAR-Datei

Die neueste Version der Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ist im Projekt <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a> im Ordner **lib** enthalten. Laden Sie die Datei mithilfe einer der folgenden Methoden herunter.

> [AZURE.NOTE] Spout und Bolt wurden zur Verwendung im Apache Storm-Projekt übermittelt. Weitere Informationen finden Sie bei GitHub unter <a href="https://github.com/apache/storm/pull/336/files">STORM-583: Initiali check-in for storm-eventhubs</a> (in englischer Sprache).

* **Herunterladen einer ZIP-Datei**: Wählen Sie auf der Website für <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a> die Schaltfläche **Download ZIP**, um eine ZIP-Datei mit dem Projekt herunterzuladen.

	![Schaltfläche "Download ZIP"](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Nach dem Herunterladen der Datei können Sie das Archiv extrahieren – die Datei befindet sich im Verzeichnis **lib**.

* **Klonen des Projekts**: Wenn <a href="http://git-scm.com/" target="_blank">Git</a> installiert ist, verwenden Sie folgenden Befehl, um das Repository lokal zu klonen – die Datei befindet sich anschließend im Verzeichnis **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Konfigurieren von Event Hub

Event Hubs ist die Datenquelle für dieses Beispiel. Führen Sie die folgenden Schritte aus, um einen neuen Event Hub zu erstellen.

1. Wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU** > **Service Bus** > **Event Hub** > **Benutzerdefiniert erstellen**.

2. Geben Sie im Bildschirm **Neuen Event Hub hinzufügen** einen Wert für **Event Hub-Name** ein, wählen Sie die **Region** aus, in der der Hub erstellt werden soll, und erstellen Sie einen neuen Namespace, oder wählen Sie einen vorhandenen Namespace aus. Klicken Sie auf den **Pfeil**, um fortzufahren.

	![Seite 1 des Assistenten](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Wählen Sie den gleichen **Speicherort** aus wie für Ihren Storm auf HDInsight-Server, um Latenz und Kosten zu verringern.

2. Geben Sie im Bildschirm **Event Hub konfigurieren** die Werte für **Partitionsanzahl** und **Nachrichtenaufbewahrung** ein. Verwenden Sie für dieses Beispiel eine Anzahl von 10 Partitionen und eine Nachrichtenaufbewahrung von 1. Notieren Sie sich die Partitionsanzahl, da Sie diesen Wert später benötigen werden.

	![Seite 2 des Assistenten](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Nachdem der Event Hub erstellt wurde, wählen Sie den Namespace, wählen Sie **Event Hubs** aus, und wählen Sie dann den zuvor erstellten Event Hub aus.

4. Klicken Sie auf **Konfigurieren**, und erstellen Sie zwei neue Zugriffsrichtlinien mit den folgenden Informationen.

	<table>
	<tr><th>Name</th><th>Berechtigungen</th></tr>
	<tr><td>Writer</td><td>Send</td></tr>
	<tr><td>Leser</td><td>Empfangen</td></tr>
	</table>

	Klicken Sie nach dem Erstellen der Berechtigungen auf das Symbol **Speichern** am unteren Seitenrand. Auf diese Weise werden die Richtlinien für den gemeinsamen Zugriff erstellt, die zum Senden (writer) und Empfangen (reader) von Daten an diesen Event Hub bzw. von diesem verwendet werden.

	![Richtlinien](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Verwenden Sie nach dem Speichern der Richtlinien den **Freigegebener Generator für Zugriffsschlüssel** am unteren Seitenrand, um den Schlüssel für die Richtlinien **writer** und **reader** abzurufen. Speichern Sie diese Schlüssel für den späteren Gebrauch.

## Konfigurieren des Tabellenspeichers

Im Tabellenspeicher werden aus Event Hub abgerufene Werte gespeichert, und Sie können den Tabellenspeicher einfach über den **Server-Explorer** in Visual Studio anzeigen. Gehen Sie wie folgt vor, um einen neuen Tabellenspeicher zu erstellen:

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU** > **Data Services** > **Speicher** > **Schnellerfassung** aus.

	![Schnellerfassung von Speicher](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Geben Sie einen **Namen** für das Speicherkonto ein, wählen Sie einen **Speicherort**, und klicken Sie auf das **Häkchen**, um das Speicherkonto zu erstellen.

	> [AZURE.NOTE] Wählen Sie den gleichen **Speicherort** wie für Ihre Event Hubs und Storm auf HDInsight-Server aus, um Latenz und Kosten zu verringern.

3. Sobald das neue Speicherkonto bereitgestellt ist, wählen Sie es aus, und verwenden Sie anschließend den Link **Zugriffsschlüssel verwalten** am unteren Seitenrand, um den **Speicherkontonamen** und den **Primären Zugriffsschlüssel** abzurufen. Speichern Sie diese Informationen, da sie später benötigt werden.

	![Zugriffsschlüssel](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Erstellen von EventHubWriter

In diesem Abschnitt erstellen Sie eine Topologie, die Daten mithilfe des Event Hubs-Bolts in Event Hubs schreibt.

1. Wenn Sie die neueste Version der HDInsight-Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Erste Schritte mit den HDInsight-Tools für Visual Studio</a>.

2. Öffnen Sie Visual Studio, und wählen Sie **Datei** > **Neu** und dann **Projekt** aus.

3. Erweitern Sie im Bildschirm **Neues Projekt** die Option **Installiert** > **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung** aus. Geben Sie unten im Bildschirm **EventHubWriter** als Namen der Anwendung ein.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Nachdem das Projekt erstellt wurde, sollten die folgenden Dateien vorliegen:

	* **Program.cs:** definiert die Topologie für Ihr Projekt. Beachten Sie, dass standardmäßig eine Topologie erstellt wird, die aus einem Spout und einem Bolt besteht.

	* **Spout.cs.** ein Beispiel-Spout.

	* **Bolt.cs:** ein Beispiel-Bolt. Diese werden gelöscht, da Sie den Event Hubs-Bolt verwenden werden, um in Event Hub zu schreiben.

### Konfiguration

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **EventHubWriter**, und wählen Sie **Eigenschaften** aus.

2. Wählen Sie in den Projekteigenschaften die Option **Einstellungen** und anschließend die Option **Dieses Projekt enthält keine Standardeinstellungsdatei. Klicken Sie hier, um eine zu erstellen** aus.

3. Geben Sie folgende Einstellungen ein. Verwenden Sie die Informationen für den Event Hub, den Sie zuvor in der Spalte **Wert** erstellt haben.

	<table>
	<tr><th style="text-align:left">Name</th><th style="text-align:left">Typ</th><th style="text-align:left">Umfang</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Anwendung</td></tr>
	</table>

4. Speichern und schließen Sie die **Eigenschaftenseite**.

### Definieren der Topologie

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Bolt.cs**, und wählen Sie **Löschen** aus. Da Sie den Java Event Hubs-Bolt verwenden, benötigen Sie diese Datei nicht.

2. Öffnen Sie die Datei **Program.cs**, und fügen Sie direkt unterhalb der Zeile `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` den folgenden Code ein.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	Die erste Zeile liest die Anzahl der Partitionen aus den zuvor definierten Eigenschaften. Die zweite Zeile definiert einen Deserialisierer, mit dem die durch den Spout erzeugten JSON-Daten in einen `java.lang.String` deserialisiert werden, sodass Java-Komponenten die Daten nutzen können.

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

	Dieser Code erstellt einen Spout und verwendet die Event Hubs-Partitionsanzahl als Parallelitätshinweis für diese Komponente. Es soll eine Instanz des Spouts für jede Partition erstellt werden.

	Außerdem wird hierdurch der zuvor erstellte Deserialisierer mit dem Ausgabedatenstrom dieser Komponente verknüpft. So kann die nachfolgende EventHubSpout-Komponente die von dem C#-Spout erzeugten Daten nutzen.

5. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu:

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

	Dieser Code weist die Topologie an, den **JavaComponentConstructor** aus dem vorherigen Schritt als Bolt zu verwenden. Die Komponente kann in dieser Topologie mit dem Anzeigenamen "EventHubBolt" referenziert werden. Der Parallelitätshinweis ist auf die Anzahl der Partitionen für den Event Hub festgelegt, und die Komponente abonniert Daten, die durch den Spout ("Spout") erzeugt werden.

An dieser Stelle ist die Arbeit mit **Program.cs** abgeschlossen. Die Topologie wurde definiert, nun müssen Sie **Spout.cs** so ändern, dass Daten in einem Format erzeugt werden, das der Event Hubs-Bolt verwenden kann.

> [AZURE.NOTE] Bei dieser Topologie wird standardmäßig nur ein Arbeitsprozess erstellt, was für Beispielzwecke ausreicht. Wenn Sie dieses Beispiel für einen Produktionscluster anpassen, sollten Sie Folgendes zum Ändern der Anzahl der Workerthreads hinzufügen:

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Ändern des Spouts

Der Event Hubs-Bolt erwartet einen einzelnen Zeichenfolgenwert, den er an Event Hub weiterleitet. Im folgenden Beispiel ändern Sie die Standarddatei **Spout.cs**, um eine JSON-Zeichenfolge zu erzeugen.

1. Öffnen Sie im **Projektmappen-Explorer** die Datei **Spout.cs**, und fügen Sie am Anfang der Datei folgenden Code ein:

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Dies vereinfacht die Arbeit mit JSON-Daten.
    
    > [AZURE.NOTE] Das JSON.NET-Paket sollte bereits installiert sein, da dies für das SCP.NET-Framework für C#-Storm-Topologien erforderlich ist.

3. Suchen Sie den folgenden Code:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Ersetzen Sie ihn durch Folgendes:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Dies ändert die Definition der von dem Spout erstellten Daten so, dass **string**-Daten und der zuvor in der Topologie (in program.cs) deklarierte **CustomizedInteropJSONSerializer** verwendet werden.

2. Ersetzen Sie die **NextTuple**-Methode durch Folgendes.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Dadurch werden nach dem Zufallsprinzip eine Geräte-ID und ein Wert erstellt, und anschließend wird Json.NET verwendet, um mithilfe dieser Werte ein JSON-Objekt auszugeben.

3. Speichern Sie die Datei **Spout.cs**.

Nun verfügen Sie über eine grundlegende Topologie, die Daten nach dem Zufallsprinzip erzeugt und mithilfe des Event Hubs-Bolts in Event Hubs speichert. Als Nächstes erstellen Sie das reader-Element.

## Erstellen von EventHubReader

In diesem Abschnitt erstellen Sie eine Topologie, die Daten mithilfe des Event Hubs-Spouts aus Event Hubs liest.

2. Öffnen Sie Visual Studio, und wählen Sie **Datei** > **Neu** > **Projekt** aus.

3. Erweitern Sie im Bildschirm **Neues Projekt** die Option **Installiert** > **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung** aus. Geben Sie unten im Bildschirm **EventHubReader** als Namen der Anwendung ein.

### Konfiguration

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **EventHubReader**, und wählen Sie **Eigenschaften** aus.

2. Wählen Sie in den Projekteigenschaften die Option **Einstellungen** und anschließend die Option **Dieses Projekt enthält keine Standardeinstellungsdatei. Klicken Sie hier, um eine zu erstellen** aus.

3. Geben Sie folgende Einstellungen ein. Verwenden Sie die Informationen für den Event Hub und das Speicherkonto, die Sie zuvor in der Spalte **Wert** erstellt haben.

	<table>
	<tr><th style="text-align:left">Name</th><th style="text-align:left">Typ</th><th style="text-align:left">Umfang</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Anwendung</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(Verbindungszeichenfolge)</th><th style="text-align:left">Anwendung</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">Anwendung</th></tr>
	</table>

	Geben Sie als **TableName** den Namen der Tabelle ein, in der Ereignisse gespeichert werden sollen.

    Für **StorageConnection** geben Sie den Wert `DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;` ein. Ersetzen Sie **myAccount** und **myKey** durch den Namen des Speicherkontos und den zuvor abgerufenen Schlüssel.

	Diese Werte werden von der Topologie zur Kommunikation mit Event Hubs und dem Tabellenspeicher verwendet.

4. Speichern und schließen Sie die **Eigenschaftenseite**.

### Definieren der Topologie

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Spout.cs**, und wählen Sie **Löschen** aus. Da Sie den Java Event Hubs-Spout verwenden, benötigen Sie diese Datei nicht.

2. Öffnen Sie die Datei **Program.cs**, und fügen Sie direkt unterhalb der Zeile `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` den folgenden Code ein:

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		EventHubSpoutConfig ehConfig = new EventHubSpoutConfig(
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount);

	Die Anzahl der Partitionen wird gelesen und einer lokalen Variable zugewiesen. Sie wird an mehreren Stellen verwendet.

	In `EventHubSpoutConfig` wird die Konfiguration für das Event Hub-Spout definiert. In diesem Fall sind dies die zuvor hinzugefügten Konfigurationsinformationen des Event Hubs. Hierbei wird im Hintergrund auf den Java Event-Hub zugegriffen und mithilfe der Event Hub-Informationen eine neue Instanz von **com.microsoft.eventhubs.spout.EventHubSpoutConfig** erstellt.

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

        topologyBuilder.SetEventHubSpout(
            "EventHubSpout", 
            ehConfig, 
            partitionCount); 

	Hierdurch wird die Topologie angewiesen, einen neuen Event Hub-Spout zu erstellen und `EventHubSpoutConfig` aus dem vorherigen Schritt als Konfiguration zu verwenden. "EventHubSpout" setzt den Anzeigenamen des Spouts, und `partitionCount` wird zum Setzen des Parallelitätshinweises verwendet. Im Hintergrund wird hierdurch anhand der angegebenen Konfigurationsdaten eine neue Instanz der Java-Komponente **com.microsoft.eventhubs.spout.EventHubSpout** erstellt.

2. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu:

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Hierdurch wird ein benutzerdefinierter Serialisierer erstellt, der zum Serialisieren der von Java-Komponenten (z. B. EventHubSpout) erzeugten Daten verwendet wird. Das beim Serialisieren erzeugte JSON-Format ist von nachgelagerten C#-Komponenten nutzbar.

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

	Dieser Code weist die Topologie an, einen (in Bolt.cs definierten) Bolt zu verwenden. Der zuvor definierte benutzerdefinierte Serialisierer wird hier verwendet, damit dieser Bolt die von vorgelagerten Java-Komponenten erzeugten Daten nutzen kann. In diesem Fall ist dies der EventHubSpout.

    > [AZURE.IMPORTANT] Der letzte Parameter für SetBolt (mit dem Wert `true`) ermöglicht ACK-Funktionalität. Dieser Parameter ist erforderlich, da die EventHubSpout-Komponente für die ausgegebenen Daten ein ACK erwartet. Wenn von nachgelagerten Komponenten keine ACKs zurückgegeben werden, stoppt der Spout den Empfang nach der Verarbeitung von ungefähr 1.000 Nachrichten.

An dieser Stelle ist die Arbeit mit **Program.cs** abgeschlossen. Die Topologie wurde definiert, nun müssen Sie eine Hilfsklasse erstellen, um Daten in den Tabellenspeicher zu schreiben. Anschließend müssen Sie **Bolt.cs** so ändern, dass die durch den Spout erzeugten Daten interpretiert werden können.

> [AZURE.NOTE] Bei dieser Topologie wird standardmäßig nur ein Arbeitsprozess erstellt, was für Beispielzwecke ausreicht. Wenn Sie dieses Beispiel für einen Produktionscluster anpassen, sollten Sie Folgendes zum Ändern der Anzahl der Workerthreads hinzufügen:

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Erstellen einer Hilfsklasse

Damit Daten in den Tabellenspeicher geschrieben werden können, müssen Sie eine Klasse erstellen, die die zu schreibenden Daten beschreibt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **Hinzufügen** und **Neue Klasse** aus. Geben Sie der neuen Klasse den Namen **Devices.cs**.

2. Öffnen Sie die Datei **Devices.cs**, und ersetzen Sie den vorhandenen Code durch Folgendes:

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

	Dieser Code erstellt Entitäten im Tabellenspeicher, die aus einem Partitionsschlüssel (auf die aus Event Hub gelesene Geräte-ID festgelegt), einem eindeutigen Zeilenschlüssel und einem aus Event Hub gelesenen Wert bestehen. Jede Entität verfügt außerdem über einen Zeitstempel, der beim Einfügen der Entität in die Tabelle automatisch erstellt wird.

### Ändern des Bolts

1. Erweitern Sie im **Projektmappen-Explorer** das Projekt **EventHubReader**, und öffnen Sie die Datei **Bolt.cs**. Fügen Sie Folgendes am Anfang der Datei hinzu:

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Dies vereinfacht die Arbeit mit JSON-Daten aus dem Bolt und das Schreiben von Daten in den Tabellenspeicher.

2. Suchen Sie die Anweisung `private int count;`, und ersetzen Sie sie durch Folgendes:

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

	Dieser Code weist den Bolt an, dass ein **string**-Wert anstelle eines **int**-Werts gesendet wird und die Daten mithilfe des zuvor in der Topologie (in der Datei program.cs) definierten **CustomizedInteropJSONDeserialzer** deserialisiert werden sollen.

3. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu:

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

    Nachdem die Entität in die Tabelle eingefügt wurde, wird `Ack()` für das Tupel aufgerufen, um den Spout über die erfolgreiche Verarbeitung der Daten zu informieren.

    > [AZURE.IMPORTANT] Die Komponente EventHubSpout erwartet von nachgelagerten Komponenten wie diesem Bolt ein ACK für jedes Tupel. Wenn keine ACKs empfangen werden, geht EventHubSpout davon aus, dass die Verarbeitung des Tupels fehlgeschlagen ist.

Nun verfügen Sie über eine vollständige Topologie, die Daten aus Event Hub liest und im Tabellenspeicher in einer Tabelle mit der Bezeichnung **events** speichert.

## Bereitstellen der Topologien

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **Submit to Storm on HDInsight** aus.

	![An Storm senden](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Wählen Sie im Dialogfeld **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen**, klicken Sie auf **Java File Paths** und dann auf **...**, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** enthält. Klicken Sie abschließend auf **Senden**.

	![Bild des Dialogfelds "Senden"](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Sobald die Topologie übermittelt wurde, wird der **Storm Topologies Viewer** angezeigt. Wählen Sie im linken Bereich die Topologie **EventHubReader** aus, um die Statistiken für diese Topologie anzuzeigen. Aktuell sollte dort nichts angezeigt werden, da noch keine Ereignisse in Event Hubs geschrieben wurden.

	![Beispiel für Speicheransicht](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubWriter**, und wählen Sie **Submit to Storm on HDInsight** aus.

2. Wählen Sie im Dialogfeld **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen**, klicken Sie auf **Java File Paths** und dann auf **...**, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** enthält. Klicken Sie abschließend auf **Senden**.

5. Sobald die Topologie übermittelt wurde, aktualisieren Sie die Topologieliste im **Storm Topologies Viewer**, um sicherzustellen, dass beide Topologien auf dem Cluster ausgeführt werden.

6. Sobald beide Topologien ausgeführt werden, wählen Sie **Server-Explorer**, erweitern Sie **Azure** > **Speicher**, und wählen Sie anschließend das zuvor erstellte Speicherkonto aus. Erweitern Sie im Speicherkonto den Eintrag **Tabellen**. Doppelklicken Sie abschließend auf die Tabelle **events**, um sie zu öffnen. Hier sollten die Daten aus der **EventHubReader**-Topologie angezeigt werden, die in der Tabelle gespeichert wurden.

	* Die Ereignisse werden durch die **EventHubWriter**-Topologie erzeugt, die sie in Event Hub schreibt.

	* **EventHubReader** liest die Ereignisse aus Event Hubs und speichert sie im Tabellenspeicher in der Tabelle **events**.

## Beenden der Topologien

Um die Topologien zu beenden, wählen Sie jede Topologie im **Storm Topologies Viewer** aus, und klicken Sie dann auf **Beenden**.

![Bild der Beendigung einer Topologie](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##Löschen des Clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Hinweise

### Setzen von Prüfpunkten

Der EventHubSpout erstellt in regelmäßigen Abständen Prüfpunkte im Zookeeper-Knoten, der den aktuellen Offset für aus der Warteschlange gelesene Nachrichten speichert. Dies ermöglicht der Komponente, in den folgenden Szenarios mit dem Empfang von Nachrichten an dem gespeicherten Offset zu beginnen:

* Die Instanz der Komponente wird aufgrund eines Fehlers neu gestartet.

* Sie vergrößern oder verkleinern den Cluster durch Hinzufügen oder Entfernen von Knoten.

* Die Topologie wird beendet und **mit demselben Namen** neu gestartet.

Die persistenten Prüfpunkte können auch an WASB (dem von Ihrem HDInsight-Cluster verwendeten Azure-Speicher) exportiert und importiert werden. Die dafür geeigneten Skripts befinden sich auf dem Storm-Cluster auf HDInsight unter **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE] Die Versionsnummer im Pfad kann abweichen, da sich die auf dem Cluster installierte Storm-Version in Zukunft ändern könnte.

Die Skripts in diesem Verzeichnis sind:

* **stormmeta\_import.cmd:** importiert alle Storm-Metadaten vom standardmäßigen Speichercontainer des Clusters in Zookeeper.

* **stormmeta\_export.cmd:** exportiert alle Storm-Metadaten von Zookeeper in den standardmäßigen Speichercontainer des Clusters.

* **stormmeta\_delete.cmd:** Alle Storm-Metadaten werden aus Zookeeper gelöscht.

Durch das Exportieren und Importieren können Sie Prüfpunktdaten persistent speichern, um beispielsweise einen Cluster zu löschen und nach dem Onlineschalten des neuen Clusters die Verarbeitung am aktuellen Offset fortsetzen zu können.

> [AZURE.NOTE] Da die Daten im Standard-Speichercontainer persistent gespeichert werden, **muss** der neue Cluster das gleiche Speicherkonto und den gleichen Container verwenden wie der vorherige Cluster.

## Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie den Java-Spout und den -Bolt für Event Hubs aus einer C#-Topologie verwenden, um mit Daten in Azure Event Hub zu arbeiten. Weitere Informationen zum Erstellen von C#-Topologien finden Sie in folgenden Themen.

* [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)
 

<!---HONumber=AcomDC_0309_2016-->