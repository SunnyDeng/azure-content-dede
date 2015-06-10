<properties
   pageTitle="CustomScript-Erweiterung unter Windows"
   description="Automatische Azure-VM-Konfigurationsaufgaben mithilfe der CustomScript-Erweiterung unter Windows"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm=""
   ms.workload="infrastructure-services"
   ms.date="02/19/2015"
   ms.author="kundanap"/>

# CustomScript-Erweiterung für Windows

Der vorliegende Artikel bietet einen Überblick über die Verwendung der CustomScript-Erweiterung unter Windows mithilfe von Azure Powershell-Cmdlets.


Mit VM-Erweiterungen, die von Microsoft und vertrauenswürdigen Drittanbietern entwickelt werden, wird die Funktionalität der VM erweitert. Eine detaillierte Übersicht über die VM-Erweiterungen finden Sie in der <a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">MSDN-Dokumentation</a>.

## Übersicht über die CustomScript-Erweiterung

Die CustomScript-Erweiterung für Windows ermöglicht Ihnen das Ausführen von Powershell-Skripts auf einem virtuellen Remotecomputer, ohne sich bei diesem anzumelden. Die Skripts können nach Bereitstellung der VM oder zu einem beliebigen Zeitpunkt im Lebenszyklus der VM ausgeführt werden, ohne dass zusätzliche Ports auf der VM geöffnet werden müssen. Die häufigsten Anwendungsfälle für benutzerdefinierte Skripts sind das Ausführen einer Installation und das Konfigurieren zusätzlicher Software auf der VM, nachdem diese bereitgestellt wurde.

### Voraussetzungen für das Ausführen der CustomScript-Erweiterung

1. Installieren Sie über <a href="http://azure.microsoft.com/downloads" target="_blank">diesen Link</a> Azure PowerShell-Cmdlets V0.8.0 oder höher.
2. Wenn die Skripts auf einer vorhandenen VM ausgeführt werden, müssen Sie sicherstellen, dass der VM-Agent auf der VM aktiviert ist. Andernfalls folgen Sie den Anweisungen in <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">diesem Artikel</a>, um einen Agent zu installieren.
3. Laden Sie die Skripts, die Sie auf der VM ausführen möchten, nach Azure Storage hoch. Die Skripts können aus einem einzelnen oder mehreren Speichercontainern stammen.
4. Das Skript sollte so geschrieben sein, dass das Eingangsskript, das durch die Erweiterung gestartet wird, weitere Skripts startet.

## Szenarien für die CustomScript-Erweiterung:

 ### Hochladen von Dateien in den Standardcontainer: Wenn sich Ihre Skripts im Speichercontainer für das Standardkonto Ihres Abonnements befinden, können Sie sie mit dem nachstehenden Cmdlet-Snippet auf der VM ausführen. Der "ContainerName" im nachstehenden Beispiel ist der Zielspeicherort, in den Sie die Skripts hochladen. Das standardmäßige Speicherkonto kann mit dem Cmdlet "Get-AzureSubscription –Default" überprüft werden.

Hinweis: In diesem Fall wird eine neue VM erstellt, wobei diese Operationen auch für eine vorhandene VM ausgeführt werden können.

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

Dieser Anwendungsfall zeigt, wie ein Nicht-Standardspeicher verwendet wird, entweder innerhalb desselben Abonnements oder in einem anderen Abonnement, um Skripts/Dateien hochzuladen. Es wird hier eine vorhandene VM verwendet, aber dieselben Operationen können auch beim Erstellen einer neuen VM verwendet werden.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM
  ### Hochladen von Skripts in mehrere Container über verschiedene Speicherkonten hinweg Wenn die Skriptdateien in mehreren Containern gespeichert sind, müssen Sie aktuelle zur Ausführung dieser Skripts die vollständige SAS-URL dieser Dateien bereitstellen.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Hinzufügen der CustomScript-Erweiterung aus dem Portal
Navigieren Sie im <a href="https://portal.azure.com/ " target="_blank">Azure-Vorschauportal </a> zum virtuellen Computer, und fügen Sie die Erweiterung hinzu, indem Sie die auszuführende Skriptdatei angeben.![][5]

  ### Deinstallieren der CustomScript-Erweiterung

Die CustomScript-Erweiterung kann mithilfe des nachstehenden Cmdlets von der VM deinstalliert werden.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### In Kürze verfügbar

In Kürze werden benutzerdefinierte Skripts für Linux sowie Beispiele hinzugefügt.

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=58-->