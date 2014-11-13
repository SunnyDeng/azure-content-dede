<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Erste Schritte mit Azure Site Recovery: Schutz &bdquo;von lokal an Azure&ldquo;" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Hyper-V-Computern in lokalen VMM-Clouds nach Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Erste Schritte mit Azure Site Recovery: Schutz &bdquo;von lokal an Azure&ldquo;" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Erste Schritte mit Azure Site Recovery: Schutz „von lokal an Azure“

<div class="dev-callout">

Verwenden Sie Azure Site Recovery, um den Schutz für virtuelle Computer auf lokalen Hyper-V-Host-Servern in VMM-Clouds zu koordinieren. Sie können Folgendes konfigurieren:

-   **Schutz "von lokal an Azure"**—Virtuelle Computer werden von einer lokalen Site an Azure repliziert. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Die Daten der virtuellen Computer werden von einem lokalen Hyper-V-Server in den Azure-Speicher repliziert.
-   **Schutz "von lokal an lokal"**—Virtuelle Computer werden von einer lokalen Site an eine andere repliziert. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Die Daten der virtuellen Computer werden von einem lokalen Hyper-V-Server an einen anderen repliziert. Weitere Informationen zu diesem Szenario finden Sie in [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“][Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“].

## <span id="about"></span></a>Informationen zu diesem Lernprogramm

Mit diesem Lernprogramm können Sie eine schnelle Machbarkeitsstudie für Azure Site Recovery in einer "von lokal an Azure"-Bereitstellung einrichten. Wo möglich, werden der schnellste Pfad und die Standardeinstellungen verwendet. Sie erstellen einen Azure Site Recovery-Tresor, installieren den Azure Site Recovery-Anbieter auf dem VMM-Quellserver, installieren den Azure Recovery Services-Agent auf den Hyper-V-Host-Servern in den VMM-Clouds, konfigurieren Cloud-Schutzeinstellungen, aktivieren den Schutz für virtuelle Computer und testen Ihre Bereitstellung.

Informationen über eine vollständige Bereitstellung finden Sie unter:

-   [Planen der Azure Site Recovery-Bereitstellung][Planen der Azure Site Recovery-Bereitstellung]—Beschreibt die Planungsschritte, die Sie vor dem Start einer vollständigen Bereitstellung durchführen sollten.
-   [Bereitstellen von Azure Site Recovery: Schutz "von lokal an Azure"][Bereitstellen von Azure Site Recovery: Schutz "von lokal an Azure"]— Enthält schrittweise Anleitungen für eine vollständige Bereitstellung.

Wenn Sie während dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel [Azure Site Recovery: Common Error Scenarios and Resolutions][Azure Site Recovery: Common Error Scenarios and Resolutions], oder posten Sie Ihre Fragen im [Azure Recovery Services-Forum][Azure Recovery Services-Forum].

</div>

## <span id="before"></span></a>Voraussetzungen

<div class="dev-callout">

Prüfen Sie die Voraussetzungen, bevor Sie mit diesem Lernprogramm beginnen.

### <span id="HVRMPrereq"></span></a>Voraussetzungen

-   **Azure-Konto** – Sie benötigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, gehen Sie zu [Kostenloses Azure-Testkonto][Kostenloses Azure-Testkonto]. Informationen zu Preisen finden Sie unter [Azure Site Recovery-Manager – Preisdetails][Azure Site Recovery-Manager – Preisdetails].
-   **Azure-Speicherkonto** – Sie benötigen ein Azure-Speicherkonto, um an Azure replizierte Daten zu speichern. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein. Weitere Informationen zum Einrichten von Azure-Speicher finden Sie unter [Einführung in Microsoft Azure Storage][Einführung in Microsoft Azure Storage].
-   **VMM-Server** – Ein VMM-Server, der auf System Center 2012 R2 ausgeführt wird.
-   **VMM-Clouds** – Mindestens eine Cloud auf dem VMM-Server. Die Cloud muss Folgendes enthalten:
    -   Eine oder mehrere VMM-Hostgruppen
    -   Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.
    -   Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud. Es muss sich um virtuelle Computer der 1. Generation handeln.
