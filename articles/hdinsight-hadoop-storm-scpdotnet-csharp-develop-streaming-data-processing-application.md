<properties title="Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm in HDInsight" pageTitle="Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und Storm | Azure" description="Erfahren Sie, wie Sie Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm in HDInsight entwickeln k&ouml;nnen." services="hdinsight" solutions="" documentationCenter="" authors="Qianlin Xia" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/15/2014" ms.author="qixia" />

# Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C\# mit Storm in HDInsight

## Einführung in SCP

SCP ist eine Plattform zur Erstellung zuverlässiger, konsistenter und leistungsfähiger Anwendungen für die Echtzeit-Datenverarbeitung. Sie basiert auf Storm (http://storm.incubator.apache.org/) – einem Streamverarbeitungssystem, welches von den OSS-Communities entworfen wurde. Storm wurde von Nathan Marz entwickelt, und der Quellcode wurde von Twitter freigegeben. Es nutzt ZooKeeper (http://zookeeper.apache.org/), ein weiteres Apache-Projekt zur Umsetzung einer extrem zuverlässigen verteilten Koordinations- und Zustandsverwaltung.

Das SCP-Projekt hat nicht nur Storm für Windows portiert, sondern auch Erweiterungen und Anpassungen für das Windows-Ökosystem hinzugefügt. Die Erweiterungen beinhalten Verbesserungen und Bibliotheken für .NET-Entwickler, und die Anpassungen umfassen die Windows-basierte Bereitstellung.

Diese Erweiterungen und Anpassungen wurden ermöglicht, ohne dafür die OSS-Projekte verzweigen zu müssen. Außerdem wurden abgeleitete Ökosysteme genutzt, die auf Storm aufbauen.

## Verarbeitungsmodell

Die Daten in SCP sind als fortlaufende Streams von Tupeln modelliert. Normalerweise landen die Tupel zunächst in einer Warteschlange. Dort werden sie abgerufen und von der Geschäftslogik in einer Storm-Topologie transformiert. Anschließend kann die Ausgabe als Tupel an ein anderes SCP-System weitergeleitet oder in einen Speicher geschrieben werden, z. B. in ein verteiltes Dateisystem oder eine SQL-Datenbank.

![][0]

Eine Anwendungstopologie in Storm definiert einen Berechnungsgraphen. Jeder Knoten in einer Topologie enthält Verarbeitungslogik, und Verknüpfungen zwischen Knoten definieren den Datenfluss. Die Eintrittsknoten für Daten in die Topologie heißen Spouts und können zur Sequenzierung der Daten verwendet werden. Die Eingabedaten können aus Protokolldateien, transaktionalen Datenbanken, Systemleistungsindikatoren usw. stammen. Die Knoten mit Eingangs- und Ausgangsdatenfluss heißen Bolts und erledigen die eigentliche Datenfilterung sowie Auswahl- und Aggregationsaufgaben.

SCP unterstützt die Datenverarbeitung nach den Prinzipien "Best Effort", "At-Least-Once" und "Exactly Once". In verteilten Anwendungen für die Streamingverarbeitung können verschiedene Fehler bei der Verarbeitung auftreten, wie z. B. Netzwerk- und Computerausfälle, Fehler im Benutzercode usw.. Mit dem "At-Least-Once"-Modell wird sichergestellt, dass alle Daten mindestens einmal verarbeitet werden, indem im Fehlerfall dieselben Daten erneut abgespielt werden. Das "At-Least-Once"-Modell ist einfach und zuverlässig und eignet sich für die meisten Anwendungen. Wenn jedoch zum Beispiel eine exakte Zählung erforderlich ist, reicht das "At-Least-Once"-Modell nicht aus, da dieselben Daten möglicherweise mehr als einmal in der Anwendungstopologie abgespielt werden. Für diese Fälle wurde das "Exactly-Once"-Modell entwickelt. Dieses Modell garantiert ein korrektes Ergebnis, selbst wenn die Daten mehrfach abgespielt und verarbeitet wurden.

Mit SCP können .NET-Entwickler Echtzeit-Anwendungen für die Datenverarbeitung entwickeln und dabei auf das JVM-basierte Storm zurückgreifen. .NET und JVM kommunizieren über ein lokales TCP-Socket. Jeder Spout/Bolt ist genauer gesagt ein .Net-/Java-Prozesspaar, wobei die Benutzerlogik in einem .NET-Prozess als Plug-In ausgeführt wird.

Die Erstellung einer Datenverarbeitungsanwendung mit SCP umfasst mehrere Schritte:

-   Design und Implementierung der Spouts zum Abrufen von Daten aus der Warteschlange.
-   Design und Implementierung der Bolts zum Verarbeiten der Eingangsdaten und zum Speichern in externe Speicherformen wie z. B. eine Datenbank.
-   Design der Topologie, und anschließend Übermitteln und Ausführen der Topologie. Die Topologie definiert Knoten und den Datenfluss zwischen den Knoten. SCP stellt diese Topologie-Definition in einem Storm-Cluster bereit, in dem jeder dieser Knoten in einem logischen Knoten ausgeführt wird. Failover und Skalierung werden vom Storm-Taskplaner übernommen.

Dieses Dokument verwendet einige einfache Beispiele, um die Erstellung von Datenverarbeitungsanwendungen mit SCP zu veranschaulichen.

## SCP-Plug-In-Schnittstelle

SCP-Plug-Ins (oder -Anwendungen) sind eigenständige EXE-Dateien, die sowohl in Visual Studio während der Entwicklungsphase als auch in der Storm-Pipeline nach der Bereitstellung in der Produktionsumgebung ausgeführt werden können. Diese SCP-Plug-Ins sind genauso einfach zu schreiben wie andere gewöhnliche Windows-Konsolenanwendungen. Die SCP.NET-Plattform deklariert Schnittstellen für Spouts/Bolts, und der Code des Benutzer-Plug-Ins implementiert diese Schnittstellen. Dank dieses Designs können sich die Benutzer auf die Geschäftslogik konzentrieren, während andere Aufgaben von der SCP.NET-Plattform erledigt werden.

Der Code des Benutzer-Plug-Ins sollte eine der folgenden Schnittstellen implementieren, je nachdem, ob die Topologie transaktional ist und ob es sich um einen Spout oder einen Bolt handelt.

-   ISCPSpout
-   ISCPBolt
-   ISCPTxSpout
-   ISCPBatchBolt

### ISCPPlugin

ISCPPlugin ist die gemeinsame Schnittstelle für alle Arten von Plug-Ins. Momentan ist dies noch eine Dummy-Schnittstelle.

    public interface ISCPPlugin
    {
    }  

### ISCPSpout

ISCPSpout ist die Schnittstelle für nichttransaktionale Spouts.

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Beim Aufruf von NextTuple() kann der C\#-Benutzercode ein oder mehrere Tupel zurückgeben. Wenn nichts zurückzugeben ist, wird die Methode verlassen, ohne etwas zurückzugeben. Beachten Sie, dass NextTuple(), Ack() und Fail() in einer engen Schleife in einem einzigen Thread im C\#-Prozess aufgerufen werden. Wenn keine Tupel ausgegeben werden, sollte NextTuple für kurze Zeit (z. B. 10 Millisekunden) in den Ruhezustand versetzt werden, um Prozessorleistung zu sparen.

Ack() und Fail() werden nur aufgerufen, wenn der Ack-Mechanismus in der Definitionsdatei aktiviert wurde. "seqId" wird verwendet, um das Tupel zu identifizieren, für das Ack bzw. Fail aufgerufen wird. Wenn also Ack in einer nichttransaktionalen Topologie verwendet wird, sollte die folgende emit-Funktion im Spout verwendet werden:

        public abstract void Emit(string streamId, List<object> values, long seqId);  

Wenn Ack in einer nichttransaktionalen Topologie nicht unterstützt wird, können Ack() und Fail() als leere Funktionen definiert werden.

Der Eingabeparameter "parms" in diesen Funktionen ist einfach ein leeres Wörterbuch. Diese Parameter sind für den zukünftigen Gebrauch reserviert.

### ISCPBolt

ISCPBolt ist die Schnittstelle für nichttransaktionale Bolts.

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Wenn ein neues Tupel verfügbar ist, wird die Execute()-Funktion für dessen Verarbeitung aufgerufen.

### ISCPTxSpout

ISCPSpout ist die Schnittstelle für transaktionale Spouts.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Ebenso wie beim nichttransaktionalen Gegenstück werden NextTx(), Ack() und Fail() in einer engen Schleife in einem einzigen Thread im C\#-Prozess aufgerufen werden. Wenn keine Daten ausgegeben werden, sollte NextTx für kurze Zeit (z. B. 10 Millisekunden) in den Ruhezustand versetzt werden, um Prozessorleistung zu sparen.

NextTx() wird aufgerufen, um eine neue Transaktion zu beginnen. Der Ausgangsparameter "seqId" identifiziert die Transaktion und wird ebenfalls in Ack() und Fail() verwendet. In NextTx() kann der Benutzer Daten an die Java-Komponente ausgeben. Die Daten werden in ZooKeeper gespeichert, um erneut abgespielt werden zu können. Da ZooKeeper eine sehr eingeschränkte Kapazität hat, sollten die Benutzer in transaktionalen Spouts nur Metadaten ausgeben, und keine großen Datenmengen.

Storm spielt eine Transaktion im Fehlerfall automatisch erneut ab. Fail() muss daher bei der normalen Verarbeitung nicht aufgerufen werden. Falls SCP jedoch die vom transaktionalen Spout ausgegebenen Metadaten überprüft, kann Fail() für ungültige Metadaten aufgerufen werden.

Der Eingabeparameter "parms" in diesen Funktionen ist einfach ein leeres Wörterbuch. Diese Parameter sind für den zukünftigen Gebrauch reserviert.

### ISCPBatchBolt

ISCPBatchBolt ist die Schnittstelle für transaktionale Bolts.

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Execute() wird aufgerufen, wenn der Bolt ein neues Tupel empfängt. FinishBatch() wird aufgerufen, wenn die Transaktion beendet ist. Der Eingabeparameter "parms" ist für den zukünftigen Gebrauch reserviert.

Für transaktionale Topologien gibt es ein wichtiges Konzept: "StormTxAttempt". Es enthält zwei Felder: "TxId" und "AttemptId". "TxId" identifiziert eine bestimmte Transaktion. Für jede Transaktion gibt es möglicherweise mehrere Versuche, wenn die Transaktion fehlschlägt und erneut abgespielt wird. SCP.NET erstellt ein neues ISCPBatchBolt für jeden neuen "StormTxAttempt", analog zum Verhalten von Storm in der Java-Komponente. Dieses Design ermöglicht die parallele Transaktionsverarbeitung. Beachten Sie, dass das entsprechende ISCPBatchBolt-Objekt nach Ende des Transaktionsversuchs vernichtet und vom Garbage Collector entsorgt wird.

## Objektmodell

SCP.NET enthält außerdem einen einfachen Satz von Schlüsselobjekten für die Entwicklung. Diese Objekte heißen **Context**, **StateStore** und **SCPRuntime**. Diese Objekte werden im Rest dieses Abschnitts besprochen.

### Context

Context stellt eine Ausführungsumgebung für die Anwendung bereit. Jede ISCPPlugin-Instanz (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) gehört zu einer Context-Instanz. Die vom Context bereitgestellten Funktionen sind in zwei Teile unterteilt: (1) der statische Teil, der im gesamten C\#-Prozess verfügbar ist, (2) der dynamische Teil, der nur für die jeweilige Context-Instanz verfügbar ist.

#### Statischer Teil

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;
    public static Config Config { get; set; }
    public static TopologyContext TopologyContext { get; set; }  

"Logger" wird zu Protokollierungszwecken bereitgestellt.

"pluginType" gibt den Plug-In-Typ des C\#-Prozesses an. Falls der C\#-Prozess im lokalen Testmodus läuft (ohne Java), ist der Plug-In-Typ "SCP\_NET\_LOCAL".

    public enum SCPPluginType
    {
        SCP_NET_LOCAL = 0,
        SCP_NET_SPOUT = 1,
        SCP_NET_BOLT = 2,
        SCP_NET_TX_SPOUT = 3,
        SCP_NET_BATCH_BOLT = 4
    }  

"Config" dient zum Abrufen von Konfigurationsparametern aus der Java-Komponente. Diese Parameter werden von der Java-Komponente beim Initialisieren des Plug-Ins übergeben. Die "Config"-Parameter sind in zwei Teile unterteilt: "stormConf” und "pluginConf”

    public Dictionary<string, Object> stormConf { get; set; }
    public Dictionary<string, Object> pluginConf { get; set; }  

"stormConf” enthält die von Storm definierten Parameter, und "pluginConf” enthält die von SCP definierten Parameter. Beispiel:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
    }  

