<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen Standorten

<div class="dev-callout">

Mit Azure Site Recovery können Sie den Schutz virtueller Computer auf lokalen Hyper-V-Hostservern in VMM-Clouds organisieren. Sie können folgende Konfigurationen vornehmen:

-   **Schutz zwischen lokalen Standorten**: Lokale virtuelle Computer werden an einem anderen lokalen Standort repliziert.Sie können Schutzeinstellungen in Azure Site Recovery-Tresoren konfigurieren und aktivieren. Daten von virtuellen Computern werden von einem lokalen Hyper-V-Server auf einen anderen repliziert. Weitere Informationen zu diesem Szenario finden Sie unter [Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen Standorten][Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen Standorten].
-   **Schutz zwischen einem lokalen Standort und Azure**: Lokale virtuelle Computer werden in Azure repliziert. Sie können Schutzeinstellungen in Azure Site Recovery-Tresoren konfigurieren und aktivieren. Daten von virtuellen Computern werden von einem lokalen Hyper-V-Server in Azure Storage repliziert. Weitere Informationen zu diesem Szenario finden Sie unter [Erste Schritte mit Azure Site Recovery: Schutz zwischen einem lokalen Standort und Azure][Erste Schritte mit Azure Site Recovery: Schutz zwischen einem lokalen Standort und Azure].

## <span id="about"></span></a>Informationen zu diesem Lernprogramm

In diesem Lernprogramm erfahren Sie, wie Sie schnell eine Machbarkeitsstudie für Azure Site Recovery in einer Bereitstellung zwischen einem lokalen Standort und Azure vorbereiten. Dabei werden der kürzeste Pfad und nach Möglichkeit Standardeinstellungen verwendet. Sie erstellen einen Azure Site Recovery-Tresor, installieren den Azure Site Recovery-Anbieter auf dem VMM-Quellserver, konfigurieren Cloudschutzeinstellungen, aktivieren den Schutz für virtuelle Computer und testen die Bereitstellung.

Informationen zu einer vollständigen Bereitstellung finden Sie hier:

-   [Planen der Bereitstellung von Azure Site Recovery][Planen der Bereitstellung von Azure Site Recovery]: Beschreibt die Planungsschritte, die Sie ausführen müssen, bevor Sie mit einer vollständigen Bereitstellung beginnen.
-   [Bereitstellen von Azure Site Recovery: Schutz zwischen lokalen Standorten][Bereitstellen von Azure Site Recovery: Schutz zwischen lokalen Standorten]: Beschreibt Schritt für Schritt die Vorgehensweise für eine vollständige Bereitstellung.

Falls Sie bei der Bearbeitung dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel [Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen][Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen], oder stellen Sie Ihre Fragen im [Azure Recovery Services-Forum][Azure Recovery Services-Forum].

</div>

## <span id="before"></span></a>Vorbemerkungen

<div class="dev-callout">

Prüfen Sie die Voraussetzungen, bevor Sie mit diesem Lernprogramm beginnen.

### <span id="HVRMPrereq"></span></a>Voraussetzungen

-   **Azure-Konto**: Sie benötigen ein Azure-Konto. Falls Sie keines haben, besuchen Sie [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion]. Preisinformationen finden Sie unter [Azure Site Recovery-Preisdetails][Azure Site Recovery-Preisdetails].
-   **VMM-Server**: Sie benötigen mindestens einen VMM-Server mit System Center 2012 SP1 oder System Center 2012 R2.
-   **VMM-Clouds**: Sie benötigen mindestens eine Cloud auf dem VMM-Quellserver, den Sie schützen möchten, und eine auf dem VMM-Zielserver. Wenn Sie einen VMM-Server ausführen, sind dafür zwei Clouds erforderlich. Die primäre Cloud, die Sie schützen möchten, muss Folgendes enthalten:
    -   Eine oder mehrere VMM-Hostgruppen
    -   Einen oder mehrere Hyper-V-Hostserver oder -Cluster in jeder Hostgruppe.
    -   Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud.

