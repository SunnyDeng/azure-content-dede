<properties
   pageTitle="Bereitstellen des StorSimple Virtual Array – Bereitstellen in Hyper-V"
   description="In diesem zweiten Tutorial der Reihe zur StorSimple Virtual Array-Bereitstellung geht es um das Bereitstellen eines virtuellen Geräts in Hyper-V."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="alkohli"/>

# Bereitstellen des StorSimple Virtual Array – Bereitstellen eines Virtual Array in Hyper-V (Vorschau)

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## Übersicht 

Dieses Tutorial zur Bereitstellung bezieht sich nur auf Microsoft Azure StorSimple Virtual Arrays (auch als „lokale virtuelle StorSimple-Geräte“ oder „virtuelle StorSimple-Geräte“ bezeichnet) mit Version 1.1.1.0 (öffentliche Vorschau). In diesem Tutorial wird beschrieben, wie Sie ein StorSimple Virtual Array auf einem Hostsystem mit Hyper-V 2008 R2, Hyper-V 2012 oder Hyper-V 2012 R2 bereitstellen.

Sie benötigen Administratorrechte, um ein virtuelles Gerät bereitzustellen und zu konfigurieren. Die Bereitstellung und die anfängliche Einrichtung dauern ca. 10 Minuten.

> [AZURE.IMPORTANT]
> 
> Diese öffentliche Vorschauversion ist nur für Testzwecke bestimmt. Die Installation dieser Vorschau in einer Produktionsumgebung wird nicht unterstützt.

## Voraussetzungen für die Bereitstellung

Hier sind die Voraussetzungen zum Bereitstellen eines virtuellen Geräts auf einem Hostsystem mit Hyper-V 2008 R2, Hyper-V 2012 oder Hyper-V 2012 R2 angegeben.

### Für den StorSimple-Manager-Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:

-   Sie haben alle Schritte unter [Vorbereiten des Portals für StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md) ausgeführt.