"TopologyContext” wird bereitgestellt, um den Topologie-Kontext abzurufen, und eignet sich hauptsächlich für Komponenten mit Mehrfach-Parallelität. Beispiel:

    //demo how to get TopologyContext info
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

#### Dynamischer Teil

Die folgenden Schnittstellen gehören zu einer bestimmten Context-Instanz. Die Context-Instanz wird von der SCP-NET-Plattform erstellt und an den Benutzercode übergeben:

    /* Declare the Output and Input Stream Schemas */
    public void DeclareComponentSchema(ComponentStreamSchema schema);
    /* Emit tuple to default stream. */
    public abstract void Emit(List<object> values);

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

Für nichttransaktionale Spouts, die Ack unterstützen, wird die folgende Methode bereitgestellt:

    /* for non-transactional Spout which supports ack */
    public abstract void Emit(string streamId, List<object> values, long seqId);

Für nichttransaktionale Bolts, die Ack unterstützen, sollte für empfangene Tupel Ack() bzw. Fail() explizit aufgerufen werden. Bei der Ausgabe neuer Tupel müssen außerdem die Anker des neuen Tupel angegeben werden. Die folgenden Methoden werden bereitgestellt.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);  

### StateStore

**StateStore** bietet Metadatendienste, monotone Sequenzgenerierung und Koordinierung ohne Wartezeiten. Parallelitätsabstraktionen auf höheren Ebenen können auf **StateStore** aufgebaut werden, inklusive verteilte Sperren, verteilte Warteschlangen, Schranken und Transaktionsdienste.