## <span id="tutorial"></span></a>Schritte im Lernprogramm

Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:

-   [Schritt 1: Erstellen eines Tresors][Schritt 1: Erstellen eines Tresors] – Erstellen Sie einen Azure Site Recovery-Tresor.
-   [Schritt 2: Installieren der Anbieteranwendung][Schritt 2: Installieren der Anbieteranwendung] – Generieren Sie einen Registrierungsschlüssel, und führen Sie die Microsoft Azure Site Recovery-Anbieteranwendung auf dem VMM-Server aus. Dadurch wird der Anbieter installiert und der VMM-Server im Tresor registriert.
-   [Schritt 3: Konfigurieren des Cloudschutzes][Schritt 3: Konfigurieren des Cloudschutzes] – Konfigurieren Sie die Schutzeinstellungen für VMM-Clouds.
-   <a href="#networkmapping">Schritt 5: Konfigurieren der Netzwerkzuordnung – Sie können optional eine Netzwerkzuordnung konfigurieren, um Quell-VM-Netzwerke Ziel-VM-Netzwerken zuzuordnen.
-   [Schritt 6: Konfigurieren der Speicherzuordnung][Schritt 6: Konfigurieren der Speicherzuordnung] – Sie können optional eine Speicherzuordnung konfigurieren, um Speicherklassifizierungen auf dem VMM-Quellserver Speicherklassifizierungen auf dem Zielserver zuzuordnen.
-   [Schritt 7: Aktivieren des Schutzes für virtuelle Computer][Schritt 7: Aktivieren des Schutzes für virtuelle Computer] – Aktivieren Sie den Schutz für die virtuellen Computer, die sich in den geschützten VMM-Clouds befinden.
-   [Schritt 8: Konfigurieren und Ausführen von Wiederherstellungsplänen][Schritt 8: Konfigurieren und Ausführen von Wiederherstellungsplänen] – Erstellen Sie einen Wiederherstellungsplan, und führen Sie ein Test-Failover für den Plan durch.

<a name="vault"></a>

## Schritt 1: Erstellen eines Tresors

</p>
1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Erweitern Sie **Datendienste**, erweitern Sie **Wiederherstellungsdienste**, und klicken Sie dann auf **Standortwiederherstellungstresor**.

3.  Klicken Sie auf **Neu erstellen**, und klicken Sie dann auf **Schnellerfassung**.

4.  Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5.  Wählen Sie unter **Region** die geografische Region für den Tresor aus. Welche Regionen unterstützt werden, erfahren Sie unter "Geografische Verfügbarkeit" unter [Azure Site Recovery-Preisdetails][1].

6.  Klicken Sie auf **Tresor erstellen**.

    ![Neuer Tresor][Neuer Tresor]

Verwenden Sie die Statusleiste, um zu bestätigen, dass der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite "Wiederherstellungsdienste" als **Aktiv** aufgeführt.

<a name="upload"></a>

## Schritt 2: Konfigurieren des Tresors

</p>
1.  Klicken Sie auf der Seite **Wiederherstellungsdienste** auf den gewünschten Tresor, um die Seite "Schnellstart" zu öffnen. Diese Seite kann auch jederzeit über das Symbol geöffnet werden.

    ![Schnellstart-Symbol][Schnellstart-Symbol]

2.  Wählen Sie in der Dropdownliste die Option **Zwischen zwei lokalen Hyper-V-Standorten** aus.
3.  Klicken Sie unter **VMM-Server vorbereiten** auf **Registrierungsschlüssel generieren**. Der generierte Schlüssel ist fünf Tage lang gültig. Kopieren Sie die Datei auf den VMM-Server. Sie benötigen sie zum Einrichten des Anbieters.

    ![Registrierungsschlüssel][Registrierungsschlüssel]

<a name="download"></a>

