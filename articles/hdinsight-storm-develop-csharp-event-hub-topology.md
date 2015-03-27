<properties
   pageTitle="Verarbeiten von Ereignissen aus Event Hub mit Storm in HDInsight | Azure"
   description="Erfahren Sie, wie Sie Event Hub-Daten mit einer C#-Storm-Topologie verarbeiten, die mithilfe der HDInsight Tools für Visual Studio in Visual Studio erstellt wurde."
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
   ms.date="03/03/2015"
   ms.author="larryfr"/>

# Verarbeiten von Ereignissen aus Azure Event Hub mit Storm in HDInsight (C#)

Azure Event Hub ermöglicht Ihnen die Verarbeitung sehr großer Datenmengen aus Websites, Apps und Geräten. Der Event Hub-Spout vereinfacht die Verwendung von Apache Storm in HDInsight, um diese Daten in Echtzeit zu analysieren. Sie können auch Daten aus Storm mithilfe des Event Hub-Bolts in Event Hub schreiben. 

In diesem Dokument erfahren Sie, wie Sie die HDInsight Tools für Visual Studio und Event Hub-Spout und -Bolt verwenden, um zwei hybride C#/Java-Topologien zu erstellen:

* **EventHubWriter**: generiert Daten nach dem Zufallsprinzip und schreibt sie in Event Hub

* **EventHubReader**: liest Daten aus Event Hub und speichert sie im Azure-Tabellenspeicher

## Voraussetzungen

* Ein <a href="../hdinsight-storm-getting-started/" target="_blank">Apache Storm in HDInsight-Cluster</a>

* Ein <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Azure Event Hub</a>

* Das <a href="http://azure.microsoft.com/downloads/" target="_blank">Azure .NET SDK</a>

* Die <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools für Visual Studio</a> - Version vom 17.2.2015 oder neuer

## Abgeschlossenes Projekt

Sie können eine abgeschlossene Version des in diesem Artikel erstellen Projekts von [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid) herunterladen. Dennoch müssen Sie Konfigurationseinstellungen bereitstellen, wie in den Schritten in diesem Dokument erläutert.

> [AZURE.NOTE] Wenn Sie das abgeschlossene Projekt verwenden, müssen Sie die für diese Lösung erforderlichen Pakete mit dem **NuGet-Paket-Manager** wiederherstellen.

## Event Hub-Spout und -Bolt

Event Hub-Spout und Bolt sind Java-Komponenten, die Ihnen die Arbeit mit Event Hub aus Apache Storm vereinfachen. Während diese Komponenten in Java geschrieben sind, ermöglichen die HDInsight Tools für Visual Studio das Erstellen von hybriden Topologien, in denen C#- und Java-Komponenten gemeinsam verwendet werden.

Spout und Bolt werden als einzelne Java-Archivdatei (.jar) mit dem Namen **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** bereitgestellt.

### Herunterladen der JAR-Datei

Die neueste Version der Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ist im Projekt <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> im Ordner **lib** enthalten. Laden Sie die Datei mithilfe einer der folgenden Methoden herunter.

> [AZURE.NOTE] Spout und Bolt wurden zur Verwendung im Apache Storm-Projekt übermittelt. Weitere Information finden Sie unter <a href="https://github.com/apache/storm/pull/336/files">Pull-Anforderung</a>.

* **Herunterladen einer ZIP-Datei**: Wählen Sie auf der Website <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> die Schaltfläche **Download ZIP** ("ZIP herunterladen"), um eine ZIP-Datei mit dem Projekt herunterzuladen.

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Nach dem Herunterladen können Sie das Archiv extrahieren - die Datei befindet sich im Verzeichnis **lib**.

* **Klonen des Projekts**: Wenn <a href="http://git-scm.com/" target="_blank">Git</a> installiert ist, verwenden Sie folgenden Befehl, um das Repository lokal zu klonen - die Datei befindet sich anschließend im Verzeichnis **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Konfigurieren des Ereignis-Hubs

Event Hub ist die Datenquelle für dieses Beispiel. Führen Sie die folgenden Schritte aus, um ein neues Ereignis-Hub zu erstellen.

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU | Service Bus | Event Hub | Benutzerdefiniert erstellen**.

2. Geben Sie im Dialogfeld **Neues Ereignis-Hub hinzufügen** einen **Ereignis-Hub-Namen** ein, wählen Sie die **Region** aus, in der das Hub erstellt werden soll, und erstellen Sie einen neuen Namespace, oder wählen Sie einen vorhandenen Namespace aus. Klicken Sie zuletzt auf den **Pfeil**.

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Wählen Sie den gleichen **Speicherort** wie für Ihren Storm auf HDInsight-Server, um Latenz und Kosten zu verringern.

