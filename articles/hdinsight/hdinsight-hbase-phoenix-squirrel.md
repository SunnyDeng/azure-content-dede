<properties 
   pageTitle="Verwenden von Apache Phoenix und SQuirreL in HDInsight | Microsoft Azure" 
   description="Erfahren Sie, wie Sie Apache Phoenix in HDInsight verwenden und wie Sie SQuirreL auf Ihrer Arbeitsstation für die Verbindung mit einem HBase-Cluster in HDInsight installieren und konfigurieren." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/29/2015"
   ms.author="jgao"/>

# Verwenden von Apache Phoenix und SQuirreL mit HBase-Clustern in HDInsight  

Erfahren Sie, wie Sie [Apache Phoenix](http://phoenix.apache.org/) in HDInsight verwenden und wie Sie SQuirreL auf Ihrer Arbeitsstation für die Verbindung mit einem HBase-Cluster in HDInsight installieren und konfigurieren. Weitere Informationen zu Phoenix finden Sie unter [Phoenix innerhalb von 15 Minuten oder weniger](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Eine Beschreibung der Phoenix-Grammatik finden Sie unter [Phoenix-Grammatik](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE]Versionshinweise für Phoenix finden Sie in HDInsight unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen?][hdinsight-versions]

##Verwendung von SQLLine
[SQLLine](http://sqlline.sourceforge.net/) ist ein Befehlszeilenprogramm zum Ausführen von SQL.

###Voraussetzungen
Bevor Sie SQLLine verwenden können, benötigen Sie Folgendes:

- **Ein HBase-Cluster in HDInsight**. Informationen zur Bereitstellung von HBase-Cluster finden Sie unter [Erste Schritte mit Apache in HDInsight][hdinsight-hbase-get-started].
- **Verbinden Sie sich per Remotedesktop-Protokoll mit dem HBase-Cluster**. Anweisungen finden Sie in [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][hdinsight-manage-portal].

**Herausfinden des Hostnamen**

1. Öffnen Sie die **Hadoop-Befehlszeile** auf dem Desktop.
2. Führen Sie den folgenden Befehl aus, um das DNS-Suffix abzurufen:

		ipconfig

	Notieren Sie sich die **verbindungsspezifische DNS-Endung**. Zum Beispiel *myhbasecluster.f5.internal.cloudapp.net*. Beim Verbinden mit einem HBase-Cluster müssen Sie die Verbindung zu einem der Zookeeper mit FQDN herstellen. Jeder HDInsight-Cluster verfügt über 3 Zookeeper. Diese sind *zookeeper0*, *zookeeper1*, und *zookeeper2*. Der FQDN in etwa so aussehen *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Verwendung von SQLLine**

1. Öffnen Sie die **Hadoop-Befehlszeile** auf dem Desktop.
2. Führen Sie die folgenden Befehle aus, um SQLLine zu öffnen:

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![HdInsight Hbase Phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

	Die im Beispiel verwendeten Befehle:

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
		
		!tables;
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;

Weitere Informationen finden Sie im [SQLLine-Handbuch](http://sqlline.sourceforge.net/#manual) sowie unter [Phoenix-Grammatik](http://phoenix.apache.org/language/index.html).


















##Verwenden von SQuirreL

[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/) ist ein grafisches Java-Programm, mit dem Sie die Struktur einer JDBC-kompatiblen Datenbank anzeigen, Daten in Tabellen durchsuchen, SQL-Befehlen ausgeben usw. können. Es kann für die Verbindung mit Apache Phoenix in HDInsight verwendet werden.

In diesem Abschnitt erfahren Sie, wie Sie SQuirreL auf Ihrer Arbeitsstation über VPN für die Verbindung mit einem HBase-Cluster in HDInsight installieren und konfigurieren.

###Voraussetzungen

Bevor Sie die Vorgehensweise befolgen, müssen Sie über Folgendes verfügen:

- Ein HBase-Cluster das auf einem virtuellen Azure-Netzwerk mit einem virtuellen DNS-Computer bereitgestellt wird. Anweisungen finden Sie unter [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet]. 

	>[AZURE.IMPORTANT]Sie müssen einen DNS-Server auf dem virtuellen Netzwerk installieren. Anweisungen finden Sie unter [Konfigurieren von DNS zwischen zwei virtuellen Netzwerken in Azure](hdinsight-hbase-geo-replication-configure-DNS.md).

- Rufen Sie das verbindungsspezifische DNS-Suffix des HBase-Clusters ab. Rufen Sie dazu mittels RDP den Cluster auf und führen Sie IPConfig aus. Das DNS-Suffix ähnelt:

		myhbase.b7.internal.cloudapp.net
- Laden Sie [Microsoft Visual Studio Express 2013 für Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) herunter und installieren Sie es auf Ihrer Arbeitsstation. Sie benötigen Makecert aus dem Paket, um Ihr Zertifikat zu erstellen.  
- Laden Sie [Java Laufzeitumgebung](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) herunter und installieren Sie es auf Ihrer Arbeitsstation. SQuirreL SQL-Client Version 3.0 und höher erfordert JRE-Version 1.6 oder höher.  


###Konfigurieren Sie eine Punkt-zu-Standort-VPN-Verbindung mit dem virtuellen Azure-Netzwerk

Die Konfiguration einer Punkt-zu-Standort-VPN-Verbindung umfasst drei Schritte:

1. [Konfigurieren eines virtuellen Netzwerks und eines dynamischen Routing-Gateway](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Erstellen Ihrer Zertifikate](#Create-your-certificates)
3. [Konfigurieren Ihres VPN-Clients](#Configure-your-VPN-client)

Siehe [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem virtuellen Azure Netzwerk](../vpn-gateway/vpn-gateway-point-to-site-create.md) für weitere Informationen.

#### Konfigurieren eines virtuellen Netzwerks und eines dynamischen Routing-Gateways

Stellen Sie sicher, dass Sie einen HBase-Cluster in einem virtuellen Azure Netzwerk bereitgestellt haben (siehe die Voraussetzungen für diesen Abschnitt). Der nächste Schritt die Konfiguration einer Punkt-zu-Standort-Verbindung.

**So konfigurieren Sie die Punkt-zu-Standort-Konnektivität**

1. Melden Sie sich auf dem [Azure-Portal][azure-portal] an.
2. Klicken Sie auf der linken Seite auf **NETZWERKE**.
3. Klicken Sie auf das virtuelle Netzwerk, das Sie erstellt haben (siehe [Bereitstellen von HBase-Clustern im virtuellen Azure Netzwerk][hdinsight-hbase-provision-vnet]).
4. Klicken Sie oben auf **KONFIGURIEREN** .
5. Im Abschnitt **Punkt-zu-Standort-Konnektivität** wählen Sie **Konfiguration Punkt-zu-Standort-Konnektivität** aus. 
6. Konfigurieren Sie **START-IP** und **CIDR**, um den IP-Adressbereich anzugeben, von dem Ihre VPN-Clients eine IP-Adresse erhalten, wenn eine Verbindung besteht. Der Bereich darf nicht mit anderen Bereichen überlappen, die sich auf Ihrem lokalen Netzwerk und dem virtuellen Azure Netzwerk befinden, mit dem Sie eine Verbindung herstellen werden. Wenn Sie zum Beispiel 10.0.0.0/20 für das virtuelle Netzwerk ausgewählt haben, können Sie 10.1.0.0/24 für den Client-Adressraum auswählen. Siehe Seite [Punkt-zu-Standort-Konnektivität][vnet-point-to-site-connectivity] für weitere Informationen.
7. Klicken Sie auf **Gateway-Subnetz hinzufügen** im Abschnitt Adressräume des virtuellen Netzwerks.
7. Klicken Sie unten auf der Seite auf **SPEICHERN**.
8. Klicken Sie auf **JA**, um die Änderung zu bestätigen. Warten Sie, bis das System die Änderung vorgenommen hat, bevor Sie mit dem nächsten Schritt fortfahren.


**Erstellen eines dynamischen Routing-Gateways**

1. Klicken Sie im Azure-Portal oben auf der Seite auf **DASHBOARD**.
2. Klicken Sie unten auf der Seite auf **GATEWAY ERSTELLEN**.
3. Klicken Sie auf **JA**, um zu bestätigen. Warten Sie, bis das Gateway erstellt wurde.
4. Klicken Sie Fall oben auf **DASHBOARD**. Es wird eine visuelle Darstellung des virtuellen Netzwerks angezeigt:

	![Virtuelles Azure-Netzwerk virtuelle Punkt-zu-Standort-Darstellung][img-vnet-diagram]

	Die Darstellung zeigt 0 Clientverbindungen. Nachdem Sie eine Verbindung mit dem virtuellen Netzwerk hergestellt haben, wird die Anzahl auf eins aktualisiert.

#### Erstellen Ihrer Zertifikate

Eine Möglichkeit zum Erstellen eines X.509-Zertifikats ist mithilfe des Tools zur Erstellung von Zertifikaten (makecert.exe), das im Lieferumfang von [Microsoft Visual Studio Express 2013 für Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) enthalten ist.


**So erstellen Sie ein selbstsigniertes Stammzertifikat**

1. Öffnen Sie auf Ihrer Arbeitsstation ein Eingabeaufforderungsfenster.
2. Navigieren Sie zum Ordner „Visual Studio-Tools“. 
3. Der folgende Befehl im Beispiel unten erstellt und installiert ein Stammzertifikat im persönlichen Zertifikatsspeicher auf Ihrer Arbeitsstation und erstellt außerdem eine entsprechende .cer-Datei, die Sie später in der Azure-Portal hochladen. 

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	Wechseln Sie zu dem Verzeichnis, in dem die .cer-Datei sich befinden soll, wobei HBaseVnetVPNRootCertificate der Name ist, den Sie für das Zertifikat verwenden sollten.

	Schließen Sie die Eingabeaufforderung nicht. Sie benötigen diese im nächsten Verfahren.

	>[AZURE.NOTE]Da Sie ein Stammzertifikat erstellt haben, aus dem Client-Zertifikate generiert werden, ist es empfehlenswert, dass Sie dieses Zertifikat zusammen mit dessen privaten Schlüssel exportieren und an einem sicheren Ort speichern, von dem es wiederhergestellt werden kann.

**Erstellen eines Client-Zertifikats**

- Aus der gleichen Befehlszeile (Muss auf demselben Computer sein, auf dem das Stammzertifikat erstellt wurde. Das Client-Zertifikat muss aus dem Stammzertifikat generiert werden) den folgenden Befehl ausführen:

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate ist der Name des Stammzertifikats. Sein Wert muss mit dem Namen des Stammzertifikats übereinstimmen.

	Sowohl das Stammzertifikat als auch das Clientzertifikat werden in Ihrem persönlichen Zertifikatspeicher auf Ihrem Computer gespeichert. Verwenden Sie zur Überprüfung den Zertifikatmanager "certmgr.msc".

	![Punkt-zu-Standort-VPN-Zertifikat für virtuelles Azure-Netzwerk][img-certificate]

	Auf jedem Computer, den Sie mit dem virtuellen Netzwerk verbinden möchten,muss ein Clientzertifikat installiert werden. Es wird empfohlen, dass Sie für alle Computer, die Sie mit dem virtuellen Netzwerk verbinden möchten, jeweils eindeutige Clientzertifikate erstellen. Verwenden Sie zum Exportieren der Clientzertifikate den Zertifikatmanager "certmgr.msc".

**Hochladen de Stammzertifikats in der Azure-Portal**

1. Klicken Sie im Azure-Portal links auf **NETZWERK**.
2. Klicken Sie auf das virtuelle Netzwerk, in das Ihr HBase Cluster bereitgestellt wird.
3. Klicken Sie oben auf **ZERTIFIKATE**.
4. Klicken Sie unten auf **HOCHLADEN** und bestimmen Sie die Stammzertifikatdatei, die Sie im vorletzten Verfahren erstellt haben. Warten Sie, bis das Zertifikat importiert wurde.
5. Klicken Sie oben auf **DASHBOARD**. Die virtuelle Darstellung zeigt den Status an.


#### Konfigurieren Ihres VPN-Clients



**Herunterladen und installieren des Client-VPN-Pakets**

1. Klicken Sie auf der Seite „DASHBOARD“ des virtuellen Netzwerks im Abschnitt „Auf einen Blick“ ja nach BS-Version Ihrer Arbeitsstation auf **64-Bit-Client-VPN-Paket herunterladen** oder **32-Bit-Client-VPN-Paket herunterladen**.
2. Klicken Sie auf **Ausführen** um das Paket zu installieren.
3. Klicken Sie beim Sicherheitshinweis auf **Weitere Informationen** und dann auf **Trotzdem ausführen**.
4. Klicken Sie zwei Mal auf **Ja**.

**Verbindung mit VPN herstellen**

1. Klicken Sie auf dem Desktop Ihrer Arbeitsstation auf das Netzwerk-Symbol in der Taskleiste. Sie sehen eine VPN-Verbindung mit dem Namen Ihres virtuellen Netzwerks.
2. Klicken Sie auf den Namen der VPN-Verbindung.
3. Klicken Sie auf **Verbinden**.

**Testen der VPN-Verbindung und die Auflösung des Domänennamens**

- Öffnen Sie eine Eingabeaufforderung von der Arbeitsstation und pingen Sie einen der folgenden Namen, vorausgesetzt, dass das DNS-Suffix des HBase Clusters myhbase.b7.internal.cloudapp.net ist:

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

###Installieren und Konfigurieren von SQuirreL auf Ihrer Arbeitsstation

**Installieren von SQuirreL**

1. Laden Sie die SQuirreL SQL Client-JAR-Datei von [http://squirrel-sql.sourceforge.net/\#installation](http://squirrel-sql.sourceforge.net/#installation) herunter.
2. Die Jar-Datei öffnen/ausführen. Hierzu ist die [Java-Laufzeitumgebung](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) erforderlich.
3. Klicken Sie zwei Mal auf **Weiter**.
4. Geben Sie einen Pfad an, an dem die Schreibberechtigung besitzen und klicken Sie dann auf **Weiter**.
	>[AZURE.NOTE]Der Standardinstallationsordner ist im Ordner „C:\\Programme\\Microsoft Files\\squirrel-Sql-3.6“. Um in diesen Pfad schreiben zu können, müssen dem Installationsprogramm Administratorrechte gewährt werden. Sie können eine Eingabeaufforderung als Administrator öffnen, zum Ordner „Bin“ von Java navigieren und dann ausführen
	>
	>     java.exe -jar [the path of the SQuirreL jar file] 
5. Klicken Sie auf **OK**, um das Zielverzeichnis zu erstellen.
6. Die Standardeinstellung ist die Installation der Basis- und Standardpakete. Klicken Sie auf **Weiter**.
7. Klicken Sie zwei Mal auf **Weiter** und anschließend auf **Fertig**.


**Installation der Phoenix-Treiber**

Die Phoenix-Treiber JAR-Datei befindet sich im HBase Cluster. Der Pfad ist je nach Version dem folgenden ähnlich:

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Sie müssen diesen unter [SQuirreL-Installationsordner]/LIB-Pfad auf Ihre Arbeitsstation kopieren. Am einfachsten ist es, ihn mit RDP in den Cluster zu bewegen und die Datei  
dann zu kopieren und auf Ihrer Arbeitsstation abzulegen (STRG + C und  
STRG + V).

**Hinzufügen eines Phoenix-Treibers zu SQuirreL**

1. Öffnen Sie den SQuirreL SQL-Client von Ihrer Arbeitsstation.
2. Klicken Sie links auf die Registerkarte **Treiber**.
2. Klicken Sie im Menü **Treiber** auf **Neuer Treiber**.
3. Geben Sie Folgendes ein:

	- **Name**: Phoenix
	- **Beispiel-URL**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **Klassenname**: org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING]Verwenden Sie in der Beispiel-URL nur Kleinbuchstaben. Falls eines von diesen ausgefallen ist, können Sie das vollständige Zookeeper-Quorum verwenden. Die Hostnamen sind zookeeper0, zookeeper1 und zookeeper2.

	![HDInsight HBase SQuirreL-Treiber][img-squirrel-driver]
4. Klicken Sie auf **OK**.

**Erstellen eines Alias für den HBase Cluster**

1. Klicken Sie in SQuirreL auf der linken Seite auf die Registerkarte **Aliase**.
2. Klicken Sie im Menü **Aliase** auf **Neuer Alias**.
3. Geben Sie Folgendes ein:

	- **Name**: Der Name des HBase Clusters oder jeder beliebige Namen, die Sie bevorzugen.
	- **Treiber**: Phoenix. Dieser muss mit dem Namen des Treibers übereinstimmen, den Sie im letzten Verfahren erstellt haben.
	- **URL**: Die URL wird aus der Treiberkonfiguration kopiert. Stellen Sie sicher, dass Sie ausschließlich Kleinbuchstaben verwenden.
	- **Benutzername**: Beliebiger Text. Da Sie in diesem Beispiel VPN-Konnektivität verwenden, wird der Benutzername nicht verwendet.
	- **Kennwort**: Beliebiger Text.

	![HDInsight HBase SQuirreL-Treiber][img-squirrel-alias]
4. Klicken Sie auf **Test**. 
5. Klicken Sie auf **Verbinden**. Nach dem Aufbau der Verbindung sieht SQuirreL wie folgt aus:

	![HBase Phoenix SQuirreL][img-squirrel]

**Ausführen eines Tests**

1. Klicken Sie auf die Registerkarte **SQL** rechts neben der Registerkarte **Objekte**.
2. Kopieren und fügen Sie den folgenden Code ein:

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Klicken Sie auf die Schaltfläche „Ausführen“.

	![HBase Phoenix SQuirreL][img-squirrel-sql]
4. Wechseln Sie zurück zur Registerkarte **Objekte**.
5. Erweitern Sie zuerst den Aliasnamen und dann **TABELLE**. Sie sehen nun die neue Tabelle aufgeführt.
 
##Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie Apache Phoenix in HDInsight verwenden. Weitere Informationen finden Sie unter

- [Übersicht HBase mit HDInsight][hdinsight-hbase-overview]\: HBase ist eine Open-Source-NoSQL-Datenbank von Apache, die auf Hadoop aufbaut und wahlfreien Zugriff sowie starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet.
- [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet]\: Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können.
- [Konfiguration von HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md): Erfahren Sie, wie Sie HBase-Replikation über zwei Azure-Rechenzentren konfigurieren. 
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment]\: Erfahren Sie, wie Sie in Echtzeit [Stimmungsanalysen](http://en.wikipedia.org/wiki/Sentiment_analysis) bei Big Data mithilfe von HBase in einem Hadoop-Cluster in HDInsight durchführen können.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=August15_HO8-->