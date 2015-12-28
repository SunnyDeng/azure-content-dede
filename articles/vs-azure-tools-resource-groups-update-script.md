<properties
	pageTitle="Aktualisieren des PowerShell-Skripts für ein Azure-Ressourcengruppenprojekt | Microsoft Azure"
	description="Beschreibt die Schritte zum manuellen Aktualisieren des PowerShell-Skripts, das Teil eines Azure-Ressourcengruppen-Bereitstellungsprojekts ist."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/20/2015"
	ms.author="kempb" />

# Aktualisieren des PowerShell-Skripts für ein Azure-Ressourcengruppenprojekt

Im Azure-Ressourcengruppen-Bereitstellungsprojekt in Visual Studio wird ein Azure PowerShell-Skript verwendet, um Ihre Ressourcen aus Visual Studio vorzubereiten und sie in Azure bereitzustellen. Für das Skript im Projekt wird die Azure PowerShell-Version 0.9.8 verwendet. Inzwischen wurde jedoch die Version 1.0 von Azure PowerShell veröffentlicht.

Die neue Version von Azure PowerShell bietet eine Reihe von neuen Funktionen und macht die Verwendung des Azure-Ressourcen-Managers zu einer erstklassigen Erfahrung. Es wurden jedoch auch Änderungen an verschiedenen Cmdlet-Funktionen vorgenommen, durch die das für die vorherige Version erstellte Skript inkompatibel wird. Um dieses Problem zu beheben, können Sie das Skript ändern und es dann außerhalb von Visual Studio in einem PowerShell-Befehlsfenster ausführen. In diesem Artikel erfahren Sie, wo Sie welche Änderungen an dem Skript vornehmen müssen. Zusätzlich zu den einzelnen Änderungen wird am Ende des Artikels auch das vollständig geänderte Skript aufgeführt.

## Optionen zur Problemumgehung

Nach der Installation der neuen Version von Azure PowerShell kann sich das Problem in Ihrem Azure-Ressourcengruppenprojekt auf zweierlei Weise zeigen.

- Beim Bereitstellungsdialog in Visual Studio können die neuen Azure-Cmdlets nicht gefunden werden, und Sie werden aufgefordert, diese zu installieren. Da die Module jedoch in der neuen Version der Cmdlets umbenannt wurden, kann Visual Studio die neuen Module nicht finden. Das kann dazu führen, dass Sie beim Installieren der Cmdlets in Visual Studio in einer Schleife stecken bleiben.

- Wenn Sie das Skript außerhalb von Visual Studio im PowerShell-Befehlsfenster ausführen, wird u. U. die folgende Fehlermeldung ausgegeben:

	*Der Begriff „Switch-AzureMode“ wurde nicht als Name eines Cmdlets, einer Funktion, einer Skriptdatei oder eines ausführbaren Programms erkannt. Prüfen Sie die Schreibweise des Namens bzw. stellen Sie sicher, dass der Pfad korrekt angegeben wurde, und versuchen Sie es erneut.*

Wenn Sie die neue Version von Azure PowerShell am Bereitstellen Ihres Azure-Ressourcengruppenprojekts hindert, bieten sich diese Lösungswege an:

- Sie können die neueste Version von Azure PowerShell deinstallieren und über den [Webplattform-Installer](http://www.microsoft.com/web/downloads/platform.aspx) wieder die vorherige Version (0.9.8) installieren.

- Sie können das Problem beheben, indem Sie spezifische, zielgerichtete Änderungen am PowerShell-Skript des Bereitstellungsprojekts vornehmen und anschließend das Skript manuell im PowerShell-Befehlsfenster ausführen. Sie können dieses Skript nicht über den Menübefehl **Bereitstellen** in Visual Studio ausführen. Die Schritte zum Aktualisieren des PowerShell-Skripts werden nachfolgend aufgeführt. Außerdem ist auch das vollständig aktualisierte Skript enthalten.

## Aktualisieren des Azure PowerShell-Skripts
Die folgenden Anweisungen beziehen sich auf Zeilennummern. Das Verfahren zum Aktivieren der Zeilennummerierung in Visual Studio finden Sie unter [Gewusst wie: Anzeigen von Zeilennummern im Editor](https://msdn.microsoft.com/library/ms165340.aspx).

1. Ersetzen Sie in Zeile 61 den folgenden Code:

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	durch:

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
	```

1. Ersetzen Sie in Zeile 87 den folgenden Code:

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	durch:

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. Ersetzen Sie in Zeile 95 diesen Code:

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	durch den folgenden Code:

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-ErrorAction Stop 	

	New-AzureRMResourceGroupDeployment
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	    -ResourceGroupName $ResourceGroupName `
	    -TemplateFile $TemplateFile `
	    -TemplateParameterFile $TemplateParametersFile `
	    @OptionalParameters `
		-Verbose -Force
	```

## Aktualisiertes Skript
Es folgt das modifizierte Skript mit allen zuvor genannten Änderungen.

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

Test-AzureRmResourceGroupDeployment `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-ErrorAction Stop 	

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $TemplateFile `
    -TemplateParameterFile $TemplateParametersFile `
    @OptionalParameters `
	-Verbose -Force

```

<!---HONumber=AcomDC_1217_2015-->