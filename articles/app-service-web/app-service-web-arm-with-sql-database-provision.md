<properties 
	pageTitle="Bereitstellen einer Web-App, die eine SQL-Datenbank verwendet" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine Web-App bereitzustellen, die eine SQL-Datenbank enthält." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tomfitz"/>

# Bereitstellen einer Web-App mit einer SQL-Datenbank

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die eine Web-App und eine SQL-Datenbank bereitstellt. Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Weitere Informationen zum Bereitstellen von Apps finden Sie unter [Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](app-service-deploy-complex-application-predictably.md).

Die vollständige Vorlage finden Sie unter [Web-App mit SQL-Datenbankvorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json).

## Was Sie bereitstellen

In dieser Vorlage stellen Sie Folgendes bereit:

- eine Web-App
- SQL-Datenbankserver
- SQL-Datenbank
- Einstellungen für automatische Skalierung
- Warnungsregeln
- App Insights

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen:

[![Bereitstellen in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-sql-database%2Fazuredeploy.json)

## Anzugebende Parameter

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### serverName

Der Name des zu erstellenden neuen Datenbankservers.

    "serverName": {
      "type": "string"
    }

### serverLocation

Der Speicherort des Datenbankservers. Für eine optimale Leistung sollte dieser Speicherort mit dem der Web-App identisch sein.

    "serverLocation": {
      "type": "string"
    }

### administratorLogin

Der Kontoname für den Datenbankserveradministrator.

    "administratorLogin": {
      "type": "string"
    }

### administratorLoginPassword

Das Kennwort für den Datenbankserveradministrator.

    "administratorLoginPassword": {
      "type": "securestring"
    }

### databaseName

Der Name der zu erstellenden neuen Datenbank.

    "databaseName": {
      "type": "string"
    }

### collation

Die Datenbanksortierung zum Steuern der ordnungsgemäßen Verwendung von Zeichen.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

### Edition

Der Typ der zu erstellenden Datenbank.

    "edition": {
      "type": "string",
      "defaultValue": "Web"
    }

### maxSizeBytes

Die maximale Größe \(in Bytes\) der Datenbank.

    "maxSizeBytes": {
      "type": "string",
      "defaultValue": "1073741824"
    }

### requestedServiceObjectiveId

Die GUID entsprechend der Leistungsstufe der Edition. Eine Liste der verfügbaren Werte finden Sie unter [Erstellen einer Datenbank](https://msdn.microsoft.com/library/azure/dn505701.aspx). Der Standardwert entspricht der Leistungsstufe "Web".

    "requestedServiceObjectiveId": {
        "type": "string",
        "defaultValue": "910b4fcb-8a29-4c3e-958f-f7ba794388b2"
    }


## Bereitzustellende Ressourcen

### SQL-Server und Datenbank

Erstellt einen neuen SQL-Server und eine Datenbank. Der Name des Servers wird im **serverName**-Parameter und der Speicherort im **serverLocation**-Parameter angegeben. Wenn Sie den neuen Server erstellen, müssen Sie einen Benutzernamen und ein Kennwort für den Datenbankserveradministrator bereitstellen.

    {
      "name": "[parameters('serverName')]",
      "type": "Microsoft.Sql/servers",
      "location": "[parameters('serverLocation')]",
      "apiVersion": "2.0",
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
      },
      "resources": [
        {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "[parameters('edition')]",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "[parameters('maxSizeBytes')]",
            "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
          }
        },
        {
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "location": "[parameters('serverLocation')]",
          "name": "AllowAllWindowsAzureIps",
          "properties": {
            "endIpAddress": "0.0.0.0",
            "startIpAddress": "0.0.0.0"
          },
          "type": "firewallrules"
        }
      ]
    },


[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]


### Web-App

    {
      "apiVersion": "2015-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": ["[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "empty"
      },
      "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2014-11-01",
          "type": "config",
          "name": "connectionstrings",
          "dependsOn": [ "[concat('Microsoft.Web/Sites/', parameters('siteName'))]" ],
          "properties": {
              "DefaultConnection":{
              "value":"[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
              "type": 2 
            },
          }
        }
      ]
    }

### AutoScale

    {
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
      "type": "microsoft.insights/autoscalesettings",
      "location": "East US",
      "tags": {"[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"},
      "dependsOn": ["[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"],
      "properties": {
        "profiles": [
        {
          "name": "Default",
          "capacity": {
            "minimum": "1",
            "maximum": "2",
            "default": "1"
            },
            "rules": [
            {
              "metricTrigger": 
              {
                "metricName": "CpuPercentage",
                "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 80.0
              },
              "scaleAction": 
              {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT10M"
              }
            },
            {
              "metricTrigger": 
              {
                "metricName": "CpuPercentage",
                "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT1H",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 60.0
              },
              "scaleAction": 
              {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT1H"
              }
            }
            ]
          }
          ],
          "enabled": false,
          "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
          "targetResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        }
    }

### Warnregeln für Statuscodes 403 und 500er, hohe CPU-Auslastung und HTTP-Warteschlangenlänge 

    {
      "apiVersion": "2014-04-01",
      "name": "[concat('ServerErrors ', parameters('siteName'))]",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": ["[concat('Microsoft.Web/sites/', parameters('siteName'))]"],
      "tags": {"[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"},
      "properties": 
      {
        "name": "[concat('ServerErrors ', parameters('siteName'))]",
        "description": "[concat(parameters('siteName'), ' has some server errors, status code 5xx.')]",
        "isEnabled": false,
        "condition": 
        {
          "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
          "dataSource": 
          {
            "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
            "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
            "metricName": "Http5xx"
          },
          "operator": "GreaterThan",
          "threshold": 0.0,
          "windowSize": "PT5M"
        },
        "action": 
        {
          "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": []
        }
      }
    },
    //Alert-Rules --> 403
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
            "description": "[concat(parameters('siteName'), ' has some requests that are forbidden, status code 403.')]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
                    "metricName": "Http403"
                },
                "operator": "GreaterThan",
                "threshold": 0,
                "windowSize": "PT5M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    },
    //Alert-Rules --> High CPU
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
            "description": "[concat('The average CPU is high across all the instances of ', parameters('hostingPlanName'))]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                    "metricName": "CpuPercentage"
                },
                "operator": "GreaterThan",
                "threshold": 90,
                "windowSize": "PT15M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    },
    //Alert-Rules --> HTTP Queue Length
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
            "description": "[concat('The HTTP queue for the instances of ', parameters('hostingPlanName'), ' has a large number of pending requests.')]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                    "metricName": "HttpQueueLength"
                },
                "operator": "GreaterThan",
                "threshold": 100.0,
                "windowSize": "PT5M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    }

### App Insights

    {
        "apiVersion": "2014-04-01",
        "name": "[parameters('siteName')]",
        "type": "microsoft.insights/components",
        "location": "Central US",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
        },
        "properties": {
            "ApplicationId": "[parameters('siteName')]"
        }
    }

## Befehle zum Ausführen der Bereitstellung

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json


 

<!---HONumber=July15_HO5-->