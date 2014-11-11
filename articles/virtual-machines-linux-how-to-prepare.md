<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Prepare a distribution" pageTitle="Prepare a distribution of Linux in Azure" metaKeywords="Azure Git CodePlex, Azure website CodePlex, Azure website Git" description="Learn how to use Git to publish to an Azure website, as well as enable continuous deployment from GitHub and CodePlex." metaCanonical="" services="virtual-machines" documentationCenter="" title="Prepare a Linux Virtual Machine for Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Vorbereiten eines virtuellen Linux-Computers für Azure

In Azure führt ein virtueller Computer (VM, Virtual Machine) das Betriebssystem aus, welches Sie beim Erstellen des virtuellen Computers auswählen. Azure speichert das Betriebssystem eines virtuellen Computers auf einer virtuellen Festplatte im VHD-Format (.vhd-Datei). Die virtuelle Festplatte (VHD, Virtual Hard Disk) eines Betriebssystems, das für eine Duplizierung vorbereitet wurde, wird als Image bezeichnet. In diesem Artikel wird beschrieben, wie Sie ein eigenes Image erstellen können, indem Sie eine .vhd-Datei mit einem von Ihnen installierten und generalisierten Betriebssystem hochladen. Weitere Informationen zu Datenträgern und Images in Azure finden Sie unter [Verwalten von Datenträgern und Images][Verwalten von Datenträgern und Images].

**Hinweis**: Wenn Sie einen virtuellen Computer erstellen, können Sie die Betriebssystemeinstellungen individuell anpassen, um das Ausführen Ihrer Anwendung zu ermöglichen. Die von Ihnen eingestellte Konfiguration wird auf einem Datenträger für den entsprechenden virtuellen Computer gespeichert. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers][Erstellen eines benutzerdefinierten virtuellen Computers].

**Wichtig**: Die Azure-Plattform-SLA bezieht sich nur dann auf die virtuellen Computer, die das Linux-Betriebssystem ausführen, wenn eine der bestätigten Distributionen mit den Konfigurationsdetails verwendet wird, die in [diesem Artikel][diesem Artikel] angegeben sind. Alle Linux-Distributionen, die in der Azure-Image-Galerie bereitgestellt werden, sind bestätigte Distributionen mit der erforderlichen Konfiguration.

## Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Elemente verfügen:

-   **Ein Verwaltungszertifikat** - Sie haben ein Verwaltungszertifikat für das Abonnement erstellt, für das Sie eine virtuelle Festplatte hochladen möchten. Sie haben das Zertifikat in eine .cer-Datei exportiert. Weitere Informationen zum Erstellen von Zertifikaten finden Sie unter [Erstellen eines Verwaltungszertifikats für Azure][Erstellen eines Verwaltungszertifikats für Azure].

-   **In einer .vhd-Datei installiertes Linux-Betriebssystem.** - Sie haben ein unterstütztes Linux-Betriebssystem auf einer virtuellen Festplatte installiert. Zum Erstellen von .vhd-Dateien stehen mehrere verschiedene Tools bereit. Sie können eine Virtualisierungslösung wie etwa Hyper-V verwenden, um die .vhd-Datei zu erstellen und das Betriebssystem zu installieren. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers][Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers].

    **Wichtig**: Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

    Eine Liste der bestätigten Distributionen finden Sie unter [Linux zu für Azure bestätigte Distributionen][Linux zu für Azure bestätigte Distributionen]. Alternativ dazu können Sie den Abschnitt am Ende dieses Artikels über die [Informationen zu nicht-bestätigten Distributionen][Informationen zu nicht-bestätigten Distributionen] lesen.

-   **Linux Azure-Befehlszeilentool.** Wenn Sie ein Linux-Betriebssystem zum Erstellen des Images nutzen, verwenden Sie dieses Tool zum Hochladen der VHD-Datei. Informationen zum Herunterladen des Tools finden Sie unter [Azure-Befehlszeilentools für Linux und Mac][Azure-Befehlszeilentools für Linux und Mac].

-   **Add-AzureVhd-Cmdlet** ist Teil des Azure PowerShell-Moduls. Informationen zum Herunterladen dieses Moduls finden Sie unter [Azure-Downloads][Azure-Downloads]. Referenzinformationen hierzu finden Sie unter [Add-AzureVhd][Add-AzureVhd].

