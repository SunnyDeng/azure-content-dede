
<properties
	pageTitle="Einrichten von Schutz mit einem einzelnen VMM-Server"
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
	ms.date="05/04/2015"
	ms.author="raynew"/>

#  Einrichten von Schutz mit einem einzelnen VMM-Server

## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden. Eine vollständige Liste der Bereitstellungsszenarien finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

Wenn Ihre Infrastruktur nur einen einzelnen VMM-Server enthält, können Sie Site Recovery bereitstellen, um virtuelle Computer in VMM-Clouds zu Azure zu replizieren oder eine Replikation zwischen Clouds auf einem einzelnen VMM-Server durchzuführen. Diese Bereitstellung sollte nur gewählt werden, wenn Sie nicht zwei VMM-Server (jeweils einen pro Standort) bereitstellen können, da hier keine nahtlosen Failover und Wiederherstellungen möglich sind. Für die Wiederherstellung müssen Sie von außerhalb der Azure Site Recovery-Konsole (mit Hyper-V-Replikat in der Hyper-V-Manager-Konsole) ein manuelles Failover des VMM-Servers durchführen.

Für die Replikation mit einem einzelnen VMM-Server stehen verschiedene Einrichtungsoptionen zur Verfügung:

### Eigenständige Bereitstellung

Stellen Sie einen eigenständigen VMM-Server als virtuellen Computer an einem primären Standort bereit, und replizieren Sie den virtuellen Computer mit Site Recovery und Hyper-V-Replikat zu einem sekundären Standort. Zur Verringerung von Ausfallzeiten kann SQL Server auf dem virtuellen VMM-Computer installiert werden. Wenn VMM einen Remotecomputer mit SQL Server verwendet, müssen Sie diesen vor dem VMM-Server wiederherstellen.

