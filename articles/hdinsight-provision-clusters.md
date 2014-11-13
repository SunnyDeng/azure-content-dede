<properties urlDisplayName="HDInsight Administration" pageTitle="Bereitstellen von Hadoop-Clustern in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Erfahren Sie, wie Sie Cluster f&uuml;r Azure HDInsight im Verwaltungsportal oder mithilfe von PowerShell oder der Befehlszeile bereitstellen k&ouml;nnen." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Bereitstellen von Hadoop-Clustern in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight

Dieser Artikel beschreibt die verschiedenen Möglichkeiten für die benutzerdefinierte Bereitstellung eines Hadoop-Clusters in Azure HDInsight: Azure-Verwaltungsportal, PowerShell, Befehlszeilentools oder HDInsight .NET SDK. Dieser Artikel behandelt die Bereitstellung eines Hadoop-Clusters. Informationen zur Bereitstellung eines HBase-Clusters finden Sie unter [Bereitstellen eines HBase-Clusters in HDInsight][Bereitstellen eines HBase-Clusters in HDInsight]. Unter [Worin besteht der Unterschied zwischen Hadoop und HBase?][Worin besteht der Unterschied zwischen Hadoop und HBase?] (What's the difference between Hadoop and HBase?, in englischer Sprache) finden Sie einen Vergleich der beiden Technologien.

## Was ist ein HDInsight-Cluster?

Vielleicht haben Sie sich schon gefragt, warum wir jedesmal Cluster erwähnen, wenn wir über Hadoop oder Big Data sprechen. Das liegt daran, dass Hadoop die verteilte Verarbeitung großer Datenmengen auf mehreren Knoten eines Clusters ermöglicht. Cluster haben eine Master/Slave-Architektur mit einem Master (oder Stamm- bzw. Namensknoten) und einer beliebigen Anzahl von Slaves (oder Datenknoten). Weitere Informationen finden Sie unter [Apache Hadoop][Apache Hadoop].

![HDInsight-Cluster][HDInsight-Cluster]

HDInsight-Cluster abstrahieren die Hadoop-Implementierungsdetails, sodass Sie sich keine Gedanken über die Kommunikation zwischen den einzelnen Clusterknoten machen müssen. Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. Weitere Informationen finden Sie unter [Einführung in Hadoop in HDInsight][Einführung in Hadoop in HDInsight]. Die zu verarbeitenden Daten liegen im Azure-Blobspeicher, auch bezeichnet als *Azure-Speicher - Blob* (oder WASB) im Kontext von HDInsight. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight].

Dieser Artikel beschreibt die verschiedenen Möglichkeiten bei der Einrichtung eines Clusters. Falls Sie auf der Suche nach einem Schnelleinstieg in die Clusterbereitstellung sind, lesen Sie [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight].

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

-   Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Die HDInsight PowerShell-Cmdlets führen Aufgaben für Ihr Abonnement aus. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][Kaufoptionen], [Spezielle Angebote][Spezielle Angebote] oder [Kostenlose Testversion][Kostenlose Testversion].

## Themen in diesem Artikel

-   [Konfigurationsoptionen][Konfigurationsoptionen]
-   [Verwenden des Azure-Verwaltungsportals][Verwenden des Azure-Verwaltungsportals]
-   [Verwenden der Azure PowerShell][Verwenden der Azure PowerShell]
-   [Die plattformübergreifende Befehlszeile][Die plattformübergreifende Befehlszeile]
-   [Verwenden des HDInsight .NET SDK][Verwenden des HDInsight .NET SDK]
-   [Nächste Schritte][Nächste Schritte]

## <span id="configuration"></span></a>Konfigurationsoptionen

### Zusätzlicher Speicher

Bei der Konfiguration müssen Sie ein Azure-Blobspeicherkonto und einen Standardcontainer angeben. Das Cluster verwendet diese Option als Standard-Speicherort. Sie können optional zusätzliche Blobs angeben, die ebenfalls Ihrem Cluster zugeordnet werden.

Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden des Azure-Blobspeichers mit HDInsight][Verwenden des Azure-Blobspeichers mit HDInsight].

