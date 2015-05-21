<properties
   pageTitle="Häufige Aufgaben für virtuelle Computer mit dem Dienstverwaltungsmodus in Azure PowerShell"
   description="Beschreibt Befehle für häufige Aufgaben für virtuelle Computer mit dem Dienstverwaltungsmodus in Azure PowerShell"
   services="virtual-machines"
   documentationCenter="windows"
   authors="KBDAzure"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="kathydav;singhkay"/>

# Häufige Aufgaben für virtuelle Computer mit Azure PowerShell im Dienstverwaltungsmodus

 > [AZURE.SELECTOR]
    - [Resource Manager](virtual-machines-manage-powershell-resource-management.md)
    - [Service Management](virtual-machines-manage-powershell-service-management.md)

Viele Aufgaben, die Sie jeden Tag zur Verwaltung der virtuellen Computer ausführen, können mithilfe von Azure PowerShell-Cmdlets im Dienstverwaltungsmodus automatisiert werden. In diesem Artikel finden Sie Beispiele für Befehle für einfachere Aufgaben und Links zu Artikeln, die die Befehle für komplexere Aufgaben behandeln.

>[AZURE.NOTE]Wenn Sie Azure PowerShell noch nicht installiert und konfiguriert haben, finden Sie [hier](install-configure-powershell.md) Anweisungen dazu.

## So verwenden Sie die Beispielbefehle
Sie müssen einen Teil des Texts in den Befehlen durch Text ersetzen, der für Ihre Umgebung geeignet ist. Die < and >-Symbole zeigen Text an, der ersetzt werden muss. Wenn Sie den Text ersetzen, entfernen Sie die Symbole, behalten jedoch die Anführungszeichen bei.

## Abrufen eines virtuellen Computers
Dies ist eine einfache Aufgabe, die Sie häufig verwenden. Verwenden Sie sie zum Abrufen von Informationen zu einem virtuellen Computer, Ausführen von Aufgaben auf diesem oder Abrufen von Ausgaben zur Speicherung in einer Variablen.

Um Informationen über den virtuellen Computer zu erhalten, führen Sie diesen Befehl aus und ersetzen allen Text in Anführungszeichen einschließlich der < and > Zeichen:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Führen Sie zum Speichern der Ausgabe in eine $vm-Variable folgenden Code aus:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Anmelden bei einem virtuellen Computer auf Windows-Basis

Führen Sie diese Befehle aus.

>[AZURE.NOTE]Sie erhalten den Namen des virtuellen Computers und Clouddiensts über die Anzeige des **Get-AzureVM** Befehls.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "\" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Anhalten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Verwenden Sie den **StayProvisioned**-Parameter, um die virtuelle IP-Adresse des Clouddiensts beizubehalten, falls es sich um den letzten virtuellen Computer in diesem Clouddienst handelt. Auch wenn Sie diesen Parameter verwenden, wird der virtuelle Computer in Rechnung gestellt.

## Starten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Anfügen eines Datenträgers
Diese Aufgabe erfordert einige Schritte. Zunächst verwenden Sie das Cmdlet ****Add-AzureDataDisk****, um dem $vm-Objekt den Datenträger hinzuzufügen, anschließend aktualisieren Sie die Konfiguration des virtuellen Computers mit dem Cmdlet "Update-AzureVM".

Sie müssen auch entscheiden, ob Sie einen neuen Datenträger anfügen oder einen, der Daten enthält. Für einen neuen Datenträger erstellt der Befehl die VHD-Datei und fügt sie im selben Befehl an.

Um einen neuen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

Um einen vorhandenen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

Führen Sie den folgenden Befehl zum Anfügen von Datenträgern aus einer vorhandenen VHD-Datei im Blob-Speicher aus:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Erstellen eines virtuellen Windows-Computers

Zum Erstellen eines auf Windows basierenden virtuellen Computers in Azure folgen Sie den Anweisungen unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md). In diesem Thema wird schrittweise erläutert, wie Sie einen PowerShell-Befehlssatz erstellen, mit dem Sie einen virtuellen Windows-Computer generieren, der mit folgenden Optionen vorkonfiguriert werden kann:

- Mitgliedschaft in einer Active Directory-Domäne
- Zusätzliche Datenträger
- Mitgliedschaft in einer vorhandenen Lastenausgleichsgruppe
- Statische IP-Adresse


<!--HONumber=52-->
