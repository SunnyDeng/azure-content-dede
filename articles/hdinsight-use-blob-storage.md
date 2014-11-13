<properties urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="Verwenden des Blobspeichers mit Hadoop in HDInsight | Azure" metaKeywords="" description="Erfahren Sie, wie HDInsight den Blobspeicher als zugrunde liegenden Datenspeicher f&uuml;r HDFS verwendet und wie Sie Daten aus dem Speicher abrufen k&ouml;nnen." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Verwenden des Azure-Blobspeichers mit Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Verwenden des Azure-Blobspeichers mit Hadoop in HDInsight

Azure HDInsight unterstützt sowohl Hadoop Distributed Files System (HDFS) als auch Azure Blob-Speicher zum Speichern von Daten. Blob-Speicher ist eine robuste Azure-Speicherlösung für allgemeine Zwecke. Blob-Speicher bietet eine umfangreiche HDFS-Dateisystemoberfläche mit reibungsloser Leistung, die dafür sorgt, dass alle Komponenten im Hadoop-System (standardmäßig) direkt mit den Daten arbeiten, die sie verwalten. Blob-Speicher ist nicht nur eine kostengünstige Lösung, sondern sorgt auch dafür, dass HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.

> [WACOM.NOTE] Die *asv://*-Syntax wird in HDInsight-Clustern der Version 3.0 und in späteren Versionen nicht unterstützt. Dies bedeutet, dass alle an einen HDInsight-Cluster der Version 3.0 übermittelten Aufträge, die die “asv://”-Syntax verwenden, fehlschlagen werden. Stattdessen sollte die *wasb://*-Syntax verwendet werden. An HDInsight-Cluster der Version 3.0 gesendete Jobs, die mithilfe eines vorhandenen Metastores erzeugt wurden, der explizite Verweise auf Ressourcen mit der asv://-Syntax enthält, schlagen ebenfalls fehl. Diese Metastores müssen mit der wasb://-Syntax neu erstellt werden, um die Ressourcen zu adressieren.

> [WACOM.NOTE] HDInsight unterstützt aktuell nur Blockblobs.

> [WACOM.NOTE]
> Die meisten HDFS-Befehle wie z. B. **ls**, **copyFromLocal**, **mkdir** usw. funktionieren weiterhin wie erwartet. Nur die für die native HDFS-Implementierung (als DFS bezeichnet) spezifischen Befehle wie**fschk** und **dfsadmin** zeigen in Azure Blob-Speicher ein anderes Verhalten.

Informationen zur Bereitstellung eines HDInsight-Clusters finden Sie unter [Erste Schritte mit HDInsight][Erste Schritte mit HDInsight] oder [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern].

## Themen in diesem Artikel

-   [HDInsight-Speicherarchitektur][HDInsight-Speicherarchitektur]
-   [Vorteile von Azure Blob-Speicher][Vorteile von Azure Blob-Speicher]
-   [Vorbereiten eines Containers für Blob-Speicher][Vorbereiten eines Containers für Blob-Speicher]
-   [Adressdateien in Blob-Speicher][Adressdateien in Blob-Speicher]
-   [Blob-Zugang über PowerShell][Blob-Zugang über PowerShell]
-   [Nächste Schritte][Nächste Schritte]

## <span id="architecture"></span></a>HDInsight-Speicherarchitektur

Das folgende Diagramm bietet einen zusammenfassenden Überblick über die HDInsight-Speicherarchitektur.

![HDI.ASVArch][HDI.ASVArch]

HDInsight bietet Zugang zum verteilten Dateisystem, das lokal an die Rechenknoten angefügt ist. Auf dieses Dateisystem kann über den vollqualifizierten URI zugegriffen werden. Beispiel:

    hdfs://<namenodehost>/<path>

Zusätzlich bietet HDInsight die Möglichkeit, auf die in Blob-Speicher gespeicherten Daten zuzugreifen. Die Syntax für den Zugriff auf Blob-Speicher lautet:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Hadoop unterstützt eine Art Standard-Dateisystem. Zu diesem gehören ein Standard-Schema und eine Standard-Berechtigung; es kann auch zur Auflösung relativer Pfade verwendet werden. Während des Prozesses zur Bereitstellung von HDInsight werden ein Azure-Speicherkonto und ein bestimmter Blob-Speichercontainer aus diesem Konto als Standard-Dateisystem festgelegt.

Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern].

