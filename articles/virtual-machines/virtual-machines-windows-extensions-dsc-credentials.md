<properties
   pageTitle="Übergeben von Anmeldeinformationen an Azure mithilfe von DSC | Microsoft Azure"
   description="Überblick über das sichere Übergeben von Anmeldeinformationen an virtuelle Azure-Computer mithilfe von PowerShell DSC"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="01/25/2016"
   ms.author="zachal"/>

# Übergeben von Anmeldeinformationen an den Azure DSC-Erweiterungs-Handler #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

In diesem Artikel wird die Erweiterung der Konfiguration für den gewünschten Zustand (Desired State Configuration; DSC) für Azure behandelt. Einen Überblick über den DSC-Erweiterungs-Handler finden Sie unter [Introduction to the Azure Desired State Configuration extension handler](virtual-machines-windows-extensions-dsc-overview.md) (Einführung zum Azure DSC-Erweiterungs-Handler).

Möglicherweise müssen Sie als Teil des Konfigurationsvorganges Benutzerkonten einrichten, auf Dienste zugreifen oder ein Programm im Benutzerkontext installieren. Um diese Aktionen durchführen zu können, müssen Sie Anmeldeinformationen bereitstellen.

DSC lässt parametrisierte Konfigurationen zu, in denen Anmeldeinformationen in die Konfiguration übergeben und sicher in MOF-Dateien gespeichert werden. Der Azure-Erweiterungs-Handler vereinfacht die Verwaltung von Anmeldeinformationen, indem er die automatische Verwaltung von Zertifikaten bietet.

Ziehen Sie das folgende DSC-Konfigurationsskript, das ein lokales Benutzerkonto mit dem angegebenen Kennwort erstellt, in Betracht:

*user\_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Es ist wichtig, dass *node localhost* Teil der Konfiguration ist. Der Erweiterungs-Handler sucht insbesondere nach der „node localhost“-Anweisung und funktioniert nicht ohne diese Anweisung. Es ist ebenfalls wichtig, dass die Typumwandlung *[PsCredential]* enthalten ist, da dieser spezifische Typ die Verschlüsselung der Anmeldeinformationen durch die Erweiterung, wie unten beschrieben, auslöst.

Veröffentlichen dieses Skripts im Blobspeicher:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Einrichten der Azure DSC-Erweiterung und Bereitstellen der Anmeldeinformationen:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```

Auf das Ausführen dieses Codes folgt die Aufforderung, Anmeldeinformationen anzugeben. Sobald diese angegeben werden, werden sie kurzfristig im Arbeitsspeicher gespeichert. Bei der Veröffentlichung mit dem `Set-AzureVmDscExtension`-Cmdlet werden sie über HTTPS an die VM übertragen, wobei Azure sie verschlüsselt auf den Datenträger speichert. Dafür wird das lokale VM-Zertifikat verwendet. Sie werden dann für kurze Zeit im Arbeitsspeicher entschlüsselt und wieder verschlüsselt, um an DSC übergeben zu werden.

Dies unterscheidet sich von der Verwendung von sicheren Konfigurationen ohne den Erweiterungs-Handler. Die Azure-Umgebung bietet durch Zertifikate eine Möglichkeit zum sicheren Übertragen von Konfigurationsdaten. Deshalb müssen bei Verwendung des DSC-Erweiterungs-Handlers keine Einträge für $CertificatePath oder $CertificateID / $Thumbprint in ConfigurationData vorgenommen werden.


## Nächste Schritte ##

Weitere Informationen zum Azure DSC-Erweiterungs-Handler finden Sie unter [Introduction to the Azure Desired State Configuration extension handler](virtual-machines-windows-extensions-dsc-overview.md) (Einführung in den Azure DSC-Erweiterungs-Handler).

Weitere Informationen zu PowerShell DSC finden Sie unter [Windows PowerShell Desired State Configuration Overview](https://msdn.microsoft.com/powershell/dsc/overview) (Überblick über Windows PowerShell DSC).

Durchsuchen Sie die Webseite [PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0), um herauszufinden, welche zusätzlichen Funktionen Sie mit PowerShell DSC verwalten können.

<!---HONumber=AcomDC_0323_2016-->