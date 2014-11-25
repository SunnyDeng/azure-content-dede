<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen Standorten

<div class="dev-callout">

<p>Mit Azure Site Recovery können Sie den Schutz virtueller Computer auf lokalen Hyper-V-Hostservern in VMM-Clouds organisieren. Sie können folgende Konfigurationen vornehmen:</p>


<ul>
<li><b>Schutz zwischen lokalen Standorten</b>: Lokale virtuelle Computer werden an einem anderen lokalen Standort repliziert.Sie können Schutzeinstellungen in Azure Site Recovery-Tresoren konfigurieren und aktivieren. Daten von virtuellen Computern werden von einem lokalen Hyper-V-Server auf einen anderen repliziert. Weitere Informationen zu diesem Szenario finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen Standorten</a>.
</a>.</li><li><b>Schutz zwischen einem lokalen Standort und Azure</b>: Lokale virtuelle Computer werden in Azure repliziert. Sie können Schutzeinstellungen in Azure Site Recovery-Tresoren konfigurieren und aktivieren. Daten von virtuellen Computern werden von einem lokalen Hyper-V-Server in Azure Storage repliziert. Weitere Informationen zu diesem Szenario finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen Standorten</a></li>

</ul>




<h2><a id="about"></a>Informationen zu diesem Lernprogramm
</h2>
<P>In diesem Lernprogramm erfahren Sie, wie Sie schnell eine Machbarkeitsstudie für Azure Site Recovery in einer Bereitstellung zwischen einem lokalen Standort und Azure vorbereiten. Dabei werden der kürzeste Pfad und nach Möglichkeit Standardeinstellungen verwendet. Sie erstellen einen Azure Site Recovery-Tresor, installieren den Azure Site Recovery-Anbieter auf dem VMM-Quellserver, konfigurieren Cloudschutzeinstellungen, aktivieren den Schutz für virtuelle Computer und testen die Bereitstellung.</P>

<P>Informationen zu einer vollständigen Bereitstellung finden Sie hier:</P>


<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planen der Bereitstellung von Azure Site Recovery</a>: Beschreibt die Planungsschritte, die Sie ausführen müssen, bevor Sie mit einer vollständigen Bereitstellung beginnen.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=402679">Bereitstellen von Azure Site Recovery: Schutz zwischen lokalen Standorten</a>: Beschreibt Schritt für Schritt die Vorgehensweise für eine vollständige Bereitstellung.</LI>

</UL>
<P>Falls Sie bei der Bearbeitung dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen</a>, oder stellen Sie Ihre Fragen im <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</P>

</div>

<h2><a id="before"></a>Vorbemerkungen</h2> 

<div class="dev-callout">

<P>Prüfen Sie die Voraussetzungen, bevor Sie mit diesem Lernprogramm beginnen.</P>

<h3><a id="HVRMPrereq"></a>Voraussetzungen</h3>

<UL>
<LI><b>Azure-Konto</b>: Sie benötigen ein Azure-Konto. Falls Sie keines haben, besuchen Sie <a href="http://aka.ms/try-azure">Kostenlose Azure-Testversion</a>. Preisinformationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Preisdetails</a>.</LI>
<LI><b>VMM-Server</b>: Sie benötigen mindestens einen VMM-Server mit System Center 2012 SP1 oder System Center 2012 R2.
<LI><b>VMM-Clouds</b>: Sie benötigen mindestens eine Cloud auf dem VMM-Quellserver, den Sie schützen möchten, und eine auf dem VMM-Zielserver. Wenn Sie einen VMM-Server ausführen, sind dafür zwei Clouds erforderlich. Die primäre Cloud, die Sie schützen möchten, muss Folgendes enthalten:<UL>
    <LI>Eine oder mehrere VMM-Hostgruppen</LI>
    <LI>Einen oder mehrere Hyper-V-Hostserver oder -Cluster in jeder Hostgruppe.</LI>
    <LI>Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud.</LI>
</UL>

