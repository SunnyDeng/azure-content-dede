<properties 
   pageTitle="Multi-Site Data Tier (Azure-Architekturmuster)" 
   description="Das Multi-Site Data Tier-Muster ist Bestandteil des Foundation-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Multi-Site Data Tier (Azure-Architekturmuster)

Das [Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) bietet Unterstützung für die Workloadintegration beim Einbinden von Anwendungen in eine Microsoft-Cloud-Lösung.

CPIF beschreibt, wie Unternehmen, Kunden und Partner auf die Cloud ausgerichtete Arbeitsauslastungen unter Verwendung der Hybridcloudplattform und Verwaltungsfunktionen von Azure, System Center und Windows Server entwerfen und bereitstellen sollten.

Das **Multi-Site Data Tier**-Muster ist Bestandteil des **Foundation**-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird.

## Multi-Site Data Tier

Das Multi-Site Data Tier-Entwurfsmuster bietet Details zu den Azure-Features und -Diensten, die zur Bereitstellung von Datenebenendiensten mit vorhersagbarer Leistung und hoher Verfügbarkeit über geografische Grenzen hinweg erforderlich sind. Im Rahmen dieses Entwurfsmusters ist eine Datenebene als eine Ebene von Diensten definiert, die traditionelle Datenplattformdienste entweder isoliert oder als Bestandteil einer mehrstufigen Anwendung bereitstellen. Innerhalb dieses Musters wird der Lastenausgleich für die Datenebene sowohl lokal innerhalb der Region als auch regionsübergreifend bereitgestellt.

Seit der Einführung von SQL Server 2012 sind AlwaysOn-Verfügbarkeitsgruppen eine Funktion zur Bereitstellung von Hochverfügbarkeit und Notfallwiederherstellung, die in Azure Infrastructure Services vollständig unterstützt wird. Ausführliche Informationen und die offizielle Ankündigung der Unterstützung für AlwaysOn-Verfügbarkeitsgruppen im Microsoft Azure Infrastructure Service finden Sie im Artikel zu den AlwaysOn-Verfügbarkeitsgruppen.

Dieses Dokument bietet eine architektonische Übersicht über ein Multi-Site Data Tier in Azure unter Verwendung von SQL AlwaysOn-Verfügbarkeitsgruppen. Mit einem optionalen schreibgeschützten Knoten in einem zusätzlichen Azure-Datencenter zur mehr Funktionalität und Notfallwiederherstellung. Durch die Verwendung von SQL AlwaysOn in Azure wird eine Datenebene mit hoher Verfügbarkeit bereitgestellt, die von Web- oder Anwendungsebenen genutzt werden kann.

Das vorliegende Dokument beschreibt vornehmlich architektonische Muster und Vorgehensweisen. Umfangreiche Anleitungen zur Bereitstellung finden Sie in den offiziellen Lernprogrammen, in denen das Konfigurieren von AlwaysOn-Gruppen in Azure sowie die Konfiguration für den AlwaysOn-Verfügbarkeitsgruppen-Listener beschrieben wird.

## Architekturmuster – Übersicht 

Dieses Dokument beschreibt ein Muster zur Bereitstellung von Zugriff auf Microsoft SQL Server-Inhalte über unterschiedliche geografische Regionen hinweg, um Verfügbarkeit und Redundanz zu gewährleisten. Nachfolgend werden wichtige Dienste gezeigt, wobei die Anwendung oder die Webebene, die auf die Daten selbst zugreift, nicht berücksichtigt wird. Das nachstehende Diagramm ist eine einfache Darstellung der relevanten Dienste und deren Verwendung als Bestandteil dieses Musters.

Jeder der Hauptdienstbereiche wird im Anschluss an das Diagramm näher beschrieben.
 
![Tags-Bereich im Blatt für Ressourcen oder Ressourcengruppen](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  Zusätzliche Ressourcen
[Load Balanced Data Tier (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## Siehe auch
[CPIF-Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Global Load Balanced Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Hybrid Networking](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Batch Processing Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO3-->