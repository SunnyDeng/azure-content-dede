<properties
 pageTitle="Automatische Skalierung von Computeressourcen im HPC-Cluster | Microsoft Azure"
 description="Informationen zu verschiedenen Methoden zum automatischen Vergrößern und Verkleinern von Computeressourcen in einem HPC Pack-Cluster in Azure."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Automatisches zentrales Hoch- und Herunterskalieren von Azure-Computeressourcen in einem HPC Pack-Cluster entsprechend der Clusterworkload

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel bezieht sich auf die Verwaltung einer Ressource, die mit dem klassischen Bereitstellungsmodell erstellt wurde.

Wenn Sie Azure-Burstknoten im HPC Pack-Cluster bereitstellen oder einen HPC Pack-Cluster in virtuellen Azure-Computern erstellen, möchten Sie möglicherweise die Azure-Computeressourcen entsprechend der aktuellen Workload der Aufträge und Aufgaben im Cluster automatisch vergrößern oder verkleinern. Auf diese Weise können Sie die Azure-Ressourcen effizienter nutzen und die Kosten kontrollieren. Verwenden Sie hierzu das HPC PowerShell-Skript **AzureAutoGrowShrink.ps1**, das mit HPC Pack installiert wird.

>[AZURE.TIP]Ab HPC Pack 2012 R2 Update 2 enthält HPC Pack einen integrierten Dienst zum automatischen Vergrößern und Verkleinern von Azure-Burstknoten oder Azure-VM-Computeknoten. Konfigurieren Sie den Dienst mit einer Einstellung im [HPC Pack IaaS-Bereitstellungsskript](virtual-machines-hpcpack-cluster-powershell-script.md), oder legen Sie die Clustereigenschaft **AutoGrowShrink** manuell fest. Siehe dazu [What’s New in Microsoft HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx) (in englischer Sprache).

## Voraussetzungen

