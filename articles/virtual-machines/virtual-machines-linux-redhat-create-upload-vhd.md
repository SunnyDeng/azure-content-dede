<properties
	pageTitle="Erstellen und Hochladen einer VHD-Datei mit Red Hat Enterprise Linux zur Verwendung in Azure"
	description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Red Hat-Linux-Betriebssystem enthält."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="mingzhan"/>


# Vorbereiten eines auf Red Hat basierenden virtuellen Computers für Azure

In diesem Artikel erfahren Sie, wie Sie einen auf Red Hat Enterprise Linux (RHEL) basierenden virtuellen Computer für die Verwendung in Azure vorbereiten. In diesem Artikel werden die RHEL-Versionen 6.7, 7.1 und 7.2 behandelt. Darüber hinaus werden in diesem Artikel die Hypervisoren Hyper-V, KVM und VMware für die Vorbereitung vorgestellt. Weitere Informationen zu den Berechtigungsvoraussetzungen für die Teilnahme am Cloud Access-Programm von Red Hat finden Sie auf der [Red Hat Cloud Access-Website](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) und unter [Running RHEL on Azure](https://access.redhat.com/articles/1989673) (in englischer Sprache).

[Vorbereiten eines virtuellen RHEL 6.7-Computers über Hyper-V-Manager](#rhel67hyperv)

[Vorbereiten eines virtuellen RHEL 7.1-/7.2-Computers über Hyper-V-Manager](#rhel7xhyperv)

[Vorbereiten eines virtuellen RHEL 6.7-Computers über KVM](#rhel67kvm)

[Vorbereiten eines virtuellen RHEL 7.1-/7.2-Computers über KVM](#rhel7xkvm)

[Vorbereiten eines virtuellen RHEL 6.7-/7.2-Computers über VMWare](#rhel67vmware)

[Vorbereiten eines virtuellen RHEL 7.1-/7.2-Computers über VMWare](#rhel7xvmware)

[Vorbereiten eines virtuellen RHEL 7.1-/7.2-Computers über eine Kickstart-Datei](#rhel7xkickstart)


## Vorbereiten eines auf Red Hat basierenden virtuellen Computers über Hyper-V-Manager
### Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits ein RHEL-Image aus einer ISO-Datei von der Red Hat-Website auf einer virtuellen Festplatte (VHD) installiert haben. Weitere Informationen zum Installieren eines Betriebssystemimages mit dem Hyper-V-Manager finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

**Installationshinweise zu RHEL**

- Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit Hyper-V-Manager oder dem PowerShell-Cmdlet **convert-vhd** in das VHD-Format konvertieren.

- VHDs müssen mit festem Format erstellt werden. Dynamische VHDs werden nicht unterstützt.

- Beim Installieren des Linux-Betriebssystems wird empfohlen, anstelle von LVM (Logical Volume Manager) (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. Bei Bedarf können Sie LVM oder RAID auf Datenträgern verwenden.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Sie können den Linux-Agent zur Erstellung einer Auslagerungsdatei auf dem temporären Ressourcendatenträger konfigurieren. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.

- Wenn Sie zum Umwandeln der Datenträgerimages in das VHD-Format **qemu-img** verwenden, beachten Sie das bekannte Problem in den qemu-img-Versionen ab 2.2.1. Dieser Fehler führt zu einer nicht ordnungsgemäß formatierten VHD. Das Problem soll in einer zukünftigen qemu-img-Version behoben werden. Bis dahin sollte die qemu-img-Version 2.2.0 oder eine ältere Version verwendet werden.

### <a id="rhel67hyperv"> </a>Vorbereiten eines virtuellen RHEL 6.7-Computers über Hyper-V-Manager###


1.	Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2.	Klicken Sie auf **Verbinden**, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3.	Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

        # sudo rpm -e --nodeps NetworkManager

    Hinweis: Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl fehl, und Sie erhalten eine Fehlermeldung. Dies entspricht dem erwarteten Verhalten.

4.	Erstellen Sie im Verzeichnis `/etc/sysconfig/` eine Datei mit dem Namen **network** und folgendem Text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Erstellen Sie im Verzeichnis `/etc/sysconfig/network-scripts/` eine Datei mit dem Namen**ifcfg-eth0** und folgendem Text:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # sudo chkconfig network on

8.	Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür `/boot/grub/menu.lst` in einem Text-Editor, und stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers in der von RHEL 6 verwendeten Kernel-Version deaktiviert.

    Neben der oben erwähnten Aktion wird empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

    Die crashkernel-Option kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

11.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ändern Sie die Datei „/etc/ssh/sshd\_config“, sodass sie die folgende Zeile enthält:

        ClientAliveInterval 180

12.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    Beachten Sie, dass durch eine Installation des WALinuxAgent-Pakets die NetworkManager- und NetworkManager-gnome-Pakete entfernt werden, sofern sie nicht bereits wie in Schritt 2 beschrieben entfernt wurden.

13.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein. Der Azure Linux Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in „/etc/waagent.conf“ entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

        # sudo subscription-manager unregister

15.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

### <a id="rhel7xhyperv"> </a>Vorbereiten eines virtuellen RHEL 7.1-/7.2-Computers über Hyper-V-Manager###

1.  Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2.	Klicken Sie auf **Verbinden**, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3.	Erstellen Sie im Verzeichnis `/etc/sysconfig/` eine Datei mit dem Namen **network** und folgendem Text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.	Erstellen Sie im Verzeichnis `/etc/sysconfig/network-scripts/` eine Datei mit dem Namen**ifcfg-eth0** und folgendem Text:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.	Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # sudo chkconfig network on

6.	Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter **GRUB\_CMDLINE\_LINUX**. Beispiel:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben der oben erwähnten Aktion wird empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

	Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

8.	Nachdem Sie die Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

        ClientAliveInterval 180

10.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein. Der Azure Linux Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` entsprechend an:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Wenn Sie die Registrierung des Abonnements aufheben möchten, führen Sie den folgenden Befehl aus:

        # sudo subscription-manager unregister

14.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.


## Vorbereiten eines auf Red Hat basierenden virtuellen Computers über KVM

### <a id="rhel67kvm"> </a>Vorbereiten eines virtuellen RHEL 6.7-Computers über KVM###


1.	Laden Sie das KVM-Image von RHEL 6.7 von der Red Hat-Website herunter.

2.	Legen Sie ein Stammkennwort fest.

    Generieren Sie ein verschlüsseltes Kennwort, und kopieren Sie die Ausgabe des Befehls:

        # openssl passwd -1 changeme

    Legen Sie ein Stammkennwort mit Guestfish fest:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

	Ändern Sie im zweiten Feld des Stammbenutzers „!!“ in das verschlüsselte Kennwort.

3.	Erstellen Sie in KVM einen virtuellen Computer aus dem qcow2-Image, legen Sie **qcow2** als Datenträgertyp fest, und legen Sie als Gerätemodell der virtuellen Netzwerkschnittstelle **virtio** fest. Starten Sie dann den virtuellen Computer, und melden Sie sich als Stammbenutzer an.

4.	Erstellen Sie im Verzeichnis `/etc/sysconfig/` eine Datei mit dem Namen **network** und folgendem Text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Erstellen Sie im Verzeichnis `/etc/sysconfig/network-scripts/` eine Datei mit dem Namen**ifcfg-eth0** und folgendem Text:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # chkconfig network on

8.	Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür `/boot/grub/menu.lst` in einem Text-Editor, und stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers in der von RHEL 6 verwendeten Kernel-Version deaktiviert.

    Neben der oben erwähnten Aktion wird empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

10. Fügen Sie Hyper-V-Module zu initramfs hinzu:

    Bearbeiten Sie `/etc/dracut.conf`, und fügen Sie Inhalt hinzu: add\_drivers+=”hv\_vmbus hv\_netvsc hv\_storvsc”

    Erstellen Sie „initramfs“ neu: # dracut –f -v

11.	Deinstallieren Sie die Cloud-Initialisierung:

        # yum remove cloud-init

12.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Starten hochfährt:

        # chkconfig sshd on

    Ändern Sie die Datei „/etc/ssh/sshd\_config“, sodass sie die folgenden Zeilen enthält:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Starten Sie sshd neu:

		# service sshd restart

13.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # yum install WALinuxAgent
        # chkconfig waagent on

15.	Der Azure Linux Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in **/etc/waagent.conf** entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

        # subscription-manager unregister

17.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18.	Fahren Sie den virtuellen Computer in KVM herunter.

19.	Konvertieren Sie das qcow2-Image in das VHD-Format: Konvertieren Sie das Bild zuerst in das raw-Format:

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf: # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Vorbereiten eines virtuellen RHEL 7.1/7.2-Computers über KVM###


1.	Laden Sie das KVM-Image von RHEL 7.1 (oder 7.2) von der Red Hat-Website herunter. In diesem Beispiel wird RHEL 7.1 verwendet.

2.	Legen Sie ein Stammkennwort fest.

    Generieren Sie ein verschlüsseltes Kennwort, und kopieren Sie die Ausgabe des Befehls:

        # openssl passwd -1 changeme

    Legen Sie ein Stammkennwort mit Guestfish fest:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Ändern Sie im zweiten Feld des Stammbenutzers „!!“ in das verschlüsselte Kennwort.

3.	Erstellen Sie in KVM einen virtuellen Computer aus dem qcow2-Image, legen Sie **qcow2** als Datenträgertyp fest, und legen Sie als Gerätemodell der virtuellen Netzwerkschnittstelle **virtio** fest. Starten Sie dann den virtuellen Computer, und melden Sie sich als Stammbenutzer an.

4.	Erstellen Sie im Verzeichnis `/etc/sysconfig/` eine Datei mit dem Namen **network** und folgendem Text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Erstellen Sie im Verzeichnis `/etc/sysconfig/network-scripts/` eine Datei mit dem Namen**ifcfg-eth0** und folgendem Text:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # chkconfig network on

7.	Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter **GRUB\_CMDLINE\_LINUX**. Beispiel:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben der oben erwähnten Aktion wird empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

9.	Nachdem Sie die Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Fügen Sie Hyper-V-Module zu initramfs hinzu:

    Bearbeiten Sie `/etc/dracut.conf`, und fügen Sie Inhalt hinzu:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Erstellen Sie „initramfs“ neu:

        # dracut –f -v

11.	Deinstallieren Sie die Cloud-Initialisierung:

        # yum remove cloud-init

12.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Starten hochfährt:

        # systemctl enable sshd

    Ändern Sie die Datei „/etc/ssh/sshd\_config“, sodass sie die folgenden Zeilen enthält:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Starten Sie sshd neu:

        systemctl restart sshd

13.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # yum install WALinuxAgent

    Aktivieren Sie den Waagent-Dienst:

        # systemctl enable waagent.service

15.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein. Der Azure Linux Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` entsprechend an:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

        # subscription-manager unregister

17.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18.	Fahren Sie den virtuellen Computer in KVM herunter.

19.	Konvertieren Sie das qcow2-Image in das VHD-Format:

    Konvertieren Sie das Bild zuerst in das raw-Format:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## Vorbereiten eines auf Red Hat basierenden virtuellen Computers über VMware
### Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen virtuellen Computer von RHEL in VMware installiert haben. Weitere Informationen zum Installieren eines Betriebssystems in VMware finden Sie im [VMware-Installationshandbuch für Gastbetriebssysteme](http://partnerweb.vmware.com/GOSIG/home.html).

- Beim Installieren des Linux-Betriebssystems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. LVM oder RAID können bei Bedarf auf Datenträgern verwendet werden.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Sie können den Linux-Agent zur Erstellung einer Auslagerungsdatei auf dem temporären Ressourcendatenträger konfigurieren. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Wählen Sie beim Erstellen der virtuellen Festplatte **Virtuellen Datenträger als einzelne Datei speichern**.



### <a id="rhel67vmware"> </a>Vorbereiten eines virtuellen RHEL 6.7-Computers über VMWare###

1.	Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

         # sudo rpm -e --nodeps NetworkManager

    Hinweis: Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl fehl, und Sie erhalten eine Fehlermeldung. Dies entspricht dem erwarteten Verhalten.

2.	Erstellen Sie eine Datei mit der Benennung **network** im Verzeichnis "/etc/sysconfig/", die den folgenden Text enthält:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	Erstellen Sie eine Datei mit der Benennung **ifcfg-eth0** im Verzeichnis "/etc/sysconfig/network-scripts/", die den folgenden Text enthält:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # sudo chkconfig network on

6.	Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür "/boot/grub/menu.lst" in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers in der von RHEL 6 verwendeten Kernel-Version deaktiviert. Neben der oben erwähnten Aktion wird empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

9.	Fügen Sie Hyper-V-Module zu initramfs hinzu:

	    Edit `/etc/dracut.conf` and add content:

	        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

	    Rebuild initramfs:

	        # dracut –f -v

10.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

        ClientAliveInterval 180

11.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12.	Richten Sie keinen SWAP-Bereich auf dem Betriebssystemdatenträger ein:

    Der Azure Linux Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` entsprechend an:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

        # sudo subscription-manager unregister

14.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Fahren Sie den virtuellen Computer herunter, und konvertieren Sie die VMDK-Datei in eine VHD-Datei.

    Konvertieren Sie das Bild zuerst in das raw-Format:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Vorbereiten eines virtuellen RHEL 7.1/7.2-Computers über VMWare###

1.	Erstellen Sie eine Datei mit der Benennung **network** im Verzeichnis "/etc/sysconfig/", die den folgenden Text enthält:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	Erstellen Sie eine Datei mit der Benennung **ifcfg-eth0** im Verzeichnis "/etc/sysconfig/network-scripts/", die den folgenden Text enthält:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

        # sudo chkconfig network on

4.	Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter **GRUB\_CMDLINE\_LINUX**. Beispiel:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben der oben erwähnten Aktion wird empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

6.	Nachdem Sie die Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Fügen Sie Hyper-V-Module zu initramfs hinzu:

    Bearbeiten Sie `/etc/dracut.conf` und fügen Sie Inhalt hinzu:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Erstellen Sie „initramfs“ neu:

        # dracut –f -v

8.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

        ClientAliveInterval 180

9.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein. Der Azure Linux Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` an:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Wenn Sie die Registrierung des Abonnements aufheben möchten, führen Sie den folgenden Befehl aus:

        # sudo subscription-manager unregister

13.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	Fahren Sie den virtuellen Computer herunter, und konvertieren Sie die VMDK-Datei in das VHD-Format.

    Konvertieren Sie das Bild zuerst in das raw-Format:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## Automatisches Vorbereiten eines auf Red Hat basierenden virtuellen Computers aus einer ISO-Datei mit einer Kickstart-Datei


### <a id="rhel7xkickstart"> </a>Vorbereiten eines virtuellen RHEL 7.1-/7.2-Computers über eine Kickstart-Datei###


1.	Erstellen Sie die Kickstart-Datei mit folgendem Inhalt, und speichern Sie die Datei. Weitere Informationen zur Kickstart-Installation finden Sie im [Kickstart-Installationshandbuch](https://access.redhat.com/documentation/de-DE/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.	Legen Sie die Kickstart-Datei an einem Ort ab, auf den im gesamten Installationssystem zugegriffen werden kann.

3.	Erstellen Sie in Hyper-V Manager einen neuen virtuellen Computer. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** die Option **Virtuelle Festplatte später zuordnen**, und schließen Sie den Assistenten für neue virtuelle Computer ab.

4.	Öffnen Sie die VM-Einstellungen:

    a. Ordnen Sie dem virtuellen Computer eine neue virtuelle Festplatte zu. Wählen Sie **VHD-Format** und **Feste Größe** aus.

    b. Ordnen Sie die ISO-Installationsdatei dem DVD-Laufwerk zu.

    c. Legen Sie fest, dass BIOS von der CD starten soll.

5.	Starten Sie den virtuellen Computer. Wenn die Installationsanweisungen angezeigt werden, drücken Sie die **TAB-TASTE**, um die Startoptionen zu konfigurieren.

6.	Geben Sie am Ende der Startoptionen `inst.ks=<the location of the kickstart file>` ein und drücken Sie die **EINGABETASTE**.

7.	Warten Sie, bis die Installation abgeschlossen ist. Wenn sie abgeschlossen ist, wird der virtuelle Computer automatisch heruntergefahren. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## Bekannte Probleme
Es gibt bekannte Probleme beim Verwenden von RHEL 7.1 in Hyper-V und Azure.

### Einfrieren von Datenträger-E/A

Dieses Problem kann bei häufigen Datenträger-E/A-Aktivitäten mit RHEL 7.1 in Hyper-V und Azure auftreten.

Reproduktionsrate:

Dieses Problem tritt zeitweise auf. Es tritt jedoch öfter während häufiger Datenträger-E/A-Vorgänge in Hyper-V und Azure auf.


[AZURE.NOTE] Dieses bekannte Problem wurde von Red Hat bereits behoben. Um die entsprechenden Fixes zu installieren, führen Sie folgenden Befehl aus:

    # sudo yum update

### Der Hyper-V-Treiber konnte dem anfänglichen RAM-Datenträger nicht hinzugefügt werden, wenn ein anderer Hypervisor als ein Hyper-V-Hypervisor verwendet wurde.

In einigen Fällen enthalten Linux-Installationsprogramme möglicherweise keine Treiber für Hyper-V auf dem anfänglichen RAM-Datenträger („initrd“ oder „initramfs“), bis sie erkennen, dass sie eine Hyper-V-Umgebung ausführen.

Wenn ein anderes Virtualisierungssystem (z.B. Virtualbox oder Xen) zur Vorbereitung des Linux-Images verwendet wird, müssen Sie möglicherweise „initrd“ neu erstellen, um sicherzustellen, dass mindestens die Kernelmodule „hv\_vmbus“ und „hv\_storvsc“ auf dem anfänglichen RAM-Datenträger verfügbar sind. Dies ist ein bekanntes Problem, zumindest auf Systemen, die auf der Red Hat-Upstream-Verteilung basieren.

Um dieses Problem zu beheben, müssen Sie Hyper-V-Module zu „initramfs“ hinzufügen und das Archiv neu erstellen:

Bearbeiten Sie `/etc/dracut.conf`, und fügen Sie Inhalt hinzu:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Erstellen Sie „initramfs“ neu:

        # dracut –f -v

Weitere Informationen finden Sie unter [Neuerstellung von „initramfs“](https://access.redhat.com/solutions/1958).

## Nächste Schritte
Sie können jetzt mit Ihrer Red Hat Enterprise Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie die VHD-Datei zum ersten Mal in Azure hochladen, führen Sie die Schritte 2 und 3 in [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](virtual-machines-linux-classic-create-upload-vhd.md) aus.

Weitere Informationen zu den Hypervisoren, die zum Ausführen von Red Hat Enterprise Linux zertifiziert sind, finden Sie auf der [Red Hat-Website](https://access.redhat.com/certified-hypervisors).

<!---HONumber=AcomDC_0323_2016-->