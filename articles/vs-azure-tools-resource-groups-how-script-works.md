<properties
	pageTitle="Übersicht über das Skript des Bereitstellungsprojekts für Azure-Ressourcengruppen | Microsoft Azure"
	description="Beschreibt die Funktionsweise des PowerShell-Skripts im Bereitstellungsprojekt für Azure-Ressourcengruppen."
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
	ms.date="11/17/2015"
	ms.author="kempb" />

# Übersicht über das Skript des Bereitstellungsprojekts für Azure-Ressourcengruppen

Bereitstellungsprojekte für Azure-Ressourcengruppen helfen Ihnen beim Staging und Bereitstellen von Dateien und anderen Artefakten in Azure. Wenn Sie ein Azure-Ressourcen-Manager-Bereitstellungsprojekt in Visual Studio erstellen, wird dem Projekt ein PowerShell-Skript namens **Deploy-AzureResourceGroup.ps1** hinzugefügt. Dieses Thema enthält Details zur Funktionsweise dieses Skripts und sowie zur Ausführung sowohl innerhalb als auch außerhalb von Visual Studio.

## Wie funktioniert das Skript?

Das Skript „Deploy-AzureResourceGroup.ps1“ führt zwei Aktionen aus, die für den Bereitstellungsworkflow von Bedeutung sind.

- Hochladen von Dateien oder Artefakten, die für die Vorlagenbereitstellung erforderlich sind
- Bereitstellen der Vorlage

Der erste Teil des Skripts lädt die Dateien und Artefakte hoch, und das letzte Cmdlet in das Skript stellt die Vorlage bereit. Wenn beispielsweise eine virtuelle Maschine mit einem Skript konfiguriert werden muss, lädt das Bereitstellungsskript zunächst das Konfigurationsskript sicher in ein Azure-Speicherkonto hoch. Dadurch wird es für den Azure-Ressourcen-Manager zur Konfiguration der virtuellen Maschine während der Bereitstellung verfügbar.

Da nicht alle Vorlagenbereitstellungen das Hochladen zusätzlicher Elemente erfordern, wird ein Switch-Parameter namens *UploadArtifacts* ausgewertet. Wenn Artefakte hochgeladen werden müssen, geben Sie beim Aufrufen des Skripts den Switch *UploadArtifacts* an. Beachten Sie, dass die Hauptvorlagendatei und die Parameterdatei nicht hochgeladen werden müssen. Nur andere Dateien, z. B. Konfigurationsskripts, geschachtelte Bereitstellungsvorlagen und Anwendungsdateien müssen übertragen werden.

## Ausführliche Skriptbeschreibung

Es folgt eine Beschreibung der Aufgaben ausgewählter Abschnitte des Azure PowerShell-Skripts „Deploy-AzureResourceGroup.ps1“.

>[AZURE.NOTE]Hier wird Version 1.0 des Skripts „Deploy-AzureResourceGroup.ps1“ beschrieben.

1.	Deklarieren Sie die für das Azure-Ressourcen-Manager-Bereitstellungsprojekt benötigten Parameter. Einige Parameter haben Standardwerte, die bei der Projekterstellung festgelegt wurden. Sie können diese Standardwerte im ändern oder andere Parameterwerte hinzufügen, bevor Sie das Skript ausführen.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |Parameter|Beschreibung|
    |---|---|
    |$ResourceGroupLocation|Die Region bzw. der Standort des Rechenzentrums für die Ressourcengruppe, z. B. **Westen der USA** oder **Ostasien**.|
    |$ResourceGroupName|Name der Azure-Ressourcengruppe|
    |$UploadArtifacts|Ein binärer Wert, der angibt, ob Artefakte aus dem System in Azure hochgeladen werden müssen|
    |$StorageAccountName|Name des Azure-Speicherkontos, in das Ihre Artefakte hochgeladen werden|
    |$StorageAccountResourceGroupName|Name der Azure-Ressourcengruppe, die das Speicherkonto enthält|
    |$StorageContainerName|Name des Speichercontainers für das Hochladen von Artefakten|
    |$TemplateFile|Pfad zur Bereitstellungsdatei (`<app name>.json`) in Ihrem Azure-Ressourcengruppenprojekt|
    |$TemplateParametersFile|Pfad zur Parameterdatei (`<app name>.parameters.json`) in Ihrem Azure-Ressourcengruppenprojekt|
    |$ArtifactStagingDirectory|Der Pfad auf dem System, in dem Artefakte lokal hochgeladen werden, einschließlich des Stammordners des PowerShell-Skripts Dieser Pfad kann absolut oder relativ zum Skriptspeicherort sein.|
    |$AzCopyPath|Der Pfad, in den das Tool „AzCopy.exe“ die ZIP-Dateien kopiert, einschließlich des Stammordners des PowerShell-Skripts. Dieser Pfad kann absolut oder relativ zum Skriptspeicherort sein.|
    |$DSCSourceFolder|Der Pfad für den DSC (Desired State Configuration)-Quellordner, einschließlich des Stammordners des PowerShell-Skripts. Dieser Pfad kann absolut oder relativ zum Skriptspeicherort sein. Weitere Informationen finden Sie ggf. unter [Einführung in die Azure PowerShell-Erweiterung DSC (Desired State Configuration)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx).|

