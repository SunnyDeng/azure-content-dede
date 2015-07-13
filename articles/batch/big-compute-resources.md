<properties
   pageTitle="Big Compute in Azure: technische Ressourcen für Batch und HPC (High Performance Computing)"
   description="In diesem Artikel werden die technischen Ressourcen vorgestellt, die Ihnen beim Ausführen Ihrer umfangreichen parallelen, Batch- und HPC-Workloads in Azure helfen."
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
   ms.date="06/24/2015"
   ms.author="danlep"/>

# Big Compute in Azure: technische Ressourcen für Batch und HPC (High Performance Computing)
Dies ist eine Anleitung zu den technischen Ressourcen, die Ihnen beim Ausführen Ihrer umfangreichen parallelen, Batch- und HPC-Workloads in Azure helfen. Erweitern Sie Ihre vorhandenen Batch- oder HPC-Workloads auf die Azure-Cloud, oder erstellen Sie mithilfe einer Reihe von Azure-Diensten neue Big Compute-Lösungen in Azure.

## Lösungsoptionen

Erfahren Sie mehr über Big Compute-Optionen in Azure, und wählen Sie den richtigen Ansatz für Ihre Workload und Ihre Geschäftserfordernisse aus.

* [Übersicht](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [Video: Big Compute in the Cloud with Azure and HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/) (Big Compute in der Cloud mit Azure und HPC, in englischer Sprache)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) ist ein Plattformdienst, mit dem Sie auf einfache Weise Ihre Anwendungen für die Cloud aktivieren und Aufträge ausführen können, ohne einen Cluster und einen Auftragsplaner einrichten und verwalten zu müssen. Verwenden Sie das SDK, um Clientanwendungen in einer Vielzahl von Sprachen mit Azure Batch zu integrieren, Daten in Azure bereitzustellen und Auftragsausführungspipelines zu erstellen.

* Anmelden für die [Vorschau](http://azure.microsoft.com/services/preview/)

* [Dokumentation](http://azure.microsoft.com/documentation/services/batch/)

* [API-Referenz](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Batch-Forum](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Lernprogramm: Erste Schritte mit der Azure Batch-Bibliothek für .NET](batch-dotnet-get-started.md)

* [Batch-Videos](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC-Clusterlösungen

Sie können Ihren vorhandenen Windows- oder Linux-HPC-Cluster zum Ausführen rechenintensiver Workloads in Azure bereitstellen oder erweitern.

### Microsoft HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029) ist die kostenlose Cluster-Manager- und Auftragsplanungslösung von Microsoft für lokales, hybrides und cloudbasiertes HPC.

* [Burst to Azure with HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (Erweiterung auf Azure mit HPC Pack, in englischer Sprache)

* [HPC Pack in Azure Virtual Machines](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Lernprogramm: Einrichten eines Hybridclusters mit HPC Pack in Azure](../cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Windows-HPC-Foren](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux-Clusterlösungen
Verwenden Sie die folgenden Azure-Ressourcen-Manager-Vorlagen zum Bereitstellen von Linux-HPC-Clustern.

* [Spin up a SLURM cluster](http://azure.microsoft.com/documentation/templates/slurm/) und [Blogbeitrag](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Spin up a Torque cluster](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) ist eine Microsoft-Implementierung des Message Passing Interface-Standards für die Entwicklung und Ausführung paralleler Anwendungen auf der Windows-Plattform.


* [MS-MPI herunterladen](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI Reference](https://msdn.microsoft.com/library/dn473458.aspx) (in englischer Sprache)

* [MPI-Forum](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Rechenintensive Instanzen

Azure bietet zum Ausführen Ihrer Batch- und HPC-Workloads [eine Reihe unterschiedlicher Größen](../virtual-machines/virtual-machines-size-specs.md) an, einschließlich rechenintensiver [A8-, A9-, A10- und A11-Instanzen](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

* [A8- und A9-Instanzen: Schnellstart mit HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Ausführen von MPI-Anwendungen auf A8- und A9-Instanzen](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Architekturpläne

* [Large-Scale Computing – Financial Services](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF in englischer Sprache) zeigt, wie umfangreiche Berechnungen und Datenanalysen in der Cloud für Risikomanagement, Berichterstellung und Simulationen operationalisiert und orchestriert werden.

## Beispiele und Demos

* [Azure Batch code samples](https://github.com/Azure/azure-batch-samples) (Azure Batch-Codebeispiele, in englischer Sprache)

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

<!---HONumber=62-->