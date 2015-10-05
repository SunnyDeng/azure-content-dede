<properties
	pageTitle="Erstellen eines virtuellen Computers unter Linux | Microsoft Azure"
	description="Erfahren Sie, wie Sie mithilfe eines Images von Azure und der Azure-Befehlszeilenschnittstelle einen virtuellen Azure-Computer unter Linux erstellen."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/13/2015"
	ms.author="rasquill"/>

# Erstellen eines virtuellen Linux-Computers

> [AZURE.SELECTOR]
- [Azure Portal](virtual-machines-linux-tutorial-portal-rm.md)
- [Azure CLI](virtual-machines-linux-tutorial.md)

Virtuelle Azure-Computer (VM), die Linux ausführen, lassen sich einfach über die Befehlszeile oder über das Portal erstellen. In diesem Lernprogramm wird gezeigt, wie Sie mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows (Azure-CLI) schnell eine Ubuntu Server-VM erstellen, die in Azure ausgeführt wird. Sie erfahren, wie mit **SSH** eine Verbindung dazu hergestellt wird und wie Sie einen neuen Datenträger erstellen und bereitstellen. (In diesem Thema wird eine Ubuntu Server-VM verwendet, aber Sie können Linux-VMs auch mithilfe [Ihrer eigenen Images als Vorlagen](virtual-machines-linux-create-upload-vhd.md) erstellen.)

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Exemplarische Vorgehensweise per Video

Hier finden Sie eine exemplarische Vorgehensweise für dieses Lernprogramm.

[AZURE.VIDEO building-a-linux-virtual-machine-tutorial]

## Installieren der Azure-Befehlszeilenschnittstelle

Als erstes [installieren Sie die Azure-CLI](../xplat-cli-install.md).

Gut. Stellen Sie jetzt sicher, dass Sie sich im Ressourcen-Manager-Modus befinden. Geben Sie hierfür `azure config mode arm` ein.

