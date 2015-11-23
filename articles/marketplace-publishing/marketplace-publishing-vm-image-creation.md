<properties
   pageTitle="Erstellen eines VM-Images für Azure Marketplace | Microsoft Azure"
   description="Ausführliche Anleitung zum Erstellen eines VM-Images, um es anderen Benutzern über den Azure Marketplace zum Kauf anzubieten"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte"/>

# Anleitung zum Erstellen eines VM-Images für Azure Marketplace

In diesem Artikel finden Sie in **Schritt 2** eine schrittweise Anleitung zur Vorbereitung der VHDs, die die Grundlage Ihrer SKU bilden, die Sie im Azure Marketplace bereitstellen. Der Prozess variiert je nachdem, ob Sie eine Linux- oder Windows-basierte SKU bereitstellen. In diesem Abschnitt werden beide Szenarios behandelt. Dieser Vorgang kann parallel zur [Kontoerstellung und -registrierung][link-acct-creation] ausgeführt werden.

## 1\. Definieren von Angeboten und SKUs

Dieser Abschnitt befasst sich mit der Definition der Angebote und der ihnen zugrunde liegenden SKUs.

Ein Angebot ist ein „übergeordnetes Element“ für alle darin enthaltenen SKUs. Sie können mehrere Angebote definieren. Wie Sie Ihre Angebote aufbauen, bleibt Ihnen überlassen. Wenn ein Angebot in die Stagingphase überführt wird, werden alle SKUs mit einbezogen. Die SKU-IDs sollten sorgfältig geprüft werden, da sie in der URL sichtbar sind.

- Azure.com – http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Azure-Vorschauportal – https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

SKU ist der Handelsname für ein VM-Image. Ein VM-Image enthält einen Betriebssystemdatenträger und null oder mehr Datenträger. Es handelt sich im Wesentlichen um das komplette Speicherprofil für einen virtuellen Computer. Pro Datenträger ist eine VHD erforderlich. Selbst für leere Datenträger muss eine VHD erstellt werden.

Fügen Sie unabhängig vom verwendeten Betriebssystem immer nur die von der SKU benötigte Mindestanzahl an Datenträgern hinzu. Der Endbenutzer kann Datenträger, die Teil eines Image sind, zum Zeitpunkt der Bereitstellung nicht entfernen, hat jedoch immer die Möglichkeit, während oder nach der Bereitstellung Datenträger hinzuzufügen, sollten diese benötigt werden.

### 1\.1 Hinzufügen eines Angebots

1. Melden Sie sich mit Ihrem Verkäuferkonto im [Veröffentlichungsportal][link-pubportal] an.
2. Rufen Sie die Registerkarte **Virtuelle Computer** des Veröffentlichtungsportals auf. Geben Sie im angezeigten Eingabefeld den Angebotsnamen ein, und erstellen Sie das Angebot.
3. Der Angebotsname ist in der Regel der Name des Produkts/Diensts, den Sie im Azure Marketplace verkaufen möchten.

### 1\.2 Definieren von SKUs
Nach dem Hinzufügen eines Angebots müssen Sie Ihre SKU(s) definieren bzw. angeben. Sie können mehrere Angebote definieren, und jedes Angebot kann mehrere SKUs enthalten. Wenn ein Angebot in die Stagingphase überführt wird, werden alle SKUs mit einbezogen.

1. **Fügen Sie eine SKU hinzu.** Die SKU benötigt eine ID, die in der URL verwendet wird. Die ID muss in Ihrem Veröffentlichungsprofil eindeutig sein. Allerdings besteht keine Gefahr eines ID-Konflikts mit anderen Herausgebern.

> [AZURE.NOTE]Angebots- und SKU-ID werden in der Angebots-URL im Marketplace angezeigt.

2. **Fügen Sie eine zusammenfassende Beschreibung für die SKU hinzu.** Da diese Informationen von Menschen gelesen werden, die das Produkt nutzen, sollten sie leicht verständlich formuliert werden. Vor dem Staging besteht keine Notwendigkeit, die Informationen zu sperren. Bis dahin können sie nach Belieben bearbeitet werden.
3. Erwerben Sie die genehmigten Versionen von Windows Server über die vorgeschlagenen Links, wenn Sie mit Windows-basierten SKUs arbeiten.

## 2\. Erstellen von Azure-kompatiblen VHDs (Linux-basiert)
Im Mittelpunkt des folgenden Abschnitts stehen bewährte Verfahren zum Erstellen eines Linux-basierten VM-Images für den Microsoft Azure Marketplace. Eine schrittweise exemplarische Vorgehensweise finden Sie in der folgenden Dokumentation: [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält.][link-azure-vm-1]

> [AZURE.TIP]Viele der folgenden Schritte (z. B. Agentinstallation, Festlegen der Kernel-Boot-Parameter) wurden für Linux-Images, die im Microsoft Azure-Image-Katalog verfügbar sind, bereits erledigt. Zur Zeitersparnis können Sie also eines dieser Images als Grundlage verwenden, anstatt ein nicht für Azure konzipiertes Linux-Image zu konfigurieren.

### 2\.1 Auswählen der richtigen VHD-Größe
Veröffentlichte SKUs (VM-Images) sollten für alle VM-Größen ausgelegt sein, die die Anzahl von Datenträgern für die SKU unterstützen. Sie können Richtlinien zu empfohlenen Größen ausgeben. Diese werden jedoch als Empfehlungen betrachtet und nicht zwangsweise durchgesetzt.

