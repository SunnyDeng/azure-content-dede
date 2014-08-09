<properties linkid="manage-services-hdinsight-administer-hdinsight-using-command-line" urlDisplayName="HDInsight Administration" pageTitle="Administer HDInsight using using the Cross-Platform Command-Line Interface | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to use the Cross-Platform Command-Line Interface to manage HDInsight clusters on any platform that supports Node.js, including Windows, Mac, and Linux." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer HDInsight using the Cross-platform Command-line Interface" authors="jgao" />

Verwalten von HDInsight mit der plattformübergreifenden Befehlszeile
====================================================================

In diesem Artikel erfahren Sie, wie Sie HDInsight-Cluster mit der plattformübergreifenden Befehlszeile verwalten können. Das Befehlszeilentool ist in Node.js implementiert. Dieses Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, inklusive Windows, Mac und Linux.

Das Befehlszeilentool ist Open Source. Der Quellcode liegt auf GitHub unter <https://github.com/WindowsAzure/azure-sdk-tools-xplat>.

Dieser Artikel behandelt nur die Befehlszeilenschnittstelle unter Windows. Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/). Umfassendes Referenzmaterial finden Sie unter [Azure-Befehlszeilentool für Mac und Linux](/de-de/manage/linux/other-resources/command-line-tools/).

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Ein Azure-Abonnement**. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Spezielle Angebote](https://www.windowsazure.com/en-us/pricing/member-offers/) oder [Kostenlose Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/).

Themen in diesem Artikel
------------------------

-   [Installation](#installation)
-   [Download und Import der Veröffentlichungseinstellungen für das Azure-Konto](#importsettings)
-   [Bereitstellen eines Clusters](#provision)
-   [Bereitstellen eines Clusters mit einer Konfigurationsdatei](#provisionconfigfile)
-   [Auflisten und Anzeigen von Clustern](#listshow)
-   [Löschen eines Clusters](#delete)
-   [Nächste Schritte](#nextsteps)

Installation
------------

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

Download und Import der Veröffentlichungseinstellungen für das Azure-Konto
--------------------------------------------------------------------------

Bevor Sie die Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Befehlszeilenschnittstelle verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Einstellung importiert werden und wird von der Befehlszeilenschnittstelle in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [WACOM.NOTE] Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Sie sollten die Datei entweder löschen oder zusätzliche Maßnahmen ergreifen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.

**Herunterladen und Importieren der Veröffentlichungseinstellungen**

1.  Öffnen Sie eine **Eingabeaufforderung**.
2.  Führen Sie den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei herunterzuladen.

        azure account download

    ![HDI.CLIAccountDownloadImport](./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png)

    Der Befehl zeigt die Anweisungen für den Download der Datei an, inklusive einer URL.

3.  Öffnen Sie **Internet Explorer** und besuchen Sie die im Eingabeaufforderungsfenster angezeigte URL.
4.  Klicken Sie auf **Speichern** und speichern Sie die Datei auf Ihrer Arbeitsstation.
5.  Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

        azure account import <file>

    Für den vorigen Screenshot wurde die Einstellungsveröffentlichungsdatei im Ordner C:\\HDInsight auf der Arbeitsstation gespeichert.

Bereitstellen eines HDInsight-Clusters
--------------------------------------

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um ein HDInsight-Cluster erstellen zu können.

Nachdem Sie die Datei mit Veröffentlichungseinstellungen importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

    azure account storage create [options] <StorageAccountName>

> [WACOM.NOTE] Das Speicherkonto muss sich im selben Rechenzentrum befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

><ul>
<li>Südostasien</li>
<li>Nordeuropa</li>
<li>Westeuropa</li>
<li>USA (Ost)</li>
<li>USA (West)</li>
</ul>

Weitere Informationen zum Erstellen eines Azure-Speicherkontos im Azure-Verwaltungsportal finden Sie unter [Erstellen eines Speicherkontos](/de-de/manage/services/storage/how-to-create-a-storage-account/).

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

    -- Speicherkonten auflisten
    azure account storage list
    -- Speicherkonto anzeigen
    azure account storage show <StorageAccountName>
    -- Schlüssel für ein Speicherkonto auflisten
    azure account storage keys list <StorageAccountName>

Details zum Abrufen dieser Informationen im Verwaltungsportal finden Sie im Bereich *Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* des Artikels [How to Manage Storage Accounts](/de-de/manage/services/storage/how-to-manage-a-storage-account/) (Verwalten von Speicherkonten, in englischer Sprache).

Der Befehl *azure hdinsight cluster create* erstellt den Container, falls dieser nicht existiert. Falls Sie den Container zuvor erstellen möchten, können Sie den folgenden Befehl verwenden:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie ein Cluster erstellen:

    azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation](./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png)

Bereitstellen eines HDInsight-Clusters mit einer Konfigurationsdatei
--------------------------------------------------------------------

Normalerweise stellen Sie ein HDInsight-Cluster bereit, führen die entsprechenden Aufgaben aus und löschen das Cluster anschließend, um die Kosten zu senken. In der Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

    azure hdinsight cluster config create <file>
     
    azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
     
    azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
           --storageAccountKey "<StorageAccountKey>"
     
    azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
           --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
     
    azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
           --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
     
    azure hdinsight cluster create --config <file>

![HDI.CLIClusterCreationConfig](./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png)

Auflisten und Anzeigen von Clusterdetails
-----------------------------------------

Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster](./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Cluster auflisten und anzeigen")

Löschen eines Clusters
----------------------

Mit dem folgenden Befehl können Sie ein Cluster löschen:

    azure hdinsight cluster delete <ClusterName>

Nächste Schritte
----------------

Sie sind nun in der Lage, verschiedene Verwaltungsaufgaben für HDInsight-Cluster auszuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal](/de-de/manage/services/hdinsight/howto-administer-hdinsight/)
-   [Verwalten von HDInsight mit PowerShell](/de-de/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Verwenden des Azure-Befehlszeilentools für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/)
-   [Azure-Befehlszeilentool für Mac und Linux](/de-de/manage/linux/other-resources/command-line-tools/)