-   **Container in mit einem Cluster verbundenen Speicherkonten:** Da der Kontoname und der Kontoschlüssel in der *core-site.xml* gespeichert sind, haben Sie vollständigen Zugriff auf die Blobs in diesen Containern.
-   **Öffentliche Container oder öffentliche Blobs in NICHT mit einem Cluster verbundenen Speicherkonten:** Sie haben nur Lesezugriff auf die Blobs in diesen Containern.

    > [WACOM.NOTE]
    >  \> Öffentliche Container erlauben das Abrufen einer Liste aller im Container verfügbaren Blobs und der Container-Metadaten. Auf öffentliche Blobs haben Sie nur Zugriff, wenn Sie die exakte URL kennen. Weitere Informationen finden Sie unter [Beschränken des Zugriffs auf Container und Blobs][Beschränken des Zugriffs auf Container und Blobs].

-   **Private Container in NICHT mit einem Cluster verbundenen Speicherkonten:** Sie können nur auf die Blobs im Container zugreifen, wenn Sie das Speicherkonto beim Übermitteln der WebHCat-Aufträge definieren. Dies wird weiter unten im Artikel erläutert.

Das beim Einrichtungsprozess definierte Speicherkonto und die Schlüssel werden in %HADOOP\_HOME%/conf/core-site.xml gespeichert. Standardmäßig verwendet HDInsight die in der Datei core-site.xml definierten Speicherkonten. Die Datei core-site.xml sollte nicht manuell bearbeitet werden, da der Cluster-Stammknoten (Master) jederzeit neu aus einem Abbild erstellt oder migriert werden kann, und die Änderungen an den Dateien in diesem Fall verloren gehen.

Verschiedene WebHCat-Aufträge, darunter Hive, MapReduce, Hadoop Streaming und Pig, können eine Beschreibung von Speicherkonten und Metadaten enthalten (funktioniert momentan nur für Pig und Speicherkonten, nicht für Metadaten.) Im Abschnitt [Blob-Zugriff über PowerShell][Blob-Zugang über PowerShell] dieses Artikels finden Sie ein Beispiel für dieses Feature. Weitere Informationen finden Sie unter [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores][Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores].

In Blob-Speichercontainern werden Daten als Schlüssel/Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen das Zeichen '/' verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines Blobs kann z. B. *input/log1.txt* heißen. Es existiert zwar in Wirklichkeit kein Verzeichnis namens *input*, aber aufgrund des Zeichens "/" im Schlüsselnamen sieht dieser aus wie ein Dateipfad.

## <span id="benefits"></span></a>Vorteile von Azure Blob-Speicher

Der Leistungsaufwand, der damit verbunden ist, dass die Berechnung und die Speicherung nicht am selben Ort erfolgen, wird dadurch abgeschwächt, dass die Cluster nahe an den Speicherkontoressourcen im Azure-Datencenter bereitgestellt werden, wo das Hochgeschwindigkeits-Netzwerk den Zugriff auf die Daten im Blob-Speicher für die Rechenknoten sehr effizient macht.

Die Speicherung von Daten in Blob-Speicher statt in HDFS hat mehrere Vorteile:

-   **Datenfreigabe und -wiederverwendung:** Die Daten in HDFS befinden sich innerhalb des Rechenclusters. Nur die Anwendungen, die Zugriff auf den Rechencluster haben, können die Daten über die HDFS-API verwenden. Auf die Dateien in Blob-Speicher kann entweder über die HDFS-APIs oder über die [Blob-Speicher-REST-APIs][Blob-Speicher-REST-APIs] zugegriffen werden. Somit kann eine größere Menge von Anwendungen (darunter andere HDInsight-Cluster) und Tools verwendet werden, um die Daten zu produzieren und abzurufen.
-   **Datenarchivierung:**Die Speicherung von Daten in Blob-Speicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.
-   **Datenspeicherkosten:** Die langfristige Datenspeicherung in DFS ist kostspieliger als die Datenspeicherung in Blob-Speicher, da die Kosten eines Rechenclusters höher liegen als diejenigen eines Blob-Speichercontainers. Da die Daten nicht für jede Erzeugung eines neues Rechenclusters neu geladen werden, sparen Sie auch Kosten für das Laden von Daten.
-   **Flexible horizontale Skalierung:** Während HDFS Ihnen ein horizontal skaliertes Dateisystem bietet, wird die Skalierung durch die Anzahl der Knoten bestimmt, die Sie für Ihren Cluster bereitstellen. Eine Änderung der Skalierung kann weitaus schwieriger werden, als auf die flexiblen Speicherkapazitäten von Blob-Speicher zu vertrauen, über die Sie automatisch verfügen.
-   **Georeplikation:** Ihre Blob-Speichercontainer können über das Azure-Portal georepliziert werden. Während Sie dadurch von geographischer Wiederherstellung und Datenredundanz profitieren, wirkt sich ein Ausfall des georeplizierten Standorts schwer auf Ihre Leistung aus und kann zusätzliche Kosten nach sich ziehen. Deshalb empfehlen wir, die Georeplikation mit Bedacht auszuwählen und nur dann anzuwenden, wenn der Wert der Daten die zusätzlichen Kosten rechtfertigt.

