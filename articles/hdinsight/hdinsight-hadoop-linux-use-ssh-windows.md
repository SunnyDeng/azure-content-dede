<properties
   pageTitle="Verwenden von SSH-Schlüsseln mit Hadoop in Linux-basierten Clustern unter Windows | Microsoft Azure"
   description="Erfahren Sie, wie Sie SSH-Schlüssel für die Authentifizierung bei Linux-basierten HDInsight-Clustern erstellen und verwenden. Verbinden Sie Cluster über Windows-basierte Clients mithilfe des PuTTY-SSH-Clients."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/22/2016"
   ms.author="larryfr"/>

#Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Mit [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) können Sie Vorgänge in Linux-basierten HDInsight-Clustern über eine Befehlszeilenschnittstelle remote ausführen. Dieses Dokument enthält Informationen zum Herstellen einer Verbindung von Windows-Clients mit HDInsight über den PuTTY SSH-Client.

> [AZURE.NOTE] Für die Schritte in diesem Artikel wird davon ausgegangen, dass Sie einen Windows-Client verwenden. Wenn Sie einen Linux-, Unix- oder OS X-Client verwenden, finden Sie entsprechende Informationen unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

##Voraussetzungen

* **PuTTY** und **PuTTYGen** für Windows-Clients. Diese Hilfsprogramme stehen unter [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) zur Verfügung.

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

1. Öffnen Sie PuTTYGen.