SCP-Anwendungen können das **State**-Objekt verwenden, um bestimmte Daten in ZooKeeper zu persistieren, insbesondere für transaktionale Topologien. Auf diese Weise kann der transaktionale Spout nach einem Absturz und Neustart die benötigten Informationen von ZooKeeper abrufen und die Pipeline neu starten.

Das **StateStore**-Objekt besteht hauptsächlich aus diesen Methoden:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states
    /// </summary>
    /// <returns>Uncommited States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns>
    public IEnumerable<Registry> Commited();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

Das **State**-Objekt besteht hauptsächlich aus diesen Methoden:

    /// <summary>
    /// Set the status of the state object to commit
    /// </summary>
    public void Commit(bool simpleMode = true);

    /// <summary>
    /// Set the status of the state object to abort
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key
    /// </summary>
    /// <param name="key">Key</param>
    /// <param name="attribute">State Attribute</param>
    public void PutAttribute<T>(string key, T attribute);

    /// <summary>
    /// Get the attribute value associated with the given key
    /// </summary>
    /// <param name="key">Key</param>
    /// <returns>State Attribute</returns>
    public T GetAttribute<T>(string key);  

Wenn simpleMode auf "true" gesetzt ist, löscht die Commit()-Methode einfach den entsprechenden ZNode in ZooKeeper. Andernfalls wird der aktuelle ZNode zurückgegeben und ein neuer Knoten im COMMITTED\_PATH hinzugefügt.

