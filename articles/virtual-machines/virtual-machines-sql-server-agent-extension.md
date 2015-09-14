<properties 
	pageTitle="Erweiterung für SQL Server-IaaS-Agent"
	description="Beschreibt die SQL Server-Agent-Erweiterung, dank der virtuelle Computer mit SQL Server in der Cloud in Azure Automatisierungsfeatures verwenden können. Darüber hinaus wird erläutert, wie Sie den Agent installieren, wenn er nicht bereits automatisch installiert wurde."
	services="virtual-machines"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="06/17/2015"
	ms.author="jeffreyg"/>

# Erweiterung für SQL Server-IaaS-Agent

Mit dieser Erweiterung kann SQL Server auf virtuellen Azure-Computern bestimmte Dienste nutzen. Diese Dienste sind in diesem Artikel aufgeführt und können nur verwendet werden, wenn die Erweiterung installiert ist. Diese Erweiterung wird automatisch für SQL Server-Galerie-Images im Azure-Vorschauportal installiert. Sie kann auf allen virtuellen SQL Server-Computern in Azure installiert werden, auf denen auch der Gast-Agent für virtuelle Azure-Computer installiert ist.
 
## Voraussetzungen
Anforderungen für die Verwendung von Powershell-Cmdlets:

- Das aktuelle Azure-Befehlszeilen-SDK ist [hier verfügbar](http://azure.microsoft.com/downloads/).

Anforderungen für die Verwendung der Erweiterung auf Ihrem virtuellen Computer:

- Gast-Agent für virtuelle Azure-Computer
- Windows Server 2012, Windows Server 2012 R2 oder höher
- SQL Server 2012, SQL Server 2014 oder höher
 
## Mit der Erweiterung verfügbare Dienste

- **Automatisierte SQL-Sicherung**: Dieser Dienst automatisiert die Planung von Sicherungen für alle Datenbanken für die Standardinstanz von SQL Server auf dem virtuellen Computer. Weitere Informationen zu diesem Dienst finden Sie unter [Automatische Sicherung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-automated-backup.md).
- **Automatische Anwendung von Patches für SQL**: Mit diesem Dienst können Sie ein Wartungsfenster konfigurieren, in dem Updates für den virtuellen Computer ausgeführt werden. Auf diese Weise werden Updates während der Spitzenzeiten für Ihre Arbeitsauslastung vermieden. Weitere Informationen zu diesem Dienst finden Sie unter [Automatisierte Anwendung von Patches für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-automated-patching.md).

## Hinzufügen der Erweiterung mit Powershell
Wenn Sie den virtuellen SQL Server-Computer mit dem [Azure-Vorschauportal](https://portal.azure.com/) bereitstellen, wird die Erweiterung automatisch installiert. Auf virtuellen SQL Server-Computern, die mit dem [Azure-Verwaltungsportal](https://manage.windowsazure.com) bereitgestellt werden, oder bei virtuellen Computern, auf denen eigene SQL-Lizenzen bereitgestellt werden, können Sie diese Erweiterung einem vorhandenen virtuellen Computer hinzufügen. Verwenden Sie dazu das folgende Azure PowerShell-Cmdlet:

**Set-AzureVMSqlServerExtension**

### Syntax

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <Einstellungen der automatischen Sicherung>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<Allgemeine Parameter>]

> [AZURE.NOTE]Es wird empfohlen, den Parameter „–Version“ auszulassen. Ohne diesen Parameter wird standardmäßig die neueste Version der Erweiterung festgelegt.

### Beispiel
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Überprüfen des Erweiterungsstatus
Zum Überprüfen des Status dieser Erweiterung und der zugeordneten Dienste können Sie beide Portale verwenden. Rufen Sie in den Details des vorhandenen virtuellen Computers unter **Einstellungen** die Option **Erweiterungen** auf.

Sie können auch das folgende Azure PowerShell-Cmdlet verwenden:

**Get-AzureVMSqlServerExtension**

### Syntax

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<Allgemeine Parameter>]

> [AZURE.NOTE]Sie können den Parameter „ –Version“ auslassen. Ohne diesen Parameter wird standardmäßig die neueste Version der Erweiterung festgelegt.

### Beispiel
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Entfernen der Erweiterung mit Powershell   
Wenn Sie diese Erweiterung vom virtuellen Computer entfernen möchten, können Sie das folgende Azure Powershell-Cmdlet verwenden:

**Remove-AzureVMSqlServerExtension**

### Syntax
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=September15_HO1-->