## Schritt 3: Installieren des Azure Site Recovery-Anbieters

</p>
1.  Klicken Sie auf der Seite **Schnellstart** unter **VMM-Server vorbereiten** auf **Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Server herunterladen**, um die aktuellste Version der Installationsdatei für den Anbieter abzurufen.

2.  Führen Sie diese Datei auf den VMM-Quell- und -Zielservern aus.

3.  Wählen Sie in **Überprüfung der Voraussetzungen** die Option zum Beenden des VMM-Diensts aus, um mit der Einrichtung des Anbieters zu beginnen. Der Dienst wird beendet und nach dem Abschluss der Einrichtung automatisch neu gestartet.

    ![Voraussetzungen][Voraussetzungen]

4.  In **Microsoft Update** können Sie Updates abonnieren. Wenn diese Einstellung aktiviert ist, werden Updates von Anbietern gemäß Ihrer Microsoft Update-Richtlinie installiert.

    ![Microsoft-Updates][Microsoft-Updates]

Nachdem der Anbieter installiert wurde, setzen Sie Setup fort, um den Server beim Tresor zu registrieren.

1.  Geben Sie auf der Seite "Internetverbindung" an, wie der Anbieter auf dem VMM-Server die Verbindung mit dem Internet herstellen soll. Wählen Sie **Standard-Systemproxyeinstellungen verwenden** aus, um die auf dem Server konfigurierten Standardeinstellungen für die Internetverbindung zu verwenden.

    ![Interneteinstellungen][Interneteinstellungen]

2.  Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
3.  Überprüfen Sie in **Tresorname** den Namen des Tresors, in dem der Server registriert wird.
4.  Geben Sie unter **Servername** einen benutzerfreundlichen Namen ein, um den VMM-Server im Tresor zu benennen.

    ![Serverregistrierung][Serverregistrierung]

5.  Wählen Sie unter **Anfängliche Cloud-Metadatensynchronisierung** aus, ob die Metadaten aller Clouds auf dem VMM-Server mit dem Tresor synchronisiert werden sollen. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Wenn Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktiviert lassen und jede Cloud einzeln in den Cloud-Eigenschaften in der VMM-Konsole synchronisieren.

6.  In **Datenverschlüsselung** generieren Sie ein Zertifikat, das verwendet wird, um geschützte Daten in Azure zu verschlüsseln.
    Wenn Sie die Replizierung zwischen zwei lokalen Standorten verwenden, ist diese Option nicht relevant.

    ![Serverregistrierung][2]

7.  Klicken Sie auf **Registrieren**, um den Prozess abzuschließen. Nach der Registrierung werden Metadaten vom VMM-Server durch Azure Site Recovery abgerufen. Der Server wird im Tresor auf der Seite **Server** auf der Registerkarte **Ressourcen** angezeigt.

## <span id="clouds"></span></a>Schritt 4: Konfigurieren der Cloudschutzeinstellungen

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Installation des Anbieters die Option **Cloud-Daten mit Tresor synchronisieren** aktiviert. Deshalb werden alle Clouds auf dem VMM-Server auf der Registerkarte **Geschützte Elemente** im Tresor angezeigt.

![Veröffentlichte Cloud][Veröffentlichte Cloud]

1.  Klicken Sie auf der Seite "Schnellstart" auf **Schutz für VMM-Clouds einrichten**.
2.  Wählen Sie auf der Registerkarte **Geschützte Elemente** die Cloud aus, die Sie konfigurieren möchten, und gehen Sie zur Registerkarte **Konfiguration**. Beachten Sie Folgendes:
3.  Wählen Sie unter **Ziel** die Option **VMM** aus.
4.  Wählen Sie unter **Zielspeicherort** den lokalen VMM-Server aus, der die für die Wiederherstellung verwendete Cloud verwaltet.
5.  Wählen Sie unter **Zielcloud** die Zielcloud aus, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten. Beachten Sie Folgendes:

    -   Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
    -   Eine Cloud kann nur zu einem einzelnen Cloudpaar gehören – entweder als primäre oder Zielcloud.

