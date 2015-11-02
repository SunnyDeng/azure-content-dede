<properties
	pageTitle="SharePoint Server 2013-Farmen in Azure | Microsoft Azure"
	description="In den hier genannten Artikeln erfahren Sie, wie Sie eine Entwicklungs-/Testumgebung oder eine SharePoint Server 2013-Produktionsfarm in Microsoft Azure einrichten."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Richten Sie Ihre erste oder nächste Entwicklungs-/Test- oder Produktions-SharePoint-Farm in Microsoft Azure-Infrastrukturdiensten ein. Dadurch erhalten Sie eine einfache Konfiguration und die Möglichkeit, die Farm schnell mit neuen Kapazitäten zu erweitern oder die wichtigsten Funktionen zu optimieren.

> [AZURE.NOTE]Microsoft hat die SharePoint Server 2016 IT Preview veröffentlicht. Damit Sie diese Vorschau einfach installieren und testen können, verwenden Sie ein Azure Virtual Machine-Katalogimage, auf dem SharePoint Server 2016 IT Preview und alle erforderlichen Komponenten vorinstalliert sind. Weitere Informationen finden Sie unter [Test the SharePoint Server 2016 IT Preview in Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/) (in englischer Sprache).

## Grundlegende SharePoint-Farm für Entwicklung/Tests

Für virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden, können Sie das Element [SharePoint 2013 non-HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) im Azure Marketplace des Azure-Vorschauportals verwenden. Damit wird eine grundlegende Test-/Entwicklungsfarm für eine SharePoint-Website mit Internetverbindung erstellt.

Sie können zudem eine Azure-Ressourcen-Manager-Vorlage verwenden. Weitere Informationen finden Sie unter [Bereitstellen einer SharePoint-Farm mit drei Servern](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

Die automatisch erstellte Umgebung besteht aus drei Servern für einen Domänencontroller, einer SQL Server-Instanz und dem SharePoint-Server in einem ausschließlich virtuellen Azure-Cloud-Netzwerk.

Um eine ähnliche Konfiguration mit dem klassischen Bereitstellungsmodell zu erstellen, verwenden Sie das Element [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md) im Azure Marketplace des Azure-Vorschauportals.


## Hochverfügbare SharePoint-Test-/Entwicklungsfarm

Für virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden, können Sie das Element [SharePoint 2013 HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) im Azure Marketplace des Azure-Vorschauportals verwenden. Damit wird eine hochverfügbare Test-/Entwicklungsfarm für eine SharePoint-Website mit Internetverbindung erstellt.

Sie können zudem eine Azure-Ressourcen-Manager-Vorlage verwenden. Weitere Informationen finden Sie unter [Bereitstellen einer SharePoint-Farm mit neun Servern](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

Die automatisch erstellte Umgebung besteht aus neun Servern in einem ausschließlich virtuellen Azure-Cloud-Netzwerk: zwei für Domänencontroller, drei für einen SQL Server-Cluster, zwei SharePoint-Server auf Anwendungsebene und zwei SharePoint-Server auf Webebene.

Um eine ähnliche Konfiguration mit dem klassischen Bereitstellungsmodell zu erstellen, verwenden Sie das Element [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md) im Azure Marketplace des Azure-Vorschauportals.


## Hybrid-Cloud-Entwicklungs-/Testfarm

Mit der [SharePoint-Intranetfarm in einer Hybrid-Cloud-Entwicklungs-/Testumgebung](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) erstellen Sie eine simulierte Hybrid-Cloud-Konfiguration, in der eine einfache, zweischichtige SharePoint-Farm gehostet wird. Sie können diese dazu verwenden, eine Intranet-SharePoint-Serverfarm zu testen, die in Azure an Ihrem Speicherort im Internet gehostet wird.

Diese Konfiguration verwendet klassische virtuelle Computer.

## Intranet-SharePoint-Produktionsfarm mit hoher Verfügbarkeit

Mit der Bereitstellung von [SharePoint 2013 mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md) erstellen Sie eine einsatzbereite Intranet-SharePoint Server 2013-Farm mit hoher Verfügbarkeit in Azure.

Diese Konfiguration verwendet klassische virtuelle Computer.

## Zusätzliche Ressourcen

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Internetwebsites in Microsoft Azure mit SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

[SharePoint Server 2013 – Notfallwiederherstellung in Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

[Verwenden von Microsoft Azure Active Directory für die SharePoint 2013-Authentifizierung](https://technet.microsoft.com/library/dn635311.aspx)

[Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=Oct15_HO4-->