-   **Virtuelle Computer**—Sie benötigen virtuelle Computer, die die Azure-Anforderungen erfüllen. Siehe [Voraussetzungen und Support][Voraussetzungen und Support] im Planungshandbuch.
-   Eine vollständige Liste der Supportanforderungen für virtuelle Computer für ein Failover an Azure finden Sie unter

## <span id="tutorial"></span></a>Schritte im Lernprogramm

Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:

-   [Schritt 1: Erstellen eines Tresors][Schritt 1: Erstellen eines Tresors]—Erstellen Sie einen Azure Site Recovery-Tresor.
-   [Schritt 2: Installieren der Anbieteranwendung][Schritt 2: Installieren der Anbieteranwendung]–– Generieren Sie einen Registrierungsschlüssel und führen Sie die Microsoft Azure Site Recovery-Anbieteranwendung auf dem VMM-Server aus. Dadurch wird der Anbieter installiert und der VMM-Server im Tresor registriert.
-   [Schritt 3: Hinzufügen eines Azure-Speicherkontos][Schritt 3: Hinzufügen eines Azure-Speicherkontos]—Erstellen Sie ein Speicherkonto, falls Sie noch keines haben.
-   [Schritt 4: Installieren der Agent-Anwendung][Schritt 4: Installieren der Agent-Anwendung]–– Installieren Sie den Microsoft Azure Recovery Services-Agent auf jedem Hyper-V-Host.
-   [Schritt 5: Konfigurieren des Cloud-Schutzes][Schritt 5: Konfigurieren des Cloud-Schutzes]—Konfigurieren Sie die Schutzeinstellungen für VMM-Clouds.
-   [Schritt 6: Konfigurieren der Netzwerkzuordnung][Schritt 6: Konfigurieren der Netzwerkzuordnung]—Optional können Sie die Netzwerkzuordnung konfigurieren, um VM-Quellnetzwerke zu Azure-Zielnetzwerken zuzuordnen.
-   [Schritt 7: Aktivieren des Schutzes für virtuelle Computer][Schritt 7: Aktivieren des Schutzes für virtuelle Computer]—Aktivieren Sie den Schutz für die virtuellen Computer in den geschützten VMM-Clouds.
-   [Schritt 8: Testen der Bereitstellung][Schritt 8: Testen der Bereitstellung]—Um Ihre Bereitstellung zu testen, können Sie einen Test-Failover für einen einzelnen virtuellen Computer ausführen oder einen Wiederherstellungsplan erstellen und einen Test-Failover für den Plan ausführen.

<a name="vault"></a>

## Schritt 1: Erstellen eines Tresors

</p>
1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Erweitern Sie **Data Services**, erweitern Sie **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.

3.  Klicken Sie auf **Neu erstellen** und anschließend auf **Schnellerfassung**.

4.  Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5.  Wählen Sie unter **Region** die geografische Region für den Tresor aus. Zu den verfügbaren geografischen Regionen zählen Ostasien, Westeuropa, USA (Westen), USA (Osten), Nordeuropa und Südostasien.
6.  Klicken Sie auf **Tresor erstellen**.

    ![Neuer Tresor][Neuer Tresor]

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als **aktiv** aufgelistet.

<a name="download"></a>

## Schritt 2: Generieren des Registrierungsschlüssels und Installieren des Azure Site Recovery-Anbieters

</p>
1.  Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

    ![Schnellstart-Symbol][Schnellstart-Symbol]

2.  Wählen Sie in der Dropdownliste den Eintrag **Zwischen einer lokalen Hyper-V-Site und Microsoft Azure** aus.
3.  Klicken Sie unter **VMM-Server vorbereiten** auf **Registrierungsschlüssel erstellen**. Der Schlüssel ist nach der Erstellung 5 Tage lang gültig. Kopieren Sie die Datei auf den VMM-Server. Sie benötigen die Datei bei der Einrichtung des Anbieters.

    ![Registrierungsschlüssel][Registrierungsschlüssel]

