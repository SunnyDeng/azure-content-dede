<properties
	pageTitle="Entwickeln von Skriptaktionen mit HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Skriptaktionen Hadoop-Cluster anpassen können."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/09/2015"
	ms.author="jgao"/>

# Entwickeln von Script Action-Skripts für HDInsight

Entwickeln von Skriptaktionsskripts für HDInsight Weitere Informationen zu Skriptaktionsskripts finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md). Der gleiche Artikel, der für den HDInsight-Cluster unter einem Linux-Betriebssystem verfasst wurde, befindet sich unter [Entwickeln von Skriptaktionsskripts für HDInsight](hdinsight-hadoop-script-actions-linux.md).

Mit Skriptaktionen (Script Action) kann zusätzliche Software in einem Hadoop-Cluster installiert oder die Konfiguration von in einem Cluster installierten Anwendungen geändert werden. Skriptaktionen sind Skripts, die auf Clusterknoten ausgeführt werden, wenn HDInsight-Cluster bereitgestellt sind. Sie werden dann ausgeführt, sobald die HDInsight-Konfiguration auf Knoten im Cluster abgeschlossen ist. Eine Skriptaktion wird mit Berechtigungen des Systemadministratorkontos ausgeführt und bietet umfassende Zugriffsrechte auf die Clusterknoten. Jeder Cluster kann mit einer Liste von Skriptaktionen bereitgestellt werden, die in der angegebenen Reihenfolge ausgeführt werden.

> [AZURE.NOTE]Möglicherweise erhalten Sie die folgende Fehlermeldung:
> 
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> Die Ursache dafür ist, dass Sie die Hilfsmethoden nicht mit aufgenommen haben. Informationen finden Sie unter [Hilfsmethoden für benutzerdefinierte Skripts](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## Beispielskripts

Für die Bereitstellung von HDInsight-Clustern unter einem Windows-Betriebssystem wird als Skriptaktion ein Azure PowerShell-Skript verwendet. Nachfolgend sehen Sie ein Beispielskript für das Konfigurieren der Websitekonfigurationsdateien:

	param (
	    [parameter(Mandatory)][string] $ConfigFileName,
	    [parameter(Mandatory)][string] $Name,
	    [parameter(Mandatory)][string] $Value,
	    [parameter()][string] $Description
	)

	if (!$Description) {
	    $Description = ""
	}

	$hdiConfigFiles = @{
	    "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
	    "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
	    "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
	    "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
	    "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
	}

	if (!($hdiConfigFiles[$ConfigFileName])) {
	    Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
	    return
	}

	[xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

	$existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

	if ($existingproperty) {
	    $existingproperty.Value = $Value
	    $existingproperty.Description = $Description
	} else {
	    $newproperty = @($configFile.configuration.property)[0].Clone()
	    $newproperty.Name = $Name
	    $newproperty.Value = $Value
	    $newproperty.Description = $Description
	    $configFile.configuration.AppendChild($newproperty)
	}

	$configFile.Save($hdiConfigFiles[$ConfigFileName])

	Write-HDILog "$configFileName has been configured."

Das Skript akzeptiert vier Parameter: den Namen der Konfigurationsdatei, die Eigenschaft, die Sie ändern möchten, den Wert, den Sie festlegen möchten, und eine Beschreibung. Beispiel:

	hive-site.xml hive.metastore.client.socket.timeout 90 

Mit diesen Parametern wird der Wert „hive.metastore.client.socket.timeout“ in der Datei „hive-site.xml“ auf 90 festgelegt. Der Standardwert beträgt 60 Sekunden.

Ein Beispielskript finden Sie auch unter [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight verfügt über mehrere Skripts zum Installieren zusätzlicher Komponenten auf HDInsight-Clustern:

Name | Skript
----- | -----
**Installieren von Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Siehe [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark].
**Installieren von R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Siehe [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-r-scripts].
**Installieren von Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Siehe [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md).
- **Installieren von Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Siehe [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md).

Skriptaktionen können über das Azure-Portal, Azure PowerShell oder das HDInsight .NET SDK bereitgestellt werden. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen][hdinsight-cluster-customize].

> [AZURE.NOTE]Die Beispielskripts funktionieren nur mit HDInsight-Clustern der Version 3.1 oder höher. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](../hdinsight-component-versioning/).





## Hilfsmethoden für benutzerdefinierte Skripts

Script Action-Hilfsmethoden sind Hilfsprogramme, die Sie zum Schreiben von benutzerdefinierten Skripts verwenden können. Diese werden in der Datei [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1) definiert und können wie folgt in Ihre Skripts eingefügt werden:

    # Download config action module from a well-known directory.
	$CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
	$CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
	$webclient = New-Object System.Net.WebClient;
	$webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);
	
	# (TIP) Import config action helper method module to make writing config action easy.
	if (Test-Path ($CONFIGACTIONMODULE))
	{ 
		Import-Module $CONFIGACTIONMODULE;
	} 
	else
	{
		Write-Output "Failed to load HDInsightUtilities module, exiting ...";
		exit;
	}

