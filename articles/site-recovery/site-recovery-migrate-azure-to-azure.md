<properties
	pageTitle="Migrieren von virtuellen Azure-IaaS-Computern zwischen Azure-Regionen mit Site Recovery | Microsoft Azure"
	description="Verwenden Sie Azure Site Recovery, um virtuelle Azure-IaaS-Computer aus einer Azure-Region in eine andere zu migrieren."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Migrieren von virtuellen Azure-IaaS-Computern zwischen Azure-Regionen mit Azure Site Recovery

## Übersicht

In diesem Artikel erfahren Sie, wie virtuelle Azure-Computer mithilfe von Site Recovery zwischen Azure-Regionen migriert werden. Bevor Sie beginnen, beachten Sie bitte Folgendes:

- Derzeit sind nur Migrationen möglich. Sie können für virtuelle Computer also ein Failover von einer Azure-Region in eine andere durchführen, aber nicht wieder zurück.
- In diesem Artikel werden viele der Schritte zusammengefasst und verwendet, die im [Artikel über das Replizieren virtueller VMware-Computer oder physischer Server in Azure](site-recovery-vmware-to-azure-classic.md) ausführlich beschrieben sind. Dieser Artikel enthält die neuesten erweiterten Anweisungen zum Einrichten der Replikation. Es empfiehlt sich, diesen Artikel bei der Migration heranzuziehen.
- Die Anweisungen in [diesem älteren Artikel](site-recovery-vmware-to-azure-classic-legacy.md) sollten **nicht** mehr verwendet werden.

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.


## Voraussetzungen

Für diese Bereitstellung benötigen Sie Folgendes:

- **Verwaltungsserver**: Ein lokaler virtueller Computer mit Windows Server 2012 R2, der als Verwaltungsserver dient. Sie können die Site Recovery-Komponenten (einschließlich Konfigurationsserver und Prozessserver) auf diesem Server installieren. Weitere Informationen finden Sie in den [Überlegungen zu Verwaltungsservern](site-recovery-vmware-to-azure-classic.md#management-server-considerations) und unter [Lokale Voraussetzungen](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Virtuelle IaaS-Computer**: die virtuellen Computer, die Sie migrieren möchten.

## Bereitstellungsschritte

1. [Erstellen Sie einen Tresor](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault).
2. [Stellen Sie einen Verwaltungsserver bereit](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server).
3. Vergewissern Sie sich nach dem Bereitstellen des Verwaltungsservers, dass er mit den zu migrierenden virtuellen Computern kommunizieren kann.
4. [Erstellen Sie eine Schutzgruppe](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Eine Schutzgruppe enthält geschützte Computer, die dieselben Replikationseinstellungen verwenden. Die für eine Gruppe angegebenen Replikationseinstellungen gelten für alle virtuellen Computer, die Sie dieser Gruppe hinzufügen.
5. [Installieren Sie den Mobilitätsdienst](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Auf jedem virtuellen Computer, den Sie schützen möchten, muss der Mobilitätsdienst installiert sein. Dieser Dienst sendet Daten an den Prozessserver. Der Mobilitätsdienst kann manuell installiert oder mittels Push automatisch durch den Prozessserver installiert werden, wenn der Schutz für den virtuellen Computer aktiviert wird. Beachten Sie, dass Firewallregeln für die zu migrierenden virtuellen IaaS-Computer konfiguriert werden müssen, um die Push-Installation des Diensts zu ermöglichen.
6. [Aktivieren Sie den Schutz für Computer](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Fügen Sie die zu schützenden Computer der Replikationsgruppe hinzu. 
7. Sie können die virtuellen IaaS-Computer, die Sie zu Azure migrieren möchten, mithilfe der privaten IP-Adressen der virtuellen Computer ermitteln. Diese Adresse finden Sie auf dem Dashboard des virtuellen Computers in Azure.
8. Klicken Sie auf der Registerkarte für die erstellte Schutzgruppe auf **Computer hinzufügen** > **Physische Computer**.

	![EC2-Ermittlung](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. Geben Sie die private IP-Adresse des virtuellen Computers an.

	![EC2-Ermittlung](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
	Nachdem Sie der Gruppe einen Computer hinzugefügt haben, wird der Schutz aktiviert, und die erste Replikation wird gemäß Einstellungen für die Schutzgruppe ausgeführt.

10. [Führen Sie ein nicht geplantes Failover durch](site-recovery-failover.md#run-an-unplanned-failover). Nach Abschluss der ersten Replikation können Sie ein nicht geplantes Failover von einer Azure-Region auf eine andere durchführen. Optional können Sie einen Wiederherstellungsplan erstellen und ein nicht geplantes Failover durchführen, um mehrere virtuelle Computer zwischen Regionen zu migrieren. Weitere Informationen zu Wiederherstellungsplänen finden Sie [hier](site-recovery-create-recovery-plans.md).
		
## Nächste Schritte

Weitere Informationen zu anderen Replikationsszenarien finden Sie in [Was ist Azure Site Recovery?](site-recovery-overview.md).

<!---HONumber=AcomDC_0323_2016-->