### Metastore

Der Metastore enthält Informationen über Hive-Tabellen, Partitionen, Schemas, Spalten usw.. Hive verwendet diese Informationen, um die Daten im HDFS zu lokalisieren (bzw. WASB für HDInsight). Standardmäßig speichert Hive diese Informationen in einer eingebetteten Datenbank.

Bei der Bereitstellung eines HDInsight-Clusters können Sie eine SQL-Datenbank angeben, die als Metastore für Hive verwendet werden soll. Auf diese Weise bleiben die Metadaten-Informationen erhalten, wenn Sie ein Cluster löschen, da diese extern in einer SQL-Datenbank liegen.

### Virtuelle Netzwerke

[Virtuelle Azure-Netzwerke][Virtuelle Azure-Netzwerke] bieten ein sicheres, persistentes Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

-   Verbinden von Cloud-Ressourcen in einem privaten Netzwerk (Nur-Cloud)

    ![Diagramm der Nur-Cloud-Konfiguration][Diagramm der Nur-Cloud-Konfiguration]

-   Verbinden Ihrer Cloud-Ressourcen mit dem Netzwerk in Ihrem lokalen Rechenzentrum (Standort-zu-Standort oder Punkt-zu-Standort) mithilfe eines virtuellen privaten Netzwerks (VPN)

    Mit einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum mit dem virtuellen Azure-Netzwerk über ein Hardware-VPN oder den Routing- und RAS-Dienst verbinden

    ![Diagramm der Standort-zu-Standort-Konfiguration][Diagramm der Standort-zu-Standort-Konfiguration]

    Mit einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden

    ![Diagramm der Punkt-zu-Standort-Konfiguration][Diagramm der Punkt-zu-Standort-Konfiguration]

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke][Überblick über virtuelle Azure-Netzwerke].

> [WACOM.NOTE] Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie ein HDInsight-Cluster bereitstellen. Weitere Informationen finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke][Konfigurationsaufgaben für virtuelle Netzwerke].
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.

> [WACOM.NOTE] Es wird dringend empfohlen, ein einzelnes Subnetz einem einzelnen Cluster zuzuordnen.

## <span id="portal"></span></a> Verwenden des Azure-Verwaltungsportals

HDInsight-Cluster verwenden einen Azure-Blobspeicher-Container als Standard-Dateisystem. Sie benötigen ein Speicherkonto im entsprechenden Rechenzentrum, um einen HDInsight-Cluster erstellen zu können. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight]. Weitere Informationen zum Erstellen eines Azure-Speicherkontos finden Sie unter [Erstellen eines Speicherkontos][Erstellen eines Speicherkontos].

> [WACOM.NOTE] HDInsight-Cluster sind momentan nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **Osten USA**, **Westen USA**, **USA Nord Mitte** und **USA Süd Mitte** verfügbar.

**So erstellen Sie einen HDInsight-Cluster über die benutzerdefinierte Erstellungsoption**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie im unteren Seitenbereich auf **+ NEW**, anschließend auf **DATA SERVICES**, auf **HDINSIGHT**und zuletzt auf **CUSTOM CREATE**.
3.  Geben Sie auf der Seite **Clusterdetails** die folgenden Daten ein:

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Eigenschaft</th>
    <th align="left">Wert</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Clustername</td>
    <td align="left"><p>Der Name des Clusters.</p>
    <ul>
    <li>Der DNS-Name muss mit einem alphanumerischen Zeichen beginnen und enden und darf Bindestriche enthalten.</li>
    <li>Der Wert in diesem Feld muss zwischen 3 und 63 Zeichen lang sein.</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Clustertyp</td>
    <td align="left">Wählen Sie unter &quot;Clustertyp&quot; den Eintrag <strong>Hadoop</strong> aus.</td>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight-Version</td>
    <td align="left">Wählen Sie die Version aus. HDInsight Version 3.1 ist die Standardversion für Hadoop und verwendet Hadoop Version 2.4.</td>
    </tr>
    </tbody>
    </table>

    Geben Sie die Werte wie in der Tabelle gezeigt ein und klicken Sie auf den Pfeil nach rechts.