Umso besser. [Melden Sie sich jetzt mit Ihrer Arbeits- oder Schulkonto-ID an](../xplat-cli-connect.md#use-the-log-in-method). Geben Sie hierfür `azure login` ein, und folgen Sie den Eingabeaufforderungen.

> [AZURE.NOTE]Wenn Sie bei der Anmeldung eine Fehlermeldung erhalten, müssen Sie möglicherweise eine [Arbeits- oder Schulkonto-ID aus Ihrem persönlichen Microsoft-Konto erstellen](resource-group-create-work-id-from-personal.md).

## Erstellen des virtuellen Azure-Computers

Geben Sie `azure group create <my-group-name> westus` ein, und ersetzen Sie _&lt;my-group-name&gt;_ durch einen eindeutigen Gruppennamen. (Sie können eine andere Region verwenden.) Folgendes sollte angezeigt werden:

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

Erstellen Sie jetzt Ihren virtuellen Computer, indem Sie `azure vm quick-create` eingeben. Sie erhalten daraufhin Eingabeaufforderungen für die verbleibenden Parameter. Verwenden Sie den Namen der Ressourcengruppe, die Sie soeben erstellt haben. Verwenden Sie für **ImageURN** den Wert `canonical:ubuntuserver:14.04.2-LTS:latest`, sodass Folgendes angezeigt wird:

	azure vm quick-create
	info:    Executing command vm quick-create
	Resource group name: myuniquegroupname
	Virtual machine name: myuniquevmname
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
	User name: ops
	Password: *********
	Confirm password: *********
	+ Looking up the VM "myuniquevmname"
	info:    Using the VM Size "Standard_D1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
	+ Looking up the storage account cli3c0464f24f1bf4f014323
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	info:    Preparing to create new virtual network and subnet
	/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
	info:    Found public ip parameters, trying to setup PublicIP profile
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
	+ Creating public ip "myuni-westu-1432328437727-pip"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	+ Creating NIC "myuni-westu-1432328437727-nic"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Creating VM "myuniquevmname"
	+ Looking up the VM "myuniquevmname"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
	data:    ProvisioningState               :Succeeded
	data:    Name                            :myuniquevmname
	data:    Location                        :westus
	data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
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
	data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :myuniquevmname
	data:      User Name                     :ops
	data:      Linux Configuration:
	data:        Disable Password Auth       :false
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-31-55-31
	data:          Provisioning State        :Succeeded
	data:          Name                      :myuni-westu-1432328437727-nic
	data:          Location                  :westus
	data:            Private IP alloc-method :Dynamic
	data:            Private IP address      :10.0.1.4
	data:            Public IP address       :191.239.51.1
	data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	info:    vm quick-create command OK

Ihr virtueller Computer ist betriebsbereit und kann jetzt verbunden werden.

## Verbinden mit Ihrem virtuellen Computer

Zu virtuellen Linux-Computern stellen Sie in der Regel über **SSH** eine Verbindung her. In diesem Thema wird eine Verbindung mit einem virtuellen Computer mithilfe von Benutzernamen und Kennwörtern hergestellt. Informationen zur Verwendung öffentlicher und privater Schlüsselpaare für die Kommunikation mit Ihrem virtuellen Computer finden Sie unter [Verwenden von SSH mit Linux auf Azure](virtual-machines-linux-use-ssh-key.md).

Falls Sie mit dem Herstellen einer Verbindung über **SSH** nicht vertraut sind, der Befehl hat das Format `ssh <username>@<publicdnsaddress> -p <the ssh port>`. In diesem Fall verwenden wir den Benutzernamen und das Kennwort aus dem vorherigen Schritt sowie Port 22, den Standardport von **SSH**.

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
	The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
	ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
	ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Fri May 22 21:02:32 UTC 2015

	  System load: 0.37              Memory usage: 2%   Processes:       207
	  Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.



	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@myuniquevmname:~$

Nachdem Sie die Verbindung zu Ihrem virtuellen Computer hergestellt haben, können Sie einen Datenträger anfügen.

## Anfügen und Bereitstellen eines Datenträgers

Neue Datenträger lassen sich schnell anfügen. Geben Sie einfach `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` ein, um eine neue GB-Festplatte für den virtuellen Computer zu erstellen und anzufügen. Das sollte in etwa so aussehen:

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


Suchen Sie jetzt den Datenträger mithilfe von `dmesg | grep SCSI` (die Methode zum Ermitteln Ihres neuen Datenträgers kann variieren). In diesem Fall sieht er in etwa so aus:

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

Wir verwenden den Datenträger `sdc`. Partitionieren Sie nun den Datenträger mit `sudo fdisk /dev/sdc` – wir gehen davon aus, dass der Datenträger `sdc` war. Legen Sie ihn auf Partition 1 als primären Datenträger fest, und übernehmen Sie die anderen Standardwerte.

	sudo fdisk /dev/sdc
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x2a59b123.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-10485759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
	Using default value 10485759

Erstellen Sie die Partition durch Eingabe von `p` an der Eingabeaufforderung:

	Command (m for help): p

	Disk /dev/sdc: 5368 MB, 5368709120 bytes
	255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x2a59b123

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdc1            2048    10485759     5241856   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

Schreiben Sie zudem mithilfe des Befehls **mkfs** ein Dateisystem für die Partition. Geben Sie dabei Ihren Dateisystemtyp und den Gerätenamen an. In diesem Thema verwenden wir `ext4` und `/dev/sdc1` von oben:

	sudo mkfs -t ext4 /dev/sdc1
	mke2fs 1.42.9 (4-Feb-2014)
	Discarding device blocks: done
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	327680 inodes, 1310464 blocks
	65523 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1342177280
	40 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736

	Allocating group tables: done
	Writing inode tables: done
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

Erstellen Sie mit `mkdir` ein Verzeichnis zum Bereitstellen des neuen Dateisystems.

	sudo mkdir /datadrive

Stellen Sie das Verzeichnis mit `mount` bereit:

	sudo mount /dev/sdc1 /datadrive

Der Datenträger kann nun als `/datadrive` verwendet werden.

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE]Sie können die Verbindung zum virtuellen Linux-Computer auch mithilfe eines SSH-Schlüssels für die Identifikation herstellen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](virtual-machines-linux-use-ssh-key.md).

## Nächste Schritte

Beachten Sie, dass der neue Datenträger bei einem Neustart in der Regel nicht für den virtuellen Computer zur Verfügung steht, es sei denn, Sie schreiben diese Informationen in Ihre [fstab-Datei](http://en.wikipedia.org/wiki/Fstab).

Weitere Informationen zu Linux auf Azure erhalten Sie unter:

- [Linux und Open-Source-Computing auf Azure](virtual-machines-linux-opensource.md)

- [Verwenden der Azure-Befehlszeilenschnittstelle](../virtual-machines-command-line-tools.md)

- [Bereitstellen einer LAMP-App mithilfe der Azure-CustomScript-Erweiterung für Linux](virtual-machines-linux-script-lamp.md)

- [Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure](virtual-machines-docker-vm-extension.md)

<!---HONumber=Sept15_HO4-->