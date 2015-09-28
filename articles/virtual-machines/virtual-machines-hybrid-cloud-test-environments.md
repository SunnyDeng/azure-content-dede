<properties
	pageTitle="Testumgebungen für Hybridclouds in Azure | Microsoft Azure"
	description="In den hier genannten Artikeln erfahren Sie, wie Sie Entwicklungs-/Testumgebungen oder IT Pro-Umgebungen für Machbarkeitsstudien für Ihre Azure-basierte Hybridcloud erstellen."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="09/16/2015"
	ms.author="josephd"/>

# Testumgebungen für Azure-Hybridclouds

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen von Ressourcen mit dem klassischen Bereitstellungsmodell.

Für Dev/Test oder eine Machbarkeitsstudie verwenden hybride Cloud-Testumgebungen Ihre lokale Internetverbindung und eine Ihrer öffentlichen IP-Adressen, und führen Sie schrittweise zur Einrichtung eines funktionsfähigen standortübergreifenden Azure Virtual Network (VNet). Sobald Sie fertig sind, können Sie Anwendungen entwickeln und testen, mit einfachen IT-Arbeitslasten experimentieren und die relative Leistung einer Standort-zu-Standort-VPN (VPN)-Verbindung im Vergleich zu Ihrem Standort im Internet zu messen.

## Hybride Cloud-Basiskonfiguration

Die [hybride Cloud-Basiskonfiguration](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) besteht aus:

- Einem vereinfachten lokalen Netzwerk mit vier virtuellen Computern (ein Domänencontroller, ein Anwendungsserver, ein Clientcomputer und ein VPN-Gerät mit Windows Server und Routing- und Remotezugriff).
- Einem virtuellen Azure-Netzwerk mit einem Replikat-Domänencontroller.
- Einer Site-to-Site-VPN-Verbindung.

## SharePoint-Intranetfarm in einer Hybridcloud

Die [SharePoint-Intranetfarm in einer hybriden Cloud-Testumgebung](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) fügt der hybriden Cloud-Basiskonfiguration einen SQL Server 2014-Server und einen SharePoint Server 2013 hinzu. Dadurch wird eine SharePoint-Farm auf zwei Ebenen erstellt, auf die Sie vom Clientcomputer im vereinfachten lokalen Netzwerk zugreifen können.

## Webbasierte Branchenanwendung (LOB-Anwendung) in einer Hybridcloud

Die [webbasierte LOB-Anwendung in einer hybriden Cloud-Testumgebung](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) fügt der hybriden Cloud-Basiskonfiguration einen SQL Server 2014-Server und einen Internet Information Services-Server (IIS) hinzu. Dadurch wird die Infrastruktur erstellt, in der Sie eine mehrstufige, webbasierte LOB-Anwendung bereitstellen und testen können.

## Office 365-Verzeichnissynchronisierungsserver (DirSync) in einer Hybridcloud

Der [Office 365 DirSync-Server in einer hybriden Cloud-Testumgebung](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md) fügt der hybriden Cloud-Basiskonfiguration einen DirSync-Server hinzu und veranschaulicht Office 365-DirSync mit Kennwortsynchronisierung in einem Office 365-Testabonnement.

## Simulierte hybride Cloud-Testumgebung

Für Organisationen und Einzelpersonen, für die eine direkte Internetverbindung und eine öffentliche IP-Adresse nicht ohne Weiteres verfügbar sind, erstellt die [simulierte hybride Cloud-Testumgebung](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) das vereinfachte lokale Netzwerk in einem separaten virtuellen Azure-Netzwerk, und verbindet die beiden virtuellen Netzwerke dann mit einer VNet-zu-VNet-VPN-Verbindung.


## Zusätzliche Ressourcen

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Azure-Infrastrukturdienste-Workload: Branchenanwendung mit hoher Verfügbarkeit](virtual-machines-workload-high-availability-LOB-application.md)

[Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=Sept15_HO3-->