6.  Lassen Sie unter **Kopierhäufigkeit** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen. Dies ist nur relevant, wenn der Hyper-V-Host Windows Server 2012 R2 ausführt. Für andere Server wird die Standardeinstellung "5 Minuten" verwendet.
7.  Lassen Sie unter **Zusätzliche Wiederherstellungspunkte** die Standardeinstellung stehen. Dieser Wert gibt an, ob zusätzliche Wiederherstellungspunkte erstellt werden sollen. Wenn die Standardeinstellung 0 (Null) aktiviert ist, wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
8.  Lassen Sie unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind. Wenn Sie diesen Wert für die Lernprogramm-Anleitung festlegen möchten, stellen Sie sicher, dass er auf eine kleinere Zahl gesetzt ist als die zusätzlichen Wiederherstellungspunkte, die Sie konfigurieren.
    ![Schutzeinstellungen konfigurieren][Schutzeinstellungen konfigurieren]
9.  Geben Sie unter **Datenübertragungskomprimierung** an, ob replizierte Daten, die übertragen werden, komprimiert werden sollen.
10. Geben Sie unter **Authentifizierung** an, wie der Datenverkehr zwischen dem primären und dem Hyper V-Wiederherstellungs-Hostserver authentifiziert wird. Wählen Sie für die Zwecke dieser Anleitung HTTPS aus, es sei denn, Sie haben eine funktionierende Kerberos-Umgebung konfiguriert. Azure Site Recovery konfiguriert automatisch Zertifikate für die HTTPS-Authentifizierung. Es ist keine manuelle Konfiguration notwendig. Bitte beachten Sie, dass diese Einstellung nur für Hyper-V-Hostserver relevant ist, die unter Windows Server 2012 R2 laufen.
11. Lassen Sie unter **Port** die Standardeinstellung stehen. Dieser Wert gibt die Nummer des Ports an, auf dem die Quell- und der Ziel-Hyper-V-Hostcomputer auf Replikationsdatenverkehr lauschen.
12. Geben Sie unter **Replikationsmethode** an, wie die anfängliche Replikation der Daten vom Quell- zum Zielspeicherort erfolgt, bevor die reguläre Replikation beginnt.

    -   **Über Netzwerk** – Das Kopieren von Daten über das Netzwerk kann zeitaufwendig und ressourcenintensiv sein. Es wird empfohlen, diese Option zu verwenden, wenn die Cloud virtuelle Computer mit relativ kleinen virtuellen Festplatten enthält und der primäre VMM-Server über eine große Bandbreite mit dem sekundären VMM-Server verbunden ist. Sie können angeben, dass der Kopiervorgang sofort gestartet werden soll, oder eine Uhrzeit auswählen. Wenn Sie Netzwerkreplikation verwenden, empfehlen wir, deren Ausführung außerhalb der Spitzenzeiten einzuplanen.
    -   **Offline** – Diese Methode gibt an, dass die erste Replikation mithilfe von externen Medien ausgeführt wird. Dies ist sinnvoll, wenn Sie einen Abfall der Netzwerkleistung vermeiden möchten oder wenn Sie mit geografisch fernen Orten arbeiten. Wenn Sie diese Methode anwenden möchten, müssen Sie den Exportspeicherort für die Quellcloud und den Importspeicherort angeben. Wenn sie den Schutz für einen virtuellen Computer aktivieren, wird die virtuelle Festplatte an den angegebenen Exportspeicherort kopiert. Sie senden sie an den Zielstandort und kopieren sie an den Importspeicherort. Das System kopiert die importierten Informationen auf die virtuellen Replikatcomputer. Eine vollständige Liste der Voraussetzungen für die Offline-Replikation finden sie unter [Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds][Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds] im Bereitstellungshandbuch.