Beachten Sie bei allen Distributionen Folgendes:

Der Azure Linux Agent (waagent) ist nicht mit NetworkManager kompatibel. Die Netzwerkkonfiguration sollte die Datei "ifcfg-eth0 " verwenden und über die ifup-/ifdown-Skripte gesteuert werden können. Waagent kann nicht installiert werden, wenn das NetworkManager-Paket erkannt wird.

Wegen eines Fehlers in den Linux-Kernel-Versionen unter 2.6.37 wird NUMA nicht unterstützt. Bei der manuellen Installation von waagent wird NUMA in der GRUB-Konfiguration für den Linux Kernel automatisch deaktiviert.

Außerdem setzt der Azure Linux Agent voraus, dass das python-pyasn1-Paket installiert ist.

Es wird empfohlen, dass Sie während der Installation keine SWAP-Partition erstellen. Sie können mit dem Azure Linux Agent eventuell SWAP-Raum konfigurieren. Zudem wird es nicht empfohlen, den gängigen Linux Kernel mit einem virtuellen Azure-Computer zu verwenden, ohne dass das entsprechende Patch auf der [Microsoft-Website][Microsoft-Website] zur Verfügung steht (die entsprechende Lösung enthält unter Umständen bereits eine Vielzahl der aktuellen Distributionen/Kernels).

Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Vorbereiten des hochzuladenden Images][Schritt 1: Vorbereiten des hochzuladenden Images]
-   [Schritt 2: Erstellen eines Speicherkontos in Azure][Schritt 2: Erstellen eines Speicherkontos in Azure]
-   [Schritt 3: Vorbereiten der Verbindung mit Azure][Schritt 3: Vorbereiten der Verbindung mit Azure]
-   [Schritt 4: Hochladen des Abbilds zu Azure][Schritt 4: Hochladen des Abbilds zu Azure]

## <span id="prepimage"></span> </a>Schritt 1: Vorbereiten des hochzuladenden Images

### Vorbereiten des Betriebssystems CentOS 6.2+

Sie müssen die spezifischen Konfigurationsschritte im Betriebssystem für den virtuellen Computer abschließen, welcher unter Azure ausgeführt werden soll.

1.  Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2.  Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

3.  Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

        rpm -e --nodeps NetworkManager

    **Hinweis:** Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl mit einer Fehlermeldung fehl. Dies entspricht dem erwarteten Verhalten.

4.  Erstellen Sie eine Datei mit dem Namen **network** und folgendem Text im Verzeichnis `/etc/sysconfig/`:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Erstellen Sie eine Datei mit dem Namen **ifcfg-eth0** und folgendem Text im Verzeichnis `/etc/sysconfig/network-scripts/`:

        DEVICE=eth0
        ONBOOT=yes
        DHCP=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Aktivieren Sie den Netzwerkdienst, indem Sie den folgenden Befehl ausführen:

        chkconfig network on

7.  CentOS 6.2 oder 6.3: Installieren Sie die Treiber für die Linux-Integrationsdienste.

    **Hinweis:** Dieser Schritt gilt nur für CentOS 6.2 und 6.3. In CentOS 6.4+ sind die Linux-Integrationsdienste bereits im Kernel verfügbar.

    a) Rufen Sie die .iso-Datei, welche die Treiber für die Linux-Integrationsdienste enthält, über das [Download Center][Download Center] ab.

    b) Klicken Sie im Hyper-V-Manager im Fensterbereich **Aktionen** auf **Einstellungen**.

    ![Hyper-V-Einstellungen öffnen][Hyper-V-Einstellungen öffnen]

    c) Klicken Sie im Fensterbereich **Hardware** auf **IDE Controller 1**.

    ![DVD-Laufwerk per Installation von Medien hinzufügen][DVD-Laufwerk per Installation von Medien hinzufügen]

    d) Klicken Sie im Feld **IDE Controller** auf **DVD-Laufwerk** und anschließend auf **Hinzufügen**.

    e) Wählen Sie **Image-Datei**. Navigieren Sie zu **Linux IC v3.2.iso**, und klicken Sie dann auf **Öffnen**.

    f) Klicken Sie auf der Seite **Einstellungen** auf **OK**.

    g) Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

    h) Geben Sie im Eingabeaufforderungsfenster die folgenden Befehle ein:

        mount /dev/cdrom /media
        /media/install.sh
        reboot

