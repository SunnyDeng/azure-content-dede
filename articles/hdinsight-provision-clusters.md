<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision HDInsight clusters | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision HDInsight clusters" authors="jgao" />

Bereitstellen eines HDInsight-Clusters
======================================

Dieser Artikel beschreibt verschiedene Möglichkeiten für die Bereitstellung von HDInsight-Clustern.

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Die HDInsight PowerShell-Cmdlets führen Aufgaben für Ihr Abonnement aus. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Spezielle Angebote](https://www.windowsazure.com/en-us/pricing/member-offers/) oder [Kostenlose Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/).

Themen in diesem Artikel
------------------------

-   [Das Azure-Verwaltungsportal](#portal)
-   [Die Azure PowerShell](#powershell)
-   [Die plattformübergreifende Befehlszeile](#cli)
-   [Das HDInsight .NET SDK](#sdk)
-   [Nächste Schritte](#nextsteps)

Das Azure-Verwaltungsportal
---------------------------

HDInsight-Cluster verwenden Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto im entsprechenden Rechenzentrum, um ein HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Use Azure Blob Storage with HDInsight](/de-de/manage/services/hdinsight/howto-blob-store/) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache). Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](/de-de/manage/services/storage/how-to-create-a-storage-account/).

> [WACOM.NOTE] Aktuell unterstützen nur die Regionen Südostasien, Nordeuropa, Westeuropa, USA Ost und USA West die Hostingfunktion für HDInsight-Cluster.

Dieser Artikel beschreibt die Erstellung eines HDInsight-Clusters mithilfe der benutzerdefinierten Erstellungsoption. Weitere Informationen zur Schnellerfassungs-Option finden Sie unter [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/).

**So erstellen Sie ein HDInsight-Cluster über die benutzerdefinierte Erstellungsoption**

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2.  Klicken Sie im unteren Seitenbereich auf **+ NEW**, anschließend auf **DATA SERVICES**, auf **HDINSIGHT**und zuletzt auf **CUSTOM CREATE**.
3.  Auf der Seite mit den Cluster-Details können Sie die folgenden Werte auswählen bzw. eingeben:

    <table data-morhtml="true" border="1">
     	<tr data-morhtml="true">
			<th data-morhtml="true">Eigenschaft</th>
			<th data-morhtml="true">Wert</th>
		</tr>
     	<tr data-morhtml="true">
			<td data-morhtml="true">CLUSTER NAME</td>
        	<td data-morhtml="true"><p data-morhtml="true">Der Name des Clusters. </p>
             	<ul data-morhtml="true">
             	<li data-morhtml="true">Der DNS-Name muss mit einem alphanumerischen Zeichen beginnen und enden und darf Bindestriche enthalten.</li>
             	<li data-morhtml="true">Der Wert in diesem Feld muss zwischen 3 und 63 Zeichen lang sein.</li>
             	</ul>
			</td>
		</tr>
     	<tr data-morhtml="true">
			<td data-morhtml="true">DATA NODES</td>
     		<td data-morhtml="true">Geben Sie die Anzahl der Knoten in diesem Cluster an. Der Standardwert ist 	4.</td>
		</tr>
     	<tr data-morhtml="true">
			<td data-morhtml="true">HDINSIGHT VERSION</td>
         	<td data-morhtml="true">W&auml;hlen Sie die Version aus. Die Standardversion ist 2.0 mit Hadoop 1.2-Clustern.  Version 3.0 verwendet Hadoop 2.2-Cluster. Weitere Informationen finden Sie unter <a data-morhtml="true" href="http://www.windowsazure.com/de-de/manage/services/hdinsight/versioning-in-hdinsight/">Welche Version von Hadoop ist in Azure HDInsight enthalten?</a>.</td>
		</tr>
     	<tr data-morhtml="true">
			<td data-morhtml="true">REGION</td>
         	<td data-morhtml="true">Geben Sie das Rechenzentrum an, indem das Cluster installiert werden soll. Dies muss derselbe Standort sein, an dem sich der Azure-Blob-Speicher befindet, der als Standarddateisystem verwendet wird. Momentan stehen nur die Optionen *S&uuml;dostasien*, *Nordeuropa*, *Westeuropa*, *USA Ost* und *USA West* zur Auswahl.</td>
     	</tr>
	</table>

    ![HDI.CustomProvision.Page1](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png)

4.  Klicken Sie auf den Pfeil nach rechts in der unteren rechten Ecke der Seite.
5.  Auf der Konfigurationsseite für die Cluster-Details können Sie die folgenden Werte auswählen bzw. eingeben:

    <table data-morhtml="true" border="1">
		<tr data-morhtml="true">
			<th data-morhtml="true">Eigenschaft</th><th data-morhtml="true">Wert</th>
		</tr>
	    <tr data-morhtml="true">
			<td data-morhtml="true">USER NAME</td>
	        <td data-morhtml="true">Geben Sie den Benutzernamen f&uuml;r das HDInsight-Cluster an.</td>
		</tr>
	    <tr data-morhtml="true">
			<td data-morhtml="true"><p data-morhtml="true">PASSWORD</p><p data-morhtml="true">CONFIRM PASSWORD</p></td>
	        <td data-morhtml="true">Geben Sie das Passwort f&uuml;r das HDInsight-Cluster an.</td>
		</tr>
	    <tr data-morhtml="true">
			<td data-morhtml="true">Enter Hive/Oozie Metastore</td>
	        <td data-morhtml="true">Geben Sie eine SQL-Datenbank im gleichen Rechenzentrum an, die als Hive/Oozie-Metastore verwendet werden soll.</td>
		</tr>
	    <tr data-morhtml="true">
			<td data-morhtml="true">HIVE META/OOZIESTORE DATABASE</td>
        	<td data-morhtml="true">Geben Sie die Azure SQL-Datenbank an, die als Metastore f&uuml;r Hive/Oozie verwendet werden soll. Die SQL-Datenbank muss sich im selben Rechenzentrum wie das HDInsight-Cluster befinden. In diesem Feld werden nur SQL-Datenbanken aufgelistet, die sich im gleichen Rechenzentrum befinden, das Sie in der Clusterdetailseite angegeben haben.</td>
		</tr>
	    <tr data-morhtml="true">
			<td data-morhtml="true">DATABASE USER</td>
	        <td data-morhtml="true">Geben Sie den SQL-Datenbankbenutzer an, der f&uuml;r die Verbindung zur Datenbank verwendet wird.</td>
		</tr>
	    <tr data-morhtml="true">
			<td data-morhtml="true">DATABASE USER PASSWORD</td>
			<td data-morhtml="true">Geben Sie das Passwort des SQL-Datenbankbenutzers an.</td>
		</tr>
	 </table>

    ![HDI.CustomProvision.Page2](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

6.  Klicken Sie auf den Pfeil nach rechts in der unteren rechten Ecke der Seite.
7.  Auf der Speicherkonto-Seite können Sie die folgenden Werte auswählen bzw. eingeben:

    <table data-morhtml="true" border="1">
		<tr data-morhtml="true">
			<th data-morhtml="true">Eigenschaft</th>
			<th data-morhtml="true">Wert</th>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">STORAGE ACCOUNT</td>
        	<td data-morhtml="true">Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem f&uuml;r das HDInsight-Cluster verwendet werden soll. Sie haben drei M&ouml;glichkeiten:
         		<ul data-morhtml="true">
             	<li data-morhtml="true">Existierenden Speicher verwenden</li>
             	<li data-morhtml="true">Neuen Speicher erstellen</li>
             	<li data-morhtml="true">Speicher aus anderem Abonnement verwenden</li>
         		</ul>
         	</td>
		</tr>
     	<tr data-morhtml="true">
			<td data-morhtml="true">ACCOUNT NAME</td>
         	<td data-morhtml="true">Wenn Sie <b data-morhtml="true">Existierenden Speicher verwenden</b> ausw&auml;hlen, enth&auml;lt dieses Listenfeld nur Speicherkonten, die sich im gleichen Rechenzentrum befinden.</td>
		</tr>
     	<tr data-morhtml="true">
			<td data-morhtml="true">ACCOUNT KEY</td>
         	<td data-morhtml="true">Dieses Feld ist nur verf&uuml;gbar, wenn Sie im Feld STORAGE ACCOUNT den Wert <strong data-morhtml="true">Speicher aus anderem Abonnement verwenden</strong> ausgew&auml;hlt haben.</td>
		</tr>	
     	<tr data-morhtml="true">
			<td data-morhtml="true">DEFAULT CONTAINER</td>
         	<td data-morhtml="true">Der Standardcontainer des Speicherkontos wird als Standard-Dateisystem f&uuml;r das HDInsight-Cluster verwendet. Wenn Sie <strong data-morhtml="true">Existierenden Speicher verwenden</strong> im Feld STORAGE ACCOUNT und <strong data-morhtml="true">Standardcontainer erstellen</strong> im Feld DEFAULT CONTAINER ausgew&auml;hlt haben, dann hat der Standardcontainer denselben Namen wie das Cluster. Wenn bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angeh&auml;ngt. Zum Beispiel mycontainer1, mycontainer2 und so weiter.</td>
		</tr>
     	<tr data-morhtml="true">
			<td data-morhtml="true">ADDITIONAL STORAGE ACCOUNTS</td>
	        <td data-morhtml="true">HDInsight unterst&uuml;tzt mehrere Speicherkonten. Es existiert kein Limit f&uuml;r die zus&auml;tzlichen Speicherkonten, die ein Cluster verwenden kann. Wenn Sie jedoch ein Cluster im Verwaltungsportal erstellen, ist diese Anzahl aufgrund der Benutzeroberfl&auml;che auf sieben beschr&auml;nkt. F&uuml;r jedes zus&auml;tzliche Speicherkonto, das Sie in diesem Feld angeben, wird eine zus&auml;tzliche Speicherkontoseite hinzugef&uuml;gt, in der Sie die entsprechenden Kontoinformationen eingeben k&ouml;nnen. Im folgenden Beispiel ist 1 zus&auml;tzliches Speicherkonto ausgew&auml;hlt, und Seite 4 wurde zum Dialog hinzugef&uuml;gt.</td>
		</tr>		
	</table>

    ![HDI.CustomProvision.Page3](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

8.  Klicken Sie auf den Pfeil nach rechts in der unteren rechten Ecke der Seite.
9.  Geben Sie in der Speicherkontoseite die Kontoinformationen für das zusätzliche Speicherkonto ein:

    ![HDI.CustomProvision.Page4](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

10. Klicken Sie auf die Schaltfläche Fertig (das Häkchen) in der unteren rechten Ecke, um mit der Bereitstellung des HDInsight-Clusters zu beginnen.

Die Bereitstellung eines Clusters kann mehrere Minuten in Anspruch nehmen. Nach Abschluss des Bereitstellungsprozesses wird in der Statusspalte des Clusters **Wird ausgeführt** angezeigt.

> [WACOM.NOTE] Nachdem Sie ein Azure-Speicherkonto für Ihr HDInsight-Cluster ausgewählt haben, können Sie das Konto nicht mehr löschen und auch kein anderes Konto auswählen.



Die Azure PowerShell
--------------------

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und die Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren der Azure-PowerShell](/de-de/documentation/articles/install-configure-powershell/). Weitere Informationen zur Verwendung von PowerShell mit HDInsight finden Sie unter [Administer HDInsight using PowerShell](/de-de/manage/services/hdinsight/administer-hdinsight-using-powershell/) (Verwalten von HDInsight mit PowerShell, in englischer Sprache). Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz](http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx).

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe der PowerShell ausgeführt werden:

-   Erstellen eines Azure-Speicherkontos
-   Erstellen eines Azure-Blob-Containers
-   Erstellen eines HDInsight-Clusters

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um ein HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum wie das HDInsight-Cluster befinden.

**So erstellen Sie ein Azure-Speicherkonto**

-   Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

          $storageAccountName = "<StorageAcccountName>"
          $location = "<MicrosoftDataCenter>"     # For example, "West US"
            
          # Erstellen eines Azure-Speicherkontos
          New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden PowerShell-Befehlen abrufen:

          # Speicherkonten für das aktuelle Abonnement auflisten
          Get-AzureStorageAccount

          # Schlüssel für ein Speicherkonto auflisten
          Get-AzureStorageKey "<StorageAccountName>"

**So erstellen Sie einen Azure-Speichercontainer**

-   Führen Sie die folgenden Befehle in einem Azure PowerShell-Fenster aus:

          $storageAccountName = "<StorageAccountName>"
          $storageAccountKey = "<StorageAccountKey>"
          $containerName="<ContainerName>"

          # Speicherkontext-Objekt erstellen
          $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                                 -StorageAccountKey $storageAccountKey  
             
          # Blob-Speichercontainer erstellen
          New-AzureStorageContainer -Name $containerName -Context $destContext

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie ein Cluster erstellen.

**So stellen Sie HDInsight-Cluster bereit**

-   Führen Sie die folgenden Befehle in einem Azure PowerShell-Fenster aus:

          $subscriptionName = "<SubscriptionName>"     	  # Der Name des Azure-Abonnements.
          $storageAccountName = "<StorageAccountName>"    # Das Azure-Speicherkonto, in dem sich der Standardcontainer befindet. Der Standardcontainer wird als Standarddateisystem verwendet.
          $containerName = "<ContainerName>"              # Der Azure Blob-Speichercontainer, der als Standarddateisystem für das HDInsight-Cluster verwendet wird.

          $clusterName = "<HDInsightClusterName>"         # Der Name Ihres HDInsight-Clusters.
          $location = "<MicrosoftDataCenter>"             # Der Standort Ihres HDInsight-Clusters. Das Cluster muss sich im selben Rechenzentrum wie das Speicherkonto befinden.
          $clusterNodes = <ClusterSizeInNodes>            # Die Anzahl der Knoten im HDInsight-Cluster.

          # Speicher-Primärschlüssel für den Kontonamen anrufen
          Select-AzureSubscription $subscriptionName
          $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

          # Neues HDInsight-Cluster erstellen
          New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    Die Bereitstellung des Clusters kann einige Minuten in Anspruch nehmen.

    ![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

Sie können ein Cluster auch bereitstellen und für die Verbindung mit mehr als einem Azure-Blobspeicher bzw. benutzerdefinierte Hive- und Oozie-Metastores konfigurieren. Mit diesem erweiterten Feature können Sie die Lebensdauer Ihrer Daten und Metadaten von der Lebensdauer des Clusters entkoppeln.

**Bereitstellung eines HDInsight-Clusters mit Konfiguration**

-   Führen Sie die folgenden Befehle in einem Windows PowerShell-Fenster aus:

          $subscriptionName = "<SubscriptionName>"
          $clusterName = "<ClusterName>"
          $location = "<MicrosoftDataCenter>"
          $clusterNodes = <ClusterSizeInNodes>
            
          $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
          $containerName_Default = "<DefaultFileSystemContainerName>"
            
          $storageAccountName_Add1 = "<AdditionalStorageAccountName>"
            
          $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
          $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
          $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
          $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"
            
          # Speicher-Kontoschlüssel abrufen
          Select-AzureSubscription $subscriptionName
          $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
          $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
            
          $oozieCreds = Get-Credential -Message "Oozie metastore"
          $hiveCreds = Get-Credential -Message "Hive metastore"
            
          # Blob-Speichercontainer erstellen
          #$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
          #New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
            
          # Neues HDInsight-Cluster erstellen
          $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
              Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
              Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                  New-AzureHDInsightCluster -Name $clusterName -Location $location

**Auflisten von HDInsight-Clustern**

-   Führen Sie die folgenden Befehle in einem Azure PowerShell-Fenster aus:

          Get-AzureHDInsightCluster -Name <ClusterName>



Die plattformübergreifende Befehlszeile
---------------------------------------

Eine weitere Möglichkeit zur Bereitstellung von HDInsight-Clustern ist die plattformübergreifende Befehlszeile. Das Befehlszeilentool ist in Node.js implementiert. Dieses Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux. Das Befehlszeilentool ist Open Source. Der Quellcode liegt auf GitHub unter <https://github.com/WindowsAzure/azure-sdk-tools-xplat>. Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/). Umfassendes Referenzmaterial finden Sie unter [Azure-Befehlszeilentool für Mac und Linux](/de-de/manage/linux/other-resources/command-line-tools/). Dieser Artikel behandelt nur die Befehlszeilenschnittstelle unter Windows.

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters in der plattformübergreifenden Befehlszeile ausgeführt werden:

-   Installieren der plattformübergreifenden Befehlszeile
-   Download und Import der Veröffentlichungseinstellungen für das Azure-Konto
-   Erstellen eines Azure-Speicherkontos
-   Bereitstellen eines Clusters

Sie können die Befehlszeilenschnittstelle entweder über den *Node.js-Paketmanager (NPM)* oder über den Windows Installer installieren.

**So installieren Sie die Befehlszeilenschnittstelle mit dem NPM**

1.  Öffnen Sie die Webseite **www.nodejs.org**.
2.  Klicken Sie auf **INSTALL** und folgen Sie den Anweisungen mit den Standardeinstellungen.
3.  Öffnen Sie die **Eingabeaufforderung** (bzw. *Azure-Eingabeaufforderung* oder *Developer-Eingabeaufforderung für VS2012*) auf Ihrer Arbeitsstation.
4.  Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein.

        npm install -g azure-cli

    > [WACOM.NOTE] Falls Sie einen Fehler erhalten und der NPM-Befehl nicht gefunden wird, vergewissern Sie sich, dass die PATH-Umgebungsvariable die folgenden Pfade enthält: *C:\\Program Files (x86)\\nodejs;C:\\Users[Benutzername]\\AppData\\Roaming\\npm* oder *C:\\Program Files\\nodejs;C:\\Users[Benutzername]\\AppData\\Roaming\\npm*

5.  Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

        azure hdinsight -h

    Sie können den Parameter *-h* auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Beispiel:

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**So installieren Sie die Befehlszeilenschnittstelle mit dem Windows Installer**

1.  Öffnen Sie die Webseite **http://www.windowsazure.com/de-de/downloads/**.
2.  Blättern Sie nach unten zum Bereich **Befehlszeilentools**, klicken Sie auf **Plattformübergreifende Befehlszeilenschnittstelle** und führen Sie den Webplattform-Installer aus.

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [WACOM.NOTE] Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Sie sollten die Datei entweder löschen oder zusätzliche Maßnahmen ergreifen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.

**Herunterladen und Importieren der Veröffentlichungseinstellungen**

1.  Öffnen Sie eine **Eingabeaufforderung**.
2.  Führen Sie den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei herunterzuladen.

        azure account download

    ![HDI.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png)

    Der Befehl zeigt die Anweisungen für den Download der Datei an, inklusive einer URL.

3.  Öffnen Sie **Internet Explorer** und besuchen Sie die im Eingabeaufforderungsfenster angezeigte URL.
4.  Klicken Sie auf **Speichern** und speichern Sie die Datei auf Ihrer Arbeitsstation.
5.  Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

        azure account import <file>

    Für den vorigen Screenshot wurde die Einstellungsveröffentlichungsdatei im Ordner C:\\HDInsight auf der Arbeitsstation gespeichert.

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um ein HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum befinden.

**So erstellen Sie ein Azure-Speicherkonto**

-   Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

          azure account storage create [options] <StorageAccountName>

Weitere Informationen zum Erstellen eines Azure-Speicherkontos im Azure-Verwaltungsportal finden Sie unter [Erstellen eines Speicherkontos](/de-de/manage/services/storage/how-to-create-a-storage-account/).

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

    -- Speicherkonten auflisten
    azure account storage list
    -- Speicherkonto anzeigen
    azure account storage show <StorageAccountName>
    -- Schlüssel für ein Speicherkonto auflisten
    azure account storage keys list <StorageAccountName>

Details zum Abrufen dieser Informationen im Verwaltungsportal finden Sie im Bereich *Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* des Artikels [Verwalten von Speicherkonten](/de-de/manage/services/storage/how-to-manage-a-storage-account/).

Der Befehl *azure hdinsight cluster create* erstellt den Container, falls dieser nicht existiert. Falls Sie den Container zuvor erstellen möchten, können Sie den folgenden Befehl verwenden:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie ein Cluster erstellen.

**So erstellen Sie ein HDInsight-Cluster**

-   Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

          azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation](./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png)



Normalerweise stellen Sie ein HDInsight-Cluster bereit, führen die entsprechenden Aufgaben aus und löschen das Cluster anschließend, um die Kosten zu senken. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

**Bereitstellung eines HDInsight-Clusters mit einer Konfigurationsdatei**

-   Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

          azure hdinsight cluster config create <file>
             
          azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
             
          azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
                 --storageAccountKey "<StorageAccountKey>"
             
          azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
             
          azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
             
          azure hdinsight cluster create --config <file>

    ![HDI.CLIClusterCreationConfig](./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png)

**Auflisten und Anzeigen von Clusterdetails**

-   Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

          azure hdinsight cluster list
          azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster](./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Cluster auflisten und anzeigen")

**Löschen von Clustern**

-   Mit dem folgenden Befehl können Sie ein Cluster löschen:

          azure hdinsight cluster delete <ClusterName>

Das HDInsight .NET SDK
----------------------

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET.

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe des SDK ausgeführt werden:

-   Installation des HDInsight .NET SDK
-   Erstellen einer Konsolenanwendung
-   Ausführen der Anwendung

**Installation des HDInsight .NET SDK** Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) installieren. Sie finden entsprechenden Anweisungen in der nächsten Prozedur.

**Erstellung einer Konsolenanwendung in Visual Studio**

1.  Öffnen Sie Visual Studio 2012.

2.  Klicken Sie im Menü Datei auf **Neu** und anschließend auf **Projekt**.

3.  Unter Neues Projekt können Sie die folgenden Werte eingeben bzw. auswählen:

    <table data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
		<tr data-morhtml="true">
			<th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
			<th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
			<td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
			<td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
			<td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td>
		</tr>
	</table>

4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

5.  Klicken Sie im Menü **Extras** auf **Paketverwaltung** und anschließend auf **Paketverwaltungskonsole**.

6.  Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

         Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Dieser Befehl installiert .NET-Bibliotheken und fügt Verweise zum aktuellen Visual Studio-Projekt hinzu.

7.  Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8.  Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

         using System.Security.Cryptography.X509Certificates;
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Fügen Sie in der Main()-Funktion den folgenden Code ein:

         string certfriendlyname = "<CertificateFriendlyName>";
         string subscriptionid = "<WindowsAzureSubscriptionID>";
         string clustername = "<HDInsightClusterName>";
         string location = "<MicrosoftDataCenter>";
         string storageaccountname = "<WindowsAzureStorageAccountName>";     // Full path must be used
         string storageaccountkey = "<WindowsAzureStorageAccountKey>";
         string containername = "<HDInsightDefaultContainerName>";
         string username = "<HDInsightUsername>";
         string password = "<HDInsightUserPassword>";
         int clustersize = <NumberOfNodesInTheCluster>;

         // Zertifikat-Objekt aus dem Zertifikatspeicher anhand des Anzeigenamens abrufen
         X509Store store = new X509Store();
         store.Open(OpenFlags.ReadOnly);
         X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

         // Speicherkonto erstellen, falls dieses nicht existiert.

         // Container erstellen, falls dieser nicht existiert.

         // HDInsightClient-Objekt erstellen
         HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
         var client = HDInsightClient.Connect(creds);

         // Clusterinformationen angeben
         ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
         {
             Name = clustername,
             Location = location,
             DefaultStorageAccountName = storageaccountname,
             DefaultStorageAccountKey = storageaccountkey,
             DefaultStorageContainer = containername,
             UserName = username,
             Password = password,
             ClusterSizeInNodes = clustersize
         };

         // Cluster erstellen
         Console.WriteLine("Creating the HDInsight cluster ...");

         ClusterDetails cluster = client.CreateCluster(clusterInfo);

         Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
         Console.WriteLine("Press ENTER to continue.");
         Console.ReadKey();

10. Ersetzen Sie die Variablen am Anfang der main()-Funktion.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. Ein Konsolenfenster wird geöffnet und zeigt den Status der Anwendung an. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.

Nächste Schritte
----------------

In diesem Artikel haben Sie mehrere Möglichkeiten zu Bereitstellung von HDInsight-Clustern kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Administer HDInsight using PowerShell (Verwalten von HDInsight mit PowerShell, in englischer Sprache)](/de-de/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Programmgesteuerte Übermittlung von Hadoop-Aufgaben](/de-de/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Dokumentation des Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

[hdinsight-version]: /de-de/manage/services/hdinsight/versioning-in-hdinsight/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
[hdinsight-getting-started]: /de-de/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-storage]: /de-de/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /de-de/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-submit-jobs]: /de-de/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-configure-powershell]: /de-de/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 
[hdinsight-powershell-reference]: http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx

[azure-create-storageaccount]: /de-de/manage/services/storage/how-to-create-a-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/
[azure-command-line-tools]: /de-de/develop/nodejs/how-to-guides/command-line-tools/
[azure-command-line-tool]: /de-de/manage/linux/other-resources/command-line-tools/
[azure-manage-storageaccount]: /de-de/manage/services/storage/how-to-manage-a-storage-account/
[azure-purchase-options]: https://www.windowsazure.com/en-us/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/en-us/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/en-us/pricing/free-trial/

[Powershell-install-configure]: /de-de/documentation/articles/install-configure-powershell/


[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png 
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png 
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png 
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png 

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