### SCPRuntime

SCPRuntime stellt die folgenden beiden Methoden bereit.

        public static void Initialize();

        public static void LaunchPlugin(newSCPPlugin createDelegate);

Initialize() wird verwendet, um die SCP-Laufzeitumgebung zu initialisieren. In dieser Methode verbindet sich der C\#-Prozess mit der Java-Komponente und ruft Konfigurationsparameter und den Topologie-Kontext ab.

LaunchPlugin() wird verwendet, um die Schleife zur Nachrichtenverarbeitung zu starten. In dieser Schleife empfängt das C\#-Plug-In Nachrichten von der Java-Komponente (inklusive Tupel und Steuersignale) und verarbeitet diese Nachrichten. Dabei wird möglicherweise die vom Benutzercode bereitgestellte Schnittstellenmethode aufgerufen. Der Eingabeparameter für die Methode LaunchPlugin() ist ein Delegat und kann ein Objekt zurückgeben, das eine der Schnittstellen ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt implementiert.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);  

Für ISCPBatchBolt können wir "StormTxAttempt” aus "parms” abrufen, um zu ermitteln, ob es sich um einen erneut abgespielten Versuch handelt. Dies wird normalerweise im Commit-Bolt ausgeführt und wird im "HelloWorldTx”-Beispiel demonstriert.

Die SCP-Plug-Ins können im Allgemeinen in zwei Modi ausgeführt werden:

1.  Lokaler Testmodus: In diesem Modus werden die SCP-Plug-Ins (der C\#-Benutzercode) in Visual Studio während der Entwicklungsphase ausgeführt. "LocalContext” kann in diesem Modus verwendet werden und stellt Methoden bereit, um die ausgegebenen Tupel in lokale Dateien zu serialisieren und anschließend zurück in den Speicher zu lesen.

        public interface ILocalContext
        {
            List<SCPTuple> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);
            void ReadFromFileToMsgQueue(string filepath);
        }  

2.  Normaler Modus: In diesem Modus werden die SCP-Plug-Ins vom Storm-Javaprozess gestartet.

    Hier sehen Sie ein Beispiel für den Start des SCP-Plug-Ins:

        namespace Scp.App.HelloWorld
        {
          public class Generator : ISCPSpout
          {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
              return new Generator(ctx);
            }
          }

          class HelloWorld
          {
            static void Main(string[] args)
            {
              /* Setting the environment variable here can change the log file name */
              System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

              SCPRuntime.Initialize();
              SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
        }
          }
        }

## Sprache für die Topologie-Spezifikation

SCP Topology Specification ist eine domänenspezifische Sprache zum Beschreiben und Konfigurieren von SCP-Topologien. Diese Sprache basiert auf Storms Clojure DSL (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) und wird durch SCP erweitert.

Topologie-Spezifikationen können direkt an das Storm-Cluster zur Ausführung mit dem ***runspec***-Befehl übermittelt werden.

SCP.NET hat die folgenden Funktionen zum Definieren der transaktionalen Topologie hinzugefügt:

| Neue Funktionen        | Parameter      | Beschreibung                                                                                                                              |
|------------------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| **tx-topolopy**        | Topologie-Name 
                           Spout-Map      
                           Bolt-Map       | Definiert eine transaktionale Topologie mit dem angegebenen Namen, der Spouts-Definitions-Map und der Bolts-Definitions-Map               |
| **scp-tx-spout**       | Exec-Name      
                          Args            
                          Felder          | Definiert einen transaktionalen Spout. Führt die Anwendung mit ***Exec-Name*** aus und verwendet dabei die Argumente ***Args***.          
                                           ***Felder*** sind die Ausgabe-Felder für den Spout                                                                                         |
| **scp-tx-batch-bolt**  | Exec-Name      
                          Args            
                          Felder          | Definiert einen transaktionalen Stapel-Bolt. Führt die Anwendung mit ***Exec-Name*** aus und verwendet dabei die Argumente ***Args***.    
                                           Felder sind die Ausgabe-Felder für den Spout.                                                                                              |
| **scp-tx-commit-bolt** | Exec-Name      
                          Args            
                          Felder          | Definiert einen transaktionalen Committer-Bolt. Führt die Anwendung mit ***Exec-Name*** aus und verwendet dabei die Argumente ***Args***. 
                                           Felder sind die Ausgabe-Felder für den Bolt                                                                                                |
| **nontx-topolopy**     | Topologie-Name 
                           Spout-Map      
                           Bolt-Map       | Definiert eine nichttransaktionale Topologie mit dem angegebenen Namen, der Spouts-Definitions-Map und der Bolts-Definitions-Map          |
| **scp-spout**          | Exec-Name      
                          Args            
                          Felder          
                          Parameter       | Definiert einen nichttransaktionalen Spout. Führt die Anwendung mit ***Exec-Name*** aus und verwendet dabei die Argumente ***Args***.     
                                           ***Felder*** sind die Ausgabe-Felder für den Spout.                                                                                        
                                           Die ***Parameter*** sind optional und werden für bestimmte Parameter wie z. B. "nontransactional.ack.enabled" verwendet.                   |
| **scp-bolt**           | Exec-Name      
                          Args            
                          Felder          
                          Parameter       | Definiert einen nichttransaktionalen Bolt. Führt die Anwendung mit ***Exec-Name*** aus und verwendet dabei die Argumente ***Args***.      
                                           ***Felder*** sind die Ausgabe-Felder für den Bolt.                                                                                         
                                           Die ***Parameter*** sind optional und werden für bestimmte Parameter wie z. B. "nontransactional.ack.enabled" verwendet.                   |

In SCP.NET sind die folgenden Schlüsselwörter definiert:

| Schlüsselwort | Beschreibung                                                               |
|---------------|----------------------------------------------------------------------------|
| **:name**     | Definiert den Namen der Topologie                                          |
| **:topology** | Definiert die Topologie mit eingebauten und den oben genannten Funktionen. |
| **:p**        | Definiert den Parallelitäts-Hinweis für die einzelnen Spouts bzw. Bolts.   |
| **:config**   | Definiert Konfigurationsparameter bzw. aktualisiert vorhandene Parameter   |
| **:schema**   | Definiert das Schema des Streams.                                          |

Einige häufig verwendete Parameter:

| Parameter                          | Beschreibung                                                          |
|------------------------------------|-----------------------------------------------------------------------|
| **"plugin.name"**                  | .exe-Dateiname des C\#-Plug-Ins                                       |
| **"plugin.args"**                  | Plug-In-Argumente                                                     |
| **"output.schema"**                | Ausgabeschema                                                         |
| **"nontransactional.ack.enabled"** | Gibt an, ob Ack in einer nichttransaktionalen Topologie aktiviert ist |

Der runspec-Befehl wird zusammen mit den Bits bereitgestellt und wird wie folgt verwendet:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Der ***resource-dir***-Parameter ist optional und muss angegeben werden, wenn Sie eine C\#-Anwendung als Plug-In verwenden möchten. Das angegebene Verzeichnis enthält in diesem Fall die Anwendung sowie deren Abhängigkeiten und Konfiguration.

Der ***classpath***-Parameter ist ebenfalls optional. Mit diesem Parameter kann der Java-Classpath angegeben werden, wenn die Spezifikationsdatei Java-Spouts oder -Bolts enthält.

## Verschiedene Features

### Deklarieren von Ein- und Ausgabeschema

Benutzer können Tupel im C\#-Prozess ausgeben, die Plattform serialisiert die Tupel in byte[], überträgt diese an die Java-Komponente, und Storm überträgt das Tupel an die Ziele. Gleichzeitig empfängt der C\#-Prozess in der Downstreamkomponente das Tupel von der Java-Komponente zurück und konvertiert es je nach Plattform in die Ausgangstypen. All diese Operationen sind von der Plattform verborgen.

Um Serialisierung und Deserialisierung zu unterstützen, muss der Benutzercode das Schema für Ein- und Ausgaben deklarieren.

Das Schema für Ein- und Ausgabestream wird als Wörterbuch definiert. Der Schlüssel ist die StreamId und der Wert sind die Typen der Spalten. In der Komponente können Multi-Streams deklariert werden.

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

Im Context-Objekt wurde die folgende API hinzugefügt:

     public void DeclareComponentSchema(ComponentStreamSchema schema)

Der Benutzercode muss sicherstellen, dass die ausgegebenen Tupel dem für den Stream definierten Schema folgen. Andernfalls löst das System eine Ausnahme zur Laufzeit aus.

### Unterstützung für Multi-Streams

SCP unterstützt Benutzercode, der mit mehreren separaten Streams gleichzeitig kommunizieren kann. Diese Unterstützung erfolgt im Context-Objekt: Die Emit-Methode nimmt einen optionalen StreamId-Parameter entgegen.

IM SCP.NET-Context-Objekt wurden zwei Methoden hinzugefügt. Diese Methoden geben Tupel aus, mit denen die StreamId angegeben wird. Die StreamId ist eine Zeichenfolge und muss in C\# und in der Topologie-Definition übereinstimmen.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);  

Ausgaben an nicht existierende Streams lösen Ausnahmen zur Laufzeit aus.

### Gruppieren von Feldern

Die integrierte Gruppierung von Feldern in Storm funktioniert in SCP.NET nicht korrekt. Im Java-Proxy sind alle Felder vom Datentyp byte[], und die Gruppierung der Felder erfolgt anhand des Hashcodes des byte[]-Objekts. Der Hashcode des byte[]-Objekts ist die Adresse des Objekts im Speicher. Die Gruppierung ist also falsch für zwei byte[]-Objekte, die zwar denselben Inhalt, jedoch nicht dieselbe Adresse haben.

SCP.NET fügt eine angepasste Gruppierungsmethode hinzu und verwendet den Inhalt des byte[] für die Gruppierung. Die Syntax der **SPEC**-Datei ist wie folgt:

    (bolt-spec
        {
            “spout_test” (scp-field-group :non-tx [0,1])
        }
        …
    )  

Hier gilt Folgendes:

