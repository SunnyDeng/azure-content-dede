<properties
	pageTitle="Migrieren von virtuellen Azure-IaaS-Computern aus einer Azure-Region in eine andere"
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
	ms.date="08/26/2015"
	ms.author="raynew"/>

#  Migrieren von virtuellen Azure-IaaS-Computern zwischen Azure-Regionen


## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden. Eine vollständige Liste mit Bereitstellungsszenarien finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

In diesem Artikel wird beschrieben, wie virtuelle Azure-IaaS-Computer mithilfe von Site Recovery aus einer Azure-Region in eine andere migriert werden. In diesem Artikel werden größtenteils die gleichen Schritte verwendet wie im Artikel [Einrichten von Schutz zwischen lokalen virtuellen VMware-Computern oder physischen Servern und Azure](site-recovery-vmware-to-azure.md). Dieser enthält ausführliche Informationen zu den einzelnen Bereitstellungsschritten.

## Erste Schritte

Sie benötigen Folgendes:

- **Konfigurationsserver**: Ein virtueller Azure-Computer, der als Konfigurationsserver fungiert. Der Konfigurationsserver koordiniert die Kommunikation zwischen lokalen Computern und Azure-Servern.
- **Masterzielserver**: Ein virtueller Azure-Computer, der als Masterzielserver fungiert. Dieser Server empfängt und speichert replizierte Daten der geschützten Computer.
- **Prozessserver**: Ein virtueller Computer mit Windows Server 2012 R2. Geschützte virtuelle Computer senden Replikationsdaten an diesen Server.
- **Virtuelle IaaS-Computer**: die virtuellen Computer, die Sie migrieren möchten.

- Weitere Informationen zu diesen Komponenten finden Sie unter [Was benötige ich?](site-recovery-vmware-to-azure.md#what-do-i-need)
- Lesen Sie auch die Richtlinien zur [Kapazitätsplanung](site-recovery-vmware-to-azure.md#capacity-planning), und vergewissern Sie sich, dass alle [Bereitstellungsvoraussetzungen](site-recovery-vmware-to-azure.md#before-you-start) erfüllt sind, bevor Sie beginnen.

## Bereitstellungsschritte

1. [Erstellen eines Tresors](site-recovery-vmware-to-azure.md/#step-1-create-a-vault)
2. [Bereitstellen eines Konfigurationsservers](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) (virtueller Azure-Computer)
3. [Bereitstellen des Masterzielservers](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) (virtueller Azure-Computer)
4. [Bereitstellen eines Prozessservers](site-recovery-vmware-to-azure.md#step-4-deploy-the-on-premises-process-server) Beachten Sie Folgendes:

	- Sie sollten den Prozessserver im gleichen virtuellen Netzwerk/Subnetz wie die virtuellen IaaS-Computer bereitstellen, die Sie migrieren möchten. ![Virtuelle IaaS-Computer](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure1.png)

	- Vergewissern Sie sich nach dem Bereitstellen des Prozessservers, dass er mit den zu migrierenden virtuellen Computern kommunizieren kann.
	- Auf jedem virtuellen Computer, den Sie schützen möchten, muss der Mobilitätsdienst installiert sein. Dieser Dienst sendet Daten an den Prozessserver. Der Mobilitätsdienst kann manuell installiert oder mittels Push automatisch durch den Prozessserver installiert werden, wenn der Schutz für den virtuellen Computer aktiviert wird. Konfigurieren Sie Firewallregeln für die zu migrierenden virtuellen IaaS-Computer, um die Push-Installation des Diensts zu ermöglichen. 

	- Wenn der Prozessserver bereitgestellt und beim Konfigurationsserver im Site Recovery-Tresor registriert wurde, wird er in der Site Recovery-Konsole auf der Registerkarte **Konfigurationsserver** angezeigt. Beachten Sie, dass dies bis zu 15 Minuten dauern kann.
	
		![Prozessserver](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure2.png)

5. [Installieren Sie die neuesten Updates](site-recovery-vmware-to-azure.md#step-5-install-latest-updates). Stellen Sie sicher, dass alle installierten Komponentenserver auf dem neuesten Stand sind.
6. [Erstellen Sie eine Schutzgruppe](site-recovery-vmware-to-azure.md#step-7-create-a-protection-group). Richten Sie eine Schutzgruppe ein, um mithilfe von Site Recovery migrierte virtuelle Computer zu schützen. Die für eine Gruppe angegebenen Replikationseinstellungen gelten für alle virtuellen Computer, die Sie dieser Gruppe hinzufügen. 
7. [Richten Sie virtuelle Computer ein](site-recovery-vmware-to-azure.md#step-8-set-up-machines-you-want-to-protect). Der Mobilitätsdienst muss für jeden virtuellen Computer installiert werden (entweder automatisch oder manuell).
8. [Schritt 8: Aktivieren des Schutzes für virtuelle Computer](site-recovery-vmware-to-azure.md#step-9-enable-protection) Fügen Sie virtuelle Computer einer Schutzgruppe hinzu, um den Schutz zu aktivieren. Beachten Sie Folgendes:

	- Sie können die virtuellen IaaS-Computer, die Sie zu Azure migrieren möchten, mithilfe der privaten IP-Adressen der virtuellen Computer ermitteln. Diese Adresse finden Sie auf dem Dashboard des virtuellen Computers in Azure.
	-  Klicken Sie auf der Registerkarte für die erstellte Schutzgruppe auf „Computer hinzufügen“ > „Physische Computer“. ![EC2-Ermittlung](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure3.png)
	- Geben Sie die private IP-Adresse des virtuellen Computers an.
		- ![EC2-Ermittlung](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure4.png)
	- Der Schutz wird aktiviert, und die erste Replikation wird gemäß den entsprechenden Einstellungen für die Schutzgruppe ausgeführt.
9. [Schritt 9: Führen Sie ein nicht geplantes Failover durch](site-recovery-failover.md#run-an-unplanned-failover). Nach Abschluss der ersten Replikation können Sie ein nicht geplantes Failover von einer Azure-Region auf eine andere durchführen. Optional können Sie einen Wiederherstellungsplan erstellen und ein nicht geplantes Failover durchführen, um mehrere virtuelle Computer zwischen Regionen zu migrieren. Weitere Informationen zu Wiederherstellungsplänen finden Sie [hier](site-recovery-create-recovery-plans.md).
		
## Nächste Schritte

Veröffentlichen von Kommentaren oder Fragen im [Site Recovery-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=Oct15_HO3-->