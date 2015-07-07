<properties
 pageTitle="Informationen über die Instanzen A8, A9, A10 und A11 | Microsoft Azure"
 description="Dieser Artikel enthält Hintergrundinformationen und Überlegungen zur Verwendung der rechenintensiven Azure-Instanzen A8 und A9, A10 und A11."
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
 ms.date="06/09/2015"
 ms.author="danlep"/>

# Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen

Dieser Artikel enthält Hintergrundinformationen und Überlegungen zur Verwendung der Azure-Instanzen A8 und A9, A10 und A11, auch bekannt als *rechenintensive* Instanzen. Wichtige Features, die diese Instanzen unterscheiden:

* **Hochleistungshardware**: Die Azure-Rechenzentrumshardware, auf der diese Instanzen ausgeführt werden, wurde für rechenintensive Anwendungen mit hoher Netzwerkauslastung entworfen und optimiert, wie z. B. für High Performance Computing(HPC)-Clusteranwendungen, Modellierung und Simulationen.

* **RDMA-Netzwerkverbindung**: Die A8- und A9-Instanzen können über ein Azure-Netzwerk mit niedriger Latenz und hohem Durchsatz kommunizieren, das auf RDMA-Technologie (Remote Direct Memory Access) basiert. Dieses Feature kann die Leistung für parallele MPI-Anwendungen (Message Passing Interface) steigern. (RDMA-Netzwerkzugriff wird derzeit nur für Clouddienste und virtuelle Computer auf Basis von Windows Server unterstützt.)

>[AZURE.NOTE]Die A10- und A11-Instanzen verfügen über die gleichen Leistungsoptimierungen und Spezifikationen wie die A8- und A9-Instanzen. Allerdings bieten sie keinen Zugriff auf das RDMA-Netzwerk in Azure. Sie wurden für HPC-Anwendungen entworfen, die keine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern und auch als parametrische oder hochgradig parallele Anwendungen bezeichnet werden.

Wie auch [andere Azure-VM-Formate](virtual-machines-size-specs.md) unterstützen die Instanzen A8 und A9, A10 und A11 Standard- oder benutzerdefinierte Windows Server- und Linux-Betriebssystemimages in Azure-VMs (IaaS) oder Azure-Gastbetriebssystemversionen in Clouddiensten (PaaS).

## Spezifikationen

### CPU und Arbeitsspeicher

Die rechenintensiven Azure-Instanzen A8 und A9, A10 und A11 verfügen über schnelle Mehrkern-CPUs und große Mengen an Arbeitsspeicher, wie in der folgenden Tabelle dargestellt.

Größe | CPU | Arbeitsspeicher
------------- | ----------- | ----------------
A8 und A10 | Intel® Xeon® E5-2670<br/>8 Kerne mit 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 und A11 | Intel® Xeon® E5-2670<br/>16 Kerne mit 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Zusätzliche Prozessordetails einschließlich der unterstützten Befehlssatzerweiterungen finden Sie auf der Website "Intel.com".


### Netzwerkadapter

Die A8- und A9-Instanzen besitzen zwei Netzwerkadapter, die mit den folgenden zwei Back-End-Azure-Netzwerken verbunden sind.


Netzwerk | Beschreibung
-------- | -----------
10-GBit/s-Ethernet | Verbindung mit Azure-Diensten (z. B. Storage und Virtual Network) und mit dem Internet
32 GBit/s-Back-End, RDMA-fähig | Ermöglicht Anwendungskommunikation mit geringer Latenz und hohem Durchsatz zwischen Instanzen in einem einzelnen Clouddienst


