<properties
   pageTitle="Verwenden des Azure PowerShell-Befehls zum Erstellen eines leeren Clouddienstcontainers"
   description="In diesem Artikel wird erläutert, wie Sie PowerShell-Skripts verwenden, um Clouddienstcontainer zu erstellen und Verwaltungsvorgänge im Zusammenhang mit Clouddiensten auszuführen."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="bscholl" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="06/19/2015"
   ms.author="cawa"/>

# Verwenden des Azure PowerShell-Befehls zum Erstellen eines leeren Clouddienstcontainers
1. Installieren Sie das Microsoft Azure PowerShell-Cmdlet über [Download Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409). Weitere Anweisungen zum Installieren des Azure PowerShell-Cmdlets und zum Herstellen einer Verbindung zu Ihrem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

2. **New-AzureService** ist das Cmdlet, mit dem ein leerer Clouddienstcontainer erstellt wird.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Ein Beispiel zum Aufrufen des Cmdlets: ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Führen Sie Folgendes aus, um weitere Informationen zum Erstellen eines Azure Clouddiensts zu erhalten: ```
Get-help New-AzureService
```

4. Nächste Schritte:

   - Informationen zur Verwaltung der Clouddienstbereitstellung erhalten Sie unter den Befehlen [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) und [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Weitere Informationen erhalten Sie auch unter [Konfigurieren von Cloud Services](cloud-services-how-to-configure.md).

    - Um Ihr Clouddienst-Projekt in Azure zu veröffentlichen, nutzen Sie das Codebeispiel **PublishCloudService.ps1** auf der Seite [Kontinuierliche Zustellung für Cloud Services in Azure](cloud-services-dotnet-continuous-delivery.md).
 

<!---HONumber=Oct15_HO3-->