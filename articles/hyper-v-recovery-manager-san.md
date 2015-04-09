<properties 
	pageTitle="Lernprogramm: Einrichten von Schutz zwischen lokalen VMM-Standorten mit einem SAN" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Hyper-V-Computern zwischen lokalen Standorten mithilfe der SAN-Replikation." 
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

# Lernprogramm: Einrichten von Schutz zwischen lokalen VMM-Standorten mit einem SAN



<h2><a id="overview" name="overview" href="#overview"></a>Übersicht</h2>
<p>Azure Site Recovery unterstützt Ihre Strategie zur Gewährleistung der Kontinuität des Geschäftsbetriebs, in dem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden.<p>

<P>In diesem Lernprogramm wird beschrieben, wie Sie Azure Site Recovery bereitstellen, um den Schutz für Arbeitsauslastungen auf einem Hyper-V-Server, der von System Center VMM verwaltet wird, automatisch zu orchestrieren und zu automatisieren, indem Sie die SAN-Replikation (Storage Array Network) an einen anderen lokalen VMM-Standort aktivieren. In diesem Lernprogramm werden nach Möglichkeit der schnellste Bereitstellungspfad und Standardeinstellungen verwendet. Inhalt dieses Szenarios</P>

<ul>
	<li>Sie nutzen Ihre vorhandene SAN-Infrastruktur zum Schützen unternehmenswichtiger in Hyper-V-Clustern bereitgestellter Anwendungen.</li>
	<li>Die SAN-Replikation bietet Unterstützung für Gastcluster und gewährleistet abhängig von den Fähigkeiten von Speicherarrays eine Replikationskonsistenz auf verschiedenen Ebenen einer Anwendung mithilfe einer synchronisierten und nicht synchronisierten Replikation.</li>
	<li>Sie konfigurieren und aktivieren Schutzeinstellungen in VMM und dem Azure Site Recovery-Tresor. Sie bestimmen und klassifizieren SAN-Speicher in VMM, stellen LUNs bereit und weisen Hyper-V-Clustern Speicher zu. Azure Site Recovery automatisiert die Replikation und orchestriert das Failover. </li>
	</ul>

<UL>
<LI>Die vollständigen Bereitstellungsanweisungen finden Sie in den Handbüchern <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planung</a> und <a href="http://go.microsoft.com/fwlink/?LinkId=519251">Bereitstellung</a>.</LI>
<LI>In der <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Übersicht über Azure Site Recovery</a> finden Sie Informationen zu weiteren Bereitstellungsszenarien für Azure Site Recovery.</LI>
<LI>Wenn Sie während dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen</a> (in englischer Sprache), oder stellen Sie Ihre Fragen im <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI>
</UL>




<h2><a id="before" name="before" href="#before"></a>Vorbereitung</h2>
<div class="dev-callout"> 
<P>Vergewissern Sie sich, dass Sie alle Voraussetzungen erfüllen, ehe Sie diese exemplarische Vorgehensweise starten.</P>