-   Sie haben das Image des virtuellen Geräts für Hyper-V aus dem Azure-Portal heruntergeladen. Weitere Informationen finden Sie unter [Schritt 3: Herunterladen des Image mit dem virtuellen Gerät](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### Für das virtuelle StorSimple-Gerät 

Stellen Sie Folgendes sicher, bevor Sie ein virtuelles Gerät bereitstellen:

-   Sie haben Zugriff auf ein Hostsystem mit Hyper-V (2008 R2 oder höher), das zum Bereitstellen eines Geräts verwendet werden kann.

-   Das Hostsystem verfügt für die Bereitstellung des virtuellen Geräts über die folgenden Ressourcen:

	-   Mindestens 4 Kerne
	
	-   Mindestens 8 GB RAM
	
	-   Eine Netzwerkschnittstelle
	
	-   Einen virtuellen Datenträger mit 500 GB für Systemdaten

### Für das Netzwerk im Datencenter 

Stellen Sie Folgendes sicher, bevor Sie beginnen:

-   Sie haben sich die Netzwerkanforderungen zum Bereitstellen eines virtuellen StorSimple-Geräts angesehen und das Netzwerk des Rechenzentrums gemäß den Anforderungen konfiguriert. Weitere Informationen finden Sie unter [StorSimple Virtual Array-Netzwerkanforderungen](storsimple-ova-system-requirements.md#networking-requirements).

## Schritt-für-Schritt-Bereitstellung 

Zum Bereitstellen und Herstellen der Verbindung mit einem virtuellen Gerät müssen Sie die folgenden Schritte ausführen:

1.  Stellen Sie sicher, dass das Hostsystem über genügend Ressourcen zum Erfüllen der Mindestanforderungen des virtuellen Geräts verfügt.

2.  Stellen Sie ein virtuelles Gerät in Ihrem Hypervisor bereit.

3.  Starten Sie das virtuelle Gerät, und rufen Sie die IP-Adresse ab.

Jeder dieser Schritte wird in den folgenden Abschnitten erläutert.

## Schritt 1: Sicherstellen, dass das Hostsystem die Mindestanforderungen für virtuelle Geräte erfüllt

Zum Erstellen eines virtuellen Geräts benötigen Sie Folgendes:

-   Hyper-V 2008 R2 SP1, Hyper-V 2012 oder Hyper-V 2012 R2 auf einem Hostsystem mit Windows Server 2008 R2 SP1, Windows Server 2012 oder Windows Server 2012 R2

-   Microsoft Hyper-V Manager auf einem Microsoft Windows-Client mit einer Verbindung mit dem Host

Sie müssen sicherstellen, dass die zugrunde liegende Hardware (Hostsystem), auf der Sie das virtuelle Gerät erstellen, die folgenden Ressourcen für Ihr virtuelles Gerät zur Verfügung stellen kann:

- Mindestens 4 Kerne
- Mindestens 8 GB RAM
- Eine Netzwerkschnittstelle
- Einen virtuellen Datenträger mit 500 GB für Systemdaten

## Schritt 2: Bereitstellen eines virtuellen Geräts in Hypervisor

Führen Sie die folgenden Schritte aus, um ein Gerät im Hypervisor bereitzustellen.

#### So stellen Sie ein virtuelles Gerät bereit

1.  Kopieren Sie das Image mit dem virtuellen Gerät auf Ihrem Windows Server-Host auf den lokalen Datenträger. Dies ist das Image, das Sie über das Azure-Portal heruntergeladen haben. Notieren Sie sich den Speicherort, an den Sie das Image kopiert haben, da Sie ihn später noch benötigen.

2.  Öffnen Sie **Server-Manager**. Klicken Sie in der oberen rechten Ecke auf **Extras**, und wählen Sie **Hyper-V-Manager**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

	Öffnen Sie den Hyper-V-Manager, wenn Sie Hyper-V 2008 R2 ausführen. Klicken Sie im Server-Manager auf **Rollen > Hyper-V > Hyper-V-Manager**.

1.  Klicken Sie im **Hyper-V-Manager** unter „Bereich“ mit der rechten Maustaste auf Ihren Systemknoten, um das Kontextmenü zu öffnen. Wählen Sie **Neu** und dann **Virtuelle Maschine**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Klicken Sie auf der Seite **Voraussetzungen** auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image3.png)

1.  Geben Sie auf der Seite **Namen und Speicherort angeben** einen **Namen** für Ihr virtuelles Gerät an. Klicken Sie auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Wählen Sie auf der Seite **Generation angeben** die Option **Generation 1** aus. Klicken Sie auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

	Dieser Bildschirm wird nicht angezeigt, wenn Sie Hyper-V 2008 R2 ausführen.

1.  Auf der Seite **Speicher zuweisen**:

    a. Geben Sie einen **Startspeicher** von 8192 MB oder höher an. Die Mindestanforderung für den Arbeitsspeicher eines virtuellen StorSimple-Geräts lautet 8 GB oder mehr. Aktivieren Sie nicht die Option **Dynamischen Arbeitsspeicher für diesen virtuellen Computer verwenden**.

    b. Klicken Sie auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Auf der Seite **Netzwerk konfigurieren**:

    a. Wählen Sie in der Dropdownliste für **Verbindung** einen virtuellen Switch aus. Sie müssen einen virtuellen Switch auswählen, der mit dem Internet verbunden ist.

    b. Klicken Sie auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Auf der Seite **Virtuelle Festplatte verbinden**:

    a. Aktivieren Sie die Option **Vorhandene virtuelle Festplatte verwenden**. Geben Sie den Pfad zur VHD an, die auf Ihr Hostsystem heruntergeladen wurde.

    b. Klicken Sie auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image8.png)

