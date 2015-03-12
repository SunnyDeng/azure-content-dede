<properties 
   pageTitle="Bereitstellen von Hadoop-Clustern unter Linux in HDInsight | Azure" 
   description="Erfahren Sie, wie Sie Hadoop-Cluster unter Linux für HDInsight über das Verwaltungsportal, die Befehlszeile und das .NET SDK bereitstellen." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>


# Benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in HDInsight (Vorschau)

Dieser Artikel beschreibt die verschiedenen Möglichkeiten für die benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in Azure HDInsight: Azure-Verwaltungsportal, PowerShell, Befehlszeilentools oder HDInsight .NET SDK.

## Was ist ein HDInsight-Cluster?

Vielleicht haben Sie sich schon gefragt, warum wir jedesmal Cluster erwähnen, wenn wir über Hadoop oder Big Data sprechen. Das liegt daran, dass Hadoop die verteilte Verarbeitung großer Datenmengen auf mehreren Knoten eines Clusters ermöglicht. Cluster haben eine Master/Detail-Architektur mit einem Primärknoten (oder Stamm- bzw. Namensknoten) und einer beliebigen Anzahl von Sekundärknoten (oder Datenknoten). Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![HDInsight Cluster][img-hdi-cluster]

HDInsight-Cluster abstrahieren die Hadoop-Implementierungsdetails, sodass Sie sich keine Gedanken über die Kommunikation zwischen den einzelnen Clusterknoten machen müssen. Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. Weitere Informationen finden Sie unter [Einführung in Hadoop in HDInsight](../hdinsight-hadoop-introduction/). Die zu verarbeitenden Daten liegen im Azure-Blob-Speicher, auch bezeichnet als  *Azure Storage - Blob* (oder WASB) im Kontext von HDInsight. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage/).

Dieser Artikel beschreibt die verschiedenen Möglichkeiten bei der Einrichtung eines Clusters. Wenn Sie schnell in der Lage sein möchten, einen Cluster bereitzustellen, lesen Sie [Erste Schritte mit Azure HDInsight unter Linux](../hdinsight-hadoop-linux-get-started).

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Die HDInsight PowerShell-Cmdlets führen Aufgaben für Ihr Abonnement aus. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter <a href="http://azure.microsoft.com/ pricing/purchase-options/" target="_blank">Kaufoptionen</a>, <a href="http://azure.microsoft.com/ pricing/member-offers/" target="_blank">Spezielle Angebote</a>oder <a href="http://azure.microsoft.com/ pricing/free-trial/" target="_blank">Kostenlose Testversion</a>.
- SSH-Schlüssel Wenn Sie mit SSH remote auf einen Linux-Cluster zugreifen möchten. Anweisungen zum Generieren von SSH-Schlüsseln finden Sie in den folgenden Artikeln:
	-  Auf einem Linux-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix).
	-  Auf einem Windows-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](../hdinsight-hadoop-linux-use-ssh-windows).

## Themen in diesem Artikel

