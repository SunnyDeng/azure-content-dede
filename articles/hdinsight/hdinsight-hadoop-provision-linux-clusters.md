<properties
   pageTitle="Bereitstellen von Hadoop-Clustern unter Linux in HDInsight | Microsoft Azure"
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
   ms.date="04/17/2015"
   ms.author="nitinme"/>


#Benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in HDInsight (Vorschau)

Dieser Artikel beschreibt die verschiedenen Möglichkeiten für die benutzerdefinierte Bereitstellung eines Hadoop-Linux-Clusters in Azure HDInsight: Azure-Portal, Azure PowerShell, Befehlszeilenschnittstelle von Azure (Azure-CLI) oder HDInsight .NET SDK.

## Was ist ein HDInsight-Cluster?

Vielleicht haben Sie sich schon gefragt, warum wir jedes Mal Cluster erwähnen, wenn wir über Hadoop oder Big Data sprechen. Das liegt daran, dass Hadoop die verteilte Verarbeitung großer Datenmengen auf mehreren Knoten eines Clusters ermöglicht. Cluster haben eine Master/Worker-Architektur mit einem Masterknoten (oder Haupt- bzw. Namensknoten) und einer beliebigen Anzahl von Workerknoten (oder Datenknoten). Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![HDInsight-Cluster][img-hdi-cluster]


HDInsight-Cluster abstrahieren die Hadoop-Implementierungsdetails, sodass Sie sich keine Gedanken über die Kommunikation zwischen den einzelnen Clusterknoten machen müssen. Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. Weitere Informationen finden Sie unter [Einführung in Hadoop in HDInsight](hdinsight-hadoop-introduction.md). Die zu verarbeitenden Daten werden in Azure-Blobspeicher gespeichert. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).


Dieser Artikel beschreibt die verschiedenen Möglichkeiten bei der Einrichtung eines Clusters. Wenn Sie schnell in der Lage sein möchten, einen Cluster bereitzustellen, lesen Sie [Erste Schritte mit Azure HDInsight unter Linux](../hdinsight-hadoop-linux-get-started.md).

**Voraussetzungen**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Abrufen der kostenlosen Testversion von Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Secure Shell (SSH)-Schlüssel**. Wenn Sie eine Remoteverbindung mit einem Linux-Cluster mithilfe von SSH und eines Schlüssels anstelle eines Kennworts herstellen möchten, ist dies die empfohlene Methode, da sie sicherer ist. Anweisungen zum Generieren von SSH-Schlüsseln finden Sie in den folgenden Artikeln:
	-  Auf einem Linux-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Auf einem Windows-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Konfigurationsoptionen

### Cluster unter Linux

HDInsight bietet die Möglichkeit, Linux-Cluster unter Azure bereitzustellen. Stellen Sie einen Linux-Cluster bereit, wenn Sie mit Linux oder Unix und der Migration von einer vorhandenen Linux-basierten Hadoop-Lösung vertraut sind, oder Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert sind. Weitere Informationen zu Azure HDInsight unter Linux finden Sie unter [Einführung in Hadoop in HDInsight](hdinsight-hadoop-introduction.md).


### Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure-Blobspeicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional zusätzliche Blobs angeben, die ebenfalls Ihrem Cluster zugeordnet werden.


Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).


### Metastore

Der Metastore enthält Informationen zu Tabellen, Partitionen, Schemas, Spalten usw. in Hive. Diese Informationen werden von Hive verwendet, um zu bestimmen, wo Daten in Hadoop Distributed File System (HDFS) oder Azure-Blobspeicher für HDInsight gespeichert sind. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank.

Bei der Bereitstellung eines HDInsight-Clusters können Sie eine SQL-Datenbank angeben, die als Metastore für Hive verwendet werden soll. Auf diese Weise bleiben die Metadateninformationen erhalten, wenn Sie einen Cluster löschen, da diese extern in der SQL-Datenbank gespeichert sind.


