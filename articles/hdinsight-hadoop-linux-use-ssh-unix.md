<properties
   pageTitle="Verwenden von SSH-Schlüsseln mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X | Azure"
   description="Erfahren Sie, wie Sie SSH-Schlüssel für die Authentifizierung bei Linux-basierten HDInsight-Clustern erstellen und verwenden."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

## Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X (Vorschau)

Linux-basierte HDInsight-Cluster bieten die Möglichkeit, den SSH-Zugriff mithilfe eines Kennworts oder eines SSH-Schlüssels zu schützen. Dieses Dokument enthält Informationen zur Verwendung von SSH mit HDInsight auf Linux-, Unix- oder OS X-Clients.

> [AZURE.NOTE] Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie einen Linux-, Unix- oder OS X-Client verwenden. Obwohl diese Schritte auf einem Windows-Client ausgeführt werden können, wenn Sie ein Paket installiert haben, das `ssh` und `ssh-keygen` (z. B. Git für Windows) bereitstellt, wird empfohlen, dass Windows-Clients die Schritte unter [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](/de-de/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/) befolgen.

## Voraussetzungen

* **ssh-keygen** und **ssh** für Linux-, Unix- und OS X-Clients. Dieses Dienstprogramm wird in der Regel im Rahmen Ihres Betriebssystem oder über das Paketverwaltungssystem bereitgestellt.

* Ein zeitgemäßer Webbrowser, der HTML5 unterstützt

ODER

* Plattformübergreifende Tools der Azure-Befehlszeilenschnittstelle.

## Was ist SSH?

SSH ist ein Dienstprogramm zur Anmeldung und Remoteausführung von Befehlen auf einem Remoteserver. Mit Linux-basiertem HDInsight stellt SSH eine sichere Verbindung mit dem Stammknoten des Clusters her und bietet eine Befehlszeile, über die Sie Befehle eingeben können. Die Befehle werden dann direkt auf dem Server ausgeführt.

## Erstellen eines SSH-Schlüssels (optional)

Wenn Sie einen Linux-basierten HDInsight-Cluster erstellen, können Sie sich mithilfe eines Kennworts oder SSH-Schlüssels auf dem Server authentifizieren, wenn Sie SSH verwenden. SSH-Schlüssel werden als sicherer erachtet, da sie auf Zertifikaten basieren. Verwenden Sie die folgenden Informationen, wenn Sie für Ihren Cluster die Verwendung von SSH-Schlüsseln planen.

1. Öffnen Sie eine Terminalsitzung, und verwenden Sie den folgenden Befehl, um zu prüfen, ob vorhandene SSH-Schlüssel verfügbar sind.

		ls -al ~/.ssh

	Achten Sie auf die folgenden Dateien im Verzeichnis. Hierbei handelt es sich um allgemeine Namen für öffentliche SSH-Schlüssel.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Wenn Sie keine vorhandene Datei verwenden möchten oder keine vorhandenen SSH-Schlüssel verfügbar sind, verwenden Sie die folgenden Schritte zum Generieren einer neuen Datei.

		ssh-keygen -t rsa

	Sie werden aufgefordert, die folgenden Informationen bereitzustellen:

	* Speicherort der Datei: die Standardeinstellung ist "~/.ssh/id\_rsa"
	* Eine Passphrase: Sie werden aufgefordert, diese erneut einzugeben
	
		> [AZURE.NOTE] Es wird dringend empfohlen, dass Sie für den Schlüssel eine sichere Passphrase verwenden. Wenn Sie die Passphrase vergessen, besteht jedoch keine Möglichkeit, diese wiederherzustellen.

	Nach Abschluss des Befehls sind zwei neue Dateien verfügbar: der private Schlüssel (z. B. **id\_rsa**) und der öffentliche Schlüssel (z. B. **id\_rsa.pub**.)

## Erstellen eines Linux-basierten HDInsight-Clusters

