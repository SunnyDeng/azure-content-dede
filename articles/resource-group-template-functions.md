<properties
   pageTitle="Funktionen von Azure Resource Manager-Vorlagen"
   description="Es werden die Funktionen beschrieben, die in einer Azure-Ressourcen-Manager-Vorlage zum Abrufen von Werten, Formatieren von Zeichenfolgen und Abrufen von Bereitstellungsinformationen verwendet werden."
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
   ms.date="09/14/2015"
   ms.author="tomfitz"/>

# Funktionen von Azure Resource Manager-Vorlagen

Dieses Thema beschreibt alle Funktionen, die Sie in einer Azure Resource Manager-Vorlage verwenden können.

## Hinzufügen

**add(operand1, operand2)**

Gibt die Summe der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Erster zu verwendender Operand.
| operand2 | Ja | Zweiter zu verwendender Operand.


## base64

**base64 (InputString)**

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

**concat (arg1, arg2, arg3, ...)**

Kombiniert mehrere Zeichenfolgewerte und gibt den resultierenden Zeichenfolgewert zurück. Diese Funktion kann eine beliebige Anzahl an Argumenten entgegennehmen.

Das folgende Beispiel zeigt die Kombinationsweise mehrerer Werte, um einen Wert zurückzugeben.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## copyIndex

**copyIndex(offset)**

Gibt den aktuellen Index einer Iterationsschleife zurück. Beispiele zur Verwendung dieser Funktion finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).

## deployment

**deployment()**

Gibt Informationen zum aktuellen Bereitstellungsvorgang zurück.

Die Informationen zur Bereitstellung werden als Objekt mit den folgenden Eigenschaften zurückgegeben:

    {
      "name": "",
      "properties": {
        "template": {},
        "parameters": {},
        "mode": "",
        "provisioningState": ""
      }
    }

Im folgenden Beispiel wird veranschaulicht, wie Bereitstellungsinformationen im Abschnitt „outputs“ zurückgegeben werden.

    "outputs": {
      "exampleOutput": {
        "value": "[deployment()]",
        "type" : "object"
      }
    }

## div

**div(operand1, operand2)**

Gibt die Ganzzahldivision der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Die zu teilende Zahl.
| operand2 | Ja | Die Zahl, durch die geteilt wird. Muss ungleich 0 sein.

## int

**int(valueToConvert)**

Konvertiert den angegebenen Wert in eine ganze Zahl (Integer).

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| valueToConvert | Ja | Der Wert, der in eine ganze Zahl konvertiert werden soll. Der Wert kann nur den Typ „String“ oder „Integer“ haben.

Im folgenden Beispiel wird der vom Benutzer angegebene Parameterwert in eine ganze Zahl konvertiert.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }

## Länge

**length(array)**

Gibt die Anzahl der Elemente in einem Array zurück. Wird normalerweise verwendet, um bei der Erstellung von Ressourcen die Anzahl der Iterationen anzugeben. Ein Beispiel zur Verwendung dieser Funktion finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).

## listKeys

**listKeys (resourceName oder resourceIdentifier, [apiVersion])**

Gibt die Schlüssel eines Speicherkontos zurück. Die Ressourcen-ID kann mithilfe der [Ressourcen-ID-Funktion](./#resourceid) oder mithilfe des Formats **providerNamespaces/resourceType/resourceName** angegeben werden. Sie können die Funktion nutzen um den Primär- und Sekundärschlüssel abzurufen.
  
| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| resourceName oder resourceIdentifier | Ja | Eindeutiger Bezeichner eines Speicherkontos.
| "apiVersion": | Ja | API-Version eines Ressourcen-Laufzeitstatus.

Das folgende Beispiel zeigt, wie die Schlüssel von einem Speicherkonto in die Ausgabeabschnitte zurückgegeben werden können.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

## mod

**mod(operand1, operand2)**

Gibt den Rest der Ganzzahldivision mit den beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Die zu teilende Zahl.
| operand2 | Ja | Die Zahl, durch die geteilt wird. Muss ungleich 0 sein.


## mul

**mul(operand1, operand2)**

Gibt die Multiplikation der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Erster zu verwendender Operand.
| operand2 | Ja | Zweiter zu verwendender Operand.


## padLeft

**padLeft(stringToPad, totalLength, paddingCharacter)**

Gibt eine rechtsbündig ausgerichtete Zeichenfolge zurück, indem links Zeichen hinzugefügt werden, bis die angegebene Gesamtlänge erreicht ist.
  
| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| stringToPad | Ja | Die Zeichenfolge, die rechtsbündig ausgerichtet werden soll.
| totalLength | Ja | Die Gesamtzahl der Zeichen in der zurückgegebenen Zeichenfolge.
| paddingCharacter | Ja | Das Zeichen, das für das Auffüllen auf der linken Seite verwendet werden soll, bis die Gesamtlänge erreicht ist.

Im folgenden Beispiel wird veranschaulicht, wie Sie den vom Benutzer angegebenen Parameterwert auffüllen, indem Sie das Nullzeichen hinzufügen, bis die Zeichenfolge zehn Zeichen lang ist. Wenn der ursprüngliche Parameterwert länger als zehn Zeichen ist, werden keine Zeichen hinzugefügt.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }


## parameters

