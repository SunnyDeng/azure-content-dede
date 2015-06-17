<properties 
	pageTitle="PowerShell-Skript zum Erstellen einer Ressource Application Insights" 
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
	ms.date="06/12/2015" 
	ms.author="awills"/>

#  PowerShell-Skript zum Erstellen einer Ressource Application Insights

*Application Insights befindet sich in der Vorschau.*

Wenn eine neue Anwendung – oder eine neue Version einer Anwendung – überwachen möchten mit [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/), richten Sie eine neue Ressource in Microsoft Azure. Diese Ressource wird die Telemetriedaten aus Ihrer app analysiert und angezeigt werden.

Sie können die Erstellung einer neuen Ressource mithilfe von PowerShell automatisieren.

Beispielsweise, wenn Sie eine app für mobile Geräte entwickeln, ist es wahrscheinlich, dass zu einem beliebigen Zeitpunkt werden mehrere veröffentlichte Versionen Ihrer App von Ihren Kunden verwendet wird. Sie möchten die Telemetrie Ergebnisse aus verschiedenen Versionen durcheinander zu erhalten. So erhalten Sie den Buildprozess eine neue Ressource für jeden Build zu erstellen.

## Skript zum Erstellen einer Ressource Application Insights

*Ausgabe*

* App Insights Name = Erimattestapp
* IKey 00000000-0000-0000-0000-000000000000 =

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
$resource = New-AzureResource -Name $appInsightsName -ResourceGroupName $resourceGroupName -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} -ResourceType "Microsoft.Insights/Components" -Location "Central US" -ApiVersion "2014-08-01"

#Give team owner access - http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/
New-AzureRoleAssignment -Mail "myTeam@fabrikam.com" -RoleDefinitionName Owner -Scope $resource.ResourceId | Out-Null

#Display iKey
Write-Host "App Insights Name = " $resource.Properties["Name"]
Write-Host "IKey = " $resource.Properties["InstrumentationKey"]

```

## Was soll mit den iKey

Jede Ressource wird durch seinen instrumentationsschlüssel (iKey) identifiziert. Der iKey ist eine Ausgabe des Skripts zum Erstellen der Ressource. Buildskript stellen sollten der iKey an Application Insights-SDK in der app eingebettet.

Es gibt zwei Möglichkeiten, den iKey SDK zur Verfügung:
  
* In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`* Ikey *`</instrumentationkey>`
* Oder im [Initialisierungscode](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`* iKey *`";`





 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->