* **Cluster mit HPC Pack 2012 R2 Update 1 oder höher** – Das Skript **AzureAutoGrowShrink.ps1** ist im Ordner "%CCP\_HOME%bin" installiert. Der Clusterhauptknoten kann entweder lokal oder auf einem virtuellen Azure-Computer bereitgestellt werden. Informationen für die ersten Schritte mit einem lokalen Hauptknoten und Azure-Burstknoten finden Sie unter [Einrichten eines Hybrid-Rechenclusters mit Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Informationen zur schnellen Bereitstellung eines HPC Pack-Clusters auf virtuellen Azure-Computern finden Sie im Artikel zur Verwendung des [HPC Pack IaaS-Bereitstellungsskripts](virtual-machines-hpcpack-cluster-powershell-script.md).

* **Für einen Cluster mit Azure-Burstknoten** – Führen Sie das Skript auf einem Clientcomputer, auf dem HPC Pack installiert ist, oder auf dem Hauptknoten aus. Wenn Sie das Skript auf einem Clientcomputer ausführen, stellen Sie sicher, dass Sie die Variable "$env:CCP\_SCHEDULER" so festlegen, dass sie auf den Hauptknoten verweist. Die Azure-Burstknoten müssen dem Cluster bereits hinzugefügt sein, sie können jedoch den Status "Nicht bereitgestellt" aufweisen.


* **Für einen auf virtuellen Azure-Computern bereitgestellten Cluster** – Führen Sie das Skript auf dem virtuellen Computer für den Hauptknoten aus, da er von den Skripts **Start HpcIaaSNode.ps1** und **Stop HpcIaaSNode.ps1** abhängt, die dort installiert sind. Für diese Skripts ist zudem ein Azure-Verwaltungszertifikat oder eine Azure-Veröffentlichungseinstellungsdatei erforderlich (siehe [Manage the number and availability of compute nodes in an HPC Pack cluster in Azure](virtual-machines-hpcpack-cluster-node-manage.md) (in englischer Sprache)). Stellen Sie sicher, dass alle virtuellen Computer für Computeknoten dem Cluster bereits hinzugefügt sind. Sie können sich jedoch im Status "Beendet" befinden.

## Syntax

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## Parameter

 * **NodeTemplates** – Namen der Knotenvorlagen zum Definieren des Bereichs, in dem sich die Knoten vergrößern und verkleinern. Wenn kein Bereich angegeben ist (der Standardwert ist "@()"), liegen alle Knoten in der Knotengruppe **AzureNodes** im Bereich, wenn für **NodeType** der Wert "AzureNodes" festgelegt ist. Und alle Knoten in der Knotengruppe **ComputeNodes** liegen im Bereich, wenn für **NodeType** der Wert "ComputeNodes" festgelegt ist.

 * **JobTemplates** – Namen der Auftragsvorlagen zum Definieren des Bereichs, in dem sich die Knoten vergrößern.

 * **NodeType** – Der Typ des Knotens, der sich vergrößert und verkleinert. Folgende Werte werden unterstützt:

     * **AzureNodes** – Für Azure-PaaS (Burst)-Knoten in einem lokalen oder Azure-IaaS-Cluster.

     * **ComputeNodes** – Nur für virtuelle Computer für Computeknoten in einem Azure-IaaS-Cluster.

* **NumOfQueuedJobsPerNodeToGrow** – Die Anzahl der in der Warteschlange befindlichen Aufträge, die erforderlich sind, damit ein Knoten vergrößert wird.

* **NumOfQueuedJobsToGrowThreshold** – Der Schwellenwert der in der Warteschlange befindlichen Aufträge zum Starten des Vergrößerungsvorgangs.

* **NumOfActiveQueuedTasksPerNodeToGrow** – Die Anzahl der aktiven in der Warteschlange befindlichen Aufgaben, die erforderlich sind, damit ein Knoten vergrößert wird. Wenn **NumOfQueuedJobsPerNodeToGrow** mit einem Wert größer als 0 angegeben ist, wird dieser Parameter ignoriert.

* **NumOfActiveQueuedTasksToGrowThreshold** – Der Schwellenwert der aktiven in der Warteschlange befindlichen Aufgaben zum Starten des Vergrößerungsvorgangs.

* **NumOfInitialNodesToGrow** – Die anfängliche minimale Anzahl der zu vergrößernden Knoten, wenn alle Knoten im Bereich den Status **Nicht bereitgestellt** oder **Beendet (Zuordnung aufgehoben)** aufweisen.

* **GrowCheckIntervalMins** – Das Intervall in Minuten zwischen Überprüfungen auf Vergrößerung.

* **ShrinkCheckIntervalMins** – Das Intervall in Minuten zwischen Überprüfungen auf Verkleinerung.

* **ShrinkCheckIdleTimes** – Die Anzahl der fortlaufenden Verkleinerungsüberprüfungen (getrennt durch **ShrinkCheckIntervalMins**), durch die angegeben wird, dass sich die Knoten im Leerlauf befinden.

* **UseLastConfigurations*** – Die vorherigen in der Argumentdatei gespeicherten Konfigurationen.

* **ArgFile** – Der Name der Argumentdatei, in der die Konfigurationen zum Ausführen des Skripts gespeichert und aktualisiert werden.

* **LogFilePrefix** – Der Präfixname der Protokolldatei. Sie können einen Pfad angeben. Standardmäßig wird das Protokoll in das aktuelle Arbeitsverzeichnis geschrieben.

### Beispiel 1

Im folgenden Beispiel werden die mit der Standardvorlage "AzureNode" bereitgestellten Azure-Burstknoten so konfiguriert, dass sie automatisch vergrößert und verkleinert werden. Wenn sich alle Knoten anfänglich im Status **Nicht bereitgestellt** befinden, werden mindestens 3 Knoten gestartet. Wenn die Anzahl der in der Warteschlange befindlichen Aufträge 8 überschreitet, startet das Skript Knoten, bis ihre Anzahl das Verhältnis der Aufträge in der Warteschlange zu **NumOfQueuedJobsPerNodeToGrow** überschreitet. Wenn sich ein Knoten in 3 aufeinanderfolgenden Leerlaufzeiten im Leerlauf befindet, wird er beendet.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### Beispiel 2

Im folgenden Beispiel werden die mit der Standardvorlage "ComputeNode" bereitgestellten virtuellen Azure Computer für den Computeknoten so konfiguriert, dass sie automatisch vergrößert und verkleinert werden. Die durch die Standardauftragsvorlage konfigurierten Aufträge definieren den Bereich der Workload auf dem Cluster. Wenn alle Knoten anfänglich beendet sind, werden mindestens 5 Knoten gestartet. Wenn die Anzahl der in der Warteschlange befindlichen aktiven Aufgaben 15 überschreitet, startet das Skript Knoten, bis ihre Anzahl das Verhältnis der aktiven Aufgaben in der Warteschlange zu **NumOfActiveQueuedTasksPerNodeToGrow** überschreitet. Wenn sich ein Knoten in 10 aufeinanderfolgenden Leerlaufzeiten im Leerlauf befindet, wird er beendet.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=Oct15_HO1-->