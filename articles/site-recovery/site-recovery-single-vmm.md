
<properties
	pageTitle="Azure Site Recovery: Replizieren von virtuellen Hyper-V-Computern (einzelner VMM-Server)"
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Computern in lokalen VMM-Clouds zu Azure oder zu einer sekundären VMM-Cloud."
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
	ms.workload="backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew"/>

#  Azure Site Recovery: Replizieren von virtuellen Hyper-V-Computern (einzelner VMM-Server)

Azure Site Recovery ist Teil einer Lösung für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), die Orchestrierung und Automatisierung der Replikation lokaler physischer Server und virtueller Computer zu Azure oder zu einem sekundären lokalen Datencenter bietet. In diesem Artikel wird beschrieben, wie Sie Site Recovery zum Schutz von virtuellen Hyper-V-Computern in einer VMM-Cloud bereitstellen, wenn die Bereitstellung nur einen einzelnen VMM-Server enthält. Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Übersicht

Sie können Hyper-V-VMs auf verschiedene Arten replizieren:

- Replizieren von Hyper-V-VMs, die sich auf Hyper-V-Hosts außerhalb einer VMM-Cloud befinden, zu Azure
- Replizieren von Hyper-V-VMs, die sich auf Hyper-V-Hosts in einer VMM-Cloud befinden, zu Azure
- Replizieren von Hyper-V-VMs, die sich auf Hyper-V-Hosts in einer VMM-Cloud befinden, zu Azure

Was geschieht aber, wenn Sie VMM verwenden möchten und nur über einen einzelnen VMM-Server in Ihrer Infrastruktur verfügen?

In diesem Fall haben Sie mehrere Möglichkeiten:

- Replizieren Sie Ihre Hyper-V-VMs in Ihren VMM-Clouds zu Azure. [Hier](site-recovery-vmm-to-azure.md) finden Sie weitere Informationen zu diesem Szenario.
- Replikation zwischen Clouds auf einem einzelnen VMM-Server

Es empfiehlt sich, die erste Option zu verwenden, weil Failover und Wiederherstellung nicht nahtlos in die zweite Option integriert und einige manuelle Schritte erforderlich sind.


### Replikation zwischen Standorten mit einem einzelnen, eigenständigen VMM-Server

In diesem Szenario stellen Sie den einzelnen, eigenständigen VMM-Server als virtuellen Computer an einem primären Standort bereit und replizieren diesen virtuellen Computer mit Site Recovery und Hyper-V-Replikat zu einem sekundären Standort. Gehen Sie dazu folgendermaßen vor:

1. Richten Sie VMM auf einer Hyper-V-VM ein. Ziehen Sie das Verschieben der von VMM verwendeten SQL Server-Instanz auf demselben virtuellen Computer in Betracht. Dies spart Zeit, da nur ein virtueller Computer instanziiert werden muss. Wenn Sie eine Remoteinstanz verwenden möchten, und ein Ausfall auftritt, müssen Sie diese Instanz vor dem Wiederherstellen von VMM wiederherstellen.
2. Stellen Sie sicher, dass für den VMM-Server mindestens zwei Clouds konfiguriert sind. Eine Cloud enthält die virtuellen Computer, die repliziert werden sollen, und die andere Cloud dient als sekundärer Standort. Die Cloud, die die zu schützenden VMs enthält, sollte über mindestens eine VMM-Hostgruppe mit einem oder mehreren Hyper-V-Hostservern in jeder Hostgruppe und mindestens einem virtuellen Hyper-V-Computer auf jedem Hostserver verfügen.
2. Erstellen Sie einen Site Recovery-Tresor, generieren Sie einen Tresorregistrierungsschlüssel und laden Sie diesen herunter, und registrieren Sie den VMM-Server im Tresor.
2. Richten Sie eine oder mehrere Clouds auf der VMM-VM ein, und fügen Sie die Hyper-V-Hosts, die zu schützende virtuelle Computer enthalten, zu diesen Clouds hinzu.
3. Konfigurieren Sie in Azure Site Recovery Schutzeinstellungen für die Clouds. Geben Sie unter „Quellspeicherort“ und unter „Zielspeicherort“ jeweils den Namen des einzelnen VMM-Servers an. Bei der Konfiguration der Netzwerkzuordnung ordnen Sie das VM-Netzwerk für die Cloud, die zu schützende virtuelle Computer enthält, dem VM-Netzwerk für die Cloud zu, in die Sie die Replikation vornehmen möchten.
4. Aktivieren Sie die Replikation für zu schützende virtuelle Computer unter Verwendung von **Über das Netzwerk** als Replikationsmethode, da sich beide Clouds auf demselben Server befinden.
4. Aktivieren Sie in der Hyper-V-Manager-Konsole Hyper-V-Replikat auf dem Hyper-V-Host, der die VMM-VM enthält, und aktivieren Sie die Replikation auf dem virtuellen Computer. Fügen Sie die VMM-VM nicht zu Clouds hinzu, die von Site Recovery geschützt werden, um sicherzustellen, dass Einstellungen von Hyper-V Replikat nicht durch Site Recovery außer Kraft gesetzt werden.
5. Wenn Sie Wiederherstellungspläne erstellen möchten, geben Sie den gleichen VMM-Server für Quelle und Ziel an. 

Beim Auftreten von Ausfällen stellen Sie Arbeitslasten auf virtuellen Hyper-V-VMs wie folgt wieder her:

1. Führen Sie unter Verwendung von Hyper-V-Manager mit einem geplanten Failover ein manuelles Failover der Replikat-VMM-VM an den sekundären Standort aus.
2. Nach der Wiederherstellung der VMM-VM können Sie sich vom sekundären Standort aus beim Hyper-V-Wiederherstellungs-Manager anmelden und ein nicht geplantes Failover der virtuellen Computer vom primären zum sekundären Standort ausführen.
3. Nach Abschluss des nicht geplanten Failovers können die Benutzer auf sämtliche Ressourcen am primären Standort zugreifen.

Beachten Sie, dass ein manuelles Failover der VMM-VM an den sekundären Standort durchgeführt werden muss, bevor ein Failover der Workloads ausgeführt werden kann.

![Eigenständiger virtueller VMM-Computer](./media/site-recovery-single-vmm/single-vmm-standalone.png)

### Replikation zwischen Standorten mit einem einzelnen VMM-Server in einem gestreckten Cluster

Anstatt einen eigenständigen VMM-Server als virtuellen Computer bereitzustellen, der zu einem sekundären Standort repliziert wird, können Sie VMM als virtuellen Computer in einem Windows-Failovercluster, der Arbeitslaststabilität und Schutz vor Hardwarefehlern bietet, bereitstellen und so hohe Verfügbarkeit erreichen. Zum Bereitstellen mit Site Recovery sollte VMM in einem gestreckten Cluster über geografisch getrennte Standorte bereitgestellt werden. Gehen Sie dazu folgendermaßen vor:

1. Sie Installieren VMM auf einem virtuellen Computer in einem Windows-Failovercluster und wählen während der Installation die Option, den Server mit hoher Verfügbarkeit auszuführen.
2. Die von VMM verwendete SQL Server-Instanz sollte mithilfe von AlwaysOn-Verfügbarkeitsgruppen (SQL Server) repliziert werden, damit ein Replikat der Datenbank am sekundären Standort verfügbar ist. 

Bei Ausfällen wird für den VMM-Server und die entsprechende SQL Server-Datenbank ein Failover durchgeführt, und der Zugriff erfolgt über den sekundären Standort.

![Gruppierter virtueller VMM-Server](./media/site-recovery-single-vmm/single-vmm-cluster.png)




 

<!---HONumber=AcomDC_1203_2015-->