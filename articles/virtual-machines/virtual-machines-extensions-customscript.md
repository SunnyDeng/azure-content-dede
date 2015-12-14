<properties
   pageTitle="Benutzerdefinierte Skripterweiterungen unter Windows | Microsoft Azure"
   description="Automatisieren Sie Konfigurationsaufgaben für virtuelle Azure-Computer mit der benutzerdefinierten Skripterweiterung zum Ausführen von PowerShell-Skripts auf einem virtuellen Remote-Computer unter Windows."
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Benutzerdefinierte Skripterweiterung für virtuelle Windows-Computer

Der vorliegende Artikel bietet einen Überblick über die Verwendung der benutzerdefinierten Skripterweiterung auf virtuellen Windows-Computern mithilfe von Azure Powershell-Cmdlets.

Mit den Erweiterungen virtueller Computer, die von Microsoft und vertrauenswürdigen Drittanbietern entwickelt werden, wird die Funktionalität des virtuellen Computers erweitert. Eine Übersicht über die Erweiterungen virtueller Computer finden Sie unter [Erweiterungen virtueller Azure-Computer und Features](virtual-machines-extensions-features.md).

Link:[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-extensions-customscript%20-with%20template.md).


## Übersicht über benutzerdefinierte Skripterweiterungen

Die benutzerdefinierte Skripterweiterung für Windows ermöglicht Ihnen das Ausführen von Powershell-Skripts auf einem virtuellen Remotecomputer, ohne sich bei diesem anzumelden. Die Skripts können nach Bereitstellung des virtuellen Computers oder zu einem beliebigen Zeitpunkt im Lebenszyklus des virtuellen Computers ausgeführt werden, ohne dass zusätzliche Ports auf dem virtuellen Computer geöffnet werden müssen. Die häufigsten Anwendungsfälle für benutzerdefinierte Skripterweiterungen sind das Ausführen, das Installieren und das Konfigurieren zusätzlicher Software auf dem virtuellen Computer, nachdem dieser bereitgestellt wurde.

### Voraussetzungen für das Ausführen der CustomScript-Erweiterung

1. Installieren Sie über <a href="http://azure.microsoft.com/downloads" target="_blank">diesen Link</a> Azure PowerShell-Cmdlets der Version 0.8.0 oder höher.
2. Wenn die Skripts auf einem vorhandenen virtuellen Computer ausgeführt werden, müssen Sie sicherstellen, dass der Agent des virtuellen Computers darauf aktiviert ist. Andernfalls folgen Sie den Anweisungen in <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">diesem Artikel</a>, um einen Agent zu installieren.
3. Laden Sie die Skripts, die Sie auf der VM ausführen möchten, nach Azure Storage hoch. Die Skripts können aus einem einzelnen oder mehreren Speichercontainern stammen.
4. Das Skript sollte so geschrieben sein, dass das Eingangsskript, das durch die Erweiterung gestartet wird, weitere Skripts startet.

## Szenarien für benutzerdefinierte Skripterweiterungen

### Hochladen von Dateien in den Standardcontainer

Wenn Sie Ihre Skripts im Speichercontainer für das Standardkonto Ihres Abonnements vorliegen, können Sie diese wie im folgenden Beispiel auf dem virtuellen Computer ausführen. Der „ContainerName“ ist der Zielspeicherort, in den Sie die Skripts hochladen. Das standardmäßige Speicherkonto kann mit dem Befehl **Get-AzureSubscription –Default** überprüft werden.

Im folgenden Beispiel wird ein neuer virtueller Computer erstellt; dasselbe Szenario kann jedoch auch auf einem vorhandenen virtuellen Computer ausgeführt werden.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Hochladen von Dateien in einen anderen Ordner als den Standardordner

Dieses Szenario zeigt, wie ein Nicht-Standardspeicher verwendet wird, entweder innerhalb desselben Abonnements oder in einem anderen Abonnement, um Skripts und Dateien hochzuladen. Es wird hier eine vorhandene VM verwendet, aber dieselben Operationen können auch beim Erstellen einer neuen VM verwendet werden.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Hochladen von Skripts in mehrere Container über verschiedene Speicherkonten hinweg

  Wenn die Skriptdateien in mehreren Containern gespeichert sind, müssen Sie zur Ausführung dieser Skripts die vollständige SAS-URL für die Dateien bereitstellen.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Hinzufügen einer benutzerdefinierten Skripterweiterung aus dem Azure-Portal

Navigieren Sie im <a href="https://portal.azure.com/ " target="_blank">Azure-Portal</a> zum virtuellen Computer, und fügen Sie die Erweiterung hinzu, indem Sie die auszuführende Skriptdatei angeben.

  ![][5]


### Deinstallieren der benutzerdefinierten Skripterweiterung

Die CustomScript-Erweiterung kann mithilfe des folgenden Befehls vom virtuellen Computer deinstalliert werden.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Verwenden der benutzerdefinierten Skripterweiterung mit Vorlagen

Um Informationen zur Verwendung der benutzerdefinierten Skripterweiterung mit Vorlagen zu erhalten, lesen Sie die Dokumentation [hier] (virtual-machines-extensions-customscript -with template.md).

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=AcomDC_1203_2015-->