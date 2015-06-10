<properties
   pageTitle="Erstellen einer Multi-VM-Bereitstellung über die Azure-Befehlszeilenschnittstelle | Azure"
   description="Erfahren Sie, wie Sie eine Bereitstellung mit mehreren virtuellen Computern über die Azure-Befehlszeilenschnittstelle erstellen."
   services="virtual-machines"
   documentationCenter="nodejs"
   authors="AlanSt"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="alanst;kasing"/>

# Erstellen einer Multi-VM-Bereitstellung über die Azure-Befehlszeilenschnittstelle

Das folgende Skript zeigt, wie Sie eine Dienstbereitstellung mit mehreren virtuellen Computern und mehreren Clouds in einem VNET über die Azure-Befehlszeilenschnittstelle (Azure-CLI) konfigurieren.

Im unten gezeigten Bild wird erläutert, wie die Bereitstellung nach Abschluss des Skripts aussehen wird:

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

Das Skript erstellt im Clouddienst **servercs** einen virtuellen Computer (**servervm**) mit zwei angehängten Datenträgern und im Clouddienst **workercs** zwei virtuelle Computer (**clientvm1, clientvm2**). Beide Clouddienste werden im VNET **samplevnet** abgelegt. Der Clouddienst **servercs** verfügt auch über einen Endpunkt, der für externe Konnektivität konfiguriert ist.

## Dafür erforderliches CLI-Skript
Der Code für diese Einrichtung ist relativ einfach:

>[AZURE.NOTE]Sie müssen wahrscheinlich die Namen der Cloud-Dienste servercs und workercs ändern, sodass es sich um eindeutige Cloud-Dienstnamen handelt.

    azure network vnet create samplevnet -l "West US"
    azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-de-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10001 -z Small –n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-de-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-de-30GB azureuser Password@1
    azure vm disk attach-new servervm 100
    azure vm disk attach-new servervm 500
    azure vm endpoint create servervm 443 443 -n https -o tcp

Dasselbe gilt für den Code zum Beenden:

    azure vm delete -b -q servervm
    azure vm delete -b -q clientvm1
    azure vm delete -b -q clientvm2
    azure network vnet delete -q samplevnet

*Die Option "-q" unterdrückt die interaktive Bestätigung zum Löschen von Objekten, während "-b" die dem virtuellen Computer zugeordneten Datenträger/Blobs bereinigt.*

## Generische Formen der verwendeten Befehle

Während Sie weitere Informationen zu jedem der Azure-CLI-Befehle mithilfe der Option –help finden, lautet die generische Form der einzelnen oben verwendeten Befehls folgendermaßen:

    azure network vnet create -l <Region> <VNet_name>
    azure network vnet delete -q <VNet_name>

    azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
    azure vm delete -b -q <VM_name>
    azure vm disk attach-new <VM_name>
    azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## Nächste Schritte


* [Linux und Open-Source-Computing auf Azure](virtual-machines-linux-opensource.md)
* [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-how-to-log-on.md)

<!---HONumber=58-->