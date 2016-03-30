 <properties
   pageTitle="Azure und Linux | Microsoft Azure"
   description="Beschreibt die Azure Compute-, Speicher- und Netzwerkdienste in Bezug auf virtuelle Linux-Computer."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/01/2016"
   ms.author="rclaus"/>

# Azure und Linux
Microsoft Azure umfasst eine wachsende Sammlung von integrierten Public Cloud-Diensten, z.B. Analyse, Virtual Machines, Datenbanken, mobile Geräte, Netzwerke, Speicher und Web, und ist somit ideal für das Hosten Ihrer Lösungen geeignet. Microsoft Azure stellt eine skalierbare Computingplattform bereit, bei der Sie nur für die tatsächliche gewünschte Nutzung bezahlen – ohne dass Sie in lokale Hardware investieren müssen. Azure ist darauf ausgelegt, dass Sie Ihre Lösungen wie gewünscht vertikal und horizontal auf den Stand hochskalieren, der für die Erfüllung der Anforderungen Ihrer Kunden erforderlich ist.
 
## Azure Virtual Machines
Mit Azure Virtual Machines können Sie sehr flexibel eine Vielzahl unterschiedlicher Computinglösungen bereitstellen. Stellen Sie einen virtuellen Windows- oder Linux-Computer aus dem Imagekatalog oder einen benutzerdefinierten virtuellen Computer von einem unserer vielen Partnerunternehmen bereit. Sie können nahezu alle Workloads in nahezu allen Sprachen auf fast allen Betriebssystemen bereitstellen. Werden Ihre Anforderungen hiermit immer noch nicht erfüllt? Keine Sorge. Sie können auch eigene Images von Ihrem lokalen Standort verwenden.
 
## Erste Schritte mit Linux unter Microsoft Azure

Verwenden Sie Microsoft Azure Virtual Machines, Azure Storage und das Azure-Netzwerk zusammen, um „Infrastructure-as-a-Service“ auf Internetebene für Ihre Linux-Computinganforderungen bereitzustellen. Sie benötigen Folgendes, um mit der Nutzung von Linux unter Azure zu beginnen:

