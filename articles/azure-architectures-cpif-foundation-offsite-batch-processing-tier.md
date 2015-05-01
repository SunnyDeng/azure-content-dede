<properties 
   pageTitle="Offsite Batch Processing Tier (Azure-Architekturmuster)" 
   description="Das Offsite Batch Processing Tier-Muster ist Bestandteil des Infrastructure-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird." 
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

# Offsite Batch Processing Tier (Azure-Architekturmuster)

Das [Cloud Platform Integration Framework (CPIF)](azure-architectures-cpif-overview.md) bietet Unterstützung für die Arbeitsauslastungsintegration beim Einbinden von Anwendungen in eine Microsoft-Cloudlösung.  

CPIF beschreibt, wie Unternehmen, Kunden und Partner auf die Cloud ausgerichtete Arbeitsauslastungen unter Verwendung der Hybridcloudplattform und Verwaltungsfunktionen von Azure, System Center und Windows Server entwerfen und bereitstellen sollten. 

Das **Offsite Batch Processing Tier**-Muster ist Bestandteil des **Infrastructure**-Bereichs, der im Dokument zur CPIF-Architektur ausführlich beschrieben wird. 

##  Offsite Batch Processing Tier

Das Offsite Batch Processing Tier-Entwurfsmuster beschreibt die Azure-Features und -Dienste, die zum Bereitstellen einer Back-End-Datenverarbeitung erforderlich sind, die sowohl fehlertolerant als auch skalierbar ist.  Diese Dienste werden als Workerrollen in Cloud-Diensten in Azure realisiert, die aktuell in einem beliebigen Azure-Datencenter bereitgestellt werden können.   

Arbeitsauslastungen für die Batchverarbeitung sind dahingehend einzigartig, dass sie in der Regel wenige oder keine Benutzerschnittstellen bereitstellen.  Ein Beispiel für diese Art von Arbeitsauslastung in einer lokalen Bereitstellung ist ein Windows-Dienst, der auf Windows Server ausgeführt wird.  Bei Vorliegen einer solchen Arbeitsauslastung in einer Cloudumgebung wäre es ineffizient, einen kompletten Server zur Ausführung einer Arbeitsauslastung bereitzustellen, wenn tatsächlich Computing, Speicher und Netzwerkkonnektivität benötigt werden.  Die Workerrolle ist die Implementierung hierfür in Azure. 

Per Definition ist ein in Azure ausgeführter Batchauftrag eine Arbeitsauslastung, die sich mit einer Ressource verbindet, Geschäftslogik (Computing) bereitstellt und eine Ausgabe liefert.  Die Eingabe- und Ausgaberessourcen werden vom Benutzer definiert und können Flatfiles, Blobs in Azure Blob-Speicher, eine NoSQL-Datenbank oder relationale Datenbanken umfassen.   

Die Geschäftslogik wird in einer Azure-Workerrolle implementiert, typischerweise durch das Definieren der erforderlichen Geschäftslogik in einer .NET-Bibliothek.  Wenngleich das Bereitstellen einer Workerrolle in Azure ein einfacher Vorgang ist, erfordert die Bereitstellung einer Workerrolle, die Fehlertoleranz und Skalierbarkeit bietet, einen Entwurf, der die Art der Dienstausführung und dessen Verwaltung in Azure berücksichtigt.  Dieses Muster bietet ausführliche Informationen zum Entwurf, der diese Anforderungen berücksichtigt und beschreibt, wie die Implementierung erfolgen kann. 

## Architekturmuster - Übersicht 

Dieses Dokument beschreibt ein Muster für die Offsite-Batchverarbeitung mithilfe von Workerrolleninstanzen, die in einem Cloud-Dienst in Azure enthalten sind.  Die wichtigsten Komponenten in diesem Entwurf werden nachfolgend gezeigt.  Dieses Diagramm veranschaulicht die mindestens erforderlichen Instanzen, um Fehlertoleranz zu erzielen.  Es können zusätzliche Instanzen bereitgestellt werden, um die Leistung des Diensts zu erhöhen.  Zusätzlich kann die automatische Skalierung aktiviert werden, mit der die Instanzen nach Zeit oder basierend auf zusätzlichen Servermetriken skaliert werden. 

##  Zusätzliche Ressourcen
[Batch Processing Tier (PDF)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## Siehe auch
[CPIF-Architektur](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Global Load Balanced Web Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Load Balanced Data Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Hybrid Networking](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure Search Tier](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 



<!--HONumber=52-->