Hier sind die Hilfsmethoden, die von diesem Skript bereitgestellt werden:

Hilfsmethode | Beschreibung
-------------- | -----------
**Save-HDIFile** | Herunterladen einer Datei vom angegebenen URI (Uniform Resource Identifier) an einen Speicherort auf dem lokalen Datenträger, der dem im Cluster zugewiesenen Azure VM-Knoten zugeordnet ist.
**Expand-HDIZippedFile** | Entpacken einer ZIP-Datei.
**Invoke-HDICmdScript** | Ausführen eines Skripts über "cmd.exe".
**Write-HDILog** | Schreiben der Ausgabe des benutzerdefinierten Skripts, das für die Skriptaktion verwendet wird.
**Get-Services** | Abrufen einer Liste von Diensten auf dem Rechner, auf dem das Skript ausgeführt wird.
**Get-Service** | Mit dem bestimmten Dienstnamen als Eingabe werden detaillierte Informationen für einen bestimmten Dienst (Dienstname, Prozess-ID, Status usw.) auf dem Computer zurückgegeben, auf dem das Skript ausgeführt wird.
**Get-HDIServices** | Abrufen einer Liste der HDInsight-Dienste auf dem Computer, auf dem das Skript ausgeführt wird.
**Get-HDIService** | Mit dem bestimmten HDInsight-Dienstnamen als Eingabe werden detaillierte Informationen für einen bestimmten Dienst (Dienstname, Prozess-ID, Status usw.) auf dem Computer zurückgegeben, auf dem das Skript ausgeführt wird.
**Get-ServicesRunning** | Abrufen einer Liste von Diensten auf dem Computer, auf dem das Skript ausgeführt wird.
**Get-ServiceRunning** | Überprüfen, ob ein bestimmter Dienst (namentlich) auf dem Computer ausgeführt wird, auf dem das Skript ausgeführt wird.
**Get-HDIServicesRunning** | Abrufen einer Liste der HDInsight-Dienste auf dem Computer, auf dem das Skript ausgeführt wird.
**Get-HDIServiceRunning** | Überprüfen, ob ein bestimmter HDInsight-Dienst (namentlich) auf dem Computer ausgeführt wird, auf dem das Skript ausgeführt wird.
**Get-HDIHadoopVersion** | Abrufen der Hadoop-Version, die auf dem Computer installiert ist, auf dem das Skript ausgeführt wird.
**Test-IsHDIHeadNode** | Überprüfen, ob der Computer, auf dem das Skript ausgeführt wird, ein Hauptknoten ist.
**Test-IsActiveHDIHeadNode** | Überprüfen, ob der Computer, auf dem das Skript ausgeführt wird, ein aktiver Hauptknoten ist.
**Test-IsHDIDataNode** | Überprüfen, ob der Computer, auf dem das Skript ausgeführt wird, ein Datenknoten ist.
**Edit-HDIConfigFile** | Bearbeiten der Konfigurationsdateien "hive-site.xml", "core-site.xml", "hdfs-site.xml", "mapred-site.xml" oder "yarn-site.xml".


## Empfohlene Methoden für die Skriptentwicklung

Wenn Sie ein benutzerdefiniertes Skript für einen HDInsight-Cluster entwickeln, sollten Sie mehrere bewährte Methoden beachten:

- Überprüfen der Hadoop-Version

	Nur HDInsight Version 3.1 (Hadoop 2.4) und höher unterstützen die Installation von benutzerdefinierten Komponenten in einem Cluster mithilfe von Skriptaktionen. Sie müssen im benutzerdefinierten Skript die Hilfsmethode **Get-HDIHadoopVersion** verwenden, um die Hadoop-Version zu überprüfen, bevor Sie mit anderen Aufgaben im Skript fortfahren können.


