<properties
   pageTitle="Erstellen von Azure-Ressourcen-Manager-Vorlagen"
   description="Erstellen Sie Azure-Ressourcen-Manager-Vorlagen mithilfe von deklarativer JSON-Syntax, um Anwendungen für Azure bereitzustellen."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Erstellen von Azure-Ressourcen-Manager-Vorlagen

Azure-Anwendungen erfordern i. d. R. eine Kombination von Ressourcen (z. B. Datenbankserver, Datenbanken oder Websites), um die gewünschten Ziele zu erreichen. Anstatt jede Ressource gesondert bereitzustellen und zu verwalten, können Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die für die Bereitstellung aller Ressourcen für Ihre Anwendung in einem einzigen koordinierten Vorgang sorgt. In der Vorlage müssen Sie die Ressourcen definieren, die für die Anwendung erforderlich sind, und Bereitstellungsparameter für die Eingabe von Werten für unterschiedliche Umgebungen angeben. Die Vorlage besteht aus JSON und Ausdrücken, mit denen Sie Werte für die Bereitstellung erstellen können.

In diesem Thema werden die Abschnitte der Vorlage beschrieben. Die tatsächlichen Schemas finden Sie unter [Schemas des Azure-Ressourcen-Manager](https://github.com/Azure/azure-resource-manager-schemas).

## Vorlagenformat

Das folgende Beispiel zeigt die Abschnitte, die die grundlegende Struktur einer Vorlage bilden.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Elementname | Erforderlich | Beschreibung
| :------------: | :------: | :----------
| $schema | Ja | Speicherort der JSON-Schemadatei, die die Version der Vorlagensprache beschreibt.
| contentVersion | Ja | Version der Vorlage (z. B. 1.0.0.0). Bei der Bereitstellung von Ressourcen mithilfe der Vorlage kann mit diesem Wert sichergestellt werden, dass die richtige Vorlage verwendet wird.
| parameters | Nein | Werte, die bei der Bereitstellung angegeben werden, um die Bereitstellung der Ressourcen anpassen.
| Variablen | Nein | Werte, die als JSON-Fragmente in der Vorlage verwendet werden, um Vorlagensprachausdrücke zu vereinfachen.
| angeben | Ja | Arten von Diensten, die in einer Ressourcengruppe bereitgestellt oder aktualisiert werden.
| outputs | Nein | Werte, die nach der Bereitstellung zurückgegeben werden.

Wir werden die Abschnitte der Vorlage weiter unten in diesem Thema ausführlich untersuchen. Jetzt prüfen wir den Teil der Syntax, aus dem sich die Vorlage zusammensetzt.

## Ausdrücke und Funktionen

Die grundlegende Syntax der Vorlage ist JSON. Allerdings erweitern Ausdrücke und Funktionen die JSON-Anfragen, die in der Vorlage verfügbar sind, und ermöglichen Ihnen das Erstellen von Werten, die keine strikten Literalwerte sind. Ausdrücke werden in Klammern eingeschlossen ([ und ]) und bei der Bereitstellung der Vorlage ausgewertet. Ausdrücke können an beliebiger Stelle in einem JSON-Zeichenfolgenwert auftreten und geben immer einen anderen JSON-Wert zurück. Wenn Sie ein Zeichenfolgenliteral verwenden müssen, das mit einer eckigen Klammer [ beginnt, müssen Sie zwei eckige Klammern verwenden [[.

In der Regel verwenden Sie Ausdrücke mit Funktionen, um Vorgänge zum Konfigurieren der Bereitstellung durchzuführen. Genau wie in JavaScript haben Funktionsaufrufe das Format **functionName(arg1,arg2,arg3)**. Auf Eigenschaften verweisen Sie mithilfe der Operatoren Punkt und [Index].

Die folgende Liste zeigt häufig verwendete Funktionen.

- **parameters(parameterName)**

    Gibt bei Ausführung der Bereitstellung einen Parameterwert zurück.

- **variables(variableName)**

    Gibt eine Variable zurück, die in der Vorlage definiert ist.

- **concat(arg1,arg2,arg3,...)**

    Kombiniert mehrere Zeichenfolgenwerte. Diese Funktion kann eine beliebige Anzahl an Argumenten entgegennehmen.

- **base64(inputString)**

    Rückkehr zur base64-Darstellung der Eingabezeichenfolge.

- **resourceGroup()**

    Gibt ein strukturiertes Objekt (mit Eigenschaften für ID, Name und Speicherort) zurück, das die aktuellen Ressourcengruppe darstellt.

- **resourceId([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

    Gibt den eindeutigen Bezeichner einer Ressource zurück. Kann zum Abrufen von Ressourcen aus einer anderen Ressourcengruppe verwendet werden.

Das folgende Beispiel zeigt, wie Sie einige der Funktionen beim Erstellen von Werten verwenden:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Sie wissen jetzt genug über Ausdrücke und Funktionen, um die Abschnitte der Vorlage zu verstehen. Ausführlichere Informationen zu sämtlichen Vorlagenfunktionen – einschließlich von Parametern und des Formats für zurückgegebene Werte – finden Sie unter [Vorlagenfunktionen im Azure-Ressourcen-Manager](./resource-group-template-functions.md).


## Parameter

Im Parameterabschnitt der Vorlage geben Sie an, welche Werte ein Benutzer beim Bereitstellen der Ressourcen eingeben kann. Sie können diese Parameterwerte in der Vorlage zum Festlegen von Werten für die bereitgestellten Ressourcen verwenden. Nur die im Parameterabschnitt deklarierten Parameter können in anderen Abschnitten der Vorlage verwendet werden.

Im Parameterabschnitt können Sie mit Parameterwerten keine anderen Parameterwerte erstellen. Diese Art von Vorgang erfolgt i. d. R. im Variablenabschnitt.

Sie definieren Parameter mit der folgenden Struktur:

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ]
       }
    }

| Elementname | Erforderlich | Beschreibung
| :------------: | :------: | :----------
| parameterName | Ja | Der Name des Parameters. Es muss sich um einen gültigen JavaScript-Bezeichner handeln.
| Typ | Ja | Der Typ des Parameterwerts. Die nachstehende Liste zeigt die zulässigen Typen.
| defaultValue | Nein | Der Standardwert für den Parameter, wenn kein Wert für den Parameter angegeben wird.
| allowedValues | Nein | Ein Array der zulässigen Werte für den Parameter, um sicherzustellen, dass der richtige Wert angegeben wird.

Die zulässigen Typen und Werte lauten folgendermaßen:

- "string" oder "secureString" – eine beliebige gültige JSON-Zeichenfolge
- "int" – eine gültige JSON-Ganzzahl
- "bool" – ein gültiger boolescher JSON-Wert
- "object" – ein gültiges JSON-Objekt
- "array" – ein gültige JSON-Array


>[AZURE.NOTE]Für Kennwörter, Schlüssel und andere geheime Informationen sollte der Typ **secureString** verwendet werden. Vorlagenparameter des Typs secureString können nach der Bereitstellung der Ressource nicht mehr gelesen werden.

Im folgenden Beispiel wird veranschaulicht, wie Sie Parameter definieren:

    "parameters": {
       "siteName": {
          "type": "string"
       },
       "siteLocation": {
          "type": "string"
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       }
    }

## Variablen

Im Variablenabschnitt erstellen Sie Werte, die zum Vereinfachen der Vorlagenausdrücke verwendet werden können. Diese Variablen basieren i. d. R. auf Werten, die von den Parametern stammen.

Das folgende Beispiel zeigt, wie Sie eine Variable definieren, die aus zwei Parameterwerten erstellt wird:

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

Das nächste Beispiel zeigt eine Variable mit einem komplexen JSON-Typ sowie Variablen, die aus anderen Variablen erstellt werden:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## Ressourcen

Im Ressourcenabschnitt definieren Sie die Ressourcen für die Bereitstellung oder Aktualisierung.

Sie definieren Ressourcen mit der folgenden Struktur:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| Elementname | Erforderlich | Beschreibung
| :----------------------: | :------: | :----------
| apiVersion | Ja | Die Version der API, die die Ressource unterstützt.
| Typ | Ja | Der Typ der Ressource. Dieser Wert ist eine Kombination aus dem Namespace des Ressourcenanbieters und dem Ressourcentyp, den der Ressourcenanbieter unterstützt.
| Name | Ja | Der Name der Ressource. Der Name muss die Einschränkungen für URI-Komponenten laut Definition in RFC3986 erfüllen.
| location | Nein | Unterstützte Standorte der angegebenen Ressource
| tags | Nein | Markierungen, die der Ressource zugeordnet sind
| dependsOn | Nein | Ressourcen, von denen die definierte Ressource abhängig ist. Die Abhängigkeiten zwischen den Ressourcen werden ausgewertet, und die Ressourcen werden in ihrer Abhängigkeitsreihenfolge bereitgestellt. Wenn Ressourcen nicht voneinander abhängig sind, wird versucht, sie parallel bereitzustellen. Der Wert kann eine durch Trennzeichen getrennte Liste von Ressourcennamen oder eindeutigen Ressourcenbezeichnern sein.
| Eigenschaften | Nein | Ressourcenspezifische Konfigurationseinstellungen
| angeben | Nein | Untergeordnete Ressourcen, die von der definierten Ressource abhängig sind.

Wenn der Ressourcenname nicht eindeutig ist, können Sie mit der **resourceId**-Hilfsfunktion (weiter unten beschrieben) den eindeutigen Bezeichner für eine Ressource abrufen.

Das folgende Beispiel zeigt eine **Microsoft.Web/serverfarms**-Ressource und eine **Microsoft.Web/Sites**-Ressource mit einer verschachtelten **Extensions**-Ressource:

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
                  "properties": {
                    "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                      "Application Path": "[parameters('siteName')]"
                    }
                  }
              }
          ]
        }
    ]