8.  Installieren Sie python-pyasn1, indem Sie den folgenden Befehl ausführen:

        sudo yum install python-pyasn1

9.  Ersetzen Sie die Datei /etc/yum.repos.d/CentOS-Base.repo durch den folgenden Text:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

10. Fügen Sie die folgenden Zeilen "/etc/yum.conf" hinzu:

        http_caching=packages
        exclude=kernel*

11. Deaktivieren Sie das yum-Modul "fastestmirror", indem Sie die Datei "/etc/yum/pluginconf.d/fastestmirror.conf" bearbeiten. Geben Sie außerdem unter [main] das Folgende ein:

        set enabled=0

12. Führen Sie den folgenden Kommentar aus, um die aktuellen yum-Metadaten zu löschen:

        yum clean all

13. Aktualisieren Sie bei CentOS 6.2 und 6.3 den Kernel eines ausgeführten virtuellen Computers, indem Sie die folgenden Befehle ausführen:

    Führen Sie bei CentOS 6.2 die folgenden Befehle aus:

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    Geben Sie bei CentOS 6.3+ Folgendes ein:

        sudo yum --disableexcludes=all install kernel

14. Ändern Sie die Kernel-Boot-Zeile in grub, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

15. Stellen Sie sicher, dass alle SCSI-Geräte, die im Kernel gemountet sind, eine I/O-Zeitlimitüberschreitung von mindestens 300 Sekunden beinhalten.

16. Kommentieren Sie in /etc/sudoers die folgende Zeile aus, sofern diese vorhanden ist:

        Defaults targetpw

17. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.

18. Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen

        yum install WALinuxAgent

19. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. Klicken Sie im Hyper-V-Manager auf **Shutdown**.

### Vorbereiten des Betriebssystems Ubuntu 12.04+

1.  Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2.  Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

3.  Ersetzen Sie die aktuellen Repositorys in Ihrem Image, um jene Azure-Repositorys zu verwenden, die die Kernel- und Agent-Pakete tragen. Letztere benötigen Sie, um ein Upgrade des virtuellen Computers durchzuführen. Die Schritte können je nach Ubuntu-Version geringfügig abweichen.

    Vor dem Bearbeiten von "/etc/apt/sources.list" empfiehlt es sich, eine Sicherung zu erstellen:
     sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04+:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  Aktualisieren Sie das Betriebssystem auf den neuesten Kernel, indem Sie die folgenden Befehle ausführen:

    Ubuntu 12.04:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 12.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 13.04 und 13.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

5.  Ubuntu wartet nach einem Systemabsturz bei der Grub-Aufforderung auf eine Benutzereingabe. Um dies zu unterbinden, müssen Sie die folgenden Schritte ausführen:

    a) Öffnen Sie die Datei /etc/grub.d/00\_header.

    b) Suchen Sie in der **make\_timeout()**-Funktion **if ["\\${recordfail}" = 1 ]; then**

    c) Ändern Sie die Anweisung unterhalb dieser Zeile in **set timeout=5**.

    d) Führen Sie "update-grub" aus.

6.  Modifizieren Sie die Kernel-Boot-Zeile in Grub oder Grub2, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.  Kommentieren Sie in /etc/sudoers die folgende Zeile aus, sofern diese vorhanden ist:

        Defaults targetpw

8.  Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.

9.  Installieren Sie den Agent, indem Sie die folgenden Befehle mit sudo ausführen:

        apt-get update
        apt-get install walinuxagent 

10. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Klicken Sie im Hyper-V-Manager auf **Shutdown**.

### Vorbereiten des Betriebssystems SUSE Linux Enterprise Server 11 SP2 & SP3