- Einrichten stabiler Verknüpfungen mit Skriptressourcen

	Benutzer müssen dafür sorgen, dass alle Skripts und anderen Artefakte, die bei der Anpassung eines Clusters verwendet werden, für die gesamte Nutzungsdauer des Clusters verfügbar bleiben und dass sich die Versionen dieser Dateien während dieses Zeitraums nicht ändern. Diese Ressourcen werden benötigt, wenn das Reimaging von Knoten im Cluster erforderlich ist. Die bewährte Methode ist das Herunterladen und Archivieren aller Daten in einem Speicherkonto, das der Benutzer steuert. Dies kann das Standardspeicherkonto oder eines der zusätzlichen Speicherkonten sein, das bei der Bereitstellung eines benutzerdefinierten Clusters angegeben wurde. In unseren benutzerdefinierten Spark- und R-Clusterbeispielen, die in der Dokumentation bereitgestellt wurden, wurde z. B. eine lokale Kopie der Ressourcen in diesem Speicherkonto erstellt: https://hdiconfigactions.blob.core.windows.net/.


- Sicherstellen, dass das Clusteranpassungsskript idempotent ist

	Sie müssen davon ausgehen, dass für die Knoten eines HDInsight-Clusters während der Nutzungsdauer des Clusters erneut ein Abbild erstellt wird. Das Clusteranpassungsskript wird immer dann ausgeführt, wenn für einen Cluster ein neues Abbild erstellt wird. Dieses Skript muss dahingehend idempotent sein, dass beim Erstellen eines neuen Image sichergestellt wird, dass der Cluster in denselben angepassten Status zurückgesetzt wird, in dem er sich befand, nachdem das Skript erstmals bei der Clustererstellung ausgeführt wurde. Beispiel: Wenn ein benutzerdefiniertes Skript bei der ersten Ausführung eine Anwendung in "D:\\AppLocation" installiert hat, muss das Skript bei nachfolgenden Ausführungen, wenn ein neues Abbild erstellt wird, prüfen, ob die Anwendung am Speicherort "D:\\AppLocation" vorhanden ist, ehe mit anderen Schritten im Skript fortgefahren wird.


- Installieren benutzerdefinierter Komponenten am optimalen Speicherort

	Wenn von Clusterknoten ein neues Image erstellt wird, können das Ressourcenlaufwerk C:\\ und das Systemlaufwerk D:\\ neu formatiert werden, wodurch es zu einem Verlust von Daten und auf diesen Laufwerken installierten Anwendungen kommen kann. Dies kann auch passieren, wenn ein zum Cluster gehörender Azure VM-Knoten ausfällt und durch einen neuen Knoten ersetzt wird. Sie können Komponenten auf Laufwerk D:/ oder im Verzeichnis "C:\\apps" im Cluster installieren. Alle anderen Speicherorte auf Laufwerk C:\\ sind reserviert. Geben Sie den Installationsort für Anwendungen oder Bibliotheken im Clusteranpassungsskript an.