1.	Überprüfen Sie, ob Artefakte in Azure hochgeladen werden müssen. Wenn dies nicht der Fall ist, fahren Sie mit Schritt 11 fort. Andernfalls führen Sie die folgenden Schritte aus:

1.	Konvertieren Sie alle Variablen mit relativen Pfade in absolute Pfade. Ändern Sie z. B. einen Pfad wie `..\Tools\AzCopy.exe` zu `C:\YourFolder\Tools\AzCopy.exe`. Initialisieren Sie außerdem die Variablen *ArtifactsLocationName* und *ArtifactsLocationSasTokenName* auf NULL. *ArtifactsLocation* und *SaSToken* können als Parameter für die Vorlage verwendet werden. Wenn nach dem Lesen in der Parameterdatei ihre Werte NULL sind, generiert das Skript die Werte für sie.

    Die Azure-Tools verwenden die Parameterwerte „*\_artifactsLocation*“ und „*\_artifactsLocationSasToken*“ in der Vorlage zur Verwaltung von Artefakten. Wenn das PowerShell-Skript Parameter mit diesen Namen findet, die Parameterwerte jedoch nicht bereitgestellt sind, lädt das Skript die Artefakte hoch und gibt die entsprechenden Werte für diese Parameter zurück. Anschließend übergibt es die Werte über `@OptionsParameters` an das Cmdlet.

	|Variable|Beschreibung|
    |---|---|
    |ArtifactsLocationName|Der Pfad, in dem sich die Azure-Artefakte befinden|
    |ArtifactsLocationSasTokenName|Der Name des vom Skript zur Authentifizierung bei Service Bus verwendeten SAS (Shared Access Signature)-Tokens Weitere Informationen finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](service-bus-shared-access-signature-authentication.md).|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	Dieser Abschnitt überprüft, ob die Datei „<app name>.parameters.json“ (als „Parameterdatei“ bezeichnet) über einen übergeordneten Knoten mit dem Namen **Parameter** (im `else`-Block) verfügt. Andernfalls hat sie keinen übergeordneten Knoten. Beide Formate sind zulässig.
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	Durchlaufen Sie die Auflistung der JSON-Parameter. Wenn „*\_artifactsLocation*“ oder „*\_artifactsLocationSasToken*“ ein Parameterwert zugewiesen wurde, geben Sie die Variable *$OptionalParameters* mit diesen Werten an. Dadurch wird verhindert, dass das Skript versehentlich Parameterwerte überschreibt, die Sie bereitstellen.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	Rufen Sie den Speicherkontoschlüssel und den Kontext für die Speicherkontoressource ab, in der sich die Artefakte für die Bereitstellung befinden.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	Wenn Sie PowerShell DSC zum Konfigurieren einer virtuellen Maschine verwenden, erfordert die DSC-Erweiterung eine einzelne ZIP-Datei mit allen Artefakten. Erstellen Sie also eine ZIP-Archivdatei für die DSC-Konfiguration. Überprüfen Sie dazu, ob „$DSCSourceFolder“ vorhanden ist. Wenn eine DSC-Konfiguration vorhanden ist, entfernen Sie diese und erstellen eine neue komprimierte Datei namens „dsc.zip“.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	Wenn in der Parameterdatei kein Pfad für Azure-Artefakte bereitgestellt wird, legen Sie einen Pfad fest, der vom PowerShell-Skript beim Hochladen von Artefakten verwendet wird. Erstellen Sie dazu einen Pfad mit einer Kombination aus dem Endpunktpfad des Speicherkonto und dem Namen des Speichercontainers. Aktualisieren Sie dann die Parameterdatei mit diesem neuen Pfad.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	Verwenden Sie das Dienstprogramm **AzCopy** (im Order **Tools** Ihres Bereitstellungsprojekts für Azure-Ressourcengruppen), um Dateien aus Ihrem lokalen Speicherablagepfad in Ihr Azure-Onlinespeicherkonto zu kopieren. Wenn dieser Schritt fehlschlägt, beenden Sie das Skript, da die Bereitstellung ohne die erforderlichen Elemente wahrscheinlich nicht erfolgreich ist.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	Wenn in der Parameterdatei kein SAS-Token für den Artefaktspeicherort enthalten ist, erstellen Sie eines, um vorübergehend Lesezugriff auf den Onlinespeichercontainer bereitzustellen. Übergeben Sie dann das SAS-Token an der Befehlszeile als „optionalParameter“. Beachten Sie, dass auf der Befehlszeile übergebene Parameter Vorrang vor in der Parameterdatei bereitgestellten Werten haben.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Wenn die Ressourcengruppe noch nicht vorhanden ist, erstellen Sie diese und überprüfen die Vorlagen- und Parameterdatei auf Validierungsfehler, die eine erfolgreiche Bereitstellung verhindern.

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Stellen Sie schließlich die Vorlage bereit. Dieser Code erstellt mithilfe eines Zeitstempels einen eindeutigen Namen für die Bereitstellung.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## Bereitstellung der Ressourcengruppe

