<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Erste Schritte mit Azure Site Recovery: Schutz &quot;von lokal an lokal&quot;" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Hyper-V-Computern in lokalen VMM-Clouds auf andere lokale Standorte." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Erste Schritte mit Azure Site Recovery: Schutz &quot;von lokal an lokal&quot;" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Erste Schritte mit Azure Site Recovery: Schutz "von lokal an lokal"

<div class="dev-callout">

Verwenden Sie Azure Site Recovery, um den Schutz für virtuelle Computer auf lokalen Hyper-V-Host-Servern in VMM-Clouds zu koordinieren. Sie können Folgendes konfigurieren:

-   **Schutz "von lokal an lokal"**—Virtuelle Computer werden von einer lokalen Site an eine andere repliziert. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Die Daten der virtuellen Computer werden von einem lokalen Hyper-V-Server an einen anderen repliziert. Weitere Informationen zu diesem Szenario finden Sie in [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“][Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“].
-   **Schutz "von lokal an Azure"**—Virtuelle Computer werden von einer lokalen Site an Azure repliziert. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Die Daten der virtuellen Computer werden von einem lokalen Hyper-V-Server in den Azure-Speicher repliziert. Weitere Informationen zu diesem Szenario finden Sie in [Erste Schritte mit Azure Site Recovery: Schutz "von lokal an Azure"][Erste Schritte mit Azure Site Recovery: Schutz "von lokal an Azure"]

## <span id="about"></span></a>Informationen zu diesem Lernprogramm

Mit diesem Lernprogramm können Sie eine schnelle Machbarkeitsstudie für Azure Site Recovery in einer "von lokal an Azure"-Bereitstellung einrichten. Wo möglich, werden der schnellste Pfad und die Standardeinstellungen verwendet. Sie erstellen einen Azure Site Recovery-Tresor, installieren den Azure Site Recovery-Anbieter auf dem VMM-Quellserver, konfigurieren Cloud-Schutzeinstellungen, aktivieren den Schutz für virtuelle Computer und testen Ihre Bereitstellung.

Informationen über eine vollständige Bereitstellung finden Sie unter:

-   [Planen der Azure Site Recovery-Bereitstellung][Planen der Azure Site Recovery-Bereitstellung]—Beschreibt die Planungsschritte, die Sie vor dem Start einer vollständigen Bereitstellung durchführen sollten.
-   [Bereitstellen von Azure Site Recovery: Schutz "von lokal an lokal"][Bereitstellen von Azure Site Recovery: Schutz "von lokal an lokal"]— Enthält schrittweise Anleitungen für eine vollständige Bereitstellung.

Wenn Sie während dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel [Azure Site Recovery: Common Error Scenarios and Resolutions][Azure Site Recovery: Common Error Scenarios and Resolutions], oder posten Sie Ihre Fragen im [Azure Recovery Services-Forum][Azure Recovery Services-Forum].

</div>

## <span id="before"></span></a>Voraussetzungen

<div class="dev-callout">

Prüfen Sie die Voraussetzungen, bevor Sie mit diesem Lernprogramm beginnen.

### <span id="HVRMPrereq"></span></a>Voraussetzungen

-   **Azure-Konto** – Sie benötigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, gehen Sie zu [Kostenloses Azure-Testkonto][Kostenloses Azure-Testkonto]. Informationen zu Preisen finden Sie unter [Azure Site Recovery-Manager – Preisdetails][Azure Site Recovery-Manager – Preisdetails].
-   **VMM-server**—Sie benötigen mindestens einen VMM-Server mit System Center 2012 SP1 oder System Center 2012 R2.
-   **VMM-Clouds**—Sie benötigen mindestens eine Cloud auf dem VMM-Quellserver, den Sie schützen möchten, und eine Cloud auf dem VMM-Zielserver. Wenn Sie einen VMM-Server ausführen, müssen zwei Clouds konfiguriert sein. Die primäre Cloud, die Sie schützen möchten, muss Folgendes enthalten:
    -   Eine oder mehrere VMM-Hostgruppen
    -   Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.
    -   Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud.

## <span id="tutorial"></span></a>Schritte im Lernprogramm

Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:

-   [Schritt 1: Erstellen eines Tresors][Schritt 1: Erstellen eines Tresors]—Erstellen Sie einen Azure Site Recovery-Tresor.
-   [Schritt 2: Installieren der Anbieteranwendung][Schritt 2: Installieren der Anbieteranwendung]–– Generieren Sie einen Registrierungsschlüssel und führen Sie die Microsoft Azure Site Recovery-Anbieteranwendung auf dem VMM-Server aus. Dadurch wird der Anbieter installiert und der VMM-Server im Tresor registriert.
-   [Schritt 3: Konfigurieren des Cloud-Schutzes][Schritt 3: Konfigurieren des Cloud-Schutzes]—Konfigurieren Sie die Schutzeinstellungen für VMM-Clouds.
-   <a href="#networkmapping">Schritt 5: Konfigurieren der Netzwerkzuordnung—Optional können Sie die Netzwerkzuordnung konfigurieren, um VM-Quellnetzwerke zu Azure-Zielnetzwerken zuzuordnen.
-   [Schritt 6: Konfigurieren der Speicherzuordnung][Schritt 6: Konfigurieren der Speicherzuordnung]—Optional können Sie die Speicherzuordnung konfigurieren, um Speicherklassifizierungen auf VMM-Quellservern zu Speicherklassifizierungen auf VMM-Zielservern zuzuordnen.
-   [Schritt 7: Aktivieren des Schutzes für virtuelle Computer][Schritt 7: Aktivieren des Schutzes für virtuelle Computer]—Aktivieren Sie den Schutz für die virtuellen Computer in den geschützten VMM-Clouds
-   [Schritt 8: Konfigurieren und Ausführen von Wiederherstellungsplänen][Schritt 8: Konfigurieren und Ausführen von Wiederherstellungsplänen]—Erstellen Sie einen Wiederherstellungsplan und führen Sie ein Test-Failover für den Plan aus.

<a name="vault"></a>

## Schritt 1: Erstellen eines Tresors

</p>
1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Erweitern Sie **Data Services**, erweitern Sie **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.

3.  Klicken Sie auf **Neu erstellen** und anschließend auf **Schnellerfassung**.

4.  Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5.  Wählen Sie unter **Region** die geografische Region für den Tresor aus. Sie finden eine Liste der unterstützten Regionen unter Geografische Verfügbarkeit auf der Seite [Azure Site Recovery – Preisdetails][Azure Site Recovery – Preisdetails]

6.  Klicken Sie auf **Tresor erstellen**.

    ![Neuer Tresor][Neuer Tresor]

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als **aktiv** aufgelistet.

<a name="upload"></a>

## Schritt 2: Konfigurieren des Tresors

</p>
1.  Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

    ![Schnellstart-Symbol][Schnellstart-Symbol]

2.  Wählen Sie in der Dropdownliste den Eintrag **Zwischen zwei lokalen Hyper-V-Sites** aus.
3.  Klicken Sie unter **VMM-Server vorbereiten** auf **Registrierungsschlüssel erstellen**. Der Schlüssel ist nach der Erstellung 5 Tage lang gültig. Kopieren Sie die Datei auf den VMM-Server. Sie benötigen die Datei bei der Einrichtung des Anbieters.

    ![Registrierungsschlüssel][Registrierungsschlüssel]

<a name="download"></a>

## Schritt 3: Installieren des Azure Site Recovery-Anbieters

</p>
1.  Klicken Sie auf der Seite **Schnellstart** unter **VMM-Server vorbereiten** auf **Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Servern herunterladen**, um die neueste Version der Anbieter-Installationsdatei herunterzuladen.

2.  Führen Sie diese Datei auf den VMM-Quell- und Zielservern aus.

3.  Geben Sie unter **Voraussetzungsprüfung** an, dass der VMM-Dienst für die Anbietereinrichtung angehalten werden soll. Der Dienst wird angehalten und wird nach Abschluss der Einrichtung automatisch neu gestartet.

    ![Voraussetzungen][Voraussetzungen]

4.  Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieter-Updates gemäß Ihrer Microsoft Update-Richtlinie installiert.

    ![Microsoft Updates][Microsoft Updates]

Fahren Sie nach der Installation des Anbieters mit der Einrichtung fort, um den Server im Tresor zu registrieren.

1.  Geben Sie auf der Seite "Internetverbindung" an, wie sich der Anbieter auf dem VMM-Server mit dem Internet verbinden soll. Wählen Sie **Proxyeinstellungen des Systems verwenden** aus, um die Standard-Internetverbindungseinstellungen auf dem Server zu verwenden.

    ![Interneteinstellungen][Interneteinstellungen]

2.  Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
3.  Prüfen Sie unter **Tresorname** den Namen des Tresors, in dem der Server registriert wird.
4.  Geben Sie unter **Servername** einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren.

    ![Serverregistrierung][Serverregistrierung]

