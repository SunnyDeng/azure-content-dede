<properties
	pageTitle="Informationen zu Images für virtuelle Computer"
	description="Informationen zur Nutzung von Images mit virtuellen Computern in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/13/2015"
	ms.author="kathydav"/>

# Informationen zu Images für virtuelle Computer

Images werden in Azure verwendet, um einen neuen virtuellen Computer mit einem Betriebssystem bereitzustellen. Ein Image kann auch über einen oder mehrere Datenträger verfügen. Images sind aus verschiedenen Quellen verfügbar:

-	Azure bietet Images im [Marketplace](http://azure.microsoft.com/gallery/virtual-machines/) an. Hier finden Sie aktuelle Versionen von Windows Server und Distributionen des Linux-Betriebssystems. Einige Images enthalten außerdem Clientanwendungen wie SQL Server. Abonnenten von MSDN Benefit und MSDN Pay-as-You-Go haben Zugriff auf weitere Images.
-	Die Open-Source-Community bietet Images im [VM Depot](http://vmdepot.msopentech.com/List/Index) an.
-	Sie können auch eigene Images in Azure speichern und nutzen, indem Sie entweder einen vorhandenen virtuellen Azure-Computer als Image erfassen oder ein Image hochladen.

## Informationen zu VM- und Betriebssystem-Images

Zwei Typen von Images können in Azure verwendet werden: *VM-Images* und *Betriebssystem-Images*. Ein VM-Image enthält ein Betriebssystem und alle Datenträger, die an einen virtuellen Computer angefügt sind, wenn das Image erstellt wird. Dies ist der neuere Typ von Image. Bevor VM-Images eingeführt wurden, konnte ein Image in Azure nur ein allgemeines Betriebssystem ohne zusätzliche Datenträger enthalten. Ein VM-Image, das nur ein verallgemeinertes Betriebssystem enthält, entspricht im Grunde dem ursprünglichen Typ von Image, dem Betriebssystem-Image.

Sie können basierend auf einem virtuellen Computer in Azure oder einem an anderer Stelle ausgeführten virtuellen Computer eigene Images erstellen, die Sie kopieren und hochladen. Wenn Sie ein Image verwenden möchten, um mehrere virtuelle Computer zu erstellen, müssen Sie es vor der Verwendung als Image durch eine Verallgemeinerung vorbereiten. Um ein Windows Server-Image zu erstellen, führen Sie den Befehl "Sysprep" auf dem Server aus, um es vor dem Hochladen der VHD-Datei zu verallgemeinern. Weitere Informationen zu Sysprep finden Sie im Artikel mit einer [Einführung in das Verwenden von Sysprep](http://go.microsoft.com/fwlink/p/?LinkId=392030). Zum Erstellen eines Linux-Images müssen Sie je nach Softwaredistribution eine Reihe von Befehlen, die für die Distribution spezifisch sind, und zudem den Azure Linux-Agent ausführen.

## Arbeiten mit Images

Die Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Windows oder das Azure PowerShell-Modul dienen zum Verwalten der Images, die in Ihrem Azure-Abonnement zur Verfügung stehen. Sie können für einige Aufgaben für Images auch das Azure-Portal nutzen, doch die Befehlszeile bieten Ihnen mehr Optionen.

Informationen zur Verwendung dieser Tools mit der Ressourcen-Manager-Bereitstellungen finden Sie unter [Navigieren zu und Auswählen von Azure Virtual Machine-Images mit PowerShell und der Azure-Befehlszeilenschnittstelle](resource-groups-vm-searching.md).

Beispiele der Verwendung der Tools in einer klassischen Bereitstellung:

- Informationen zur Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Dienstverwaltung](virtual-machines-command-line-tools.md).
- Die folgende Liste enthält Azure PowerShell-Befehle. Ein Beispiel für das Auffinden eines Images zum Erstellen einer VM finden Sie unter "Schritt 3: Ermitteln der ImageFamily" in [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md).

-	**Alle Images abrufen**: `Get-AzureVMImage` gibt eine Liste aller Images in Ihrem aktuellen Abonnement zurück, und zwar sowohl Ihre Images als auch von Azure oder Partnern bereitgestellte Images. Dies bedeutet, dass die Liste umfangreich sein kann. Die nächsten Beispiele zeigen, wie eine kürzere Liste abgerufen wird.
-	**Image-Familien abrufen**: `Get-AzureVMImage | select ImageFamily` Ruft eine Liste der Image-Familien ab, indem Zeichenfolgen mit der **ImageFamily**-Eigenschaft angezeigt werden.
-	**Alle Images in einer bestimmten Familie abrufen**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**VM-Images suchen**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` Hierbei erfolgt eine Filterung nach der "DataDiskConfiguration"-Eigenschaft, die nur für VM-Images gilt. Bei diesem Beispiel wird die Ausgabe auch nur nach der Bezeichnung und dem Namen des Images gefiltert.
-	**Ein verallgemeinertes Image speichern**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Ein spezialisiertes Image speichern**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip]Der "OSState"-Parameter ist erforderlich, wenn Sie ein VM-Image erstellen möchten, das neben dem Betriebssystem-Datenträger auch andere Datenträger enthält. Wenn Sie den Parameter nicht verwenden, erstellt das Cmdlet ein Betriebssystem-Image. Der Wert des Parameters gibt an, ob das Image verallgemeinert oder spezialisiert ist, was davon abhängt, ob der Betriebssystem-Datenträger auf die erneute Verwendung vorbereitet wurde.
-	**Ein Image löschen**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## Zusätzliche Ressourcen

[Verschiedene Möglichkeiten zum Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-choices-create-vm.md)

[Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=August15_HO8-->