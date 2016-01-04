<properties 
	pageTitle="Aktivieren der Diagnose mithilfe von PowerShell in Azure Cloud Services | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die Diagnose mithilfe von PowerShell in Clouddiensten aktivieren." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# Aktivieren der Diagnose mithilfe von PowerShell in Azure Cloud Services

Mit der Azure-Diagnoseerweiterung können Sie Diagnosedaten wie Anwendungsprotokolle, Leistungsindikatoren usw. von einem Clouddienst sammeln. In diesem Artikel wird beschrieben, wie Sie die Azure-Diagnoseerweiterung für einen Clouddienst mithilfe von PowerShell aktivieren. Informationen zu den erforderlichen Komponenten für diesen Artikel finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

## Aktivieren der Diagnoseerweiterung bei der Bereitstellung eines Clouddiensts

Dieser Ansatz eignet sich besonders für Szenarios mit fortlaufender Integration, in denen die Diagnoseerweiterung aktiviert werden kann. Sie können die Diagnoseerweiterung als Teil der Bereitstellung des Clouddiensts aktivieren, indem Sie den *ExtensionConfiguration*-Parameter an das Cmdlet [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) übergeben. Der *ExtensionConfiguration*-Parameter akzeptiert ein Array von Diagnosekonfigurationen, die Sie mit dem Cmdlet [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) erstellen können.

Das folgende Beispiel zeigt, wie Sie die Diagnose für einen Clouddienst mit einer Webrolle und einer Workerrolle mit jeweils unterschiedlicher Diagnosekonfiguration aktivieren können.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## Aktivieren der Diagnoseerweiterung für einen vorhandenen Clouddienst

Mit dem Cmdlet [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) können Sie die Diagnose für einen Clouddienst aktivieren, der bereits ausgeführt wird.


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## Abrufen der aktuellen Konfiguration der Diagnoseerweiterung
Mit dem Cmdlet [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) können Sie die aktuelle Diagnosekonfiguration für einen Clouddienst abrufen.
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## Entfernen der Diagnoseerweiterung
Sie können zum Deaktivieren der Diagnose für einen Clouddienst das Cmdlet [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) verwenden.

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Wenn Sie die Diagnoseerweiterung entweder mit *Set-AzureServiceDiagnosticsExtension* oder mit *New-AzureServiceDiagnosticsExtensionConfig* ohne den *Role*-Parameter aktiviert haben, können Sie die Erweiterung mit *Remove-AzureServiceDiagnosticsExtension* ohne den *Role*-Parameter entfernen. Wenn beim Aktivieren der Erweiterung der *Role*-Parameter verwendet wurde, muss er auch zum Entfernen der Erweiterung verwendet werden.

So entfernen Sie die Diagnoseerweiterung aus jeder einzelnen Rolle

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## Nächste Schritte

- Zusätzliche Anleitungen zur Verwendung von Azure-Diagnose und anderen Techniken zur Problembehandlung finden Sie unter [Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines](cloud-services-dotnet-diagnostics.md).
- Das [Diagnosekonfigurationsschema](https://msdn.microsoft.com/library/azure/dn782207.aspx) erläutert die verschiedenen Optionen der XML-Konfigurationen für die Diagnoseerweiterung.
- Weitere Informationen zum Aktivieren der Diagnoseerweiterung für virtuelle Computer finden Sie unter [Erstellen eines virtuellen Windows-Computers mit Überwachung und Diagnose mithilfe von Azure-Ressourcen-Manager-Vorlagen](virtual-machines-extensions-diagnostics-windows-template.md).  

<!---HONumber=Nov15_HO4-->