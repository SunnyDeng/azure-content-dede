<properties 
	pageTitle="Lernprogramm: Einrichten von Schutz zwischen lokalen VMM-Standorten" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Hyper-V-Computern zwischen lokalen VMM-Standorten." 
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


# Erste Schritte mit Azure Site Recovery:  Schutz zwischen lokalen VMM-Standorten mithilfe der Hyper-V-Replikation


<div class="dev-callout"> 

<p>Azure Site Recovery unterstützt Ihre Strategie zur Gewährleistung der Kontinuität des Geschäftsbetriebs, in dem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden.<p>

<P>In diesem Lernprogramm wird beschrieben, wie Sie Azure Site Recovery bereitstellen, um Arbeitsauslastungen an einem lokalen VMM-Standort zu schützen, indem Sie die Hyper-V-Replikation an einen anderen lokalen VMM-Standort aktivieren.  In diesem Lernprogramm werden nach Möglichkeit der schnellste Bereitstellungspfad und Standardeinstellungen verwendet.</P>

<UL>
<LI>Die vollständigen Bereitstellungsanweisungen finden Sie in den Handbüchern <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planung</a> und <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Bereitstellung</a>.</LI>
<LI>In der <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Übersicht zu Azure Site Recovery</a> finden Sie Informationen zu weiteren Bereitstellungsszenarien für Azure Site Recovery.</LI>
<LI>Wenn Sie während dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen</a>, oder stellen Sie Ihre Fragen im <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI>
</UL>

</div>


<h2><a id="before"></a>Voraussetzungen</h2> 
<div class="dev-callout"> 
<P>Vergewissern Sie sich, dass Sie alle Voraussetzungen erfüllen, ehe Sie das Lernprogramm starten.</P>

<UL>
<LI><b>Azure-Konto</b> - Sie benötigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, lesen Sie <a href="http://aka.ms/try-azure">1 Monat kostenlose Testversion</a>. Preisinformationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Preisdetails</a>.</LI>
<LI><b>VMM-Server</b> - Sie benötigen mindestens einen VMM-Server mit System Center 2012 SP1 oder System Center 2012 R2.</LI>
<LI><b>VMM-Clouds</b> - Sie benötigen mindestens eine Cloud auf dem VMM-Quellserver, den Sie schützen möchten, und eine auf dem VMM-Zielserver. Wenn Sie einen VMM-Server ausführen, müssen zwei Clouds konfiguriert sein. Die primäre Cloud, die Sie schützen möchten, muss Folgendes enthalten:<UL>
	<LI>Eine oder mehrere VMM-Hostgruppen</LI>
	<LI>Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.</LI>
	<li>Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud.</li>
		</UL></LI>
<LI>**Netzwerke** - Sie können optional eine Netzwerkzuordnung konfigurieren, um sicherzustellen, dass replizierte virtuelle Computer nach einem Failover optimal auf Hyper-V-Hostservern platziert werden und dass sie sich mit den entsprechenden VM-Netzwerken verbinden können. Wenn eine Netzwerkzuordnung aktiviert ist, wird ein virtueller Computer am primären Standort mit einem Netzwerk verbunden, und sein Replikat am Zielstandort wird mit dem ihm zugeordneten Netzwerk verbunden. Wenn Sie keine Netzwerkzuordnung konfigurieren, werden virtuelle Computer nach einem Failover nicht mit VM-Netzwerken verbunden. In diesem Lernprogramm werden für die exemplarische Vorgehensweise die einfachsten Einstellungen ohne Netzwerkzuordnung beschrieben. Sie erfahren mehr dazu unter</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Netzwerkzuordnung</a> im Planungshandbuch.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522293">Aktivieren der Netzwerkzuordnung</a> im Bereitstellungshandbuch.</LI>
	</UL></LI>

</UL>



