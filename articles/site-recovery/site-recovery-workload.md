<properties
	pageTitle="Leitfaden zu Site Recovery-Workloads | Microsoft Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung lokaler virtueller Computer und physischer Server zu Azure oder zu einem sekundären lokalen Standort." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/21/2015" 
	ms.author="pratshar"/>

# Leitfaden zu Site Recovery-Workloads

Mithilfe von Azure Site Recovery können Kunden anwendungsorientierte Verfügbarkeitslösungen bereitstellen. Sie können Azure Site Recovery zum Umsetzen der Notfallwiederherstellung, von On-Demand-Entwicklungs- und -Testumgebungen oder Cloudmigrationen verwenden – unabhängig davon, ob es sich um Windows Server- oder Linux-basierte Anwendungen, Erstanbieter-Unternehmensanwendungen von Microsoft oder Angebote anderer Anbieter handelt.

Microsoft verfügt über fundiertes Fachwissen und viel Erfahrung in Bezug auf die Entwicklung von erstklassigen Unternehmensanwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory. Microsoft arbeitet auch eng mit führenden Anbietern zusammen, z. B. Oracle, SAP, IBM und Red Hat, um sicherzustellen, dass seine Programme und Dienste auf Microsoft-Plattformen reibungslos funktionieren, z. B. Azure und Hyper-V. Wir haben diese einzigartigen Stärken genutzt, um ein tiefes Verständnis der Verfügbarkeitsanforderungen jeder Anwendung zu entwickeln. Sie können somit professionelle Lösungen für die Notfallwiederherstellung und hohe Verfügbarkeit bereitstellen, die für jeden Anwendungsfall angepasst werden können.


##Wichtige Features
Bei Azure Site Recovery-Features wurde auf den Schutz und die Wiederherstellung auf Anwendungsebene geachtet:

- Nahezu synchrone Replikation mit geringen RPOs von bis zu 30 Sekunden, um die Anforderungen der meisten kritischen Anwendungen zu erfüllen
- Anwendungskonsistente Momentaufnahmen für Single- oder N-Tier-Anwendungen
- Integration in Replikation auf Anwendungsebene. Nutzen von führenden Angeboten auf Anwendungsebene, z. B. AD-Replikation, SQL Always On, Exchange-Datenbankverfügbarkeitsgruppen und Oracle Data Guard
- Flexible Wiederherstellungspläne ermöglichen die Wiederherstellung des gesamten Anwendungsstapels mit einem einzigen Mausklick, einschließlich der Ausführung externer Skripts oder sogar manueller Aktionen 
- Erweiterte Netzwerkverwaltung in ASR und Azure automatisiert alle speziellen Netzwerkkonfigurationen Ihrer Anwendung: Reservieren von IP-Adressen, Konfigurieren des Lastenausgleichs oder Verwenden des Traffic Manager von Microsoft für Netzwerk-Switchovers mit niedrigem RTO
- Umfassende Automatisierungsbibliothek mit produktionsbereiten, anwendungsspezifischen Skripts (zum Herunterladen und Integrieren in Ihre ASR-basierten Lösungen)


##Zusammenfassung des Workload-Leitfadens

Die ASR-Replikationstechnologien sind mit jeder Anwendung kompatibel, die auf einem virtuellen Computer ausgeführt wird. Wir haben in Zusammenarbeit mit Anwendungsproduktteams zusätzliche Tests durchgeführt, um die Unterstützung der einzelnen Anwendungen noch weiter zu optimieren.

**Workload** | <p>**Replizieren virtueller Hyper-V-Computer**</p> <p>**(Ziel: sekundärer Standort)**</p> | <p>**Replizieren virtueller Hyper-V-Computer**</p> <p>**(Ziel: Azure)**</p> | <p>**Replizieren virtueller VMware-Computer**</p> <p>**(Ziel: sekundärer Standort)**</p> | <p>**Replizieren virtueller VMware-Computer**</p><p>**(Ziel: Azure)****</p>
---|---|---|---|---
Active Directory, DNS | J | J | J | J
Web-Apps (IIS, SQL) | J | J | J | J
SCOM | J | J | J | J
SharePoint | J | J | J | J
<p>SAP</p><p>Replizieren eines SAP-Standorts zu Azure (kein Cluster)</p> | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft)
Exchange (kein DAG) | J | In Kürze verfügbar | J | J
Remotedesktop/VDI | J | J | J | N/A
<p>Linux</p> <p>(Betriebssystem und Apps)</p> | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft)
Dynamics AX | J | J | J | J
Dynamics CRM | J | In Kürze verfügbar | J | In Kürze verfügbar
Oracle | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft)
Windows-Dateiserver | J | J | J | J

##Active Directory und DNS

