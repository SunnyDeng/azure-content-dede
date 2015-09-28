<properties
	pageTitle="Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers"
	description="Listet die verschiedenen Möglichkeiten zum Erstellen eines virtuellen Windows-Computers auf und enthält Links zu Anweisungen."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers

Azure bietet verschiedene Möglichkeiten zum Erstellen eines virtuellen Computers, da virtuelle Computer für verschiedene Benutzer und Zwecke geeignet sind. Daher müssen Sie einige Optionen für den virtuellen Computer und dessen Erstellung auswählen. Dieser Artikel bietet einen Überblick über diese Optionen sowie Links zu Anweisungen.

Azure-Ressourcen-Manager-Vorlagen wurden vor Kurzem als eine Möglichkeit eingeführt, um einen virtuellen Computer und dessen unterschiedlichen Ressourcen als eine logische Bereitstellungseinheit zu erstellen und zu verwalten. Sie finden nachfolgend Anweisungen für diesen Ansatz, sofern verfügbar. Weitere Informationen zum Azure-Ressourcen-Manager und zum Verwalten von Ressourcen als eine Einheit finden Sie in der [Übersicht][].

## Tool-Optionen

### GUI: Azure-Portal oder Azure-Vorschauportal

Die grafische Benutzeroberfläche des Azure-Portals bietet eine einfache Möglichkeit zum Testen einer virtuellen Maschine. Dies gilt insbesondere dann, wenn Sie mit Azure beginnen. Erstellen Sie den virtuellen Computer über das Azure-Portal oder das Azure-Vorschauportal.

[Erstellen eines virtuellen Computers unter Windows][]

### Befehlsshell: Azure-Befehlszeilenschnittstelle oder Azure PowerShell

Wenn Sie lieber in einer Befehlsshell arbeiten, wählen Sie zwischen der Azure-Befehlszeilenschnittstelle (CLI) für Mac- und Linux-Benutzer oder Azure PowerShell. Letzteres verfügt über Windows PowerShell-Cmdlets für Azure und eine benutzerdefinierte Konsole.

Informationen zur Azure-Befehlszeilenschnittstelle finden Sie unter [Befehlsentsprechungen für Ressourcen-Manager und Dienstverwaltung für VM-Vorgänge mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows][]. Informationen zum Verwenden einer Vorlage finden Sie unter [Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI][].

Informationen zu Azure PowerShell finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer][]. Informationen zum Verwenden einer Vorlage finden Sie unter [Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und PowerShell][]. Informationen zum Erstellen virtueller Computer im Dienstverwaltungsstapel finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer][].

### Entwicklungsumgebung: Visual Studio

[Erstellen eines virtuellen Computers für eine Website mit Visual Studio][]

[Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET][]

## Betriebssystem und Image-Optionen

Wählen Sie ein Image basierend auf dem Betriebssystem, das Sie ausführen möchten. Azure und seine Partner bieten viele Images, von denen einige Anwendungen und Tools enthalten. Sie können auch ein eigenes Images verwenden.

### Azure-Images

Diese Anweisungen erläutern, wie Sie mit einem Azure-Image einen virtuellen Computer erstellen, der mit Optionen für Netzwerke, Lastenausgleich und mehr individuell konfiguriert wird. Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers mit Windows][].

### Verwenden eines eigenen Image

Verwenden Sie ein Image, das auf einem vorhandenen virtuellen Azure-Computer basiert, indem Sie diesen virtuellen Computer *erfassen* oder ein eigenes, auf einer virtuellen Festplatte (Virtual Hard Disk, VHD) gespeichertes Image hochladen:

- [Erfassen eines virtuellen Windows-Computers, um ihn als Image zu verwenden][]
- [Erstellen und Hochladen einer Windows Server-VHD in Azure][]

## Nächste Schritte

[Melden Sie sich beim virtuellen Computer an.][]

[Anfügen eines Datenträgers][]

## Zusätzliche Ressourcen
[Testumgebung für die Basiskonfiguration][]

[Testumgebungen für Azure-Hybridclouds][]

<!-- LINKS -->
[Übersicht]: ../resource-group-overview.md

[Erstellen eines virtuellen Computers unter Windows]: virtual-machines-windows-tutorial.md

[Befehlsentsprechungen für Ressourcen-Manager und Dienstverwaltung für VM-Vorgänge mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Create and preconfigure a Windows virtual machine with Resource Manager and Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer]: virtual-machines-ps-create-preconfigure-windows-vms.md
[Erstellen eines benutzerdefinierten virtuellen Computers mit Windows]: virtual-machines-windows-create-custom.md

[Erfassen eines virtuellen Windows-Computers, um ihn als Image zu verwenden]: virtual-machines-capture-image-windows-server.md

[Erstellen und Hochladen einer Windows Server-VHD in Azure]: virtual-machines-create-upload-vhd-windows-server.md


[Erstellen eines virtuellen Computers für eine Website mit Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET]: virtual-machines-arm-deployment.md

[Melden Sie sich beim virtuellen Computer an.]: virtual-machines-log-on-windows-server.md

[Anfügen eines Datenträgers]: storage-windows-attach-disk.md

[Testumgebung für die Basiskonfiguration]: virtual-machines-base-configuration-test-environment.md

[Testumgebungen für Azure-Hybridclouds]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Sept15_HO3-->