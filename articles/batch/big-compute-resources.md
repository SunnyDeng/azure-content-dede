<properties
   pageTitle="Big Compute: Technische Ressourcen für Batch und High Performance Computing (HPC) | Microsoft Azure"
   description="Hier werden die technischen Ressourcen vorgestellt, die Ihnen beim Ausführen Ihrer umfangreichen parallelen Batch- und HPC-Workloads in Azure helfen."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Big Compute in Azure: Technische Ressourcen für Batch und HPC (High Performance Computing)
Dies ist eine Anleitung zu den technischen Ressourcen, die Ihnen beim Ausführen Ihrer umfangreichen parallelen, Batch- und HPC-Workloads in Azure helfen. Erweitern Sie Ihre vorhandenen Batch- oder HPC-Workloads auf die Azure-Cloud, oder erstellen Sie mithilfe einer Reihe von Azure-Diensten neue Big Compute-Lösungen in Azure.

## Lösungsoptionen

Erfahren Sie mehr über Big Compute-Optionen in Azure, und wählen Sie den richtigen Ansatz für Ihren Workload und Ihre Geschäftsanforderungen aus.

* [Batch- und HPC-Lösungen](batch-hpc-solutions.md)

* [Video: Big Compute in der Cloud mit Azure und HPC ](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) ist ein Plattformdienst, mit dem Sie auf einfache Weise Ihre Anwendungen für die Cloud aktivieren und Aufträge ausführen können, ohne einen Cluster und einen Auftragsplaner einrichten und verwalten zu müssen. Verwenden Sie das SDK, um Clientanwendungen in einer Vielzahl von Sprachen mit Azure Batch zu integrieren, Daten in Azure bereitzustellen und Auftragsausführungspipelines zu erstellen.

* [Dokumentation](http://azure.microsoft.com/documentation/services/batch/)

* [API-Referenz](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Tutorial: Erste Schritte mit der Azure Batch-Bibliothek für .NET](batch-dotnet-get-started.md)

* [Batch-Forum](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Batch-Videos](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC-Clusterlösungen

Sie können Ihren vorhandenen Windows- oder Linux-HPC-Cluster zum Ausführen rechenintensiver Workloads in Azure bereitstellen oder erweitern.

### Microsoft HPC Pack

HPC Pack ist die kostenlose HPC-Lösung von Microsoft, die auf Microsoft Azure und Windows Server-Technologien basiert und in der Lage ist, Windows- und Linux HPC-Workloads auszuführen.

* [HPC Pack 2012 R2 Update 3 herunterladen](https://www.microsoft.com/de-DE/download/details.aspx?id=49922)

* [Dokumentation](https://technet.microsoft.com/library/jj899572.aspx)


* [HPC-Clusteroptionen mit Microsoft HPC Pack in Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [Burst to Azure with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (in englischer Sprache)

* [Burst to Azure Batch with Microsoft HPC Pack](https://technet.microsoft.com/library/mt612877.aspx) (in englischer Sprache)


* [Windows-HPC-Foren](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux- und OSS-Clusterlösungen

Mithilfe dieser Azure-Schnellstartvorlagen können Sie Linux-HPC-Cluster mit Open-Source-Tools bereitstellen.

* [Spin up a SLURM cluster](http://azure.microsoft.com/documentation/templates/slurm/) und [Blogbeitrag](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Spin up a Torque cluster](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) ist eine Microsoft-Implementierung des Message Passing Interface-Standards für die Entwicklung und Ausführung paralleler Anwendungen auf der Windows-Plattform.


* [MS-MPI herunterladen](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI-Referenz](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI-Forum](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Rechenintensive Instanzen

Azure bietet zum Ausführen Ihrer Linux- und Windows-HPC-Workloads [eine Reihe unterschiedlicher Größen](../virtual-machines/virtual-machines-size-specs.md) an, einschließlich rechenintensiver [A8-, A9-, A10- und A11-Instanzen](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

* [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Einrichten eines Windows RDMA-Clusters mit Microsoft HPC Pack zum Ausführen von MPI-Anwendungen](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## Architekturpläne

* [Large-Scale Computing – Financial Services](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF in englischer Sprache) zeigt, wie umfangreiche Berechnungen und Datenanalysen in der Cloud für Risikomanagement, Berichterstellung und Simulationen durchgeführt und koordiniert werden.

## Beispiele und Demos

* [Azure Batch code samples (Azure Batch-Codebeispiele, in englischer Sprache)](https://github.com/Azure/azure-batch-samples)

* [Batch Apps Blender sample](https://github.com/Azure/azure-batch-apps-blender) (Batch App-Blender-Beispiel, in englischer Sprache) und [Blogbeitrag zu Blender in Azure Batch](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/) (in englischer Sprache)

## Verwandte Azure-Dienste

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Virtuelle Computer](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Cloud Services](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Media Services](http://azure.microsoft.com/documentation/services/media-services/)



## Nächste Schritte

* Die neuesten Ankündigungen finden Sie im [Blog des HPC- und Batch-Teams von Microsoft](http://blogs.technet.com/b/windowshpc/) und im [Azure-Blog](http://azure.microsoft.com/blog/tag/hpc/).
* Weitere Informationen finden Sie auch unter [Neuigkeiten bei Batch](http://azure.microsoft.com/updates/?service=batch) oder indem Sie den [RSS-Feed](http://azure.microsoft.com/updates/feed/?service=batch) abonnieren.

<!---HONumber=AcomDC_1203_2015-->