Wenn Sie einen Linux-basierten HDInsight-Cluster erstellen, müssen Sie den zuvor erstellten **öffentlichen Schlüssel** bereitstellen. Es gibt zwei Möglichkeiten, um einen HDInsight-Cluster über einen Linux-, Unix- oder OS X-Client zu erstellen:

* **Azure-Verwaltungsportal**: verwendet ein webbasiertes Portal zum Erstellen des Clusters

* **Plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli)**: verwendet Befehle der Befehlszeile zum Erstellen des Clusters

Jede dieser Methoden erfordert entweder ein **Kennwort** oder einen **öffentlichen Schlüssel**. Ausführliche Informationen zum Erstellen eines Linux-basierten HDInsight-Clusters finden Sie unter <a href="/de-de/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Bereitstellen Linux-basierter HDInsight-Cluster</a>.

### Azure-Verwaltungsportal

Wenn Sie das Portal verwenden, um einen Linux-basierten HDInsight-Cluster zu erstellen, müssen Sie einen **SSH-Benutzernamen** eingeben und auswählen, ob Sie ein **Kennwort** oder einen **öffentlichen SSH-Schlüssel** eingeben. Wenn Sie einen **öffentlichen SSH-Schlüssel** auswählen, müssen Sie den öffentlichen Schlüssel (in der Datei mit der Erweiterung **pub** enthalten) in das folgenden Formular einfügen.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] Die Schlüsseldatei ist einfach eine Textdatei. Die Inhalte sollten in etwa wie folgt aussehen.
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Dadurch wird eine Anmeldung für den angegebenen Benutzer mit dem bereitgestellten Kennwort oder öffentlichen Schlüssel erstellt.

### Plattformübergreifende Azure-Befehlszeilenschnittstelle

Verwenden Sie die <a href="../xplat-cli/" target="_brad">Plattformübergreifende Azure-Befehlszeilenschnittstelle</a>, um mithilfe des Befehls `azure hdinsight cluster create` einen neuen Cluster zu erstellen.

Weitere Informationen zur Verwendung dieses Befehls finden Sie unter <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in HDInsight</a>

## Verbinden mit einem Linux-basierten HDInsight-Cluster

Verwenden Sie in einer Terminalsitzung den SSH-Befehl, um die Verbindung mit dem Cluster herzustellen, indem Sie den Benutzernamen und die Clusteradresse bereitstellen.

* **SSH-Adresse**: der Clustername, gefolgt von **-ssh.azurehdinsight.net** Beispiel: **mycluster-ssh.azurehdinsight.net**

* **Benutzername**: der SSH-Benutzername, den Sie bei der Erstellung des Clusters angegeben haben

Im folgende Beispiel wird als Benutzer **me** eine Verbindung mit dem Cluster **mycluster** hergestellt.

	ssh me@mycluster-ssh.azurehdinsight.net

Wenn Sie ein **Kennwort** für das Benutzerkonto verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert.

Wenn Sie einen **SSH-Schlüssel** verwendet haben, der mit einer Passphrase geschützt ist, werden Sie zur Eingabe der Passphrase aufgefordert. Andernfalls versucht SSH auf dem Client eine automatische Authentifizierung mithilfe eines lokalen privaten Schlüssels.

> [AZURE.NOTE] Wenn SSH die Authentifizierung nicht automatisch mit dem richtigen **privaten Schlüssel** durchführt, verwenden Sie den Parameter **-i**, und geben Sie den Pfad zum privaten Schlüssel an. Im folgenden Beispiel wird der **private Schlüssel** aus `~/.ssh/id_rsa` geladen.
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

## Hinzufügen zusätzlicher Konten