Bestimmte MapReduce-Jobs und -Pakete können zu Zwischenergebnissen führen, die Sie nicht wirklich im Blob-Speichercontainer speichern möchten. In diesem Falle können Sie sich immer noch entscheiden, die Dateien im lokalen HDFS zu speichern. Tatsächlich verwendet HDInsight DFS für einige dieser Zwischenergebnisse in Hive-Jobs und anderen Prozessen.

## <span id="preparingblobstorage"></span></a>Vorbereiten eines Containers für Blob-Speicher

Um Blobs zu verwenden, erstellen Sie zuerst ein [Azure-Speicherkonto][Azure-Speicherkonto]. Dabei geben Sie ein Azure-Datencenter an, in dem die mithilfe dieses Kontos erstellten Objekte gespeichert werden. Der Cluster und das Speicherkonto müssen im selben Datencenter gehostet werden. (Hive-Metastore-SQL-Datenbanken und Oozie-Metastore-SQL-Datenbanken müssen sich ebenfalls im selben Datencenter befinden.) Ein Blob gehört unabhängig davon, wo es sich befindet, stets zu einem Container in Ihrem Speicherkonto. Dieser Container kann ein außerhalb von HDInsight erstellter Blob-Speichercontainer sein, oder es handelt sich um einen Container, der für einen HDInsight-Cluster erstellt wird.

### Erstellen eines Blob-Containers für HDInsight mithilfe des Verwaltungsportals

Wenn Sie einen HDInsight-Cluster im Azure-Verwaltungsportal bereitstellen, haben Sie zwei Optionen: *Schnellerstellung* und *Benutzerdefinierte Erstellung*. Für die Schnellerstellung muss das Azure-Speicherkonto bereits vorher erstellt worden sein. Informationen dazu finden Sie unter [Erstellen eines Speicherkontos][Azure-Speicherkonto].

Wenn Sie die Option "Schnellerstellung" verwenden, können Sie ein vorhandenes Speicherkonto auswählen. Während des Bereitstellungsprozesses wird ein neuer Container mit dem Namen des HDInsight-Clusters erstellt. Wenn ein Container mit demselben Namen bereits vorhanden ist, wird <clustername>-<x> verwendet. Zum Beispiel myHDIcluster-1. Dieser Container wird als Standard-Dateisystem verwendet.

![HDI.Schnellerfassung][HDI.Schnellerfassung]

Mit der benutzerdefinierten Erstellung können Sie für das Standard-Speicherkonto eine der folgenden Optionen auswählen:

-   Vorhandenen Speicher verwenden
-   Neuen Speicher erstellen
-   Speicher aus anderem Abonnement verwenden.

Sie haben außerdem die Auswahl, einen neuen Blob-Container zu erstellen oder einen vorhandenen zu verwenden.

![HDI.CustomCreateStorageAccount][HDI.CustomCreateStorageAccount]

### Erstellen eines Containers mit Azure PowerShell.

[Azure PowerShell][Azure PowerShell] kann zur Erstellung von Blob-Containern verwendet werden. Nachfolgend ein PowerShell-Beispielskript:

    $subscriptionName = "<SubscriptionName>"    # Azure subscription name
    $storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
    $containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

    # Connect to your Azure account and selec the current subscription
    Add-AzureAccount # The connection will expire in a few hours.
    Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

    # Create a storage context object
    $storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext 

## <span id="addressing"></span></a>Adressdateien in Blob-Speicher

Das URI-Schema für den Dateizugriff im Blob-Speicher ist:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

> [WACOM.NOTE] Die Syntax zum Ansprechen der Dateien im Speicheremulator (ausgeführt im HDInsight-Emulator) lautet *wasb:[//\<ContainerName\>@storageemulator][//\<ContainerName\>@storageemulator]*.

Das URI-Schema bietet sowohl unverschlüsselten Zugriff mit dem Präfix *wasb:* als auch SSL-verschlüsselten Zugriff mit *wasbs*. Wir empfehlen die Verwendung von *wasbs*, wann immer es möglich ist, selbst für den Zugriff auf Daten, die sich im selben Azure-Datencenter befinden.

\<BlobStorageContainerName\> ist der Name des Blob-Speichercontainers.
\<StorageAccountName\> ist der Name des Azure-Speicherkontos. Ein vollqualifizierter Domänenname (FQDN) ist erforderlich.

Wenn weder \<BlobStorageContainerName\> noch \<StorageAccountName\> angegeben ist, wird das Standard-Dateisystem verwendet. Für die Dateien im Standard-Dateisystem können Sie entweder relative oder absolute Pfade verwenden. Auf die Datei hadoop-mapreduce-examples.jar, die sich in HDInsight-Clustern befindet, kann z. B. mit einem der folgenden Befehle verwiesen werden:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [WACOM.NOTE] In HDInsight-Clustern der Version 1.6 und 2.1 lautet der Dateiname *hadoop-examples.jar*.

\<path\> ist der HDFS-Pfadname der Datei oder des Verzeichnisses. Da Blob-Speichercontainer nur ein Schlüssel/Wert-Paar sind, gibt es kein wirkliches hierarchisches Dateisystem. Ein "/" in einem Blob-Schlüssel wird als Verzeichnis-Trennzeichen interpretiert. Der Blob-Name für *hadoop-mapreduce-examples.jar* ist z. B.:

    example/jars/hadoop-mapreduce-examples.jar

## <span id="powershell"></span></a>Blob-Zugang über Azure PowerShell

Informationen zur Installation und Konfiguration von Azure PowerShell auf Ihrer Arbeitsstation finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Azure PowerShell] Sie können das Azure PowerShell-Konsolenfenster oder PowerShell-ISE verwenden, um PowerShell-Cmdlets auszuführen.