<UL>
<LI><b>Azure-Konto</b> - Sie benötigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, finden Sie weitere Informationen unter <a href="http://aka.ms/try-azure">1 Monat kostenlose Testversion</a>. Preisinformationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Preise</a>.</LI>
<LI>Wenn Sie wissen möchten, welche Informationen während Azure Site Recovery-Vorgängen gesammelt, verarbeitet oder übermittelt werden, lesen Sie die <a href="http://go.microsoft.com/fwlink/?LinkId=513016">Datenschutzanforderungen </a> auf der MSDN-Website.</LI>
<LI><b>VMM-Server</b> - Sie benötigen an jedem lokalen Standort einen VMM-Server, der als eigenständiger physischer oder virtueller Server oder als virtueller Cluster bereitgestellt ist, auf dem System Center 2012 R2 mit <a href="http://go.microsoft.com/fwlink/?LinkId=517707">VMM Updaterollup 5.0 Preview</a> ausgeführt wird.</LI>
<LI>Sie benötigen einen am primären und sekundären Standort bereitgestellten Hyper-V-Hostcluster mit mindestens Windows Server 2012 mit den neuesten Updates.</LI>
<LI><b>VMM-Clouds</b> - Sie benötigen mindestens eine Cloud auf dem primären VMM-Server, den Sie schützen möchten, und eine auf dem sekundären VMM-Server. Die primäre Cloud, die Sie schützen möchten, muss Folgendes enthalten:<UL>
	<LI>Eine oder mehrere VMM-Hostgruppen.</LI>
	<LI>Einen oder mehrere Hyper-V-Cluster in jeder Hostgruppe.</LI>
	<li>Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud.</li>
		</UL>Weitere Informationen zum Einrichten von VMM-Clouds finden Sie im Planungshandbuch unter <a href="http://go.microsoft.com/fwlink/?LinkId=513015">Planen der VMM-Infrastruktur</a>.</LI>
<LI><b>SAN-Speicher</b> - Mithilfe der SAN-Replikation können virtuelle Computer in Gastclustern mit iSCSI- oder Fibre Channel-Speicher oder gemeinsam genutzten virtuellen Festplatten (VHDX) replizieren. Die SAN-Voraussetzungen sind wie folgt:</LI>
	<UL>
	<LI>Sie benötigen zwei eingerichtete SAN-Arrays, eines am primären und eines am sekundären Standort.</LI>
	<LI>Zwischen den Arrays muss Netzwerkinfrastruktur eingerichtet sein. Peering und Replikation müssen konfiguriert sein. Replikationslizenzen müssen in Einklang mit den Anforderungen an Speicherarrays eingerichtet sein.</LI>
	<LI>Zwischen den Hyper-V-Hostservern und dem Speicherarray müssen Netzwerkverbindungen eingerichtet sein, damit die Hosts über iSCSI oder Fibre Channel mit den Speicher-LUNs kommunizieren können.</LI>
	<LI>Eine Liste der unterstützten Speicherarrays finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=518669">Bereitstellen von Azure Site Recovery mit VMM und SAN - unterstützte Speicherarrays</a> (in englischer Sprache).</LI>
	
	<LI>Von EMC und NetApp bereitgestellte SMI-S-Anbieter müssen installiert sein, und die SAN-Arrays müssen vom Anbieter verwaltet werden. Richten Sie den Anbieter gemäß der Dokumentation ein.</LI>
	<LI>Vergewissern Sie sich, dass sich der SMI-S-Anbieter des Arrays auf einem Server befindet, auf den der VMM-Server über das Netzwerk über IP-Adresse oder den FQDN zugreifen kann.</LI>
	<LI>Bei dieser Bereitstellung muss jedem SAN-Array mindestens ein Speicherpool zur Verfügung stehen.</LI>
	<LI>Der VMM-Server am primären Standort muss das primäre Array und der sekundäre VMM-Server das sekundäre Array verwalten.</LI>
	</UL>
<LI><b>Netzwerke</b> - Sie können optional eine Netzwerkzuordnung konfigurieren, um sicherzustellen, dass replizierte virtuelle Computer nach einem Failover optimal auf Hyper-V-Hostservern platziert werden und dass sie sich mit den entsprechenden VM-Netzwerken verbinden können. Wenn eine Netzwerkzuordnung aktiviert ist, wird ein virtueller Computer am primären Standort mit einem Netzwerk verbunden, und sein Replikat am Zielstandort wird mit dem ihm zugeordneten Netzwerk verbunden. Wenn Sie keine Netzwerkzuordnung konfigurieren, werden virtuelle Computer nach einem Failover nicht mit VM-Netzwerken verbunden. In diesem Lernprogramm werden für die exemplarische Vorgehensweise die einfachsten Einstellungen ohne Netzwerkzuordnung beschrieben. Sie erfahren mehr dazu unter</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Netzwerkzuordnung</a> im Planungshandbuch.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522290">Aktivieren der Netzwerkzuordnung</a> im SAN-Bereitstellungshandbuch.</LI>
	</UL>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Schritte im Lernprogramm</h2>
Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:

1. <a href="#VMM">Schritt 1: Vorbereiten der VMM-Infrastruktur</a>
	- Integrieren und klassifizieren Sie SAN-Speicher in VMM.
	- Stellen Sie logische Einheiten (LUNs) und Speicherpools bereit, und weisen Sie Hyper-V-Clustern Speicher zu.
	- Erstellen Sie Replikationsgruppen für LUNs, die gemeinsam repliziert werden sollen. 
2. <a href="#vault">Schritt 2: Erstellen eines Tresors</a>
3. <a href="#download">Schritt 3: Installieren des Anbieters auf den VMM-Servern</a>- Generieren Sie im Tresor einen Registrierungsschlüssel, und laden Sie die Setupdatei für den Anbieter herunter. Sie führen das Setup auf den VMM-Servern aus, um den Anbieter zu installieren und die Server im Tresor zu registrieren.
4. <a href="#storage">Schritt 4: Zuordnen von Speicherarrays und -pools</a>- Ordnen Sie Arrays zu, um anzugeben, welcher sekundäre Speicherpool Replikationsdaten vom primären Pool empfängt. Ordnen Sie Speicher vor der Konfiguration des Cloudschutzes zu, da die Zuordnungsinformationen genutzt werden, wenn Sie den Schutz für Replikationsgruppen aktivieren.
5. <a href="#clouds">Schritt 5: Konfigurieren des Cloudschutzes</a>- Konfigurieren Sie Schutzeinstellungen für VMM-Clouds.
7. <a href="#replication">Schritt 6: Aktivieren von Replikationsgruppen</a>- Bevor Sie Schutz für virtuelle Computer aktivieren können, müssen Sie die Replikation für Speicherreplikationsgruppen aktivieren.
8. <a href="#enablevirtual">Schritt 7: Aktivieren des Schutzes für virtuelle Computer</a>- Aktivieren Sie nach der Replikation von Replikationsgruppen Schutz für virtuelle Computer.
9. <a href="#recovery plans">Schritt 8: Testen der Bereitstellung</a>- Durch Ausführen eines Testfailovers können testen, ob das Failover von virtuellen Computern und Daten wie erwartet erfolgt. 


<a name="VMM"></a> <h3>Schritt 1: Vorbereiten der VMM-Infrastruktur</h3>

<a name="SAN"></a> <h4>Integrieren und klassifizieren Sie SAN-Speicher in VMM.</h4>


1. Klicken Sie im Arbeitsbereich **Fabric** auf **Speicher**. Klicken Sie auf **Start** > **Ressourcen hinzufügen** > **Speichergeräte**, um dem Assistenten zum Hinzufügen von Speichergeräten zu öffnen.
2. Wählen Sie auf der Seite **Wählen Sie einen Speicheranbietertyp aus** den Eintrag **Von einem SMI-S-Anbieter erkannte und verwaltete SAN- und NAS-Geräte** aus.

	![Provider type](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Providertype.png)

3. Wählen Sie auf der Seite **Protokoll und Adresse des SMI-S-Speicheranbieters angeben** den Eintrag **SMI-S CIMXML** aus, und geben Sie die Einstellungen zum Herstellen einer Verbindung mit dem Anbieter an.
4. Geben Sie in **IP-Adresse oder FQDN des Anbieters** und **TCP/IP-Port** die Einstellungen zum Herstellen einer Verbindung mit dem Anbieter an. Für SMI-S CIMXML können Sie nur eine SSL-Verbindung verwenden.

	![Provider connect](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ConnectSettings.png)

