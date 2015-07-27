<properties 
	pageTitle="Sperren von Ressourcen mit dem Azure-Ressourcen-Manager" 
	description="Sperren Sie Ressourcen, um zu verhindern, dass Benutzer bestimmte Ressourcen aktualisieren oder löschen." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Sperren von Ressourcen mit dem Azure-Ressourcen-Manager

Als Administrator gibt es Szenarien, in denen Sie eine Ressource oder Ressourcengruppe sperren möchten, um zu verhindern, dass andere Benutzer in Ihrer Organisation Schreibvorgänge durchführen oder versehentlich eine wichtige Ressource löschen.

Azure-Ressourcen-Manager bietet die Möglichkeit, Vorgänge für Ressourcen mithilfe von Ressourcenverwaltungssperren zu beschränken. Ressourcensperren sind Richtlinien, die eine Sperrebene für einen bestimmten Bereich erzwingen. Die Sperrebene identifiziert den Typ der Erzwingung für die Richtlinie, der derzeit zwei Werte haben kann: **CanNotDelete** und **ReadOnly**. Der Bereich kann wird als URI ausgedrückt und kann entweder eine Ressource oder eine Ressourcengruppe sein.

Sperren unterscheiden sich vom Zuweisen von Benutzerberechtigungen zum Ausführen bestimmter Aktionen. Weitere Informationen zum Festlegen von Berechtigungen für Benutzer und Rollen finden Sie unter [Rollenbasierte Zugriffssteuerung über das Vorschauportal](role-based-access-control-configure.md) und [Verwalten und Überwachen des Zugriffs auf Ressourcen](resource-group-rbac.md).

## Häufige Szenarien

Ein häufiges Szenario ist eine Ressourcengruppe mit Ressourcen, die in einem Aus-und-An-Muster verwendet werden. VM-Ressourcen werden in regelmäßigen Abständen aktiviert, um Daten für ein angegebenes Zeitintervall zu verarbeiten, und dann wieder deaktiviert. In diesem Szenario möchten Sie das Herunterfahren der virtuellen Computer ermöglichen, ohne dabei jedoch ein Speicherkonto zu löschen. In diesem Szenario verwenden Sie für das Speicherkonto eine Ressourcensperre mit der Sperrebene **CanNotDelete**.

In einem anderen Szenario gibt es in Ihrem Unternehmen möglicherweise Zeiträume, in denen keine Updates in der Produktionsumgebung durchgeführt werden sollen. Mit der Sperrebene **ReadOnly** können Sie die Erstellung oder Updates stoppen. Als Einzelhandelsunternehmen möchten Sie womöglich verhindern, dass während der Einkaufsperiode rund um die Feiertage Updates durchgeführt werde. Als Finanzdienstleister gelten bei Ihnen möglicherweise Einschränkungen in Bezug auf Bereitstellungen während bestimmter Marktstunden. Eine Ressourcensperre kann eine Richtlinie bereitstellen, um die Ressourcen je nach Bedarf zu sperren. Diese kann für bestimmte Ressourcen oder die gesamte Ressourcengruppe angewendet werden.

## Erstellen eine Sperre in einer Vorlage

Das folgende Beispiel zeigt eine Vorlage, mit der eine Sperre für ein Speicherkonto erstellt wird. Das Speicherkonto, für das die Sperre gelten soll, wird als Parameter bereitgestellt und in Verbindung mit der concat()-Funktion verwendet. Das Ergebnis ist der Ressourcenname, der auf "Microsoft.Authorization" endet und an den der Name der Sperre angefügt wird, in diesem Fall **myLock**.

Der bereitgestellte Typ ist Ressourcentyp-spezifisch. Für Speicher ist dies der Typ "Microsoft.Storage/storageaccounts/providers/locks".

Die Bereichsebene wird mit der **level**-Eigenschaft der Ressource festgelegt. Da es in dem Beispiel darum geht, ein versehentliches Löschen zu verhindern, wird die Ebene auf **CannotDelete** festgelegt.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "lockedResource": {
                "type": "string"
            }
        },
        "resources": [
            {
                "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
                "type": "Microsoft.Storage/storageAccounts/providers/locks",
                "apiVersion": "2015-01-01",
                "properties": {
	                "level": "CannotDelete"
                }
            }
        ]
    }

## Erstellen einer Sperre mit der REST-API

Sie können bereitgestellte Ressourcen mit der [REST-API für Verwaltungssperren](https://msdn.microsoft.com/library/azure/mt204563.aspx) sperren. Die REST-API ermöglicht es Ihnen, Sperren zu erstellen und zu löschen sowie Informationen zu vorhandenen Sperren abzurufen.

Führen Sie zum Erstellen einer Sperre Folgendes durch:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Bei dem Bereich kann es sich um ein Abonnement, die Ressourcengruppe oder die Ressource handeln. Geben Sie für "lock-name" den jeweiligen Namen der Sperre ein. Verwenden Sie für "api-version" **2015-01-01**.

Schließen Sie in die Anforderung ein JSON-Objekt ein, das die Eigenschaften für die Sperre angibt.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Geben Sie für "lock-level" entweder **CanNotDelete** oder **ReadOnly** an.

Beispiele finden Sie in unter [REST-API für Verwaltungssperren](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Erstellen einer Sperre mit Azure PowerShell

Sie können bereitgestellte Ressourcen mit Azure PowerShell sperren, indem Sie wie unten gezeigt das **New-AzureResourceLock**-Cmdlet verwenden.

    PS C:> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

PowerShell bietet auch andere Befehle für die Arbeit mit Sperren, z. B. **Set-AzureResourceLock**, um eine Sperre zu aktualisieren, und **Remove-AzureResourceLock**, um eine Sperre zu löschen.

## Nächste Schritte

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](resource-group-using-tags.md)
- [Verschieben von Ressourcen in eine neue Ressourcengruppe](resource-group-move-resources.md)

<!---HONumber=July15_HO3-->