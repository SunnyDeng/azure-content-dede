<properties
   pageTitle="Erste Schritte in Azure RemoteApp mit Powershell"
   description="Erfahren Sie mehr über Azure RemoteApp mit Powershell"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="07/07/2015"
   ms.author="guscatal;spatnaik;elizapo"/>



# Erste Schritte in Azure RemoteApp mit Powershell
=====================================


## Abrufen der Cmdlets 
-------------
Zunächst müssen Sie [hier](http://go.microsoft.com/?linkid=9811175) die Azure PowerShell-Cmdlets herunterladen. Die RemoteApp-Befehle sind darin enthalten.

## Konfigurieren Sie Azure-Cmdlets, um Ihr Abonnement zu verwenden
------------------
Folgen Sie den Anweisungen [dieses Handbuchs](../powershell-install-configure.md), sodass Sie die Cmdlets für Ihr Azure-Abonnement verwenden können.

## Erstellen einer Cloud-Sammlung
--------------------
Es ist ganz leicht. Führen Sie den folgenden Befehl aus:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

Microsoft Office 365-Anwendungen (Excel, OneNote, Outlook, PowerPoint, Visio und Word) werden vom oben aufgeführten Befehl automatisch veröffentlicht.

Die Erstellung der Sammlung dauert 30 Minuten oder länger, bis sie abgeschlossen ist. Dieser Befehl gibt daher eine Nachverfolgungs-ID zurück, die Sie wie folgt nutzen können:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Nach Abschluss der Sammlung können Sie Benutzer mit dem folgenden Befehl hinzufügen:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Und Sie haben es geschafft! Dieser Benutzer sollte eine Verbindung zur Anwendung mit dem Azure RemoteApp-Client herstellen können, der sich [hier](https://www.remoteapp.windowsazure.com/) befindet.

## Verfügbare Cmdlets
Es gibt viele weitere Befehle, für die es in Kürze eine Dokumentation gibt:

Grundlegende Sammlungs-Cmdlets für die RemoteApp:

- New-AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set-AzureRemoteAppCollection
- Update-AzureRemoteAppCollection
- Remove-AzureRemoteAppCollection
- Add-AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Remove-AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Disconnect-AzureRemoteAppSession
- Invoke-AzureRemoteAppSessionLogoff
- Send-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publish-AzureRemoteAppProgram
- Unpublish-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

VM-Cmdlets für die RemoteApp:

- New-AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set-AzureRemoteAppVNet
- Remove-AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get-- AzureRemoteAppVpnDeviceConfigScript
- Reset-AzureRemoteAppVpnSharedKey

Vorlagenimage-Cmdlets für die RemoteApp:

- New-AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Rename-AzureRemoteAppTemplateImage
- Remove-AzureRemoteAppTemplateImage

Andere RemoteApp-Cmdlets:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 

<!---HONumber=August15_HO6-->