<properties
   pageTitle="Verwenden von SSH-Schlüsseln mit Hadoop für Linux-basiertes HDInsight unter Windows | Azure"
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

## Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows (Vorschau)

Linux-basierte HDInsight-Cluster bieten die Möglichkeit, den SSH-Zugriff mithilfe eines Kennworts oder eines SSH-Schlüssels zu schützen. Dieses Dokument enthält Informationen zum Herstellen einer Verbindung von Windows-Clients, die den PuTTY SSH-Client verwenden, zu HDInsight.

> [AZURE.NOTE] Für die Schritte in diesem Artikel wird davon ausgegangen, dass Sie einen Windows-Client verwenden. Wenn Sie einen Linux-, Unix- oder OS X-Client verwenden, finden Sie entsprechende Informationen unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

## Voraussetzungen

* **PuTTY** und **PuTTYGen** für Windows-Clients. Diese Dienstprogramme sind erhältlich unter <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

* Ein zeitgemäßer Webbrowser, der HTML5 unterstützt

ODER

* Azure PowerShell

ODER

* Plattformübergreifende Tools der Azure-Befehlszeilenschnittstelle.

## Was ist SSH?

SSH ist ein Dienstprogramm zur Anmeldung und Remoteausführung von Befehlen auf einem Remoteserver. Mit Linux-basiertem HDInsight stellt SSH eine sichere Verbindung mit dem Stammknoten des Clusters her und bietet eine Befehlszeile, über die Sie Befehle eingeben können. Die Befehle werden dann direkt auf dem Server ausgeführt.

## Erstellen eines SSH-Schlüssels (optional)

Wenn Sie einen Linux-basierten HDInsight-Cluster erstellen, können Sie sich mithilfe eines Kennworts oder SSH-Schlüssels auf dem Server authentifizieren, wenn Sie SSH verwenden. SSH-Schlüssel werden als sicherer erachtet, da sie auf Zertifikaten basieren. Verwenden Sie die folgenden Informationen, wenn Sie für Ihren Cluster die Verwendung von SSH-Schlüsseln planen.

1. Öffnen Sie **PuTTYGen**.

