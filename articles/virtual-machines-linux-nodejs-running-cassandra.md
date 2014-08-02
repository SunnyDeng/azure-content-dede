<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Cassandra with Linux" pageTitle="Run Cassandra with Linux on Azure" metaKeywords="" description="Explains how to run a Cassandra cluster on Linux in Azure Virtual Machines." metaCanonical="" services="virtual-machines" documentationCenter="Node.js" title="Running Cassandra with Linux on Azure and Accessing it from Node.js" authors="" solutions="" manager="" editor="" />

Ausführen von Cassandra mit Linux auf Azure und Zugreifen darauf mit Node.js
============================================================================

**Autor:** Hanu Kommalapati

Inhaltsverzeichnis
------------------

-   [Übersicht](#overview)
-   [Schema zur Bereitstellung von Cassandra](#schematic)
-   [Zusammengesetzte Bereitstellung](#composite)
-   [Bereitstellen eines virtuellen Azure-Computers](#deployment)
-   [Aufgabe 1: Bereitstellen eines Linux-Clusters](#task1)
-   [Aufgabe 2: Einrichten von Cassandra auf jedem virtuellen Computer](#task2)
-   [Aufgabe 3: Zugreifen auf das Cassandra-Cluster in Node.js](#task3)
-   [Zusammenfassung](#conclusion)

Übersicht
---------

Azure bietet einen NoSQL-Datenbankdienst über den Azure-Tabellenspeicherdienst, der eine schemalose Speicherung von Geschäftsobjekten erlaubt. Dieser Service kann mit Node.JS, .NET, Java und allen anderen Sprachen genutzt werden, die HTTP und REST verstehen. Es gibt allerdings auch noch andere populäre NoSQL-Datenbanken wie Cassandra und Couchbase, die aufgrund des verwendeten zustandslosen Clouddienst-Modells nicht auf Azure-PaaS ausgeführt werden können. Azure Virtual Machines erlaubt nun die Ausführung dieser NoSQL-Datenbanken ohne Änderungen an der Codebasis. In diesem Artikel soll gezeigt werden, wie ein Cassandra-Cluster auf virtuellen Computern ausgeführt wird und wie mit Node.js darauf zugegriffen wird. Nicht vollständig beschrieben wird die Cassandra-Bereitstellung für Produktionsvorgänge in der Wirklichkeit, wo Cassandra-Cluster in mehreren Rechenzentren mit den entsprechenden Backup- und Wiederherstellungsstrategien überwacht werden müssen. In dieser Übung verwenden wir die Linux-Version Ubuntu 12.04 und Cassandra 1.0.10; der Prozess kann allerdings auch an jede andere Linux-Distribution angepasst werden.

Schema zur Bereitstellung von Cassandra
---------------------------------------

Die Funktion Azure Virtual Machines ermöglicht die einfache Ausführung von NoSQL-Datenbanken wie [Cassandra](http://wiki.apache.org/cassandra/) in einer öffentlichen Microsoft-Cloud oder in einer privaten Cloud-Umgebung. Allerdings gibt es eine für die Infrastruktur von Azure Virtual Machines spezifische Ausnahme in Bezug auf die Konfiguration des virtuellen Netzwerks. Da zum aktuellen Zeitpunkt Cassandra nicht als verwalteter Dienst auf Azure verfügbar ist, beschreiben wir die Einrichtung eines Cassandra-Clusters auf virtuellen Computern und den Zugriff auf diese Cluster von einer anderen Linux-Instanz aus, die auch in Virtual Machines gehostet wird. Die gezeigten Codeausschnitte aus Node.js können auch in einer Webanwendung oder einem Webdienst mit PaaS-Hosting verwendet werden. Eine der wesentlichen Stärken von Azure ist das Modell der zusammengesetzten Anwendungen, das die Nutzung der besten Elemente von PaaS und IaaS erlaubt.

Es gibt zwei Bereitstellungsmodelle, die sich für die Cassandra-Anwendungsumgebung eignen: die Bereitstellung in selbständigen virtuellen Maschinen und die zusammengesetzte Bereitstellung. In einer zusammengesetzten Bereitstellung wird ein in Virtual Machines gehostetes Cassandra-Cluster von einer PaaS-gehosteten Webanwendung (oder einem PaaS-gehosteten Webdienst) genutzt. Dies erfolgt über die Thrift-Schnittstelle des Lastenausgleichsmoduls. Auch wenn jeder Cassandra-Knoten die Anfrage im Falle eines Schlüsselbereichsfehlers an andere Peer-Knoten weiterleitet, hilft das Lastenausgleichsmodul beim Lastenausgleich der eingehenden Anfragen. Außerdem erstellt es eine Firewall-geschützte Sandbox für bessere Datenkontrolle.

Zusammengesetzte Bereitstellung
-------------------------------

Das Ziel einer zusammengesetzten Bereitstellung ist die Maximierung der PaaS-Verwendung bei gleichzeitig minimierter Beteiligung virtueller Computer, um den Aufwand zu reduzieren, mit dem die Verwaltung der Infrastruktur dieser virtuellen Computer verbunden ist. Aufgrund des Serververwaltungsaufwands werden nur die Komponenten bereitgestellt, die ein statussensitives Verhalten erfordern und deren Änderung aus verschiedenen Gründen wie der baldigen Produkteinführung, der schlechten Auffindbarkeit im Quellcode oder des mangelnden Zugriffs auf das Betriebssystem nicht leicht ist.

![Diagramm einer zusammengesetzten Bereitstellung](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Bereitstellen eines virtuellen Azure-Computers
----------------------------------------------

![Virtuellen Computer bereitstellen](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

Im oben gezeigten Diagramm wird ein Cassandra-Cluster mit vier Knoten in Virtual Machines hinter einem Lastenausgleichmodul bereitgestellt, das Thrift-Verkehr erlaubt. Die Azure-gehostete PaaS-Anwendung greift mithilfe sprachspezifischer Thrift-Bibliotheken auf das Cluster zu. Es gibt Bibliotheken für die Sprachen Java, C\#, Node.js, Python und C++. Bei der im zweiten Diagramm gezeigten Bereitstellung auf selbständigen virtuellen Computern werden Daten von Anwendungen genutzt, die in einem anderen in Virtual Machines gehosteten Clouddienst laufen.

Aufgabe 1: Bereitstellen eines Linux-Clusters
---------------------------------------------

In der Vorabversion von Virtual Machines müssen alle Computer im selben Clouddienst bereitgestellt werden, damit die virtuellen Linux-Computer Teil desselben virtuellen Netzwerks sind. Die Erstellung eines Clusters geht normalerweise in dieser Reihenfolge vor sich:

![Ablaufdiagramm für das Erstellen eines Clusters](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png)

**Schritt 1: Erstellen eines SSH-Schlüsselpaars**

Azure benötigt einen öffentlichen X509-Schlüssel, der bei seiner Bereitstellung entweder PEM- oder DER-kodiert ist. Erstellen Sie ein öffentlich/privates Schlüsselpaar, indem Sie den Anweisungen unter [Verwenden von SSH mit Linux auf Azure](http://www.windowsazure.com/en-us/manage/linux/how-to-guides/ssh-into-linux/) folgen. Wenn Sie putty.exe als SSH-Client auf Windows oder Linux verwenden möchten, müssen Sie den PEM-kodierten privaten RSA-Schlüssel mithilfe von puttygen.exe in das PPK-Format umwandeln. Informationen dazu finden Sie unter [Generating SSH Key Pair for Linux VM Deployment on Windows Azure (Generieren eines SSH-Schlüsselpaars für die Bereitstellung virtueller Linux-Computer auf Windows Azure, in englischer Sprache)](http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx).

**Schritt 2: Erstellen eines virtuellen Ubuntu-Computers**

Melden Sie sich zur Erstellung des ersten virtuellen Ubuntu-Computers auf dem Azure-Vorschauportal an, klicken Sie auf **Neu**, auf **Virtueller Computer**, auf **From Gallery** und auf **Ubuntu Server 12.xx**. Klicken Sie dann auf den rechten Pfeil. Ein Lernprogramm, das die Erstellung eines virtuellen Linux-Computers beschreibt, finden Sie unter [Erstellen eines virtuellen Linux-Computers](http://www.windowsazure.com/en-us/manage/linux/tutorials/virtual-machine-from-gallery/).

Geben Sie anschließend auf dem Bildschirm "Konfiguration des virtuellen Computers" folgende Informationen ein:

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">Feldname</th>
		<th data-morhtml="true">Feldwert</th>
		<th data-morhtml="true">Anmerkungen</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Name des virtuellen Computers</td>
		<td data-morhtml="true">hk-cas1</td>
		<td data-morhtml="true">Dies ist der Hostname des virtuellen Computers</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Neuer Benutzername</td>
		<td data-morhtml="true">localadmin</td>
		<td data-morhtml="true">&quot;admin&quot; ist ein reservierter Benutzername in Ubuntu 12.xx</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Neues Kennwort</td>
		<td data-morhtml="true"><i data-morhtml="true">sicheres Kennwort</i></td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Kennwort best&auml;tigen</td>
		<td data-morhtml="true"><i data-morhtml="true">sicheres Kennwort</i></td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Gr&ouml;&szlig;e</td>
		<td data-morhtml="true">Klein</td>
		<td data-morhtml="true">W&auml;hlen Sie den virtuellen Computer entsprechend den E/A-Anforderungen. </td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Sichern Sie die Authentifizierung mit einem SSH-Schl&uuml;ssel</td>
		<td data-morhtml="true">Klicken Sie auf das Kontrollk&auml;stchen</td>
		<td data-morhtml="true">Aktivieren Sie es, wenn Sie die Authentifizierung mit einem SSH-Schl&uuml;ssel sichern m&ouml;chten</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Zertifikat</td>
		<td data-morhtml="true"><i data-morhtml="true">Dateiname des Zertifikats f&uuml;r den &ouml;ffentlichen Schl&uuml;ssel</i></td>
		<td data-morhtml="true">Mit OpenSSL oder anderen Tools generierter DER- oder PEM-kodierter &ouml;ffentlicher Schl&uuml;ssel</td>
	</tr>
</table>

<p>Geben Sie auf dem Bildschirm &quot;VM-Modus&quot;die folgenden Informationen ein:</p>

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">Feldname</th>
		<th data-morhtml="true">Feldwert</th>
		<th data-morhtml="true">Anmerkungen</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Eigenst&auml;ndiger virtueller Computer</td>
		<td data-morhtml="true">&quot;Aktivieren&quot; Sie das Optionsfeld</td>
		<td data-morhtml="true">Dies gilt f&uuml;r den ersten virtuellen Computer. F&uuml;r die weiteren virtuellen Computer verwenden wir die Option &quot;Connect to Existing VM&quot;.</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">DNS-Name</td>
		<td data-morhtml="true"><i data-morhtml="true">eindeutiger Name</i>.cloudapp.net</td>
		<td data-morhtml="true">Geben Sie dem Lastenausgleichsmodul einen dem Computer nicht bekannten Namen</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Speicherkonto</td>
		<td data-morhtml="true"><i data-morhtml="true">Standard-Speicherkonto</i></td>
		<td data-morhtml="true">Verwenden Sie das Standard-Speicherkonto, das Sie erstellt haben</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Region/Affinit&auml;tsgruppe/Virtuelles Netzwerk</td>
		<td data-morhtml="true">USA (Westen)</td>
		<td data-morhtml="true">W&auml;hlen Sie eine Region aus, in der Ihre Webanwendungen auf das Cassandra-Cluster zugreifen</td>
	</tr>
</table>

Wiederholen Sie das oben beschriebene Verfahren für alle virtuellen Computer, die Teil des Cassandra-Clusters sind. An diesem Punkt sind alle Computer Teil desselben Netzwerks und können einander "pingen". Wenn der Ping nicht funktioniert, prüfen Sie die Konfiguration der Firewall des virtuellen Computers (z. B. iptables), um sicherzugehen, dass ICMP erlaubt ist. Deaktivieren Sie ICMP, wenn Sie die Netzwerkkonnektivität erfolgreich getestet haben, um die Sicherheitsanfälligkeit zu reduzieren.

**Schritt 3: Hinzufügen eines Thrift-Endpunkts mit Lastenausgleich**

Nach Schritt 1 und Schritt sollte bereits für jeden virtuellen Computer ein SSH-Endpunkt definiert sein. Lassen Sie uns nun den Thrift-Endpunkt mit Lastenausgleich über den öffentlichen Port 9160 hinzufügen. Dies ist die entsprechende Sequenz.

a. Klicken Sie in der Detailansicht der ersten virtuellen Maschine auf "Endpunkt hinzufügen".

b. Wählen Sie auf dem Bildschirm "Add endpoint to virtual machine" die Optionsschaltfläche "Endpunkt hinzufügen"

c. Klicken Sie auf den rechten Pfeil

d. Geben Sie auf dem Bildschirm "Specify endpoint details" Folgendes ein:

<table data-morhtml="true">
	<tr data-morhtml="true">
		<th data-morhtml="true">Feldname</th>
		<th data-morhtml="true">Feldwert</th>
		<th data-morhtml="true">Anmerkungen</th>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Name</td>
		<td data-morhtml="true">cassandra</td>
		<td data-morhtml="true">Jeder eindeutige Endpunktname ist geeignet</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Protokoll</td>
		<td data-morhtml="true">TCP</td>
		<td data-morhtml="true"></td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">&Ouml;ffentlicher Port</td>
		<td data-morhtml="true">9160</td>
		<td data-morhtml="true">Standard-Thrift-Port. </td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">Privater Port</td>
		<td data-morhtml="true">9160</td>
		<td data-morhtml="true">Wenn Sie dies nicht in cassandra.yaml ge&auml;ndert haben</td>
	</tr>
</table>

Nach der Ausführung dieser Schritte ist am Cassandra-Endpunkt des ersten virtuellen Computers die Option "LOAD BALANCED" auf "NEIN" gesetzt. Ignorieren Sie dies für den Moment. Die Option wird auf "JA" gestellt, wenn wir diesen Endpunkt zu den weiteren virtuellen Computern hinzugefügt haben.

e. Wählen Sie nun den zweiten virtuellen Computer aus, und fügen Sie den Endpunkt hinzu, indem Sie das oben beschriebene Verfahren wiederholen. Der einzige Unterschied ist, dass Sie "Load-balance traffic on an existing endpoint" wählen und im Dropdown-Feld auf "cassandra-960" klicken. An diesem Punkt wird durch die Zuordnung des Endpunkts an beide virtuelle Computer die Statusoption "LOAD BALANCED" von "NEIN" auf "JA" gesetzt.

Wiederholen Sie "e" für die weiteren Knoten in diesem Cluster.

Nach dem Erstellen der virtuellen Maschinen ist es nun Zeit, Cassandra auf jeder dieser virtuellen Maschine einzurichten. Da Cassandra in vielen Linux-Distributionen nicht standardmäßig vorhanden ist, beschreiben wir hier einen manuellen Bereitstellungsprozess.

[Bitte beachten Sie, dass wir die Software auf jedem virtuellen Computer manuell installieren. Der Prozess kann allerdings beschleunigt werden, indem wir einen vollständig funktionsfähigen virtuellen Cassandra-Computer einrichten, ihn als Basis-Image erfassen und aus diesem Basis-Image weitere Instanzen erstellen. Die Anweisungen zum Erfassen des Linux-Images finden Sie unter [Erfassen des Images eines virtuellen Linux-Computers](https://www.windowsazure.com/en-us/manage/linux/how-to-guides/capture-an-image/).]

Aufgabe 2: Einrichten von Cassandra auf jedem virtuellen Computer
-----------------------------------------------------------------

**Schritt 1: Voraussetzungen für die Installation**

Cassandra erfordert Java Virtual Machine. Deswegen installieren die neueste JRE, indem wir den folgenden Befehl für Debian-Derivate einschließlich Ubuntu verwenden:

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**Schritt 2: Cassandra-Installation**

1.  Melden Sie sich über SSH auf dem virtuellen Linux (Ubuntu)-Computer an.

2.  Verwenden Sie wget, um Cassandra-Daten von dem unter (http://cassandra.apache.org/download/)[http://cassandra.apache.org/download/] vorgeschlagenen Mirror als apache-cassandra-bin.tar.gz in das Verzeichnis "\~/downloads" herunterzuladen. Bitte beachten Sie, dass die Versionsnummer nicht in der Download-Datei enthalten ist, um sicherzustellen, dass der Publikation die Version nicht bekannt ist.

3.  Entpacken Sie den Tarball in das Standard-Anmeldeverzeichnis, indem Sie den folgenden Befehl eingeben:

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    Dadurch wird die Archivdatei in das Verzeichnis apache-cassandra-[version] entpackt.

4.  Erstellen Sie die beiden folgenden Standardverzeichnisse für die Protokolle und die Daten:

         $ sudo chown -R /var/lib/cassandra
         $ sudo chown -R /var/log/cassandra

5.  Vergeben Sie eine Schreibberechtigung an die Benutzeridentität, unter der Casandra läuft

        a.   sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  Starten Sie Cassandra aus dem Verzeichnis apache-cassandra-[version]/bin mit dem folgenden Befehl:

        $ ./cassandra

Dadurch wird der Cassandra-Knoten als Hintergrundprozess gestartet. Verwenden Sie -cassandra "f", um den Prozess im Vordergrund zu starten.

Das Protokoll kann nun einen mx4j-:Fehler anzeigen. Cassandra funktioniert problemlos ohne mx4j, aber dies ist für die Verwaltung der Cassandra-Installation erforderlich. Beenden Sie den Cassandra-Prozess vor dem nächsten Schritt.

**Schritt 3: mx4j installieren**

    a)   mx4j herunterladen: wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
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

Nun, da die erforderliche Software auf allen einzelnen virtuellen Computern eingerichtet ist, ist es Zeit, die Kommunikation zwischen den Knoten durch die Seed-Konfiguration herzustellen. Prüfen Sie die Informationen unter <http://wiki.apache.org/cassandra/MultinodeCluster> auf detaillierte Angaben zur Konfiguration von Clustern mit mehreren Knoten.

**Schritt 6: Einrichten eines Clusters mit mehreren Knoten**

Bearbeiten Sie cassandra.yaml, um die folgenden Eigenschaften in allen virtuellen Computern zu ändern:

**a) cluster\_name**

Der Standard-Clustername lautet "Test Cluster". Ändern Sie ihn in eine Bezeichnung für Ihre Anwendung. Beispiel: "AppStore". Wenn Sie das Cluster zum Testen während der Installation bereits mit dem Namen "Test Cluster" gestartet haben, kommt es zu einem Namenskonflikt. Löschen Sie alle Dateien im Verzeichnis /var/lib/cassandra/data/system directory, um dieses Problem zu beheben.

**b) seeds**

Die hier angegebenen IP-Adressen werden von neuen Knoten verwendet, um Informationen zur Ringtopologie zu erhalten. Legen Sie die zuverlässigsten Knoten als Ihre Seeds in einem kommagetrennten Format fest. "*host1*,*host2*" . Beispieleinstellung: "hk-ub1,hk-ub2".

Wir übernehmen die Standard-Tokens von den Seed-Servern, da dies für die Übung nicht wichtig ist. Informationen zur Generierung optimaler Tokens finden Sie im Python-Skript unter: <http://wiki.apache.org/cassandra/GettingStarted>.

Starten Sie Cassandra auf allen Knoten neu, um die oben beschriebenen Änderungen zu übernehmen.

**Schritt 7: Testen des Clusters mit mehreren Knoten**

Das im bin-Verzeichnis von Cassandra installierte Nodetool hilft bei Cluster-Operationen. Wir verwenden Nodetool, um die Cluster-Einrichtung über das folgende Befehlsformat zu prüfen:

    $ bin/nodetool -h <hostname> -p 7199 ring

Wenn die Konfiguration korrekt ist, sollten die Informationen wie unten für einen Cluster mit 3 Knoten dargestellt angezeigt werden:

<table data-morhtml="true">
	<tr data-morhtml="true">
		<td data-morhtml="true">Adresse</td>
		<td data-morhtml="true">DC</td>
		<td data-morhtml="true">Rack</td>
		<td data-morhtml="true">Status</td>
		<td data-morhtml="true">Zustand</td>
		<td data-morhtml="true">Last</td>
		<td data-morhtml="true">Eigent&uuml;mer</td>
		<td data-morhtml="true">Token</td>
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true"></td>
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true"></td>	
		<td data-morhtml="true">149463697837832744402916220269706844972</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.196.68</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">Up</td>	
		<td data-morhtml="true">Normal</td>	
		<td data-morhtml="true">15,69 KB</td>	
		<td data-morhtml="true">25.98%</td>	
		<td data-morhtml="true">114445918355431753244435008039926455424</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.198.81</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">Up</td>	
		<td data-morhtml="true">Normal</td>	
		<td data-morhtml="true">15,69 KB</td>	
		<td data-morhtml="true">53.44%</td>	
		<td data-morhtml="true">70239176883275351288292106998553981501</td>	
	</tr>
	<tr data-morhtml="true">
		<td data-morhtml="true">10.26.198.84</td>
		<td data-morhtml="true">datacenter1</td>	
		<td data-morhtml="true">rack1</td>	
		<td data-morhtml="true">Up</td>	
		<td data-morhtml="true">Normal</td>	
		<td data-morhtml="true">18,35 KB</td>	
		<td data-morhtml="true">25.98%</td>	
		<td data-morhtml="true">149463697837832744402916220269706844972</td>	
	</tr>
</table>


An diesem Punkt ist das Cluster bereit für Thrift-Clients über die Clouddienst-URL, die während der Aufgabe "Bereitstellen eines Linux-Clusters" erstellt wurde. (Der DNS-Name wird bei der Erstellung des ersten virtuellen Computers vergeben.)

Aufgabe 3: Zugreifen auf das Cassandra-Cluster in Node.js
---------------------------------------------------------

Erstellen Sie eine virtuelle Linux-Maschine in Azure mithilfe des in den vorhergehenden Aufgaben beschriebenen Prozesses. Stellen Sie sicher, dass es sich um einen eigenständigen virtuellen Computer handelt, da wir ihn als Cient für den Zugriff auf das Cassandra-Cluster verwenden. Wir installieren Node.js, NPM und [cassandra-client](https://github.com/racker/node-cassandra-client) von Github, bevor wir auf diesem virtuellen Computer eine Verbindung zum Cassandra-Cluster herstellen:

**Schritt 1: Installieren von Node.js und NPM**

a) Installation der Voraussetzungen

    sudo apt-get install g++ libssl-dev apache2-utils make

b) Wir verwenden zur Kompilierung und Installation die Quelle von Github. Bevor wir das Repository klonen, müssen wir die Core Runtime von Git installieren:

    sudo apt-get install git-core

c) Knoten-Repository klonen

    git clone git://github.com/joyent/node.git

d) Dadurch wird ein Verzeichnis mit dem Name "node" erstellt. Geben Sie die folgende Befehlssequenz ein, um Node.js zu kompilieren und zu installieren:

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

![Zeilen und Spalten](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png)

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

Die Funktion createKeysapce nimmt eine Rückruffunktion als Argument zur Ausführung der COLUMNFAMILY-Erstellungsfunktion, da KEYSPACE eine Voraussetzung für die COLUMNFAMILY-Erstellung ist. Bitte beachten Sie, dass wir eine Verbindung zum "System" KEYSPACE herstellen müssen, um die Anwendung KEYSPACE zu definieren. In all diesen Ausschnitten wird für die Interaktion mit dem Cluster durchgehend [Cassandra Query Language (CQL)](http://cassandra.apache.org/doc/cql/CQL.html) verwendet. Da die im Skript oben zusammengestellte CQL keine Parametermarker aufweist, verwenden wir für die Methode PooledConnection.execute() eine leere Parametersammlung ("[]").

Nach der erfolgreichen Keyspace-Erstellung wird die im folgenden Ausschnitt gezeigte Funktion createColumnFamily() ausgeführt, um die erforderlichen COLUMNFAMILY-Definitionen zu erstellen:

    casdemo.js: 
    //Erstellt COLUMNFAMILY
    function createColumnFamily(ksConOptions, callback){
      var params = ['customers_cf','custid','varint','custname',
                    'text','custaddress','text'];
      var cql = 'CREATE COLUMNFAMILY 
     (
     
     PRIMARY KEY,
     
    , 
     
    )';
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
    //Daten einfügen
    function populateCustomerData() {
       var params = ['John','Infinity Dr, TX', 1];
       updateCustomer(ksConOptions,params);

       params = ['Tom','Fermat Ln, WA', 2];
       updateCustomer(ksConOptions,params);
    }

    //Durch Aktualisierung wird der Datensatz eingefügt, wenn bisher keiner vorhanden ist
    function updateCustomer(ksConOptions,params)
    {
      var cql = 'UPDATE customers_cf SET custname=
       ,custaddress=
        where 
                 custid=
       ';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) console.log(err);
          else console.log("Inserted customer : " + params[0]);
      });
      con.shutdown();
    }

populateCustomerData() fügt einige Zeilen namens customers\_cf in die COLUMNFAMILY ein. In Cassandra Query Language fügt UPDATE den Datensatz ein, wenn dieser nicht bereits vorhanden ist. Dies macht die Anweisung INSERT CQL überflüssig.

Die Rückrufkette sieht bis jetzt so aus: von createKeyspace() zu createColumnFamily() zu populateCustomerData(). Nun ist es Zeit für uns, den Code entsprechend dem folgenden Codeausschnitt auszuführen:

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    createKeyspace(createColumnFamily);
    //Rest nicht gezeigt

Führen Sie in der Eingabeaufforderung der Shell den folgenden Befehl aus, um das Skript auszuführen:

    //Mit dem folgenden Befehl werden der KEYSPACE und die COLUMNFAMILY //erstellt sowie zwei Kundendatensätze eingefügt
    $ node casdemo.js

Mit der Methode readCustomer() wird auf das in Azure gehosteten Cluster zugegriffen und der JSON-Ausschnitt angezeigt, der nach der Ausführung der CQL-Anfrage abgerufen wird:

    casdemo.js: 
    //Die beiden oben angezeigten Zeilen lesen
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
    //Rest des Codes nicht gezeigt

Zusammenfassung
---------------

Azure Virtual Machines erlaubt die Erstellung virtueller Linux- (von Microsoft-Partnern bereitgestellte Images) und Windows-Computer, die eine Migration vorhandener Server-Produkte und -Anwendungen ohne Änderungen ermöglichen. Ein Beispiel dafür ist der in diesem Artikel diskutierte Cassandra-NoSQL-Datenbankserver. Auf das in diesem Artikel eingerichtete Cassandra-Cluster kann über Azure-gehostete Clouddienste, öffentliche Clouds von Drittanbietern und private Clouds in Windows- und Linux-Umgebungen zugegriffen werden. In diesem Artikel haben wir Node.js als Client genannt; ein Zugriff auf Cassandra ist aber auch mit .NET-, Java- und anderen Umgebungen möglich.