1. Linux-Betriebssystem-VHD: Die Linux-Betriebssystem-VHD in Ihrem VM-Image sollte als VHD mit festem Format mit 30 GB bis 50 GB erstellt werden. Sie darf nicht kleiner sein als 30 GB. Liegt die physische Größe unter der VHD-Größe, sollte die VHD von geringer Dichte sein. Linux-VHDs, die größer sind als 50 GB, werden je nach Einzelfall berücksichtigt. Wenn bereits eine VHD in einem anderen Format vorhanden ist, können Sie das [Format mit dem PowerShell-Cmdlet „Convert-VHD“ ändern][link-technet-1].
2. Datenträger-VHD: Datenträger können bis zu 1 TB groß sein. Datenträger-VHDs sollten als VHDs mit festem Format erstellt werden, jedoch auch von geringer Dichte sein. Bedenken Sie bei der Entscheidung über die Datenträgergröße, dass Endbenutzer die Größe von VHDs in einem Image nicht verändern können.

### 2\.2 Installieren des neuesten Azure Linux-Agent
Stellen Sie bei der Vorbereitung der Betriebssystem-VHD sicher, dass der neueste [Azure Linux-Agent][link-azure-vm-2] installiert ist. Verwenden Sie RPM- oder DEB-Pakete. Das Paket trägt häufig den Namen „walinuxagent“ oder „WALinuxAgent“. Überprüfen Sie aber zur Sicherheit Ihre Verteilung. Der Agent stellt wichtige Funktionen für IaaS-Bereitstellungen von Linux in Azure bereit, wie etwa Funktionen zur Bereitstellung von virtuellen Computern und Netzwerkfunktionen.

Der Agent kann auf verschiedene Weise konfiguriert werden. Um höchstmögliche Kompatibilität zu sichern, wird jedoch eine allgemeine Agent-Konfiguration empfohlen. Die manuelle Installation des Agent ist zwar möglich, es wird jedoch ausdrücklich empfohlen, die vorkonfigurierten Pakete aus Ihrer Verteilung zu verwenden (sofern verfügbar).

Wenn Sie sich für eine manuelle Installation des Agent über das [GitHub-Repository][link-github-waagent] entscheiden, kopieren Sie zunächst die Datei „waagent“ nach „/usr/sbin“, und führen Sie dann die folgenden Befehle als root-Benutzer aus:

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

Die Agent-Konfigurationsdatei wird unter „/etc/waagent.conf“ abgelegt.

### 2\.3 Überprüfen, ob die erforderlichen Bibliotheken enthalten sind
Außer dem Azure Linux-Agent müssen auch die folgenden Bibliotheken enthalten sein:

1. Im Kernel muss mindestens [Linux-Integrationsdienste][link-intsvc] Version 3.0 aktiviert sein. Informationen finden Sie unter [Linux-Kernelanforderungen](../virtual-machines/virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements).
2. [Kernel-Patch](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) für Azure-E/A-Stabilität (wahrscheinlich für neuere Kernels nicht erforderlich, sollte jedoch überprüft werden)
3. [Python][link-python] 2.6 oder höher
4. Python] pyasn1-Paket, falls nicht bereits installiert
5. [OpenSSL][link-openssl] (v1. 0 oder höher empfohlen)

### 2\.4 Einrichten von Datenträgerpartitionen
Die Verwendung von Logical Volume Manager wird **nicht** empfohlen. Erstellen Sie eine einzelne Stammpartition für den Betriebssystemdatenträger. Verwenden Sie keine Swap-Partition im Betriebssystem oder auf dem Datenträger. Es wird empfohlen, eine vorhandene Swap-Partition zu entfernen, selbst wenn sie nicht in „/etc/fstab“ eingebunden ist. Im Bedarfsfall kann vom Linux-Agent eine Swap-Partition auf der lokalen Datenträgerressource (/dev/sdb) erstellt werden.

### 2\.5 Hinzufügen erforderlicher Parameter aus der Kernel-Boot-Zeile
Der Kernel-Boot-Zeile müssen auch folgende Parameter hinzugefügt werden:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

Damit wird gewährleistet, dass der Azure-Support Kunden bei Bedarf mit der Ausgabe über die serielle Konsole unterstützen kann. Außerdem sorgt dies für ein adäquates Timeout für das Einbinden des Betriebssystemdatenträgers aus dem Cloudspeicher. Selbst wenn Ihre SKU Endbenutzer am direkten SSH-Zugriff auf einen virtuellen Computer hindert, muss die Ausgabe über die serielle Konsole aktiviert sein.

### 2\.6 Standardmäßiges Einbeziehen des SSH-Servers
Es wird dringend empfohlen, SSH für den Endbenutzer zu aktivieren. Wenn der SSH-Server aktiviert ist, fügen Sie SSH-Keep-Alive mit der folgenden Option zur Datei „sshd config“ hinzu: ClientAliveInterval 180. Der empfohlene Wert ist 180, der zulässige Bereich reicht jedoch von 30 bis 235. Nicht alle Anwendungen verlangen, dem Endbenutzer direkten SSH-Zugriff auf den virtuellen Computer zu gewähren. Wird SSH ausdrücklich blockiert, erübrigt sich die Festlegung der Option „ClientAliveInterval“.

### 2\.7 Erfüllen von Netzwerkanforderungen
Im Folgenden werden die Netzwerkanforderungen für ein Azure-kompatibles Linux-VM-Image erläutert.