**HINWEIS:** Mit [SUSE Studio][SUSE Studio] können Sie Ihre SLES/openSUSE-Abbilder für Azure und Hyper-V auf einfache Weise erstellen und verwalten. Zusätzlich können zur einfachen benutzerdefinierten Anpassung die folgenden offiziellen Abbilder aus der SUSE Studio Gallery in ein eigenes SUSE Studio-Konto heruntergeladen oder geklont werden:

> -   [SLES 11 SP2 für Azure in SUSE Studio Gallery][SLES 11 SP2 für Azure in SUSE Studio Gallery]
> -   [SLES 11 SP3 für Azure in SUSE Studio Gallery][SLES 11 SP3 für Azure in SUSE Studio Gallery]

1.  Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2.  Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

3.  Fügen Sie das Repository hinzu, welches den neuesten Kernel und Azure Linux Agent enthält. Führen Sie den Befehl `zypper lr` aus. So sollte bei SLES 11 SP3 die Ausgabe z. B. ähnlich wie im Folgenden aussehen:

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    Falls der Befehl eine Fehlermeldung etwa wie die folgende ausgibt:

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    then the repositories may need to be re-enabled or the system registered. This can be done via the suse\_register utility. Weitere Informationen finden Sie in der [SLES-Dokumentation][SLES-Dokumentation].

Falls eines der relevanten Update-Repositorys nicht aktiviert ist, können Sie es mit dem folgenden Befehl aktivieren:

        zypper mr -e [REPOSITORY NUMBER]

Im oben aufgeführten Fall sieht der angemessene Befehl folgendermaßen aus:

        zypper mr -e 1 2 3 4

1.  Aktualisieren Sie den Kernel auf die neueste verfügbare Version:

        zypper up kernel-default

2.  Installieren des Azure Linux Agent:

        zypper up WALinuxAgent

    Eine Meldung ähnlich der folgenden wird unter Umständen angezeigt:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
        Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

    Da der Anbieter des Pakets nicht mehr die "Microsoft Corporation" sondern "SUSE LINUX Products GmbH, Nuernberg, Germany" ist, muss das entsprechende Paket explizit wie in der Meldung angegeben installiert werden.

    Hinweis: Die Version des WALinuxAgent-Pakets kann eventuell geringfügig abweichend sein.

3.  Ändern Sie die Kernel-Boot-Zeile in grub, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

4.  Es wird empfohlen, dass Sie /etc/sysconfig/network/dhcp oder ein Äquivalent von DHCLIENT\_SET\_HOSTNAME="yes" auf DHCLIENT\_SET\_HOSTNAME="no" einstellen.

5.  Kommentieren Sie in /etc/sudoers die folgende Zeile aus, sofern diese vorhanden ist:

        Defaults targetpw

6.  Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.

7.  Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Klicken Sie im Hyper-V-Manager auf **Shutdown**.

### Vorbereiten des Betriebssystems openSUSE 12.3

**HINWEIS:** Mit [SUSE Studio][SUSE Studio] können Sie Ihre SLES/openSUSE-Abbilder für Azure und Hyper-V auf einfache Weise erstellen und verwalten. Zusätzlich können zur einfachen benutzerdefinierten Anpassung die folgenden offiziellen Abbilder aus der SUSE Studio Gallery in ein eigenes SUSE Studio-Konto heruntergeladen oder geklont werden:

> -   [openSUSE 12.3 für Azure in SUSE Studio Gallery][openSUSE 12.3 für Azure in SUSE Studio Gallery]

1.  Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2.  Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

3.  Aktualisieren Sie das Betriebssystem mit dem neuesten verfügbaren Kernel und den entsprechenden Patches.

4.  Führen Sie in der Shell den Befehl `zypper lr` aus. Wenn dieser Befehl Folgendes zurückgibt

        # | Alias                     | Name                      | Enabled | Refresh
        --+---------------------------+---------------------------+---------+--------
        1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
        2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
        3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

dann werden die Repositorys wie erwartet konfiguriert, und es sind keine Anpassungen notwendig.

Wenn der Befehl "No repositories defined. Use the 'zypper addrepo' command to add one
or more repositories" zurückgibt, müssen die Repositorys erneut aktiviert werden:

        zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
        zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

Überprüfen Sie, ob die Repositorys hinzugefügt wurden, indem Sie 'zypper lr' erneut aufrufen.

