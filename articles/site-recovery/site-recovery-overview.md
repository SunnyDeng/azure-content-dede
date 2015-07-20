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
	ms.date="05/10/2015" 
	ms.author="raynew"/>

#  Übersicht über Site Recovery

Site Recovery ist Teil einer Lösung für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), die Ihre lokalen physischen Server und Ihre virtuellen Computer mittels Orchestrierung und Automatisierung von Replikation und Failover zu Azure (oder zu einem sekundären lokalen Datencenter) schützt.

- **Vereinfachung**: Site Recovery unterstützt Sie beim Konfigurieren der Replikation sowie beim Ausführen von Failover- und Wiederherstellungsprozessen für lokale Workloads und Anwendungen und trägt so zur Vereinfachung Ihrer BCDR-Strategie bei.
- **Replikation**: Lokale Workloads können zu Azure-Speicher oder zu einem sekundären Datencenter repliziert werden. 
- **Tresor**: Die Replikation wird über einen Site Recovery-Tresor in einer Azure-Region Ihrer Wahl verwaltet. Alle Metadaten bleiben in dieser Region.
- **Metadaten**: Es werden keine Anwendungsdaten an Azure gesendet. Site Recovery benötigt nur Metadaten (etwa den Namen des virtuellen Computers und der VMM-Cloud), um die Replikation und das Failover orchestrieren zu können. 
- **Verbindung mit Azure**: Die Kommunikation zwischen Verwaltungsservern und Azure ist abhängig von Ihrem Bereitstellungsszenario. Wenn Sie beispielsweise virtuelle Computer in einer lokalen VMM-Cloud replizieren, kommuniziert der VMM-Server mit Site Recovery über eine verschlüsselte ausgehende HTTPS-Verbindung. Für die virtuellen Computer oder Hyper-V-Hosts ist keine Verbindung erforderlich.
- **Hyper-V-Replikat**: Azure Site Recovery nutzt Hyper-V-Replikat für den Replikationsprozess und kann auch die SAN-Replikation nutzen, wenn die Replikation zwischen zwei lokalen VMM-Standorten stattfindet. Dank der intelligenten Replikation von Site Recovery werden bei der ersten Replikation nur Datenblöcke (und nicht die gesamte virtuelle Festplatte) repliziert. Bei der laufenden Replikation werden nur geänderte Daten repliziert. Site Recovery unterstützt die Offlinedatenübertragung und kann mit WAN-Optimierern kombiniert werden.
- **Preise**: Preisinformationen zu Site Recovery finden Sie [hier](pricing/details/site-recovery).


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

Die ASR-Replikationstechnologien sind mit jeder Anwendung kompatibel, die auf einem virtuellen Computer ausgeführt wird. Wir haben in Zusammenarbeit mit Anwendungsproduktteams zusätzliche Tests durchgeführt, um die Unterstützung der einzelnen Anwendungen noch weiter zu optimieren.

**Workload** | <p>**Replizieren virtueller Hyper-V-Computer**</p> <p>**(Ziel: sekundärer Standort)**</p> | <p>**Replizieren virtueller Hyper-V-Computer**</p> <p>**(Ziel: Azure)**</p> | <p>**Replizieren virtueller VMware-Computer**</p> <p>**(Ziel: sekundärer Standort)**</p> | <p>**Replizieren virtueller VMware-Computer**</p><p>**(Ziel: Azure)**</p>
---|---|---|---|---
Active Directory, DNS | J | J | J | In Kürze verfügbar 
Web-Apps (IIS, SQL) | J | J | J | In Kürze verfügbar
SCOM | J | J | J | In Kürze verfügbar
SharePoint | J | J | J | In Kürze verfügbar
<p>SAP</p><p>Replizieren eines SAP-Standorts zu Azure (kein Cluster)</p> | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | In Kürze verfügbar
Exchange (kein DAG) | J | In Kürze verfügbar | J | In Kürze verfügbar
Remotedesktop/VDI | J | J | J | In Kürze verfügbar 
<p>Linux</p> <p>(Betriebssystem und Apps)</p> | J (getestet von Microsoft) | J (getestet von Microsoft) | J (getestet von Microsoft) | In Kürze verfügbar 
Dynamics AX | J | J | J | In Kürze verfügbar
Dynamics CRM | In Kürze verfügbar | In Kürze verfügbar | J | In Kürze verfügbar
Oracle | In Kürze verfügbar | In Kürze verfügbar | J (getestet von Microsoft) | In Kürze verfügbar