2. Wählen Sie für **Type of key to generate** die Option **SSH-2 RSA**, und klicken Sie dann auf **Generate**.

	![PuTTYGen-Oberfläche](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Bewegen Sie die Maus im Bereich unterhalb der Statusanzeige, bis die Anzeige gefüllt ist. Durch das Bewegen der Maus werden zufällige Daten generiert, die zum Generieren des Schlüssels verwendet werden.

	![Bewegen Sie die Maus](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	Nachdem der Schlüssel generiert wurde, wird der öffentliche Schlüssel angezeigt.

4. Zur Erhöhung der Sicherheit können Sie eine Passphrase in das Feld **Key passphrase** und dann denselben Wert in das Feld **Confirm passphrase** eingeben.

	![Passphrase](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE] Es wird dringend empfohlen, dass Sie für den Schlüssel eine sichere Passphrase verwenden. Wenn Sie die Passphrase vergessen, besteht jedoch keine Möglichkeit, diese wiederherzustellen.

5. Klicken Sie auf **Save private key**, um den Schlüssel in einer **PPK**-Datei zu speichern. Dieser Schlüssel wird zum Authentifizieren für die Linux-basierten HDInsight-Cluster verwendet.

	> [AZURE.NOTE] Sie sollten diesen Schlüssel an einem sicheren Ort verwahren, da dieser für den Zugriff auf Ihre Linux-basierten HDInsight-Cluster verwendet werden kann.

6. Klicken Sie auf **Save public key**, um den Schlüssel in einer **TXT**-Datei zu speichern. Dadurch können Sie den öffentlichen Schlüssel zukünftig wiederverwenden, wenn Sie zusätzliche Linux-basierte HDInsight-Cluster erstellen.

	> [AZURE.NOTE] Der öffentliche Schlüssel wird auch am oberen Rand von PuTTYGen angezeigt. Sie können mit der rechten Maustaste auf dieses Feld klicken, den Wert kopieren und ihn dann in ein Formular einfügen, wenn Sie einen Cluster mit dem Azure-Portal erstellen.

##Erstellen eines Linux-basierten HDInsight-Clusters

Wenn Sie einen Linux-basierten HDInsight-Cluster erstellen, müssen Sie den zuvor erstellten öffentlichen Schlüssel bereitstellen. Es gibt Möglichkeiten, um über Windows-Clients einen Linux-basierten HDInsight-Cluster zu erstellen:

* **Azure-Portal**: Zum Erstellen des Clusters wird ein webbasiertes Portal verwendet.

* **Azure-CLI für Mac, Linux und Windows **: Zum Erstellen des Clusters werden Befehle über die Befehlszeile eingegeben.

Für jede dieser Methoden ist der öffentliche Schlüssel erforderlich. Vollständige Informationen zum Erstellen eines Linux-basierten HDInsight-Clusters finden Sie unter [Bereitstellen von Linux-basierten HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md).

###Azure-Portal

Wenn Sie das [Azure-Portal][preview-portal] verwenden, um einen Linux-basierten HDInsight-Cluster zu erstellen, müssen Sie einen **SSH-Benutzernamen** eingeben und auswählen, ob Sie ein **Kennwort** oder einen **öffentlichen SSH-Schlüssel** eingeben.

Wenn Sie **ÖFFENTLICHER SSH-SCHLÜSSEL** auswählen, können Sie den öffentlichen Schlüssel (angezeigt im Feld __Öffentliche Schlüssel für die Datei für autorisierte OpenSSH-Schlüssel__ in PuttyGen) in das Feld __Öffentlicher SSH-Schlüssel__ einfügen. Oder klicken Sie auf __Datei auswählen__, um die Datei mit dem öffentlichen Schlüssel zu suchen und auszuwählen.

![Abbildung eines Formulars, das den öffentlichen Schlüssel anfordert](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Dadurch wird für den angegebenen Benutzer ein Anmeldename erstellt und die Kennwort- oder SSH-Schlüsselauthentifizierung ermöglicht.

###Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Microsoft Azure

Sie können über die [Azure-CLI für Mac, Linux und Windows ](../xplat-cli-install.md) einen neuen Cluster mithilfe des Befehls `azure hdinsight cluster create` erstellen.

Weitere Informationen zur Verwendung dieses Befehls finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

##Verbinden mit einem Linux-basierten HDInsight-Cluster

1. Öffnen Sie PuTTY.

	![PuTTY-Oberfläche](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Sofern Sie beim Erstellen des Benutzerkontos einen SSH-Schlüssel bereitgestellt haben, müssen Sie die folgenden Schritte ausführen, um den privaten Schlüssel auszuwählen, der zum Authentifizieren für den Cluster verwendet wird:

	Erweitern Sie in **Category** erst **Connection**, dann **SSH**, und wählen Sie anschließend **Auth** aus. Klicken Sie abschließend auf **Browse**, und wählen Sie die PPK-Datei aus, die Ihren privaten Schlüssel enthält.

	![PuTTY-Oberfläche, privaten Schlüssel auswählen](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. Wählen Sie in **Category** die Option **Session** aus. Geben Sie auf dem Bildschirm **Basic options for your PuTTY session** die SSH-Adresse Ihres HDInsight-Servers in das Feld **Host name (or IP address)** ein. Die SSH-Adresse ist Ihr Clustername, gefolgt von **-ssh.azurehdinsight.net**. Beispiel: **mycluster-ssh.azurehdinsight.net**.

	![PuTTY-Oberfläche mit eingegebener SSH-Adresse](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Geben Sie zum Speichern der Verbindungsinformationen für die künftige Verwendung einen Namen für diese Verbindung unter **Saved Sessions** ein, und klicken Sie dann auf **Save**. Die Verbindung wird zur Liste der gespeicherten Sitzungen hinzugefügt.

5. Klicken Sie auf **Open**, um die Verbindung mit dem Cluster herzustellen.

	> [AZURE.NOTE] Wenn Sie das erste Mal eine Verbindung mit dem Cluster hergestellt haben, wird eine Sicherheitswarnung angezeigt. Dies ist normal. Wählen Sie **Yes** aus, um den RSA2-Schlüssel des Servers zum Fortfahren zwischenzuspeichern.

6. Geben Sie nach der entsprechenden Aufforderung den Benutzer ein, den Sie beim Erstellen des Clusters eingegeben haben. Wenn Sie für den Benutzer ein Kennwort angegeben haben, werden Sie auch zur Eingabe dieses Kennworts aufgefordert.

> [AZURE.NOTE] In den oben genannten Schritten wird davon ausgegangen, dass Sie Port 22 verwenden. Über diesen Port wird eine Verbindung mit „Hauptknoten 0“ im HDInsight-Cluster hergestellt. Wenn Sie Port 23 verwenden, wird eine Verbindung mit „head node 1“ hergestellt. Weitere Informationen zu Hauptknoten finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight](hdinsight-high-availability-linux.md).

###Herstellen einer Verbindung mit den Workerknoten

Auf die Workerknoten kann von außerhalb des Azure-Datencenters nicht direkt zugegriffen werden. Doch auf dem Hauptknoten des Clusters ist der Zugriff darauf über SSH möglich.

Sofern Sie beim Erstellen des Benutzerkontos einen SSH-Schlüssel bereitgestellt haben, müssen Sie die folgenden Schritte ausführen, um den privaten Schlüssel auszuwählen, der zum Authentifizieren für den Cluster verwendet wird (wenn Sie eine Verbindung mit den Workerknoten herstellen möchten).

1. Laden Sie Pageant von [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) herunter, und installieren Sie dieses Programm. Es dient zum Zwischenspeichern von SSH-Schlüsseln für PuTTY.

2. Führen Sie Pageant aus. Es wird im Statusbereich minimiert als Symbol angezeigt. Klicken Sie mit rechten Maustaste, und wählen Sie **Add Key** aus.

    ![Schlüssel hinzufügen](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Wenn das Dialogfeld "Durchsuchen" angezeigt wird, wählen Sie die PPK-Datei aus, die den Schlüssel enthält, und klicken Sie dann auf **Open**. Dadurch wird den Schlüssel zu Pageant hinzugefügt, das ihn PuTTY beim Verbinden mit dem Cluster bereitstellt.

    > [AZURE.IMPORTANT] Wenn Sie einen SSH-Schlüssel verwendet haben, um Ihr Konto zu schützen, müssen Sie die vorherigen Schritte abschließen, bevor eine Verbindung mit Workerknoten hergestellt werden kann.

4. Öffnen Sie PuTTY.

5. Bei Verwendung eines SSH-Schlüssels zur Authentifizierung erweitern Sie im Abschnitt **Category** erst **Connection**, dann **SSH**, und wählen Sie anschließend **Auth** aus.

    Aktivieren Sie im Abschnitt **Authentication parameters** das Kontrollkästchen **Allow agent forwarding**. Dadurch kann PuTTY die Zertifikatauthentifizierung über die Verbindung automatisch an den Hauptknoten des Clusters übergeben, wenn eine Verbindung mit Workerknoten hergestellt wird.

    ![Agent-Weiterleitung zulassen](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Verbinden Sie sich, wie weiter oben beschrieben, mit dem Cluster. Wenn Sie einen SSH-Schlüssel für die Authentifizierung verwenden, müssen Sie den Schlüssel nicht auswählen. Der Pageant hinzugefügte SSH-Schlüssel wird zur Authentifizierung beim Cluster verwendet.

7. Nachdem die Verbindung hergestellt wurde, verwenden Sie den folgenden Befehl zum Abrufen einer Liste der Knoten im Cluster. Ersetzen Sie *ADMINPASSWORD* durch das Kennwort Ihres Clusteradministratorkontos. Ersetzen Sie *CLUSTERNAME* durch den Namen Ihres Clusters.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Dadurch werden Informationen im JSON-Format zu den Knoten im Cluster zurückgegeben, einschließlich `host_name`, die den vollqualifizierten Domänennamen (FQDN) für jeden Knoten enthalten. Es folgt ein Beispiel eines Eintrags vom Typ `host_name`, der vom Befehl **curl** zurückgegeben wird:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Sobald Sie eine Liste der Workerknoten haben, mit denen Sie eine Verbindung herstellen möchten, geben Sie in der PuTTY-Sitzung mit dem Server den folgenden Befehl ein, um eine Verbindung mit einem Workerknoten herzustellen:

        ssh USERNAME@FQDN

    Ersetzen Sie *USERNAME* durch Ihren SSH-Benutzernamen und *FQDN* durch den vollqualifizierten Domänennamen des Workerknotens. Beispiel: `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Wenn Sie ein Kennwort zur Authentifizierung Ihrer SSH-Sitzung verwenden, werden Sie aufgefordert, das Kennwort erneut einzugeben. Wenn Sie einen SSH-Schlüssel verwenden, sollte die Verbindung ohne Aufforderungen fertig gestellt werden.

9. Sobald die Sitzung eingerichtet ist, ändert sich die Eingabeaufforderung für Ihre PuTTY-Sitzung von `username@hn0-clustername` in `username@wn0-clustername`, um anzugeben, dass Sie mit dem Workerknoten verbunden sind. Alle Befehle, die Sie ab diesem Punkt ausführen, werden auf dem Workerknoten ausgeführt.

10. Wenn Sie mit dem Ausführen von Aktionen auf dem Workerknoten fertig sind, geben Sie den Befehl `exit` zum Schließen der Sitzung mit dem Workerknoten ein. Sie kehren zur Eingabeaufforderung `username@hn0-clustername` zurück.

##Hinzufügen weiterer Konten

Wenn Sie weitere Konten zum Cluster hinzufügen möchten, führen Sie die folgenden Schritte aus:

1. Generieren Sie einen neuen öffentlichen Schlüssel und privatem Schlüssel für das neue Benutzerkonto, wie zuvor beschrieben.

2. Fügen Sie in einer SSH-Sitzung mit dem Cluster den neuen Benutzer mithilfe des folgenden Befehls hinzu:

		sudo adduser --disabled-password <username>

	Dadurch wird ein neues Benutzerkonto erstellt, aber die Kennwortauthentifizierung deaktiviert.

3. Erstellen Sie das Verzeichnis und die Dateien zum Speichern des Schlüssels mit den folgenden Befehlen:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Wenn der Nano-Editor geöffnet wird, kopieren Sie die Inhalte des öffentlichen Schlüssels für das neue Benutzerkonto und fügen sie dann ein. Drücken Sie schließlich **STRG+X** zum Speichern der Datei, und beenden Sie den Editor.

	![Abbildung von Nano-Editor mit Beispielschlüssel](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Verwenden Sie den folgenden Befehl, um den Ordner ".ssh" und dessen Inhalt dem neuen Benutzerkonto zuzuordnen:

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Sie sollten jetzt in der Lage sein, sich beim Server mit dem neuen Benutzerkonto und dem privaten Schlüssel zu authentifizieren.

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

<!---HONumber=AcomDC_0128_2016-->