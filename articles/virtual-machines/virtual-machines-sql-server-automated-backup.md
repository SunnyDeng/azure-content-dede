<properties
	pageTitle="Automatisierte Sicherung für virtuelle SQL Server-Computer | Microsoft Azure"
	description="Erläutert das Feature „Automatisierte Sicherung“ für SQL Server auf virtuellen Azure-Computern mithilfe des Ressourcen-Manager-Bereitstellungsmodells."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern

Die automatisierte Sicherung konfiguriert automatisch [Managed Backup für Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) für alle vorhandenen und neuen Datenbanken auf einer Azure-VM, auf der SQL Server 2014 Standard oder Enterprise ausgeführt wird. Dies bietet Ihnen die Möglichkeit, reguläre Datenbanksicherungen zu konfigurieren, die permanenten Azure-Blob-Speicher nutzen.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.

## Einstellungen für die automatisierte Sicherung

In der folgenden Tabelle werden die Optionen beschrieben, die für die automatisierte Sicherung konfiguriert werden können. Die tatsächlichen Konfigurationsschritte variieren abhängig davon, ob Sie das Azure-Portal oder Azure Windows PowerShell-Befehle verwenden.

|Einstellung|Bereich (Standard)|Beschreibung|
|---|---|---|
|**Automatisierte Sicherung**|Aktivieren/Deaktivieren (deaktiviert)|Aktiviert oder deaktiviert die automatisierte Sicherung für eine Azure-VM mit SQL Server 2014 Standard oder Enterprise.|
|**Aufbewahrungszeitraum**|1 bis 30 Tage (30 Tage)|Die Anzahl von Tagen, für die eine Sicherung aufbewahrt wird.|
|**Speicherkonto**|Azure-Speicher-Konto (das für die angegebene VM erstellte Speicherkonto)|Ein Azure-Speicherkonto, mit dem Dateien der automatisierten Sicherung im Blob-Speicher gespeichert werden. An diesem Speicherort wird ein Container zum Speichern aller Sicherungsdateien erstellt. Die Namenskonvention für die Sicherungsdatei enthält das Datum, die Uhrzeit und den Computernamen.|
|**Verschlüsselung**|Aktivieren/Deaktivieren (deaktiviert)|Aktiviert oder deaktiviert die Verschlüsselung. Wenn die Verschlüsselung aktiviert ist, befinden sich die Zertifikate zum Wiederherstellen der Sicherung im angegebenen Speicherkonto im gleichen automaticbackup-Container (mit derselben Namenskonvention). Wenn das Kennwort geändert wird, wird ein neues Zertifikat mit diesem Kennwort generiert, das alte Zertifikat bleibt jedoch zum Wiederherstellen vorheriger Sicherungen erhalten.|
|**Kennwort**|Kennworttext (keiner)|Ein Kennwort für Verschlüsselungsschlüssel. Ein Kennwort ist nur erforderlich, wenn die Verschlüsselung aktiviert ist. Um eine verschlüsselte Sicherung wiederherzustellen, benötigen Sie das richtige Kennwort und das zugehörige Zertifikat, das beim Erstellen der Sicherung verwendet wurde.|

## Konfigurieren der automatisierten Sicherung im Azure-Portal

Sie können das Azure-Portal zum Konfigurieren der automatisierten Sicherung verwenden, wenn Sie einen neuen virtuellen Computer mit SQL Server 2014 erstellen.

