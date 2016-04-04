<properties
 pageTitle="Informationen zu A8-, A9-, A10- und A11-Instanzen und Linux | Microsoft Azure"
 description="Hier finden Sie Hintergrundinformationen und Überlegungen zur Verwendung der rechenintensiven Azure-Instanzgrößen A8, A9, A10 und A11 für virtuelle Linux-Computer."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Informationen zur Verwendung der rechenintensiven A8-, A9-, A10- und A11-Instanzen mit Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Zugreifen auf das RDMA-Netzwerk

In einem einzelnen Clouddienst oder einer einzelnen Verfügbarkeitsgruppe können die A8- und A9-Instanzen auf das RDMA-Netzwerk in Azure zugreifen, um Linux-MPI-Anwendungen auszuführen. Azure Linux RDMA wird derzeit nur mit [Intel MPI Library 5](https://software.intel.com/de-DE/intel-mpi-library/) unterstützt.

>[AZURE.NOTE] Azure Linux RDMA-Treiber können derzeit nicht über Treibererweiterungen installiert werden. Sie sind nur bei Verwendung des RDMA-fähigen SLES 12-Image aus dem Azure Marketplace verfügbar.

In der folgenden Tabelle werden die Voraussetzungen für den Zugriff von Linux-MPI-Anwendungen auf das RDMA-Netzwerk in Clustern von Compute-Knoten (IaaS) zusammengefasst. Bereitstellungsoptionen und Konfigurationsschritte finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-classic-rdma-cluster.md).

Voraussetzung | Virtuelle Computer (IaaS)
------------ | -------------
Betriebssystem | SLES 12-HPC-Image aus dem Azure Marketplace
MPI | Intel MPI Library 5

## Überlegungen zu HPC Pack und Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) ist die kostenlose HPC-Cluster- und Auftragsverwaltungslösung von Microsoft für Windows. Die neuesten Versionen von HPC Pack 2012 R2 unterstützen das Ausführen von mehreren Linux-Distributionen auf Computeknoten, die auf virtuellen Azure-Computern bereitgestellt wurden und von einem Windows Server-Hauptknoten verwaltet werden. Auf virtuellen A8- oder A9-Computern bereitgestellte Linux-Computeknoten, auf denen eine unterstützte MPI-Implementierung ausgeführt wird, sind für MPI-Anwendungen geeignet, die auf das RDMA-Netzwerk zugreifen. Eine Einführung finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Nächste Schritte

* Ausführliche Informationen zu Verfügbarkeit und Preisen von A8-, A9-, A10- und A11-Instanzen finden Sie unter [Virtual Machines – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/).

* Informationen zu ersten Schritten bei der Bereitstellung und Verwendung von A8- und A9-Instanzen mit RDMA unter Linux finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0323_2016-->