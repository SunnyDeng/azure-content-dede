<properties
	pageTitle="Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers | Microsoft Azure"
	description="Auflistung der verschiedenen Möglichkeiten zum Erstellen eines virtuellen Windows-Computers mit Ressourcen-Manager"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers mit Ressourcen-Manager

Azure bietet verschiedene Möglichkeiten zum Erstellen eines virtuellen Computers, da virtuelle Computer für verschiedene Benutzer und Zwecke geeignet sind. Daher müssen Sie einige Optionen für den virtuellen Computer und dessen Erstellung auswählen. Dieser Artikel bietet einen Überblick über diese Optionen sowie Links zu Anweisungen.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

Azure-Ressourcen-Manager-Vorlagen wurden vor Kurzem als eine Möglichkeit eingeführt, um einen virtuellen Computer und dessen unterschiedlichen Ressourcen als eine logische Bereitstellungseinheit zu erstellen und zu verwalten. Sie finden nachfolgend Anweisungen für diesen Ansatz, sofern verfügbar. Weitere Informationen zum Azure-Ressourcen-Manager und zum Verwalten von Ressourcen als eine Einheit finden Sie in der [Übersicht][].

## Tool-Optionen

### GUI: Azure-Portal

Die grafische Benutzeroberfläche des klassischen Azure-Portals bietet eine einfache Möglichkeit zum Testen einer virtuellen Maschine. Dies gilt insbesondere dann, wenn Sie mit Azure beginnen. Verwenden des Azure-Portals zur Erstellung eines virtuellen Computers:

[Erstellen eines virtuellen Computers unter Windows][]

### Befehlsshell: Azure-Befehlszeilenschnittstelle oder Azure PowerShell

Wenn Sie lieber in einer Befehlsshell arbeiten, wählen Sie zwischen der Azure-Befehlszeilenschnittstelle (CLI) für Mac- und Linux-Benutzer oder Azure PowerShell. Letzteres verfügt über Cmdlets für Azure und eine benutzerdefinierte Konsole.

Informationen zur Azure-Befehlszeilenschnittstelle finden Sie unter:

- [Befehlsentsprechungen für den Ressourcen-Manager und die Dienstverwaltung für Aufgaben für virtuelle Computer mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows.][]
- [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI][]

Informationen zu Azure PowerShell finden Sie unter:

- [Erstellen einer Windows-VM mit dem Ressourcen-Manager und PowerShell][]
- [Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell][]
- [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und PowerShell][]
- [Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage und PowerShell][]

### Entwicklungsumgebung: Visual Studio

[Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET][]

## Betriebssystem und Image-Optionen

Wählen Sie ein Image basierend auf dem Betriebssystem, das Sie ausführen möchten. Azure und seine Partner bieten viele Images, von denen einige Anwendungen und Tools enthalten. Sie können auch ein eigenes Images verwenden. Sie finden das für Ihre Anwendung benötigte Plattform-Image mittels der Informationen unter: [Navigieren zu und Auswählen von Images virtueller Azure-Computer mit Windows PowerShell und der Azure-Befehlszeilenschnittstelle][].

<!-- LINKS -->
[Übersicht]: ../resource-group-overview.md

[Erstellen eines virtuellen Computers unter Windows]: virtual-machines-windows-tutorial.md

[Befehlsentsprechungen für den Ressourcen-Manager und die Dienstverwaltung für Aufgaben für virtuelle Computer mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows.]: xplat-cli-azure-manage-vm-asm-arm.md
[Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Erstellen einer Windows-VM mit dem Ressourcen-Manager und PowerShell]: virtual-machines-create-windows-powershell-resource-manager.md
[Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage und PowerShell]: virtual-machines-create-windows-powershell-resource-manager-template.md


[Navigieren zu und Auswählen von Images virtueller Azure-Computer mit Windows PowerShell und der Azure-Befehlszeilenschnittstelle]: resource-groups-vm-searching.md
[Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=AcomDC_0204_2016-->