2. Generieren Sie für das neue Benutzerkonto einen neuen **öffentlichen Schlüssel** und einen **privaten Schlüssel**, wie im Abschnitt [Erstellen eines SSH-Schlüssels](#create) beschrieben.

	> [AZURE.NOTE] Der private Schlüssel sollte entweder auf einem Client generiert werden, den der Benutzer zum Herstellen der Verbindung mit dem Cluster verwendet, oder nach der Erstellung sicher zum Client übertragen werden.

1. Fügen Sie den neuen Benutzer mithilfe des folgenden Befehls aus einer SSH-Sitzung mit dem Cluster hinzu.

		sudo adduser --disabled-password <username> 

	Dadurch wird ein neues Benutzerkonto erstellt, aber die Kennwortauthentifizierung deaktiviert.

2. Erstellen Sie das Verzeichnis und die Dateien zum Speichern des Schlüssels mit den folgenden Befehlen.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Wenn der Nano-Editor geöffnet wird, kopieren Sie die Inhalte des **öffentlichen Schlüssels** für das neue Benutzerkonto und fügen sie dann ein. Verwenden Sie schließlich **STRG+X** zum Speichern der Datei, und beenden Sie den Editor.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Verwenden Sie den folgenden Befehl, um den Ordner ".ssh" und dessen Inhalt dem neuen Benutzerkonto zuzuordnen.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Sie sollten jetzt in der Lage sein, sich beim Server mit dem neuen Benutzerkonto und dem **privaten Schlüssel** zu authentifizieren.

## <a id="tunnel"></a>SSH-Tunneling

SSH kann auch zum Tunneln lokaler Anforderungen, z. B. Webanforderungen, zum HDInsight-Cluster verwendet werden. Die Anforderung wird dann zur angeforderten Ressource weitergeleitet, als ob sie vom Stammknoten des HDInsight-Clusters stammen würde.

Dies ist besonders beim Zugriff auf webbasierte Dienste auf dem HDInsight-Cluster hilfreich, die interne Domänennamen für den Stamm- oder Arbeitsknoten im Cluster verwenden. Einige Abschnitte der Ambari-Webseite verwenden z. B. interne Domänennamen wie **headnode0.mycluster.d1.internal.cloudapp.net**. Diese Namen können nicht außerhalb des Clusters aufgelöst werden, aber über SSH getunnelte Anforderungen haben ihren Ursprung im Cluster und werden ordnungsgemäß aufgelöst.

Verwenden Sie die folgenden Schritte, um einen SSH-Tunnel zu erstellen und Ihren Browser zu konfigurieren, damit Sie mit ihm die Verbindung zum Cluster herstellen können.

1. Mithilfe des folgenden Befehls können Sie einen SSH-Tunnel zum Stammknoten des Clusters herstellen.

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.net

	Dadurch wird eine Verbindung erstellt, über die der Datenverkehr über SSH an den lokalen Port 9876 des Clusters weitergeleitet wird. Die Optionen sind:

	* **D 8080**: der lokale Port, der den Datenverkehr durch den Tunnel weiterleitet

	* **C**: alle Daten werden komprimiert, da der Webdatenverkehr hauptsächlich aus Text besteht

	* **2**: SSH zwingen, nur Protokollversion 2 zu verwenden

	* **Q**: stiller Modus

	* **T**: Pseudo-TTY-Zuordnung deaktivieren, da lediglich ein Port weitergeleitet wird
	
	* **n**: verhindert den Lesevorgang für STDIN, da lediglich ein Port weitergeleitet wird

	* **N**: keine Remotebefehle ausführen, da lediglich ein Port weitergeleitet wird

	* **f**: im Hintergrund ausführen

	Wenn Sie den Cluster mit einem **SSH-Schlüssel** konfiguriert haben, müssen Sie unter Umständen den Parameter `-i` verwenden und den Pfad zum privaten SSH-Schlüssel angeben.

	Nach Abschluss des Befehls wird der an den lokalen Port 9876 des lokalen Computers gesendete Datenverkehr über SSL an den Stammknoten des Clusters weitergeleitet, der dann seinen Ursprung darstellt.

2. Konfigurieren Sie das Clientprogramm, z. B. Firefox, für die Verwendung von **localhost:9876** als **SOCKS v5**-Proxy. Die Firefox-Einstellungen sollten wie folgt aussehen.

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE] Durch die Auswahl von **Remote-DNS** werden DNS-Anforderungen mithilfe des HDInsight-Clusters aufgelöst. Ist diese Option deaktiviert, wird DNS lokal aufgelöst.

	Sie können überprüfen, ob der Datenverkehr durch den Tunnel weitergeleitet wird, indem Sie eine Website wie <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> mit aktivierten Proxyeinstellungen und deaktivierten Firefox-Proxyeinstellungen besuchen. Bei entsprechender Aktivierung wird die IP-Adresse für einen Computer im Microsoft Azure-Rechenzentrum angezeigt.

