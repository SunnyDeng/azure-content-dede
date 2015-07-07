<properties
	pageTitle="Hochladen von Daten für Hadoop-Aufträge in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie Daten für Hadoop-Aufträge in HDInsight mithilfe der Azure-Befehlszeilenschnittstelle, Azure-Speicher-Explorer, Azure PowerShell, der Hadoop-Befehlszeile oder Sqoop hochladen und abrufen können."
	services="hdinsight,storage"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="jgao"/>



#Hochladen von Daten für Hadoop-Aufträge in HDInsight

Azure HDInsight stellt über Azure-Blobspeicher ein Hadoop Distributed File System (HDFS) mit vollem Funktionsumfang zur Verfügung. Es ist als eine HDFS-Erweiterung konzipiert, die eine nahtlose Benutzererfahrung bietet. Der vollständige Satz von Komponenten im Hadoop-Ökosystem kann direkt mit den verwalteten Daten verwendet werden. Azure-Blobspeicher und HDFS sind unterschiedliche Dateisysteme, die jeweils für die Datenspeicherung und Berechnungen dieser Daten optimiert sind. Die Vorteile der Verwendung von Azure-Blobspeicher werden unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage] beschrieben.

##Voraussetzungen

Beachten Sie die folgenden Voraussetzungen, bevor Sie beginnen:

* Ein Azure HDInsight-Cluster. Anweisungen finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started] or [Bereitstellen eines HDInsight-Clusters][hdinsight-provision].

##Warum Blob-Speicher?

Azure HDInsight-Cluster werden in der Regel bereitgestellt, um MapReduce-Aufträge auszuführen. Die Cluster werden gelöscht, sobald diese Aufträge abgeschlossen sind. Nachdem die Berechnungen erfolgt sind, wäre es zu teuer, diese Daten weiterhin in den HDFS-Clustern zu speichern. Der Azure-Blobspeicher ist eine hoch skalierbare und verfügbare, kostengünstige und freigabefähige Speicheroption mit hoher Kapazität für Daten, die mit HDInsight verarbeitet werden sollen. Das Speichern von Daten in einem Blob sorgt dafür, dass die für Berechnungen verwendeten HDInsight-Cluster sicher freigegeben werden können, ohne Daten zu verlieren.

###Verzeichnisse

In Azure-Blobspeichercontainern werden Daten als Schlüssel-Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen das Zeichen '/' verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. HDInsight betrachtet diese Schlüsselnamen, als wären es tatsächliche Verzeichnisse.

Der Schlüssel eines Blobs kann z. B. *input/log1.txt* heißen. Das Verzeichnis 'input' existiert zwar nicht, wegen des Zeichens '/' im Schlüsselnamen sieht es jedoch so aus, als gäbe es einen Dateipfad.

Daher werden in den Azure Explorer-Tools möglicherweise einige Dateien mit 0 Byte angezeigt. Diese Dateien dienen zwei Zwecken:

- Bei leeren Ordnern markieren sie das Vorhandensein der Ordner. Azure-Blobspeicher erkennt, dass es bei Vorhandensein eines Blobs mit dem Namen "foo/bar" einen Ordner namens **foo** gibt. Soll es jedoch einen leeren Ordner namens **foo** geben, kann dieser nur mit der speziellen 0-Byte-Datei bezeichnet werden.

- Diese Dateien enthalten einige spezielle vom Hadoop-Dateisystem benötigte Metadaten, insbesondere die Berechtigungen und Eigentümer der Ordner.

##Befehlszeilenprogramme

Microsoft bietet die folgenden Hilfsprogramme für die Verwendung mit dem Azure-Blob-Speicher:

| Tool | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Azure-Befehlszeilenschnittstelle][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Hadoop-Befehl](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE]Während die Azure-Befehlszeilenschnittstelle, Azure PowerShell und AzCopy von außerhalb von Azure verwendet werden können, ist der Hadoop-Befehl nur im HDInsight-Cluster verfügbar, außerdem lässt er nur das Laden von Daten aus dem lokalen Dateisystem in den Azure-Blob-Speicher zu.