<h2><a id="tutorial"></a>Schritte im Lernprogramm</h2>

Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:
<UL>
<LI><a href="#vault">Schritt 1: Erstellen eines Tresors</a>– Erstellen Sie einen Azure Site Recovery-Tresor.</LI>
<LI><a href="#download">Schritt 2: Installieren der Anbieteranwendung</a> – Generieren Sie einen Registrierungsschlüssel, und führen Sie die Microsoft Azure Site Recovery-Anbieteranwendung auf dem VMM-Server aus. Dadurch wird der Anbieter installiert und der VMM-Server im Tresor registriert.</LI>
<LI><a href="#clouds">Schritt 3: Konfigurieren des Cloudschutzes</a> – Konfigurieren Sie die Schutzeinstellungen für VMM-Clouds.</LI>
<LI><a href="#networkmapping">Schritt 5: Konfigurieren der Netzwerkzuordnung </a>– Sie können optional eine Netzwerkzuordnung konfigurieren, um Quell-VM-Netzwerke Ziel-VM-Netzwerken zuzuordnen.</LI>
<LI><a href="#storagemapping">Schritt 6: Konfigurieren der Speicherzuordnung</a>– Sie können optional eine Speicherzuordnung konfigurieren, um Speicherklassifizierungen auf dem VMM-Quellserver Speicherklassifizierungen auf dem Zielserver zuzuordnen.</LI>
<LI><a href="#enablevirtual">Schritt 7: Aktivieren des Schutzes für virtuelle Computer</a> – Aktivieren Sie den Schutz für die virtuellen Computer, die sich in den geschützten VMM-Clouds befinden.</LI>
<LI><a href="#recovery plans">Schritt 8: Konfigurieren und Ausführen von Wiederherstellungsplänen</a> – Erstellen Sie einen Wiederherstellungsplan, und führen Sie ein Test-Failover für den Plan durch.</LI>

</UL>



<a name="vault"></a> <h2>Schritt 1: Erstellen eines Tresors</h2>


1.  Melden Sie sich im <a href="#obtaincert">Verwaltungsportal</a> an.

2.  Erweitern Sie <b>Datendienste</b>, erweitern Sie <b>Wiederherstellungsdienste</b>, und klicken Sie dann auf <b>Standortwiederherstellungstresor</b>.

3.  Klicken Sie auf <b>Neu erstellen</b>, und klicken Sie dann auf <b>Schnellerfassung</b>.

4.  Geben Sie unter <b>Name</b> einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5.  Wählen Sie unter <b>Region</b> die geografische Region für den Tresor aus. Welche Regionen unterstützt werden, erfahren Sie unter "Geografische Verfügbarkeit" unter [Azure Site Recovery-Preisdetails][1].

6.  Klicken Sie auf <b>Tresor erstellen</b>.

    ![Neuer Tresor][Neuer Tresor]

<p>Verwenden Sie die Statusleiste, um zu bestätigen, dass der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite "Wiederherstellungsdienste" als <b>Aktiv</b> aufgeführt. </p>

<a name="upload"></a> <h2>Schritt 2: Konfigurieren des Tresors</h2>


1.  Klicken Sie auf der Seite <b>Wiederherstellungsdienste</b> auf den gewünschten Tresor, um die Seite "Schnellstart" zu öffnen. Diese Seite kann auch jederzeit über das Symbol geöffnet werden.

    ![Schnellstart-Symbol][Schnellstart-Symbol]

2.  Wählen Sie in der Dropdownliste die Option **Zwischen zwei lokalen Hyper-V-Standorten** aus.
3.  Klicken Sie unter **VMM-Server vorbereiten** auf **Registrierungsschlüssel generieren**. Der generierte Schlüssel ist fünf Tage lang gültig. Kopieren Sie die Datei auf den VMM-Server. Sie benötigen sie zum Einrichten des Anbieters.

    ![Registrierungsschlüssel][Registrierungsschlüssel]

<a name="download"></a> <h2>Schritt 3: Installieren des Azure Site Recovery-Anbieters</h2>
1.  Klicken Sie auf der Seite <b>Schnellstart</b> unter **VMM-Server vorbereiten** auf <b>Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Server herunterladen</b>, um die aktuellste Version der Installationsdatei für den Anbieter abzurufen.

2.  Führen Sie diese Datei auf den VMM-Quell- und -Zielservern aus.

3.  Wählen Sie in **Überprüfung der Voraussetzungen** die Option zum Beenden des VMM-Diensts aus, um mit der Einrichtung des Anbieters zu beginnen. Der Dienst wird beendet und nach dem Abschluss der Einrichtung automatisch neu gestartet.

    ![Voraussetzungen][Voraussetzungen]

4.  In **Microsoft Update** können Sie Updates abonnieren. Wenn diese Einstellung aktiviert ist, werden Updates von Anbietern gemäß Ihrer Microsoft Update-Richtlinie installiert.

    ![Microsoft-Updates][Microsoft-Updates]

Nachdem der Anbieter installiert wurde, setzen Sie Setup fort, um den Server beim Tresor zu registrieren.