4.  Klicken Sie auf der Seite **Schnellstart** unter **VMM-Server vorbereiten** auf **Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Servern herunterladen**, um die neueste Version der Anbieter-Installationsdatei herunterzuladen.

5.  Führen Sie diese Datei auf dem VMM-Quellserver aus.

6.  Geben Sie unter **Voraussetzungsprüfung** an, dass der VMM-Dienst für die Anbietereinrichtung angehalten werden soll. Der Dienst wird angehalten und wird nach Abschluss der Einrichtung automatisch neu gestartet.

    ![Voraussetzungen][Voraussetzungen]

7.  Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieter-Updates gemäß Ihrer Microsoft Update-Richtlinie installiert.

    ![Microsoft Updates][Microsoft Updates]

Fahren Sie nach der Installation des Anbieters mit der Einrichtung fort, um den Server im Tresor zu registrieren.

1.  Geben Sie auf der Seite **Internetverbindung** an, wie sich der Anbieter auf dem VMM-Server mit dem Internet verbinden soll. Wählen Sie **Proxyeinstellungen des Systems verwenden** aus, um die Standard-Internetverbindungseinstellungen auf dem Server zu verwenden.

    ![Interneteinstellungen][Interneteinstellungen]

2.  Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
3.  Prüfen Sie unter **Tresorname** den Namen des Tresors, in dem der Server registriert wird.
4.  Geben Sie unter **Servername** einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren.

    ![Serverregistrierung][Serverregistrierung]

5.  Wählen Sie unter **Erste Cloudmetadaten-Synchronisierung** aus, ob Sie Metadaten für alle Clouds auf dem VMM-Server mit dem Tresor synchronisieren möchten. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Falls Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktivieren und die Clouds einzeln in den Cloud-Einstellungen in der VMM-Konsole synchronisieren.

6.  Geben Sie unter **Datenverschlüsselung** einen Speicherort für ein SSL-Zertifikat an, das automatisch für die Datenverschlüsselung generiert wird. Dieses Zertifikat wird verwendet, wenn Sie Datenverschlüsselung für eine Cloud im Azure Site Recovery-Portal aktivieren. Bewahren Sie dieses Zertifikat sicher auf. Wenn Sie einen Failover nach Azure ausführen, wählen Sie das Zertifikat aus, um verschlüsselte Daten zu entschlüsseln.
    Diese Option ist irrelevant, wenn Sie von einer lokalen Site an eine andere replizieren.

    ![Serverregistrierung][1]

7.  Klicken Sie auf **Registrieren**, um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten vom VMM-Server von Azure Site Recovery abgerufen. Der Server wird auf der Ressourcen **Ressourcen** auf der Seite **Server** im Tresor angezeigt.

## <span id="storage"></span></a>Schritt 3: Erstellen eines Azure-Speicherkontos

Falls Sie noch kein Azure-Speicherkonto haben, klicken Sie auf **Azure-Speicherkonto hinzufügen**. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein.

Mit diesem Lernprogramm können Sie eine schnelle Machbarkeitsstudie für Azure Site Recovery in einer "von lokal an Azure"-Bereitstellung einrichten. Wo möglich, werden der schnellste Pfad und die Standardeinstellungen verwendet. Sie erstellen einen Azure Site Recovery-Tresor, installieren den Azure Site Recovery-Anbieter auf dem VMM-Quellserver, installieren den Azure Recovery Services-Agent auf den Hyper-V-Host-Servern in den VMM-Clouds, konfigurieren Cloud-Schutzeinstellungen, aktivieren den Schutz für virtuelle Computer und testen Ihre Bereitstellung.

![Speicherkonto][Speicherkonto]

## <span id="agent"></span></a>Schritt 4: Installieren des Azure Recovery Services-Agent auf den Hyper-V-Hosts

Installieren Sie den Azure Recovery Services-Agent auf jedem Hyper-V-Hostserver in den VMM-Clouds, die Sie schützen möchten.

1.  Klicken Sie auf der Seite Schnellstart auf **Azure Site Recovery Services-Anbieter herunterladen und auf Hosts installieren**, um die neueste Version der Agent-Installationsdatei herunterzuladen.

    ![Recovery Services-Agent installieren][Recovery Services-Agent installieren]