5. Geben Sie in **Ausführendes Konto** ein ausführendes VMM-Konto ein, das auf den Anbieter zugreifen kann, oder erstellen Sie ein neues Konto.
6. VMM versucht auf der Seite **Informationen sammeln** automatisch, die Informationen zu Speichergeräten zu erfassen. Klicken Sie zum Wiederholen der Erkennung auf **Anbieter durchsuchen**. Bei Erfolg des Erkennungsprozesses werden erkannte Speicherarrays und -pools, Hersteller, Modell und Kapazität auf der Seite aufgeführt.

	![Discover storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Discover.png)

7. Wählen Sie in **Wählen Sie Speicherpools aus, die Sie unter "Verwaltung" platzieren, und weisen Sie dann eine Klassifizierung zu** die Speicherpools aus, die VMM verwalten sollen, und weisen Sie ihnen eine Klassifizierung zu. LUN-Informationen werden aus den Speicherpools importiert. Erstellen Sie LUNs basierend auf den zu schützenden Anwendungen, ihrer Kapazitätsanforderungen und Ihrer Anforderungen an die Elemente, die gemeinsam repliziert werden sollen.

	![Classify storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Classify.png)

<a name="LUNs"></a> <h4>Erstellen von LUNs und Zuweisen von Speicher</h4>


1. Nach der Integration von SAN-Speicher in VMM können Sie LUNs erstellen (bereitstellen). Einzelheiten finden Sie hier:
	
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518490">Auswählen eine Methode zum Erstellen von logischen Einheiten in VMM</a>
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518491">Bereitstellen von logischen Speichereinheiten in VMM</a>
2. Weisen Sie anschließend dem Hyper-V-Hostcluster Speicherkapazität zu, damit VMM Daten auf virtuellen Computern im bereitgestellten Speicher bereitstellen kann: 
	- Vor der Zuordnung von Speicher zum Cluster müssen Sie der VMM-Hostgruppe Speicher zuordnen, in der sich der Cluster befindet. Siehe hierzu <a href="http://go.microsoft.com/fwlink/?LinkId=518493">Zuordnen von logischen Speichereinheiten zu einer Hostgruppe</a> und <a href="http://go.microsoft.com/fwlink/?LinkId=518492">Zuordnen von Speicherpools zu einer Hostgruppe</a>.
	- Ordnen Sie dem Cluster Speicherkapazität gemäß den Angaben unter <a href="http://go.microsoft.com/fwlink/?LinkId=513017">Konfigurieren von Speicher für einen Hyper-V-Hostcluster in VMM</a> zu.
	

<a name="RGs"></a> <h4>Erstellen von Replikationsgruppen</h4>

1. Erstellen Sie eine Replikationsgruppe, die alle LUNs enthält, die gemeinsam repliziert werden sollen.
2. Öffnen Sie in der VMM-Konsole die Registerkarte **Replikationsgruppen** für die Eigenschaften von Speicherarrays, und klicken Sie auf **Neu**.
	![SAN replication group](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RepGroup.png)



<a name="vault"></a> <h3>Schritt 2: Erstellen eines Tresors</h3>
Erstellen Sie nach dem Überprüfen der Bereitstellungsvoraussetzungen einen Azure Site Recovery-Tresor. Alternativ können Sie einen vorhandenen Tresor nutzen und die SAN-Replikation konfigurieren.

