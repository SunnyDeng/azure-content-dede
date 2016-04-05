<properties
	pageTitle="Welche Workloads können mit Azure Site Recovery geschützt werden?" 
	description="Azure Site Recovery schützt Ihre Workloads und Anwendungen durch Koordinieren von Replikation, Failover und Wiederherstellung lokaler virtueller Computer und physischer Server in Azure oder an einem sekundären lokalen Standort." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="03/27/2016" 
	ms.author="raynew"/>

# Welche Workloads können mit Azure Site Recovery geschützt werden?


In diesem Artikel wird beschrieben, welche Workloads und Anwendungen Sie mit Azure Site Recovery schützen können.

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.

## Übersicht

Organisationen benötigen eine Strategie für die Bereiche Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), mit der bestimmt wird, wie Apps, Workloads und Daten bei geplanten und ungeplanten Ausfallzeiten verfügbar bleiben und so schnell wie möglich die normalen Arbeitsbedingungen wiederhergestellt werden können.

Site Recovery ist ein Azure-Dienst, der einen Beitrag zu Ihrer BCDR-Strategie leistet, indem es Ihnen ermöglicht wird, anwendungsorientierte Resilienzlösungen bereitzustellen. Hierzu wird die Replikation von lokalen physischen Servern und virtuellen Computern in die Cloud (Azure) oder an einen sekundären Standort orchestriert. Unabhängig davon, ob Ihre Apps Windows- oder Linux-basiert sind, auf physischen Servern ausgeführt werden oder VMware- oder Hyper-V-VMs sind, gilt Folgendes: Sie können Site Recovery verwenden, um die Replikation zu orchestrieren, Tests für die Notfallwiederherstellung durchzuführen und Failover und Failbacks auszuführen.


Site Recovery ist in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory integriert. Microsoft arbeitet auch eng mit führenden Anbietern zusammen, z.B. Oracle, SAP, IBM und Red Hat, um sicherzustellen, dass seine Programme und Dienste auf Microsoft-Plattformen reibungslos funktionieren, z.B. Azure. Sie können eine Lösung für jede App speziell anpassen.

## Gründe für die Verwendung von Site Recovery für den Anwendungsschutz

Site Recovery trägt wie folgt zum Schutz und zur Wiederherstellung auf Anwendungsebene bei:

- Nahezu synchrone Replikation mit geringen RPOs von bis zu 30 Sekunden, um die Anforderungen der meisten kritischen Apps von Unternehmen zu erfüllen.
- Anwendungskonsistente Momentaufnahmen für Anwendungen mit einer oder mehreren Ebenen.
- Integration mit SQL Server AlwaysOn und Partnerschaft mit anderen Replikationstechnologien auf Anwendungsebene, einschließlich Active Directory-Replikation, SQL AlwaysOn, Exchange-Datenbankverfügbarkeitsgruppen und Oracle Data Guard.
- Flexible Wiederherstellungspläne, die die Wiederherstellung des gesamten Anwendungsstapels mit einem einzigen Mausklick und das Einfügen von externen Skripts und manuellen Aktionen in den Plan ermöglichen. 
- Erweiterte Netzwerkverwaltung in Site Recovery und Azure, um die App-Netzwerkanforderungen zu vereinfachen, z.B. das Reservieren von IP-Adressen, Konfigurieren des Lastenausgleichs und Integrieren in Azure Traffic Manager, um Netzwerk-Switchovers mit niedrigem RTO zu erzielen.
-  Eine umfassende Automatisierungsbibliothek bietet produktionsbereite, anwendungsspezifische Skripts, die heruntergeladen und in Wiederherstellungspläne integriert werden können.



##Übersicht über Workloads

Mit Site Recovery können alle Apps repliziert werden, die auf einem unterstützten virtuellen Computer ausgeführt werden. Außerdem haben wir mit Produktteams zusammengearbeitet, um zusätzliche App-spezifische Tests durchzuführen.

