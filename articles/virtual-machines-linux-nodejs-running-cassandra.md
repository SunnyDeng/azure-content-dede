<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Cassandra with Linux" pageTitle="Run Cassandra with Linux on Azure" metaKeywords="" description="Explains how to run a Cassandra cluster on Linux in Azure Virtual Machines." metaCanonical="" services="virtual-machines" documentationCenter="nodejs" title="Running Cassandra with Linux on Azure and Accessing it from Node.js" authors="hanuk" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanuk" />

# <span></span></a>Ausführen von Cassandra mit Linux auf Azure und Zugreifen darauf mit Node.js

**Autor:** Hanu Kommalapati

## Inhaltsverzeichnis

-   [Übersicht][Übersicht]
-   [Schema zur Bereitstellung von Cassandra][Schema zur Bereitstellung von Cassandra]
-   [Zusammengesetzte Bereitstellung][Zusammengesetzte Bereitstellung]
-   [Bereitstellen eines virtuellen Azure-Computers][Bereitstellen eines virtuellen Azure-Computers]
-   [Aufgabe 1: Bereitstellen eines Linux-Clusters][Aufgabe 1: Bereitstellen eines Linux-Clusters]
-   [Aufgabe 2: Einrichten von Cassandra auf jedem virtuellen Computer][Aufgabe 2: Einrichten von Cassandra auf jedem virtuellen Computer]
-   [Aufgabe 3: Zugreifen auf das Cassandra-Cluster in Node.js][Aufgabe 3: Zugreifen auf das Cassandra-Cluster in Node.js]
-   [Zusammenfassung][Zusammenfassung]

## <span id="overview"></span> </a>Übersicht

Azure bietet einen NoSQL-Datenbankdienst über den Azure-Tabellenspeicherdienst, der eine schemalose Speicherung von Geschäftsobjekten erlaubt. Dieser Service kann mit Node.JS, .NET, Java und allen anderen Sprachen genutzt werden, die HTTP und REST verstehen. Es gibt allerdings auch noch andere populäre NoSQL-Datenbanken wie Cassandra und Couchbase, die aufgrund des verwendeten zustandslosen Clouddienst-Modells nicht auf Azure-PaaS ausgeführt werden können. Azure Virtual Machines erlaubt nun die Ausführung dieser NoSQL-Datenbanken ohne Änderungen an der Codebasis. In diesem Artikel soll gezeigt werden, wie ein Cassandra-Cluster auf virtuellen Computern ausgeführt wird und wie mit Node.js darauf zugegriffen wird. Nicht vollständig beschrieben wird die Cassandra-Bereitstellung für Produktionsvorgänge in der Wirklichkeit, wo Cassandra-Cluster in mehreren Rechenzentren mit den entsprechenden Backup- und Wiederherstellungsstrategien überwacht werden müssen. In dieser Übung verwenden wir die Linux-Version Ubuntu 12.04 und Cassandra 1.0.10; der Prozess kann allerdings auch an jede andere Linux-Distribution angepasst werden.

## <span id="schematic"></span> </a>Schema zur Bereitstellung von Cassandra

Die Funktion Azure Virtual Machines ermöglicht die einfache Ausführung von NoSQL-Datenbanken wie [Cassandra][Cassandra] in einer öffentlichen Microsoft-Cloud oder in einer privaten Cloud-Umgebung. Allerdings gibt es eine für die Infrastruktur von Azure Virtual Machines spezifische Ausnahme in Bezug auf die Konfiguration des virtuellen Netzwerks. Da zum aktuellen Zeitpunkt Cassandra nicht als verwalteter Dienst auf Azure verfügbar ist, beschreiben wir die Einrichtung eines Cassandra-Clusters auf virtuellen Computern und den Zugriff auf diese Cluster von einer anderen Linux-Instanz aus, die auch in Virtual Machines gehostet wird. Die gezeigten Codeausschnitte aus Node.js können auch in einer Webanwendung oder einem Webdienst mit PaaS-Hosting verwendet werden. Eine der wesentlichen Stärken von Azure ist das Modell der zusammengesetzten Anwendungen, das die Nutzung der besten Elemente von PaaS und IaaS erlaubt.

