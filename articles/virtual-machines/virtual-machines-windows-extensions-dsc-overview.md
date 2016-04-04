<properties
   pageTitle="Konfigurieren des gewünschten Zustands für Azure – Übersicht | Microsoft Azure"
   description="Übersicht über die Verwendung des Microsoft Azure-Erweiterungshandlers für PowerShell DSC. Mit Informationen zu Voraussetzungen, Architektur, Cmdlets..."
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

# Einführung in den Handler der Azure-Erweiterung zum Konfigurieren des gewünschten Zustands #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Der Azure VM-Agent und die dazugehörigen Erweiterungen sind Teil der Microsoft Azure-Infrastrukturdienste. VM-Erweiterungen sind Softwarekomponenten zur Erweiterung der VM-Funktionen und vereinfachen verschiedene VM-Verwaltungsvorgänge. So können Sie beispielsweise mit der VMAccess-Erweiterung das Kennwort eines virtuellen Computers zurücksetzen oder mit der Erweiterung für benutzerdefinierte Skripts ein Skript auf dem virtuellen Computer ausführen.

In diesem Artikel wird die PowerShell-Erweiterung zum Konfigurieren des gewünschten Zustands (Desired State Configuration, DSC) für virtuelle Azure-Computer als Teil des Azure PowerShell SDKs vorgestellt. Sie können neue Cmdlets verwenden, um eine PowerShell DSC-Konfiguration auf einen virtuellen Azure-Computer, der über die PowerShell DSC-Erweiterung verfügt, hochzuladen und anzuwenden. Die PowerShell DSC-Erweiterung richtet einen Aufruf an PowerShell DSC, um die empfangene DSC-Konfiguration auf dem virtuellen Computer in Kraft zu setzen. Diese Funktion steht auch über die Benutzeroberfläche des Vorschauportals zur Verfügung.

## Voraussetzungen ##
**Lokaler Computer** Für die Interaktion mit der Erweiterung für virtuelle Azure-Computer müssen Sie entweder die Benutzeroberfläche des Vorschauportals oder das Azure PowerShell SDK verwenden.

**Gast-Agent** Der virtuelle Azure-Computer, der mit der DSC-Konfiguration konfiguriert wird, muss über ein Betriebssystem verfügen, das Version 4.0 oder 5.0 von Windows Management Framework (WMF) unterstützt. Die vollständige Liste der unterstützten Betriebssystemversionen finden Sie im Versionsverlauf der DSC-Erweiterung.

## Begriffe und Konzepte ##
Für dieses Handbuch müssen Sie mit folgenden Konzepten vertraut sein:

Konfiguration: Ein DSC-Konfigurationsdokument.

Knoten: Ein Ziel für die DSC-Konfiguration. In diesem Dokument bezieht sich „Knoten“ immer auf einen virtuellen Azure-Computer.

Konfigurationsdaten: Eine PSD1-Datei mit Umgebungsdaten für eine Konfiguration.

## Übersicht über die Architektur ##

Die Azure DSC-Erweiterung nutzt das Agent-Framework von Azure zur Übermittlung und Inkraftsetzung von DSC-Konfigurationen auf virtuellen Azure-Computern sowie zur Erstellung entsprechender Berichte. Die DSC-Erweiterung erwartet eine ZIP-Datei mit mindestens einem Konfigurationsdokument sowie einen Parametersatz, der entweder über das Azure PowerShell SDK oder über die Benutzeroberfläche des Verwaltungsportals bereitgestellt wird.

Wenn die Erweiterung zum ersten Mal aufgerufen wird, wird ein Installationsvorgang ausgeführt. Dabei wird eine Version von Windows Management Framework (WMF) installiert:

1. Bei einem virtuellen Azure-Computer unter Windows Server 2016 wird keine Aktion ausgeführt. Unter Windows Server 2016 ist bereits die neueste Version von PowerShell installiert.
2. Bei Angabe der Eigenschaft `wmfVersion` wird die entsprechende WMF-Version installiert (es sei denn, sie ist nicht mit dem Betriebssystem des virtuellen Computers kompatibel).
3. Ohne Angabe der Eigenschaft `wmfVersion` wird die neueste geeignete WMF-Version installiert.

