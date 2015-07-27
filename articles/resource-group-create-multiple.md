<properties
   pageTitle="Erstellen von mehreren Instanzen von Ressourcen"
   description="Beschreibt, wie Sie den copy-Vorgang in einer Azure-Ressourcen-Manager-Vorlage verwenden, um sie beim Bereitstellen von Ressourcen mehrere Male zu durchlaufen."
   services="na"
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
   ms.date="07/14/2015"
   ms.author="tomfitz"/>

# Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager

In diesem Thema erfahren Sie, wie Sie die Azure-Ressourcen-Manager-Vorlage durchlaufen können, um mehrere Instanzen einer Ressource zu erstellen.

## copy und copyIndex()

Um innerhalb der Ressource mehrere Instanzen zu erstellen, können Sie ein **copy**-Objekt definieren, das die Anzahl der Iterationen angibt. Die Kopie hat das folgende Format:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Mit der **copyIndex()**-Funktion können Sie den aktuellen Iterationswert abrufen, wie nachfolgend in der Verkettungsfunktion gezeigt.

    [concat('examplecopy-', copyIndex())]

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
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
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
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Nächste Schritte
- [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md) (Erstellen von Vorlagen im Azure-Ressourcen-Manager)
- [Azure Resource Manager Template Functions](./resource-group-template-functions.md) (Vorlagenfunktionen im Azure-Ressourcen-Manager)
- [Deploy an application with Azure Resource Manager Template](azure-portal/resource-group-template-deploy.md) (Bereitstellen einer Anwendung mit einer Vorlage im Azure-Ressourcen-Manager)

<!---HONumber=July15_HO3-->