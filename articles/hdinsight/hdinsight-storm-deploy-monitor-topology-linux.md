<properties
   pageTitle="Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight unter Linux | Microsoft Azure"
   description="Erfahren Sie, wie Sie Apache Storm-Topologien mithilfe des Storm-Dashboards in Linux-basiertem HDInsight bereitstellen, überwachen und verwalten. Hadoop-Tools für Visual Studio verwenden"
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
   ms.date="10/26/2015"
   ms.author="larryfr"/>

# Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight unter Linux

Dieses Dokument enthält die Grundlagen zur Verwaltung und Überwachung von Storm-Topologien, die auf Linux-basiertem Storm in HDInsight-Clustern ausgeführt werden.

> [AZURE.IMPORTANT]Die Schritte in diesem Dokument erfordern einen Linux-basierten HDInsight-Cluster. Informationen zur Bereitstellung und Überwachung von Topologien in Windows-basiertem HDInsight finden Sie unter [Bereitstellen und Verwalten von Apache Storm-Topologien in Windows-basiertem HDInsight](hdinsight-storm-deploy-monitor-topology.md)

## Voraussetzungen

- **Storm unter Linux in einem HDInsight-Cluster**: Informationen zum Erstellen eines Clusters finden Sie unter [Erste Schritte mit Apache Storm in HDInsight](hdinsight-storm-get-started-linux.md).

- **Erfahrung mit SSH und SCP**: Weitere Informationen zur Verwendung von SSH und SCP mit HDInsight finden Sie in den folgenden Artikeln:
    - **Linux-, Unix- oder OS X-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, OS X oder Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    - **Windows-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- **Ein SCP-Client**: Dieser wird auf allen Linux-, Unix- und OS X-Systemen bereitgestellt. Bei Windows-Clients empfehlen wir PSCP, der auf der [PuTTY-Downloadseite](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) verfügbar ist.

## Starten einer Storm-Topologie

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Ersetzen Sie **USERNAME** durch den Namen des SSH-Anmeldung. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres HDInsight-Clusters.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Weitere Informationen zur Verwendung von SSH für den Verbindungsaufbau mit dem HDInsight-Cluster finden Sie in den folgenden Dokumenten:
    
        - **Linux, Unix or OS X clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X or Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        - **Windows clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Verwenden Sie zum Starten einer Beispieltopologie den folgenden Befehl:

        storm jar storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Hierdurch wird die WordCount-Beispieltopologie im Cluster gestartet. Nach dem Zufallsprinzip werden Sätze generiert und die Instanzen jedes Worts in den Sätzen gezählt.

    > [AZURE.NOTE]Wenn die Topologie an den Cluster gesendet wird, müssen Sie zuerst die JAR-Datei mit dem Cluster kopieren, bevor Sie den Befehl `storm` verwenden. Hierzu können Sie den Befehl `scp` auf dem Client mit der Datei verwenden. Beispiel: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Das Beispiel "WordCount" und andere Storm-Starter-Beispiele sind unter `/usr/hdp/current/storm-client/contrib/storm-starter/` bereits auf dem Cluster enthalten.

##Überwachen und Verwalten mit dem Befehl "storm"

Mit dem Dienstprogramm `storm` können Sie an der Befehlszeile mit ausgeführten Topologien arbeiten. Im Folgenden finden Sie eine Liste mit häufig verwendeten Befehlen. Eine vollständige Liste der Befehle erhalten Sie mit `storm -h`.

###Auflisten der Topologien

Mit dem folgenden Befehl können Sie alle ausgeführten Topologien auflisten:

    storm list
    
Die Ausgabe dieses Befehls sieht etwa so aus:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

###Deaktivieren und erneutes Aktivieren

Durch das Deaktivieren wird eine Topologie unterbrochen, bis sie beendet oder erneut aktiviert wird. Verwenden Sie zum Deaktivieren und erneuten Aktivieren folgende Befehle:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

###Beenden einer ausgeführten Topologie

Storm-Topologien werden, nachdem sie einmal gestartet wurden, so lange ausgeführt, bis sie beendet werden. Führen Sie zum Beenden einer Topologie den folgenden Befehl aus:

    storm stop TOPOLOGYNAME

###Ausgleichen

Durch das Ausgleichen einer Topologie kann das System die Parallelität der Topologie überarbeiten. Wenn Sie z. B. die Größe des Clusters geändert haben, um zusätzliche Notizen hinzuzufügen, ermöglicht ein Ausgleich einer ausgeführten Topologie, die neuen Knoten zu verwenden.

