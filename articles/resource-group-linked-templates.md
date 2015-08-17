<properties
   pageTitle="Verwenden von verknüpften Vorlagen mit Azure-Ressourcen-Manager"
   description="Beschreibt, wie verknüpfte Vorlagen in einer Azure-Ressourcen-Manager-Vorlage zum Erstellen einer modularen Vorlagenprojektmappe verwendet werden. Zeigt, wie Parameterwerte übergeben, eine Parameterdatei festgelegt und URLs dynamisch erstellt werden."
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

# Verwenden von verknüpften Vorlagen mit Azure-Ressourcen-Manager

Aus einer Azure-Ressourcen-Manager-Vorlage heraus können Sie einen Link zu einer anderen Vorlage herstellen, sodass Sie die Bereitstellung in eine Reihe von ausgewählten und zweckgebundenen Vorlagen zerlegen können. Wie das Zerlegen einer Anwendung in eine Reihe von Codeklassen bietet diese Zerlegung Vorteile in Bezug auf Tests, Wiederverwendung und Lesbarkeit.

Sie können Parameter aus einer Hauptvorlage an eine verknüpfte Vorlage übergeben, und diese Parameter können direkt Parametern oder Variablen zugeordnet werden, die von der aufrufenden Vorlage verfügbar gemacht werden. Die verknüpfte Vorlage kann auch eine Ausgabevariable zurück an die Quellvorlage übergeben, wodurch ein bidirektionaler Datenaustausch zwischen Vorlagen ermöglicht wird.

## Verknüpfen mit einer Vorlage

Sie erstellen einen Link zwischen zwei Vorlagen durch Hinzufügen einer Bereitstellungsressource innerhalb der Hauptvorlage, die auf die verknüpfte Vorlage verweist. Sie legen die **templateLink**-Eigenschaft für den URI der verknüpften Vorlage fest. Für die verknüpfte Vorlage können Sie Parameterwerte angeben, indem Sie die Werte entweder direkt in der Vorlage oder per Verknüpfung mit einer Parameterdatei angeben. Im folgenden Beispiel wird die **parameters**-Eigenschaft verwendet, um direkt einen Parameterwert anzugeben.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Verknüpfen mit einer Parameterdatei

Im nächsten Beispiel wird die **parametersLink**-Eigenschaft genutzt, um eine Verknüpfung mit einer Parameterdatei herzustellen.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Verwenden von Variablen für das Verknüpfen von Vorlagen

Die vorherigen Beispiele zeigen hartcodierte URL-Werte für die Vorlagenlinks. Dieser Ansatz funktioniert u. U. für eine einfache Vorlage, aber er funktioniert nicht gut bei der Arbeit mit einer großen Anzahl von modularen Vorlagen. Stattdessen können Sie eine statische Variable erstellen, die eine Basis-URL für die Hauptvorlage speichert, und dann aus dieser Basis-URL dynamisch URLs für die verknüpften Vorlagen erstellen. Der Vorteil dieses Ansatzes besteht darin, dass Sie die Vorlage problemlos verschieben oder verzweigen können, da Sie nur die statische Variable in der Hauptvorlage ändern müssen. Die Hauptvorlage übergibt die richtigen URIs an die zerlegten Vorlagen.

Das folgende Beispiel zeigt, wie Sie eine Basis-URL verwenden können, um zwei URLs für verknüpfte Vorlagen (**sharedTemplateUrl** und **vmTemplate**) zu erstellen.

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

## Zurückgeben von Werten aus einer verknüpften Vorlage

Wenn Sie einen Wert aus der verknüpften Vorlage an die Hauptvorlage übergeben müssen, können Sie einen Wert im **outputs**-Abschnitt der verknüpften Vorlage erstellen. Ein Beispiel finden Sie unter [Freigeben des Status in Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-state.md).

## Nächste Schritte
- [Erstellen von Vorlagen](./resource-group-authoring-templates.md)
- [Bereitstellen von Vorlagen](azure-portal/resource-group-template-deploy.md)

<!---HONumber=August15_HO6-->