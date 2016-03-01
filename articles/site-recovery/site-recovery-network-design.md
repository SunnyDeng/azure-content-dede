<properties
	pageTitle="Überlegungen zur Bereitstellung für VMM und Site Recovery | Microsoft Azure"
	description="Dieser Artikel beschreibt praktische Vorüberlegungen zum Entwurf für die Integration in Azure Site Recovery."
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
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Überlegungen zur Bereitstellung für VMM und Site Recovery

Der Dienst Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md).


## Übersicht

Dieser Artikel hilft Ihnen beim Entwerfen und Implementieren einer DCDR-Lösung und Infrastruktur, die System Center VMM und Azure Site Recovery enthält.

Zweck einer BCDR-Strategie ist es, die Ausführung der Unternehmensanwendungen zu gewährleisten und fehlgeschlagene Workloads und Dienste wiederherzustellen, damit die Organisation schnell wieder den normalen Betrieb aufnehmen kann. Aufgrund der Schwierigkeit, unerwartete Ereignisse vorherzusehen, und wegen der hohen Kosten für das Implementieren von angemessenem Schutz vor Ausfällen mit weitreichenden Folgen stellt die Entwicklung von Strategien zur Notfallwiederherstellung eine echte Herausforderung dar. Mit Site Recovery können Sie Schutz und Failover von Ihrem primären auf ein sekundäres Rechenzentrum (oder auf Azure) implementieren, indem zuerst die primären Daten kopiert (repliziert) und dann in regelmäßigen Abständen die Replikate aktualisiert werden.

Als wichtiger Bestandteil der BCDR-Planung müssen Sie Ihre RTO (Recovery Time Objective) und RPO (Recovery Point Objective) definieren, sodass Sie Ihre Unternehmensdaten so schnell wie möglich (mit einer niedrigen RTO) und bei minimalen Datenverlusten (niedrige RPO) wieder online schalten können. Der Netzwerkentwurf in Ihrer Organisation stellt einen potenziellen Engpass für Ihre RTO- und RPO-Ziele dar. Durch die Planung eines soliden Entwurfs kann dieser Engpass vermieden werden.


Wenn Sie Site Recovery zum Replizieren von Hyper-V-VMs in VMM-Clouds bereitstellen, müssen Sie berücksichtigen, wie VMM in Ihre Site Recovery-Replikation und Failoverstrategie integriert ist.

## Integrieren eines eigenständigen VMM-Servers

In dieser Topologie stellen Sie einen VMM-Server auf einem virtuellen Computer am primären Standort bereit und replizieren den virtuellen Computer dann mit Site Recovery und Hyper-V-Replikat an einen sekundären Standort. Sie sollten erwägen, den VMM-Server und den unterstützenden SQL Server auf derselben virtuellen Maschine zu installieren. Dies kann Ausfallzeiten reduzieren, da nur eine virtuelle Maschine instanziiert werden muss. Wenn der VMM-Dienst einen Remotecomputer mit SQL Server verwendet, müssen Sie die SQL Server-Instanz vor dem VMM-Server wiederherstellen.

So stellen Sie eine einzelne VMM auf einem virtuellen Computer mit Hyper-V-Replikat bereit:

1. Richten Sie VMM auf einem virtuellen Computer ein, auf dem SQL Server installiert ist.
2. Fügen Sie die zu verwaltenden Hosts zu Clouds auf diesem VMM-Server hinzu.
3. Melden Sie sich beim Azure-Portal an, und konfigurieren Sie Clouds für den Schutz.
4. Aktivieren Sie die Replikation für alle virtuellen Computer, die vom VMM-Server geschützt werden müssen.
5. Navigieren Sie zur Hyper-V-Manager-Konsole, wählen Sie Hyper-V-Replikat aus, und aktivieren Sie dann die Replikation auf dem virtuellen VMM-Computer.
6. Stellen Sie sicher, dass der virtuelle VMM-Computer nicht zu den Clouds hinzugefügt wird, die vom ASR-Dienst geschützt werden, damit die Einstellungen der Hyper-V-Replikation nicht von ASR überschrieben werden.

Bei einem Notfall können Workloads auf folgende Weise wiederhergestellt werden:

1. Failover der VMM-Replikat-VM an den Wiederherstellungsstandort mit Hyper-V-Manager.
2. Nach Wiederherstellung der VMM-VM kann der Benutzer sich vom sekundären Standort beim Hyper-V-Wiederherstellungs-Manager anmelden.
3. Nach Abschluss des nicht geplanten Failovers können die Benutzer auf sämtliche Ressourcen am primären Standort zugreifen.
4. Beachten Sie, dass ein manuelles Failover der VMM-VM an den sekundären Standort durchgeführt werden muss, bevor ein Failover der Workloads ausgeführt werden kann.


### Integrieren eines VMM-Clusters


Die [Bereitstellung von VMM in einem Cluster](https://technet.microsoft.com/library/gg610675.aspx) bietet hohe Verfügbarkeit und Schutz vor einem Hardware-Failover. Wenn Sie den VMM-Cluster mit Site Recovery bereitstellen, beachten Sie Folgendes:

- Der VMM-Server sollte in einem gestreckten Cluster über geografisch getrennte Standorte bereitgestellt werden.
- Die von VMM verwendete SQL Server-Datenbank muss mithilfe von AlwaysOn-Verfügbarkeitsgruppen (SQL Server) mit einem Replikat am sekundären Standort geschützt werden.
- Wenn ein Notfall eintritt, erfolgt automatisch ein Failover des VMM-Servers und des zugehörigen SQL-Servers an den Wiederherstellungsstandort. Sie können dann ein Failover der Workloads mithilfe von Site Recovery durchführen.

	![Gestreckte VMM-Cluster](./media/site-recovery-network-design/network-design1.png)


## Nächste Schritte

[Hier](site-recovery-network-mapping.md) erfahren Sie, wie Site Recovery Quell- und Zielnetzwerke zuordnet.

<!---HONumber=AcomDC_0218_2016-->