1. Melden Sie sich im [Verwaltungsportal](https://manage.windowsazure.com) an.


2. Erweitern Sie <b>Data Services</b>, dann <b>Recovery Services</b>, und klicken Sie auf <b>Site Recovery-Tresor</b>.


3. Klicken Sie auf <b>Neu erstellen</b> und anschließend auf <b>Schnellerfassung</b>.
	
4. Geben Sie unter <b>Name</b> einen Anzeigenamen ein, mit dem der Tresor identifiziert wird.

5. Wählen Sie unter <b>Region</b> die geografische Region für den Tresor aus. Sie finden eine Liste der unterstützten Regionen im Abschnitt zur geografischen Verfügbarkeit auf der Seite <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery-Preise</a>.

6. Klicken Sie auf <b>Tresor erstellen</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als <b>aktiv</b> aufgelistet.</P>

<a name="upload"></a> <h3>Schritt 3: Konfigurieren des Tresors</h3>


1. Klicken Sie auf der Seite <b>Recovery Services</b> auf den Tresor, um die Seite Schnellstart zu öffnen. Die Seite "Schnellstart" kann auch jederzeit über das Symbol geöffnet werden.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen lokalen Hyper-V-Standorten mithilfe der Arrayreplikation** aus.


<a name="download"></a> <h3>Schritt 3: Installieren des Azure Site Recovery-Anbieters</h3>
Generieren Sie nach Erstellen des Tresors eine Registrierungsdatei, die einen Registrierungsschlüssel enthält. Sie wählen diese Datei aus, wenn Sie den Azure Site Recovery-Anbieter installieren. 

1. Klicken Sie auf der Seite <b>Schnellstart</b> unter **VMM-Server vorbereiten** auf **Registrierungsschlüsseldatei erstellen**. Der Schlüssel ist nach der Erstellung 5 Tage lang gültig. Laden Sie die Datei an einen sicheren Speicherort herunter, auf den VMM-Server zugreifen können. Wählen Sie z. B. eine Freigabe. Sie benötigen die Datei bei der Einrichtung des Anbieters.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartRegKey.png)


2. Klicken Sie auf der Seite <b>Schnellstart</b> unter **VMM-Server** vorbereiten auf <b>Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Servern herunterladen</b>, um die neueste Version der Anbieterinstallationsdatei herunterzuladen.
3. Führen Sie diese Datei auf den VMM-Quell- und -Zielservern aus.
4. Geben Sie unter **Voraussetzungsprüfung** an, dass der VMM-Dienst für die Anbietereinrichtung angehalten werden soll. Der Dienst wird angehalten und nach Abschluss der Einrichtung automatisch neu gestartet. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderPrereq.png)

5. Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieterupdates gemäß Ihrer Microsoft Updaterichtlinie installiert.

Fahren Sie nach der Installation des Anbieters mit der Einrichtung fort, um den Server im Tresor zu registrieren.

6. Geben Sie auf der Seite Internetverbindung an, wie der Anbieter auf dem VMM-Server die Verbindung mit dem Internet herstellen soll. Wählen Sie <b>Standard-Systemproxyeinstellungen verwenden</b> aus, um die auf dem Server konfigurierten Standardeinstellungen für die Internetverbindung zu verwenden.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderProxy.png)

7. Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
8. Geben Sie unter **Servername** einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderRegKeyServerName.png)

9. Wählen Sie unter **Anfängliche Cloud-Metadatensynchronisierung** aus, ob die Metadaten aller Clouds auf dem VMM-Server mit dem Tresor synchronisiert werden sollen. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Falls Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktivieren und die Clouds

10. einzeln in den Cloudeinstellungen in der VMM-Konsole synchronisieren.

11. Die Option **Datenverschlüsselung** ist in diesem Szenario nicht relevant. 

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderSyncEncrypt.png)

12. Klicken Sie auf <b>Registrieren</b>, um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten vom VMM-Server von Azure Site Recovery abgerufen. Der Server wird im Tresor auf der Seite <b>Server</b> auf der Registerkarte **Ressourcen** angezeigt. Nach der Installation des Anbieters ändern Sie die Anbietereinstellungen in der VMM-Konsole.

 
<h3><a id="storage"></a>Schritt 4: Zuordnen von Speicherarrays und -pools</h3>

Sie müssen am primären und sekundären Standort eine Zuordnung zwischen Speicherarrays und -pools erstellen.

Prüfen Sie, bevor Sie beginnen, ob im Tresor Clouds vorhanden sind. Clouds werden erkannt, indem Sie bei der Installation des Anbieters in der VMM-Konsole in den Cloud-Einstellungen auf der Registerkarte **Allgemein** die Option zum Synchronisieren aller Clouds oder einer bestimmten Cloud auswählen. Ordnen Sie dann Speicherarrays und -pools wie folgt zu:

1. Klicken Sie auf **Ressourcen** > **Serverspeicher** > **Quell- und Zielarrays zuordnen**.
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMap.png)
2. Wählen Sie die Speicherarrays für den primären Standort aus, und ordnen Sie sie Speicherarrays für den sekundären Standort zu.
3.  Ordnen Sie innerhalb der Arrays Quell- und Zielspeicherpools zu. Wählen Sie hierzu in **Speicherpools** einen zuzuordnenden Quell- und Zielspeicherpool aus.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMapPool.png)




<h3><a id="clouds"></a>Schritt 5: Konfigurieren der Cloudschutzeinstellungen</h3>

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Installation des Anbieters die Option **Cloud-Daten mit Tresor synchronisieren** aktiviert. Deshalb werden alle Clouds auf dem VMM-Server auf der Registerkarte <b>Geschützte Elemente</b> im Tresor angezeigt.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SRSAN_CloudsList.png)

1. Klicken Sie auf der Seite Schnellstart auf **Schutz für VMM-Clouds einrichten**.
2. Klicken Sie auf der Registerkarte **Geschützte Elemente** auf die Cloud, die Sie konfigurieren möchten, und wechseln Sie zur Registerkarte **Konfiguration**. Beachten Sie Folgendes:
3. Wählen Sie unter <b>Ziel</b> die Option <b>VMM</b> aus.
4. Wählen Sie unter <b>Zielspeicherort</b> den lokalen VMM-Server aus, der die Cloud verwaltet, die Sie für die Wiederherstellung verwenden möchten.
5. Wählen Sie unter <b>Zielcloud</b> die Zielcloud aus, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten. Beachten Sie Folgendes:
	- Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
	- Eine Cloud kann nur zu einem einzelnen Cloudpaar gehören - entweder als primäre oder Zielcloud.
6. Azure Site Recovery prüft, ob Clouds Zugriff auf für die SAN-Replikation geeigneten Speicher haben und ob für die Speicherarrays ein Peering erfolgt ist. Teilnehmende Arraypeers werden angezeigt.
7. Wählen Sie nach erfolgreicher Überprüfung in **Replikationstyp** den Eintrag **SAN**.

<p>Nachdem Sie die Einstellungen gespeichert haben, wird ein Auftrag erstellt, der auf der Registerkarte <b>Aufträge</b> überwacht werden kann. Cloud-Einstellungen können auf der Registerkarte <b>Konfigurieren</b> bearbeitet werden. Wenn Sie den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und die Cloud anschließend neu konfigurieren.</p>


<h3><a id="replication"></a>Schritt 6: Aktivieren der Replikation für Replikationsgruppen</h3>

Bevor Sie Schutz für virtuelle Computer aktivieren können, müssen Sie die Replikation für Speicherreplikationsgruppen aktivieren. 

1. Öffnen Sie im Azure Site Recovery-Portal auf der Eigenschaftenseite der primären Cloud die Registerkarte **Virtuelle Computer**. Klicken Sie auf **Replikationsgruppe hinzufügen**.
2. Wählen Sie eine oder mehrere VMM-Replikationsgruppen aus, die der Cloud zugeordnet sind, überprüfen Sie Quell- und Zielarrays, und geben Sie die Replikationsfrequenz an.

<P>Nach diesem Vorgang stellt Azure Site Recovery gemeinsam mit VMM und den SMI-S-Anbietern die Speicher-LUNs für den Zielstandort bereit und aktiviert die Speicherreplikation. Falls die Replikationsgruppe bereits repliziert wurde, verwendet Azure Site Recovery die vorhandene Replikationsbeziehung erneut und aktualisiert die Informationen in Azure Site Recovery.</P>