<h2><a id="tutorial"></a>Schritte im Lernprogramm</h2> 

Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:
<UL>
<LI><a href="#vault">Schritt 1: Erstellen eines Tresors</a>-Erstellen Sie einen Azure Site Recovery-Tresor.</LI>
<LI><a href="#download">Schritt 2: Erstellen Sie den Anbieter auf allen VMM-Servern</a>- Generieren Sie im Tresor einen Registrierungsschlüssel, und laden Sie die Setupdatei für den Anbieter herunter. Sie führen das Setup auf den VMM-Servern aus, um den Anbieter zu installieren und den VMM-Server im Tresor zu registrieren.</LI>
<LI><a href="#clouds">Schritt 3: Konfigurieren des Cloud-Schutzes</a>-Konfigurieren Sie die Schutzeinstellungen für VMM-Clouds. Diese Schutzeinstellungen gelten für alle virtuellen Computer in der Cloud, die Sie für den Azure Site Recovery-Schutz aktivieren.</LI>
<LI><a href="#storagemapping">Schritt 4: Konfigurieren der Speicherzuordnung</a>- Wenn Sie angeben möchten, wo Replikationsdaten gespeichert werden sollen, können Sie eine Speicherzuordnung konfigurieren. Damit werden Speicherklassifizierungen auf dem VMM-Quellserver Speicherklassifizierungen auf dem VMM-Zielserver zugeordnet.</LI>
<LI><a href="#enablevirtual">Schritt 5: Aktivieren des Schutzes für virtuelle Computer</a>-Aktivieren Sie den Schutz für die virtuellen Computer in den geschützten VMM-Clouds</LI>
<LI><a href="#recovery plans">Schritt 6: Konfigurieren und Ausführen von Wiederherstellungsplänen</a>-Erstellen Sie einen Wiederherstellungsplan und führen Sie ein Test-Failover für den Plan aus, um seine Funktionsfähigkeit sicherzustellen.</LI>

</UL>




<a name="vault"></a> <h2>Schritt 1: Erstellen eines Tresors</h2>

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.


2. Erweitern Sie <b>Data Services</b>, dann <b>Recovery Services</b>, und klicken Sie auf <b>Site Recovery-Tresor</b>.


3. Klicken Sie auf <b>Neu erstellen</b> und anschließend auf <b>Schnellerfassung</b>.
	
4. Geben Sie unter <b>Name</b> einen Anzeigenamen ein, mit dem der Tresor identifiziert wird.

5. Wählen Sie unter <b>Region</b> die geografische Region für den Tresor aus. Welche Regionen unterstützt werden, erfahren Sie unter "Geografische Verfügbarkeit" unter <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery-Preisdetails</a>

6. Klicken Sie auf <b>Tresor erstellen</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als <b>aktiv</b> aufgelistet.</P>

<a name="upload"></a> <h2>Schritt 2: Konfigurieren des Tresors</h2>


1. Klicken Sie auf der Seite <b>Recovery Services</b> auf den Tresor, um die Seite Schnellstart zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste die Option **Zwischen zwei lokalen Hyper-V-Standorten** aus.
3. Klicken Sie unter **VMM-Server vorbereiten** auf **Registrierungsschlüssel erstellen**. Der Schlüssel ist nach der Erstellung 5 Tage lang gültig. Kopieren Sie die Datei auf den VMM-Server. Sie benötigen die Datei bei der Einrichtung des Anbieters.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png)
	



<a name="download"></a> <h2>Schritt 3: Installieren des Azure Site Recovery-Anbieters</h2>
	

1. Klicken Sie auf der Seite <b>Schnellstart</b> unter **VMM-Server** vorbereiten auf <b>Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Servern herunterladen</b>, um die neueste Version der Anbieterinstallationsdatei herunterzuladen.

2. Führen Sie diese Datei auf den VMM-Quell- und Zielservern aus.

3. Geben Sie unter **Voraussetzungsprüfung** an, dass der VMM-Dienst für die Anbietereinrichtung angehalten werden soll. Der Dienst wird angehalten und wird nach Abschluss der Einrichtung automatisch neu gestartet. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieter-Updates gemäß Ihrer Microsoft Update-Richtlinie installiert.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Fahren Sie nach der Installation des Anbieters mit der Einrichtung fort, um den Server im Tresor zu registrieren.

5. Geben Sie auf der Seite Internetverbindung an, wie der Anbieter auf dem VMM-Server die Verbindung mit dem Internet herstellen soll. Wählen Sie <b>Standard-Systemproxyeinstellungen verwenden</b> aus, um die auf dem Server konfigurierten Standardeinstellungen für die Internetverbindung zu verwenden.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
7. Prüfen Sie unter **Tresorname** den Namen des Tresors, in dem der Server registriert wird.
8. Geben Sie unter **Servername** einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)


9. Wählen Sie unter **Anfängliche Cloud-Metadatensynchronisierung** aus, ob die Metadaten aller Clouds auf dem VMM-Server mit dem Tresor synchronisiert werden sollen. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Falls Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktivieren und die Clouds einzeln in den Cloud-Einstellungen in der VMM-Konsole synchronisieren.


