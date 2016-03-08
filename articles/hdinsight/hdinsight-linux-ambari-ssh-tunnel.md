<properties
pageTitle="Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen"
description="Erfahren Sie, wie Sie einen SSH-Tunnel verwenden, um auf Webressourcen auf Ihren Linux-basierten HDInsight-Knoten zuzugreifen."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="get-started-article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/12/2016"
ms.author="larryfr"/>

#Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen

Linux-basierte HDInsight-Cluster bieten Zugriff auf die Ambari-Webbenutzeroberfläche über das Internet, dabei stehen aber einige Funktionen der Benutzeroberfläche nicht zur Verfügung. Dies gilt beispielsweise für die Webbenutzeroberflächen anderer Dienste, die nur durch Ambari dargestellt werden. Für eine vollständige Funktionalität der Ambari-Webbenutzeroberfläche müssen Sie einen SSH-Tunnel zum Clusterhauptknoten verwenden.

##Wofür ist ein SSH-Tunnel erforderlich?

Einige der Menüs in Ambari werden ohne einen SSH-Tunnel SSH nicht vollständig aufgefüllt, da sie Websites und Dienste benötigen, die von anderen im Cluster ausgeführten Hadoop-Diensten verfügbar gemacht werden. Diese Websites sind oft nicht gesichert, sodass es nicht sicher wäre, sie direkt im Internet verfügbar zu machen. In einigen Fällen führt der Dienst die Website auf einem anderen Clusterknoten aus, z. B. auf einem Zookeeper-Knoten.

Im Folgenden finden Sie die Dienste, die von der Ambari-Webbenutzeroberfläche verwendet werden und auf die nur über einen SSH-Tunnel zugegriffen werden kann:

* ResourceManager,
* JobHistory,
* NameNode,
* Threadstapel,
* Oozie-Webbenutzeroberfläche
* Benutzeroberfläche für HBase-Master und -Protokolle

Wenn Sie für die Clusteranpassung Skriptaktionen verwenden, benötigen Sie für alle Dienste und Dienstprogramme, die Sie installieren und die eine Webbenutzeroberfläche verfügbar machen, einen SSH-Tunnel. Bei der Installation von Hue mit einer Skriptaktion müssen Sie z. B. einen SSH-Tunnel verwenden, um auf die Hue-Webbenutzeroberfläche zuzugreifen.

##Was ist ein SSH-Tunnel?

[Secure Shell (SSH)-Tunneling](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) leitet Datenverkehr an einen Port auf der lokalen Arbeitsstation über eine SSH-Verbindung zu Ihrem HDInsight-Clusterhauptknoten weiter, auf dem die Anforderung dann so aufgelöst wird, als ob sie vom Hauptknoten stammt. Die Antwort wird dann wieder durch den Tunnel an Ihre Arbeitsstation weitergeleitet.

##Voraussetzungen

Sie benötigen für die Verwendung eines SSH-Tunnels für Webdatenverkehr Folgendes:

* Einen SSH-Client Für Linux- und Unix-Distributionen oder Macintosh OS X steht der Befehl `ssh` über das Betriebssystem zur Verfügung. Bei Windows wird [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) empfohlen.

	> [AZURE.NOTE] Wenn Sie einen anderen SSH-Client als `ssh` oder PuTTY verwenden möchten, finden Sie Informationen zum Herstellen eines SSH-Tunnels in der Dokumentation zu Ihrem Client.

* Einen Webbrowser, der für die Verwendung eines SOCKS-Proxys konfiguriert werden kann.

* __(Optional)__: ein Plug-In wie [FoxyProxy](http://getfoxyproxy.org/,), das Regeln anwenden kann, sodass nur routenspezifische Anfragen durch den Tunnel geleitet werden.

	> [AZURE.WARNING] Ohne ein Plug-In wie FoxyProxy können alle über den Browser gesendeten Anfragen über den Tunnel weitergeleitet werden. Dies kann dazu führen, dass Webseiten in Ihrem Browser langsamer geladen werden.

##<a name="usessh"></a>Erstellen von Tunneln mit dem Befehl "ssh"

Verwenden Sie den folgenden Befehl zum Erstellen eines SSH-Tunnels mithilfe des Befehls `ssh`. Ersetzen Sie __USERNAME__ mit einem SSH-Benutzer für Ihren HDInsight-Cluster und __CLUSTERNAME__ mit dem Namen des HDInsight-Clusters.

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Dadurch wird eine Verbindung erstellt, über die der Datenverkehr über SSH an den lokalen Port 9876 des Clusters weitergeleitet wird. Die Optionen sind:

* **D 9876**: der lokale Port, der den Datenverkehr durch den Tunnel weiterleitet.

* **C**: Alle Daten werden komprimiert, da der Webdatenverkehr hauptsächlich aus Text besteht.

* **2**: SSH zwingen, nur Protokollversion 2 zu verwenden.

* **q**: Stiller Modus.

* **T**: Pseudo-TTY-Zuordnung deaktivieren, da lediglich ein Port weitergeleitet wird.

* **n**: Verhindert den Lesevorgang für STDIN, da lediglich ein Port weitergeleitet wird.

* **N**: Keine Remotebefehle ausführen, da lediglich ein Port weitergeleitet wird.

* **f**: Im Hintergrund ausführen.

Wenn Sie den Cluster mit einem SSH-Schlüssel konfiguriert haben, müssen Sie unter Umständen den Parameter `-i` verwenden und den Pfad zum privaten SSH-Schlüssel angeben.

Nach Abschluss des Befehls wird der an den lokalen Port 9876 des lokalen Computers gesendete Datenverkehr über SSL (Secure Sockets Layer) an den Hauptknoten des Clusters weitergeleitet, der dann seinen Ursprung darstellt.

##<a name="useputty"></a>Erstellen von Tunneln mit PuTTY

Führen Sie die folgenden Schritte aus, um einen SSH-Tunnel mithilfe von PuTTY zu erstellen.

1. Öffnen Sie PuTTY, und geben Sie die Verbindungsinformationen ein. Wenn Sie nicht mit PuTTY vertraut sind, finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md) Informationen für die Verwendung mit HDInsight.

