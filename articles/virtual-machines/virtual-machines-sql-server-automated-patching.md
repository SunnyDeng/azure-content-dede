<properties 
   pageTitle="Automatisches Patchen für SQL Server auf virtuellen Azure-Computern"
   description="Erläutert die Funktion „Automatisiertes Patchen“ für virtuelle SQL Server-Computer in Azure."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />

<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="jroth" />


# Automatisches Patchen für SQL Server auf virtuellen Azure-Computern

Beim automatischen Patchen wird ein Wartungsfenster für einen virtuellen Azure-Computer mit SQL Server 2012 oder 2014 eingerichtet. Automatische Updates können nur während dieses Wartungsfensters installiert werden. Dadurch wird bei SQL Server sichergestellt, dass Systemupdates und alle erforderlichen Neustarts zum bestmöglichen Zeitpunkt für die Datenbank stattfinden. Dieser ist abhängig vom SQL Server-IaaS Agent.

>[AZURE.IMPORTANT]Das automatische Patchen basiert auf dem SQL Server-IaaS-Agent. Zum Installieren und Konfigurieren des Agents muss der Azure-VM-Agent auf dem virtuellen Zielcomputer ausgeführt werden. Bei neueren Katalogimages für virtuelle Computer ist diese Option standardmäßig aktiviert, der Azure-VM-Agent ist auf vorhandenen virtuellen Computern aber möglicherweise nicht installiert. Wenn Sie ein eigenes Image für virtuelle Computer verwenden, müssen Sie auch den SQL Server-IaaS-Agent installieren. Weitere Informationen finden Sie unter [VM-Agent und Erweiterungen](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

## Konfigurieren des automatischen Patchens im Portal

Sie können das [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) zum Konfigurieren des automatischen Patchens verwenden, wenn Sie einen neuen virtuellen Computer mit SQL Server erstellen. Das folgende Bildschirmfoto zeigt diese Optionen unter **OPTIONALE KONFIGURATION**|**AUTOMATISIERTES SQL-PATCHEN**.

![Automatisches Patchen für SQL im Azure-Portal](./media/virtual-machines-sql-server-automated-patching/IC778484.jpg)

Wählen Sie für vorhandene virtuelle Computer mit SQL Server 2012 oder 2014 die Einstellungen für das **automatische Patchen** im Abschnitt **Konfiguration** der Eigenschaften des virtuellen Computers aus. Im Fenster zum **automatisierten Patchen** können Sie das Feature aktivieren, den Wartungszeitplan und den Beginn festlegen und die Dauer des Wartungsfensters auswählen. Diese Einstellungen sind auf dem folgenden Bildschirmfoto zu sehen:

![Konfiguration der automatischen Patchens im Azure-Portal](./media/virtual-machines-sql-server-automated-patching/IC792132.jpg)

>[AZURE.NOTE]Wenn Sie das automatische Patchen zum ersten Mal aktivieren, konfiguriert Azure den SQL Server-IaaS-Agent im Hintergrund. Im Portal wird währenddessen nicht angezeigt, dass das automatische Patchen konfiguriert wird. Warten Sie einige Minuten, bis der Agent installiert und konfiguriert wurde. Danach werden die neuen Einstellungen im Portal angezeigt.

## Konfigurieren des automatischen Patchens mit PowerShell

Sie können das automatisierte Patchen auch mithilfe von PowerShell konfigurieren.

Im folgenden Beispiel wird PowerShell zum Konfigurieren des automatisierten Patchens auf einem vorhandenen virtuellen SQL Server-Computer verwendet. Der Befehl **New-AzureVMSqlServerAutoPatchingConfig** konfiguriert ein neues Wartungsfenster für automatische Updates.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
    
    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

In der folgenden Tabelle wird basierend auf diesem Beispiel die tatsächliche Auswirkung auf den virtuellen Azure-Zielcomputer beschrieben:

|Parameter|Effekt|
|---|---|
|**DayOfWeek**|Patches werden jeden Donnerstag installiert.|
|**MaintenanceWindowStartingHour**|Updates werden um 11:00 Uhr gestartet.|
|**MaintenanceWindowsDuration**|Patches müssen innerhalb von 120 Minuten installiert werden. Auf der Grundlage der Startzeit müssen sie um 13:00 Uhr abgeschlossen sein.|
|**PatchCategory**|Die einzig mögliche Einstellung für diesen Parameter lautet „Wichtig“.|

Die Installation und Konfiguration des SQL Server-IaaS-Agents kann mehrere Minuten in Anspruch nehmen.

Führen Sie zum Deaktivieren des automatisierten Patchens das gleiche Skript ohne den Parameter „-Enable“ für „New-AzureVMSqlServerAutoPatchingConfig“ aus. Wie bei der Installation kann auch das Deaktivieren des automatisierten Patchens mehrere Minuten dauern.

## Deaktivieren und Deinstallieren des SQL Server-IaaS-Agents

Verwenden Sie den folgenden Befehl, wenn Sie den SQL Server-IaaS-Agent für die automatisierte Sicherung und für automatisiertes Patchen deaktivieren möchten:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Verwenden Sie zum Deinstallieren des SQL Server-IaaS-Agents die folgende Syntax:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

Sie können die Erweiterung auch mit dem Befehl **Remove-AzureVMSqlServerExtension** deinstallieren:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Kompatibilität

Die folgenden Produkte sind mit den SQL Server-IaaS-Agent-Funktionen für das automatisierte Patchen kompatibel:

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## Nächste Schritte

Eine verwandte Funktion für virtuelle SQL Server-Computer in Azure ist die [automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-automated-backup.md).

Lesen Sie auch die weiteren [Ressourcen für die Ausführung von SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO6-->