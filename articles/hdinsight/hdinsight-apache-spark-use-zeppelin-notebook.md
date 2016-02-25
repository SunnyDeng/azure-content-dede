<properties 
	pageTitle="Verwenden von Zeppelin Notebooks mit einem Spark-Cluster in HDInsight Linux | Azure" 
	description="Enthält eine Schritt-für-Schritt-Anleitung zur Verwendung von Zeppelin Notebooks mit Spark-Clustern in HDInsight Linux." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Verwenden von Zeppelin Notebooks mit Spark-Cluster in HDInsight (Linux)

Erfahren Sie, wie Zeppelin Notebooks auf Spark-Clustern installiert und wie Zeppelin Notebooks verwendet werden.

> [AZURE.IMPORTANT] Ein Zeppelin Notebook für Spark-Cluster in HDInsight dient zur Veranschaulichung der Verwendung von Zeppelin in einer Azure HDInsight Spark-Umgebung. Wenn Sie Notebooks zum Arbeiten mit HDInsight Spark verwenden möchten, empfehlen wir die Verwendung von Jupyter Notebooks. Jupyter Notebooks bieten zudem verschiedene Kerneloptionen, z. B. Scala, und werden weiterhin Verbesserungen von Features aufweisen. Hinweise zur Verwendung von Jupyter Notebooks mit HDInsight Spark finden Sie unter [Ausführen von Spark-SQL-Abfragen mit einem Jupyter Notebook](hdinsight-apache-spark-jupyter-spark-sql.md#jupyter).

**Voraussetzungen:**

* Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie über ein Azure-Abonnement verfügen. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Einen Apache Spark-Cluster. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
* Einen SSH-Client Für Linux- und Unix-Distributionen oder Macintosh OS X steht der Befehl `ssh` über das Betriebssystem zur Verfügung. Bei Windows wird [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) empfohlen.

	> [AZURE.NOTE] Wenn Sie einen anderen SSH-Client als `ssh` oder PuTTY verwenden möchten, finden Sie Informationen zum Herstellen eines SSH-Tunnels in der Dokumentation zu Ihrem Client.

* Einen Webbrowser, der für die Verwendung eines SOCKS-Proxys konfiguriert werden kann.

* __(Optional)__: ein Plug-In wie [FoxyProxy](http://getfoxyproxy.org/,), das Regeln anwenden kann, sodass nur routenspezifische Anfragen durch den Tunnel geleitet werden.

	> [AZURE.WARNING] Ohne ein Plug-In wie FoxyProxy können alle über den Browser gesendeten Anfragen über den Tunnel weitergeleitet werden. Dies kann dazu führen, dass Webseiten in Ihrem Browser langsamer geladen werden.

## Installieren von Zeppelin im Rahmen der Clustererstellung

Sie können Zeppelin mithilfe von Skriptaktionen in einem Spark-Cluster installieren. Skriptaktionen verwenden benutzerdefinierte Skripts zum Installieren von Komponenten im Cluster, die nicht standardmäßig verfügbar sind. Das benutzerdefinierte Skript zum Installieren von Zeppelin in einem Spark-Cluster finden Sie unter **https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

### Verwenden des Azure-Portals

Eine Anleitung zur Verwendung des HDInsight .NET SDK zum Ausführen der Skriptaktion zum Installieren von Zeppelin finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). Sie müssen an den Anweisungen in diesem Artikel einige Änderungen vornehmen.

* Sie müssen das Skript zum Installieren von Zeppelin verwenden. Das zu verwendende Skript ist **https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* Sie müssen die Skriptaktion nur auf dem Hauptknoten ausführen.

* Das Skript benötigt keine Parameter.

### Das HDInsight .NET SDK

Eine Anleitung zur Verwendung des HDInsight .NET SDK zum Ausführen der Skriptaktion zum Installieren von Zeppelin finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). Sie müssen an den Anweisungen in diesem Artikel einige Änderungen vornehmen.

