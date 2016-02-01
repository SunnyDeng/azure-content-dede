<properties pageTitle="Aktivieren der Azure-Diagnose auf einer virtuellen Azure-Maschine unter Windows mithilfe von PowerShell | Microsoft Azure" services="virtual-machines" documentationCenter="" description="Hier erfahren Sie, wie Sie mithilfe von PowerShell die Azure-Diagnose auf einer virtuellen Maschine unter Windows aktivieren." authors="sbtron" manager="" editor="""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="saurabh"/>


# Aktivieren der Azure-Diagnose auf einer virtuellen Azure-Maschine unter Windows mithilfe von PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Die Azure-Diagnose ist eine Funktion in Azure, mit der Diagnosedaten für eine bereitgestellte Anwendung erfasst werden können. Mithilfe der Diagnoseerweiterung können Sie Diagnosedaten wie Anwendungsprotokolle oder Leistungsindikatoren von einer virtuellen Azure-Maschine (VM) unter Windows sammeln. In diesem Artikel wird beschrieben, wie Sie die Diagnoseerweiterung für eine VM mithilfe von Windows PowerShell aktivieren. Informationen zu den erforderlichen Komponenten für diesen Artikel finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

## Aktivieren der Diagnoseerweiterung bei Verwendung des Ressourcen-Manager-Bereitstellungsmodells

Die Diagnoseerweiterung kann im Zuge der Erstellung einer Windows-VM mit dem Azure-Ressourcen-Manager-Bereitstellungsmodell durch Hinzufügen der Erweiterungskonfiguration zur Ressourcen-Manager-Vorlage aktiviert werden. Weitere Informationen finden Sie unter [Erstellen einer virtuellen Windows-Maschine mit Überwachung und Diagnose mithilfe von Azure-Ressourcen-Manager-Vorlagen](virtual-machines-extensions-diagnostics-windows-template.md).

Wenn Sie die Diagnoseerweiterung auf einer bereits vorhandenen VM aktivieren möchten, die mithilfe des Ressourcen-Manager-Bereitstellungsmodells erstellt wurde, können Sie wie weiter unten gezeigt das PowerShell-Cmdlet [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) verwenden.


	$vm_resourcegroup = "myvmresourcegroup"
	$vm_name = "myvm"
	$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig\_path* ist der Pfad zu der Datei mit der XML-Diagnosekonfiguration, wie im [Beispiel](#sample-diagnostics-configuration) weiter unten beschrieben.

Falls in der Diagnosekonfigurationsdatei ein Element vom Typ **StorageAccount** mit einem Speicherkontonamen angegeben ist, konfiguriert das Skript *Set-AzureRMVMDiagnosticsExtension* die Diagnoseerweiterung automatisch so, dass die Diagnosedaten an dieses Speicherkonto gesendet werden. Dazu muss das Speicherkonto allerdings dem gleichen Abonnement angehören wie die VM.

Wurde in der Diagnosekonfiguration kein Element vom Typ **StorageAccount** angegeben, müssen Sie den Parameter *StorageAccountName* an das Cmdlet übergeben. Bei Angabe des Parameters *StorageAccountName* verwendet das Cmdlet immer das im Parameter angegebene Speicherkonto und nicht die Angabe aus der Diagnosekonfigurationsdatei.

Wenn das Diagnosespeicherkonto einem anderen Abonnement angehört als die VM, müssen explizit die Parameter *StorageAccountName* und *StorageAccountKey* an das Cmdlet übergeben werden. Der Parameter *StorageAccountKey* ist nicht erforderlich, wenn das Diagnosespeicherkonto dem gleichen Abonnement angehört, da das Cmdlet beim Aktivieren der Diagnoseerweiterung den Schlüsselwert automatisch abfragen und festlegen kann. Gehört das Diagnosespeicherkonto dagegen einem anderen Abonnement an, kann das Cmdlet den Schlüssel unter Umständen nicht automatisch abrufen, weshalb er in diesem Fall explizit über den Parameter *StorageAccountKey* angegeben werden muss.

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Nachdem die Diagnoseerweiterung auf einer VM aktiviert wurde, können die aktuellen Einstellungen mithilfe des Cmdlets [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) abgerufen werden.

	Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Das Cmdlet gibt das Element *PublicSettings*, das die XML-Konfiguration in einem Base64-codierten Format enthält. Zum Lesen des XML-Codes muss dieser zunächst decodiert werden.

	$publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
	$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
	$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
	Write-Host $xmlconfig

Mit dem Cmdlet [Remove-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) können Sie die Diagnoseerweiterung von einer VM entfernen.

## Aktivieren der Diagnoseerweiterung bei Verwendung des klassischen Bereitstellungsmodells

Mit dem Cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) können Sie eine Diagnoseerweiterung auf einer VM aktivieren, die mit dem klassischen Bereitstellungsmodell erstellt wurde. Das folgende Beispiel zeigt, wie Sie mit dem klassischen Bereitstellungsmodell eine neue VM mit aktivierter Diagnoseerweiterung erstellen.

	$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
	$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
	$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Wenn Sie die Diagnoseerweiterung auf einer bereits vorhandenen VM aktivieren möchten, die mit dem klassischen Bereitstellungsmodell erstellt wurde, rufen Sie zunächst die VM-Konfiguration mit dem Cmdlet [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) ab. Aktualisieren Sie anschließend mithilfe des Cmdlets [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) die VM-Konfiguration, sodass diese die Diagnoseerweiterung beinhaltet. Wenden Sie abschließend mithilfe von [Update-AzureVM](https://msdn.microsoft.com/library/mt589121.aspx) die aktualisierte Konfiguration auf die VM an.

	$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
	$VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## Beispielhafte Diagnosekonfiguration

Der folgende XML-Code kann für die öffentliche Konfiguration der Diagnose mit den obigen Skripts verwendet werden. Diese Beispielkonfiguration überträgt neben verschiedenen Leistungsindikatoren auch Fehler aus dem Anwendungs-, Sicherheits- und Systemkanal der Windows-Ereignisprotokolle sowie sämtliche Fehler aus den Diagnoseinfrastrukturprotokollen an das Diagnosespeicherkonto.

Die Konfiguration muss mit Folgendem aktualisiert werden:

- Das Attribut *resourceID* des Elements **Metrics** muss mit der Ressourcen-ID für die VM aktualisiert werden.
	- Die Ressourcen-ID kann nach folgendem Muster erstellt werden: /subscriptions/{*Abonnement-ID für das Abonnement mit der VM*}/resourceGroups/{*Ressourcengruppenname für die VM*}/providers/Microsoft.Compute/virtualMachines/{*VM-Name*}.
	- Wenn die Abonnement-ID für das Abonnement mit der VM also beispielsweise **11111111-1111-1111-1111-111111111111**, der Ressourcengruppenname **MyResourceGroup** und der VM-Name **MyWindowsVM** lautet, sieht der Wert für *resourceID* wie folgt aus:

		```
		<Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
		```

	- Weitere Informationen zur Generierung von Metriken auf der Grundlage von Leistungsindikatoren und Metriken finden Sie in der [Metriktabelle für Azure-Diagnose im Speicher](virtual-machines-extensions-diagnostics-windows-template.md#wadmetrics-tables-in-storage).

- Das Element **StorageAccount** muss mit dem Namen des Diagnosespeicherkontos aktualisiert werden.

	```
	<?xml version="1.0" encoding="utf-8"?>
	<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
	        <PerformanceCounters scheduledTransferPeriod="PT1M">
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU utilization" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU privileged time" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU user time" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
	        <annotation displayName="CPU frequency" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Processes" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Threads" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Handles" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Memory usage" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory available" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory committed" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory commit limit" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory paged pool" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory non-paged pool" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active time" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active read time" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active write time" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk operations" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk read operations" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk write operations" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk speed" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk read speed" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk write speed" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average queue length" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average read queue length" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average write queue length" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk free space (percentage)" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk free space (MB)" locale="de-DE"/>
	      </PerformanceCounterConfiguration>
	    </PerformanceCounters>
		<Metrics resourceId="(Update with resource ID for the VM)" >
	        <MetricAggregation scheduledTransferPeriod="PT1H"/>
	        <MetricAggregation scheduledTransferPeriod="PT1M"/>
	    </Metrics>
	    <WindowsEventLog scheduledTransferPeriod="PT1M">
	      <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
	      <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
	      <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
	    </WindowsEventLog>
	      </DiagnosticMonitorConfiguration>
	    </WadCfg>
	    <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
	</PublicConfig>
	```

## Nächste Schritte
- Weitere Anleitungen zur Verwendung von Azure-Diagnosefunktionen und anderen Techniken zur Problembehandlung finden Sie unter [Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines](cloud-services-dotnet-diagnostics.md).
- Im [Diagnosekonfigurationsschema](https://msdn.microsoft.com/library/azure/mt634524.aspx) werden die verschiedenen Optionen der XML-Konfigurationen für die Diagnoseerweiterung erläutert.

<!---HONumber=AcomDC_0121_2016-->