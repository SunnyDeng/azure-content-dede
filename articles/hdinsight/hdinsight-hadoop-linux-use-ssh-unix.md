<properties
   pageTitle="Verwenden von SSH-Schlüsseln mit Linux-basiertem Hadoop unter Linux, Unix oder OS X | Microsoft Azure"
   description="Auf das Linux-basierte HDInsight können Sie über Secure Shell (SSH) zugreifen. Dieses Dokument enthält Informationen zur Verwendung von SSH mit HDInsight auf Linux-, Unix- oder OS X-Clients."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/28/2016"
   ms.author="larryfr"/>

#Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) ermöglicht die Remoteausführung von Vorgängen in Linux-basierten HDInsight-Clustern über eine Befehlszeilenschnittstelle. Dieses Dokument enthält Informationen zur Verwendung von SSH mit HDInsight auf Linux-, Unix- oder OS X-Clients.

> [AZURE.NOTE] Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie einen Linux-, Unix- oder OS X-Client verwenden. Obwohl diese Schritte auf einem Windows-Client ausgeführt werden können, wenn Sie ein Paket installiert haben, das `ssh` und `ssh-keygen` (z. B. Git für Windows) bereitstellt, wird empfohlen, dass Windows-Clients die Schritte unter [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md) befolgen.

##Voraussetzungen

* **ssh-keygen** und **ssh** für Linux-, Unix- und OS X-Clients. Diese Hilfsprogramme werden in der Regel vom Betriebssystem oder über das Paketverwaltungssystem bereitgestellt.

* Ein zeitgemäßer Webbrowser, der HTML5 unterstützt.

OR

* [Azure-CLI für Mac, Linux und Windows](../xplat-cli-install.md).

##Was ist SSH?

SSH ist ein Dienstprogramm zur Anmeldung und Remoteausführung von Befehlen auf einem Remoteserver. Bei Linux-basiertem HDInsight stellt SSH eine verschlüsselte Verbindung mit dem Hauptknoten des Clusters her und bietet eine Befehlszeile, über die Sie Befehle eingeben können. Die Befehle werden dann direkt auf dem Server ausgeführt.

###SSH-Benutzername

Ein SSH-Benutzername ist der Name, den Sie für die Authentifizierung beim HDInsight-Cluster verwenden. Wenn Sie während der Erstellung des Clusters einen SSH-Benutzernamen angeben, wird dieser Benutzer in allen Knoten im Cluster erstellt. Nach dem Erstellen des Clusters können Sie diesen Benutzernamen zum Herstellen einer Verbindung mit den Hauptknoten des HDInsight-Clusters verwenden. Ausgehend von den Hauptknoten können Sie dann eine Verbindung mit den einzelnen Workerknoten herstellen.

###SSH-Kennwort oder öffentlicher Schlüssel

Ein SSH-Benutzer kann entweder ein Kennwort oder einen öffentlichen Schlüssel für die Authentifizierung verwenden. Ein Kennwort ist nur eine von Ihnen erstellte Textzeichenfolge, während ein öffentlicher Schlüssel Teil eines kryptografischen Schlüsselpaars ist, das generiert wurde, um Sie eindeutig zu identifizieren.

Ein Schlüssel ist sicherer als ein Kennwort, es sind jedoch zusätzliche Schritte erforderlich, um den Schlüssel zu generieren, und Sie müssen die Dateien mit dem Schlüssel an einem sicheren Ort verwalten. Wenn jemand Zugriff auf die Schlüsseldateien erhält, hat er Zugriff auf Ihr Konto. Wenn die Schlüsseldateien verloren gehen, können Sie sich nicht mehr bei Ihrem Konto anmelden.

Ein Schlüsselpaar besteht aus einem öffentlichen Schlüssel (der an den HDInsight-Server gesendet wird) und einem privaten Schlüssel (der auf dem Clientcomputer gespeichert ist). Beim Herstellen einer Verbindung mit dem HDInsight-Server über SSH verwendet der SSH-Client den privaten Schlüssel auf Ihrem Computer zur Authentifizierung beim Server.

##Erstellen eines SSH-Schlüssels

Verwenden Sie die folgenden Informationen, wenn Sie für Ihren Cluster die Verwendung von SSH-Schlüsseln planen. Wenn Sie ein Kennwort verwenden möchten, können Sie diesen Abschnitt überspringen.

