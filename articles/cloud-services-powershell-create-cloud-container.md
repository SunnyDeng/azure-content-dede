<properties 
   pageTitle="Verwenden des Azure PowerShell-Befehls zum Erstellen eines leeren Cloud-Dienst-Containers" 
   description="In diesem Artikel wird erläutert, wie Sie PowerShell-Skripts verwenden, um Cloud-Dienst-Container zu erstellen und Verwaltungsvorgänge im Zusammenhang mit Cloud-Diensten auszuführen" 
   services="cloud-services" 
   documentationCenter=".net" 
   authors="cawaMS" 
   manager="" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="cawa"/>

# Verwenden des Azure PowerShell-Befehls zum Erstellen eines leeren Cloud-Dienst-Containers
1. Installieren Sie das Microsoft Azure PowerShell-Cmdlet: [Herunterladen von Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409). Weitere Anweisungen zum Installieren des Azure PowerShell-Cmdlets und Herstellen einer Verbindung zu Ihrem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md).

2. **New-AzureService** ist das Cmdlet, mit dem ein leerer Cloud-Dienst-Container erstellt wird. 

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>] 
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Ein Beispiel zum Aufrufen des Cmdlets: 
```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Um weitere Informationen zum Erstellen eines Azure Cloud-Diensts zu erhalten, führen Sie diesen Befehl aus: 
```
Get-help New-AzureService
```

4. Nächste Schritte:

   - Informationen zur Verwaltung der Cloud-Dienst-Bereitstellung erhalten Sie unter den Befehlen [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) und [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Informationen hierzu erhalten Sie auch unter Also [Konfigurieren von Cloud-Diensten](cloud-services-how-to-configure.md)

    - Um Ihre Cloud-Dienst-Projekt in Azure zu veröffentlichen, nutzen Sie das Codebeispiel **PublishCloudService.ps1** auf der Seite [Kontinuierliche Zustellung für Cloud Services in Azure](cloud-services-dotnet-continuous-delivery.md)


    



<!--HONumber=52-->