7. Unter **Datenverschlüsselung** generieren Sie ein Zertifikat, das zum Verschlüsseln von Daten dient, die in Azure geschützt werden. 
Diese Option ist für das in diesem Lernprogramm beschriebene Szenario nicht relevant.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Klicken Sie auf <b>Registrieren</b>, um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten vom VMM-Server von Azure Site Recovery abgerufen. Der Server wird im Tresor auf der Seite <b>Server</b> auf der Registerkarte **Ressourcen** angezeigt.




<h2><a id="clouds"></a>Schritt 4: Konfigurieren der Cloudschutzeinstellungen</h2>

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Installation des Anbieters die Option **Cloud-Daten mit Tresor synchronisieren** aktiviert. Deshalb werden alle Clouds auf dem VMM-Server auf der Registerkarte <b>Geschützte Elemente</b> im Tresor angezeigt.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)

1. Klicken Sie auf der Seite Schnellstart auf **Schutz für VMM-Clouds einrichten**.
2. Klicken Sie auf der Registerkarte **Geschützte Elemente** auf die Cloud, die Sie konfigurieren möchten, und wechseln Sie zur Registerkarte **Konfiguration**. Beachten Sie Folgendes:
3. Wählen Sie unter <b>Ziel</b> die Option <b>VMM</b> aus.
4. Wählen Sie unter <b>Zielspeicherort</b> den lokalen VMM-Server aus, der die Cloud verwaltet, die Sie für die Wiederherstellung verwenden möchten.
4. Wählen Sie unter <b>Zielcloud</b> die Zielcloud aus, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten. Beachten Sie Folgendes:
	- Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
	- Eine Cloud kann nur zu einem einzigen Cloudpaar gehören -- entweder als primäre oder als Zielcloud.

6. Lassen Sie unter <b>Kopierhäufigkeit</b> die Standardeinstellung unverändert. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen. Dies ist nur relevant, wenn der Hyper-V-Host Windows Server 2012 R2 ausführt. Für andere Server wird die Standardeinstellung "5 Minuten" verwendet.
7. Lassen Sie unter <b>Zusätzliche Wiederherstellungspunkte für</b> die Standardeinstellung unverändert. Dieser Wert gibt an, ob zusätzliche Wiederherstellungspunkte erstellt werden sollen. Mit der Standardeinstellung 0 (Null) wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
8. Lassen Sie unter <b>Häufigkeit von anwendungskonsistenten Momentaufnahmen</b> die Standardeinstellung unverändert. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind.  Wenn Sie diesen Wert für die Lernprrogramm-Anleitung festlegen möchten, stellen Sie sicher, dass er auf eine kleinere Zahl gesetzt ist als die zusätzlichen Wiederherstellungspunkte, die Sie konfigurieren.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png)
9. Geben Sie unter <b>Datenübertragungskomprimierung</b> an, ob replizierte Daten, die übertragen werden, komprimiert werden sollen. 
10. Geben Sie unter <b>Authentifizierung</b> an, wie der Datenverkehr zwischen dem primären und dem Hyper-V-Hostserver für die Wiederherstellung authentifiziert wird. Wenn Sie nicht über eine funktionierende Kerberos-Umgebung verfügen, sollten Sie für diese Anleitung HTTPS auswählen. Azure Site Recovery konfiguriert die Zertifikate für die HTTPS-Authentifizierung automatisch. Es ist keine manuelle Konfiguration notwendig. Bitte beachten Sie, dass diese Einstellung nur für Hyper-V-Hostserver relevant ist, die unter Windows Server 2012 R2 laufen.
11. Lassen Sie unter <b>Ports</b> die Standardeinstellung unverändert. Dieser Wert gibt die Nummer des Ports an, auf dem die Quell- und der Ziel-Hyper-V-Hostcomputer auf Replikationsdatenverkehr lauschen.
12. Geben Sie unter <b>Replikationsmethode</b> an, wie die anfängliche Replikation der Daten vom Quell- zum Zielspeicherort erfolgt, bevor die reguläre Replikation beginnt. 
	- <b>Über Netzwerk</b> - Das Kopieren von Daten über das Netzwerk kann zeitaufwendig und ressourcenintensiv sein. Es wird empfohlen, diese Option zu verwenden, wenn die Cloud virtuelle Computer mit relativ kleinen virtuellen Festplatten enthält und der primäre VMM-Server über eine große Bandbreite mit dem sekundären VMM-Server verbunden ist. Sie können angeben, dass der Kopiervorgang sofort gestartet werden soll, oder eine Uhrzeit auswählen. Wenn Sie Netzwerkreplikation verwenden, empfehlen wir, deren Ausführung außerhalb der Spitzenzeiten einzuplanen.
	- <b>Offline</b> - Mit dieser Methode wird die erste Replikation unter Verwendung externer Medien durchgeführt. Dies ist sinnvoll, wenn Sie einen Abfall der Netzwerkleistung vermeiden möchten oder wenn Sie mit geografisch fernen Orten arbeiten. Wenn Sie diese Methode anwenden möchten, müssen Sie den Exportspeicherort für die Quellcloud und den Importspeicherort angeben. Wenn sie den Schutz für einen virtuellen Computer aktivieren, wird die virtuelle Festplatte an den angegebenen Exportspeicherort kopiert. Sie senden sie an den Zielstandort und kopieren sie an den Importspeicherort. Das System kopiert die importierten Informationen auf die virtuellen Replikatcomputer. Eine vollständige Liste der Voraussetzungen für die Offline-Replikation finden sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds</a> im Bereitstellungshandbuch.