- Sicherstellen einer hohen Verfügbarkeit der Clusterarchitektur

	HDInsight hat eine Aktiv-Passiv-Architektur für hohe Verfügbarkeit, in dem sich ein Hauptknoten im aktiven Modus (d. h. die HDInsight-Dienste werden ausgeführt) und der andere Hauptknoten im Standby-Modus (d. h. HDInsight-Dienste werden nicht ausgeführt) befindet. Die Knoten schalten zwischen aktivem und passivem Modus um, wenn die HDInsight-Dienste unterbrochen werden. Wenn eine Skriptaktion zur Installation von Diensten auf beiden Hauptknoten für hohe Verfügbarkeit verwendet wird, ist der HDInsight-Failovermechanismus nicht in der Lage, für diese vom Benutzer installierten Dienste automatisch ein Failover durchzuführen. Daher müssen vom Benutzer installierte Dienste auf HDInsight-Hauptknoten, die hoch verfügbar sein sollen, im Aktiv-Passiv-Modus über ihren eigenen Failovermechanismus verfügen oder sich im Aktiv-Aktiv-Modus befinden.

	Der HDInsight-Befehl "Script Action" wird auf beiden Hauptknoten ausgeführt, wenn die Hauptknotenrolle als Wert im Parameter *ClusterRoleCollection* angegeben ist (Informationen dazu finden Sie im nachstehenden Abschnitt [Ausführen einer Skriptaktion](#runScriptAction)). Vergewissern Sie sich beim Entwurf eines benutzerdefinierten Skripts, dass Ihrem Skript diese Konfiguration bekannt ist. Sie sollten beispielsweise keine Probleme bekommen, wenn dieselben Dienste auf beiden Hauptknoten installiert und gestartet wurden und letztlich in Konkurrenz zueinander treten. Daten gehen auch beim Re-imaging verloren, weshalb mithilfe von Skriptaktionen installierte Software bei solchen Ereignissen ausfallsicher sein muss. Anwendungen sollten so konzipiert sein, dass sie mit hoch verfügbaren Daten arbeiten können, die über viele Knoten verteilt werden. Beachten Sie, dass für maximal 1/5 der Knoten eines Clusters gleichzeitig ein neues Abbild erstellt werden kann.


- Konfigurieren benutzerdefinierter Komponenten zur Verwendung von Azure-Blobspeicher

	Die benutzerdefinierten Komponenten, die Sie auf den Clusterknoten installieren, sind möglicherweise standardmäßig so konfiguriert, dass sie den HDFS-Speicher (Hadoop Distributed File System) verwenden. Ändern Sie die Konfiguration, um stattdessen Azure-Blobspeicher zu verwenden. Auf einem Cluster-Reimage wird das HDFS-Dateisystem formatiert, sodass Sie alle darauf gespeicherten Daten verlieren würden. Mithilfe von Azure-Blobspeicher wird stattdessen sichergestellt, dass Ihre Daten erhalten bleiben.

## Gängige Verwendungsmuster

Dieser Abschnitt enthält Anweisungen für das Implementieren einiger gängiger Verwendungsmuster, die Ihnen beim Schreiben Ihrer eigenen benutzerdefinierten Skripts begegnen können.

### Konfigurieren von Umgebungsvariablen

Bei der Entwicklung von Skriptaktionen müssen häufig Umgebungsvariablen festgelegt werden. Das wahrscheinlichste Szenario ist wohl das Herunterladen einer Binärdatei von einer externen Website, ihre Installation im Cluster und das Hinzufügen des Speicherorts der Installation zur Umgebungsvariablen "PATH". Der folgende Codeausschnitt zeigt die Vorgehensweise zum Festlegen von Umgebungsvariablen im benutzerdefinierten Skript.

	Write-HDILog "Starting environment variable setting at: $(Get-Date)";
	[Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Mit dieser Anweisung wird die Umgebungsvariable **MDS\_RUNNER\_CUSTOM\_CLUSTER** auf den Wert "true" und auch der Gültigkeitsbereich dieser Variablen (für den gesamten Computer) festgelegt. Mitunter ist es wichtig, dass Umgebungsvariablen im entsprechenden Bereich (Computer oder Benutzer) festgelegt werden. [Hier][1] finden Sie weitere Informationen zum Festlegen von Umgebungsvariablen.

### Zugriff auf Speicherorte benutzerdefinierter Skripts

Skripts zur Anpassung eines Clusters müssen entweder im Standardspeicherkonto des Clusters oder in einem öffentlichen schreibgeschützten Container eines anderen Speicherkontos enthalten sein. Wenn Ihr Skript auf externe Ressourcen zugreift, müssen diese öffentlich zugänglich sein (oder mindestens einen öffentlichen Lesezugriff aufweisen). Sie möchten z. B. auf eine Datei zugreifen und sie mithilfe des Befehls "SaveFile-HDI" speichern.

	Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

In diesem Beispiel müssen Sie sicherstellen, dass der Container "somecontainer" im Speicherkonto "somestorageaccount" öffentlich zugänglich ist. Andernfalls löst das Skript die Ausnahme "Nicht gefunden" aus und schlägt fehl.

### Übergeben von Parametern an das Cmdlet "Add-AzureRmHDInsightScriptAction"

Um mehrere Parameter an das Cmdlet "Add-AzureRmHDInsightScriptAction" zu übergeben, müssen Sie den Zeichenfolgenwert so formatieren, dass er alle Parameter für das Skript enthält. Beispiel:

	"-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"
 
or

	$parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### Auslösen einer Ausnahme bei nicht erfolgreicher Clusterbereitstellung

Wenn Sie präzise darüber benachrichtigt werden möchten, dass die Clusteranpassung nicht wie erwartet erfolgt ist, ist es wichtig, eine Ausnahme auszulösen und die Clusterbereitstellung abzubrechen. Sie möchten z. B. eine Datei verarbeiten, wenn sie vorhanden ist, und den Fehlerfall behandeln, wenn die Datei nicht vorhanden ist. Dadurch wird sichergestellt, dass das Skript ordnungsgemäß beendet wird und der Status des Clusters ordnungsgemäß bekannt ist. Der folgende Codeausschnitt zeigt ein Beispiel hierzu:

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	exit
	}

Wenn die Datei nicht vorhanden ist, würde dieser Codeausschnitt zu einem Status führen, bei dem das Skript ordnungsgemäß nach Ausgabe der Fehlermeldung beendigt wird, und der Cluster würde einen Ausführungsstatus erreichen, sofern der Clusteranpassungsprozess "erfolgreich" abgeschlossen wurde. Wenn Sie genaue Informationen darüber wünschen, dass die Clusteranpassung aufgrund einer fehlenden Datei nicht erwartungsgemäß abgeschlossen werden konnte, sollten Sie stattdessen eine Ausnahme auslösen und den Clusteranpassungsschritt fehlschlagen lassen. Sie müssten dann stattdessen den folgenden Codeausschnitt verwenden.

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	throw
	}


## Prüfliste für die Bereitstellung einer Skriptaktion
Es folgen unsere Schritte bei der Vorbereitung der Bereitstellung dieser Skripts:

1. Legen Sie Daten mit den benutzerdefinierten Skripts an einem Speicherort ab, auf den die Clusterknoten während der Bereitstellung zugreifen können. Dies kann ein beliebiges Standardkonto oder zusätzliches Speicherkonto, das während der Bereitstellung angegeben wurde, oder ein anderer öffentlich zugänglicher Speichercontainer sein.
2. Fügen Sie Skripts Überprüfungen hinzu, um sicherzustellen, dass sie idempotent ausgeführt werden, damit das Skript mehrmals auf demselben Knoten ausgeführt werden kann.
3. Verwenden Sie das PowerShell-Cmdlet **Write-Output**, um für eine Ausgabe in STDOUT und STDERR zu sorgen. Verwenden Sie nicht **Write-Host**.
4. Verwenden Sie einen temporären Dateiordner wie "$env:TEMP", um die heruntergeladene von den Skripts verwendete Dateien aufzubewahren, und leeren Sie den Ordner nach der Ausführung der Skripts.
5. Installieren Sie benutzerdefinierte Software nur auf "D:\" oder in "C:\\apps". Andere Speicherorte auf Laufwerk C:\\ dürfen nicht verwendet werden, da sie reserviert sind. Beachten Sie, dass das Installieren von Dateien auf Laufwerk C:\\ außerhalb des Ordners "C:/apps" beim Erstellen neuer Abbilder des Knotens zu Einrichtungsfehlern führen kann.
6. Wenn sich Einstellungen auf Betriebssystemebene oder Hadoop-Dienstkonfigurationsdateien geändert haben, können Sie bei Bedarf die HDInsight-Dienste neu starten. Diese können dann Einstellungen auf Betriebssystemebene übernehmen, z. B. die in den Skripts festgelegten Umgebungsvariablen.



## Testen von benutzerdefinierten Skripts mit dem HDInsight-Emulator

Eine einfache Möglichkeit, ein benutzerdefiniertes Skript vor der Verwendung im HDInsight-Skriptaktionsbefehl zu testen, ist seine Ausführung im HDInsight-Emulator. Sie können den HDInsight-Emulator auf einer Azure-IaaS-VM (Infrastructure as a Service) mit Windows Server 2012 R2 oder einem lokalen Computer installieren und beobachten, ob sich das Skript ordnungsgemäß verhält. Beachten Sie, dass die Windows Server 2012 R2 VM dieselbe VM ist, die HDInsight für seine Knoten verwendet.

In diesem Abschnitt werden die Schritte zur lokalen Nutzung des HDInsight Emulators für Testzwecke beschrieben, aber das Verfahren für die Verwendung eines virtuellen Computers ist ähnlich.

**Installieren des HDInsight-Emulators**: Um die Skriptaktion lokal auszuführen, muss der HDInsight-Emulator installiert sein. Installationsanweisungen finden Sie unter [Erste Schritte mit dem HDInsight-Emulator](../hdinsight-get-started-emulator/).

**Festlegen der Ausführungsrichtlinie für Azure PowerShell**: Öffnen Sie Azure PowerShell, und führen Sie den folgenden Befehl (als Administrator) aus, um die Ausführungsrichtlinie auf *LocalMachine* und auf *Unrestricted* festzulegen:

	Set-ExecutionPolicy Unrestricted –Scope LocalMachine

Diese Richtlinie muss uneingeschränkt sein, da Skripts nicht signiert sind.

**Laden Sie die Skriptaktion herunter**, die Sie mit einem lokalen Ziel ausführen möchten. Die folgenden Beispielskripts können von den folgenden Adressen herunterladen werden:

* **Spark**. https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* **R**. https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1
* **Solr**. https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1
* **Giraph**. https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

**Ausführen der Skriptaktion**: Öffnen Sie ein neues Azure PowerShell-Fenster im Administratormodus, und führen Sie das Spark- oder R-Installationsskript am lokalen Speicherort aus, an dem es gespeichert wurde.

**Verwendungsbeispiele**: Bei Verwendung der Spark- und R-Cluster sind benötigte Datendateien ggf. nicht im HDInsight-Emulator enthalten. Daher müssen Sie möglicherweise die relevanten TXT-Dateien mit Daten in einen Pfad in HDFS hochladen und dann diesen Pfad für den Datenzugriff verwenden. Zum Beispiel:

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


Mitunter kann ein benutzerdefiniertes Skript tatsächlich von HDInsight-Komponenten abhängig sein, z. B. zum Erkennen, ob bestimmte Hadoop-Dienste in Betrieb sind. In diesem Fall müssen Sie Ihre benutzerdefinierten Skripts testen, indem Sie sie in einem tatsächlichen HDInsight-Cluster bereitstellen.


## Debuggen von benutzerdefinierten Skripts

Die Skriptfehlerprotokolle werden mit anderen Ausgaben im Standardspeicherkonto gespeichert, das Sie für den Cluster bei seiner Erstellung angegeben haben. Die Protokolle befinden sich in einer Tabelle mit dem Namen *u<\\cluster-name-fragment><\\time-stamp>setuplog*. Dabei handelt es sich um zusammengeführte Protokolle mit Aufzeichnungen aller Knoten (Haupt- und Workerknoten), auf denen das Skript im Cluster ausgeführt wurde. Die Protokolle können ganz einfach mit den HDInsight-Tools für Visual Studio überprüft werden. Informationen zum Installieren der Tools finden Sie unter [Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio).

**So überprüfen Sie das Protokoll mit Visual Studio**

1. Öffnen Sie Visual Studio.
2. Klicken Sie auf **Ansicht** und anschließend auf **Server-Explorer**.
3. Klicken Sie mit der rechten Maustaste auf „Azure“, klicken Sie auf **Mit Microsoft Azure-Abonnements verbinden**, und geben Sie dann Ihre Anmeldeinformationen ein.
4. Erweitern Sie **Speicher**, das als Standarddateisystem verwendete Azure-Speicherkonto und **Tabellen**, und doppelklicken Sie dann auf den Tabellennamen.


Sie können auch remote auf die Clusterknoten zugreifen, um STDOUT und STDERR für benutzerdefinierte Skripts anzuzeigen. Die Protokolle auf jedem Knoten beziehen sich nur auf diesen Knoten und befinden sich unter **C:\\HDInsightLogs\\DeploymentAgent.log**. In diesen Protokolldateien werden alle Ausgaben aus dem benutzerdefinierten Skript aufgezeichnet. Ein Beispielprotokollauszug einer Spark-Skriptaktion sieht so aus:

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
	Version : 2.1.0.0;
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
	AzureVMName : HEADNODE0;
	IsException : False;
	ExceptionType : ;
	ExceptionMessage : ;
	InnerExceptionType : ;
	InnerExceptionMessage : ;
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

	Version : 2.1.0.0;
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
	AzureVMName : HEADNODE0;
	IsException : False;
	ExceptionType : ;
	ExceptionMessage : ;
	InnerExceptionType : ;
	InnerExceptionMessage : ;
	Exception : ;
	...

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
	Version : 2.1.0.0;
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
	AzureVMName : HEADNODE0;
	IsException : False;
	ExceptionType : ;
	ExceptionMessage : ;
	InnerExceptionType : ;
	InnerExceptionMessage : ;
	Exception : ;


Aus diesem Protokoll geht hervor, dass die Spark-Skriptaktion auf dem virtuellen Computer HEADNODE0 ausgeführt wurde und keine Ausnahmen verursacht hat.

Bei Auftreten eines Ausführungsfehlers enthält die Protokolldatei auch die beschreibende Ausgabe. Die Informationen in diesen Protokollen sind hilfreich zum Debuggen eventuell auftretender Probleme.


## Weitere Informationen

- [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen][hdinsight-cluster-customize]
- [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark]
- [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-r-scripts]
- [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md)
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md)

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx

<!---HONumber=AcomDC_1203_2015-->