Verwenden Sie den folgenden Befehl, um die Blob-bezogenen Cmdlets aufzulisten:

    Get-Command *blob*

![Blob.PowerShell.cmdlets][Blob.PowerShell.cmdlets]

**PowerShell-Beispiel für das Hochladen einer Datei**

Siehe [Hochladen von Daten in HDInsight][Hochladen von Daten in HDInsight].

**PowerShell-Beispiel für das Herunterladen einer Datei**

Mit dem folgenden Skript wird ein Blockblob in das aktuelle Verzeichnis heruntergeladen. Wechseln Sie vor der Ausführung des Skripts in ein Verzeichnis, für das Sie Schreibberechtigungen haben.

    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

**PowerShell-Beispiel für das Löschen einer Datei**

Das folgende Skript löscht eine Datei.
 $storageAccountName = "<azurestorageaccountname>" \# Das Speicherkonto für das bei der Einrichtung angegebene Standard-Dateisystem.
 $containerName = "<blobstoragecontainername>" \# Der Container für das Standard-Dateisystem hat denselben Namen wie der Cluster.
 $blob = "example/data/sample.log" \# Der Name des Blobs, das heruntergeladen werden soll.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Delete the blob ..." -ForegroundColor Green
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 

**PowerShell-Beispiel für das Auflisten von Dateien in einem Ordner**

Das folgende Skript listet die Dateien in einem "Ordner" auf. Das nächste Beispiel ruft das Invoke-Hive-Cmdlet auf, um den Befehl "dfs ls" zum Auflisten eines Ordners auszuführen.

    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blobPrefix = "example/data/"

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "List the files in $blobPrefix ..."
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**PowerShell-Beispiel für den Zugriff auf ein undefiniertes Speicherkonto**

Dieses Beispiel listet einen Ordner aus einem Speicherkonto auf, das nicht beim Einrichtungsprozess definiert wurde.
 $clusterName = "<hdinsightclustername>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Blob-Speicher in HDInsight verwenden können und dass Blob-Speicherung ein fundamentaler Bestandteil von HDInsight ist. Dadurch können Sie skalierbare Datenerfassungslösungen mit langfristiger Archivierung in Azure Blob-Speicher aufbauen und HDInsight verwenden, um die Informationen innerhalb der gespeicherten Daten zu entsperren.

Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit HDInsight]
-   [Hochladen von Daten zu HDInsight][Hochladen von Daten in HDInsight]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]

  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Bereitstellen von HDInsight-Clustern]: ../hdinsight-provision-clusters/
  [HDInsight-Speicherarchitektur]: #architecture
  [Vorteile von Azure Blob-Speicher]: #benefits
  [Vorbereiten eines Containers für Blob-Speicher]: #preparingblobstorage
  [Adressdateien in Blob-Speicher]: #addressing
  [Blob-Zugang über PowerShell]: #powershell
  [Nächste Schritte]: #nextsteps
  [HDI.ASVArch]: ./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight-Speicherarchitektur"
  [Beschränken des Zugriffs auf Container und Blobs]: http://msdn.microsoft.com/de-de/library/windowsazure/dd179354.aspx
  [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
  [Blob-Speicher-REST-APIs]: http://msdn.microsoft.com/de-de/library/windowsazure/dd135733.aspx
  [Azure-Speicherkonto]: ../storage-create-storage-account/
  [HDI.Schnellerfassung]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
  [HDI.CustomCreateStorageAccount]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png
  [Azure PowerShell]: ../install-configure-powershell/
  [//\<ContainerName\>@storageemulator]: mailto://<ContainerName>@storageemulator
  [Blob.PowerShell.cmdlets]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png
  [Hochladen von Daten in HDInsight]: ../hdinsight-upload-data/
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
