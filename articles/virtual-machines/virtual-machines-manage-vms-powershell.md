<properties
   pageTitle="manage-vms-azure-powershell"
   description="Verwalten Ihrer VMs mit Azure PowerShell"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="kasing"/>

# Verwalten Ihrer virtuellen Computer mit Azure PowerShell

Bevor Sie beginnen, müssen Sie sicherstellen, dass Azure PowerShell installiert ist. Besuchen Sie hierzu [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

## Abrufen eines Images

Vor dem Erstellen einer VM müssen Sie entscheiden, **welches Image verwendet werden soll**. Sie können mit dem folgenden Cmdlet eine Liste der Images abrufen:

      Get-AzureVMImage

Dieses Cmdlet gibt eine Liste aller in Azure verfügbaren Images zurück. Es handelt sich um eine sehr lange Liste, und es kann schwierig sein, genau das Image zu finden, das Sie verwenden möchten. Im nachstehenden Beispiel werden andere PowerShell-Cmdlets verwendet, um die Liste der zurückgegebenen Images zu verkürzen. Es werden ausschließlich Images angezeigt, die auf **Windows Server 2012 R2 Datacenter** basieren. Zusätzlich wird nur das neueste Image abgerufen, indem für das zurückgegebene Image-Array [-1] angegeben wird.

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## Erstellen einer VM

Das Erstellen einer VM beginnt mit dem Cmdlet **New-AzureVMConfig**. Hier geben Sie über **name** den Namen Ihrer VM, mit **size** die Größe Ihrer VM und schließlich das für die VM verwendete **image** an. Dieses Cmdlet erstellt ein lokales VM-Objekt **$myVM**, das später mithilfe anderer Azure PowerShell-Cmdlets in diesen Anweisungen geändert wird.

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

Als Nächstes müssen Sie **Benutzername** und **Kennwort** für Ihre VM angeben. Hierzu verwenden Sie das Cmdlet **Add-AzureProvisioningConfig**. Über dieses Cmdlet erhält Azure Info darüber, welches Betriebssystem für die VM verwendet wird. Beachten Sie, dass Sie weiterhin Änderungen am lokalen **$myVM**-Objekt vornehmen.

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

Jetzt können Sie Ihre VM in Azure erstellen. Hierzu verwenden Sie das Cmdlet **New-AzureVM**.

> [AZURE.NOTE] Sie müssen den Cloud-Dienst konfigurieren, bevor Sie Ihre VM erstellen können. Es gibt hierbei zwei Möglichkeiten.
* Erstellen Sie den Cloud-Dienst mit dem Cmdlet "New-AzureService". Wenn Sie diese Methode wählen, müssen Sie sicherstellen, dass der im Cmdlet "New-AzureVM" festgelegte Speicherort dem Ihres Cloud-Diensts entspricht, andernfalls tritt bei der Ausführung des Cmdlets "New-AzureVM" ein Fehler auf.
* Überlassen Sie diese Aufgabe dem Cmdlet "New-AzureVM". Sie müssen sicherstellen, dass der Dienstname eindeutig ist, andernfalls tritt bei der Ausführung des Cmdlets "New-AzureVM" ein Fehler auf.

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**OPTIONAL**

Sie können weitere Cmdlets, z. B.**Add-AzureDataDisk** oder **Add-AzureEndpoint** verwenden, um zusätzliche Optionen für Ihre VM zu konfigurieren.

## Abrufen einer VM
Nun, da Sie eine VM auf Azure erstellt haben, möchten Sie sicherlich sehen, wie sie funktioniert. Verwenden Sie hierzu das nachstehend gezeigte Cmdlet **Get-AzureVM**:

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## Nächste Schritte
[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Virtuelle Azure-Computer - häufig gestellte Fragen](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
 