5.  Geben Sie auf der Seite "Internetverbindung" an, wie der Anbieter auf dem VMM-Server die Verbindung mit dem Internet herstellen soll. Wählen Sie <b>Standard-Systemproxyeinstellungen verwenden</b> aus, um die auf dem Server konfigurierten Standardeinstellungen für die Internetverbindung zu verwenden.

    ![Interneteinstellungen][Interneteinstellungen]

6.  Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
7.  Überprüfen Sie in **Tresorname** den Namen des Tresors, in dem der Server registriert wird.
8.  Geben Sie unter **Servername** einen benutzerfreundlichen Namen ein, um den VMM-Server im Tresor zu benennen.

    ![Serverregistrierung][Serverregistrierung]

9.  Wählen Sie unter **Anfängliche Cloud-Metadatensynchronisierung** aus, ob die Metadaten aller Clouds auf dem VMM-Server mit dem Tresor synchronisiert werden sollen. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Wenn Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktiviert lassen und jede Cloud einzeln in den Cloud-Eigenschaften in der VMM-Konsole synchronisieren.

7.  In **Datenverschlüsselung** generieren Sie ein Zertifikat, das verwendet wird, um geschützte Daten in Azure zu verschlüsseln.
    Wenn Sie die Replizierung zwischen zwei lokalen Standorten verwenden, ist diese Option nicht relevant.

    ![Serverregistrierung][2]

8.  Klicken Sie auf <b>Registrieren</b>, um den Prozess abzuschließen. Nach der Registrierung werden Metadaten vom VMM-Server durch Azure Site Recovery abgerufen. Der Server wird im Tresor auf der Seite **Server** auf der Registerkarte <b>Ressourcen</b> angezeigt.

<h2><a id="clouds"></a>Schritt 4: Konfigurieren der Cloudschutzeinstellungen</h2>

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Installation des Anbieters die Option **Cloud-Daten mit Tresor synchronisieren** aktiviert. Deshalb werden alle Clouds auf dem VMM-Server auf der Registerkarte <b>Geschützte Elemente</b> im Tresor angezeigt.

![Veröffentlichte Cloud][Veröffentlichte Cloud]

1.  Klicken Sie auf der Seite "Schnellstart" auf **Schutz für VMM-Clouds einrichten**.
2.  Wählen Sie auf der Registerkarte **Geschützte Elemente** die Cloud aus, die Sie konfigurieren möchten, und gehen Sie zur Registerkarte **Konfiguration**. Beachten Sie Folgendes:
3.  Wählen Sie unter <b>Ziel</b> die Option <b>VMM</b> aus.
4.  Wählen Sie unter <b>Zielspeicherort</b> den lokalen VMM-Server aus, der die für die Wiederherstellung verwendete Cloud verwaltet.
5.  Wählen Sie unter <b>Zielcloud</b> die Zielcloud aus, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten. Beachten Sie Folgendes:
    -   Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
    -   Eine Cloud kann nur zu einem einzelnen Cloudpaar gehören – entweder als primäre oder Zielcloud.

6.  Lassen Sie unter <b>Kopierhäufigkeit</b> die Standardeinstellung stehen. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen. Dies ist nur relevant, wenn der Hyper-V-Host Windows Server 2012 R2 ausführt. Für andere Server wird die Standardeinstellung "5 Minuten" verwendet.
7.  Lassen Sie unter <b>Zusätzliche Wiederherstellungspunkte</b> die Standardeinstellung stehen. Dieser Wert gibt an, ob zusätzliche Wiederherstellungspunkte erstellt werden sollen. Wenn die Standardeinstellung 0 (Null) aktiviert ist, wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
8.  Lassen Sie unter <b>Häufigkeit von anwendungskonsistenten Momentaufnahmen</b> die Standardeinstellung stehen. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind. Wenn Sie diesen Wert für die Lernprogramm-Anleitung festlegen möchten, stellen Sie sicher, dass er auf eine kleinere Zahl gesetzt ist als die zusätzlichen Wiederherstellungspunkte, die Sie konfigurieren.
    ![Schutzeinstellungen konfigurieren][Schutzeinstellungen konfigurieren]
