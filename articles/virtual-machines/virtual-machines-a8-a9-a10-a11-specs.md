<properties
 pageTitle="Informationen über die Instanzen A8, A9, A10 und A11 | Microsoft Azure"
 description="Sie erhalten Hintergrundinformationen und Überlegungen zur Verwendung der rechenintensiven Azure-Instanzen A8 und A9, A10 und A11."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/17/2015"
 ms.author="danlep"/>

# Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen

Dieser Artikel enthält Hintergrundinformationen und Überlegungen zur Verwendung der Azure-Instanzen A8 und A9, A10 und A11, auch bekannt als *rechenintensive* Instanzen. Die wichtigsten Merkmale dieser Instanzen lauten wie folgt:

* **Hochleistungshardware**: Die Azure-Rechenzentrumshardware, auf der diese Instanzen ausgeführt werden, wurde für rechenintensive Anwendungen mit hoher Netzwerkauslastung entworfen und optimiert, wie z. B. für High Performance Computing(HPC)-Clusteranwendungen, Modellierung und Simulationen.

* **RDMA-Netzwerkverbindung für MPI-Anwendungen**: Wenn die A8- und A9-Instanzen mit den erforderlichen Netzwerktreibern konfiguriert sind, können sie über ein Azure-Netzwerk mit niedriger Latenz und hohem Durchsatz, das auf RDMA-Technologie (Remote Direct Memory Access) basiert, mit anderen A8- und A9-Instanzen kommunizieren. Diese Funktion kann die Leistung von Anwendungen steigern, die unterstützte MPI-Implementierungen (Message Passing Interface) für Linux oder Windows verwenden.

* **Unterstützung für Linux- und Windows-HPC-Cluster**: Stellen Sie auf den Instanzen A8 und A9, A10 und A11 in Azure Clusterverwaltungs- und Auftragsplanungssoftware bereit, um einen eigenständigen HPC-Cluster zu erstellen oder einem lokalen Cluster Kapazität hinzuzufügen. Wie auch andere Azure-VM-Formate unterstützen die Instanzen A8 und A9, A10 und A11 Standard- oder benutzerdefinierte Windows Server- und Linux-Betriebssystemimages oder Ressourcen-Manager-Vorlagen in Azure-VMs (IaaS) oder Azure-Gastbetriebssystemversionen in Clouddiensten (PaaS, nur für Windows Server).

>[AZURE.NOTE]Die A10- und A11-Instanzen verfügen über die gleichen Leistungsoptimierungen und Spezifikationen wie die A8- und A9-Instanzen. Allerdings bieten sie keinen Zugriff auf das RDMA-Netzwerk in Azure. Sie wurden für HPC-Anwendungen entworfen, die keine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern und auch als parametrische oder hochgradig parallele Anwendungen bezeichnet werden. Wenn andere Workloads als MPI-Anwendungen ausgeführt werden, greifen die A8- und A9-Instanzen nicht auf das RDMA-Netzwerk zu, und sie entsprechen funktional den A10- und A11-Instanzen.


## Spezifikationen

### CPU und Arbeitsspeicher

Die rechenintensiven Azure-Instanzen A8 und A9, A10 und A11 verfügen über schnelle Mehrkern-CPUs und große Mengen an Arbeitsspeicher, wie in der folgenden Tabelle dargestellt.

Größe | CPU | Arbeitsspeicher
------------- | ----------- | ----------------
A8 und A10 | Intel® Xeon® E5-2670<br/>8 Kerne mit 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 und A11 | Intel® Xeon® E5-2670<br/>16 Kerne mit 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Zusätzliche Prozessordetails einschließlich der unterstützten Befehlssatzerweiterungen finden Sie auf der Website "Intel.com". Informationen zu Speicherkapazitäten virtueller Computer und Details zu den Datenträgern finden Sie unter [Größen für Virtual Machines](virtual-machines-size-specs.md).

### Netzwerkadapter

