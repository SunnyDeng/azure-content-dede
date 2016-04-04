<properties
	pageTitle="Vorbereiten einer Debian Linux-VHD | Microsoft Azure"
	description="Erfahren Sie, wie Debian 7- und 8-VHD-Dateien für die Bereitstellung in Azure erstellt werden."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




# Vorbereiten einer Debian-VHD für Azure

## Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits ein Debian Linux-Betriebssystem aus einer ISO-Datei von der [Debian-Website](https://www.debian.org/distrib/) auf eine virtuelle Festplatte installiert haben. Es gibt mehrere Tools zum Erstellen von VHD-Dateien. Hyper-V ist nur ein Beispiel. Anweisungen zur Verwendung von Hyper-V finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](https://technet.microsoft.com/library/hh846766.aspx).


## Installationshinweise

- Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem **Convert-VHD-Cmdlet** in das VHD-Format konvertieren.
- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. LVM oder RAID kann wahlweise auf Datenträgern verwendet werden.
- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Azure-Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen dazu finden Sie in den folgenden Schritten.
- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.


## Debian 7.x und 8.x

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2. Klicken Sie auf **Verbinden**, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3. Kommentieren Sie die Zeile für **Deb-CD-ROM** in `/etc/apt/source.list`, wenn Sie den virtuellen Computer mit einer ISO-Datei einrichten.

4. Bearbeiten Sie die Datei `/etc/default/grub` und ändern Sie den **GRUB\_CMDLINE\_LINUX**-Parameter wie folgt, um zusätzliche Kernelparameter für Azure einzubinden.

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. Erstellen Sie Grub neu und führen Sie Folgendes aus:

        # sudo update-grub

6. Installieren Sie die Abhängigkeitspakete für Azure Linux Agent:

        # apt-get install -y git parted

7.	Installieren Sie entsprechend der [Anleitung](virtual-machines-linux-update-agent.md) den Azure Linux-Agent aus GitHub, und wählen Sie Version 2.0.14 aus:

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. Setzen Sie den virtuellen Computer zurück und bereiten Sie ihn für die Bereitstellung in Azure vor. Führen Sie Folgendes aus:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Klicken Sie im Hyper-V-Manager auf **Aktion -> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## Verwenden eines Credativ-Skripts zum Erstellen einer Debian-VHD

Auf der Credativ-Website steht ein Skript zu Verfügung, mit dem Sie die Debian-VHD automatisch erstellen können. Sie können es [hier](https://gitlab.credativ.com/de/azure-manage) herunterladen und auf Ihrer Linux-VM installieren. Zum Erstellen einer Debian-VHD (z. B. Debian 7) führen Sie Folgendes aus:

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

Falls bei der Verwendung dieses Skripts ein Problem auftritt, wenden Sie sich [hier](https://gitlab.credativ.com/groups/de/issues) an Credativ.

## Nächste Schritte

Sie können jetzt mit Ihrer Debian-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie die VHD-Datei zum ersten Mal in Azure hochladen, führen Sie die Schritte 2 und 3 in [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](virtual-machines-linux-classic-create-upload-vhd.md) aus.

<!---HONumber=AcomDC_0323_2016-->