## Features und Anforderungen 

Die folgende Tabelle enthält Informationen zu den wichtigsten Features von Site Recovery sowie zu deren Behandlung bei der Replikation zu Azure bzw. zu einem sekundären Standort – entweder mit dem standardmäßigen Hyper-V-Replikat oder unter Verwendung von SAN.

<table border="1">
<thead>
<tr>
	<th>Feature</th><th>Replikation zu Azure</th>
	<th>Replikation zu einem sekundären Standort (Hyper-V-Replikat)</th>
	<th>Replikation zu einem sekundären Standort (SAN)</th>
</tr>
</thead>

<tr>
<td>Datenreplikation</td>
<td><p>Metadaten zu lokalen Servern und virtuellen Computern werden im Site&#160;Recovery-Tresor gespeichert.</p> <p>Replizierte Daten werden in Azure-Speicher gespeichert.</p></td>
<td><p>Metadaten zu lokalen Servern und virtuellen Computern werden im Site&#160;Recovery-Tresor gespeichert.</p> <p>Replizierte Daten werden an dem Standort gespeichert, der durch den Hyper-V-Zielserver angegeben wird.</p></td>
<td><p>Metadaten zu lokalen Servern und virtuellen Computern werden im Site&#160;Recovery-Tresor gespeichert.</p> <p>Replizierte Daten werden im Zielarrayspeicher gespeichert.</p></td>
</tr>

<tr>
<td>Tresoranforderungen</td>
<td><p>Azure-Konto mit Site&#160;Recovery-Dienst</p></td>
<td><p>Azure-Konto mit Site&#160;Recovery-Dienst</p>
</td><td><p>Azure-Konto mit Site&#160;Recovery-Dienst</p></td>
</tr>

<tr>
<td>Replikation</td>
<td>Replikation des virtuellen Computers auf dem Hyper-V-Quellhost zum Azure-Speicher. Failback zum Quellort.</td>
<td>Replikation des virtuellen Computers auf dem Hyper-V-Quellhost zum Hyper-V-Zielhost. Failback zum Quellort.</td>
<td>Replikation virtueller Computer vom SAN-Quellspeichergerät zum SAN-Zielgerät. Failback zum Quellort.</td>
</tr>

<tr>
<td>Virtueller Computer</td>
<td>In Azure-Speicher gespeicherte VM-Festplatte</td>
<td>Auf Hyper-V-Host gespeicherte VM-Festplatte</td>
<td>In SAN-Speicherarray gespeicherte VM-Festplatte</td>
</tr>

<tr>
<td>Azure-Speicher</td>
<td>Zum Speichern replizierter VM-Festplatten erforderlich</td>
<td>Nicht zutreffend</td>
<td>Nicht zutreffend</td>
</tr>

<tr>
<td>SAN-Speicherarray</td>
<td><p>Nicht zutreffend</p></td>
<td>Nicht zutreffend</td>
<td>Das SAN-Speicherarray muss sowohl am Quell- als auch am Zielort verfügbar sein und von VMM verwaltet werden.</td>
</tr>

<tr>
<td>VMM-Server</td>
<td>Nur VMM-Server am Quellort. </td>
<td>VMM-Server am Quell- und Zielort empfohlen. Replikation zwischen Clouds auf einem einzelnen VMM-Server möglich.</td>
<td>VMM-Server am VMM-Quell- und -Zielort. Clouds müssen mindestens einen Hyper-V-Cluster umfassen.</td>
</tr>

<tr>
<td>VMM-Version</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 mit SP1</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 mit VMM Update Rollup 5.0</p></td>
</tr>

<tr>
<td>VMM-Konfiguration</td>
<td><p>Einrichten von Clouds am Quell- und Zielstandort</p><p>Einrichten von VM-Netzwerken am Quell- und Zielstandort</p><p>Einrichten von Speicherklassifizierungen am Quell- und Zielstandort <p>Installieren des Anbieters auf den VMM-Quell- und -Zielservern</p></td>
<td><p>Einrichten von Clouds am Quellstandort</p><p>Einrichten des SAN-Speichers</p><p>Einrichten von VM-Netzwerken am Quellstandort</p><p>Installieren des Anbieters auf dem VMM-Quellserver</p><p>Aktivieren des Schutzes für virtuelle Computer</p></td>
<td><p>Einrichten von Clouds am Quell- und Zielstandort</p><p>Einrichten von VM-Netzwerken am Quell- und Zielstandort</p><p>Installieren des Anbieters auf den VMM-Quell- und -Zielservern</p><p>Aktivieren des Schutzes für virtuelle Computer</p></td>
</tr>

