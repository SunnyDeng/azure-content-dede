<properties
   pageTitle="Vorlagenfunktionen im Ressourcen-Manager | Microsoft Azure"
   description="Es werden die Funktionen beschrieben, die in einer Azure-Ressourcen-Manager-Vorlage zum Abrufen von Werten, Arbeiten mit Zeichenfolgen und numerischen Werten sowie Abrufen von Bereitstellungsinformationen verwendet werden."
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
   ms.date="02/22/2016"
   ms.author="tomfitz"/>

# Vorlagenfunktionen im Azure-Ressourcen-Manager

Dieses Thema beschreibt alle Funktionen, die Sie in einer Azure Resource Manager-Vorlage verwenden können.

Bei Vorlagenfunktionen und ihren Parametern wird Groß-und Kleinschreibung nicht unterschieden. Der Ressourcen-Manager löst beispielsweise **variables('var1')** und **VARIABLES('VAR1')** identisch auf. Bei der Auswertung wird die Groß-/Kleinschreibung beibehalten, sofern diese nicht ausdrücklich durch die Funktion geändert wird (z. B. mit „toUpper“ oder „toLower“). Für spezielle Ressourcentypen gelten möglicherweise Vorgaben zur Schreibweise ungeachtet der Auswertungsweise von Funktionen.

## Numerische Funktionen

Der Ressourcen-Manager stellt die folgenden Funktionen für das Arbeiten mit ganzen Zahlen bereit:

- [Hinzufügen](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [Länge](#length)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### Hinzufügen

**add(operand1, operand2)**

Gibt die Summe der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Erster zu verwendender Operand.
| operand2 | Ja | Zweiter zu verwendender Operand.


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Gibt den aktuellen Index einer Iterationsschleife zurück.

Diese Funktion wird immer mit einem **copy**-Objekt verwendet. Beispiele zur Verwendung von **copyIndex** finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).


<a id="div" />
### div

**div(operand1, operand2)**

Gibt die Ganzzahldivision der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Die zu teilende Zahl.
| operand2 | Ja | Die Zahl, durch die geteilt wird. Muss ungleich 0 sein.


<a id="int" />
### int

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


<a id="length" />
### Länge

**Länge ("Array" oder "String")**

Gibt die Anzahl der Elemente in einem Array oder die Anzahl der Zeichen in einer Zeichenfolge zurück. Sie können diese Funktion mit einem Array verwenden, um bei der Erstellung von Ressourcen die Anzahl der Iterationen anzugeben. Im folgenden Beispiel bezieht sich der Parameter **siteNames** auf ein Array von Namen, die bei der Erstellung der Websites verwendet werden.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Weitere Informationen zur Verwendung dieser Funktion mit einem Array finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).

Sie können auch eine Zeichenfolge verwenden:

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

Gibt den Rest der Ganzzahldivision mit den beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Die zu teilende Zahl.
| operand2 | Ja | Die Zahl, durch die geteilt wird. Muss ungleich 0 sein.



<a id="mul" />
### mul

**mul(operand1, operand2)**

Gibt die Multiplikation der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Erster zu verwendender Operand.
| operand2 | Ja | Zweiter zu verwendender Operand.


<a id="sub" />
### sub

**sub(operand1, operand2)**

Gibt die Differenz der beiden angegebenen ganzen Zahlen zurück.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| operand1 | Ja | Zahl, von der subtrahiert werden soll.
| operand2 | Ja | Zahl, die subtrahiert werden soll.


## Zeichenfolgenfunktionen

Der Ressourcen-Manager stellt die folgenden Funktionen für das Arbeiten mit Zeichenfolgen bereit:

- [base64](#base64)
- [concat](#concat)
- [padLeft](#padleft)
- [replace](#replace)
- [split](#split)
- [string](#string)
- [substring](#substring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [trim](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)

Die Anzahl der Zeichen in einer Zeichenfolge oder einem Array ermitteln Sie mit [length](#length).

<a id="base64" />
### base64

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

<a id="concat" />
### concat

**concat (arg1, arg2, arg3, ...)**

Kombiniert mehrere Werte und gibt das verkettete Ergebnis zurück. Diese Funktion akzeptiert eine beliebige Anzahl von Argumenten und Zeichenfolgen oder Arrays für die Parameter.

Das folgende Beispiel zeigt, wie mehrere Zeichenfolgenwerte kombiniert werden, um eine verkettete Zeichenfolge zu erhalten.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

Im nächsten Beispiel wird veranschaulicht, wie zwei Arrays kombiniert werden.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="padleft" />
### padLeft

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

<a id="replace" />
### replace

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

<a id="split" />
### split

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

<a id="string" />
### string

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

<a id="substring" />
### substring

**substring(stringToParse, startIndex, length)**

Gibt eine Teilzeichenfolge zurück, die an der angegebenen Zeichenposition beginnt und die angegebene Anzahl von Zeichen enthält.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| stringToParse | Ja | Die ursprüngliche Zeichenfolge, aus der die Teilzeichenfolge extrahiert wird.
| startIndex | Nein | Die nullbasierte Anfangsposition für die Teilzeichenfolge.
| Länge | Nein | Die Anzahl der Zeichen der Teilzeichenfolge.

Im folgenden Beispiel werden die ersten drei Zeichen aus einem Parameter extrahiert.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="tolower" />
### toLower

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

<a id="toupper" />
### toUpper

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

<a id="trim" />
### trim

**trim (stringToTrim)**

Entfernt alle führenden und nachgestellten Leerzeichen aus der angegebenen Zeichenfolge.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| stringToTrim | Ja | Die zu kürzende Zeichenfolgen.

Im folgenden Beispiel werden die Leerzeichen aus dem vom Benutzer bereitgestellten Parameterwert entfernt.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (stringForCreatingUniqueString, ...)**

Führt einen 64-Bit-Hash mit den bereitgestellten Zeichenfolgen durch, um eine eindeutige Zeichenfolge zu erstellen. Diese Funktion ist hilfreich, wenn Sie einen eindeutigen Namen für eine Ressource erstellen müssen. Sie geben Parameterwerte an, die die Ebene der Eindeutigkeit für das Ergebnis darstellen. Sie können angeben, ob der Name für Ihr Abonnement, die Ressourcengruppe oder die Bereitstellung eindeutig ist.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | Ja | Die Basiszeichenfolge, die in der Hashfunktion verwendet wird, um eine eindeutige Zeichenfolge zu erstellen.
| Zusätzliche Parameter nach Bedarf. | Nein | Sie können beliebig viele Zeichenfolgen hinzufügen, ganz wie sie zum Erstellen des Werts benötigt werden, der die Ebene der Eindeutigkeit angibt.

Der zurückgegebene Wert ist keine vollständig zufällige Zeichenfolge, sondern eher das Ergebnis einer Hashfunktion. Der zurückgegebene Wert ist 13 Zeichen lang. Es ist nicht garantiert, dass er global eindeutig ist. Möglicherweise sollten Sie den Wert mit einem Präfix aus Ihren Benennungskonventionen kombinieren, um einen besser geeigneten Anzeigenamen zu erstellen.

Die folgenden Beispiele zeigen, wie Sie mithilfe von uniqueString einen eindeutigen Wert für verschiedene, häufig verwendete Ebenen erstellen können.

Eindeutig auf Grundlage des Abonnements

    "[uniqueString(subscription().subscriptionId)]"

Eindeutig auf Grundlage der Ressourcengruppe

    "[uniqueString(resourceGroup().id)]"

Eindeutig auf Grundlage der Bereitstellung für eine Ressourcengruppe

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
Das folgende Beispiel zeigt, wie Sie einen eindeutigen Namen für ein Speicherkonto auf Grundlage seiner Ressourcengruppe erstellen.

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

<a id="uri" />
### uri

**uri (baseUri, relativeUri)**

Erstellt einen absoluten URI durch Kombinieren der baseUri- und der relativeUri-Zeichenfolge.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| baseUri | Ja | Die Zeichenfolge mit dem Basis-URI.
| relativeUri | Ja | Der Zeichenfolge mit dem relativen URI, die der Zeichenfolge mit dem Basis-URI hinzugefügt werden soll.

Der Wert für den **baseUri**-Parameter kann eine bestimmte Datei enthalten, beim Erstellen des URI wird jedoch nur der Basispfad verwendet. Beispielsweise führt das Übergeben von ****http://contoso.com/resources/azuredeploy.json** als BaseUri-Parameter zu einem Basis-URI von ****http://contoso.com/resources/**.

Im folgenden Beispiel wird veranschaulicht, wie basierend auf dem Wert der übergeordneten Vorlage eine Verknüpfung zu einer geschachtelten Vorlage erstellt wird.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## Arrayfunktionen

Der Ressourcen-Manager stellt die folgenden Funktionen für das Arbeiten mit Arraywerten bereit:

Zum Kombinieren mehrerer Arrays in einem Array, verwenden Sie [concat](#concat).

Um die Anzahl der Elemente in einem Array zu erhalten, verwenden Sie [length](#length).

Um einen Zeichenfolgenwert in ein Array von Zeichenfolgenwerten zu unterteilen, verwenden Sie [split](#split).

## Funktionen für Bereitstellungswerte

Der Ressourcen-Manager stellt die folgenden Funktionen zum Abrufen von Werten aus Vorlagenabschnitten sowie von bereitstellungsbezogenen Werten bereit:

- [deployment](#deployment)
- [parameters](#parameters)
- [Variablen](#variables)

Informationen zum Abrufen von Werten aus Ressourcen, Ressourcengruppen oder Abonnements finden Sie unter [Ressourcenfunktionen](#resource-functions).

<a id="deployment" />
### deployment

**deployment()**

Gibt Informationen zum aktuellen Bereitstellungsvorgang zurück.

Diese Funktion gibt das Objekt zurück, das während der Bereitstellung übergeben wird. Die Eigenschaften im zurückgegebenen Objekt hängen davon ab, ob das Bereitstellungsobjekt als Link oder als Inline-Objekt übergeben wird. Wenn das Bereitstellungsobjekt als Inlineobjekt übergeben wird, z. B. bei Verwendung des **-TemplateFile**-Parameters in Azure PowerShell zum Verweisen auf eine lokale Datei, hat das zurückgegebene Objekt folgendes Format:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Wenn das Objekt als Link übergeben wird, z. B. bei Verwendung des **-TemplateUri**-Parameters zum Verweisen auf ein Remoteobjekt, hat das zurückgegebene Objekt folgendes Format:

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
            "mode": "",
            "provisioningState": ""
        }
    }

Im folgenden Beispiel wird veranschaulicht, wie die Bereitstellung() verwendet wird, um zu einer anderen Vorlage basierend auf dem URI der übergeordneten Vorlage eine Verknüpfung erstellt wird.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  


<a id="parameters" />
### parameters

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

<a id="variables" />
### Variablen

**Variablen (variableName)**

Gibt den Wert der Variablen zurück. Der angegebene Variablenname muss im Variablenabschnitt der Vorlage definiert werden.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| Variablenname | Ja | Der Name der zurückzugebenden Variable.



## Ressourcen-Funktionen

Der Ressourcen-Manager stellt die folgenden Funktionen zum Abrufen von Ressourcenwerten bereit:

- [listkeys](#listkeys)
- [list*](#list)
- [providers](#providers)
- [Referenz](#reference)
- [Ressourcengruppe](#resourcegroup)
- [Ressourcen-ID](#resourceid)
- [Abonnement](#subscription)

Informationen zum Abrufen von Werten aus Parametern, Variablen oder der aktuellen Bereitstellung finden Sie unter [Bereitstellungswertfunktionen](#deployment-value-functions).

<a id="listkeys" />
### listKeys

**listKeys (resourceName oder resourceIdentifier, [apiVersion])**

Gibt den Schlüssel für einen beliebigen Ressourcentyp zurück, der den listKeys-Vorgang unterstützt. Die Ressourcen-ID kann mithilfe der [Ressourcen-ID-Funktion](./#resourceid) oder mithilfe des Formats **providerNamespaces/resourceType/resourceName** angegeben werden. Sie können die Funktion nutzen um den Primär- und Sekundärschlüssel abzurufen.
  
| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| resourceName oder resourceIdentifier | Ja | Eindeutiger Bezeichner für die Ressource.
| apiVersion | Ja | API-Version eines Ressourcen-Laufzeitstatus.

Das folgende Beispiel zeigt, wie die Schlüssel von einem Speicherkonto in die Ausgabeabschnitte zurückgegeben werden können.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="list" />
### list*

**list* (resourceName oder resourceIdentifier, apiVersion)**

Jeder Vorgang, der mit **list** beginnt, kann als Funktion in der Vorlage verwendet werden. Dies schließt **listKeys** ein, wie oben gezeigt, aber auch Vorgänge wie **list**, **listAdminKeys** oder **listStatus**. Verwenden Sie zum Aufrufen der Funktion ihren tatsächlichen Namen, nicht „list*“. Um zu bestimmen, welche Ressourcentypen einen list-Vorgang aufweisen, verwenden Sie den folgenden PowerShell-Befehl.

    PS C:\> Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

Stattdessen können Sie die Liste auch über die Azure-Befehlszeilenschnittstelle abrufen. Das folgende Beispiel ruft alle Vorgänge für **apiapps** ab und verwendet das JSON-Dienstprogramm [jq](http://stedolan.github.io/jq/download/), um nur die list-Vorgänge zu filtern.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

<a id="providers" />
### providers

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

<a id="reference" />
### Referenz

**reference (resourceName oder resourceIdentifier, [apiVersion])**

Aktiviert einen Ausdruck, um seinen Wert vom Laufzeitstatus einer anderen Ressource abzuleiten.

| Parameter | Erforderlich | Beschreibung
| :--------------------------------: | :------: | :----------
| resourceName oder resourceIdentifier | Ja | Name oder eindeutiger Bezeichner einer Ressource
| "apiVersion": | Nein | API-Version der angegebenen Ressource. Sie müssen diesen Parameter einschließen, wenn die Ressource nicht innerhalb der gleichen Vorlage bereitgestellt wird.

Die **Referenz**-Funktion leitet ihren Wert von einem Laufzeitstatus ab und kann somit nicht im Variablen-Abschnitt verwendet werden. Sie kann in Ausgabeabschnitten einer Vorlage verwendet werden.

Mithilfe der Referenzfunktion können Sie implizit deklarieren, dass eine Ressource von einer anderen abhängt, wenn die referenzierte Ressource innerhalb der gleichen Vorlage zur Verfügung gestellt wird. Sie müssen nicht zusätzlich die Eigenschaft **dependsOn** verwenden. Die Funktion wird nicht ausgewertet, bis die Ressource, auf die verwiesen wird, die Bereitstellung abgeschlossen hat.

Im folgenden Beispiel wird auf ein Speicherkonto verwiesen, das in der gleichen Vorlage bereitgestellt wird.

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

Im folgenden Beispiel wird auf ein Speicherkonto verwiesen, das nicht in dieser Vorlage bereitgestellt wird, aber innerhalb der gleichen Ressourcengruppe wie die bereitgestellten Ressourcen enthalten ist.

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
			"type" : "object"
		}
	}

Sie können wie folgt über das zurückgegebene Objekt, beispielsweise den Blob-Endpunkt-URI, einen bestimmten Wert abrufen:

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Wenn Sie die API-Version in Ihrer Vorlage nicht direkt angeben möchten, können Sie wie folgt mithilfe der [providers](#providers)-Funktion einen der Werte abrufen, etwa die aktuelle Version:

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Im folgenden Beispiel wird auf ein Speicherkonto in einer anderen Ressourcengruppe verwiesen:

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

<a id="resourcegroup" />
### Ressourcengruppe

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

<a id="resourceid" />
### Ressourcen-ID

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

<a id="subscription" />
### Abonnement

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


## Nächste Schritte
- Eine Beschreibung der Abschnitte in einer Azure-Ressourcen-Manager-Vorlage finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
- Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen mit Azure-Ressourcen-Manager](resource-group-linked-templates.md).
- Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).
- Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0224_2016-->