<h2><a id="enablevirtual"></a>Schritt 7: Aktivieren des Schutzes für virtuelle Computer</h2>
<p>Nach der Replikation einer Speichergruppe aktivieren Sie den Schutz für virtuelle Computer in der VMM-Konsole mittels einer der folgenden Methoden:</p>



- **Neuer virtueller Computer** - Wenn Sie in der VMM-Konsole einen neuen virtuellen Computer erstellen, aktivieren Sie den Azure Site Recovery-Schutz und ordnen den virtuellen Computer der Replikationsgruppe zu.
Bei Wahl dieser Option arbeitet VMM mit der intelligenten Platzierung, um den Speicher virtueller Computer optimal in den LUNs der Replikationsgruppe zu platzieren. Azure Site Recovery orchestriert die Erstellung eines virtuellen Schattencomputers am sekundären Standort und weist Kapazität so zu, dass replizierte virtuelle Computer nach einem Failover gestartet werden können.
- **Vorhandener virtueller Computer** - Wenn bereits ein virtueller Computer in VMM bereitgestellt ist, können Sie den Azure Site Recovery-Schutz aktivieren und eine Speichermigration zu einer Replikationsgruppe durchführen. Im Anschluss erkennen VMM und Azure Site Recovery den neuen virtuellen Computer und beginnen mit dessen Verwaltung in Azure Site Recovery zu Schutzzwecken. Ein virtueller Schattencomputer wird am sekundären Standort erstellt. Diesem wird Kapazität so zugewiesen, dass der replizierte virtuelle Computer nach einem Failover gestartet werden kann.


	![Enable protection](./media/hyper-v-recovery-manager-configure-vault/SRSAN_EnableProtection.png)
	

<P>Nachdem virtuelle Computer zu Schutzzwecken aktiviert wurden, werden sie in der Azure Site Recovery-Konsole angezeigt. Sie können die Eigenschaften virtueller Computer anzeigen, den Status nachverfolgen und für Replikationsgruppen, die mehrere virtuelle Computer enthalten, ein Failover ausführen. Beachten Sie, dass bei der SAN-Replikation für alle einer Replikationsgruppe zugeordneten virtuellen Computer das Failover gemeinsam erfolgen muss. Der Grund ist, dass das Failover zuerst auf der Speicherebene erfolgt. Wichtig ist es, dass Sie Ihre Replikationsgruppen ordnungsgemäß gruppieren und nur einander zugeordnete virtuelle Computer gemeinsam platzieren.</P>

Sie können den Fortschritt der Schutzaktivierung einschließlich der ersten Replikation auf der Registerkarte **Aufträge** verfolgen. Nach der Ausführung des Auftrags zum Abschließen des Schutzes ist der virtuelle Computer für ein Failover bereit. 
	![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SRSAN_JobPropertiesTab.png)

<h3><a id="recoveryplans"></a>Schritt 8: Testen der Bereitstellung</h3>



Testen Sie Ihre Bereitstellung, um sicherzustellen, dass das Failover von virtuellen Computern und Daten wie erwartet erfolgt. Hierzu erstellen Sie einen Wiederherstellungsplan durch Auswählen von Replikationsgruppen. Führen Sie dann ein Testfailover gemäß dem Plan aus.

1. Klicken Sie auf der Registerkarte **Wiederherstellungspläne** auf **Erstellen**.
2. Geben Sie einen Namen für den Wiederherstellungsplan sowie die VMM-Quell- und Zielserver an. Der Quellserver muss virtuelle Computer aufweisen, die für Failover und Wiederherstellung aktiviert sind. Wählen Sie **SAN** aus, um nur Clouds anzuzeigen, die für die SAN-Replikation konfiguriert sind.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlan.png)

