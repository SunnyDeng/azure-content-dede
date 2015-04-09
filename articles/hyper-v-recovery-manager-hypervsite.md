<properties 
	pageTitle="Lernprogramm: Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Computern zwischen einem lokalen Hyper-V-Standort und Azure." 
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

# Lernprogramm: Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure


<h2><a id="overview" name="overview" href="#overview"></a>Übersicht</h2>
<p>Azure Site Recovery unterstützt Ihre Strategie zur Gewährleistung der Kontinuität des Geschäftsbetriebs, in dem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden.<p>

In diesem Lernprogramm wird beschrieben, wie Azure Site Recovery zum Schutz von Arbeitslasten auf einem Hyper-V-Server mit Windows Server 2012 R2 an einem lokalen Standort bereitgestellt wird. Die virtuellen Computer auf dem Hyper-V-Server werden mithilfe der Hyper-V-Replikation in Azure repliziert. Diese Bereitstellung ist besonders hilfreich, wenn Sie Hyper-V-Server in Ihrer Niederlassung oder Zweigstelle betreiben, System Center VMM aber nicht bereitgestellt ist.


<LI>In diesem Lernprogramm werden der schnellste Bereitstellungspfad mit Mindest- und Standardeinstellungen verwendet.
Die vollständigen Bereitstellungsanweisungen finden Sie in den Handbüchern <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Planung</a> und <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Bereitstellung</a>.</LI>
<LI>In der <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Übersicht über Azure Site Recovery</a> finden Sie Informationen zu weiteren Bereitstellungsszenarien für Azure Site Recovery.</LI>
<LI>Wenn Sie während dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen</a> (in englischer Sprache), oder stellen Sie Ihre Fragen im <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>Voraussetzungen</h2>
<div class="dev-callout"> 
<P>Vergewissern Sie sich, dass Sie alle Voraussetzungen erfüllen, ehe Sie starten.</P>

<UL>
<LI><b>Azure-Konto</b> - Sie benötigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, finden Sie weitere Informationen unter <a href="http://aka.ms/try-azure">1 Monat kostenlose Testversion</a>. Preisinformationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Preise</a>.</LI>
<LI><b>Hyper-V</b> - Am lokalen Quellstandort benötigen Sie mindestens einen Server mit Windows Server 2012 R2 und der Hyper-V-Rolle. Der Hyper-V-Server muss mindestens einen virtuellen Computer enthalten. Der Hyper-V-Server muss entweder direkt oder über einen Proxy mit dem Internet verbunden sein.</LI>
<LI><b>Virtuelle Computer </b> - Virtuelle Computer, die Sie schützen möchten, müssen die Azure-Voraussetzungen für virtuelle Computer erfüllen. Siehe <a href="http://go.microsoft.com/fwlink/?LinkId=522287">Unterstützung virtueller Computer</a>.</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Schritte im Lernprogramm</h2>
Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:
<UL>
<LI><a href="#vault">Schritt 1: Erstellen eines Tresors</a>- Erstellen und konfigurieren Sie einen Azure Site Recovery-Tresor.</LI>
<LI><a href="#site">Schritt 2: Erstellen eines Hyper-V-Standorts</a>- Erstellen Sie einen Hyper-V-Standort als logischen Container aller Hyper-V-Server mit den virtuellen Computern, die Sie schützen möchten.</LI>
<LI><a href="#download">Schritt 3: Vorbereiten der Hyper-V-Server</a>- Generieren Sie einen Registrierungsschlüssel, und laden Sie die Setupdatei für den Anbieter herunter. Führen Sie die Datei auf allen Hyper-V-Servern am Standort aus, und wählen Sie den Schlüssel, um den Server im Tresor zu registrieren.</LI>
<LI><a href="#resources">Schritt 4: Vorbereiten von Ressourcen</a>- Erstellen Sie ein Azure-Speicherkonto zum Speichern replizierter virtueller Computer.</LI>
<LI><a href="#protectiongroup">Schritt 5: Erstellen und Konfigurieren von Schutzgruppen</a>- Erstellen Sie eine Schutzgruppe, und ordnen Sie ihr Schutzeinstellungen zu. Die Schutzeinstellungen gelten für alle virtuellen Computer, die Sie der Gruppe hinzufügen.</LI>
<LI><a href="#enablevirtual">Schritt 6: Aktivieren des Schutzes für virtuelle Computer</a>- Aktivieren Sie Schutz für virtuelle Computer, indem Sie sie einer Schutzgruppe hinzufügen.</LI>
<LI><a href="#recovery plans">Schritt 7: Testen der Bereitstellung</a>- Führen Sie ein Testfailover für einen virtuellen Computer aus.</LI>

</UL>




<a name="vault"></a> <h3>Schritt 1: Erstellen eines Tresors</h3>