Es gibt zwei Bereitstellungsmodelle, die sich für die Cassandra-Anwendungsumgebung eignen: die Bereitstellung in selbständigen virtuellen Maschinen und die zusammengesetzte Bereitstellung. In einer zusammengesetzten Bereitstellung wird ein in Virtual Machines gehostetes Cassandra-Cluster von einer PaaS-gehosteten Webanwendung (oder einem PaaS-gehosteten Webdienst) genutzt. Dies erfolgt über die Thrift-Schnittstelle des Lastenausgleichsmoduls. Auch wenn jeder Cassandra-Knoten die Anfrage im Falle eines Schlüsselbereichsfehlers an andere Peer-Knoten weiterleitet, hilft das Lastenausgleichsmodul beim Lastenausgleich der eingehenden Anfragen. Außerdem erstellt es eine Firewall-geschützte Sandbox für bessere Datenkontrolle.

## <span id="composite"></span> </a> Zusammengesetzte Bereitstellung

Das Ziel einer zusammengesetzten Bereitstellung ist die Maximierung der PaaS-Verwendung bei gleichzeitig minimierter Beteiligung virtueller Computer, um den Aufwand zu reduzieren, mit dem die Verwaltung der Infrastruktur dieser virtuellen Computer verbunden ist. Aufgrund des Serververwaltungsaufwands werden nur die Komponenten bereitgestellt, die ein statussensitives Verhalten erfordern und deren Änderung aus verschiedenen Gründen wie der baldigen Produkteinführung, der schlechten Auffindbarkeit im Quellcode oder des mangelnden Zugriffs auf das Betriebssystem nicht leicht ist.

![Diagramm einer zusammengesetzten Bereitstellung][Diagramm einer zusammengesetzten Bereitstellung]

## <span id="deployment"></span> </a>Bereitstellen eines virtuellen Azure-Computers

![Virtuellen Computer bereitstellen][Virtuellen Computer bereitstellen]

Im oben gezeigten Diagramm wird ein Cassandra-Cluster mit vier Knoten in Virtual Machines hinter einem Lastenausgleichmodul bereitgestellt, das Thrift-Verkehr erlaubt. Die Azure-gehostete PaaS-Anwendung greift mithilfe sprachspezifischer Thrift-Bibliotheken auf das Cluster zu. Es gibt Bibliotheken für die Sprachen Java, C#, Node.js, Python und C++. Bei der im zweiten Diagramm gezeigten Bereitstellung auf selbständigen virtuellen Computern werden Daten von Anwendungen genutzt, die in einem anderen in Virtual Machines gehosteten Clouddienst laufen.

## <span id="task1"></span> </a>Aufgabe 1: Bereitstellen eines Linux-Clusters

Die Erstellung eines Clusters geht normalerweise in dieser Reihenfolge vor sich:

![Ablaufdiagramm für das Erstellen eines Clusters][Ablaufdiagramm für das Erstellen eines Clusters]

**Schritt 1: Erstellen eines SSH-Schlüsselpaars**

Azure benötigt einen öffentlichen X509-Schlüssel, der bei seiner Bereitstellung entweder PEM- oder DER-kodiert ist. Erstellen Sie ein öffentlich/privates Schlüsselpaar, indem Sie den Anweisungen unter [Verwenden von SSH mit Linux auf Azure][Verwenden von SSH mit Linux auf Azure] folgen. Wenn Sie "putty.exe" unter Windows oder Linux als SSH-Client verwenden möchten, müssen Sie den PEM-codierten privaten RSA-Schlüssel mithilfe von "puttygen.exe" in das PPK-Format konvertieren. Ein Anleitung dazu finden Sie unter [Generieren eines SSH-Schlüsselpaars für die Bereitstellung virtueller Linux-Computer auf Windows Azure][Generieren eines SSH-Schlüsselpaars für die Bereitstellung virtueller Linux-Computer auf Windows Azure].

**Schritt 2: Erstellen eines virtuellen Ubuntu-Computers**