13. Wählen Sie **Virtuellen Replikatcomputer löschen** aus, um anzugeben, dass der virtuelle Replikatcomputer gelöscht werden soll, wenn er nicht mehr geschützt wird, weil Sie die Option **Schutz für virtuellen Computer löschen** auf der Registerkarte Virtuelle Computer der Cloud-Eigenschaften ausgewählt haben. Wenn diese Einstellung aktiviert ist, und Sie deaktivieren den Schutz, wird der virtuelle Computer aus Azure Site Recovery entfernt, die Site Recovery-Einstellungen für den virtuellen Computer werden aus der VMM-Konsole entfernt, und das Replikat wird gelöscht.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png)

<p>Nachdem Sie die Einstellungen gespeichert haben, wird ein Auftrag erstellt, der auf der Registerkarte <b>Aufträge</b> überwacht werden kann. Alle Hyper-V-Hostserver in der VMM-Quell-Cloud werden für die Replikation konfiguriert. Cloud-Einstellungen können auf der Registerkarte <b>Konfigurieren</b> bearbeitet werden. Wenn Sie den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und die Cloud anschließend neu konfigurieren.</p>

<h2><a id="storagemapping"></a>Schritt 5: Konfigurieren der Speicherzuordnung</h2>

<p>Dieses Lernprogramm beschreibt den einfachsten Weg zur Einrichtung von Azure Site Recovery in einer Testumgebung. Wenn Sie die Speicherzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, führen Sie die im Bereitstellungshandbuch unter <a href="http://go.microsoft.com/fwlink/?LinkId=402535">Konfigurieren der Speicherzuordnung</a> beschriebenen Schritte aus.</p>


<h2><a id="enablevirtual"></a>Schritt 6: Schutz für virtuellen Computer aktivieren</h2>
<p>Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren.</p>
<OL>
<li>Klicken Sie auf der Registerkarte <b>Virtuelle Computer</b> in der Cloud, in der sich der virtuelle Computer befindet, auf <b>Schutz aktivieren</b>, und wählen Sie dann <b>Virtuelle Computer hinzufügen</b> aus. </li>
<li>Wählen Sie aus der Liste der virtuellen Computer in der Cloud denjenigen aus, den Sie schützen möchten.</li> 
</OL>

![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png)


<P>Sie können den Fortschritt der Schutzaktivierung einschließlich der ersten Replikation auf der Registerkarte **Aufträge** verfolgen. Nachdem der Auftrag zum Abschließen des Schutzes ausgeführt wurde, ist der virtuelle Computer bereit für den Failover. Nachdem der Schutz aktiviert und die virtuellen Computer repliziert wurden, sind diese in Azure sichtbar.</p>



![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="recoveryplans"></a>Schritt 7: Testen der Bereitstellung</h2>

Testen Sie Ihre Bereitstellung, um sicherzustellen, dass das Failover von virtuellen Computern und Daten wie erwartet erfolgt. Hierzu erstellen Sie einen Wiederherstellungsplan durch Auswählen von Replikationsgruppen. Führen Sie dann ein Testfailover gemäß dem Plan aus.

1. Klicken Sie auf der Registerkarte **Wiederherstellungspläne** auf **Erstellen**.
2. Geben Sie einen Namen für den Wiederherstellungsplan sowie die VMM-Quell- und Zielserver an. Der Quellserver muss virtuelle Computer aufweisen, die für Failover und Wiederherstellung aktiviert sind. Wählen Sie **Hyper-V** aus, um nur Clouds anzuzeigen, die für die Hyper-V-Replikation konfiguriert sind.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP1.png)

