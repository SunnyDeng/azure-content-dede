<properties
   pageTitle="Verwenden von Python-Komponenten in einer Storm-Topologie in HDinsight | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie Python-Komponenten mit Apache Storm in Azure HDInsight verwenden. Das Dokument enthält Informationen zur Verwendung von Python-Komponenten in Java- und Clojure-basierten Storm-Topologien."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/01/2016"
   ms.author="larryfr"/>

#Entwickeln von Apache Storm-Topologien mit Python in HDInsight

Apache Storm unterstützt mehrere Sprachen und ermöglicht sogar das Kombinieren von Komponenten in verschiedenen Sprachen in einer Topologie. In diesem Dokument erfahren Sie, wie Sie Python-Komponenten in Ihren Java- und Clojure-basierten Storm-Topologien in HDInsight verwenden.

##Voraussetzungen

* Python 2.7 oder höher

* Java JDK 1.7 oder höher

* [Leiningen](http://leiningen.org/)

##Storm-Unterstützung für mehrere Sprachen

Storm ist für die Verwendung mit in beliebigen Programmiersprachen geschriebenen Komponenten konzipiert. Dies erfordert jedoch, dass die Komponenten „verstehen“, wie die [Thrift-Definition für Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift) verwendet wird. Für Python wird ein Modul als Teil des Apache Storm-Projekts bereitgestellt, das Ihnen eine problemlose Verknüpfung mit Storm ermöglicht. Sie finden dieses Modul unter [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Da Apache Storm ein Java-Prozess ist, der auf der Java Virtual Machine (JVM) ausgeführt wird, werden in anderen Sprachen geschriebene Komponenten als Unterprozesse ausgeführt. Die in der JVM ausgeführten Storm-Bits kommunizieren mithilfe von JSON-Nachrichten, die über „stdin/stdout“ gesendet werden, mit diesen Unterprozessen. Weitere Informationen zur Kommunikation zwischen Komponenten finden Sie in der Dokumentation zum [Multi-Lang Protocol](https://storm.apache.org/documentation/Multilang-protocol.html).

###Das Storm-Modul

Das Storm-Modul (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) stellt die erforderlichen Bits zum Erstellen von Python-Komponenten, die mit Storm funktionieren, bereit.

Das Modul unterstützt u. a. `storm.emit` zum Ausgeben von Tupeln und `storm.logInfo` zum Schreiben in die Protokolle. Ich empfehle Ihnen, diese Datei durchzulesen und sich damit vertraut zu machen, welche Möglichkeiten sie bietet.

##Herausforderungen

Mithilfe des Moduls __storm.py__ können Sie Python-Spouts erstellen, die Daten nutzen, und Bolts, die Daten verarbeiten. Die allgemeine Storm-Topologiedefinition für die Kommunikation zwischen Komponenten wird jedoch nach wie vor in Java oder Clojure geschrieben. Zudem müssen Sie bei der Verwendung von Java auch Java-Komponenten erstellen, die als Schnittstelle zu den Python-Komponenten fungieren.

Da Storm-Cluster verteilt ausgeführt werden, müssen Sie außerdem sicherstellen, dass alle erforderlichen Module für Ihre Python-Komponenten auf sämtlichen Workerknoten im Cluster verfügbar sind. Storm bietet keine Möglichkeit, mit der sich dies für mehrsprachige Ressourcen einfach bewerkstelligen lässt. Sie müssen entweder alle Abhängigkeiten in die JAR-Datei für die Topologie aufnehmen oder Abhängigkeiten manuell auf jedem Workerknoten im Cluster installieren.

Es gibt einige Projekte, die versuchen, diese Mängel zu beheben, z. B. [Pyleus](https://github.com/Yelp/pyleus) und [Streamparse](https://github.com/Parsely/streamparse). Beide Projekte können zwar auf Linux-basierten HDInsight-Clustern ausgeführt werden, erfordern aber Anpassungen beim Einrichten des Clusters und wurden nicht vollständig auf HDInsight-Clustern getestet. Sie bilden daher nicht den Schwerpunkt dieses Dokuments. Hinweise zur Verwendung dieser Frameworks mit HDInsight finden Sie am Ende des Dokuments.

###Java- und Clojure-Topologiedefinitionen im Vergleich

Von den zwei Methoden zum Definieren einer Topologie ist Clojure bei weitem die einfachste/sauberste Methode, da Sie direkt in der Topologiedefinition auf Python-Komponenten verweisen können. Bei Java-basierten Topologiedefinitionen müssen Sie auch Java-Komponenten für bestimmte Aufgaben definieren, z. B. zum Deklarieren der Felder in den von den Python-Komponenten zurückgegebenen Tupeln.

In diesem Dokument werden beide Methoden anhand von Beispielprojekten beschrieben.

##Python-Komponenten mit einer Java-Topologie

> [AZURE.NOTE] Dieses Beispiel finden Sie unter [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) im Verzeichnis __JavaTopology__. Dies ist ein Maven-basiertes Projekt. Falls Sie nicht mit Maven vertraut sind, finden Sie unter [Entwickeln von Java-basierten Topologien mit Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md) weitere Informationen zum Erstellen eines Maven-Projekts für eine Storm-Topologie.

Eine Java-basierte Topologie mit Python (oder anderen JVM-Sprachkomponenten) scheint auf den ersten Blick Java-Komponenten zu verwenden. Wenn Sie sich aber die einzelnen Java-Spouts/-Bolts ansehen, werden Sie Code wie den folgenden finden:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

An dieser Stelle ruft Java Python auf und führt das Skript aus, das die eigentliche Bolt-Logik enthält. Die Java-Spouts/-Bolts (in diesem Beispiel) deklarieren lediglich die Felder im Tupel, das von der zugrunde liegenden Python-Komponente ausgegeben wird.

Die eigentlichen Python-Dateien werden in diesem Beispiel im Verzeichnis `/multilang/resources` gespeichert. Auf das Verzeichnis `/multilang` wird in der Datei __pom.xml__ verwiesen:

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

Dadurch werden alle Dateien im Ordner `/multilang` in die JAR-Datei eingeschlossen, die aus diesem Projekt erstellt wird.

> [AZURE.IMPORTANT] Beachten Sie, dass hiermit nur das Verzeichnis `/multilang` angegeben wird und nicht `/multilang/resources`. Da Storm Nicht-JVM-Ressourcen in einem `resources`-Verzeichnis erwartet, wird bereits intern danach gesucht. Die Platzierung von Komponenten in diesem Ordner ermöglicht es Ihnen lediglich, im Java-Code anhand des Namens auf sie zu verweisen. Beispiel: `super("python", "countbolt.py");`. Sie können sich das so vorstellen, als würde Storm das Verzeichnis `resources` beim Zugriff auf mehrsprachige Ressourcen als das Stammverzeichnis (/) betrachten.
>
> Bei diesem Beispielprojekt ist das Modul `storm.py` im Verzeichnis `/multilang/resources` enthalten.

###Erstellen und Ausführen des Projekts

Zur lokalen Ausführung können Sie das Projekt einfach mit dem folgenden Maven-Befehl erstellen und im lokalen Modus ausführen:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Verwenden Sie STRG+C, um den Prozess zu beenden.

Um das Projekt auf einem HDInsight-Cluster mit Apache Storm bereitzustellen, gehen Sie wie folgt vor:

1. Erstellen Sie ein Uberjar:

        mvn package

    Dadurch wird eine Datei mit dem Namen __WordCount--1.0-SNAPSHOT.jar__ im Verzeichnis `/target` für das Projekt erstellt.

2. Laden Sie die JAR-Datei mit einer der folgenden Methoden in den Hadoop-Cluster hoch:

    * Für __Linux-basierte__ HDInsight-Cluster: Verwenden Sie `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar`, um die JAR-Datei in den Cluster zu kopieren, und ersetzen Sie dabei USERNAME durch Ihren SSH-Benutzernamen und CLUSTERNAME durch den Namen des HDInsight-Clusters.

        Nachdem die Datei hochgeladen wurde, stellen Sie über SSH eine Verbindung mit dem Cluster her, und starten Sie die Topologie mit `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`.

    * Für __Windows-basierte__ HDInsight-Cluster: Stellen Sie eine Verbindung mit dem Storm-Dashboard her, indem Sie in Ihrem Browser zu HTTPS://CLUSTERNAME.azurehdinsight.net/ wechseln. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters, und geben Sie den Administratornamen und das Kennwort ein, wenn Sie dazu aufgefordert werden.

        Führen Sie mithilfe des Formulars die folgenden Aktionen aus:

        * __JAR-Datei__: Klicken Sie auf __Durchsuchen__, und wählen Sie dann die Datei __WordCount-1.0-SNAPSHOT.jar__ aus.
        * __Klassenname__: Geben Sie `com.microsoft.example.WordCount` ein.
        * __Zusätzliche Parameter__: Geben Sie einen Anzeigenamen (z. B. `wordcount`) zum Identifizieren der Topologie ein.

        Klicken Sie abschließend auf __Senden__, um die Topologie zu starten.

> [AZURE.NOTE] Eine gestartete Storm-Topologie wird ausgeführt, bis sie beendet (abgebrochen) wird. Führen Sie zum Beenden der Topologie entweder den Befehl `storm kill TOPOLOGYNAME` über die Befehlszeile aus (z. B. in einer SSH-Sitzung mit einem Linux-Cluster), oder wählen Sie in der Storm-Benutzeroberfläche die Topologie aus, und klicken Sie dann auf die Schaltfläche __Kill__.

##Python-Komponenten mit einer Clojure-Topologie

> [AZURE.NOTE] Dieses Beispiel finden Sie unter [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) im Verzeichnis __ClojureTopology__.

Diese Topologie wurde mithilfe von [Leiningen](http://leiningen.org) erstellt, um [ein neues Clojure-Projekt zu erstellen](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Danach wurden die folgenden Änderungen am erstellten Projekt vorgenommen:

* __project.clj__: Es wurden Abhängigkeiten für Storm und Ausschlüsse für Elemente hinzugefügt, die bei der Bereitstellung auf dem HDInsight-Server Probleme verursachen können.
* __resources/resources__: Leiningen erstellt ein Standardverzeichnis `resources`, die darin gespeicherten Dateien werden jedoch anscheinend dem Stamm der aus dem Projekt erstellten JAR-Datei hinzugefügt, und Storm erwartet Dateien in einem Unterverzeichnis mit dem Namen `resources`. Daher wurde ein Unterverzeichnis hinzugefügt, und die Python-Dateien werden in `resources/resources` gespeichert. Zur Laufzeit wird dieses Unterverzeichnis als das Stammverzeichnis (/) für den Zugriff auf Python-Komponenten behandelt.
* __src/wordcount/core.clj__: Diese Datei enthält die Topologiedefinition. Auf die Datei wird in __project.clj__ verwiesen. Weitere Informationen zur Verwendung von Clojure zum Definieren einer Storm-Topologie finden Sie unter [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###Erstellen und Ausführen des Projekts

Verwenden Sie den folgenden Befehl, um das __Projekt zu erstellen und lokal auszuführen __:

    lein do clean, run

Verwenden Sie zum Beenden der Topologie __STRG+C__.

Gehen Sie wie folgt vor, um __ein Uberjar zu erstellen und in HDInsight bereitzustellen__:

1. Erstellen Sie ein Uberjar, das die Topologie und die erforderlichen Abhängigkeiten enthält:

        lein uberjar

    Dadurch wird eine neue Datei namens `wordcount-1.0-SNAPSHOT.jar` im Verzeichnis `target\uberjar+uberjar` erstellt.
    
2. Verwenden Sie eine der folgenden Methoden, um die Topologie in einem HDInsight-Cluster bereitzustellen und auszuführen:

    * __HDInsight (Linux-basiert)__
    
        1. Kopieren Sie die Datei mit `scp` in den Hauptknoten des HDInsight-Clusters. Beispiel:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Ersetzen Sie USERNAME durch einen SSH-Benutzer für Ihren Cluster und CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.
            
        2. Nachdem die Datei in den Cluster kopiert wurde, stellen Sie über SSH eine Verbindung mit dem Cluster her, und übermitteln Sie den Auftrag. Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:
        
            * [Verwenden von SSH mit Linux-basiertem HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Verwenden von SSH mit Linux-basiertem HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Führen Sie nach dem Herstellen der Verbindung den folgenden Befehl aus, um die Topologie zu starten:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight (Windows-basiert)__
    
        1. Stellen Sie eine Verbindung mit dem Storm-Dashboard her, indem Sie in Ihrem Browser zu HTTPS://CLUSTERNAME.azurehdinsight.net/ wechseln. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters, und geben Sie den Administratornamen und das Kennwort ein, wenn Sie dazu aufgefordert werden.

        2. Führen Sie mithilfe des Formulars die folgenden Aktionen aus:

            * __JAR-Datei__: Klicken Sie auf __Durchsuchen__, und wählen Sie dann die Datei __wordcount-1.0-SNAPSHOT.jar__ aus.
            * __Klassenname__: Geben Sie `wordcount.core` ein.
            * __Zusätzliche Parameter__: Geben Sie einen Anzeigenamen (z. B. `wordcount`) zum Identifizieren der Topologie ein.

            Klicken Sie abschließend auf __Senden__, um die Topologie zu starten.

> [AZURE.NOTE] Eine gestartete Storm-Topologie wird ausgeführt, bis sie beendet (abgebrochen) wird. Führen Sie zum Beenden der Topologie entweder den Befehl `storm kill TOPOLOGYNAME` über die Befehlszeile aus (in einer SSH-Sitzung mit einem Linux-Cluster), oder wählen Sie in der Storm-Benutzeroberfläche die Topologie aus, und klicken Sie dann auf die Schaltfläche __Kill__.

##Pyleus-Framework

[Pyleus](https://github.com/Yelp/pyleus) ist ein Framework, das folgende Funktionen bereitstellt, um die Verwendung von Python mit Storm zu erleichtern:

* __YAML-basierte Topologiedefinitionen__: Diese Funktion vereinfacht das Definieren der Topologie, da keine Kenntnisse in Java oder Clojure erforderlich sind.
* __MessagePack-basiertes Serialisierungsprogramm__: Anstelle von JSON wird MessagePack als Standardserialisierung verwendet. Dadurch kann der Nachrichtenaustausch zwischen Komponenten beschleunigt werden.
* __Abhängigkeitsverwaltung__: Mit Virtualenv wird sichergestellt, dass Python-Abhängigkeiten auf allen Workerknoten bereitgestellt werden. Dies erfordert die Installation von Virtualenv auf den Workerknoten.

> [AZURE.IMPORTANT] Pyleus erfordert Storm in Ihrer Entwicklungsumgebung. Die Verwendung der Apache Storm 0.9.3-Basisverteilung scheint zu JAR-Dateien zu führen, die nicht mit der mit HDInsight bereitgestellten Version von Storm kompatibel sind. In den folgenden Schritten wird daher der HDInsight-Cluster als Entwicklungsumgebung verwendet.

Sie können die Pyleus-Beispieltopologien erstellen, indem Sie den HDInsight-Hauptknoten als Buildumgebung verwenden:

1. Beim Bereitstellen einer neuen Storm-Topologie auf einem HDInsight-Cluster müssen Sie sicherstellen, dass Python Virtualenv auf den Clusterknoten installiert ist. Verwenden Sie beim Erstellen eines neuen Linux-basierten HDInsight-Clusters die folgenden Skriptaktionseinstellungen für die [Clusteranpassung](hdinsight-hadoop-customize-cluster.md):

    * __Name__: Geben Sie nur einen Anzeigenamen an.
    * \_\_ Skript-URI\_\_: Verwenden Sie `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` als Wert. Das Skript installiert Python Virtualenv auf den Knoten.
    
        > [AZURE.NOTE] Außerdem werden einige Verzeichnisse erstellt, die an späterer Stelle dieses Dokuments vom Streamparse-Framework verwendet werden.
        
    * __Nimbus__: Aktivieren Sie diesen Eintrag, damit das Skript auf die Nimbus-Knoten (Hauptknoten) angewendet wird.
    * __Supervisor__: Aktivieren Sie diesen Eintrag, damit das Skript auf die Supervisorknoten (Workerknoten) angewendet wird.
    
    Lassen Sie die anderen Einträge leer.

1. Stellen Sie nach dem Erstellen des Clusters über SSH eine Verbindung her:

    * [Verwenden von SSH mit Linux-basiertem HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Verwenden von SSH mit Linux-basiertem HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Verwenden Sie in der SSH-Sitzung die folgenden Befehle, um eine neue virtuelle Umgebung zu erstellen und Pyleus zu installieren:

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. Laden Sie anschließend das Pyleus-Git-Repository herunter, und erstellen Sie das WordCount-Beispiel:

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    Danach enthält das aktuelle Verzeichnis eine neue Datei mit dem Namen `word_count.jar`.
    
4. Verwenden Sie den folgenden Befehl, um die Topologie an den Storm-Cluster zu senden:

        pyleus submit -n localhost word_count.jar
    
    Der Parameter `-n` gibt den Nimbus-Host an. Da wir auf dem Hauptknoten arbeiten, können wir `localhost` verwenden.
    
    Sie können auch den Befehl `pyleus` verwenden, um andere Storm-Aktionen auszuführen. Verwenden Sie die folgenden Befehle, um die aktiven Topologien aufzulisten und anschließend die `word_count`-Topologie zu beenden:
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Streamparse-Framework

[Streamparse](https://github.com/Parsely/streamparse) ist ein Framework, das folgende Funktionen bereitstellt, um die Verwendung von Python mit Storm zu erleichtern:

* __Gerüstbau__: Dank dieser Funktion können Sie das Gerüst für ein Projekt einfach erstellen und anschließend Dateien ändern, um Ihre Logik hinzuzufügen.
* __Clojure DSL-Funktionen__: Diese Funktionen verringern den Ausführlichkeitsgrad der Verwendung von Python-Komponenten in einer Clojure-Topologiedefinition.
* __Abhängigkeitsverwaltung__: Mit Virtualenv wird sichergestellt, dass Python-Abhängigkeiten auf allen Workerknoten bereitgestellt werden. Dies erfordert die Installation von Virtualenv auf den Workerknoten.
* __Remotebereitstellung__: Streamparse kann die SSH-Automatisierung verwenden, um Komponenten auf Workerknoten bereitzustellen, und erstellt einen SSH-Tunnel für die Kommunikation mit Nimbus. Die Bereitstellung von Ihrer Entwicklungsumgebung auf Linux-basierten Clustern wie HDInsight ist daher problemlos möglich.

> [AZURE.IMPORTANT] Streamparse beruht auf Komponenten, die [Unix-Signale](https://en.wikipedia.org/wiki/Unix_signal) erwarten, die unter Windows nicht verfügbar sind. Sie müssen daher eine Linux-, Unix- oder OS X-Entwicklungsumgebung und einen Linux-basierten HDInsight-Cluster verwenden.

1. Beim Bereitstellen einer neuen Storm-Topologie auf einem HDInsight-Cluster müssen Sie sicherstellen, dass Python Virtualenv auf den Clusterknoten installiert ist. Verwenden Sie beim Erstellen eines neuen Linux-basierten HDInsight-Clusters die folgenden Skriptaktionseinstellungen für die [Clusteranpassung](hdinsight-hadoop-customize-cluster.md):

    * __Name__: Geben Sie nur einen Anzeigenamen an.
    * \_\_ Skript-URI\_\_: Verwenden Sie `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` als Wert. Dieses Skript installiert Python Virtualenv auf den Knoten und erstellt von Streamparse verwendete Verzeichnisse.
    * __Nimbus__: Aktivieren Sie diesen Eintrag, damit das Skript auf die Nimbus-Knoten (Hauptknoten) angewendet wird.
    * __Supervisor__: Aktivieren Sie diesen Eintrag, damit das Skript auf die Supervisorknoten (Workerknoten) angewendet wird.
    
    Lassen Sie die anderen Einträge leer.
    
    > [AZURE.WARNING] Zur Remotebereitstellung mit Streamparse müssen Sie außerdem einen __öffentlichen Schlüssel__ verwenden, um den SSH-Benutzer für Ihren HDInsight-Cluster zu sichern.
    >
    > Weitere Informationen zur Verwendung von Schlüsseln mit SSH in HDInsight finden Sie in den folgenden Dokumenten:
    >
    > * [Verwenden von SSH mit Linux-basiertem HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Verwenden von SSH mit Linux-basiertem HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Installieren Sie während der Clusterbereitstellung mit dem folgenden Befehl Streamparse in Ihrer Entwicklungsumgebung:

        pip install streamparse
        
3. Führen Sie nach der Installation von Streamparse den folgenden Befehl aus, um ein Beispielprojekt zu erstellen:

        sparse quickstart wordcount
        
    Dadurch wird ein neues Verzeichnis namens `wordcount` erstellt und mit einem Word Count-Beispielprojekt aufgefüllt.

4. Ändern Sie die Verzeichnisse in das Verzeichnis `wordcount`, und starten Sie die Topologie im lokalen Modus:

        cd wordcount
        sparse run

    Verwenden Sie zum Beenden der Topologie STRG+C.

###Bereitstellen der Topologie

Gehen Sie nach dem Erstellen Ihres Linux-basierten HDInsight-Clusters wie folgt vor, um die Topologie im Cluster bereitzustellen:

1. Verwenden Sie den folgenden Befehl, um den vollqualifizierten Domänennamen der Workerknoten für Ihren Cluster zu ermitteln:

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    Dadurch werden die Hostinformationen für den Cluster abgerufen, an „grep“ umgeleitet und die Einträge für die Workerknoten zurückgegeben. Die Ergebnisse sollten in etwa wie folgt aussehen:
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    Speichern Sie den Wert `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"`, da er im nächsten Schritt verwendet wird.

2. Öffnen Sie die Datei __config.json__ im Verzeichnis `wordcount`, und ändern Sie die folgenden Einträge:

    * __user__: Legen Sie diesen Eintrag auf den Namen des SSH-Benutzerkontos fest, das Sie für den HDInsight-Cluster konfiguriert haben. Dieser Name wird bei der Bereitstellung für die Authentifizierung im Cluster verwendet.
    * __nimbus__: Legen Sie diesen Eintrag auf `CLUSTERNAME-ssh.azurehdinsight.net` fest. Ersetzen Sie CLUSTERNAME durch den Namen Ihres Clusters. Dieser Wert wird bei der Kommunikation mit dem Nimbus-Knoten verwendet, dem Hauptknoten des Clusters.
    * __workers__: Füllen Sie diesen Eintrag mit den mit „curl“ abgerufenen Hostnamen für die Workerknoten auf. Beispiel:
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__: Legen Sie diesen Eintrag auf „/virtualenv“ fest.
    
    Dadurch wird das Projekt für Ihren HDInsight-Cluster konfiguriert, einschließlich des Verzeichnisses `/virtualenv`, das während der Bereitstellung durch die Skriptaktion erstellt wurde.

4. Da Ihre Authentifizierung bei der Streamparse-Bereitstellung in HDInsight über den Hauptknoten an die Workerknoten weitergeleitet werden muss, muss `ssh-agent` auf der Arbeitsstation gestartet werden. Bei den meisten Betriebssystemen wird der Agent automatisch gestartet. Überprüfen Sie mit dem folgenden Befehl, ob der Agent ausgeführt wird:

        echo "$SSH_AUTH_SOCK"
    
    Wenn `ssh-agent` ausgeführt wird, sollte die Antwort in etwa wie folgt aussehen:
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE] Der vollständige Pfad kann je nach Betriebssystem unterschiedlich sein. Bei OS X kann der Pfad z. B. `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners` lauten. Wenn der Agent ausgeführt wird, sollte jedoch in jedem Fall ein Pfad zurückgegeben werden.
    
    Falls nichts zurückgegeben wird, starten Sie den Agent mit dem Befehl `ssh-agent`.
    
5. Stellen Sie sicher, dass der Agent über den Schlüssel verfügt, den Sie zur Authentifizierung beim HDInsight-Server verwenden. Führen Sie den folgenden Befehl aus, um die für den Agent verfügbaren Schlüssel aufzulisten:

        ssh-add -L
    
    Dieser Befehl gibt die privaten Schlüssel zurück, die dem Agent hinzugefügt wurden. Sie können die Ergebnisse mit dem Inhalt des privaten Schlüssels vergleichen, den Sie beim Erstellen eines SSH-Schlüssels zur Authentifizierung in HDInsight generiert haben.
    
    Wenn keine Informationen zurückgegeben werden oder die zurückgegebenen Informationen nicht mit Ihrem privaten Schlüssel übereinstimmen, verwenden Sie den folgenden Befehl, um dem Agent den privaten Schlüssel hinzuzufügen:
    
        ssh-add /path/to/key/file
    
    Beispiel: `ssh-add ~/.ssh/id_rsa`.

4. Sie müssen auch SSH konfigurieren, damit für Ihren HDInsight-Cluster die Weiterleitung verwendet wird. Fügen Sie `~/.ssh/config` den folgenden Code hinzu: Falls diese Datei nicht vorhanden ist, erstellen Sie sie mit dem folgenden Inhalt:

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.
    
    Dadurch wird der SSH-Agent auf der Arbeitsstation so konfiguriert, dass er die Weiterleitung Ihrer SSH-Anmeldeinformationen über beliebige *.azurehdinsight.net-Systeme ermöglicht, mit denen Sie eine Verbindung herstellen. In diesem Fall erfolgt die Weiterleitung über den Hauptknoten des Clusters. Anschließend wird der Befehl konfiguriert, der für SSH-Proxydatenverkehr vom Hauptknoten zu den einzelnen Workerknoten verwendet wird (internal.cloudapp.net). Dadurch kann Streamparse mit der Schlüsselauthentifizierung für Ihr SSH-Konto eine Verbindung mit dem Hauptknoten und dann von dort aus eine Verbindung mit den einzelnen Workerknoten herstellen.
    
5. Zum Schluss verwenden Sie den folgenden Befehl, um die Topologie aus Ihrer lokalen Entwicklungsumgebung an den HDInsight-Cluster zu senden:

        sparse submit
    
    Dieser Befehl stellt eine Verbindung mit dem HDInsight-Cluster her, stellt die Topologie sowie alle Python-Abhängigkeiten bereit und startet anschließend die Topologie.

##Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Python-Komponenten in einer Storm-Topologie verwendet werden. In den folgenden Dokumenten werden weitere Möglichkeiten zur Verwendung von Python mit HDInsight beschrieben:

* [Verwenden von Python für Streaming-MapReduce-Aufträge ](hdinsight-hadoop-streaming-python.md)
* [Verwenden von benutzerdefinierten Python-Funktionen (UDFs) in Pig und Hive](hdinsight-python.md)

<!---HONumber=AcomDC_0204_2016-->