2. Geben Sie im Dialogfeld **Ereignis-Hub konfigurieren** die Werte für **Anzahl der Partitionen** und **Nachrichtenaufbewahrung** ein. Verwenden Sie für dieses Beispiel eine Anzahl von 10 Partitionen und eine Nachrichtenaufbewahrung von 1. Notieren Sie sich die Partitionsanzahl, da Sie diesen Wert später benötigen werden.

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Wählen Sie nach der Erstellung des Ereignis-Hubs den Namespace aus, und klicken Sie auf **Ereignis-Hubs**. Wählen Sie zuletzt das Ereignis-Hub aus, das Sie zuvor erstellt haben.

4. Klicken Sie auf **Konfigurieren**, und erstellen Sie zwei neue Zugriffsrichtlinien mit den folgenden Daten.

	<table>
	<tr><th>Name</th><th>Berechtigungen</th></tr>
	<tr><td>writer</td><td>Senden</td></tr>
	<tr><td>reader</td><td>Empfangen</td></tr>
	</table>

	Klicken Sie nach dem Erstellen der Berechtigungen auf das **Speichern**-Symbol am unteren Seitenrand. Auf diese Weise werden die Richtlinien für den gemeinsamen Zugriff erstellt, die zum Senden (writer) und Empfangen (reader) von Daten an bzw. von diesen Event Hub verwendet werden.

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Verwenden Sie nach dem Speichern der Richtlinien den **Generator für freigegebene Zugriffsschlüssel** am unteren Seitenrand, um den Schlüssel für die Richtlinien **writer** und **reader** abzurufen. Speichern Sie diese Schlüssel für den späteren Gebrauch.

## Konfigurieren des Tabellenspeichers

Im Tabellenspeicher werden aus Event Hub abgerufene Werte gespeichert, und Sie können den Tabellenspeicher einfach über den **Server-Explorer** in Visual Studio anzeigen. Gehen Sie wie folgt vor, um einen neuen Tabellenspeicher zu erstellen.

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU | Data Services | Speicher | Schnellerfassung**.

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Geben Sie einen **Namen** für das Speicherkonto ein, wählen Sie einen **Speicherort**, und klicken Sie auf das **Häkchen**, um das Speicherkonto zu erstellen.

	> [AZURE.NOTE] Wählen Sie den gleichen **Speicherort** wie für Ihren Event Hub und Storm auf HDInsight-Server, um Latenz und Kosten zu verringern.

3. Sobald das neue Speicherkonto bereitgestellt ist, wählen Sie das Konto aus, und verwenden Sie anschließend den Link **Zugriffsschlüssel verwalten** am unteren Seitenrand, um den **Speicherkontonamen** und den **Primären Zugriffsschlüssel** abzurufen. Speichern Sie diese Informationen, da sie später benötigt werden.

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Erstellen von EventHubWriter

In diesem Abschnitt erstellen Sie eine Topologie, die Daten mithilfe des Event Hub-Bolts in Event Hub schreibt.

1. Wenn Sie die neueste Version der HDInsight Tools für Visual Studio noch nicht installiert haben, finden Sie Informationen dazu unter <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Erste Schritte mit den HDInsight Tools für Visual Studio</a>.

2. Öffnen Sie Visual Studio, wählen Sie **Datei**, **Neu**, und klicken Sie dann auf **Projekt**.

3. Erweitern Sie im Dialogfeld **Neues Projekt** die Option **Installiert**, **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung** aus. Geben Sie unten im Dialogfeld **EventHubWriter** als Namen der Anwendung ein.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Nachdem das Projekt erstellt wurde, sollten die folgenden Dateien vorliegen:

	* **Program.cs**: definiert die Topologie für Ihr Projekt Beachten Sie, dass standardmäßig eine Topologie erstellt wird, die aus einem Spout und einem Bolt besteht.

	* **Spout.cs**: ein Beispiel-Spout

	* **Bolt.cs**: ein Beispiel-Bolt Diese werden gelöscht, da Sie den Event Hub-Bolt verwenden werden, um in Event Hub zu schreiben.

### Konfiguration

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **EventHubWriter**, und wählen Sie **Eigenschaften**.

2. Wählen Sie in den Projekteigenschaften die Option **Einstellungen** und anschließend die Option **Dieses Projekt enthält keine Standardeinstellungsdatei. Klicken Sie hier, um eine zu erstellen.**