>[AZURE.NOTE] Die automatisierte Sicherung basiert auf dem SQL Server-IaaS-Agent. Zum Installieren und Konfigurieren des Agents muss der Azure-VM-Agent auf dem virtuellen Zielcomputer ausgeführt werden. Bei neueren Katalogimages für virtuelle Computer ist diese Option standardmäßig aktiviert, der Azure-VM-Agent ist auf vorhandenen virtuellen Computern aber möglicherweise nicht installiert. Wenn Sie ein eigenes Image für virtuelle Computer verwenden, müssen Sie auch den SQL Server-IaaS-Agent installieren. Weitere Informationen finden Sie unter [VM-Agent und Erweiterungen](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

Der folgende Azure-Portal-Screenshot zeigt die oben beschriebenen Optionen unter **OPTIONALE KONFIGURATION** | **AUTOMATISIERTE SQL-SICHERUNG**.

![Konfigurieren der automatischen SQL-Sicherung im Azure-Portal](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

Wählen Sie für vorhandene virtuelle Computer mit SQL Server 2014 die Einstellungen für die **automatische Sicherung** im Abschnitt **Konfiguration** der Eigenschaften des virtuellen Computers aus. Im Fenster **Automatisierte Sicherung** können Sie die Funktion aktivieren, den Aufbewahrungszeitraum festlegen, das Speicherkonto auswählen und die Verschlüsselung festlegen. Diese Einstellungen zeigt der folgende Screenshot.

![Konfigurieren der automatisierten Sicherung im Azure-Portal](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE] Wenn Sie die automatisierte Sicherung zum ersten Mal aktivieren, konfiguriert Azure den SQL Server-IaaS-Agent im Hintergrund. Im Azure-Portal wird währenddessen nicht angezeigt, dass die automatisierte Sicherung konfiguriert wird. Warten Sie einige Minuten, bis der Agent installiert und konfiguriert wurde. Danach werden die neuen Einstellungen im Azure-Portal angezeigt.

## Konfigurieren der automatisierten Sicherung mit PowerShell

Im folgenden PowerShell-Beispiel wird die automatisierte Sicherung für eine vorhandene SQL Server 2014-VM konfiguriert. Der Befehl **New-AzureVMSqlServerAutoBackupConfig** konfiguriert die Einstellungen der automatisierten Sicherung zum Speichern von Sicherungen in dem mit der $storageaccount-Variable angegebenen Azure-Speicherkonto. Die Sicherungen werden für 10 Tage beibehalten. Der Befehl **Set AzureVMSqlServerExtension** aktualisiert die angegebene Azure-VM mit diesen Einstellungen.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Die Installation und Konfiguration des SQL Server-IaaS-Agents kann mehrere Minuten in Anspruch nehmen.

Um die Verschlüsselung zu aktivieren, ändern Sie das vorherige Skript, um den EnableEncryption-Parameter und ein Kennwort (sichere Zeichenfolge) für den CertificatePassword-Parameter zu übergeben. Das folgende Skript aktiviert die Einstellungen der automatisierten Sicherung im vorherigen Beispiel und fügt die Verschlüsselung hinzu.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Führen Sie zum Deaktivieren der automatisierten Sicherung das gleiche Skript ohne den **-Enable**-Parameter für **New-AzureVMSqlServerAutoBackupConfig** aus. Ähnlich wie die Installation kann auch das Deaktivieren der automatisierten Sicherung mehrere Minuten dauern.

## Deaktivieren und Deinstallieren des SQL Server-IaaS-Agents

Verwenden Sie den folgenden Befehl, wenn Sie den SQL Server-IaaS-Agent für die automatisierte Sicherung und für automatisiertes Patchen deaktivieren möchten:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Verwenden Sie zum Deinstallieren des SQL Server-IaaS-Agents die folgende Syntax:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

Sie können die Erweiterung auch mit dem Befehl **Remove-AzureVMSqlServerExtension** deinstallieren:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE] Durch das Deaktivieren und Deinstallieren des SQL Server-IaaS-Agents werden die zuvor konfigurierten Managed Backup-Einstellungen nicht entfernt. Die automatisierte Sicherung sollte vor dem Deaktivieren oder Deinstallieren des SQL Server-IaaS-Agents deaktiviert werden.

## Kompatibilität

Die folgenden Produkte sind mit den SQL Server-IaaS-Agent-Funktionen für die automatisierte Sicherung kompatibel:

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## Nächste Schritte

Die automatisierte Sicherung konfiguriert Managed Backup für Azure-VMs. Lesen Sie daher unbedingt die [Dokumentation für Managed Backup](https://msdn.microsoft.com/library/dn449496.aspx), um sich mit dem Verhalten und den Auswirkungen vertraut zu machen.

Weitere Informationen zur Sicherung und Wiederherstellung für SQL Server auf Azure-VMs finden Sie im folgenden Thema: [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-backup-and-restore.md).

Eine verwandte Funktion für SQL Server-VMs in Azure ist die [automatisierte Anwendung von Patches für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-automated-patching.md).

Lesen Sie auch die weiteren [Ressourcen für die Ausführung von SQL Server in Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0204_2016-->