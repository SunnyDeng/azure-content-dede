<properties
 pageTitle="Linux-HPC Pack-Clusteroptionen in der Cloud | Microsoft Azure"
 description="Informationen zu Optionen mit Microsoft HPC Pack, um einen Linux-HPC-Cluster (High Performance Computing) in der Azure-Cloud zu erstellen und zu verwalten"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Optionen zum Erstellen und Verwalten eines Linux-HPC-Clusters (High Performance Computing) in Azure mit Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

## Ausführen eines HPC Pack-Clusters in virtuellen Azure-Computern

### Azure-Vorlagen


* (Marketplace) [HPC Pack cluster for Linux workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) (HPC Pack-Cluster für Linux-Workloads)

* (Schnellstart) [Create an HPC cluster with Linux compute nodes](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) (Erstellen eines HPC-Clusters mit Linux-Computeknoten)


### Azure VM-Images

* [HPC Pack unter Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)



### PowerShell-Bereitstellungsskript

* [Erstellen eines HPC-Clusters mit dem HPC Pack IaaS-Bereitstellungsskript](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Lernprogramme

* [Tutorial: Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Tutorial: Ausführen von NAMD mit dem Microsoft HPC Pack auf Linux-Computeknoten in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Lernprogramm: Ausführen von OpenFOAM mit Microsoft HPC Pack auf einem Linux-RDMA-Cluster in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)



### Clusterverwaltung

* [Übermitteln von Aufträgen an einen HPC Pack-Cluster in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)



## Erstellen von RDMA-Clustern für MPI-Workloads

* [Lernprogramm: Ausführen von OpenFOAM mit Microsoft HPC Pack auf einem Linux-RDMA-Cluster in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0323_2016-->