Alle Unternehmensanwendungen, z. B. SharePoint, Dynamics AX und SAP, basieren auf einer AD- und DNS-Infrastruktur, um richtig funktionieren zu können. Beim Erstellen einer Lösung für die Notfallwiederherstellung für eine Anwendung dieser Art ist es wichtig, AD zu schützen und wiederherzustellen, bevor die anderen Komponenten der Anwendung bei einer Störung wieder verfügbar sind.

Mit Azure Site Recovery können Sie für Ihre AD-Umgebung einen vollständig automatisierten Notfallwiederherstellungsplan erstellen. Falls Sie an Ihrem primären Standort eine AD-Umgebung für mehrere Anwendungen verwenden, z. B. SharePoint und SAP, und Sie sich für ein Failover des gesamten Standorts entscheiden, können Sie das Failover mit dem Notfallwiederherstellungsplan zuerst für AD durchführen und anschließend das Failover für die anderen Anwendungen durchführen, indem Sie spezielle Wiederherstellungspläne verwenden.

Ausführliche Informationen zum [Bereitstellen von Azure Site Recovery für AD](http://aka.ms/asr-ad) finden Sie im verlinkten Dokument.

##SQL Server
Microsoft SQL Server stellt die Grundlage für viele Erstanbieter-, Drittanbieter- und benutzerdefinierte Branchenanwendungen der Unternehmensklasse dar, die im lokalen Rechenzentrum eines Kunden ausgeführt werden. Anwendungen wie SharePoint, Dynamics und SAP verwenden SQL Server zum Bereitstellen von Datendiensten. Azure Site Recovery und SQL Server-HA/DR-Technologie stellen eine Ergänzung dar und können zum Bereitstellen des umfassenden Schutzes von Unternehmensanwendungen mit mehreren Ebenen verwendet werden. Azure Site Recovery bietet folgende Vorteile für SQL Server-Umgebungen:

- Einfaches Schützen von eigenständigen oder geclusterten SQL-Servern für Azure oder einen sekundären Standort. Erweitern einer einfachen, kostengünstigen DR-Lösung für alle Versionen und Editionen von SQL Server
- Integrieren von SQL Always On-Verfügbarkeitsgruppen, einer erstklassigen Lösung für Notfallwiederherstellung und Verwalten des Failover/Failback-Vorgangs mit ASR-Wiederherstellungsplänen
- Erstellen von umfassenden Wiederherstellungsplänen für den gesamten Anwendungsstapel, einschließlich SQL-Datenbanken
- Verwenden von Azure Site Recovery zum zentralen Hochskalieren von SQL Server bei Belastungsspitzen, indem diese in Azure in größere IaaS-VM-Größen umgewandelt werden (Bursting)
- Verwenden von Azure Site Recovery zum Erstellen von Testkopien von SQL Server in Azure oder an einem sekundären Standort. Verwenden dieser Kopie für Analysen und Überprüfungen der Datenkompatibilität ohne Beeinträchtigung der Produktionsumgebung

Ausführliche Informationen zum [Bereitstellen von Azure Site Recovery für SQL](http://aka.ms/asr-sql) finden Sie im verlinkten Dokument.

##SharePoint
SharePoint ist eine beliebte Anwendung für die Zusammenarbeit von Unternehmen, indem Intranetportale, Dokument- und Dateiverwaltung, soziale Netzwerke, Websites und Suchfunktionen für Unternehmen bereitgestellt werden. Es ist auch eine Anwendungsplattform für die einfache Bereitstellung von benutzerdefinierten Anwendungen und Workflows. Mit Azure Site Recovery für SharePoint haben Sie folgende Möglichkeiten:

- Beseitigen Sie die Anforderung für eine Standby-Farm zur Notfallwiederherstellung und die damit verbundenen Infrastrukturkosten. Mithilfe von ASR können Sie den Schutz der gesamten Farm (Web-, App- und Datenbankebene) auf Basis von Azure oder eines sekundären Standorts ermöglichen.
- Vereinfachen Sie die Bereitstellung und Verwaltbarkeit von Anwendungen. Updates, die am primären Standort bereitgestellt werden, werden automatisch repliziert und sind verfügbar, wenn die Farm am sekundären Standort wiederhergestellt wird. So wird der komplexe Verwaltungsaufwand vermieden, der für das Halten einer Standby-Farm auf dem neuesten Stand anfällt, und die Gesamtbetriebskosten werden gesenkt.
- Vereinfachen Sie die Entwicklung und das Testing von SharePoint-Anwendungen, indem Sie eine der Produktionsversion ähnelnde Kopie bedarfsgesteuert für Test- und Debugzwecke erstellen.
- Vereinfachen Sie den Weg in die Cloud, indem Sie ASR zum Migrieren von SharePoint-Bereitstellungen zu Azure verwenden.

Ausführliche Informationen zum [Bereitstellen von Azure Site Recovery für SharePoint](http://aka.ms/asr-sharepoint) finden Sie im verlinkten Dokument.


##Dynamics AX
Microsoft Dynamics AX ist eine führende Enterprise Resource Planning (ERP)-Lösung, die einfach zu erlernen und zu verwenden ist. Damit können Sie schneller Nutzen schaffen, Geschäftschancen nutzen und die Beteiligung und Innovationen von Benutzern in der gesamten Organisation fördern.

- Mit ASR können Sie für den Schutz der gesamten Dynamics AX-Umgebung (Web- und AOS-Ebene, Datenbankebenen, SharePoint) basierend auf Azure oder einem sekundären Standort sorgen.
- Vereinfachen Sie den Weg in die Cloud, indem Sie ASR zum Migrieren von Dynamics AX-Bereitstellungen zu Azure verwenden.
- Vereinfachen Sie die Entwicklung und das Testing von Dynamics-Anwendungen, indem Sie eine der Produktionsversion ähnelnde Kopie bedarfsgesteuert für Test- und Debugzwecke erstellen.

Ausführliche Informationen zum [Bereitstellen von Azure Site Recovery für Dynamics AX](http://aka.ms/asr-dynamics) finden Sie im verlinkten Dokument.

## RDS 
Windows Server-Remotedesktopdienste (RDS) sorgen für eine Beschleunigung und Erweiterung von Desktop- und Anwendungsbereitstellungen auf allen Geräten und eine Verbesserung der Effizienz von Remotearbeitern. Gleichzeitig tragen sie dazu bei, wichtiges geistiges Eigentum zu schützen und die Einhaltung von Bestimmungen zu vereinfachen. Mit Remotedesktopdiensten werden eine Virtual Desktop Infrastructure (VDI) sowie sitzungsbasierte Desktops und Anwendungen ermöglicht, damit Benutzer an jedem Ort arbeiten können.

Mit ASR können Sie für den Schutz von verwalteten oder nicht verwalteten virtuellen Desktops eines Pools an einem sekundären Standort und von Remoteanwendungen und -sitzungen an einem sekundären Standort oder unter Azure sorgen.

Ausführliche Informationen zum [Bereitstellen von Azure Site Recovery für RDS/VDI](http://aka.ms/asr-rds) finden Sie im verlinkten Dokument.


## Exchange
Microsoft Exchange ist die bevorzugte Software, die von Unternehmen zum Hosten ihrer Nachrichten- und E-Mail-Dienste verwendet wird. Mit Exchange wird der Zugriff auf die Kommunikation über PC, Smartphone oder Browser hinweg sichergestellt, während gleichzeitig einzigartige Zuverlässigkeit, Verwaltbarkeit und der Schutz von Daten erzielt wird.

Microsoft Exchange verfügt über systemeigene Unterstützung von Lösungen für hohe Verfügbarkeit und Notfallwiederherstellung der Unternehmensklasse. Exchange-Datenbankverfügbarkeitsgruppen und Azure Site Recovery-Technologie stellen hierzu die Ergänzung dar.

- Exchange-Datenbankverfügbarkeitsgruppen sind die empfohlene Bereitstellungsoption, um eine erstklassige Notfallwiederherstellung für Exchange-Bereitstellungen zu ermöglichen. ASR-Wiederherstellungspläne können Datenbankverfügbarkeitsgruppen (DAGs) zum Orchestrieren von DAG-Failovers zwischen Standorten enthalten.
- Für kleine Bereitstellungen, einen einzelnen Server oder nicht geclusterte Server, kann Azure Site Recovery Schutz bieten und für einzelne Server das Failover auf Azure oder einen sekundären Standort durchführen.

Ausführliche Informationen zum [Bereitstellen von Azure Site Recovery für Exchange](http://aka.ms/asr-exchange) finden Sie im verlinkten Dokument.

## SAP

SAP ist eine führende Enterprise Resource Planning (ERP)-Software, die weltweit von vielen Organisationen genutzt wird. SAP wird in Unternehmen häufig als eine der Anwendungen angesehen, die am wichtigsten für den Erfolg sind. Die Architektur und Abläufe dieser Anwendungen ist in der Regel sehr komplex, und es ist sehr wichtig, dass Sie die BCDR-Anforderungen erfüllen.

- Mit ASR können Sie für den Schutz der gesamten SAP-Bereitstellung mit unterschiedlichen Ebenen unter Azure oder an einem sekundären Standort sorgen.
- Vereinfachen Sie den Weg in die Cloud, indem Sie ASR zum Migrieren von SAP-Bereitstellungen zu Azure verwenden.
- Vereinfachen Sie die Entwicklung und das Testing von SAP-Anwendungen, indem Sie eine der Produktionsversion ähnelnde Kopie bedarfsgesteuert für Test- und Debugzwecke erstellen.

Ausführliche Informationen zum [Bereitstellen von Azure Site Recovery für SAP NetWeaver](http://aka.ms/asr-sap) finden Sie im verlinkten Dokument.

<!----HONumber=Sept15_HO4-->