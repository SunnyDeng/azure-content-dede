<properties 
   pageTitle="Hybrid Networking (Azure-Architekturmuster)" 
   description="Das Hybrid Networking-Muster ist Bestandteil des Infrastructure-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird." 
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

# Hybrid Networking (Azure-Architekturmuster)

Das [Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) bietet Unterstützung für die Workloadintegration beim Einbinden von Anwendungen in eine Microsoft-Cloud-Lösung.

CPIF beschreibt, wie Unternehmen, Kunden und Partner auf die Cloud ausgerichtete Arbeitsauslastungen unter Verwendung der Hybridcloudplattform und Verwaltungsfunktionen von Azure, System Center und Windows Server entwerfen und bereitstellen sollten.

Das **Hybrid Networking**-Muster ist Bestandteil des **Infrastructure**-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird.

##  Hybrid Networking

Das Hybrid Networking-Entwurfsmuster beschreibt die Azure-Features und -Dienste, die zum Bereitstellen von Netzwerkfunktionalität erforderlich sind, um vorhersagbare Leistung und hohe Verfügbarkeit über geografische Grenzen hinweg zu gewährleisten. Eine vollständige Liste der Microsoft Azure-Regionen sowie der Dienste, die in jeder Region verfügbar sind, erhalten Sie auf der Website mit der Microsoft Azure-Dokumentation. Dieses Dokument bietet einen Überblick über Microsoft Azure-Netzwerkfunktionen für Hybridumgebungen. Microsoft Azure Virtual Networking ermöglicht es Ihnen, logisch isolierte Netzwerke in Azure zu erstellen und auf sichere Weise über das Internet oder eine private Netzwerkverbindung eine Verbindung mit Ihrem lokalen Datencenter herzustellen. Zusätzlich können sich einzelne Clientcomputer über eine IPSec-VPN-Verbindung mit einem isolierten Azure-Netzwerk verbinden.

Das Hybrid Networking-Architekturmuster umfasst die folgenden Hauptbereiche:

- Verbindungsherstellung zwischen lokalen Netzwerken und Azure 
- Erweitern von virtuellen Azure-Netzwerken über Regionen hinweg 
- Erweitern virtueller Azure-Netzwerke über Abonnements hinweg 
- Bereitstellen von Remotenetzwerkzugriff für Entwickler 

## Architekturmuster – Übersicht 

Das Hybrid Networking-Architekturmuster ist aufgrund der möglichen Anzahl von Szenarien, die erstellt werden können, sehr komplex. Dieses Architekturmuster konzentriert sich auf die folgenden vier Szenarien:

- Standort-zu-Standort-Hybridnetzwerke mit Mehrfachhop-Netzwerkrouting in einem einzelnen Abonnement und einer Region 
- Standort-zu-Standort-Hybridnetzwerke mit virtuellem Mehrfachhop-Netzwerkrouting über Abonnements und Regionen hinweg 
- ExpressRoute-Hybridnetzwerke mit MPLS-Konnektivität 
- ExpressRoute-Hybridnetzwerke mit IXP-Konnektivität 

##  Zusätzliche Ressourcen
[Hybrid Networking (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

## Siehe auch
[CPIF-Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Global Load Balanced Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Load Balanced Data Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Azure Search Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Batch Processing Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=August15_HO6-->