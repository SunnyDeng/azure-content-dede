<properties
pageTitle="Hinzufügen von Hive-Bibliotheken während der Erstellung des HDInsight-Clusters | Azure"
description="Erfahren Sie, wie Sie Hive-Bibliotheken (JAR-Dateien) einem HDInsight-Cluster während der Erstellung des Clusters hinzufügen."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/16/2016"
ms.author="larryfr"/>

#Hinzufügen von Hive-Bibliotheken während der Erstellung des HDInsight-Clusters

Wenn Sie über Bibliotheken verfügen, die Sie häufig mit Hive in HDInsight verwenden, enthält dieses Dokument Informationen zur Verwendung einer Skriptaktion zum Vorabladen der Bibliotheken während der Clustererstellung. Auf diese Weise sind die Bibliotheken in Hive global verfügbar (sodass [JAR-DATEIEN HINZUFÜGEN](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) nicht verwendet werden muss, um sie zu laden.)

##So funktioniert's

Wenn Sie einen Cluster erstellen, können Sie optional eine Skriptaktion angeben, die ein Skript auf den Clusterknoten ausführt, während diese erstellt werden. Das Skript in diesem Dokument akzeptiert einen einzelnen Parameter, nämlich den WASB-Speicherort mit den (als JAR-Dateien gespeichert) Bibliotheken, die vorab geladen werden.

Während der Clustererstellung zählt das Skript die Dateien auf, kopiert diese auf Haupt- und Workerknoten in das Verzeichnis `/usr/lib/customhivelibs/` und fügt sie dann der `hive.aux.jars.path`-Eigenschaft in der Datei `core-site.xml` hinzu. Bei Linux-basierten Clustern aktualisiert es außerdem die Datei `hive-env.sh` mit dem Speicherort der Dateien.

> [AZURE.NOTE] Mithilfe der Skriptaktionen in diesem Artikel werden die Bibliotheken in den folgenden Szenarien verfügbar:
>
> * __Linux-basiertes HDInsight__: Bei Verwendung der __Hive-Befehlszeile__, von __WebHCat__ (Templeton) und __HiveServer2__.
> * __Windows-basiertes HDInsight__: Bei Verwendung der __Hive-Befehlszeile__ und von __WebHCat__ (Templeton).

##Das Skript

__Skriptspeicherort__

Bei __Linux-basierten Clustern__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Bei __Windows-basierten Clustern__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Anforderungen__

* Die Skripts müssen sowohl auf die __Hauptknoten__ als auch die __Workerknoten__ angewendet werden.

* Die JAR-Dateien, die Sie installieren möchten, müssen in Azure Blob Storage in einem __einzelnen Container__ gespeichert werden.

* Das Speicherkonto mit der Bibliothek von JAR-Dateien __muss__ bei der Erstellung mit dem HDInsight-Cluster verknüpft werden. Dies kann auf zwei Arten erfolgen:

    * Durch Hinzufügen zu einem Container im Standardspeicherkonto des Clusters.
    
    * Durch Hinzufügen zu einem Container für einen verknüpften Speichercontainer. Im Portal können Sie z. B. über __Optionale Konfiguration__ > __Verknüpfte Speicherkonten__ weiteren Speicher hinzuzufügen.

* Der WASB-Pfad zum Container muss als Parameter für die Skriptaktion angegeben werden. Wenn sich die JAR-Dateien beispielsweise in einem Container namens __libs__ in einem Speicherkonto namens __mystorage__ befinden, lautet der Parameter \_\___wasb://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Es wird vorausgesetzt, dass Sie bereits ein Speicherkonto und einen Blobcontainer erstellt haben und Dateien in diesen geladen haben.
    >
    > Wenn Sie noch kein Speicherkonto erstellt haben, ist dies über das [Azure-Portal](https://portal.azure.com) möglich. Anschließend können Sie in einem Hilfsprogramm wie z. B. [Azure-Speicher-Explorer ](http://storageexplorer.com/) einen neuen Container im Konto erstellen und Dateien in diesen hochladen.

##Erstellen eines Clusters mithilfe des Skripts

> [AZURE.NOTE] Gehen Sie wie folgt vor, um einen neuen Linux-basierten HDInsight-Cluster zu erstellen. Wählen Sie zum Erstellen eines neuen Windows-basierten Clusters während der Clustererstellung __Windows__ als Clusterbetriebssystem aus, und verwenden Sie das Windows PowerShell-Skript und nicht das Bash-Skript.
> 
> Sie können auch Azure PowerShell oder das HDInsight .NET SDK zum Erstellen eines Clusters mit diesem Skript verwenden. Weitere Informationen zur Verwendung dieser Methoden finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

1. Beginnen Sie die Bereitstellung eines Clusters anhand der Schritte in [Bereitstellen von HDInsight-Clustern unter Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), schließen Sie sie jedoch nicht ab.

2. Wählen Sie auf dem Blatt **Optionale Konfiguration** die Option **Skriptaktionen**, und geben Sie die folgenden Informationen an:

    * __NAME__: Geben Sie einen Anzeigenamen für die Skriptaktion ein.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __HEAD__: Aktivieren Sie diese Option.
    * __WORKER__: Aktivieren Sie diese Option.
    * __ZOOKEEPER__: Belassen Sie diese Option deaktiviert.
    * __PARAMETER__: Geben Sie die WASB-Adresse des Containers und Speicherkontos an, das die JAR-Dateien enthält. Beispiel: \_\___wasb://libs@mystorage.blob.core.windows.net/__.

3. Verwenden Sie am unteren Rand der **Skriptaktionen** die Schaltfläche **Auswählen**, um die Konfiguration zu speichern.

4. Wählen Sie auf dem Blatt **Optionale Konfiguration** die Option __Verknüpfte Speicherkonten__ und den Link __Speicherschlüssel hinzufügen__ aus. Wählen Sie das Speicherkonto mit den JAR-Dateien aus, und klicken Sie dann auf die Schaltfläche __Auswählen__, um die Einstellungen zu speichern und zum Blatt __Optionale Konfiguration__ zurückzukehren.

5. Klicken Sie unten im Blatt **Optionale Konfiguration** auf die Schaltfläche **Auswählen**, um die optionalen Konfigurationsinformationen zu speichern.

6. Setzen Sie die Bereitstellung des Clusters entsprechend der Beschreibung unter [Bereitstellen von HDInsight-Clustern unter Linux](hdinsight-hadoop-provision-linux-clusters.md#portal) fort.

Nach der Erstellung des Clusters können Sie die durch dieses Skript hinzugefügten JAR-Dateien in Hive verwenden, ohne die `ADD JAR`-Anweisung verwenden zu müssen.

##Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Hive-Bibliotheken in JAR-Dateien einem HDInsight-Cluster während der Erstellung des Clusters hinzugefügt werden. Weitere Informationen zum Arbeiten mit Hive finden Sie unter [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md).

<!---HONumber=AcomDC_0218_2016-->