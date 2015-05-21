<properties
   pageTitle="Funktionen von Azure Resource Manager-Vorlagen"
   description="Beschreibt die in einer Azure Resource Manager-Vorlage zu verwendenden Funktionen, um Azure Anwendungen bereitzustellen."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Funktionen von Azure Resource Manager-Vorlagen

Dieses Thema beschreibt alle Funktionen, die Sie in einer Azure Resource Manager-Vorlage verwenden können.

## base64

**base64 \(InputString\)**

Rückkehr zur base64-Darstellung der Eingabezeichenfolge.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| inputString | Ja | Der Zeichenfolgewert, der als base64-Darstellung zurückgegeben wird.

Das folgende Beispiel zeigt die Funktionsweise der base64-Funktion.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat \(arg1, arg2, arg3, ...\)**

Kombiniert mehrere Zeichenfolgewerte und gibt den resultierenden Zeichenfolgewert zurück. Diese Funktion kann eine beliebige Anzahl an Argumenten entgegennehmen. 

Das folgende Beispiel zeigt die Kombinationsweise mehrerer Werte, um einen Wert zurückzugeben.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys \(resourceName oder resourceIdentifier, [apiVersion]\)**

Gibt die Schlüssel eines Speicherkontos zurück. Die Ressourcen-ID kann mithilfe der [Ressourcen-ID-Funktion](./#resourceid) oder mithilfe des Formats **providerNamespaces/resourceType/resourceName** angegeben werden. Sie können die Funktion nutzen um den Primär- und Sekundärschlüssel abzurufen.
  
| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| resourceName oder resourceIdentifier | Ja | Eindeutiger Bezeichner eines Speicherkontos.
| "apiVersion": | Ja | API-Version eines Ressourcen-Laufzeitstatus.

Das folgende Beispiel zeigt, wie die Schlüssel von einem Speicherkonto in die Ausgabeabschnitte zurückgegeben werden können.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## angeben

**Parameter \(parameterName\)**

Gibt einen Parameterwert zurück. Der spezifizierte Parametername muss im Parameterabschnitt der Vorlage definiert werden.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| parameterName | Ja | Der Name des zurückzugebenden Parameter.

Die folgenden Beispiele zeigen eine vereinfachte Nutzungsweise der Parameterfunktion.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

## Anbieter

**Anbieter \(providerNamespace, [resourceType]\)**

Rückgabe von Informationen über einen Ressourcenanbieter und die von ihm unterstützten Ressourcentypen. Wenn kein Typ angegeben wird, werden alle unterstützten Typen zurückgegeben.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| providerNamespace | Ja | Namespace des Anbieters
| resourceType | Nein | Der Ressourcentyp innerhalb des angegebenen Namespace.

Jeder unterstützte Typ wird im folgenden Format zurückgegeben:

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

Das folgende Beispiel zeigt die Nutzungsweise der Anbieterfunktion:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

## Referenz

**Referenz \(resourceName oder resourceIdentifier, [apiVersion]\)**

Aktiviert einen Ausdruck, um seinen Wert vom Laufzeitstatus einer anderen Ressource abzuleiten.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| resourceName oder resourceIdentifier | Ja | Name oder eindeutiger Bezeichner einer Ressource.
| "apiVersion": | Nein | API-Version eines Ressourcen-Laufzeitstatus. Parameter sollten verwendet werden, wenn die Ressource nicht innerhalb der gleichen Vorlage zur Verfügung gestellt ist.

Die **Referenz**-Funktion leitet ihren Wert von einem Laufzeitstatus ab und kann somit nicht im Variablen-Abschnitt verwendet werden. Sie kann in Ausgabeabschnitten einer Vorlage verwendet werden.

Mithilfe des Referenzausdrucks können Sie deklarieren, dass eine Ressource von einer anderen abhängt, wenn die verwiesene Ressource innerhalb der gleichen Vorlage zur Verfügung gestellt wird. 

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## Ressourcengruppe

**resourceGroup\(\)**

Gibt ein strukturiertes Objekt zurück, das die aktuelle Ressourcengruppe repräsentiert. Das Objekt wird das folgende Format haben:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

Das folgende Beispiel nutzt den Speicherort der Ressourcengruppe, um einer Website den Speicherort zuzuweisen.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

## Ressourcen-ID

**Ressourcen-ID \([resourceGroupName], resourceType, resourceName1, [resourceName2]...\)**

Gibt den eindeutigen Bezeichner einer Ressource zurück. Diese Funktion wird verwendet, wenn der Ressourcenname zweideutig ist oder nicht innerhalb der gleichen Vorlage zur Verfügung gestellt wird. Der Bezeichner wird im folgenden Format zurückgeben:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Parameter | Erforderlich | Beschreibung
| :---------------: | :------: | :----------
| ResourceGroupName | Nein | Optionaler Name von Ressourcengruppe. Der Standardwert ist die aktuelle Ressourcengruppe. Geben Sie diesen Wert an, wenn Sie eine Ressource in einer anderen Ressource abrufen.
| resourceType | Ja | Ressourcentyp einschließlich Namespace von Ressourcenanbieter.
| resourceName1 | Ja | Name der Ressource.
| resourceName2 | Nein | Nächstes Ressourcen-Namensegment, wenn die Ressource geschachtelt ist.

Das folgende Beispiel zeigt, wie die Ressourcen-IDs für eine Website und eine Datenbank abgerufen werden können. Die Website besteht in einer Ressourcengruppe namens **myWebsitesGroup** und die Datenbank in der aktuellen Ressourcengruppe für diese Vorlage.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
Sie müssen diese Funktion oft nutzen, wenn Sie ein Speicherkonto oder einen virtuellen Computer in einer alternativen Ressourcengruppe verwenden. Das Speicherkonto oder der virtuelle Computer können über mehrere Ressourcengruppen hinweg genutzt werden. Daher sollten Sie diese beim Löschvorgang für eine einzelne Ressourcengruppe nicht löschen. Das folgende Beispiel zeigt, wie eine Ressource von einer externen Ressourcengruppe leicht verwendet werden kann:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }


## Abonnement

**subscription\(\)**

Gibt Details über das Abonnement im folgenden Format zurück:

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

Das folgende Beispiel zeigt ein Abrufen der Abonnement-Funktion im Ausgabeabschnitt.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## Variablen

**Variablen \(variableName\)**

Gibt den Wert der Variablen zurück. Der angegebene Variablenname muss im Variablenabschnitt der Vorlage definiert werden.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| Variablenname | Ja | Der Name der zurückzugebenden Variable.


## Nächste Schritte
- [Erstellen von Resource Manager-Vorlagen](./resource-group-authoring-templates.md)
- [Erweiterte Vorlagenvorgänge](./resource-group-advanced-template.md)
- [Bereitstellen einer Anwendung mit einer Azure Resource Manager-Vorlage](./resouce-group-template-deploy.md)
- [Azure Resource Manager Cmdlets](./resource-group-overview.md):

<!--HONumber=52-->