13. Wählen Sie **Virtuellen Replikatcomputer löschen** aus, um anzugeben, dass der virtuelle Replikatcomputer gelöscht werden soll, wenn er nicht mehr geschützt wird, weil Sie die Option **Schutz für virtuellen Computer löschen** auf der Registerkarte "Virtuelle Computer" der Cloud-Eigenschaften ausgewählt haben. Wenn diese Einstellung aktiviert ist und Sie den Schutz deaktivieren, wird der virtuelle Computer aus Azure Site Recovery entfernt, die Site Recovery-Einstellungen für den virtuellen Computer werden aus der VMM-Konsole entfernt, und das Replikat wird gelöscht.
    ![Schutzeinstellungen konfigurieren][3]

Nachdem Sie die Einstellungen gespeichert haben, wird ein Auftrag erstellt, der auf der Registerkarte **Aufträge** überwacht werden kann. Alle Hyper-V-Hostserver in der VMM-Quellcloud werden für die Replikation konfiguriert. Die Cloudeinstellungen können auf der Registerkarte **Konfigurieren** bearbeitet werden. Wenn Sie den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und dann die Cloud neu konfigurieren.

## <span id="networkmapping"></span></a>Schritt 5: Konfigurieren der Netzwerkzuordnung

In diesem Lernprogramm wird der einfachste Weg zum Bereitstellen von Azure Site Recovery in einer Testumgebung beschrieben. Wenn Sie die Netzwerkzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, lesen Sie im Planungshandbuch unter [Vorbereiten der Netzwerkzuordnung][Vorbereiten der Netzwerkzuordnung] nach. Führen Sie zum Konfigurieren der Zuordnung die Schritte unter [Konfigurieren der Netzwerkzuordnung][Konfigurieren der Netzwerkzuordnung] im Bereitstellungshandbuch aus.

## <span id="storagemapping"></span></a>Schritt 6: Konfigurieren der Speicherzuordnung

In diesem Lernprogramm wird der einfachste Weg zum Bereitstellen von Azure Site Recovery in einer Testumgebung beschrieben. Wenn Sie die Speicherzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, führen Sie die im Bereitstellungshandbuch unter [Konfigurieren der Speicherzuordnung][Konfigurieren der Speicherzuordnung] beschriebenen Schritte aus.

## <span id="enablevirtual"></span></a>Schritt 7: Aktivieren des Schutzes für virtuelle Computer

Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren.

1.  Klicken Sie auf der Registerkarte **Virtuelle Computer** in der Cloud, in der sich der virtuelle Computer befindet, auf **Schutz aktivieren**, und wählen Sie dann **Virtuelle Computer hinzufügen** aus.
2.  Wählen Sie aus der Liste der virtuellen Computer in der Cloud den virtuellen Computer aus, den Sie schützen möchten.

![Aktivieren des Schutzes für virtuelle Computer][Aktivieren des Schutzes für virtuelle Computer]

Sie können den Fortschritt der Schutzaktivierung einschließlich der ersten Replikation auf der Registerkarte \*\*Aufträge\*\* verfolgen. Nach der Ausführung des Auftrags zum Abschließen des Schutzes ist der virtuelle Computer für ein Failover bereit. Nach dem Aktivieren des Schutzes und der Replikation der virtuellen Computer können Sie die virtuellen Computer in Azure sehen.

![Schutzauftrag für virtuelle Computer][Schutzauftrag für virtuelle Computer]

## <span id="recoveryplans"></span></a>Schritt 8: Testen der Bereitstellung

Um Ihre Bereitstellung zu testen, können Sie ein Test-Failover für einen einzelnen virtuellen Computer durchführen oder einen Wiederherstellungsplan erstellen, der mehrere virtuelle Computer umfasst, und ein Test-Failover für diesen Plan durchführen. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk.