4.  Geben Sie auf der Seite **Cluster konfigurieren** die folgenden Daten ein:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Name</th>
    <th align="left">Wert</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Datenknoten</td>
    <td align="left">Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten.<br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td>
    </tr>
    <tr class="even">
    <td align="left">Region/virtuelles Netzwerk</td>
    <td align="left"><p>Wählen Sie dieselbe Region wie für das Speicherkonto, das Sie im letzten Verfahren erstellt haben. In HDInsight muss das Speicherkonto sich in derselben Region befinden. Später in dieser Konfiguration können Sie nur ein Speicherkonto wählen, das sich in der hier angegebenen Region befindet.</p>
    <p>Die verfügbaren Regionen sind: <strong>Ostasien</strong>, <strong>Südostasien</strong>, <strong>Nordeuropa</strong>, <strong>Westeuropa</strong>, <strong>Osten USA</strong>, <strong>Westen USA</strong>, <strong>USA Nord Mitte</strong>, <strong>USA Süd Mitte</strong><br />Wenn Sie ein virtuelles Azure-Netzwerk erstellt haben, können Sie das Netzwerk auswählen, mit dem das HDInsight-Cluster verwendet werden soll.</p>
    <p>Weitere Informationen zur Erstellung von virtuellen Azure-Netzwerken finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](http://msdn.microsoft.com/de-de/library/azure/jj156206.aspx).</p></td>
    </tr>
    </tbody>
    </table>

5.  Geben Sie auf der Seite **Clusterbenutzer konfigurieren** die folgenden Daten ein:

    ![HDI.CustomCreateCluster.ClusterUser][HDI.CustomCreateCluster.ClusterUser]

    | Eigenschaft                  | Wert                                                                                                                                                                                                                                                                                                                                            |
    |------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Benutzername                 | Geben Sie den Benutzernamen für den HDInsight-Cluster an.                                                                                                                                                                                                                                                                                       |
    | Kennwort/Kennwort bestätigen | Geben Sie das Kennwort für den HDInsight-Cluster an.                                                                                                                                                                                                                                                                                            |
    | Enter Hive/Oozie Metastore   | Markieren Sie dieses Kontrollkästchen, um eine SQL-Datenbank im gleichen Rechenzentrum wie der Cluster für die Verwendung als Hive-/Oozie-Metastore anzugeben. Dies ist praktisch, wenn Sie die Metadaten von Hive-/Oozie-Jobs behalten möchten, nachdem der Cluster gelöscht wurde.                                                            |
    | Metastore-Datenbank          | Geben Sie die Azure SQL-Datenbank an, die als Metastore für Hive/Oozie verwendet werden soll. Die SQL-Datenbank muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden. Dieses Listenfeld enthält nur die SQL-Datenbanken, die sich im gleichen Rechenzentrum befinden, das Sie auf der Seite **Clusterdetails** angegeben haben. |
    | Datenbankbenutzer            | Geben Sie den SQL-Datenbankbenutzer an, der für die Verbindung zur Datenbank verwendet wird.                                                                                                                                                                                                                                                    |
    | Datenbank-Benutzerkennwort   | Geben Sie das Kennwort des SQL-Datenbankbenutzers an.                                                                                                                                                                                                                                                                                           |

    > [WACOM.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Azure SQL-Datenbank-Dashboard mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Windows Azure-Dienste** den Wert **Ja** aus und klicken Sie auf **Speichern**.

    Klicken Sie auf den Pfeil nach rechts.

6.  Geben Sie auf der Seite **Speicherkonto** den folgenden Wert ein:

    ![HDI.CustomCreateCluster.StorageAccount][HDI.CustomCreateCluster.StorageAccount]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Eigenschaft</th>
    <th align="left">Wert</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Speicherkonto</td>
    <td align="left">Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten:
    <ul>
    <li>Vorhandenen Speicher verwenden</li>
    <li>Neuen Speicher erstellen</li>
    <li>Speicher aus anderem Abonnement verwenden</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Kontoname</td>
    <td align="left"><ul>
    <li>Falls Sie einen vorhandenen Speicher verwenden, wählen Sie unter <strong>Kontoname</strong> ein vorhandenes Speicherkonto aus. Die Dropdownliste enthält nur die Speicherkonten im gleichen Rechenzentrum, in dem Sie auch den Cluster eingerichtet haben.</li>
    <li>Falls Sie <strong>Neuen Speicher erstellen</strong> oder <strong>Speicher aus anderem Abonnement verwenden</strong> ausgewählt haben, müssen Sie den Namen des Speicherkontos angeben.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Kontoschlüssel</td>
    <td align="left">Geben Sie den Speicherschlüssel für das entsprechende Konto ein, falls Sie <strong>Speicher aus anderem Abonnement verwenden</strong> ausgewählt haben.</td>
    </tr>
    <tr class="even">
    <td align="left">Standardcontainer</td>
    <td align="left"><p>Gibt den Standardcontainer im Speicherkonto an, der als Standard-Dateisystem für den HDInsight-Cluster verwendet werden soll. Falls Sie <strong>Vorhandenen Speicher verwenden</strong> im Feld <strong>Speicherkonto</strong> ausgewählt haben und dieses Konto keine Container enthält, wird der Container standardmäßig mit demselben Namen wie der Cluster erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt. Zum Beispiel mycontainer1, mycontainer2 und so weiter. Sie können jedoch auch Container im vorhandenen Speicherkonto verwenden, die einen anderen Namen als der Cluster haben.</p>
    <p>Falls Sie einen neuen Speicher erstellen oder einen Speicher aus einem anderen Azure-Abonnement verwenden, müssen Sie den Namen des Standardcontainers angeben</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Zusätzliche Speicherkonten</td>
    <td align="left">HDInsight unterstützt mehrere Speicherkonten. Ein Cluster kann beliebig viele Speicherkonten verwenden. Wenn Sie den Cluster jedoch im Verwaltungsportal erstellen, können Sie aufgrund von Einschränkungen der Benutzeroberfläche maximal sieben Speicherkonten einrichten. Für jedes angegebene Speicherkonto wird eine zusätzliche Seite im Assistenten hinzugefügt, in der Sie die Kontoinformationen angeben können. Im obigen Screenshot wurde z. B. ein zusätzliches Speicherkonto ausgewählt, und Seite 5 wurde zum Dialog hinzugefügt.</td>
    </tr>
    </tbody>
    </table>

    Klicken Sie auf den Pfeil nach rechts.

7.  Geben Sie auf der Seite **Speicherkonto** die Kontoinformationen für das zusätzliche Speicherkonto ein:

    ![HDI.CustomCreateCluster.AddOnStorage][HDI.CustomCreateCluster.AddOnStorage]

    Hier haben Sie erneut die Auswahl zwischen einem vorhandenen Speicher, einem neu erstellten Speicher und einem Speicher aus einem anderen Azure-Abonnement. Die Angabe der Werte erfolgt auf dieselbe Weise wie im vorherigen Schritt.

    Klicken Sie auf das Häkchen, um mit der Bereitstellung des Clusters zu beginnen. Wenn die Bereitstellung abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

    > [WACOM.NOTE] Nachdem Sie ein Azure-Speicherkonto für Ihren HDInsight-Cluster ausgewählt haben, können Sie das Konto nicht mehr löschen und auch kein anderes Konto auswählen.

## <span id="powershell"></span></a> Verwenden der Azure PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Informationen zur Konfiguration einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure-PowerShell][Installieren und Konfigurieren von Azure-PowerShell]. Weitere Informationen zum Verwenden von PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell][Verwalten von HDInsight mit PowerShell]. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][HDInsight-Cmdlet-Referenz].

> [WACOM.NOTE] Die Skripts in diesem Abschnitt dienen zum Konfigurieren eines HDInsight-Clusters in einem virtuellen Azure-Netzwerk, jedoch nicht zur Erstellung des virtuellen Azure-Netzwerks. Weitere Informationen zur Erstellung von virtuellen Azure-Netzwerken finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke][Konfigurationsaufgaben für virtuelle Netzwerke].

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe der PowerShell ausgeführt werden:

-   Erstellen eines Azure-Speicherkontos
-   Erstellen eines Azure-Blob-Containers
-   Erstellen eines HDInsight-Clusters

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden.

**So erstellen Sie ein Azure-Speicherkonto**

-   Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden PowerShell-Befehlen abrufen:

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**So erstellen Sie einen Azure-Blob-Speichercontainer: erstellen**

-   Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie HDInsight-Cluster bereit**

> [WACOM.NOTE] Die PowerShell-Cmdlets sind der einzige empfohlene Weg zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster. Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden. Die per PowerShell gesetzten Konfigurationswerte bleiben erhalten, wenn das Cluster gepatcht wird.

-   Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Bereitstellung des Clusters kann einige Minuten in Anspruch nehmen.

    ![HDI.CLI.Provision][HDI.CLI.Provision]

**Sie stellen Sie einen HDInsight-Cluster mit benutzerdefinierten Optionen bereit**

Bei der Bereitstellung eines Clusters können Sie erweiterte Optionen verwenden, wie z. B. die Verbindung mit mehr als einem Azure-Blobspeicher, Verwendung eines virtuellen Netzwerks oder einer Azure SQL-Datenbank für Hive- und Oozie-Metastores. Auf diese Weise können Sie die Lebensdauer Ihrer Daten und Metadaten von der Lebensdauer des Clusters trennen.

> [WACOM.NOTE] Die PowerShell-Cmdlets sind der einzige empfohlene Weg zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster. Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden. Die per PowerShell gesetzten Konfigurationswerte bleiben erhalten, wenn das Cluster gepatcht wird.

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

        # Get the virtual network ID and subnet name
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>" 

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

    > [WACOM.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Azure SQL-Datenbank-Dashboard mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Windows Azure-Dienste** den Wert **Ja** aus und klicken Sie auf **Speichern**.

**So listen Sie HDInsight-Cluster auf**

-   Führen Sie die folgenden Befehle in einer Azure PowerShell-Konsole aus, um den HDInsight-Cluster aufzulisten und zu überprüfen, ob der Cluster erfolgreich eingerichtet wurde:

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a> Plattformübergreifende Befehlszeile

> [WACOM.NOTE] Zum 29.8.2014 kann die plattformübergreifende Befehlszeilenschnittstelle nicht verwendet werden, um ein Cluster zu einem virtuellen Azure-Netzwerk zuzuordnen.

Eine weitere Möglichkeit zur Bereitstellung von HDInsight-Clustern ist die plattformübergreifende Befehlszeile. Das Befehlszeilentool ist in Node.js implementiert. Dieses Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, einschließlich Windows, Mac und Linux. Das Befehlszeilentool ist Open Source. Der Quellcode wird auf GitHub unter <https://github.com/Azure/azure-sdk-tools-xplat> verwaltet. Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux][Verwenden der Azure-Befehlszeilentools für Mac und Linux]. Umfassendes Referenzmaterial finden Sie unter [Azure-Befehlszeilentool für Mac und Linux][Azure-Befehlszeilentool für Mac und Linux]. Dieser Artikel behandelt nur die Befehlszeilenschnittstelle unter Windows.

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters in der plattformübergreifenden Befehlszeile ausgeführt werden:

-   Installieren der plattformübergreifenden Befehlszeile
-   Download und Import der Veröffentlichungseinstellungen für das Azure-Konto
-   Erstellen eines Azure-Speicherkontos
-   Bereitstellen eines Clusters

Sie können die Befehlszeilenschnittstelle entweder über den *Node.js-Paketmanager (NPM)* oder über den Windows Installer installieren. Microsoft empfiehlt, dass Sie nur eine der beiden Optionen für die Installation verwenden.

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

1.  Navigieren Sie zur Webseite **<http://azure.microsoft.com/de-de/downloads/>**.
2.  Blättern Sie nach unten zum Bereich **Befehlszeilentools**, klicken Sie auf **Plattformübergreifende Befehlszeilenschnittstelle** und führen Sie den Webplattform-Installer aus.

**So laden Sie Veröffentlichungseinstellungen herunter und importieren sie**

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [WACOM.NOTE] Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.

1.  Öffnen Sie eine **Eingabeaufforderung**.
2.  Führen Sie den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei herunterzuladen.

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können.

3.  Klicken Sie in der Aufforderung auf **Speichern** und geben Sie einen Speicherort für die Datei an.
4.  Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

        azure account import <file>

    Für den vorigen Screenshot wurde die Einstellungsveröffentlichungsdatei im Ordner C:\\HDInsight auf der Arbeitsstation gespeichert.

**So erstellen Sie ein Azure-Speicherkonto**

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum befinden.

-   Führen Sie den folgenden Befehl in einem Eingabeaufforderungsfenster aus, um ein Azure-Speicherkonto zu erstellen:

        azure storage account create [options] <StorageAccountName>

    Wählen Sie einen geeigneten Ort für die Bereitstellung des HDInsight-Clusters aus, wenn Sie dazu aufgefordert werden. Der Speicher muss sich am gleichen Standort befinden wie der HDInsight-Cluster. HDInsight-Cluster sind momentan nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **Osten USA**, **Westen USA**, **USA Nord Mitte** und **USA Süd Mitte** verfügbar.

Weitere Informationen zum Erstellen eines Azure-Speicherkontos im Azure-Verwaltungsportal finden Sie unter [Erstellen eines Speicherkontos][Erstellen eines Speicherkontos].

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

Details zum Abrufen dieser Informationen im Verwaltungsportal finden Sie im Bereich *Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* des Artikels [Verwalten von Speicherkonten][Verwalten von Speicherkonten].

Für Ihren HDInsight-Cluster benötigen Sie außerdem einen Container innerhalb des Speicherkontos. Falls das angegebene Speicherkonto noch keinen Container enthält, werden Sie von *azure hdinsight cluster create* zur Eingabe eines Containernamens aufgefordert, und der Container wird erstellt. Sie können den folgenden Befehl verwenden, um den Container vorab zu erstellen:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie HDInsight-Cluster bereit**

-   Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

**So stellen Sie einen HDInsight-Cluster mit einer Konfigurationsdatei bereit**

Normalerweise erstellen Sie einen HDInsight-Cluster, führen Ihre Jobs aus und löschen den Cluster anschließend, um Kosten zu sparen. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

-   Führen Sie im Eingabeaufforderungsfenster die folgenden Befehle aus:

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Azure SQL-Datenbank-Dashboard mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Windows Azure-Dienste** den Wert **Ja** aus und klicken Sie auf **Speichern**.

    ![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

**So listen Sie Clusterdetails auf und zeigen sie an**

-   Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][HDI.CLIListCluster]

**So löschen Sie Cluster**

-   Mit dem folgenden Befehl können Sie einen Cluster löschen:

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> Verwenden des HDInsight .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen.

Führen Sie die folgenden Schritte aus, um einen HDInsight-Cluster mithilfe des SDK bereitzustellen:

-   Installation des HDInsight .NET SDK
-   Erstellen eines selbstsignierten Zertifikats
-   Erstellen einer Konsolenanwendung
-   Ausführen der Anwendung

**So installieren Sie das HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet][NuGet] installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats][Erstellen eines selbstsignierten Zertifikats].