1. Ein kostenloses Testkonto. [Hier erhalten Sie ein Konto](https://azure.microsoft.com/pricing/free-trial/).
2. Die Azure-Befehlszeilenschnittstelle (Azure CLI) für Linux, Mac und Windows. [Diese können Sie hier installieren](../xplat-cli-install.md).
3. Eine Linux-VM. [Erstellen Sie diese hier](virtual-machines-linux-quick-create-cli.md).
4. Weitere Informationen zu Linux und Azure, z.B. zur Qualifikation für die Vereinbarung zum Servicelevel (SLA). **Lesen Sie sich dieses Dokument auch dann durch, wenn Sie keine rechtlichen Dokumente mögen**.

## Logistik: Regionen, Distributionen, Verfügbarkeit, VM-Größen und -Kontingente
### Regionen
Microsoft Azure-Ressourcen sind auf mehrere geografische Regionen weltweit verteilt. Eine „Region“ umfasst mehrere Rechenzentren in einem bestimmten geografischen Bereich. Ab 1. Januar 2016 gilt folgender Stand: 8 in Amerika, 2 in Europa, 6 in der Asien-Pazifik-Region, 2 in China (Festland) und 3 in Indien. Falls Sie eine vollständige Liste mit allen Azure-Regionen benötigen, können Sie **[hier](https://azure.microsoft.com/regions/)** die Liste mit den vorhandenen und neu angekündigten Regionen anzeigen.

### Distributionen
Microsoft Azure unterstützt die Ausführung einer Reihe von beliebten Linux-Distributionen, die von verschiedenen Partnerunternehmen bereitgestellt und gepflegt werden. Auf dem Azure Marketplace finden Sie Distributionen wie CentOS, Debian, Red Hat Enterprise, Ubuntu, FreeBSD usw. Wir arbeiten aktiv mit verschiedenen Linux-Communitys zusammen, um der Liste mit unterstützten Distributionen noch mehr Einträge hinzuzufügen. **[Aktuelle Distributionen](virtual-machines-linux-endorsed-distros.md)** Falls Ihre bevorzugte Linux-Distribution derzeit nicht im Katalog enthalten ist, können Sie den Ansatz „Bring your own Linux VM“ verfolgen. Halten Sie sich hierzu an die Richtlinien **[auf dieser Seite](virtual-machines-linux-create-upload-generic.md)**.

## Verfügbarkeit und Microsoft Azure-SLA
Damit Ihre Bereitstellung die Qualifikation für unsere VM-Vereinbarung zum Servicelevel von 99,95% erreicht, müssen Sie mindestens zwei VMs bereitstellen, die Ihre Workload innerhalb einer Verfügbarkeitsgruppe ausführen. So wird sichergestellt, dass Ihre VMs auf mehrere Fehlerdomänen in unseren Rechenzentren verteilt und auf Hosts mit unterschiedlichen Wartungsfenstern bereitgestellt werden. Alle Details zu unserer Vereinbarung zum Servicelevel (SLA) können Sie **[hier online anzeigen](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)**.

## VM-Größen und -Kontingente
Wenn Sie einen virtuellen Computer in Azure bereitstellen, wählen Sie eine unserer verfügbaren VM-Größen aus, die für Ihre Workload geeignet ist. Die Größe beeinflusst auch die Verarbeitungsleistung, den Arbeitsspeicher und die Speicherkapazität des virtuellen Computers. Die Kosten werden Ihnen basierend auf der Ausführungsdauer der VM und der Nutzung der zugeordneten Ressourcen berechnet. Eine vollständigere Liste finden Sie im folgenden Artikel zu den [Größen von virtuellen Computern](virtual-machines-linux-sizes.md).

Hier sind einige grundlegende Richtlinien zur Auswahl einer VM-Größe aus einer unserer Serien (A, D, DS, G und GS) aufgeführt.

* VMs der A-Serie sind unsere kostengünstigen Einsteiger-VMs für kleine Workloads und Entwicklungs- und Testszenarien. Sie sind in allen Regionen verfügbar und ermöglichen die Verbindung mit und Nutzung aller Standardressourcen für virtuelle Computer.
* Bei den Größen der A-Serie (A8 - A11) handelt es sich um spezielle rechenintensive Konfigurationen, die für Computing-Clusteranwendungen mit hoher Leistung geeignet sind.
* VMs der D-Serie dienen zum Ausführen von Anwendungen, die eine höhere Rechenleistung und eine höhere temporäre Datenträgerleistung erfordern. VMs der D-Serie bieten schnellere Prozessoren, ein höheres Verhältnis von Speicher zu Kern und ein SSD (Solid State Drive) für den temporären Datenträger. 
* Die Dv2-Serie ist die neuste Version unserer D-Serie und verfügt über eine leistungsfähigere CPU. Die CPU der Dv2-Serie ist ca. 35 % schneller als die CPU der D-Serie. Sie basiert auf der neuesten Generation des 2,4-GHz-Intel Xeon ® E5-2673 v3-Prozessors (Haswell) und kann mit der Intel Turbo Boost Technology 2.0 bis auf 3,2 GHz gebracht werden. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.
* Virtuelle Computer der G-Serie bieten den meisten Arbeitsspeicher und werden auf Hosts mit Prozessoren der Intel Xeon E5 V3-Familie ausgeführt.

Hinweis: Für VMs der DS-Serie und GS-Serie besteht Zugriff auf Storage Premium, unsere Speicherlösung mit SSD-Unterstützung, hoher Leistung und geringer Latenz für Workloads mit hoher E/A-Intensität. Premium-Speicher ist in bestimmten Regionen verfügbar. Nähere Informationen finden Sie unter **[Storage Premium: Hochleistungsspeicher für Azure Virtual Machine-Workloads](../storage/storage-premium-storage-preview-portal.md)**.

Jedes Azure-Abonnement verfügt über standardmäßige Kontingentgrenzen, durch die die Bereitstellung einer großen Anzahl von VMs für Ihr Projekt beeinträchtigt werden kann. Der derzeitige Grenzwert pro Abonnement liegt bei 20 VMs pro Region. Sie können diese Kontingentgrenzen erhöhen lassen, indem Sie ein Supportticket erstellen und die Erhöhung anfordern. Weitere Informationen zu Kontingentgrenzen finden Sie unter **[Diensteinschränkungen des Azure-Abonnements](../azure-subscription-service-limits.md)**.

## Nächste Schritte

Ein kostenloses Testkonto. **[Hier erhalten Sie ein Konto.](https://azure.microsoft.com/pricing/free-trial/)** Falls Sie bereits über ein Konto verfügen, können Sie es ausprobieren, indem Sie die **[Azure-Befehlszeilenschnittstelle installieren](../xplat-cli-install.md)**. Anschließend können Sie dann die [Linux-VM erstellen](virtual-machines-linux-quick-create-cli.md).

<!---HONumber=AcomDC_0323_2016-->