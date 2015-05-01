<properties 
   pageTitle="Global Load Balanced Web Tier (Azure-Architekturmuster)" 
   description="Das Global Load Balanced Web Tier-Muster ist Bestandteil des Foundation-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird." 
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

# Global Load Balanced Web Tier (Azure-Architekturmuster)

Das [Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) bietet Unterstützung für die Arbeitsauslastungsintegration beim Einbinden von Anwendungen in eine Microsoft-Cloudlösung. 

CPIF beschreibt, wie Unternehmen, Kunden und Partner auf die Cloud ausgerichtete Arbeitsauslastungen unter Verwendung der Hybridcloudplattform und Verwaltungsfunktionen von Azure, System Center und Windows Server entwerfen und bereitstellen sollten. 

Das **Global Load Balanced Web Tier**-Muster ist Bestandteil des **Foundation**-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird. 

##  Global Load Balanced Web Tier

Das Global Load Balanced Web Tier-Entwurfsmuster bietet Details zu den Azure-Features und -Diensten, die zur Bereitstellung von Webebenendiensten mit vorhersagbarer Leistung und hoher Verfügbarkeit über geografische Grenzen hinweg erforderlich sind. 

Im Rahmen dieses Entwurfsmusters ist eine Webebene als eine Ebene von Diensten definiert, die traditionellen HTTP/HTTP-Inhalt oder Anwendungsdienste entweder isoliert oder als Bestandteil einer mehrstufigen Webanwendung bereitstellen.  Innerhalb dieses Musters wird der Lastenausgleich für die Webebene sowohl lokal innerhalb der Region als auch regionsübergreifend bereitgestellt. Auf Computingebene können diese Dienste über Microsoft Azure Virtual Machines, Websites oder eine Kombination aus beidem bereitgestellt werden.  Virtuelle Computer, die Webdienste bereitstellen, können Inhalte unter Verwendung beliebiger unterstützter Gastbetriebssysteme (Microsoft Windows oder Linux-Distributionen) innerhalb von Microsoft Azure hosten. 


## Architekturmuster - Übersicht 

Dieses Dokument beschreibt ein Muster für die Bereitstellung von Zugriff auf Webdienste oder Webserverinhalte über unterschiedliche geografische Regionen hinweg, um Verfügbarkeit und Redundanz zu gewährleisten.  Nachfolgend werden wichtige Dienste gezeigt, wobei Einschränkungen durch die Webplattform oder die Entwicklungsmethodologie innerhalb des Webdiensts selbst nicht berücksichtigt werden.  Es gibt zwei Varianten dieses Musters - eine Variante hostet Webinhalte oder -dienste auf virtuellen Computern (mithilfe von Betriebssystemen und -familien, die von Azure unterstützt werden), die andere Variante verwendet Azure Websites.  Das nachstehende Diagramm ist eine einfache Darstellung der relevanten Dienste und deren Verwendung als Bestandteil dieses Musters am Beispiel von virtuellen Computern.   

##  Zusätzliche Ressourcen
[Global Load Balanced Web Tier (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

## Siehe auch
[CPIF-Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Load Balanced Data Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Hybrid Networking](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Batch Processing Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)



<!--HONumber=52-->