Die WMF-Installation erfordert einen Neustart. Nach dem Neustart lädt die Erweiterung die in der Eigenschaft `modulesUrl` angegebene ZIP-Datei herunter. Befindet sich der Speicherort im Azure-Blob Storage, kann in der Eigenschaft `sasToken` ein SAS-Token für den Dateizugriff angegeben werden. Nachdem die ZIP heruntergeladen und entpackt wurde, wird durch Ausführen der in `configurationFunction` definierten Konfigurationsfunktion die MOF-Datei generiert. Anschließend führt die Erweiterung `Start-DscConfiguration -force` für die generierte MOF-Datei aus. Die Erweiterung erfasst die Ausgabe und schreibt sie in den Azure-Statuskanal. Ab diesem Punkt behandelt der DSC-LCM die Überwachung und Korrektur wie gewohnt.

## PowerShell-Cmdlets ##

PowerShell-Cmdlets können mit ARM oder ASM zum Verpacken, Veröffentlichen und Überwachen der Bereitstellung von DSC-Erweiterungen verwendet werden. Bei den im Anschluss aufgeführten Cmdlets handelt es sich um ASM-Module, „Azure“ kann jedoch durch „AzureRM“ ersetzt werden, um das ARM-Modell zu verwenden. Beispiel: `Publish-AzureVMDscConfiguration` verwendet ASM, `Publish-AzureRmVMDscConfiguration` verwendet ARM.

`Publish-AzureVMDscConfiguration` durchsucht eine Konfigurationsdatei nach abhängigen DSC-Ressourcen und erstellt eine ZIP-Datei mit der Konfiguration und erforderlichen DSC-Ressourcen für deren Inkraftsetzung. Mithilfe des Parameters `-ConfigurationArchivePath` kann das Paket lokal erstellt werden. Andernfalls wird die ZIP-Datei im Azure-Blob Storage veröffentlicht und durch ein SAS-Token geschützt.

Bei der durch dieses Cmdlet erstellten ZIP-Datei befindet sich das PS1-Skript im Stammverzeichnis des Archivordners. Der Modulordner für Ressourcen wird im Archivordner platziert.

`Set-AzureVMDscExtension` fügt die erforderlichen Einstellungen für die PowerShell DSC-Erweiterung in ein VM-Konfigurationsobjekt ein, das dann mit `Update-AzureVm` auf einen virtuellen Azure-Computer angewendet werden kann.

`Get-AzureVMDscExtension` ruft den DSC-Erweiterungsstatus eines bestimmten virtuellen Computers ab.

`Get-AzureVMDscExtensionStatus` ruft den Status der DSC-Konfiguration ab, die durch den DSC-Erweiterungshandler auf einem virtuellen Computer oder für eine Gruppe von virtuellen Computern in Kraft gesetzt wurde.

`Remove-AzureVMDscExtension` entfernt den Erweiterungshandler von einem bestimmten virtuellen Computer. Dies bewirkt **nicht** die Entfernung der Konfiguration, die Deinstallation von WMF oder eine Änderung der angewendeten Einstellungen auf dem virtuellen Computer. Es wird lediglich der Erweiterungshandler entfernt.

**Wichtige Unterschiede zwischen ASM- und ARM-Cmdlets**

- ARM-Cmdlets sind synchron. ASM-Cmdlets sind asynchron.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version und Location sind jeweils neue erforderliche Parameter.
- ArchiveResourceGroupName ist ein neuer optionaler Parameter für ARM. Diesen können Sie angeben, wenn Ihr Speicherkonto nicht der Ressourcengruppe angehört, in der der virtuelle Computer erstellt wird.
- ConfigurationArchive heißt bei ARM ArchiveBlobName.
- ContainerName heißt bei ARM ArchiveContainerName.
- StorageEndpointSuffix heißt bei ARM ArchiveStorageEndpointSuffix.
- Für ARM wurde der Switch „–AutoUpdate“ hinzugefügt, um die automatische Aktualisierung des Erweiterungshandlers auf die neueste Version zu ermöglichen, sobald diese verfügbar ist. Hierdurch wird der virtuelle Computer unter Umständen neu gestartet, wenn eine neue WMF-Version veröffentlicht wird. 


