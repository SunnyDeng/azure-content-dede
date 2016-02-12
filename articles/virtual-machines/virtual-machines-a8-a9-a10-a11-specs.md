<properties
 pageTitle="Informationen über die Instanzen A8, A9, A10 und A11 | Microsoft Azure"
 description="Hier finden Sie Hintergrundinformationen und Überlegungen zur Verwendung der rechenintensiven Azure-Instanzen A8 und A9, A10 und A11 für virtuelle Computer und Clouddienste."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Dieser Artikel enthält Hintergrundinformationen und Überlegungen zur Verwendung der Azure-Instanzen A8 und A9, A10 und A11, auch bekannt als *rechenintensive* Instanzen. Die wichtigsten Merkmale dieser Instanzen lauten wie folgt:

* **Hochleistungshardware**: Die Azure-Rechenzentrumshardware, auf der diese Instanzen ausgeführt werden, wurde für rechenintensive Anwendungen mit hoher Netzwerkauslastung konzipiert und optimiert. Hierzu zählen beispielsweise High Performance Computing (HPC)-Clusteranwendungen, Modellierung und Simulationen.

* **RDMA-Netzwerkverbindung für MPI-Anwendungen**: Sie können die A8- und A9-Instanzen so einrichten, dass sie über ein Azure-Netzwerk mit niedriger Latenz und hohem Durchsatz, das auf RDMA-Technologie (Remote Direct Memory Access) basiert, mit anderen A8- und A9-Instanzen kommunizieren können. Dieses Feature kann die Leistung von bestimmten Linux- und Windows-MPI-Anwendungen (Message Passing Interface) steigern.

* **Unterstützung für Linux- und Windows-HPC-Cluster**: Stellen Sie auf den Instanzen A8 und A9, A10 und A11 in Azure Clusterverwaltungs- und Auftragsplanungssoftware bereit, um einen eigenständigen HPC-Cluster zu erstellen oder einem lokalen Cluster Kapazität hinzuzufügen.

>[AZURE.NOTE]Die A10- und A11-Instanzen verfügen über die gleichen Leistungsoptimierungen und Spezifikationen wie die A8- und A9-Instanzen. Allerdings bieten sie keinen Zugriff auf das RDMA-Netzwerk in Azure. Sie wurden für HPC-Anwendungen entworfen, die keine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern und auch als parametrische oder hochgradig parallele Anwendungen bezeichnet werden.


## Spezifikationen

### CPU und Arbeitsspeicher

Die rechenintensiven Azure-Instanzen A8 und A9, A10 und A11 verfügen über schnelle Mehrkern-CPUs und große Mengen an Arbeitsspeicher, wie in der folgenden Tabelle dargestellt.

Größe | CPU | Arbeitsspeicher
------------- | ----------- | ----------------
A8 und A10 | Intel Xeon E5-2670<br/>8 Kerne, 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 und A11 | Intel Xeon E5-2670<br/>16 Kerne, 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Weitere Prozessordetails sowie die unterstützten Befehlssatzerweiterungen finden Sie auf der Website „Intel.com“. Informationen zu Speicherkapazitäten virtueller Computer und Details zu den Datenträgern finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).

### Netzwerkadapter

Die A8- und A9-Instanzen besitzen zwei Netzwerkadapter, die mit den beiden folgenden Azure-Back-End-Netzwerken verbunden sind:


Netzwerk | Beschreibung
-------- | -----------
Ethernet mit 10 GBit/s | Verbindung mit Azure-Diensten (z. B. Azure Storage und Azure Virtual Network) und mit dem Internet
Back-End mit 32 GBit/s, RDMA-fähig | Ermöglicht Anwendungskommunikation mit geringer Latenz und hohem Durchsatz zwischen Instanzen in einem einzelnen Clouddienst oder in einer einzelnen Verfügbarkeitsgruppe Für MPI-Verkehr reserviert.