2.  Führen Sie die Installationsdatei auf jedem Hyper-V-Hostserver aus, der sich in den den VMM-Clouds befindet, die Sie schützen möchten.
3.  Klicken Sie auf der Seite **Voraussetzungsüberprüfung** auf **Weiter**. Alle fehlenden Komponenten, die Voraussetzung sind, werden automatisch installiert.

    ![Voraussetzungen Recovery Services-Agent][Voraussetzungen Recovery Services-Agent]

4.  Geben Sie auf der Seite **Installationseinstellungen** an, wo Sie den Agent installieren möchten, und wählen Sie den Cachespeicherort aus, in dem die Sicherungsmetadaten installiert werden. Klicken Sie dann auf **Weiter**.

## <span id="clouds"></span></a>Schritt 5: Konfigurieren der Cloudschutzeinstellungen

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Anbieterinstallation die Option **Clouddaten mit Tresor synchronisieren** aktiviert. Daher werden alle Clouds auf dem VMM-Server auf der Registerkarte **Geschützte Elemente** im Tresor angezeigt.

![Veröffentlichte Cloud][Veröffentlichte Cloud]

1.  Klicken Sie auf der Seite „Schnellstart“ auf **Schutz für VMM-Clouds einrichten**.

2.  Klicken Sie auf der Registerkarte **Geschützte Elemente** auf die Cloud, die Sie konfigurieren möchten, und gehen Sie zur Registerkarte **Konfiguration**.
3.  Wählen Sie unter **Ziel** die Option **Microsoft Azure** aus.
4.  Wählen Sie unter **Speicherkonto** den Azure-Speicher aus, den Sie zum Speichern der virtuellen Azure-Computer verwenden möchten.
5.  Legen Sie für **Gespeicherte Daten verschlüsseln** die Option **Aus** fest. Diese Einstellung gibt an, dass die Daten beim Replizieren zwischen dem lokalen Standort und Azure verschlüsselt werden sollen.
6.  Lassen Sie unter **Kopierhäufigkeit** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen.
7.  Lassen Sie unter **Wiederherstellungspunkte beibehalten für** die Standardeinstellung stehen. Wenn die Standardeinstellung 0 (Null) aktiviert ist, wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
8.  Lassen Sie unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind. Wenn Sie einen Wert festlegen möchten, stellen Sie sicher, dass er geringer als die Anzahl der konfigurierten zusätzlichen Wiederherstellungspunkte ist.
9.  Geben Sie unter **Startzeit für Replikation** an, wann die erste Datenreplikation an Azure starten sollte. Die Zeitzone auf dem Hyper-V-Hostserver wird verwendet. Es wird empfohlen, die erste Replikation außerhalb der Spitzenzeiten zu planen.

    ![Cloud-Replikationseinstellungen][Cloud-Replikationseinstellungen]

Nach dem Speichern der Einstellungen wird ein Auftrag erstellt und kann auf der Registerkarte **Aufträge** überwacht werden. Alle Hyper-V-Hostserver in der VMM-Quell-Cloud werden für die Replikation konfiguriert. Nach dem Speichern können die Cloudeinstellungen auf der Registerkarte **Konfigurieren** bearbeitet werden. Um den Zielspeicherort bzw. Zielspeicher zu ändern, müssen Sie die Cloudkonfiguration entfernen und dann die Cloud neu konfigurieren. Beachten Sie: Wenn Sie das Speicherkonto ändern, wird die Änderung nur für virtuelle Computer angewandt, für die der Schutz nach Änderung des Speicherkontos aktiviert wird. Vorhandene virtuelle Computer werden nicht an das neue Speicherkonto migriert.

## <span id="networkmapping"></span></a>Schritt 6: Konfigurieren der Netzwerkzuordnung