> [AZURE.WARNING]Beim Ausgleichen einer Topologie wird diese zunächst deaktiviert. Anschließend werden die Worker gleichmäßig auf den Cluster verteilt, und zum Schluss wird die Topologie wieder in den Zustand vor dem Ausgleich zurückgesetzt. Wenn die Topologie also z. B. aktiv war, wird sie wieder aktiviert. Wenn sie deaktiviert war, bleibt sie deaktiviert.

    storm rebalance TOPOLOGYNAME

##Überwachen und Verwalten mit der Storm-Benutzeroberfläche

Die Storm-Benutzeroberfläche bietet eine Weboberfläche zum Arbeiten mit ausgeführten Topologien und befindet sich auf dem HDInsight-Cluster.

> [AZURE.IMPORTANT]Die Storm-Benutzeroberfläche ist nicht öffentlich über das Internet verfügbar. Der Zugriff muss über einen SSH-Tunnel zum Hauptknoten des HDInsight-Clusters erfolgen. Weitere Informationen zum Erstellen und Verwenden eines SSH-Tunnels finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

Führen Sie die folgenden Schritte aus, um die Storm-Benutzeroberfläche anzuzeigen:

1. Öffnen Sie in einem Webbrowser Ambari Web für Ihren HDInsight-Cluster. Die URL für Ambari Web lautet https://CLUSTERNAME.azurehdinsight.net, wobei __CLUSTERNAME__ der Name des Clusters ist.

2. Wählen Sie aus der Liste der Dienste auf der linken Seite __Storm__ aus. Wählen Sie dann unter __QuickLinks__ den Eintrag __Storm-UI__ aus.

    ![Eintrag der Storm-Benutzeroberfläche in den Quicklinks](./media/hdinsight-storm-deploy-monitor-topology-linux/ambari-storm.png)

    Damit wird die Storm-Benutzeroberfläche geöffnet:

    ![Storm-Benutzeroberfläche](./media/hdinsight-storm-deploy-monitor-topology-linux/storm-ui.png)

> [AZURE.NOTE]Bei der Arbeit mit der Storm-Benutzeroberfläche stellen Sie möglicherweise fest, dass sie von einigen Versionen von Internet Explorer nach dem ersten Besuch nicht ordnungsgemäß aktualisiert wird. Beispielsweise werden die von Ihnen übermittelten neuen Topologien nicht gezeigt, oder es wird eine Topologie als aktiv angezeigt, die Sie zuvor deaktiviert haben. Microsoft ist sich dieses Umstands bewusst und arbeitet an einer Lösung.

### Hauptseite

Die Hauptseite der Storm-Benutzeroberfläche bietet die folgenden Informationen: - **Clusterzusammenfassung**: grundlegende Informationen zum Storm-Cluster. - **Topology summary**: eine Liste der aktiven Topologien. Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Topologien anzuzeigen. - **Supervisor summary**: Informationen zum Storm-Supervisor. - **Nimbus configuration**: Nimbus-Konfiguration für den Cluster.

### Topologiezusammenfassung

Wenn Sie einen Link aus dem Abschnitt **Topology summary** auswählen, werden die folgenden Informationen zur Topologie angezeigt: - **Topology summary**: grundlegende Informationen zur Topologie. - **Topology actions**: Verwaltungsaktionen, die für die Topologie ausgeführt werden können. - **Aktivieren**: setzt die Verarbeitung einer deaktivierten Topologie fort. - **Deaktivieren**: hält eine aktive Topologie an. - **Neu ausgleichen**: passt die Parallelität der Topologie an. Sie sollten aktive Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Dadurch kann die Topologie die Parallelität anpassen, um die höhere oder geringere Anzahl der Knoten im Cluster zu kompensieren.

      For more information, see <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of a Storm topology</a>.

  - **Beenden:** beendet eine Storm-Topologie nach dem angegebenen Timeout.

- **Topologiestatistik:** Statistiken zur Topologie. Verwenden Sie die Links in der Spalte **Fenster**, um den Zeitrahmen für die verbleibenden Einträge auf der Seite festzulegen.
- **Spouts:** die von der Topologie verwendeten Spouts. Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Spouts anzuzeigen.
- **Bolts:** die von der Topologie verwendeten Bolts. Verwenden Sie die Links in diesem Abschnitt, um weitere Informationen zu bestimmten Bolts anzuzeigen.
- **Topologiekonfiguration:** die Konfiguration der ausgewählten Topologie.

### Spout und Bolt: Zusammenfassung

