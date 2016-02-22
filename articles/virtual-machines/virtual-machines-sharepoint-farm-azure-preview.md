<properties
	pageTitle="Erstellen von SharePoint-Serverfarmen | Microsoft Azure"
	description="Erstellen Sie im Marketplace des Azure-Portals schnell eine neue grundlegende oder hoch verfügbare SharePoint Server 2013-Farm."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="josephd"/>

# Erstellen von SharePoint-Serverfarmen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]klassisches Modell.

Mit dem Marketplace im Microsoft Azure-Portal können Sie schnell eine vorkonfigurierte SharePoint Server 2013-Farm erstellen. Damit können Sie viel Zeit sparen, wenn Sie eine grundlegende oder hochverfügbare SharePoint-Farm für eine Test- oder Entwicklungsumgebung benötigen oder wenn Sie SharePoint Server 2013 als Zusammenarbeitslösung für Ihre Organisation bewerten möchten.

> [AZURE.NOTE] Das Element **SharePoint-Serverfarm** wurde aus dem Azure Marketplace des Azure-Portals entfernt. Es wurde durch die Elemente **SharePoint 2013 nicht hoch verfügbare Farm** und **SharePoint 2013 hoch verfügbare Farm** ersetzt.

Die grundlegende SharePoint-Farm besteht aus drei virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

Sie können diese Farmkonfiguration für eine vereinfachte Einrichtung für die SharePoint-App-Entwicklung oder für eine erste Bewertung von SharePoint 2013 verwenden.

So erstellen Sie die grundlegende SharePoint-Farm (mit drei Servern):

1. Klicken Sie [hier](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Klicken Sie auf **Bereitstellen**.
3. Klicken Sie im Bereich **SharePoint 2013 nicht hoch verfügbare Farm** auf **Erstellen**.
4. Geben Sie die Einstellungen für die sieben Schritte des Bereichs **SharePoint 2013 nicht hoch verfügbare Farm erstellen** ein, und klicken Sie dann auf **Erstellen**.

Die hochverfügbare SharePoint-Farm besteht aus neun virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

Sie können diese Farmkonfiguration verwenden, um höhere Clientlasten, Hochverfügbarkeit eines externen SharePoint-Standorts sowie SQL Server AlwaysOn für eine SharePoint-Farm zu testen. Außerdem können Sie diese Konfiguration für die SharePoint-App-Entwicklung in einer Hochverfügbarkeitsumgebung nutzen.

So erstellen Sie die hoch verfügbare SharePoint-Farm (neun Server):

1. Klicken Sie [hier](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Klicken Sie auf **Bereitstellen**.
3. Klicken Sie im Bereich **SharePoint 2013 hoch verfügbare Farm** auf **Erstellen**.
4. Geben Sie die Einstellungen für die sieben Schritte des Bereichs **SharePoint 2013 hoch verfügbare Farm erstellen** ein, und klicken Sie dann auf **Erstellen**.

## Verwalten der SharePoint-Farmen

Sie können die Server dieser Farmen über Remotedesktopverbindungen verwalten. Weitere Informationen finden Sie unter [Anmelden beim virtuellen Computer](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine).

Auf der SharePoint-Website für die Zentraladministration können Sie eigene Websites, SharePoint-Anwendungen und andere Funktionen konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

> [AZURE.NOTE] Das Azure-Portal erstellt beide Farmen in einem virtuellen Cloudnetzwerk mit einer über das Internet zugänglichen Webpräsenz. Es gibt keine Standort-zu-Standort-VPN- oder ExpressRoute-Verbindung mit Ihrem Unternehmensnetzwerk.

## Nächster Schritt

- Lernen Sie weitere Konfigurationen von [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) in Azure-Infrastrukturdiensten kennen.

<!---HONumber=AcomDC_0211_2016-->