9.  Geben Sie unter <b>Datenübertragungskomprimierung</b> an, ob replizierte Daten, die übertragen werden, komprimiert werden sollen.
10. Geben Sie unter <b>Authentifizierung</b> an, wie der Datenverkehr zwischen dem primären und dem Hyper V-Wiederherstellungs-Hostserver authentifiziert wird. Wählen Sie für die Zwecke dieser Anleitung HTTPS aus, es sei denn, Sie haben eine funktionierende Kerberos-Umgebung konfiguriert. Azure Site Recovery konfiguriert automatisch Zertifikate für die HTTPS-Authentifizierung. Es ist keine manuelle Konfiguration notwendig. Bitte beachten Sie, dass diese Einstellung nur für Hyper-V-Hostserver relevant ist, die unter Windows Server 2012 R2 laufen.
11. Lassen Sie unter <b>Port</b> die Standardeinstellung stehen. Dieser Wert gibt die Nummer des Ports an, auf dem die Quell- und der Ziel-Hyper-V-Hostcomputer auf Replikationsdatenverkehr lauschen.
12. Geben Sie unter <b>Replikationsmethode</b> an, wie die anfängliche Replikation der Daten vom Quell- zum Zielspeicherort erfolgt, bevor die reguläre Replikation beginnt.

    -   <b>Über Netzwerk</b> – Das Kopieren von Daten über das Netzwerk kann zeitaufwendig und ressourcenintensiv sein. Es wird empfohlen, diese Option zu verwenden, wenn die Cloud virtuelle Computer mit relativ kleinen virtuellen Festplatten enthält und der primäre VMM-Server über eine große Bandbreite mit dem sekundären VMM-Server verbunden ist. Sie können angeben, dass der Kopiervorgang sofort gestartet werden soll, oder eine Uhrzeit auswählen. Wenn Sie Netzwerkreplikation verwenden, empfehlen wir, deren Ausführung außerhalb der Spitzenzeiten einzuplanen.
    -   <b>Offline</b> – Diese Methode gibt an, dass die erste Replikation mithilfe von externen Medien ausgeführt wird. Dies ist sinnvoll, wenn Sie einen Abfall der Netzwerkleistung vermeiden möchten oder wenn Sie mit geografisch fernen Orten arbeiten. Wenn Sie diese Methode anwenden möchten, müssen Sie den Exportspeicherort für die Quellcloud und den Importspeicherort angeben. Wenn sie den Schutz für einen virtuellen Computer aktivieren, wird die virtuelle Festplatte an den angegebenen Exportspeicherort kopiert. Sie senden sie an den Zielstandort und kopieren sie an den Importspeicherort. Das System kopiert die importierten Informationen auf die virtuellen Replikatcomputer. Eine vollständige Liste der Voraussetzungen für die Offline-Replikation finden sie unter [Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds][Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds] im Bereitstellungshandbuch.

13. Wählen Sie **Virtuellen Replikatcomputer löschen** aus, um anzugeben, dass der virtuelle Replikatcomputer gelöscht werden soll, wenn er nicht mehr geschützt wird, weil Sie die Option **Schutz für virtuellen Computer löschen** auf der Registerkarte "Virtuelle Computer" der Cloud-Eigenschaften ausgewählt haben. Wenn diese Einstellung aktiviert ist und Sie den Schutz deaktivieren, wird der virtuelle Computer aus Azure Site Recovery entfernt, die Site Recovery-Einstellungen für den virtuellen Computer werden aus der VMM-Konsole entfernt, und das Replikat wird gelöscht.
    ![Schutzeinstellungen konfigurieren][3]

Nachdem Sie die Einstellungen gespeichert haben, wird ein Auftrag erstellt, der auf der Registerkarte <b>Aufträge</b> überwacht werden kann. Alle Hyper-V-Hostserver in der VMM-Quellcloud werden für die Replikation konfiguriert. Die Cloudeinstellungen können auf der Registerkarte <b>Konfigurieren</b> bearbeitet werden. Wenn Sie den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und dann die Cloud neu konfigurieren.

<h2><a id="networkmapping"></a>Schritt 5: Konfigurieren der Netzwerkzuordnung</h2>

<p>In diesem Lernprogramm wird der einfachste Weg zum Bereitstellen von Azure Site Recovery in einer Testumgebung beschrieben. Wenn Sie die Netzwerkzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, lesen Sie im Planungshandbuch unter <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Vorbereiten der Netzwerkzuordnung</a> nach. Führen Sie zum Konfigurieren der Zuordnung die Schritte unter <a href="http://go.microsoft.com/fwlink/?LinkId=402534">Konfigurieren der Netzwerkzuordnung</a> im Bereitstellungshandbuch aus.</p>

<h2><a id="storagemapping"></a>Schritt 6: Konfigurieren der Speicherzuordnung</h2>

<p>In diesem Lernprogramm wird der einfachste Weg zum Bereitstellen von Azure Site Recovery in einer Testumgebung beschrieben. Wenn Sie die Speicherzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, führen Sie die im Bereitstellungshandbuch unter <a href="http://go.microsoft.com/fwlink/?LinkId=402535">Konfigurieren der Speicherzuordnung</a> beschriebenen Schritte aus.</p>

