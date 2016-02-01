<properties
   pageTitle="Bereitstellen von StorSimple Virtual Array 1 – Vorbereiten des Portals"
   description="Erstes Tutorial zum Bereitstellen von virtuellen StorSimple-Arrays mit Vorbereitung des Portals"
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

# Bereitstellen von StorSimple Virtual Array – Vorbereiten des Portals (Vorschau)

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## Einführung 

Dieser Artikel bezieht sich nur auf Microsoft Azure StorSimple Virtual Array (auch als „lokales virtuelles StorSimple-Gerät“ oder „virtuelles StorSimple-Gerät“ bezeichnet) mit Version 1.1.1.0 (öffentliche Vorschau). Dies ist der erste Artikel einer Reihe von Tutorials, in denen die vollständige Bereitstellung Ihres virtuellen Arrays als Dateiserver oder iSCSI-Server erläutert wird. In diesem Artikel wird die Vorbereitung beschrieben, die für die Erstellung und Konfiguration Ihres StorSimple Manager-Diensts vor dem Bereitstellen eines virtuellen Arrays erforderlich ist. Außerdem enthält dieser Artikel Links zu einer Prüfliste für die Bereitstellungskonfiguration und Konfigurationsvoraussetzungen.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Es wird empfohlen, die Prüfliste für die Bereitstellungskonfiguration durchzugehen, bevor Sie beginnen. Die Vorbereitung des Portals dauert weniger als 10 Minuten.

Die in diesem Artikel veröffentlichten Informationen zur StorSimple-Bereitstellung gelten nur für StorSimple Virtual Arrays.

> [AZURE.IMPORTANT]
> 
> Diese öffentliche Vorschauversion ist nur für Evaluierungs- und Planungszwecke im Rahmen einer Bereitstellung bestimmt. Die Installation dieser Vorschau in einer Produktionsumgebung wird nicht unterstützt.

### Erste Schritte

Der Bereitstellungsworkflow umfasst das Vorbereiten des Portals, das Bereitstellen eines virtuellen Arrays in Ihrer virtualisierten Umgebung und das Durchführen des Setups. Zum Beginnen mit der StorSimple Virtual Array-Bereitstellung als Dateiserver oder iSCSI-Server benötigen Sie die folgenden Ressourcen (Artikel und Videos) in der angegebenen Tabelle.

#### Artikel zur Bereitstellung

Sehen Sie sich die folgenden Artikel in der angegebenen Reihenfolge an, um Ihr StorSimple Virtual Array bereitzustellen.