>[AZURE.IMPORTANT]Informationen über zusätzliche Anforderungen bei Linux- und Windows-MPI-Anwendungen für den Zugriff auf das RDMA-Netzwerk finden Sie in diesem Artikel unter [Zugreifen auf das RDMA-Netzwerk](#access-the-rdma-network).

A10- und A11-Instanzen besitzen einen einzelnen 10-GBit/s-Ethernet-Netzwerkadapter, der mit den Azure-Diensten und dem Internet verbunden ist.

## Überlegungen zur Bereitstellung

* **Azure-Konto**: Wenn Sie eine größere Anzahl von rechenintensiven Instanzen bereitstellen möchten, sollten Sie ein nutzungsbasiertes Abonnement oder andere Kaufoptionen in Erwägung ziehen. Sie können auch Ihr MSDN-Abonnement verwenden. Weitere Informationen finden Sie unter [Azure-Vorteile für MSDN-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Bei Verwendung einer [kostenlosen Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/) können Sie nur eine begrenzte Anzahl von Azure-Compute-Kernen nutzen.

* **Kernkontingent**: Unter Umständen müssen Sie das Kontingent an CPU-Kernen in Ihrem Azure-Abonnement erhöhen. Standardmäßig stehen 20 Kerne pro Abonnement (im klassischen Bereitstellungsmodell) oder 20 Kerne pro Region (im Azure-Ressourcen-Manager-Bereitstellungsmodell) zur Verfügung. Zum Anfordern einer Kontingenterhöhung erstellen Sie ein kostenloses Supportticket, wie in [Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (Grundlegendes zu Einschränkungen und Steigerungen in Azure) beschrieben.

    >[AZURE.NOTE]Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantien. Nur verwendete Kerne werden auch in Rechnung gestellt.

* **Virtuelles Netzwerk**: Ein [virtuelles Azure-Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) ist nicht erforderlich, um die rechenintensiven Instanzen zu verwenden. Sie benötigen möglicherweise jedoch für viele IaaS-Szenarios mindestens ein cloudbasiertes virtuelles Azure-Netzwerk oder für den Zugriff auf lokale Ressourcen, z. B. einen Anwendungslizenzserver, eine Site-to-Site-Verbindung. Sie müssen ein neues virtuelles Netzwerk erstellen, um die Instanzen bereitzustellen. Das Hinzufügen von A8-, A9-, A10- oder A11-VMs zu einem virtuellen Netzwerk in einer Affinitätsgruppe wird nicht unterstützt.

* **Clouddienst oder Verfügbarkeitsgruppe**: Um eine Verbindung über das RDMA-Netzwerk herzustellen, müssen Sie die A8- und A9-Instanzen im gleichen Clouddienst (falls Sie das klassische Bereitstellungsmodell nutzen) oder in der gleichen Verfügbarkeitsgruppe (falls Sie das Azure-Ressourcen-Manager-Bereitstellungsmodell nutzen) bereitgestellt werden.

## Überlegungen zur Verwendung von HPC Pack

### Überlegungen zu HPC Pack und Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) ist die kostenlose HPC-Cluster- und Auftragsverwaltungslösung von Microsoft für Windows. Die neuesten Versionen von HPC Pack 2012 R2 unterstützen das Ausführen von mehreren Linux-Distributionen auf Computeknoten, die auf virtuellen Azure-Computern bereitgestellt wurden und von einem Windows Server-Hauptknoten verwaltet werden. Weitere Informationen finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md).

### Überlegungen zu HPC Pack und Windows

HPC Pack ist nicht für die Verwendung der Instanzen A8 und A9, A10 und A11 mit Windows Server erforderlich, wird jedoch als Tool zum Erstellen von Windows HPC Server-basierten Clustern in Azure empfohlen. Im Fall von A8- und A9-Instanzen ist HPC Pack die effizienteste Methode zum Ausführen Windows-basierter MPI-Anwendungen, die auf das RDMA-Netzwerk in Azure zugreifen. HPC Pack enthält eine Laufzeitumgebung für die Microsoft-Implementierung des Message Passing Interface (MS MPI) für Windows.

Weitere Informationen und Prüflisten zur Verwendung rechenintensiver Instanzen mit HPC Pack auf Windows Server finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-hpcpack-cluster-rdma.md).

## Zugreifen auf das RDMA-Netzwerk

### Zugriff von virtuellen Linux A8- und A9-Computern

In einem einzelnen Clouddienst oder einer einzelnen Verfügbarkeitsgruppe können die A8- und A9-Instanzen auf das RDMA-Netzwerk in Azure zugreifen, um Linux-MPI-Anwendungen auszuführen. Azure Linux RDMA wird derzeit nur mit der [Intel MPI Library 5](https://software.intel.com/de-DE/intel-mpi-library/) unterstützt.

>[AZURE.NOTE] Azure Linux RDMA-Treiber können derzeit nicht über Treibererweiterungen installiert werden. Sie sind nur bei Verwendung des RDMA-fähigen SLES 12-Image aus dem Azure Marketplace verfügbar.

In der folgenden Tabelle werden die Voraussetzungen für den Zugriff von Linux-MPI-Anwendungen auf das RDMA-Netzwerk in Clustern von Compute-Knoten (IaaS) zusammengefasst. Bereitstellungsoptionen und Konfigurationsschritte finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md).

Voraussetzung | Virtuelle Computer (IaaS)
------------ | -------------
Betriebssystem | SLES 12-HPC-Image aus dem Azure Marketplace
MPI | Intel MPI Library 5

### Zugriff von Windows-A8- und A9-Instanzen

In einem einzelnen Clouddienst oder einer einzelnen Verfügbarkeitsgruppe können die A8- und A9-Instanzen auf das RDMA-Netzwerk in Azure zugreifen, um Windows-MPI-Anwendungen auszuführen, die die Microsoft Network Direct-Schnittstelle zur Kommunikation zwischen Instanzen verwenden.

Weitere Informationen zu den Voraussetzungen für MPI-Anwendungen zum Zugreifen auf das RDMA-Netzwerk in virtuellen Computerbereitstellungen (IaaS) und Clouddienst-Bereitstellungen (PaaS) der Instanzen A8 oder A9 finden Sie in der folgenden Tabelle. Informationen zu typischen Bereitstellungsszenarios finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-hpcpack-cluster-rdma.md).


Voraussetzung | Virtuelle Computer (IaaS) | Clouddienste (PaaS)
---------- | ------------ | -------------
Betriebssystem | Windows Server 2012 R2 oder Windows Server 2012 | Gastbetriebssystemfamilie Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2
MPI | MS-MPI 2012 R2 oder höher, entweder eigenständig oder installiert über HPC Pack 2012 R2 oder höher<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 oder höher, installiert über HPC Pack 2012 R2 oder höher<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Für IaaS-Szenarios muss den virtuellen Computern die HpcVmDrivers-Erweiterung hinzugefügt werden, sodass die für RDMA-Verbindungen benötigten Windows-Netzwerkgerätetreiber installiert werden. Je nach Bereitstellungsmethode wird die HpcVmDriversDrivers-Erweiterung zu einem virtuellen Computer der Größe A8 oder A9 automatisch hinzugefügt, oder Sie fügen sie selbst hinzu. Informationen zum Hinzufügen der Erweiterung finden Sie unter [Verwalten von Erweiterungen für virtuelle Computer](virtual-machines-extensions-install.md).


## Weitere wichtige Informationen

* **Preisgestaltung** - Die VM-Größen A8 bis A11 sind nur im Tarif „Standard“ verfügbar.

* **Größenänderung** – Sie können einer rechenintensiven Instanz (A8-A11) nur die Größe A8 bis A11 zuweisen, und Sie können eine rechenintensive Instanz nicht in eine nicht rechenintensive Instanz ändern. Dies liegt an der speziellen Hardware und den Leistungsoptimierungen, die für rechenintensive Instanzen typisch sind.

* **RDMA-Netzwerkadressbereich** - Das RDMA-Netzwerk in Azure reserviert sich den Adressbereich 172.16.0.0/12. Wenn Sie beabsichtigen, MPI-Anwendungen auf A8- und A9-Instanzen in einem virtuellen Azure-Netzwerk auszuführen, stellen Sie sicher, dass der Adressbereich des virtuellen Netzwerks sich nicht mit dem RDMA-Netzwerk überschneidet.

## Nächste Schritte

* Ausführliche Informationen zur Verfügbarkeit und Preisgestaltung der Instanzen A8 und A9, A10 und A11 finden Sie unter [Virtuelle Computer Preise](https://azure.microsoft.com/pricing/details/virtual-machines/) sowie unter [Cloud Services Preise](https://azure.microsoft.com/pricing/details/cloud-services/).
* Informationen zum Bereitstellen und Konfigurieren eines Linux-basierten Clusters mit A8- und A9-Instanzen für den Zugriff auf das Azure-RDMA-Netzwerk finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md).
* Informationen zu ersten Schritten für die Bereitstellung und Verwendung von A8- und A9-Instanzen mit HPC Pack unter Windows finden Sie unter [Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen](virtual-machines-windows-hpcpack-cluster-rdma.md).

<!---HONumber=AcomDC_0204_2016-->