Melden Sie sich zur Erstellung des ersten virtuellen Ubuntu-Computers auf dem Azure-Vorschauportal an, klicken Sie auf **Neu**, auf **Virtueller Computer**, auf **From Gallery** und auf **Ubuntu Server 12.xx**. Klicken Sie dann auf den rechten Pfeil. Ein Lernprogramm, das die Erstellung eines virtuellen Linux-Computers beschreibt, finden Sie unter [Erstellen eines virtuellen Linux-Computers][Erstellen eines virtuellen Linux-Computers].

Geben Sie anschließend auf dem Bildschirm "Konfiguration des virtuellen Computers" folgende Informationen ein:

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Feldname</th>
<th align="left">Feldwert</th>
<th align="left">Anmerkungen</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Name des virtuellen Computers</td>
<td align="left">hk-cas1</td>
<td align="left">Dies ist der Hostname des virtuellen Computers</td>
</tr>
<tr class="even">
<td align="left">Neuer Benutzername</td>
<td align="left">localadmin</td>
<td align="left">&quot;admin&quot; ist ein reservierter Benutzername in Ubuntu 12.xx</td>
</tr>
<tr class="odd">
<td align="left">Neues Kennwort</td>
<td align="left"><em>sicheres Kennwort</em></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Kennwort bestätigen</td>
<td align="left"><em>sicheres Kennwort</em></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Größe</td>
<td align="left">Klein</td>
<td align="left">Wählen Sie den virtuellen Computer entsprechend den E/A-Anforderungen.</td>
</tr>
<tr class="even">
<td align="left">Sichern Sie die Authentifizierung mit einem SSH-Schlüssel</td>
<td align="left">Klicken Sie auf das Kontrollkästchen</td>
<td align="left">Aktivieren Sie es, wenn Sie die Authentifizierung mit einem SSH-Schlüssel sichern möchten</td>
</tr>
<tr class="odd">
<td align="left">Zertifikat</td>
<td align="left"><em>Dateiname des Zertifikats für den öffentlichen Schlüssel</em></td>
<td align="left">Mit OpenSSL oder anderen Tools generierter DER- oder PEM-kodierter öffentlicher Schlüssel</td>
</tr>
</tbody>
</table>

Geben Sie auf dem Bildschirm "VM-Modus"die folgenden Informationen ein:

| Feldname                                    | Feldwert                         | Anmerkungen                                                                                                                           |
|---------------------------------------------|----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Eigenständiger virtueller Computer          | "Aktivieren" Sie das Optionsfeld | Dies gilt für den ersten virtuellen Computer. Für die weiteren virtuellen Computer verwenden wir die Option "Connect to Existing VM". |
| DNS-Name                                    | *eindeutiger Name*.cloudapp.net  | Geben Sie dem Lastenausgleichsmodul einen dem Computer nicht bekannten Namen                                                          |
| Speicherkonto                               | *Standard-Speicherkonto*         | Verwenden Sie das Standard-Speicherkonto, das Sie erstellt haben                                                                      |
| Region/Affinitätsgruppe/Virtuelles Netzwerk | USA (West)                       | Wählen Sie eine Region aus, in der Ihre Webanwendungen auf das Cassandra-Cluster zugreifen                                            |

Wiederholen Sie das oben beschriebene Verfahren für alle virtuellen Computer, die Teil des Cassandra-Clusters sind. An diesem Punkt sind alle Computer Teil desselben Netzwerks und können einander "pingen". Wenn der Ping nicht funktioniert, prüfen Sie die Konfiguration der Firewall des virtuellen Computers (z. B. iptables), um sicherzugehen, dass ICMP erlaubt ist. Deaktivieren Sie ICMP, wenn Sie die Netzwerkkonnektivität erfolgreich getestet haben, um die Sicherheitsanfälligkeit zu reduzieren.

**Schritt 3: Hinzufügen eines Thrift-Endpunkts mit Lastenausgleich**

Nach Schritt 1 und Schritt 2 sollte bereits für jeden virtuellen Computer ein SSH-Endpunkt definiert sein. Als Nächstes fügen wir den Thrift-Endpunkt mit Lastenausgleich über den öffentlichen Port 9160 hinzu. Die Schritte sind:

a. Klicken Sie in der Detailansicht des ersten virtuellen Computers auf "Add Endpoint".

b. Wählen Sie auf dem Bildschirm "Add endpoint to virtual machine" die Optionsschaltfläche "Add endpoint" aus.

c. Klicken Sie auf den rechten Pfeil.

d. Geben Sie auf dem Bildschirm "Specify endpoint details" Folgendes ein:

<table>

<tr>

<th>
Feldname

</th>

<th>
Feldwert

</th>

<th>
Anmerkungen

</th>

</tr>

<tr>

<td>
Name

</td>

<td>
cassandra

</td>

<td>
Jeder eindeutige Endpunktname ist geeignet

</td>

</tr>

<tr>

<td>
Protokoll

</td>

<td>
TCP

</td>

<td>
</td>

</tr>

<tr>

<td>
Öffentlicher Port

</td>

<td>
9160

</td>

<td>
Standard-Thrift-Port.

</td>

</tr>

<tr>

<td>
Privater Port

</td>

<td>
9160

</td>

<td>
Wenn Sie dies nicht in cassandra.yaml geändert haben

</td>

</tr>

</table>
Nach der Ausführung dieser Schritte ist am Cassandra-Endpunkt des ersten virtuellen Computers die Option "LOAD BALANCED" auf "NO" festgelegt. Ignorieren Sie dies für den Moment. Die Option wird auf "JA" gestellt, wenn wir diesen Endpunkt zu den weiteren virtuellen Computern hinzugefügt haben.

</p>
e. Wählen Sie nun den zweiten virtuellen Computer aus, und fügen Sie den Endpunkt hinzu, indem Sie das oben beschriebene Verfahren wiederholen. Der einzige Unterschied ist, dass Sie "Load-balance traffic on an existing endpoint" auswählen und im Dropdownfeld auf "cassandra-960" klicken. An diesem Punkt wird durch die Zuordnung des Endpunkts an beide virtuelle Computer die Statusoption "LOAD BALANCED" von "NEIN" auf "JA" gesetzt.

Wiederholen Sie "e" für die weiteren Knoten in diesem Cluster.

Nach dem Erstellen der virtuellen Maschinen ist es nun Zeit, Cassandra auf jeder dieser virtuellen Maschine einzurichten. Da Cassandra in vielen Linux-Distributionen nicht standardmäßig vorhanden ist, beschreiben wir hier einen manuellen Bereitstellungsprozess.

