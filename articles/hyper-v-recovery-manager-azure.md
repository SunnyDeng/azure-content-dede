<properties 
	pageTitle="Lernprogramm: Einrichten von Schutz zwischen einem lokalen VMM-Standort und Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Hyper-V-Computern in lokalen VMM-Clouds nach Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Lernprogramm: Einrichten von Schutz zwischen einem lokalen VMM-Standort und Azure

<h2><a id="overview" name="overview" href="#overview"></a>Übersicht</h2>
<p>Azure Site Recovery unterstützt Ihre Strategie zur Gewährleistung der Kontinuität des Geschäftsbetriebs, in dem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden.<p>

<P>In diesem Lernprogramm wird beschrieben, wie Sie Azure Site Recovery bereitstellen, um Schutz zwischen einem lokalen VMM-Standort und Azure mithilfe der Hyper-V-Replikation zu gewährleisten.  In diesem Lernprogramm werden nach Möglichkeit der schnellste Bereitstellungspfad und Standardeinstellungen verwendet.</P>

<UL>
<LI>Informationen zu einer vollständigen Bereitstellung finden Sie in den Handbüchern <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planung</a> und <a href="http://go.microsoft.com/fwlink/?LinkId=402679">Bereitstellung</a>.</LI>
<LI>In der <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Übersicht über Azure Site Recovery</a> finden Sie Informationen zu weiteren Bereitstellungsszenarien für Azure Site Recovery.</LI>
<LI>Wenn Sie während dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen</a> (in englischer Sprache), oder stellen Sie Ihre Fragen im <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI>
</UL>


<h2><a id="prerequisites" name="prerequisites" href="#prerequisites"></a>Voraussetzungen</h2>
<div class="dev-callout"> 
<P>Vergewissern Sie sich, dass Sie alle Voraussetzungen erfüllen, ehe Sie das Lernprogramm starten.</P>

<UL>
<LI><b>Azure-Konto</b> - Sie benötigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, finden Sie weitere Informationen unter <a href="http://aka.ms/try-azure">1 Monat kostenlose Testversion</a>. Preisinformationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Preise</a>.</LI>

<LI><b>Azure-Speicherkonto</b> - Sie benötigen ein Azure-Speicherkonto, um an Azure replizierte Daten zu speichern. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein. Weitere Informationen zum Einrichten von Azure-Speicher finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Einführung in Microsoft Azure Storage</a>.</LI><LI><b>VMM-Server</b> - Ein VMM-Server, der in System Center 2012 R2 ausgeführt wird.</LI>
<LI><b>VMM-Clouds</b> - Mindestens eine Cloud auf dem VMM-Server. Die Cloud muss Folgendes enthalten:
	<UL>
	<LI>Eine oder mehrere VMM-Hostgruppen</LI>
	<LI>Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.</LI>
	<li>Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud. Es muss sich um virtuelle Computer der 1. Generation handeln.</li>
		</UL></LI>	
<LI><b>Virtuelle Computer</b> - Sie benötigen virtuelle Computer, die die Azure-Anforderungen erfüllen. Siehe <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Voraussetzungen und Support</a> im Planungshandbuch.</LI>
<LI>Eine vollständige Liste der Supportanforderungen für virtuelle Computer für ein Failover an Azure finden Sie unter  </LI>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Schritte im Lernprogramm</h2>
Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:
<UL>

<LI><a href="#vault">Schritt 1: Erstellen eines Tresors</a>- Erstellen und konfigurieren Sie einen Azure Site Recovery-Tresor.</LI>
<LI><a href="#download">Schritt 2: Installieren der Anbieteranwendung auf dem VMM-Server</a>- Generieren Sie im Tresor einen Registrierungsschlüssel, und laden Sie die Setupdatei für den Anbieter herunter. Sie führen das Setup auf dem VMM-Server aus, um den Anbieter zu installieren und den VMM-Server im Tresor zu registrieren.</LI>
<LI><a href="#storage">Schritt 3: Hinzufügen eines Azure-Speicherkontos</a>- Erstellen Sie ein Speicherkonto, falls Sie noch keines haben. </LI>
<LI><a href="#agent">Schritt 4: Installieren der Agent-Anwendung</a>-Installieren Sie den Microsoft Azure Recovery Services-Agent auf allen Hyper-V-Hostservern in den VMM-Clouds, die Sie schützen möchten.</LI>
<LI><a href="#clouds">Schritt 5: Konfigurieren des Cloudschutzes</a>- Konfigurieren Sie Schutzeinstellungen für VMM-Clouds.</LI>
<LI><a href="#NetworkMapping">Schritt 6: Konfigurieren der Netzwerkzuordnung</a>- Optional können Sie eine Netzwerkzuordnung aktivieren, um VM-Quellnetzwerke virtuellen Azure-Zielnetzwerken zuzuordnen.</LI>
<LI><a href="#virtualmachines">Schritt 7: Aktivieren des Schutzes für virtuelle Computer</a>- Aktivieren Sie Schutz für virtuelle Computer in geschützten VMM-Clouds.</LI>
<LI><a href="#test">Schritt 8: Testen der Bereitstellung</a>- Um Ihre Bereitstellung zu testen, können Sie ein Testfailover für einen einzelnen virtuellen Computer ausführen oder einen Wiederherstellungsplan erstellen und ein Testfailover für den Plan ausführen, um seine Funktionsfähigkeit sicherzustellen.</LI>
</UL>