5.  Wählen Sie unter **Erste Cloudmetadaten-Synchronisierung** aus, ob Sie Metadaten für alle Clouds auf dem VMM-Server mit dem Tresor synchronisieren möchten. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Falls Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktivieren und die Clouds einzeln in den Cloud-Einstellungen in der VMM-Konsole synchronisieren.

6.  Unter **Datenverschlüsselung** generieren Sie ein Zertifikat, das für die Verschlüsselung der in Azure geschützten Daten verwendet wird.
    Diese Optionen ist irrelevant, wenn Sie von einer lokalen Site an eine andere replizieren.

    ![Serverregistrierung][1]

7.  Klicken Sie auf **Registrieren**, um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten vom VMM-Server von Azure Site Recovery abgerufen. Der Server wird auf der Ressourcen **Ressourcen** auf der Seite **Server** im Tresor angezeigt.

## <span id="clouds"></span></a>Schritt 4: Konfigurieren der Cloudschutzeinstellungen

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Anbieterinstallation die Option **Clouddaten mit Tresor synchronisieren** aktiviert. Daher werden alle Clouds auf dem VMM-Server auf der Registerkarte **Geschützte Elemente** im Tresor angezeigt.

![Veröffentlichte Cloud][Veröffentlichte Cloud]

1.  Klicken Sie auf der Seite „Schnellstart“ auf **Schutz für VMM-Clouds einrichten**.
2.  Wählen Sie auf der Registerkarte **Geschützte Elemente** die Cloud aus, die Sie konfigurieren möchten, und gehen Sie zur Registerkarte **Konfiguration**. Beachten Sie Folgendes:
3.  Wählen Sie unter **Ziel** **VMM** aus.
4.  Wählen Sie unter **Zielspeicherort** den lokalen VMM-Server aus, der die Cloud verwaltet, die Sie für die Wiederherstellung verwenden möchten.
5.  Wählen Sie unter **Zielcloud** die Zielcloud aus, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten. Beachten Sie Folgendes:

    -   Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
    -   Eine Cloud kann nur zu einem einzigen Cloudpaar gehören –– entweder als primäre oder als Zielcloud.

6.  Lassen Sie unter **Kopierhäufigkeit** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen. Dies ist nur relevant, wenn der Hyper-V-Host Windows Server 2012 R2 ausführt. Für andere Server wird die Standardeinstellung "5 Minuten" verwendet.
7.  Lassen Sie unter **Zusätzliche Wiederherstellungspunkte** die Standardeinstellung stehen. Dieser Wert gibt an, ob zusätzliche Wiederherstellungspunkte erstellt werden sollen. Mit der Standardeinstellung 0 (Null) wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
8.  Lassen Sie unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind. Wenn Sie diesen Wert für dieses Lernprogramm festlegen möchten, sollten Sie eine kleinere Zahl verwenden als die zusätzlichen Wiederherstellungspunkte, die Sie konfigurieren.
    ![Konfigurieren der Schutzeinstellungen][Konfigurieren der Schutzeinstellungen]
9.  Geben Sie unter **Datenübertragungskomprimierung** an, ob replizierte Daten für die Übertragung komprimiert werden sollen.
10. Geben Sie unter **Authentifizierung** an, wie der Datenverkehr zwischen dem primären und dem Hyper V-Wiederherstellungs-Hostserver authentifiziert wird. Wenn Sie nicht über eine funktionierende Kerberos-Umgebung verfügen, sollten Sie für diese Anleitung HTTPS auswählen. Azure Site Recovery konfiguriert die Zertifikate für die HTTPS-Authentifizierung automatisch. Es ist keine manuelle Konfiguration notwendig. Bitte beachten Sie, dass diese Einstellung nur für Hyper-V-Hostserver relevant ist, die unter Windows Server 2012 R2 laufen.
11. Lassen Sie unter **Port** die Standardeinstellung stehen. Dieser Wert gibt die Nummer des Ports an, auf dem die Quell- und der Ziel-Hyper-V-Hostcomputer auf Replikationsdatenverkehr lauschen.
12. Geben Sie unter **Replikationsmethode** an, wie die erste Replikation von Daten vom Quell- zum Zielspeicherort erfolgen soll, bevor die reguläre Replikation beginnt.

    -   **Über das Netzwerk**–– Das Kopieren von Daten über das Netzwerk kann zeitraubend und ressourcenintensiv sein. Es wird empfohlen, diese Option zu verwenden, wenn die Cloud virtuelle Computer mit relativ kleinen virtuellen Festplatten enthält und der primäre VMM-Server über eine große Bandbreite mit dem sekundären VMM-Server verbunden ist. Sie können angeben, dass der Kopiervorgang sofort gestartet werden soll, oder eine Uhrzeit auswählen. Wenn Sie Netzwerkreplikation verwenden, empfehlen wir, deren Ausführung außerhalb der Spitzenzeiten einzuplanen.
    -   **Offline**–– Mit dieser Methode wird die erste Replikation unter Verwendung externer Medien durchgeführt. Dies ist sinnvoll, wenn Sie einen Abfall der Netzwerkleistung vermeiden möchten oder wenn Sie mit geografisch fernen Orten arbeiten. Wenn Sie diese Methode anwenden möchten, müssen Sie den Exportspeicherort für die Quellcloud und den Importspeicherort angeben. Wenn sie den Schutz für einen virtuellen Computer aktivieren, wird die virtuelle Festplatte an den angegebenen Exportspeicherort kopiert. Sie senden sie an den Zielstandort und kopieren sie an den Importspeicherort. Das System kopiert die importierten Informationen auf die virtuellen Replikatcomputer. Eine vollständige Liste der Voraussetzungen für die Offline-Replikation finden sie unter [Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds][Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds] im Bereitstellungshandbuch.