## Funktionen des Vorschauportals ##
Navigieren Sie zu einem virtuellen Computer. Klicken Sie unter „Einstellungen“ > „Verwalten“ auf „Erweiterungen“. Ein neuer Bereich wird erstellt. Klicken Sie auf „Hinzufügen“, und wählen Sie „PowerShell DSC“ aus.

Nehmen Sie im Portal die erforderlichen Eingaben vor. **Konfigurationsmodule oder -skript**: Dies ist ein Pflichtfeld. Erfordert eine PS1-Datei mit einem Konfigurationsskript oder eine ZIP-Datei mit einem PS1-Konfigurationsskript am Verzeichnisstamm und allen abhängigen Ressourcen in Modulordnern innerhalb der ZIP. Die Datei kann mithilfe des Cmdlets `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` aus dem Azure PowerShell SDK erstellt werden. Die ZIP-Datei wird in Ihren Benutzer-Blob Storage hochgeladen und durch ein SAS-Token geschützt.

**PSD1-Konfigurationsdatendatei**: Dies ist ein optionales Feld. Wenn Ihre Konfiguration eine Konfigurationsdatendatei in PSD1 erfordert, wählen Sie mithilfe dieses Felds eine solche Datei aus, und laden Sie sie in Ihren Benutzer-Blob Storage hoch, wo sie durch ein SAS-Token geschützt wird.
 
**Modulspezifischer Konfigurationsname**: .PS1-Dateien können mehrere Konfigurationsfunktionen besitzen. Geben Sie den Namen des PS1-Konfigurationsskripts ein – gefolgt von '' und dem Namen der Konfigurationsfunktion. Beispiel:

**Konfigurationsargumente**: Falls die Konfigurationsfunktion Argumente akzeptiert, geben Sie diese hier im folgenden Format ein: `argumentName1=value1,argumentName2=value2`. Hierbei handelt es sich um ein anderes Format als bei den Konfigurationsargumenten, die von PowerShell-Cmdlets oder ARM-Vorlagen akzeptiert werden.

## Erste Schritte ##

Die Azure DSC-Erweiterung akzeptiert DSC-Konfigurationsdokumente und setzt sie auf virtuellen Azure-Computern in Kraft. Im Anschluss sehen Sie ein einfaches Beispiel für eine Konfiguration. Speichern Sie es lokal als „IisInstall.ps1“:

```powershell
configuration IISInstall 
{ 
    node ("localhost") 
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

In den folgenden Schritten wird das Skript „IisInstall.ps1“ dann auf dem angegebenen virtuellen Computer platziert, die Konfiguration wird ausgeführt, und der Status wird gemeldet.
 
```powershell
#Requires Azure Powershell cmdlets
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -vm $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -verbose

#Azure Powershell batches commands until an update-AzureVM command is given
$demoVM | Update-AzureVM -Verbose

#check on status
get-azurevmdscextensionstatus -VM $demovm -verbose
```

## Protokollierung ##

Speicherort der Protokolle:

C:\\WindowsAzure\\Logs\\Plugins\\Microsoft.Powershell.DSC[Versionsnummer]

## Nächste Schritte ##

Weitere Informationen zu PowerShell DSC finden Sie im [PowerShell-Dokumentationscenter](https://msdn.microsoft.com/powershell/dsc/overview).

Weitere Funktionen, die Sie mit PowerShell DSC verwalten können, finden Sie, indem Sie den [PowerShell-Katalog](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) nach weiteren DSC-Ressourcen durchsuchen.

Ausführliche Informationen zum Übergeben von sensiblen Parametern an Konfigurationen finden Sie unter [Sicheres Verwalten von Anmeldeinformationen mit dem DSC-Erweiterungshandler](virtual-machines-windows-extensions-dsc-credentials.md).

<!---HONumber=AcomDC_0323_2016-->