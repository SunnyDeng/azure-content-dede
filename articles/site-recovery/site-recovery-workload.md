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
	ms.date="12/01/2015" 
	ms.author="raynew"/>

# Welche Workloads können mit Azure Site Recovery geschützt werden?

Azure Site Recovery ermöglicht Kunden die Bereitstellung anwendungsabhängiger Verfügbarkeitslösungen zur Unterstützung der Strategie Ihres Unternehmens für Geschäftskontinuität und Notfallwiederherstellung. Azure Site Recovery ermöglicht für Windows Server- oder Linux-Apps, Microsoft-Unternehmensanwendungen und Angebote anderer Anbieter die Notfallwiederherstellung, bedarfsgesteuerte Entwicklungs- und Testumgebungen sowie die Cloudmigration.

Site Recovery ist in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory integriert. Microsoft arbeitet auch eng mit führenden Anbietern zusammen, z. B. Oracle, SAP, IBM und Red Hat, um sicherzustellen, dass seine Programme und Dienste auf Microsoft-Plattformen reibungslos funktionieren, z. B. Azure und Hyper-V. Sie können Ihre Lösung für die Notfallwiederherstellung für jede spezifische Anwendung anpassen.


##Wichtige Features
Azure Site Recovery-Features mit einem Beitrag zu Ihrer Schutz- und Wiederherstellungsstrategie auf Anwendungsebene sind u. a.:

- Nahezu synchrone Replikation mit geringen RPOs von bis zu 30 Sekunden, um die Anforderungen der meisten kritischen Anwendungen zu erfüllen.
- Anwendungskonsistente Momentaufnahmen für Anwendungen mit einer oder mehreren Ebenen.
- Integration mit SQL Server AlwaysOn und Partnerschaft mit anderen Replikationstechnologien auf Anwendungsebene, einschließlich Active Directory-Replikation, SQL AlwaysOn, Exchange-Datenbankverfügbarkeitsgruppen und Oracle Data Guard.
- Flexible Wiederherstellungspläne, die die Wiederherstellung des gesamten Anwendungsstapels mit einem einzigen Mausklick ermöglichen und externe Skripts oder manuelle Aktionen enthalten. 
- Die erweiterte Netzwerkverwaltung in Site Recovery und Azure vereinfacht das Erfüllen von Netzwerkanforderungen einer App, z. B. das Reservieren von IP-Adressen, die Load Balancer-Konfiguration und Integration von Azure Traffic Manager für Netzwerkswitchovers mit kurzem RTO.
-  Eine umfassende Automatisierungsbibliothek bietet produktionsbereite, anwendungsspezifischen Skripts, die heruntergeladen und in Site Recovery integriert werden können.



##Übersicht über Workloads

Site Recovery-Replikationstechnologien sind mit allen Anwendungen kompatibel, die auf einem virtuellen Computer ausgeführt werden. Wir haben außerdem in Zusammenarbeit mit Anwendungsproduktteams zusätzliche Tests durchgeführt, um die Unterstützung der einzelnen Anwendungen noch weiter zu optimieren.

**Workload** | <p>** Replizieren von Hyper-V-VMs **</p> <p>** (an einen sekundären Standort)**</p> | <p>**Replizieren von Hyper-V-VMs **</p> <p>** (in Azure)**</p> | <p>**Replizieren von Hyper-V-VMs **</p> <p>** (an einen sekundären Standort)**</p>| <p>**Replizieren von Hyper-V-VMs **</p><p>** (in Azure)**</p> ---|---|---|---|--- Active Directory, DNS | J | J | J | J Web-Apps (IIS, SQL) | J | J | J | J SCOM | J | J | J | J SharePoint | J | J | J | J <p>SAP</p><p>Replizieren eines SAP-Standorts in Azure für Nichtcluster</p> | J (von Microsoft getestet) | J (von Microsoft getestet) | J (von Microsoft getestet) | J (von Microsoft getestet) Exchange (keine DAG) | J | Bald verfügbar | J | J Remotedesktop/VDI | J | J | J | Nicht verfügbar<p>Linux</p> <p>(Betriebssystem und Apps)</p> | J (von Microsoft getestet) | J (von Microsoft getestet) | J (von Microsoft getestet) | J (von Microsoft getestet) Dynamics AX | J | J | J | J Dynamics CRM | J | Bald verfügbar | J | Bald verfügbar Oracle | J (von Microsoft getestet) | J (von Microsoft getestet) | J (von Microsoft getestet) | Bald verfügbar (von Microsoft getestet) Windows File Server | J | J | J | J

##Schützen von Active Directory und DNS

Alle Unternehmensanwendungen, z. B. SharePoint, Dynamics AX und SAP, hängen von einer Active Directory- und DNS-Infrastruktur ab. Als Teil Ihrer Lösung für Geschäftskontinuität und Notfallwiederherstellung müssen Sie diese Infrastrukturkomponenten schützen und wiederherstellen, ehe Sie Ihre Workloads und Apps wiederherstellen.

Mit Site Recovery können Sie für Active Directory und DNS einen vollständig automatisierten Notfallwiederherstellungsplan erstellen. Wenn Sie beispielsweise an Ihrem primären Standort Active Directory für mehrere Anwendungen wie SharePoint und SAP nutzen und für den gesamten Standort ein Failover wünschen, können Sie für Active Directory ein Failover mithilfe eines Wiederherstellungsplans und anschließend für die von Active Directory abhängigen Anwendungen ein Failover mithilfe anwendungsspezifischer Wiederherstellungspläne ausführen.

