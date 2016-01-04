<properties
   pageTitle="Verwalten Ihrer virtuellen Computer mit Azure PowerShell | Microsoft AzureVerwalten Sie Ihre virtuellen Computer mithilfe von Azure PowerShell | Microsoft Azure"
   description="Lernen Sie Befehle kennen, mit denen Sie Aufgaben bei der Verwaltung Ihrer virtuellen Computer verwenden können."
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="kasing"/>

# Verwalten Ihrer virtuellen Computer mithilfe von Azure PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Viele Aufgaben, die Sie jeden Tag zur Verwaltung der virtuellen Computer ausführen, können mithilfe von Azure PowerShell-Cmdlets automatisiert werden. In diesem Artikel finden Sie Beispiele für Befehle für einfachere Aufgaben und Links zu Artikeln, die die Befehle für komplexere Aufgaben behandeln.

>[AZURE.NOTE]Wenn Sie Azure PowerShell noch nicht installiert und konfiguriert haben, erhalten Sie die Anweisungen dazu im Artikel [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

## So verwenden Sie die Beispielbefehle
Sie müssen einen Teil des Texts in den Befehlen durch Text ersetzen, der für Ihre Umgebung geeignet ist. Die < and >-Symbole zeigen Text an, der ersetzt werden muss. Wenn Sie den Text ersetzen, entfernen Sie die Symbole, behalten jedoch die Anführungszeichen bei.

## Abrufen eines virtuellen Computers
Dies ist eine einfache Aufgabe, die Sie häufig verwenden. Verwenden Sie sie zum Abrufen von Informationen zu einem virtuellen Computer, Ausführen von Aufgaben auf diesem oder Abrufen von Ausgaben zur Speicherung in einer Variablen.

Um Informationen über den virtuellen Computer zu erhalten, führen Sie diesen Befehl aus und ersetzen allen Text in Anführungszeichen einschließlich der < and >-Zeichen:

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
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Anhalten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Verwenden Sie diesen Parameter, um die virtuelle IP-Adresse des Clouddiensts beizubehalten, falls es sich um den letzten virtuellen Computer in diesem Clouddienst handelt. <br><br> Wenn Sie diesen Parameter verwenden, wird der virtuelle Computer dennoch in Rechnung gestellt.

## Starten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Datenträger anfügen
Diese Aufgabe erfordert einige Schritte. Zunächst verwenden Sie das Cmdlet ****Add-AzureDataDisk**** zum Hinzufügen des Datenträgers zum $vm-Objekt. Dann verwenden Sie das Cmdlet **Update-AzureVM**, um die Konfiguration des virtuellen Computers zu aktualisieren.

Sie müssen auch entscheiden, ob Sie einen neuen Datenträger anfügen oder einen, der Daten enthält. Für einen neuen Datenträger erstellt der Befehl die VHD-Datei und fügt sie an.

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

## Erstellen eines virtuellen Computers auf Windows-Basis

Zum Erstellen eines auf Windows basierenden virtuellen Computers in Azure folgen Sie den Anweisungen unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md). In diesem Thema wird schrittweise erläutert, wie Sie einen Azure PowerShell-Befehlssatz erstellen, mit dem Sie einen virtuellen Windows-Computer generieren, der vorkonfiguriert werden kann:

- Mit einer Mitgliedschaft in einer Active Directory-Domäne.
- Mit zusätzlichen Datenträgern.
- Als Mitglied in einer vorhandenen Lastenausgleichsgruppe.
- Mit einer statischen IP-Adresse.

## Erstellen eines virtuellen Linux-Computers

Zum Erstellen eines auf Linux basierenden virtuellen Computers in Azure folgen Sie den Anweisungen unter [Erstellen und Vorabkonfigurieren eines virtuellen Linux-Computers mit Azure PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md).

- Mit zusätzlichen Datenträgern.
- Als Mitglied in einer vorhandenen Lastenausgleichsgruppe.
- Mit einer statischen IP-Adresse.

<!---HONumber=AcomDC_1217_2015-->