###<a id="xplatcli"></a>Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle ist ein plattformübergreifendes Tool zur Verwaltung von Azure-Diensten. Verwenden Sie die folgenden Schritte zum Hochladen von Daten in Azure-Blobspeicher:

1. [Installieren und konfigurieren Sie die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows](../xplat-cli.md).

2. Öffnen Sie eine Eingabeaufforderung, Bash oder eine andere Shell, und authentifizieren Sie Ihr Azure-Abonnement wie folgt.

		azure login

	Geben Sie nach Aufforderung den Benutzernamen und das Kennwort für Ihr Abonnement ein.

3. Geben Sie den folgenden Befehl ein, um die Speicherkonten für Ihr Abonnement aufzulisten:

		azure storage account list

4. Wählen Sie das Speicherkonto mit dem zu verwendenden Blob aus, und rufen Sie mit dem folgenden Befehl den Schlüssel für dieses Konto ab:

		azure storage account keys list <storage-account-name>

	Damit sollten der **primäre** und der **sekundäre** Schlüssel zurückgegeben werden. Kopieren Sie den Wert des **primären** Schlüssels für die Verwendung in den nächsten Schritten.

5. Rufen Sie mit dem folgenden Befehl eine Liste der Blobcontainer im Speicherkonto ab:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Verwenden Sie die folgenden Befehle zum Hoch- und Herunterladen von Dateien im Blob:

	* So laden Sie eine Datei hoch:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* So laden Sie eine Datei herunter:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Wenn Sie immer mit demselben Speicherkonto arbeiten, können die folgenden Umgebungsvariablen festlegen, anstatt das Konto und den Schlüssel für jeden Befehl anzugeben:
>
> * **AZURE_STORAGE_ACCOUNT**: der Name des Speicherkontos
>
> * **AZURE_STORAGE_ACCESS_KEY**: der Speicherkontoschlüssel

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell ist eine Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Azure steuern und automatisieren können. Informationen zum Konfigurieren der Arbeitsstation für die Ausführung von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

**So laden Sie eine lokale Datei in den Azure-Blobspeicher hoch**

1. Öffnen Sie die Azure PowerShell-Konsole entsprechend den Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).
2. Legen Sie die Werte der ersten fünf Variablen im folgenden Skript fest:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"

		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}

		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		# Copy the file from local workstation to the Blob container
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Fügen Sie das Skript in die Azure PowerShell-Konsole ein, um es auszuführen und die Datei zu kopieren.

