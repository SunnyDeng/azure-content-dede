<properties 
	pageTitle="Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers" 
	description="Listet die verschiedenen Möglichkeiten zum Erstellen eines virtuellen Linux-Computers auf und enthält Links zu Anweisungen." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services"
	ms.date="05/14/2015" 
	ms.author="kathydav"/>

# Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers

Azure bietet verschiedene Möglichkeiten zum Erstellen eines virtuellen Computers, da virtuelle Computer für verschiedene Benutzer und Zwecke geeignet sind. Sie wählen daher aus verschiedenen VM-Optionen und legen die Erstellungsmethode fest. Dieser Artikel bietet einen Überblick über diese Optionen sowie Links zu Anweisungen.

Azure-Ressourcen-Manager-Vorlagen wurden vor Kurzem als eine Möglichkeit eingeführt, um einen virtuellen Computer und dessen unterschiedlichen Ressourcen als eine logische Bereitstellungseinheit zu erstellen und zu verwalten. Sie finden nachfolgend Anweisungen für diesen Ansatz, sofern verfügbar. Weitere Informationen zum Azure-Ressourcen-Manager und zum Verwalten von Ressourcen als eine Einheit finden Sie in der [Übersicht][].

## Tool-Optionen

### GUI: Azure-Portal oder Vorschauportal 

Die grafische Benutzeroberfläche des Azure-Portals bietet eine einfache Möglichkeit zum Testen einer virtuellen Maschine. Dies gilt insbesondere dann, wenn Sie mit Azure beginnen. Erstellen Sie den virtuellen Computer über das Azure-Portal oder das Azure-Vorschauportal. Allgemeine Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Linux-Computers][].

### Befehlsshell: Azure-Befehlszeilenschnittstelle oder Azure PowerShell

Wenn Sie lieber in einer Befehlsshell arbeiten, wählen Sie zwischen der Azure-Befehlszeilenschnittstelle (CLI) für Mac- und Linux-Benutzer oder Azure PowerShell. Letzteres verfügt über Windows PowerShell-Cmdlets für Azure und eine benutzerdefinierte Konsole.

Informationen zur Azure-Befehlszeilenschnittstelle finden Sie unter [Befehlsentsprechungen für Ressourcen-Manager und Service Management für VM-Vorgänge mit der Azure CLI für Mac, Linux und Windows][]. Informationen zum Verwenden einer Vorlage finden Sie unter [Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI][].

Informationen zu Azure PowerShell finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren von Linux-basierten virtuellen Computern][]. Informationen zum Verwenden einer Vorlage finden Sie unter [Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und PowerShell][].

### Entwicklungsumgebung: Visual Studio

[Erstellen eines virtuellen Computers für eine Website mit Visual Studio][]

[Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET][]

## Betriebssystem und Image-Optionen

Wählen Sie ein Image basierend auf dem Betriebssystem, das Sie ausführen möchten. Azure und seine Partner bieten viele Images, von denen einige Anwendungen und Tools enthalten. Sie können auch ein eigenes Images verwenden.

### Azure-Images

Diese Anweisungen erläutern, wie Sie mit einem Azure-Image einen virtuellen Computer erstellen, der mit Optionen für Netzwerke, Lastenausgleich und mehr individuell konfiguriert wird. Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers unter Linux in Azure][].

### Verwenden eines eigenen Image

Verwenden Sie ein Image, das auf einem vorhandenen virtuellen Azure-Computer basiert, indem Sie diesen virtuellen Computer *erfassen* oder ein eigenes, auf einer virtuellen Festplatte (Virtual Hard Disk, VHD) gespeichertes Image hochladen:

- [Erfassen eines virtuellen Linux-Computers mit der CLI, um ihn als Vorlage zu verwenden][]
- [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält][]

## Nächste Schritte

[Anmelden beim virtuellen Computer][]

[Anfügen eines Datenträgers][]

## Zusätzliche Ressourcen
[Informationen zu Azure-VM-Konfigurationseinstellungen][]

[Testumgebung für die Basiskonfiguration][]

[Testumgebungen für Azure-Hybridclouds][]

<!-- LINKS -->
[Übersicht]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Erstellen eines virtuellen Linux-Computers]: virtual-machines-linux-tutorial.md

[Befehlsentsprechungen für Ressourcen-Manager und Service Management für VM-Vorgänge mit der Azure CLI für Mac, Linux und Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren von Linux-basierten virtuellen Computern]: virtual-machines-ps-create-preconfigure-linux-vms.md

[Erstellen eines benutzerdefinierten virtuellen Computers unter Linux in Azure]: virtual-machines-linux-create-custom.md
[Erfassen eines virtuellen Linux-Computers mit der CLI, um ihn als Vorlage zu verwenden]: virtual-machines-vm-capture-image-cli.md

[Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält]: virtual-machines-linux-create-upload-vhd.md

[Erstellen eines virtuellen Computers für eine Website mit Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET]: virtual-machines-arm-deployment.md

[Anmelden beim virtuellen Computer]: virtual-machines-linux-how-to-log-on.md

[Anfügen eines Datenträgers]: virtual-machines-linux-how-to-attach-disk.md

[Informationen zu Azure-VM-Konfigurationseinstellungen]: http://msdn.microsoft.com/library/azure/dn763935.aspx
[Testumgebung für die Basiskonfiguration]: virtual-machines-base-configuration-test-environment.md
[Testumgebungen für Azure-Hybridclouds]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=July15_HO2-->