Wenn Sie in einem der Abschnitte **Spouts** oder **Bolts** einen Spout auswählen, werden die folgenden Informationen zum ausgewählten Element angezeigt: - **Komponentenzusammenfassung**: grundlegende Informationen zum Spout oder Bolt. - **Spout/Bolt stats**: Statistiken zum Spout oder Bolt. Verwenden Sie die Links in der Spalte **Fenster**, um den Zeitrahmen für die verbleibenden Einträge auf der Seite festzulegen. - **Input stats** (nur Bolts): Informationen zu den Eingabedatenströmen, die vom Bolt verbraucht werden. - **Output stats**: Informationen zu den Datenströmen, die von diesem Spout oder Bolt ausgegeben werden. - **Executors**: Informationen zu den Instanzen von Spout oder Bolt. Wählen Sie den Eintrag **Port** für einen bestimmten Ausführer aus, um ein Protokoll mit Diagnoseinformationen anzuzeigen, das für diese Instanz generiert wurde. - **Fehler**: Fehlerinformationen zu diesem Spout oder Bolt.

## REST-API

Die Storm-Benutzeroberfläche baut auf der REST-API auf, sodass Sie mithilfe der REST-API ähnliche Verwaltungs- und Überwachungsfunktionen ausführen können. Mithilfe der REST-API können Sie benutzerdefinierte Tools zum Verwalten und Überwachen von Storm-Topologien erstellen.

Weitere Informationen finden Sie unter <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">Storm UI REST API</a> (in englischer Sprache). Die folgenden Informationen gelten für die Verwendung der REST-API mit Apache Storm in HDInsight.

> [AZURE.IMPORTANT]Die Storm-REST-API ist nicht öffentlich über das Internet verfügbar. Der Zugriff muss über einen SSH-Tunnel zum Hauptknoten des HDInsight-Clusters erfolgen. Weitere Informationen zum Erstellen und Verwenden eines SSH-Tunnels finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

### Basis-URI

Der Basis-URI für die REST-API auf Linux-basierten HDInsight-Clustern ist auf dem Hauptknoten unter ****https://HEADNODEFQDN:8744/api/v1/** verfügbar. Der Domänenname des Hauptknotens wird während der Clustererstellung generiert und ist nicht statisch.

Sie können den vollqualifizierten Domänennamen (FQDN) für den Clusterhauptknoten auf unterschiedliche Arten ermitteln:

* __Über eine SSH-Sitzung__: Verwenden Sie den Befehl `headnode -f` für eine SSH-Sitzung mit dem Cluster.
* __Über Ambari Web__: Wählen Sie oben auf der Seite __Dienste__ und dann __Storm__. Wählen Sie auf der Registerkarte __Zusammenfassung__ die Option __Storm UI-Server__. Der vollqualifizierte Domänenname des Knotens, auf dem die Storm UI und die REST-API ausgeführt wird, wird oben auf der Seite angezeigt.
* __Über die Ambari-REST-API__: Verwenden Sie den Befehl `curl -u admin:PASSWORD -G "https://CLUSTERNAME
.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"`, um Informationen zu dem Knoten abzurufen, auf dem die Storm UI und die REST-API ausgeführt werden. Ersetzen Sie __PASSWORD__ durch das Administratorkennwort für den Cluster. Ersetzen Sie __CLUSTERNAME__ durch den Namen des Clusters. In der Antwort enthält der Eintrag „host\_name“ den vollqualifizierten Domänennamen des Knotens.

### Authentifizierung

Anforderungen an die REST-API müssen die **Standardauthentifizierung** und somit den Benutzernamen und das Kennwort des HDInsight-Clusteradministrators verwenden.

> [AZURE.NOTE]Da die Standardauthentifizierung unverschlüsselt gesendet wird, sollten Sie **immer** HTTPS verwenden, um die Kommunikation mit dem Cluster zu schützen.

### Rückgabewerte

Von der REST-API zurückgegebene Informationen sind möglicherweise nur innerhalb des Clusters oder auf den virtuellen Computern in demselben Azure Virtual Network wie der Cluster verwendbar. Auf den für Zookeeper-Server zurückgegebenen vollqualifizierten Domänennamen (FQDN) kann z. B. nicht über das Internet zugegriffen werden.

## Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Topologien mithilfe des Storm-Dashboards bereitstellen und überwachen, lesen Sie jetzt die Informationen zum [Entwickeln von Java-Topologien mithilfe von Maven](hdinsight-storm-develop-java-topology.md).

Eine Liste weiterer Beispieltopologien finden Sie unter [Beispieltopologien für Storm auf HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=Nov15_HO1-->