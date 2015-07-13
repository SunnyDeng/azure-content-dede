<properties
	pageTitle="Einführung zu Linux in Azure – Azure-Lernprogramm"
	description="Erfahren Sie, wie Sie virtuelle Linux-Computer in Azure verwenden."
	services="virtual-machines"
	documentationCenter="python"
	authors="szarkos"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/11/2015"
	ms.author="szark"/>





#Einführung in Linux in Azure

Dieses Thema bietet einen Überblick über einige Aspekte der Verwendung virtueller Linux-Computer in der Azure-Cloud. Die Bereitstellung eines virtuellen Linux-Computers ist ein unkomplizierter Prozess, wenn Sie ein Image aus dem Katalog verwenden.

## Inhaltsverzeichnis ##

* [Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel](#authentication)
* [Erlangen von Superuser-Berechtigungen mit sudo](#superuserprivileges)
* [Firewall-Konfiguration](#firewallconfiguration)
* [Hostnamen-Änderungen](#hostnamechanges)
* [Image-Erfassung virtueller Computer](#virtualmachine)
* [Anfügen von Datenträgern](#attachingdisks)

## <a id="authentication"></a>Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel

Wenn Sie mithilfe des Azure-Verwaltungsportals einen virtuellen Linux-Computer erstellen, werden Sie aufgefordert, einen Benutzernamen, ein Kennwort oder einen öffentlichen SSH-Schlüssel anzugeben. Bei der Auswahl eines Benutzernamens während der Bereitstellung virtueller Linux-Computer gibt es folgende Einschränkungen: bereits im virtuellen Computer vorhandene Namen von Systemkonten (UID <100) sind nicht zulässig, z. B. 'root'.


 - Weitere Informationen finden Sie unter [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-tutorial.md)
 - Siehe [Verwenden von SSH mit Linux in Azure](../linux-use-ssh-key.md)


## <a id="superuserprivileges"></a> Erlangen von Superuser-Berechtigungen`sudo`

Das Benutzerkonto, das während der Bereitstellung der virtuellen Computerinstanz auf Azure angegeben wird, ist ein Konto mit weit reichenden Berechtigungen. Dieses Konto wird vom Azure Linux-Agent konfiguriert, damit die Berechtigungen mithilfe des Hilfsprogramms `sudo` auf "root" (Superuser-Konto) erweitert werden können. Sobald Sie über dieses Benutzerkonto angemeldet sind, können Sie mit dieser Befehlssyntax Befehle als "root" ausführen.

	# sudo <COMMAND>

Optional können Sie über **sudo -s** eine Root-Shell abrufen.

- Siehe [Verwenden von Rootberechtigungen auf virtuellen Linux-Computern in Azure](virtual-machines-linux-use-root-privileges.md)


## <a id="firewallconfiguration"></a>Firewall-Konfiguration

Azure bietet einen integrierten Paketfilter, der die Konnektivität auf die im Verwaltungsportal angegebenen Ports beschränkt. Standardmäßig ist SSH der einzige erlaubte Port. Sie können den Zugriff auf zusätzliche Ports auf Ihrem virtuellen Linux-Computer aktivieren, indem Sie im Verwaltungsportal Endpunkte konfigurieren.

 - Siehe: [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-set-up-endpoints.md)

Die Linux-Images im Azure-Katalog aktivieren nicht standardmäßig die *iptables*-Firewall. Bei Bedarf kann die Firewall für die Bereitstellung einer zusätzlichen Filterung konfiguriert werden.


## <a id="hostnamechanges"></a>Hostnamen-Änderungen

Wenn Sie eine Instanz eines Linux-Images zum ersten Mal bereitstellen, müssen Sie einen Hostnamen für den virtuellen Computer bereitstellen. Wenn der virtuelle Computer läuft, wird dieser Hostname auf den Plattform-DNS-Servern veröffentlicht, so dass mehrere miteinander verbundene virtuelle Computer IP-Adresssuchen anhand von Hostnamen durchführen können.

Wenn nach der Bereitstellung eines virtuellen Computers Hostnamenänderungen gewünscht werden, verwenden Sie den Befehl

	# sudo hostname <newname>

Der Azure Linux Agent enthält eine Funktion zur automatischen Erkennung dieser Namensänderung und zur entsprechenden Konfiguration des virtuellen Computers für eine Beibehaltung dieser Änderung und für ihre Veröffentlichung auf DNS-Plattformservern.

 - [Benutzerhandbuch für Azure Linux-Agent](virtual-machines-linux-agent-user-guide.md)

### cloud-init
**Ubuntu**- und **CoreOS**-Images nutzen cloud-init bei Azure, was zusätzliche Funktionen für das Bootstrapping eines virtuellen Computers bietet.

 - [Einfügen von benutzerdefinierten Daten](virtual-machines-how-to-inject-custom-data.md)
 - [Custom Data and Cloud-Init on Microsoft Azure (Benutzerdefinierte Daten und Cloud-Init in Microsoft Azure, in englischer Sprache)](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [AzureSwapPartitions (in englischer Sprache)](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Verwenden von CoreOS in Azure](virtual-machines-linux-coreos-how-to.md)


## <a id="virtualmachine"></a>Image-Erfassung virtueller Computer

Azure bietet die Möglichkeit, den Status eines vorhandenen virtuellen Computers in einem Image zu erfassen, das danach verwendet kann, um zusätzliche virtuelle Computerinstanzen bereitzustellen. Der Azure Linux Agent kann verwendet werden, um einige während des Bereitstellungsprozesses durchgeführten Anpassungen zurückzusetzen. Sie können die Schritte unten ausführen, um einen virtuellen Computer als Image zu erfassen.

1. Führen Sie **waagent -deprovision** aus, um bei Bereitstellungen durchgeführte Anpassungen rückgängig zu machen. Oder optional **waagent -deprovision+user**, um das während der Bereitstellung angegebene Benutzerkonto und alle damit zusammenhängenden Daten zu löschen.

2. Schalten Sie den virtuellen Computer aus.

3. Klicken Sie im Verwaltungsportal auf *Erfassen*, oder verwenden Sie Powershell oder Befehlszeilen-Schnittstellentools, um den virtuellen Computer als Image zu erfassen.

 - Siehe: [Erfassen eines virtuellen Linux-Computers zur Verwendung als Vorlage](virtual-machines-linux-capture-image.md)


## <a id="attachingdisks"></a>Anfügen von Datenträgern

An jeden virtuellen Computer ist ein temporärer lokaler *Ressourcendatenträger* angefügt. Da die Daten auf einem Ressourcendatenträger bei Neustarts verloren gehen können, werden Ressourcendatenträger oft von Anwendungen und Prozessen zur **temporären** Datenspeicherung auf dem virtuellen Computer verwendet. Außerdem dienen sie zur Speicherung von Auslagerungsdateien für das Betriebssystem.

Unter Linux wird der Ressourcendatenträger normalerweise vom Azure Linux Agent verwaltet und automatisch an **/mnt/resource** (oder auf Ubuntu-Images an **/mnt**) angefügt.


	>[AZURE.NOTE] Note that the resource disk is a **temporary** disk, and might be deleted and reformatted when the VM is rebooted.

Der Datenträger unter Linux kann vom Kernel den Namen `/dev/sdc` erhalten. Die Benutzer müssen diese Ressource partitionieren, formatieren und bereitstellen. Dieser Vorgang wird Schritt für Schritt im Lernprogramm [Anfügen eines Datenträgers an einen virtuellen Computer](virtual-machines-linux-how-to-attach-disk.md) beschrieben.

 - **Siehe auch:** [Konfigurieren des Software-RAID unter Linux](virtual-machines-linux-configure-raid.md)
 

<!---HONumber=July15_HO1-->