- In vielen Fällen ist es am besten, NetworkManager zu deaktivieren. Eine Ausnahme stellen CentOS 7.x-basierte Systeme (und Ableitungen) dar. Bei diesen Systemen muss NetworkManager aktiviert bleiben.
- Die Netzwerkkonfiguration sollte über die ifup-/ifdown-Skripte gesteuert werden können. Der Linux-Agent kann diese Befehle zum Neustarten der Netzwerke während der Bereitstellung verwenden.
- Eine benutzerdefinierte Netzwerkkonfiguration darf nicht stattfinden. Im letzten Schritt sollte die Datei „resolv.conf“ gelöscht werden. Dies geschieht in der Regel bei der Aufhebung der Bereitstellung (siehe [Benutzerhandbuch für Azure Linux-Agent](../virtual-machines/virtual-machines-linux-agent-user-guide/)). Mithilfe des folgenden Befehls können Sie diesen Schritt auch manuell ausführen:

        rm /etc/resolv.conf

- Das Netzwerkgerät muss beim Start in Betrieb genommen werden und DHCP verwenden.
- IPv6 6 wird von Azure nicht unterstützt. Wenn diese Eigenschaft aktiviert ist, funktioniert sie nicht.

### 2\.8 Anwenden bewährter Sicherheitsverfahren
Für SKUs im Azure Marketplace ist es unabdingbar, dass hinsichtlich der Sicherheit bewährte Verfahren eingehalten werden. Dazu zählt Folgendes:

- Installieren Sie alle Sicherheitspatches für Ihre Verteilung.
- Befolgen Sie die Richtlinien für die Verteilungssicherheit.
- Erstellen Sie keine Standardkonten, die unverändert bleiben, über Bereitstellungsinstanzen hinweg.
- Löschen Sie bash-Verlaufseinträge.
- Beziehen Sie iptables-Software (Firewall) ein, aktivieren Sie jedoch keine Regeln. Dadurch ermöglichen Sie Kunden ein nahtloses Standarderlebnis. Kunden, die eine VM-Firewall für zusätzliche Konfigurationsschritte verwenden möchten, können die iptables-Regeln entsprechend ihren besonderen Anforderungen konfigurieren.

### 2\.9 Generalisieren des Images
Alle Images im Azure Marketplace müssen allgemein wiederverwendbar sein. Hierzu ist es erforderlich, bestimmte Besonderheiten der Konfiguration zu entfernen. In Linux muss dazu die Bereitstellung der Betriebssystem-VHD aufgehoben werden.

Der Linux-Befehl für die Aufhebung der Bereitstellung lautet:

        # waagent -deprovision

Dieser Befehl führt automatisch die folgenden Aktionen aus:

- Entfernt die Nameserver-Konfiguration in „/etc/resolv.conf“.
- Entfernen von zwischengespeicherten DHCP-Clientleases
- Setzt den Hostnamen auf "localhost.localdomain" zurück

Es wird empfohlen, in der Konfigurationsdatei (/etc/waagent.conf) festzulegen, dass auch die folgenden Aktionen ausgeführt werden:

- „Provisioning.RegenerateSshHostKeyPair“ in der Konfigurationsdatei auf „y“ festlegen, um alle SSH-Hostschlüssel zu entfernen
- „Provisioning.DeleteRootPassword“ in der Konfigurationsdatei auf „y“ festlegen, um das Stammkennwort aus „/etc/shadow“ zu entfernen Eine Dokumentation zu den Inhalten der Konfigurationsdatei finden Sie im Abschnitt „KONFIGURATION“ der README-Datei auf der Agent Github-Repository-Seite ([https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent), führen Sie hier einen Bildlauf nach unten durch).  

Damit ist das Generalisieren des virtuellen Linux-Computers abgeschlossen. Fahren Sie den virtuellen Computer über das Azure-Portal, die Befehlszeile oder über den virtuellen Computer herunter. Fahren Sie nach dem Herunterfahren mit Schritt 3.4 fort.

## 3\. Erstellen einer Azure-kompatiblen VHD (Windows-basiert)
Im Mittelpunkt des folgenden Abschnitts stehen die Schritte zum Erstellen einer SKU auf Basis von Windows Server für den Microsoft Azure Marketplace.

### 3\.1. Verwenden der richtigen Basis-VHDs
Die Betriebssystem-VHD für das VM-Image muss auf einem für Microsoft Azure genehmigten Basisimage beruhen, das Windows Server oder SQL Server enthält.

Erstellen Sie zunächst einen virtuellen Computer aus einem der folgenden Images, die sich im [Microsoft Azure-Portal][link-azure-portal] befinden:

- Windows Server 2012 ([R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
- SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Diese Verknüpfungen finden Sie auch im Veröffentlichungsportal auf der SKU-Seite.

> [AZURE.TIP]Wenn Sie das aktuelle Azure-Verwaltungsportal oder PowerShell verwenden, sind am 8. September 2014 und später veröffentlichte Windows Server-Images genehmigt.


### 3\.2 Erstellen des virtuellen Windows-Computers
Im Microsoft Azure-Portal können Sie den virtuellen Computer auf der Grundlage eines genehmigten Basisimages in nur wenigen einfachen Schritten erstellen. Im Folgenden finden Sie einen Überblick über den Prozess.

1. Wählen Sie auf der Seite des Basisimages **VM erstellen** aus, um zum neuen [Microsoft Azure-Portal][link-azure-portal] zu gelangen.

    ![Abbildung][img-acom-1]

2. Melden Sie sich mit dem Microsoft-Konto (MSA) und -Kennwort für das gewünschte Azure-Abonnement beim Portal an.
3. Befolgen Sie die Anweisungen zum Erstellen eines virtuellen Computers auf der Grundlage des von Ihnen ausgewählten Basisimages. Sie müssen mindestens einen Hostnamen (Name des Computers), einen Benutzernamen (registrierter Admin-Benutzer) und ein Kennwort für den virtuellen Computer angeben.

    ![Abbildung][img-portal-vm-create]

4. Wählen Sie die Größe des bereitzustellenden virtuellen Computers aus.

    a. Wenn Sie vorhaben, die VHD lokal zu entwickeln, spielt die Größe keine Rolle. Die Verwendung eines kleineren virtuellen Computers wird empfohlen.

    b. Wenn Sie vorhaben, das Image in Azure zu entwickeln, sollten Sie eine der empfohlenen VM-Größen für das ausgewählte Image verwenden.

    c. Preisinformationen finden Sie im Portal in der Auswahl „Empfohlener Tarif“. Hier sind die drei empfohlenen Größen angegeben, die vom Herausgeber bereitgestellt werden. (In diesem Fall ist der Herausgeber Microsoft.)

    ![Abbildung][img-portal-vm-size]

5. Legen Sie Eigenschaften fest.

    a. Zur schnellen Bereitstellung können Sie die Standardwerte für die Eigenschaften unter **Optionale Konfiguration** und **Ressourcengruppe** beibehalten.

    b. Unter „Speicherkonto“ können Sie auf Wunsch das Speicherkonto auswählen, in dem die Betriebssystem-VHD gespeichert wird.

    c. Unter „Ressourcengruppe“ können Sie auf Wunsch die logische Gruppe auswählen, in der der virtuelle Computer abgelegt werden soll.
6. Wählen Sie den **Speicherort** für die Bereitstellung aus.

    a. Wenn Sie vorhaben, die VHD lokal zu entwickeln, spielt der Ort keine Rolle, da Sie das Image später in Azure hochladen.

    b. Wenn Sie planen, das Image in Azure zu entwickeln, sollten Sie gleich zu Anfang eine der US-amerikanischen Microsoft Azure-Regionen auswählen. Hierdurch beschleunigen Sie den VHD-Kopiervorgang, den Microsoft für Sie ausführt, wenn Sie Ihr Image zur Zertifizierung einsenden.

    ![Abbildung][img-portal-vm-location]

7. Klicken Sie auf **Erstellen**. Die Bereitstellung des virtuellen Computers beginnt. Die Bereitstellung ist innerhalb weniger Minuten abgeschlossen, und Sie können mit dem Erstellen des Images für Ihre SKU beginnen.

### 3\.3 Entwickeln der VHD in der Cloud
Es wird nachdrücklich empfohlen, die VHD in der Cloud mithilfe des Remotedesktopprotokolls (RDP) zu entwickeln. Die Verbindung zum RDP wird mit dem Benutzernamen und dem Kennwort hergestellt, die Sie während der Bereitstellung angegeben haben.

> [AZURE.IMPORTANT]\: Wenn Sie die VHD lokal entwickeln (was nicht empfohlen wird), finden Sie unter [Erstellen eines lokalen Images eines virtuellen Computers für Azure Marketplace](marketplace-publishing-vm-image-creation-on-premise.md) weitere Informatione. **Beim Entwickeln in der Cloud ist das Herunterladen der VHD NICHT notwendig**.


**Herstellen der Verbindung mit dem [Microsoft Azure-Portal][link-azure-portal] über RDP**

1. Wählen Sie **Durchsuchen** und dann **VMs** aus.
2. Das Blatt „VMs“ wird geöffnet. Stellen Sie sicher, dass der virtuelle Computer, mit dem Sie eine Verbindung herstellen möchten, ausgeführt wird, und wählen Sie ihn in der Liste der bereitgestellten virtuellen Computer aus.
3. Ein Blatt mit der Beschreibung des ausgewählten virtuellen Computers wird geöffnet. Klicken Sie am oberen Rand auf **Verbinden**.
4. Sie werden aufgefordert, den Benutzernamen und das Kennwort einzugeben, den bzw. das Sie zum Zeitpunkt der Bereitstellung festgelegt haben.

**Herstellen der Verbindung über RDP mit PowerShell**

Zum Herunterladen einer Remotedesktopdatei auf einen lokalen Computer können Sie das [Get-AzureRemoteDesktopFile-Cmdlet][link-technet-2] verwenden. Hierzu müssen Sie den Namen des Diensts und den Namen des virtuellen Computers kennen. Wenn Sie den virtuellen Computer im [Microsoft Azure-Portal][link-azure-portal] erstellt haben, finden Sie diese Informationen unter „VM-Eigenschaften“.

1. Wählen Sie im Microsoft Azure-Portal **Durchsuchen** und dann **VMs** aus.
2. Das Blatt **Virtuelle Computer** wird geöffnet. Wählen Sie den von Ihnen bereitgestellten virtuellen Computer in der Liste der virtuellen Computer aus.
3. Ein Blatt mit der Beschreibung des ausgewählten virtuellen Computers wird geöffnet.
4. Klicken Sie auf **Eigenschaften**.
5. Der erste Teil des Domänennamens ist der Dienstname. Der Hostname ist der Name des virtuellen Computers.

    ![Abbildung][img-portal-vm-rdp]

6. Das Cmdlet zum Herunterladen der RDP-Datei für den erstellten virtuellen Computer auf den lokalen Desktop des Administrators lautet wie folgt:

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Weitere Informationen zu RDP finden Sie im MSDN-Artikel [Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Konfigurieren eines virtuellen Computers und Erstellen der SKU**

Verwenden Sie nach dem Herunterladen der Betriebssystem-VHD Hyper-V, und konfigurieren Sie einen virtuellen Computer, um mit dem Erstellen einer SKU zu beginnen. Eine ausführliche Anleitung finden Sie unter folgendem TechNet-Link: [Installieren von Hyper-V und Erstellen eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

### 3\.4 Auswählen der richtigen VHD-Größe
Die Windows-Betriebssystem-VHD in Ihrem VM-Image sollte als VHD mit festem Format mit 128 GB erstellt werden.

Beträgt die physische Größe weniger als 128 GB, sollte die VHD von geringer Dichte sein. Die bereitgestellten Windows- und SQL Server-Images erfüllen diese Anforderungen. Ändern Sie weder das Format noch die Größe der VHD.

Datenträger können bis zu 1 TB groß sein. Bedenken Sie bei der Entscheidung über die Datenträgergröße, dass Endbenutzer die Größe von VHDs in einem Image zum Zeitpunkt der Bereitstellung nicht verändern können. Datenträger-VHDs sollten als VHDs mit festem Format erstellt werden, jedoch auch von geringer Dichte sein. Datenträger können leer sein oder Daten enthalten.


### 3\.5 Installieren der neuesten Windows-Patches
Die Basisimages enthalten die neuesten, bis zum Veröffentlichungsdatum erschienenen Patches. Vergewissern Sie sich vor der Veröffentlichung der von Ihnen erstellten Betriebssystem-VHD, dass Windows Update ausgeführt wurde und dass alle kritischen und wichtigen Sicherheitsupdates installiert wurden.

### 3\.6 Konfigurieren zusätzlicher Einstellungen und Planen von Aufgaben nach Bedarf
Falls zusätzliche Einstellungen konfiguriert werden müssen, können Sie eine geplante Aufgabe einrichten, die beim Start ausgeführt wird. So ist es möglich, nach der Bereitstellung des virtuellen Computers letzte Änderungen daran vorzunehmen.

- Es gilt als bewährtes Verfahren, die Aufgabe so zu konfigurieren, dass sie sich nach der erfolgreichen Ausführung selbst löscht.
- Die Konfiguration sollte sich nur auf die Laufwerke C:\\ oder D:\\ stützen, denn diese beiden Laufwerke sind die einzigen, die mit Sicherheit immer vorhanden sind. C:\\ ist der Betriebssystemdatenträger und D:\\ ist der temporäre lokale Datenträger.

### 3\.7 Generalisieren des Images
Alle Images im Azure Marketplace müssen allgemein wiederverwendbar sein. Anders ausgedrückt: Die Betriebssystem-VHD muss verallgemeinert werden.

- Unter Windows sollte für das Image eine Systemvorbereitung mit „Sysprep“ durchgeführt werden. Einstellungen, die den Befehl „Sysprep“ nicht unterstützen, dürfen nicht konfiguriert werden.
- Sie können den unten angegebenen Befehl über das Verzeichnis „%windir%\\System32\\Sysprep“ ausführen.

        sysprep.exe /generalize /oobe /sshutdown

  Anleitungen zur Systemvorbereitung des Betriebssystems mit „Sysprep“ finden Sie in Schritt 1 des folgenden MSDN-Artikels: [Erstellen und Hochladen einer Windows Server-VHD in Azure](../virtual-machines/virtual-machines-create-upload-vhd-windows-server/).

## 4\. Bereitstellen eines virtuellen Computers auf Basis der VHDs
Wenn die VHDs, die verallgemeinerte Betriebssystem-VHD und null oder mehr Datenträger-VHDs in ein Azure-Speicherkonto hochgeladen wurden, können Sie sie als Benutzer-VM-Image registrieren, mit dem Sie Tests durchführen. Hinweis: Da die Betriebssystem-VHD verallgemeinert wurde, ist eine direkte Bereitstellung des virtuellen Computers durch Angabe der URL der VHD nicht möglich.

Weitere Informationen zu VM-Images finden Sie in folgenden Blogbeiträgen:

- [VM-Image](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell How To](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/) (VM Image PowerShell – Vorgehensweisen; in englischer Sprache)
- [About VM Images in Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx) (Informationen zu VM-Images in Azure; in englischer Sprache)

### 4\.1 Erstellen eines Benutzer-VM-Images
Wenn Sie ein Benutzer-VM-Image auf Basis Ihrer SKU erstellen und mehrere virtuelle Computer bereitstellen möchten, müssen Sie VHDs mithilfe der REST-API [Create VM Image](http://msdn.microsoft.com/library/azure/dn775054.aspx) als VM-Image registrieren.

Mit dem Cmdlet „Invoke-WebRequest“ können Sie ein VM-Image aus PowerShell erstellen. Das folgende PowerShell-Skript zeigt, wie Sie ein VM-Image mit einem Betriebssystemdatenträger und einem Datenträger erstellen. Beachten Sie, dass die PowerShell-Sitzung bereits eingerichtet sein und ein Abonnement bestehen sollte.

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


Durch Ausführen dieses Skripts wird ein Benutzer-VM-Image mit dem Namen erstellt, den Sie im ImageName-Parameter „myVMImage“ angegeben haben. Es besteht aus einem Betriebssystemdatenträger und einem Datenträger.

Diese API ist ein asynchroner Vorgang und gibt den Status „202“ (zulässig) zurück. Wenn Sie sehen möchten, ob das VM-Image erstellt wurde, müssen Sie den Vorgangsstatus abfragen. Der Wert „x-ms-request-id“ in der zurückgegebenen Antwort ist die Vorgangs-ID. Diese ID muss im Parameter „$opId“ (siehe unten) festgelegt werden.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

Verwenden Sie das folgende Skript zum Erstellen eines Benutzer-VM-Image aus einer Betriebssystem-VHD und einem zusätzlichen leeren Datenträger (die VHD für diesen Datenträger haben Sie nicht erstellt) mit der API „Create VM Image“:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

Durch Ausführen dieses Skripts wird ein Benutzer-VM-Image mit dem Namen erstellt, den Sie im ImageName-Parameter „myVMImage“ angegeben haben. Es besteht aus einem Betriebssystemdatenträger und einem Datenträger.

Diese API ist ein asynchroner Vorgang und gibt den Status „202“ (zulässig) zurück. Wenn Sie sehen möchten, ob das VM-Image erstellt wurde, müssen Sie den Vorgangsstatus abfragen. Der Wert „x-ms-request-id“ in der zurückgegebenen Antwort ist die Vorgangs-ID. Diese ID muss im Parameter „$opId“ (siehe unten) festgelegt werden.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

Verwenden Sie das folgende Skript zum Erstellen eines Benutzer-VM-Image aus einer Betriebssystem-VHD und einem zusätzlichen leeren Datenträger (die VHD für diesen Datenträger haben Sie nicht erstellt) mit der API „Create VM Image“:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

Durch Ausführen dieses Skripts wird ein Benutzer-VM-Image mit dem Namen erstellt, den Sie im ImageName-Parameter „myVMImage“ angegeben haben. Es besteht aus einem Betriebssystemdatenträger, basierend auf der von Ihnen übergebenen VHD, und einem leeren Datenträger mit 32 GB.

### 4\.2 Bereitstellen eines virtuellen Computers aus einem Benutzer-VM-Image
Zum Bereitstellen eines virtuellen Computers aus einem Benutzer-VM-Image können Sie das aktuelle [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder PowerShell verwenden.

**Bereitstellen eines virtuellen Computers aus dem aktuellen Azure-Verwaltungsportal**

1. Wechseln Sie zu **Neu > Compute > VM > Aus Katalog**.

    ![Abbildung][img-manage-vm-new]

2. Wechseln Sie zu **Meine Images**, und wählen Sie das VM-Image aus, aus dem ein virtueller Computer bereitgestellt werden soll.
  1. Achten Sie genau darauf, welches Image Sie auswählen, da unter „Meine Images“ sowohl Betriebssystemimages als auch VM-Images aufgeführt werden.
  2. Die Datenträgeranzahl lässt Rückschlüsse auf die Art des bereitgestellten Images zu, da die Mehrheit der VM-Images mehr als einen Datenträger enthält. Es ist jedoch möglich, dass ein VM-Image nur über einen Betriebssystemdatenträger verfügt. Für die Anzahl der Datenträger wird in diesem Fall „1“ angegeben.

    ![Abbildung][img-manage-vm-select]

3. Folgen Sie den Schritten im Assistenten zum Erstellen virtueller Computer, und geben Sie den Namen und die Größe des virtuellen Computers, den Ort, den Benutzernamen und das Kennwort an.

**Bereitstellen eines virtuellen Computers mit PowerShell**

Zum Bereitstellen eines großen virtuellen Computers aus dem soeben erstellten verallgemeinerten VM-Image können die folgenden Cmdlets verwendet werden.

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\. Erwerb der Zertifizierung für das VM-Image
Die nächste Aufgabe in der Vorbereitung des VM-Image für den Azure Store ist die Zertifizierung.

Dieser Prozess umfasst mehrere Schritte: Ausführen eines speziellen Zertifizierungstools, Hochladen der Ergebnisse der Überprüfung in den Azure-Container, in dem sich die VHDs befinden, Hinzufügen eines Angebots, Definieren der SKU und Einsenden des VM-Images zur Zertifizierung.

### 5\.1 Herunterladen und Ausführen des Microsoft Azure-Zertifizierungstools
Das Microsoft Azure-Zertifizierungstool wird für einen laufenden virtuellen Computer ausgeführt, bereitgestellt aus Ihrem Benutzer-VM-Image, um sicherzustellen, dass das VM-Image mit Microsoft Azure kompatibel ist. Das Tool überprüft, ob die Leitlinien und Anforderungen in Bezug auf die Vorbereitung Ihrer VHD eingehalten und erfüllt wurden. Die Ausgabe des Tools ist ein Kompatibilitätsbericht, der beim Anfordern der Zertifizierung in das Veröffentlichungsportal hochgeladen werden muss.

Das Zertifizierungstool funktioniert mit virtuellen Windows- und Linux-Computern. Die Verbindung mit virtuellen Windows-Computern wird über PowerShell hergestellt, mit virtuellen Linux-Computern über SSH.Net.

1. Laden Sie das Zertifizierungstool zunächst von der [Microsoft-Downloadwebsite][link-msft-download] herunter.
2. Öffnen Sie das Zertifizierungstool, und klicken Sie auf die Schaltfläche **Neuen Test starten**.
3. Geben Sie auf dem Bildschirm **Testinformationen** einen Namen für den Testlauf ein.
4. Geben Sie an, ob Ihr virtueller Computer unter Linux oder Windows ausgeführt wird. Wählen Sie je nach Auswahl die nachfolgenden Optionen aus.

### **Verbinden des Zertifizierungstools mit einem Linux-VM-Image**

1. Wählen Sie den SSH-Authentifizierungsmodus aus: Kennwort oder Schlüsseldatei.
2. Geben Sie bei kennwortbasierter Authentifizierung den DNS-Namen, den Benutzernamen und das Kennwort an.
3. Bei Authentifizierung per Schlüsseldatei geben Sie DNS, Benutzername und Speicherort des privaten Schlüssels an.

  ![Kennwortauthentifizierung eines Linux-VM-Images][img-cert-vm-pswd-lnx]

  ![Authentifizierung per Schlüsseldatei eines Linux-VM-Images][img-cert-vm-key-lnx]

### **Verbinden des Zertifizierungstools mit einem Windows-VM-Image**

1. Geben Sie den vollqualifizierten DNS-Namen des virtuellen Computers ein, z. B. MyVMName.ClOudapp.net.
2. Geben Sie den Benutzernamen und das Kennwort ein.

  ![Kennwortauthentifizierung eines Windows-VM-Images][img-cert-vm-pswd-win]

Klicken Sie nach Auswahl der richtigen Optionen für Ihr Linux- oder Windows-VM-Image auf **Verbindung testen**, um sicherzustellen, dass von SSH.Net bzw. PowerShell eine gültige Verbindung für Testzwecke aufgebaut wurde. Sobald eine Verbindung besteht, klicken Sie auf **Weiter**, um den Test zu starten.

Nach Abschluss des Tests erhalten Sie die Ergebnisse (Erfolgreich/Fehler/Warnung) für jedes einzelne Testelement.

![Testfälle für Linux-VM-Images][img-cert-vm-test-lnx]

![Testfälle für Windows-VM-Images][img-cert-vm-test-win]

Wenn einer dieser Tests fehlschlägt, wird das Image nicht zertifiziert. Überprüfen Sie die Anforderungen, und nehmen Sie ggf. Änderungen vor.

Im Anschluss an den automatisierten Test werden Sie zur Eingabe zusätzlicher Informationen zum VM-Image über einen Bildschirm mit einem Fragebogen aufgefordert. Beantworten Sie die Fragen, und klicken Sie auf **Weiter**.

![Fragebogen im Zertifizierungstool][img-cert-vm-questionnaire]

![Fragebogen im Zertifizierungstool][img-cert-vm-questionnaire-2]

Wenn Sie den Fragebogen ausgefüllt haben, können Sie zusätzliche Informationen wie SSH-Zugriffsinformationen für das Linux-VM-Image und Erklärungen für fehlgeschlagene Bewertungen bereitstellen. Sie können die Testergebnisse und Protokolldateien für die ausgeführten Testfälle sowie Ihre Antworten aus dem Fragebogen herunterladen. Speichern Sie die Ergebnisse im gleichen Container wie die VHDs.

![Zertifizierungstestergebnisse speichern][img-cert-vm-results]

### 5\.2 Erstellen des SAS (Shared Access Signature)-URI (Uniform Resource Identifier) für die VM-Images

Im Laufe des Veröffentlichungsprozesses geben Sie die URIs an, die zu jeder einzelnen VHD führen, die Sie für Ihre SKU erstellt haben. Microsoft benötigt während des Zertifizierungsprozesses Zugriff auf diese VHDs. Sie müssen daher für jede VHD einen SAS-URI (Shared Access Signature Uniform Resource Identifier) erstellen. Dies ist der URI, der auf der Registerkarte **Images** im Veröffentlichungsportal eingegeben werden muss.

Der erstellte SAS-URI sollte die folgenden Anforderungen erfüllen:

- Beim Generieren von SAS-URIs für die VHDs sind Listen- und Leseberechtigungen ausreichend. Gewähren Sie keinen Schreib- oder Löschzugriff.
- Die Zugriffsdauer sollte mindestens sieben Werktage betragen, gerechnet ab dem Erstellungsdatum des SAS-URI.
- Geben Sie eine Uhrzeit 15 Minuten vor der aktuellen Uhrzeit an, um unmittelbare Fehler aufgrund von Zeitabweichungen zu vermeiden.

Eine Anleitung zum Erstellen eines SAS-URI finden Sie unter [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell][link-azure-1] und [Shared Access Signatures, Teil 2: Erstellen und Verwenden einer SAS mit dem Blob-Dienst][link-azure-2].

Statt einen gemeinsam verwendeten Zugriffsschlüssel mithilfe von Code zu erstellen, können Sie auch Speichertools wie etwa [Azure-Speicher-Explorer][link-azure-codeplex] verwenden.

**Generieren eines gemeinsam verwendeten Zugriffsschlüssels mit Azure-Speicher-Explorer**

1. Laden Sie [Azure-Speicher-Explorer][link-azure-codeplex] 6 oder höher aus CodePlex herunter.
2. Öffnen Sie die Anwendung nach Abschluss der Installation.
3. Klicken Sie auf **Konto hinzufügen**.

    ![Abbildung][img-azstg-add]

4. Geben Sie den Speicherkontonamen, den Speicherkontoschlüssel und die Domäne der Speicherendpunkte an. Wählen Sie **nicht** „Https verwenden“.

    ![Abbildung][img-azstg-setup-1]

5. Nach der Ausführung der oben genannten Schritte ist Azure-Speicher-Explorer mit Ihrem bestimmten Speicherkonto verbunden. Anschließend werden alle Container im Speicherkonto angezeigt. Wählen Sie den Container, in den Sie die VHD-Datei mit dem Betriebssystemdatenträger (auch Datenträger, wenn sie für Ihr Szenario gelten) kopiert haben.

    ![Abbildung][img-azstg-setup-2]

6. Nach der Auswahl des Blob-Containers startet die Azure-Speicher-Explorer-Anwendung und zeigt die Dateien im Container an. Wählen Sie die Image-Datei (.vhd), die gesendet werden soll.

    ![Abbildung][img-azstg-setup-3]

7. Nachdem Sie die Datei (.vhd) im Container ausgewählt haben, klicken Sie auf die unten hervorgehobene Registerkarte **Sicherheit**.

    ![Abbildung][img-azstg-setup-4]

8. Nach dem Klicken auf die Registerkarte **Sicherheit** wird folgendes Dialogfeld angezeigt. Behalten Sie die Standardwerte auf der Registerkarte „Zugriffsebene“ bei, und klicken Sie auf die Registerkarte „Shared Access Signatures“.

    ![Abbildung][img-azstg-setup-5]

9. Führen Sie auf dieser Registerkarte die unten beschriebenen Schritte aus, um eine SAS-URL für das VHD-Image zu generieren.

    ![Abbildung][img-azstg-setup-6]

    a. Zugriff erlaubt ab -> Wählen Sie bei UTC-Zeit das gestrige Datum aus. Beispiel: Ist heute der 06.10.2014, wählen Sie hier den 05.10.2014 aus.

    b. Zugriff erlaubt -> Geben Sie mindestens sieben bis acht Tage an.

    c. Erlaubte Aktionen -> Stellen Sie sicher, dass sowohl Listen- als auch Leseberechtigungen angegeben sind.

    d. Wenn Sie die VHD-Datei ordnungsgemäß ausgewählt haben, wird beim Blob-Namen Ihre Datei mit der Erweiterung „.vhd“ angezeigt.

    e. Klicken Sie auf „Signatur generieren“.

    f. Überprüfen Sie im generierten SAS-URI Folgendes (wie oben angegeben):

    - i. Vergewissern Sie sich, dass die URL nicht mit „https“ beginnt.
    - ii. Der URI enthält den Imagedateinamen „.vhd“.
    - iii. Am Ende der Signatur muss „=rl“ angefügt sein (dies weist darauf hin, dass Lese- und Listenberechtigungen gewährt wurden). Überprüfen Sie, ob der generierte SAS-URI funktioniert, indem Sie auf „In Browser testen“ klicken. Der Downloadprozess sollte gestartet werden.
10. Kopieren Sie den SAS-URI. Dies ist der URI, der in das Veröffentlichungsportal eingefügt wird.
11. Wiederholen Sie diese Schritte für jede VHD in der SKU.

### 5\.3 Angeben von Informationen zum VM-Image und Anfordern der Zertifizierung im Veröffentlichungsportal
Nach dem Erstellen des Angebots und der SKU müssen Sie die zu dieser SKU gehörigen Imagedetails eingeben.

1. Rufen Sie das [Veröffentlichungsportal][link-pubportal] auf, und melden Sie sich mit Ihrem Verkäuferkonto an.
2. Wählen Sie die Registerkarte **VM-Images**.
3. Die am oberen Seitenrand aufgeführte ID ist NICHT die SKU-ID, sondern die Angebots-ID.
4. Tragen Sie im Abschnitt „SKUs“ die Eigenschaften ein.

    ![Abbildung][img-pubportal-vm-skus]

5. Wählen Sie unter **Betriebssystemfamilie** den zu der Betriebssystem-VHD gehörigen Betriebssystemtyp aus.
6. Geben Sie unter **Betriebssystem** eine Beschreibung des Betriebssystems ein. Wählen Sie ein Format nach dem Schema Betriebssystemfamilie, Typ, Version und Updates. Beispiel: Windows Server Datacenter 2014 R2.
7. Wählen Sie drei empfohlene Größen für virtuelle Computer aus. Dies sind Empfehlungen, die für Endbenutzer auf dem Blatt „Tarif“ im Azure-Verwaltungsportal sichtbar sind, wenn sie Ihr Image erwerben und bereitstellen möchten.

  >[AZURE.NOTE]Es handelt sich nur um Empfehlungen. Der Endbenutzer kann eine beliebige VM-Größe auswählen, die sich für die in Ihrem Image festgelegten Datenträger eignet.

8. Geben Sie die Version ein. Das Versionsfeld kapselt eine semantische Version, um das Produkt und seine Updates zu identifizieren.
  -	Versionen sollten im Format „X.Y.Z“ angegeben werden. „X“, „Y“ und „Z“ sind dabei ganze Zahlen.
  -	Images in verschiedenen SKUs können verschiedene Haupt- und Nebenversionen enthalten.
  -	Bei Versionen innerhalb einer SKU sollte es sich nur um inkrementelle Änderungen handeln, die die Patchversion erhöhen („Z“ aus „X.Y.Z“).
9. Geben Sie als **URL der Betriebssystem-VHD** den SAS-URI ein, der für die Betriebssystem-VHD erstellt wurde.
10. Wenn dieser SKU Datenträger zugeordnet sind, wählen Sie die logische Gerätenummer (Logical Unit Number, LUN) aus, mit der dieser Datenträger bei der Bereitstellung eingebunden werden soll.
11. Geben Sie als URL der LUN X VHD den SAS-URI ein, der für die erste Daten-VHD erstellt wurde.
12.	Laden Sie Ihre Überprüfungs-/Testergebnisse aus dem Zertifizierungstool hoch.
13.	Klicken Sie auf **Zertifizierung anfordern**.
14.	Wiederholen Sie die Schritte 11, 12 und 13 für jede weitere Datenträger-VHD.

    ![Abbildung][img-pubportal-vm-skus-2]

## Nächster Schritt
Sobald Sie Ihre VM-Image-SKU zur Zertifizierung übermittelt haben, können Sie mit dem [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] fortfahren. In diesem Schritt des Veröffentlichungsvorgangs stellen Sie den Marketinginhalt, Preise und weitere vor **Schritt 3. Testen Ihres virtuellen Computers in Staging ** erforderliche Informationen bereit. In Schritt 3 testen Sie verschiedene Szenarien für Anwendungsfälle, bevor Sie das Angebot im Azure Marketplace veröffentlichen und zum Kauf bereitstellen.

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ../virtual-machines/virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ../virtual-machines/virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=Nov15_HO3-->