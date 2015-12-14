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

Diese automatisch erstellte Umgebung besteht aus drei Servern in einem virtuellen Cloud-Netzwerk in Azure: einem Domänencontroller, einem SQL-Server und dem SharePoint-Server.

Weitere Informationen finden Sie unter [SharePoint 2013 nicht hoch verfügbare Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) im Azure Marketplace des Azure-Portals. Damit wird eine grundlegende Test-/Entwicklungsfarm für eine SharePoint-Website mit Internetverbindung erstellt. Weitere Informationen finden Sie unter [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md).

Sie können zudem eine Azure-Ressourcen-Manager-Vorlage verwenden. Weitere Informationen finden Sie unter [Bereitstellen einer SharePoint-Farm mit drei Servern](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

> [AZURE.NOTE]Das Element **SharePoint-Serverfarm** wurde aus dem Azure Marketplace des Azure-Portals entfernt.

## Hochverfügbare SharePoint-Test-/Entwicklungsfarm

Die automatisch erstellte Umgebung besteht aus neun Servern in einem virtuellen Cloud-Netzwerk in Azure: zwei Server für Domänencontroller, drei Server für einen SQL Server-Cluster, zwei SharePoint-Server auf Anwendungsebene und zwei SharePoint-Server auf Webebene.

Weitere Informationen finden Sie unter [SharePoint 2013 hoch verfügbare Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) im Azure Marketplace des Azure-Portals. Damit wird eine hoch verfügbare Entwicklungs-/Testfarm für eine SharePoint-Website mit Internetverbindung erstellt. Weitere Informationen finden Sie unter [Erstellen von SharePoint-Serverfarmen](virtual-machines-sharepoint-farm-azure-preview.md).

Sie können zudem eine Azure-Ressourcen-Manager-Vorlage verwenden. Weitere Informationen finden Sie unter [Bereitstellen einer SharePoint-Farm mit neun Servern](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

> [AZURE.NOTE]Das Element **SharePoint-Serverfarm** wurde aus dem Azure Marketplace des Azure-Portals entfernt.

## Hybrid-Cloud-Entwicklungs-/Testfarm

Mit der [SharePoint-Intranetfarm in einer Hybrid-Cloud-Entwicklungs-/Testumgebung](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) erstellen Sie eine simulierte Hybrid-Cloud-Konfiguration, in der eine einfache, zweischichtige SharePoint-Farm gehostet wird. Sie können diese dazu verwenden, eine Intranet-SharePoint-Serverfarm zu testen, die in Azure an Ihrem Speicherort im Internet gehostet wird.

Diese Konfiguration verwendet das klassische Bereitstellungsmodell.

## Intranet-SharePoint-Produktionsfarm mit hoher Verfügbarkeit

Mit der Bereitstellung von [SharePoint 2013 mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md) erstellen Sie eine einsatzbereite Intranet-SharePoint Server 2013-Farm mit hoher Verfügbarkeit in Azure.

Diese Konfiguration verwendet das klassische Bereitstellungsmodell.

## Zusätzliche Ressourcen

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Internetwebsites in Microsoft Azure mit SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

[SharePoint Server 2013 – Notfallwiederherstellung in Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

[Verwenden von Microsoft Azure Active Directory für die SharePoint 2013-Authentifizierung](https://technet.microsoft.com/library/dn635311.aspx)

[Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=AcomDC_1203_2015-->