Dieses Lernprogramm beschreibt den einfachsten Weg zur Einrichtung von Azure Site Recovery in einer Testumgebung. Falls Sie die Netzwerkzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, finden Sie weitere Informationen unter [Vorbereiten der Netzwerkzuordnung][Vorbereiten der Netzwerkzuordnung] im Planungshandbuch. Führen Sie die Schritte zum [Konfigurieren der Netzwerkzuordnung][Konfigurieren der Netzwerkzuordnung] im Bereitstellungsleitfaden aus, um die Zuordnung zu konfigurieren.

## <span id="virtualmachines"></span></a>Schritt 7: Aktivieren des Schutzes für virtuelle Computer

Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren. Beachten Sie Folgendes:

-   Die virtuellen Computer müssen die Azure-Anforderungen erfüllen. Sie finden diese Anforderungen unter [Voraussetzungen und Support][Voraussetzungen und Support] im Planungshandbuch.
-   Um den Schutz zu aktivieren, müssen die Eigenschaften "Betriebssystem" und "Betriebssystem-Datenträger" für den virtuellen Computer gesetzt sein. Sie können diese Eigenschaften setzen, wenn Sie den virtuellen Computer in VMM mithilfe einer Vorlage für virtuelle Maschinen erstellen. Außerdem können Sie diese Eigenschaften für existierende virtuelle Computer auf den Registerkarten \*\*Allgemein\*\* und \*\*Hardware-Konfiguration\*\* in den Eigenschaften der virtuellen Computer setzen. Falls diese Eigenschaften in VMM nicht angezeigt werden, sollten Sie sie dennoch im Azure Site Recovery-Portal konfigurieren können.

![Erstellen eines virtuellen Computers][Erstellen eines virtuellen Computers]

![Eigenschaften des virtuellen Computers ändern][Eigenschaften des virtuellen Computers ändern]

1.  Um den Schutz zu aktivieren, klicken Sie auf der Registerkarte **Virtuelle Computer** für die Cloud, in der sich der virtuelle Computer befindet, auf **Schutz aktivieren** und dann auf **Virtuelle Computer hinzufügen**
2.  Wählen Sie aus der Liste der virtuellen Computer in der Cloud denjenigen aus, den Sie schützen möchten.

    ![Schutz für virtuellen Computer aktivieren][Schutz für virtuellen Computer aktivieren]

3.  Überprüfen Sie den virtuellen Computer und nehmen Sie ggf. Änderungen vor.

    ![Überprüfen des virtuellen Computers][Überprüfen des virtuellen Computers]

Verfolgen Sie den Fortschritt der Schutzaktivierung in der Registerkarte \*\*Aufträge\*\*, inklusive der ersten Replikation. Nachdem der Auftrag zum Abschließen des Schutzes ausgeführt wurde, ist der virtuelle Computer bereit für den Failover. Nachdem der Schutz aktiviert und die virtuellen Computer repliziert wurden, sind diese in Azure sichtbar.

![Schutzauftrag für virtuellen Computer][Schutzauftrag für virtuellen Computer]

## <span id="test"></span></a>Schritt 8: Testen der Bereitstellung

Um Ihre Bereitstellung zu testen, können Sie einen Test-Failover für einen einzelnen virtuellen Computer ausführen oder einen Wiederherstellungsplan für mehrere virtuelle Computer erstellen und einen Test-Failover für den Plan ausführen. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk. Beachten Sie Folgendes:

-   Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Computer in Azure über Remote Desktop herstellen möchten, aktivieren Sie die Remote Desktop-Verbindung auf dem virtuellen Computer, bevor Sie das Test-Failover ausführen.
-   Nach dem Failover verwenden Sie eine öffentliche IP-Adresse, um eine Verbindung zum virtuellen Computer in Azure über Remote Desktop herzustellen. Wenn Sie dies möchten, stellen Sie sicher, dass keine Domänenrichtlinien vorhanden sind, die das Verbinden zu einem virtuellen Computer über eine öffentliche Adresse verhindern.

-   Anweisungen zur Erstellung eines Wiederherstellungsplans finden Sie unter [Erstellen und Anpassen von Wiederherstellungsplänen: Von lokal an Azure][Erstellen und Anpassen von Wiederherstellungsplänen: Von lokal an Azure].
-   Anweisungen zur Ausführung eines Test-Failovers finden Sie unter [Testen einer Bereitstellung "von lokal an Azure"][Testen einer Bereitstellung "von lokal an Azure"].

