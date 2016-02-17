<properties 
	pageTitle="Erstellen von Application Insights-Ressourcen mithilfe von PowerShell" 
	description="Erstellen Sie programmgesteuert Application Insights-Ressourcen als Teil des Builds." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/22/2016" 
	ms.author="awills"/>
 
# Erstellen von Application Insights-Ressourcen mithilfe von PowerShell

In diesem Artikel wird beschrieben, wie eine [Application Insights](app-insights-overview.md)-Ressource automatisch in Azure erstellt wird. Dies kann z. B. als Teil eines Buildvorgangs erfolgen. Zusammen mit der grundlegenden Application Insights-Ressource können Sie [Verfügbarkeitswebtests](app-insights-monitor-web-app-availability.md) erstellen, [Warnungen einrichten](app-insights-alerts.md) und andere Azure-Ressourcen erstellen.

Im Wesentlichen werden diese Ressourcen mit JSON-Vorlagen für den [Azure-Ressourcen-Manager](powershell-azure-resource-manager.md) erstellt. Die Vorgehensweise lässt sich wie folgt zusammenfassen: Sie laden die JSON-Definitionen vorhandener Ressourcen herunter, parametrisieren bestimmte Werte, z. B. Namen, und führen dann die Vorlage immer aus, wenn Sie eine neue Ressource erstellen möchten. Sie können mehrere Ressourcen zusammenfassen und in einem Durchgang erstellen, z. B. einen App-Monitor mit Verfügbarkeitstests, Warnungen und Speicher für fortlaufenden Export. Einige Parametrisierungen weisen Besonderheiten auf, die hier erläutert werden.

## Einmalige Konfiguration

Wenn Sie PowerShell noch nicht mit Ihrem Azure-Abonnement verwendet haben:

Installieren Sie das Azure PowerShell-Modul auf dem Computer, auf dem die Skripts ausgeführt werden sollen:

1. Installieren Sie [Microsoft-Webplattform-Installer (Version 5 oder höher)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Installieren Sie hiermit Microsoft Azure PowerShell.

## Kopieren der JSON-Definition für vorhandene Ressourcen

1. Richten Sie [Application Insights](app-insights-overview.md) für ein Projekt ein, das den Projekten ähnelt, die automatisch generiert werden sollen. Fügen Sie bei Bedarf Webtests und Warnungen hinzu.
2. Erstellen Sie eine neue JSON-Datei, in diesem Beispiel die Datei `template1.json`. Kopieren Sie den folgenden Inhalt in die Datei:


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
			"testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    Mit dieser Vorlage wird zusätzlich zur Hauptressource ein Verfügbarkeitstest eingerichtet.


2. Öffnen Sie den [Azure-Ressourcen-Manager](https://resources.azure.com/). Navigieren Sie über Abonnements, Ressourcengruppen und Komponenten nach unten zu Ihrer App-Ressource.

    ![](./media/app-insights-powershell/01.png)

    *Komponenten* sind die grundlegenden Application Insights-Ressourcen zum Anzeigen von Anwendungen. Für die zugeordneten Warnungsregeln und Verfügbarkeitswebtests liegen separate Ressourcen vor.

3. Kopieren Sie die JSON-Definition der Komponente an die entsprechende Stelle in der Datei `template1.json`.
6. Löschen Sie folgende Eigenschaften:
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. Öffnen Sie die Abschnitte „webtests“ und „alertrules“, und kopieren Sie die JSON-Definition für einzelne Elemente in die Vorlage. (Kopieren Sie die Definition nicht aus den Knoten „webtests“ oder „alertrules“, sondern aus den Elementen unter diesen Knoten.)

    Jeder Webtest weist eine zugeordnete Warnungsregel auf, die Sie auch kopieren müssen.

    Der Webtest muss vor der Warnungsregel kopiert werden.

5. Fügen Sie die folgende Zeile in jeder Ressource ein, um die Anforderungen für das Schema zu erfüllen.

    `"apiVersion": "2014-04-01",`

    (Für das Schema wird auch eine Fehlermeldung zur Großschreibung der Ressourcentypnamen angezeigt (`Microsoft.Insights/*`), ändern Sie diese jedoch *nicht*.)


## Parametrisieren der Vorlage

Nun müssen Sie die Namen durch Parameter ersetzen. Zum [Parametrisieren einer Vorlage](resource-group-authoring-templates.md) schreiben Sie Ausdrücke mithilfe einer [Reihe von Hilfsfunktionen](resource-group-template-functions.md).

Sie können nicht einen Teil einer Zeichenfolge parametrisieren. Verwenden Sie daher `concat()` zum Erstellen von Zeichenfolgen.

Es folgen einige Beispiele der Ersetzungen, die Sie vornehmen können. Es gibt mehrere Vorkommen der einzelnen Ersetzungen. In Ihrer Vorlage müssen Sie unter Umständen andere Ersetzungen vornehmen. In diesen Beispielen werden die Parameter und Variablen verwendet, die oben in der Vorlage definiert wurden.

Suchen | Ersetzen durch
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"` (Kleinbuchstaben) | `"[toLower(parameters('appName'))]"`
`"<WebTest Name="myWebTest" ...`<br/>` Url="http://fabrikam.com/home" ...>"`|`[concat('<WebTest Name="',` <br/> `parameters('webTestName'),` <br/> `'" ... Url="', parameters('Url'),` <br/> `'"...>')]" `

## Wenn Ihre App eine Azure-Web-App ist

Fügen Sie diese Ressource hinzu. Oder wenn eine `siteextensions`-Ressource bereits vorhanden ist, parametrisieren Sie sie wie folgt:

```json
    {
      "apiVersion": "2014-04-01",
      "name": "Microsoft.ApplicationInsights.AzureWebSites",
      "type": "siteextensions",
      "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]",
        "[resourceId('Microsoft.Web/Sites/config', parameters('siteName'), 'web')]",
        "[resourceId('Microsoft.Web/sites/sourcecontrols', parameters('siteName'), 'web')]"
      ],
      "properties": { }
    }

```

Diese Ressource stellt das Application Insights SDK für Ihre Azure-Web-App bereit.

## Festlegen von Abhängigkeiten zwischen den Ressourcen

Die Ressourcen sollten in Azure in strikter Reihenfolge eingerichtet werden. Um sicherzustellen, dass eine Einrichtung abgeschlossen ist, bevor die nächste beginnt, fügen Sie Abhängigkeitszeilen hinzu:

* In der Ressource für Webtests:

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* In der Ressource für Warnungen:

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## Erstellen von Application Insights-Ressourcen

1. Melden Sie sich in PowerShell bei Azure an.

    `Login-AzureRmAccount`

2. Führen Sie beispielsweise den folgenden Befehl aus:

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"

    ``` 

    * -ResourceGroupName: die Gruppe, in der Sie die neuen Ressourcen erstellen möchten.
    * -templateFile: muss vor den benutzerdefinierten Parametern angegeben werden.
    * -appName: der Name der zu erstellenden Ressource.
    * -webTestName: der Name des zu erstellenden Webtests.
    * -Url: die URL der Web-App.
    * -text: eine Zeichenfolge, die auf der Webseite angezeigt wird.
    * -siteName: Wird verwendet, wenn es sich um eine Azure-Website handelt


## Definieren von Metrikwarnungen

Es gibt eine spezifische [PowerShell-Methode zum Festlegen von Warnungen](app-insights-alerts.md/#set-alerts-by-using-powershell).


## Beispiel

Es folgt eine vollständige Vorlage für Komponenten, Webtests und Webtestwarnungen:

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name="', 
                parameters('webTestName'), 
              '"  Id="32cfc791-aaad-4b50-9c8d-993c21beb218"   Enabled="True"         CssProjectStructure=""    CssIteration=""  Timeout="120"  WorkItemIds=""         xmlns="http://microsoft.com/schemas/VisualStudio/TeamTest/2010"         Description=""  CredentialUserName=""  CredentialPassword=""         PreAuthenticate="True"  Proxy="default"  StopOnError="False"         RecordedResultFile=""  ResultsLocale="">  <Items>  <Request Method="GET"         Guid="a6f2c90b-61bf-b28hh06gg969"  Version="1.1"  Url="', 
              parameters('Url'), 
              '" ThinkTime="0"  Timeout="300" ParseDependentRequests="True"         FollowRedirects="True" RecordResult="True" Cache="False"         ResponseTimeGoal="0"  Encoding="utf-8"  ExpectedHttpStatusCode="200"         ExpectedResponseUrl="" ReportingName="" IgnoreHttpStatusCode="False" />        </Items>  <ValidationRules> <ValidationRule  Classname="Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" DisplayName="Find Text"         Description="Verifies the existence of the specified text in the response."         Level="High"  ExectuionOrder="BeforeDependents">  <RuleParameters>        <RuleParameter Name="FindText" Value="', 
              parameters('text'), 
              '" />  <RuleParameter Name="IgnoreCase" Value="False" />  <RuleParameter Name="UseRegularExpression" Value="False" />  <RuleParameter Name="PassIfTextFound" Value="True" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

<!---HONumber=AcomDC_0128_2016-->