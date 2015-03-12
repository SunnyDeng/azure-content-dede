<properties 
	pageTitle="Entwickeln von Skriptaktionen mit HDInsight | Azure" 
	description="Erfahren Sie, wie Sie mit Skriptaktionen Hadoop-Cluster anpassen können." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="bradsev"/> 

# Entwickeln von Skriptaktionen mit HDInsight 

Skriptaktionen erweitern die Funktionalität von Azure HDInsight, um zusätzliche Software in einem Hadoop-Cluster zu installieren oder um die Konfiguration von in einem Cluster installierten Anwendungen zu ändern. Skriptaktionen sind Skripts, die auf Clusterknoten ausgeführt werden, wenn HDInsight-Cluster bereitgestellt sind. Sie werden dann ausgeführt, sobald die HDInsight-Konfiguration auf Knoten im Cluster abgeschlossen ist. Die Skriptaktion wird mit Berechtigungen des Systemadministratorkontos ausgeführt und bietet umfassende Zugriffsrechte auf die Clusterknoten. Jeder Cluster kann mit einer Liste von auszuführenden Skriptaktionen bereitgestellt werden, die in der angegebenen Reihenfolge ausgeführt werden.

"Skriptaktion" kann über Azure PowerShell oder das HDInsight .NET SDK bereitgestellt werden.  Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen][hdinsight-cluster-customize].


## Themen in diesem Artikel

