<properties
	pageTitle="Befehlsentsprechungen für Ressourcen-Manager und Dienstverwaltung für VM-Vorgänge mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows"
	description="Enthält Informationen für die Befehlsentsprechungen der Azure-Befehlszeilenschnittstelle für das Erstellen und Verwalten von virtuellen Azure Computern im Ressourcen-Manager und dem Service Management"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="04/28/2015"
	ms.author="danlep"/>


# Befehlsentsprechungen für Ressourcen-Manager und Dienstverwaltung für VM-Vorgänge mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows
In diesem Artikel werden die Entsprechungen für die Befehle der Microsoft Azure Befehlszeilenschnittstelle (Azure CLI) zum Erstellen und Verwalten von Azure-VMs im ASM- (Dienstverwaltung) und ARM-Modus (Ressourcen-Manager) beschrieben. Verwenden Sie ihn als eine praktische Anleitung zum Migrieren von Skripts aus einem Befehlsmodus in einen anderen.

* Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht installiert und mit Ihrem Abonnement verbunden haben, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle ](../xplat-cli-install.md) und [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](../xplat-cli-connect.md). Achten Sie bei Verwendung der Befehle im ARM-Modus darauf, dass Sie die Verbindung mit der Anmeldemethode herstellen.

* Den Einstieg in den ARM-Modus der Azure-Befehlszeilenschnittstelle und den Wechsel der Befehlsmodi finden Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](xplat-cli-azure-resource-manager.md).

* Onlinehilfe zu den Befehlen und Optionen erhalten Sie durch Eingabe von `azure <command> <subcommand> --help` oder `azure help <command> <subcommand>`.

## Szenarios
Die folgende Tabelle enthält allgemeine VM-Vorgänge, die Sie mit Befehlen der Azure-Befehlszeilenschnittstelle im ASM- und ARM-Modus ausführen können. Bei vielen Befehlen im ARM-Modus müssen Sie den Namen einer vorhandenen Ressourcengruppe übergeben.

> [AZURE.NOTE]Diese Beispiele umfassen keine vorlagenbasierten Vorgänge im ARM-Modus. Weitere Informationen finden Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](xplat-cli-azure-resource-manager.md).

Szenario | ASM-Modus | ARM-Modus
-------------- | ----------- | -------------------------
Erstellen grundlegender virtueller Computer | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`
Erstellen eines virtuellen Linux-Computers | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Erstellen eines virtuellen Windows-Computers | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Auflisten der virtuellen Computer | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Abrufen von Informationen zu einem virtuellen Computer | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Starten eines virtuellen Computers | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Anhalten eines virtuellen Computers | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Neustarten eines virtuellen Computers | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Löschen eines virtuellen Computers | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Erfassen eines virtuellen Computers | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Erstellen eines virtuellen Computers aus einem Benutzer-Image | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Erstellen eines virtuellen Computers von einem speziellen Datenträger | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Hinzufügen eines Datenträgers zu einem virtuellen Computer | `azure  vm disk attach [options] <vm-name> <disk-image-name>` – oder – <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Entfernen eines Datenträgers aus einem virtuellen Computer | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Hinzufügen einer generischen Erweiterung zu einem virtuellen Computer | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Hinzufügen einer VMAccess-Erweiterung zu einem virtuellen Computer | Nicht verfügbar | `azure vm reset-access [options] <resource-group> <name>`
Hinzufügen einer Docker-Erweiterung zu einem virtuellen Computer | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Hinzufügen einer Chef-Erweiterung zu einem virtuellen Computer | `azure  vm extension get-chef [options] <vm-name>` | Nicht verfügbar
Deaktivieren einer VM-Erweiterung | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Nicht verfügbar
Entfernen einer VM-Erweiterung | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Auflisten von VM-Erweiterungen | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Auflisten von VM-Images | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` – oder – <br/> `azure vm image list-publishers [options] <location>` – oder – <br/> `azure vm image list-offers [options] <location>` – oder – <br/> `azure vm image list-skus [options] <location>`
Anzeigen eines VM-Images | `azure vm image show [options]` | Nicht verfügbar


## Nächste Schritte

* Weitere Informationen zur Verwendung der Azure-Befehlszeilenschnittstelle mit Ressourcen im ARM-Modus finden Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](xplat-cli-azure-resource-manager.md) und [Verwalten der rollenbasierten Zugriffssteuerung mit der plattformübergreifenden Befehlszeilenschnittstelle](../role-based-access-control-xplat-cli.md).
* Weitere Beispiele für die Befehle der Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle (Azure-CLI)](../virtual-machines-command-line-tools.md) und [Verwenden der Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager](azure-cli-arm-commands.md)

<!---HONumber=July15_HO4-->