1. Melden Sie sich im [Verwaltungsportal](https://manage.windowsazure.com) an.


2. Erweitern Sie <b>Data Services</b>, dann <b>Recovery Services</b>, und klicken Sie auf <b>Site Recovery-Tresor</b>.


3. Klicken Sie auf <b>Neu erstellen</b> und anschließend auf <b>Schnellerfassung</b>.
	
4. Geben Sie unter <b>Name</b> einen Anzeigenamen ein, mit dem der Tresor identifiziert wird.

5. Wählen Sie unter <b>Region</b> die geografische Region für den Tresor aus. Welche Regionen unterstützt werden, erfahren Sie unter "Geografische Verfügbarkeit" unter <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery-Preisdetails</a>.

6. Klicken Sie auf <b>Tresor erstellen</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als <b>aktiv</b> aufgelistet.</P>

<a name="site"></a> <h3>Schritt 2: Erstellen eines Hyper-V-Standorts</h3>

1. Klicken Sie auf der Seite Recovery Services auf den Tresor, um die Seite Schnellstart zu öffnen. Die Seite "Schnellstart" kann auch jederzeit über das Symbol geöffnet werden.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen einem lokalen Hyper-V-Standort und Azure** aus.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. Klicken Sie in **Hyper-V-Standort erstellen** auf **Hyper-V-Standort erstellen**. Geben Sie einen Standortnamen ein, und speichern Sie ihn.

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>Schritt 3: Vorbereiten der Hyper-V-Server</h3>
	

1. Klicken Sie unter **Hyper-V-Server vorbereiten** auf **Registrierungsschlüssel herunterladen**.
2. Klicken Sie auf der Seite **Registrierungsschlüssel herunterladen** neben dem Standort auf **Herunterladen**. Laden Sie den Schlüssel an einen sicheren Speicherort herunter, auf den der Hyper-V-Server einfach zugreifen kann. Der Schlüssel ist nach der Erstellung 5 Tage lang gültig.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. Klicken Sie auf <b>Anbieter herunterladen</b>, um die neueste Version abzurufen.
5. Führen Sie die Datei auf allen Hyper-V-Servern aus, die Sie im Tresor registrieren möchten. Die Datei installiert zwei Komponenten:
	- **Azure Site Recovery-Anbieter** - ist zuständig für die Kommunikation und Orchestrierung zwischen Hyper-V-Server und Azure Site Recovery-Portal. 
	- **Azure Recovery Services-Agent** -  ist zuständig für den Datentransport zwischen virtuellen Computern auf dem Hyper-V-Quellserver und dem Azure-Speicher. 
6. Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieter- und Agent-Updates gemäß Ihrer Microsoft Update-Richtlinie installiert.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. Geben Sie in **Installation** an, wo der Anbieter und der Agent auf dem Hyper-V-Server installiert werden sollen.

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. Fahren Sie nach der Installation mit der Einrichtung fort, um den Server im Tresor zu registrieren.

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. Geben Sie auf der Seite **Internetverbindung** an, wie sich der Anbieter mit Azure Site Recovery verbindet. Wählen Sie <b>Standard-Systemproxyeinstellungen verwenden</b> aus, um die auf dem Server konfigurierten Standardeinstellungen für die Internetverbindung zu verwenden. 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. Klicken Sie auf der Seite **Tresoreinstellungen** auf **Durchsuchen**, um die Schlüsseldatei auszuwählen. Geben Sie das Azure Site Recovery-Abonnement, den Tresornamen und den Hyper-V-Standort an, zu dem der Hyper-V-Server gehört.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. Die Registrierung des Servers im Tresor beginnt.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. Nach der Registrierung werden Metadaten von Azure Site Recovery vom Hyper-V-Server abgerufen, und der Server wird im Tresor auf der Seite **Server** auf der Registerkarte **Hyper-V-Standorte** angezeigt.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>Schritt 4: Vorbereiten von Ressourcen</h3>


1. Wählen Sie auf der Seite "Schnellstart" unter **Ressourcen vorbereiten** die Option **Speicherkonto erstellen** aus, um ein Azure-Speicherkonto zu erstellen, sofern noch nicht vorhanden. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Tresor befinden und dem gleichen Abonnement zugeordnet sein.

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>Schritt 5: Erstellen und Konfigurieren von Schutzgruppen</h3>

<p>In Schutzgruppen werden virtuelle Computer mit denselben Schutzeinstellungen gruppiert. Sie weisen einer Schutzgruppe Schutzeinstellungen zu, die für alle virtuellen Computer gelten, die Sie der Gruppe hinzufügen.</p>
1. Klicken Sie in **Schutzgruppen erstellen und konfigurieren** auf **Schutzgruppe erstellen**. Falls bestimmte Voraussetzungen nicht erfüllt sind, wird eine Meldung ausgegeben. Sie können auf **Details anzeigen** klicken, um weitere Informationen zu erhalten.

2. Fügen Sie auf der Registerkarte **Schutzgruppe** eine neue Schutzgruppe hinzu. Geben Sie einen Namen, den Hyper-V-Quellstandort, das Ziel in **Azure**, den Namen Ihres Azure Site Recovery-Abonnements und das Azure-Speicherkonto an.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. Lassen Sie unter **Replikationseinstellungen** die Standardeinstellungen unverändert.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>Schritt 6: Aktivieren des Schutzes für virtuelle Computer</h3>
<p>Aktivieren Sie Schutz für virtuelle Computer, indem Sie sie einer Schutzgruppe hinzufügen.</p>

1. Klicken Sie auf der Registerkarte <b>Virtuelle Computer</b> der Schutzgruppe auf <b>Virtuelle Computer zum Aktivieren von Schutz zu Schutzgruppen hinzufügen</b>.
2. Klicken Sie auf **Schutz für virtuelle Computer aktivieren**, und wählen Sie dann den virtuellen Computer aus, den Sie schützen möchten. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

Der Auftrag Schutz aktivieren wird ausgeführt. Der Fortschritt kann auf der Registerkarte **Aufträge** nachverfolgt werden. Nach der Ausführung des Auftrags zum Abschließen des Schutzes ist der virtuelle Computer für ein Failover bereit. Nach dem Aktivieren des Schutzes und der ersten Replikation können Sie die virtuellen Computer in Azure sehen.

In Azure Site-Recovery können Sie geschützte virtuelle Computer unter **Geschützte Elemente** > **Schutzgruppen** > *protectiongroup_name* > **Virtuelle Computer** anzeigen. 


<h3><a id="recoveryplans"></a>Schritt 7: Testen der Bereitstellung</h3>

Testen Sie Ihre Bereitstellung zum Simulieren Ihrer Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk, ohne die Produktionsumgebung zu beeinträchtigen. Führen Sie hierzu ein Testfailover für einen geschützten virtuellen Computer aus.

1. Wählen Sie unter **Geschützte Elemente** > **Schutzgruppen** > *protectiongroup_name* > **Virtuelle Computer** den für ein Failover gewünschten virtuellen Computer aus, und klicken Sie auf **Testfailover**.
2. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** aus. 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. Fortschritt und Status können Sie auf der Registerkarte **Auftrag** nachverfolgen.
4. Wenn das Failover die Phase **Test abschließen** erreicht, schließen Sie die Überprüfung wie folgt ab:
	- Zeigen Sie nach dem Failover den replizierten virtuellen Computer im Azure-Portal an. Prüfen Sie, ob der virtuelle Computer erfolgreich startet.
	- Wenn Sie den Zugriff auf virtuelle Computer aus Ihrem lokalen Netzwerk eingerichtet haben, können Sie eine Remotedesktopverbindung mit dem virtuellen Computer herstellen.
	- Klicken Sie auf **Test abschließen**, um ihn zu beenden.
	- Klicken Sie auf **Notizen**, um alle Beobachtungen im Zusammenhang mit dem Testfailover aufzuzeichnen und zu speichern.
	- Klicken Sie auf **Das Testfailover ist abgeschlossen**. Bereinigen Sie die Testumgebung, um den virtuellen Testcomputer automatisch auszuschalten und ihn zu löschen.

<h2><a id="runtest" name="runtest" href="#runtest"></a>Aktivität überwachen</h2>
<p>Sie können die Registerkarte <b>Aufträge</b> und das <b>Dashboard</b> verwenden, um die Hauptaufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Hierzu zählen das Konfigurieren des Schutzes für eine Cloud, das Aktivieren und Deaktivieren des Schutzes für einen virtuellen Computer, das Durchführen eines Failovers (geplant, nicht geplant oder Test) und das Durchführen eines Commitvorgangs für ein nicht geplantes Failover.</p>

<p>Auf der Registerkarte <b>Aufträge</b> können Sie Aufträge anzeigen, Details zu den einzelnen Aufträgen und Fehlern aufrufen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und nicht erfolgreiche Aufträge neu starten.</p>

<p>Im <b>Dashboard</b> können Sie die aktuellen Versionen der Installationsdateien für Anbieter und Agent herunterladen, Konfigurationsinformationen für den Tresor abrufen, die Anzahl der virtuellen Computer anzeigen, deren Schutz vom Tresor verwaltet wird, die letzten Aufträge anzeigen, das Tresorzertifikat verwalten und virtuelle Computer erneut synchronisieren.</p>

<p>Weitere Informationen zur Interaktion mit Aufträgen und dem Dashboard finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Betriebs- und Überwachungshandbuch</a>.</p>

<h2><a id="next" name="next" href="#next"></a>Nächste Schritte</h2>
<UL>
<LI>Informationen zur Planung und Bereitstellung von Azure Site Recovery in einer realen Produktionsumgebung finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Planungshandbuch für Azure Site Recovery</a> und im <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Bereitstellungshandbuch für Azure Site Recovery</a>.</LI>
<LI>Wenn Sie Fragen haben, besuchen Sie das <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a></LI> 
</UL>

<!--HONumber=49-->