[Bitte beachten Sie, dass wir die Software auf jedem virtuellen Computer manuell installieren. Der Prozess kann allerdings beschleunigt werden, indem wir einen vollständig funktionsfähigen virtuellen Cassandra-Computer einrichten, ihn als Basis-Image erfassen und aus diesem Basis-Image weitere Instanzen erstellen. Die Anweisungen zum Erfassen des Linux-Images finden Sie unter [Erfassen des Images eines virtuellen Linux-Computers][Bitte beachten Sie, dass wir die Software auf jedem virtuellen Computer manuell installieren. Der Prozess kann allerdings beschleunigt werden, indem wir einen vollständig funktionsfähigen virtuellen Cassandra-Computer einrichten, ihn als Basis-Image erfassen und aus diesem Basis-Image weitere Instanzen erstellen. Die Anweisungen zum Erfassen des Linux-Images finden Sie unter [Erfassen des Images eines virtuellen Linux-Computers].]

## <span id="task2"></span> </a>Aufgabe 2: Einrichten von Cassandra auf jedem virtuellen Computer

**Schritt 1: Voraussetzungen für die Installation**

Cassandra erfordert Java Virtual Machine. Deswegen installieren die neueste JRE, indem wir den folgenden Befehl für Debian-Derivate einschließlich Ubuntu verwenden:

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**Schritt 2: Cassandra-Installation**

1.  Melden Sie sich über SSH auf dem virtuellen Linux (Ubuntu)-Computer an.

2.  Verwenden Sie "wget", um Cassandra-Daten von der unter (<http://cassandra.apache.org/download/>)[<http://cassandra.apache.org/download/>] vorgeschlagenen Spiegelung als "apache-cassandra-bin.tar.gz" in das Verzeichnis "~/downloads" herunterladen. Beachten Sie, dass die Versionsnummer nicht in der Downloaddatei enthalten ist, um sicherzustellen, dass der Veröffentlichung die Version nicht bekannt ist.

3.  Entpacken Sie den Tarball in das Standard-Anmeldeverzeichnis, indem Sie den folgenden Befehl eingeben:

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    Dadurch wird die Archivdatei in das Verzeichnis apache-cassandra-[version] entpackt.

4.  Erstellen Sie die beiden folgenden Standardverzeichnisse für die Protokolle und die Daten:

        $ sudo chown -R /var/lib/cassandra
        $ sudo chown -R /var/log/cassandra

5.  Vergeben Sie eine Schreibberechtigung an die Benutzeridentität, unter der Cassandra läuft

        a.  sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  Starten Sie Cassandra aus dem Verzeichnis apache-cassandra-[version]/bin mit dem folgenden Befehl:

        $ ./cassandra

Dadurch wird der Cassandra-Knoten als Hintergrundprozess gestartet. Verwenden Sie -cassandra "f", um den Prozess im Vordergrund zu starten.

Das Protokoll kann nun einen mx4j-:Fehler anzeigen. Cassandra funktioniert problemlos ohne mx4j, aber dies ist für die Verwaltung der Cassandra-Installation erforderlich. Beenden Sie den Cassandra-Prozess vor dem nächsten Schritt.

**Schritt 3: mx4j installieren**

    a)  Download mx4j: wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
    b)  tar -zxvf mx4j.tar.gz
    c)  cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
    d)  rm -rf mx4j-23.0.2
    e)  rm mx4j.tar.gz

Starten Sie den Cassandra-Prozess an diesem Punkt neu

**Schritt 4: Testen der Cassandra-Installation**

Führen Sie im bin-Verzeichnis von Cassandra den folgenden Befehl aus, um eine Verbindung zu einem Thrift-Client herzustellen:

    cassandra-cli -h localhost -p 9160

**Schritt 5: Aktivieren von Cassandra für externe Verbindungen**

Standardmäßig wird Cassandra nur eingerichtet, um die Loopback-Adresse zu überwachen. Deshalb ist diese Änderung für externe Verbindungen erforderlich.

Bearbeiten Sie "conf/cassandra.yaml", indem Sie **listen\_address** und **rpc\_address** in die IP-Adresse oder den Hostnamen des Servers ändern, sodass der aktuelle Knoten für die anderen Knoten und für die externen Lastenausgleichsmodule sichtbar ist.

Wiederholen Sie die Schritte 1 bis 5 für alle Knoten in dem Cluster.

Nun, da die erforderliche Software auf allen einzelnen virtuellen Computern eingerichtet ist, ist es Zeit, die Kommunikation zwischen den Knoten durch die Seed-Konfiguration herzustellen. Prüfen Sie die Informationen unter [][]<http://wiki.apache.org/cassandra/MultinodeCluster></a> auf detaillierte Angaben zur Konfiguration von Clustern mit mehreren Knoten.

**Schritt 6: Einrichten eines Clusters mit mehreren Knoten**

Bearbeiten Sie cassandra.yaml, um die folgenden Eigenschaften in allen virtuellen Computern zu ändern:

**a) cluster\_name**

Der Standard-Clustername lautet "Test Cluster". Ändern Sie ihn in eine Bezeichnung für Ihre Anwendung. Beispiel: "AppStore". Wenn Sie das Cluster zum Testen während der Installation bereits mit dem Namen "Test Cluster" gestartet haben, kommt es zu einem Namenskonflikt. Löschen Sie alle Dateien im Verzeichnis /var/lib/cassandra/data/system directory, um dieses Problem zu beheben.

**b) seeds**

Die hier angegebenen IP-Adressen werden von neuen Knoten verwendet, um Informationen zur Ringtopologie zu erhalten. Legen Sie die zuverlässigsten Knoten als Ihre Seeds in einem kommagetrennten Format fest. "*host1*,*host2*". Beispieleinstellung: "hk-ub1,hk-ub2".