</p>
### <span id="runtest"></span></a>Aktivität überwachen

Verwenden Sie die Registerkarte **Jobs** und das **Dashboard**, um die wichtigsten Aufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Dazu zählen das Konfigurieren von Schutz für eine Cloud, Aktivieren und Deaktivieren von Schutz für einen virtuellen Computer, Ausführen eines Failovers (geplant, ungeplant oder Test) und Übernehmen eines ungeplanten Failovers.

Auf der Registerkarte **Jobs** können Sie Aufträge anzeigen, ein Drill-Down zu den Auftragsdetails und -fehlern durchführen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und fehlgeschlagene Aufträge neu starten.

Über das **Dashboard** können Sie die neuesten Versionen der Anbieter- und Agent-Installationsdateien herunterladen, Konfigurationsinformationen für den Tresor erhalten, die Anzahl der virtuellen Computer anzeigen, deren Schutz vom Tresor verwaltet wird, kürzlich ausgeführte Aufträge anzeigen und virtuelle Computer neu synchronisieren.

Weitere Informationen über Interaktionen mit Aufträgen und dem Dashboard finden Sie im [Betriebs- und Überwachungshandbuch][Betriebs- und Überwachungshandbuch].

## <span id="next"></span></a>Nächste Schritte

-   Informationen zum Planen und Bereitstellen von Azure Site Recovery in einer vollständigen Produktionsumgebung finden Sie im [Planungshandbuch für Azure Site Recovery][Planen der Azure Site Recovery-Bereitstellung] und im [Bereitstellungshandbuch für Azure Site Recovery][Bereitstellungshandbuch für Azure Site Recovery].
-   Wenn Sie Fragen haben, besuchen Sie das [Azure Recovery Services-Forum][Azure Recovery Services-Forum]

</div>

  [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Planen der Azure Site Recovery-Bereitstellung]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Bereitstellen von Azure Site Recovery: Schutz "von lokal an Azure"]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery: Common Error Scenarios and Resolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Azure Recovery Services-Forum]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Kostenloses Azure-Testkonto]: http://aka.ms/try-azure
  [Azure Site Recovery-Manager – Preisdetails]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Einführung in Microsoft Azure Storage]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Voraussetzungen und Support]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Schritt 1: Erstellen eines Tresors]: #vault
  [Schritt 2: Installieren der Anbieteranwendung]: #download
  [Schritt 3: Hinzufügen eines Azure-Speicherkontos]: #storage
  [Schritt 4: Installieren der Agent-Anwendung]: #agent
  [Schritt 5: Konfigurieren des Cloud-Schutzes]: #clouds
  [Schritt 6: Konfigurieren der Netzwerkzuordnung]: #NetworkMapping
  [Schritt 7: Aktivieren des Schutzes für virtuelle Computer]: #virtualmachines
  [Schritt 8: Testen der Bereitstellung]: #test
  [Verwaltungsportal]: https://manage.windowsazure.com
  [Neuer Tresor]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Schnellstart-Symbol]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Registrierungsschlüssel]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [Voraussetzungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Interneteinstellungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Serverregistrierung]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Speicherkonto]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Recovery Services-Agent installieren]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Voraussetzungen Recovery Services-Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Veröffentlichte Cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Cloud-Replikationseinstellungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [Vorbereiten der Netzwerkzuordnung]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Konfigurieren der Netzwerkzuordnung]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Erstellen eines virtuellen Computers]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Eigenschaften des virtuellen Computers ändern]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [Schutz für virtuellen Computer aktivieren]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Überprüfen des virtuellen Computers]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [Schutzauftrag für virtuellen Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Erstellen und Anpassen von Wiederherstellungsplänen: Von lokal an Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Testen einer Bereitstellung "von lokal an Azure"]: http://go.microsoft.com/fwlink/?LinkId=511494
  [Betriebs- und Überwachungshandbuch]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Bereitstellungshandbuch für Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295