**Workload** | **Replizieren von Hyper-V-VMs an einem sekundären Standort** | **Replizieren von Hyper-V-VMs in Azure** | **Replizieren von VMware-VMs an einem sekundären Standort** | **Replizieren von VMware-VMs in Azure**
---|---|---|---|---
Active Directory, DNS | J | J | J | J 
Web-Apps (IIS, SQL) | J | J | J | J
SCOM | J | J | J | J
SharePoint | J | J | J | J
SAP<br/><br/>Replizieren eines SAP-Standorts zu Azure (kein Cluster) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft)
Exchange (kein DAG) | J | In Kürze verfügbar | J | Y
Remotedesktop/VDI | J | J | Y | N/V 
Linux (Betriebssystem und Apps) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) 
Dynamics AX | J | J | J | J
Dynamics CRM | J | In Kürze verfügbar | J | In Kürze verfügbar
Oracle | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft)
Windows-Dateiserver | J | J | J | J


## Replizieren von Active Directory und DNS

Eine Infrastruktur mit Active Directory und DNS ist für die meisten Unternehmens-Apps wichtig. Während der Notfallwiederherstellung müssen Sie diese Infrastrukturkomponenten schützen und wiederherstellen, bevor Sie Ihre Workloads und Apps wiederherstellen.

Sie können Site Recovery verwenden, um für Active Directory und DNS einen vollständigen automatisierten Notfallwiederherstellungsplan zu erstellen. Wenn Sie beispielsweise für SharePoint und SAP ein Failover von einem primären auf einen sekundären Standort durchführen möchten, können Sie einen Wiederherstellungsplan einrichten, bei dem das Failover zuerst für Active Directory erfolgt. Anschließend erstellen Sie einen weiteren App-spezifischen Wiederherstellungsplan, um das Failover für die anderen Apps durchzuführen, die auf Active Directory basieren.

[Erfahren Sie mehr](site-recovery-active-directory.md) zum Schutz von Active Directory und DNS.

## Schützen von SQL Server

SQL Server stellt die Grundlage für Datendienste vieler Unternehmens-Apps in einem lokalen Rechenzentrum dar. Site Recovery kann zusammen mit SQL Server-Technologie für hohe Verfügbarkeit und Notfallwiederherstellung verwendet werden, um Unternehmens-Apps mit mehreren Ebenen zu schützen, für die SQL Server verwendet wird. Site Recovery bietet Folgendes:

- Eine einfache und kostengünstige Notfallwiederherstellungslösung für SQL Server. Replikation mehrerer Versionen und Editionen von eigenständigen SQL Server-Servern- und -Clustern zu Azure oder einem sekundären Standort.  
- Integration in SQL AlwaysOn-Verfügbarkeitsgruppen zum Verwalten von Failover- und Failbackprozessen mit Azure Site Recovery-Wiederherstellungsplänen.
- Umfassende Wiederherstellungspläne für alle Ebenen einer Anwendung, einschließlich SQL Server-Datenbanken.
- Skalierung von SQL Server bei Lastspitzen mit Site Recovery, indem in Azure größere virtuelle IaaS-Computer verwendet werden.
- Einfaches Testen der SQL Server-Notfallwiederherstellung. Sie können Testfailover ausführen, um Daten zu analysieren und die Compliance zu prüfen, ohne dass sich dies auf die Produktionsumgebung auswirkt.

[Erfahren Sie mehr](site-recovery-sql.md) über das Schützen von SQL Server.

##Schützen von SharePoint

Mit Azure Site Recovery können SharePoint-Bereitstellungen wie folgt geschützt werden:

- Wegfall der Notwendigkeit und zugehörigen Infrastrukturkosten einer Standbyfarm für die Notfallwiederherstellung. Verwenden Sie Site Recovery, um eine gesamte Farm (Web-, App- und Datenbankebenen) zu Azure oder einem sekundären Standort zu replizieren.
- Vereinfachen der Bereitstellung und Verwaltung von Anwendungen. Updates, die am primären Standort bereitgestellt werden, werden automatisch repliziert und sind nach dem Failover und der Wiederherstellung einer Farm daher an einem sekundären Standort verfügbar. Außerdem werden die Verwaltungskomplexität und Kosten verringert, die entstehen, wenn eine Standbyfarm auf dem neuesten Stand gehalten werden muss.
- Vereinfachen von Entwicklung und Tests von SharePoint-Anwendungen, indem für Test- und Debugzwecke eine der Produktionsversion ähnliche Replikatumgebung bedarfsgesteuert erstellt wird.
- Vereinfachen des Übergangs in die Cloud, indem Site Recovery zum Migrieren von SharePoint-Bereitstellungen in Azure verwendet wird.

