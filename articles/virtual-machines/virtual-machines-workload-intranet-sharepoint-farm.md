<properties
	pageTitle="SharePoint Server 2013-Farm in Azure | Microsoft Azure"
	description="Entdecken Sie den Wert einer SharePoint Server 2013-Farm in Azure, richten Sie eine Testumgebung ein, und stellen Sie eine hochverfügbare Konfiguration bereit."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# Azure-Infrastrukturdienste-Workload: SharePoint-Intranetfarm

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen von Ressourcen mit dem klassischen Bereitstellungsmodell.

Richten Sie Ihre erste bzw. nächste SharePoint-Farm in Microsoft Azure ein. Dadurch erhalten Sie eine einfache Konfiguration und die Möglichkeit, die Farm schnell mit neuen Kapazitäten zu erweitern oder die wichtigsten Funktionen zu optimieren. Viele SharePoint-Farmen wachsen von einer standardmäßigen hochverfügbaren Konfiguration mit drei Ebenen zu einer Farm an, die unter Umständen über ein Dutzend oder mehr Server mit Optimierung für Performance oder separate Rollen verfügt, z. B. Distributed Caching oder Search.

Mit den Funktionen für virtuelle Computer und virtuelle Netzwerke der Azure-Infrastrukturdienste können Sie eine SharePoint-Farm, die transparent mit Ihrem lokalen Netzwerk verbunden ist, schnell bereitstellen und ausführen. Beispielsweise können Sie das folgende Netzwerk einrichten:

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Da Azure Virtual Network eine Erweiterung Ihres lokalen Netzwerks mit den richtigen Benennungen und dem richtigen Routing des Datenverkehrs ist, greifen Ihre Benutzer genauso darauf zu, als ob es Teil des lokalen Rechenzentrums wäre.

Dank dieser Konfiguration können Sie die SharePoint-Farm mühelos erweitern, indem Sie neue virtuelle Azure-Computer hinzufügen, für die die laufenden Kosten sowohl für Hardware als auch für die Wartung geringer als bei einer Ausführung einer vergleichbaren Farm in Ihrem Rechenzentrum sind.

Das Hosten einer SharePoint-Intranetfarm in Azure-Infrastrukturdiensten ist ein Beispiel für eine Branchenanwendung. Eine Übersicht finden Sie unter [Blaupause zur Architektur von Branchenanwendungen](http://msdn.microsoft.com/dn630664).

Im nächste Schritt richten Sie eine SharePoint-Intranetfarm für die Entwicklung bzw. zum Testen ein, die unter Azure gehostet wird.

> [AZURE.NOTE]Microsoft hat die SharePoint Server 2016 IT Preview veröffentlicht. Damit Sie diese Vorschau einfach installieren und testen können, verwenden Sie ein Azure Virtual Machine-Katalogimage, auf dem SharePoint Server 2016 IT Preview und alle erforderlichen Komponenten vorinstalliert sind. Weitere Informationen finden Sie unter [Test the SharePoint Server 2016 IT Preview in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/) (in englischer Sprache).

## Erstellen einer unter Azure gehosteten SharePoint-Intranetfarm für die Entwicklung bzw. zum Testen

Sie haben zwei Möglichkeiten, wie Sie eine Entwicklungs- bzw. Testumgebung für eine SharePoint-Farm unter Azure erstellen können:

- Virtuelles Nur-Cloud-Netzwerk
- Standortübergreifendes virtuelles Netzwerk

Sie können diese Entwicklungs- und Testumgebungen im Rahmen Ihres [MSDN-Abonnements](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) oder eines [Azure-Testabonnements](http://azure.microsoft.com/pricing/free-trial/) kostenlos erstellen.

### Virtuelles Nur-Cloud-Netzwerk

Ein virtuelles Nur-Cloud-Netzwerk ist nicht mit einem lokalen Netzwerk verbunden. Falls Sie nur schnell eine grundlegende oder hochverfügbare SharePoint-Farm erstellen möchten, helfen Ihnen die Informationen unter [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md) weiter. Das folgende Beispiel zeigt die grundlegende Konfiguration für eine SharePoint-Farm.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/SPFarm_Basic.png)

### Standortübergreifendes virtuelles Netzwerk

Ein standortübergreifendes virtuelles Netzwerk ist mit einem lokalen Netzwerk per Standort-zu-Standort-VPN oder ExpressRoute-Verbindung verbunden. Wenn Sie eine Entwicklungs- oder Testumgebung erstellen möchten, die der endgültigen Konfiguration ähnelt, und mit dem Zugriff auf den SharePoint-Server und der Remoteverwaltung über eine VPN-Verbindung experimentieren möchten, helfen Ihnen die Informationen unter [Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) weiter.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

Der nächste Schritt ist die Erstellung einer SharePoint-Intranetfarm mit hoher Verfügbarkeit in Azure.

## Bereitstellen einer in Azure gehosteten SharePoint-Intranetfarm

Die grundlegende, repräsentative Konfiguration für eine funktionelle und hochverfügbare SharePoint-Intranetfarm wird im folgenden Beispiel gezeigt.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Sie besteht aus folgenden Elementen:

- SharePoint-Intranetfarm mit zwei Servern auf Web-, Anwendungs- und Datenbankebene
- Eine Konfiguration mit SQL Server AlwaysOn-Verfügbarkeitsgruppen und zwei SQL-Servern sowie einem Hauptknotencomputer in einem Cluster
- Azure Active Directory im virtuellen Netzwerk mit zwei Replikatdomänencontrollern

Diese Konfiguration finden Sie als Infografik unter [SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

### Stückliste

Diese Basiskonfiguration erfordert den folgenden Satz von Azure-Diensten und -Komponenten:

- Neun virtuelle Computer
- Vier zusätzliche Datenträger für die Domänencontroller und SQL-Server
- Drei Clouddienste
- Vier Verfügbarkeitsgruppen
- Ein standortübergreifendes virtuelles Netzwerk
- Ein Speicherkonto
- Ein Azure-Abonnement

### Phasen der Bereitstellung

Verwenden Sie zum Bereitstellen dieser Konfiguration das folgende Verfahren:

- Phase 1: Konfigurieren von Azure

	Verwenden Sie das Azure-Portal und Azure PowerShell, um ein Speicherkonto, Clouddienste und ein standortübergreifendes virtuelles Netzwerk zu erstellen. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Phase 2: Konfigurieren der Domänencontroller

	Konfigurieren Sie zwei Azure Active Directory-Replikatdomänencontroller und DNS-Einstellungen für das virtuelle Netzwerk. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Phase 3: Konfigurieren der SQL Server-Infrastruktur

	Bereiten Sie die virtuellen SQL Server-Computer für die Verwendung mit SharePoint vor, und erstellen Sie den SQL Server-Cluster. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Phase 4: Konfigurieren der SharePoint-Server

	Konfigurieren Sie die vier virtuellen SharePoint-Computer für eine neue SharePoint-Farm. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Phase 5: Erstellen einer AlwaysOn-Verfügbarkeitsgruppe

	Bereiten Sie die SharePoint-Datenbanken vor, erstellen Sie eine AlwaysOn-Verfügbarkeitsgruppe, und fügen Sie dann die SharePoint-Datenbanken hinzu. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Nach Abschluss der Konfiguration können Sie diese SharePoint-Farm gemäß den Informationen unter [Microsoft Azure-Architekturen für SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx) erweitern.

## Zusätzliche Ressourcen

[Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-deploy-spsqlao-overview.md)

[Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: Branchenanwendung mit hoher Verfügbarkeit](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Sept15_HO3-->