3. Geben Sie folgende Einstellungen ein. Verwenden Sie die Informationen für den Event Hub, den Sie zuvor in der Spalte **Wert** erstellt haben.

	<table>
	<tr><th style="text-align:left">Name</th><th style="text-align:left">Typ</th><th style="text-align:left">Bereich</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">string</td><td style="text-align:left">Anwendung</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Anwendung</td></tr>
	</table>

4. Speichern und schließen Sie die Eigenschaftenseite.

### Definieren der Topologie

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Bolt.cs**, und wählen Sie **Löschen**. Da Sie den Java Event Hub-Bolt verwenden, benötigen Sie diese Datei nicht.

2. Öffnen Sie die Datei **Program.cs**, und fügen Sie folgenden Code direkt unterhalb der Zeile `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` ein.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	Die erste Zeile liest die Anzahl der Partitionen aus den zuvor definierten Eigenschaften. Die zweite Zeile definiert ein Deserialisierungsprogramm, mit dem die durch den Spout erzeugten JSON-Daten in eine `java.lang.String`-Zeichenfolge deserialisiert werden, sodass der Event Hub-Bolt die Daten nutzen kann.

4. Suchen Sie den folgenden Code.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Ersetzen Sie ihn durch Folgendes.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Dieser Code erstellt einen Spout und verwendet die Event Hub-Partitionsanzahl als Parallelitätshinweis für diese Komponente. Es soll eine Instanz des Spouts für jede Partition erstellt werden.
	
	Dieser Code legt auch fest, dass der Ausgabestream das zuvor erstellte Deserialisierungsprogramm verwenden soll.

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

	Dieser Code erstellt einen neuen Konstruktor für den Java-Bolt, mit dem zur Laufzeit eine neue Instanz des Bolts konfiguriert wird. In diesem Fall verwenden Sie die <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>, um den Spout mit den zuvor hinzugefügten Event Hub-Konfigurationsinformationen zu konfigurieren. Genauer gesagt wird dieser Code von HDInsight zur Laufzeit für Folgendes verwendet:

	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.bolt.EventHubBoltConfig** mithilfe der von Ihnen bereitgestellten Event Hub-Informationen.
	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.bolt.EventHubBolt** und Übergabe der **EventHubBoltConfig**-Instanz.

6. Suchen Sie den folgenden Code.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Ersetzen Sie ihn durch Folgendes.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Dieser Code weist die Topologie an, den **JavaComponentConstructor** aus dem vorherigen Schritt als Bolt zu verwenden. Die Komponente kann in dieser Topologie mit dem Anzeigenamen "EventHubBolt" referenziert werden. Der Parallelitätshinweis ist auf die Anzahl der Partitionen für den Event Hub festgelegt, und die Komponente abonniert Daten, die durch den Spout ("Spout") erzeugt werden.

An dieser Stelle ist die Arbeit mit **Program.cs** abgeschlossen. Die Topologie wurde definiert, nun müssen Sie **Spout.cs** so ändern, dass Daten in einem Format erzeugt werden, das der Event Hub-Bolt verwenden kann.

### Ändern des Spouts

Der Event Hub-Bolt erwartet einen einzelnen Zeichenfolgenwert, den er an Event Hub weiterleitet. Im folgenden Beispiel ändern Sie die standardmäßige **Spout.cs**-Datei, um eine JSON-Zeichenfolge zu erzeugen.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubWriter**, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie nach dem **Json.Net**-Paket, und fügen Sie es zur Projektmappe hinzu. So lassen sich ganz einfach JSON-Daten erstellen, die mithilfe des Bolts an Event Hub gesendet werden.

1. Öffnen Sie **Spout.cs**, und fügen Sie am Anfang der Datei folgenden Code ein.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Dies vereinfacht die Arbeit mit JSON-Daten.

3. Suchen Sie den folgenden Code.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Ersetzen Sie ihn durch Folgendes.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Dies ändert die Definition der durch den Spout erstellten Daten, sodass **string**-Daten und ein **Benutzerdefiniertes JSON-Serialisierungsprogramm** verwendet werden.

2. Ersetzen Sie die **NextTuple**-Methode durch Folgendes.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Dadurch werden nach dem Zufallsprinzip eine Geräte-ID und ein Wert erstellt, und anschließend wird Json.net verwendet, um mithilfe dieser Werte ein JSON-Objekt auszugeben.

3. Speichern Sie die **Spout.cs**-Datei.

Nun verfügen Sie über eine grundlegende Topologie, die Daten nach dem Zufallsprinzip erzeugt und sie mithilfe des Event Hub-Bolts in Event Hub speichert. Als Nächstes erstellen Sie das reader-Element.