| **#** | **Schritt** | **Aktion** | **Verwendete Dokumente**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1\. | **Einrichten des klassischen Azure-Portals** | Erstellen und konfigurieren Sie Ihren StorSimple Manager-Dienst, bevor Sie ein virtuelles StorSimple-Gerät bereitstellen. |[Vorbereiten des Portals](storsimple-ova-deploy1-portal-prep.md)| 
|2\. | **Bereitstellen des Virtual Array** | Stellen Sie für Hyper-V ein virtuelles StorSimple-Gerät auf einem Hostsystem mit Hyper-V 2008 R2, Hyper-V 2012 oder Hyper-V 2012 R2 bereit, und stellen Sie die Verbindung her. <br></br> <br></br> Stellen Sie für VMware ein lokales virtuelles StorSimple-Gerät auf einem Hostsystem mit VMware ESXi 5.5 und höher bereit, und stellen Sie die Verbindung her.| [Bereitstellen eines virtuellen Arrays in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Bereitstellen eines virtuellen Arrays in VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3\. | **Einrichten des Virtual Array** | Führen Sie für Ihren Dateiserver das anfängliche Setup durch, registrieren Sie Ihren StorSimple-Dateiserver, und führen Sie die Geräteinstallation durch. Anschließend können Sie die SMB-Freigaben bereitstellen. <br></br><br></br> Führen Sie für Ihren iSCSI-Server das anfängliche Setup durch, registrieren Sie Ihren StorSimple-iSCSI-Server, und führen Sie die Geräteinstallation durch. Anschließend können Sie die iSCSI-Volumes bereitstellen.| [Einrichten des virtuellen Arrays als Dateiserver](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Einrichten des virtuellen Arrays als iSCSI-Server](storsimple-ova-deploy3-iscsi-setup.md)|

#### Videos zur Bereitstellung

| **Schritt** | **Video**|
|----------------|-------------|
| Enthält eine Schritt-für-Schritt-Anleitung für die ersten Schritte mit dem StorSimple Virtual Array. | [Get Started with the StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array)|
| Enthält eine Schritt-für-Schritt-Anleitung zum Bereitstellen eines StorSimple Virtual Array in Hyper-V.|[Create a StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array) |
|Enthält eine Schritt-für-Schritt-Anleitung zum Konfigurieren und Registrieren eines StorSimple Virtual Array.|[Configure a StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array)|
|Enthält eine Schritt-für-Schritt-Anleitung zum Erstellen von Freigaben, Sichern von Freigaben und Wiederherstellen von Daten in einem StorSimple Virtual Array, das als Dateiserver konfiguriert wird.|[Use the StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array)|
|Enthält eine Schritt-für-Schritt-Anleitung für das Failover und die Notfallwiederherstellung eines StorSimple Virtual Array.|[StorSimple Virtual Array Disaster Recovery](http://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery)

Sie können jetzt mit der Einrichtung des klassischen Azure-Portals beginnen.

## Prüfliste für die Konfiguration

Die Prüfliste für die Konfiguration enthält die Informationen, die Sie vor der Konfiguration der Software auf dem StorSimple-Gerät erfassen müssen. Wenn Sie diese Informationen im Voraus sammeln, beschleunigt dies die Bereitstellung des StorSimple-Geräts in Ihrer Umgebung. Welche der folgenden Prüflisten Sie benötigen, hängt davon ab, ob Sie das virtuelle StorSimple-Gerät als Dateiserver oder iSCSI-Server bereitstellen.

-   Laden Sie die PDF-Datei [StorSimple Virtual Array File Server Configuration Checklist](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf) herunter.

-   Laden Sie die PDF-Datei [StorSimple Virtual Array iSCSI Server Configuration Checklist](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf) herunter.

## Voraussetzungen

Hier sind die Konfigurationsvoraussetzungen für Ihren StorSimple Manager-Dienst, Ihr virtuelles StorSimple-Gerät und das Netzwerk des Rechenzentrums angegeben.

### Für den StorSimple Manager-Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:

-   Sie besitzen ein Microsoft-Konto mit Anmeldeinformationen für den Zugriff.

-   Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.

-   Ihr Microsoft Azure-Abonnement ist für den StorSimple Manager-Dienst aktiviert.

### Für das virtuelle StorSimple-Gerät 

Stellen Sie Folgendes sicher, bevor Sie ein virtuelles Gerät bereitstellen:

-   Sie haben Zugriff auf ein Hostsystem mit Hyper-V (2008 R2 oder höher) oder VMware (ESXi 5.5 oder höher), das zum Bereitstellen eines Geräts verwendet werden kann.

-   Das Hostsystem verfügt für die Bereitstellung des virtuellen Geräts über die folgenden Ressourcen:
	
	-   Mindestens 4 Kerne
	
	-   Mindestens 8 GB RAM
	
	-   Eine Netzwerkschnittstelle
	
	-   Einen virtuellen Datenträger mit 500 GB für Systemdaten

### Für das Netzwerk des Rechenzentrums 

Stellen Sie Folgendes sicher, bevor Sie beginnen:

-   Das Netzwerk in Ihrem Rechenzentrum ist gemäß den Netzwerkanforderungen für das StorSimple-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für das StorSimple Virtual Array](storsimple-ova-system-requirements.md).

-   Für das virtuelle StorSimple-Gerät steht jederzeit eine Internetbandbreite von 5 MBit/s (oder mehr) zur Verfügung. Diese Bandbreite sollte nicht gemeinsam mit anderen Anwendungen genutzt werden.

## Schritt-für-Schritt-Vorbereitung

Verwenden Sie die folgende Schritt-für-Schritt-Anleitung, um Ihr Portal für den StorSimple Manager-Dienst vorzubereiten.

## Schritt 1: Erstellen eines neuen Diensts

Mit einer einzelnen Instanz des StorSimple Manager-Diensts können mehrere StorSimple 1200-Geräte verwaltet werden. Führen Sie die folgenden Schritte aus, um einen neuen StorSimple Manager-Dienst zu erstellen. Falls Sie einen vorhandenen StorSimple Manager-Dienst zum Verwalten Ihrer 1200-Geräte verwenden, können Sie diesen Schritt überspringen und mit [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key) fortfahren.

#### So erstellen Sie einen neuen Dienst

1.  Melden Sie sich mit Ihren Microsoft-Kontoanmeldeinformationen unter dieser URL beim klassischen Azure-Portal an: <https://manage.windowsazure.com/>

2.  Klicken Sie im Portal auf **Neu > Datendienste > StorSimple Manager > Schnellerfassung**.

3.  Gehen Sie im angezeigten Formular folgendermaßen vor:

	1.  Geben Sie einen eindeutigen **Namen** für Ihren Dienst an. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.

	2.  Wählen Sie in der Dropdownliste unter **Verwalteter Gerätetyp** die Option **Serie virtueller Geräte**, um einen Dienst zum Verwalten eines virtuellen StorSimple-Geräts auszuwählen.

	3.  Geben Sie einen **Standort** für Ihren Dienst an. Der Standort bezieht sich auf die geografische Region, in der Ihr Gerät bereitgestellt werden soll.

	 -   Falls Sie über weitere Workloads in Azure verfügen, die über Ihr StorSimple-Gerät bereitgestellt werden sollen, empfiehlt sich die Verwendung dieses Rechenzentrums.

   	 -   StorSimple Manager und der Azure-Speicher können sich an zwei verschiedenen Standorten befinden. In einem solchen Fall müssen Sie das StorSimple Manager- und das Azure-Speicherkonto separat erstellen. Um ein Azure-Speicherkonto zu erstellen, wechseln Sie zum Azure Storage-Dienst im Portal und befolgen die Schritte unter [Erstellen eines Azure Storage-Kontos](storage-create-storage-account.md#create-a-storage-account). Nach der Erstellung dieses Kontos fügen Sie es dem StorSimple Manager-Dienst hinzu, indem Sie die Schritte unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#appendix-a-configure-a-new-storage-account-for-the-service) ausführen.
   	 
   	 -   Hinweis: In der Vorschauversion können Sie den StorSimple Manager-Dienst nur in den Regionen „USA, Westen“ und „Japan, Osten“ erstellen.
	
	1.  Wählen Sie ein **Abonnement** aus der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.

	1.  Aktivieren Sie **Neues Azure-Speicherkonto erstellen**, um automatisch ein Speicherkonto mit dem Dienst zu erstellen. Dieses Speicherkonto erhält einen speziellen Namen, z. B. „storsimplebwv8c6dcnf“. Deaktivieren Sie dieses Kontrollkästchen, wenn Sie Ihre Daten an einem anderen Speicherort benötigen.

	1.  Klicken Sie auf **StorSimple-Manager erstellen**, um den Dienst zu erstellen.

		![](./media/storsimple-ova-deploy1-portal-prep/image1.png)

	Sie werden auf die Startseite **Dienst** weitergeleitet. Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, werden Sie entsprechend benachrichtigt.

	![](./media/storsimple-ova-deploy1-portal-prep/image2.png)

	Der Status des Diensts ändert sich in **Aktiv**.

	![](./media/storsimple-ova-deploy1-portal-prep/image3.png)

> [AZURE.IMPORTANT]
> 
> Wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben, müssen Sie mindestens ein Speicherkonto erstellen, nachdem Sie einen Dienst erstellt haben.
> 

> - Wenn Sie nicht automatisch ein Speicherkonto erstellt haben, finden Sie unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#appendix-a-configure-a-new-storage-account-for-the-service) ausführliche Anweisungen.
> 

> - Wenn Sie die automatische Erstellung eines Speicherkontos aktiviert haben, fahren Sie mit [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2-get-the-service-registration-key) fort.


## Schritt 2: Abrufen des Dienstregistrierungsschlüssels


Nachdem der StorSimple Manager-Dienst eingerichtet wurde und ausgeführt wird, müssen Sie den Dienstregistrierungsschlüssel abrufen. Dieser Schlüssel wird zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Dienst verwendet.

Führen Sie die folgenden Schritte im [klassischen Azure-Portal](https://manage.windowsazure.com/) aus.

#### So rufen Sie den StorSimple-Dienstregistrierungsschlüssel ab

1.  Klicken Sie auf der Seite **StorSimple Manager-Dienst** unten auf **Registrierungsschlüssel**.

2.  Sie müssen einige Minuten warten, während der Schlüssel abgerufen werden. Das Dialogfeld **Dienstregistrierungsschlüssel** wird angezeigt.


	1.  Suchen Sie nach dem **Dienstregistrierungsschlüssel**.

	2.  Klicken Sie auf das Kopiersymbol ![](./media/storsimple-ova-deploy1-portal-prep/image6.png), um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.

> [AZURE.NOTE]
> 
> Der Dienstregistrierungsschlüssel wird zur Registrierung aller StorSimple Manager-Geräte verwendet, die für den StorSimple-Manager-Dienst registriert werden müssen.

## Schritt 3: Herunterladen des Image mit dem virtuellen Gerät

Nach dem Beschaffen des Dienstregistrierungsschlüssels müssen Sie nun das passende Image mit dem virtuellen Gerät herunterladen, um auf Ihrem Hostsystem ein virtuelles Gerät bereitzustellen. Die Images virtueller Geräte sind betriebssystemspezifisch und können im klassischen Azure-Portal von der Seite „Schnellstart“ heruntergeladen werden.

Führen Sie die folgenden Schritte im [klassischen Azure-Portal](https://manage.windowsazure.com/) aus.

#### So rufen Sie das Image mit dem virtuellen Gerät ab

1.  Klicken Sie auf der Seite des Diensts **StorSimple-Manager** auf den Dienst, den Sie erstellt haben. Damit gelangen Sie auf die Seite **Schnellstart**. (Sie können jederzeit über das Schnellstartsymbol ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) auf die Seite **Schnellstart** zugreifen.)


1.  Laden Sie die entsprechende VHD auf eine Netzwerkfreigabe in Ihrem Rechenzentrum herunter. Separate VHDs sind verfügbar für:

	-   Hyper-V 2008 R2 und höher

	-   VMWare ESXi 5.5 und höher

1.  Klicken Sie auf das Bild für Ihr Hostbetriebssystem, das Sie zum Bereitstellen des virtuellen Geräts verwenden möchten. So gelangen Sie zum Microsoft Download Center.

1.  Laden Sie bei Verwendung von Hyper-V die VHD für Hyper-V 2008 R2 und höher herunter. Laden Sie das VMDK herunter, wenn Sie VMware verwenden. Die VHD ist eine ZIP-Datei mit einer Größe von 4,77 GB, und das VMDK hat eine Größe von 4,75 GB. Die für das Herunterladen der Datei erforderliche Zeit richtet sich nach Ihrer Internetverbindung.

2.  Entzippen Sie die Datei, und notieren Sie sich den entsprechenden Speicherort auf Ihrem lokalen Datenträger.

![Video-Symbol](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Video verfügbar**

Sehen Sie sich dieses Video mit einer Schritt-für-Schritt-Anleitung für die ersten Schritte mit dem StorSimple Virtual Array an.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]

## Nächster Schritt

Im nächsten Schritt wird eine virtuelle Maschine für Ihr virtuelles StorSimple-Gerät bereitgestellt. Sehen Sie sich je nach Hostbetriebssystem die folgenden ausführlichen Anweisungen an:

-   [StorSimple Virtual Array-Bereitstellung in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [StorSimple Virtual Array-Bereitstellung in VMware](storsimple-ova-deploy2-provision-vmware.md)


## Anhang A: Konfigurieren eines neuen Speicherkontos für den Dienst

Dies ist ein optionaler Schritt, der nur dann ausgeführt werden muss, wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben.

Wenn Sie ein Azure-Speicherkonto in einer anderen Region erstellen müssen, hilft Ihnen die Schritt-für-Schritt-Anleitung unter [Erstellen eines Speicherkontos](storage-create-storage-account.md#create-a-storage-account) weiter.

Führen Sie die folgenden Schritte im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf der Seite „StorSimple Manager-Dienst“ aus, um ein vorhandenes Microsoft Azure-Speicherkonto hinzuzufügen.

#### So fügen Sie ein Speicherkonto hinzu

1.  Wählen Sie auf der StorSimple-Manager-Dienst-Startseite Ihren Dienst aus, und doppelklicken Sie dann darauf. Damit gelangen Sie auf die Seite **Schnellstart**. Wählen Sie die Seite **Konfigurieren** aus.

2.  Klicken Sie auf **Speicherkonto hinzufügen/bearbeiten**. Gehen Sie im Dialogfeld **Speicherkonten hinzufügen/bearbeiten** folgendermaßen vor:

	1.  Klicken Sie auf **Neues hinzufügen**.

	1.  Geben Sie einen Namen für Ihr Speicherkonto an.

	1.  Geben Sie den primären **Zugriffsschlüssel** für Ihr Microsoft Azure-Speicherkonto an.

	1.  Aktivieren Sie **SSL-Modus aktivieren**, um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem Gerät und der Cloud zu erstellen. Deaktivieren Sie das Kontrollkästchen **SSL-Modus aktivieren** nur, wenn der Betrieb in einer privaten Cloud stattfindet.

	1.  Klicken Sie auf das Häkchensymbol ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Sie werden benachrichtigt, nachdem das Speicherkonto erfolgreich erstellt wurde.

		![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  Das neu erstellte Speicherkonto wird auf der Seite **Konfigurieren** unter **Speicherkonten** angezeigt. Klicken Sie auf **Speichern**, um das neu erstellte Speicherkonto zu speichern. Klicken Sie auf **OK**, wenn Sie zur Bestätigung aufgefordert werden.

<!---HONumber=AcomDC_0121_2016-->