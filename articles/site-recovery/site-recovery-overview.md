<properties
	pageTitle="Was ist Site Recovery? | Microsoft Azure" 
	description="Enthält eine Übersicht über den Azure Site Recovery-Dienst, und es wird beschrieben, wie der Dienst bereitgestellt werden kann." 
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
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  Was ist Site Recovery?

Willkommen bei Azure Site Recovery! Beginnen Sie mit diesem Artikel, um sich schnell einen Überblick über den Site Recovery-Dienst und seinen möglichen Beitrag zu Ihrer Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) zu verschaffen.

Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Ressourcen-Manager und klassische Bereitstellungen](../resource-manager-deployment-model.md). Dieser Artikel gilt für beide Modelle. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

## Übersicht

Ein wichtiger Teil der BCDR-Strategie Ihrer Organisation ist die Ermittlung, wie geschäftliche Workloads und Apps verfügbar gehalten werden können, wenn geplante und ungeplante Ausfälle auftreten.

Site Recovery unterstützt dies durch die Orchestrierung von Replikation, Failover und Wiederherstellung von Workloads und Apps, damit diese Komponenten über einen sekundären Standort zur Verfügung stehen, wenn der primäre Standort ausfällt.

## Gründe für Azure Site Recovery 

Site Recovery hat für Ihr Unternehmen folgende Vorteile:

- **Vereinfachung Ihrer BCDR-Strategie:** Site Recovery vereinfacht die Replikation, das Failover und die Wiederherstellung mehrerer Geschäftsworkloads und -Apps von einem einzelnen Standort. Site Recovery orchestriert die Replikation und das Failover, aber das Tool fängt keine Anwendungsdaten ab und besitzt auch keinerlei Informationen hierzu.
- **Bereitstellung einer flexiblen Replikation:** Mit Site Recovery können Sie Workloads replizieren, die auf virtuellen Hyper-V-Computern, virtuellen VMware-Computern und physischen Windows bzw. Linux-Servern ausgeführt werden. 
- **Einfaches Failover und leichte Wiederherstellung:** Site Recovery verfügt über Testfailover, mit denen Übungen zur Notfallwiederherstellung unterstützt werden, ohne dass sich dies auf Produktionsumgebungen auswirkt. Außerdem können Sie geplante Failover ohne Datenverlust für erwartete Ausfälle oder ungeplante Failover mit minimalem Datenverlust (je nach Replikationsfrequenz) für unerwartete Notfälle ausführen. Nach dem Failover können Sie ein Failback zu Ihren primären Standorten durchführen. Site Recovery verfügt über Wiederherstellungspläne, die Skripts und Azure Automation-Arbeitsmappen enthalten können, sodass Sie das Failover und die Wiederherstellung von Anwendungen mit mehreren Ebenen anpassen können. 
- **Beseitigung des sekundären Rechenzentrums:** Sie können die Replikation zu einem sekundären lokalen Standort oder zu Azure durchführen. Bei Verwendung von Azure als Ziel für die Notfallwiederherstellung werden die Kosten und die Komplexität für die Verwaltung eines sekundären Standorts beseitigt, und replizierte Daten werden in Azure Storage gespeichert, sodass die damit verbundene Resilienz genutzt werden kann.
- **Integration in vorhandene BCDR-Technologien:** Site Recovery kann zusammen mit anderen BCDR-Features für Anwendungen eingesetzt werden. Beispielsweise können Sie Site Recovery verwenden, um das SQL Server-Back-End von geschäftlichen Workloads zu schützen, einschließlich der nativen Unterstützung für SQL Server AlwaysOn zum Verwalten des Failovers von Verfügbarkeitsgruppen. 

## Was kann ich replizieren?

Unten ist zusammengefasst, was Sie mit Site Recovery replizieren können.

![Lokal zu lokal](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **REPLIKATIONSQUELLE** | **REPLIKATIONSZIEL** | **ARTIKEL**
---|---|---|---
Workloads auf VMware-VMs | Lokaler VMware-Server | Azure-Speicher | [Bereitstellen](site-recovery-vmware-to-azure-classic.md)
Workloads auf VMware-VMs | Lokaler VMware-Server | Sekundärer VMware-Standort | [Bereitstellen](site-recovery-vmware-to-vmware.md) 
Workloads auf Hyper-V-VMs | Lokaler Hyper-V-Hostserver in VMM-Cloud | Azure-Speicher | [Bereitstellen](site-recovery-vmm-to-azure.md)
Workloads auf Hyper-V-VMs | Lokaler Hyper-V-Hostserver in VMM-Cloud | Sekundärer VMM-Standort | [Bereitstellen](site-recovery-vmm-to-vmm.md)
Workloads auf Hyper-V-VMs | Lokaler Hyper-V-Hostserver in VMM-Cloud mit SAN-Speicher| Sekundärer VMM-Standort mit SAN-Speicher | [Bereitstellen](site-recovery-vmm-san.md)
Workloads auf Hyper-V-VMs | Lokaler Hyper-V-Standort (kein VMM) | Azure-Speicher | [Bereitstellen](site-recovery-hyper-v-site-to-azure.md)
Workloads auf physischen Windows-/Linux-Servern | Lokaler physischer Server | Azure-Speicher | [Bereitstellen](site-recovery-vmware-to-azure-classic.md)
Workloads auf physischen Windows-/Linux-Servern | Lokaler physischer Server | Sekundäres Datencenter | [Bereitstellen](site-recovery-vmware-to-vmware.md) 


## Welche Workloads können geschützt werden?

Site Recovery kann anwendungsorientierte BCDR-Vorgänge unterstützen, damit Workloads und Apps auch bei Ausfällen auf konsistente Weise ausgeführt werden können. Site Recovery bietet Folgendes:

- **Anwendungskonsistente Momentaufnahmen:** Replikation mit anwendungskonsistenten Momentaufnahmen für Single- oder N-Tier-Apps. **Nahezu synchrone Replikation:** Replikationsfrequenz von bis zu lediglich 30 Sekunden für Hyper-V und fortlaufende Replikation für VMware. **Integration in SQL Server AlwaysOn:** Sie können das Failover von Verfügbarkeitsgruppen in Site Recovery-Wiederherstellungsplänen verwalten. 
- **Flexible Wiederherstellungspläne:** Sie können Wiederherstellungspläne mit externen Skripts, manuellen Aktionen und Azure Automation-Runbooks erstellen und anpassen, die Ihnen die Möglichkeit verschaffen, einen gesamten Anwendungsstapel mit nur einem Klick wiederherzustellen.
- **Automation-Bibliothek:** Eine umfassende Azure Automation-Bibliothek verfügt über produktionsbereite, anwendungsspezifische Skripts, die heruntergeladen und in Site Recovery integriert werden können. -** Einfache Netzwerkverwaltung**: Mit der erweiterten Netzwerkverwaltung in Site Recovery und Azure werden die Anforderungen des Anwendungsnetzwerks vereinfacht, z. B. das Reservieren von IP-Adressen, das Konfigurieren von Lastenausgleichsmodulen und das Integrieren von Azure Traffic Manager zur Erzielung effizienter Netzwerkwechsel.


## Nächste Schritte

- Unter [Welche Workloads kann Site Recovery schützen?](site-recovery-workload.md) finden Sie weitere Informationen.
- Weitere Informationen zur Site Recovery-Architektur finden Sie unter [Wie funktioniert Site Recovery?](site-recovery-components.md)
 

<!---HONumber=AcomDC_0316_2016-->