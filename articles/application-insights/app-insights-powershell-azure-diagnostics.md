<properties
    pageTitle="Senden von Azure-Diagnosedaten an Application Insights mit PowerShell | Microsoft Azure"
    description="Automatisieren der Konfiguration der Azure-Diagnose zum Senden von Daten an Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Senden von Azure-Diagnosedaten an Application Insights mit PowerShell

[Microsoft Azure](https://azure.com) kann zum [Senden von Azure-Diagnosedaten](app-insights-azure-diagnostics.md) an [Visual Studio Application Insights](app-insights-overview.md) konfiguriert werden. Die Diagnosedaten beziehen sich auf Azure Cloud Services und Azure VMs. Sie ergänzen die Telemetriedaten, die Sie mit dem Application Insights SDK aus der App senden. Als Teil der Automatisierung des Prozesses zum Erstellen neuer Ressourcen in Azure können Sie die Diagnose mit PowerShell konfigurieren.

## Aktivieren der Diagnoseerweiterung bei der Bereitstellung eines Clouddiensts

Das Cmdlet `New-AzureDeployment` hat den `ExtensionConfiguration`-Parameter, der ein Array von Diagnosekonfigurationen verwendet. Diese können mit dem Cmdlet `New-AzureServiceDiagnosticsExtensionConfig` erstellt werden. Beispiel:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## Aktivieren der Diagnoseerweiterung für einen vorhandenen Clouddienst

Verwenden Sie bei einem vorhandenen Dienst `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## Abrufen der aktuellen Konfiguration der Diagnoseerweiterung

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## Entfernen der Diagnoseerweiterung

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Wenn Sie die Diagnoseerweiterung entweder mit `Set-AzureServiceDiagnosticsExtension` oder `New-AzureServiceDiagnosticsExtensionConfig` ohne den „Role“-Parameter aktiviert haben, können Sie die Erweiterung mit `Remove-AzureServiceDiagnosticsExtension` ohne den „Role“-Parameter entfernen. Wenn beim Aktivieren der Erweiterung der „Role“-Parameter verwendet wurde, muss er auch zum Entfernen der Erweiterung verwendet werden.

So entfernen Sie die Diagnoseerweiterung aus jeder einzelnen Rolle

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Verwandte Themen

* [Überwachen von Azure Cloud Services-Apps mit Application Insights](app-insights-cloudservices.md)
* [Senden von Azure-Diagnosedaten an Application Insights](app-insights-azure-diagnostics.md)

<!---HONumber=AcomDC_1125_2015-->