Falls eines der relevanten Update-Repositorys nicht aktiviert ist, können Sie es mit dem folgenden Befehl aktivieren:

        zypper mr -e [NUMBER OF REPOSITORY]

1.  Deaktivieren der automatischen DVD ROM-Sondierung.

2.  Installieren des Azure Linux Agent:

    Fügen Sie zunächst das Repository hinzu, welches den neuen WALinuxAgent enthält:

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    Führen Sie dann den folgenden Befehl aus:

        zypper up WALinuxAgent

    Nachdem Sie diesen Befehl ausgeführt haben, erhalten Sie eventuell eine Nachricht, die ähnlich wie im Folgenden aussieht:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    Diese Nachricht entspricht den Erwartungen. Da der Anbieter des Pakets nicht mehr die "Microsoft Corporation" sondern "obs://build.opensuse.org/Cloud" ist, muss das entsprechende Paket explizit wie in der Meldung angegeben installiert werden.

3.  Ändern Sie die Kernel-Boot-Zeile in grub, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Entfernen Sie in "/boot/grub/menu.lst" die folgenden Parameter aus der Kernel-Boot-Zeile, sofern diese vorhanden sind:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

4.  Es wird empfohlen, dass Sie /etc/sysconfig/network/dhcp oder ein Äquivalent von DHCLIENT\_SET\_HOSTNAME="yes" auf DHCLIENT\_SET\_HOSTNAME="no" einstellen.

5.  Kommentieren Sie in /etc/sudoers die folgende Zeile aus, sofern diese vorhanden ist:

        Defaults targetpw

6.  Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.

7.  Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Stellen Sie sicher, dass der Azure Linux Agent beim Start ausgeführt wird:

        systemctl enable waagent.service

10. Klicken Sie im Hyper-V-Manager auf **Shutdown**.

## <span id="createstorage"></span> </a>Schritt 2: Erstellen eines Speicherkontos in Azure

Ein Speicherkonto stellt die höchste Ebene des Namespace für den Zugriff auf die Speicherdienste dar. Es ist mit Ihrem Azure-Abonnement verknüpft. Sie benötigen in Azure ein Speicherkonto, um eine .vhd-Datei nach Azure hochladen zu können, die zum Erstellen eines virtuellen Computers verwendet werden kann. Sie können ein Speicherkonto erstellen, indem Sie das Azure-Verwaltungsportal verwenden.

1.  Melden Sie sich auf dem Azure-Verwaltungsportal an.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.

    ![Speicherkonto erstellen][Speicherkonto erstellen]

3.  Klicken Sie auf **Speicherkonto** und anschließend auf **Schnellerfassung**.

    ![Speicherkonto schnell erfassen][Speicherkonto schnell erfassen]

4.  Füllen Sie die Felder wie folgt aus:

    ![Speicherkontodetails eingeben][Speicherkontodetails eingeben]

-   Geben Sie unter **URL** einen Unterdomänennamen ein, der im URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname im URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

-   Wählen Sie den Standort oder die Affinitätsgruppe für das Speicherkonto aus. Durch das Angeben einer Affinitätsgruppe können Sie die Clouddienste im selben Datencenter gemeinsam mit der Speicherung unterbringen.

-   Sie können wählen, ob Sie Georeplikation für das Speicherkonto verwenden möchten. Georeplikation ist als Standard voreingestellt. Mit dieser Option werden Ihre Daten kostenfrei in einem sekundären Speicherort repliziert. So wird die Speicherung auf einen sekundären Speicherort umgeschaltet, wenn ein größerer Ausfall auftritt, der im primären Speicherort nicht behoben werden kann. Der sekundäre Speicherort wird automatisch zugewiesen und kann nicht verändert werden. Falls die gesetzlichen Anforderungen oder Unternehmensrichtlinien eine schärfere Kontrolle des Speicherorts für eine cloudbasierte Speicherung erforderlich machen, können Sie die Georeplikation abschalten. Beachten Sie jedoch, dass bei einem späteren Aktivieren der Georeplikation eine einmalige Datenübertragungsgebühr fällig wird, um Ihre vorhandenen Daten in dem sekundären Speicherort zu replizieren. Die Speicherdienste ohne Georeplikation werden mit einem Rabatt angeboten.