* Sie müssen das Skript zum Installieren von Zeppelin verwenden. Das zu verwendende Skript ist **https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* Das Skript benötigt keine Parameter.

* Legen Sie den zu erstellenden Clustertyp auf „Spark“ fest.

### Verwenden von Azure PowerShell

Verwenden Sie den folgenden PowerShell-Codeausschnitt, um einen Spark-Cluster unter HDInsight Linux mit Installation von Zeppelin zu erstellen. Stellen Sie vor dem Fortfahren sicher, dass PowerShell installiert ist. Entsprechende Anweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## Einrichten von SSH-Tunneling für den Zugriff auf ein Zeppelin Notebook

Sie verwenden SSH-Tunnel, um auf die Zeppelin Notebooks zuzugreifen, die im Spark-Cluster unter HDInsight Linux ausgeführt werden. In den unten angegebenen Schritten wird gezeigt, wie Sie einen SSH-Tunnel über die ssh-Befehlszeile (Linux) und PuTTY (Windows) erstellen.

### Erstellen von Tunneln mit dem Befehl „ssh“ (Linux)

Verwenden Sie den folgenden Befehl zum Erstellen eines SSH-Tunnels mithilfe des Befehls `ssh`. Ersetzen Sie __USERNAME__ mit einem SSH-Benutzer für Ihren HDInsight-Cluster und __CLUSTERNAME__ mit dem Namen des HDInsight-Clusters.

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Dadurch wird eine Verbindung erstellt, über die der Datenverkehr über SSH an den lokalen Port 9876 des Clusters weitergeleitet wird. Die Optionen sind:

* **D 9876**: der lokale Port, der den Datenverkehr durch den Tunnel weiterleitet.

* **C**: Alle Daten werden komprimiert, da der Webdatenverkehr hauptsächlich aus Text besteht.

* **2**: SSH zwingen, nur Protokollversion 2 zu verwenden.

* **q**: Stiller Modus.

* **T**: Pseudo-TTY-Zuordnung deaktivieren, da lediglich ein Port weitergeleitet wird.

* **n**: Verhindert den Lesevorgang für STDIN, da lediglich ein Port weitergeleitet wird.

* **N**: Keine Remotebefehle ausführen, da lediglich ein Port weitergeleitet wird.

* **f**: Im Hintergrund ausführen.

Wenn Sie den Cluster mit einem SSH-Schlüssel konfiguriert haben, müssen Sie unter Umständen den Parameter `-i` verwenden und den Pfad zum privaten SSH-Schlüssel angeben.

Nach Abschluss des Befehls wird der an den lokalen Port 9876 des lokalen Computers gesendete Datenverkehr über SSL (Secure Sockets Layer) an den Hauptknoten des Clusters weitergeleitet, der dann seinen Ursprung darstellt.

### Erstellen von Tunneln mit PuTTY (Windows)

Führen Sie die folgenden Schritte aus, um einen SSH-Tunnel mithilfe von PuTTY zu erstellen.

1. Öffnen Sie PuTTY, und geben Sie die Verbindungsinformationen ein. Wenn Sie nicht mit PuTTY vertraut sind, finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md) Informationen für die Verwendung mit HDInsight.

2. Erweitern Sie auf der linken Seite des Dialogfelds im Abschnitt **Category** erst **Connection**, dann **SSH**, und wählen Sie anschließend **Tunnels** aus.