Die A8- und A9-Instanzen besitzen zwei Netzwerkadapter, die mit den folgenden zwei Back-End-Azure-Netzwerken verbunden sind.


Netzwerk | Beschreibung
-------- | -----------
10-GBit/s-Ethernet | Verbindung mit Azure-Diensten (z. B. Storage und Virtual Network) und mit dem Internet
32 GBit/s-Back-End, RDMA-fähig | Ermöglicht Anwendungskommunikation mit geringer Latenz und hohem Durchsatz zwischen Instanzen in einem einzelnen Clouddienst oder in einer einzelnen Verfügbarkeitsgruppe. Für MPI-Verkehr reserviert.


>[AZURE.IMPORTANT]Auf virtuellen A8- und A9-Computern, auf denen Linux in IaaS ausgeführt wird, ist der Zugriff auf das RDMA-Netzwerk derzeit nur über Anwendungen möglich, die Azure Linux RDMA und Intel MPI Library 5.0 unter SUSE Linux Enterprise Server 12 verwenden. Auf A8- und A9-Instanzen, auf denen Windows Server in IaaS oder PaaS ausgeführt wird, ist der Zugriff auf das RDMA-Netzwerk derzeit nur über Anwendungen möglich, die die Microsoft Network Direct-Schnittstelle über Microsoft MPI (MS-MPI) verwenden. Informationen zu weiteren Anforderungen finden Sie unter [Zugreifen auf das RDMA-Netzwerk](#access-the-rdma-network) in diesem Artikel.

Die Instanzen A10 und A11 besitzen einen einzelnen 10-GBit/s-Ethernet-Netzwerkadapter, der mit den Azure-Diensten und dem Internet verbunden ist.

## Überlegungen zum Azure-Abonnement

* **Azure-Konto**: Wenn Sie mehr als eine kleine Anzahl von rechenintensiven Instanzen bereitstellen möchten, sollten Sie ein nutzungsbasiertes Abonnement oder andere Kaufoptionen in Erwägung ziehen. Sie können auch Ihr MSDN-Abonnement verwenden. Weitere Informationen finden Sie unter [Azure-Vorteile für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Bei Verwendung einer [Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/) können Sie nur eine begrenzte Anzahl von Azure-Rechenkernen nutzen.

* **Kernnutzungskontingent**: Sie müssen möglicherweise das Kernnutzungskontingent im Azure-Abonnement vom Standardwert von 20 Kernen erhöhen. Diese 20 Kerne sind für viele Szenarios mit Instanzen mit 8 Kernen oder 16 Kernen nicht ausreichend . Für anfängliche Tests sollten Sie eine Erhöhung des Kontingents auf 100 Kerne in Erwägung ziehen. Eröffnen Sie hierzu ein kostenloses Supportticket wie unter [Understanding Azure Limits and Increases](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (in englischer Sprache) dargestellt.

>[AZURE.NOTE]Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantieren. Es werden nur Kerne in Rechnung gestellt, die Sie verwenden.

* **Affinitätsgruppe**: Für die meisten neuen Bereitstellungen werden Affinitätsgruppen derzeit nicht empfohlen. Wenn Sie jedoch eine Affinitätsgruppe verwenden, die Instanzen von einer anderen Größe als A8-A11 enthält, können Sie sie nicht für die A8-A11-Instanzen verwenden und umgekehrt.

* **Virtuelles Netzwerk**: Ein virtuelles Azure-Netzwerk ist nicht erforderlich, um die rechenintensiven Instanzen zu verwenden. Sie benötigen möglicherweise jedoch für viele IaaS-Szenarios mindestens ein cloudbasiertes virtuelles Azure-Netzwerk oder für den Zugriff auf lokale Ressourcen, z. B. einen Anwendungslizenzserver, eine Site-to-Site-Verbindung. Sie müssen ein neues (regionales) virtuelles Netzwerk erstellen, bevor Sie die Instanzen bereitstellen. Das Hinzufügen von A8-, A9-, A10- oder A11-VMs zu einem virtuellen Netzwerk in einer Affinitätsgruppe wird nicht unterstützt. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks](https://msdn.microsoft.com/library/azure/dn631643.aspx) und [Konfigurieren eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung](../vpn-gateway/vpn-gateway-site-to-site-create.md).

* **Clouddienst oder Verfügbarkeitsgruppe**: Zum Herstellen der Verbindung über das RDMA-Netzwerk müssen die A8- und A9-Instanzen in demselben Clouddienst (für IaaS-Szenarien mit virtuellen Linux- oder Windows-Computern in der Azure-Dienstverwaltung, für PaaS-Szenarien mit Windows Server) oder in derselben Verfügbarkeitsgruppe (für virtuelle Linux- oder Windows-Computer im Azure-Ressourcen-Manager) bereitgestellt werden.

## Überlegungen zur Verwendung von HPC Pack

### Überlegungen zu HPC Pack und Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) ist die kostenlose HPC-Cluster- und Auftragsverwaltungslösung von Microsoft für Windows. HPC Pack unterstützt ab HPC Pack 2012 R2 Update 2 das Ausführen von mehreren Linux-Distributionen auf Compute-Knoten, die auf virtuellen Azure-Computern bereitgestellt wurden und von einem Windows Server-Hauptknoten verwaltet werden. Mit der neuesten Version von HPC Pack können Sie einen Linux-basierten Cluster bereitstellen, der MPI-Anwendungen mit Zugriff auf das RDMA-Netzwerk in Azure ausführen kann. Weitere Informationen finden Sie in der [Dokumentation zu HPC Pack](http://go.microsoft.com/fwlink/?LinkId=617894).

### Überlegungen zu HPC Pack und Windows

HPC Pack ist nicht für die Verwendung der Instanzen A8 und A9, A10 und A11 mit Windows Server erforderlich, wird jedoch als Tool zum Erstellen von Windows-HPC-Clustern in Azure empfohlen. Im Fall von A8- und A9-Instanzen ist HPC Pack die effizienteste Methode zum Ausführen von Windows-MPI-Anwendungen, die auf das RDMA-Netzwerk in Azure zugreifen. HPC Pack enthält eine Laufzeitumgebung für die Microsoft-Implementierung des Message Passing Interface für Windows.

Weitere Informationen und Prüflisten zum Bereitstellen und Verwenden der rechenintensiven Instanzen in IaaS- und PaaS-Szenarien mit HPC Pack unter Windows Server finden Sie unter [Rechenintensive A8- und A9-Instanzen: Schnellstart mit HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Zugreifen auf das RDMA-Netzwerk

### Zugriff von virtuellen Linux A8- und A9-Computern

In einem einzelnen Clouddienst oder einer einzelnen Verfügbarkeitsgruppe können die A8- und A9-Instanzen auf das RDMA-Netzwerk in Azure zugreifen, wenn MPI-Anwendungen ausgeführt werden, die für die Kommunikation zwischen Instanzen die Linux-RDMA-Treiber verwenden. Azure Linux RDMA wird derzeit nur mit [Intel MPI Library 5.0](https://software.intel.com/de-de/intel-mpi-library/) unterstützt.

>[AZURE.NOTE]Azure Linux RDMA-Treiber können derzeit nicht über Treibererweiterungen installiert werden. Sie sind nur mithilfe des RDMA-fähigen SLES 12-Images aus dem Azure Marketplace verfügbar.

In der folgenden Tabelle sind die Voraussetzungen für den Zugriff von Linux-MPI-Anwendungen auf das RDMA-Netzwerk in Clustern von Compute-Knoten (IaaS) aufgeführt. Bereitstellungsoptionen und Konfigurationsschritte finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md).

Voraussetzung | Virtuelle Computer (IaaS)
------------ | -------------
Betriebssystem | SUSE Linux Enterprise Server 12 (SLES 12)-HPC-Image aus dem Azure Marketplace
MPI | Intel MPI Library 5.0

### Zugriff von Windows-A8- und A9-Instanzen

In einem einzelnen Clouddienst oder einer einzelnen Verfügbarkeitsgruppe können die A8- und A9-Instanzen auf das RDMA-Netzwerk in Azure zugreifen, wenn MPI-Anwendungen ausgeführt werden, die die Microsoft Network Direct-Schnittstelle zur Kommunikation zwischen Instanzen verwenden. Die Instanzen A10 und A11 bieten keinen Zugriff auf das RDMA-Netzwerk.

Weitere Informationen zu den Voraussetzungen für MPI-Anwendungen zum Zugreifen auf das RDMA-Netzwerk in virtuellen Computerbereitstellungen (IaaS) und Clouddienst-Bereitstellungen (PaaS) der Instanzen A8 oder A9 finden Sie in der folgenden Tabelle. Typische Bereitstellungsszenarios finden Sie unter [Rechenintensive A8- und A9-Instanzen: Schnellstart mit HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).


Voraussetzung | Virtuelle Computer (IaaS) | Clouddienste (PaaS)
---------- | ------------ | -------------
Betriebssystem | Windows Server 2012 R2- oder Windows Server 2012-VMs | Gastbetriebssystemfamilie Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2
MPI | MS-MPI 2012 R2 oder höher, entweder eigenständig oder installiert über HPC Pack 2012 R2 oder höher<br/><br/>Intel MPI Library 5.0 | MS-MPI 2012 R2 oder höher, installiert über HPC Pack 2012 R2 oder höher<br/><br/>Intel MPI Library 5.0


>[AZURE.NOTE]Für IaaS-Szenarios muss den virtuellen Computern die [HpcVmDrivers-Erweiterung](https://msdn.microsoft.com/library/azure/dn690126.aspx) hinzugefügt werden, sodass die Windows-Treiber für RDMA-Verbindungen installiert werden.


## Weitere wichtige Informationen

* Die VM-Größen A8-A11 sind nur bei der Preisstufe "Standard" verfügbar.

* Eine vorhandene Azure-VM kann nicht in die Größen A8, A9, A10 oder A11 geändert werden.

* Die Instanzen A8, A9, A10 und A11 können derzeit nicht mit einem Clouddienst bereitgestellt werden, der Teil einer vorhandenen Affinitätsgruppe ist. Ebenso kann eine Affinitätsgruppe mit einem Clouddienst mit den Instanzen A8 und A9, A10 und A11 nicht für Bereitstellungen von Instanzen anderer Größen verwendet werden. Beim Versuch einer solchen Bereitstellung wird eine der folgenden ähnliche Fehlermeldung angezeigt: `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`

* Das RDMA-Netzwerk in Azure reserviert den Addressbereich 172.16.0.0/12. Wenn Sie beabsichtigen, MPI-Anwendungen auf A8- und A9-Instanzen auszuführen, die in einem virtuellen Azure-Netzwerk bereitgestellt wurden, stellen Sie sicher, dass der Adressraum des virtuellen Netzwerks sich nicht mit dem RDMA-Netzwerk überschneidet.

## Nächste Schritte

* Ausführliche Informationen zur Verfügbarkeit und Preisgestaltung der Instanzen A8 und A9, A10 und A11 finden Sie unter [Virtuelle Computer Preise](http://azure.microsoft.com/pricing/details/virtual-machines/) und [Cloud Services Preise](http://azure.microsoft.com/pricing/details/cloud-services/).
* Informationen zum Bereitstellen und Konfigurieren eines Linux-Clusters mit A8- und A9-Instanzen für den Zugriff auf das Azure RDMA-Netzwerk finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md).
* Informationen zu ersten Schritten bei der Bereitstellung und Verwendung von A8- und A9-Instanzen mit HPC Pack unter Windows finden Sie unter [Rechenintensive A8- und A9-Instanzen: Schnellstart mit HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) und [Ausführen von MPI-Anwendungen auf A8- und A9-Instanzen](https://msdn.microsoft.com/library/azure/dn592104.aspx).

<!---HONumber=July15_HO4-->