![Eigenständiger virtueller VMM-Computer](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### Clusterbereitstellung

Wenn VMM hochverfügbar sein soll, können Sie VMM als virtuellen Computer in einem Windows-Failovercluster bereitstellen. Dies ist hilfreich, wenn kritische Workloads von VMM verwaltet werden, da so die Verfügbarkeit der Workloads gewährleistet ist und die Workloads vor Hardwareausfällen des Hosts geschützt sind, auf dem VMM ausgeführt wird. Wenn Sie einen einzelnen VMM-Server mit Site Recovery bereitstellen, muss der virtuelle VMM-Computer unter Verwendung eines Stretchclusters zwischen geografisch getrennten Standorten bereitgestellt werden. Die von VMM verwendete SQL Server-Datenbank muss mithilfe von AlwaysOn-Verfügbarkeitsgruppen (SQL Server) mit einem Replikat am sekundären Standort geschützt werden. Bei einem Notfall wird für den VMM-Server und die entsprechende SQL Server-Datenbank ein Failover durchgeführt, und der Zugriff erfolgt über den sekundären Standort.

![Gruppierter virtueller VMM-Server](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## Vorbereitung

- Die Schritte der exemplarischen Vorgehensweise zeigen, wie Sie Site Recovery mit einem einzelnen eigenständigen VMM-Server bereitstellen.
- Stellen Sie sicher, dass die [erforderlichen Komponenten](site-recovery-vmm-to-vmm.md/#before-you-start) vorhanden sind, bevor Sie mit der Bereitstellung beginnen.
- Für den einzelnen VMM-Server müssen mindestens zwei Clouds konfiguriert sein. Eine Cloud fungiert als geschützte Cloud, die andere als schützende Cloud.
- Die zu schützenden Clouds müssen Folgendes enthalten:
	- Mindestens eine VMM-Hostgruppe
	- Mindestens einen Hyper-V-Hostserver in jeder Hostgruppe
	- Mindestens einen virtuellen Hyper-V-Computer auf jedem Hostserver

Sollten beim Einrichten dieses Szenarios Probleme auftreten, besuchen Sie das [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).



## Konfigurieren einer Einzelserverbereitstellung

1. Falls VMM nicht bereitgestellt ist, richten Sie VMM auf einem virtuellen Computer mit installierter SQL Server-Datenbank an. Lesen Sie die [Systemanforderungen](https://technet.microsoft.com/library/dn771747.aspx). 
2. Richten Sie auf dem VMM-Server mindestens zwei Clouds ein. Weitere Informationen:

	- [What’s New in Private Cloud with System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=) (Neues in der Private Cloud mit System Center 2012 R2 VMM; in englischer Sprache) und [VMM 2012 and the Clouds](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) (VMM 2012 und die Clouds; in englischer Sprache). 
	- [Konfigurieren der VMM-Cloud-Struktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [Erstellen einer privaten Cloud in VMM](https://technet.microsoft.com/library/jj860425.aspx) und [Exemplarische Vorgehensweise: Erstellen von privaten Clouds mit System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)
3. Fügen Sie den Hyper-V-Quellhostserver mit dem zu schützenden virtuellen Computer der Cloud hinzu, die geschützt werden soll (Quellcloud). Fügen Sie den Hyper-V-Zielhostserver der schützenden Cloud auf dem VMM-Server hinzu.
4. [Erstellen](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault) Sie einen Azure Site Recovery-Tresor, und generieren Sie einen Tresorregistrierungsschlüssel.
4. [Installieren](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider) Sie den Azure Site Recovery-Anbieter auf dem VMM-Server, und registrieren Sie den Server im Tresor. 
5. Vergewissern Sie sich, dass die Clouds im Site Recovery-Portal angezeigt werden, und [konfigurieren Sie die Cloudschutzeinstellungen](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings).
	- Geben Sie unter **Quellenspeicherort** und unter **Zielspeicherort** jeweils den Namen des einzelnen VMM-Servers an.
	- Wählen Sie für die erste Replikation unter **Replikationsmethode** die Option **Über das Netzwerk** aus, da sich die Clouds auf dem gleichen Server befinden.

6. Optional: [Konfigurieren Sie die Netzwerkzuordnung](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping):

	- Geben Sie unter **Quelle** und unter **Ziel** jeweils den Namen des einzelnen VMM-Servers an.
	- Wählen Sie unter **Netzwerk auf Quelle** das für die geschützte Cloud konfigurierte VM-Netzwerk aus.
	- Wählen Sie unter **Netzwerk am Ziel** das VM-Netzwerk aus, das für die Cloud konfiguriert ist, die zum Schutz verwendet werden soll.
	- Die Netzwerkzuordnung kann zwischen zwei VM-Netzwerken auf dem gleichen VMM-Server konfiguriert werden. Wenn das gleiche VMM-Netzwerk an zwei unterschiedlichen Standorten vorhanden ist, kann die Zuordnung zwischen den gleichen Netzwerken erfolgen.
7. [Aktivieren Sie den Schutz](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection) für virtuelle Computer in der zu schützenden VMM-Cloud. 
7. Richten Sie in der Hyper-V-Manager-Konsole die Replikation für den virtuellen VMM-Computer mit Hyper-V-Replikat ein. Der virtuelle VMM-Computer darf keiner VMM-Cloud hinzugefügt werden.


## Failover und Wiederherstellung

### Erstellen von Wiederherstellungsplänen

Wiederherstellungspläne dienen zum Gruppieren virtueller Computer, für die ein gemeinsames Failover und eine gemeinsame Wiederherstellung durchgeführt werden sollen.

1. Geben Sie bei Erstellung eines Wiederherstellungsplans unter **Quelle** und unter **Ziel** jeweils den Namen des einzelnen VMM-Servers an. Unter **Virtuelle Computer auswählen** werden die virtuellen Computer angezeigt, die der primären Cloud zugeordnet sind.
2. [Erstellen Sie Wiederherstellungspläne, und passen Sie sie an](https://msdn.microsoft.com/library/azure/dn337331.aspx).


### Wiederherstellen

Bei einem Notfall können Workloads auf folgende Weise wiederhergestellt werden:

1. Führen Sie für den virtuellen VMM-Replikatcomputer mithilfe der Hyper-V-Manager-Konsole ein manuelles Failover zum Wiederherstellungsstandort durch.
2. Nach der Wiederherstellung des virtuellen VMM-Computers können Sie sich über das Portal bei der Hyper-V-Wiederherstellungs-Manager-Konsole anmelden und ein nicht geplantes Failover der virtuellen Computer vom primären Standort zum Wiederherstellungsstandort durchführen.
3.  Nach Abschluss des nicht geplanten Failovers können Benutzer auf sämtliche Ressourcen am primären Standort zugreifen.


 

<!---HONumber=July15_HO2-->