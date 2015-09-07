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
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen

Richten Sie Ihre erste oder nächste Entwicklungs-/Test- oder Produktions-SharePoint-Farm in Microsoft Azure-Infrastrukturdiensten ein. Dadurch erhalten Sie eine einfache Konfiguration und die Möglichkeit, die Farm schnell mit neuen Kapazitäten zu erweitern oder die wichtigsten Funktionen zu optimieren.

## Grundlegende SharePoint-Farm für Entwicklung/Tests

Bei virtuellen Computern, die in der Dienstverwaltung erstellt wurden, können Sie die [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md)-Vorlage aus dem Azure-Vorschauportal verwenden, um eine grundlegende Test-/Entwicklungsfarm für eine SharePoint-Website mit Internetanbindung zu erstellen.

Die automatisch erstellte Umgebung besteht aus drei Servern für einen Domänencontroller, einer SQL Server-Instanz und dem SharePoint-Server in einem ausschließlich virtuellen Azure-Cloud-Netzwerk.

Verwenden Sie eine Vorlage, um eine ähnliche Konfiguration mit virtuellen Computern zu erstellen, die im Ressourcen-Manager erstellt wurden. Weitere Informationen finden Sie unter [Bereitstellen einer SharePoint-Farm mit drei Servern](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

## Hochverfügbare SharePoint-Test-/Entwicklungsfarm

Bei virtuellen Computern, die in der Dienstverwaltung erstellt wurden, können Sie außerdem die [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md)-Funktion aus dem Azure-Vorschauportal verwenden, um eine SharePoint-Test-/Entwicklungsfarm mit hoher Verfügbarkeit für eine SharePoint-Website mit Internetanbindung zu erstellen.

Die automatisch erstellte Umgebung besteht aus neun Servern in einem ausschließlich virtuellen Azure-Cloud-Netzwerk: zwei für Domänencontroller, drei für ein SQL Server-Cluster, zwei SharePoint-Server auf Anwendungsebene und zwei SharePoint-Server auf Webebene.

Verwenden Sie eine Vorlage, um eine ähnliche Konfiguration mit virtuellen Computern zu erstellen, die im Ressourcen-Manager erstellt wurden. Weitere Informationen finden Sie unter [Bereitstellen einer SharePoint-Farm mit neun Servern](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

## Hybrid-Cloud-Entwicklungs-/Testfarm

Mit der [SharePoint-Intranetfarm in einer Hybrid-Cloud-Entwicklungs-/Testumgebung](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) erstellen Sie eine simulierte Hybrid-Cloud-Konfiguration, in der eine einfache, zweischichtige SharePoint-Farm gehostet wird. Sie können diese dazu verwenden, eine Intranet-SharePoint-Serverfarm zu testen, die in Azure an Ihrem Speicherort im Internet gehostet wird.

Bei dieser Konfiguration werden virtuelle Computer verwendet, die in der Dienstverwaltung erstellt wurden.

## Intranet-SharePoint-Produktionsfarm mit hoher Verfügbarkeit

Mit der Bereitstellung von [SharePoint 2013 mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md) erstellen Sie eine einsatzbereite Intranet-SharePoint Server 2013-Farm mit hoher Verfügbarkeit in Azure.

Bei dieser Konfiguration werden virtuelle Computer verwendet, die in der Dienstverwaltung erstellt wurden.

## Zusätzliche Ressourcen

Zusätzliche Informationen und Konfigurationen zu SharePoint in Azure finden Sie unter diesen Ressourcen:

- [Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

- [Internetwebsites in Microsoft Azure mit SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

- [SharePoint Server 2013 – Notfallwiederherstellung in Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

- [Verwenden von Microsoft Azure Active Directory für die SharePoint 2013-Authentifizierung](https://technet.microsoft.com/library/dn635311.aspx)

- [Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=August15_HO9-->