4. Wählen Sie in **Virtuellen Computer auswählen** Replikationsgruppen aus. Alle der Replikationsgruppe zugeordneten virtuellen Computer werden ausgewählt und dem Wiederherstellungsplan hinzugefügt. Diese virtuellen Computer werden der Standardgruppe im Wiederherstellungsplan, Gruppe 1, hinzugefügt. Sie können bei Bedarf weitere Gruppen hinzufügen. Beachten Sie, dass nach der Replikation virtuelle Computer gemäß der Reihenfolge der Wiederherstellungsplangruppen gestartet werden.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlanVM.png)	
5. Nachdem ein Wiederherstellungsplan erstellt wurde, wird er auf der Registerkarte **Wiederherstellungspläne** angezeigt. 
6. Wählen Sie den gewünschten Wiederherstellungsplan auf der Registerkarte **Wiederherstellungspläne** aus, und klicken Sie auf **Testfailover**.
7. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** aus. Bei Aktivierung dieser Option werden nach einem Failover die virtuellen Computer im Replikat mit keinem Netzwerk verbunden. Es wird getestet, ob das Failover für den virtuellen Computer wie erwartet erfolgt, ohne dass Ihre Netzwerkumgebung für die Replikation getestet wird. Wenn Sie ein umfassenderes Testfailover ausführen möchten, lesen Sie <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testen einer Bereitstellung zwischen lokalen Standorten</a> in MSDN.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


8. Der virtuelle Computer für den Test wird auf dem Host erstellt, auf dem auch der replizierte virtuelle Computer vorhanden ist. Er wird nicht der Cloud hinzugefügt, in der sich der replizierte virtuelle Computer befindet.
9. Nach der Replikation hat der replizierte virtuelle Computer eine IP-Adresse, die nicht der IP-Adresse des primären virtuellen Computers entspricht. Wenn Sie Adressen über DHCP ausstellen, wird DNS automatisch aktualisiert. Wenn Sie nicht DHCP verwenden und sicherstellen möchten, dass die Adressen identisch sind, müssen Sie verschiedene Skripts ausführen.
10. Führen Sie diese Beispielskript aus, um die IP-Adresse abzurufen.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. Führen Sie dieses Beispielskript aus, um DNS zu aktualisieren, wobei Sie die IP-Adresse angeben, die Sie mithilfe des vorherigen Beispielskripts abgerufen haben.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**

<h2><a id="runtest" name="runtest" href="#runtest"></a>Aktivität überwachen</h2>
<p>Sie können die Registerkarte <b>Aufträge</b> und das <b>Dashboard</b> verwenden, um die Hauptaufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Hierzu zählen das Konfigurieren des Schutzes für eine Cloud, das Aktivieren und Deaktivieren des Schutzes für einen virtuellen Computer, das Durchführen eines Failovers (geplant, nicht geplant oder Test) und das Durchführen eines Commitvorgangs für ein nicht geplantes Failover.</p>

<p>Auf der Registerkarte <b>Aufträge</b> können Sie Aufträge anzeigen, Details zu den einzelnen Aufträgen und Fehlern aufrufen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und nicht erfolgreiche Aufträge neu starten.</p>

<p>Im <b>Dashboard</b> können Sie die aktuellen Versionen der Installationsdateien für Anbieter und Agent herunterladen, Konfigurationsinformationen für den Tresor abrufen, die Anzahl der virtuellen Computer anzeigen, deren Schutz vom Tresor verwaltet wird, die letzten Aufträge anzeigen, das Tresorzertifikat verwalten und virtuelle Computer erneut synchronisieren.</p>

<p>Weitere Informationen zur Interaktion mit Aufträgen und dem Dashboard finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Betriebs- und Überwachungshandbuch</a>.</p>
	
<h2><a id="next" name="next" href="#next"></a>Nächste Schritte</h2>
<UL>
<LI>Informationen zur Planung und Bereitstellung von Azure Site Recovery in einer realen Produktionsumgebung finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planungshandbuch für Azure Site Recovery</a> und im <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Bereitstellungshandbuch für Azure Site Recovery</a>.</LI>
<LI>Wenn Sie Fragen haben, besuchen Sie das <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI> 
</UL>

<!--HONumber=49-->