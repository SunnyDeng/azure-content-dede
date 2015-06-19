<properties 
	pageTitle="In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen" 
	description="Erfahren Sie, wie Sie eine Entwicklungs-/Test- oder Produktions-SharePoint 2013-Farm in Azure-Infrastrukturdiensten einrichten." 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="josephd"/>

# In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen

Richten Sie Ihre erste oder nächste Entwicklungs-/Test- oder Produktions-SharePoint-Farm in Microsoft Azure-Infrastrukturdiensten ein. Dadurch erhalten Sie eine einfache Konfiguration und die Möglichkeit, die Farm schnell mit neuen Kapazitäten zu erweitern oder die wichtigsten Funktionen zu optimieren.

## Grundlegende SharePoint-Farm für Entwicklung/Tests 

Sie können die [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md)-Vorlage aus dem Azure-Vorschauportal verwenden, um eine grundlegende Test-/Entwicklungsfarm für eine SharePoint-Website mit Internetanbindung zu erstellen.

Die automatisch erstellte Umgebung besteht aus drei Servern für einen Domänencontroller, einer SQL Server-Instanz und dem SharePoint-Server in einem ausschließlich virtuellen Azure-Cloud-Netzwerk.

## Hochverfügbare SharePoint-Entwicklungs-/Testfarm

Sie können außerdem die [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md)-Vorlage aus dem Azure-Vorschauportal verwenden, um eine Test-/Entwicklungsfarm mit hoher Verfügbarkeit für eine SharePoint-Website mit Internetanbindung zu erstellen.

Die automatisch erstellte Umgebung besteht aus neun Servern in einem ausschließlich virtuellen Azure-Cloud-Netzwerk: zwei für Domänencontroller, drei für ein SQL Server-Cluster, zwei SharePoint-Server auf Anwendungsebene und zwei SharePoint-Server auf Webebene.

## Hybrid-Cloud-Entwicklungs-/Testfarm

Mit der [SharePoint-Intranetfarm in einer Hybrid-Cloud-Entwicklungs-/Testumgebung](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) erstellen Sie eine simulierte Hybrid-Cloud-Konfiguration, in der eine einfache, zweischichtige SharePoint-Farm gehostet wird. Sie können diese dazu verwenden, eine Intranet-SharePoint-Serverfarm zu testen, die in Azure an Ihrem Speicherort im Internet gehostet wird.

## Hochverfügbare Intranet-SharePoint-Produktionsfarm

Unter [Bereitstellen von SharePoint 2013 mit SQL Server-AlwaysOn-Verfügbarkeitsgruppen in Azure](https://msdn.microsoft.com/library/dn275959.aspx) erstellen Sie eine einsatzbereite, hochverfügbare Intranet-SharePoint Server 2013-Farm in Azure.

## Zusätzliche Ressourcen

[SharePoint Server in Azure-Infrastrukturdiensten](https://msdn.microsoft.com/library/dn275955.aspx)

[Planen von SharePoint 2013 für Azure-Infrastrukturdienste](https://msdn.microsoft.com/library/dn275958.aspx)

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=52-->
 