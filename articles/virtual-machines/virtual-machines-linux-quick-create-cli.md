<properties
   pageTitle="Erstellen einer Linux-VM über die CLI | Microsoft Azure"
   description="Erstellen Sie eine neue Linux-VM unter Microsoft Azure mithilfe der Azure-CLI auf Mac, Linux oder Windows."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="03/04/2016"
   ms.author="v-livech"/>


# Erstellen einer Linux-VM über die Azure-CLI für Entwicklung und Tests

In diesem Thema wird veranschaulicht, wie Sie eine Linux-VM schnell in einer einfachen Azure-Umgebung mithilfe des `azure vm quick-create`-Befehls [der Azure-CLI](../xplat-cli-install.md) für Testversion, Tests und andere kurzlebige Szenarien erstellen. Der Zugriff auf die VM erfolgt über das Kennwort und ist zum Internet hin geöffnet. Für Produktionen oder andere länger andauernde Szenarien sollten Sie eine gesicherte [Linux-VM mit Azure-Vorlagen](virtual-machines-linux-create-ssh-secured-vm-from-template.md) erstellen.

## Voraussetzungen

Folgende Voraussetzungen müssen erfüllt sein: Sie besitzen ein Azure-Konto ([laden Sie eine kostenlose Testversion herunter](https://azure.microsoft.com/pricing/free-trial/)), und die [Azure-CLI](../xplat-cli-install.md) muss durch Eingabe von `azure config mode arm` in den Ressourcenmodus gesetzt werden. Sie müssen sich durch Eingabe von `azure login` bei Azure anmelden und den Bildschirmanweisungen folgen. Aktuelle Version: 0.9.16.

## Zusammenfassung der Schnellbefehle

Sie müssen nur einen Befehl verwenden:

1. `azure vm quick-create`

Ersetzen Sie in den folgenden Befehlsbeispielen die Werte zwischen &lt; und &gt; durch die Werte aus Ihrer eigenen Umgebung.

## Erstellen der Linux-VM

Im folgenden Befehl können Sie ein beliebiges Image verwenden, aber in diesem Beispiel wird `canonical:ubuntuserver:14.04.2-LTS:latest` verwendet, um schnell eine VM zu erstellen. (Um ein Image auf dem Marketplace zu finden, können Sie [ein Image suchen](virtual-machines-linux-cli-ps-findimage.md) oder [Ihr eigenes benutzerdefiniertes Image hochladen](virtual-machines-linux-create-upload-generic.md).) Etwa Folgendes sollte angezeigt werden:

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: quickcreate
Virtual machine name: quickcreate
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: canonical:ubuntuserver:14.04.2-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "quickcreate"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "quickcreate"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/quickcreate/providers/Microsoft.Compute/virtualMachines/quickcreate
data:    ProvisioningState               :Succeeded
data:    Name                            :quickcreate
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :quickcreate
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Sie können jetzt eine SSH-Verbindung mit Ihrer VM auf dem SSH-Standardport 22 herstellen.

## Ausführliche exemplarische Vorgehensweise

`azure vm quick-create` erstellt schnell eine VM, sodass Sie sich anmelden und arbeiten können. Sie besitzt jedoch keine komplexe Umgebung. Wenn Sie Ihre Umgebung anpassen möchten, können Sie [eine Azure Resource Manager-Vorlage verwenden, um schnell eine bestimmte Bereitstellung zu erstellen](virtual-machines-linux-cli-deploy-templates.md), oder Sie können [Ihre eigene benutzerdefinierte Umgebung für eine Linux-VM direkt mit Azure-CLI-Befehlen erstellen](virtual-machines-linux-cli-deploy-templates.md).

Das obige Beispiel erstellt:

- ein Azure Storage-Konto zum Speichern der VHD-Datei, die das VM-Image ist
- ein virtuelles Azure-Netzwerk und Subnetz für die Verbindung mit der VM
- eine virtuelle Netzwerkschnittstellen-Karte (Network Interface Card, NIC), um die VM dem Netzwerk zuzuordnen
- eine öffentliche IP-Adresse und ein Unterdomänenpräfix für eine Internetadresse für die externe Verwendung

und erstellt dann die Linux-VM in der Umgebung. Diese VM wird direkt dem Internet verfügbar gemacht und ist nur durch einen Benutzernamen und ein Kennwort geschützt.

## Nächste Schritte

Sie haben nun schnell eine Linux-VM zum Testen und für Demonstrationszwecke erstellt. Sie können eine sicherere Ausführungsumgebung mit einer Linux-VM in Azure erstellen durch:

- [Erstellen einer Linux-VM in Azure mithilfe von Azure-Vorlagen](virtual-machines-linux-cli-deploy-templates.md)
- [Erstellen einer per SSH geschützten Linux-VM in Azure mit Azure-Vorlagen](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Erstellen einer Linux-VM in Azure mit der Azure-CLI und Anpassen der Infrastruktur](virtual-machines-linux-create-cli-complete.md)

sowie einer beliebigen Anzahl von proprietären und Open Source-Tools zur Infrastrukturbereitstellung, Konfiguration und Orchestrierung.

<!---HONumber=AcomDC_0323_2016-->