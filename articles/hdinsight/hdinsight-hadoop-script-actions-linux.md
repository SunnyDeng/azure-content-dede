<properties
    pageTitle="Entwickeln von Skriptaktionen mit Linux-basiertem HDInsight | Microsoft Azure"
    description="Erfahren Sie, wie Sie mit Skriptaktionen Linux-basierte HDInsight-Cluster anpassen können."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="larryfr"/>

# Entwickeln von Skriptaktionen mit HDInsight

Skriptaktionen stellen eine Möglichkeit zum Anpassen von Azure HDInsight-Clustern dar, indem Cluster-Konfigurationseinstellungen während der Installation angegeben oder zusätzliche Dienste, Tools oder andere Software auf dem Cluster installiert werden.

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten für Linux-basierte HDInsight-Cluster. Informationen zur Verwendung von Skriptaktionen mit Windows-basierten Clustern finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## Was sind Skriptaktionen?

Skriptaktionen sind Bash-Skripts, die während der Bereitstellung auf den Clusterknoten ausgeführt werden. Eine Skriptaktion wird als Stamm ausgeführt und bietet Vollzugriffsrechte auf die Clusterknoten.

Skriptaktionen können bei der Bereitstellung eines Clusters über das __Azure-Vorschauportal__, __Azure PowerShell__ oder das __HDInsight .NET SDK__ verwendet werden.

Eine exemplarische Vorgehensweise zum Anpassen eines Clusters mithilfe von Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Bewährte Methoden für die Entwicklung von Skripts

Wenn Sie ein benutzerdefiniertes Skript für einen HDInsight-Cluster entwickeln, sollten Sie mehrere bewährte Methoden beachten:

- [Auswählen der Hadoop-Version](#bPS1)
- [Einrichten stabiler Verknüpfungen mit Skriptressourcen](#bPS2)
- [Verwenden vorkompilierter Ressourcen](#bPS4)
- [Sicherstellen, dass das Clusteranpassungsskript idempotent ist](#bPS3)
- [Sicherstellen einer hohen Verfügbarkeit der Clusterarchitektur](#bPS5)
- [Konfigurieren benutzerdefinierter Komponenten zur Verwendung von Azure-Blobspeicher](#bPS6)
- [Schreiben von Informationen in STDOUT und STDERR](#bPS7)
- [Speichern von Dateien im ASCII-Format mit LF-Zeilenenden](#bps8)

> [AZURE.IMPORTANT]Skriptaktionen müssen innerhalb von 60 Minuten abgeschlossen sein, andernfalls werden sie mit Timeout abgebrochen. Während der Knotenbereitstellung wird das Skript gleichzeitig mit anderen Einrichtungs- und Konfigurationsprozessen ausgeführt. Der Wettbewerb um Ressourcen wie CPU-Zeit oder Netzwerkbandbreite kann dazu führen, dass es länger als in Ihrer Entwicklungsumgebung dauert, bis das Skript abgeschlossen ist.

### <a name="bPS1"></a>Auswählen der Hadoop-Version

In den verschiedenen HDInsight-Versionen sind unterschiedliche Versionen von Hadoop-Diensten und Hadoop-Komponenten installiert. Wenn bei Ihrem Skript eine bestimmte Version eines Diensts oder einer Komponente vorausgesetzt wird, sollten Sie das Skript nur mit der HDInsight-Version verwenden, die die erforderlichen Komponenten enthält. Informationen zu den in HDInsight enthaltenen Komponentenversionen finden Sie im Dokument [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen](hdinsight-component-versioning.md).

### <a name="bPS2"></a>Einrichten stabiler Verknüpfungen mit Skriptressourcen

Benutzer müssen dafür sorgen, dass alle Skripts und Ressourcen, die im Skript verwendet werden, für die gesamte Nutzungsdauer des Clusters verfügbar bleiben und dass sich die Versionen dieser Dateien während dieses Zeitraums nicht ändern. Diese Ressourcen werden benötigt, wenn für die Knoten im Cluster ein neues Abbild erstellt wird.

Die bewährte Methode ist das Herunterladen und Archivieren aller Daten in einem Azure-Speicherkonto Ihres Abonnements.

> [AZURE.IMPORTANT]Bei dem verwendeten Speicherkonto muss es sich um das Standardspeicherkonto des Clusters oder um einen öffentlichen, schreibgeschützten Container eines anderen Speicherkontos handeln.

Die von Microsoft bereitgestellten Beispiele sind beispielsweise im Speicherkonto [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) gespeichert, einem öffentlichen, schreibgeschützten Container, der vom HDInsight-Team verwaltet wird.

### Verwenden vorkompilierter Ressourcen<a name="bPS4"></a>

Zur Verringerung des Zeitraums, der für die Ausführung des Skripts benötigt wird, sollten Sie Vorgänge vermeiden, mit denen Ressourcen aus dem Quellcode kompiliert werden. Führen Sie stattdessen eine Vorkompilierung der Ressourcen durch, und speichern Sie die binäre Version im Azure-BLOB-Speicher, damit sie per Skript schnell in den Cluster heruntergeladen werden kann.

### <a name="bPS3"></a>Sicherstellen, dass das Clusteranpassungsskript idempotent ist

Sie müssen davon ausgehen, dass für die Knoten eines HDInsight-Clusters während der Nutzungsdauer des Clusters ein neues Abbild erstellt wird und dass in diesem Fall das Clusteranpassungsskript verwendet wird. Das Skript muss dahingehend idempotent sein, dass beim Erstellen eines neuen Abbilds sichergestellt wird, dass der Cluster bei jeder Ausführung in denselben Status zurückgesetzt wird.

Beispiel: Wenn ein benutzerdefiniertes Skript bei der ersten Ausführung eine Anwendung unter "/usr/local/bin" installiert, muss das Skript bei allen nachfolgenden Ausführungen prüfen, ob die Anwendung am Speicherort "/usr/local/bin" bereits vorhanden ist, ehe mit anderen Schritten im Skript fortgefahren wird.

### <a name="bPS5"></a>Sicherstellen einer hohen Verfügbarkeit der Clusterarchitektur

Linux-basierte HDInsight-Cluster umfassen zwei Hauptknoten, die im Cluster aktiv sind. Skriptaktionen werden für beide Knoten ausgeführt. Wenn die zu installierenden Komponenten nur einen Hauptknoten erwarten, müssen Sie ein Skript erstellen, mit dem die Komponenten nur auf einem der beiden Hauptknoten im Cluster installiert werden.

> [AZURE.IMPORTANT]Standarddienste, die als Teil von HDInsight installiert werden, sind so konzipiert, dass bei Bedarf ein Failover zwischen den beiden Hauptknoten durchgeführt wird. Diese Funktionalität erstreckt sich jedoch nicht auf benutzerdefinierte Komponenten, die über Skriptaktionen installiert werden. Wenn die über eine Skriptaktion installierten Komponenten eine hohe Verfügbarkeit aufweisen sollen, müssen Sie einen eigenen Failovermechanismus für die beiden verfügbaren Hauptknoten implementieren.

### <a name="bPS6"></a>Konfigurieren benutzerdefinierter Komponenten zur Verwendung von Azure-Blobspeicher

Die Komponenten, die Sie auf dem Cluster installieren, sind möglicherweise standardmäßig so konfiguriert, dass sie den HDFS-Speicher (Hadoop Distributed File System) verwenden. Bei einem Re-Imaging des Clusters wird das HDFS-Dateisystem formatiert, sodass alle darauf gespeicherten Daten verloren gehen. Sie sollten die Konfiguration so ändern, dass stattdessen der Azure-Blob-Speicher (WASB) verwendet wird, da dies der Standardspeicher für den Cluster ist, der auch dann nach dem Löschen des Clusters beibehalten wird.

Mit dem folgenden Code wird beispielsweise die Datei "giraph-examples.jar" aus dem lokalen Dateisystem in WASB kopiert:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Schreiben von Informationen in STDOUT und STDERR

Die in STDOUT und STDERR geschriebenen Informationen werden protokolliert und können nach der Bereitstellung des Clusters über die Ambari-Webbenutzeroberfläche angezeigt werden.

Die meisten Dienstprogramme und Installationspakete schreiben bereits Informationen in STDOUT und STDERR. Möglicherweise möchten Sie jedoch weitere Protokollierungsinformationen hinzufügen. Verwenden Sie `echo`, um Text an STDOUT zu senden. Beispiel:

        echo "Getting ready to install Foo"

Standardmäßig wird durch `echo` der String an STDOUT gesendet. Soll dieser an STDERR geleitet werden, setzen Sie `>&2` vor `echo`. Beispiel:

        >&2 echo "An error occured installing Foo"

Damit werden die an STDOUT gesendeten Informationen (1, Standardwert und daher hier nicht aufgeführt) an STDERR (2) umgeleitet. Weitere Informationen zur E/A-Umleitung finden Sie unter [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Weitere Informationen zum Anzeigen der durch Skriptaktionen protokollierten Daten finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

###<a name="bps8"></a>Speichern von Dateien im ASCII-Format mit LF-Zeilenenden

Bash-Skripts sollten im ASCII-Format und mit LF als Zeilenende gespeichert werden. Wenn Dateien im Format UTF-8, das eine Bytereihenfolge-Marke am Anfang der Datei enthalten kann, oder mit CR-LF-Zeilenenden gespeichert werden (gängig bei Windows-Editoren), schlägt das Skript mit Fehlermeldungen fehl, die der folgenden ähneln:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

## <a name="helpermethods"></a>Hilfsmethoden für benutzerdefinierte Skripts

Script Action-Hilfsmethoden sind Hilfsprogramme, die Sie zum Schreiben von benutzerdefinierten Skripts verwenden können. Diese werden in der Datei [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) definiert und können wie folgt in Ihre Skripts eingefügt werden:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Damit können Sie folgende Hilfsprogramme in Ihrem Skript nutzen:

| Hilfsprogramm | Beschreibung |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Lädt eine Datei aus der Quell-URL in den angegebenen Dateipfad herunter. Standardmäßig wird eine vorhandene Datei nicht überschrieben. |
| `untar_file TARFILE DESTDIR` | Extrahiert eine TAR-Datei (mit `-xf`) in das Zielverzeichnis. |
| `test_is_headnode` | Bei Ausführung auf dem Hauptknoten eines Clusters wird "1" zurückgegeben, andernfalls "0". |
| `test_is_datanode` | Wenn der aktuelle Knoten ein Datenknoten (Workerknoten) ist, wird "1" zurückgegeben, andernfalls "0". |
| `test_is_first_datanode` | Wenn der aktuelle Knoten der erste Datenknoten (Workerknoten) ist (mit dem Namen "workernode0"), wird "1" zurückgegeben, andernfalls "0". |

## <a name="commonusage"></a>Gängige Verwendungsmuster

Dieser Abschnitt enthält Anweisungen für das Implementieren einiger gängiger Verwendungsmuster, die Ihnen beim Schreiben Ihrer eigenen benutzerdefinierten Skripts begegnen können.

### Übergeben von Parametern an ein Skript

In einigen Fällen müssen Sie Parameter für das Skript angeben. Beispielsweise müssen Sie das Administratorkennwort für den Cluster angeben, um Informationen über die Ambari-REST-API abzurufen.

Parameter, die an das Skript übergeben werden, werden als _Positionsparameter_ bezeichnet und werden für den ersten Parameter `$1` zugewiesen, für den zweiten Parameter `$2` usw. `$0` enthält den Namen des Skripts.

Werte, die als Parameter an das Skript übergeben werden, sollten in einfache Anführungszeichen (') eingeschlossen werden, sodass der übergebene Wert als Literal behandelt wird und darin enthaltene Zeichen wie z. B. "!" nicht besonders behandelt werden.

### Festlegen von Umgebungsvariablen

Eine Umgebungsvariable wird wie folgt festgelegt:

    VARIABLENAME=value

Dabei ist VARIABLENNAME der Name der Variable. Verwenden Sie `$VARIABLENAME`, um anschließend auf die Variable zuzugreifen. Um einen Wert, der von einem Positionsparameter bereitgestellt wird, als Umgebungsvariable mit dem Namen "PASSWORD" zuzuweisen, verwenden Sie beispielsweise folgenden Code:

    PASSWORD=$1

Für den anschließenden Zugriff auf die Informationen kann dann `$PASSWORD` verwendet werden.

Umgebungsvariablen, die im Skript festgelegt werden, gelten nur innerhalb des Gültigkeitsbereichs des Skripts. In einigen Fällen müssen Sie möglicherweise systemweite Umgebungsvariablen hinzufügen, die nach Abschluss des Skripts beibehalten werden. Dies erfolgt in der Regel, damit Benutzer, die über SSH eine Verbindung mit dem Cluster herstellen, die mit Ihrem Skript installierten Komponenten verwenden können. Fügen Sie dazu `/etc/environment` die Umgebungsvariable hinzu. Mit dem folgenden Beispiel wird z. B. __HADOOP\_CONF\_DIR__ hinzugefügt:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### Zugriff auf Speicherorte benutzerdefinierter Skripts

Skripts zur Anpassung eines Clusters müssen entweder im Standardspeicherkonto des Clusters oder im Fall eines anderen Speicherkontos in einem öffentlichen schreibgeschützten Container gespeichert sein. Wenn Ihr Skript auf externe Ressourcen zugreift, müssen auch diese öffentlich zugänglich sein (oder mindestens einen öffentlichen Lesezugriff aufweisen). Beispielsweise können Sie eine Datei mithilfe von `download_file` in den Cluster herunterladen.

Durch Speichern der Datei in einem Azure-Speicherkonto, auf das der Cluster zugreifen kann (z. B. das Standardspeicherkonto), wird ein schneller Zugriff ermöglicht, da sich dieser Speicher im Azure-Netzwerk befindet.

## <a name="deployScript"></a>Prüfliste für die Bereitstellung einer Skriptaktion

Es folgen unsere Schritte bei der Vorbereitung der Bereitstellung dieser Skripts:

- Legen Sie Daten mit den benutzerdefinierten Skripts an einem Speicherort ab, auf den die Clusterknoten während der Bereitstellung zugreifen können. Dies kann ein beliebiges Standardkonto oder zusätzliches Speicherkonto, das während der Bereitstellung angegeben wurde, oder ein anderer öffentlich zugänglicher Speichercontainer sein.

- Fügen Sie Skripts Überprüfungen hinzu, um sicherzustellen, dass sie idempotent ausgeführt werden, damit das Skript mehrmals auf demselben Knoten ausgeführt werden kann.

- Legen Sie die heruntergeladenen von den Skripts verwendeten Dateien in einem temporären Dateiverzeichnis ab (z. B. "/tmp"), und löschen Sie sie nach der Ausführung der Skripts.

- Wenn sich Einstellungen auf Betriebssystemebene oder Hadoop-Dienstkonfigurationsdateien geändert haben, können Sie bei Bedarf die HDInsight-Dienste neu starten. Diese können dann Einstellungen auf Betriebssystemebene übernehmen, z. B. die in den Skripts festgelegten Umgebungsvariablen.

## <a name="runScriptAction"></a>Ausführen einer Skriptaktion

Sie können Skriptaktionen zum Anpassen von HDInsight-Clustern mithilfe des Azure-Portals, von Azure PowerShell oder des HDInsight .NET SDK ausführen. Anweisungen hierzu finden Sie unter [Verwenden einer Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md#howto).

## <a name="sampleScripts"></a>Beispiele benutzerdefinierter Skripts

Microsoft bietet Beispielskripts für die Installation von Komponenten in einem HDInsight-Cluster. Die Beispielskripts und Nutzungsanweisungen stehen unter den folgenden Links zur Verfügung:

- [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md)
- [Installieren und Verwenden von Spark in HDInsight-Clustern](hdinsight-hadoop-spark-install-linux.md)
- [Installieren und Verwenden von R in HDInsight Hadoop-Clustern](hdinsight-hadoop-r-scripts-linux.md)
- [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md)
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE]Die oben aufgeführten Dokumente gelten für Linux-basierte HDInsight-Cluster. Informationen zu Skripts für Windows-basierte HDInsight-Cluster finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight (Windows)](hdinsight-hadoop-script-actions.md), oder über die Links am Seitenanfang der einzelnen Artikel.

##Problembehandlung

Bei der Verwendung der von Ihnen entwickelten Skripts können die folgenden Fehler auftreten:

__Fehler__: `$'\r': command not found`. Manchmal gefolgt von `syntax error: unexpected end of file`.

_Ursache_: Dieser Fehler tritt auf, wenn die Zeilen in einem Skript mit CR-LF enden. In UNIX-Systemen wird als Zeilenende nur LF erwartet.

Dieses Problem tritt am häufigsten auf, wenn das Skript in einer Windows-Umgebung erstellt wird, da CR-LF in vielen Text-Editoren unter Windows als gängiges Zeilenende verwendet wird.

_Lösung_: Wenn Ihr Text-Editor über die entsprechende Option verfügt, wählen Sie UNIX als Format oder LF für das Zeilenende aus. In einem UNIX-System können Sie außerdem die folgenden Befehle verwenden, um CR-LF in LF zu ändern:

> [AZURE.NOTE]Mit den folgenden annähernd gleichwertigen Befehlen sollten sich die CR-LF-Zeilenenden in LF ändern lassen. Wählen Sie einen Befehl entsprechend den in Ihrem System verfügbaren Dienstprogrammen aus.

| Befehl | Hinweise |
| ------- | ----- |
| `unix2dos -b INFILE` | Die ursprüngliche Datei wird mit einer BAK-Erweiterung gesichert. |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE enthält eine Version, die ausschließlich LF-Zeilenenden umfasst. |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Damit wird die Datei direkt geändert, ohne dass eine neue Datei erstellt wird. |
| ```sed 's/$'"/`echo \\r`/" INFILE > OUTFILE``` | OUTFILE enthält eine Version, die ausschließlich LF-Zeilenenden umfasst.

__Fehler__: `line 1: #!/usr/bin/env: No such file or directory`.

_Ursache_: Dieser Fehler tritt auf, wenn das Skript im Format UTF-8 mit einer Bytereihenfolge-Marke (BOM) gespeichert wurde.

_Lösung_: Speichern Sie die Datei entweder im ASCII-Format oder im UTF-8-Format ohne Bytereihenfolge-Marke. In einem Linux- oder UNIX-System können Sie auch mit dem folgenden Befehl eine neue Datei ohne Bytereihenfolge-Marke erstellen:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Ersetzen Sie den oben aufgeführten Befehl __INFILE__ durch die Datei mit Bytereihenfolge-Marke. Für __OUTFILE__ sollte ein neuer Dateiname eingegeben werden. Die Datei enthält dann das Skript ohne Bytereihenfolge-Marke.

## <a name="seeAlso"></a>Weitere Informationen

[Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md)

<!---HONumber=Nov15_HO1-->