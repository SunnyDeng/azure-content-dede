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


Hier eine Liste der Punkte, die beim Testen von SAP NetWeaver auf Microsoft Azure SUSE-Linux-VMs zu beachten sind. Zurzeit gibt es noch keine offizielle Supportzusage von SAP für SAP-Linux-Azure. Dennoch können Kunden Tests durchführen und Demos oder Prototypen erstellen, sofern sie nicht vom offiziellen SAP-Support abhängig sind.

Die folgende Liste soll Ihnen einfach nur helfen, diverse potenzielle Fallstricke zu erkennen, um das Leben einfacher zu machen:



## SUSE-Images auf Microsoft Azure zum Testen von SAP 

Für SAP-Tests auf Azure sollten nur SLES 11 SP4 und SLES 12 verwendet werden. Ein spezielles SUSE-Image befindet sich im Azure-Katalog für Images: "SLES 11 SP3 for SAP CAL". Dies ist jedoch nicht für die allgemeine Nutzung vorgesehen. Es ist für die SAP Cloud Appliance Library-Lösung mit Namen "SAP CAL" reserviert (<https://cal.sap.com/>). Es gab keine Möglichkeit, dieses Image für die Öffentlichkeit auszublenden. Also verwenden Sie es einfach nicht.

Für alle neuen Tests in Azure sollte der Azure-Ressourcen-Manager verwendet werden. Für die Suche nach SUSE SLES-Images und Versionen mit Azure Powershell oder CLI verwenden Sie die nachstehenden Befehle. Die Ausgabe kann z. B. verwendet werden, um das OS-Image in einer json-Vorlage für die Bereitstellung einer neuen SUSE Linux-VM zu definieren:

* Suchen nach vorhandenen Herausgebern einschließlich SUSE:

   ```
   PS  : Get-AzureVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Suchen nach vorhandenen Angeboten von SUSE:
      
   ```
   PS  : Get-AzureVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
      
* Suchen nach SUSE SLES-Angeboten:
      
   ```
   PS  : Get-AzureVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```
      
* Suchen nach einer bestimmten Version einer SLES-SKU:
      
   ```
   PS  : Get-AzureVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```
     
## Installieren von WALinuxAgent in einer SUSE-VM 
 
Der Agent ist Teil der SLES-Images im Azure-Katalog. Hier die Orte, an denen Sie Informationen zur manuellen Installation finden können (z. B. beim Hochladen einer SLES OS-VHD [Virtual Hard Disk, virtuelle Festplatte] vom lokalen Standort):

<http://software.opensuse.org/package/WALinuxAgent>

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/>

<https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/>

## Anfügen von Azure-Datenträgern an eine Azure Linux-VM

Stellen Sie NIEMALS Azure-Datenträger in einer Azure Linx-VM per Geräte-ID bereit. Verwenden Sie stattdessen die UUID. Seien Sie vorsichtig, wenn Sie beispielsweise grafische Tools für das Bereitstellen von Azure-Datenträgern verwenden. Überprüfen Sie die Einträge in "/etc/fstab". Das Problem mit der Geräte-ID besteht darin, dass diese sich ändern kann und die Azure-VM sich dann möglicherweise beim Startvorgang aufhängt. Das Problem kann mit dem Hinzufügen des Parameters "nofail" in "/etc/fstab" behoben werden. Beachten Sie jedoch, dass Anwendungen bei Verwendung von "nofail" den Bereitstellungspunkt wie zuvor verwenden könnten und für den Fall, dass ein externer Azure-Datenträger während des Startvorgangs nicht bereitgestellt wurde, in das Stammdateisystem schreiben könnten.

## Hochladen einer SUSE-VM vom lokalen Standort nach Azure

Diese Schritte werden im folgenden Blog beschrieben: <https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/>

Wenn Sie eine VM hochladen möchten, ohne die Bereitstellung zum Schluss aufzuheben, z. B. eine vorhandene SAP-Installation sowie den Hostnamen, müssen die folgenden Elemente überprüft werden:

* Vergewissern Sie sich, dass der Betriebssystemdatenträger über die UUID und NICHT über die Geräte-ID bereitgestellt wurde. Das alleinige Ändern der UUID in "/etc/fstab" ist für die Betriebssystemfestplatte NICHT ausreichend. Möglicherweise haben Sie auch vergessen, das Startladeprogramm über "yast" oder durch Bearbeiten von "boot/grub/menu.lst" anzupassen.
* Für den Fall, dass Sie das VHDX-Format für die SUSE-Betriebssystemfestplatte verwendet und dieses für den Upload nach Azure nach VHD konvertiert haben, wurde das Netzwerkgerät höchstwahrscheinlich von "eth0" in "eth1" geändert. Um beim späteren Starten in Azure Probleme zu vermeiden, sollte das Netzwerkgerät wieder in "eth0" umbenannt werden, wie hier beschrieben: <https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/>

Neben den Erläuterungen in diesem Artikel empfiehlt es sich zudem, Folgendes zu entfernen:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Mit der Installation von "waagent" sollten sich ebenfalls potenzielle Probleme vermeiden lassen, solange es nicht mehrere Netzwerkkarten gibt.

## SAP-Lizenz und Hardwareschlüssel

Für die offizielle SAP-Microsoft Azure-Zertifizierung wurde ein neuer Mechanismus eingeführt, um den SAP-Hardwareschlüssel zu berechnen, der für die SAP-Lizenz verwendet wird. Der SAP-Kernel musste angepasst werden, um diesen Mechanismus verwenden zu können. Die aktuellen SAP-Kernelversionen für Linux enthalten diese Codeänderung nicht. Daher kann es in bestimmten Situationen vorkommen (z. B. bei einer Änderung der Größe der Azure-VM), dass sich der SAP-Hardwareschlüssel ändert und die SAP-Lizenz ihre Gültigkeit verliert.

## NFS-Freigabe in verteilten SAP-Installationen

Im Falle einer verteilten Installation, in der z. B. die Datenbank und die SAP-Anwendungsserver in separaten VMs installiert werden sollen, kann das Verzeichnis "/sapmnt" via NFS freigegeben werden. Wenn nach der Erstellung der NFS-Freigabe für "/sapmnt" Probleme mit den Installationsschritten auftreten, prüfen Sie, ob "no\_root\_squash" für die Freigabe festgelegt wurde. Darin bestand die Lösung bei einem internen Testfall.


## Logische Datenträger (LVMs)

LVMs wurden in Azure nicht vollständig validiert. Wenn ein großer logischer Datenträger bestehend aus mehreren Azure-Datenträgern benötigt wird (z. B. für die SAP-Datenbank), sollte "mdadm" verwendet werden. Hierzu gibt es einen nützlichen Blog, in dem beschrieben wird, wie Linux-RAID in Azure mit "mdadm" eingerichtet wird:

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/>


## SUSE-Azure-Repository

Für den Fall, dass ein Problem mit dem Zugriff auf das standardmäßige Azure-SUSE-Repository auftritt, gibt es einen einfachen Befehl für das Zurücksetzen. Dieses Problem kann auftreten, wenn ein privates Betriebssystemimage in einer Azure-Region erstellt und das Image dann in eine andere Region kopiert wird, in der neue VMs basierend auf diesem privaten Betriebssystemimage bereitstellt werden sollen. Führen Sie einfach den folgenden Befehl in der VM aus:

   ```
   service guestregister restart
   ```

## Gnome Desktop

Wenn Gnome Desktop für die Installation eines vollständigen SAP-Demosystems in einer VM einschließlich SAP-Benutzeroberfläche, Browser, SAP-Verwaltungskonsole usw. verwendet werden soll, hier ein kleiner Tipp für die Installation in Azure SLES-Images:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```
      
   SLES 12
   
   ```
   zypper in -t pattern gnome-basic
   ```

## SAP-Oracle-Unterstützung unter Linux in der Cloud
 
Dies ist eher ein allgemeines als ein Azure-spezifisches Thema. Dennoch ist das Verständnis wichtig. Es gibt eine Supporteinschränkung von Oracle unter Linux in virtualisierten Umgebungen. Unter dem Strich bedeutet dies, dass SAP Oracle unter SUSE oder RedHat in einer öffentlichen Cloud wie Azure nicht unterstützt. Die Kunden sollten sich direkt an Oracle wenden, um das Thema zu diskutieren.

<!---HONumber=AcomDC_1203_2015-->