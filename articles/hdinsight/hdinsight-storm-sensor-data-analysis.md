<properties
   pageTitle="Analysieren von Sensordaten mit Apache Storm und HBase | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit einem virtuellen Netzwerk eine Verbindung mit Apache Storm herstellen. Verwenden Sie Storm mit HBase, um Sensordaten von einem Event Hub zu verarbeiten und mit D3.js darzustellen."
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
   ms.date="04/28/2015"
   ms.author="larryfr"/>

# Analysieren von Sensordaten mit Apache Storm, Event Hub und HBase in HDInsight (Hadoop)

Erfahren Sie, wie Sie Apache Storm in HDInsight zur Verarbeitung von Sensordaten von Azure Event Hubs verwenden und mit "D3.js" visualisieren. Dieses Dokument beschreibt weiterhin die Verwendung virtueller Azure-Netzwerke zum Verbinden von Storm in HDInsight mit HBase in HDInsight sowie das Speichern von Daten aus der Topologie in HBase.

## Voraussetzungen

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Ein [Apache Storm in HDInsight-Cluster](../hdinsight-storm-getting-started.md)

* [Node.js](http://nodejs.org/): Wird für das Webdashboard und zum Senden von Sensordaten an Event Hub verwendet.

* [Java und das JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE]Java, das JDK, Maven und Git stehen auch über den [Chocolatey NuGet](http://chocolatey.org/)-Paket-Manager zur Verfügung.

## Architektur

![Architekturdiagramm](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Dieses Beispiel umfasst die folgenden Komponenten:

* **Azure Event Hub**: Stellt von Sensoren gesammelte Daten bereit. In diesem Beispiel wird eine Anwendung bereitgestellt, die falsche Daten generiert.

* **Storm in HDInsight**: Bietet Echtzeitverarbeitung von Event Hub-Daten.

* **HBase in HDInsight** (optional): Stellt einen persistenten NoSQL-Datenspeicher bereit.

* **Azure Virtual Network-Dienst** (optional, für HBase erforderlich): Ermöglicht die sichere Kommunikation zwischen Storm in HDInsight-Clustern und HBase in HDInsight-Clustern.

* **Dashboard-Website**: Ein Beispiel-Dashboard, das Daten in Echtzeit aufzeichnet.

	* Die Website ist in "Node.js" implementiert, sodass sie auf jedem Clientbetriebssystem für Tests ausgeführt oder auf Azure-Websites bereitgestellt werden kann.

	* [Socket.io](http://socket.io/) wird für die Echtzeitkommunikation zwischen der Storm-Topologie und der Website verwendet.

		> [AZURE.NOTE]Dies ist ein Implementierungsdetail. Sie können alle Kommunikationsframeworks, wie z. B. reine WebSockets oder SignalR, verwenden.

	* [D3.js](http://d3js.org/) wird verwendet, um die Daten grafisch darzustellen, die an die Website gesendet werden.

Die Topologie liest Daten von Event Hub mit der **com.microsoft.eventhubs.spout.EventHubSpout**-Klasse, die im Storm in HDInsight-Cluster bereitgestellt wird. Kommunikation mit der Website erfolgt mithilfe von [socket.io client.java](https://github.com/nkzawa/socket.io-client.java).

Die Kommunikation mit HBase erfolgt optional mithilfe der [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html)-Klasse, die als Teil von Storm bereitgestellt wird.

Hier sehen Sie eine Darstellung der Topologie:

![Topologiediagramm](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE]Dies ist eine äußerst vereinfachte Ansicht der Topologie. Zur Laufzeit wird eine Instanz jeder Komponente für jede Partition des Event Hubs erstellt, der gelesen wird. Diese Instanzen werden über die Knoten im Cluster verteilt, und Daten werden wie folgt zwischen ihnen weitergeleitet:
>
> * Für Daten vom Spout an den Parser wird ein Lastenausgleich ausgeführt.
> * Daten vom Parser zum Dashboard und zu HBase (sofern verwendet) werden nach Geräte-ID gruppiert, sodass Nachrichten vom gleichen Gerät immer an die gleiche Komponente weitergeleitet werden.

### Komponenten

* **EventHub-Spout**: Der Spout wird als Teil der [HDInsight Storm-Beispiele](https://github.com/hdinsight/hdinsight-storm-examples) auf GitHub bereitgestellt.

* **ParserBolt.java**: Der Spout gibt reine JSON-Daten aus, und gelegentlich werden mehrere Ereignisse gleichzeitig ausgegeben. Dieser Bolt veranschaulicht, wie vom Spout ausgegebene Daten gelesen und als ein Tupel, das mehrere Felder enthält, an einen neuen Datenstrom ausgegeben werden.

* **DashboardBolt.java**: Dies veranschaulicht, wie die Socket.io-Clientbibliothek für Java zum Senden von Daten in Echtzeit für das Webdashboard verwendet wird.

## Vorbereiten der Umgebung

Bevor Sie dieses Beispiel verwenden können, müssen Sie einen Azure Event Hub erstellen, der von der Storm-Topologie gelesen wird. Sie müssen auch eine Storm in HDInsight-Topologie erstellen, da die Komponente, die zum Lesen von Daten aus Event Hub verwendet wird, nur im Cluster verfügbar ist.

> [AZURE.NOTE]Schließlich wird der Event Hub-Spout von Maven verfügbar sein.

### Konfigurieren von Event Hub

Event Hub ist die Datenquelle für dieses Beispiel. Führen Sie die folgenden Schritte aus, um einen neuen Event Hub zu erstellen.

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU | Service Bus | Event Hub | Benutzerdefiniert erstellen**.

2. Geben Sie im Dialogfeld **Neuen Event Hub hinzufügen** einen **Event Hub-Namen** ein, wählen Sie die **Region** aus, in der der Hub erstellt werden soll, und erstellen Sie einen neuen Namespace, oder wählen einen vorhandenen Namespace aus. Klicken Sie zum Schluss auf den Pfeil, um fortzufahren.

2. Füllen Sie im Dialogfeld **Event Hub konfigurieren** die Felder **Anzahl der Partitionen** und **Aufbewahrung der Nachrichten** aus. Verwenden Sie für dieses Beispiel eine Anzahl von 10 Partitionen und eine Nachrichtenaufbewahrung von 1.

3. Wählen Sie nach der Erstellung des Event Hubs den Namespace aus, und klicken Sie auf **Event Hubs**. Wählen Sie zuletzt den Event Hub aus, den Sie zuvor erstellt haben.

4. Klicken Sie auf **Konfigurieren**, und erstellen Sie zwei neue Zugriffsrichtlinien mit den folgenden Informationen.

	<table>
<tr><th>Name</th><th>Berechtigungen</th></tr>
<tr><td>Geräte</td><td>Send</td></tr>
<tr><td>Storm</td><td>Empfangen</td></tr>
</table>Klicken Sie nach der Erstellung der Berechtigungen auf das **Speichern**-Symbol am unteren Seitenrand. Daraufhin werden die gemeinsam genutzten Zugriffsrichtlinien erstellt, die für den Austausch von Nachrichten mit diesem Hub verwendet werden.

5. Verwenden Sie nach dem Speichern der Richtlinien den **Generator für freigegebene Zugriffsschlüssel** unten auf der Seite, um den Schlüssel für die Richtlinien **devices** und **storm** abzurufen. Speichern Sie diese, da Sie sie später verwenden werden.

### Erstellen des Storm in HDInsight-Clusters

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) an.

2. Klicken Sie links auf **HDInsight** und anschließend unten links auf der Seite auf **+NEU**.

3. Klicken Sie in der zweiten Spalte auf das HDInsight-Symbol, und wählen Sie **Benutzerdefiniert** aus.

4. Geben Sie in der Seite **Clusterdetails** den Namen für das neue Cluster ein und wählen Sie **Storm** als **Clustertyp** aus. Klicken Sie auf den Pfeil, um fortzufahren.

5. Geben Sie 1 für die Anzahl der **Datenknoten** für diesen Cluster ein.

	> [AZURE.NOTE]Reduzieren Sie die **Clustergröße** auf 1, und löschen Sie den Cluster nach der Verwendung, um die Kosten für den in diesem Artikel verwendeten Cluster zu minimieren.

6. Geben Sie den **Benutzernamen** und das **Kennwort** für den Administrator ein, und klicken Sie auf den Pfeil, um fortzufahren.

4. Wählen Sie unter **Speicherkonto** entweder **Neuen Speicher erstellen** aus oder verwenden Sie ein vorhandenes Speicherkonto. Wählen Sie den zu verwendenden **Kontonamen** und den **Standardcontainer** aus. Klicken Sie unten links auf das Häkchensymbol, um den Storm-Cluster zu erstellen.

## Herunterladen und Installieren des EventHubSpout

1. Laden Sie das [HDInsight Storm-Beispielprojekt](https://github.com/hdinsight/hdinsight-storm-examples/) herunter. Suchen Sie nach dem Download die Datei **lib/eventhubs/eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

2. Verwenden Sie den folgenden Befehl an der Befehlszeile, um die Datei **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** im lokalen Maven-Speicher zu installieren. Auf diese Weise können Sie sie später als Verweis im Storm-Projekt hinzufügen.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

## Herunterladen und Konfigurieren des Projekts

Führen Sie den folgenden Befehl aus, um das Projekt von GitHub herunterzuladen.

	git clone https://github.com/Blackmist/hdinsight-eventhub-example

Nach Abschluss des Befehls verfügen Sie über die folgende Verzeichnisstruktur:

	hdinsight-eventhub-example/
		TemperatureMonitor/ - this is the Java topology
			conf/
				Config.properties
				hbase-site.xml
			src/
			test/
			dashboard/ - this is the node.js web dashboard
			SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE]Dieses Dokument geht nicht ausführlich auf alle Details des in diesem Beispiel enthaltenen Codes ein. Der Code ist jedoch vollständig kommentiert.

Öffnen Sie die Datei **Config.properties**, und fügen Sie die Informationen hinzu, die Sie zuvor beim Erstellen des Event Hubs verwendet haben. Speichern Sie die Datei, nachdem Sie diese Informationen hinzugefügt haben.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

	## if not provided, will use storm's zookeeper settings
	## zookeeper.connectionstring=localhost:2181

	eventhubspout.checkpoint.interval = 10

	eventhub.receiver.credits = 1024

## Kompilieren und lokales Testen

Vor dem Testen müssen Sie das Dashboard zur Anzeige der Ausgabe der Topologie und zum Generieren der Daten starten, die im Event Hub gespeichert werden sollen.

### Starten der Webanwendung

1. Öffnen Sie eine neue Eingabeaufforderung oder ein Terminal, und wechseln Sie ins Verzeichnis **hdinsight-eventhub-example/dashboard**. Verwenden Sie dann folgenden Befehl, um die von der Webanwendung benötigten Abhängigkeiten zu installieren:

		npm install

2. Verwenden Sie den folgenden Befehl, um die Webanwendung zu starten.

		node server.js

	Es wird eine Meldung ähnlich der folgenden angezeigt:

		Server listening at port 3000

2. Öffnen Sie einen Webbrowser, und geben Sie **http://localhost:3000/** als Adresse ein. Es wird eine Seite ähnlich der folgenden angezeigt:

	![Webdashboard](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Lassen Sie die Eingabeaufforderung oder das Terminal geöffnet. Verwenden Sie nach dem Testen STRG+C, um den Webserver zu beenden.

### Starten der Datengenerierung

> [AZURE.NOTE]Die Schritte in diesem Abschnitt verwenden "Node.js", sodass sie auf einer beliebigen Plattform ausgeführt werden können. Andere Sprachbeispiele finden Sie im **SendEvents**-Verzeichnis.


1. Öffnen Sie eine neue Eingabeaufforderung oder ein neues Terminal, und wechseln Sie ins Verzeichnis **hdinsight-eventhub-example/SendEvents/nodejs**. Verwenden Sie dann folgenden Befehl, um die von der Anwendung benötigten Abhängigkeiten zu installieren:

		npm install

2. Öffnen Sie die Datei **app.js** in einem Text-Editor, und fügen Sie die Event Hub-Informationen hinzu, die Sie zuvor abgerufen haben:

		// ServiceBus Namespace
		var namespace = 'servicebusnamespace';
		// Event Hub Name
		var hubname ='eventhubname';
		// Shared access Policy name and key (from Event Hub configuration)
		var my_key_name = 'devices';
		var my_key = 'key';

2. Verwenden Sie den folgenden Befehl zum Einfügen neuer Einträge in Event Hub:

		node app.js

	Es sollten mehrere Ausgabezeilen angezeigt werden, die die an Event Hub gesendeten Daten enthalten. Dies sieht in etwa wie folgt aus:

		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":0,"Temperature":7}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":1,"Temperature":39}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":2,"Temperature":86}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":3,"Temperature":29}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":4,"Temperature":30}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":5,"Temperature":5}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":6,"Temperature":24}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":7,"Temperature":40}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":8,"Temperature":43}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":9,"Temperature":84}

### Starten der Topologie

2. Führen Sie den folgenden Befehl aus, um die Topologie lokal zu starten:

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Dieser Befehl startet die Topologie, liest Dateien aus dem Event Hub und sendet die Daten an das Dashboard, das in einer Azure Website ausgeführt wird. Im Webdashboard sollten nun Linien angezeigt werden, die den folgenden ähneln:

	![Dashboard mit Daten](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Verwenden Sie während der Ausführung des Dashboards den Befehl `node app.js` aus den vorherigen Schritten, um neue Daten an das Dashboard zu senden. Da die Temperaturwerte zufällig generiert werden, sollte das Diagramm aktualisiert werden, um die neuen Werte anzuzeigen.

3. Beenden Sie die Topologie durch Eingabe von STRG+C, nachdem Sie überprüft haben, dass dies funktioniert. Um die SendEvent-App zu beenden, wählen Sie das Fenster aus, und drücken Sie eine beliebige Taste. Sie können auch STRG+C verwenden, um den Webserver zu beenden.

## Verpacken und Bereitstellen der Topologie in HDInsight

Führen Sie die folgenden Schritte in Ihrer Entwicklungsumgebung aus, um die Temperaturtopologie in Ihrem HDInsight-Storm-Cluster auszuführen.

### Veröffentlichen des Website-Dashboards

1. Um das Dashboard für eine Azure-Website bereitzustellen, folgen Sie den Anweisungen unter [Erstellen und Bereitstellen einer Node.js-Website für Azure](../web-sites-nodejs-develop-deploy-mac.md). Notieren Sie sich die URL der Website, die ungefähr folgendermaßen lautet: **mywebsite.azurewebsites.net**.

2. Wenn die Website erstellt wurde, wechseln Sie im Azure-Portal zur Website, und wählen Sie die Registerkarte **Konfigurieren** aus. Aktivieren Sie **Websockets**, und klicken Sie dann unten auf der Seite auf **Speichern**.

2. Öffnen Sie **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts\\DashboardBolt.java**, und ändern Sie die folgende Zeile so, dass sie auf die URL des veröffentlichten Dashboards verweist:

		socket = IO.socket("http://mywebsite.azurewebsites.net");

3. Speichern Sie die Datei **DashboardBolt.java**.

### Verpacken und Bereitstellen der Topologie

1. Führen Sie den folgenden Befehl aus, um ein JAR-Paket aus Ihrem Projekt zu erstellen.

		mvn package

	Daraufhin wird eine Datei mit dem Namen **TemperatureMonitor-1.0-SNAPSHOT.jar** im Verzeichnis **target** für Ihr Projekt erstellt.

2. Führen Sie die Schritte unter [Bereitstellen und Verwalten von Storm-Topologien](hdinsight-storm-deploy-monitor-topology.md) aus, um die Topologie auf den Storm in HDInsight-Cluster mithilfe des **Storm-Dashboards** hochzuladen und zu starten.

3. Nachdem die Topologie gestartet wurde, öffnen Sie die Website, die Sie in Azure veröffentlicht haben, in einem Browser, und verwenden Sie dann den Befehl `node app.js` zum Senden von Daten an Event Hub. Das Webdashboard sollte mit den Informationen aktualisiert werden.

	![Dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Optional: Verwenden von HBase

Um Storm und HBase gemeinsam zu verwenden, müssen Sie zunächst ein virtuelles Azure-Netzwerk und anschließend innerhalb des Netzwerks einen Storm- und HBase-Cluster erstellen.

### Erstellen eines virtuellen Azure-Netzwerks (optional)

Wenn Sie HBase mit diesem Beispiel verwenden möchten, müssen Sie ein virtuelles Netzwerk in Azure erstellen, das einen Storm in HDInsight-Cluster und einen HBase in HDInsight-Cluster enthält.

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com) an.

2. Klicken Sie unten auf der Seite auf **+NEU** > **Network Services** > **Virtual Network** > **Schnellerfassung**.

3. Geben Sie die folgenden Werte ein, oder wählen Sie sie aus:

	- **Name**: Name des virtuellen Netzwerks.

	- **Adressraum**: Wählen Sie einen Adressraum für das virtuelle Netzwerk, der groß genug ist, um Adressen für alle Knoten im Cluster zur Verfügung zu stellen. Andernfalls funktioniert die Bereitstellung nicht.

	- **Maximale Anzahl virtueller Computer**: Wählen Sie einen Wert für die maximale Anzahl virtueller Computer aus.

	- **Speicherort**: Der Speicherort muss derselbe sein wie der des zu erstellenden HBase-Clusters.

	- **DNS-Server**:In diesem Artikel werden interne DNS-Server verwendet, die von Azure bereitgestellt werden. Daher können Sie **Kein** auswählen. Erweiterte Netzwerkkonfigurationen mit benutzerdefinierten DNS-Servern werden ebenfalls unterstützt. Detaillierte Anleitungen finden Sie unter [Namensauflösung (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).

4. Klicken Sie auf **Virtuelles Netzwerk erstellen**. Der Name des neuen virtuellen Netzwerks wird in der Liste angezeigt. Warten Sie, bis in der Statusspalte **Created** angezeigt wird.

5. Klicken Sie im Hauptbereich auf das virtuelle Netzwerk, das Sie gerade erstellt haben.

6. Klicken Sie oben auf der Seite auf **DASHBOARD**.

7. Notieren Sie sich die **VIRTUAL NETWORK ID**. Sie finden diese unter **Schnelleinsicht**. Sie benötigen diesen Wert für die Bereitstellung der Storm- und HBase-Cluster.

8. Klicken Sie oben auf der Seite auf **KONFIGURIEREN**.

9. Unten auf der Seite finden Sie den Namen des Standardsubnetzes. Dieser lautet **Subnet-1**. Klicken Sie auf die Schaltfläche **Subnetz hinzufügen**, um **Subnet-2** hinzuzufügen. Diese Subnetze werden die Storm- und HBase-Cluster enthalten.

	> [AZURE.NOTE]In diesem Artikel verwenden wir Cluster mit nur einem Knoten. Falls Sie Cluster mit mehreren Knoten erstellen, müssen Sie den Wert **CIDR (ANZAHL ADRESSEN)** für das Subnetz überprüfen, das Sie für den Cluster verwenden. Die Anzahl der Adressen muss größer als die Anzahl der Workerknoten plus sieben sein (Gateway: 2, Hauptknoten: 2, Zookeeper: 3). Wenn Sie zum Beispiel einen HBase-Cluster mit 10 Knoten benötigen, muss die Anzahl der Adressen für das Subnetz größer als 17 (10 + 7) sein. Andernfalls funktioniert die Bereitstellung nicht.
	>
	> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

11. Klicken Sie unten auf der Seite auf **Speichern**.

### Erstellen eines Storm- und HBase-Clusters im virtuellen Netzwerk

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) an.

2. Klicken Sie links auf **HDInsight** und anschließend unten links auf der Seite auf **+NEU**.

3. Klicken Sie in der zweiten Spalte auf das HDInsight-Symbol, und wählen Sie **Benutzerdefiniert** aus.

4. Geben Sie in der Seite **Clusterdetails** den Namen für das neue Cluster ein und wählen Sie **Storm** als **Clustertyp** aus. Klicken Sie auf den Pfeil, um fortzufahren.

5. Geben Sie 1 für die Anzahl der **Datenknoten** für dieses Cluster ein. Wählen Sie unter **Region/Virtuelles Netzwerk** das zuvor erstellte virtuelle Azure-Netzwerk aus. Wählen Sie unter **Subnetze des virtuellen Netzwerks** den Eintrag **Subnet-1** aus.

	> [AZURE.NOTE]Reduzieren Sie die **Clustergröße** auf 1, und löschen Sie den Cluster nach der Verwendung, um die Kosten für den in diesem Artikel verwendeten Cluster zu minimieren.

6. Geben Sie den **Benutzernamen** und das **Kennwort** für den Administrator ein, und klicken Sie auf den Pfeil, um fortzufahren.

4. Wählen Sie unter **Speicherkonto** entweder **Neuen Speicher erstellen** aus oder verwenden Sie ein vorhandenes Speicherkonto. Wählen Sie den zu verwendenden **Kontonamen** und den **Standardcontainer** aus. Klicken Sie unten links auf das Häkchensymbol, um den Storm-Cluster zu erstellen.

5. Wiederholen Sie die Schritte zum Erstellen eines neuen **HBase**-Clusters. Im Folgenden sind die wichtigsten Unterschiede aufgeführt:

	* **Clustertyp**: Wählen Sie **HBase** aus.

	* **Subnetze des virtuellen Netzwerks**: Wählen Sie **Subnet-2** aus.

	* **Speicherkonto**: Sie sollten einen anderen Container als denjenigen verwenden, den Sie für den Storm-Cluster verwendet haben.

### Ermitteln des HBase DNS-Suffix

Sie müssen den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für den HBase-Cluster verwenden, um vom Storm-Cluster aus in den HBase-Cluster schreiben zu können. Führen Sie den folgenden Befehl aus, um diese Informationen abzurufen:

	curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

Suchen Sie in den zurückgegebenen JSON-Daten nach dem Eintrag **"host_name"**. Dieser Eintrag enthält den vollqualifizierten Domänennamen (FQDN) für die Knoten im Cluster. Beispiel:

	...
	"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
	...

Der Teil des Domänennamens, der mit dem Clusternamen beginnt, ist das DNS-Suffix, z. B. **mycluster.b1.cloudapp.net**.

### Aktivieren des HBase-Bolts

1. Öffnen Sie **hdinsight-eventhub-example\\TemperatureMonitor\\conf\\hbase-site.xml**, und ersetzen Sie die `suffix`-Einträge in der folgenden Zeile durch das DNS-Suffix, das Sie zuvor für den HBase-Cluster abgerufen haben. Speichern Sie die Datei, nachdem Sie diese Änderungen vorgenommen haben.

		<value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>

	Dies wird vom HBase-Bolt für die Kommunikation mit dem HBase-Cluster verwendet.

1. Öffnen Sie **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts** in einem Text-Editor, und heben Sie die Kommentierung der folgenden Zeilen auf, indem Sie die Zeichen `//` am Anfang entfernen. Speichern Sie die Datei, nachdem Sie diese Änderungen vorgenommen haben.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Der HBase-Bolt ist nun aktiviert.

	> [AZURE.NOTE]Sie sollten den HBase-Bolt nur aktivieren, wenn Sie den Storm-Cluster bereitstellen, nicht bei lokalen Tests.

### HBase- und Storm-Daten

Vor dem Ausführen der Topologie müssen Sie HBase zur Annahme der Daten vorbereiten.

1. Stellen Sie per Remotedesktop eine Verbindung mit dem HBase-Cluster her.

2. Starten Sie die HDInsight-Befehlszeile über den Desktop, und geben Sie die folgenden Befehle ein.

    cd %HBASE_HOME% bin\\hbase shell

3. Geben Sie in der HBase-Befehlszeile den folgenden Befehl ein, um eine Tabelle zu erstellen, in der die Sensordaten gespeichert werden:

    create 'SensorData', 'cf'

4. Geben Sie den folgenden Befehl ein, um sich zu vergewissern, dass die Tabelle keine Daten enthält:

    scan 'SensorData'

Wenn Sie die Topologie auf dem Storm-Cluster gestartet und die Daten verarbeitet haben, können Sie mithilfe des `scan 'SensorData'`-Befehls erneut sicherstellen, dass die Daten in HBase eingefügt wurden.


## Nächste Schritte

Sie haben gelernt, wie Sie Daten mithilfe von Storm aus einem Event Hub lesen und Informationen aus Storm mit SignalR und D3.js in einem externen Dashboard anzeigen können. Wenn Sie die optionalen Schritte durchgeführt haben, haben Sie außerdem erfahren, wie Sie HDInsight in einem virtuellen Netzwerk konfigurieren und wie Sie die Kommunikation zwischen einer Storm-Topologie und HBase mit dem HBase-Bolt herstellen.

* Weitere Beispiele für Storm-Topologien mit HDinsight finden Sie unter:

    * [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

* Weitere Informationen zu Apache Storm finden Sie auf der [Apache Storm](https://storm.incubator.apache.org/)-Website.

* Weitere Informationen zu HBase in HDInsight finden Sie unter [HBase mit HDInsight: Übersicht](hdinsight-hbase-overview.md).

* Weitere Informationen über Socket.io finden Sie auf der [socket.io](http://socket.io/)-Website (in englischer Sprache).

* Weitere Informationen zu D3.js finden Sie unter [D3.js – Data Driven Documents](http://d3js.org/) (in englischer Sprache).

* Informationen zum Erstellen von Topologien in Java finden Sie unter [Entwickeln von Java-Topologien für Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md).

* Informationen zum Erstellen von Topologien in .NET finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://manage.windowsazure.com/
 

<!---HONumber=58_postMigration-->