**Parameter (parameterName)**

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

## providers

**providers (providerNamespace, [resourceType])**

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

**reference (resourceName oder resourceIdentifier, [apiVersion])**

Aktiviert einen Ausdruck, um seinen Wert vom Laufzeitstatus einer anderen Ressource abzuleiten.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| resourceName oder resourceIdentifier | Ja | Name oder eindeutiger Bezeichner einer Ressource.
| "apiVersion": | Nein | API-Version eines Ressourcen-Laufzeitstatus. Parameter sollten verwendet werden, wenn die Ressource nicht innerhalb der gleichen Vorlage zur Verfügung gestellt ist.

Die **Referenz**-Funktion leitet ihren Wert von einem Laufzeitstatus ab und kann somit nicht im Variablen-Abschnitt verwendet werden. Sie kann in Ausgabeabschnitten einer Vorlage verwendet werden.

Mithilfe des Referenzausdrucks können Sie implizit deklarieren, dass eine Ressource von einer anderen abhängt, wenn die referenzierte Ressource innerhalb der gleichen Vorlage zur Verfügung gestellt wird. Sie müssen nicht zusätzlich die Eigenschaft **dependsOn** verwenden. Der Ausdruck wird nicht ausgewertet, bis die Ressource, auf die verwiesen wird, die Bereitstellung abgeschlossen hat.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## replace

**replace(originalString, oldCharacter, newCharacter)**

Gibt eine neue Zeichenfolge zurück, in der alle Instanzen eines Zeichens in der angegebenen Zeichenfolge durch ein anderes Zeichen ersetzt wurden.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| originalString | Ja | Die Zeichenfolge, für die alle Instanzen eines Zeichens durch ein anderes Zeichen ersetzt werden sollen.
| oldCharacter | Ja | Das Zeichen, das aus der ursprünglichen Zeichenfolge entfernt werden soll.
| newCharacter | Ja | Das Zeichen, das für das entfernte Zeichen eingefügt werden soll.

Im folgenden Beispiel wird veranschaulicht, wie Sie alle Bindestriche aus einer vom Benutzer angegebenen Zeichenfolge entfernen.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

## Ressourcengruppe

**resourceGroup()**

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

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

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
    
Sie müssen diese Funktion oft nutzen, wenn Sie ein Speicherkonto oder einen virtuellen Computer in einer alternativen Ressourcengruppe verwenden. Das Speicherkonto oder der virtuelle Computer können über mehrere Ressourcengruppen hinweg genutzt werden. Daher sollten Sie diese beim Löschvorgang für eine einzelne Ressourcengruppe nicht löschen. Das folgende Beispiel zeigt, wie eine Ressource einer externen Ressourcengruppe leicht genutzt werden kann:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## split

**split(inputString, Trennzeichen)** **split(inputString, [Trennzeichen])**

Gibt ein Array von Zeichenfolgen zurück, das die Teilzeichenfolgen der Eingabezeichenfolge enthält, die durch die gesendeten Trennzeichen getrennt sind.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| inputString | Ja | Die zu teilende Zeichenfolge.
| Trennzeichen | Ja | Das verwendete Trennzeichen. Kann eine einzelne Zeichenfolge oder ein Array von Zeichenfolgen sein.

Im folgenden Beispiel wird die Eingabezeichenfolge durch ein Komma unterteilt.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

## string

**string(valueToConvert)**

Konvertiert den angegebenen Wert in eine Zeichenfolge (String).

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| valueToConvert | Ja | Der Wert, der in eine Zeichenfolge konvertiert werden soll. Der Wert kann nur den Typ „Boolean“, „Integer“ oder „String“ haben.

Im folgenden Beispiel wird der vom Benutzer angegebene Parameterwert in eine Zeichenfolge konvertiert.

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
    }

## sub

**sub(operand1, operand2)**

Gibt die Differenz der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Zahl, von der subtrahiert werden soll.
| operand2 | Ja | Zahl, die subtrahiert werden soll.


## Abonnement

**subscription()**

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

## toLower

**toLower(stringToChange)**

Konvertiert die angegebene Zeichenfolge in Kleinbuchstaben.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| stringToChange | Ja | Die Zeichenfolge, die in Kleinbuchstaben konvertiert werden soll.

Im folgenden Beispiel wird der vom Benutzer angegebene Parameterwert in Kleinbuchstaben konvertiert.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

## toUpper

**toUpper(stringToChange)**

Konvertiert die angegebene Zeichenfolge in Großbuchstaben.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| stringToChange | Ja | Die Zeichenfolge, die in Großbuchstaben konvertiert werden soll.

Im folgenden Beispiel wird der vom Benutzer angegebene Parameterwert in Großbuchstaben konvertiert.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }


## Variablen

**Variablen (variableName)**

Gibt den Wert der Variablen zurück. Der angegebene Variablenname muss im Variablenabschnitt der Vorlage definiert werden.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| Variablenname | Ja | Der Name der zurückzugebenden Variable.


## Nächste Schritte
- Eine Beschreibung der Abschnitte in einer Azure-Ressourcen-Manager-Vorlage finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
- Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen mit Azure-Ressourcen-Manager](resource-group-linked-templates.md).
- Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).
- Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](azure-portal/resource-group-template-deploy.md).

<!---HONumber=Sept15_HO3-->