>[AZURE.IMPORTANT]Der Zugriff auf das RDMA-Netzwerk ist derzeit nur für Anwendungen aktiviert, die die Microsoft Network Direct-Schnittstelle verwenden. Siehe auch [Zugreifen auf das RDMA-Netzwerk](#access-the-RDMA-network) in diesem Artikel.


Die Instanzen A10 und A11 besitzen einen einzelnen 10-GBit/s-Ethernet-Netzwerkadapter, der mit den Azure-Diensten und dem Internet verbunden ist.

## Überlegungen zum Azure-Abonnement

* **Azure-Konto**: Wenn Sie mehr als eine kleine Anzahl von rechenintensiven Instanzen bereitstellen möchten, sollten Sie ein nutzungsbasiertes Abonnement oder andere Kaufoptionen in Erwägung ziehen. Sie können auch Ihr MSDN-Abonnement verwenden. Weitere Informationen finden Sie unter [Azure-Vorteile für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Bei Verwendung einer [Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/) können Sie nur eine begrenzte Anzahl von Azure-Rechenkernen nutzen.

* **Kernnutzungskontingent**: Sie müssen möglicherweise das Kernnutzungskontingent im Azure-Abonnement vom Standardwert von 20 Kernen erhöhen. Diese 20 Kerne sind für viele Szenarios mit Instanzen mit 8 Kernen oder 16 Kernen nicht ausreichend . Für anfängliche Tests sollten Sie eine Erhöhung des Kontingents auf 100 Kerne in Erwägung ziehen. Eröffnen Sie hierzu ein kostenloses Supportticket wie unter [Understanding Azure Limits and Increases](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (in englischer Sprache) dargestellt.

>[AZURE.NOTE]Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantieren. Es werden nur Kerne in Rechnung gestellt, die Sie verwenden.

* **Affinitätsgruppe**: Eine Azure-Affinitätsgruppe kann die Leistung durch die Gruppierung von Diensten oder virtuellen Computern im gleichen Azure-Rechenzentrum optimieren . Um die rechenintensiven Instanzen zu gruppieren, sollten Sie neue Affinitätsgruppen in der Region erstellen, in der die Instanzen bereitgestellt werden sollen. Dabei hat sich die Methode bewährt, nur Affinitätsgruppen für die rechenintensiven Instanzen zu verwenden, nicht für Instanzen anderer Größe.

* **Virtuelles Netzwerk**: Ein virtuelles Azure-Netzwerk ist nicht erforderlich, um die rechenintensiven Instanzen zu verwenden. Sie benötigen möglicherweise jedoch mindestens ein cloudbasiertes virtuelles Azure-Netzwerk für viele IaaS-Szenarios oder eine Site-to-Site-Verbindung für den Zugriff auf lokale Ressourcen. Sie müssen ein neues (regionales) virtuelles Netzwerk erstellen, bevor Sie die Instanzen bereitstellen. Das Hinzufügen von A8-, A9-, A10- oder A11-VMs zu einem virtuellen Netzwerk in einer Affinitätsgruppe wird nicht unterstützt. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks](https://msdn.microsoft.com/library/azure/dn631643.aspx) und [Konfigurieren eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung](../vpn-gateway/vpn-gateway-site-to-site-create.md).

## Überlegungen zur Verwendung von HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029) ist die kostenlose Verwaltungslösung von Microsoft für HPC-Cluster und -Aufträge. Sie ist zur Verwendung der Instanzen A8 und A9, A10 und A11 nicht erforderlich, wird jedoch als Tool zum Erstellen von Windows-HPC-Clustern in Azure empfohlen. Im Fall von A8- und A9-Instanzen ist HPC Pack die effizienteste Methode zum Ausführen von Windows-MPI-Anwendungen, die auf das RDMA-Netzwerk in Azure zugreifen. HPC Pack enthält eine Laufzeitumgebung für die Microsoft-Implementierung des Message Passing Interface (MS MPI) für Windows.

Weitere Informationen und Prüflisten zum Verwenden der rechenintensiven Instanzen mit HPC Pack finden Sie unter [Rechenintensive A8- und A9-Instanzen: Schnellstart mit HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Zugreifen auf das RDMA-Netzwerk

In einem einzelnen Clouddienst können die Instanzen A8 und A9 auf das RDMA-Netzwerk in Azure zugreifen, wenn MPI-Anwendungen ausgeführt werden, die die Microsoft Network Direct-Schnittstelle zur Kommunikation zwischen Instanzen verwenden. Zum jetzigen Zeitpunkt wird Network Direct nur von MS-MPI von Microsoft für Windows unterstützt. Die Instanzen A10 und A11 bieten keinen Zugriff auf das RDMA-Netzwerk.

>[AZURE.NOTE]Wie andere Azure-Instanzen können A8 und A9-Instanzen mithilfe der verfügbaren CPU-Kerne, des Arbeitsspeichers und des Festplattenspeicherplatzes andere Workloads als MPI-Anwendungen ausführen. In diesen Fällen stellen die Instanzen keine Verbindung mit dem RDMA-Netzwerk her und sind funktional äquivalent zu den Instanzen A10 und A11.


Weitere Informationen zu den Voraussetzungen für MPI-Anwendungen zum Zugreifen auf das RDMA-Netzwerk in virtuellen Computerbereitstellungen (IaaS) und Clouddienst-Bereitstellungen (PaaS) der Instanzen A8 oder A9 finden Sie in der folgenden Tabelle. Typische Bereitstellungsszenarios finden Sie unter [Rechenintensive A8- und A9-Instanzen: Schnellstart mit HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).


Voraussetzung | Virtuelle Computer (IaaS) | Clouddienste (PaaS)
---------- | ------------ | -------------
Betriebssystem | Windows Server 2012 R2- oder Windows Server 2012-VMs | Windows Server 2012 R2- oder Windows Server 2008 R2-Gastbetriebssystemfamilie
MPI | MS-MPI 2012 R2 oder höher, entweder eigenständig oder installiert über HPC Pack 2012 R2 oder höher | MS-MPI 2012 R2 oder höher, installiert über HPC Pack 2012 R2 oder höher


>[AZURE.NOTE]Für IaaS-Szenarios muss den virtuellen Computern die [HpcVmDrivers-Erweiterung](https://msdn.microsoft.com/library/azure/dn690126.aspx) hinzugefügt werden, sodass die Windows-Treiber für RDMA-Verbindungen installiert werden.


## Weitere wichtige Informationen

* Eine vorhandene Azure-VM kann nicht in die Größen A8, A9, A10 oder A11 geändert werden.

* Die Instanzen A8, A9, A10 und A11 können derzeit nicht mit einem Clouddienst bereitgestellt werden, der Teil einer vorhandenen Affinitätsgruppe ist. Ebenso kann eine Affinitätsgruppe mit einem Clouddienst mit den Instanzen A8 und A9, A10 und A11 nicht für Bereitstellungen von Instanzen anderer Größen verwendet werden. Beim Versuch einer solchen Bereitstellung wird eine der folgenden ähnliche Fehlermeldung angezeigt: `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`


## Nächste Schritte

* Ausführliche Informationen zur Verfügbarkeit und Preisgestaltung der Instanzen A8 und A9, A10 und A11 finden Sie unter [Virtuelle Computer Preise](http://azure.microsoft.com/pricing/details/virtual-machines/) und [Cloud Services Preise](http://azure.microsoft.com/pricing/details/cloud-services/).
* Informationen zu ersten Schritten bei der Bereitstellung und Verwendung von A8- und A9-Instanzen mit HPC Pack finden Sie unter [Rechenintensive A8- und A9-Instanzen: Schnellstart mit HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) und [Ausführen von MPI-Anwendungen in den rechenintensiven A8- und A9-Instanzen](https://msdn.microsoft.com/library/azure/dn592104.aspx).
 

<!---HONumber=62-->