### So stellen Sie die Ressourcengruppe in Visual Studio bereit

1. Wählen Sie im Kontextmenü Azure-Ressourcengruppenprojekts die Optionen **Bereitstellen** > **Neue Bereitstellung**.

    ![][0]

1. Wählen Sie im Dialogfeld **Für Ressourcengruppe bereitstellen** entweder eine vorhandene Ressourcengruppe aus der Dropdownliste zur Bereitstellung aus, oder wählen Sie **&lt;Neu erstellen…&gt;**, um eine neue Ressourcengruppe zu erstellen.

    ![][1]

1. Wenn Sie dazu aufgefordert werden, geben Sie im Dialogfeld **Ressourcengruppe erstellen** einen Namen und einen Speicherort für die Ressourcengruppe ein und wählen dann die Schaltfläche **Erstellen**.

    ![][2]

1. Wählen Sie die Schaltfläche **Parameter bearbeiten**, um das Dialogfeld **Parameter bearbeiten** aufzurufen, und geben Sie dann alle fehlenden Parameterwerte ein.

    ![][3]

	>[AZURE.NOTE]Wenn erforderliche Parameter Werte benötigen, wird dieses Dialogfeld bei der Bereitstellung automatisch angezeigt.

    ![][4]

1. Wenn Sie alle Parameterwerte eingegeben haben, wählen Sie die Schaltfläche **Speichern** und dann die Schaltfläche **Bereitstellen**.

    Das Bereitstellungsskript (Deploy-AzureResourceGroup.ps1) wird ausgeführt, und Ihre Vorlage sowie alle Artefakte werden in Azure bereitgestellt.

### So stellen Sie die Ressourcengruppe mithilfe von PowerShell bereit

Wenn Sie das Skript ohne den Visual Studio-Befehl und Benutzeroberfläche ausführen möchten, wählen Sie im Kontextmenü für das Skript die Option **Mit PowerShell ISE öffnen**.

![][5]


## Beispiele für die Bereitstellung von Befehlen

### Bereitstellung mit Standardwerten

In diesem Beispiel wird die Ausführung des Skripts mit den Standardwerten für Parameter veranschaulicht. (Da der Standortparameter keinen Standardwert aufweist, müssen Sie einen bereitstellen.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### Bereitstellung durch Überschreiben der Standardwerte

Dieses Beispiel veranschaulicht die Ausführung des Skripts zur Bereitstellung von Vorlagen- und Parameterdateien, die von den Standardwerten abweichen.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### Bereitstellung durch Staging mithilfe von „UploadArtifacts“

Dieses Beispiel veranschaulicht die Ausführung des Skripts zum Hochladen von Artefakten aus dem Freigabeordner und zum Bereitstellen von nicht standardmäßigen Vorlagen.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Dieses Beispiel veranschaulicht die Ausführung des Skript in einer Azure PowerShell-Aufgabe in Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## Nächste Schritte
Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

<!---HONumber=AcomDC_1217_2015-->