1. Öffnen Sie eine Terminalsitzung, und verwenden Sie den folgenden Befehl, um zu prüfen, ob SSH-Schlüssel vorhanden sind:

		ls -al ~/.ssh

	Achten Sie auf die folgenden Dateien in der Verzeichnisauflistung. Hierbei handelt es sich um allgemeine Namen für öffentliche SSH-Schlüssel.

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. Wenn Sie keine vorhandene Datei verwenden möchten oder keine SSH-Schlüssel vorhanden sind, führen Sie die folgenden Schritte zum Generieren einer neuen Datei aus:

		ssh-keygen -t rsa

	Sie werden aufgefordert, die folgenden Informationen bereitzustellen:

	* Speicherort der Datei: Die Standardeinstellung ist "~/.ssh/id\\_rsa".
	* Eine Passphrase: Sie werden aufgefordert, diese erneut einzugeben.

		> [AZURE.NOTE] Es wird dringend empfohlen, dass Sie für den Schlüssel eine sichere Passphrase verwenden. Wenn Sie die Passphrase vergessen, besteht jedoch keine Möglichkeit, diese wiederherzustellen.

	Nach Abschluss des Befehls sind zwei neue Dateien verfügbar: der private Schlüssel (z. B. **id\\_rsa**) und der öffentliche Schlüssel (z. B. **id\\_rsa.pub**).

##Erstellen eines Linux-basierten HDInsight-Clusters

Wenn Sie einen Linux-basierten HDInsight-Cluster erstellen, müssen Sie den zuvor erstellten öffentlichen Schlüssel bereitstellen. Es gibt zwei Möglichkeiten, um einen HDInsight-Cluster über einen Linux-, Unix- oder OS X-Client zu erstellen:

* **Azure-Portal**: – Zum Erstellen des Clusters wird ein webbasiertes Portal verwendet.

* **Azure-CLI für Mac, Linux und Windows **: Zum Erstellen des Clusters werden Befehle über die Befehlszeile eingegeben.

Jede dieser Methoden erfordert entweder ein Kennwort oder einen öffentlichen Schlüssel. Vollständige Informationen zum Erstellen eines Linux-basierten HDInsight-Clusters finden Sie unter [Bereitstellen von Linux-basierten HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md).

###Azure-Portal

Wenn Sie das [Azure Portal][preview-portal] verwenden, um einen Linux-basierten HDInsight-Cluster zu erstellen, müssen Sie einen **SSH-Benutzernamen** eingeben und auswählen, ob Sie ein **Kennwort** oder einen **öffentlichen SSH-Schlüssel** eingeben.

Wenn Sie **ÖFFENTLICHER SSH-SCHLÜSSEL** auswählen, können Sie entweder den öffentlichen Schlüssel (aus der Datei mit der Erweiterung **.pub**) in das Feld __Öffentlicher SSH-Schlüssel__ einfügen oder auf __Datei auswählen__ klicken, um die Datei mit dem öffentlichen Schlüssel zu suchen und auszuwählen.

![Abbildung eines Formulars, das den öffentlichen Schlüssel anfordert](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] Die Schlüsseldatei ist einfach eine Textdatei. Die Inhalte sollten in etwa wie folgt aussehen:
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Dadurch wird eine Anmeldung für den angegebenen Benutzer mit dem bereitgestellten Kennwort oder öffentlichen Schlüssel erstellt.

###Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Microsoft Azure

Sie können über die [Azure-CLI für Mac, Linux und Windows ](../xplat-cli-install.md) einen neuen Cluster mithilfe des Befehls `azure hdinsight cluster create` erstellen.

Weitere Informationen zur Verwendung dieses Befehls finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

##Verbinden mit einem Linux-basierten HDInsight-Cluster

Geben Sie in einer Terminalsitzung den SSH-Befehl ein, um die Verbindung mit dem Hauptknoten des Clusters herzustellen, indem Sie Adresse und den Benutzernamen angeben:

* **SSH-Adresse**: Der Clustername, gefolgt von **-ssh.azurehdinsight.net**. Beispiel: **mycluster-ssh.azurehdinsight.net**.

* **Benutzername**: Der SSH-Benutzername, den Sie bei der Erstellung des Clusters angegeben haben.