## Ausgaben

Im Ausgabenabschnitt legen Sie Werte fest, die von der Bereitstellung zurückgegeben werden. Sie könnten z. B. den URI für den Zugriff auf eine bereitgestellte Ressource zurückgeben.

Das folgende Beispiel zeigt die Struktur einer Ausgabedefinition:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| Elementname | Erforderlich | Beschreibung
| :------------: | :------: | :----------
| outputName | Ja | Name des Ausgabewerts. Es muss sich um einen gültigen JavaScript-Bezeichner handeln.
| Typ | Ja | Der Typ des Ausgabewerts. Ausgabewerte unterstützen dieselben Typen wie Vorlagen-Eingabeparameter.
| value | Ja | Ein Vorlagensprachausdruck, der ausgewertet und als Ausgabewert zurückgegeben wird.


Das folgende Beispiel zeigt einen Wert, der im Ausgabeabschnitt zurückgegeben wird.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## Fortgeschrittenere Szenarios.
Dieses Thema bietet einen einführenden Einblick in die Vorlage. Ihr Szenario erfordert jedoch möglicherweise komplexere Aufgaben.

Sie müssen möglicherweise zwei Vorlagen zusammenführen oder eine untergeordnete Vorlage innerhalb einer übergeordneten Vorlage verwenden. Weitere Informationen finden Sie unter [Geschachtelte Profile](../resource-group-advanced-template#nested-template).

Möglicherweise müssen Sie Ressourcen verwenden, die in einer anderen Ressourcengruppe enthalten sind. Dies geschieht normalerweise bei der Arbeit mit Speicherkonten oder virtuellen Netzwerken, die in mehreren Ressourcengruppen gemeinsam verwendet werden. Weitere Informationen finden Sie unter der [resourceId-Funktion](../resource-group-template-functions#resourceid).

## Vollständige Vorlage
Die folgende Vorlage stellt eine Web-App bereit und stattet sie mit Code aus einer ZIP-Datei aus.

    {
       "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
               ],
               "properties": {
                 "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                 "dbType": "None",
                 "connectionString": "",
                 "setParameters": {
                   "Application Path": "[parameters('siteName')]"
                 }
               }
             }
           ]
         }
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## Nächste Schritte
- [Vorlagenfunktionen im Azure-Ressourcen-Manager](./resource-group-template-functions.md)
- [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](azure-portal/resource-group-template-deploy.md)
- [Erweiterte Vorlagenvorgänge](./resource-group-advanced-template.md)
- [Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Übersicht über den Azure Resource Manager](./resource-group-overview.md)
- [Schemas des Azure-Ressourcen-Manager](https://github.com/Azure/azure-resource-manager-schemas)

<!---HONumber=July15_HO4-->