* [Konfigurationsoptionen](#configuration)
* [Optionen für die Bereitstellung von HDInsight-Linux-Clustern](#options)
* [Nächste Schritte](#nextsteps)

## <a id="configuration"></a>Konfigurationsoptionen

### Cluster unter Linux

HDInsight bietet die Möglichkeit, Linux-Cluster unter Azure bereitzustellen. Stellen Sie einen Linux-Cluster bereit, wenn Sie mit Linux oder Unix und der Migration von einer vorhandenen Linux-basierten Hadoop-Lösung vertraut sind, oder Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert sind. Weitere Informationen zu Azure HDInsight unter Linux finden Sie unter [Einführung in Hadoop in HDInsight](../hdinsight-hadoop-introduction). 

### Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure Blob-Speicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional zusätzliche Blobs angeben, die ebenfalls Ihrem Cluster zugeordnet werden.

Weitere Informationen zu sekundären BLOB-Speichern finden Sie unter [Verwenden des Azure-BLOB-Speichers mit HDInsight](http://azure.microsoft.com/ documentation/articles/hdinsight-use-blob-storage/).

### Metastore

Der Metastore enthält Informationen über Hive-Tabellen, Partitionen, Schemas, Spalten usw.. Hive verwendet diese Informationen, um die Daten im HDFS zu lokalisieren (bzw. WASB für HDInsight). Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank.

Bei der Bereitstellung eines HDInsight-Clusters können Sie eine SQL-Datenbank angeben, die als Metastore für Hive verwendet werden soll. Auf diese Weise bleiben die Metadaten-Informationen erhalten, wenn Sie ein Cluster löschen, da diese extern in einer SQL-Datenbank liegen.

> [WACOM.NOTE] Die Option zur Verwendung von Metastores ist derzeit nur bei der Bereitstellung von HDInsight für Linux mit .NET SDK verfügbar. Anweisungen finden Sie unter [Bereitstellen von HDInsight-Clustern unter Linux mit .NET SDK](#sdk).


## <a id="options"></a> Optionen für die Bereitstellung von HDInsight-Linux-Clustern

Sie können einen Hadoop-Linux-Cluster von HDInsight sowohl über einen Linux- als auch über einen Windows-Computer bereitstellen. Die folgende Tabelle enthält Informationen über die Optionen für die Bereitstellung, die für die verschiedenen Betriebssystemen verfügbar sind, sowie Links zu den entsprechenden Anweisungen.

Bereitstellen von Linux-Clustern über einen Computer, der dieses Betriebssystem ausführt| Verwenden des Azure-Verwaltungsportals | Verwenden der plattformübergreifenden Befehlszeilenschnittstelle | Verwenden von .NET SDK | Verwenden von PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Klicken Sie [hier](#portal) | Klicken Sie [hier](#cli)| Nicht zutreffend | In Kürze verfügbar
Windows | Klicken Sie [hier](#portal) | Klicken Sie [hier](#cli) | Klicken Sie [hier](#sdk) | In Kürze verfügbar

### <a id="portal"></a> Verwenden des Azure-Verwaltungsportals

HDInsight-Cluster verwenden einen Azure-Blobspeicher-Container als Standard-Dateisystem. Sie benötigen ein Speicherkonto im entsprechenden Rechenzentrum, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Verwenden von Azure BLOB-Speicher mit HDInsight][hdinsight-storage]. Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos][azure-create-storageaccount].


> [WACOM.NOTE] HDInsight-Cluster sind derzeit nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Mitte/Norden)** und **USA (Mitte/Süden)** verfügbar.

**So erstellen Sie einen HDInsight-Cluster mit der benutzerdefinierten Erstellungsoption**

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie im unteren Seitenbereich auf **+ NEU**, und dann auf **DATENDIENSTE** und auf **HDINSIGHT**, und wählen Sie zuletzt **BENUTZERDEFINIERT ERSTELLEN**.
3. Auf der Seite mit den **Cluster-Details** können Sie die folgenden Werte auswählen bzw. eingeben:

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Clustername</td>
			<td><p>Der Name des Clusters. </p>
				<ul>
				<li>Der DNS-Name muss mit einem alphanumerischen Zeichen beginnen und enden und darf Bindestriche enthalten.</li>
				<li>Der Wert in diesem Feld muss zwischen 3 und 63 Zeichen lang sein.</li>
				</ul></td></tr>
		<tr><td>Clustertyp</td>
			<td>Wählen Sie unter "Clustertyp" <strong>Hadoop</strong>.</td></tr>
		<tr><td>Betriebssystem</td>
			<td>Wählen Sie <b>Ubuntu 12.04 LTS Preview</b> aus, um HDInsight-Cluster unter Linux bereitzustellen. Weitere Informationen zum Bereitstellen eines Windows-Clusters finden Sie unter <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-provision-clusters/" target="_blank">Bereitstellen von Hadoop-Clustern unter Windows in HDInsight</a>.</td></tr>
		<tr><td>HDInsight-Version</td>
			<td>Wählen Sie die Version aus. Für HDInsight unter Linux ist HDInsight Version 3.2 die Standardversion, die Hadoop Version 2.5 verwendet.</td></tr>
		</table>

	Geben Sie die Werte wie in der Tabelle gezeigt ein und klicken Sie auf den Pfeil nach rechts.

4. Auf der Seite **Cluster konfigurieren**  können Sie die folgenden Werte auswählen bzw. eingeben:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Datenknoten</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten. <br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td></tr>
	<tr><td>Region/virtuelles Netzwerk</td><td><p>Wählen Sie dieselbe Region wie für das Speicherkonto, das Sie im letzten Verfahren erstellt haben. In HDInsight muss das Speicherkonto sich in derselben Region befinden. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet.</p><p>Die verfügbaren Regionen sind: <strong>Ostasien</strong>, <strong>Südostasien</strong>, <strong>Nordeuropa</strong>, <strong>Westeuropa</strong>, <strong>Osten USA</strong>, <strong>Westen USA</strong>, <strong>USA (Mitte/Norden)</strong>, <strong>USA (Mitte/Süden)</strong><br/></p></td></tr>
	</table>



5. Auf der Seite **Clusterbenutzer konfigurieren** können Sie die folgenden Werte auswählen bzw. eingeben:

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>HTTP-Kennwort</td>
			<td>Geben Sie das Kennwort für den Standard-HTTP-Benutzer ein: <i>admin</i>.</td></tr>
		<tr><td>SSH-Benutzername</td>
			<td>Geben Sie den SSH-Benutzernamen an. Sie verwenden diesen Benutzernamen zum Initiieren einer SSH-Remotesitzung auf den HDInsight-Clusterknoten.</td></tr>
		<tr><td>SSH-Authentifizierungstyp</td>
			<td>Geben Sie an, ob Sie ein Kennwort oder einen SSH-Schlüssel verwenden möchten, um einen SSH-Benutzer zu authentifizieren.</td></tr>
		<tr><td>SSH-Kennwort</td>
			<td>Wenn Sie ein Kennwort als Authentifizierungstyp ausgewählt haben, geben Sie das SSH-Kennwort zum Authentifizieren eines SSH-Benutzers an. Wenn Sie versuchen, eine SSH-Sitzung auf dem Linux-Remotecomputer zu initiieren, werden Sie zur Eingabe dieses Kennworts aufgefordert.</td></tr>
		<tr><td>Öffentlicher SSH-Schlüssel</td>
			<td>Wenn Sie einen Schlüssel als Authentifizierungstyp ausgewählt haben, geben Sie den öffentlichen SSH-Schlüssel an, den Sie bereits generiert haben. Wenn Sie eine SSH-Sitzung mit einem Hinweis im Linux-Cluster initiieren, verwenden Sie den privaten Schlüssel, der diesem öffentlichen Schlüssel zugeordnet ist.<br>
			Anweisungen zum Generieren eines SSH-Schlüssels auf einem Linux-Computer finden Sie <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">hier</a>. Anweisungen zum Generieren eines SSH-Schlüssels auf einem Windows-Computer finden Sie <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">hier</a>.
		</td></tr>
		</table>

	> [WACOM.NOTE] Es wird empfohlen, die Authentifizierung mit einem öffentlichen SSH-Schlüssel zu verwenden, da diese sicherer ist als die Kennwortauthentifizierung.

	Klicken Sie auf den Pfeil nach rechts.


6. Auf der Seite **Speicherkonto** können Sie die folgenden Werte auswählen bzw. eingeben:

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Speicherkonto</td>
			<td>Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten:
			<ul>
				<li>Vorhandenen Speicher verwenden</li>
				<li>Neuen Speicher erstellen</li>
				<li>Speicher aus anderem Abonnement verwenden</li>
			</ul>
			</td></tr>
		<tr><td>Kontoname</td>
			<td><ul>
				<li>Falls Sie einen vorhandenen Speicher verwenden, wählen Sie unter <strong>"Kontoname"</strong>ein vorhandenes Speicherkonto aus. Die Dropdownliste enthält nur die Speicherkonten im gleichen Rechenzentrum, in dem Sie auch den Cluster eingerichtet haben.</li>
				<li>Wenn Sie <strong>"Neuen Speicher erstellen"</strong> oder <strong>"Speicher aus anderem Abonnement verwenden"</strong> wählen, müssen Sie den Speicherkontonamen angeben.</li>
			</ul></td></tr>
		<tr><td>Kontoschlüssel</td>
			<td>Wenn Sie <strong>"Speicher aus anderem Abonnement verwenden"</strong> wählen, geben Sie den Kontoschlüssel für dieses Speicherkonto an.</td></tr>
		<tr><td>Standardcontainer</td>
			<td><p>Gibt den Standardcontainer im Speicherkonto an, der als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Wenn Sie <strong>"Vorhandenen Speicher verwenden"</strong> für das Feld <strong>Speicherkonto</strong> ausgewählt haben und dieses Konto keine Container enthält, wird der Container standardmäßig mit demselben Namen wie der Cluster erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt. Zum Beispiel mycontainer1, mycontainer2 und so weiter. Sie können jedoch auch Container im vorhandenen Speicherkonto verwenden, die einen anderen Namen als der Cluster haben.</p>
            <p>Falls Sie einen neuen Speicher erstellen oder einen Speicher aus einem anderen Azure-Abonnement verwenden, müssen Sie den Namen des Standardcontainers angeben</p>
        </td></tr>
		<tr><td>Zusätzliche Speicherkonten</td>
			<td>HDInsight unterstützt mehrere Speicherkonten. Ein Cluster kann beliebig viele Speicherkonten verwenden. Wenn Sie den Cluster jedoch im Verwaltungsportal erstellen, können Sie aufgrund von Einschränkungen der Benutzeroberfläche maximal sieben Speicherkonten einrichten. Für jedes angegebene Speicherkonto wird eine zusätzliche Seite im Assistenten hinzugefügt, in der Sie die Kontoinformationen angeben können. Im obigen Screenshot wurde z. B. ein zusätzliches Speicherkonto ausgewählt, und Seite 5 wurde zum Dialog hinzugefügt.</td></tr>
	</table>

	Klicken Sie auf den Pfeil nach rechts.

7. Wenn Sie für den Cluster zusätzlichen Speicher konfigurieren möchten, geben Sie auf der Seite **Speicherkonto** die Kontoinformationen für das zusätzliche Speicherkonto ein:

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

    Hier haben Sie erneut die Auswahl zwischen einem vorhandenen Speicher, einem neu erstellten Speicher und einem Speicher aus einem anderen Azure-Abonnement. Die Angabe der Werte erfolgt auf dieselbe Weise wie im vorherigen Schritt.

    > [WACOM.NOTE] Nachdem Sie ein Azure-Speicherkonto für Ihren HDInsight-Cluster ausgewählt haben, können Sie das Konto nicht mehr löschen und auch kein anderes Konto auswählen.

 	Nachdem Sie die zusätzlichen Speicherkonten angegeben haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten. 

### <a id="cli"></a> Verwenden der plattformübergreifenden Befehlszeile

> [WACOM.NOTE] Ab dem 29.8.2014 kann die plattformübergreifende Befehlszeilenschnittstelle nicht verwendet werden, um ein Cluster zu einem virtuellen Azure-Netzwerk zuzuordnen.

Eine weitere Möglichkeit zur Bereitstellung von HDInsight-Clustern ist die plattformübergreifende Befehlszeile. Das Befehlszeilentool ist in Node.js implementiert. Dieses Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux. Das Befehlszeilentool ist Open Source.  Der Quellcode wird auf GitHub unter <a href= "https://github.com/Azure/azure-sdk-tools-xplat">"https://github.com/Azure/azure-sdk-tools-xplat" verwaltet</a>. Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux][azure-command-line-tools]. Umfassendes Referenzmaterial finden Sie unter [Azure-Befehlszeilentool für Mac und Linux][azure-command-line-tool].

- [Einrichten der plattformübergreifenden Azure-Befehlszeile für Linux](#clilin)
- [Einrichten der plattformübergreifenden Azure-Befehlszeile für Windows](#cliwin)
- [Bereitstellen eines HDInsight-Clusters mithilfe der plattformübergreifenden Azure-Befehlszeile](#cliprovision)

#### <a id="clilin"></a>Einrichten der plattformübergreifenden Befehlszeile für Linux

Führen Sie die folgenden Verfahren zum Einrichten des Linux-Computers aus, um Azure-Befehlszeilentools zu verwenden.

- Installieren der plattformübergreifenden Befehlszeile mithilfe von NPM
- Verbinden mit Ihrem Azure-Abonnement

**So installieren Sie die Befehlszeilenschnittstelle mit dem NPM**

1.	Öffnen Sie auf dem Linux-Computer ein Terminalfenster, und führen Sie den folgenden Befehl aus:

		sudo npm install -g azure-cli

2.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter *-h* auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Verbinden mit Ihrem Azure-Abonnement**

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [WACOM.NOTE] Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker. 


1.	Öffnen Sie ein Terminalfenster.
2.	Führen Sie den folgenden Befehl aus, um sich bei Ihrem Azure-Abonnement anzumelden:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können. Wenn die Webseite nicht geöffnet wird, klicken Sie auf den Link im Terminalfenster, um die Browserseite zu öffnen und sich beim Portal anzumelden. 

3.	Laden Sie die Datei mit den Veröffentlichungseinstellungen auf den Computer herunter.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Einrichten der plattformübergreifenden Befehlszeile für Windows

Führen Sie die folgenden Verfahren zum Einrichten des Windows-Computers aus, um Azure-Befehlszeilentools zu verwenden.

- Installieren der plattformübergreifenden Befehlszeile (mithilfe von NPM oder Windows Installer)
- Download und Import der Veröffentlichungseinstellungen für das Azure-Konto


Sie können die Befehlszeilenschnittstelle entweder über *Node.js Package Manager (NPM)* oder mithilfe des Windows Installer installieren. Microsoft empfiehlt, dass Sie nur eine der beiden Optionen für die Installation verwenden.

**So installieren Sie die Befehlszeilenschnittstelle mit dem NPM**

1.	Navigieren Sie zu **www.nodejs.org**.
2.	Klicken Sie auf **Installieren**, und befolgen die Anweisungen mit den Standardeinstellungen.
3.	Öffnen Sie die **Eingabeaufforderung** (oder *Azure Command Prompt* oder *Developer Command Prompt for VS2012*) von Ihrer Arbeitsstation aus.
4.	Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein.

		npm install -g azure-cli

	> [WACOM.NOTE] Falls Sie einen Fehler erhalten und der NPM-Befehl nicht gefunden wird, vergewissern Sie sich, dass die PATH-Umgebungsvariable die folgenden Pfade enthält: <i>C:\Programme (x86)\nodejs;C:\Users\[Benutzername]\AppData\Roaming\npm</i> oder <i>C:\Programme\nodejs;C:\Users\[Benutzername]\AppData\Roaming\npm</i>

5.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter *-h* auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So installieren Sie die Befehlszeilenschnittstelle mit dem Windows Installer**

1.	Navigieren Sie zur Webseite **http://azure.microsoft.com/de-de/downloads/**.
2.	Blättern Sie nach unten zum Bereich **Befehlszeilentools**, klicken Sie auf **Plattformübergreifende Befehlszeilenschnittstelle**, und führen Sie den Webplattform-Installer aus.

**So laden Sie Veröffentlichungseinstellungen herunter und importieren sie**

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [WACOM.NOTE] Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.


1.	Öffnen Sie eine **Eingabeaufforderung**.
2.	Führen Sie den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei herunterzuladen.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können.

3.	Klicken Sie nach Aufforderung zum Speichern der Datei auf **Speichern**, und geben Sie einen Speicherort für die Datei an.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>

	
#### <a id="cliprovision"></a>Bereitstellen eines HDInsight-Clusters mithilfe der plattformübergreifenden Azure-Befehlszeile

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters in der plattformübergreifenden Befehlszeile ausgeführt werden:

- Erstellen eines Azure-Speicherkontos
- Bereitstellen eines Clusters


**So erstellen Sie ein Azure-Speicherkonto**

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum befinden.

- Führen Sie den folgenden Befehl in einem Eingabeaufforderungsfenster aus, um ein Azure-Speicherkonto zu erstellen:

		azure storage account create [options] <StorageAccountName>

	Wählen Sie einen geeigneten Ort für die Bereitstellung des HDInsight-Clusters aus, wenn Sie dazu aufgefordert werden. Der Speicher muss sich am gleichen Standort befinden wie der HDInsight-Cluster. HDInsight-Cluster sind derzeit nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Mitte/Norden)** und **USA (Mitte/Süden)** verfügbar.  

Informationen zum Erstellen eines Azure-Speicherkontos im Azure-Verwaltungsportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

	-- Auflisten von Speicherkonten
	azure storage account list

	-- Anzeigen von Informationen für ein Speicherkonto
	azure storage account show <Speicherkontoname>

	-- Auflisten der Schüssel für ein Speicherkonto
	azure storage account keys list <Speicherkontoname>

Details zum Abrufen der Informationen mit dem Verwaltungsportal finden Sie im *How to: View, copy and regenerate storage access keys*-Abschnitt von [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storageaccount].

Für Ihren HDInsight-Cluster benötigen Sie außerdem einen Container innerhalb des Speicherkontos. Falls das angegebene Speicherkonto noch keinen Container enthält, werden Sie von  *azure hdinsight cluster create* zur Eingabe eines Containernamens aufgefordert, und der Container wird erstellt. Sie können den folgenden Befehl verwenden, um den Container vorab zu erstellen:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie ein HDInsight-Cluster bereit**

- Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword> --OSType Linux --SshUserName <SSH username> --SshPassword <SSH user password>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**So stellen Sie einen HDInsight-Cluster mit einer Konfigurationsdatei bereit**

Normalerweise erstellen Sie einen HDInsight-Cluster, führen Ihre Jobs aus und löschen den Cluster anschließend, um Kosten zu sparen. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

- Führen Sie im Eingabeaufforderungsfenster die folgenden Befehle aus:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>" --OSType Linux --SshUserName "<SSH username>" --SshPassword <SSH user password>

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Azure SQL-Datenbank-Dashboard mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Windows Azure-Dienste** den Wert **Ja** aus, und klicken Sie dann auf **Speichern**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**So listen Sie Clusterdetails auf und zeigen sie an**

- Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**So löschen Sie ein Cluster**

- Mit dem folgenden Befehl können Sie einen Cluster löschen:

		azure hdinsight cluster delete <ClusterName>



### <a id="sdk"></a> Verwenden des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen.

Führen Sie die folgenden Schritte aus, um einen HDInsight-Cluster unter Linux mithilfe des SDK bereitzustellen:

- Installation des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) herunterladen und installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Anweisungen finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013.

2. Klicken Sie im Menü "Datei" auf **Neu** und anschließend auf **Projekt**.

3. Unter "Neues Projekt" können Sie die folgenden Werte eingeben bzw. auswählen:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Fügen Sie in der Main()-Funktion den folgenden Code ein:

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		
		//if required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores
		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));
        

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Ersetzen Sie die Variablen am Anfang der main()-Funktion.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio, und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.



## <a id="nextsteps"></a> Nächste Schritte
In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters (Hadoop) unter Linux kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Arbeiten mit HDInsight unter Linux](../hdinsight-hadoop-linux-information) Lernen Sie die Abstufungen bei der Arbeit mit einem HDInsight-Cluster unter Linux kennen.
- [Verwalten von HDInsight-Clustern mit Ambari](../hdinsight-hadoop-manage-ambari) Erfahren Sie, wie Sie Ihren Linux-basierten Hadoop-Cluster in HDInsight mithilfe von Ambari Web oder der Ambari-REST-API überwachen und verwalten. 
- [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce] Erhalten Sie Informationen zu den verschiedenen Möglichkeiten zum Ausführen von MapReduce-Aufträgen auf einem Cluster.
- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]. Erhalten Sie Informationen zu den verschiedenen Möglichkeiten zum Ausführen einer Hive-Abfrage auf einem Cluster.
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]. Erhalten Sie Informationen zu den verschiedenen Möglichkeiten zum Ausführen eines Pig-Auftrags auf einem Cluster.
- [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage) Erfahren Sie, wie HDInsight Azure Blob-Speicher zum Speichern von Daten für HDInsight-Cluster verwendet.
- [Hochladen von Daten in HDInsight][hdinsight-upload-data] Erfahren Sie, wie Sie mit Daten arbeiten, die in Azure Blob-Speicher für einen HDInsight-Cluster gespeichert werden.

[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/ documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/ documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /de-de/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=45--> 
