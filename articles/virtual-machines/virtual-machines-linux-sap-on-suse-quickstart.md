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
   ms.date="02/12/2016"
   ms.author="hermannd"/>

# Testen von SAP NetWeaver auf Microsoft Azure SUSE-Linux-VMs


Dieser Artikel beschreibt verschiedene Aspekte, die Sie berücksichtigen müssen, wenn Sie SAP NetWeaver auf virtuellen Microsoft Azure SUSE-Linux-Computern (VMs) testen. Derzeit liegt keine offizielle SAP-Aussage zur Unterstützung für diese virtuellen Computer vor. Dennoch können Sie Tests und Vorführungen ausführen oder Prototypen erstellen, sofern sie nicht von offizieller SAP-Unterstützung abhängig sind.

Die folgenden Informationen sollten Ihnen helfen, einige potenzielle Probleme zu vermeiden.

## SUSE-Images auf Azure zum Testen von SAP

Verwenden Sie zum Testen von SAP in Azure nur SUSE Linux Enterprise Server (SLES) 11 SP4 und SLES 12. Ein spezielles SUSE-Image befindet sich im Azure Marketplace („SLES 11 SP3 for SAP CAL“). Dies ist jedoch nicht für die allgemeine Nutzung vorgesehen. Verwenden Sie dieses Image nicht, weil es für die [SAP Cloud Appliance Library](https://cal.sap.com/) vorgesehen ist.

Für alle neuen Tests in Azure sollte der Azure-Ressourcen-Manager verwendet werden. Für die Suche nach SUSE SLES-Images und Versionen mit Azure PowerShell oder der Azure Befehlszeilenschnittstelle (CLI) verwenden Sie die nachstehenden Befehle. Die Ausgabe kann dann z. B. verwendet werden, um das OS-Image in einer JSON-Vorlage für die Bereitstellung einer neuen SUSE Linux-VM zu definieren. Die folgenden PowerShell-Befehle gelten für Azure PowerShell Version 1.0.1 und höher.

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

Der Agent namens „WALinuxAgent“ ist Teil der SLES-Images im Azure Marketplace. Informationen zur manuellen Installation (z. B. beim Hochladen einer SLES OS-VHD [Virtual Hard Disk, virtuelle Festplatte] vom lokalen Standort) finden Sie unter:

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azure](virtual-machines-linux-endorsed-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Anfügen von Azure-Datenträgern an eine Azure Linux-VM

Stellen Sie niemals Azure-Datenträger in einer Azure Linux-VM per Geräte-ID bereit. Verwenden Sie stattdessen den Universally Unique Identifier (UUID). Seien Sie vorsichtig, wenn Sie grafische Tools z. B. für das Einbinden von Azure-Datenträgern verwenden. Überprüfen Sie die Einträge in "/etc/fstab".

Das Problem mit der Geräte-ID besteht darin, dass diese sich ändern kann und die Azure-VM sich dann möglicherweise beim Startvorgang aufhängt. Sie könnten das Problem dann durch Hinzufügen des Parameters „nofail“ in „/etc/fstab“ beheben. Beachten Sie jedoch, dass Anwendungen bei Verwendung von „nofail“ den Bereitstellungspunkt wie zuvor verwenden könnten, und für den Fall, dass ein externer Azure-Datenträger während des Startvorgangs nicht eingebunden wurde, in das Stammdateisystem schreiben könnten.

Die einzige Ausnahme zur Einbindung per UUID ist das Anfügen eines Betriebssystem-Datenträgers zu Problembehandlungszwecken. Dies ist im folgenden Abschnitt beschrieben.

## Problembehandlung bei SUSE-VM, auf die kein Zugriff mehr möglich ist

Es kann zu Situationen kommen, in denen eine SUSE-VM unter Azure beim Startvorgang hängen bleibt (z. B. aufgrund eines Fehlers bei der Einbindung von Datenträgern). Sie können dieses Problem mithilfe der Startdiagnosefunktion für Azure Virtual Machines v2 im Azure-Portal überprüfen. Weitere Informationen finden Sie unter [Boot Diagnostics for Virtual Machines v2](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) (Startdiagnose für Virtual Machines v2).

Eine Möglichkeit zur Lösung des Problems ist, den Betriebssystem-Datenträger aus der beschädigten VM einer anderen SUSE-VM in Azure anzufügen. Nehmen Sie dann entsprechende Änderungen wie die Bearbeitung von „/etc/fstab“ oder das Entfernen von Netzwerk-Udev-Regeln vor, wie im nächsten Abschnitt beschrieben.

Es ist aber wichtig, einen Aspekt zu berücksichtigen. Beim Bereitstellen mehrerer SUSE-VMs über das gleiche Azure Marketplace-Image (z. B. SLES 11 SP4) wird der Betriebssystem-Datenträger immer mit der gleichen UUID eingebunden. Darum führt die Verwendung der UUID zum Anfügen eines Betriebssystem-Datenträgers von einer anderen VM, die mit dem gleichen Azure Marketplace-Image bereitgestellt wurde, zu zwei identischen UUIDs. Dies führt zu Problemen und kann bedeuten, dass die für die Problembehandlung bestimmte VM vom angefügten und fehlerhaften Betriebssystem-Datenträger gestartet wird, anstatt vom ursprünglichen Datenträger.

Dies kann auf zwei Arten vermieden werden:

* Verwenden Sie ein anderes Azure Marketplace-Image für die Problembehandlungs-VM (z. B. SLES 12 anstelle von SLES 11 SP4).
* Vermeiden Sie das Anfügen des fehlerhaften Betriebssystem-Datenträgers von einer anderen VM per UUID, und verwenden Sie etwas anderes.

## Hochladen einer SUSE-VM vom lokalen Standort nach Azure

Eine Beschreibung der Schritte zum Hochladen einer SUSE-VM aus einem lokalen Standort in Azure finden Sie unter [Prepare a SLES or openSUSE virtual machine for Azure](virtual-machines-linux-create-upload-vhd-suse.md) (Vorbereiten eines virtuellen SLES- oder openSUSE-Computers für Azure).

Wenn Sie eine VM hochladen möchten, ohne die Bereitstellung zum Schluss aufzuheben (z. B., um eine vorhandene SAP-Installation sowie den Hostnamen zu behalten), müssen Sie Folgendes beachten:

* Vergewissern Sie sich, dass der Betriebssystem-Datenträger über die UUID und nicht über die Geräte-ID eingebunden wurde. Das alleinige Ändern der UUID in „etc/fstab“ ist für die Betriebssystemfestplatte nicht ausreichend. Zusätzlich dürfen Sie nicht vergessen, den Bootloader über „YaST“ oder durch Bearbeiten von „/boot/grub/menu.lst“ anzupassen.
* Für den Fall, dass Sie das VHDX-Format für den SUSE-Betriebssystem-Datenträger verwendet und dieses für den Upload nach Azure nach VHD konvertiert haben, wird das Netzwerkgerät höchstwahrscheinlich von „eth0“ in „eth1“ geändert. Um beim späteren Starten in Azure Probleme zu vermeiden, sollte das Netzwerkgerät später wieder in „eth0“ umbenannt werden, wie in [Fixing eth0 in cloned SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/) (Korrigieren von „eth0“ in geklonter SLES 11 VMware) beschrieben.

Neben den Erläuterungen in diesem Artikel empfehlen wir zudem, Folgendes zu entfernen:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Sie können auch den Azure Linux Agent (waagent) installieren, um potenzielle Probleme zu vermeiden, solange es nicht mehrere Netzwerkkarten gibt.

## Bereitstellen einer SUSE-VM in Azure

Neue SUSE-VMs sollten Sie über JSON-Vorlagendateien im neuen Azure Resource Manager-Modell erstellen. Nach der Erstellung der JSON-Vorlagendatei können Sie die VM mithilfe des folgenden CLI-Befehls als Alternative zu PowerShell bereitstellen:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Weitere Informationen zu JSON-Vorlagendateien finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen-Vorlagen](resource-group-authoring-templates.md) und [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/).

