<properties
   pageTitle="Testen von SAP NetWeaver in Microsoft Azure SUSE-Linux-VMs | Microsoft Azure"
   description="Testen von SAP NetWeaver in Microsoft Azure SUSE-Linux-VMs"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="11/26/2015"
   ms.author="hermannd"/>

# Testen von SAP NetWeaver in Microsoft Azure SUSE-Linux-VMs


Hier eine Liste der Punkte, die Sie beim Testen von SAP NetWeaver auf Microsoft Azure SUSE-Linux-VMs beachten müssen. Zurzeit gibt es noch keine offizielle Supportzusage von SAP für SAP-Linux-Azure. Dennoch können Kunden Tests und Vorführungen durchführen oder Prototypen erstellen, sofern sie nicht vom offiziellen SAP-Support abhängig sind.

Die folgenden Informationen sollten Ihnen helfen, einige potenzielle Probleme zu vermeiden.



## SUSE-Images auf Microsoft Azure zum Testen von SAP

Verwenden Sie zum Testen von SAP in Azure nur SUSE Linux Enterprise Server (SLES) 11 SP4 und SLES 12. Ein spezielles SUSE-Image befindet sich im Azure Marketplace („SLES 11 SP3 for SAP CAL“). Dies ist jedoch nicht für die allgemeine Nutzung vorgesehen. Es ist für die [SAP Cloud Appliance Library](https://cal.sap.com/) vorgesehen. Es bestand keine Möglichkeit, dieses Image für die Allgemeinheit auszublenden. Also verwenden Sie es einfach nicht.

Für alle neuen Tests in Azure sollte der Azure-Ressourcen-Manager verwendet werden. Für die Suche nach SUSE SLES-Images und Versionen mit Azure PowerShell oder der Azure Befehlszeilenschnittstelle (CLI) verwenden Sie die nachstehenden Befehle. Die Ausgabe kann dann z. B. verwendet werden, um das OS-Image in einer JSON-Vorlage für die Bereitstellung einer neuen SUSE Linux-VM zu definieren. Die folgenden PowerShell-Befehle gelten für Azure PowerShell Version 1.0.1 oder höher.

* Suchen nach vorhandenen Herausgebern einschließlich SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Suchen nach vorhandenen Angeboten von SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Suchen nach SUSE SLES-Angeboten:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Suchen nach einer bestimmten Version einer SLES-SKU:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Installieren von WALinuxAgent in einer SUSE-VM

Der Agent namens „WALinuxAgent“ ist Teil der SLES-Images im Azure Marketplace. Hier die Orte, an denen Sie Informationen zur manuellen Installation finden können (z. B. beim Hochladen einer SLES OS-VHD [Virtual Hard Disk, virtuelle Festplatte] vom lokalen Standort):

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azure](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Anfügen von Azure-Datenträgern an eine Azure Linux-VM

Stellen Sie niemals Azure-Datenträger in einer Azure Linux-VM per Geräte-ID bereit. Verwenden Sie stattdessen die UUID. Seien Sie vorsichtig, wenn Sie beispielsweise grafische Tools für das Bereitstellen von Azure-Datenträgern verwenden. Überprüfen Sie die Einträge in "/etc/fstab".

Das Problem mit der Geräte-ID besteht darin, dass diese sich ändern kann und die Azure-VM sich dann möglicherweise beim Startvorgang aufhängt. Sie können das Problem dann mit dem Hinzufügen des Parameters „nofail“ in „/etc/fstab“ beheben. Beachten Sie jedoch, dass Anwendungen bei Verwendung von „nofail“ den Bereitstellungspunkt wie zuvor verwenden könnten und für den Fall, dass ein externer Azure-Datenträger während des Startvorgangs nicht bereitgestellt wurde, in das Stammdateisystem schreiben könnten.

## Hochladen einer SUSE-VM vom lokalen Standort nach Azure

[Dieser Artikel](virtual-machines-linux-create-upload-vhd-suse.md) beschreibt die Schritte zum Hochladen einer SUSE VM in Azure von Ihrem lokalen Standort aus.

Wenn Sie eine VM hochladen möchten, ohne die Bereitstellung zum Schluss aufzuheben, z. B. um eine vorhandene SAP-Installation sowie den Hostnamen zu behalten, müssen Sie folgendes beachten:

* Vergewissern Sie sich, dass der Betriebssystemdatenträger über die UUID und nicht über die Geräte-ID bereitgestellt wurde. Das alleinige Ändern der UUID in „etc/fstab“ ist für die Betriebssystemfestplatte nicht ausreichend. Zusätzlich dürfen Sie nicht vergessen, den Bootloader über „YaST“ oder durch Bearbeiten von „boot/grub/menu.lst“ anzupassen.
* Für den Fall, dass Sie das VHDX-Format für die SUSE-Betriebssystemfestplatte verwendet und dieses für den Upload nach Azure nach VHD konvertiert haben, wurde das Netzwerkgerät höchstwahrscheinlich von „eth0“ in „eth1“ geändert. Um beim späteren Starten in Azure Probleme zu vermeiden, sollte das Netzwerkgerät später wieder in „eth0“ umbenannt werden, wie [hier](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/) beschrieben.

Neben den Erläuterungen in diesem Artikel empfehlen wir zudem, Folgendes zu entfernen:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Die Installation von Azure Linux Agent (waagent) sollte Ihnen ebenfalls dabei helfen, potenzielle Probleme zu vermeiden, solange es nicht mehrere Netzwerkkarten gibt.

## Bereitstellen einer SUSE-VM in Azure

Neue SUSE VMs sollten über JSON-Vorlagendateien im neuen Azure-Ressourcen-Manager-Modell erstellt werden. Nach der Erstellung der JSON-Vorlagendatei können Sie den virtuellen Computer mithilfe des folgenden CLI-Befehls als Alternative zu PowerShell bereitstellen:

   ``` azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` Weitere Informationen zu JSON-Vorlagen finden Sie in diesem [Artikel](resource-group-authoring-templates.md) und auf dieser [Webseite](https://azure.microsoft.com/documentation/templates/).

Weitere Informationen zur Befehlszeilenschnittstelle (CLI) und Azure-Ressourcen-Manager finden Sie in diesem [Artikel](xplat-cli-azure-resource-manager.md):

## SAP-Lizenz und Hardwareschlüssel

Für die offizielle SAP-Microsoft Azure-Zertifizierung wurde ein neuer Mechanismus eingeführt, um den SAP-Hardwareschlüssel zu berechnen, der für die SAP-Lizenz verwendet wird. Der SAP-Kernel musste angepasst werden, um diesen Mechanismus verwenden zu können. Die aktuellen SAP-Kernelversionen für Linux enthalten diese Codeänderung nicht. Daher kann es in bestimmten Situationen vorkommen (z. B. bei einer Änderung der Größe der Azure-VM), dass sich der SAP-Hardwareschlüssel ändert und die SAP-Lizenz ihre Gültigkeit verliert.

## SUSE-Sapconf-Paket

SUSE stellt ein Paket namens „Sapcon“ bereit, das für einen Satz an SAP-spezifischen Einstellungen zuständig ist. Ausführliche Informationen zu diesem Paket, dessen Funktionsweise und der Installation und Verwendung finden Sie in diesem [Blogeintrag zu SUSE](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) und in diesem [Blogeintrag zu SAP](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## NFS-Freigabe in verteilten SAP-Installationen

Im Falle einer verteilten Installation, in der z. B. die Datenbank und die SAP-Anwendungsserver in separaten VMs installiert werden sollen, kann das Verzeichnis „/sapmnt“ über das Network File System (NFS) freigegeben werden. Wenn nach der Erstellung der NFS-Freigabe für „/sapmnt“ Probleme mit den Installationsschritten auftreten, prüfen Sie, ob „no\_root\_squash“ für die Freigabe festgelegt wurde. Darin bestand die Lösung bei einem internen Testfall.


## Logische Datenträger (LVMs)

Logical Volume Manager (LVM) wurde in Azure nicht vollständig validiert. Wenn Sie einen großen logischen Datenträger bestehend aus mehreren Azure-Datenträgern benötigen(z. B. für die SAP-Datenbank), sollte „mdadm“ verwendet werden. [Dieser Artikel](virtual-machines-linux-configure-raid.md) beschreibt die Einrichtung von Linux-RAID in Azure mit „mdadm“.


## Azure SUSE Repository

Für den Fall, dass ein Problem mit dem Zugriff auf das standardmäßige Azure SUSE Repository auftritt, gibt es einen einfachen Befehl für das Zurücksetzen. Dieses Problem kann auftreten, wenn ein privates Betriebssystemimage in einer Azure-Region erstellt und das Image dann in eine andere Region kopiert wird, in der neue VMs basierend auf diesem privaten Betriebssystemimage bereitgestellt werden sollen. Führen Sie einfach den folgenden Befehl in der VM aus:

   ```
   service guestregister restart
   ```

## Gnome Desktop

Wenn Sie Gnome Desktop für die Installation eines vollständigen SAP-Demosystems in einer VM einschließlich SAP-Benutzeroberfläche, Browser, SAP-Verwaltungskonsole usw. verwenden wollen, hier ein kleiner Tipp für die Installation in Azure SLES-Images:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## SAP-Oracle-Unterstützung unter Linux in der Cloud

Es gibt eine Supporteinschränkung von Oracle unter Linux in virtualisierten Umgebungen. Dies ist ein allgemeines und kein Azure spezifisches Thema. Dennoch ist das Verständnis wichtig. In öffentlichen Clouds wie Azure werden Oracle, SUSE oder Red Hat nicht von SAP unterstützt. Die Kunden sollten sich direkt an Oracle wenden, um das Thema zu diskutieren.

<!---HONumber=AcomDC_0114_2016-->