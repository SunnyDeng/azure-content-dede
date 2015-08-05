<properties 
   pageTitle="Cloud Platform Integration Framework - Azure-Architekturmuster" 
   description="Das Cloud Platform Integration Framework bietet durch Architekturmuster für Microsoft Azure Unterstützung für die Arbeitsauslastungsintegration beim Einbinden von Anwendungen in eine Microsoft-Cloudlösung." 
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


# Cloud Platform Integration Framework (Azure-Architekturmuster)

Das Cloud Platform Integration Framework bietet Unterstützung für die Arbeitsauslastungsintegration beim Einbinden von Anwendungen in eine Microsoft-Cloudlösung.

CPIF beschreibt, wie Unternehmen, Kunden und Partner auf die Cloud ausgerichtete Arbeitsauslastungen unter Verwendung der Hybridcloudplattform und Verwaltungsfunktionen von Azure, System Center und Windows Server entwerfen und bereitstellen sollten. Die CPIF-Bereiche wurden in die folgenden Funktionen unterteilt:

![Tags-Bereich im Blatt für Ressourcen oder Ressourcengruppen](./media/azure-architecture-cpif-overview/overview.png)

##  CPIF-Architektur

Sowohl öffentliche als auch private Cloudumgebungen bieten allgemeine Elemente, um die Ausführung von komplexen Arbeitsauslastungen zu unterstützen. Während diese Architekturen in herkömmlichen lokalen physischen und virtualisierten Umgebungen relativ einfach zu verstehen sind, erfordern die Konstrukte innerhalb von Microsoft Azure eine zusätzliche Planung, um die Infrastruktur- und Plattformfunktionen in öffentlichen Cloudumgebungen zu rationalisieren. Um die Entwicklung von gehosteten Anwendungen oder Diensten in Azure zu unterstützen, sind verschiedene Muster zur Umschreibung der verschiedenen Komponenten erforderlich, die für eine vorgegebene Arbeitsauslastungssituation benötigt werden.

Diese Architekturmuster fallen in die folgenden Kategorien:

- **Infrastructure** – Microsoft Azure ist eine IaaS- (Infrastructure-as-a-Service) und PaaS-Lösung (Platform-as-a-Service), die verschiedene zugrunde liegende Dienste und Funktionen umfasst. Diese Dienste können allgemein in Computing-, Speicher- und Netzwerkdienste unterteilt werden, es gibt jedoch verschiedene Funktionalitäten, die möglicherweise nicht in diese Definition passen. Infrastrukturmuster bieten Details zu einem bestimmten Funktionsbereich von Microsoft Azure, der für die Bereitstellung eines Diensts für eine oder mehrere Lösungen erforderlich ist, die innerhalb eines Azure-Abonnements gehostet werden. 
- **Foundation** – Bei der Entwicklung mehrstufiger Anwendungen oder Dienste innerhalb von Azure müssen verschiedene Komponenten kombiniert werden, um eine geeignete Hostingumgebung bereitzustellen. Foundation-Muster stellen einen oder mehrere Dienste von Microsoft Azure zusammen, um eine vorgegebene Funktionsebene innerhalb einer Anwendung zu unterstützen. Hierzu ist möglicherweise die Verwendung von einer oder mehrerer Komponenten erforderlich, die in den oben genannten Infrastrukturmustern beschrieben werden. Beispielsweise erfordert die Darstellungsschicht einer mehrstufigen Anwendung Computing-, Netzwerk- und Speicherfunktionen in Azure, um die gewünschte Funktionalität zu bieten. Foundation-Muster werden als Bestandteil einer vorgegebenen Lösung mit anderen Mustern kombiniert.
- **Solution** – Solution-Muster werden mit Infrastruktur- und/oder Foundation-Mustern kombiniert, um eine in der Entwicklung befindliche Endanwendung oder einen Dienst zu repräsentieren. Es ist vorgesehen, dass komplexe Lösungen nicht unabhängig von anderen Mustern entwickelt werden. Stattdessen sollten sie die Komponenten und Schnittstellen verwenden, die in jeder der oben genannten Musterkategorien definiert werden.    

## Azure-Architekturmuster – Konzepte

Zur Unterstützung der Entwicklung von Lösungsarchitekturen innerhalb von Azure wird eine Reihe von Anleitungen für Muster für gängige Szenarien bereitgestellt. Diese Szenarioleitfäden werden nach und nach bereitgestellt, wobei folgende Muster angestrebt werden:

- Global Load Balanced Web Tier 
- Load Balanced Data Tier
- Batch Processing Tier
- Hybrid Networking
- Azure Search 

##  Zusätzliche Ressourcen
[CPIF-Architektur (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

## Siehe auch
[Global Load Balanced Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Load Balanced Data Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Batch Processing Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Azure Networking](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=July15_HO4-->