### Browsererweiterungen

Obwohl Sie den Browser für die Verwendung des Tunnels konfigurieren, möchten Sie in der Regel jedoch nicht den gesamten Datenverkehr über den Tunnel weiterleiten. Browsererweiterungen wie <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>  unterstützen den Musterabgleich für URL-Anforderungen (nur FoxyProxy Standard oder Plus), sodass nur Anforderungen für bestimmte URLs durch den Tunnel gesendet werden.

Wenn Sie **FoxyProxy Standard** installiert haben, konfigurieren Sie es folgendermaßen, um nur den Datenverkehr für HDInsight über den Tunnel weiterzuleiten.

1. Öffnen Sie die FoxyProxy-Erweiterung in Ihrem Browser. Wählen Sie z. B. in Firefox das FoxyProxy-Symbol neben dem Adressfeld aus.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. Wählen Sie **Neuer Proxy** aus, und geben Sie auf der Registerkarte **Allgemein** einen Proxynamen für **HDInsight** ein.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. Wählen Sie die Registerkarte **Proxy-Details** aus, und geben Sie die entsprechenden Werte in die Felder ein.

	* **Host- oder IP-Adresse**: Localhost, da wir auf dem lokalen Computer einen SSH-Tunnel verwenden.

	* **Port**: Der Port, den Sie für den SSH-Tunnel verwendet haben.

	* **SOCKS-Proxy**: Aktivieren Sie diese Option, damit der Browser den Tunnel als Proxy verwenden kann.

	* **SOCKS v5**: Aktivieren Sie diese Option, um die erforderliche Version für den Proxy festzulegen.

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. Wählen Sie die Registerkarte **URL-Muster** und anschließend **Neues Muster** aus. Gehen Sie wie folgt vor, um das Muster zu definieren, und klicken Sie dann auf **OK**.

	* **Name des Musters**: **Stammknoten** - Dies ist lediglich ein Anzeigename für das Muster.

	* **URL-Muster**: **\*stammknoten\*** - Definiert ein Muster, das mit jeder URL übereinstimmt, die das Wort **Stammknoten** enthält.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. Wählen Sie **OK** aus, um den Proxy hinzuzufügen und die **Proxyeinstellungen** zu schließen.

5. Am oberen Rand des FoxyProxy-Dialogfelds ändern Sie **Modus auswählen** in **Verwende Proxies entsprechend ihrer konfigurierten Muster und Prioritäten**, und klicken Sie dann auf **Schließen**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

Nachdem Sie diese Schritte ausgeführt haben, werden ausschließlich Anforderungen für URLs über den SSL-Tunnel weitergeleitet, die die Zeichenfolge **Stammknoten** enthalten.

## Nächste Schritte

Nachdem Sie jetzt wissen, wie die Authentifizierung mithilfe eines SSH-Schlüssels erfolgt, erfahren Sie, wie Sie MapReduce mit Hadoop für HDInsight verwenden.

* [Verwenden von Hive mit HDInsight](../hdinsight-use-hive/)

* [Verwenden von Pig mit HDInsight](../hdinsight-use-pig/)

* [Verwenden von MapReduce-Aufträgen mit HDInsight](../hdinsight-use-mapreduce/)
 
<!--HONumber=45--> 
