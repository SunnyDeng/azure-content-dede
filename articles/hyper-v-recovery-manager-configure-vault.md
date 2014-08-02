<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-hyper-v-recovery-vault" pageTitle="Configure Hyper-V Recovery Manager to protect virtual machines in VMM clouds" metaKeywords="hyper-v recovery, VMM, clouds, disaster recovery" description="Azure Hyper-V Recovery Manager can help you protect applications and services by coordinating the replication and recovery of virtual machines located in System Center 2012 VMM private clouds." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Configure Azure Hyper-V Recovery Manager" editor="jimbe" manager="cfreeman" authors="" />

Konfigurieren von Azure Hyper-V-Wiederherstellungsmanager
=========================================================

Azure Hyper-V-Wiederherstellungsmanager koordiniert und verwaltet den Schutz virtueller Hyper-V-Computer, die sich in privaten Clouds oder auf Virtual Machine Manager (VMM)-Servern in System Center 2012 Service Pack 1 (SP1) oder System Center 2012 R2 befinden. Hyper-V-Wiederherstellungsmanager steuert das Failover dieser virtuellen Computer von einem lokalen Hyper-V-Hostserver zu einem anderen. Hyper-V-Wiederherstellungsmanager-Tresore in Azure werden verwendet, um Ihre Schutzkonfiguration zu speichern.

Informationen zu diesem Lernprogramm
------------------------------------

Dieses Lernprogramm bietet eine schnelle Anleitung für den Einsatz von Hyper-V-Wiederherstellungsmanager. Lesen Sie die folgenden Abschnitte, um noch genauere Informationen zu erhalten:

-   [Planungshandbuch für Hyper-V-Wiederherstellungsmanager](http://go.microsoft.com/fwlink/?LinkId=321294) – Enthält detaillierte Informationen zu den Planungsschritten, die Sie ausführen sollten, bevor Sie eine vollständige Bereitstellung starten.
-   [Bereitstellungshandbuch für Hyper-V-Wiederherstellungsmanager](http://go.microsoft.com/fwlink/?LinkId=321295) – Enthält schrittweise Anweisungen für eine vollständige Bereitstellung.
-   Wenn während dieses Lernprogramms Probleme auftreten, öffnen Sie [Hyper-V-Wiederherstellungsmanager: Allgemeine Fehlerszenarien und Lösungen](http://go.microsoft.com/fwlink/?LinkId=389879), oder stellen Sie Ihre Fragen im [Azure Recovery Services-Forum](http://go.microsoft.com/fwlink/?LinkId=313628)

Voraussetzungen
---------------

Prüfen Sie die Voraussetzungen, bevor Sie mit diesem Lernprogramm beginnen.

### Voraussetzungen für Hyper-V-Wiederherstellungsmanager

-   \*\*Azure-Konto\*\* – Sie benötigen ein Azure-Konto, in dem die Funktion "Azure Recovery Services" aktiviert ist. Wenn Sie kein Konto haben oder diese Funktion nicht registriert ist, siehe [Kostenloses Azure-Testkonto](http://aka.ms/try-azure) und [Hyper-V-Wiederherstellungs-Manager – Preisdetails](http://go.microsoft.com/fwlink/?LinkId=378268).
-   \*\*Zertifikat (.cer)\*\* – Um VMM-Server in einem Hyper-V-Wiederherstellungsmanager-Tresor registrieren zu können, müssen Sie ein Verwaltungszertifikat hochladen, das den öffentlichen Schlüssel zu diesem Tresor enthält. Beachten Sie Folgendes:
    -   Für die Zwecke dieses Lernprogramms können Sie ein selbstsigniertes Zertifikat verwenden, das Sie mit dem Hilfsprogramm Makecert.exe erstellen können. Für eine vollständige Bereitstellung können Sie jedes gültige SSL-Zertifikat verwenden, das die im Planungshandbuch unter [Voraussetzungen und Support](%20http://go.microsoft.com/fwlink/?LinkId=386485) beschriebenen Voraussetzungen für Zertifikate erfüllt.
    -   Jedem Tresor ist immer nur ein einziges CER-Zertifikat zugeordnet. Bei Bedarf können Sie ein neues Zertifikat hochladen, um ein vorhandenes zu ersetzen.
-   \*\*Zertifikat (PFX-Datei)\*\* – Das CER-Zertifikat muss als PFX-Datei (die den privaten Schlüssel enthält) exportiert werden. Diese Datei wird anschließend auf jeden VMM-Server importiert, der virtuelle Computer enthält, die Sie schützen möchten. Wenn Sie dann während der Bereitstellung den Agent "Hyper-V-Wiederherstellungsmanager-Anbieter" auf jedem VMM-Server installieren, wählen Sie die PFX-Datei aus, um den VMM-Server im Tresor zu registrieren.

### VMM-Voraussetzungen

-   In System Center 2012 SP1 oder System Center 2012 R2 wird mindestens ein VMM-Server ausgeführt.
-   Private VMM-Clouds. Wenn Sie einen VMM-Server ausführen, müssen dafür zwei Clouds konfiguriert sein. Für zwei oder mehr VMM-Server benötigen Sie mindestens eine Cloud auf dem Quell-VMM-Server, den Sie schützen möchten, und eine Cloud auf dem Ziel-VMM-Server, den Sie für die Wiederherstellungen verwenden. Die primäre Cloud, die Sie schützen möchten, muss Folgendes enthalten:
    -   Eine oder mehrere VMM-Hostgruppen
    -   Einen oder mehrere Hyper-V-Hostserver in jeder Hostgruppe.
-   Wenn virtuelle Computer nach einem Failover mit einem VM-Netzwerk verbunden werden sollen, müssen Sie die Netzwerkzuordnung in Hyper-V-Wiederherstellungsmanager konfigurieren. Prüfen Sie Folgendes, bevor Sie mit der Bereitstellung beginnen:
    -   Der virtuelle Computer in der Cloud auf dem Quell-VMM-Server ist mit einem VM-Netzwerk verbunden. Dieses VM-Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.
    -   Die Zielcloud auf dem Ziel-VMM-Server verfügt über ein entsprechendes VM-Netzwerk. Dieses VM-Netzwerk sollte mit einem entsprechenden logischen Netzwerk verbunden sein, das der Zielcloud zugeordnet ist.

Schritte im Lernprogramm
------------------------

Gehen Sie nach der Prüfung der Voraussetzungen folgendermaßen vor:

-   [Abrufen und Konfigurieren von Zertifikaten](#createcert) – Rufen Sie ein CER-Zertifikat ab, exportieren Sie es als PFX-Datei, und importieren Sie die PFX-Datei auf die entsprechenden VMM-Server.
-   [Schritt 1: Erstellen eines Tresors](#vault) – 'Erstellen Sie einen Hyper-V-Wiederherstellungsmanager-Tresor in Azure.
-   [Schritt 2: Hochladen des Zertifikats](#upload) – Laden Sie das Verwaltungszertifikat in den Tresor hoch.
-   [Schritt 3: Download und Installation des Anbieters](#download) – Laden Sie den Hyper-V-Wiederherstellungsmanager-Anbieter herunter, und installieren Sie ihn auf den VMM-Servern, die Sie schützen möchten. Registrieren Sie anschließend die VMM-Server im Tresor.
-   [Schritt 4: Konfigurieren des Cloudschutzes](#clouds) – Konfigurieren Sie die Schutzeinstellungen für VMM-Clouds.
-   [Schritt 5: Konfigurieren der Netzwerkzuordnung](#networks) – Ordnen Sie VM-Netzwerke von Quell-VMM-Servern Ziel-VMM-Servern zu.
-   [Schritt 6: Aktivieren des Schutzes für virtuelle Computer](#virtualmachines) – Aktivieren Sie den Schutz für die virtuellen Hyper-V-Computer, die sich in den VMM-Clouds befinden.
-   [Schritt 7: Konfigurieren und Ausführen von Wiederherstellungsplänen](#recovery%20plans) – Erstellen Sie individuelle Wiederherstellungspläne, mit denen virtuelle Computer für Failover gruppiert werden. Führen Sie den Wiederherstellungsplan anschließend aus.
-   [Schritt 8: Überwachen](#jobs) – Überwachen Sie die Einstellungen, den Status und den Fortschritt auf den Registerkarten \*\*Aufträge\*\* und \*\*Dashboard\*\*.

Abrufen und Konfigurieren von Zertifikaten
------------------------------------------

Gehen Sie folgendermaßen vor, um Zertifikate abzurufen und zu konfigurieren:

1.  [Abrufen eines Zertifikats für die Anleitung](#obtaincert) – Rufen Sie ein Zertifikat mit dem Hilfsprogramm MakeCert ab, oder verwenden Sie ein vorhandenes Zertifikat,das die [Zertifikatanforderungen](http://go.microsoft.com/fwlink/?LinkId=321294) erfüllt.
2.  [Export des Zertifikats im pfx-.Format](#exportcert) – Exportieren Sie auf dem Server, auf dem das Zertifikat abgelegt ist oder erstellt wurde, die CER-Datei als PFX-Datei (mit dem privaten Schlüssel).
3.  [Import des PFX-Zertifikats auf VMM-Server](#importcert) – Importieren Sie die PFX-Datei nach dem Export in den persönlichen Zertifikatsspeicher auf jedem VMM-Server, den Sie im Tresor registrieren möchten.

### Abrufen eines selbstsignierten Zertifikats (.cer)

Verwenden Sie das Hilfsprogramm MakeCert, um ein .cer x.509-Zertifikat zu erstellen, das alle Zertifikatvoraussetzungen erfüllt. Gehen Sie dabei folgendermaßen vor:

1.  Laden Sie auf dem Computer, auf dem Sie MakeCert ausführen möchten, die neueste Version des [Windows SDK](http://go.microsoft.com/fwlink/?LinkId=378269) herunter. Bitte beachten Sie, dass der Befehl makecert.exe Teil des zentralen Windows Software Development Kits ist, sodass Sie nicht das gesamte SDK herunterladen und installieren müssen.
2.  Wählen Sie auf der Seite "Speicherort angeben" die Option \*\*Install the Windows Software Development Kit for Windows 8.1 to this computer\*\* aus.
3.  Wählen Sie auf der Seite "Am Windows-Programm zur Verbesserung der Benutzerfreundlichkeit teilnehmen" die gewünschte Einstellung aus.
4.  Klicken Sie auf der Seite "Lizenzvertrag" auf \*\*Zustimmen\*\*, um den Bedingungen des Lizenzvertrags zuzustimmen.
5.  Deaktivieren Sie auf der Seite "Features zur Installation auswählen" alle Optionen außer \*\*Windows Software Development Kit\*\*.
6.  Prüfen Sie nach der Installation, ob makecert.exe im Ordner C:\\ProgramFiles (x86)\\Windows Kits\\*WindowsVersion*\\bin\\x64 aufgeführt ist.
7.  Öffnen Sie eine Eingabeaufforderung (cmd.exe) mit Administratorrechten, und navigieren Sie zum Ordner mit der Datei makecert.exe.
8.  \>Führen Sie den folgenden Befehl aus, um das selbstsignierte Zertifikat zu erstellen. Ersetzen Sie "CertificateName" durch den gewünschten Namen für das Zertifikat, und geben Sie hinter -e das tatsächliche Ablaufdatum für das Zertifikat an:

Das Zertifikat wird erstellt und im gleichen Ordner gespeichert. Es empfiehlt sich, das Zertifikat an einen benutzerfreundlicheren Speicherort zu verschieben, um es zu exportieren.

### Exportieren des Zertifikats im PFX-Format

Gehen Sie die in diesem Verfahren beschriebenen Schritte aus, um die CER-Datei im PFX-Format zu exportieren.

1.  Geben Sie im Bildschirm Start die Zeichenfolge \*\*mmc.exe\*\* ein, um die Microsoft Management Console (MMC) zu starten.
2.  Klicken Sie im Menü \*\*Datei\*\* auf \*\*Snap-In hinzufügen/entfernen\*\*. Das Dialogfeld \*\*Snap-Ins hinzufügen bzw. entfernen\*\* wird geöffnet.
3.  Klicken Sie unter \*\*Verfügbare Snap-Ins\*\* auf \*\*Zertifikate\*\* und anschließend auf \*\*Hinzufügen\*\*.
4.  Wählen Sie \*\*Computerkonto\*\* aus, und klicken Sie auf \*\*Weiter\*\*.
5.  Wählen Sie \*\*Lokaler Computer\*\* aus, und klicken Sie auf \*\*Fertig stellen\*\*.
6.  Erweitern Sie in der Konsolenstruktur der MMC zunächst \*\*Zertifikate\*\* und anschließend \*\*Persönlich\*\*.
7.  Klicken Sie im Detailbereich auf das Zertifikat, das Sie verwalten möchten.
8.  Zeigen Sie im Menü \*\*Aktion\*\* auf \*\*Alle Tasks\*\*, und klicken Sie anschließend auf \*\*Exportieren\*\*. Der Zertifikatsexport-Assistent wird geöffnet. Klicken Sie auf \*\*Weiter\*\*.
9.  Klicken Sie auf der Seite \*\*Privaten Schlüssel exportieren\*\* auf \*\*Ja\*\*, um den privaten Schlüssel zu entfernen. Klicken Sie auf \*\*Weiter\*\*. Bitte beachten Sie, dass dies nur dann erforderlich ist, wenn Sie den privaten Schlüssel nach der Installation auf andere Server exportieren möchten.
10. Wählen Sie auf der Seite "Format der zu exportierenden Datei" die Option \*\*Privater Informationsaustausch – PKCS \#12 (.PFX)\*\* aus. Klicken Sie auf \*\*Weiter\*\*.
11. Geben Sie auf der Seite \*\*Kennwort\*\* das für die Verschlüsselung des privaten Schlüssels verwendete Kennwort ein, und bestätigen Sie es. Klicken Sie auf \*\*Weiter\*\*.
12. Führen Sie die Schritte auf den übrigen Seiten des Assistenten aus, um das Zertifikat im PFX-Format zu exportieren.

### Importieren des PFX-Zertifikats auf VMM-Server

Nachdem Sie die PFX-Datei exportiert haben, kopieren Sie sie auf die einzelnen VMM-Server, die registriert werden sollen, und importieren Sie sie anschließend wie folgt. Bitte beachten Sie: Wenn Sie MakeCert.exe auf einem VMM-Server ausgeführt haben, müssen Sie das Zertifikat nicht auf den betreffenden Server importieren.

1.  Kopieren Sie die Zertifikatsdateien des privaten Schlüssels (.pfx) an einen Speicherort auf dem lokalen Server.
2.  Wählen Sie im MMC-Snap-in "Zertifikate" die Option \*\*Computerkonto\*\* aus, und klicken Sie auf "Weiter".
3.  Wählen Sie "Lokaler Computer" aus, und klicken Sie auf \*\*Fertig stellen\*\*. Klicken Sie im Dialogfeld "Snap-Ins hinzufügen bzw. entfernen" auf \*\*OK\*\*.
4.  Erweitern Sie in der MMC die Option \*\*Zertifikate\*\*, klicken Sie mit der rechten Maustaste auf \*\*Persönlich\*\*, zeigen Sie auf "Alle Tasks", und klicken Sie anschließend auf \*\*Importieren\*\*, um den Zertifikatimport-Assistenten zu starten.
5.  Klicken Sie auf der Seite "Zertifikatimport-Assistent – Willkommen" auf \*\*Weiter\*\*.
6.  Klicken Sie auf der Seite "Zu importierende Datei" auf \*\*Durchsuchen\*\*, und wechseln Sie zu dem Ordner, in dem die PFX-Zertifikatsdatei mit dem zu importierenden Zertifikat enthalten ist. Wählen Sie die passende Datei aus, und klicken Sie auf \*\*Öffnen\*\*.
7.  Geben Sie auf der Seite "Kennwort" im Feld \*\*Kennwort\*\* das Kennwort für die Datei des privaten Schlüssels ein, das Sie im vorhergehenden Verfahren festgelegt haben, und klicken Sie auf \*\*Weiter\*\*.
8.  Wählen Sie auf der Seite "Zertifikatspeicher" die Option \*\*Alle Zertifikate in folgendem Speicher platzieren\*\* aus, klicken Sie auf \*\*Durchsuchen\*\*, wählen Sie den Speicher \*\*Persönlich\*\* aus, und klicken Sie auf \*\*OK\*\* sowie auf "Weiter".
9.  Klicken Sie auf der Seite "Abschließen des Zertifikatimport-Assistenten" auf \*\*Fertig stellen\*\*.

Nach der Ausführung dieser Schritte können Sie das CER-Zertifikat, das Sie in den Tresor hochladen möchten, sowie das PFX-Zertifikat auswählen, wenn Sie während der Anbieter-Installation einen VMM-Server registrieren.

&lt;h2 data-morhtml="true"\>Schritt 1: Erstellen eines Tresors&lt;/h2\>

1.  Melden Sie sich im [Verwaltungsportal](https://manage.windowsazure.com) an.

2.  Klicken Sie auf **Data Services** und anschließend auf **Recovery Services**.

    ![Vorschauprogramm](./media/hyper-v-recovery-manager-configure-vault/RS_PreviewProgram.png)

3.  Klicken Sie auf **Recovery Services** und anschließend auf **Neu erstellen**, zeigen Sie auf **Hyper-V-Wiederherstellungsmanager-Tresor**, und klicken Sie auf **Schnellerfassung**.

    ![Neuer Tresor](./media/hyper-v-recovery-manager-configure-vault/RS_hvvault.png)

4.  Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5.  Wählen Sie unter **Region** die geografische Region für den Tresor aus. Die verfügbaren geografischen Regionen sind "Asien Pazifik Südost", "Europa Nord" und "USA Ost".*\*\**

6.  Geben Sie unter **Abonnement** Ihre Abonnementinformationen ein.

7.  Klicken Sie auf **Tresor erstellen**.

Prüfen Sie den Tresorstatus in den Mitteilungen unten im Portal. In einer der Mitteilungen wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird auf der Seite "Recovery Services" als \*\*Online\*\* aufgelistet.

&lt;h2 data-morhtml="true"\>Schritt 2: Hochladen eines Zertifikats (.cer)&lt;/h2\>

1.  Öffnen Sie auf der Seite **Recovery Services** den gewünschten Tresor.
2.  Klicken Sie auf das Schnellstart-Symbol, um die Seite "Schnellstart" zu öffnen.

    ![Schnellstart-Symbol](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStartIcon.png)

3.  Klicken Sie auf **Zertifikat verwalten**.

    ![Schnellstart](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStart.png)

4.  Klicken Sie im Dialogfeld **Zertifikat verwalten** auf **Datei suchen**, um nach der CER-Datei zu suchen, die Sie in den Tresor hochladen möchten.

    ![Zertifikat verwalten](./media/hyper-v-recovery-manager-configure-vault/RS_ManageCert.png)

&lt;h2 data-morhtml="true"\>Schritt 3: Download und Installation des Anbieters&lt;/h2\> Installieren Sie den Anbieter für Hyper-V-Wiederherstellungsmanager auf jedem VMM-Server, den Sie im Tresor registrieren möchten. Die neueste Version der Anbieter-Installationsdatei wird im Azure Download Center gespeichert. Wenn Sie die Datei auf einem VMM-Server ausführen, wird der Anbieter installiert und der VMM-Server im Tresor registriert.

1.  Klicken Sie auf der Seite **Schnellstart** auf **Anbieter herunterladen**, um die .exe-Datei zur Anbieterinstallation abzurufen. Führen Sie diese Datei auf dem VMM-Server aus, um mit dem Setup des Anbieters zu beginnen.

    ![Agent herunterladen](./media/hyper-v-recovery-manager-configure-vault/RS_installwiz.png)

2.  Gehen Sie folgendermaßen vor, um die Installation des Anbieters abzuschließen.

    ![Setup abgeschlossen](./media/hyper-v-recovery-manager-configure-vault/RS_SetupComplete.png)

3.  Wenn die Installation des Anbieters abgeschlossen ist, führen Sie die Schritte im Assistenten aus, um den VMM-Server im Tresor zu registrieren.
4.  Geben Sie auf der Seite "Internetverbindung" an, wie der Anbieter auf dem VMM-Sever die Verbindung mit dem Internet herstellen soll. Der Anbieter kann dafür die Standardeinstellungen für die Internetverbindung auf dem Server verwenden oder auf **Proxyserver für Internetanforderungen verwenden** klicken, um benutzerdefinierte Einstellungen zu verwenden.

    ![Interneteinstellungen](./media/hyper-v-recovery-manager-configure-vault/RS_ProviderProxy.png)

    Wenn Sie im Laufe dieser Anleitung benutzerdefinierte Einstellungen verwenden möchten, lesen Sie die Informationen in [Schritt 2: Installation des Anbieters und Registrierung der VMM-Server](http://go.microsoft.com/fwlink/?LinkId=378266) des Bereitstellungshandbuchs.

5.  Wählen Sie auf der Seite Zertifikatregistrierung die PFX-Datei aus, die der CER-Datei entspricht, die Sie in den Tresor hochgeladen haben.

    ![Zertifikatregistrierung](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg1.png)

    ![Zertifikattresor](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg2.png)

6.  Geben Sie auf der Seite "VMM-Server" einen Anzeigenamen für den VMM-Server an. Diese Name wird verwendet, um den Server in der Hyper-V-Wiederherstellungsmanager-Konsole zu identifizieren.
7.  Wählen Sie **Cloud-Daten mit Tresor synchronisieren** aus, um die Daten zu synchronisieren, die in den privaten Clouds enthalten sind, die auf dem VMM-Server mit dem Hyper-V-Wiederherstellungsmanager-Tresor konfiguriert sind. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Wenn Sie nicht alle Clouds synchronisieren möchten, können Sie jede Cloud einzeln zur Synchronisierung veröffentlichen, bevor Sie die Schutzeinstellungen für die Clouds konfigurieren.
8.  Klicken Sie auf **Registrieren**, um den Prozess abzuschließen.

    ![Interneteinstellungen](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloudSetup.png)

In dieser Phase ruft Hyper-V-Wiederherstellungsmanager die Metadaten vom VMM-Server ab, um Failover und Wiederherstellung zu steuern. Nachdem ein Server erfolgreich registriert wurde, wird sein Anzeigename auf der Registerkarte auf der Registerkarte \*\*Ressourcen\*\* der Seite "Server" im Tresor angezeigt.

Schritt 4: Konfigurieren der Cloudschutzeinstellungen
-----------------------------------------------------

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Wenn Sie nach der Installation des Anbieters auf dem VMM-Server die Option **Clouddaten mit Tresor synchronisieren** aktivieren, müssen Sie die Cloud im Hyper-V-Wiederherstellungsmanager auf der VMM-Konsole veröffentlichen. Nach der Erkennung der Clouds durch Hyper-V-Wiederherstellungsmanager können Sie die Schutzeinstellungen konfigurieren.

### Veröffentlichen einer Cloud

1.  Öffnen Sie in der VMM-Konsole den Arbeitsbereich **VMs und Dienste**.
2.  Öffnen Sie im Bereich **VMs und Dienste** die Cloud, die Sie veröffentlichen möchten.
3.  Aktivieren Sie zum Veröffentlichen der Cloud auf der Seite **Allgemein** für die Cloudeigenschaften die Option **Konfigurationsdaten für diese Cloud an den Windows Azure Hyper-V-Wiederherstellungs-Manager senden**. Nach der Veröffentlichung der Cloud wird diese im Tresor angezeigt.

    ![Clouds](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloud.png)

    ![Veröffentlichte Cloud](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

### Konfigurieren von Clouds

Gehen Sie folgendermaßen vor, um den Cloudschutz zu konfigurieren:

1.  Klicken Sie auf der Seite "Schnellstart" auf **Schutzeinstellungen konfigurieren**.
2.  Wählen Sie auf der Registerkarte **Geschützte Elemente** die Cloud aus, die Sie konfigurieren möchten, und gehen Sie zur Registerkarte **Konfiguration**.

    ![Cloudkonfiguration](./media/hyper-v-recovery-manager-configure-vault/RS_CloudConfig.png)

3.  Geben Sie unter **Zielspeicherort** den VMM-Server an, der die für die Wiederherstellung verwendete Cloud verwaltet.
4.  Geben Sie unter **Zielcloud** die Zielcloud an, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten.
5.  Lassen Sie unter **Kopierhäufigkeit** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen. Dies ist nur relevant, wenn der Hyper-V-Host Windows Server 2012 R2 ausführt. Für andere Server wird die Standardeinstellung "5 Minuten" verwendet.
6.  Lassen Sie unter **Zusätzliche Wiederherstellungspunkte** die Standardeinstellung stehen. Dieser Wert gibt an, ob zusätzliche Wiederherstellungspunkte erstellt werden sollen. Wenn die Standardeinstellung 0 (Null) aktiviert ist, wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
7.  Lassen Sie unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind. Wenn Sie diesen Wert für die Lernprrogramm-Anleitung festlegen möchten, stellen Sie sicher, dass er auf eine kleinere Zahl gesetzt ist als die zusätzlichen Wiederherstellungspunkte, die Sie konfigurieren.
8.  Geben Sie unter **Datenübertragungskomprimierung** an, ob replizierte Daten, die übertragen werden, komprimiert werden sollen.
9.  Geben Sie unter **Authentifizierung** an, wie der Datenverkehr zwischen dem primären und dem Hyper V-Wiederherstellungs-Hostserver authentifiziert wird. Wenn Sie nicht über eine funktionierende Kerberos-Umgebung verfügen, wird empfohlen, für die Zwecke dieser Anleitung HTTPS zu verwenden. Wenn HTTPS ausgewählt ist, authentifizieren die Hostserver einander über ein Serverzertifikat, und der Datenverkehr wird verschlüsselt. Hyper-V-Wiederherstellungsmanager konfiguriert Zertifikate, die automatisch für die HTTPS-Authentifizierung verwendet werden. Es ist keine manuelle Konfiguration notwendig. Bitte beachten Sie, dass diese Einstellung nur für Hyper-V-Hostserver relevant ist, die unter Windows Server 2012 R2 laufen.
10. Lassen Sie unter **Port** die Standardeinstellung stehen. Dieser Wert gibt die Nummer des Ports an, auf dem die Quell- und der Ziel-Hyper-V-Hostcomputer auf Replikationsdatenverkehr lauschen.
11. Geben Sie unter **Initial Replication Settings** an, wie die erste Replikation von Daten vom Quell- zum Zielspeicherort erfolgt, bevor die reguläre Replikation beginnt.
    -   **Über das Netzwerk** – Das Kopieren von Daten über das Netzwerk kann zeitraubend und ressourcenintensiv sein. Es wird empfohlen, diese Option zu verwenden, wenn die Cloud virtuelle Computer mit relativ kleinen virtuellen Festplatten enthält und der primäre VMM-Server über eine große Bandbreite mit dem sekundären VMM-Server verbunden ist. Sie können angeben, dass der Kopiervorgang sofort gestartet werden soll, oder eine Uhrzeit auswählen. Wenn Sie Netzwerkreplikation verwenden, empfehlen wir, deren Ausführung außerhalb der Spitzenzeiten einzuplanen.
    -   **Offline** – Mit dieser Methode wird die erste Replikation mithilfe externer Medien durchgeführt. Dies ist sinnvoll, wenn Sie einen Abfall der Netzwerkleistung vermeiden möchten oder wenn Sie mit geografisch fernen Orten arbeiten. Wenn Sie diese Methode anwenden möchten, müssen Sie den Exportspeicherort für die Quellcloud und den Importspeicherort angeben. Wenn sie den Schutz für einen virtuellen Computer aktivieren, wird die virtuelle Festplatte an den angegebenen Exportspeicherort kopiert. Sie senden sie an den Zielstandort und kopieren sie an den Importspeicherort. Das System kopiert die importierten Informationen auf die virtuellen Replikatcomputer. Eine vollständige Liste der Voraussetzungen für die Offline-Replikation finden sie unter [Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds](http://go.microsoft.com/fwlink/?LinkId=323469) im Bereitstellungshandbuch.

#### Einstellungen nach der Schutzkonfiguration

Nach der Konfiguration einer Cloud werden alle Cluster und Hostserver, die in der Quell- und der Zielcloud konfiguriert werden, für die Replikation konfiguriert. Insbesondere wird Folgendes konfiguriert:

-   Firewall-Regeln zur Anwendung durch Hyper-V Replica, sodass Ports für den Replikationsverkehr geöffnet werden.
-   Zertifikate, die für die Replikation erforderlich sind, werden installiert.
-   Die Einstellungen von Hyper-V Replica werden konfiguriert.

Die Cloudeinstellungen können auf der Registerkarte **Konfigurieren** bearbeitet werden. Beachten Sie Folgendes:

-   Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
-   Eine Cloud kann nur zu einem einzigen Cloudpaar gehören – entweder als primäre oder als Zielcloud.
-   Nach dem Speichern der Cloudkonfiguration wird ein Auftrag erstellt und kann auf der Registerkarte **Aufträge** überwacht werden. Wenn Sie nach dem Speichern der Konfiguration den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und die Cloud anschließend neu konfigurieren.

Schritt 5: Konfigurieren der Netzwerkzuordnung
----------------------------------------------

Auf der Registerkarte **Netzwerke** konfigurieren sie die Zuordnung zwischen den VM-Netzwerken auf dem Quell- und dem Ziel-VMM-Server. Die Netzwerkzuordnung stellt sicher, dass virtuelle Replikatcomputer nach dem Failover mit passenden Netzwerken verbunden werden und dass die virtuellen Replikatcomputer auf Hosts platziert werden, die auf die VM-Netzwerke zugreifen können. Prüfen Sie die VMM-Netzwerk-Voraussetzungen im Planungshandbuch unter [Voraussetzungen und Support](%20http://go.microsoft.com/fwlink/?LinkId=386485). Es wird empfohlen, die Netzwerke wie folgt zuzuordnen:

-   Ordnen Sie die Netzwerke für jede Cloud zu, die für den Schutz konfiguriert wird.
-   Führen Sie die Netzwerkzuordnung durch, bevor Sie die virtuellen Computer für den Schutz aktivieren, da die Zuordnung während der Platzierung der virtuellen Replikatcomputer verwendet wird. Die Zuordnung funktioniert korrekt, wenn Sie sie nach der Aktivierung des Schutzes konfigurieren, aber eventuell müssen Sie einige virtuelle Replikatcomputer migrieren.

Gehen Sie folgendermaßen vor, um Netzwerke zuzuordnen:

1.  Klicken Sie auf der Seite "Schnellstart" auf **Netzwerkzuordnung konfigurieren**.
2.  Wählen Sie unter **Quellspeicherort** den Quell-VMM-Server aus.
3.  Wählen Sie unter **Zielspeicherort** den Ziel-VMM-Server aus. Wenn Sie Hyper-V-Wiederherstellungsmanager mit einem einzelnen VMM-Server bereitstellen, sind die Quelle und das Ziel identisch. Die Liste der Quell-VM-Netzwerke und der ihnen zugeordneten Ziel-VM-Netzwerke wird angezeigt. Für Netzwerke, die nicht zugeordnet sind, wird ein leerer Wert angezeigt.
4.  Wählen Sie in der Liste der Quell- und Zielnetzwerke einen nicht zugeordneten Eintrag aus, und klicken Sie auf **Zuordnen**. Der Dienst erkennt die VM-Netzwerke auf dem Zielserver und zeigt sie an.

    ![Zertifikat verwalten](./media/hyper-v-recovery-manager-configure-vault/RS_networks.png)

5.  Wählen Sie auf der Seite "Zielnetzwerk" das Ziel-VM-Netzwerk aus, das Sie auf dem Ziel-VMM-Server verwenden möchten. ![Zielnetzwerk](./media/hyper-v-recovery-manager-configure-vault/RS_TargetNetwork.png)

6.  Klicken Sie auf die Informationssymbole neben den Namen des Quell- und des Zielnetzwerks, um die Subnetze für jedes Netzwerk und den jeweiligen Netzwerktyp anzuzeigen.

    Wenn Sie ein Zielnetzwerk auswählen, werden die geschützten Clouds angezeigt, die das Quellnetzwerk verwenden, sowie die Verfügbarkeit der den Clouds zugeordneten Ziel-VM-Netzwerke angezeigt. Es wird empfohlen, ein Zielnetzwerk auszuwählen, dass für alle für den Schutz verwendeten Clouds verfügbar ist.

7.  Aktivieren Sie das Kontrollkästchen, um den Zuordnungsprozess abzuschließen. Ein Auftrag beginnt mit der Verfolgung des Zuordnungsprozesses. Dieser Auftrag kann auf der Registerkarte **Aufträge** angezeigt werden.

    Hierbei werden alle vorhandenen virtuellen Replikatcomputer, die den mit dem Quell-VM-Netzwerk verbundenen virtuellen Computern entsprechen, mit dem Ziel-VM-Netzwerk verbunden. Außerdem werden die neuen virtuellen Replikatcomputer, die nach der Aktivierung der Replikation auf den mit dem Quell-VM-Netzwerk verbundenen virtuellen Computern erstellt werden, mit dem Ziel-WM-Netzwerk verbunden.

### Ändern von Netzwerkzuordnungen

Netzwerkzuordnungen können auf der Registerkarte **Netzwerke** geändert oder entfernt werden. Beachten Sie Folgendes:

-   Wenn Sie eine ausgewählte Zuordnung aufheben, wird die Zuordnung entfernt, und die virtuellen Replikatcomputer werden von dem Netzwerk getrennt, mit dem sie zum Zeitpunkt der Zuordnung verbunden waren.
-   Wenn Sie die ausgewählte Zuordnung ändern, werden die Änderungen aktualisiert, und die virtuellen Replikatcomputer werden mit den angegebenen neuen Netzwerkeinstellungen verbunden.

Schritt 6: Aktivieren des Schutzes für virtuelle Computer
---------------------------------------------------------

Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren. Aktivieren Sie den Schutz in der VMM-Konsole durch Rechtsklick auf jeden virtuellen Computer, den sie schützen möchten, und durch Auswahl von \*\*Wiederherstellung aktivieren\*\*.

Nach der Aktivierung des Schutzes werden die virtuellen Computer in der Liste der virtuellen Computer der Cloud angezeigt. Sie können den Fortschritt der Schutzaktivierung auf der Registerkarte \*\*Aufträge\*\* verfolgen.

![Virtuelle Computer](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

Wenn Sie den Schutz virtueller Computer aktivieren, werden drei Aufträge erstellt. Der Auftrag "Schutz aktivieren" wird ausgeführt. Nach der ersten Replikation werden zwei weitere Aufträge zum Abschließen des Schutzes ausgeführt. Der virtuelle Computer ist erst dann für ein Failover bereit, wenn diese Aufträge erfolgreich abgeschlossen sind.

Schritt 7: Konfigurieren und Ausführen von Wiederherstellungsplänen
-------------------------------------------------------------------

Ein Wiederherstellungsplan fasst virtuelle Computer in Gruppen zusammen, sodass sie als einzelne Einheit verarbeitet werden können, und gibt die Reihenfolge an, in der das Failover dieser Gruppen durchgeführt wird. Zusätzlich zur Definition von Gruppen virtueller Computer können Sie die Wiederherstellungspläne so anpassen, dass sie automatische Skripts ausführen oder auf die Bestätigung manuell durchgeführter Aktionen warten. Gehen Sie folgendermaßen vor, um einen Wiederherstellungsplan zu erstellen:

1.  Klicken Sie auf der Seite "Schnellstart" auf **Wiederherstellungsplan erstellen**.
2.  Geben Sie auf der Seite "Specify the Recovery Page Name and Target" einen Namen sowie den Quell- und den Ziel-VMM-Server an. Wenn Sie Hyper-V-Wiederherstellungsmanager auf einem einzelnen VMM-Server bereitstellen, sind die Quelle und das Ziel identisch. Der Quell-VMM-Server muss virtuelle Computer mit aktiviertem Schutz enthalten.

    ![Wiederherstellungsplan erstellen](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan1.png)

3.  Wahlen Sie auf der Seite "Virtuelle Computer auswählen" die virtuellen Computer aus, die zum Wiederherstellungsplan hinzugefügt werden sollen. Diese werden zur Standardgruppe des Wiederherstellungsplans (Gruppe 1) hinzugefügt.
4.  Aktivieren Sie das Kontrollkästchen, um den Wiederherstellungsplan zu erstellen. Die erstellten Wiederherstellungspläne können auf der Registerkarte **Wiederherstellungspläne** gelöscht werden.

    ![Wiederherstellungspläne für virtuelle Computer](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan2.png)

Nach der Erstellung eines Wiederherstellungsplans können Sie die folgenden Aktionen durchführen:

-   Passen Sie den Plan an Ihre Wünsche an. Sie können neue Gruppen zu einem Wiederherstellungsplan oder virtuelle Computer zu einer Gruppe hinzufügen. Sie können benutzerdefinierte Aktionen in Form von Skripts und manuellen Aktionen festlegen, die vor und nach bestimmten Gruppen durchgeführt werden. Gruppennamen sind inkrementell. Nach dem Gruppe 1 des Standardwiederherstellungsplans erstellen Sie Gruppe 2, dann Gruppe 3 usw. Das Failover der virtuellen Computer wird entsprechend der Gruppenreihenfolge durchgeführt. Weitere Informationen zur Anpassung von Wiederherstellungsplänen finden Sie unter [Schritt 6: Erstellen und Anpassen von Wiederherstellungsplänen](http://go.microsoft.com/fwlink/?LinkId=378271) im Bereitstellungshandbuch.
-   Führen Sie ein Test-Failover durch.

### Ausführen von Wiederherstellungsplänen

Wiederherstellungspläne können als Teil eines proaktiven Tests oder eines geplanten Failovers und während eines ungeplanten Failovers ausgeführt werden. Diese Anleitung beschreibt die Ausführung eines Test-Failovers. Informationen über andere Failover-Arten finden Sie im [Betriebs- und Überwachungshandbuch](hhttp://go.microsoft.com/fwlink/?LinkId=378272).

### Ausführung des Plans.

Führen Sie ein Test-Failover durch, um Ihren Wiederherstellungsplan in einer Testumgebung zu prüfen. Ein Test-Failover ist sinnvoll, um zu überprüfen, ob Ihr Wiederherstellungsplan und die Failover-Strategie für Ihre virtuellen Computer wie erwartet funktionieren. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk.

##### Vorbereitung

Wenn ein Test-Failover ausgelöst wird, werden Sie aufgefordert, festzulegen, wie die virtuellen Testcomputer nach dem Failover mit Netzwerken verbunden werden sollen.

-   Wenn Sie ein vorhandenes Netzwerk verwenden möchten, empfehlen wir, zu diesem Zweck ein separates logisches Netzwerk zu erstellen, das nicht in der Produktion verwendet wird.
-   Wenn Sie die Option auswählen, automatisch ein Test-VM-Netzwerk zu erstellen, werden die temporären Netzwerke und virtuellen Test-Computer nach dem Abschluss des Test-Failovers automatisch bereinigt.
-   Wenn Sie ein logisches Netzwerk auf Basis eines virtuellen LAN (VLAN) verwenden, stellen Sie sicher, dass die Netzwerkstandorte, die Sie hinzufügen, isoliert sind.
-   Wenn Sie ein logisches Netzwerk auf Basis einer Windows-Netzwerkvirtualisierung verwenden, erstellt Hyper-V-Wiederherstellungsmanager automatisch isolierte VM-Netzwerke.

##### Durchführung eines Test-Failovers

Gehen Sie folgendermaßen vor, um ein Test-Failover für einen Wiederherstellungsplan durchzuführen:

Wählen Sie den gewünschten Wiederherstellungsplan auf der Registerkarte \*\*Wiederherstellungspläne\*\* aus.

Klicken Sie auf die Schaltfläche \*\*Test-Failover\*\*, um das Failover zu initiieren.

Geben Sie auf der Seite "Test-Failover bestätigen" an, wie die virtuellen Computer nach dem Test-Failover mit Netzwerken verbunden werden sollen. Gehen Sie dazu folgendermaßen vor:

-   \*\*Keine\*\* – Wählen Sie diese Einstellung aus, um festzulegen, dass für das Test-Failover keine VM-Netzwerke verwendet werden sollen. Verwenden Sie diese Option, wenn Sie statt Ihrer gesamten Netzwerkkonfiguration nur einzelne virtuelle Computer testen möchten. Außerdem erhalten Sie dadurch einen schnellen Eindruck, wie das Test-Failover funktioniert. Virtuelle Test-Computer werden nach einem Failover nicht mit Netzwerken verbunden.
-   \*\*Vorhandene verwenden\*\* – Verwenden Sie diese Option, wenn Sie bereits ein VM-Netzwerk erstellt und isoliert haben, das für ein Test-Failover verwendet werden soll. Nach dem Failover werden alle in dem Test-Failover verwendeten virtuellen Test-Computer mit dem in \*\*VM-Netzwerk\*\* angegebenen Netzwerk verbunden.
-   \*\*Automatisch erstellen\*\* – Wählen Sie diese Einstellung aus, um festzulegen, dass Hyper-V-Wiederherstellungsmanager automatisch ein VM-Netzwerk auf Basis der Einstellung, die Sie in "Logisches Netzwerk" angegeben haben, sowie der entsprechenden Netzwerkstandorte erstellen soll. Verwenden Sie diese Option, wenn für den Wiederherstellungsplan mehr als ein VM-Netzwerk verwendet wird. Im Falle von Netzwerken auf Basis von Windows-Netzwerkvirtualisierungen kann diese Option verwendet werden, um automatisch VM-Netzwerke mit den gleichen Einstellungen (Subnetze und Adresspools) zu erstellen wie im Netzwerk des virtuellen Replikatcomputers. Diese VM-Netzwerke werden nach dem Abschluss des Test-Failovers automatisch bereinigt.

Gehen Sie nach dem Test-Failover folgendermaßen vor:

1.  Prüfen Sie, ob die virtuellen Computer erfolgreich starten.
2.  Schließen Sie nach der Prüfung, ob die virtuellen Computer erfolgreich starten, das Test-Failover ab, um die isolierte Umgebung zu bereinigen. Wenn Sie ausgewählt haben, VM-Netzwerke automatisch zu erstellen, werden durch die Bereinigung alle virtuellen Test-Computer und Test-Netzwerke gelöscht.
3.  Klicken Sie auf \*\*Notizen\*\*, um alle Beobachtungen im Zusammenhang mit dem Test-Failover aufzuzeichnen und zu speichern.
4.  Wenn ein Test-Failover für einen Wiederherstellungsplan initiiert wird, wird der Failover-Prozess auf der Seite "Details" des Wiederherstellungsplans angezeigt. Hier können Sie die einzelnen Test-Failover-Schritte und ihren Status sehen sowie Notizen zum Test-Failover anzeigen oder erstellen. Zusätzliche Details zu Failover-Aufträgen finden Sie auf der Registerkarte \*\*Aufträge\*\*. Hier werden jeder mit dem Failover zusammenhängende Auftrag sowie sein Status, seine Startzeit und seine Dauer angezeigt. Sie können jeden Auftrag in der Failover-Liste in ein Excel-Tabellenblatt exportieren.

Beachten Sie Folgendes:

-   Auf der Seite "Test-Failover bestätigen" werden Details zum VMM-Server angezeigt, auf dem die virtuellen Test-Computer erstellt werden.
-   Sie können den Fortschritt der Test-Failover-Aufträge auf der Registerkarte **Aufträge** anzeigen.

Schritt 8: Überwachung
----------------------

### Verwendung der Registerkarte "Aufträge" und des Dashboards

#### Aufträge

Auf der Registerkarte **Aufträge** werden die wichtigsten Tasks aufgeführt, die vom Hyper-V-Wiederherstellungsmanager-Tresor ausgeführt werden. Dazu zählen die Konfiguration des Schutzes für eine Cloud, die Aktivierung und Deaktivierung des Schutzes für einen virtuellen Computer, die Durchführung eines Failovers (geplant, ungeplant oder zu Testzwecken), das Ausführen eines Commits für ein ungeplantes Failover und die Konfiguration einer umgekehrten Replikation.

Auf der Registerkarte **Aufträge** können Sie die folgenden Tasks ausführen:

-   **Eine Auftragsabfrage ausführen** – Sie können eine Abfrage ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen. Sie können die Aufträge anhand der folgenden Parameter filtern:

-   Aufträge finden, die auf einem bestimmen VMM-Server ausgeführt wurden.
-   Aufträge finden, die in einer Cloud, auf einem virtuellen Computer, in einem Netzwerk und/oder im Rahmen eines Wiederherstellungsplans durchgeführt wurden.
-   Aufträge identifizieren, die an einem bestimmten Datum und innerhalb eines bestimmten Zeitraums ausgeführt wurden.

Bitte beachten Sie, dass nach einer Abfrage bis zu 200 Aufträge zurückgegeben werden können. Daher wird empfohlen, die Abfrageparameter einzugrenzen, sodass weniger Aufträge zurückgegeben werden als dieses Maximum.

-   **Auftragsdetails abrufen** – Sie können auf einen Auftrag in der Liste **Aufträge** klicken, um weitere Details anzuzeigen. Zu diesen Details gehören der Name des Auftrags, sein Status, der Startzeitpunkt sowie seine Dauer. Auf der Registerkarte mit den Auftragsdetails können Sie die Auftragsdetails in ein Excel-Tabellenblatt exportieren oder versuchen, einen fehlgeschlagenen, übersprungenen oder unterbrochenen Auftrag neu zu starten.
-   **Aufträge exportieren** – Sie können die Ergebnisse einer Auftragsabfrage in ein Excel-Tabellenblatt exportieren.
-   **Neu starten** – Sie können fehlgeschlagene Aufträge neu starten und auf diese Weise versuchen, sie erneut auszuführen.
-   **Fehlerdetails** – Wenn ein Auftrag fehlgeschlagen ist, können Sie auf **Fehlerdetails** klicken, um eine Fehlerliste für den Auftrag abzurufen. Für jeden Fehler werden mögliche Gründe und Empfehlungen angezeigt. Die Fehlerbeschreibung kann zur Problembehandlung in die Zwischenablage kopiert werden.

> Die Registerkarte **Aufträge** enthält die folgenden Informationen:

-   \*\*Name\*\* – Der Auftragsname
-   \*\*Element\*\* – Der Name der Cloud, des Wiederherstellungsplans, des VMM-Servers oder des virtuellen Computers, auf dem der Auftrag ausgeführt wurde.
-   \*\*Status\*\* – Der Auftragsstatus: Abgeschlossen, Abgebrochen, Fehlgeschlagen oder Sonstiges
-   \*\*Typ\*\* – Der Auftragstyp
-   \*\*Gestartet am\*\* – Die Startzeit des Auftrags
-   \*\*Task-Zeit\*\* – Die Dauer des Auftrags

![Registerkarte "Aufträge"](./media/hyper-v-recovery-manager-configure-vault/RS_Jobs.png)

#### Ausführung einer Auftragsabfrage

Sie können Abfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, z. B.:

-   Aufträge, die auf einem bestimmen VMM-Server ausgeführt wurden.
-   Aufträge, die in einer bestimmten Cloud, auf einem bestimmten virtuellen Computer, in einem bestimmten Netzwerk und/oder im Rahmen eines bestimmten Wiederherstellungsplans ausgeführt wurden.
-   Aufträge, die innerhalb eines bestimmten Zeitrahmens ausgeführt wurden.

Gehen Sie folgendermaßen vor, um eine Auftragsabfrage durchzuführen:

1.  Öffnen Sie die Registerkarte \*\*Aufträge\*\*
2.  Geben Sie unter \*\*Server\*\* den VMM-Server an, auf dem der Auftrag ausgeführt wurde
3.  Geben Sie unter \*\*Typ\*\* an, ob der Auftrag in einer Cloud, auf einem virtuellen Computer, in einem Netzwerk oder im Rahmen eines Wiederherstellungsplans in einer Cloud ausgeführt wurde.
4.  Geben Sie unter \*\*Status\*\* den Auftragsstatus an.
5.  Geben Sie unter \*\*Dauer\*\* den Zeitraum an, innerhalb dessen der Auftrag ausgeführt wurde.

#### Dashboard

Auf der Registerkarte \*\*Dashboard\*\* erhalten Sie einen schnellen Überblick über die Verwendung von Hyper-V-Wiederherstellungsmanager in Ihrer Organisation. Sie bietet ein zentralisiertes Gateway zur Anzeige der virtuellen Computer, die in Hyper-V-Wiederherstellungsmanager-Tresoren geschützt werden. Das Dashboard bietet die folgenden Funktionen:

Auf der Registerkarte **Dashboard** können Sie die folgenden Tasks ausführen:

-   **Anbieter herunterladen** – Laden Sie den Hyper-V-Wiederherstellungsmanager-Anbieter herunter, um ihn auf dem VMM-Server zu installieren.
-   **Zertifikat verwalten** – Ändern Sie die Einstellungen für das dem Tresor zugeordnete Zertifikat.
-   **Löschen –** Löschen Sie einen Tresor.
-   **Virtuelle Computer erneut synchronisieren** – Wenn Hyper-V-Wiederherstellungsmanager erkennt, dass primäre oder wiederhergestellte virtuelle Computer nicht wie erwartet synchronisiert wurden, können Sie eine Liste der relevanten virtuellen Computer anzeigen und auf dem Dashboard jeden virtuellen Computer für den Versuch einer erneuten Synchronisierung auswählen.

Die Registerkarte **Dashboard** enthält die folgenden Informationen:

-   \*\*Schnelleinsicht\*\* – Zeigt wichtige Konfigurationsinformationen für Wiederherstellungsdienste und den Hyper-V-Wiederherstellungsmanager-Tresor an. Hier erfahren Sie, ob der Tresor online ist, welches Zertifikat ihm zugeordnet ist und wann das Zertifikat abläuft. Außerdem erhalten Sie Details zum Abonnement des Dienstes.
-   \*\*Letzte Aufträge\*\* – Zeigt Aufträge an, die in den letzten 24 Stunden erfolgreich ausgeführt wurden, fehlgeschlagen sind, noch laufen oder auf eine Aktion warten.
-   \*\*Probleme\*\* – Das Dashboard zeigt Informationen über Probleme mit VMM-Server-Bindungen, mit Cloudkonfigurationseinstellungen oder mit der Synchronisierung der Replikation virtueller Computer an. Hier können Sie weitere Details zu einem Problem erhalten, Aufträge im Zusammenhang mit dem Problem anzeigen oder versuchen, virtuelle Computer erneut zu synchronisieren.

![Dashboard](./media/hyper-v-recovery-manager-configure-vault/RS_Dashboard.png)

Nächste Schritte
----------------

-   Informationen zur Planung und Bereitstellung von Hyper-V-Wiederherstellungsmanager in einer wirklichen Produktionsumgebung finden sie im[Planungshandbuch für Hyper-V-Wiederherstellungsmanager](http://go.microsoft.com/fwlink/?LinkId=321294) und im [Bereitstellungshandbuch für Hyper-V-Wiederherstellungsmanager](http://go.microsoft.com/fwlink/?LinkId=321295).
-   Wenn Sie Fragen haben, besuchen Sie das [Azure Recovery Services-Forum](http://go.microsoft.com/fwlink/?LinkId=313628)

