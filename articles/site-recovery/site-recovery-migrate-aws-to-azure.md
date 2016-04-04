<properties
	pageTitle="Migrieren virtueller Windows-Computer von Amazon Web Services (AWS) zu Azure mit Site Recovery | Microsoft Azure"
	description="Dieser Artikel beschreibt, wie Sie in Amazon Web Services (AWA) ausgeführte virtuelle Windows-Computer zu Azure migrieren."
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
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Migrieren virtueller Windows-Computer in Amazon Web Services (AWS) zu Azure mit Azure Site Recovery

## Übersicht

Dieser Artikel beschreibt, wie Sie in AWS ausgeführte Windows-Instanzen mithilfe von Site Recovery zu Azure migrieren. Dabei ist Folgendes zu beachten:

- Derzeit sind nur Migrationen möglich. Sie können also ein Failover von AWS an Azure durchführen, aber nicht wieder zurück.
- In diesem Artikel werden viele der Schritte zusammengefasst und verwendet, die im [Artikel über das Replizieren virtueller VMware-Computer oder physischer Server in Azure](site-recovery-vmware-to-azure-classic.md) ausführlich beschrieben sind. Dieser Artikel enthält die neuesten erweiterten Anweisungen zum Einrichten der Replikation. Es empfiehlt sich, diesen Artikel bei der Migration heranzuziehen.
- Die Anweisungen in [diesem älteren Artikel](site-recovery-vmware-to-azure-classic-legacy.md) sollten **nicht** mehr verwendet werden.

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.


## Voraussetzungen

Sie benötigen Folgendes:

- **Verwaltungsserver**: Ein lokaler virtueller Computer mit Windows Server 2012 R2, der als Verwaltungsserver dient. Sie können die Site Recovery-Komponenten (einschließlich Konfigurationsserver und Prozessserver) auf diesem Server installieren. Weitere Informationen finden Sie in den [Überlegungen zu Verwaltungsservern](site-recovery-vmware-to-azure-classic.md#management-server-considerations) und unter [Lokale Voraussetzungen](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **EC2-VM-Instanzen**: Die Instanzen, die Sie migrieren und anschließend schützen möchten.

## Bereitstellungsschritte

1. [Erstellen eines Tresors](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [Stellen Sie einen Verwaltungsserver bereit](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server). 
3. Vergewissern Sie sich nach dem Bereitstellen des Verwaltungsservers, dass er mit den zu migrierenden EC2-Instanzen kommunizieren kann.
4. [Erstellen Sie eine Schutzgruppe](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Eine Schutzgruppe enthält geschützte Computer, die dieselben Replikationseinstellungen verwenden. Die für eine Gruppe angegebenen Replikationseinstellungen gelten für alle virtuellen Computer, die Sie dieser Gruppe hinzufügen. 
5. [Installieren Sie den Mobilitätsdienst](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Auf jedem virtuellen Computer, den Sie schützen möchten, muss der Mobilitätsdienst installiert sein. Dieser Dienst sendet Daten an den Prozessserver. Der Mobilitätsdienst kann manuell installiert oder mittels Push automatisch durch den Prozessserver installiert werden, wenn der Schutz für den virtuellen Computer aktiviert wird. Konfigurieren Sie Firewallregeln für die zu migrierenden EC2-Instanzen, um die Push-Installation des Diensts zu ermöglichen. Die Sicherheitsgruppe für EC2-Instanzen muss über folgende Regeln verfügen:

	![Firewallregeln](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Aktivieren Sie den Schutz für Computer](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Fügen Sie die zu schützenden Computer der Replikationsgruppe hinzu.

	![Aktivieren des Schutzes](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. Die EC2-Instanzen, die Sie zu Azure migrieren möchten, können anhand der privaten IP-Adresse der Instanz ermittelt werden. Diese können Sie über die EC2-Konsole abrufen. In der Schutzgruppe können Sie die private IP-Adresse jeder Instanz hinzufügen.

	![Aktivieren des Schutzes](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	Nachdem Sie der Gruppe einen Computer hinzugefügt haben, wird der Schutz aktiviert, und die erste Replikation wird gemäß Einstellungen für die Schutzgruppe ausgeführt.

8. [Führen Sie ein nicht geplantes Failover durch.](site-recovery-failover.md#run-an-unplanned-failover) Nach Abschluss der ersten Replikation können Sie für jeden virtuellen Computer ein nicht geplantes Failover von AWS auf Azure durchführen. Optional können Sie einen Wiederherstellungsplan erstellen und ein nicht geplantes Failover durchführen, um mehrere virtuelle Computer von AWS zu Azure zu migrieren. Weitere Informationen zu Wiederherstellungsplänen finden Sie [hier](site-recovery-create-recovery-plans.md).
		
## Nächste Schritte

Weitere Informationen zu anderen Replikationsszenarien finden Sie in [Was ist Azure Site Recovery?](site-recovery-overview.md).

<!---HONumber=AcomDC_0323_2016-->