2. Erweitern Sie auf der linken Seite des Dialogfelds im Abschnitt **Category** erst **Connection**, dann **SSH**, und wählen Sie anschließend **Tunnels** aus.

3. Geben Sie die folgenden Informationen in das Formular **Options controlling SSH port forwarding** ein:

	* **Source port**: Der Port auf dem Client, den Sie weiterleiten möchten. Beispiel: **9876**.

	* **Destination**: Die SSH-Adresse des Linux-basierten HDInsight-Clusters. Beispiel: **mycluster-ssh.azurehdinsight.net**.

	* **Dynamic**: Ermöglicht das dynamische SOCKS-Proxyrouting.

	![Abbildung von Tunneloptionen](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Klicken Sie auf **Add**, um die Einstellungen hinzuzufügen. Klicken Sie dann auf **Open**, um eine SSH-Verbindung zu öffnen.

5. Melden Sie sich bei entsprechender Aufforderung am Server an. Dadurch wird eine SSH-Sitzung eingerichtet und der Tunnel aktiviert.

##Verwenden des Tunnels im Browser

> [AZURE.NOTE] Für die Schritte in diesem Abschnitt wird der Firefox-Browser verwendet, der für Linux, Unix, Macintosh OS X und Windows-Systeme frei verfügbar ist. Andere moderne Browser wie Google Chrome, Microsoft Edge oder Apple Safari sollten ebenfalls funktionieren. Allerdings ist möglicherweise das in einigen Schritten verwendete FoxyProxy-Plug-In nicht für alle Browser verfügbar.

1. Konfigurieren Sie den Browser für die Verwendung von **localhost:9876** als **SOCKS v5**-Proxy. Die Firefox-Einstellungen sollten wie folgt aussehen. Wenn Sie einen anderen Port als 9876 verwenden, ändern Sie den Port entsprechend:

	![Abbildung von Firefox-Einstellungen](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE] Durch die Auswahl von **Remote-DNS** werden DNS-Anforderungen (Domain Name System) mithilfe des HDInsight-Clusters aufgelöst. Ist diese Option deaktiviert, wird DNS lokal aufgelöst.

2. Überprüfen Sie, ob Datenverkehr durch den Tunnel weitergeleitet wird, indem Sie eine Website wie [http://www.whatismyip.com/](http://www.whatismyip.com/) mit aktivierten und deaktivierten Proxyeinstellungen in Firefox aufrufen. Bei aktivierten Einstellungen wird die IP-Adresse eines Computers im Microsoft Azure-Datencenter angezeigt.

###Browsererweiterungen

Obwohl Sie den Browser für die Verwendung des Tunnels konfigurieren, möchten Sie in der Regel jedoch nicht den gesamten Datenverkehr über den Tunnel weiterleiten. Browsererweiterungen wie [FoxyProxy](http://getfoxyproxy.org/) unterstützen den Musterabgleich für URL-Anforderungen (nur FoxyProxy Standard oder Plus), sodass nur Anforderungen für bestimmte URLs durch den Tunnel gesendet werden.

Wenn Sie FoxyProxy Standard installiert haben, konfigurieren Sie es folgendermaßen, um nur den Datenverkehr für HDInsight über den Tunnel weiterzuleiten.

1. Öffnen Sie die FoxyProxy-Erweiterung in Ihrem Browser. Wählen Sie z. B. in Firefox das FoxyProxy-Symbol neben dem Adressfeld aus.

	![FoxyProxy (Symbol)](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. Wählen Sie **Neuen Proxy hinzufügen** aus, und geben Sie auf der Registerkarte **Allgemein** einen Proxynamen für **HDInsightProxy** ein.

	![FoxyProxy allgemein](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. Wählen Sie die Registerkarte **Proxydetails** aus, und geben Sie die entsprechenden Werte in die Felder ein:

	* **Host- oder IP-Adresse**: "localhost", da wir auf dem lokalen Computer einen SSH-Tunnel verwenden.

	* **Port**: Der Port, den Sie für den SSH-Tunnel verwendet haben.

	* **SOCKS-Proxy**: Aktivieren Sie diese Option, damit der Browser den Tunnel als Proxy verwenden kann.

	* **SOCKS v5**: Aktivieren Sie diese Option, um die erforderliche Version für den Proxy festzulegen.

	![FoxyProxy-Proxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. Wählen Sie die Registerkarte **URL-Muster** und anschließend **Neues Muster** aus. Gehen Sie wie folgt vor, um das Muster zu definieren, und klicken Sie dann auf **OK**:

	* **Name des Musters**: **Clusterknoten** – Dies ist lediglich ein Anzeigename für das Muster.

	* **URL-Muster** – ***internal.cloudapp.net*** – Dies definiert ein Muster, das mit dem internen vollständig qualifizierten Domänennamen der Clusterknoten übereinstimmt.

	![FoxyProxy-Muster](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

4. Klicken Sie auf **OK**, um den Proxy hinzuzufügen und **Proxyeinstellungen** zu schließen.

5. Am oberen Rand des FoxyProxy-Dialogfelds ändern Sie **Modus auswählen** in **Proxys basierend auf ihren vordefinierten Mustern und Prioritäten verwenden**, und klicken Sie dann auf **Schließen**.

	![FoxyProxy – Modus auswählen](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

Nachdem Sie diese Schritte ausgeführt haben, werden ausschließlich Anforderungen für URLs, die die Zeichenfolge __internal.cloudapp.net__ enthalten, über den SSL-Tunnel weitergeleitet.

##Überprüfen der Ambari-Webbenutzeroberfläche

Nachdem der Cluster eingerichtet wurde, gehen Sie folgendermaßen vor, um zu überprüfen, ob Sie von der Ambari-Webbenutzeroberfläche auf Dienst-Webbenutzeroberflächen zugreifen können:

1. Navigieren Sie im Webbrowser zu https://CLUSTERNAME.azurehdinsight.net, wobei "CLUSTERNAME" der Name des HDInsight-Clusters ist.

	Geben Sie bei der entsprechenden Aufforderung den Benutzernamen ("admin") und das Kennwort des Administrators für den Cluster ein. Sie werden eventuell von der Ambari-Webbenutzeroberfläche ein zweites Mal zur Eingabe aufgefordert. Geben Sie in diesem Fall die Informationen erneut ein.

2. Wählen Sie auf der Ambari-Webbenutzeroberfläche aus der Liste links auf der Seite YARN aus.

	![Bild mit ausgewähltem YARN-Dienst](./media/hdinsight-linux-ambari-ssh-tunnel/yarnservice.png)

3. Wenn die Dienstinformationen zu YARN angezeigt werden, wählen Sie __QuickLinks__ aus. Es wird eine Liste der Clusterhauptknoten angezeigt. Wählen Sie einen der Hauptknoten und dann __ResourceManager UI__ aus.

	![Bild mit erweitertem Menü "QuickLinks"](./media/hdinsight-linux-ambari-ssh-tunnel/yarnquicklinks.png)

	> [AZURE.NOTE] Wenn Sie über eine langsame Internetverbindung verfügen oder der Hauptknoten stark ausgelastet ist, wird möglicherweise anstelle eines Menüs eine Benachrichtigung zum Warten angezeigt, wenn Sie __QuickLinks__ auswählen. Warten Sie in diesem Fall eine oder zwei Minuten auf den Empfang der Daten vom Server, und wiederholen Sie dann das Auflisten.


	> [AZURE.TIP] Wenn Sie einen Monitor mit niedriger Auflösung verwenden oder das Browserfenster nicht maximiert ist, werden einige Einträge im Menü __QuickLinks__ möglicherweise auf der rechten Seite des Bildschirms abgeschnitten. Erweitern Sie in diesem Fall das Menü mit der Maus, und verwenden Sie die Nach-Rechts-Taste, um nach rechts zu scrollen und das restliche Menü anzuzeigen.

4. Eine Seite ähnlich der folgenden wird angezeigt:

	![Bild der YARN-ResourceManager-Benutzeroberfläche](./media/hdinsight-linux-ambari-ssh-tunnel/yarnresourcemanager.png)

	> [AZURE.TIP] Beachten Sie die URL für diese Seite. Sie sollte in etwa \_\___http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__ lauten. Dabei wird der interne vollständig qualifizierte Domänenname (FQDN) des Knotens verwendet, auf den nicht ohne Verwendung eines SSH-Tunnels zugegriffen werden kann.

##Nächste Schritte

Nachdem Sie erfahren haben, wie Sie einen SSH-Tunnel erstellen und verwenden, finden Sie in den folgenden Ressourcen Informationen zum Überwachen und Verwalten des Clusters mithilfe von Ambari:

* [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md)

Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=AcomDC_0302_2016-->