PowerShell-Skripts, die für die Verwendung mit HDInsight erstellt wurden, finden Sie z. B. unter [HDInsight-Tools](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy ist ein Befehlszeilenprogramm, das den Austausch von Daten mit einem Azure Storage-Konto vereinfachen soll. Sie können dieses Dienstprogramm als eigenständiges Tool verwenden oder in eine vorhandene Anwendung integrieren. [AzCopy herunterladen][azure-azcopy-download].

Die AzCopy-Syntax lautet folgendermaßen:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Weitere Informationen finden Sie unter [AzCopy – Hochladen/Herunterladen von Dateien für Azure-Blobs][azure-azcopy].


###<a id="commandline"></a>Hadoop-Befehlszeile

Die Hadoop-Befehlszeile eignet sich nur zum Speichern von Daten im Blob-Speicher, wenn die Daten bereits auf dem Hauptknoten des Clusters vorhanden sind.

Um den Hadoop-Befehl verwenden zu können, müssen Sie zunächst mithilfe einer der folgenden Methoden eine Verbindung zum Hauptknoten herstellen:

* **HDInsight (Windows-basiert)**: [Herstellen einer Verbindung mithilfe von Remotedesktop](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight (Linux-basiert)**: Herstellen einer Verbindung mithilfe von SSH ([SSH-Befehl](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) oder [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Nachdem die Verbindung hergestellt wurde, verwenden Sie die folgende Syntax, um eine Datei in den Speicher hochzuladen.

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

Beispiel: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Da sich das Standarddateisystem für HDInsight im Azure-Blob-Speicher befindet, befindet sich die Datei "/example/data.txt" auch tatsächlich im Azure-Blob-Speicher. Sie können auch folgendermaßen auf die Datei verweisen:

	wasb:///example/data/data.txt

oder

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Eine Liste weiterer Hadoop-Befehle für die Arbeit mit Dateien finden Sie unter [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html).

##Clients mit grafischer Benutzeroberfläche

Es gibt auch einige Anwendungen, die eine grafische Benutzeroberfläche für die Arbeit mit Azure Storage bereitstellen. Im Folgenden sind einige dieser Anwendungen aufgelistet:

| Client- | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/) | | | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Zudio](https://zudio.co/) | ✔ | ✔ | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###<a id="storageexplorer"></a>Azure-Speicher-Explorer

*Azure-Speicher-Explorer* ist ein hilfreiches Tool zum Prüfen und Ändern der Daten in Azure Storage. Dieses kostenlose Tool kann von CodePlex heruntergeladen werden: [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/ "Azure-Speicher-Explorer").

Bevor Sie das Tool verwenden können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen. Anleitungen zum Abrufen dieser Informationen finden Sie unter [Erstellen, Verwalten oder Löschen von Speicherkonten][azure-create-storage-account] im Abschnitt "Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln".

1. Führen Sie den Azure-Speicher-Explorer aus.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Klicken Sie auf **Konto hinzufügen**. Nachdem Sie Azure-Speicher-Explorer ein Konto hinzugefügt haben, müssen Sie diesen Schritt nicht noch einmal durchführen.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Geben Sie den **Speicherkontonamen** und den **Speicherkontoschlüssel** ein, und klicken Sie dann auf **Speicherkonto hinzufügen**. Sie können mehrere Speicherkonten hinzufügen. Jedes Konto wird auf einer Registerkarte angezeigt.

4. Klicken Sie unter **Speichertyp** auf **Blobs**.

	![HDI.ASEBlob][image-ase-blob]

5. Klicken Sie unter **Container** auf den Container, der Ihrem HDInsight-Cluster zugeordnet ist. Beim Erstellen eines HDInsight-Clusters müssen Sie einen Container angeben. Andernfalls wird der Container beim Erstellen des Clusters automatisch angelegt.

6. Klicken Sie unter **Blob** auf **Hochladen**.

7. Geben Sie eine hochzuladende Datei an, und klicken Sie auf **Öffnen**.


##Dienste

###Azure Data Factory

Der Azure Data Factory-Dienst ist ein vollständig verwalteter Dienst für das Kombinieren von Verarbeitungs-, Speicher- und Verschiebungsdienste für Daten in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines.

Mit Azure Data Factory können Daten in den Azure-Blob-Speicher verschoben oder Datenpipelines erstellt werden, die HDInsight-Funktionen, z. B. Hive und Pig, direkt verwenden.

Weitere Informationen finden Sie in der [Dokumentation zu Azure Data Factory](http://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop ist ein Tool zum Übertragen von Daten zwischen Hadoop und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle in das verteilte Dateisystem von Hadoop (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und sie anschließend wieder in ein RDBMS exportieren.

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop].

##Entwicklungs-SDKs

Auf den Azure-Blob-Speicher kann auch mithilfe eines Azure-SDK über die folgenden Programmiersprachen zugegriffen werden:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Weitere Informationen zum Installieren der Azure-SDKs finden Sie unter [Azure-Downloads](http://azure.microsoft.com/downloads/).


## Nächste Schritte
Jetzt wissen Sie, wie Sie Daten in HDInsight importieren. Lesen Sie in den folgenden Artikeln, wie Sie die Daten analysieren:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
 

<!---HONumber=58_postMigration-->