**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1.  Öffnen Sie Visual Studio 2013.

2.  Klicken Sie im Menü Datei auf **Neu** und anschließend auf **Projekt**.

3.  Unter Neues Projekt können Sie die folgenden Werte eingeben bzw. auswählen:

    | Eigenschaft | Wert                        |
    |-------------|-----------------------------|
    | Kategorie   | Vorlagen/Visual C#/Windows |
    | Vorlage     | Konsolenanwendung           |
    | Name        | CreateHDICluster            |

4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

5.  Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6.  Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7.  Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8.  Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Fügen Sie in der Main()-Funktion den folgenden Code ein:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
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

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Ersetzen Sie die Variablen am Anfang der main()-Funktion.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight]
-   [Verwalten von HDInsight mit PowerShell][Verwalten von HDInsight mit PowerShell]
-   [Programmgesteuerte Übermittlung von Hadoop-Jobs][Programmgesteuerte Übermittlung von Hadoop-Jobs]
-   [Dokumentation zum Azure HDInsight SDK][Dokumentation zum Azure HDInsight SDK]

  [Bereitstellen eines HBase-Clusters in HDInsight]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-hbase-get-started/
  [Worin besteht der Unterschied zwischen Hadoop und HBase?]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [HDInsight-Cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png
  [Einführung in Hadoop in HDInsight]: ../hdinsight-hadoop-introduction/
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Kaufoptionen]: http://azure.microsoft.com/de-de/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/de-de/pricing/member-offers/
  [Kostenlose Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Konfigurationsoptionen]: #configuration
  [Verwenden des Azure-Verwaltungsportals]: #portal
  [Verwenden der Azure PowerShell]: #powershell
  [Die plattformübergreifende Befehlszeile]: #cli
  [Verwenden des HDInsight .NET SDK]: #sdk
  [Nächste Schritte]: #nextsteps
  [Verwenden des Azure-Blobspeichers mit HDInsight]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-use-blob-storage/
  [Virtuelle Azure-Netzwerke]: http://azure.microsoft.com/de-de/documentation/services/virtual-network/
  [Diagramm der Nur-Cloud-Konfiguration]: .\media\hdinsight-provision-clusters\cloud-only.png
  [Diagramm der Standort-zu-Standort-Konfiguration]: .\media\hdinsight-provision-clusters\site-to-site.png
  [Diagramm der Punkt-zu-Standort-Konfiguration]: .\media\hdinsight-provision-clusters\point-to-site.png
  [Überblick über virtuelle Azure-Netzwerke]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [Konfigurationsaufgaben für virtuelle Netzwerke]: http://msdn.microsoft.com/de-de/library/azure/jj156206.aspx
  [Erstellen eines Speicherkontos]: ../storage-create-storage-account/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Installieren und Konfigurieren von Azure-PowerShell]: ../install-configure-powershell/
  [Verwalten von HDInsight mit PowerShell]: ../hdinsight-administer-use-powershell/
  [HDInsight-Cmdlet-Referenz]: http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [Verwenden der Azure-Befehlszeilentools für Mac und Linux]: ../xplat-cli/
  [Azure-Befehlszeilentool für Mac und Linux]: ../command-line-tools/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [Verwalten von Speicherkonten]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Cluster auflisten und anzeigen"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [Erstellen eines selbstsignierten Zertifikats]: http://go.microsoft.com/fwlink/?LinkId=511138
  [Programmgesteuerte Übermittlung von Hadoop-Jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Dokumentation zum Azure HDInsight SDK]: http://msdn.microsoft.com/de-de/library/dn479185.aspx
