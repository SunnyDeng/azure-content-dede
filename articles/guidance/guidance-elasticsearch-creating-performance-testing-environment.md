<properties
   pageTitle="Erstellen einer Leistungstestumgebung für Elasticsearch | Microsoft Azure"
   description="So richten Sie eine Umgebung zum Testen der Leistung eines Elasticsearch-Clusters ein"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Erstellen einer Leistungstestumgebung für Elasticsearch in Azure

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch.md).

Dieses Dokument beschreibt, wie Sie eine Umgebung zum Testen der Leistung eines Elasticsearch-Clusters einrichten. Diese Konfiguration wurde verwendet, um die Leistung der Datenerfassung und Abfrageworkloads gemäß der Beschreibung in [Optimierung der Datenerfassungsleistung für Elasticsearch in Azure][] zu testen.

Für den Leistungstestprozess wurde [Apache JMeter](http://jmeter.apache.org/) verwendet. Dabei war der [Standardsatz](http://jmeter-plugins.org/wiki/StandardSet/) von Plug-Ins in einer Master/Slave-Konfiguration mit einem Satz von dedizierten virtuellen Computern (nicht Teil der Elasticsearch-Clusters) installiert, die speziell für diesen Zweck konfiguriert wurden.

Der [PerfMon-Server-Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/) wurde auf jedem Elasticsearch-Knoten installiert. Die folgenden Abschnitte enthalten Anleitungen zur Wiederherstellung der Testumgebung, damit Sie eigene Leistungstests mit JMeter durchführen können. Diese Anleitungen setzen voraus, dass Sie bereits einen Elasticsearch-Cluster mit Knoten erstellt haben, die über ein virtuelles Azure-Netzwerk verbunden sind.

Beachten Sie, dass die Testumgebung auch als ein Satz von virtuellen Azure-Computern ausgeführt wird, der mithilfe einer einzelne Azure-Ressourcengruppe verwaltet wird.

[Marvel](https://www.elastic.co/products/marvel) wurde ebenfalls installiert und so konfiguriert, dass eine einfachere Überwachung und Analyse der internen Aspekte des Elasticsearch-Clusters möglich ist. Wenn die JMeter-Statistiken Spitzen- oder Talwerte bei der Leistung anzeigen, können sich über Marvel verfügbare Informationen beim Ermitteln der Ursache für die Schwankungen als sehr nützlich erweisen.

Die folgende Abbildung zeigt die Struktur des gesamten Systems.

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

Beachten Sie folgende Punkte:

- Auf dem virtuellen JMeter-Mastercomputer wird Windows Server ausgeführt, um die GUI-Umgebung für die JMeter-Konsole bereitzustellen. Der virtuelle JMeter-Mastercomputer stellt die GUI (die *jmeter* Anwendung) bereit, um einem Tester das Erstellen und Ausführen von Tests sowie das Visualisieren der Ergebnisse zu ermöglichen. Dieser virtuelle Computer wird mit den virtuellen JMeter-Mastercomputern koordiniert, die tatsächlich die Anforderungen senden, aus denen sich die Tests zusammensetzen.

- Auf den untergeordneten virtuellen JMeter-Computern wird Ubuntu Server (Linux) ausgeführt; für diese virtuellen Computer ist keine GUI erforderlich. Auf den virtuellen JMeter-Computern wird die JMeter-Serversoftware (die *jmeter-server*-Anwendung) ausgeführt, um Anforderungen an den Elasticsearch-Cluster zu senden.

- Dedizierte Clientknoten wurden nicht verwendet, dedizierte Masterknoten jedoch schon.

- Die Anzahl der Datenknoten im Cluster kann je nach getestetem Szenario variieren.

- Alle Knoten im Elasticsearch-Cluster führen Marvel aus, um die Leistung zur Laufzeit zu beobachten; außerdem wird der JMeter-Server-Agent zum Sammeln von Überwachungsdaten für die spätere Analyse ausgeführt.

- Beim Testen von Elasticsearch 2.0.0 und höher führt einer der Datenknoten auch Kibana aus; dies ist für die Marvel-Version erforderlich, die auf Elasticsearch 2.0.0 und höher ausgeführt wird.

## Erstellen einer Azure-Ressourcengruppe für die virtuellen Computer

Der JMeter-Mastercomputer muss in der Lage sein, eine direkte Verbindung zu jedem der Knoten im Elasticsearch-Cluster herzustellen, um Leistungsdaten zu sammeln. Unterscheidet sich das JMeter-VNet vom VNet des Elasticsearch-Clusters, muss jeder Elasticsearch-Knoten mit einer öffentlichen IP-Adresse konfiguriert werden. Wenn dies ein Problem mit Ihrer Elasticsearch-Konfiguration darstellt, erwägen Sie eine Implementierung der virtuellen JMeter-Computer im selben VNet wie der Elasticsearch-Cluster mithilfe der gleichen Ressourcengruppe; in diesem Fall können Sie diese erste Prozedur auslassen.

[Erstellen Sie zunächst eine Ressourcengruppe](../articles/resource-group-portal/#create-resource-group-and-resources). In diesem Dokument wird davon ausgegangen, dass Ihre Ressourcengruppe *JMeterPerformanceTest* heißt. Wenn Sie die virtuellen JMeter-Computer im selben VNet wie den Elasticsearch-Cluster ausführen möchten, verwenden Sie dieselbe Ressourcengruppe wie dieser Cluster anstatt eine neue zu erstellen.

## Erstellen des virtuellen JMeter-Mastercomputers

Erstellen Sie als nächstes mithilfe des *Windows Server 2008 R2 SP1*-Images einen [virtuellen Windows-Computer](../articles/virtual-machines-windows-tutorial/). Es wird empfohlen, einen virtuellen Computer mit ausreichend Kernen und Arbeitsspeicher zum Ausführen von Leistungstests zu wählen. Im Idealfall verfügt der Computer über mindestens 2 Kerne und 3,5 GB RAM (A2-Standard oder größer).

<!-- TODO add info on why disabling diagnostics is positive -->

Es wird empfohlen, die Diagnose zu deaktivieren. Das Erstellen des virtuellen Computers im Portal erfolgt unter *Diagnose* im Abschnitt *Überwachung* auf dem Blatt *Einstellungen*. Behalten Sie für andere Einstellungen die Standardwerte bei.

Stellen Sie sicher, dass der virtuelle Computer und alle zugehörigen Ressourcen erfolgreich erstellt wurden, indem Sie im Portal die [Ressourcengruppe untersuchen](../articles/resource-group-portal/#browse-resource-groups). Die aufgelisteten Ressourcen sollten Folgendes umfassen: einen virtuellen Computer, eine Netzwerksicherheitsgruppe und eine öffentliche IP-Adresse mit dem gleichen Namen sowie eine Netzwerkschnittstelle und ein Speicherkonto mit Namen, die auf dem des virtuellen Computers basieren.

## Erstellen der untergeordneten virtuellen JMeter-Computer

Erstellen Sie jetzt mithilfe des *Ubuntu Server 14.04 LTS*-Images einen [virtuellen Linux-Computer](../articles/virtual-machines-linux-tutorial-portal-rm/). Wählen Sie wie für den virtuellen JMeter-Mastercomputer einen virtuellen Computer mit ausreichend Kernen und Arbeitsspeicher zum Ausführen von Leistungstests. Im Idealfall verfügt der Computer über mindestens 2 Kerne und mindestens 3,5 GB RAM (A2-Standard oder größer).

Auch hier wird empfohlen, die Diagnose zu deaktivieren.

Sie können beliebig viele untergeordnete virtuelle Computer erstellen.

## Installieren eines JMeter-Servers auf den untergeordnete virtuellen JMeter-Computern

Auf den untergeordneten virtuellen JMeter-Computern wird Linux ausgeführt, und Sie können standardmäßig keine Verbindung mit Ihnen herstellen, indem Sie eine Remotedesktopverbindung (RDP) öffnen. Stattdessen können Sie auf jedem virtuellen Computer [mithilfe von PuTTY ein Befehlszeilenfenster öffnen](../articles/virtual-machines-linux-how-to-log-on/).

Nachdem Sie eine Verbindung zu einem der untergeordneten virtuellen Computer hergestellt haben, richten wir JMeter mithilfe von Bash ein.

Installieren Sie zunächst die Java-Laufzeitumgebung, die zum Ausführen von JMeter erforderlich ist.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

Laden Sie jetzt die als ZIP-Datei verpackte JMeter-Software herunter.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

Installieren Sie den Befehl zum Entzippen, und verwenden Sie ihn dann zum Erweitern der JMeter-Software. Die Software wird in einen Ordner mit dem Namen **apache-jmeter-2.13** kopiert.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

Wechseln Sie zum *bin*-Verzeichnis mit den ausführbaren JMeter-Dateien, und machen Sie die Programme *jmeter-server* und *jmeter* ausführbar.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

Nun müssen wir die Datei `jmeter.properties` bearbeiten, die sich im aktuellen Ordner befindet (verwenden Sie den Text-Editor, mit denen Sie am besten vertraut sind, z. B. *vi* oder *vim*). Suchen Sie die folgenden Zeilen:

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

Entfernen Sie den Kommentar (entfernen Sie die vorangestellten ##-Zeichen), und ändern Sie diese Zeilen wie unten gezeigt. Speichern Sie dann die Datei, und schließen Sie den Editor:

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

Führen Sie jetzt die folgenden Befehle zum Öffnen von Port 4441 für eingehenden TCP-Datenverkehr aus (dies ist der Port, auf dem Sie gerade *jmeter-server* für das Abhören konfiguriert haben):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

Laden Sie eine ZIP-Datei mit der Standardsammlung von Plug-Ins für JMeter herunter (diese Plug-Ins bieten Leistungsüberwachung mithilfe von Indikatoren), und extrahieren Sie die Datei. Durch Entzippen der Datei an diesem Speicherort werden die Plug-Ins im richtigen Ordner platziert.

Wenn Sie aufgefordert werden, die LICENSE-Datei zu ersetzen, geben Sie „A“ ein (für alle):

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

Verwenden Sie `nohup`, um den JMeter-Server im Hintergrund zu starten. Er sollte mit der Anzeige einer Prozess-ID und einer Meldung reagieren, dass er ein Remoteobjekt erstellt hat und bereit ist, mit dem Empfang von Befehlen zu beginnen.

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] Beim Herunterfahren des virtuellen Computers wird das JMeter-Serverprogramm beendet. Sie müssen die Verbindung mit dem virtuellen Computer herstellen und sie manuell neu starten. Sie können das System auch für die automatische Ausführung des Befehls *jmeter-server* beim Start konfigurieren. Fügen Sie dazu der `/etc/rc.local`-Datei folgende Befehle hinzu (vor dem Befehl *exit 0*):

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

Ersetzen Sie `<username>` durch Ihren Benutzernamen.

Es ist sinnvoll, das Terminalfenster geöffnet zu lassen, damit Sie den Fortschritt des JMeter-Servers überwachen können, während der Test ausgeführt wird.

Sie müssen diese Schritte für jeden untergeordneten virtuellen JMeter-Computer wiederholen.

## Installieren des JMeter-Server-Agents auf den Elasticsearch-Knoten

Dieses Verfahren setzt voraus, dass Sie Anmeldezugriff auf die Elasticsearch-Knoten haben. Wenn Sie den Cluster mithilfe der ARM-Vorlage erstellt haben, können Sie wie im Abschnitt [Azure-Produktionstopologie](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies) veranschaulicht über den virtuellen Jump Box-Computer Verbindungen mit den einzelnen Knoten herstellen. Für die Verbindung mit Jump Box können Sie auch PuTTY verwenden.

Damit können Sie den *ssh*-Befehl für die Anmeldung bei allen Knoten im Elasticsearch-Cluster verwenden.

Melden Sie sich bei einem der Elasticsearch-Knoten als Administrator an. Geben Sie an der Bash-Eingabeaufforderung die folgenden Befehle zum Erstellen eines Ordners für den JMeter-Server-Agent ein, und navigieren Sie zu diesem Ordner:

```bash
mkdir server-agent
cd server-agent
```

Führen Sie die folgenden Befehle zum Installieren des *unzip*-Befehls (falls er nicht bereits installiert ist) aus, laden Sie die JMeter-Server-Agent-Software herunter und entpacken Sie sie:

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
Führen Sie den folgenden Befehl zum Konfigurieren der Firewall aus, und aktivieren Sie TCP-Datenverkehr über Port 4444 (dieser Port wird vom JMeter-Server-Agent verwendet):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

Führen Sie den folgenden Befehl zum Starten des JMeter-Server-Agents im Hintergrund aus:

```bash
nohup ./startAgent.sh &
```

Der JMeter-Server-Agent sollte mit Meldungen reagieren, dass er gestartet wurde und an Port 4444 lauscht. Drücken Sie die EINGABETASTE, um eine Eingabeaufforderung aufzurufen, und führen Sie dann folgenden Befehl aus: Ersetzen Sie `<nodename>` durch den Namen Ihres Knotens. Wenn Sie den Namen Ihres Knotens nicht wissen, finden Sie ihn mithilfe des `hostname`-Befehls:

```bash
telnet <nodename> 4444
```

Mit diesem Befehl wird auf Ihrem lokalen Computer eine Telnet-Verbindung zu Port 4444 geöffnet. Über diese Verbindung können Sie überprüfen, ob der JMeter-Server-Agent ordnungsgemäß ausgeführt wird.

Wenn der JMeter-Server-Agent nicht ausgeführt wird, erhalten Sie die folgende Antwort:

`*telnet: Unable to connect to remote host: Connection refused*.`

Wenn der JMeter-Server-Agent ausgeführt wird und Port 4444 ordnungsgemäß konfiguriert wurde, sehen Sie die folgende Antwort:

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] Die Telnet-Sitzung bietet nach dem Herstellen der Verbindung keine Eingabeaufforderung.

Geben Sie in der Telnet-Sitzung den folgenden Befehl ein:

``` 
test
```

Wenn der JMeter-Server-Agent konfiguriert ist und ordnungsgemäß lauscht, sollte er darauf hinweisen, dass er den Befehl empfangen hat und mit der Nachricht *Yep* antworten.

> [AZURE.NOTE] Sie können andere Befehle zum Abrufen von Leistungsüberwachungsdaten eingeben. Mit dem Befehl `metric-single:cpu:idle` erhalten Sie z. B. den aktuellen Anteil der Zeit, den die CPU im Leerlauf verbringt (hierbei handelt es sich um eine Momentaufnahme). Eine vollständige Liste mit Befehlen finden Sie auf der Seite [PerfMon-Server-Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/).

Geben Sie in der Telnet-Sitzung den folgenden Befehl zum Beenden der Sitzung ein, und kehren Sie zur Bash-Eingabeaufforderung zurück:

``` 
exit
```

> [AZURE.NOTE] Wie bei den untergeordneten virtuellen JMeter-Computern muss der JMeter-Server-Agent mit dem `startAgent.sh` Befehl manuell neu gestartet werden, wenn Sie sich abmelden oder wenn dieser Computer heruntergefahren und neu gestartet wird. Wenn der JMeter-Server-Agent automatisch gestartet werden soll, fügen Sie den folgenden Befehl am Ende der `/etc/rc.local`-Datei ein, bevor Sie den *exit 0*-Befehl ausführen. Ersetzen Sie `<username>` durch Ihren Benutzernamen.

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

Ersetzen Sie `<username>` durch Ihren Benutzernamen.

Sie können jetzt entweder diesen gesamten Prozess für alle anderen Knoten im Elasticsearch-Cluster wiederholen, oder Sie können mithilfe des `scp`-Befehls den Ordner „server-agent“ und seinen Inhalt auf alle anderen Knoten kopieren und den JMeter-Server-Agent mithilfe des `ssh`-Befehls wie unten dargestellt starten. Ersetzen Sie `<username>` durch Ihren Benutzernamen und `<nodename>` durch den Namen des Knotens, auf den Sie die Software kopieren und ausführen möchten (möglicherweise werden Sie beim Ausführen der einzelnen Befehle zur Eingabe Ihres Kennworts aufgefordert):

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## Installieren und Konfigurieren von JMeter auf dem virtuellen JMeter-Mastercomputer

Klicken Sie im Azure-Portal auf *Ressourcengruppen*. Klicken Sie im Blatt *Ressourcengruppen* auf die Ressourcengruppe mit den virtuellen JMeter-Master- und Slave-Computern. Klicken Sie auf dem Blatt *Ressourcengruppe* auf den virtuellen JMeter-Mastercomputer. Klicken Sie auf Blatt „Virtueller Computer“ auf der Symbolleiste auf *Verbinden*. Öffnen Sie die RDP-Datei, wenn Sie vom Webbrowser dazu aufgefordert werden. Windows erstellt eine Remotedesktopverbindung mit Ihrem virtuellen Computer. Geben Sie bei entsprechender Aufforderung den Benutzernamen und das Kennwort für den virtuellen Computer ein.

Navigieren Sie auf dem virtuellen Computer in Internet Explorer zur Seite [Java für Windows herunterladen](http://www.java.com/en/download/ie_manual.jsp). Folgen Sie den Anweisungen zum Herunterladen und Ausführen des Java-Installationsprogramms.

Navigieren Sie im Webbrowser zur Seite [Apache JMeter herunterladen](http://jmeter.apache.org/download_jmeter.cgi), und laden Sie die ZIP-Datei mit der aktuellen Binärdatei herunter. Speichern Sie die ZIP-Datei an einem geeigneten Speicherort auf Ihrem virtuellen Computer.

Wechseln Sie zur Website [Benutzerdefinierte JMeter-Plug-Ins](http://jmeter-plugins.org/), und laden Sie den Standardsatz von Plug-Ins herunter. Speichern Sie die ZIP-Datei im gleichen Ordner wie den JMeter-Download aus dem vorherigen Schritt.

Öffnen Sie in Windows Explorer den Ordner mit der ZIP-Datei „apache-jmeter-*xx*“ (wobei *xx* für die aktuelle Version von JMeter steht), und extrahieren Sie die Dateien in den aktuellen Ordner (die ZIP-Datei erstellt einen Unterordner namens „apache-jmeter-*xxx*“).

Extrahieren Sie die Dateien in der Datei „JMeterPlugins-Standard-*yyy*.zip“ (wobei *yyy* die aktuelle Version der Plug-Ins ist) im Ordner „apache-jmeter-*xxx*“. Dadurch werden die Plug-Ins dem richtigen Ordner für JMeter hinzugefügt (Sie können die lib-Ordner problemlos zusammenführen und bei entsprechender Aufforderung die Lizenz- und Readme-Dateien überschreiben).

Wechseln Sie zum Ordner „apache-jmeter -*xxx*/bin“, und bearbeiten Sie die Datei „jmeter.properties“ mit Notepad. Suchen Sie in der `jmeter.properties`-Datei den Abschnitt mit der Bezeichnung *Remotehosts und RMI-Konfiguration*. Suchen Sie in diesem Abschnitt der Datei nach der folgenden Zeile:

```yaml
remote_hosts=127.0.0.1
```

Ändern Sie diese Zeile, und ersetzen Sie die IP-Adresse „127.0.0.1“ durch eine kommagetrennte Liste von IP-Adressen oder Hostnamen für die einzelnen untergeordneten JMeter-Server. Beispiel:

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

Suchen Sie die folgende Zeile, entfernen Sie das `#`-Zeichen am Anfang der Zeile, und ändern Sie den Wert der „client.rmi.localport“-Einstellungen von:

```yaml
#client.rmi.localport=0
```

to:

```yaml
client.rmi.localport=4440
```

Speichern Sie die Datei, und schließen Sie Notepad.

Klicken Sie auf der Windows-Symbolleiste auf *Start*, *Verwaltungstools* und dann auf *Windows-Firewall mit erweiterter Sicherheit*. Klicken Sie im Fenster *Windows-Firewall mit erweiterter Sicherheit* im linken Bereich mit der rechten Maustaste auf *Eingehende Regeln*, und klicken Sie dann auf *Neue Regel*.

Wählen Sie auf der Seite *Regeltyp* im *Assistenten* für neue *eingehende Regel* die Option *Port*, und klicken Sie dann auf *Weiter*. Wählen Sie auf der Seite *Protokolle und Ports* zuerst *TCP*, dann *Bestimmte lokale Ports*, geben Sie im Textfeld `4440-4444` ein, und klicken Sie dann auf *Weiter*. Wählen Sie auf der Seite *Aktion* die Option *Verbindung zulassen* aus, und klicken Sie anschließend auf *Weiter*. Lassen Sie auf der Seite *Profil* alle Optionen aktiviert, und klicken Sie dann auf *Weiter*. Geben Sie auf der Seite *Name* im Textfeld *Name* *JMeter* ein, und klicken Sie dann auf *Fertig stellen*. Schließen Sie das Fenster *Windows-Firewall mit erweiterter Sicherheit*.

Doppelklicken Sie in Windows-Explorer im Ordner „apache-jmeter-*xx*/bin“ auf die Windows-Batchdatei *jmeter*, um die GUI zu starten. Die Benutzeroberfläche sollte angezeigt werden:

![](./media/guidance-elasticsearch/performance-image17.png)

Klicken Sie in der Menüleiste auf *Ausführen* und auf *Remotestart*, und stellen Sie sicher, dass die beiden untergeordneten JMeter-Computer aufgelistet sind:

![](./media/guidance-elasticsearch/performance-image18.png)

Jetzt sind Sie bereit, mit den Leistungstests zu beginnen.

## Installieren und Konfigurieren von Marvel

Die Elasticsearch-Schnellstartvorlage für Azure installiert und konfiguriert die geeignete Version von Marvel automatisch, wenn Sie die MARVEL- und KIBANA-Parameter beim Erstellen des Clusters festlegen auf „true“ festlegen:

![](./media/guidance-elasticsearch/performance-image19.png)

Wenn Sie Marvel einem vorhandenen Cluster hinzufügen, müssen Sie die Installation manuell ausführen, und der Prozess unterscheidet sich wie in den folgenden Verfahren beschrieben je nachdem, ob Sie Elasticsearch Version 1.7.x oder 2.x verwenden.

### Installieren von Marvel mit Elasticsearch 1.73 oder früher

Wenn Sie Elasticsearch 1.7.3 oder früher verwenden, führen Sie die folgenden Schritte *auf jedem Knoten* im Cluster aus:

- Melden Sie sich beim Knoten an, und wechseln Sie zum Elasticsearch-Basisverzeichnis. Unter Linux ist das Stammverzeichnis in der Regel `/usr/share/elasticsearch`.

-  Führen Sie den folgenden Befehl aus, um das Marvel-Plug-In für Elasticsearch herunterzuladen und zu installieren:

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- Beenden Sie Elasticsearch, und starten Sie es auf dem Knoten neu:

```bash
sudo service elasticsearch restart
```

- Um sicherzustellen, dass Marvel ordnungsgemäß installiert wurde, öffnen Sie einen Webbrowser und rufen die URL `http://<server>:9200/_plugin/marvel` auf. Ersetzen Sie `<server>` durch den Namen oder die IP-Adresse eines beliebigen Elasticsearch-Servers im Cluster. Stellen Sie sicher, dass eine Seite ähnlich der unten dargestellten angezeigt wird:

![](./media/guidance-elasticsearch/performance-image20.png)


### Installieren von Marvel mit Elasticsearch 2.0.0 oder höher

Wenn Sie Elasticsearch 2.0.0 oder höher verwenden, führen Sie die folgenden Aufgaben *auf jedem Knoten* im Cluster aus:

Melden Sie sich beim Knoten an und wechseln Sie zum Elasticsearch-Basisverzeichnis (in der Regel `/usr/share/elasticsearch`). Führen Sie die folgenden Befehle zum Herunterladen und Installieren des Marvel-Plug-Ins für Elasticsearch aus:

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

Beenden Sie Elasticsearch, und starten Sie es auf dem Knoten neu:

```bash
sudo service elasticsearch restart
```

Ersetzen Sie im folgenden Verfahren `<kibana-version>` durch „4.2.2“, wenn Sie Elasticsearch 2.0.0 oder Elasticsearch 2.0.1 verwenden, oder durch „4.3.1“, wenn Sie Elasticsearch 2.1.0 oder höher verwenden. Ersetzen Sie `<marvel-version>` durch „2.0.0“, wenn Sie Elasticsearch 2.0.0 oder Elasticsearch 2.0.1 verwenden, oder durch „2.1.0“, wenn Sie Elasticsearch 2.1.0 oder höher verwenden. Führen Sie die folgenden Aufgaben *auf einem Knoten* im Cluster aus:

Melden Sie sich beim Knoten an und laden Sie von der [Elasticsearch-Downloadwebsite](https://www.elastic.co/downloads/past-releases) den richtigen Build von Kibana für Ihre Version von Elasticsearch herunter, und extrahieren Sie dann das Paket:

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

Öffnen Sie Port 5601, um eingehende Anforderungen zu akzeptieren:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Wechseln Sie zum Kibana-Konfigurationsordner (`kibana-<kibana-version>-linux-x64/config`), bearbeiten Sie die `kibana.yml`-Datei, und fügen Sie die folgende Zeile hinzu. Ersetzen Sie `<server>` durch den Namen oder die IP-Adresse eines Elasticsearch-Servers im Cluster.

```yaml
elasticsearch.url: "http://<server>:9200"
```

Wechseln Sie zum Kibana-Ordner „Bin“ (`kibana-<kibana-version>-linux-x64/bin`), und führen Sie den folgenden Befehl zum Integrieren des Marvel-Plug-Ins in Kibana aus:

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Starten Sie Kibana:

```bash
sudo nohup ./kibana &
```

Öffnen Sie zum Überprüfen der Marvel-Installation einen Webbrowser, und rufen Sie die URL `http://<server>:5601/app/marvel` auf. Ersetzen Sie `<server>` durch den Namen oder die IP-Adresse des Servers, auf dem Kibana ausgeführt wird.

Stellen Sie sicher, dass eine Seite ähnlich der unten dargestellten angezeigt wird (der Name Ihres Clusters lautet wahrscheinlich anders als in der Abbildung).

![](./media/guidance-elasticsearch/performance-image21.png)

Klicken Sie auf den Link, der Ihrem Cluster entspricht („elasticsearch210“ in der Abbildung oben). Eine Seite ähnlich der folgenden sollte angezeigt werden:

![](./media/guidance-elasticsearch/performance-image22.png)

[Optimierung der Datenerfassungsleistung für Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->