1.  Überprüfen Sie die angezeigte **Zusammenfassung**. Klicken Sie auf **Fertig stellen**, um die virtuelle Maschine zu erstellen.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Sie benötigen vier Kerne, um die Mindestanforderungen zu erfüllen. Gehen Sie zum Hinzufügen von vier virtuellen Prozessoren wie folgt vor: Wählen Sie Ihr Hostsystem im Fenster **Hyper-V-Manager** aus, und suchen Sie im rechten Bereich in der Liste **Virtuelle Computer** nach der virtuellen Maschine, die Sie gerade erstellt haben. Klicken Sie mit der rechten Maustaste auf den ausgewählten Namen der Maschine, und wählen Sie **Einstellungen**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Klicken Sie auf der Seite **Einstellungen** im linken Bereich auf **Prozessor**. Legen Sie im rechten Bereich die **Anzahl virtueller Prozessoren** auf „4“ (oder mehr) fest. Klicken Sie auf **Übernehmen**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Um die Mindestanforderungen zu erfüllen, müssen Sie auch einen virtuellen Datenträger mit 500 GB hinzufügen. Auf der Seite **Einstellungen**:

    1.  Wählen Sie im linken Bereich die Option **SCSI-Controller**. 
    2.  Wählen Sie im rechten Bereich die Option **Festplatte**, und klicken Sie auf **Hinzufügen**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Wählen Sie auf der Seite **Festplatte** die Option **Virtuelle Festplatte**, und klicken Sie auf **Neu**. Der **Assistent für neue virtuelle Festplatten** wird gestartet.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Klicken Sie auf der Seite **Vorbereitungen** auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image14.png)

1.  Übernehmen Sie auf der Seite **Datenträgerformat auswählen** die Standardoption **VHDX** für das Format. Klicken Sie auf **Weiter**. Dieser Bildschirm wird nicht angezeigt, wenn Sie Hyper-V 2008 R2 ausführen.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Legen Sie auf der Seite **Datenträgertyp auswählen** den Datenträgertyp für die virtuelle Festplatte auf **Dynamisch erweiterbar** fest (empfohlen). Sie können auch die Option **Feste Größe** wählen, aber dies ist unter Umständen mit einer langen Wartezeit verbunden. Die Verwendung der Option **Differenzierend** ist nicht zu empfehlen. Klicken Sie auf **Weiter**. Beachten Sie, dass **Dynamisch erweiterbar** in Hyper-V 2012 und Hyper-V 2012 R2 die Standardeinstellung ist. In Hyper-V 2008 R2 ist **Feste Größe** die Standardeinstellung.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Geben Sie auf der Seite **Namen und Speicherort angeben** einen **Namen** und einen **Speicherort** (z. B. per Durchsuchen) für den Datenträger an. Klicken Sie auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Wählen Sie auf der Seite **Datenträger konfigurieren** die Option **Neue virtuelle Festplatte ohne Inhalt erstellen**, und geben Sie als Größe **500 GB** (oder mehr) an. Klicken Sie auf **Weiter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Sehen Sie sich auf der Seite **Zusammenfassung** die Details Ihres virtuellen Datenträgers an, und klicken Sie zum Erstellen des Datenträgers auf **Fertig stellen**, wenn alles korrekt ist. Der Assistent wird geschlossen, und Ihrem Computer wird eine virtuelle Festplatte hinzugefügt.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

1.  Sie gelangen zurück auf die Seite **Einstellungen**. Klicken Sie auf **OK**, um die Seite **Einstellungen** zu schließen, und kehren Sie zum Hyper-V-Manager-Fenster zurück.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## Schritt 3: Starten des virtuellen Geräts und Abrufen der IP-Adresse

Führen Sie die folgenden Schritte aus, um Ihr virtuelles Gerät zu starten und eine Verbindung dafür herzustellen.

#### So starten Sie das virtuelle Gerät