Wir übernehmen die Standard-Tokens von den Seed-Servern, da dies für die Übung nicht wichtig ist. Informationen zur Generierung optimaler Tokens finden Sie im Python-Skript unter
[][1]<http://wiki.apache.org/cassandra/GettingStarted></a>:

Starten Sie Cassandra auf allen Knoten neu, um die oben beschriebenen Änderungen zu übernehmen.

**Schritt 7: Testen des Clusters mit mehreren Knoten**

Das im bin-Verzeichnis von Cassandra installierte Nodetool hilft bei Cluster-Operationen. Wir verwenden Nodetool, um die Cluster-Einrichtung über das folgende Befehlsformat zu prüfen:

    $ bin/nodetool -h <hostname> -p 7199 ring

Wenn die Konfiguration korrekt ist, sollten die Informationen wie unten für einen Cluster mit 3 Knoten dargestellt angezeigt werden:

<table>
<colgroup>
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Adresse</td>
<td align="left">DC</td>
<td align="left">Rack</td>
<td align="left">Status</td>
<td align="left">Zustand</td>
<td align="left">Last</td>
<td align="left">Eigentümer</td>
<td align="left">Token</td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
<tr class="odd">
<td align="left">10.26.196.68</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">25.98%</td>
<td align="left">114445918355431753244435008039926455424</td>
</tr>
<tr class="even">
<td align="left">10.26.198.81</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">53.44%</td>
<td align="left">70239176883275351288292106998553981501</td>
</tr>
<tr class="odd">
<td align="left">10.26.198.84</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">18,35 KB</td>
<td align="left">25.98%</td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
</tbody>
</table>

An diesem Punkt ist das Cluster bereit für Thrift-Clients über die Clouddienst-URL, die während der Aufgabe "Bereitstellen eines Linux-Clusters" erstellt wurde. (Der DNS-Name wird bei der Erstellung des ersten virtuellen Computers vergeben.)

## <span id="task3"></span> </a>Aufgabe 3: Zugreifen auf das Cassandra-Cluster in Node.js

Erstellen Sie eine virtuelle Linux-Maschine in Azure mithilfe des in den vorhergehenden Aufgaben beschriebenen Prozesses. Stellen Sie sicher, dass es sich um einen eigenständigen virtuellen Computer handelt, da wir ihn als Client für den Zugriff auf den Cassandra-Cluster verwenden. Wir installieren Node.js, NPM und [cassandra-client][cassandra-client] von Github, bevor wir auf diesem virtuellen Computer eine Verbindung zum Cassandra-Cluster herstellen:

**Schritt 1: Installieren von Node.js und NPM**

a) Installation der erforderlichen Komponenten

    sudo apt-get install g++ libssl-dev apache2-utils make

b) Wir verwenden zur Kompilierung und Installation die Github-Quelle. Bevor wir das Repository klonen, müssen wir die Core Runtime von Git installieren:

    sudo apt-get install git-core

c) Knotenrepository klonen

    git clone git://github.com/joyent/node.git

c) Dadurch wird ein Verzeichnis mit dem Name "node" erstellt. Geben Sie die folgende Befehlssequenz ein, um Node.js zu kompilieren und zu installieren:

    cd node
    ./configure
    make
    sudo make install

e) Installieren Sie NPM aus stabilen Binärdateien, indem Sie den folgenden Befehl ausführen:

    curl http://npmjs.org/install.sh | sh

**Schritt 2: Installieren des Cassandra-Clientpakets**

    npm cassandra-client 

**Schritt 3: Vorbereiten des Cassandra-Speichers**

Der Cassandra-Speicher verwendet die Konzepte KEYSPACE und COLUMNFAMILY, die mit den Strukturen DATABASE und TABLE in RDBMS vergleichbar sind. KEYSPACE enthält einen Satz von COLUMNFAMILY-Definitionen. Jede COLUMNFAMILY enthält einen Satz von Zeilen. Jede Zeile wiederum enthält wie in der zusammengesetzten Ansicht unten gezeigt einige Spalten:

![Zeilen und Spalten][Zeilen und Spalten]

