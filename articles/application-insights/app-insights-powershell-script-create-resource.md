<properties 
	pageTitle="PowerShell-Skript zum Erstellen einer Application Insights-Ressource" 
	description="Automatisierung der Erstellung von Application Insights-Ressourcen." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="awills"/>

#  PowerShell-Skript zum Erstellen einer Application Insights-Ressource

*Application Insights befindet sich in der Vorschau.*

Wenn Sie eine neue Anwendung – oder eine neue Version einer Anwendung – mit [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/) überwachen möchten, richten Sie eine neue Ressource in Microsoft Azure ein. In dieser Ressource werden die Telemetriedaten aus Ihrer App analysiert und angezeigt.

Sie können die Erstellung einer neuen Ressource mithilfe von PowerShell automatisieren.

Wenn Sie beispielsweise eine App für mobile Geräte entwickeln, ist es wahrscheinlich, dass irgendwann mehrere veröffentlichte Versionen Ihrer App von Ihren Kunden verwendet werden. Sie möchten vermeiden, dass die Telemetrieergebnisse aus verschiedenen Versionen durcheinander geraten. Daher richten Sie den Buildprozess so ein, dass für jeden Build eine neue Ressource erstellt wird.

## Skript zum Erstellen einer Application Insights-Ressource

*Ausgabe*

* App Insights Name = erimattestapp
* IKey = 00000000-0000-0000-0000-000000000000

*PowerShell-Skript*

```PowerShell

cls


##################################################################
# Set Values
##################################################################

#If running manually, comment this out before the first execution to login to the Azure Portal
#Add-AzureAccount

#Set the name of the Application Insights Resource
$appInsightsName = "TestApp"

#Set the application name used for the value of the Tag "AppInsightsApp" - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

#Set the name of the Resource Group to use.  By default will use the application name as a starter
$resourceGroupName = "MyAppResourceGroup"

##################################################################
# Create the Resource and Output the name and iKey
##################################################################
#Set the script to Resource Manager - http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/
Switch-AzureMode AzureResourceManager

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

#Create the App Insights Resource
$resource = New-AzureResource -Name $appInsightsName -ResourceGroupName $resourceGroupName -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} -ResourceType "Microsoft.Insights/Components" -Location "Central US" -ApiVersion "2014-08-01" -PropertyObject @{"Type"="ASP.NET"} -Force 

#Give team owner access - http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/
New-AzureRoleAssignment -Mail "myTeam@fabrikam.com" -RoleDefinitionName Owner -Scope $resource.ResourceId | Out-Null

#Display iKey
Write-Host "App Insights Name = " $resource.Properties["Name"]
Write-Host "IKey = " $resource.Properties["InstrumentationKey"]

```

## Zweck des "iKey"

Jede Ressource wird durch ihren Instrumentationsschlüssel (iKey) identifiziert. Der iKey ist eine Ausgabe des Skripts zum Erstellen der Ressource. Ihr Buildskript sollte den iKey dem Application Insights-SDK bereitstellen, das in Ihrer App eingebettet ist.

Es gibt zwei Möglichkeiten, den iKey dem SDK zur Verfügung zu stellen:
  
* In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Oder im [Initialisierungscode](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`





 

<!---HONumber=July15_HO4-->