1.  Starten Sie das virtuelle Gerät.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Wenn das Gerät ausgeführt wird, wählen Sie das Gerät aus, klicken Sie mit der rechten Maustaste, und wählen Sie **Verbinden**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Unter Umständen müssen Sie 5 bis 10 Minuten warten, bis das Gerät bereit ist. In der Konsole wird eine Statusmeldung angezeigt, die den Fortschritt angibt. Wenn das Gerät bereit ist, wählen Sie **Aktion**. Drücken Sie `Ctrl + Alt + Delete`, um sich am virtuellen Gerät anzumelden. Der Standardbenutzer ist *StorSimpleAdmin*, und das Standardkennwort ist *Password1*.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Aus Sicherheitsgründen gilt das Kennwort des Geräteadministrators nur für die erste Anmeldung. Sie werden aufgefordert, das Kennwort zu ändern.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

	Geben Sie ein Kennwort ein, das mindestens acht Zeichen lang ist. Das Kennwort muss mindestens drei der folgenden vier Anforderungen erfüllen: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen. Geben Sie das Kennwort erneut ein, um es zu bestätigen. Sie werden benachrichtigt, dass das Kennwort geändert wurde.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Nachdem das Kennwort erfolgreich geändert wurde, wird das virtuelle Gerät unter Umständen neu gestartet. Warten Sie, bis das Gerät gestartet wurde.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

 	Die Windows PowerShell-Konsole des Geräts wird zusammen mit der Statusanzeige eingeblendet.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Die Schritte 6 bis 8 gelten nur beim Starten in anderen Umgebungen als einer DHCP-Umgebung. Falls Sie sich in einer DHCP-Umgebung befinden, sollten Sie diese Schritte überspringen und mit Schritt 9 fortfahren. Wenn Sie Ihr Gerät in einer anderen Umgebung als einer DHCP-Umgebung gestartet haben, wird der folgende Bildschirm angezeigt.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image28.png)

 	Sie müssen das Netzwerk jetzt konfigurieren.

1.  Verwenden Sie den Befehl `Get-HcsIpAddress`, um die Netzwerkschnittstellen aufzulisten, die auf Ihrem virtuellen Gerät aktiviert sind. Wenn für das Gerät eine einzelne Netzwerkschnittstelle aktiviert ist, wird dieser Schnittstelle der Standardname `Ethernet` zugewiesen.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image29.png)

1.  Verwenden Sie das `Set-HcsIpAddress`-Cmdlet, um das Netzwerk zu konfigurieren. Unten ist ein Beispiel angegeben:

 	`Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

 	![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Nachdem die anfängliche Einrichtung abgeschlossen und das Gerät gestartet wurde, wird der Bannertext für das Gerät angezeigt. Notieren Sie sich die IP-Adresse und die URL, die im Bannertext für die Verwaltung des Geräts angezeigt wird. Sie verwenden diese IP-Adresse zum Herstellen der Verbindung mit der Web-UI Ihres virtuellen Geräts und zum Durchführen des lokalen Setups und der Registrierung.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image31.png)

	Wenn Ihr Gerät die Mindestanforderungen für die Konfiguration nicht erfüllt, wird im Bannertext ein Fehler angezeigt (siehe unten). Sie müssen die Gerätekonfiguration ändern, damit sie über ausreichende Ressourcen zum Erfüllen der Mindestanforderungen verfügt. Sie können das Gerät dann neu starten und die Verbindung dafür herstellen. Die Mindestanforderungen für die Konfiguration finden Sie unter [Schritt 1: Sicherstellen, dass das Hostsystem die Mindestanforderungen für virtuelle Geräte erfüllt](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

	![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Falls bei der anfänglichen Konfiguration über die lokale Web-UI andere Fehler auftreten, helfen Ihnen die folgenden Workflows unter [Verwalten des StorSimple Virtual Array über die lokale Web-UI](storsimple-ova-web-ui-admin.md) weiter.

-   Ausführen von Diagnosetests zur [Problembehandlung für das Web-UI-Setup](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)

-   [Generieren des Protokollpakets und Anzeigen von Protokolldateien](storsimple-ova-web-ui-admin.md#generate-a-log-package)

![Video-Symbol](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **Video verfügbar**

Sehen Sie sich das Video an, in dem die Bereitstellung eines StorSimple Virtual Array in Hyper-V beschrieben wird.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## Nächste Schritte

-   [Einrichten des StorSimple Virtual Array als Dateiserver](storsimple-ova-deploy3-fs-setup.md)

-   [Einrichten des StorSimple Virtual Array als iSCSI-Server](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0128_2016-->