<properties 
	pageTitle="Erstellen und Hochladen einer VHD-Datei mit Red Hat Enterprise Linux zur Verwendung in Azure" 
	description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Red Hat-Linux-Betriebssystem enthält." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="SuperScottz" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="mingzhan"/>


# Vorbereiten eines auf Red Hat basierenden virtuellen Computers für Azure
In diesem Artikel erfahren Sie, wie einen auf Red Hat Enterprise Linux (RHEL) basierenden virtuellen Computer für die Verwendung in Azure vorbereiten. In diesem Artikel werden die RHEL-Versionen 6.7, 7.1 und 7.2 sowie die Hypervisoren Hyper-V, KVM und VMware für die Vorbereitung vorgestellt. Weitere Informationen zu den Berechtigungsvoraussetzungen für die Teilnahme am Cloud Access-Programm von Red Hat finden Sie auf der [Red Hat Cloud Access-Website](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) und unter [Running RHEL on Azure](https://access.redhat.com/articles/1989673) (in englischer Sprache).

[Vorbereiten einer virtuellen RHEL 6.7-Maschine über Hyper-V-Manager](#rhel67hyperv)

[Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über Hyper-V-Manager](#rhel7xhyperv)

[Vorbereiten einer virtuellen RHEL 6.7-Maschine über KVM](#rhel67kvm)

[Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über KVM](#rhel7xkvm)

[Vorbereiten einer virtuellen RHEL 6.7-Maschine über VMWare](#rhel67vmware)

[Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über VMWare](#rhel7xvmware)

[Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über eine Kickstart-Datei](#rhel7xkickstart)


##Vorbereiten eines Images mit Hyper-V-Manager 
###Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits ein RHEL-Image aus einer ISO-Datei von der Red Hat-Website auf eine virtuelle Festplatte (VHD) installiert haben. Weitere Informationen zum Installieren eines Betriebssystemimages mit dem Hyper-V-Manager finden Sie unter [Installieren von Hyper-V und Erstellen eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

**Installationshinweise zu RHEL**

- Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit Hyper-V-Manager oder dem Cmdlet convert-vhd-powershell in das VHD-Format konvertieren.

- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. LVM oder RAID kann wahlweise auf Datenträgern verwendet werden.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.

- Wenn Sie zum Umwandeln der Datenträgerimages in das VHD-Format qemu-img verwenden, beachten Sie das bekannte Problem in den Versionen von qemu-img ab 2.2.1, das zu einer falsch formatierten VHD-Datei führt. Das Problem wird in einer zukünftigen qemu-img-Version behoben werden. Bis es soweit ist, empfiehlt es sich jedoch, auf die qemu-img-Version 2.2.0 oder niedriger zurückzugreifen.

### <a id="rhel67hyperv"> </a>Vorbereiten einer virtuellen RHEL 6.7-Maschine über Hyper-V-Manager###


1.	Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2.	Klicken Sie auf **Verbinden**, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3.	Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

        # sudo rpm -e --nodeps NetworkManager

    **Hinweis:** Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl fehl, und Sie erhalten eine Fehlermeldung. Dies entspricht dem erwarteten Verhalten.

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

9.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Fedora EPEL 6-Repository übertragen. Aktivieren Sie das EPEL-Repository, indem Sie den folgenden Befehl ausführen:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür `/boot/grub/menu.lst` in einem Text-Editor, und stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers in der von RHEL 6 verwendeten Kernel-Version deaktiviert.

    Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

    Die crashkernel-Option kann bei Bedarf konfiguriert gelassen werden. Beachten Sie jedoch, dass dieser Parameter den verfügbaren Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

11.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ändern Sie die /etc/ssh/sshd\_config, sodass sie die folgende Zeile enthält:

        ClientAliveInterval 180

12.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **Hinweis:** Durch die Installation des WALinuxAgent-Pakets werden die Pakete „NetworkManager“ und „NetworkManager-gnome“ entfernt, sofern sie nicht bereits wie in Schritt 2 beschrieben entfernt wurden.

13.	Richten Sie keinen SWAP-Bereich auf dem Betriebssystemdatenträger ein. Der Azure Linux-Agent kann automatisch einen SWAP-Bereich auf dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Heben Sie die Registrierung des Abonnements (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

        # sudo subscription-manager unregister

15.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	Klicken Sie im Hyper-V-Manager auf **Aktion -> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

### <a id="rhel7xhyperv"> </a>Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über Hyper-V-Manager###

1.  Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2.	Klicken Sie auf Verbinden, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

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

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto
    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert gelassen werden. Beachten Sie jedoch, dass dieser Parameter den verfügbaren Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

8.	Sobald Sie die oben beschriebene Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der Grub-Konfiguration aus:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

        ClientAliveInterval 180

10.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Fedora EPEL 6-Repository übertragen. Aktivieren Sie das EPEL-Repository, indem Sie den folgenden Befehl ausführen:

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service 

12.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein. Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` an:

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

15.	Klicken Sie im Hyper-V-Manager auf **Aktion -> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.


##Image von KVM vorbereiten 

### <a id="rhel67kvm"> </a>Vorbereiten einer virtuellen RHEL 6.7-Maschine über KVM###


1.	Laden Sie das KVM-Image von RHEL 6.7 von der Red Hat-Website herunter.

2.	Stammkennwort festlegen

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

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers in der von RHEL 6 verwendeten Kernel-Version deaktiviert.

    Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert gelassen werden. Beachten Sie jedoch, dass dieser Parameter den verfügbaren Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

10.	Deinstallieren Sie die Cloud-Initialisierung:

        # yum remove cloud-init

11.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Starten hochfährt:
 
        # chkconfig sshd on

    Ändern Sie die /etc/ssh/sshd\_config, sodass Sie die folgenden Zeilen enthält:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Starten Sie sshd neu:

		# service sshd restart

12.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Fedora EPEL 6-Repository übertragen. Aktivieren Sie das EPEL-Repository, indem Sie den folgenden Befehl ausführen:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # yum install WALinuxAgent
        # chkconfig waagent on

14.	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in **/etc/waagent.conf** entsprechend:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:
        
        # subscription-manager unregister

16.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Fahren Sie den virtuellen Computer in KVM herunter.

18.	Konvertieren Sie das qcow2-Image in das vhd-Format: Konvertieren Sie das Image zuerst in das raw-Format:
         
         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Stellen Sie sicher, dass das raw-Image auf 1 MB ausgerichtet ist, und runden Sie ggf. die Größe auf 1 MB auf: # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Konvertieren Sie den raw-Datenträger in das vhd-Format mit festgelegter Größe:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über KVM###


1.	Laden Sie das KVM-Image von RHEL 7.1(oder 7.2) von der Red Hat-Website herunter. In diesem Beispiel wird RHEL 7.1 verwendet.

2.	Stammkennwort festlegen

    Generieren Sie ein verschlüsseltes Kennwort, und kopieren Sie die Ausgabe des Befehls.

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

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert gelassen werden. Beachten Sie jedoch, dass dieser Parameter den verfügbaren Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

9.	Sobald Sie die oben beschriebene Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der Grub-Konfiguration aus:

        # grub2-mkconfig -o /boot/grub2/grub.cfg
        
10.	Fügen Sie Hyper-V-Module zu initramfs hinzu:

    Bearbeiten Sie `/etc/dracut.conf` und fügen Sie Inhalt hinzu:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Erstellen Sie initramfs neu:

        # dracut –f -v
        
11.	Deinstallieren Sie die Cloud-Initialisierung:

        # yum remove cloud-init

12.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Starten hochfährt:

        # systemctl enable sshd

    Ändern Sie die /etc/ssh/sshd\_config, sodass Sie die folgenden Zeilen enthält:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Starten Sie sshd neu:

        systemctl restart sshd	

13.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Fedora EPEL 6-Repository übertragen. Aktivieren Sie das EPEL-Repository, indem Sie den folgenden Befehl ausführen:

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

14.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # yum install WALinuxAgent

    Aktivieren Sie den Waagent-Dienst:

        # systemctl enable waagent.service

15.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein. Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` an:

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

19.	Konvertieren Sie das qcow2-Image in das vhd-Format:

    Konvertieren Sie das Bild zuerst in das raw-Format:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Stellen Sie sicher, dass das raw-Image auf 1 MB ausgerichtet ist und runden Sie andernfalls die Größe auf 1 MB auf:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Konvertieren Sie den raw-Datenträger in das vhd-Format mit festgelegter Größe:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##Vorbereiten eines Images von VMware
###Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen virtuellen Computer von RHEL in VMware installiert haben. Weitere Informationen zum Installieren eines Betriebssystems in VMware finden Sie im [VMware-Installationshandbuch für Gastbetriebssysteme](http://partnerweb.vmware.com/GOSIG/home.html).
 
- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. LVM oder RAID kann wahlweise auf Datenträgern verwendet werden.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Wählen Sie beim Erstellen der virtuellen Festplatte **Virtuellen Datenträger als einzelne Datei speichern** aus.



### <a id="rhel67vmware"> </a>Vorbereiten einer virtuellen RHEL 6.7-Maschine über VMWare###

1.	Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

         # sudo rpm -e --nodeps NetworkManager

    **Hinweis:** Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl fehl, und Sie erhalten eine Fehlermeldung. Dies entspricht dem erwarteten Verhalten.

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

7.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Fedora EPEL 6-Repository übertragen. Aktivieren Sie das EPEL-Repository, indem Sie den folgenden Befehl ausführen:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür „/boot/grub/menu.lst“ in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers in der von RHEL 6 verwendeten Kernel-Version deaktiviert. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert gelassen werden. Beachten Sie jedoch, dass dieser Parameter den verfügbaren Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

9.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

        ClientAliveInterval 180

10.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11.	Richten Sie keinen SWAP-Bereich auf dem Betriebssystemdatenträger ein:
    
    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` an:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

        # sudo subscription-manager unregister

13.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

        # sudo waagent -force -deprovision 
        # export HISTSIZE=0
        # logout

14.	Fahren Sie den virtuellen Computer herunter, und konvertieren Sie die VMDK-Datei in eine VHD-Datei.

    Konvertieren Sie das Bild zuerst in das raw-Format:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Stellen Sie sicher, dass das raw-Image auf 1 MB ausgerichtet ist und runden Sie andernfalls die Größe auf 1 MB auf:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Konvertieren Sie den raw-Datenträger in das vhd-Format mit festgelegter Größe:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über VMWare###

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

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu entfernen:

        rhgb quiet crashkernel=auto

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die crashkernel-Option kann bei Bedarf konfiguriert gelassen werden. Beachten Sie jedoch, dass dieser Parameter den verfügbaren Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

6.	Sobald Sie die oben beschriebene Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der Grub-Konfiguration aus:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Fügen Sie Hyper-V-Module zu initramfs hinzu:

    Bearbeiten Sie `/etc/dracut.conf` und fügen Sie Inhalt hinzu:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Erstellen Sie initramfs neu:

        # dracut –f -v

8.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

        ClientAliveInterval 180

9.	Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Fedora EPEL 6-Repository übertragen. Aktivieren Sie das EPEL-Repository, indem Sie den folgenden Befehl ausführen:


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10.	Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein. Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` an:

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

    Stellen Sie sicher, dass das raw-Image auf 1 MB ausgerichtet ist und runden Sie andernfalls die Größe auf 1 MB auf:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Konvertieren Sie den raw-Datenträger in das vhd-Format mit festgelegter Größe:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##Mit Kickstart-Datei automatisch aus ISO vorbereiten


### <a id="rhel7xkickstart"> </a>Vorbereiten einer virtuellen RHEL 7.1-/7.2-Maschine über eine Kickstart-Datei###


1.	Erstellen Sie die Kickstart-Datei mit folgendem Inhalt, und speichern Sie die Datei. Weitere Informationen zur Kickstartinstallation finden Sie unter [Kickstartinstallationen](https://access.redhat.com/documentation/de-DE/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).


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

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

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

 

2.	Legen Sie die Kickstart-Datei an einem Ort ab, der aus dem gesamten Installationssystem erreichbar ist.
 
3.	Erstellen Sie in Hyper-V Manager einen neuen virtuellen Computer. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** die Option **Virtuelle Festplatte später zuordnen** aus und schließen Sie den Assistenten für neue virtuelle Computer ab.

4.	Öffnen Sie die VM-Einstellungen:

    a. Ordnen Sie dem virtuellen Computer eine neue virtuelle Festplatte zu und wählen Sie **VHD-Format** und **Feste Größe** aus.
    
    b. Ordnen Sie die ISO-Installation dem DVD-Laufwerk zu.

    c. Legen Sie fest, dass BIOS von der CD starten soll.

5.	Starten Sie den virtuellen Computer. Wenn die Installationsanweisungen angezeigt werden, drücken Sie die **TAB-TASTE**, um die Startoptionen zu konfigurieren.

6.	Geben Sie am Ende der Startoptionen `inst.ks=<the location of the Kickstart file>` ein und drücken Sie die **EINGABETASTE**.

7.	Warten Sie, bis die Installation abgeschlossen ist. Der virtuelle Computer wird dann automatisch heruntergefahren. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

##Bekannte Probleme
Es gibt bekannte Probleme beim Verwenden von RHEL 7.1 in Hyper-V und Azure.

###Problem: Einfrieren von Datenträger-E/A 

Dieses Problem kann bei häufigen Datenträger-E/A-Aktivitäten mit RHEL 7.1 in Hyper-V und Azure auftreten.

Reproduktionsrate:

Dieses Problem tritt zeitweise auf, jedoch öfter während häufigen Datenträger-E/A-Vorgängen in Hyper-V und Azure.

    
[AZURE.NOTE]Dieses bekannte Problem wurde von Red Hat bereits behoben. Um die entsprechenden Fixes zu installieren, führen Sie folgenden Befehl aus:

    # sudo yum update


## Nächste Schritte
Nun können Sie mit Ihrer Red Hat Enterprise Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Falls Sie zum ersten Mal Azure verwenden und die VHD-Datei in Azure hochladen, können Sie sich an die Schritte 2 und 3 in [diesem Leitfaden](virtual-machines-linux-create-upload-vhd.md) halten.
 
Weitere Informationen zu den Hypervisoren, die zum Ausführen von Red Hat Enterprise Linux zertifiziert sind, finden Sie auf der [Red Hat-Website](https://access.redhat.com/certified-hypervisors).

<!---HONumber=AcomDC_0121_2016-->