Weitere Informationen zu CLI und Azure Resource Manager finden Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager](xplat-cli-azure-resource-manager.md).

## SAP-Lizenz und Hardwareschlüssel

Für die offizielle SAP-Azure-Zertifizierung wurde ein neuer Mechanismus eingeführt, um den SAP-Hardwareschlüssel zu berechnen, der für die SAP-Lizenz verwendet wird. Der SAP-Kernel musste angepasst werden, um diesen Mechanismus verwenden zu können. Die aktuellen SAP-Kernelversionen für Linux enthalten diese Codeänderung nicht. Daher kann es in bestimmten Situationen vorkommen (z. B. bei einer Änderung der Größe der Azure-VM), dass sich der SAP-Hardwareschlüssel ändert und die SAP-Lizenz ihre Gültigkeit verliert.

## SUSE-Sapconf-Paket

SUSE stellt ein Paket namens „sapconf“ bereit, das einen Satz an SAP-spezifischen Einstellungen verwaltet. Weitere Informationen zur Funktionsweise dieses Pakets sowie zu seiner Installation und Verwendung finden Sie unter [Using sapconf to prepare a SUSE Linux Enterprise Server to run SAP systems](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) (Verwendung von „sapconf“ zur Vorbereitung eines SUSE Linux Enterprise Servers zum Ausführen von SAP-Systemen) und [What is sapconf or how to prepare a SUSE Linux Enterprise Server for running SAP systems?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems) (Was ist „sapconf“ – oder wie wird ein SUSE Linux Enterprise Server für die Ausführung von SAP-Systemen vorbereitet?).

