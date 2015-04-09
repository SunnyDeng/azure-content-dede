<properties 
	pageTitle="Lernprogramm: Einrichten von Schutz zwischen lokalen VMware-Standorten" 
	description="InMage in Azure Site Recovery verarbeitet die Replikation, das Failover und die Wiederherstellung zwischen lokalen VMware-Standorten." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Lernprogramm: Einrichten von Schutz zwischen lokalen VMware-Standorten


<h2><a id="overview" name="overview" href="#overview"></a>Übersicht</h2>

<p>InMage in Azure Site Recovery ermöglicht eine Echtzeitreplikation zwischen lokalen VMware-Standorten. InMage gehört zum Funktionsumfang von Abonnements des Azure Site Recovery-Diensts.</p>




<h2><a id="before" name="before" href="#before"></a>Voraussetzungen</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Azure-Konto</b> - Sie benötigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, finden Sie weitere Informationen unter <a href="http://aka.ms/try-azure">1 Monat kostenlose Testversion</a>. Preisinformationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Preise</a>.</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Schritte im Lernprogramm</h2>
<a name="vault"></a> <h3>Schritt 1: Erstellen eines Tresors und Herunterladen von InMage</h3>

1. Melden Sie sich im [Verwaltungsportal](https://manage.windowsazure.com) an.


2. Erweitern Sie <b>Data Services</b>, dann <b>Recovery Services</b>, und klicken Sie auf <b>Site Recovery-Tresor</b>.


3. Klicken Sie auf <b>Neu erstellen</b> und anschließend auf <b>Schnellerfassung</b>.
	
4. Geben Sie unter <b>Name</b> einen Anzeigenamen ein, mit dem der Tresor identifiziert wird.

5. Wählen Sie unter <b>Region</b> die geografische Region für den Tresor aus. Sie finden eine Liste der unterstützten Regionen im Abschnitt zur geografischen Verfügbarkeit auf der Seite <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery-Preise</a>.

6. Klicken Sie auf <b>Tresor erstellen</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als <b>aktiv</b> aufgelistet.</P>

<a name="upload"></a> <h3>Schritt 2: Konfigurieren des Tresors</h3>


1. Klicken Sie auf der Seite <b>Recovery Services</b> auf den Tresor, um die Seite Schnellstart zu öffnen. Die Seite "Schnellstart" kann auch jederzeit über das Symbol geöffnet werden.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste die Option **Zwischen zwei lokalen VMware-Standorten** aus.
3. Laden Sie InMage Scout herunter.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. Richten Sie die Replikation zwischen zwei VMWare-Standorten gemäß der InMage-Dokumentation ein, die mit dem Produkt heruntergeladen wird.


<h2><a id="next" name="next" href="#next"></a>Nächste Schritte</h2>
<UL>

<LI>Wenn Sie Fragen haben, besuchen Sie das <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI> 
</UL>

<!--HONumber=49-->