Wir verwenden das zuvor bereitgestellte Cassandra-Cluster, um den Zugriff mit Node.js zu demonstrieren, indem wir die oben dargestellten Datenstrukturen erstellen und abfragen. Wir erstellen ein einfaches Node.js-Skript, das die grundlegende Vorbereitung des Clusters auf die Speicherung von Kundendaten durchführt. Die in diesem Skript gezeigten Verfahren können leicht in einer Node.js-Webanwendung oder einem Node.js-Webdienst verwendet werden. Bitte denken Sie daran, dass mit diesen Ausschnitten nur das Funktionsprinzip gezeigt werden soll. In der Wirklichkeit bietet der gezeigte Code noch viele Verbesserungsmöglichkeiten (z. B. unter den Aspekten Sicherheit, Protokollierung, Skalierbarkeit usw.).

Definieren wir nun die erforderlichen Variablen auf Skriptebene, um PooledConnection vom Cassandra-Client-Modul, häufig genutzte Keyspace-Namen und die Keyspace-Verbindungsparameter einzuschließen.

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

Als Vorbereitung auf die Speicherung der Kundendaten müssen wir zuerst einen KEYSPACE nach dem folgenden Skript-Beispiel erstellen:

    casdemo.js: 
    function createKeyspace(callback){
       var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
       strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
       var sysConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'],  
                             keyspace: 'system', use_bigints: false };
       var con = new pooledCon(sysConOptions);
       con.execute(cql,[],function(err) {
       if (err) {
         console.log("Failed to create Keyspace: " + ksName);
         console.log(err);
       }
       else {
         console.log("Created Keyspace: " + ksName);
         callback(ksConOptions, populateCustomerData);
       }
       });
       con.shutdown();
    } 

Die Funktion createKeysapce nimmt eine Rückruffunktion als Argument zur Ausführung der COLUMNFAMILY-Erstellungsfunktion, da KEYSPACE eine Voraussetzung für die COLUMNFAMILY-Erstellung ist. Bitte beachten Sie, dass wir eine Verbindung zum "System" KEYSPACE herstellen müssen, um die Anwendung KEYSPACE zu definieren. In all diesen Ausschnitten wird für die Interaktion mit dem Cluster durchgehend [Cassandra Query Language (CQL)][Cassandra Query Language (CQL)] verwendet. Da die im Skript oben zusammengestellte CQL keine Parametermarker aufweist, verwenden wir für die Methode PooledConnection.execute() eine leere Parametersammlung ("[]").

Nach der erfolgreichen Keyspace-Erstellung wird die im folgenden Ausschnitt gezeigte Funktion createColumnFamily() ausgeführt, um die erforderlichen COLUMNFAMILY-Definitionen zu erstellen:

    casdemo.js: 
    //Creates COLUMNFAMILY
    function createColumnFamily(ksConOptions, callback){
      var params = ['customers_cf','custid','varint','custname',
                    'text','custaddress','text'];
      var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
    var con =  new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) {
             console.log("Failed to create column family: " + params[0]);
             console.log(err);
          }
          else {
             console.log("Created column family: " + params[0]);
             callback();
          }
      });
      con.shutdown();
    } 

Die mit Parametern ausgestattete CQL-Vorlage wird mit dem Objekt "params" kombiniert, um eine gültige CQL für die COLUMNFAMILY-Erstellung zu generieren. Nach der erfolgreichen Erstellung der COLUMNFAMILY wird der Rückruf, in diesem Falle populateCustomerData(), als Teil der Kette asynchroner Aufrufe ausgeführt.

    casdemo.js: 
    //populate Data
    function populateCustomerData() {
       var params = ['John','Infinity Dr, TX', 1];
       updateCustomer(ksConOptions,params);

       params = ['Tom','Fermat Ln, WA', 2];
       updateCustomer(ksConOptions,params);
    }

    //update will also insert the record if none exists
    function updateCustomer(ksConOptions,params)
    {
      var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where 
                 custid=?';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) console.log(err);
          else console.log("Inserted customer : " + params[0]);
      });
      con.shutdown();
    }