## NFS-Freigabe in verteilten SAP-Installationen

Im Falle einer verteilten Installation, in der z. B. die Datenbank und die SAP-Anwendungsserver in separaten VMs installiert werden sollen, können Sie das Verzeichnis „/sapmnt“ über das Network File System (NFS) freigeben. Wenn nach der Erstellung der NFS-Freigabe für „/sapmnt“ Probleme mit den Installationsschritten auftreten, prüfen Sie, ob „no\_root\_squash“ für die Freigabe festgelegt wurde.

## Logische Datenträger (LVMs)

Logical Volume Manager (LVM) wurde in Azure nicht vollständig validiert. Wenn Sie einen großen, aus mehreren Azure-Datenträgern bestehenden logischen Datenträger benötigen (z. B. für die SAP-Datenbank), sollten Sie „mdadm“ verwenden. Unter [Konfigurieren von Software-RAID unter Linux](virtual-machines-linux-configure-raid.md) erfahren Sie, wie Sie Linux-RAID unter Azure mithilfe von „mdadm“ einrichten.


## Azure SUSE Repository

Für den Fall, dass ein Problem mit dem Zugriff auf das standardmäßige Azure SUSE Repository auftritt, können Sie einen einfachen Befehl für das Zurücksetzen nutzen. Dieses Problem könnte auftreten, wenn Sie ein privates Betriebssystemimage in einer Azure-Region erstellen und das Image dann in eine andere Region kopieren, in der Sie neue VMs basierend auf diesem privaten Betriebssystemimage bereitstellen möchten. Führen Sie einfach den folgenden Befehl in der VM aus:

   ```
   service guestregister restart
   ```

## Gnome Desktop

Wenn Sie Gnome Desktop für die Installation eines vollständigen SAP-Demosystems in einer VM – einschließlich SAP-Benutzeroberfläche, Browser und SAP-Verwaltungskonsole – verwenden möchten, hier ein kleiner Tipp für die Installation in Azure SLES-Images:

   Für SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Für SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## SAP-Unterstützung für Oracle unter Linux in der Cloud

Es gibt eine Supporteinschränkung von Oracle unter Linux in virtualisierten Umgebungen. Dies ist zwar kein Azure-spezifisches Thema, jedoch müssen Sie damit vertraut sein. In öffentlichen Clouds wie Azure unterstützt SAP Oracle unter SUSE oder Red Hat nicht. Um dieses Thema zu diskutieren, wenden Sie sich bitte direkt an Oracle.

<!---HONumber=AcomDC_0323_2016-->