[Weitere Informationen](http://aka.ms/asr-ad)

##Schützen von SQL Server

SQL Server stellt die Grundlage für Datendienste vieler Geschäftsanwendungen in einem lokalen Datencenter dar. Site Recovery und SQL Server-Technologien für Hochverfügbarkeit und Notfallwiederherstellung ergänzen einander und können gemeinsam zum Bieten eines umfassenden Schutzes von Unternehmensanwendungen mit mehreren Ebenen verwendet werden. Site Recovery für SQL Server-Umgebungen bietet folgende Vorteile:

- Einfaches Schützen und Replizieren eigenständiger oder gruppierter SQL Server-Computer in Azure oder an einen sekundären Standort. Einfache und kostengünstige Notfallwiederherstellungslösung für viele SQL Server-Versionen und -Editionen.
- Integration mit SQL AlwaysOn-Verfügbarkeitsgruppen, Verwalten von Failover- und Failbackprozessen mithilfe von Wiederherstellungsplänen in Azure Site Recovery.
- Umfassende Wiederherstellungspläne für den gesamten Anwendungsstapel, einschließlich SQL Server-Datenbanken.
- Zentrales Hochskalieren von SQL Server bei Lastspitzen mithilfe von Azure Site Recovery, indem in Azure größere virtuelle-IaaS-Computer verwendet werden.
- Testen von SQL Server in Azure oder an einem sekundären Standort mithilfe von Azure Site Recovery. Testfailover können für Kompatibilitätsüberprüfungen von Analysedaten verwendet werden, ohne die Produktionsumgebung zu beeinträchtigen.

[Weitere Informationen](http://aka.ms/asr-sql)

##Schützen von SharePoint

Azure Site Recovery unterstützt Sie beim Schützen Ihrer SharePoint-Bereitstellung. Site Recovery ermöglicht Ihnen Folgendes

- Wegfall der Notwendigkeit und zugehörigen Infrastrukturkosten einer Standbyfarm für die Notfallwiederherstellung. Mithilfe von Site Recovery können Sie den Schutz der gesamten Farm (Web-, App- und Datenbankebene) mithilfe von Azure oder eines sekundären Standorts ermöglichen.
- Vereinfachen der Bereitstellung und Verwaltbarkeit von Anwendungen. Updates, die am primären Standort bereitgestellt werden, werden automatisch repliziert und sind verfügbar, sobald die Farm am sekundären Standort wiederhergestellt wurde. So wird der komplexe Verwaltungsaufwand vermieden, der für das Halten einer Standbyfarm auf dem neuesten Stand anfällt, wodurch die Gesamtbetriebskosten gesenkt werden.
- Vereinfachen von Entwicklung und Tests von SharePoint-Anwendungen, indem für Test- und Debugzwecke eine der Produktionsversion ähnliche Replikatumgebung bedarfsgesteuert erstellt wird.
- Vereinfachen des Übergangs in die Cloud, indem Sie Site Recovery zum Migrieren von SharePoint-Bereitstellungen in Azure verwenden.

[Weitere Informationen](http://aka.ms/asr-sharepoint)


## Schützen von Dynamics AX

Azure Site Recovery unterstützt Sie beim Schützen Ihrer Dynamics AX ERP-Bereitstellung.

- Mit Site Recovery können Sie für den Schutz der gesamten Dynamics AX-Umgebung (Web- und AOS-Ebene, Datenbankebenen, SharePoint) basierend auf Azure oder einem sekundären Standort sorgen.
- Vereinfachen der Migration in die Cloud, indem Sie Site Recovery zum Migrieren von Dynamics AX-Bereitstellungen in Azure verwenden.
- Vereinfachen von Entwicklung und Tests von Dynamics AX-Anwendungen, indem für Test- und Debugzwecke eine der Produktionsversion ähnliche Kopie bedarfsgesteuert erstellt wird.

[Weitere Informationen](http://aka.ms/asr-dynamics)

## Schützen von RDS 
Mit Remotedesktopdiensten werden eine Virtual Desktop Infrastructure (VDI) sowie sitzungsbasierte Desktops und Anwendungen ermöglicht, damit Benutzer an jedem Ort arbeiten können. Mit Site Recovery können Sie für den Schutz von verwalteten oder nicht verwalteten virtuellen Desktops eines Pools an einem sekundären Standort und von Remoteanwendungen und -sitzungen an einem sekundären Standort oder in Azure sorgen.

[Weitere Informationen](http://aka.ms/asr-rds)


## Schützen von Exchange

Microsoft Exchange bietet integrierte Unterstützung für Hochverfügbarkeit und Notfallwiederherstellung. Exchange-Datenbankverfügbarkeitsgruppen und Azure Site Recovery können zusammenarbeiten.

- Exchange-Datenbankverfügbarkeitsgruppen sind die empfohlene Lösung für die Exchange-Notfallwiederherstellung in einem Unternehmen. Wiederherstellungspläne in Site Recovery können Datenbankverfügbarkeitsgruppen zum Orchestrieren von deren Failover zwischen Standorten enthalten.
- Für kleine Bereitstellungen, z. B. einen einzelnen oder nicht gruppierten Server, kann Site Recovery Schutz bieten und für das Failover in Azure oder an einen sekundären Standort sorgen.

[Weitere Informationen](http://aka.ms/asr-exchange)

## Schützen von SAP

Verwenden von Site Recovery zum Schutz Ihrer SAP-Bereitstellung:

- Sie können für den Schutz der gesamten SAP-Bereitstellung mit unterschiedlichen Ebenen in Azure oder an einem sekundären Standort sorgen.
- Vereinfachen der Cloudmigration mithilfe von Site Recovery zum Migrieren Ihrer SAP-Bereitstellung in Azure.
- Vereinfachen Sie Entwicklung und Tests von SAP-Anwendungen, indem Sie für Test- und Debugzwecke eine der Produktionsversion ähnliche Kopie bedarfsgesteuert erstellen.

[Weitere Informationen](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_1203_2015-->