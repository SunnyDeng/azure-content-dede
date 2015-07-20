<properties 
	pageTitle="Testumgebungen für Azure-Hybridclouds" 
	description="Bei diesen Schlüsselthemen erfahren Sie, wie Sie Testumgebungen erstellen, die Sie für Dev/Tests oder einer Machbarkeitsstudie für Ihre Azure-Hybridcloud verwenden können." 
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
	ms.date="05/29/2015" 
	ms.author="josephd"/>

# Testumgebungen für Azure-Hybridclouds

Für Dev/Test oder eine Machbarkeitsstudie verwenden hybride Cloud-Testumgebungen Ihre lokale Internetverbindung und eine Ihrer öffentlichen IP-Adressen, und führen Sie schrittweise zur Einrichtung eines funktionsfähigen standortübergreifenden Azure Virtual Network (VNet). Nach Einrichtung können Sie Anwendungsentwicklung und -tests durchführen, mit einfachen IT-Arbeitslasten experimentieren und die relative Leistung einer Standort-zu-Standort-VPN (VPN)-Verbindung im Vergleich zu Ihrem Standort im Internet zu messen.

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

[PDF-Datei mit der Architekturblaupause für 3-D-Branchenanwendungen](http://download.microsoft.com/download/2/C/8/2C8EB75F-AC45-4A79-8A63-C1800C098792/MS_Arch_LOB_App_3D_pdf.pdf)

[Bereitstellen von Office 365-Verzeichnissynchronisierung (DirSync) in Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)


 

<!---HONumber=July15_HO2-->