2. Wählen Sie für **Type of key to generate** die Option **SSH-2 RSA**, und klicken Sie dann auf **Generate**.

	![PuTTYGen interface](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Bewegen Sie die Maus im Bereich unterhalb der Statusanzeige, bis die Anzeige gefüllt ist. Durch das Bewegen der Maus werden zufällige Daten generiert, die zum Generieren des Schlüssels verwendet werden.

	![moving the mouse around](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	Nachdem der Schlüssel generiert wurde, wird der öffentliche Schlüssel angezeigt.

4. Zur Erhöhung der Sicherheit können Sie eine Passphrase in das Feld **Key passphrase** und dann denselben Wert in das Feld **Confirm passphrase** eingeben.

	![passphrase](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)
	
	> [AZURE.NOTE] Es wird dringend empfohlen, dass Sie für den Schlüssel eine sichere Passphrase verwenden. Wenn Sie die Passphrase vergessen, besteht jedoch keine Möglichkeit, diese wiederherzustellen.

5. Klicken Sie auf **Save private key**, um den Schlüssel in einer **PPK**-Datei zu speichern. Dieser Schlüssel wird zum Authentifizieren für die Linux-basierten HDInsight-Cluster verwendet.

	> [AZURE.NOTE] Sie sollten diesen Schlüssel an einem sicheren Ort verwahren, da dieser für den Zugriff auf Ihre Linux-basierten HDInsight-Cluster verwendet werden kann.

6. Klicken Sie auf **Save public key**, um den Schlüssel in einer **TXT**-Datei zu speichern. Dadurch können Sie den öffentlichen Schlüssel zukünftig wiederverwenden, wenn Sie zusätzliche Linux-basierte HDInsight-Cluster erstellen.

	> [AZURE.NOTE] Der öffentliche Schlüssel wird auch am oberen Rand von **PuTTYGen** angezeigt. Sie können mit der rechten Maustaste in dieses Felds klicken, den Wert kopieren und ihn dann in ein Formular einfügen, z. B. in den HDInsight-Assistenten des Azure-Verwaltungsportals.

## Erstellen eines Linux-basierten HDInsight-Clusters

Wenn Sie einen Linux-basierten HDInsight-Cluster erstellen, müssen Sie den zuvor erstellten **öffentlichen Schlüssel** bereitstellen. Es gibt Möglichkeiten, um über Windows-Clients einen Linux-basierten HDInsight-Cluster zu erstellen:

* **Azure-Verwaltungsportal**: verwendet ein webbasiertes Portal zum Erstellen des Clusters.

* **Plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli)**: verwendet Befehle der Befehlszeile zum Erstellen des Clusters.

Für jede dieser Methoden ist der **öffentliche Schlüssel** erforderlich. Vollständige Informationen zum Erstellen eines Linux-basierten HDInsight-Clusters finden Sie unter <a href="./hdinsight-hadoop-provision-linux-clusters/" target="_blank">Bereitstellen von Linux-basierten HDInsight-Clustern</a>.

### Azure-Verwaltungsportal

Wenn Sie das Portal zum Erstellen eines Linux-basierten HDInsight-Clusters verwenden, müssen Sie einen Benutzernamen und ein Kennwort oder einen öffentlichen Schlüssel in der folgende Formular eingeben.

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Dadurch wird für den angegebenen Benutzer ein Anmeldename erstellt und die Kennwort- oder SSH-Schlüsselauthentifizierung ermöglicht.

### Plattformübergreifende Azure-Befehlszeilenschnittstelle

Können Sie die <a href="../xplat-cli/" target="_brad">Plattformübergreifende Azure-Befehlszeilenschnittstelle</a>, zum Erstellen eines neuen Clusters mithilfe des `Azure Hdinsight-Cluster erstellen`-Befehls verwenden.

Weitere Informationen über die Verwendung dieses Befehls finden Sie unter <a href="../hdinsight-hadoop-provision-linux-clusters/" target="_blank">Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight</a>

## <a id="connect"></a>Verbinden mit einem Linux-basierten HDInsight-Cluster

1. Öffnen Sie PuTTY.

	![putty interface](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Sofern Sie beim Erstellen des Benutzerkontos einen **SSH-Schlüssel** bereitgestellt haben, müssen Sie die folgenden Schritte ausführen, um den **privaten Schlüssel** auszuwählen, der zum Authentifizieren für den Cluster verwendet wird.

	Erweitern Sie in **Category** die Option **Connection**, **SSH**, und wählen Sie dann **Auth** aus. Klicken Sie abschließend auf **Browse**, und wählen Sie die **PPK**-Datei aus, die Ihren **privaten Schlüssel** enthält.

	![putty interface, select private key](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. Wählen Sie in **Category** die Option **Session** aus. Geben Sie auf dem Bildschirm **Grundoptionen für PuTTY-Sitzung** die SSH-Adresse Ihres HDInsight-Servers in das Feld **Hostname (oder IP-Adresse)** ein. Die SSH-Adresse ist Ihr Clustername, gefolgt von **-ssh.azurehdinsight.net**. Beispiel: **mycluster-ssh.azurehdinsight.net**.

	![putty interface with ssh address entered](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Geben Sie zum Speichern der Verbindungsinformationen für die zukünftige Verwendung einen Namen für diese Verbindung unter **Gespeicherte Sitzungen** ein, und klicken Sie dann auf **Speichern**. Die Verbindung wird zur Liste der gespeicherten Sitzungen hinzugefügt.

5. Klicken Sie auf **Öffnen**, um die Verbindung zum Cluster herzustellen.

	> [AZURE.NOTE] Wenn Sie das erste Mal eine Verbindung mit dem Cluster hergestellt haben, wird eine Sicherheitswarnung angezeigt. Das ist normal. Wählen Sie **Ja** aus, um den RSA2-Schlüssel des Servers zum Fortfahren zwischenzuspeichern.

6. Geben Sie nach der entsprechenden Aufforderung den Benutzer ein, den Sie beim Erstellen des Clusters eingegeben haben. Wenn Sie für den Benutzer ein **Kennwort** angegeben haben, werden Sie auch zur Eingabe dieses Kennworts aufgefordert.

## Hinzufügen zusätzlicher Konten

2. Generieren Sie einen neuen **öffentlichen Schlüssel** und **privatem Schlüssel** für das neue Benutzerkonto, wie zuvor beschrieben.

1. Fügen Sie den neuen Benutzer mithilfe des folgenden Befehls aus einer SSH-Sitzung mit dem Cluster hinzu.

		sudo adduser --disabled-password <username> 

	Dadurch wird ein neues Benutzerkonto erstellt, aber die Kennwortauthentifizierung deaktiviert.

2. Erstellen Sie das Verzeichnis und die Dateien zum Speichern des Schlüssels mit den folgenden Befehlen.

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Wenn der Nano-Editor geöffnet wird, kopieren Sie die Inhalte des **öffentlichen Schlüssels** für das neue Benutzerkonto und fügen sie dann ein. Verwenden Sie schließlich **STRG+X** zum Speichern der Datei, und beenden Sie den Editor.

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

4. Verwenden Sie den folgenden Befehl, um den Ordner ".ssh" und dessen Inhalt dem neuen Benutzerkonto zuzuordnen.

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Sie sollten jetzt in der Lage sein, sich beim Server mit dem neuen Benutzerkonto und dem **privaten Schlüssel** zu authentifizieren.

## <a id="tunnel"></a>SSH-Tunneling

SSH kann auch zum Tunneln lokaler Anforderungen, z. B. Webanforderungen, zum HDInsight-Cluster verwendet werden. Die Anforderung wird dann zur angeforderten Ressource weitergeleitet, als ob sie vom Stammknoten des HDInsight-Clusters stammen würde.

Dies ist besonders beim Zugriff auf webbasierte Dienste auf dem HDInsight-Cluster hilfreich, die interne Domänennamen für den Stamm- oder Arbeitsknoten im Cluster verwenden. Einige Abschnitte der Ambari-Webseite verwenden z. B. interne Domänennamen wie **headnode0.mycluster.d1.internal.cloudapp.net**. Diese Namen können nicht außerhalb des Clusters aufgelöst werden, aber über SSH getunnelte Anforderungen haben ihren Ursprung im Cluster und werden ordnungsgemäß aufgelöst.

Verwenden Sie die folgenden Schritte, um einen SSH-Tunnel zu erstellen und Ihren Browser zu konfigurieren, damit Sie mit ihm die Verbindung zum Cluster herstellen können.

1. Öffnen Sie PuTTY, und geben Sie die Verbindungsinformationen ein, wie im Abschnitt [Verbinden](#connect) beschrieben.

2. Erweitern Sie auf der linken Seite des Dialogfelds im Abschnitt **Category** die Option **Connection**, **SSH**, und wählen Sie anschließend **Tunnels** aus.

2. Geben Sie die folgenden Informationen für **Options controlling SSH port forwarding** an.

	* **Source port**: der Port auf dem Client, den Sie weiterleiten möchten Beispiel: **9876**

	* **Destination**: die SSH-Adresse für den Linux-basierten HDInsight-Cluster Beispiel: **mycluster-ssh.azurehdinsight.net**

	* **Dynamic**: ermöglicht das dynamische SOCKS-Proxyrouting

	![image of tunneling options](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

3. Wählen Sie abschließend **Add** aus, um die Einstellungen hinzuzufügen. Wählen Sie dann die Option **Open** aus, um eine SSH-Verbindung zu öffnen.

4. Melden Sie sich bei entsprechender Aufforderung am Server an. Dadurch wird eine SSH-Sitzung eingerichtet und der Tunnel aktiviert.

2. Konfigurieren Sie das Clientprogramm, z. B. Firefox, für die Verwendung von **localhost:9876** als **SOCKS v5**-Proxy. Die Firefox-Einstellungen sollten wie folgt aussehen.

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE] Durch die Auswahl von **Remote-DNS** werden DNS-Anforderungen mithilfe des HDInsight-Clusters aufgelöst. Ist diese Option deaktiviert, wird DNS lokal aufgelöst.

	Sie können überprüfen, ob Datenverkehr durch den Tunnel weitergeleitet wird, indem Sie eine Website wie z. B. <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> mit aktivierten Proxyeinstellungen und deaktivierten in Firefox aufrufen. Bei entsprechender Aktivierung wird die IP-Adresse für einen Computer im Microsoft Azure-Rechenzentrum angezeigt.

### Browsererweiterungen

Obwohl Sie den Browser für die Verwendung des Tunnels konfigurieren, möchten Sie in der Regel jedoch nicht den gesamten Datenverkehr über den Tunnel weiterleiten. Browsererweiterungen wie <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a> unterstützen den Musterabgleich für URL-Anforderungen (nur FoxyProxy Standard oder Plus), sodass nur Anforderungen für bestimmte URLs durch den Tunnel gesendet werden.

Wenn Sie **FoxyProxy Standard** installiert haben, konfigurieren Sie es folgendermaßen, um nur den Datenverkehr für HDInsight über den Tunnel weiterzuleiten.

1. Öffnen Sie die FoxyProxy-Erweiterung in Ihrem Browser. Wählen Sie z. B. in Firefox das FoxyProxy-Symbol neben dem Adressfeld aus.

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. Wählen Sie **Neuer Proxy** aus, und geben Sie auf der Registerkarte **Allgemein** einen Proxynamen für **HDInsight** ein.

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. Wählen Sie die Registerkarte **Proxy-Details** aus, und geben Sie die entsprechenden Werte in die Felder ein.

	* **Host- oder IP-Adresse**: Localhost, da wir auf dem lokalen Computer einen SSH-Tunnel verwenden.

	* **Port**: Der Port, den Sie für den SSH-Tunnel verwendet haben.

	* **SOCKS-Proxy**: Aktivieren Sie diese Option, damit der Browser den Tunnel als Proxy verwenden kann.

	* **SOCKS v5**: Aktivieren Sie diese Option, um die erforderliche Version für den Proxy festzulegen.

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. Wählen Sie die Registerkarte **URL-Muster** und anschließend **Neues Muster** aus. Gehen Sie wie folgt vor, um das Muster zu definieren, und klicken Sie dann auf **OK**.

	* **Name des Musters**: **Stammknoten** - Dies ist lediglich ein Anzeigename für das Muster.

	* **URL-Muster**: <strong>*stammknoten*</strong> - Definiert ein Muster, das mit jeder URL übereinstimmt, die das Wort **Stammknoten** enthält.

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. Wählen Sie **OK** aus, um den Proxy hinzuzufügen und die **Proxyeinstellungen** zu schließen.

5. Am oberen Rand des FoxyProxy-Dialogfelds ändern Sie **Modus auswählen** in **Verwende Proxies entsprechend ihrer konfigurierten Muster und Prioritäten**, und klicken Sie dann auf **Schließen**.

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

Nachdem Sie diese Schritte ausgeführt haben, werden ausschließlich Anforderungen für URLs über den SSL-Tunnel weitergeleitet, die die Zeichenfolge **Stammknoten** enthalten.

## Nächste Schritte

Nachdem Sie jetzt wissen, wie die Authentifizierung mithilfe eines SSH-Schlüssels erfolgt, erfahren Sie, wie Sie MapReduce mit Hadoop für HDInsight verwenden.

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)

* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)
 
<!--HONumber=47-->
