<properties title="Azure Data Factory - Release Notes" pageTitle="Data Factory - Versionshinweise | Azure" description="Data Factory – Versionshinweise" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Versionshinweise für Azure Data Factory

## Anmerkungen zur Version von Data Factory vom 11.12.2014

### Neue Verbesserungen

- Azure Machine Learning-Integration
	- Mit dieser Version des Azure Data Factory-Diensts können Sie Azure Data Factory mit Azure Machine Learning (ML) mithilfe von **AzureMLLinkedService** und **AzureMLBatchScoringActivity** integrieren. Weitere Informationen finden Sie unter [Erstellen von Vorhersagepipelines mithilfe von Data Factory und Azure Machine Learning][adf-azure-ml]. 
- Der Status der Gatewayversion wird bereitgestellt.
	- Der Status "NewVersionAvailable" wird im Azure Vorschauportal und in der Ausgabe des Cmdlet "Get-AzureDataFactoryGateway" angezeigt, wenn eine neuere Version des Gateways als die derzeit installierte Version verfügbar ist. Sie können dann den Portalanweisungen folgen, um die neue Installationsdatei (MSI) herunterzuladen und auszuführen, um das neueste Gateway zu installieren. Es ist keine zusätzliche Konfiguration erforderlich.

### Änderungen

- "JobsContainer" in "HdInsightOnDemandLinkedService" wird entfernt.
	- In der JSON-Definition für ein "HDInsightOnDemandLinkedService" müssen Sie die **JobsContainer**-Eigenschaft nicht länger angeben. Wenn Sie die Eigenschaft für einen verknüpften, bedarfsgesteuerten Dienst angegeben haben, wird die Eigenschaft ignoriert. Sie können die Eigenschaft aus der JSON-Definition für den verknüpften Dienst entfernen und die Definition des verknüpften Diensts mithilfe des Cmdlet "New-AzureDataFactoryLinkedService" aktualisieren.
- Optionale Konfigurationsparameter für "HDInsightOnDemandLinkedService"
	- Diese Version führt die Unterstützung für einige optionale Konfigurationsparameter für "HDInsightOnDemandLinked" (bedarfsgesteuerter HDInsight-Cluster) ein. Weitere Informationen finden Sie unter [ClusterCreateParameters-Eigenschaften][on-demand-hdi-parameters].
- Speicherort für Gateway wird entfernt.
	- Wenn Sie ein Azure Data Factory-Gateway über das Portal oder PowerShell (New-AzureDataFactoryGateway) erstellen, müssen Sie den Speicherort für das Gateway nicht länger angeben. Die Region für die Data Factory wird übernommen. Ebenso wird zum Konfigurieren eines verknüpften SQL Server-Diensts mithilfe von JSON die Eigenschaft "gatewayLocation" nicht länger benötigt. Das Data Factory .NET SDK wird ebenfalls aktualisiert, um diese Änderungen widerzuspiegeln.
	- Wenn Sie eine ältere Version des SDK und von Azure PowerShell verwenden, müssen Sie weiterhin die Standorteinstellung bereitstellen.
 
     

#### Wichtige Änderungen
	
- CustomActivity zu DotNetActivity
	- Die **ICustomActivity**-Schnittstelle wurde in **IDotNetActivity** umbenannt. Sie müssen Data Factory NuGet-Pakete aktualisieren und "ICustomActivity" im Quellcode für Ihre benutzerdefinierte Aktivität in "IDotNetActivity" ändern.  
	- Der Typ der benutzerdefinierten Aktivität in der JSON-Definition für die benutzerdefinierte Aktivität muss von **CustomActivity** in **DotNetActivity** geändert werden. 
	- Die Klassen **CustomActivity** und **CustomActivityProperties** wurden mit dem gleichen Eigenschaftensatz in **DotNetActivity** und **DotNetActivityProperties** umbenannt.

		Wenn Sie die ältere Version des SDK und von Azure PowerShell verwenden, können Sie weiterhin "CustomActivity" statt "DotNetActivity" verwenden.
    
  		Eine exemplarische Vorgehensweise zum Erstellen einer benutzerdefinierten Aktivität und zu deren Verwendung in einer Azure Data Factory-Pipeline finden Sie unter [Verwenden benutzerdefinierter Aktivitäten in einer Azure Data Factory-Pipeline][adf-custom-activities].  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->
