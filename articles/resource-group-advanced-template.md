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
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Erweiterte Anwendungen mit Vorlagen

In diesem Thema werden der Kopiervorgang und geschachtelte Vorlagen beschrieben, die Sie verwenden können, um komplexere Aufgaben auszuführen, wenn Sie Ressourcen in Azure bereitstellen.

## copy

Ermöglicht Iterationen innerhalb eines Arrays und das Verwenden aller Elemente beim Bereitstellen von Ressourcen.
   
Im folgenden Beispiel werden drei Websites mit den Namen „examplecopy-Contoso“, „examplecopy-Fabrikam“ und „examplecopy-Coho“ bereitgestellt.

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

## Geschachtelte Vorlage

Zuweilen ist es erforderlich, zwei Vorlagen zusammenzuführen oder anhand einer übergeordneten Vorlage eine neue untergeordnete Vorlage einzuführen. Dies kann durch Nutzung einer Bereitstellungsressource innerhalb der Mastervorlage geschehen, um eine untergeordnete Vorlage bereitzustellen. Sie stellen die URI der geschachtelten Vorlage bereit wie unten dargestellt.

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Nächste Schritte
- [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md)(Erstellen von Vorlagen im Azure-Ressourcen-Manager)
- [Azure Resource Manager Template Functions](./resource-group-template-functions.md)(Vorlagenfunktionen im Azure-Ressourcen-Manager)
- [Deploy an application with Azure Resource Manager Template](./resouce-group-template-deploy.md)(Bereitstellen einer Anwendung mit einer Vorlage im Azure-Ressourcen-Manager)
- [Azure Resource Manager Overview](./resource-group-overview.md)(Übersicht über den Azure-Ressourcen-Manager)

<!--HONumber=52-->
