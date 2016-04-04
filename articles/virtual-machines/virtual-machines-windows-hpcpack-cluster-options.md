<properties
 pageTitle="Windows HPC Pack-Clusteroptionen in der Cloud | Microsoft Azure"
 description="Informationen zu Optionen mit Microsoft HPC Pack, um einen Windows HPC-Cluster (High Performance Computing) in der Azure-Cloud zu erstellen und zu verwalten."
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Optionen zum Erstellen und Verwalten eines Windows HPC-Clusters (High Performance Computing) in Azure mit Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

## Ausführen eines HPC Pack-Clusters in virtuellen Azure-Computern

### Azure-Vorlagen

* (Marketplace) [HPC Pack cluster for Windows workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) (HPC Pack-Cluster für Windows-Workloads)

* (Marketplace) [HPC Pack cluster for Excel workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/) (HPC Pack-Cluster für Excel-Workloads)

* (Schnellstart) [Create an HPC cluster](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) (Erstellen eines HPC-Clusters)

* (Schnellstart) [Create an HPC cluster with custom compute node image](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) (Erstellen eines HPC-Clusters mit einem benutzerdefinierten Computeknotenimage)

### Azure VM-Images

* [HPC Pack unter Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [HPC Pack-Computeknoten unter Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [HPC Pack-Computeknoten mit Excel unter Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### PowerShell-Bereitstellungsskript

* [Erstellen eines HPC-Clusters mit dem HPC Pack IaaS-Bereitstellungsskript](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### Lernprogramme

* [Tutorial: Erste Schritte mit einem HPC Pack-Cluster in Azure zum Ausführen von Excel- und SOA-Workloads](virtual-machines-windows-excel-cluster-hpcpack.md)



### Manuelle Bereitstellung über das Azure-Portal

* [Einrichten des Hauptknotens eines HPC Pack-Clusters in einem virtuellen Azure-Computer](virtual-machines-windows-hpcpack-cluster-headnode.md)

### Clusterverwaltung

* [Verwalten von Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)


* [Vergrößern und Verkleinern von Azure-Compute-Ressourcen in einem HPC Pack-Cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Übermitteln von Aufträgen an einen HPC Pack-Cluster in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Hinzufügen von Workerrolleknoten zu einem HPC Pack-Cluster


* [Burst to Azure with Microsoft HPC Pack (in englischer Sprache)](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Einrichten eines Hybridclusters mit HPC Pack in Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Hinzufügen von Azure-"Burst"-Knoten zu einem HPC Pack-Hauptknoten in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)

* [Vergrößern und Verkleinern von Azure-Compute-Ressourcen in einem HPC Pack-Cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

## Integration in Azure Batch 

* [Burst to Azure Batch with Microsoft HPC Pack (in englischer Sprache)](https://technet.microsoft.com/library/mt612877.aspx)

## Erstellen von RDMA-Clustern für MPI-Workloads

* [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0323_2016-->