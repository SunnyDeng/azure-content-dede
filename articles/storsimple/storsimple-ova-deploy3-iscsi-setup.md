<properties 
   pageTitle="StorSimple Virtual Array-iSCSI-Server – Setup | Microsoft Azure"
   description="Es wird beschrieben, wie Sie die anfängliche Einrichtung durchführen, den StorSimple-iSCSI-Server registrieren und die Geräteinstallation durchführen."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="02/08/2016"
   ms.author="alkohli" />


# Bereitstellen von StorSimple Virtual Array – Einrichten des virtuellen Geräts als iSCSI-Server (Vorschau)

![Prozessablauf für iSCSI-Setup](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## Übersicht

Dieses Tutorial zur Bereitstellung bezieht sich nur auf Microsoft Azure StorSimple Virtual Arrays (auch als „lokale virtuelle StorSimple-Geräte“ oder „virtuelle StorSimple-Geräte“ bezeichnet) mit der öffentlichen Vorschauversion 1.1.1.0. In diesem Tutorial wird beschrieben, wie Sie die anfängliche Einrichtung durchführen, den StorSimple-iSCSI-Server registrieren, die Geräteinstallation durchführen und dann Volumes auf dem virtuellen StorSimple-iSCSI-Server erstellen, bereitstellen, initialisieren und formatieren. Die StorSimple-Setupinformationen in diesem Artikel gelten nur für StorSimple Virtual Arrays.

Die hier beschriebenen Verfahren dauern zwischen 30 Minuten und einer Stunde. Die in diesem Artikel veröffentlichten Informationen gelten nur für StorSimple Virtual Arrays.

>[AZURE.IMPORTANT] 
>
>- StorSimple Virtual Array befindet sich in der Vorschauphase und ist für die Evaluierung und Bereitstellungsplanung vorgesehen. Die Installation dieser Vorschauversion in einer Produktionsumgebung wird nicht unterstützt. 
>- Wenn Probleme mit StorSimple Virtual Array auftreten, posten Sie sie im [StorSimple-MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

## Voraussetzungen für das Setup

Überprüfen Sie Folgendes, bevor Sie Ihr virtuelles StorSimple-Gerät konfigurieren und einrichten:

- Sie haben ein virtuelles Gerät bereitgestellt und die Verbindung dafür hergestellt, wie unter [Bereitstellen des StorSimple Virtual Array – Bereitstellen eines virtuellen Arrays in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) oder [Bereitstellen des StorSimple Virtual Array – Bereitstellen eines virtuellen Arrays in VMware](storsimple-ova-deploy2-provision-vmware.md) beschrieben.

- Sie verfügen über den Dienstregistrierungsschlüssel aus dem StorSimple Manager-Dienst, den Sie zum Verwalten von virtuellen StorSimple-Geräten erstellt haben. Weitere Informationen finden Sie unter **Schritt 2: Abrufen des Dienstregistrierungsschlüssels** in [Bereitstellen des StorSimple Virtual Array – Vorbereiten des Portals](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Falls dies das zweite oder ein nachfolgendes virtuelles Gerät ist, das Sie für einen vorhandenen StorSimple Manager-Dienst registrieren, sollten Sie den Dienstdaten-Verschlüsselungsschlüssel bereithalten. Dieser Schlüssel wurde generiert, als das erste Gerät erfolgreich für den Dienst registriert wurde. Wenn Sie diesen Schlüssel verloren haben, helfen Ihnen die Informationen unter **Abrufen des Dienstdaten-Verschlüsselungsschlüssels** in [Verwenden der Webbenutzeroberfläche zum Verwalten des StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) weiter.

## Schritt-für-Schritt-Einrichtung 

Verwenden Sie die folgende Schritt-für-Schritt-Anleitung, um Ihr virtuelles StorSimple-Gerät einzurichten und zu konfigurieren:

-  [Schritt 1: Durchführen der Einrichtung für die lokale Webbenutzeroberfläche und Registrieren Ihres Geräts](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Schritt 2: Durchführen der erforderlichen Geräteinstallation](#step-2-complete-the-required-device-setup)
-  [Schritt 3: Hinzufügen eines Volumes](#step-3-add-a-volume)
-  [Schritt 4: Bereitstellen, Initialisieren und Formatieren eines Volumes](#step-4-mount-initialize-and-format-a-volume)  

## Schritt 1: Durchführen der Einrichtung für die lokale Webbenutzeroberfläche und Registrieren Ihres Geräts 

#### So führen Sie das Setup durch und registrieren das Gerät

1. Öffnen Sie ein Browserfenster, und stellen Sie eine Verbindung mit der Webbenutzeroberfläche her, indem Sie Folgendes eingeben:

    `https://<ip-address of network interface>`

    Verwenden Sie die Verbindungs-URL aus dem vorherigen Schritt. Es wird eine Fehlermeldung mit dem Hinweis angezeigt, dass ein Problem mit dem Sicherheitszertifikat der Website aufgetreten ist. Klicken Sie auf **Mit dieser Webseite fortfahren**.

    ![Sicherheitszertifikatfehler](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Melden Sie sich an der Webbenutzeroberfläche des virtuellen Geräts als **StorSimpleAdmin** an. Geben Sie das Geräteadministratorkennwort ein, das Sie unter „Schritt 3: Starten des virtuellen Geräts“ im Abschnitt [Bereitstellen des StorSimple Virtual Array – Bereitstellen eines virtuellen Geräts in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) bzw. [Bereitstellen des StorSimple Virtual Array – Bereitstellen eines virtuellen Geräts in VMware](storsimple-ova-deploy2-provision-vmware.md) geändert haben.

    ![Anmeldeseite](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Sie gelangen auf die **Startseite**. Auf dieser Seite werden die verschiedenen Einstellungen beschrieben, die zum Konfigurieren und Registrieren des virtuellen Geräts beim StorSimple Manager-Dienst erforderlich sind. Beachten Sie, dass die **Netzwerkeinstellungen**, **Webproxyeinstellungen** und **Uhrzeiteinstellungen** optional sind. Die einzigen erforderlichen Einstellungen sind **Geräteeinstellungen** und **Cloudeinstellungen**.

    ![Startseite](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Auf der Seite **Netzwerkeinstellungen** unter **Netzwerkschnittstellen** wird DATA 0 automatisch für Sie konfiguriert. Jede Netzwerkschnittstelle ist standardmäßig so eingestellt, dass die IP-Adresse automatisch abgerufen wird (DHCP). Daher werden IP-Adresse, Subnetz und Gateway automatisch zugewiesen (sowohl für IPv4 als auch für IPv6).

    Da Sie planen, Ihr Gerät als iSCSI-Server bereitzustellen (zur Bereitstellung eines Blockspeichers), empfehlen wir Folgendes: Deaktivieren Sie die Option **IP-Adresse automatisch beziehen**, und konfigurieren Sie statische IP-Adressen.

    ![Seite „Netzwerkeinstellungen“](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Wenn Sie während der Bereitstellung des Geräts mehr als eine Netzwerkschnittstelle hinzugefügt haben, können Sie diese hier konfigurieren.

5. DNS-Server sind erforderlich, da sie verwendet werden, wenn Ihr Gerät versucht, mit Ihren Cloudspeicher-Dienstanbietern zu kommunizieren. Sie werden auch genutzt, um Ihr Gerät nach dem Namen aufzulösen, wenn es als Dateiserver konfiguriert ist. Auf der Seite **Netzwerkeinstellungen** unter **DNS-Server**:

    1. Es werden automatisch ein primärer und ein sekundärer DNS-Server konfiguriert. Wenn Sie sich für die Konfiguration statischer IP-Adressen entscheiden, können Sie DNS-Server angeben. Zur Sicherstellung der hohen Verfügbarkeit wird empfohlen, einen primären und einen sekundären DNS-Server zu konfigurieren.

    2. Klicken Sie auf **Übernehmen**. Die Netzwerkeinstellungen werden angewendet und überprüft.

6. Auf der Seite **Geräteeinstellungen**:

    1. Weisen Sie Ihrem Gerät einen eindeutigen **Namen** zu. Dieser Name kann 1 bis 15 Zeichen lang sein und Buchstaben, Zahlen und Bindestriche enthalten.

    2. Klicken Sie auf das Symbol **iSCSI-Server** ![Symbol „iSCSI-Server“](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) für den **Typ** von Gerät, den Sie erstellen. Ein iSCSI-Server ermöglicht Ihnen die Bereitstellung von Blockspeicher.

    3. Geben Sie an, ob für das Gerät der Beitritt zu einer Domäne durchgeführt werden soll. Wenn es sich bei Ihrem Gerät um einen iSCSI-Server handelt, ist der Domänenbeitritt optional. Wenn Sie sich gegen den Beitritt des iSCSI-Servers zu einer Domäne entscheiden, klicken Sie auf **Übernehmen**, warten das Anwenden der Einstellungen ab und springen dann zum nächsten Schritt.

        Gehen Sie wie folgt vor, wenn Sie für das Gerät den Domänenbeitritt durchführen möchten: Geben Sie einen **Domänennamen** ein (unten dargestellt).

    4. Klicken Sie auf **Übernehmen**.

    5. Ein Dialogfeld wird angezeigt. Geben Sie die Anmeldeinformationen für die Domäne im angegebenen Format ein. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Die Anmeldeinformationen für die Domäne werden überprüft. Es wird eine Fehlermeldung angezeigt, wenn die Anmeldeinformationen falsch sind.

        ![Anmeldeinformationen](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)
        
           > [AZURE.NOTE]
	   > 
	   > Stellen Sie beim Beitritt Ihres iSCSI-Servers zu einer Domäne sicher, dass sich Ihr virtuelles Array in seiner eigenen Organisationseinheit für Microsoft Azure Active Directory befindet und keine Gruppenrichtlinienobjekte darauf angewendet sind.
	   

    6. Klicken Sie auf **Übernehmen**. Die Geräteeinstellungen werden angewendet und überprüft.
 
7. Optional: Konfigurieren Sie Ihren Webproxyserver. Die Webproxykonfiguration ist optional. Achten Sie jedoch bei Verwendung eines Webproxys darauf, dass dieser nur hier konfiguriert werden kann.

    ![Webproxy konfigurieren](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Auf der Seite **Webproxy**:

    1. Geben Sie die **Webproxy-URL** in diesem Format an: *http://host-IP-Adresse * oder *FQDN:Portnummer*. Beachten Sie, dass HTTPS-URLs nicht unterstützt werden.

    2. Geben Sie unter **Authentifizierung** die Option **Einfach**, **NTLM**, oder **Keine** an.

    3. Wenn Sie die Authentifizierung verwenden, müssen Sie auch einen **Benutzernamen** und ein **Kennwort** angeben.

    4. Klicken Sie auf **Übernehmen**. Die konfigurierten Webproxyeinstellungen werden überprüft und angewendet.
 
8. Optional: Konfigurieren Sie die Zeiteinstellungen für Ihr Gerät, z. B. die Zeitzone und die primären und sekundären NTP-Server. NTP-Server sind für die Zeitsynchronisierung erforderlich, damit Ihr Gerät bei den Clouddienstanbietern authentifiziert werden kann.

    ![Uhrzeiteinstellungen](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Auf der Seite **Uhrzeiteinstellungen**:

    1. Legen Sie über die Dropdownliste die **Zeitzone** basierend auf dem geografischen Standort fest, an dem das Gerät bereitgestellt wird. Die Standardzeitzone für Ihr Gerät ist „PST“. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.

    2. Geben Sie einen **Primären NTP-Server** für das Gerät an, oder übernehmen Sie den Standardwert „time.windows.com“. Stellen Sie sicher, dass Ihr Netzwerk NTP-Datenverkehr vom Rechenzentrum ins Internet zulässt.

    3. Geben Sie optional einen **Sekundären NTP-Server** für Ihr Gerät an.

    4. Klicken Sie auf **Übernehmen**. Die konfigurierten Uhrzeiteinstellungen werden überprüft und angewendet.

9. Konfigurieren Sie die Cloudeinstellungen für Ihr Gerät. In diesem Schritt führen Sie die lokale Gerätekonfiguration durch und registrieren das Gerät dann beim StorSimple Manager-Dienst.

    1. Geben Sie den **Dienstregistrierungsschlüssel** ein, den Sie in **Schritt 2: Abrufen des Dienstregistrierungsschlüssels** unter [Bereitstellen des StorSimple Virtual Array – Vorbereiten des Portals](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) beschafft haben.

    2. Falls dies nicht das erste Gerät ist, das Sie bei diesem Dienst registrieren, müssen Sie den **Dienstdaten-Verschlüsselungsschlüssel** angeben. Dieser Schlüssel ist zusammen mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte beim StorSimple Manager-Dienst erforderlich. Sehen Sie sich die weiteren Informationen zum [Abrufen des Verschlüsselungsschlüssels für Dienstdaten](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) für Ihre lokale Webbenutzeroberfläche an.

    3. Klicken Sie auf **Registrieren**. Das Gerät wird neu gestartet. Sie müssen ggf. zwei bis drei Minuten warten, bis das Gerät erfolgreich registriert wurde. Nachdem das Gerät neu gestartet wurde, wird die Anmeldeseite angezeigt.

       ![Gerät registrieren](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Wechseln Sie zurück zum klassischen Azure-Portal. Vergewissern Sie sich auf der Seite **Geräte**, dass das Gerät erfolgreich mit dem Dienst verbunden wurde, indem Sie seinen Status überprüfen. Der Gerätestatus sollte **Aktiv** lauten.

    ![Seite "Geräte"](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## Schritt 2: Durchführen der erforderlichen Geräteinstallation

Für die Konfiguration Ihres StorSimple-Geräts benötigen Sie Folgendes:

- Wählen Sie ein Speicherkonto, das Sie Ihrem Gerät zuordnen möchten.

- Wählen Sie die Verschlüsselungseinstellungen für die Daten aus, die an die Cloud gesendet werden sollen.

Führen Sie die folgenden Schritte im klassischen Azure-Portal aus, um die erforderliche Geräteinstallation durchzuführen.

#### So führen Sie die minimale Geräteinstallation durch

1. Wählen Sie auf der Seite **Geräte** das gerade erstellte Gerät aus. Für das Gerät wird **Aktiv** angezeigt. Klicken Sie auf den Pfeil neben dem Gerätenamen und dann auf **Schnellstart**.

    ![Seite "Geräte"](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Klicken Sie auf **Geräteinstallation abschließen**, um den Assistenten zum Konfigurieren von Geräten zu starten.

    ![Assistent „Gerät konfigurieren“](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. Gehen Sie im Assistenten zum Konfigurieren von Geräten auf der Seite **Grundlegende Einstellungen** wie folgt vor:

   1. Geben Sie ein Speicherkonto für das Gerät an. In diesem Abonnement können Sie in der Dropdownliste ein vorhandenes Speicherkonto auswählen oder **Weitere hinzufügen** angeben, um ein Konto eines anderen Abonnements auszuwählen.

   2. Definieren Sie die Verschlüsselungseinstellungen für alle ruhenden Daten, die in die Cloud gesendet werden. (Für StorSimple wird die AES-256-Verschlüsselung verwendet.) Aktivieren Sie zum Verschlüsseln der Daten das Kontrollkästchen **Cloudspeicherverschlüsselung aktivieren**. Geben Sie eine Cloudspeicherverschlüsselung ein, die 32 Zeichen lang ist. Geben Sie den Schlüssel erneut ein, um ihn zu bestätigen.

   3. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Grundlegende Einstellungen](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Die Einstellungen werden jetzt aktualisiert. Nachdem die Einstellungen erfolgreich aktualisiert wurden, ist die Schaltfläche „Geräteinstallation abschließen“ nicht mehr verfügbar. Sie gelangen zurück auf die Seite **Schnellstart** des Geräts.

>[AZURE.NOTE]Alle anderen Geräteeinstellungen können über die Seite **Konfigurieren** geändert werden.

## Schritt 3: Hinzufügen eines Volumes

Führen Sie die folgenden Schritte im klassischen Azure-Portal aus, um ein Volume zu erstellen.

#### So erstellen Sie ein Volume

1. Klicken Sie auf dem Gerät auf der Seite **Schnellstart** auf **Volume hinzufügen**. Dadurch wird der Assistent "Volume hinzufügen" gestartet.

2. Gehen Sie im Assistenten "Volume hinzufügen" unter **Grundlegende Einstellungen** folgendermaßen vor:

    1. Geben Sie einen eindeutigen Namen für Ihr Volume an. Der Name muss eine Zeichenfolge mit einer Länge von 3 bis 127 Zeichen sein.

    2. Geben Sie eine Beschreibung für das Volume an. Die Beschreibung ist für die Identifizierung der Volumebesitzer hilfreich.

    3. Wählen Sie einen Verwendungstyp für das Volume aus. Der Verwendungstyp kann **Mehrstufiges Volume** oder **Lokales Volume** lauten. (**Mehrstufiges Volume** ist die Standardeinstellung.) Für Workloads, die lokale Garantien, niedrige Latenzzeiten und höhere Leistung benötigen, wählen Sie ein **lokales** **Volume** aus. Wählen Sie für alle anderen Daten ein **mehrstufiges** **Volume** aus.

        Ein lokales Volume wird mit vollständiger Speicherzuweisung (Thick Provisioning) bereitgestellt und stellt sicher, dass die primären Daten auf dem Volume auf dem Gerät verbleiben und nicht in die Cloud übergehen. Wenn Sie ein lokales Volume erstellen, überprüft das Gerät den verfügbaren Speicherplatz auf den lokalen Ebenen, um ein Volume in der angeforderten Größe bereitzustellen. Bei der Erstellung eines lokalen Volumes müssen vorhandene Daten unter Umständen vom Gerät in die Cloud übertragen werden, und das Erstellen des Volumes kann längere Zeit in Anspruch nehmen. Die gesamte Zeit hängt von der Größe des bereitgestellten Volumes, von der verfügbaren Netzwerkbandbreite und von den Daten auf Ihrem Gerät ab.

        Ein mehrstufiges Volume wird mit schlanker Speicherzuweisung bereitgestellt und kann sehr schnell erstellt werden. Wenn Sie ein mehrstufiges Volume erstellen, werden ca. 10 % des Speicherplatzes auf der lokalen Ebene und 90 % des Speicherplatzes in der Cloud bereitgestellt. Wenn Sie beispielsweise ein Volume mit 1 TB bereitstellen, befinden sich 100 GB lokal, und 900 GB werden in der Cloud zum Anordnen der Daten genutzt. Dies bedeutet wiederum Folgendes: Wenn der gesamte lokale Speicherplatz auf dem Gerät aufgebraucht ist, können Sie keine mehrstufige Freigabe bereitstellen (da die 10 % nicht verfügbar sind).

    4. Geben Sie die bereitgestellte Kapazität für das Volume an. Beachten Sie, dass die angegebene Kapazität kleiner als die verfügbare Kapazität sein sollte. Wenn Sie ein mehrstufiges Volume erstellen, sollte diese Größe zwischen 500 GB und 20 TB liegen. Geben Sie für ein lokales Volume eine Volumegröße zwischen 50 GB und 2 TB an. Verwenden Sie die verfügbare Kapazität als Richtschnur für die Bereitstellung eines Volumes. Wenn die verfügbare lokale Kapazität 0 GB beträgt, können Sie keine lokalen oder mehrstufigen Volumes bereitstellen.

        ![Grundlegende Einstellungen](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Klicken Sie auf den Pfeil ![Pfeilsymbol](./media/storsimple-ova-deploy3-iscsi-setup/image18.png), um zur nächsten Seite zu wechseln.

3. Fügen Sie auf der Seite **Zusätzliche Einstellungen** einen neuen Datensatz für die Zugriffssteuerung (Access Control Record, ACR) hinzu:

    1. Geben Sie einen **Namen** für den ACR ein.

    2. Geben Sie unter **iSCSI-Initiatorname** den qualifizierten iSCSI-Namen (IQN) des Windows-Hosts an. Wenn Sie den IQN nicht kennen, fahren Sie mit [Anhang A: Abrufen des IQNs eines Windows Server-Hosts](#appendix-a-get-the-iqn-of-a-windows-server-host) fort.

    3. Es wird empfohlen, eine Standardsicherung zu aktivieren, indem Sie das Kontrollkästchen **Standardsicherung für dieses Volume aktivieren** aktivieren. Die Standardsicherung erstellt eine Richtlinie, die jeden Tag um 22:30 Uhr ausgeführt wird (Uhrzeit des Geräts) und eine Cloud-Momentaufnahme dieses Volumes erstellt.

        ![Zusätzliche Einstellungen](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Der Auftrag zur Erstellung des Volumes wird gestartet. Sie sehen eine Statusmeldung, die folgender Meldung ähnelt:

        ![Statusmeldung](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Ein Volume wird mit den angegebenen Einstellungen erstellt. Standardmäßig werden für das Volume die Überwachung und die Sicherung aktiviert.

    5. Wechseln Sie zur Seite **Volumes**, um zu bestätigen, dass die Erstellung des Volumes erfolgreich war. Das Volume sollte hier aufgeführt werden.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## Schritt 4: Bereitstellen, Initialisieren und Formatieren eines Volumes

Führen Sie die folgenden Schritte aus, um Ihre StorSimple-Volumes auf einem Windows Server-Host bereitzustellen, zu initialisieren und zu formatieren.

#### So stellen Sie ein Volume bereit und initialisieren und formatieren dieses

1. Starten Sie den Microsoft iSCSI-Initiator.

2. Klicken Sie im Fenster **Eigenschaften des iSCSI-Initiators** auf der Registerkarte **Erkennung** auf **Portal ermitteln**.

    ![Portal ermitteln](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Geben Sie im Dialogfeld **Zielportal ermitteln** die IP-Adresse Ihrer iSCSI-aktivierten Netzwerkschnittstelle an, und klicken Sie dann auf **OK**.

    ![IP-Adresse](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Suchen Sie auf der Registerkarte **Ziele** des Fensters **Eigenschaften des iSCSI-Initiators** nach **Ermittelte Ziele**. (Jedes Volume ist ein ermitteltes Ziel.) Als Gerätestatus sollte **Inaktiv** angezeigt werden.

    ![Ermittelte Ziele](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Wählen Sie ein Zielgerät aus, und klicken Sie dann auf **Verbinden**. Nachdem das Gerät verbunden ist, sollte sich der Status in **Verbunden** ändern. (Weitere Informationen zum Verwenden des Microsoft iSCSI-Initiators finden Sie unter [Installieren und Konfigurieren des Microsoft iSCSI-Initiators][1].)

    ![Zielgerät auswählen](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. Drücken Sie auf Ihrem Windows-Host die Windows-Logo-Taste+X, und klicken Sie dann auf **Ausführen**.

7. Geben Sie im Dialogfeld **Ausführen** die Angabe **Diskmgmt.msc** ein. Klicken Sie auf **OK**. Das Dialogfeld **Datenträgerverwaltung** wird angezeigt. Im rechten Bereich werden die Volumes auf Ihrem Host angezeigt.

8. Im Fenster **Datenträgerverwaltung** werden die bereitgestellten Volumes wie in der folgenden Abbildung dargestellt angezeigt. Klicken Sie mit der rechten Maustaste auf das ermittelte Volume (klicken Sie auf den Datenträgernamen), und klicken Sie dann auf **Online**.

    ![Datenträgerverwaltung](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Klicken Sie mit der rechten Maustaste, und wählen Sie **Datenträgerinitialisierung**.

    ![Datenträger 1 initialisieren](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Wählen Sie im Dialogfeld die zu initialisierenden Datenträger aus, und klicken Sie auf **OK**.

    ![Datenträger 2 initialisieren](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. Der Assistent zum Erstellen neuer einfacher Volumes wird gestartet. Wählen Sie eine Datenträgergröße aus, und klicken Sie dann auf **Weiter**.

    ![Assistent für neue Volumes 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Weisen Sie dem Volume einen Laufwerkbuchstaben zu, und klicken Sie auf **Weiter**.

    ![Assistent für neue Volumes 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Geben Sie die Parameter zum Formatieren des Volumes ein. **Unter Windows Server wird nur NTFS unterstützt.** Legen Sie den AUS-Wert (Größe der Zuordnungseinheit) auf 64 K fest. Geben Sie eine Bezeichnung für das Volume an. Die empfohlene bewährte Methode besteht darin, dass dieser Name mit dem Volumenamen identisch ist, den Sie auf Ihrem virtuellen StorSimple-Gerät angegeben haben. Klicken Sie auf **Weiter**.

    ![Assistent für neue Volumes 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Überprüfen Sie die Werte für Ihr Volume, und klicken Sie dann auf **Fertig stellen**.

    ![Assistent für neue Volumes 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Die Volumes werden auf der Seite **Datenträgerverwaltung** als **Online** angezeigt.

    ![Volumes online](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## Nächste Schritte

Erfahren Sie, wie Sie die lokale Webbenutzeroberfläche verwenden, um [Ihr StorSimple Virtual Array zu verwalten](storsimple-ova-web-ui-admin.md).

## Anhang A: Abrufen des IQN eines Windows Server-Hosts

Führen Sie die folgenden Schritte aus, um den IQN (iSCSI Qualified Name) eines Windows-Hosts abzurufen, auf dem Windows Server 2012 ausgeführt wird.

#### So rufen Sie den IQN eines Windows Server-Hosts ab

1. Starten Sie den Microsoft iSCSI-Initiator auf dem Windows-Host.

2. Wählen Sie im Fenster **iSCSI Initiator Properties** auf der Registerkarte **Configuration** die Zeichenfolge im Feld **Initiator Name** aus, und kopieren Sie sie.

    ![Eigenschaften von iSCSI-Initiator](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Speichern Sie diese Zeichenfolge.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx

<!---HONumber=AcomDC_0218_2016-->