-   Anweisungen zum Erstellen eines Wiederherstellungsplans finden Sie unter [Erstellen und Anpassen von Wiederherstellungsplänen: Schutz zwischen lokalen Standorten und Azure][Erstellen und Anpassen von Wiederherstellungsplänen: Schutz zwischen lokalen Standorten und Azure].
-   Anweisungen zum Durchführen eines Test-Failovers finden Sie unter [Testen einer Bereitstellung zwischen lokalen Standorten][Testen einer Bereitstellung zwischen lokalen Standorten].

### <span id="runtest"></span></a>Überwachen von Aktivitäten

Sie können die Registerkarte **Aufträge** und das **Dashboard** verwenden, um die Hauptaufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Hierzu zählen das Konfigurieren des Schutzes für eine Cloud, das Aktivieren und Deaktivieren des Schutzes für einen virtuellen Computer, das Durchführen eines Failovers (geplant, nicht geplant oder Test) und das Durchführen eines Commitvorgangs für ein nicht geplantes Failover.

Auf der Registerkarte **Aufträge** können Sie Aufträge anzeigen, Details zu den einzelnen Aufträgen und zu Fehlern aufrufen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und fehlgeschlagene Aufträge neu starten.

Im **Dashboard** können Sie die aktuellen Versionen der Installationsdateien für Anbieter und Agent herunterladen, Konfigurationsinformationen für den Tresor abrufen, die Anzahl der virtuellen Computer anzeigen, deren Schutz von dem Tresor verwaltet wird, die letzten Aufträge anzeigen, das Tresorzertifikat verwalten und virtuelle Computer erneut synchronisieren.

Weitere Informationen zur Interaktion mit Aufträgen und mit dem Dashboard finden Sie im [Betriebs- und Überwachungshandbuch][Betriebs- und Überwachungshandbuch].

## <span id="next"></span></a>Nächste Schritte

-   Informationen zur Planung und Bereitstellung von Azure Site Recovery in einer wirklichen Produktionsumgebung finden Sie im [Planungshandbuch für Azure Site Recovery][Planen der Bereitstellung von Azure Site Recovery] und im [Bereitstellungshandbuch für Azure Site Recovery][Bereitstellen von Azure Site Recovery: Schutz zwischen lokalen Standorten].
-   Wenn Sie Fragen haben, besuchen Sie das [Azure Recovery Services-Forum][Azure Recovery Services-Forum].

</div>

  [Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen Standorten]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Erste Schritte mit Azure Site Recovery: Schutz zwischen einem lokalen Standort und Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Planen der Bereitstellung von Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Bereitstellen von Azure Site Recovery: Schutz zwischen lokalen Standorten]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: Allgemeine Fehlerszenarien und Lösungen]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure Recovery Services-Forum]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Kostenlose Azure-Testversion]: http://aka.ms/try-azure
  [Azure Site Recovery-Preisdetails]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Schritt 1: Erstellen eines Tresors]: #vault
  [Schritt 2: Installieren der Anbieteranwendung]: #download
  [Schritt 3: Konfigurieren des Cloudschutzes]: #clouds
  [Schritt 6: Konfigurieren der Speicherzuordnung]: #storagemapping
  [Schritt 7: Aktivieren des Schutzes für virtuelle Computer]: #enablevirtual
  [Schritt 8: Konfigurieren und Ausführen von Wiederherstellungsplänen]: #recovery%20plans
  [Verwaltungsportal]: https://manage.windowsazure.com
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
  [Vorbereiten der Netzwerkzuordnung]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Konfigurieren der Netzwerkzuordnung]: http://go.microsoft.com/fwlink/?LinkId=402534
  [Konfigurieren der Speicherzuordnung]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Aktivieren des Schutzes für virtuelle Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Schutzauftrag für virtuelle Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Erstellen und Anpassen von Wiederherstellungsplänen: Schutz zwischen lokalen Standorten und Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testen einer Bereitstellung zwischen lokalen Standorten]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Betriebs- und Überwachungshandbuch]: http://go.microsoft.com/fwlink/?LinkId=398534
