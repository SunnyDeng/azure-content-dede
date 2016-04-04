<properties
 pageTitle="Informationen zu A8-, A9-, A10- und A11-Instanzen mit Windows | Microsoft Azure"
 description="Hier finden Sie Hintergrundinformationen und Überlegungen zur Verwendung der rechenintensiven Azure-Instanzgrößen A8, A9, A10 und A11 für virtuelle Windows-Computer und Clouddienste."
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Informationen zur Verwendung der rechenintensiven A8-, A9-, A10- und A11-Instanzen mit Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Zugreifen auf das RDMA-Netzwerk

In einem einzelnen Clouddienst oder einer einzelnen Verfügbarkeitsgruppe können die A8- und A9-Instanzen auf das RDMA-Netzwerk in Azure zugreifen, um Windows-MPI-Anwendungen auszuführen, die die Microsoft Network Direct-Schnittstelle zur Kommunikation zwischen Instanzen verwenden.

Weitere Informationen zu den Voraussetzungen für MPI-Anwendungen zum Zugreifen auf das RDMA-Netzwerk in virtuellen Computerbereitstellungen (IaaS) und Clouddienst-Bereitstellungen (PaaS) der Instanzen A8 oder A9 finden Sie in der folgenden Tabelle. Informationen zu typischen Bereitstellungsszenarios finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).


Voraussetzung | Virtuelle Computer (IaaS) | Clouddienste (PaaS)
---------- | ------------ | -------------
Betriebssystem | Windows Server 2012 R2 oder Windows Server 2012 | Gastbetriebssystemfamilie Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2
MPI | MS-MPI 2012 R2 oder höher, entweder eigenständig oder installiert über HPC Pack 2012 R2 oder höher<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 oder höher, installiert über HPC Pack 2012 R2 oder höher<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Für IaaS-Szenarios muss den virtuellen Computern die HpcVmDrivers-Erweiterung hinzugefügt werden, sodass die für RDMA-Verbindungen benötigten Windows-Netzwerkgerätetreiber installiert werden. Je nach Bereitstellungsmethode wird die HpcVmDriversDrivers-Erweiterung zu einem virtuellen Computer der Größe A8 oder A9 automatisch hinzugefügt, oder Sie fügen sie selbst hinzu. Informationen zum Hinzufügen der Erweiterung finden Sie unter [Verwalten von Erweiterungen für virtuelle Computer](virtual-machines-windows-classic-manage-extensions.md).

## Überlegungen zu HPC Pack und Windows

HPC Pack ist zwar für die Verwendung der A8-, A9-, A10- und A11-Instanzen mit Windows Server nicht erforderlich, wird jedoch als Tool zum Ausführen Windows-basierter MPI-Anwendungen empfohlen, die auf das RDMA-Netzwerk in Azure zugreifen. HPC Pack enthält eine Laufzeitumgebung für die Microsoft-Implementierung des Message Passing Interface (MS MPI) für Windows.

Weitere Informationen und Prüflisten zur Verwendung rechenintensiver Instanzen mit HPC Pack auf Windows Server finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

## Nächste Schritte

* Ausführliche Informationen zur Verfügbarkeit und Preisgestaltung der Instanzen A8 und A9, A10 und A11 finden Sie unter [Virtuelle Computer Preise](https://azure.microsoft.com/pricing/details/virtual-machines/) sowie unter [Cloud Services Preise](https://azure.microsoft.com/pricing/details/cloud-services/).

* Informationen zu ersten Schritten für die Bereitstellung und Verwendung von A8- und A9-Instanzen mit HPC Pack unter Windows finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Informationen zur Verwendung von A8- und A9-Instanzen zum Ausführen von MPI-Anwendungen mit Azure Batch finden Sie unter [Verwendung von Tasks mit mehreren Instanzen zum Ausführen von MPI-Anwendungen (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0323_2016-->