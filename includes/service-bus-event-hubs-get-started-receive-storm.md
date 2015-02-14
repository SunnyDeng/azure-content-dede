## Empfangen von Nachrichten mit Apache Storm

[**Apache Storm**](https://storm.incubator.apache.org) ist ein verteiltes Echtzeit-Berechnungssystem, das die zuverlässige Verarbeitung von unbegrenzten Datenströmen vereinfacht. In diesem Abschnitt wird gezeigt, wie Sie den Ereignis-Hub-Spout in Storm zum Empfangen von Ereignissen vom Ereignis-Hubs verwenden. Mit Apache Storm können Sie Ereignisse auf mehrere Prozesse aufteilen, die in verschiedenen Knoten gehostet werden. Die Ereignis-Hub-Integration in Storm vereinfacht die Ereignisnutzung durch transparente Prüfung des Fortschritts mithilfe der Zookeeper Installation von Storm, der Verwaltung von permanenten Prüfpunkten und dem parallelen von Ereignissen von Ereignis-Hubs.

Weitere Informationen zu Empfangsmustern von Ereignis-Hubs finden Sie unter [Übersicht über Ereignis-Hubs].

In diesem Lernprogramm wird eine [HDInsight Storm]-Installation verwendet, in der der Ereignis-Hub-Spout bereits verfügbar ist.

1. Gehen Sie wie unter [HDInsight Storm - Erste Schritte](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-storm-getting-started/) beschrieben vor, um ein neues HDInsight-Cluster zu erstellen und über Remotedesktop eine Verbindung damit herzustellen.

2. Kopieren Sie die Datei `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` in Ihre lokale Entwicklungsumgebung. Sie enthält events-storm-spout.

3. Führen Sie den folgenden Befehl aus, um das Paket im lokalen Maven-Speicher zu installieren. Auf diese Weise können Sie das Paket später als Verweis im Storm-Projekt hinzufügen.

		mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. Erstellen Sie in Eclipse ein neues Maven-Projekt (klicken Sie auf **File**, dann auf **New** und **Project**).

   	![][12]

5. Wählen Sie **Use default Workspace location** aus, und klicken Sie dann auf **Next**

6. Wählen Sie den Archetyp **maven-archetype-quickstart** aus, und klicken Sie auf **Next**

7. Fügen Sie **GroupId** und **ArtifactId** ein, und klicken Sie auf **Finish**

8. Fügen Sie in der Datei **pom.xml** die folgenden Abhängigkeiten in den Knoten `<dependency>` ein.
		
		<dependency>
			<groupId>org.apache.storm</groupId>
			<artifactId>storm-core</artifactId>
			<version>0.9.2-incubating</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>com.microsoft.eventhubs</groupId>
			<artifactId>eventhubs-storm-spout</artifactId>
			<version>0.9</version>
		</dependency>
		<dependency>
			<groupId>com.netflix.curator</groupId>
			<artifactId>curator-framework</artifactId>
			<version>1.3.3</version>
			<exclusions>
				<exclusion>
					<groupId>log4j</groupId>
					<artifactId>log4j</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-log4j12</artifactId>
				</exclusion>
			</exclusions>
			<scope>provided</scope>
		</dependency>

9. Erstellen Sie im Ordner **src** eine Datei namens **Config.properties**, und kopieren Sie den folgenden Inhalt, wobei Sie die folgenden Werte ersetzen:

		eventhubspout.username = ReceiveRule
		
		eventhubspout.password = {receive rule key}
		
		eventhubspout.namespace = ioteventhub-ns
		
		eventhubspout.entitypath = {event hub name}
		
		eventhubspout.partitions.count = 16
		
		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181
		
		eventhubspout.checkpoint.interval = 10
		
		eventhub.receiver.credits = 10

	Der Wert für **eventhub.receiver.credits** bestimmt, wie viele Ereignisse als Stapel verarbeitet werden, bevor sie für die Storm-Pipeline freigegeben werden. Der Einfachheit halber wird in diesem Beispiel der Wert auf 10 gesetzt. In einer Produktionsumgebung sollten normalerweise höhere Werte festgelegt werden, beispielsweise 1024.

10. Erstellen Sie eine neue Klasse namens **LoggerBolt**, und verwenden Sie dazu den folgenden Code:

		import java.util.Map;
		import org.slf4j.Logger;
		import org.slf4j.LoggerFactory;
		import backtype.storm.task.OutputCollector;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseRichBolt;
		import backtype.storm.tuple.Tuple;
		
		public class LoggerBolt extends BaseRichBolt {
			private OutputCollector collector;
			private static final Logger logger = LoggerFactory
				      .getLogger(LoggerBolt.class);
		
			@Override
			public void execute(Tuple tuple) {				
				String value = tuple.getString(0);
				logger.info("Tuple value: " + value);
				
				collector.ack(tuple);
			}
		
			@Override
			public void prepare(Map map, TopologyContext context, OutputCollector collector) {
				this.collector = collector;
				this.count = 0;
			}
		
			@Override
			public void declareOutputFields(OutputFieldsDeclarer declarer) {
				// no output fields
			}
		
		}

	Diese Storm-Klasse protokolliert den Inhalt der empfangenen Ereignisse. Sie kann problemlos erweitert werden, um Tupel in einem Speicherdienst speichern. Im [Lernprogramm zur HDInsight-Sensoranalyse] wird derselbe Ansatz zum Speichern von Daten in HBase verwendet.

11. Erstellen Sie eine neue Klasse namens **LogTopology**, und verwenden Sie dazu den folgenden Code:

		import java.io.FileReader;
		import java.util.Properties;
		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import com.microsoft.eventhubs.samples.EventCount;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
		
		public class LogTopology {
			protected EventHubSpoutConfig spoutConfig;
			protected int numWorkers;
		
			protected void readEHConfig(String[] args) throws Exception {
				Properties properties = new Properties();
				if (args.length > 1) {
					properties.load(new FileReader(args[1]));
				} else {
					properties.load(EventCount.class.getClassLoader()
							.getResourceAsStream("Config.properties"));
				}
		
				String username = properties.getProperty("eventhubspout.username");
				String password = properties.getProperty("eventhubspout.password");
				String namespaceName = properties
						.getProperty("eventhubspout.namespace");
				String entityPath = properties.getProperty("eventhubspout.entitypath");
				String zkEndpointAddress = properties
						.getProperty("zookeeper.connectionstring"); // opt
				int partitionCount = Integer.parseInt(properties
						.getProperty("eventhubspout.partitions.count"));
				int checkpointIntervalInSeconds = Integer.parseInt(properties
						.getProperty("eventhubspout.checkpoint.interval"));
				int receiverCredits = Integer.parseInt(properties
						.getProperty("eventhub.receiver.credits")); // prefetch count
																	// (opt)
				System.out.println("Eventhub spout config: ");
				System.out.println("  partition count: " + partitionCount);
				System.out.println("  checkpoint interval: "
						+ checkpointIntervalInSeconds);
				System.out.println("  receiver credits: " + receiverCredits);
		
				spoutConfig = new EventHubSpoutConfig(username, password,
						namespaceName, entityPath, partitionCount, zkEndpointAddress,
						checkpointIntervalInSeconds, receiverCredits);
		
				// set the number of workers to be the same as partition number.
				// the idea is to have a spout and a logger bolt co-exist in one
				// worker to avoid shuffling messages across workers in storm cluster.
				numWorkers = spoutConfig.getPartitionCount();
		
				if (args.length > 0) {
					// set topology name so that sample Trident topology can use it as
					// stream name.
					spoutConfig.setTopologyName(args[0]);
				}
			}
		
			protected StormTopology buildTopology() {
				TopologyBuilder topologyBuilder = new TopologyBuilder();
		
				EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
				topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
						spoutConfig.getPartitionCount()).setNumTasks(
						spoutConfig.getPartitionCount());
				topologyBuilder
						.setBolt("LoggerBolt", new LoggerBolt(),
								spoutConfig.getPartitionCount())
						.localOrShuffleGrouping("EventHubsSpout")
						.setNumTasks(spoutConfig.getPartitionCount());
				return topologyBuilder.createTopology();
			}
		
			protected void runScenario(String[] args) throws Exception {
				boolean runLocal = true;
				readEHConfig(args);
				StormTopology topology = buildTopology();
				Config config = new Config();
				config.setDebug(false);
		
				if (runLocal) {
					config.setMaxTaskParallelism(2);
					LocalCluster localCluster = new LocalCluster();
					localCluster.submitTopology("test", config, topology);
					Thread.sleep(5000000);
					localCluster.shutdown();
				} else {
					config.setNumWorkers(numWorkers);
				    StormSubmitter.submitTopology(args[0], config, topology);
				}
			}
		
			public static void main(String[] args) throws Exception {
				LogTopology topology = new LogTopology();
				topology.runScenario(args);
			}
		}

	Diese Klasse erstellt einen neuen Ereignis-Hubs-Spout, und verwendet die Eigenschaften in der Konfigurationsdatei zum Instanziieren. Beachten Sie unbedingt, dass in diesem Beispiel eine der Anzahl der Partitionen auf dem Ereignis-Hub entsprechende die Anzahl von Spout-Aufgaben erstellt wird, um die maximale für diesen Ereignis-Hub zulässige Parallelität zulässig zu verwenden.

<!-- Links -->
[Übersicht über Ereignis-Hubs]: http://msdn.microsoft.com/de-de/library/azure/dn821413.aspx
[HDInsight Storm]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-storm-overview/
[Lernprogramm zur HDInsight-Sensoranalyse]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-storm-sensor-data-analysis/

<!-- Images -->

[12]: ./media/service-bus-event-hubs-getstarted/create-storm1.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png


<!--HONumber=42-->
