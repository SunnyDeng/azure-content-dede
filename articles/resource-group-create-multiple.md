<properties
   pageTitle="Bereitstellen mehrerer Instanzen von Ressourcen | Microsoft Azure"
	description="Verwenden Sie den copy-Vorgang und Arrays in einer Azure-Ressourcen-Manager-Vorlage, um sie beim Bereitstellen von Ressourcen mehrere Male zu durchlaufen."
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
	ms.date="08/27/2015"
	ms.author="tomfitz"/>

# Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager

In diesem Thema erfahren Sie, wie Sie die Azure-Ressourcen-Manager-Vorlage durchlaufen können, um mehrere Instanzen einer Ressource zu erstellen.

## "copy", "copyIndex" und "length"

Um innerhalb der Ressource mehrere Instanzen zu erstellen, können Sie ein **copy**-Objekt definieren, das die Anzahl der Iterationen angibt. Die Kopie hat das folgende Format:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Mit der **copyIndex()**-Funktion können Sie den aktuellen Iterationswert abrufen, wie nachfolgend in der Verkettungsfunktion gezeigt.

    [concat('examplecopy-', copyIndex())]

Wenn Sie mehrere Ressourcen aus einem Array von Werten erstellen, können Sie die Anzahl über die Funktion **length** angeben. Das Array wird als Parameter für die length-Funktion bereitgestellt.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## Verwenden des Indexwerts in Namen

Sie können den copy-Vorgang verwenden, um mehrere Instanzen einer Ressource mit einem eindeutigen Namen basierend auf dem inkrementellen Index zu erstellen. Es kann beispielsweise sein, dass Sie am Ende des Namens jeder bereitgestellten Ressource eine eindeutige Zahl hinzufügen möchten. So stellen Sie drei Websites mit den folgenden Namen bereit:

- examplecopy-0
- examplecopy-1
- examplecopy-2

Verwenden Sie die folgende Vorlage:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Versetzen des Indexwerts

Im obigen Beispiel wird Ihnen auffallen, dass der Indexwert von 0 bis 2 reicht. Zum Versetzen des Indexwerts können Sie einen Wert in der **copyIndex()**-Funktion übergeben, z. B. **copyIndex(1)**. Die Anzahl von durchzuführenden Durchläufen wird weiterhin im copy-Element angegeben, aber der Wert von copyIndex wird um den angegebenen Wert versetzt. Wenn Sie dieselbe Vorlage wie im vorherigen Beispiel verwenden, dieses Mal aber **copyIndex(1)** angeben, werden also drei Websites mit den folgenden Namen bereitgestellt:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Verwenden mit Arrays
   
Der copy-Vorgang ist besonders bei Verwendung von Arrays hilfreich, weil Sie jedes Element im Array durchlaufen können. So stellen Sie drei Websites mit den folgenden Namen bereit:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

Verwenden Sie die folgende Vorlage:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {} 
      } 
    ]

Natürlich legen Sie die copy-Anzahl auf einen anderen Wert fest als die Länge des Arrays. Sie können beispielsweise ein Array mit vielen Werten erstellen und dann einen Parameterwert übergeben, der festlegt, wie viele der Arrayelemente bereitgestellt werden sollen. In diesem Fall legen Sie die copy-Anzahl wie im ersten Beispiel gezeigt fest.

## Nächste Schritte
- Informationen zu den Abschnitten einer Vorlage finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](./resource-group-authoring-templates.md).
- Unter [Funktionen von Azure-Ressourcen-Manager-Vorlagen](./resource-group-template-functions.md) finden Sie alle Funktionen, die Sie in einer Vorlage verwenden können.
- Informationen zum Bereitstellen Ihrer Vorlage finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](azure-portal/resource-group-template-deploy.md).

<!---HONumber=September15_HO1-->