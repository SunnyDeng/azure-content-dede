<properties
	pageTitle="HBase-Tutorial: Erste Schritte mit Linux-basierten HBase-Clustern in Hadoop | Microsoft Azure"
	description="Führen Sie dieses HBase-Lernprogramm aus, um sich in Apache HBase mit Hadoop in HDInsight einzuarbeiten. Erstellen Sie über die HBase-Shell Tabellen und fragen Sie diese mit Hive ab."
	keywords="Apache Hbase, Hbase, Hbase-Shell, Hbase-Tutorial"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/01/2016"
	ms.author="jgao"/>



# HBase-Tutorial: Erste Schritte mit Apache HBase mit Linux-basiertem Hadoop in HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Erfahren Sie, wie Sie einen HBase-Cluster in HDInsight erstellen, HBase-Tabellen erstellen und Tabellen mit Hive abfragen. Allgemeine Informationen zu HBase finden Sie unter [Überblick zu HDInsight HBase][hdinsight-hbase-overview].

Die Informationen in diesem Artikel gelten für Linux-basierte HDInsight-Cluster. Informationen zu Windows-basierten Clustern erhalten Sie, indem Sie die Registerkartenauswahl oben auf der Seite zum Umschalten verwenden.

###Voraussetzungen

Bevor Sie mit diesem Lernprogramm zu HBase beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell (SSU)](hdinsight-hadoop-linux-use-ssh-unixl.md). 
- [curl](http://curl.haxx.se/download.html).

## Erstellen eines HBase-Clusters

Im folgenden Verfahren wird eine Azure ARM-Vorlage verwendet, um einen HBase-Cluster zu erstellen. Informationen zu den Parametern, die in diesem Verfahren und in anderen Verfahren zur Clustererstellung verwendet werden, finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicken Sie auf die folgende Abbildung, um eine ARM-Vorlage im Azure-Portal zu öffnen. Die ARM-Vorlage befindet sich in einem öffentlichen Blobcontainer. 

    [![Bereitstellen in Azure](./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2FHbase.json)

2. Geben Sie über das Blatt **Parameter** Folgendes ein:

    - **ClusterName**: Geben Sie einen Namen für den HBase-Cluster ein, den Sie erstellen möchten.
    - **ClusterStorageAccountName**: Jeder Cluster verfügt über eine Abhängigkeit von einem Azure Blob Storage-Konto. Nach dem Löschen eines Clusters bleiben die Daten im Speicherkonto erhalten.
    - **Cluster-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet **admin**.
    - **SSH-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet **sshuser**. Sie können auch einen anderen Namen festlegen. Andere Parameter sind optional.  
3. Klicken Sie auf **OK**, um die Parameter zu speichern.
4. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf das Dropdownfeld **Ressourcengruppe**, und klicken Sie dann auf **Neu**, um eine neue Ressourcengruppe zu erstellen. Die Ressourcengruppe ist ein Container, in dem der Cluster, das abhängige Speicherkonto und andere verknüpfte Ressourcen gruppiert werden.
5. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.
6. Klicken Sie auf **Erstellen**. Das Erstellen eines Clusters dauert ca. 20 Minuten.


>[AZURE.NOTE] Nachdem Sie den HBase-Cluster gelöscht haben, können Sie im gleichen Standardblobcontainer einen neuen HBase-Cluster erstellen. Der neue Cluster übernimmt die im vorherigen Cluster erstellten HBase-Tabellen.

## Erstellen von Tabellen und Einfügen von Daten

Sie können SSH verwenden, um eine Verbindung mit HBase-Clustern herzustellen, und mit HBase Shell können Sie HBase-Tabellen erstellen, Daten einfügen und Daten abfragen. Informationen zur Verwendung von SSH unter Linux, Unix, OS X und Windows finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix und OS X](hdinsight-hadoop-linux-use-ssh-unix.md) und [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Den meisten Benutzern werden die Daten im Tabellenformat angezeigt:

![HDInsight HBase-Tabellendaten][img-hbase-sample-data-tabular]

In HBase, das eine Implementierung von BigTable ist, sehen die gleichen Daten so aus:

![HDInsight HBase-BigTable-Daten][img-hbase-sample-data-bigtable]

Diese Darstellung ergibt nach Abschluss der nächsten Prozedur mehr Sinn.


**So verwenden Sie die HBase-Shell**

1. Führen Sie unter SSH den folgenden Befehl aus:

		hbase shell

4. Erstellen Sie eine HBase-Tabelle mit zwei Spaltenfamilien:

		create 'Contacts', 'Personal', 'Office'
		list
5. Fügen Sie Daten ein:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![HDInsight Hadoop HBase-Shell][img-hbase-shell]

6. Rufen Sie eine einzelne Zeile ab:

		get 'Contacts', '1000'

	Sie erhalten das gleiche Ergebnis wie mit dem Scanbefehl, da nur eine Zeile vorhanden ist.

	Weitere Informationen zum HBase-Tabellenschema finden Sie unter [Einführung in das HBase-Schemadesign][hbase-schema]. Beschreibungen weiterer HBase-Befehle finden Sie im [Apache HBase-Referenzhandbuch][hbase-quick-start].

6. Beenden Sie die Shell:

		exit

**So laden Sie Massendaten in die HBase-Kontakttabelle hoch**

HBase bietet mehrere Methoden zum Laden von Daten in Tabellen. Weitere Informationen finden Sie unter [Laden von Massendaten](http://hbase.apache.org/book.html#arch.bulk.load).


Eine Beispieldatendatei befindet sich im folgenden öffentlichen Blobcontainer: **wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*. Diese Datendatei hat folgenden Inhalt:

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

Wenn Sie möchten, können Sie eine Textdatei erstellen und die Datei in Ihr eigenes Speicherkonto hochladen. Anweisungen hierzu finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] In der folgenden Prozedur wird die soeben erstellte HBase-Kontakttabelle verwendet.

1. Führen Sie unter SSH den folgenden Befehl aus, um die Datendatei in das StoreFiles-Format zu konvertieren und unter dem durch „Dimporttsv.bulk.output“ angegebenen relativen Pfad zu speichern: Verwenden Sie den exit-Befehl, um den Vorgang zu beenden, falls Sie sich in der HBase Shell befinden.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Führen Sie den folgenden Befehl aus, um die Daten aus "/example/data/storeDataFileOutput" in die HBase-Tabelle hochzuladen:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Sie können die HBase-Shell öffnen und den Tabelleninhalt mit dem Scanbefehl auflisten.



## Verwenden von Hive zum Abfragen von HBase

Sie können Daten in HBase-Tabellen mit Hive abfragen. In diesem Abschnitt erstellen Sie eine Ihrer HBase-Tabelle zugeordnete Hive-Tabelle, mit der Sie die Daten Ihrer HBase-Tabelle abfragen.

1. Öffnen Sie **PuTTY**, und stellen Sie eine Verbindung mit dem Cluster her. Anweisungen finden Sie weiter oben.
2. Öffnen Sie die Hive-Shell.

	hive
3. Führen Sie das folgende HiveQL-Skript aus, um eine der HBase-Tabelle zugeordnete Hive-Tabelle zu erstellen. Stellen Sie vor Ausführung dieser Anweisung sicher, dass Sie die zuvor in diesem Lernprogramm erwähnte Beispieltabelle über die HBase-Shell erstellt haben.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Führen Sie das folgende HiveQL-Skript aus. Die Hive-Abfrage fragt die Daten in der HBase-Tabelle ab:

     	SELECT count(*) FROM hbasecontacts;

## Verwenden der HBase-REST-APIs mit Curl

> [AZURE.NOTE] Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen auch den Clusternamen als Teil des URIs (Uniform Resource Identifier) verwenden, um die Anforderungen an den Server zu senden.
>
> Ersetzen Sie für die Befehle in diesem Abschnitt die Option **BENUTZERNAME** zur Authentifizierung im Cluster durch den Benutzer und die Option **KENNWORT** durch das Kennwort für das Benutzerkonto. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters.
>
> Die REST-API wird durch [Standardauthentifizierung](http://en.wikipedia.org/wiki/Basic_access_authentication) gesichert. Sie sollten Anforderungen immer über HTTPS (Secure HTTP) stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Verwenden Sie den folgenden Befehl in einer Befehlszeile, um zu überprüfen, ob Sie die Verbindung zum HDInsight-Cluster herstellen können:

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	Sie sollten eine Antwort empfangen, die in etwa der im Folgenden aufgeführten entspricht:

    {"status":"ok","version":"v1"}

  Folgende Parameter werden in diesem Befehl verwendet:

    * **-u** - Der Benutzername und das Kennwort für die Authentifizierung der Anforderung
    * **-G** - Gibt an, dass dies eine GET-Anforderung ist

2. Verwenden Sie den folgenden Befehl, um die vorhandenen HBase-Tabellen aufzulisten:

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Verwenden Sie den folgenden Befehl, um eine neue HBase-Tabelle mit zwei Spaltenfamilien zu erstellen:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{\"@name\":\"test\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}"

	Das Schema wird im JSON-Format bereitgestellt.

4. Fügen Sie mit dem folgenden Befehl Daten ein:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{\"Row\":{\"key\":\"1000\",\"Cell\":{\"column\":\"Personal:Name\", \"$\":\"John Dole\"}}}"

5. Rufen Sie mit dem folgenden Befehl eine Zeile ab:

		curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## Überprüfen des Clusterstatus

HBase in HDInsight wird mit einer Web-Benutzeroberfläche ausgeliefert, über die Cluster überwacht werden können. In dieser Web-Benutzeroberfläche können Sie Statistiken und Informationen zu Regionen anfordern.

SSH kann auch zum Tunneln lokaler Anforderungen, z. B. Webanforderungen, zum HDInsight-Cluster verwendet werden. Die Anforderung wird dann zur angeforderten Ressource weitergeleitet, als ob sie vom Stammknoten des HDInsight-Clusters stammen würde. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows (Vorschau)](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**So richten Sie eine SSH-Tunnelsitzung ein**

1. Öffnen Sie **PuTTY**.  
2. Wenn Sie während des Erstellungsprozesses beim Erstellen des Benutzerkontos einen SSH-Schlüssel bereitgestellt haben, müssen Sie den folgenden Schritt ausführen, um den privaten Schlüssel auszuwählen, der zum Authentifizieren beim Cluster verwendet wird:

	Erweitern Sie in **Category** erst **Connection**, dann **SSH**, und wählen Sie anschließend **Auth** aus. Klicken Sie abschließend auf **Browse**, und wählen Sie die PPK-Datei aus, die Ihren privaten Schlüssel enthält.

3. Klicken Sie in **Category** auf **Session**.
4. Geben Sie für die grundlegenden Optionen im PuTTY-Sitzungsbildschirm die folgenden Werte ein:

	- **Hostname**: Die SSH-Adresse des HDInsight-Servers im Feld "Hostname" (oder "IP-Adresse"). Die SSH-Adresse ist Ihr Clustername, gefolgt von **-ssh.azurehdinsight.net**. Beispiel: *mycluster-ssh.azurehdinsight.net*.
	- **Port**: 22. Der SSH-Port auf dem Hauptknoten 0 ist „22“.  
5. Erweitern Sie auf der linken Seite des Dialogfelds im Abschnitt **Category** erst **Connection**, dann **SSH**, und klicken Sie anschließend auf **Tunnels**.
6. Geben Sie die folgenden Informationen in das Formular "Options controlling SSH port forwarding" ein:

	- **Source port**: Der Port auf dem Client, den Sie weiterleiten möchten. Beispiel: 9876.
	- **Dynamic**: Ermöglicht das dynamische SOCKS-Proxyrouting.
7. Klicken Sie auf **Hinzufügen**, um die Einstellungen hinzuzufügen.
8. Klicken Sie auf **Öffnen** unten im Dialogfeld, um eine SSH-Verbindung zu öffnen.
9. Wenn Sie dazu aufgefordert werden, melden Sie sich mit einem SSH-Konto beim Server an. Dadurch wird eine SSH-Sitzung eingerichtet und der Tunnel aktiviert.

**So finden Sie den FQDN der Zookeeper mit Ambari**

1. Navigieren Sie zu "https://<ClusterName>.azurehdinsight.net/".
2. Geben Sie zwei Mal die Anmeldeinformationen für das Cluster-Benutzerkonto ein.
3. Klicken Sie im linken Menü auf **Zookeeper**.
4. Klicken Sie auf einen der drei Links mit der Bezeichnung **ZooKeeper-Server** in der Zusammenfassungsliste.
5. Kopieren Sie den Wert unter **Hostname**. Beispiel: zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**So konfigurieren Sie ein Clientprogramm (Firefox) und überprüfen den Clusterstatus**

1. Öffnen Sie Firefox.
2. Klicken Sie auf die Schaltfläche **Menü öffnen**.
3. Klicken Sie auf **Options**.
4. Klicken Sie auf **Erweitert**, klicken Sie auf **Netzwerk**, und klicken Sie dann auf **Einstellungen**.
5. Wählen Sie **Manuelle Proxy-Konfiguration** aus.
6. Geben Sie die folgenden Werte ein:

	- **SOCKS-Host**: localhost
	- **Port**: Verwenden Sie den Port, den Sie für das Putty-SSH-Tunneling konfiguriert haben. Beispiel: 9876.
	- **SOCKS v5**: (ausgewählt)
	- **Externer DNS-Server**: (ausgewählt)
7. Klicken Sie zum Speichern der Änderungen auf **OK**.
8. Navigieren Sie zu "http://<TheFQDN of a ZooKeeper>:60010/master-status".

In einem HighAvailability-Cluster gibt es einen Link zum aktuellen aktiven HBase-Masterknoten, unter dem die Web-Benutzeroberfläche gehostet wird.



## Nächste Schritte?
In diesem HBase-Tutorial für HDInsight haben Sie erfahren, wie Sie einen HBase-Cluster erstellen, Tabellen erstellen und die Daten in diesen Tabellen über die HBase-Shell anzeigen. Außerdem haben Sie gelernt, wie Sie an den Daten in HBase-Tabellen eine Hive-Abfrage ausführen und wie Sie mit den HBase C#-REST-APIs HBase-Tabellen erstellen und Daten aus diesen Tabellen abrufen.

Weitere Informationen finden Sie unter:

- [Übersicht HBase mit HDInsight][hdinsight-hbase-overview]: HBase ist eine Open-Source-NoSQL-Datenbank von Apache, die auf Hadoop aufbaut und wahlfreien Zugriff sowie starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0204_2016-->