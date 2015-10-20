<properties
	pageTitle="Netzwerkzuordnung für Azure Site Recovery | Microsoft Azure"
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
	ms.date="10/07/2015"
	ms.author="raynew"/>


# Netzwerkzuordnung für Azure Site Recovery


Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Informationen zu möglichen Bereitstellungsszenarien finden Sie unter [Übersicht über Site Recovery](site-recovery-overview.md).


## Informationen zum Artikel

Die Netzwerkzuordnung ist ein wichtiger Bestandteil der Bereitstellung von VMM und Site Recovery. Sie sorgt dafür, dass die virtuellen Replikatcomputer optimal auf Hyper-V-Hostzielservern platziert werden und nach dem Failover mit geeigneten Netzwerken verbunden sind. Dieser Artikel enthält Informationen zur Netzwerkzuordnung sowie einige Beispiele zur Veranschaulichung der Funktionsweise.


Etwaige Fragen können Sie im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) stellen.

## Übersicht

Die Einrichtung der Netzwerkzuordnung ist abhängig vom jeweiligen Site Recovery-Bereitstellungsszenario.



- **Lokal zu lokal (VMM-Server)**: Die Netzwerkzuordnung wird zwischen VM-Netzwerken auf einem VMM-Quellserver und VM-Netzwerken auf einem VMM-Zielserver für Folgendes eingesetzt:

	- **Verbinden virtueller Computer nach einem Failover**: Stellt sicher, dass die virtuellen Computer nach dem Failover mit geeigneten Netzwerken verbunden sind. Der virtuelle Replikatcomputer wird mit dem Zielnetzwerk verbunden, das dem Quellnetzwerk zugeordnet ist.
	- **Platzieren virtueller Replikatcomputer auf Hostservern**: Virtuelle Replikatcomputer werden optimal auf Hyper-V-Hostservern platziert. Virtuelle Replikatcomputer werden auf Hosts platziert, die auf die zugeordneten VM-Netzwerke zugreifen können.
	- **Keine Netzwerkzuordnung**: Wenn Sie keine Netzwerkzuordnung konfigurieren, werden virtuelle Replikatcomputer nach dem Failover nicht mit VM-Netzwerken verbunden.

- **Lokaler VMM-Server zu Azure**: Die Netzwerkzuordnung wird zwischen VM-Netzwerken auf dem VMM-Quellserver und den Azure-Zielnetzwerken für Folgendes eingesetzt:
	- **Verbinden virtueller Computer nach einem Failover**: Alle Computer, für die ein Failover im gleichen Netzwerk durchgeführt wird, können unabhängig vom jeweiligen Wiederherstellungsplan eine Verbindung untereinander herstellen.
	- **Netzwerkgateway**: Wenn im Azure-Zielnetzwerk ein Netzwerkgateway eingerichtet ist, können die virtuellen Computer eine Verbindung mit anderen lokalen virtuellen Computern herstellen.
	- **Keine Netzwerkzuordnung**: Wenn Sie keine Netzwerkzuordnung konfigurieren, können nach dem Failover zu Azure nur virtuelle Computer aus dem gleichen Wiederherstellungsplan eine Verbindung untereinander herstellen.

## VM-Netzwerke

Ein logisches VMM-Netzwerk bietet eine abstrakte Sicht auf die physische Netzwerkinfrastruktur. VM-Netzwerke stellen eine Netzwerkschnittstelle bereit, sodass virtuelle Computer eine Verbindung mit logischen Netzwerken herstellen können. Ein logisches Netzwerk benötigt mindestens ein VM-Netzwerk. Wenn Sie einen virtuellen Computer zu dessen Schutz in einer Cloud platzieren, muss er mit einem VM-Netzwerk verbunden sein, das über eine Verknüpfung mit einem logischen Netzwerk verfügt, welches wiederum der Cloud zugeordnet ist. Weitere Informationen:

- [Logical Networks (Part 1)](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) (Logische Netzwerke (Teil 1); in englischer Sprache)
- [Virtual Networking in VMM 2012 SP1](http://blogs.technet.com/b/scvmm/archive/2013/01/08/virtual-networking-in-vmm-2012-sp1.aspx) (Virtuelle Netzwerke in VMM 2012 SP1; in englischer Sprache)

## Beispiel

Die Netzwerkzuordnung kann zwischen VM-Netzwerken auf zwei VMM-Servern konfiguriert werden (oder auf einem einzelnen VMM-Server, wenn zwei Standorte vom gleichen Server verwaltet werden). Bei korrekt konfigurierter Zuordnung und aktivierter Replikation ist ein virtueller Computer am primären Ort mit einem Netzwerk verbunden, und sein Replikat am Zielort wird mit dem zugeordneten Netzwerk verbunden.

Wenn die Netzwerke in VMM ordnungsgemäß eingerichtet wurden, gilt Folgendes: Wenn Sie bei der Netzwerkzuordnung ein VM-Zielnetzwerk auswählen, werden die VMM-Quellclouds, die das VM-Quellnetzwerk verwenden, sowie die verfügbaren VM-Zielnetzwerke in den Zielclouds angezeigt, die für den Schutz verwendet werden.

Im Anschluss folgt ein Beispiel zur Veranschaulichung dieses Mechanismus. Für das Beispiel verwenden wir eine Organisation mit zwei Standorten in New York und Chicago.

**Standort** | **VMM-Server** | **VM-Netzwerke** | **Zuordnung**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Zugeordnet zu „VMNetwork1-Chicago“
 | | VMNetwork2-NewYork | Nicht zugeordnet
Chicago | VMM-Chicago| VMNetwork1-Chicago | Zugeordnet zu „VMNetwork1-NewYork“
 | | VMNetwork2-Chicago | Nicht zugeordnet

In diesem Beispiel gilt Folgendes:

- Wenn für einen virtuellen, mit „VMNetwork1-NewYork“ verbundenen Computer ein virtueller Replikatcomputer erstellt wird, wird dieser mit „VMNetwork1-Chicago“ verbunden.
- Ein für „VMNetwork2-NewYork“ oder „VMNetwork2-Chicago“ erstellter virtueller Replikatcomputer wird mit keinem Netzwerk verbunden.

Im Anschluss finden Sie Informationen zur Einrichtung der VMM-Clouds für unser Beispielunternehmen sowie zu den logischen Netzwerken, die den Clouds zugeordnet sind.

### Cloudschutzeinstellungen

**Geschützte Cloud** | **Schützende Cloud** | **Logisches Netzwerk (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>k. A.</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>k. A.</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### Einstellungen für das logische und das VM-basierte Netzwerk

**Standort** | **Logisches Netzwerk** | **Zugeordnetes VM-Netzwerk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2-Chicago | VMNetwork2-Chicago

### Zielnetzwerke

Die folgende Tabelle zeigt die Optionen, die zur Verfügung stehen, wenn Sie bei Verwendung dieser Einstellungen das VM-Zielnetzwerk auswählen:

**Auswahl** | **Geschützte Cloud** | **Schützende Cloud** | **Verfügbarkeit des Zielnetzwerks**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Verfügbar
 | GoldCloud1 | GoldCloud2 | Verfügbar
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Nicht verfügbar
 | GoldCloud1 | GoldCloud2 | Verfügbar



## Mehrere Subnetze

Wenn das Zielnetzwerk mehrere Subnetze enthält und eines dieser Subnetze den gleichen Namen besitzt wie das Subnetz des virtuellen Quellcomputers, wird der virtuelle Replikatcomputer nach dem Failover mit diesem Zielsubnetz verbunden. Gibt es kein Zielsubnetz mit einem übereinstimmenden Namen, wird der virtuelle Computer mit dem ersten Subnetz im Netzwerk verbunden.


### Failback

Zur Veranschaulichung der Vorgänge bei einem Failback (umgekehrte Replikation) nehmen wir an, dass zwischen „VMNetwork1-NewYork“ und „VMNetwork1-Chicago“ eine Zuordnung mit den folgenden Einstellungen besteht:


**Virtueller Computer** | **Verbundenes VM-Netzwerk**
---|---
VM1 | VMNetwork1-NewYork
VM2 (Replikat von VM1) | VMNetwork1-Chicago

Einige mögliche Szenarien mit diesen Einstellungen:

**Szenario** | **Ergebnis**
---|---
Keine Änderung der Netzwerkeigenschaften von VM-2 nach dem Failover | VM-1 bleibt mit dem Quellnetzwerk verbunden.
Änderung der Netzwerkeigenschaften von VM-2 nach dem Failover und Trennung der Verbindung | VM-1 wird getrennt.
Änderung der Netzwerkeigenschaften von VM-2 nach dem Failover und Verbindung mit "VMNetwork2-Chicago" | Wenn "VMNetwork2-Chicago" nicht zugeordnet ist, wird VM-1 getrennt.
Änderung der Netzwerkzuordnung von "VMNetwork1-Chicago" | VM-1 wird mit dem Netzwerk verbunden, das jetzt "VMNetwork1-Chicago" zugeordnet ist.


## Nächste Schritte

Nachdem Sie die Netzwerkzuordnung nun besser nachvollziehen können, können Sie sich zur Vorbereitung auf die Bereitstellung mit den [bewährten Methoden](site-recovery-best-practices.md) vertraut machen.

<!---HONumber=Oct15_HO3-->