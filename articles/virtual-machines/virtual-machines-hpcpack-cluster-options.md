<properties
 pageTitle="HPC Pack-Clusteroptionen in der Cloud | Microsoft Azure"
 description="Informationen zu Optionen mit Microsoft HPC Pack, um einen HPC-Cluster (High Performance Computing) in der Azure-Cloud zu erstellen und zu verwalten."
 services="virtual-machines,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Optionen zum Erstellen und Verwalten eines HPC-Clusters (High Performance Computing) in Azure mit Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Nutzen Sie Microsoft HPC Pack und die Azure-Dienste für Berechnung und Infrastruktur, um einen HPC-Cluster (High Performance Computing) zu erstellen und zu verwalten. [HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) ist die kostenlose HPC-Lösung von Microsoft, die auf Microsoft Azure und Windows Server-Technologien basiert und sowohl Windows- als auch Linux HPC-Workloads unterstützt. Ein cloudbasierter HPC Pack-Cluster stellt einem Clusteradministrator oder unabhängigen Softwarehersteller (Independent Software Vendor, ISV) eine flexible, skalierbare Plattform zur Ausführung rechenintensiver Anwendungen zur Verfügung, die es ermöglicht, Investitionen in eine lokale Computecluster-Infrastruktur zu reduzieren.


## Ausführen eines HPC Pack-Clusters in virtuellen Azure-Computern

### Azure-Vorlagen

* (Marketplace) [HPC Pack cluster for Windows workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) (HPC Pack-Cluster für Windows-Workloads)

* (Marketplace) [HPC Pack cluster for Excel workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/) (HPC Pack-Cluster für Excel-Workloads)

* (Marketplace) [HPC Pack cluster for Linux workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) (HPC Pack-Cluster für Linux-Workloads)

* (Schnellstart) [Create an HPC cluster](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) (Erstellen eines HPC-Clusters)

* (Schnellstart) [Create an HPC cluster with Linux compute nodes](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) (Erstellen eines HPC-Clusters mit Linux-Serverknoten)

* (Schnellstart) [Create an HPC cluster with custom compute node image](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) (Erstellen eines HPC-Clusters mit einem benutzerdefinierten Serverknotenimage)

### Azure VM-Images

* [HPC Pack unter Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [HPC Pack-Computeknoten unter Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [HPC Pack-Computeknoten mit Excel unter Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### PowerShell-Bereitstellungsskript

* [Erstellen eines HPC-Clusters mit dem HPC Pack IaaS-Bereitstellungsskript](virtual-machines-hpcpack-cluster-powershell-script.md)

### Lernprogramme

* [Tutorial: Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md)

* [Tutorial: Ausführen von NAMD mit dem Microsoft HPC Pack auf Linux-Computeknoten in Azure](virtual-machines-linux-cluster-hpcpack-namd.md)

* [Lernprogramm: Ausführen von OpenFOAM mit Microsoft HPC Pack auf einem Linux-RDMA-Cluster in Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md)

* [Tutorial: Erste Schritte mit einem HPC Pack-Cluster in Azure zum Ausführen von Excel- und SOA-Workloads](virtual-machines-excel-cluster-hpcpack.md)



### Manuelle Bereitstellung über das Azure-Portal

* [Einrichten des Hauptknotens eines HPC Pack-Clusters in einem virtuellen Azure-Computer](virtual-machines-hpcpack-cluster-headnode.md)

### Clusterverwaltung

* [Verwalten von Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-hpcpack-cluster-node-manage.md)


* [Vergrößern und Verkleinern von Azure-Compute-Ressourcen in einem HPC Pack-Cluster](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Übermitteln von Aufträgen an einen HPC Pack-Cluster in Azure](virtual-machines-hpcpack-cluster-submit-jobs.md)


## Hinzufügen von Workerrolleknoten zu einem HPC Pack-Cluster


* [Burst to Azure with Microsoft HPC Pack (in englischer Sprache)](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Einrichten eines Hybridclusters mit HPC Pack in Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Hinzufügen von Azure-"Burst"-Knoten zu einem HPC Pack-Hauptknoten in Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Vergrößern und Verkleinern von Azure-Compute-Ressourcen in einem HPC Pack-Cluster](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

## Integration in Azure Batch 

* [Burst to Azure Batch with Microsoft HPC Pack (in englischer Sprache)](https://technet.microsoft.com/library/mt612877.aspx)

## Erstellen von RDMA-Clustern für MPI-Workloads

* [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-hpcpack-cluster-rdma.md)

* [Lernprogramm: Ausführen von OpenFOAM mit Microsoft HPC Pack auf einem Linux-RDMA-Cluster in Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md)

* [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md)

<!---HONumber=AcomDC_0211_2016-->