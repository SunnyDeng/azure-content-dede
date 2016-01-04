<properties
	pageTitle="Was ist Site Recovery? | Microsoft Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung lokaler virtueller Computer und physischer Server zu Azure oder zu einem sekundären lokalen Standort." 
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
	ms.date="12/14/2015" 
	ms.author="raynew"/>

#  Was ist Site Recovery?

Site Recovery ist ein Azure-Dienst zur Optimierung Ihrer Strategie für Geschäftskontinuität und Notfallwiederherstellung. Dazu erfolgt eine Orchestrierung der Replikation Ihrer lokalen Server und virtuellen Computer in ein sekundäres Datencenter oder Azure. Site Recovery übernimmt die Replikation, und Sie können ein Failover oder eine Wiederherstellung über einen einfachen Mausklick auslösen. Im Abschnitt [Häufig gestellte Fragen](site-recovery-faq.md) finden Sie eine Liste häufig gestellter Fragen.


## Gründe für Azure Site Recovery 

- **Vereinfachung von Geschäftskontinuität und Notfallwiederherstellung**: Mit Site Recovery werden Replikation, Failover und Wiederherstellung für Ihre lokalen Workloads und Anwendungen erleichtert.
- **Flexible Replikation**: Sie können lokale Server, Hyper-V-Computer und virtuelle VMware-Maschinen replizieren. Dank der intelligenten Replikation von Site Recovery werden bei der ersten Replikation nur Datenblöcke (und nicht die gesamte virtuelle Festplatte) repliziert. Bei der laufenden Replikation werden nur geänderte Daten repliziert. Site Recovery unterstützt die Offlinedatenübertragung und kann mit WAN-Optimierern kombiniert werden. 
- **Keine Notwendigkeit sekundärer Datencenter**: Site Recovery kann die Replikation zwischen Datencentern automatisieren, bietet aber auch die Möglichkeit, auf einen sekundären lokalen Standort zu verzichten, indem eine Replikation in Azure erfolgt. Replizierte Daten werden in Azure Storage gespeichert, was für Ausfallsicherheit sorgt.


## Bereitstellungsszenarien

Die folgende Tabelle enthält eine Übersicht über die von Site Recovery unterstützten Replikationsszenarien:

**REPLIZIEREN** | **REPLIKATIONSQUELLE** | **REPLIKATIONSZIEL** | **ARTIKEL**
---|---|---|---
Virtuelle VMware-Computer | Lokaler VMware-Server | Azure-Speicher | [Bereitstellen](site-recovery-vmware-to-azure.md)
Physischer Windows-/Linux-Server | Lokaler physischer Server | Azure-Speicher | [Bereitstellen](site-recovery-vmware-to-azure.md)
Virtuelle Hyper-V-Computer | Lokaler Hyper-V-Hostserver in VMM-Cloud | Azure-Speicher | [Bereitstellen](site-recovery-vmm-to-azure.md)
Virtuelle Hyper-V-Computer | Lokaler Hyper-V-Standort (ein oder mehrere Hyper-V-Hostserver) | Azure-Speicher | [Bereitstellen](site-recovery-hyper-v-site-to-azure.md)
Lokale Hyper-V-Computer| Lokaler Hyper-V-Hostserver in VMM-Cloud | Lokaler Hyper-V-Hostserver in VMM-Cloud in sekundärem Datencenter | [Bereitstellen](site-recovery-vmm-to-vmm.md)
Virtuelle Hyper-V-Computer | Lokaler Hyper-V-Hostserver in VMM-Cloud mit SAN-Speicher| Lokaler Hyper-V-Hostserver in VMM-Cloud mit SAN-Speicher in sekundärem Datencenter | [Bereitstellen](site-recovery-vmm-san.md)
Virtuelle VMware-Computer | Lokaler VMware-Server | Sekundäres in VMware ausgeführtes Datencenter | [Bereitstellen](site-recovery-vmware-to-vmware.md) 
Physischer Windows-/Linux-Server | Lokaler physischer Server | Sekundäres Datencenter | [Bereitstellen](site-recovery-vmware-to-vmware.md) 

Diese werden in den folgenden Diagrammen zusammengefasst.

![Lokal zu lokal](./media/site-recovery-overview/asr-overview-graphic.png)

## Welche Workloads können geschützt werden?

Site Recovery unterstützt geräteabhängige Geschäftskontinuität. Mit Site Recovery können Sie die Notfallwiederherstellung für Windows- und Drittanbieter-Apps orchestrieren. Dieser anwendungsabhängige Schutz bietet Folgendes:


- Nahezu synchrone Replikation mit kurzen RPOs von bis zu 30 Sekunden für Hyper-V und fortlaufende Replikation für VMware zum Erfüllen der Anforderungen der wichtigsten Anwendungen.
- Anwendungskonsistente Momentaufnahmen für Anwendungen mit einer oder mehreren Ebenen.
- Integration mit SQL Server AlwaysOn, Partnerschaft mit anderen Replikationstechnologien auf Anwendungsebene, einschließlich Active Directory-Replikation, Exchange-Datenbankverfügbarkeitsgruppen und Oracle Data Guard.
- Flexible Wiederherstellungspläne, die die Wiederherstellung des gesamten Anwendungsstapels mit einem einzigen Mausklick ermöglichen und externe Skripts oder manuelle Aktionen enthalten. 
- Die erweiterte Netzwerkverwaltung in Site Recovery und Azure vereinfacht das Erfüllen von Netzwerkanforderungen einer App, z. B. das Reservieren von IP-Adressen, die Load Balancer-Konfiguration und Integration von Azure Traffic Manager für Netzwerkswitchovers mit kurzem RTO.
- Eine umfassende Automatisierungsbibliothek bietet produktionsbereite, anwendungsspezifische Skripts, die heruntergeladen und in Site Recovery integriert werden können.  


Unter [Welche Workloads kann Site Recovery schützen?](site-recovery-workload.md) finden Sie weitere Informationen.


## Nächste Schritte

Nach dieser Übersicht erfahren Sie [hier](site-recovery-components.md) mehr über die Site Recovery-Architektur.
 

<!---HONumber=AcomDC_1217_2015-->