## Erstellen von EventHubReader

In diesem Abschnitt erstellen Sie eine Topologie, die Daten mithilfe des Event Hub-Spouts aus Event Hub liest.

2. Öffnen Sie Visual Studio, wählen Sie **Datei**, **Neu**, und klicken Sie dann auf **Projekt**.

3. Erweitern Sie im Dialogfeld **Neues Projekt** die Option **Installiert**, **Vorlagen**, und wählen Sie dann **HDInsight** aus. Wählen Sie in der Liste der Vorlagen die Option **Storm-Anwendung** aus. Geben Sie unten im Dialogfeld **EventHubReader** als Namen der Anwendung ein.

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
	</table>

	Geben Sie als **TableName** den Namen der Tabelle ein, in der Ereignisse gespeichert werden sollen.

	Diese Werte werden von der Topologie zur Kommunikation mit Event Hub und dem Tabellenspeicher verwendet.

4. Speichern und schließen Sie die Eigenschaftenseite.

### Definieren der Topologie

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Spout.cs**, und wählen Sie **Löschen**. Da Sie den Java Event Hub-Spout verwenden, benötigen Sie diese Datei nicht.

2. Öffnen Sie die Datei **Program.cs**, und fügen Sie direkt unterhalb der Zeile `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` den folgenden Code ein.

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

	Die Anzahl der Partitionen wird eingelesen und einer lokalen Variablen zugewiesen, da sie mehrfach verwendet wird.

	Der `JavaComponentConstructor` definiert, wie der Java-Spout zur Laufzeit konstruiert wird. In diesem Fall verwenden Sie die <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>, um den Spout mit den zuvor hinzugefügten Event Hub-Konfigurationsinformationen zu konfigurieren. Genauer gesagt wird dieser Code von HDInsight zur Laufzeit für Folgendes verwendet:

	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.spout.EventHubSpoutConfig** mithilfe der von Ihnen bereitgestellten Event Hub-Informationen.
	
	* Erstellen einer neuen Instanz von **com.microsoft.eventhubs.spout.EventHubSpout** und Übergabe der **EventHubSpoutConfig**-Instanz.

5. Suchen Sie den folgenden Code.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Ersetzen Sie ihn durch Folgendes.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	Dieser Code weist die Topologie an, den **JavaComponentConstructor** aus dem vorherigen Schritt als Spout zu verwenden und mit dem Namen "EventHubSpout" zu versehen. Der Code legt ebenfalls den Parallelitätshinweis für diese Komponente auf die Anzahl der Partitionen in Event Hub fest. 

2. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu.

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Dieser Code erstellt ein benutzerdefiniertes Serialisierungsprogramm, mit dem die durch den Java-Spout erzeugten Informationen in das JSON-Format serialisiert werden.

3. Suchen Sie den folgenden Code.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Ersetzen Sie ihn durch Folgendes.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Dieser Code weist die Topologie an, einen (in Bolt.cs definierten) Bolt zu verwenden. Diese Ergänzungen weisen die Topologie an, das benutzerdefinierte Serialisierungsprogramm für Daten zu verwenden, die von dieser Komponente genutzt werden und aus **EventHubSpout** (dem Java-Spout) stammen.

An dieser Stelle ist die Arbeit mit **Program.cs** abgeschlossen. Die Topologie wurde definiert, nun müssen Sie eine Hilfsklasse erstellen, um Daten in den Tabellenspeicher zu schreiben. Anschließend müssen Sie **Bolt.cs** so ändern, dass die durch den Spout erzeugten Daten interpretiert werden können.

### Erstellen einer Hilfsklasse

Damit Daten in den Tabellenspeicher geschrieben werden können, müssen Sie eine Klasse erstellen, die die zu schreibenden Daten beschreibt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **Hinzufügen**, **Neue Klasse**. Geben Sie der neuen Klasse den Namen **Devices.cs**.

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

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie nach dem **Json.Net**-Paket, und fügen Sie es zur Projektmappe hinzu. Auf diese Weise lassen sich die aus dem Spout übermittelten JSON-Daten einfach verarbeiten. Fügen Sie auch ein **Microsoft Azure Storage**-Paket hinzu, das das Schreiben von Daten in den Tabellenspeicher ermöglicht.

1. Öffnen Sie **Bolt.cs**, und fügen Sie am Anfang der Datei Folgendes ein.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Dies vereinfacht die Arbeit mit JSON-Daten aus dem Bolt und das Schreiben von Daten in den Tabellenspeicher.