13. Mit **Virtuellen Replikatcomputer löschen** können Sie angeben, dass der virtuelle Replikatcomputer gelöscht werden soll, wenn Sie den Schutz beenden, indem Sie die Option **Schutz für virtuellen Computer löschen** in der Registerkarte Virtuelle Computer in den Cloud-Eigenschaften auswählen. Mit dieser Einstellung wird der virtuelle Computer aus Azure Site Recovery gelöscht, die Site Recovery-Einstellungen für den virtuellen Computer werden in der VMM-Konsole entfernt und das Replikat wird gelöscht, wenn Sie den Schutz des virtuellen Computers deaktivieren.
    ![Konfigurieren der Schutzeinstellungen][2]

Nach dem Speichern der Einstellungen wird ein Auftrag erstellt und kann auf der Registerkarte **Aufträge** überwacht werden. Alle Hyper-V-Hostserver in der VMM-Quell-Cloud werden für die Replikation konfiguriert. Die Cloudeinstellungen können auf der Registerkarte **Konfigurieren** bearbeitet werden. Wenn Sie den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und die Cloud anschließend neu konfigurieren.

## <span id="networkmapping"></span></a>Schritt 5: Konfigurieren der Netzwerkzuordnung

Dieses Lernprogramm beschreibt den einfachsten Weg zur Einrichtung von Azure Site Recovery in einer Testumgebung. Falls Sie die Netzwerkzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, finden Sie weitere Informationen unter [Vorbereiten der Netzwerkzuordnung][Vorbereiten der Netzwerkzuordnung] im Planungshandbuch. Führen Sie die Schritte zum [Konfigurieren der Netzwerkzuordnung][Konfigurieren der Netzwerkzuordnung] im Bereitstellungsleitfaden aus, um die Zuordnung zu konfigurieren.

## <span id="storagemapping"></span></a>Schritt 6: Konfigurieren der Speicherzuordnung

Dieses Lernprogramm beschreibt den einfachsten Weg zur Einrichtung von Azure Site Recovery in einer Testumgebung. Falls Sie die Speicherzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, finden Sie weitere Informationen unter [Konfigurieren der Speicherzuordnung][Konfigurieren der Speicherzuordnung] im Bereitstellungshandbuch.

## <span id="enablevirtual"></span></a>Schritt 7: Schutz für virtuellen Computer aktivieren

Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren.

1.  Klicken Sie auf der Registerkarte **Virtuelle Computer** für die Cloud, in der sich der virtuelle Computer befindet, auf **Schutz aktivieren** und dann auf **Virtuelle Computer hinzufügen**.
2.  Wählen Sie aus der Liste der virtuellen Computer in der Cloud denjenigen aus, den Sie schützen möchten.

![Schutz für virtuellen Computer aktivieren][Schutz für virtuellen Computer aktivieren]

Verfolgen Sie den Fortschritt der Schutzaktivierung in der Registerkarte \*\*Aufträge\*\*, inklusive der ersten Replikation. Nachdem der Auftrag zum Abschließen des Schutzes ausgeführt wurde, ist der virtuelle Computer bereit für den Failover. Nachdem der Schutz aktiviert und die virtuellen Computer repliziert wurden, sind diese in Azure sichtbar.

![Schutzauftrag für virtuellen Computer][Schutzauftrag für virtuellen Computer]