1.  Klicken Sie auf **Speicherkonto erstellen**.

    Das Konto ist nun unter den **Speicherkonten** aufgelistet.

    ![Speicherkonto erfolgreich erstellt][Speicherkonto erfolgreich erstellt]

## <span id="#connect"></span> </a>Schritt 3: Vorbereiten der Verbindung mit Azure

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen.

1.  Öffnen Sie ein Azure PowerShell-Fenster.

2.  Geben Sie Folgendes ein:

    `Get-AzurePublishSettingsFile`

    Mit diesem Befehl wird ein Browser-Fenster geöffnet und eine .publishsettings-Datei automatisch heruntergeladen. Letztere enthält die Informationen und ein Zertifikat zu Ihrem Azure-Abonnement.

3.  Speichern Sie die .publishsettings-Datei.

4.  Geben Sie Folgendes ein:

    `Import-AzurePublishSettingsFile <PathToFile>`

    Dabei stellt `<PathToFile>` den vollständigen Pfad zur PUBLISHSETTINGS-Datei dar.

    Weitere Informationen finden Sie unter [Erste Schritte mit Azure Cmdlets][Erste Schritte mit Azure Cmdlets].

## <span id="upload"></span> </a>Schritt 4: Hochladen des Abbilds zu Azure

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** den URL für das Speicherkonto dar, das Sie im Schritt 2 erstellt haben. Zudem stellt **YourImagesFolder** den Container innerhalb des Blobspeichers dar, in dem Sie Ihre Images speichern möchten. **VHDName** steht für die Bezeichnung, die im Verwaltungsportal zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** stellt den vollständigen Pfad und den Namen der .vhd-Datei dar.

Führen Sie einen der folgenden Schritte aus:

-   Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Weitere Informationen hierzu finden Sie unter [Add-AzureVhd][Add-AzureVhd].

-   Verwenden Sie das Linux-Befehlszeilentool zum Hochladen des Images. Sie können ein Image hochladen, indem Sie den folgenden Befehl verwenden:

        Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <span id="nonendorsed"></span> </a>Informationen zu nicht-bestätigten Distributionen

Im Wesentlichen müssen sämtliche Distributionen, die in Azure ausgeführt werden, die folgenden Voraussetzungen erfüllen, damit sie auf der Plattform angemessen betrieben werden können.

Diese Liste umfasst in keinem Fall alle Aspekte, da jede Distribution unterschiedlich ist. Es ist durchaus möglich, dass Sie, selbst wenn Sie alle unten aufgeführten Kriterien erfüllen, ein betreffendes Image noch erheblich optimieren müssen, um gewährleisten zu können, dass es ordnungsgemäß auf der Plattform ausgeführt wird.

Aus diesem Grund wird empfohlen, dass Sie mit einem unserer [Images bestätigt von Partnern][Images bestätigt von Partnern] starten.

Die unten aufgeführte Liste ersetzt den Schritt 1 des Prozesses zur Erstellung einer eigenen virtuellen Festplatte:

1.  Sie müssen sicherstellen, dass Sie einen Kernel ausführen, der entweder die neuesten LIS-Treiber für Hyper V enthält oder mit dem Sie diese erfolgreich kompiliert haben (sie wurden als Open Source zur Verfügung gestellt). Die Treiber sind [unter diesem Speicherort][unter diesem Speicherort] zu finden.

2.  Ihr Kernel sollte ebenfalls die neueste Version des ATA PiiX-Treibers enthalten, der zur Bereitstellung der Abbilds verwendet wird und der die Updates aufweist, die an den Kernel mit Commit cd006086fa5d91414d8ff9ff2b78fbb593878e3c übergeben werden (Datum: Fri May 4 22:15:11 2012 +0100 ata\_piix: Datenträger standardmäßig zu Hyper-V-Treibern verschieben).

3.  Die komprimierte Datei "intird" sollte kleiner als 40 MB sein (\* wir arbeiten ständig daran, diesen Wert zu erhöhen; er kann also in der Zwischenzeit bereits überholt sein).