1.  "scp-field-group" steht für "Customized field grouping implemented by SCP" (Von SCP implementierte angepasste Feldgruppierung).
2.  ":tx” bzw. ":non-tx” gibt an, ob es sich um eine transaktionale Topologie handelt. Wir brauchen diese Information, da tx- und non-tx-Topologien einen unterschiedlichen Start-Index haben.
3.  [0,1] bedeutet ein HashSet mit Feld-Ids, beginnend ab 0.

### Hybrid-Topologie

Die systemeigene Storm-Version ist in Java geschrieben, und das wichtigste Feature des SCP-Projekts ist die Möglichkeit für unsere Benutzer, ihre Geschäftslogik in C\# schreiben zu können. Wir unterstützen jedoch auch Hybrid-Topologien, die neben C\#-Spouts und -Bolts auch Java-Spouts und -Bolts enthalten.

#### Definieren von Java-Spouts/-Bolts in der Spezifikationsdatei

"scp-spout" und "scp-bolt" in der Spezifikationsdatei können auch verwendet werden, um Java-Spouts und -Bolts zu definieren, wie im folgenden Beispiel gezeigt:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)  

"microsoft.scp.example.HybridTopology.Generator” ist in diesem Fall der Klassenname des Java-Spouts.

#### Angeben des Java-Classpath im runSpec-Befehl

Wenn Sie eine Topologie mit Java-Spouts oder -Bolts übermitteln möchten, sollten Sie die Java-Spouts bzw. -Bolts zunächst kompilieren und die Jar-Dateien erstellen. Anschließend geben Sie den Java-Classpath mit den Jar-Dateien an, wenn Sie die Topologie übermitteln. Beispiel:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  

"examples\\HybridTopology\\java\\target" ist in diesem Fall der Ordner, der die Jar-Datei enthält.

#### Serialisierung und Deserialisierung zwischen Java und C

In Hybrid-Topologien müssen wir die Serialisierung beliebiger Objekte in der Java-Komponente und die Deserialisierung in der C\#-Komponente unterstützen, und umgekehrt. **Aktuell unterstützen wir nur Java-Spouts und C\#-Bolts**. Weitere Komponenten sind für eine zukünftige Version geplant.

Zunächst geben wir die Standardimplementierung für die Serialisierung in der Java-Komponente und Deserialisierung in der C\#-Komponente an. Die Serialisierungsmethode in der Java-Komponente kann in der SPEC-Datei angegeben werden:

      (scp-bolt
        {
          "plugin.name" "HybridTopology.exe"
          "plugin.args" ["displayer"]
          "output.schema" {}
          "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
        })  

Die Deserialisierungsmethode in der C\#-Komponente sollte im C\#-Benutzercode angegeben werden:

    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
    inputSchema.Add("default", new List<Type>() { typeof(Person) });
    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
    this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

Diese Standardimplementierung sollte die meisten Fälle verarbeiten können, wenn der Datentyp nicht zu komplex ist. Wenn ein Datentyp eines Benutzer zu komplex ist oder die Leistung unserer Standardimplementierung die Anforderungen des Benutzers nicht erfüllt, kann dieser stattdessen eine eigene Implementierung verwenden.

Die Serialisierungs-Schnittstelle in der Java-Komponente ist wie folgt definiert:

    public interface ICustomizedInteropJavaSerializer {
        public void prepare(String[] args);
        public List<ByteBuffer> serialize(List<Object> objectList);
    }  

Die Deserialisierungs-Schnittstelle in der C\#-Komponente ist wie folgt definiert:

    public interface ICustomizedInteropCSharpDeserializer
    {
        List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
    }  

## SCP-Host-Modus

In diesem Modus können Benutzer ihren Code als DLL kompilieren und die von SCP bereitgestellte SCPHost.exe verwenden, um ihre Topologie zu übermitteln. Die Spec-Datei sieht wie folgt aus:

        (scp-spout
          {
            "plugin.name" "SCPHost.exe"
            "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
            "output.schema" {"default" ["sentence"]}
          })

"plugin.name" ist in diesem Fall angegeben als "SCPHost.exe", die vom SCP SDK bereitgestellt wird. SCPHost.exe nimmt genau drei Parameter entgegen:

1.  Der erste Parameter ist der Name der DLL, in diesem Beispiel "HelloWorld.dll".
2.  Der zweite Parameter ist der Klassenname, in diesem Beispiel "Scp.App.HelloWorld.Generator".
3.  Der dritte Parameter ist der Name einer öffentlichen statischen Methode, die aufgerufen werden kann, um eine Instanz von ISCPPlugin zu erhalten.

Im Hostmodus wird der Benutzercode als DLL kompiliert und von der SCP-Plattform aufgerufen. Auf diese Weise erhält die SCP-Plattform vollständige Kontrolle über die gesamte Verarbeitungslogik. Wir empfehlen unseren Kunden, ihre Topologien im SCP-Hostmodus zu übermitteln. Auf diese Weise wird nicht nur die Entwicklung vereinfacht, sondern wir erhalten auch mehr Flexibilität und eine bessere Abwärtskompatibilität für zukünftige Versionen.

## SCP-Programmierbeispiele

### HelloWorld

HelloWorld ist ein sehr einfaches Beispiel und gibt einen Einblick in SCP.Net. Das Projekt verwendet eine nichttransaktionale Topologie mit einem Spout namens "generator” und zwei Bolts mit den Namen "splitter” und "counter”. Der Spout "generator” erzeugt zufällige Sätze und übermittelt Sätze an "splitter”. Der Bolt "splitter” teilt die Sätze in Wörter auf und übermittelt diese Sätze an den Bolt "counter". Der Bolt "counter” verwendet ein Wörterbuch, um die Vorkommnisse der einzelnen Wörter zu zählen.

Hier sehen Sie die beiden Spec-Dateien "HelloWorld.spec” und "HelloWorld\_EnableAck.spec” für dieses Beispiel. Im C\#-Code können Sie ermitteln, ob Ack aktiviert ist, indem Sie pluginConf aus der Java-Komponente abrufen.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);  

Falls Ack aktiviert ist, verwendet der Spout ein Wörterbuch, um die Tupel zwischenzuspeichern, für die kein Ack aufgerufen wurde. Wenn Fail() aufgerufen wird, wird das fehlgeschlagene Tupel erneut abgespielt:

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### HelloWorldTx

Das HelloWorldTx-Beispiel zeigt die Implementierung einer transaktionalen Topologie. Es enthält einen Spout mit dem Namen "generator", einen Stapel-Bolt mit dem Namen "partial-count" und einen Commit-Bolt namens "count-sum". Außerdem enthält das Projekt drei vorab erstellte txt-Dateien: "DataSource0.txt", "DataSource1.txt" und "DataSource2.txt".

Für jede Transaktion wählt der Spout "generator" nach dem Zufallsprinzip zwei der drei vorab erstellten Dateien aus und übermittelt die beiden Dateinamen an den Bolt "partial-count". Der Bolt "partial-count" liest zunächst den Dateinamen aus dem empfangenen Tupel, öffnet die Datei und zählt die Anzahl der Wörter in der Datei, und übermittelt zuletzt die Wortzahl an den Bolt "count-sum". Der Bolt "count-sum" summiert die Gesamtzahlen auf.

Um das "Exactly-Once"-Prinzip zu verwenden, muss der Bolt "count-sum" beurteilen können, ob es sich um eine wiederholt abgespielte Transaktion handelt. In diesem Beispiel enthält der Bolt eine statische Membervariable:

    public static long lastCommittedTxId = -1;  

Beim Erstellen einer ISCPBatchBolt-Instanz wird txAttempt aus den Eingabeparametern gelesen:

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

Beim Aufrufen von FinishBatch() wird lastCommittedTxId aktualisiert, wenn es sich nicht um eine wiederholt abgespielte Transaktion handelt.

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
        … …
    }  

### HybridTopology

Diese Topologie enthält einen Java-Spout und einen C\#-Bolt. Sie verwendet die Standardimplementierungen der SCP-Plattform für Serialisierung und Deserialisierung. In der Datei "HybridTopology.spec" im Ordner "examples\\HybridTopology" finden Sie Hinweise zu den Details der Spec-Datei, und "SubmitTopology.bat" enthält ein Beispiel für die Angabe des Java-Classpath.

### SCPHost demo

Dieses Beispiel ist dem HelloWorld-Beispiel sehr ähnlich. In diesem Projekt wird der Benutzercode jedoch als DLL kompiliert, und die Topologie wird mithilfe von SCPHost.exe übermittelt. Eine genauere Beschreibung finden Sie im Abschnitt "SCP-Host-Modus”.

  [0]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png