<tr>
<td><p>Azure Site Recovery-Anbieter</p><p>(für die HTTPS-Verbindung mit Site Recovery)</p></td>
<td>Auf VMM-Quellserver installiert</td>
<td>Auf VMM-Quell- und Zielservern installiert</td>
<td>Auf VMM-Quell- und Zielservern installiert</td>
</tr>

<tr>
<td><p>Azure Recovery Services-Agent</p><p>(für die HTTPS-Verbindung mit Site Recovery)</p></td>
<td>Auf Hyper-V-Hostservern installiert</td>
<td>Nicht erforderlich</td>
<td>Nicht erforderlich</td>
</tr>

<tr>
<td>Wiederherstellungspunkte für virtuellen Computer</td>
<td><p>Legen Sie Wiederherstellungspunkte nach Zeit fest.</p> <p>Gibt an, wie lange ein Wiederherstellungspunkt aufbewahrt werden soll (zwischen 0 und 24&#160;Stunden).</p></td>
<td><p>Legen Sie Wiederherstellungspunkte nach Menge fest.</p> <p>Gibt an, wie viele zusätzliche Wiederherstellungspunkte aufbewahrt werden sollen (0-15). Standardmäßig wird jede Stunde ein Wiederherstellungspunkt erstellt.</p></td>
<td>In den Arrayspeichereinstellungen konfiguriert</td>
</tr>

<tr>
<td>Netzwerkzuordnung</td>
<td><p>Ordnen Sie VM-Netzwerke Azure-Netzwerken zu.</p> <p>Durch die Netzwerkzuordnung wird sichergestellt, dass alle virtuellen Computer im gleichen VM-Quellnetzwerk, für die ein Failover erfolgt, nach dem Failover eine Verbindung herstellen können. Wenn ein Netzwerk-Gateway im Azure-Zielnetzwerk vorhanden ist, können die virtuellen Computer zudem eine Verbindung zu den lokalen virtuellen Computern herstellen. </p><p>Wenn die Zuordnung nicht aktiviert ist, können nur virtuelle Computer im gleichen Wiederherstellungsplan, für die ein Failover erfolgt, nach dem Failover an Azure eine Verbindung miteinander herstellen.</p></td>
<td><p>Ordnen Sie VM-Quellnetzwerke zu VM-Zielnetzwerken zu.</p> <p>Die Netzwerkzuordnung wird verwendet, um replizierte virtuelle Computer auf optimalen Hyper-V-Hostservern abzulegen. Sie stellt sicher, dass die virtuellen Computer, die dem VM-Quellnetzwerk zugeordnet sind, nach dem Failover dem zugeordneten Zielnetzwerk zugeordnet sind. </p><p>Wenn die Zuordnung nicht aktiviert ist, werden die replizierten virtuellen Computer nicht mit einem Netzwerk verbunden.</p></td>
<td><p>Ordnen Sie VM-Quellnetzwerke zu VM-Zielnetzwerken zu.</p> <p>Die Netzwerkzuordnung stellt sicher, dass mit dem VM-Quellnetzwerk verknüpfte virtuelle Computer nach dem Failover mit dem zugeordneten Zielnetzwerk verknüpft werden. </p><p>Wenn die Zuordnung nicht aktiviert ist, werden die replizierten virtuellen Computer nicht mit einem Netzwerk verbunden.</p></td>
</tr>

<tr>
<td>Speicherzuordnung</td>
<td>Nicht zutreffend</td>
<td><p>Ordnet Speicherklassifizierungen auf VMM-Quellservern den Speicherklassifizierungen auf VMM-Zielservern zu.</p> <p>Wenn die Zuordnung aktiviert ist, befinden sich die Festplatten virtueller Computer aus der Quellspeicherklassifizierung nach den Failover in der Zielspeicherklassifizierung.</p><p>Wenn die Speicherzuordnung nicht aktiviert ist, werden die replizierten virtuellen Festplatten am Standardspeicherort auf dem Ziel-Hyper-V-Hostserver gespeichert.</p></td>
<td><p>Zuordnungen zwischen Speicherarrays und -pools am primären und sekundären Standort.</p></td>
</tr>

</table>

## Nächste Schritte

Lesen Sie nach dieser Übersicht die [bewährten Methoden](site-recovery-best-practices.md), um mit der Planung der Bereitstellung zu beginnen.
 

<!---HONumber=July15_HO2-->