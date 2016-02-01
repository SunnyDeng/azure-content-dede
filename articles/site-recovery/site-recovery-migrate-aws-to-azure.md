<properties
	pageTitle="Migrieren virtueller Windows-Computer von Amazon Web Services (AWS) zu Azure mit Site Recovery | Microsoft Azure"
	description="Verwenden Sie Azure Site Recovery, um in Amazon Web Services (AWA) ausgeführte virtuelle Windows-Computer zu Azure zu migrieren."
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
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Migrieren virtueller Windows-Computer in Amazon Web Services (AWS) zu Azure mit Azure Site Recovery


## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungen aufeinander abgestimmt werden. Eine vollständige Liste mit Bereitstellungsszenarien finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

Dieser Artikel beschreibt, wie Sie in AWS ausgeführte Windows-Instanzen mithilfe von Site Recovery zu Azure migrieren oder ein Failover auf Azure durchführen. In diesem Artikel werden größtenteils die gleichen Schritte verwendet wie im Artikel [Einrichten von Schutz zwischen lokalen virtuellen VMware-Computern oder physischen Servern und Azure](site-recovery-vmware-to-azure-classic-legacy.md). Dieser enthält ausführliche Informationen zu den einzelnen Bereitstellungsschritten.

## Erste Schritte

Sie benötigen Folgendes:

- **Konfigurationsserver**: Ein virtueller Azure-Computer, der als Konfigurationsserver fungiert. Der Konfigurationsserver koordiniert die Kommunikation zwischen lokalen Computern und Azure-Servern.
- **Masterzielserver**: Ein virtueller Azure-Computer, der als Masterzielserver fungiert. Dieser Server empfängt und speichert replizierte Daten der geschützten Computer.
- **Prozessserver**: Ein virtueller Computer mit Windows Server 2012 R2. Geschützte virtuelle Computer senden Replikationsdaten an diesen Server.
- **EC2-VM-Instanzen**: Die Instanzen, die Sie migrieren und anschließend schützen möchten.

- Weitere Informationen zu diesen Komponenten finden Sie unter [Was benötige ich?](site-recovery-vmware-to-azure-classic-legacy.md#what-do-i-need).
- Lesen Sie auch die Richtlinien zur [Kapazitätsplanung](site-recovery-vmware-to-azure-classic-legacy.md#capacity-planning), und vergewissern Sie sich, dass alle [Bereitstellungsvoraussetzungen](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start) erfüllt sind, bevor Sie beginnen.

## Bereitstellungsschritte

1. [Erstellen eines Tresors](site-recovery-vmware-to-azure-classic-legacy.md#step-1-create-a-vault)
2. [Bereitstellen eines Konfigurationsservers](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) (virtueller Azure-Computer)
3. [Bereitstellen des Masterzielservers](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) (virtueller Azure-Computer)
4. [Bereitstellen eines Prozessservers](site-recovery-vmware-to-azure-classic-legacy.md#step-4-deploy-the-on-premises-process-server) Beachten Sie Folgendes:

	- Der Prozessserver muss im gleichen Subnetz/in der gleichen Amazon Virtual Private Cloud bereitgestellt werden wie Ihre EC2-Instanzen. ![EC2-Instanzen](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration1.png)

	- Vergewissern Sie sich nach dem Bereitstellen des Prozessservers, dass er mit den zu migrierenden EC2-Instanzen kommunizieren kann.
	- Auf jedem virtuellen Computer, den Sie schützen möchten, muss der Mobilitätsdienst installiert sein. Dieser Dienst sendet Daten an den Prozessserver. Der Mobilitätsdienst kann manuell installiert oder mittels Push automatisch durch den Prozessserver installiert werden, wenn der Schutz für den virtuellen Computer aktiviert wird. Konfigurieren Sie Firewallregeln für die zu migrierenden EC2-Instanzen, um die Push-Installation des Diensts zu ermöglichen. Die Sicherheitsgruppe für EC2-Instanzen muss über folgende Regeln verfügen:

		![Firewallregeln](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration2.png)

	- Wenn der Prozessserver bereitgestellt und beim Konfigurationsserver im Site Recovery-Tresor registriert wurde, wird er in der Site Recovery-Konsole auf der Registerkarte **Konfigurationsserver** angezeigt. Dies kann bis zu 15 Minuten dauern.
	
		![Prozessserver](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration3.png)

5. [Installieren Sie die neuesten Updates](site-recovery-vmware-to-azure-classic-legacy.md#step-5-install-latest-updates). Stellen Sie sicher, dass alle installierten Komponentenserver auf dem neuesten Stand sind.
6. [Erstellen Sie eine Schutzgruppe.](site-recovery-vmware-to-azure-classic-legacy.md#step-7-create-a-protection-group) Richten Sie eine Schutzgruppe ein, um migrierte Instanzen mithilfe von Site Recovery zu schützen. Die für eine Gruppe angegebenen Replikationseinstellungen gelten für alle Instanzen, die Sie dieser Gruppe hinzufügen. 
7. [Richten Sie virtuelle Computer ein.](site-recovery-vmware-to-azure-classic-legacy.md#step-8-set-up-machines-you-want-to-protect) Der Mobilitätsdienst muss für jede Instanz installiert werden (entweder automatisch oder manuell).
8. [Aktivieren Sie den Schutz für virtuelle Computer.](site-recovery-vmware-to-azure-classic-legacy.md#step-9-enable-protection) Fügen Sie Instanzen einer Schutzgruppe hinzu, um den Schutz zu aktivieren. Beachten Sie Folgendes:

	- Die EC2-Instanzen, die Sie zu Azure migrieren möchten, können anhand der privaten IP-Adresse der Instanz ermittelt werden. Diese können Sie über die EC2-Konsole abrufen.
	-  Klicken Sie auf der Registerkarte für die erstellte Schutzgruppe auf „Computer hinzufügen“ > „Physische Computer“. ![EC2-Ermittlung](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration4.png)
	- Geben Sie die private IP-Adresse der Instanz an.
		- ![EC2-Ermittlung](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration5.png)
	- Der Schutz wird aktiviert, und die erste Replikation wird gemäß den entsprechenden Einstellungen für die Schutzgruppe ausgeführt.
9. [Führen Sie ein nicht geplantes Failover durch.](site-recovery-failover-classic-legacy.md#run-an-unplanned-failover) Nach Abschluss der ersten Replikation können Sie ein nicht geplantes Failover von AWS auf Azure durchführen. Optional können Sie einen Wiederherstellungsplan erstellen und ein nicht geplantes Failover durchführen, um mehrere virtuelle Computer von AWS zu Azure zu migrieren. Weitere Informationen zu Wiederherstellungsplänen finden Sie [hier](site-recovery-create-recovery-plans.md).
		
## Nächste Schritte

Veröffentlichen von Kommentaren oder Fragen im [Site Recovery-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0121_2016-->