3. Wählen Sie in **Virtuellen Computer auswählen** Replikationsgruppen aus. Alle der Replikationsgruppe zugeordneten virtuellen Computer werden ausgewählt und dem Wiederherstellungsplan hinzugefügt. Diese virtuellen Computer werden der Standardgruppe im Wiederherstellungsplan, Gruppe 1, hinzugefügt. Sie können bei Bedarf weitere Gruppen hinzufügen. Beachten Sie, dass nach der Replikation virtuelle Computer gemäß der Reihenfolge der Wiederherstellungsplangruppen gestartet werden.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP2.png)	

4. Nachdem ein Wiederherstellungsplan erstellt wurde, wird er auf der Registerkarte **Wiederherstellungspläne** angezeigt. 
5. Wählen Sie den gewünschten Wiederherstellungsplan auf der Registerkarte **Wiederherstellungspläne** aus, und klicken Sie auf **Testfailover**.
6. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** aus. Bei Aktivierung dieser Option werden nach einem Failover die virtuellen Computer im Replikat mit keinem Netzwerk verbunden. Es wird getestet, ob das Failover für den virtuellen Computer wie erwartet erfolgt, ohne dass Ihre Netzwerkumgebung für die Replikation getestet wird. Wenn Sie ein umfassenderes Testfailover ausführen möchten, lesen Sie <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testen einer Bereitstellung zwischen lokalen Standorten</a> in MSDN.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


7. Der virtuelle Computer für den Test wird auf dem Host erstellt, auf dem auch der replizierte virtuelle Computer vorhanden ist. Er wird nicht der Cloud hinzugefügt, in der sich der replizierte virtuelle Computer befindet.
8. Nach der Replikation hat der replizierte virtuelle Computer eine IP-Adresse, die nicht der IP-Adresse des primären virtuellen Computers entspricht. Wenn Sie Adressen mit DHCP ausstellen, wird DNS automatisch aktualisiert. Wenn Sie nicht DHCP verwenden und sicherstellen möchten, dass die Adressen identisch sind, müssen Sie verschiedene Skripts ausführen.
9. Führen Sie diese Beispielskript aus, um die IP-Adresse abzurufen.
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


<h3><a id="runtest"></a>Überwachen von Aktivitäten</h3>
<p>Sie können die Registerkarte <b>Aufträge</b> und das <b>Dashboard</b> verwenden, um die Hauptaufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Hierzu zählen das Konfigurieren des Schutzes für eine Cloud, das Aktivieren und Deaktivieren des Schutzes für einen virtuellen Computer, das Durchführen eines Failovers (geplant, nicht geplant oder Test) und das Durchführen eines Commitvorgangs für ein nicht geplantes Failover.</p>

<p>Auf der Registerkarte <b>Aufträge</b> können Sie Aufträge anzeigen, Details zu den einzelnen Aufträgen und Fehlern aufrufen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und nicht erfolgreiche Aufträge neu starten.</p>

<p>Im <b>Dashboard</b> können Sie die aktuellen Versionen der Installationsdateien für Anbieter und Agent herunterladen, Konfigurationsinformationen für den Tresor abrufen, die Anzahl der virtuellen Computer anzeigen, deren Schutz vom Tresor verwaltet wird, die letzten Aufträge anzeigen, das Tresorzertifikat verwalten und virtuelle Computer erneut synchronisieren.</p>

<p>Weitere Informationen zur Interaktion mit Aufträgen und dem Dashboard finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Betriebs- und Überwachungshandbuch</a>.</p>
	
<h2><a id="next"></a>Nächste Schritte</h2>
<UL>
<LI>Informationen zur Planung und Bereitstellung von Azure Site Recovery in einer realen Produktionsumgebung finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planungshandbuch für Azure Site Recovery</a> und im <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Bereitstellungshandbuch für Azure Site Recovery</a>.</LI>
<LI>Wenn Sie Fragen haben, besuchen Sie das <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI> 
</UL>

<!--HONumber=35.2-->

<!--HONumber=46--> 