Im folgende Beispiel stellt der Benutzer **me** eine Verbindung mit dem Cluster **mycluster** her:

	ssh me@mycluster-ssh.azurehdinsight.net

Wenn Sie ein Kennwort für das Benutzerkonto verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert.

Wenn Sie einen SSH-Schlüssel verwendet haben, der mit einer Passphrase geschützt ist, werden Sie aufgefordert, die Passphrase einzugeben. Andernfalls versucht SSH automatisch, sich mithilfe eines lokalen privaten Schlüssels auf dem Client zu authentifizieren.

> [AZURE.NOTE] Wenn sich SSH nicht automatisch mit dem richtigen privaten Schlüssel authentifiziert, verwenden Sie den Parameter **-i** und geben den Pfad zum privaten Schlüssel an. Im folgenden Beispiel wird der private Schlüssel aus `~/.ssh/id_rsa` geladen:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Wenn kein Port angegeben wird, verwendet SSH standardmäßig Port 22. Über diesen Port wird eine Verbindung mit „Hauptknoten 0“ im HDInsight-Cluster hergestellt. Wenn Sie Port 23 verwenden, wird eine Verbindung mit „head node 1“ hergestellt. Weitere Informationen zu Hauptknoten finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight](hdinsight-high-availability-linux.md).

###Herstellen einer Verbindung mit den Workerknoten

Auf die Workerknoten kann von außerhalb des Azure-Datencenters nicht direkt zugegriffen werden. Doch auf dem Hauptknoten des Clusters ist der Zugriff darauf über SSH möglich.

Wenn Sie einen SSH-Schlüssel verwenden, um Ihr Benutzerkonto zu authentifizieren, müssen Sie auf dem Client die folgenden Schritte ausführen:

1. Öffnen Sie `~/.ssh/config` in einem Text-Editor. Wenn diese Datei nicht vorhanden ist, können Sie sie durch Eingabe von `touch ~/.ssh/config` im Terminal erstellen.

2. Fügen Sie der Datei folgenden Code hinzu. Ersetzen Sie *CLUSTERNAME* durch den Namen Ihres HDInsight-Clusters.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Dadurch wird die SSH-Agent-Weiterleitung für Ihren HDInsight-Cluster konfiguriert.