<a name="vault"></a> <h3>Schritt 1: Erstellen eines Tresors</h3>

1. Melden Sie sich im [Verwaltungsportal](https://manage.windowsazure.com) an.


2. Erweitern Sie <b>Data Services</b>, dann <b>Recovery Services</b>, und klicken Sie auf <b>Site Recovery-Tresor</b>.

3. Klicken Sie auf <b>Neu erstellen</b> und anschließend auf <b>Schnellerfassung</b>.
	

4. Geben Sie unter <b>Name</b> einen Anzeigenamen ein, mit dem der Tresor identifiziert wird.

5. Wählen Sie unter <b>Region</b> die geografische Region für den Tresor aus. Zu den verfügbaren geografischen Regionen zählen Ostasien, Westeuropa, USA (Westen), USA (Osten), Nordeuropa und Südostasien.
6. Klicken Sie auf <b>Tresor erstellen</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als <b>aktiv</b> aufgelistet.</P>





 <a name="download"></a> <h3>Schritt 2: Generieren des Registrierungsschlüssels und Installieren des Azure Site Recovery-Anbieters</h3>
 

1. Klicken Sie auf der Seite <b>Recovery Services</b> auf den Tresor, um die Seite Schnellstart zu öffnen. Die Seite "Schnellstart" kann auch jederzeit über das Symbol geöffnet werden.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen einem lokalen Hyper-V-Standort und Microsoft Azure** aus.
3. Klicken Sie unter **VMM-Server vorbereiten** auf **Registrierungsschlüssel erstellen**. Der Schlüssel ist nach der Erstellung 5 Tage lang gültig. Kopieren Sie die Datei auf den VMM-Server. Sie benötigen die Datei bei der Einrichtung des Anbieters.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. Klicken Sie auf der Seite <b>Schnellstart</b> unter **VMM-Server** vorbereiten auf <b>Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Servern herunterladen</b>, um die neueste Version der Anbieterinstallationsdatei herunterzuladen.

2. Führen Sie diese Datei auf dem VMM-Quellserver aus.


3. Geben Sie unter **Voraussetzungsprüfung** an, dass der VMM-Dienst für die Anbietereinrichtung angehalten werden soll. Der Dienst wird angehalten und nach Abschluss der Einrichtung automatisch neu gestartet.

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieterupdates gemäß Ihrer Microsoft Updaterichtlinie installiert.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Fahren Sie nach der Installation des Anbieters mit der Einrichtung fort, um den Server im Tresor zu registrieren.

5. Geben Sie auf der Seite **Internetverbindung** an, wie sich der Anbieter auf dem VMM-Server mit dem Internet verbinden soll. Wählen Sie <b>Standard-Systemproxyeinstellungen verwenden</b> aus, um die auf dem Server konfigurierten Standardeinstellungen für die Internetverbindung zu verwenden.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
7. Prüfen Sie unter **Tresorname** den Namen des Tresors, in dem der Server registriert wird.
8. Geben Sie unter **Servername** einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren.

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. Wählen Sie unter **Anfängliche Cloud-Metadatensynchronisierung** aus, ob die Metadaten aller Clouds auf dem VMM-Server mit dem Tresor synchronisiert werden sollen. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Falls Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktivieren und die Clouds einzeln in den Cloudeinstellungen in der VMM-Konsole synchronisieren.


9. Geben Sie unter **Datenverschlüsselung** einen Speicherort für ein SSL-Zertifikat an, das automatisch für die Datenverschlüsselung generiert wird. Dieses Zertifikat wird verwendet, wenn Sie Datenverschlüsselung für eine Cloud im Azure Site Recovery-Portal aktivieren. Bewahren Sie dieses Zertifikat sicher auf. Wenn Sie ein Failover in Azure ausführen, wählen Sie es aus, um verschlüsselte Daten zu entschlüsseln. 
Diese Option ist nicht relevant, wenn Sie eine Replikation von einem lokalen Standort an einen anderen ausführen.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Klicken Sie auf <b>Registrieren</b>, um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten vom VMM-Server von Azure Site Recovery abgerufen. Der Server wird im Tresor auf der Seite <b>Server</b> auf der Registerkarte **Ressourcen** angezeigt.

<h3><a id="storage"></a>Schritt 3: Erstellen eines Azure-Speicherkontos</h3>
Falls Sie noch kein Azure-Speicherkonto haben, klicken Sie auf **Azure-Speicherkonto** hinzufügen. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein.

<P>Mit diesem Lernprogramm können Sie eine schnelle Machbarkeitsstudie für Azure Site Recovery in einer Bereitstellung von einem lokalen Standort zu Azure einrichten. Dabei werden der kürzeste Pfad und nach Möglichkeit Standardeinstellungen verwendet. Sie erstellen einen Azure Site Recovery-Tresor, installieren den Azure Site Recovery-Anbieter auf dem VMM-Quellserver, installieren den Azure Recovery Services-Agent auf den Hyper-V-Host-Servern in den VMM-Clouds, konfigurieren Cloud-Schutzeinstellungen, aktivieren den Schutz für virtuelle Computer und testen Ihre Bereitstellung.</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h3><a id="agent"></a>Schritt 4: Installieren des Azure Recovery Services-Agent auf den Hyper-V-Hosts</h3>

Installieren Sie den Azure Recovery Services-Agent auf jedem Hyper-V-Hostserver in den VMM-Clouds, die Sie schützen möchten.

1. Klicken Sie auf der Seite Schnellstart auf <b>Azure Site Recovery Services-Agent für die Installation auf Hosts herunterladen</b>, um die neueste Version der Agent-Installationsdatei herunterzuladen.

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. Führen Sie die Installationsdatei auf jedem Hyper-V-Hostserver aus, der sich in den VMM-Clouds befindet, die Sie schützen möchten.
3. Klicken Sie auf der Seite **Voraussetzungsüberprüfung** auf <b>Weiter</b>. Alle fehlenden Komponenten, die Voraussetzung sind, werden automatisch installiert.

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. Geben Sie auf der Seite **Installationseinstellungen** an, wo Sie den Agent installieren möchten, und wählen Sie den Cachespeicherort aus, in dem die Sicherungsmetadaten installiert werden. Klicken Sie auf <b>Installieren</b>.


<h3><a id="clouds"></a>Schritt 5: Konfigurieren der Cloudschutzeinstellungen</h3>

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Installation des Anbieters die Option **Cloud-Daten mit Tresor synchronisieren** aktiviert. Deshalb werden alle Clouds auf dem VMM-Server auf der Registerkarte <b>Geschützte Elemente</b> im Tresor angezeigt.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. Klicken Sie auf der Seite Schnellstart auf **Schutz für VMM-Clouds einrichten**.
2. Klicken Sie auf der Registerkarte **Geschützte Elemente** auf die Cloud, die Sie konfigurieren möchten, und wechseln Sie zur Registerkarte **Konfiguration**.
3. Wählen Sie unter <b>Ziel</b> die Option <b>Microsoft Azure</b> aus.
4. Wählen Sie unter <b>Speicherkonto</b> den Azure-Speicher aus, den Sie zum Speichern der virtuellen Azure-Computer verwenden möchten.
5. Legen Sie <b>Gespeicherte Daten verschlüsseln</b> auf <b>Aus</b> fest. Diese Einstellung gibt an, dass die Daten beim Replizieren zwischen dem lokalen Standort und Azure verschlüsselt werden sollen.
6. Lassen Sie unter <b>Kopierhäufigkeit</b> die Standardeinstellung unverändert. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen.
7. Lassen Sie unter <b>Wiederherstellungspunkte beibehalten für</b> die Standardeinstellung unverändert. Wenn die Standardeinstellung 0 (Null) aktiviert ist, wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
8. Lassen Sie unter <b>Häufigkeit von anwendungskonsistenten Momentaufnahmen</b> die Standardeinstellung unverändert. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind.  Wenn Sie einen Wert festlegen möchten, stellen Sie sicher, dass er geringer als die Anzahl der konfigurierten zusätzlichen Wiederherstellungspunkte ist.
9. Geben Sie unter <b>Startzeit für Replikation</b> an, wann die erste Datenreplikation an Azure starten sollte. Die Zeitzone auf dem Hyper-V-Hostserver wird verwendet. Es wird empfohlen, die erste Replikation außerhalb der Spitzenzeiten zu planen. 

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

Nachdem Sie die Einstellungen gespeichert haben, wird ein Auftrag erstellt, der auf der Registerkarte <b>Aufträge</b> überwacht werden kann. Alle Hyper-V-Hostserver in der VMM-Quellcloud werden für die Replikation konfiguriert.

Nach dem Speichern können die Cloud-Einstellungen auf der Registerkarte <b>Konfigurieren</b> bearbeitet werden. Um den Zielspeicherort bzw. Zielspeicher zu ändern, müssen Sie die Cloudkonfiguration entfernen und dann die Cloud neu konfigurieren. Beachten Sie: Wenn Sie das Speicherkonto ändern, wird die Änderung nur für virtuelle Computer angewandt, für die der Schutz nach Änderung des Speicherkontos aktiviert wird. Vorhandene virtuelle Computer werden nicht an das neue Speicherkonto migriert.</p>

<h3><a id="networkmapping"></a>Schritt 6: Konfigurieren der Netzwerkzuordnung</h3>

<p>Dieses Lernprogramm beschreibt den einfachsten Weg zur Einrichtung von Azure Site Recovery in einer Testumgebung. Falls Sie die Netzwerkzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, finden Sie weitere Informationen unter <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Vorbereiten der Netzwerkzuordnung</a> im Planungshandbuch. Führen Sie die Schritte zum <a href="http://go.microsoft.com/fwlink/?LinkId=402533">Konfigurieren der Netzwerkzuordnung</a> im Bereitstellungsleitfaden aus, um die Zuordnung zu konfigurieren.</p>




<h3><a id="virtualmachines"></a>Schritt 7: Aktivieren des Schutzes für virtuelle Computer</h3>

Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren. Beachten Sie Folgendes:

- Die virtuellen Computer müssen die Azure-Anforderungen erfüllen. Unter <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Voraussetzungen und Support</a> im Planungshandbuch können Sie diese prüfen.
- Um den Schutz zu aktivieren, müssen die Eigenschaften "Betriebssystem" und "Betriebssystem-Datenträger" für den virtuellen Computer gesetzt sein. Sie können diese Eigenschaften setzen, wenn Sie den virtuellen Computer in VMM mithilfe einer Vorlage für virtuelle Maschinen erstellen. Außerdem können Sie diese Eigenschaften für vorhandene virtuelle Computer auf den Registerkarten **Allgemein** und **Hardwarekonfiguration** in den Eigenschaften der virtuellen Computer festlegen. Falls diese Eigenschaften in VMM nicht angezeigt werden, sollten Sie sie dennoch im Azure Site Recovery-Portal konfigurieren können.

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. Klicken Sie zum Aktivieren von Schutz auf der Registerkarte <b>Virtuelle Computer</b> in der Cloud, in der sich der virtuelle Computer befindet, auf <b>Schutz aktivieren</b>, und wählen Sie dann <b>Virtuelle Computer hinzufügen</b> aus.
2. Wählen Sie aus der Liste der virtuellen Computer in der Cloud denjenigen aus, den Sie schützen möchten. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. Überprüfen Sie den virtuellen Computer und nehmen Sie ggf. Änderungen vor.

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

Sie können den Fortschritt der Schutzaktivierung einschließlich der ersten Replikation auf der Registerkarte **Aufträge** verfolgen. Nach der Ausführung des Auftrags zum Abschließen des Schutzes ist der virtuelle Computer für ein Failover bereit. Nach dem Aktivieren des Schutzes und der Replikation der virtuellen Computer können Sie die virtuellen Computer in Azure sehen.


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h3><a id="test"></a>Schritt 8: Testen der Bereitstellung</h3>
Um Ihre Bereitstellung zu testen, können Sie ein Testfailover für einen einzelnen virtuellen Computer durchführen oder einen Wiederherstellungsplan erstellen, der mehrere virtuelle Computer umfasst, und ein Testfailover für diesen Plan durchführen.  Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk. Beachten Sie Folgendes:
<UL>
<li>Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Computer in Azure über Remote Desktop herstellen möchten, aktivieren Sie die Remote Desktop-Verbindung auf dem virtuellen Computer, bevor Sie das Test-Failover ausführen.</li>
<li>Nach dem Failover verwenden Sie eine öffentliche IP-Adresse, um eine Verbindung zum virtuellen Computer in Azure über Remote Desktop herzustellen. Wenn Sie dies möchten, stellen Sie sicher, dass keine Domänenrichtlinien vorhanden sind, die das Verbinden zu einem virtuellen Computer über eine öffentliche Adresse verhindern.</li>
</UL>

1. Fügen Sie auf der Registerkarte **Wiederherstellungspläne** einen neuen Plan hinzu. Geben Sie einen Namen, **VMM** in **Quelltyp** und den VMM-Quellserver in **Quelle** an. Das Ziel ist Azure.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** aus. Beachten Sie, dass ein Testfailover mit dieser Einstellung prüft, ob der virtuelle Computer ordnungsgemäß nach Azure repliziert wurde, ohne die Konfiguration des Replikationsnetzwerks zu prüfen. Wenn Sie einen Test mit einem angegebenen Azure-Netzwerk ausführen möchten, lesen Sie <a href="http://go.microsoft.com/fwlink/?LinkId=522292">Testen einer Bereitstellung von einem lokalen Standort zu Azure</a>.

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. Wenn das Failover die Phase **Test abschließen** erreicht, klicken Sie auf **Test abschließen**, um das Testfailover abzuschließen. Auf der Registerkarte **Auftrag** können Sie den Failoverfortschritt und -status detailliert nachverfolgen und etwaige erforderliche Aufgaben ausführen.
4. Gehen Sie nach dem Failover folgendermaßen vor:
	- Prüfen Sie, ob die virtuellen Computer erfolgreich starten.
	- Klicken Sie auf **Notizen**, um alle Beobachtungen im Zusammenhang mit dem Testfailover aufzuzeichnen und zu speichern.
	- Klicken Sie auf **Das Testfailover ist abgeschlossen**. Bereinigen Sie die Testumgebung, um den virtuellen Testcomputer automatisch auszuschalten und das Azure-Testnetzwerk zu löschen.
5. Nach dem Failover können Sie das Testreplikat des virtuellen Computers im Azure-Portal sehen. Wenn Sie den Zugriff auf virtuelle Computer aus Ihrem lokalen Netzwerk eingerichtet haben, können Sie eine Remotedesktopverbindung mit dem virtuellen Computer herstellen.



<h2><a id="runtest" name="runtest" href="#runtest"></a>Aktivität überwachen</h2>
<p>Sie können die Registerkarte <b>Aufträge</b> und das <b>Dashboard</b> verwenden, um die Hauptaufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Hierzu zählen das Konfigurieren des Schutzes für eine Cloud, das Aktivieren und Deaktivieren des Schutzes für einen virtuellen Computer, das Durchführen eines Failovers (geplant, nicht geplant oder Test) und das Durchführen eines Commitvorgangs für ein nicht geplantes Failover.</p>

<p>Auf der Registerkarte <b>Aufträge</b> können Sie Aufträge anzeigen, Details zu den einzelnen Aufträgen und Fehlern aufrufen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und nicht erfolgreiche Aufträge neu starten.</p>

<p>Im <b>Dashboard</b> können Sie die aktuellen Versionen der Installationsdateien für Anbieter und Agent herunterladen, Konfigurationsinformationen für den Tresor abrufen, die Anzahl der virtuellen Computer anzeigen, deren Schutz vom Tresor verwaltet wird, die letzten Aufträge anzeigen, das Tresorzertifikat verwalten und virtuelle Computer erneut synchronisieren.</p>

<p>Weitere Informationen zur Interaktion mit Aufträgen und dem Dashboard finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Betriebs- und Überwachungshandbuch</a>.</p>

<h2><a id="next" name="next" href="#next"></a>Nächste Schritte</h2>
<UL>
<LI>Informationen zur Planung und Bereitstellung von Azure Site Recovery in einer realen Produktionsumgebung finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planungshandbuch für Azure Site Recovery</a> und im <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Bereitstellungshandbuch für Azure Site Recovery</a>.</LI>


<LI>Wenn Sie Fragen haben, besuchen Sie das <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a></LI> 
</UL>

<!--HONumber=49-->