## <span id="recoveryplans"></span></a>Schritt 8: Testen der Bereitstellung

Um Ihre Bereitstellung zu testen, können Sie einen Test-Failover für einen einzelnen virtuellen Computer ausführen oder einen Wiederherstellungsplan für mehrere virtuelle Computer erstellen und einen Test-Failover für den Plan ausführen. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk.

-   Anweisungen zur Erstellung eines Wiederherstellungsplans finden Sie unter [Erstellen und Anpassen von Wiederherstellungsplänen: Von lokal an Azure][Erstellen und Anpassen von Wiederherstellungsplänen: Von lokal an Azure].
-   Anweisungen zur Ausführung eines Test-Failovers finden Sie unter [Testen einer Bereitstellung "von lokal an lokal"][Testen einer Bereitstellung "von lokal an lokal"].

### <span id="runtest"></span></a>Aktivität überwachen

Verwenden Sie die Registerkarte **Jobs** und das **Dashboard**, um die wichtigsten Aufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Dazu zählen das Konfigurieren von Schutz für eine Cloud, Aktivieren und Deaktivieren von Schutz für einen virtuellen Computer, Ausführen eines Failovers (geplant, ungeplant oder Test) und Übernehmen eines ungeplanten Failovers.

Auf der Registerkarte **Jobs** können Sie Aufträge anzeigen, ein Drill-Down zu den Auftragsdetails und -fehlern durchführen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und fehlgeschlagene Aufträge neu starten.

Über das **Dashboard** können Sie die neuesten Versionen der Anbieter- und Agent-Installationsdateien herunterladen, Konfigurationsinformationen für den Tresor erhalten, die Anzahl der virtuellen Computer anzeigen, deren Schutz vom Tresor verwaltet wird, kürzlich ausgeführte Aufträge anzeigen und virtuelle Computer neu synchronisieren.

Weitere Informationen über Interaktionen mit Aufträgen und dem Dashboard finden Sie im [Betriebs- und Überwachungshandbuch][Betriebs- und Überwachungshandbuch].

## <span id="next"></span></a>Nächste Schritte

-   Informationen zum Planen und Bereitstellen von Azure Site Recovery in einer vollständigen Produktionsumgebung finden Sie im [Planungshandbuch für Azure Site Recovery][Planen der Azure Site Recovery-Bereitstellung] und im [Bereitstellungshandbuch für Azure Site Recovery][Bereitstellen von Azure Site Recovery: Schutz "von lokal an lokal"].
-   Wenn Sie Fragen haben, besuchen Sie das [Azure Recovery Services-Forum][Azure Recovery Services-Forum]

</div>

  [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Erste Schritte mit Azure Site Recovery: Schutz "von lokal an Azure"]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Planen der Azure Site Recovery-Bereitstellung]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Bereitstellen von Azure Site Recovery: Schutz "von lokal an lokal"]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: Common Error Scenarios and Resolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure Recovery Services-Forum]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Kostenloses Azure-Testkonto]: http://aka.ms/try-azure
  [Azure Site Recovery-Manager – Preisdetails]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Schritt 1: Erstellen eines Tresors]: #vault
  [Schritt 2: Installieren der Anbieteranwendung]: #download
  [Schritt 3: Konfigurieren des Cloud-Schutzes]: #clouds
  [Schritt 6: Konfigurieren der Speicherzuordnung]: #storagemapping
  [Schritt 7: Aktivieren des Schutzes für virtuelle Computer]: #enablevirtual
  [Schritt 8: Konfigurieren und Ausführen von Wiederherstellungsplänen]: #recovery%20plans
  [Verwaltungsportal]: https://manage.windowsazure.com
  [Azure Site Recovery – Preisdetails]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Neuer Tresor]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Schnellstart-Symbol]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Registrierungsschlüssel]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Voraussetzungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Interneteinstellungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Serverregistrierung]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Veröffentlichte Cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Konfigurieren der Schutzeinstellungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Vorbereiten der Netzwerkzuordnung]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Konfigurieren der Netzwerkzuordnung]: http://go.microsoft.com/fwlink/?LinkId=402534
  [Konfigurieren der Speicherzuordnung]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Schutz für virtuellen Computer aktivieren]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Schutzauftrag für virtuellen Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Erstellen und Anpassen von Wiederherstellungsplänen: Von lokal an Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testen einer Bereitstellung "von lokal an lokal"]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Betriebs- und Überwachungshandbuch]: http://go.microsoft.com/fwlink/?LinkId=398534