3. Testen Sie die SSH-Agent-Weiterleitung über die Eingabe des folgenden Befehls in das Terminal:

        echo "$SSH_AUTH_SOCK"

    Die Ausgabe dieses Befehls sollte etwa so aussehen:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Wenn nichts zurückgegeben wird, bedeutet dies, dass **ssh-agent** nicht ausgeführt wird. Suchen Sie in der Betriebssystemdokumentation nach detaillierten Informationen zur Installation und Konfiguration von **ssh-agent**, oder lesen Sie den Artikel [Verwenden von ssh-agent mit ssh](http://mah.everybody.org/docs/ssh) (in englischer Sprache).

4. Nachdem Sie sichergestellt haben, dass **ssh-Agent** ausgeführt wird, verwenden Sie folgenden Befehl, um Ihren privaten SSH-Schlüssel dem Agent hinzuzufügen:

        ssh-add ~/.ssh/id_rsa

    Wenn Ihr privater Schlüssel in einer anderen Datei gespeichert ist, ersetzen Sie `~/.ssh/id_rsa` durch den Pfad zur Datei.

Führen Sie die folgenden Schritte zum Verbinden mit den Workerknoten für Ihren Cluster aus.

> [AZURE.IMPORTANT] Wenn Sie einen SSH-Schlüssel verwenden, um Ihr Konto zu authentifizieren, müssen Sie die vorherigen Schritte ausführen, um zu bestätigen, dass die Agent-Weiterleitung funktioniert.

1. Stellen Sie, wie zuvor beschrieben, über SSH eine Verbindung mit dem HDInsight-Cluster her.

2. Sobald Sie verbunden sind, führen Sie den folgenden Befehl zum Abrufen einer Liste der Knoten im Cluster aus. Ersetzen Sie *ADMINPASSWORD* durch das Kennwort Ihres Clusteradministratorkontos. Ersetzen Sie *CLUSTERNAME* durch den Namen Ihres Clusters.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Dadurch werden Informationen im JSON-Format zu den Knoten im Cluster zurückgegeben, einschließlich `host_name`, die den vollqualifizierten Domänennamen (FQDN) für jeden Knoten enthalten. Es folgt ein Beispiel eines Eintrags vom Typ `host_name`, der vom Befehl **curl** zurückgegeben wird:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Sobald Sie eine Liste der Workerknoten haben, mit denen Sie eine Verbindung herstellen möchten, geben Sie in der SSH-Sitzung mit dem Server den folgenden Befehl ein, um eine Verbindung mit einem Workerknoten herzustellen:

        ssh USERNAME@FQDN

    Ersetzen Sie *USERNAME* durch Ihren SSH-Benutzernamen und *FQDN* durch den vollqualifizierten Domänennamen des Workerknotens. Beispiel: `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Wenn Sie ein Kennwort zur Authentifizierung Ihrer SSH-Sitzung verwenden, werden Sie aufgefordert, das Kennwort erneut einzugeben. Wenn Sie einen SSH-Schlüssel verwenden, sollte die Verbindung ohne Aufforderungen fertig gestellt werden.

4. Sobald die Sitzung eingerichtet ist, ändert sich die Terminaleingabeaufforderung von `username@hn0-clustername` in `username@wk0-clustername`, um anzugeben, dass Sie mit dem Workerknoten verbunden sind. Alle Befehle, die Sie ab diesem Punkt ausführen, werden auf dem Workerknoten ausgeführt.

4. Wenn Sie mit dem Ausführen von Aktionen auf dem Workerknoten fertig sind, geben Sie den Befehl `exit` zum Schließen der Sitzung mit dem Workerknoten ein. Sie kehren zur Eingabeaufforderung `username@hn0-clustername` zurück.

##Hinzufügen weiterer Konten

1. Generieren Sie für das neue Benutzerkonto einen neuen öffentlichen Schlüssel und einen privaten Schlüssel, wie im Abschnitt [Erstellen eines SSH-Schlüssels](#create-an-ssh-key-optional) beschrieben.

	> [AZURE.NOTE] Der private Schlüssel sollte entweder auf einem Client generiert werden, den der Benutzer zum Herstellen der Verbindung mit dem Cluster verwendet, oder nach der Erstellung sicher zu einem solchen Client übertragen werden.

1. Fügen Sie in einer SSH-Sitzung mit dem Cluster den neuen Benutzer mithilfe des folgenden Befehls hinzu:

		sudo adduser --disabled-password <username>

	Dadurch wird ein neues Benutzerkonto erstellt, aber die Kennwortauthentifizierung deaktiviert.

2. Erstellen Sie das Verzeichnis und die Dateien zum Speichern des Schlüssels mit den folgenden Befehlen:

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. Wenn der Nano-Editor geöffnet wird, kopieren Sie die Inhalte des öffentlichen Schlüssels für das neue Benutzerkonto und fügen sie dann ein. Drücken Sie schließlich **STRG+X** zum Speichern der Datei, und beenden Sie den Editor.

	![Abbildung von Nano-Editor mit Beispielschlüssel](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Verwenden Sie den folgenden Befehl, um den Ordner ".ssh" und dessen Inhalt dem neuen Benutzerkonto zuzuordnen:

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Sie sollten jetzt in der Lage sein, sich beim Server mit dem neuen Benutzerkonto und dem privaten Schlüssel zu authentifizieren.

##<a id="tunnel"></a>SSH-Tunnel

SSH kann auch zum Tunneln lokaler Anforderungen wie etwa Webanforderungen zum HDInsight-Cluster verwendet werden. Die Anforderung wird dann zur angeforderten Ressource weitergeleitet, als ob sie vom Stammknoten des HDInsight-Clusters stammen würde.

> [AZURE.IMPORTANT] Ein SSH-Tunnel ist für manche Hadoop-Dienste eine Voraussetzung für den Zugriff auf die Webbenutzeroberfläche. Auf die Benutzeroberfläche des Auftragsverlaufs und des Ressourcen-Managers kann beispielsweise nur über einen SSH-Tunnel zugegriffen werden.

Weitere Informationen zum Erstellen und Verwenden eines SSH-Tunnels finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

##Nächste Schritte

Nachdem Sie jetzt wissen, wie die Authentifizierung mithilfe eines SSH-Schlüssels erfolgt, erfahren Sie, wie Sie MapReduce mit Hadoop für HDInsight verwenden.

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)

* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->