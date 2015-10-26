<properties
	pageTitle="Übersicht über Site Recovery" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung lokaler virtueller Computer und physischer Server zu Azure oder zu einem sekundären lokalen Standort." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="08/05/2015" 
	ms.author="raynew"/>

#  Übersicht über Site Recovery

Site Recovery ist Teil einer Lösung für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), die Ihre lokalen physischen Server und Ihre virtuellen Computer mittels Orchestrierung und Automatisierung von Replikation und Failover zu Azure (oder zu einem sekundären lokalen Datencenter) schützt.

- **Vereinfachung**: Site Recovery unterstützt Sie beim Konfigurieren der Replikation sowie beim Ausführen von Failover- und Wiederherstellungsprozessen für lokale Workloads und Anwendungen und trägt so zur Vereinfachung Ihrer BCDR-Strategie bei.
- **Replikation**: Lokale Workloads können zu Azure-Speicher oder zu einem sekundären Datencenter repliziert werden. 
- **Tresor**: Die Replikation wird über einen Site Recovery-Tresor in einer Azure-Region Ihrer Wahl verwaltet. Alle Metadaten bleiben in dieser Region.
- **Metadaten**: Es werden keine Anwendungsdaten an Azure gesendet. Site Recovery benötigt nur Metadaten (etwa den Namen des virtuellen Computers und der VMM-Cloud), um die Replikation und das Failover orchestrieren zu können. 
- **Verbindung mit Azure**: Die Kommunikation zwischen Verwaltungsservern und Azure ist abhängig von Ihrem Bereitstellungsszenario. Wenn Sie beispielsweise virtuelle Computer in einer lokalen VMM-Cloud replizieren, kommuniziert der VMM-Server mit Site Recovery über eine verschlüsselte ausgehende HTTPS-Verbindung. Für die virtuellen Computer oder Hyper-V-Hosts ist keine Verbindung erforderlich.
- **Hyper-V-Replikat**: Azure Site Recovery nutzt Hyper-V-Replikat für den Replikationsprozess und kann auch die SAN-Replikation nutzen, wenn die Replikation zwischen zwei lokalen VMM-Standorten stattfindet. Dank der intelligenten Replikation von Site Recovery werden bei der ersten Replikation nur Datenblöcke (und nicht die gesamte virtuelle Festplatte) repliziert. Bei der laufenden Replikation werden nur geänderte Daten repliziert. Site Recovery unterstützt die Offlinedatenübertragung und kann mit WAN-Optimierern kombiniert werden.
- **Preise**: Preisinformationen zu Site Recovery finden Sie [hier](http://azure.microsoft.com/pricing/details/site-recovery/).


## Bereitstellungsszenarien

Die folgende Tabelle enthält eine Übersicht über die von Site Recovery unterstützten Replikationsszenarien:

**Replikationsziel** | **Replikationsquelle (lokal)** | **Details** | **Artikel**
---|---|---|---
Azure | Hyper-V-Standort | Repliziert virtuelle Computer auf lokalen, als Hyper-V-Standort definierten Hyper-V-Hostservern zu Azure. Hierzu ist kein VMM-Server erforderlich. | [Weitere Informationen](site-recovery-hyper-v-site-to-azure.md)
Azure| VMM-Server | Repliziert virtuelle Computer auf lokalen Hyper-V-Hostservern in einer VMM-Cloud zu Azure. | [Weitere Informationen](site-recovery-vmm-to-azure.md) 
Azure | Physischer Server unter Windows | Repliziert einen physischen Server unter Windows oder Linux zu Azure. | [Weitere Informationen](site-recovery-vmware-to-azure.md)
Azure | Virtueller VMware-Computer | Repliziert virtuelle VMware-Computer zu Azure | [Weitere Informationen](site-recovery-vmware-to-azure.md)
Sekundäres Datencenter | VMM-Server | Repliziert virtuelle Computer auf lokalen Hyper-V-Hostservern in einer VMM-Cloud zu einem sekundären VMM-Server in einem anderen Datencenter. | [Weitere Informationen](site-recovery-vmm-to-vmm.md)
Sekundäres Datencenter | VMM-Server mit SAN | Repliziert virtuelle Computer auf lokalen Hyper-V-Hostservern in einer VMM-Cloud mittels SAN-Replikation zu einem sekundären VMM-Server in einem anderen Datencenter.| [Weitere Informationen](site-recovery-vmm-san.md)
Sekundäres Datencenter | Einzelner VMM-Server | Repliziert virtuelle Computer auf lokalen Hyper-V-Hostservern in einer VMM-Cloud zu einer sekundären Cloud auf dem gleichen VMM-Server. | [Weitere Informationen](site-recovery-single-vmm.md) 


## Informationen zu Workloads

In [diesem Dokument](site-recovery-workload.md) finden Sie Informationen zur Verwendung von Azure Site Recovery für unterschiedliche Workloads.

## Features und Anforderungen 

Die folgende Tabelle enthält Informationen zu den wichtigsten Features von Site Recovery sowie zu deren Behandlung bei der Replikation zu Azure bzw. zu einem sekundären Standort – entweder mit dem standardmäßigen Hyper-V-Replikat oder unter Verwendung von SAN.

Feature|Replikation zu Azure|Replikation zu einem sekundären Standort (Hyper-V-Replikat)|Replikation zu einem sekundären Standort (SAN)
---|---|---|---
Datenreplikation|Metadaten zu lokalen Servern und virtuellen Computern werden im Site Recovery-Tresor gespeichert.</p> <p>Replizierte Daten werden im Azure-Speicher gespeichert.|Metadaten zu lokalen Servern und virtuellen Computern werden im Site Recovery-Tresor gespeichert.</p> <p>Replizierte Daten werden an dem Standort gespeichert, der durch den Hyper-V-Zielserver angegeben wird.|Metadaten zu lokalen Servern und virtuellen Computern werden im Site Recovery-Tresor gespeichert.</p> <p>Replizierte Daten werden im Zielarrayspeicher gespeichert.
Tresoranforderungen|Azure-Konto mit Site Recovery-Dienst|Azure-Konto mit Site Recovery-Dienst|Azure-Konto mit Site Recovery-Dienst
Replikation|Replikation des virtuellen Computers auf dem Hyper-V-Quellhost zum Azure-Speicher. Failback zum Quellort.|Replikation des virtuellen Computers auf dem Hyper-V-Quellhost zum Hyper-V-Zielhost. Failback zum Quellort.|Replikation virtueller Computer vom SAN-Quellspeichergerät zum SAN-Zielgerät. Failback zum Quellort.
Virtueller Computer|In Azure-Speicher gespeicherte VM-Festplatte|Auf Hyper-V-Host gespeicherte VM-Festplatte|In SAN-Speicherarray gespeicherte VM-Festplatte
Azure-Speicher|Zum Speichern replizierter VM-Festplatten erforderlich|Nicht zutreffend|Nicht zutreffend
SAN-Speicherarray|Nicht zutreffend|Nicht zutreffend|Das SAN-Speicherarray muss sowohl am Quell- als auch am Zielort verfügbar sein und von VMM verwaltet werden.
VMM-Server|Nur VMM-Server am Quellort.|VMM-Server am Quell- und Zielort empfohlen. Replikation zwischen Clouds auf einem einzelnen VMM-Server möglich.|VMM-Server am VMM-Quell- und -Zielort. Clouds müssen mindestens einen Hyper-V-Cluster umfassen.
VMM-Version|System Center 2012 R2<p>System Center 2012 mit SP1|System Center 2012 R2|System Center 2012 R2 mit VMM Update Rollup 5.0
VMM-Konfiguration|Einrichten von Clouds am Quell-und Zielstandort</p><p>Einrichten von VM-Netzwerken am Quell- und Zielstandort<p>Einrichten von Speicherklassifizierungen am Quell- und Zielstandort<p>Installieren des Anbieters auf den VMM-Quell- und -Zielservern|Einrichten von Clouds am Quellstandort</p><p>Einrichten des SAN-Speichers</p><p>Einrichten von VM-Netzwerken am Quellstandort</p><p>Installieren des Anbieters auf dem VMM-Quellserver</p><p>Aktivieren des Schutzes für virtuelle Computer|Einrichten von Clouds am Quell- und Zielstandort</p><p>Einrichten von VM-Netzwerken am Quell- und Zielstandort</p><p>Installieren des Anbieters auf den VMM-Quell- und -Zielservern</p><p>Aktivieren des Schutzes für virtuelle Computer
Azure Site Recovery-Anbieter</p><p>(für die HTTPS-Verbindung mit Site Recovery)|Auf VMM-Quellserver installiert|Auf VMM-Quell- und Zielservern installiert|Auf VMM-Quell- und Zielservern installiert
Azure Recovery Services-Agent</p><p>(für die HTTPS-Verbindung mit Site Recovery)|Auf Hyper-V-Hostservern installiert|Nicht erforderlich|Nicht erforderlich
Wiederherstellungspunkte für virtuellen Computer|Legen Sie Wiederherstellungspunkte nach Zeit fest.</p> <p>Gibt an, wie lange ein Wiederherstellungspunkt aufbewahrt werden soll (zwischen 0 und 24 Stunden).|Legen Sie Wiederherstellungspunkte nach Menge fest.</p> <p>Gibt an, wie viele zusätzliche Wiederherstellungspunkte aufbewahrt werden sollen (0-15). Standardmäßig wird jede Stunde ein Wiederherstellungspunkt erstellt.|In den Arrayspeichereinstellungen konfiguriert
Netzwerkzuordnung|Ordnen Sie VM-Netzwerke Azure-Netzwerken zu.</p> <p>Durch die Netzwerkzuordnung wird sichergestellt, dass alle virtuellen Computer im gleichen VM-Quellnetzwerk, für die ein Failover erfolgt, nach dem Failover eine Verbindung herstellen können. Wenn ein Netzwerk-Gateway im Azure-Zielnetzwerk vorhanden ist, können die virtuellen Computer zudem eine Verbindung zu den lokalen virtuellen Computern herstellen. </p><p>Wenn die Zuordnung nicht aktiviert ist, können nur virtuelle Computer im gleichen Wiederherstellungsplan, für die ein Failover erfolgt, nach dem Failover auf Azure eine Verbindung miteinander herstellen.|Ordnen Sie VM-Zielnetzwerken VM-Quellnetzwerke zu.</p> <p>Die Netzwerkzuordnung wird verwendet, um replizierte virtuelle Computer auf optimalen Hyper-V-Hostservern abzulegen. Sie stellt sicher, dass die virtuellen Computer, die dem VM-Quellnetzwerk zugeordnet sind, nach dem Failover dem zugeordneten Zielnetzwerk zugeordnet sind. </p><p>Wenn die Zuordnung nicht aktiviert ist, werden die replizierten virtuellen Computer nicht mit einem Netzwerk verbunden.|Ordnen Sie VM-Zielnetzwerken VM-Quellnetzwerke zu.</p> <p>Die Netzwerkzuordnung stellt sicher, dass mit dem VM-Quellnetzwerk verknüpfte virtuelle Computer nach dem Failover mit dem zugeordneten Zielnetzwerk verknüpft werden. </p><p>Wenn die Zuordnung nicht aktiviert ist, werden die replizierten virtuellen Computer nicht mit einem Netzwerk verbunden.
Speicherzuordnung|Nicht zutreffend|Ordnet Speicherklassifizierungen auf VMM-Quellservern den Speicherklassifizierungen auf VMM-Zielservern zu.</p> <p>Wenn die Zuordnung aktiviert ist, befinden sich die Festplatten virtueller Computer aus der Quellspeicherklassifizierung nach dem Failover in der Zielspeicherklassifizierung.</p><p>Wenn die Speicherzuordnung nicht aktiviert ist, werden die replizierten virtuellen Festplatten am Standardspeicherort auf dem Ziel-Hyper-V-Hostserver gespeichert.|Zuordnungen zwischen Speicherarrays und -pools am primären und sekundären Standort.

## Nächste Schritte

Lesen Sie nach dieser Übersicht die [bewährten Methoden](site-recovery-best-practices.md), um mit der Planung der Bereitstellung zu beginnen.
 

<!---HONumber=Oct15_HO3-->