3. Geben Sie die folgenden Informationen in das Formular **Options controlling SSH port forwarding** ein:

	* **Source port**: Der Port auf dem Client, den Sie weiterleiten möchten. Beispiel: **9876**.

	* **Destination**: Die SSH-Adresse des Linux-basierten HDInsight-Clusters. Beispiel: **mycluster-ssh.azurehdinsight.net**.

	* **Dynamic**: Ermöglicht das dynamische SOCKS-Proxyrouting.

	![Abbildung von Tunneloptionen](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Klicken Sie auf **Add**, um die Einstellungen hinzuzufügen. Klicken Sie dann auf **Open**, um eine SSH-Verbindung zu öffnen.

5. Melden Sie sich bei entsprechender Aufforderung am Server an. Dadurch wird eine SSH-Sitzung eingerichtet und der Tunnel aktiviert.

### Verwenden des Tunnels im Browser

> [AZURE.NOTE] Für die Schritte in diesem Abschnitt wird der Firefox-Browser verwendet, der für Linux, Unix, Macintosh OS X und Windows-Systeme frei verfügbar ist. Andere moderne Browser wie Google Chrome, Microsoft Edge oder Apple Safari sollten ebenfalls funktionieren. Allerdings ist möglicherweise das in einigen Schritten verwendete FoxyProxy-Plug-In nicht für alle Browser verfügbar.

1. Konfigurieren Sie den Browser für die Verwendung von **localhost:9876** als **SOCKS v5**-Proxy. Die Firefox-Einstellungen sollten wie folgt aussehen. Wenn Sie einen anderen Port als 9876 verwenden, ändern Sie den Port entsprechend:

	![Abbildung von Firefox-Einstellungen](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] Durch die Auswahl von **Remote-DNS** werden DNS-Anforderungen (Domain Name System) mithilfe des HDInsight-Clusters aufgelöst. Ist diese Option deaktiviert, wird DNS lokal aufgelöst.

2. Überprüfen Sie, ob Datenverkehr durch den Tunnel weitergeleitet wird, indem Sie eine Website wie [http://www.whatismyip.com/](http://www.whatismyip.com/) mit aktivierten und deaktivierten Proxyeinstellungen in Firefox aufrufen. Bei aktivierten Einstellungen wird die IP-Adresse eines Computers im Microsoft Azure-Datencenter angezeigt.

### Browsererweiterungen

Obwohl Sie den Browser für die Verwendung des Tunnels konfigurieren, möchten Sie in der Regel jedoch nicht den gesamten Datenverkehr über den Tunnel weiterleiten. Browsererweiterungen wie [FoxyProxy](http://getfoxyproxy.org/) unterstützen den Musterabgleich für URL-Anforderungen (nur FoxyProxy Standard oder Plus), sodass nur Anforderungen für bestimmte URLs durch den Tunnel gesendet werden.

Wenn Sie FoxyProxy Standard installiert haben, konfigurieren Sie es folgendermaßen, um nur den Datenverkehr für HDInsight über den Tunnel weiterzuleiten.

1. Öffnen Sie die FoxyProxy-Erweiterung in Ihrem Browser. Wählen Sie z. B. in Firefox das FoxyProxy-Symbol neben dem Adressfeld aus.

	![FoxyProxy (Symbol)](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Wählen Sie **Neuen Proxy hinzufügen** aus, und geben Sie auf der Registerkarte **Allgemein** einen Proxynamen für **HDInsightProxy** ein.

	![FoxyProxy allgemein](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Wählen Sie die Registerkarte **Proxydetails** aus, und geben Sie die entsprechenden Werte in die Felder ein:

	* **Host- oder IP-Adresse**: "localhost", da wir auf dem lokalen Computer einen SSH-Tunnel verwenden.

	* **Port**: Der Port, den Sie für den SSH-Tunnel verwendet haben.

	* **SOCKS-Proxy**: Aktivieren Sie diese Option, damit der Browser den Tunnel als Proxy verwenden kann.

	* **SOCKS v5**: Aktivieren Sie diese Option, um die erforderliche Version für den Proxy festzulegen.

	![FoxyProxy-Proxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Wählen Sie die Registerkarte **URL-Muster** und anschließend **Neues Muster** aus. Gehen Sie wie folgt vor, um das Muster zu definieren, und klicken Sie dann auf **OK**:

	* **Name des Musters**: **zeppelinnotebook** – Dies ist lediglich ein Anzeigename für das Muster.

	* **URL-Muster**: ***hn0*** – Dient zum Definieren eines Musters, das mit dem internen vollqualifizierten Domänennamen des Endpunkts übereinstimmt, auf dem die Zeppelin Notebooks gehostet werden. Da Zeppelin Notebooks nur auf „headnode0“ des Clusters verfügbar sind und der Endpunkt normalerweise `http://hn0-<string>.internal.cloudapp.net` lautet, wird durch die Verwendung des Musters **hn0** sichergestellt, dass die Anforderung an den Zeppelin-Endpunkt umgeleitet wird.

		![FoxyProxy-Muster](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Klicken Sie auf **OK**, um den Proxy hinzuzufügen und **Proxyeinstellungen** zu schließen.

5. Am oberen Rand des FoxyProxy-Dialogfelds ändern Sie **Modus auswählen** in **Proxys basierend auf ihren vordefinierten Mustern und Prioritäten verwenden**, und klicken Sie dann auf **Schließen**.

	![FoxyProxy – Modus auswählen](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

Nachdem Sie diese Schritte ausgeführt haben, werden ausschließlich Anforderungen für URLs, die die Zeichenfolge __internal.cloudapp.net__ enthalten, über den SSL-Tunnel weitergeleitet.

## Zugreifen auf das Zeppelin Notebook

Nach dem Einrichten des SSH-Tunneling können Sie die folgenden Schritte ausführen, um auf das Zeppelin Notebook auf dem Spark-Cluster zuzugreifen.

1. Öffnen Sie im Webbrowser den folgenden Endpunkt:

		http://hn0-myspar:9995

	* **hn0** steht für „headnode0“.
	* **myspar** sind die ersten sechs Buchstaben des Namens des Spark-Clusters.
	* **9995** ist der Port, über den das Zeppelin Notebook zugänglich ist

2. Erstellen Sie ein neues Notebook. Klicken Sie im Headerbereich auf **Notebook**, und wählen Sie die Option **Neue Notiz erstellen** aus.

	![Erstellen eines neuen Zeppelin Notebooks](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "Erstellen eines neuen Zeppelin Notebooks")

	Auf derselben Seite sollte unter der Überschrift **Notebook** ein neues Notebook angezeigt werden, dessen Name mit **Notiz XXXXXXXXX** beginnt. Klicken Sie auf das neue Notebook.

3. Klicken Sie auf der Webseite für das neue Notebook auf die Überschrift, und ändern Sie den Namen des Notebooks, wenn Sie dies möchten. Drücken Sie die EINGABETASTE, um die Namensänderung zu speichern. Stellen Sie außerdem sicher, dass im Header des Notebooks in der oberen rechten Ecke der Status **Verbunden** angezeigt wird.

	![Zeppelin Notebook-Status](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Zeppelin Notebook-Status")

4. Laden Sie Beispieldaten in eine temporäre Tabelle. Wenn Sie einen Spark-Cluster in HDInsight erstellen, wird die Beispieldatei **hvac.csv** in das zugeordnete Speicherkonto unter **\\HdiSamples\\SensorSampleData\\hvac** kopiert.

	Fügen Sie in den leeren Absatz, der im neuen Notebook standardmäßig erstellt wird, den folgenden Codeausschnitt ein.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Drücken Sie UMSCHALT+EINGABETASTE, oder klicken Sie auf die Schaltfläche **Wiedergeben** für den Absatz, um den Codeausschnitt auszuführen. Der Status in der rechten Ecke des Absatzes sollte sich entsprechend ändern: BEREIT, AUSSTEHEND, WIRD AUSGEFÜHRT bis zu BEENDET. Die Ausgabe wird unten im Absatz angezeigt. Der Screenshot sieht folgendermaßen aus:

	![Erstellen einer temporären Tabelle aus Rohdaten](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "Erstellen einer temporären Tabelle aus Rohdaten")

	Sie können auch einen Titel für jeden Absatz angeben. Klicken Sie in der rechten Ecke auf das Symbol **Einstellungen** und dann auf **Titel anzeigen**.

5. Sie können jetzt Spark-SQL-Anweisungen für die **hvac**-Tabelle ausführen. Fügen Sie die folgende Abfrage in einen neuen Absatz ein. Mit der Abfrage werden die Gebäude-ID und der Unterschied zwischen den Ziel- und Ist-Temperaturen für jedes Gebäude an einem bestimmten Datum abgerufen. Drücken Sie UMSCHALT+EINGABETASTE.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	Mit der **%sql**-Anweisung am Anfang wird das Notebook angewiesen, den Spark-SQL-Interpreter zu verwenden. Sie können die definierten Interpreter im Header des Notebooks auf der Registerkarte **Interpreter** anzeigen.

	Im folgenden Screenshot ist die Ausgabe dargestellt.

	![Ausführen einer Spark-SQL-Anweisung mit dem Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "Ausführen einer Spark-SQL-Anweisung mit dem Notebook")

	 Klicken Sie auf die Anzeigeoptionen (im Rechteck hervorgehoben), um für eine Ausgabe zwischen unterschiedlichen Darstellungen zu wechseln. Klicken Sie auf **Einstellungen**, um auszuwählen, worum es sich bei den Schlüsseln und Werten in der Ausgabe handelt. Im obigen Screenshot werden **buildingID** als Schlüssel und der Mittelwert von **temp\_diff** als Wert verwendet.

	
6. Sie können auch Spark-SQL-Anweisungen ausführen, indem Sie die Variablen in der Abfrage verwenden. Der nächste Codeausschnitt zeigt, wie Sie eine Variable (**Temp**) in der Abfrage mit den möglichen Werten definieren, die für die Abfrage verwendet werden sollen. Beim ersten Ausführen der Abfrage wird automatisch eine Dropdownliste mit den Werten aufgefüllt, die Sie für die Variable angegeben haben.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Fügen Sie diesen Codeausschnitt in einen neuen Absatz ein, und drücken Sie UMSCHALT+EINGABETASTE. Im folgenden Screenshot ist die Ausgabe dargestellt.

	![Ausführen einer Spark-SQL-Anweisung mit dem Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "Ausführen einer Spark-SQL-Anweisung mit dem Notebook")

	Für nachfolgende Abfragen können Sie einen neuen Wert aus der Dropdownliste auswählen und die Abfrage erneut ausführen. Klicken Sie auf **Einstellungen**, um auszuwählen, worum es sich bei den Schlüsseln und Werten in der Ausgabe handelt. Im obigen Screenshot werden **buildingID** als Schlüssel, der Mittelwert von **temp\_diff** als Wert und **targettemp** als Gruppe verwendet.

7. Starten Sie den Spark-SQL-Interpreter neu, um die Anwendung zu beenden. Klicken Sie oben auf die Registerkarte **Interpreter**, und klicken Sie für den Spark-Interpreter auf **Neu starten**.

	![Neustarten des Zeppelin-Interpreters](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "Neustarten des Zeppelin-Interpreters")


## <a name="seealso"></a>Weitere Informationen


* [Übersicht: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md)

### Szenarios

* [Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools.md)

* [Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark-Streaming: Erstellen von Echtzeitstreaminganwendungen mithilfe von Spark in HDInsight](hdinsight-apache-spark-eventhub-streaming.md)

* [Websiteprotokollanalyse mithilfe von Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Erstellen und Ausführen von Anwendungen

* [Erstellen einer eigenständigen Anwendung mit Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Remoteausführung von Aufträgen in einem Spark-Cluster mithilfe von Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Tools und Erweiterungen

* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Verfügbare Kernels für Jupyter Notebook im Spark-Cluster für HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Verwalten von Ressourcen

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!----HONumber=AcomDC_0211_2016-->