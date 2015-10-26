<properties
   pageTitle="Verarbeiten von Ereignissen aus Event Hubs mit Storm in HDInsight mit Java | Azure"
   description="Erfahren Sie, wie Sie Daten von Event Hubs mit einer mit Maven erstellten Java Storm-Topologie erstellen."
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
   ms.date="10/09/2015"
   ms.author="larryfr"/>

# Verarbeiten von Ereignissen aus Azure Event Hubs mit Storm in HDInsight (Java)

Azure Event Hubs ermöglichen Ihnen die Verarbeitung sehr großer Datenmengen aus Websites, Apps und Geräten. Der Event Hubs-Spout vereinfacht die Verwendung von Apache Storm in HDInsight, um diese Daten in Echtzeit zu analysieren. Sie können auch Daten aus Storm mithilfe des Event Hubs-Bolts in Event Hub schreiben.

In diesem Lernprogramm erfahren Sie, wie Sie den Event-Hub-Spout und Bolt zum Lesen und Schreiben von Daten in einer Java-basierten Storm-Topologie verwenden.

## Voraussetzungen

* Ein Apache Storm-Cluster in HDInsight. Verwenden Sie einen der folgenden Artikel zu ersten Schritten, um einen Cluster zu erstellen:

    - Ein [Linux-basierter Cluster](hdinsight-apache-storm-tutorial-get-started-linux.md): Wählen Sie diese Option, wenn Sie SSH zum Arbeiten mit dem Cluster auf Linux, Unix, OS X oder Windows-Clients verwenden möchten.

    - Ein [Windows-basierter Cluster](hdinsight-apache-storm-tutorial-get-started.md): Wählen Sie diese Option, wenn Sie PowerShell zum Arbeiten mit dem Cluster auf einem Windows-Client verwenden möchten.

    > [AZURE.NOTE]Die beiden Cluster unterscheiden sich nur darin, ob Sie SSH zum Übermitteln der Topologie an den Cluster oder ein Webformular verwenden.

* Ein [Azure Event Hub](../event-hubs/service-bus-event-hubs-csharp-ephcs-getstarted.md)

