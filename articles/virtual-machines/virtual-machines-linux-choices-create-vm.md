<properties
	pageTitle="Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers | Microsoft Azure"
	description="Listet die verschiedenen Möglichkeiten zum Erstellen eines virtuellen Linux-Computers in Azure auf und enthält Links zu weiteren Anweisungen."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Um bei verschiedenen Benutzern und für verschiedene Zwecke möglichst gut zu passen, verfügt Azure über verschiedene Möglichkeiten, einen virtuellen Computer zu erstellen. Dieser Artikel fasst diese Unterschiede sowie die Entscheidungen, die Sie beim Erstellen Ihres virtuellen Linux-Computers treffen können, zusammen.

Azure-Ressourcen-Manager-Vorlagen können auch verwendet werden, um einen virtuellen Computer und dessen diverse Ressourcen als eine logische Bereitstellungseinheit zu erstellen und zu verwalten. Anweisungen für diesen Ansatz, sind an anwendbarer Stelle enthalten. Weitere Informationen zum Azure-Ressourcen-Manager und zum Verwalten von Ressourcen als eine Einheit finden Sie in der [Übersicht][].

## Tool-Optionen

### GUI: Klassisches Azure-Portal oder Azure-Portal

Die grafische Benutzeroberfläche des Portals bietet eine einfache Möglichkeit zum Testen eines virtuellen Computers. Dies gilt insbesondere dann, wenn Sie mit Azure beginnen. Erstellen Sie den virtuellen Computer über das [klassische Azure-Portal](https://manage.windowsazure.com) oder das [Azure-Portal](https://portal.azure.com). Allgemeine Anweisungen erhalten Sie, indem Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers][] ein beliebiges Linux-Image aus dem **Katalog** auswählen. Beachten Sie, dass virtuelle Computer über das [klassische Azure-Portal](https://manage.windowsazure.com) nur mithilfe des klassischen Bereitstellungsmodells erstellt werden.

### Befehlsshell: Azure-Befehlszeilenschnittstelle oder Azure PowerShell

Wenn Sie lieber in einer Befehlsshell arbeiten, wählen Sie zwischen der Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux- und Windows-Benutzer oder dem Azure PowerShell. Letzteres verfügt über Windows PowerShell Cmdlets für Azure und eine benutzerdefinierte Konsole.

Informationen zum Erstellen von virtuellen Computern mittels Azure-Befehlszeilenschnittstelle im Ressourcenmanager-Bereitstellungsmodell finden Sie unter [Erstellen einer virtuellen Linux-Maschine][]. Verwenden Sie die Registerkarten oder die Orientierungshilfen dieses Artikels, um Anweisungen zur Verwendung von Azure PowerShell und Vorlagen zu finden.

Informationen zum klassischen Bereitstellungsmodell finden Sie unter [Erstellung eines benutzerdefinierten virtuellen Computers mit Azure CLI](virtual-machines-linux-create-custom.md) und [Azure PowerShell verwenden, um Linux-basierte virtuelle Computer zu erstellen und vorzukonfigurieren][].


### Entwicklungsumgebung: Visual Studio

Visual Studio unterstützt ebenfalls die Erstellung von virtuellen Azure-Computern. Informationen zum klassischen Bereitstellungsmodell finden Sie unter [Erstellen eines virtuellen Computers für eine Website mit Visual Studio][]. Informationen zum Ressourcenmanager-Bereitstellungsmodell finden Sie unter [Bereitstellen von Azure-Ressourcen mithilfe der Compute-, Netzwerk- und Speicherbibliotheken von .NET][].


## Betriebssystem und Image-Optionen

Wählen Sie ein Image basierend auf dem Betriebssystem, das Sie ausführen möchten. Azure und seine Partner bieten viele Images, von denen einige Anwendungen und Tools enthalten. Sie können auch ein eigenes Images verwenden.


### Azure-Images

In allen obigen Artikeln können Sie einfach ein vorhandenes Azure-Image verwenden, um einen virtuellen Computer zu erstellen und ihn für die Vernetzung, den Lastenausgleich oder andere Anforderungen anzupassen. Die Portale bieten einen Katalog oder eine Liste von Images, die von Azure geliefert werden. Eine ähnliche Liste erhalten Sie unter Verwendung der Befehlszeile. Führen Sie beispielsweise `azure vm image list` in der Azure-Befehlszeilenschnittstelle aus, um eine Liste aller verfügbaren Images, geordnet nach Ort und Herausgeber zu erhalten.


### Verwenden eines eigenen Image

Verwenden Sie ein Image, das auf einem vorhandenen virtuellen Azure-Computer basiert, indem Sie diesen virtuellen Computer *erfassen* oder ein eigenes, auf einer virtuellen Festplatte (Virtual Hard Disk, VHD) gespeichertes Image hochladen. Informationen für das klassische Bereitstellungsmodell finden Sie unter [Erfassen eines virtuellen Linux-Computers, um ihn mit der CLI als Vorlage zu verwenden][] und [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält][]. Informationen zum Ressourcenmanager-Bereitstellungsmodell finden Sie unter [Erfassen eines virtuellen Linux-Computers, um ihn als Ressourcenmanagervorlage zu verwenden](virtual-machines-linux-capture-image-resource-manager.md).

## Nächste Schritte

[Anmelden beim virtuellen Computer][]

[Anfügen eines Datenträgers][]

## Zusätzliche Ressourcen

[Testumgebung für die Basiskonfiguration][]

[Testumgebungen für Azure-Hybridclouds][]

[Befehlsentsprechungen für Ressourcen-Manager und Service Management für VM-Vorgänge mit der Azure CLI für Mac, Linux und Windows][]

<!-- LINKS -->
[Übersicht]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Befehlsentsprechungen für Ressourcen-Manager und Service Management für VM-Vorgänge mit der Azure CLI für Mac, Linux und Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Azure PowerShell verwenden, um Linux-basierte virtuelle Computer zu erstellen und vorzukonfigurieren]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[Erfassen eines virtuellen Linux-Computers, um ihn mit der CLI als Vorlage zu verwenden]: virtual-machines-linux-capture-image.md

[Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält]: virtual-machines-linux-create-upload-vhd.md

[Erstellen eines virtuellen Computers für eine Website mit Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Bereitstellen von Azure-Ressourcen mithilfe der Compute-, Netzwerk- und Speicherbibliotheken von .NET]: virtual-machines-arm-deployment.md

[Anmelden beim virtuellen Computer]: virtual-machines-linux-how-to-log-on.md

[Anfügen eines Datenträgers]: virtual-machines-linux-how-to-attach-disk.md

[Testumgebung für die Basiskonfiguration]: virtual-machines-base-configuration-test-environment.md
[Testumgebungen für Azure-Hybridclouds]: virtual-machines-hybrid-cloud-test-environments.md

[Erstellen einer virtuellen Linux-Maschine]: virtual-machines-linux-tutorial.md
[Erstellen eines benutzerdefinierten virtuellen Computers]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->