- [Empfohlene Methoden für die Skriptentwicklung](#bestPracticeScripting)
- [Hilfsmethoden für benutzerdefinierte Skripts](#helpermethods)
- [Prüfliste für die Bereitstellung einer Skriptaktion](#deployScript)
- [Ausführen einer Skriptaktion](#runScriptAction)
- [Beispiele benutzerdefinierter Skripts](#sampleScripts) 
- [Testen des benutzerdefinierten Skripts mit dem HDInsight Emulator](#testScript)
- [Debuggen eines benutzerdefinierten Skripts](#debugScript)
- [Siehe auch](#seeAlso)


## <a name="bestPracticeScripting"></a>Empfohlene Methoden für die Skriptentwicklung

Wenn Sie ein benutzerdefiniertes Skript für einen HDInsight-Cluster entwickeln, sollten Sie mehrere bewährte Methoden beachten:

* [Überprüfen der Hadoop-Version](#bPS1)
* [Einrichten stabiler Verknüpfungen mit Skriptressourcen](#bPS2)
* [Das Clusteranpassungsskript muss idempotent sein](#bPS3)
* [Der Installationsort der benutzerdefinierten Komponenten](#bPS4)
* [Clusterarchitektur: Sicherstellen hoher Verfügbarkeit](#bPS5)
* [Konfigurieren der benutzerdefinierten Komponenten für die Verwendung von WASB](#bPS6)

### <a name="bPS1"></a>Überprüfen der Hadoop-Version
Nur HDInsight Version 3.1 (Hadoop 2.4) und höher unterstützen die Installation von benutzerdefinierten Komponenten in einem Cluster mithilfe von Skriptaktionen. Sie müssen im benutzerdefinierten Skript die Hilfsmethode **Get-HDIHadoopVersion** verwenden, um die Hadoop-Version zu überprüfen, bevor Sie mit anderen Aufgaben im Skript fortfahren können.


### <a name="bPS2"></a>Einrichten stabiler Verknüpfungen mit Skriptressourcen 
Benutzer müssen dafür sorgen, dass alle Skripts und anderen Artefakte, die bei der Anpassung eines Clusters verwendet werden, für die gesamte Nutzungsdauer des Clusters verfügbar bleiben und dass sich die Versionen dieser Dateien während dieses Zeitraums nicht ändern. Diese Ressourcen werden benötigt, wenn das Reimaging von Knoten im Cluster erforderlich ist. Die bewährte Methode ist das Herunterladen und Archivieren aller Daten in einem Speicherkonto, das der Benutzer steuert. Dies kann das Standardspeicherkonto oder eines der zusätzlichen Speicherkonten sein, das bei der Bereitstellung eines benutzerdefinierten Clusters angegeben wurde.
In unseren benutzerdefinierten Spark- und R-Clusterbeispielen, die in der Dokumentation bereitgestellt wurden, wurde z. B. eine lokale Kopie der Ressourcen in diesem Speicherkonto erstellt:: https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>Das Clusteranpassungsskript muss idempotent sein
Sie müssen davon ausgehen, dass für die Knoten eines HDInsight-Clusters während der Nutzungsdauer des Clusters erneut ein Abbild erstellt wird. Das Clusteranpassungsskript wird immer dann ausgeführt, wenn für einen Cluster ein neues Abbild erstellt wird. Dieses Skript muss dahingehend idempotent sein, dass beim Erstellen eines neuen Image sichergestellt wird, dass der Cluster in denselben angepassten Status zurückgesetzt wird, in dem er sich befand, nachdem das Skript erstmals bei der Clustererstellung ausgeführt wurde. Beispiel: Wenn ein benutzerdefiniertes Skript bei der ersten Ausführung eine Anwendung in "D:\AppLocation" installiert hat, muss das Skript bei nachfolgenden Ausführungen, wenn ein neues Abbild erstellt wird, prüfen, ob die Anwendung am Speicherort "D:\AppLocation" vorhanden ist, ehe mit anderen Schritten im Skript fortgefahren wird.


### <a name="bPS4"></a>Der Installationsort der benutzerdefinierten Komponenten 
Wenn von Clusterknoten ein neues Image erstellt wird, können das Ressourcenlaufwerk C:\ und das Systemlaufwerk D:\ neu formatiert werden, wodurch es zu einem Verlust von Daten und auf diesen Laufwerken installierten Anwendungen kommen kann. Dies kann auch passieren, wenn ein zum Cluster gehörender Azure VM-Knoten ausfällt und durch einen neuen Knoten ersetzt wird. Sie können Komponenten auf Laufwerk D:/ oder im Verzeichnis "C:\apps" im Cluster installieren. Alle anderen Speicherorte auf Laufwerk C:\ sind reserviert. Geben Sie den Installationsort für Anwendungen oder Bibliotheken im Clusteranpassungsskript an. 


### <a name="bPS5"></a>Clusterarchitektur: Sicherstellen hoher Verfügbarkeit

HDInsight hat eine Aktiv-Passiv-Architektur für hohe Verfügbarkeit, in dem sich ein Hauptknoten im aktiven Modus (d. h. die HDInsight-Dienste werden ausgeführt) und der andere Hauptknoten im Standby-Modus (d. h. HDInsight-Dienste werden nicht ausgeführt) befindet. Die Knoten schalten zwischen aktivem und passivem Modus um, wenn die HDInsight-Dienste unterbrochen werden. Wenn eine Skriptaktion zur Installation von Diensten auf beiden Hauptknoten für hohe Verfügbarkeit verwendet wird, ist der HDInsight-Failovermechanismus nicht in der Lage, für diese vom Benutzer installierten Dienste automatisch ein Failover durchzuführen. Daher müssen vom Benutzer installierte Dienste auf HDInsight-Hauptknoten, die hoch verfügbar sein sollen, im Aktiv-Passiv-Modus über ihren eigenen Failovermechanismus verfügen oder sich im Aktiv-Aktiv-Modus befinden. 

Ein HDInsight Script Action-Befehl wird auf beiden Hauptknoten ausgeführt, wenn die Hauptknotenrolle als Wert im Parameter *ClusterRoleCollection* angegeben ist. (Informationen dazu finden Sie im unten stehenden Abschnitt [Ausführen einer Skriptaktion](#runScriptAction).) Vergewissern Sie sich beim Entwurf eines benutzerdefinierten Skripts, dass Ihrem Skript diese Konfiguration bekannt ist. Sie sollten beispielsweise keine Probleme bekommen, wenn dieselben Dienste auf beiden Hauptknoten installiert und gestartet wurden und letztlich in Konkurrenz zueinander treten. Daten gehen auch beim Reimaging verloren, weshalb mithilfe von Skriptaktionen installierte Software bei solchen Ereignissen ausfallsicher sein muss. Anwendungen sollten so konzipiert sein, dass sie mit hoch verfügbaren Daten arbeiten können, die über viele Knoten verteilt werden. Beachten Sie, dass für maximal 1/5 der Knoten eines Clusters gleichzeitig ein neues Abbild erstellt werden kann.


### <a name="bPS6"></a>Konfigurieren der benutzerdefinierten Komponenten für die Verwendung von WASB
Die benutzerdefinierten Komponenten, die Sie auf den Clusterknoten installieren, sind möglicherweise standardmäßig so konfiguriert, dass sie den HDFS-Speicher verwenden. Ändern Sie die Konfiguration, um stattdessen Azure-Speicher-BLOB (WASB) zu verwenden. Auf einem Cluster-Reimage wird das HDFS-Dateisystem formatiert, sodass Sie alle darauf gespeicherten Daten verlieren würden. Wenn Sie stattdessen WASB verwenden, können Sie sicherstellen, dass Ihre Daten erhalten bleiben.

## <a name="helpermethods"></a>Hilfsmethoden für benutzerdefinierte Skripts 

Script Action bietet die folgenden Hilfsmethoden, mit denen Sie zum Schreiben von benutzerdefinierten Skripts verwenden können.

Hilfsmethode | Beschreibung
-------------- | -----------
**Save-HDIFile** | Herunterladen einer Datei vom angegebenen URI an einen Speicherort auf dem lokalen Datenträger, der dem auf dem Cluster zugewiesenen Azure VM-Knoten zugeordnet ist
**Expand-HDIZippedFile** | Entpacken der ZIP-Datei
**Invoke-HDICmdScript** | Ausführen eines Skripts über cmd.exe
**Write-HDILog** | Schreiben der Ausgabe aus dem benutzerdefinierten Skript, das für die Skriptaktion verwendet wird
**Get-Services** | Abrufen einer Liste an Diensten, die auf den Computer ausgeführt werden, auf denen das Skript ausgeführt wird
**Get-Service** | Mit dem bestimmten Dienstnamen als Eingabe werden detaillierte Informationen für einen bestimmten Dienst (Dienstname, Prozess-ID, Status usw.) auf dem Computer, auf dem das Skript ausgeführt wird, zurückgegeben.
**Get-HDIServices** | Abrufen einer Liste der HDInsight-Dienste auf dem Computer, auf dem das Skript ausgeführt wird
**Get-HDIService** | Mit dem bestimmten HDInsight-Dienstnamen als Eingabe werden detaillierte Informationen für einen bestimmten Dienst (Dienstname, Prozess-ID, Status usw.) auf dem Computer, auf dem das Skript ausgeführt wird, zurückgegeben.
**Get-ServicesRunning** | Abrufen einer Liste von Diensten, die auf dem Computer ausgeführt werden, auf dem das Skript ausgeführt wird
**Get-ServiceRunning** | Überprüfen, ob ein bestimmter Dienst (namentlich) auf dem Computer ausgeführt wird, auf dem das Skript ausgeführt wird
**Get-HDIServicesRunning** | Abrufen einer Liste der HDInsight-Dienste auf dem Computer, auf dem das Skript ausgeführt wird
**Get-HDIServiceRunning** | Überprüfen, ob ein bestimmter HDInsight-Dienst (namentlich) auf dem Computer ausgeführt wird, auf dem das Skript ausgeführt wird
**Get-HDIHadoopVersion** | Abrufen der Hadoop-Version, die auf dem Computer installiert ist, auf dem das Skript ausgeführt wird
**Test-IsHDIHeadNode** | Überprüfen, ob der Computer, auf dem das Skript ausgeführt wird, ein Hauptknoten ist
**Test-IsActiveHDIHeadNode** | Überprüfen, ob der Computer, auf dem das Skript ausgeführt wird, ein aktiver Hauptknoten ist
**Test-IsHDIDataNode** | Überprüfen, ob der Computer, auf dem das Skript ausgeführt wird, ein Datenknoten ist
**Edit-HDIConfigFile** | Bearbeiten der Konfigurationsdateien "hive-site.xml", "core-site.xml", "hdfs-site.xml", "mapred-site.xml" oder "yarn-site.xml"
 

## <a name="deployScript"></a>Prüfliste für die Bereitstellung einer Skriptaktion
Es folgen unsere Schritte bei der Vorbereitung der Bereitstellung dieser Skripts:

1. Legen Sie Daten mit den benutzerdefinierten Skripts an einem Speicherort ab, auf den die Clusterknoten während der Bereitstellung zugreifen können. Dies kann ein beliebiges Standardkonto oder zusätzliches Speicherkonto, das während der Bereitstellung angegeben wurde, oder ein anderer öffentlich zugänglicher Speichercontainer sein.
2. Fügen Sie Skripts Überprüfungen hinzu, um sicherzustellen, dass sie idempotent ausgeführt werden, damit das Skript mehrmals auf demselben Knoten ausgeführt werden kann.
3. Verwenden Sie das PowerShell-Cmdlet **Write-Output**, um für eine Ausgabe in STDOUT und STDERR zu sorgen. Verwenden Sie nicht **Write-Host**.
4. Verwenden Sie einen temporären Dateiordner wie "$env:TEMP", um die heruntergeladene von den Skripts verwendete Dateien aufzubewahren, und leeren Sie den Ordner nach der Ausführung der Skripts.
5. Installieren Sie angepasste Software nur an den folgenden Speicherorten: D:/ oder C:/apps. Andere Speicherorte auf Laufwerk C:\ dürfen nicht verwendet werden, da sie reserviert sind. Beachten Sie, dass das Installieren von Dateien auf Laufwerk C:\ außerhalb des Ordners "C:/apps" beim Erstellen neuer Abbilder des Knotens zu Einrichtungsfehlern führen kann.
6. Wenn sich Einstellungen auf Betriebssystemebene oder Hadoop-Dienstkonfigurationsdateien geändert haben, können Sie bei Bedarf die HDInsight-Dienste neu starten. Diese können dann Einstellungen auf Betriebssystemebene übernehmen, z. B. die in den Skripts festgelegten Umgebungsvariablen.


## <a name="runScriptAction"></a>Ausführen einer Skriptaktion

Sie können Skriptaktionen zum Anpassen von HDInsight-Clustern mithilfe des Azure-Verwaltungsportals, PowerShell oder dem HDInsight .NET SDK verwenden. Anweisungen hierzu finden Sie unter [Verwenden von Script Action](./hdinsight-hadoop-customize-cluster/#howto). 


## <a name="sampleScripts"></a>Beispiele benutzerdefinierter Skripts

Microsoft bietet Beispielskripts für die Installation von Komponenten in einem HDInsight-Cluster. Die Beispielskripts und Nutzungsanweisungen stehen unter den folgenden Links zur Verfügung:

- [Installieren und Verwenden von Spark 1.0 in HDInsight-Clustern][hdinsight-install-spark]
- [Installieren und Verwenden von R in HDInsight Hadoop-Clustern][hdinsight-r-scripts]
- [Installieren und Verwenden von Solr in HDInsight-Clustern](../hdinsight-hadoop-solr-install).
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](../hdinsight-hadoop-giraph-install).  

> [AZURE.NOTE] Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1 oder höher. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

## <a name="testScript"></a>Testen des benutzerdefinierten Skripts mit dem HDInsight Emulator

Eine einfache Möglichkeit, ein benutzerdefiniertes Skript vor seiner Verwendung im HDInsight Script Action-Befehl zu testen, besteht darin, es manuell auf dem HDInsight Emulator auszuführen. Der Emulator kann lokal oder auf einem virtuellen Windows Server 2012 R2-Computer in Azure IaaS installiert sein, um zu prüfen, ob sich das Skript ordnungsgemäß verhält. Beachten Sie, dass Windows Server 2012 R2 derselbe virtuelle Computer ist, den HDInsight für seine Knoten verwendet.

In diesem Abschnitt werden die Schritte zur lokalen Nutzung des HDInsight Emulators für Testzwecke beschrieben, aber das Verfahren für die Verwendung eines virtuellen Computers ist ähnlich.

**Installieren des HDInsight Emulator**: Zum lokalen Ausführen von Skriptaktionen muss der HDInsight Emulator installiert sein. Installationsanweisungen finden Sie unter [Erste Schritte mit dem HDInsight Emulator](http://azure.microsoft.com/documentation/articles/hdinsight-get-started-emulator/)

**Legen Sie die Ausführungsrichtlinie für Azure PowerShell fest:** Öffnen Sie Microsoft Azure PowerShell, und führen Sie den folgenden Befehl (als Administrator) aus, um die Ausführungsrichtlinie auf die *LocalMachine* und als *Unrestricted* festzulegen.
 
	Set-ExecutionPolicy Unrestricted -Scope LocalMachine

Diese Richtlinie muss uneingeschränkt sein, da Skripts nicht signiert sind.

**Laden Sie die Skriptaktion herunter**, die Sie als lokales Ziel ausführen möchten. Die Spark- und R-Skripts, die lokal ausgeführt werden können, stehen z. B. an den folgenden Speicherorten zur Verfügung:

* https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1

**Ausführen der Aktionsskripts**: Öffnen Sie ein neues Azure PowerShell im Administratormodus, und führen Sie das Spark- oder R-Installationsskript vom lokalen Speicherort aus, an dem es gespeichert wurde.

**Anwendungsbeispiele**
Bei der Verwendung von Spark- und R-Clustern, sind benötigte Datendateien u. U. nicht im HDInsight Emulator vorhanden, sodass Sie die entsprechenden TXT-Dateien hochladen müssen, die Daten zu einem Pfad in HDFS enthalten, um dann über diesen Pfad auf die Daten zuzugreifen. Beispiel:

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


Mitunter kann ein benutzerdefiniertes Skript tatsächlich von HDInsight-Komponenten abhängig sein, z. B. zum Erkennen, ob bestimmte Hadoop-Dienste in Betrieb sind. In diesem Fall müssen Sie Ihre benutzerdefinierten Skripts testen, indem Sie sie in einem tatsächlichen HDInsight-Cluster bereitstellen.


## <a name="debugScript"></a>Debuggen eines benutzerdefinierten Skripts

Die Skriptfehlerprotokolle werden mit anderen Ausgaben im Standardspeicherkonto gespeichert, das Sie für den Cluster bei seiner Erstellung angegeben haben. Die Protokolle werden in einer Tabelle mit dem Namen *u<\cluster-name-fragment><\time-stamp>setuplog* gespeichert. Dabei handelt es sich um zusammengeführte Protokolle mit Aufzeichnungen aller Knoten (Haupt- und Arbeitsknoten), auf denen das Skript im Cluster ausgeführt wurde.

Sie können auch remote auf die Clusterknoten zugreifen, um STDOUT und STDERR der benutzerdefinierten Skripts anzuzeigen. Die Protokolle auf jedem Knoten beziehen sich nur auf diesen Knoten und befinden sich unter **C:\HDInsightLogs\DeploymentAgent.log**. In diesen Protokolldateien werden alle Ausgaben aus dem benutzerdefinierten Skript aufgezeichnet. Ein Beispielprotokollauszug einer Spark-Skriptaktion sieht so aus:

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


## <a name="seeAlso"></a>Weitere Informationen

[Anpassen von HDInsight-Clustern mithilfe von Skriptaktion][hdinsight-cluster-customize] 


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/
<!--HONumber=42-->