"populateCustomerData()" fügt einige Zeilen in die COLUMNFAMILY mit dem Namen "customers\_cf" ein. In der Abfragesprache Cassandra fügt UPDATE den Datensatz ein, wenn dieser nicht bereits vorhanden ist. Dies macht die Anweisung INSERT CQL überflüssig.

Die Rückrufkette sieht bis jetzt so aus: von createKeyspace() zu createColumnFamily() zu populateCustomerData(). Nun ist es Zeit für uns, den Code entsprechend dem folgenden Codeausschnitt auszuführen:

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    createKeyspace(createColumnFamily);
    //rest of the not shown

Führen Sie in der Eingabeaufforderung der Shell den folgenden Befehl aus, um das Skript auszuführen:

    //the following command will create the KEYSPACE, COLUMNFAMILY and //inserts two customer records
    $ node casdemo.js

Mit der Methode readCustomer() wird auf das in Azure gehosteten Cluster zugegriffen und der JSON-Ausschnitt angezeigt, der nach der Ausführung der CQL-Anfrage abgerufen wird:

    casdemo.js: 
    //read the two rows inserted above
    function readCustomer(ksConOptions)
    {
      var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,[],function(err,rows) {
          if (err) 
             console.log(err);
          else 
             for (var i=0; i<rows.length; i++)
                console.log(JSON.stringify(rows[i]));
        });
       con.shutdown();
    } 

Modifizieren Sie casdemo.js wie unten dargestellt, um die oben gezeigte Funktion hinzuzufügen und nach der Kommentierung der zuvor aufgerufenen Methode createKeyspace() aufzurufen:

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    //createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    //rest of the code below not shown
        

## <span id="conclusion"></span> </a>Zusammenfassung

Azure Virtual Machines erlaubt die Erstellung virtueller Linux- (von Microsoft-Partnern bereitgestellte Images) und Windows-Computer, die eine Migration vorhandener Server-Produkte und -Anwendungen ohne Änderungen ermöglichen. Ein Beispiel dafür ist der in diesem Artikel diskutierte Cassandra-NoSQL-Datenbankserver. Auf das in diesem Artikel eingerichtete Cassandra-Cluster kann über Azure-gehostete Clouddienste, öffentliche Clouds von Drittanbietern und private Clouds in Windows- und Linux-Umgebungen zugegriffen werden. In diesem Artikel haben wir Node.js als Client genannt; ein Zugriff auf Cassandra ist aber auch mit .NET-, Java- und anderen Umgebungen möglich.

  [Übersicht]: #overview
  [Schema zur Bereitstellung von Cassandra]: #schematic
  [Zusammengesetzte Bereitstellung]: #composite
  [Bereitstellen eines virtuellen Azure-Computers]: #deployment
  [Aufgabe 1: Bereitstellen eines Linux-Clusters]: #task1
  [Aufgabe 2: Einrichten von Cassandra auf jedem virtuellen Computer]: #task2
  [Aufgabe 3: Zugreifen auf das Cassandra-Cluster in Node.js]: #task3
  [Zusammenfassung]: #conclusion
  [Cassandra]: http://wiki.apache.org/cassandra/
  [Diagramm einer zusammengesetzten Bereitstellung]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png
  [Virtuellen Computer bereitstellen]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png
  [Ablaufdiagramm für das Erstellen eines Clusters]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png
  [Verwenden von SSH mit Linux auf Azure]: http://www.windowsazure.com/de-de/manage/linux/how-to-guides/ssh-into-linux/
  [Generieren eines SSH-Schlüsselpaars für die Bereitstellung virtueller Linux-Computer auf Windows Azure]: http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx
  [Erstellen eines virtuellen Linux-Computers]: http://www.windowsazure.com/de-de/manage/linux/tutorials/virtual-machine-from-gallery/
  [Erfassen des Images eines virtuellen Linux-Computers]: https://www.windowsazure.com/de-de/manage/linux/how-to-guides/capture-an-image/
  []: http://wiki.apache.org/cassandra/MultinodeCluster
  [1]: http://wiki.apache.org/cassandra/GettingStarted
  [cassandra-client]: https://github.com/racker/node-cassandra-client
  [Zeilen und Spalten]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png