<h2><a id="enablevirtual"></a>Schritt 7: Aktivieren des Schutzes für virtuelle Computer</h2>
<p>Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren.</p>

<OL>
<li>Klicken Sie auf der Registerkarte <b>Virtuelle Computer</b> in der Cloud, in der sich der virtuelle Computer befindet, auf <b>Schutz aktivieren</b>, und wählen Sie dann <b>Virtuelle Computer hinzufügen</b> aus.</li>
<li>Wählen Sie aus der Liste der virtuellen Computer in der Cloud den virtuellen Computer aus, den Sie schützen möchten.</li> 
</OL>

![Aktivieren des Schutzes für virtuelle Computer][Aktivieren des Schutzes für virtuelle Computer]

<P>Sie können den Fortschritt der Schutzaktivierung einschließlich der ersten Replikation auf der Registerkarte <b>Aufträge</b> verfolgen. Nach der Ausführung des Auftrags zum Abschließen des Schutzes ist der virtuelle Computer für ein Failover bereit. Nach dem Aktivieren des Schutzes und der Replikation der virtuellen Computer können Sie die virtuellen Computer in Azure sehen.</P>

![Schutzauftrag für virtuelle Computer][Schutzauftrag für virtuelle Computer]

<h2><a id="recoveryplans"></a>Schritt 8: Testen der Bereitstellung</h2>

Um Ihre Bereitstellung zu testen, können Sie ein Test-Failover für einen einzelnen virtuellen Computer durchführen oder einen Wiederherstellungsplan erstellen, der mehrere virtuelle Computer umfasst, und ein Test-Failover für diesen Plan durchführen. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk.

<UL>
<li>Anweisungen zum Erstellen eines Wiederherstellungsplans finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=511492">Erstellen und Anpassen von Wiederherstellungsplänen: Schutz zwischen lokalen Standorten und Azure</a>.</li>
<li>Anweisungen zum Durchführen eines Test-Failovers finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=511493">Testen einer Bereitstellung zwischen lokalen Standorten</a>.
</li>
</UL>


<h3><a id="runtest"></a>Überwachen von Aktivitäten</h3>

<p>Sie können die Registerkarte <b>Aufträge</b> und das <b>Dashboard</b> verwenden, um die Hauptaufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Hierzu zählen das Konfigurieren des Schutzes für eine Cloud, das Aktivieren und Deaktivieren des Schutzes für einen virtuellen Computer, das Durchführen eines Failovers (geplant, nicht geplant oder Test) und das Durchführen eines Commitvorgangs für ein nicht geplantes Failover.</p>

<p>Auf der Registerkarte <b>Aufträge</b> können Sie Aufträge anzeigen, Details zu den einzelnen Aufträgen und zu Fehlern aufrufen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und fehlgeschlagene Aufträge neu starten.</p>

<p>Im <b>Dashboard</b> können Sie die aktuellen Versionen der Installationsdateien für Anbieter und Agent herunterladen, Konfigurationsinformationen für den Tresor abrufen, die Anzahl der virtuellen Computer anzeigen, deren Schutz von dem Tresor verwaltet wird, die letzten Aufträge anzeigen, das Tresorzertifikat verwalten und virtuelle Computer erneut synchronisieren.</p>

<p>Weitere Informationen zur Interaktion mit Aufträgen und mit dem Dashboard finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Betriebs- und Überwachungshandbuch]</a>.</p>

<h2><a id="next"></a>Nächste Schritte</h2>
<UL>
<LI>

Informationen zur Planung und Bereitstellung von Azure Site Recovery in einer wirklichen Produktionsumgebung finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planen der Bereitstellung von Azure Site Recovery</a> und im <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Bereitstellen von Azure Site Recovery: Schutz zwischen lokalen Standorten</a>.</LI>
<LI>Wenn Sie Fragen haben, besuchen Sie das <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</LI> 
</UL>


  [Azure Site Recovery-Preisdetails]: http://go.microsoft.com/fwlink/?LinkId=378268
  [1]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Neuer Tresor]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Schnellstart-Symbol]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Registrierungsschlüssel]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Voraussetzungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft-Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Interneteinstellungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Serverregistrierung]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Veröffentlichte Cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Schutzeinstellungen konfigurieren]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds]: http://go.microsoft.com/fwlink/?LinkId=323469
  [3]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Aktivieren des Schutzes für virtuelle Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Schutzauftrag für virtuelle Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