2. Suchen Sie die Anweisung `private int count;`, und ersetzen Sie sie durch Folgendes.

        private CloudTable table;

	Diese Anweisung wird beim Herstellen einer Verbindung mit der Tabelle verwendet.

4. Suchen Sie den folgenden Code.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Ersetzen Sie ihn durch Folgendes.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Dieser Code weist den Bolt an, dass ein **string**-Wert anstelle eines **int**-Werts gesendet wird und die Daten mithilfe von **CustomizedInteropJSONDeserializer** deserialisiert werden sollen.

3. Fügen Sie unmittelbar nach dem vorherigen Code Folgendes hinzu.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Dieser Code stellt unter Verwendung der zuvor konfigurierten Verbindungszeichenfolge eine Verbindung mit der Tabelle **events** her. Wenn die Tabelle nicht vorhanden ist, wird sie erstellt.

2. Suchen Sie die Methode **Execute**, und ersetzen Sie sie durch Folgendes.

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
            }
        }

	Diese Methode verwendet Json.net, um die JSON-Daten aus dem Spout zu analysieren, und wählt anschließend die Felder **deviceId** und **deviceValue** aus. Anschließend wird ein neues **Device**-Objekt erstellt, wobei während der Initialisierung die **deviceId** verwendet wird, um den Partitionsschlüssel für die Tabelle festzulegen. Danach wird der Wert auf **deviceValue** festgelegt, und die Entität wird in die Tabelle eingefügt.

Nun verfügen Sie über eine vollständige Topologie, die Daten aus Event Hub liest und sie im Tabellenspeicher in einer Tabelle mit der Bezeichnung **events** speichert.

## Bereitstellen der Topologien

1. Öffnen Sie die Projektmappe **EventHubReader**. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubReader**, und wählen Sie **An Storm in HDInsight übermitteln** aus.

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Wählen Sie im Dialogfeld **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen**, klicken Sie auf **Java-Dateipfade** und **...**, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** enthält. Wählen Sie anschließend **Übermitteln** aus.

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Sobald die Topologie übermittelt wurde, wird der **Storm-Topologie-Viewer** angezeigt. Wählen Sie auf der linken Seite des Dialogfelds die Topologie **EventHubReader** aus, um die Statistiken für diese Topologie anzuzeigen. Aktuell sollte dort nichts angezeigt werden, da noch keine Ereignisse in Event Hub geschrieben wurden.

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Öffnen Sie die Projektmappe **EventHubWriter**. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **EventHubWriter**, und wählen Sie **An Storm in HDInsight übermitteln** aus.

2. Wählen Sie im Dialogfeld **Topologie übermitteln** Ihren **Storm-Cluster** aus. Erweitern Sie **Zusätzliche Konfigurationen**, klicken Sie auf **Java-Dateipfade** und **...**, und wählen Sie das Verzeichnis aus, das die zuvor heruntergeladene Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** enthält. Wählen Sie anschließend **Übermitteln** aus.

5. Sobald die Topologie übermittelt wurde, aktualisieren Sie die Topologieliste im **Storm-Topologie-Viewer**, um sicherzustellen, dass beide auf dem Cluster ausgeführt werden.

6. Sobald beide ausgeführt werden, wählen Sie **Server-Explorer**, erweitern Sie **Azure** und **Speicher**, und erweitern Sie anschließend das Speicherkonto, das Sie zuvor erstellt haben. Erweitern Sie im Speicherkonto den Eintrag **Tabellen**. Doppelklicken Sie abschließend auf die Tabelle **events**, um sie zu öffnen. Hier sollten die Daten aus der **EventHubReader**-Topologie angezeigt werden, die in der Tabelle gespeichert wurden.

	* Die Ereignisse werden durch die **EventHubWriter**-Topologie erzeugt, die sie in Event Hub schreibt.

	* **EventHubReader** liest die Ereignisse aus Event Hub und speichert sie im Tabellenspeicher in der Tabelle **events**.

## Beenden der Topologien

Um die Topologien zu beenden, wählen Sie jede Topologie im **Storm-Topologie-Viewer**, und wählen Sie **Beenden**.

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## Zusammenfassung

In diesem Dokument haben Sie erfahren, wie Sie die Java Event Hub-Spouts und -Bolts aus einer C#-Topologie verwenden, um mit Daten in Azure Event Hub zu arbeiten. Weitere Informationen zum Erstellen von C#-Topologien finden Sie in folgenden Themen.

* [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/)

* [HDInsight-Storm-Beispiele](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
