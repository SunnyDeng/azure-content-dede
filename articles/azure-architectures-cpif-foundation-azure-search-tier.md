<properties 
   pageTitle="Azure Search Tier (Azure-Architekturmuster)" 
   description="Das Azure Search Tier-Muster ist Bestandteil des Foundation-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird." 
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

# Azure Search Tier (Azure-Architekturmuster)

Das [Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) bietet Unterstützung für die Workloadintegration beim Einbinden von Anwendungen in eine Microsoft-Cloud-Lösung.

CPIF beschreibt, wie Unternehmen, Kunden und Partner auf die Cloud ausgerichtete Arbeitsauslastungen unter Verwendung der Hybridcloudplattform und Verwaltungsfunktionen von Azure, System Center und Windows Server entwerfen und bereitstellen sollten.

Das **Azure Search Tier**-Muster ist Bestandteil des **Foundation**-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird.

##  Azure Search Tier

Das Azure Search Tier-Entwurfsmuster bietet Details zu den Azure-Features und -Diensten, die zur Bereitstellung von Suchdiensten mit vorhersagbarer Leistung und hoher Verfügbarkeit über geografische Grenzen hinweg erforderlich sind. Darüber hinaus wird ein Architekturmuster zur Verwendung von Azure Search bei der Entwicklung einer Suchlösung bereitgestellt. Azure Search ist ein in Microsoft Azure integrierter "Search-as-a-Service", der es Entwicklern ermöglicht, Suchfunktionen in Anwendungen zu integrieren, ohne hierzu Infrastrukturdienste bereitzustellen, verwalten oder warten zu müssen. Der Zweck dieses Musters liegt darin, eine wiederverwendbare Lösung für den Einsatz in verschiedenen Situationen und Entwürfen bereitzustellen.

## Architekturmuster – Übersicht 

Dieses Dokument beschreibt ein grundlegendes Muster für die Verwendung von Azure Search mit zwei Varianten des Grundmusters, um die architektonischen Möglichkeiten des Diensts zu veranschaulichen. Das Grundmuster umfasst Azure Search sowie zugehörige Azure-Dienste und ist dazu gedacht, Hilfestellung beim Erstellen von End-to-End-Entwürfen zu bieten. Varianten des Musters, insbesondere die Shared Service- und Concurrency-Muster, sind ebenfalls in diesem Abschnitt enthalten, um Anleitungen basierend auf verschiedenen Anforderungen, SLAs (Service Level Agreements) und anderen spezifischen Bedingungen zu bieten.

##  Zusätzliche Ressourcen
[Azure Search Tier (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

## Siehe auch
[CPIF-Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Global Load Balanced Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Load Balanced Data Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Hybrid Networking](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Batch Processing Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO3-->