* [Oracle Java Developer Kit (JDK) Version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) oder Äquivalent, z. B. [OpenJDK](http://openjdk.java.net/)

* [Maven](https://maven.apache.org/download.cgi) ist ein Projekterstellungssystem für Java-Projekte

* Ein Texteditor oder eine integrierte Entwicklungsumgebung (IDE) für Java

	> [AZURE.NOTE]Der Editor oder die IDE verfügen möglicherweise über bestimmte Funktionen für die Arbeit mit Maven, die in diesem Dokument nicht behandelt werden. Informationen zu den Funktionen der Bearbeitungsumgebung finden Sie in der Dokumentation für das von Ihnen verwendete Produkt.

 * Einen SSH-Client Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

    - [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* Ein SCP-Client. Dieser wird auf allen Linux-, Unix- und OS X-Systemen bereitgestellt. Bei Windows-Clients empfehlen wir PSCP, der auf der [PuTTY-Downloadseite](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) verfügbar ist.

##Grundlegendes zum Beispiel

Das [hdinsight-java-storm-eventub](https://github.com/Blackmist/hdinsight-java-storm-eventhub) Beispiel enthält zwei Topologien:

__com.microsoft.example.EventHubWriter__ schreibt zufällige Daten in ein Azure Event Hub. Die Daten werden von einem Spout generiert und sind zufällige Geräte-IDs und Gerät-Werte. Also wird Hardware simuliert, die eine Zeichenfolgen-ID und einen numerischen Wert ausgibt.

__com.microsoft.example.EventHubReader__ liest Daten vom Event Hub (die von EventHubWriter geschriebenen Daten) und speichert diese in HDFS (in diesem Fall WASB, da Schreiben und Testen in Azure HDInsight erfolgt sind) im Verzeichnis /devicedata.

Die Daten werden vor dem Schreiben in den Event Hub als JSON-Dokument formatiert, und beim Lesen durch den Reader werden sie außerhalb von JSON in Tupel analysiert. Das JSON-Format sieht folgendermaßen aus:

    { "deviceId": "unique identifier", "deviceValue": some value }

Wir verwenden ein JSON-Dokument zum Speichern der Daten in Event Hub ist, da wir das Format kennen und uns nicht auf die interne Formatierungsmechanik des Event-Hub-Spouts und Bolts verlassen.

###Projektkonfiguration

Die **POM.xml** Datei enthält Konfigurationsinformationen für dieses Maven-Projekt. Die interessantesten davon sind:

####Die EventHubs Storm Spout-Abhängigkeit

    <dependency>
      <groupId>com.microsoft.eventhubs</groupId>
      <artifactId>eventhubs-storm-spout</artifactId>
      <version>0.9.3</version>
    </dependency>

Dadurch wird eine Abhängigkeit für das eventhubs-storm-spout-Paket erstellt, das ein Spout zum Lesen aus Event Hubs sowie ein Bolt zum Schreiben in diesen Event Hub enthält.

> [AZURE.NOTE]Dieses Paket ist für Maven nicht verfügbar und wird in einem späteren Schritt manuell in Ihrem lokalen Repository installiert.

####Die HdfsBolt- und WASB-Komponenten

Der HdfsBolt wird in der Regel zum Speichern von Daten im Hadoop Distributed File System HDFS verwendet. Jedoch nutzen HDInsight-Cluster Azure-Speicher (WASB) als Standard-Informationsspeicher, sodass wir mehrere Komponenten laden müssen, die HdfsBolt ermöglichen, das WASB-Dateisystem zu verstehen.

      <!--HdfsBolt stuff -->
      <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
        <exclusions>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
          </exclusion>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
          </exclusion>
        </exclusions>
        <version>0.9.3</version>
      </dependency>
      <!--
     This is a temporary workaround to make HdfsBolt work with WASB through hadoop-azure project.
     For now, we have to build hadoop-client, hadoop-hdfs and hadoop-azure from Hadoop trunk
     (which defaults to 3.0.0-SNAPSHOT version). And push those jars and dependencies to local
     mvn repo (take a look at push_lib_mvn.ps1).

     Once Hadoop 2.7 is released, we can just switch to that version.
     Note that hadoop-azure is added to Hadoop on Hadoop 2.7.
     -->
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-client</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-hdfs</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-azure</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-common</artifactId>
       <version>3.0.0-SNAPSHOT</version>
       <exclusions>
         <exclusion>
           <groupId>org.slf4j</groupId>
           <artifactId>slf4j-log4j12</artifactId>
         </exclusion>
       </exclusions>
     </dependency>
     <dependency>
       <groupId>com.microsoft.windowsazure.storage</groupId>
       <artifactId>microsoft-windowsazure-storage-sdk</artifactId>
       <version>0.6.0</version>
     </dependency>

> [AZURE.NOTE]Die Pakete zum Aktivieren von WASB sind für das Maven-Repository nicht verfügbar und werden in einem späteren Schritt manuell installiert.

####Das maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Dadurch erfährt Maven, dass das Projekt mit Kompatibilität für Java 7 kompiliert werden soll, da diese Version von HDInsight-Clustern verwendet wird.

####Das maven-shade-plugin

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <!-- Keep us from getting a can't overwrite file error -->
          <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
          </transformers>
          <!-- Keep us from getting a bad signature error -->
          <filters>
            <filter>
                <artifact>*:*</artifact>
                <excludes>
                    <exclude>META-INF/*.SF</exclude>
                    <exclude>META-INF/*.DSA</exclude>
                    <exclude>META-INF/*.RSA</exclude>
                </excludes>
            </filter>
        </filters>
        </configuration>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

Hiermit wird die Lösung in ein Uberjar gepackt, das den Projektcode und die erforderlichen Abhängigkeiten enthält. Es wird auch für folgende Zwecke verwendet:

* Umbenennen von Lizenzdateien für die Abhängigkeiten: Wenn das nicht erfolgt, kann das Laufzeitfehler in Windows-basierten HDInsight-Clustern zur Folge haben.

* Ausschließen von Sicherheit/Signaturen: Wenn das nicht erfolgt, kann das Laufzeitfehler im HDInsight-Cluster zur Folge haben.

####Das exec-maven-plugin

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.2.1</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Hiermit können Sie die Topologie einfach in Ihrer Entwicklungsumgebung ausführen. Geben Sie dazu folgenden Befehl ein:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Beispiel: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####Der Abschnitt „Ressourcen“

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Hier werden erforderliche Ressourcen für das Projekt definiert:

- **EventHubs.properties**: enthält Informationen zum Verbinden mit einem Azure Event Hub
- **core-site.xml**: enthält Informationen zu dem Azure-Speicher, der vom HDInsight-Cluster verwendet wird.

Sie müssen für beide Informationen zu Ihrem Event Hub und HDInsight-Cluster eingeben.

##Konfigurieren von Umgebungsvariablen

Bei der Installation von Java und dem JDK auf Ihrer Entwicklungsworkstation können die folgenden Umgebungsvariablen festgelegt werden. Sie sollten dennoch prüfen, ob die Variablen vorhanden sind und korrekte Werte für Ihr System enthalten.

* **JAVA\_HOME** – sollte auf das Verzeichnis verweisen, in dem die Java-Laufzeitumgebung (Java Runtime Environment, JRE) installiert ist. Für eine Unix- oder Linux-Distribution sollte z. B. ein Wert wie `/usr/lib/jvm/java-7-oracle` verwendet werden. Unter Windows sollte der Wert so ähnlich sein wie `c:\Program Files (x86)\Java\jre1.7`

* **PATH** – sollte die folgenden Pfade enthalten:

	* **JAVA\_HOME** (oder den entsprechenden Pfad)

	* **JAVA\_HOME\\bin** (oder den entsprechenden Pfad)

	* Das Verzeichnis, in dem Maven installiert ist.

## Konfigurieren von Event Hub

Event Hubs ist die Datenquelle für dieses Beispiel. Führen Sie die folgenden Schritte aus, um einen neuen Event Hub zu erstellen.

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com) die Optionen **NEU** > **Service Bus** > **Event Hub** > **Benutzerdefiniert erstellen**.

2. Geben Sie im Bildschirm **Neuen Event Hub hinzufügen** einen Wert für **Event Hub-Name** ein, wählen Sie die **Region** aus, in der der Hub erstellt werden soll, und erstellen Sie einen neuen Namespace, oder wählen Sie einen vorhandenen Namespace aus. Klicken Sie auf den **Pfeil**, um fortzufahren.

	![Seite 1 des Assistenten](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE]Wählen Sie den gleichen **Speicherort** aus wie für Ihren Storm auf HDInsight-Server, um Latenz und Kosten zu verringern.

2. Geben Sie im Bildschirm **Event Hub konfigurieren** die Werte für **Partitionsanzahl** und **Nachrichtenaufbewahrung** ein. Verwenden Sie für dieses Beispiel eine Anzahl von 10 Partitionen und eine Nachrichtenaufbewahrung von 1. Notieren Sie sich die Partitionsanzahl, da Sie diesen Wert später benötigen werden.

	![Seite 2 des Assistenten](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Nachdem der Event Hub erstellt wurde, wählen Sie den Namespace, wählen Sie **Event Hubs** aus, und wählen Sie dann den zuvor erstellten Event Hub aus.

4. Klicken Sie auf **Konfigurieren**, und erstellen Sie zwei neue Zugriffsrichtlinien mit den folgenden Informationen.

	<table>
<tr><th>Name</th><th>Berechtigungen</th></tr>
<tr><td>Writer</td><td>Send</td></tr>
<tr><td>Leser</td><td>Empfangen</td></tr>
</table>Klicken Sie nach dem Erstellen der Berechtigungen auf das Symbol **Speichern** am unteren Seitenrand. Auf diese Weise werden die Richtlinien für den gemeinsamen Zugriff erstellt, die zum Senden (writer) und Empfangen (reader) von Daten an diesen Event Hub bzw. von diesem verwendet werden.

	![Richtlinien](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Verwenden Sie nach dem Speichern der Richtlinien den **Freigegebener Generator für Zugriffsschlüssel** am unteren Seitenrand, um den Schlüssel für die Richtlinien **writer** und **reader** abzurufen. Speichern Sie diese Schlüssel für den späteren Gebrauch.

## Herunterladen und Erstellen des Projekts

1. Laden Sie das Projekt von GitHub herunter: [hdinsight-java-storm-eventhub](https://github.com/Blackmist/hdinsight-java-storm-eventhub). Sie können das Paket als Zip-Archiv herunterladen oder [git](https://git-scm.com/) verwenden, um das Projekt lokal zu klonen.

2. Verwenden Sie die folgenden Befehle, um die im Projekt enthaltenen Pakete in Ihr lokales Maven-Repository installieren. Diese aktivieren Event-Hub-Spouts und Bolts und ermöglichen die Verwendung von HdfsBolt zum Schreiben in den Azure-Speicher (WASB).

		mvn -q install:install-file -Dfile=lib/eventhubs/eventhubs-storm-spout-0.9.3-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9.3 -Dpackaging=jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-azure-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-client-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-hdfs-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.jar -DpomFile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom

	> [AZURE.NOTE]Wenn Sie Powershell verwenden, müssen Sie möglicherweise die `-D`-Parameter in Anführungszeichen setzen. Beispiel: `"-Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom"`.

	Auch diese Dateien stammen ursprünglich aus https://github.com/hdinsight/hdinsight-storm-examples, sodass Sie die neuesten Versionen vorfinden können.

3. Verwenden Sie die folgenden Elemente, um das Projekt zu erstellen und zu packen:

        mvn package

    Damit laden Sie die erforderlichen Abhängigkeiten herunter und können dann das Projekt erstellen und packen. Die Ausgabe wird im Verzeichnis __/target__ als __EventHubExample-1.0-SNAPSHOT.jar__ gespeichert.

## Bereitstellen der Topologien

Die von diesem Projekt erstellte JAR-Datei enthält zwei Topologien: __com.microsoft.example.EventHubWriter__ und __com.microsoft.example.EventHubReader__. Die Topologie EventHubWriter sollte zuerst gestartet werden, da sie Ereignisse in ein Event Hub schreibt, die dann vom EventHubReader gelesen werden.

###Bei Verwendung eines Linux-basierten Clusters

1. Kopieren Sie das JAR-Paket mit SCP in Ihren HDInsight-Cluster. Ersetzen Sie USERNAME durch den SSH-Benutzer Ihres Clusters. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Wenn Sie ein Kennwort für das SSH-Konto verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel mit dem Konto verwendet haben, müssen Sie möglicherweise den `-i`-Parameter verwenden, um den Pfad zur Schlüsseldatei anzugeben. Beispiel: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE]Wenn es sich bei Ihrem Client um eine Windows-Arbeitsstation handelt, ist möglicherweise kein SCP-Befehl installiert. Wir empfehlen PSCP, das Sie auf der [PuTTY-Downloadseite](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) herunterladen können.

    Mit diesem Befehl wird die Datei in das Basisverzeichnis des SSH-Benutzers im Cluster kopiert.

1. Nachdem der Upload der Datei abgeschlossen wurde, können Sie SSH zum Verbinden mit dem HDInsight-Cluster verwenden. Ersetzen Sie **USERNAME** durch den Namen der SSH-Anmeldung. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres HDInsight-Clusters:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE]Wenn Sie ein Kennwort für das SSH-Konto verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel mit dem Konto verwendet haben, müssen Sie möglicherweise den `-i`-Parameter verwenden, um den Pfad zur Schlüsseldatei anzugeben. Im folgenden Beispiel wird der private Schlüssel aus `~/.ssh/id_rsa` geladen:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    Wenn Sie PuTTY verwenden, geben Sie `CLUSTERNAME-ssh.azurehdinsight.net` im Feld __Hostname (oder IP-Adresse)__ ein, und klicken Sie dann auf __Öffnen__, um eine Verbindung herzustellen. Sie werden zum Eingeben Ihres SSH-Kontonamens aufgefordert.

    > [AZURE.NOTE]Wenn Sie ein Kennwort für das SSH-Konto verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel mit dem Konto verwendet haben, müssen Sie möglicherweise die folgenden Schritte ausführen, um den Schlüssel auszuwählen:
    >
    > 1. Erweitern Sie in **Category** erst **Connection**, dann **SSH**, und wählen Sie anschließend **Auth** aus.
    > 2. Klicken Sie auf **Durchsuchen**, und wählen Sie die PPK-Datei aus, die Ihren privaten Schlüssel enthält.
    > 3. Klicken Sie auf __Öffnen__, um eine Verbindung herzustellen.

2. Verwenden Sie den folgenden Befehl, um die Website zu starten.

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Dadurch werden die Topologien gestartet und mit den Namen „Reader“ und „Writer“ versehen.

3. Geben Sie den Topologien ein oder zwei Minuten, um die Ereignisse vom Event Hub zu schreiben und lesen, und prüfen Sie dann mit dem folgenden Befehl, ob der EventHuabReader Daten in Ihrem HDInsight-Speicher speichert:

        hadoop fs -ls /devicedata

    Daraufhin sollte eine Liste von Dateien ähnlich der folgenden zurückgegeben werden:

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE]Möglicherweise wird für einige Dateien die Dateigröße 0 angezeigt, da sie vom EventHubReader erstellt wurden, aber keine Daten darin gespeichert wurden.

    Sie können den Inhalt dieser Dateien mit dem folgenden Befehl anzeigen:

        hadoop fs -text /devicedata/*.txt

    Die ausgegebenen Daten sehen in etwa so aus:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    Die erste Spalte enthält den Wert der Geräte-ID und die zweite Spalte den Geräte-Wert.

4. Verwenden Sie die folgenden Befehle, um die Topologien zu stoppen:

        storm kill reader
        storm kill writer

###Verwendung eines Windows-basierten Clusters

1. Öffnen Sie mit Ihren Browser https://CLUSTERNAME.azurehdinsight.net. Geben Sie die Anmeldeinformationen für den HDInsight-Cluster ein, wenn Sie dazu aufgefordert werden. Das Storm-Dashboard wird angezeigt.

2. Verwenden Sie die Dropdown-Liste __-JAR-Datei__, um die EventHubExample-1.0-SNAPSHOT.jar-Datei aus der aktuellen Buildumgebung zu suchen und auszuwählen.

3. Geben Sie für __Klassenname__ `com.mirosoft.example.EventHubWriter` ein.

4. Geben Sie für __Zusätzliche Parameter__ `writer` ein. Klicken Sie abschließend auf __Senden__, um die JAR-Datei hochzuladen und die EventHubWriter-Topologie zu starten.

5. Verwenden Sie nach dem Starten der Topologie das Formular, um den EventHubReader zu starten:

    * __JAR-Datei__: Wählen Sie die EventHubExample-1.0-SNAPSHOT.jar, die zuvor hochgeladen wurde
    * __Klassenname__: Geben Sie `com.microsoft.example.EventHubReader` ein.
    * __Zusätzliche Parameter__ Geben Sie `reader` ein.

    Klicken Sie auf „Senden“, um die EventHubReader-Topologie zu starten.

6. Geben Sie den Topologien einige Minuten, um Ereignisse zu erstellen und im Azure-Speicher zu sichern, und wählen Sie dann die Registerkarte __Abfragekonsole__ am oberen Rand der Seite __Storm-Dashboard__.

7. Wählen Sie auf der __Abfragekonsole__ die Option __Hive-Editor__, und ersetzen Sie den Standardnamen `select * from hivesampletable` durch den folgenden:

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasb:///devicedata/';
        select * from devicedata limit 10;

    Klicken Sie auf __Auswählen__, um die Abfrage auszuführen. Dadurch werden 10 Zeilen der Daten ausgegeben, die vom EventHubReader im Azure-Speicher (WASB) geschrieben wurden. Nach Abschluss der Abfrage sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Wählen Sie das __Storm-Dashboard__ am oberen Seitenrand, und wählen Sie dann __Storm-Benutzeroberfläche__. Wählen Sie auf der __Storm-Benutzeroberfläche__ den Link für die __Reader__-Topologie, und verwenden Sie dann die Schaltfläche __Beenden__, um die Topologie zu beenden. Wiederholen Sie den Vorgang für die __Writer__-Topologie.



### Setzen von Prüfpunkten

Der EventHubSpout erstellt in regelmäßigen Abständen Prüfpunkte im Zookeeper-Knoten, der den aktuellen Offset für aus der Warteschlange gelesene Nachrichten speichert. Dies ermöglicht der Komponente, in den folgenden Szenarios mit dem Empfang von Nachrichten an dem gespeicherten Offset zu beginnen:

* Die Instanz der Komponente wird aufgrund eines Fehlers neu gestartet.

* Sie vergrößern oder verkleinern den Cluster durch Hinzufügen oder Entfernen von Knoten.

* Die Topologie wird beendet und **mit demselben Namen** neu gestartet.

####Windows-basierte HDInsight-Cluster

Die persistenten Prüfpunkte können an WASB (der von Ihrem HDInsight-Cluster verwendete Azure-Speicher) exportiert und importiert werden. Die dafür geeigneten Skripts befinden sich auf dem Storm-Cluster auf HDInsight unter **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE]Die Versionsnummer im Pfad kann abweichen, da sich die auf dem Cluster installierte Storm-Version in Zukunft ändern könnte.

Die Skripts in diesem Verzeichnis sind:

* **stormmeta\_import.cmd:** importiert alle Storm-Metadaten vom standardmäßigen Speichercontainer des Clusters in Zookeeper.

* **stormmeta\_export.cmd:** exportiert alle Storm-Metadaten von Zookeeper in den standardmäßigen Speichercontainer des Clusters.

* **stormmeta\_delete.cmd:** Alle Storm-Metadaten werden aus Zookeeper gelöscht.

Durch das Exportieren und Importieren können Sie Prüfpunktdaten persistent speichern, um beispielsweise einen Cluster zu löschen und nach dem Onlineschalten des neuen Clusters die Verarbeitung am aktuellen Offset fortsetzen zu können.

> [AZURE.NOTE]Da die Daten im Standard-Speichercontainer persistent gespeichert werden, **muss** der neue Cluster das gleiche Speicherkonto und den gleichen Container verwenden wie der vorherige Cluster.

##Problembehandlung

Wenn unter /devicedata gespeicherte Dateien nicht angezeigt werden (mit dem Befehl `hadoop fs -ls /devicedata` oder dem Hive-Befehl in der Abfrage), prüfen Sie die Storm-Benutzeroberfläche auf mögliche von den Topologien zurückgegebenen Fehler.

Weitere Informationen zur Storm-Benutzeroberfläche finden Sie den folgenden Themen:

* Informationen zur Verwendung eines __Linux-basierten__ Clusters finden Sie unter [Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight unter Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Informationen zur Verwendung eines __Windows-basierten__ Clusters finden Sie unter [Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight unter Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

##Nächste Schritte

* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=Oct15_HO3-->