[Erfahren Sie mehr](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) über das Schützen von SharePoint.


## Schützen von Dynamics AX

Azure Site Recovery unterstützt Sie wie folgt beim Schützen Ihrer Dynamics AX ERP-Lösung:

- Orchestrieren der Replikation der gesamten Dynamics AX-Umgebung (Web- und AOS-Ebene, Datenbankebenen, SharePoint) zu Azure oder einem sekundären Standort.
- Vereinfachen der Migration von Dynamics AX-Bereitstellungen zur Cloud (Azure).
- Vereinfachen von Entwicklung und Tests von Dynamics AX-Anwendungen, indem für Test- und Debugzwecke eine der Produktionsversion ähnliche Kopie bedarfsgesteuert erstellt wird.

[Erfahren Sie mehr](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) über das Schützen von Dynamics AX.

## Schützen von RDS 

Mit Remotedesktopdiensten (RDS) werden eine Virtual Desktop Infrastructure (VDI) sowie sitzungsbasierte Desktops und Anwendungen ermöglicht, damit Benutzer an jedem Ort arbeiten können. Mit Azure Site Recovery haben Sie folgende Möglichkeiten:

- Replizieren von verwalteten oder nicht verwalteten virtuellen Desktops eines Pools an einem sekundären Standort und von Remoteanwendungen und -sitzungen an einem sekundären Standort oder in Azure.
- Sie können Folgendes replizieren:

**RDS** | **Replizieren von Hyper-V-VMs an einem sekundären Standort** | **Replizieren von Hyper-V-VMs in Azure** | **Replizieren von VMware-VMs an einem sekundären Standort** | **Replizieren von VMware-VMs in Azure** | **Replizieren von physischen Servern an einem sekundären Standort** | **Replizieren physischer Server in Azure**
---|---|---|---|---|---|---
**Virtuelle Desktops eines Pools (nicht verwaltet)** | Ja | Nein | Ja | Nein | Ja | Nein
**Virtuelle Desktops eines Pools (verwaltet und ohne UPD)** | Ja | Nein | Ja | Nein | Ja | Nein
**Remoteanwendungen und Remotedesktopsitzungen (ohne UPD)** | Ja | Ja | Ja | Ja | Ja | Ja


[Erfahren Sie mehr](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) über das Schützen von RDS.


## Schützen von Exchange

Mit Site Recovery können Sie Exchange wie folgt schützen:

- Für kleine Exchange-Bereitstellungen, z.B. einen einzelnen oder nicht gruppierten Server, können mit Site Recovery die Replikation und das Failover in Azure oder an einem sekundären Standort durchgeführt werden.
- Für größere Bereitstellungen kann Site Recovery in Exchange-Datenbankverfügbarkeitsgruppen (DAGs) integriert werden.
- Exchange-Datenbankverfügbarkeitsgruppen sind die empfohlene Lösung für die Exchange-Notfallwiederherstellung in einem Unternehmen. Site Recovery-Wiederherstellungspläne können Datenbankverfügbarkeitsgruppen zum Orchestrieren von DAG-Failovern zwischen Standorten enthalten.


[Erfahren Sie mehr](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) über das Schützen von Exchange.

## Schützen von SAP

Verwenden Sie Site Recovery wie folgt zum Schützen Ihrer SAP-Bereitstellung:

- Sie können für den Schutz der gesamten SAP-Bereitstellung sorgen, indem Sie unterschiedliche Bereitstellungsebenen in Azure oder an einem sekundären Standort replizieren.
- Vereinfachen der Cloudmigration mithilfe von Site Recovery zum Migrieren Ihrer SAP-Bereitstellung in Azure.
- Vereinfachen Sie Entwicklung und Tests von SAP-Anwendungen, indem Sie für Test- und Debugzwecke eine der Produktionsversion ähnliche Kopie bedarfsgesteuert erstellen.

[Erfahren Sie mehr](http://aka.ms/asr-sap) über das Schützen von SAP.

## Nächste Schritte

[Vorbereiten](site-recovery-best-practices.md) der Site Recovery-Bereitstellung

<!---HONumber=AcomDC_0330_2016-->