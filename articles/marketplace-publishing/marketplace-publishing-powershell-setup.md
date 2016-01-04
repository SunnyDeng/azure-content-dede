<properties
   pageTitle="Einrichten von PowerShell zum Erstellen eines virtuellen Computers für den Marketplace | Microsoft Azure"
   description="Anweisungen zum Einrichten von Azure PowerShell und Verwenden als optionalen Prozessablauf zum Erstellen von VM-Images für die Bereitstellung und den Verkauf in Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="hascipio"/>

# Einrichten von Azure PowerShell zum Erstellen eines Angebots für Azure Marketplace
Ausführliche Informationen zum Einrichten von PowerShell in Azure finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Ein einfacher Ansatz ist die Zertifikatmethode, bei der ein für die Authentifizierung benötigtes Zertifikat heruntergeladen und importiert wird. Um das erforderliche Zertifikat zu erhalten, verwenden Sie das Cmdlet **Get-AzurePublishSettingsFile**. Speichern Sie die Datei, wenn Sie dazu aufgefordert werden. Verwenden Sie das Cmdlet **Import-AzurePublishSettingsFile**, um das Zertifikat in eine PowerShell-Sitzung zu importieren.

Verwenden Sie zum Konfigurieren und Speichern der allgemeinen Microsoft Azure-Abonnementeinstellungen für die PowerShell-Sitzung die Cmdlets **Set-AzureSubscription** und **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Der erste Befehl ordnet dem Abonnement ein Standardspeicherkonto zu (das für einige VM-Bereitstellungsvorgänge erforderlich ist). Die zweite richtet das Abonnement als das aktuelle ein (was von anderen Cmdlets erkannt wird).

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots in Azure Marketplace](marketplace-publishing-getting-started.md)
- [Erstellen eines VM-Images für den Marketplace](marketplace-publishing-vm-image-creation.md)

<!---HONumber=Nov15_HO4-->