<properties
   pageTitle="Erweiterte Anwendungen mit Vorlagen im Azure-Ressourcen-Manager"
   description="Hierin wird beschrieben, wie geschachtelte Vorlagen und der Kopiervorgang in einer Vorlage des Azure-Ressourcen-Managers verwendet werden, um Anwendungen in Azure bereitzustellen."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# Erweiterte Anwendungen mit Vorlagen

In diesem Thema werden der Kopiervorgang und geschachtelte Vorlagen beschrieben, die Sie verwenden können, um komplexere Aufgaben auszuführen, wenn Sie Ressourcen in Azure bereitstellen.

## copy

Ermöglicht Ihnen beim Bereitstellen einer Ressource eine angegebene Anzahl von Durchläufen.
   
Der copy-Vorgang ist besonders bei Verwendung von Arrays hilfreich, weil Sie jedes Element im Array durchlaufen können. Die **copyIndex()**-Funktion gibt den aktuellen Wert für den Durchlauf zurück. Sie können drei Websites mit den folgenden Namen bereitstellen:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

Hierfür verwenden Sie die folgende Vorlage.

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

Sie können den copy-Vorgang auch ohne Array verwenden. Es kann beispielsweise sein, dass Sie am Ende des Namens jeder bereitgestellten Ressource eine Inkrementzahl hinzufügen möchten. Sie können drei Websites mit den folgenden Namen bereitstellen:

- examplecopy-0
- examplecopy-1
- examplecopy-2

Hierfür verwenden Sie die folgende Vorlage.

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

Im obigen Beispiel wird Ihnen auffallen, dass der Indexwert von 0 bis 2 reicht. Zum Versetzen des Indexwerts können Sie einen Wert in der **copyIndex()**-Funktion übergeben, z. B. **copyIndex(1)**. Die Anzahl von durchzuführenden Durchläufen wird weiterhin im copy-Element angegeben, aber der Wert von copyIndex wird um den angegebenen Wert versetzt. Wenn Sie dieselbe Vorlage wie im vorherigen Beispiel verwenden, dieses Mal aber **copyIndex(1)** angeben, werden also drei Websites mit den folgenden Namen bereitgestellt:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Geschachtelte Vorlage

Es kann erforderlich sein, zwei Vorlagen zusammenzuführen oder anhand einer übergeordneten Vorlage eine neue untergeordnete Vorlage einzuführen. Dies kann durch Nutzung einer Bereitstellungsressource innerhalb der Mastervorlage geschehen, die auf die geschachtelte Vorlage zeigt. Sie legen die **templateLink**-Eigenschaft für den URI der geschachtelten Vorlage fest. Für die geschachtelte Vorlage können Sie Parameterwerte angeben, indem Sie die Werte entweder direkt in der Vorlage oder per Verknüpfung mit einer Parameterdatei angeben. Im ersten Beispiel wird die **parameters**-Eigenschaft verwendet, um einen Parameterwert direkt anzugeben.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Im nächsten Beispiel wird die **parametersLink**-Eigenschaft genutzt, um eine Verknüpfung mit einer Parameterdatei herzustellen.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Nächste Schritte
- [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md) (Erstellen von Vorlagen im Azure-Ressourcen-Manager)
- [Azure Resource Manager Template Functions](./resource-group-template-functions.md) (Vorlagenfunktionen im Azure-Ressourcen-Manager)
- [Deploy an application with Azure Resource Manager Template](azure-portal/resource-group-template-deploy.md) (Bereitstellen einer Anwendung mit einer Vorlage im Azure-Ressourcen-Manager)
- [Übersicht über den Azure Resource Manager](./resource-group-overview.md)

<!---HONumber=July15_HO3-->