4.  Modifizieren Sie die Kernel-Boot-Zeile in Grub oder Grub2, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  Es wird empfohlen, dass Sie /etc/sysconfig/network/dhcp oder ein Äquivalent von DHCLIENT\_SET\_HOSTNAME="yes" auf DHCLIENT\_SET\_HOSTNAME="no" einstellen.

6.  Sie sollten sicherstellen, dass alle SCSI-Geräte, die im Kernel gemountet sind, eine I/O-Zeitlimitüberschreitung von mindestens 300 Sekunden beinhalten.

7.  Sie müssen den Azure Linux Agent gemäß den im [Linux Agent Guide][Linux Agent Guide] aufgeführten Schritten installieren. Der Agent wurde mit der Apache 2-Lizenz freigegeben. Sie können die aktuellsten Bits unter [Agent GitHub Location][Agent GitHub Location] abrufen.

8.  Kommentieren Sie in /etc/sudoers die folgende Zeile aus, sofern diese vorhanden ist:

        Defaults targetpw

9.  Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.

10. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Sie müssen die folgenden Befehle ausführen, um den virtuellen Computer zurückzusetzen:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Sie müssen den virtuellen Computer anschließend herunterfahren und mit dem Upload-Vorgang fortfahren.

  [Verwalten von Datenträgern und Images]: http://msdn.microsoft.com/de-de/library/windowsazure/jj672979.aspx
  [Erstellen eines benutzerdefinierten virtuellen Computers]: /de-de/manage/windows/how-to-guides/custom-create-a-vm/
  [diesem Artikel]: http://support.microsoft.com/kb/2805216
  [Erstellen eines Verwaltungszertifikats für Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg551722.aspx
  [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers]: http://technet.microsoft.com/de-de/library/hh846766.aspx
  [Linux zu für Azure bestätigte Distributionen]: ../linux-endorsed-distributions
  [Informationen zu nicht-bestätigten Distributionen]: #nonendorsed
  [Azure-Befehlszeilentools für Linux und Mac]: http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409
  [Azure-Downloads]: /de-de/develop/downloads/
  [Add-AzureVhd]: http://msdn.microsoft.com/de-de/library/windowsazure/dn205185.aspx
  [Microsoft-Website]: http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409
  [Schritt 1: Vorbereiten des hochzuladenden Images]: #prepimage
  [Schritt 2: Erstellen eines Speicherkontos in Azure]: #createstorage
  [Schritt 3: Vorbereiten der Verbindung mit Azure]: #connect
  [Schritt 4: Hochladen des Abbilds zu Azure]: #upload
  [Download Center]: http://www.microsoft.com/de-de/download/details.aspx?id=34603
  [Hyper-V-Einstellungen öffnen]: ./media/virtual-machines-linux-how-to-prepare/settings.png
  [DVD-Laufwerk per Installation von Medien hinzufügen]: ./media/virtual-machines-linux-how-to-prepare/installiso.png
  [SUSE Studio]: http://www.susestudio.com
  [SLES 11 SP2 für Azure in SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp2-for-windows-azure
  [SLES 11 SP3 für Azure in SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [SLES-Dokumentation]: https://www.suse.com/documentation/sles11/
  [openSUSE 12.3 für Azure in SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/opensuse-12-3-for-windows-azure
  [Speicherkonto erstellen]: ./media/virtual-machines-linux-how-to-prepare/create.png
  [Speicherkonto schnell erfassen]: ./media/virtual-machines-linux-how-to-prepare/storage-quick-create.png
  [Speicherkontodetails eingeben]: ./media/virtual-machines-linux-how-to-prepare/storage-create-account.png
  [Speicherkonto erfolgreich erstellt]: ./media/virtual-machines-linux-how-to-prepare/Storagenewaccount.png
  [Erste Schritte mit Azure Cmdlets]: http://msdn.microsoft.com/de-de/library/windowsazure/jj554332.aspx
  [Images bestätigt von Partnern]: https://www.windowsazure.com/de-de/manage/linux/other-resources/endorsed-distributions/
  [unter diesem Speicherort]: http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409
  [Linux Agent Guide]: https://www.windowsazure.com/de-de/manage/linux/how-to-guides/linux-agent-guide/
  [Agent GitHub Location]: http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409