> [AZURE.NOTE]Die Option zur Verwendung von Metastores ist derzeit nur bei der Bereitstellung von HDInsight für Linux mit dem .NET SDK verfügbar. Anweisungen finden Sie unter [Bereitstellen von HDInsight-Clustern unter Linux mit .NET SDK](#sdk).



## <a id="options"></a>Optionen für die Bereitstellung von HDInsight-Linux-Clustern

Sie können einen Hadoop-Linux-Cluster von HDInsight sowohl über einen Linux- als auch über einen Windows-Computer bereitstellen. Die folgende Tabelle enthält Informationen über die Optionen für die Bereitstellung, die für die verschiedenen Betriebssystemen verfügbar sind, sowie Links zu den entsprechenden Anweisungen.

Bereitstellen eines Linux-Clusters über einen Computer mit diesem Betriebssystem| Verwenden des Azure-Portals | Verwenden der Azure-Befehlszeilenschnittstelle (CLI) | Verwenden des .NET-SDK | Die Azure PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Klicken Sie [hier](#portal) | Klicken Sie [hier](#cli)| Nicht zutreffend | Nicht zutreffend
Windows | Klicken Sie [hier](#portal) | Klicken Sie [hier](#cli) | Klicken Sie [hier](#sdk) | Klicken Sie [hier](#powershell)

### <a id="portal"></a>Verwenden des Azure-Portals

HDInsight-Cluster verwenden als Standarddateisystem einen Azure-Blobspeichercontainer. Sie benötigen ein Azure-Speicherkonto im entsprechenden Datencenter, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md). Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).


> [AZURE.NOTE]HDInsight-Cluster können derzeit nur in den Regionen **Südostasien**, **Nordeuropa**, **USA (Osten)** und **USA (Süden-Mitte)** gehostet werden.

**So erstellen Sie einen HDInsight-Cluster mit der benutzerdefinierten Erstellungsoption**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie im unteren Seitenbereich auf **+ NEW**, anschließend auf **DATA SERVICES**, auf **HDINSIGHT**und zuletzt auf **CUSTOM CREATE**.
3. Geben Sie auf der Seite **Clusterdetails** die folgenden Daten ein:

	![Bereitstellen von Hadoop HDInsight-Clustern – Details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Clustername</td>
			<td><p>Der Name des Clusters. </p>
				<ul>
				<li>Der DNS-Name (Domain Name System) muss mit einem alphanumerischen Zeichen beginnen und enden und darf Bindestriche enthalten.</li>
				<li>Das Feld muss eine Zeichenfolge mit 3 bis 63 Zeichen enthalten.</li>
				</ul></td></tr>
		<tr><td>Clustertyp</td>
			<td>Wählen Sie <strong>Hadoop</strong> aus.</td></tr>
		<tr><td>Betriebssystem</td>
			<td>Wählen Sie <b>Ubuntu 12.04 LTS Preview</b> aus, um einen HDInsight-Cluster unter Linux bereitzustellen. Informationen zum Bereitstellen eines Windows-Clusters finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Bereitstellen von Hadoop-Clustern unter Windows in HDInsight</a>.</td></tr>
		<tr><td>HDInsight-Version</td>
			<td>Wählen Sie die Version aus. Für HDInsight unter Linux ist HDInsight Version 3.2 die Standardversion, die Hadoop Version 2.6 verwendet.</td></tr>
		</table>

	Geben Sie die Werte wie in der Tabelle gezeigt ein, und klicken Sie auf den Pfeil nach rechts.

4. Geben Sie auf der Seite **Cluster konfigurieren** die folgenden Daten ein:

	![Bereitstellen von Hadoop HDInsight-Clustern – Details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Datenknoten</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten. <br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td></tr>
	<tr><td>Region/virtuelles Netzwerk</td><td><p>Wählen Sie dieselbe Region wie für das Speicherkonto, das Sie zuvor erstellt haben. HDInsight-Cluster verlangen, dass sich das Speicherkonto in derselben Region befindet. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet.</p></td></tr>
	<tr><td>Größe des Hauptknotens</td><td><p>Wählen Sie eine VM-Größe für den Hauptknoten aus.</p></td></tr>
	<tr><td>Datenknotengröße</td><td><p>Wählen Sie eine VM-Größe für die Datenknoten aus.</p></td></tr>
	</table>

	>[AZURE.NOTE]Je nach Wahl der VMs können Ihre Kosten variieren. HDInsight verwendet für Clusterknoten VMs mit Standardtarif. Informationen über die Auswirkungen der VM-Größe auf Ihre Kosten finden Sie unter <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight-Preise</a>.


5. Geben Sie auf der Seite **Clusterbenutzer konfigurieren** die folgenden Daten ein:

    ![Bereitstellen von Hadoop HDInsight-Clustern – Benutzer](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>Eigenschaft</th><th>Value</th></tr>
		<tr><td>HTTP-Kennwort</td>
			<td>Geben Sie das Kennwort für den Standard-HTTP-Benutzer, <strong>admin</strong>, ein.</td></tr>
		<tr><td>SSH-Benutzername</td>
			<td>Geben Sie den SSH-Benutzernamen an. Sie verwenden diesen Benutzernamen zum Einleiten einer SSH-Remotesitzung auf den HDInsight-Clusterknoten.</td></tr>
		<tr><td>SSH-Authentifizierungstyp</td>
			<td>Geben Sie an, ob Sie ein Kennwort oder einen SSH-Schlüssel verwenden möchten, um einen SSH-Benutzer zu authentifizieren.</td></tr>
		<tr><td>SSH-Kennwort</td>
			<td>Wenn Sie ein Kennwort als Authentifizierungstyp ausgewählt haben, geben Sie das SSH-Kennwort zum Authentifizieren eines SSH-Benutzers an. Wenn Sie versuchen, eine SSH-Sitzung auf dem Linux-Remotecomputer zu initiieren, werden Sie zur Eingabe dieses Kennworts aufgefordert.</td></tr>
		<tr><td>Öffentlicher SSH-Schlüssel</td>
			<td>Wenn Sie einen Schlüssel als Authentifizierungstyp ausgewählt haben, geben Sie den öffentlichen SSH-Schlüssel an, den Sie bereits generiert haben. Wenn Sie eine SSH-Sitzung mit einem Knoten im Linux-Cluster einleiten, verwenden Sie den privaten Schlüssel, der diesem öffentlichen Schlüssel zugeordnet ist.<br>
			Anweisungen zum Generieren eines SSH-Schlüssels auf einem Linux-Computer finden Sie <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">hier</a>. Anweisungen zum Generieren eines SSH-Schlüssels auf einem Windows-Computer finden Sie <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">hier</a>.
		</td></tr>
		<tr><td>Hive-/Oozie-Metastore eingeben</td>
			<td>Aktivieren Sie dieses Kontrollkästchen, um eine SQL-Datenbank im gleichen Datencenter wie der Cluster für die Verwendung als Hive-/Oozie-Metastore anzugeben. Wenn Sie dieses Kontrollkästchen aktivieren, müssen Sie auf den nachfolgenden Seiten des Assistenten Details zur Azure SQL-Datenbank angeben. Dies ist praktisch, wenn Sie die Metadaten von Hive-/Oozie-Jobs behalten möchten, nachdem der Cluster gelöscht wurde.</td></tr>
		</td></tr>
		</table>	


	> [AZURE.NOTE]Es wird empfohlen, die Authentifizierung mit einem öffentlichen SSH-Schlüssel zu verwenden, da diese sicherer ist als die Kennwortauthentifizierung.

	Klicken Sie auf den Pfeil nach rechts.

6. Geben Sie auf der Seite **Hive/Oozie-Metastore konfigurieren** die folgenden Daten ein:

    ![Bereitstellen von Hadoop HDInsight-Clustern – Benutzer](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	Geben Sie eine Azure SQL-Datenbank an, die als Metastore für Hive/Oozie verwendet werden soll. Sie können dieselbe Datenbank für Hive- und Oozie-Metastores angeben. Die SQL-Datenbank muss sich im selben Rechenzentrum wie das HDInsight-Cluster befinden. In diesem Feld werden nur SQL-Datenbanken aufgelistet, die sich im gleichen Datencenter befinden, das Sie auf der Seite <strong>Clusterdetails</strong> angegeben haben. Geben Sie auch den Benutzernamen und das Kennwort zum Herstellen der Verbindung mit der Azure SQL-Datenbank an, die Sie ausgewählt haben.

    >[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.

    Klicken Sie auf den Pfeil nach rechts.


6. Geben Sie auf der Seite **Speicherkonto** die folgenden Werte ein:


    ![Bereitstellen eines Speicherkontos für Hadoop HDInsight-Cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Speicherkonto</td>
			<td>Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für das HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten:
			<ul>
				<li><strong>Vorhandenen Speicher verwenden</strong></li>
				<li><strong>Neuen Speicher erstellen</strong></li>
				<li><strong>Speicher aus anderem Abonnement verwenden</strong></li>
			</ul>
			</td></tr>
		<tr><td>Kontoname</td>
			<td><ul>
				<li>Falls Sie vorhandenen Speicher verwenden, wählen Sie unter <strong>Kontoname</strong> ein vorhandenes Speicherkonto aus. Die Dropdownliste enthält nur die Speicherkonten im gleichen Datencenter, in dem Sie auch den Cluster eingerichtet haben.</li>
				<li>Falls Sie <strong>Neuen Speicher erstellen</strong> oder <strong>Speicher aus anderem Abonnement verwenden</strong> ausgewählt haben, müssen Sie den Namen des Speicherkontos angeben.</li>
			</ul></td></tr>
		<tr><td>Kontoschlüssel</td>
			<td>Geben Sie den Speicherschlüssel für das entsprechende Konto ein, falls Sie <strong>Speicher aus anderem Abonnement verwenden</strong> ausgewählt haben.</td></tr>
		<tr><td>Standardcontainer</td>
			<td><p>Gibt den Standardcontainer im Speicherkonto an, der als Standarddateisystem für den HDInsight-Cluster verwendet werden soll. Wenn Sie <strong>Vorhandenen Speicher verwenden</strong> für das Feld <strong>Speicherkonto</strong> wählen und in dem betreffenden Konto keine Container vorhanden sind, wird der Container standardmäßig mit demselben Namen wie der des Clusters erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt. Zum Beispiel mycontainer1, mycontainer2 und so weiter. Sie können jedoch auch Container im vorhandenen Speicherkonto verwenden, die einen anderen Namen als der Cluster haben.</p>
	        <p>Falls Sie einen neuen Speicher erstellen oder einen Speicher aus einem anderen Azure-Abonnement verwenden, müssen Sie den Namen des Standardcontainers angeben.</p>
	    </td></tr>
		<tr><td>Zusätzliche Speicherkonten</td>
			<td>HDInsight unterstützt mehrere Speicherkonten. Es gibt keine Beschränkung in Bezug auf die zusätzlichen Speicherkonten, die von einem Cluster verwendet werden können. Wenn Sie den Cluster jedoch im Azure-Portal erstellen, können Sie aufgrund von Einschränkungen der Benutzeroberfläche maximal sieben Speicherkonten einrichten. Für jedes angegebene Speicherkonto wird dem Assistenten eine zusätzliche Seite <strong>Speicherkonto</strong> hinzugefügt, in der Sie die Kontoinformationen angeben können. Im obigen Bildschirmfoto wurde z. B. ein zusätzliches Speicherkonto ausgewählt, weshalb Seite 5 dem Dialogfeld hinzugefügt wurde.</td></tr>
	</table>

	Klicken Sie auf den Pfeil nach rechts.

7. Wenn Sie für den Cluster zusätzlichen Speicher konfigurieren möchten, geben Sie auf der Seite **Speicherkonto** die Kontoinformationen für das zusätzliche Speicherkonto ein:

	![Zusätzliche Speicherdetails für HDInsight-Cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page6.png)

    Hier haben Sie erneut die Auswahl zwischen einem vorhandenen Speicher, einem neu erstellten Speicher und einem Speicher aus einem anderen Azure-Abonnement. Die Angabe der Werte erfolgt auf dieselbe Weise wie im vorherigen Schritt.


    > [AZURE.NOTE]Nachdem Sie ein Azure-Speicherkonto für Ihren HDInsight-Cluster ausgewählt haben, können Sie das Konto nicht mehr löschen und es nicht für einen anderen Cluster auswählen.


 	Nachdem Sie die zusätzlichen Speicherkonten angegeben haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

###<a id="cli"></a> Verwenden der Azure-Befehlszeilenschnittstelle (Azure-CLI)

Eine weitere Möglichkeit zur Bereitstellung von HDInsight-Clustern ist die Azure-Befehlszeilenschnittstelle (Azure-CLI). Die Azure-CLI ist in Node.js implementiert. Das Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, einschließlich Windows, Mac und Linux. Sie können die Azure-CLI von den folgenden Adressen herunterladen und installieren:

- **Node.js SDK** – <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Azure-CLI für Mac, Linux und Windows** – <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Eine allgemeine Anleitung für die Azure-CLI finden Sie unter [Azure-CLI für Mac, Linux und Windows](../xplat-cli.md).

Im Folgenden finden Sie Anweisungen zum Installieren der Azure-CLI unter Linux und Windows und zum Verwenden der Befehlszeilenschnittstelle zum Bereitstellen eines Clusters.

- [Einrichten der Azure-Befehlszeilenschnittstelle für Linux](#clilin)
- [Einrichten der Azure-Befehlszeilenschnittstelle für Windows](#cliwin)
- [Bereitstellen von HDInsight-Clustern mithilfe der Azure-Befehlszeilenschnittstelle](#cliprovision)

#### <a id="clilin"></a>Einrichten der Azure-CLI für Linux

Führen Sie die folgenden Verfahren zum Einrichten des Linux-Computers aus, um die Azure-Befehlszeilenschnittstelle (Azure-CLI) zu verwenden:

- Installieren der Azure-CLI mithilfe von Node.js-Paketmanager (NPM)
- Verbinden mit Ihrem Azure-Abonnement

**So installieren Sie die Azure-CLI mithilfe von NPM**

1.	Öffnen Sie auf dem Linux-Computer ein Terminalfenster, und führen Sie den folgenden Befehl aus:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter **-h** auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Zum Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Verbinden mit Ihrem Azure-Abonnement**

Bevor Sie die Azure-CLI verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Azure-CLI verwendet Ihre Azure-Abonnementinformationen, um sich mit Ihrem Konto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Konfigurationseinstellung importiert werden, die von der Azure-CLI in nachfolgenden Vorgängen verwendet wird. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.


> [AZURE.NOTE]Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Ändern Sie unter Windows die Ordnereigenschaften, oder verwenden Sie die BitLocker-Laufwerkverschlüsselung.



1.	Öffnen Sie ein Terminalfenster.
2.	Führen Sie den folgenden Befehl aus, um sich bei Ihrem Azure-Abonnement anzumelden:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können. Wenn die Webseite nicht geöffnet wird, klicken Sie auf den Link im Terminalfenster, um die Browserseite zu öffnen und sich beim Portal anzumelden.

3.	Laden Sie die Datei mit den Veröffentlichungseinstellungen auf den Computer herunter.
4.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Einrichten der Azure-CLI für Windows

Führen Sie die folgenden Verfahren zum Einrichten des Windows-Computers aus, damit Sie die Befehlszeilenschnittstelle von Azure (Azure-CLI) verwenden können:

- Installieren der Azure-CLI mithilfe von NPM oder Windows Installer
- Download und Import der Veröffentlichungseinstellungen für das Azure-Konto


Sie können die Azure-Befehlszeilenschnittstelle entweder über NPM oder Windows Installer installieren. Microsoft empfiehlt, dass Sie nur eine der beiden Optionen für die Installation verwenden.

**So installieren Sie die Azure-CLI mithilfe von NPM**

1.	Öffnen Sie die Webseite **www.nodejs.org**.
2.	Klicken Sie auf **INSTALL**, und befolgen Sie die Anweisungen unter Beibehaltung der Standardeinstellungen.
3.	Öffnen Sie die **Eingabeaufforderung** (bzw. **Azure-Eingabeaufforderung** oder **Developer-Eingabeaufforderung für VS2012**) auf Ihrer Arbeitsstation.
4.	Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Wenn Sie die Fehlermeldung erhalten, dass der NPM-Befehl nicht gefunden wurde, überprüfen Sie, ob sich die folgenden Pfade in der Umgebungsvariablen **PATH** befinden: <i>C:\\Program Files (X 86) \\nodejs, C:\\Users[Benutzername]\\AppData\\Roaming\\npm</i> oder <i>C:\\Program Files\\nodejs; C:\\Users[Benutzername]\\AppData\\Roaming\\npm</i>


5.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter **-h** auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So installieren Sie die Azure-Befehlszeilenschnittstelle mit Windows Installer**

1.	Navigieren Sie zu **http://azure.microsoft.com/downloads/**. 2.	Führen Sie einen Bildlauf nach unten zum Abschnitt **Befehlszeilentools** durch, und klicken Sie dann auf **Azure-Befehlszeilenschnittstelle**, und führen Sie den Assistenten für den Webplattform-Installer aus.

**Herunterladen und Importieren der Veröffentlichungseinstellungen**

Bevor Sie die Azure-CLI verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Azure-Befehlszeilenschnittstelle verwendet Ihre Azure-Abonnementinformationen, um sich mit Ihrem Konto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Konfigurationseinstellung importiert werden, die von der Azure-CLI in nachfolgenden Vorgängen verwendet wird. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.


> [AZURE.NOTE]Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Ändern Sie unter Windows die Ordnereigenschaften, oder verwenden Sie BitLocker.



1.	Öffnen Sie eine Eingabeaufforderung.
2.	Führen Sie den folgenden Befehl aus, um die Datei mit den Veröffentlichungseinstellungen herunterzuladen:

		azure account download


	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können.


3.	Klicken Sie in der Aufforderung auf **Speichern** und geben Sie einen Speicherort für die Datei an.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]


#### <a id="cliprovision"></a>Bereitstellen eines HDInsight-Clusters mithilfe der Azure-Befehlszeilenschnittstelle

Die folgenden Verfahren werden benötigt, um einen HDInsight-Cluster mit der Azure-Befehlszeilenschnittstelle bereitzustellen:

- Erstellen eines Azure-Speicherkontos
- Bereitstellen eines Clusters


**Erstellen eines Azure-Speicherkontos**

HDInsight verwendet Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Datencenter befinden.

- Führen Sie den folgenden Befehl in einem Eingabeaufforderungsfenster aus, um ein Azure-Speicherkonto zu erstellen:

		azure storage account create [options] <StorageAccountName>


	Wählen Sie einen geeigneten Ort für die Bereitstellung des HDInsight-Linux-Clusters aus, wenn Sie dazu aufgefordert werden. Der Speicher muss sich am gleichen Standort befinden wie der HDInsight-Cluster.


Informationen zum Erstellen von Azure-Speicherkonten im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account.md).

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Ausführliche Informationen zum Abrufen dieser Informationen im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account.md) im Abschnitt "Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln".

Für einen HDInsight-Cluster benötigen Sie außerdem einen Container innerhalb des Speicherkontos. Falls das angegebene Speicherkonto noch keinen Container enthält, werden Sie von **azure hdinsight cluster create** zur Eingabe eines Containernamens aufgefordert, und der Container wird erstellt. Sie können den folgenden Befehl verwenden, um den Container vorab zu erstellen:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

**Bereitstellen eines HDInsight-Clusters**

- Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

	>[AZURE.NOTE]Die Werte, die Sie für **--userName** und **--password** angeben, sind für den Hadoop-Benutzer. Für den Hadoop-Benutzer müssen Sie stets "--userName" als "admin" angeben.

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Bereitstellen eines HDInsight-Clusters mit einer Konfigurationsdatei**

Normalerweise erstellen Sie einen HDInsight-Cluster, führen Ihre Jobs aus und löschen den Cluster anschließend, um Kosten zu sparen. In der Azure-CLI können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

- Führen Sie im Eingabeaufforderungsfenster die folgenden Befehle aus:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster. Make sure you specify --userName as "admin"
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>


	>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im rechts im SQL-Datenbank-Dashboard auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.



	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Auflisten und Anzeigen von Clusterdetails**

- Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Löschen eines Clusters**

- Mit dem folgenden Befehl können Sie ein Cluster löschen:

		azure hdinsight cluster delete <ClusterName>

###<a id="powershell"></a> Verwenden von Azure PowerShell
Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Dieser Abschnitt enthält Anweisungen für die Bereitstellung eines HDInsight-Clusters mit Azure PowerShell. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure-PowerShell](../install-configure-powershell.md). Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md). Eine Liste der HDInsight Windows PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe von Azure PowerShell ausgeführt werden:

- Erstellen eines Azure-Speicherkontos
- Erstellen eines Azure-Blobcontainers
- Erstellen eines HDInsight-Clusters

Sie können die Windows PowerShell-Konsole oder Windows PowerShell Integrated Scripting Environment (ISE) zum Ausführen der Skripts verwenden.

HDInsight verwendet Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Datencenter wie der HDInsight-Cluster befinden. HDInsight-Cluster können derzeit nur in den Regionen **Südostasien**, **Nordeuropa**, **USA (Osten)** und **USA (Süden-Mitte)** gehostet werden.

**Verbinden mit Ihrem Azure-Konto**

		Add-AzureAccount

Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

**Erstellen eines Azure-Speicherkontos**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Windows PowerShell-Befehlen abrufen:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Erstellen eines Azure-Blobspeichercontainers**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

**Bereitstellen eines HDInsight-Clusters**

Die beiden wichtigsten Parameter, die für die Bereitstellung von Linux-Clustern festgelegt werden müssen, sind diejenigen zum Angeben des Typs des Betriebssystems und der Details zum SSH-Benutzer:

- Stellen Sie sicher, dass Sie den Parameter **- OSType** als **Linux** angeben.
- Um SSH für Remotesitzungen mit dem Cluster zu verwenden, können Sie entweder das SSH-Benutzerkennwort oder den öffentlichen SSH-Schlüssel angeben. Wenn Sie sowohl das SSH-Benutzerkennwort und als auch den öffentlichen SSH-Schlüssel angeben, wird der Schlüssel ignoriert. Wenn Sie den SSH-Schlüssel für Remotesitzungen verwenden möchten, müssen Sie bei der Aufforderung zur Eingabe ein leeres SSH-Kennwort angeben.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $containerName = $clusterName					  # Azure Blob container that is used as the default file system for the HDInsight cluster


		# Get the credentials for HTTP and SSH users for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $clusterCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey


>[AZURE.NOTE]Die Werte, die Sie für **$clusterCredentials** angeben, werden verwendet, um das Hadoop-Benutzerkonto für den Cluster zu erstellen. Sie verwenden dieses Konto für die Verbindung mit dem Cluster. Die Werte, die Sie für **$sshCredentials** angeben, werden verwendet, um den SSH-Benutzer für den Cluster zu erstellen. Sie verwenden dieses Konto zum Starten einer SSH-Remotesitzung mit dem Cluster und Ausführen von Aufträgen. Wenn Sie im Azure-Portal die Option "Schnellerfassung" verwenden, um einen Cluster bereitzustellen, ist der Hadoop-Standardbenutzername "admin" und der SSH-Standardbenutzername "ddiuser".

Die Bereitstellung des Clusters kann einige Minuten dauern.

![HDI.CLI.Provision][image-hdi-ps-provision]

**Bereitstellen eines HDInsight-Clusters mit benutzerdefinierten Konfigurationsoptionen**

Bei der Bereitstellung eines Clusters können Sie erweiterte Optionen verwenden, wie z. B. die Verbindung mit mehr als einem Azure-Blobspeichercontainer oder die Verwendung einer Azure SQL-Datenbank für Hive- und Oozie-Metastores. Auf diese Weise können Sie die Lebensdauer Ihrer Daten und Metadaten von der Lebensdauer des Clusters trennen.

Die beiden wichtigsten Parameter, die für die Bereitstellung von Linux-Clustern festgelegt werden müssen, sind diejenigen zum Angeben des Typs des Betriebssystems und der Details zum SSH-Benutzer:

- Stellen Sie sicher, dass Sie den Parameter **- OSType** als **Linux** angeben.
- Um SSH für Remotesitzungen mit dem Cluster zu verwenden, können Sie entweder das SSH-Benutzerkennwort oder den öffentlichen SSH-Schlüssel angeben. Wenn Sie sowohl das SSH-Benutzerkennwort und als auch den öffentlichen SSH-Schlüssel angeben, wird der Schlüssel ignoriert. Wenn Sie den SSH-Schlüssel für Remotesitzungen verwenden möchten, müssen Sie bei der Aufforderung zur Eingabe ein leeres SSH-Kennwort angeben.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster

		# Get the credentials for HTTP user, SSH user, and Hive/Oozie metastore databases for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}

		# Create a cluster configuration file
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore

		# Create the cluster
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $clusterLocation -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey

>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.

Die Bereitstellung des Clusters kann einige Minuten dauern.

![HDI.CLI.Provision][image-hdi-ps-config-provision]

###<a id="sdk"></a>Verwenden des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in einer .NET Framework-Anwendung.

Führen Sie die folgenden Schritte aus, um einen HDInsight-Cluster unter Linux mithilfe des SDK bereitzustellen:

- Installieren des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**Installieren des HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) herunterladen und installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


**Erstellen einer Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013.

2. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3. Unter **Neues Projekt** können Sie die folgenden Werte eingeben bzw. auswählen:

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

6. Führen Sie den folgenden Befehl in der Konsole aus, um die Pakete zu installieren:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Fügen Sie in der Funktion **Main()**den folgenden Code ein:

	> [AZURE.NOTE]Beachten Sie, dass HDInsight Linux-Cluster derzeit nur in den Regionen **Südostasien**, **Nordeuropa**, **USA (Osten)** und ** USA (Süden-Mitte)** gehostet werden können. Das Speicherkonto, das Sie bereitstellen, muss sich außerdem in derselben Region befinden.

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";		# Make sure you specify this username as "admin"
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;


		// If required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores

		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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
			HiveMetastore = hiveMetastore,		//Only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//Only if you created an ooziemetastore object earlier
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

10. Ersetzen Sie die Variablen am Anfang der Funktion **Main()**.

**Ausführen der Anwendung**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.



##<a id="nextsteps"></a>Nächste Schritte
In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters (Hadoop) unter Linux kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Arbeiten mit HDInsight unter Linux](hdinsight-hadoop-linux-information.md): Einführung in die Nuancen des Arbeitens mit einem HDInsight-Cluster unter Linux.
- [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md): Erfahren Sie, wie Sie Ihren Linux-basierten Hadoop-Cluster in HDInsight mithilfe von Ambari Web oder der Ambari-REST-API überwachen und verwalten.
- [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md): Informationen zu den verschiedenen Möglichkeiten zum Ausführen von MapReduce-Aufträgen in einem Cluster.
- [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md): Informationen zu den verschiedenen Möglichkeiten zum Anwenden einer Hive-Abfrage auf einen Cluster.
- [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md): Informationen zu den verschiedenen Möglichkeiten zum Ausführen eines Pig-Auftrags in einem Cluster.
- [Verwenden von Azure Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md): Erfahren Sie, wie HDInsight Azure-Blobspeicher zum Speichern von Daten für HDInsight-Cluster verwendet.
- [Hochladen von Daten in HDInsight](hdinsight-upload-data.md): Erfahren Sie, wie Sie mit Daten arbeiten, die in Azure-Blobspeicher für einen HDInsight-Cluster gespeichert sind.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Auflisten und Anzeigen von Clustern"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Verwenden von Sqoop mit HDInsight"
 

<!----HONumber=July15_HO4-->