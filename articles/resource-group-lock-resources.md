<properties 
	pageTitle="Sperren von Ressourcen mit dem Ressourcen-Manager | Microsoft Azure" 
	description="Verhindern Sie, dass Benutzer bestimmte Ressourcen aktualisieren oder löschen, indem Sie eine Einschränkung auf alle Benutzer und Rollen anwenden." 
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
	ms.date="01/21/2016" 
	ms.author="tomfitz"/>

# Sperren von Ressourcen mit dem Azure-Ressourcen-Manager

Als Administrator gibt es Szenarien, in denen Sie ein Abonnement, eine Ressource oder eine Ressourcengruppe sperren möchten, um zu verhindern, dass andere Benutzer in Ihrer Organisation versehentlich eine wichtige Ressource löschen.

Azure-Ressourcen-Manager bietet die Möglichkeit, Vorgänge für Ressourcen mithilfe von Ressourcenverwaltungssperren zu beschränken. Sperren sind Richtlinien, die eine Sperrebene für einen bestimmten Bereich erzwingen. Bei dem Bereich kann es sich um ein Abonnement, die Ressourcengruppe oder die Ressource handeln. Die Sperrebene identifiziert den Typ der Erzwingung für die Richtlinie, der derzeit auf **CanNotDelete** festgelegt werden kann. **CanNotDelete** bedeutet, dass autorisierte Benutzer weiterhin Ressourcen lesen und ändern können, aber sie können keine eingeschränkten Ressourcen löschen.

Sperren unterscheiden sich von der Verwendung der rollenbasierten Zugriffssteuerung in Bezug auf die Zuweisung von Benutzerberechtigungen zur Durchführung bestimmter Aktionen. Informationen zum Festlegen von Benutzer- und Rollenberechtigungen finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md). Im Gegensatz zur rollenbasierten Zugriffssteuerung verwenden Sie Verwaltungssperren, um eine Einschränkung über alle Benutzer und Rollen hinweg anzuwenden, und meist wenden Sie die Sperren nur für eine begrenzte Dauer an.

Wenn Sie eine Sperre in einem übergeordneten Bereich anwenden, erben alle untergeordneten Ressourcen diese Sperre.

## Häufige Szenarios

Ein häufiges Szenario ist eine Ressourcengruppe mit Ressourcen, die in einem Aus-und-An-Muster verwendet werden. VM-Ressourcen werden in regelmäßigen Abständen aktiviert, um Daten für ein angegebenes Zeitintervall zu verarbeiten, und dann wieder deaktiviert. In diesem Szenario möchten Sie das Herunterfahren der virtuellen Computer ermöglichen, ohne dabei jedoch ein Speicherkonto zu löschen. In diesem Szenario verwenden Sie für das Speicherkonto eine Ressourcensperre mit der Sperrebene **CanNotDelete**.

## Personen in Ihrer Organisation, die Sperren erstellen oder löschen können

Zum Erstellen oder Löschen von Verwaltungssperren müssen Sie Zugriff auf Aktionen vom Typ **Microsoft.Authorization/*** oder **Microsoft.Authorization/locks/*** haben. Von den integrierten Rollen verfügen nur **Owner** (Besitzer) und **User Access Administrator** (Benutzerzugriffsadministrator) über diese Aktionen. Weitere Informationen zum Zuweisen der Zugriffssteuerung finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md).

## Erstellen einer Sperre in einer Vorlage

Das folgende Beispiel zeigt eine Vorlage, mit der eine Sperre für ein Speicherkonto erstellt wird. Das Speicherkonto, für das die Sperre gelten soll, wird als Parameter bereitgestellt und in Verbindung mit der concat()-Funktion verwendet. Das Ergebnis ist der Ressourcenname, der auf „Microsoft.Authorization“ endet und an den der Name der Sperre angefügt wird. In diesem Fall ist dies **myLock**.

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

Bei dem Bereich kann es sich um ein Abonnement, die Ressourcengruppe oder die Ressource handeln. Geben Sie für "lock-name" den jeweiligen Namen der Sperre ein. Verwenden Sie als „api-version“ die Einstellung **2015-01-01**.

Schließen Sie in die Anforderung ein JSON-Objekt ein, das die Eigenschaften für die Sperre angibt.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Geben Sie für „lock-level“ die Option **CanNotDelete** an.

Beispiele finden Sie unter [REST-API für Verwaltungssperren](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Erstellen einer Sperre mit Azure PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Sie können bereitgestellte Ressourcen mit Azure PowerShell sperren, indem Sie wie unten gezeigt das **New-AzureRmResourceLock**-Cmdlet verwenden. Mithilfe von PowerShell können Sie **LockLevel** nur auf **CanNotDelete** festlegen.

    PS C:\> New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure PowerShell bietet auch andere Befehle für die Arbeit mit Sperren, z. B. **Set-AzureRmResourceLock**, um eine Sperre zu aktualisieren, und **Remove-AzureRmResourceLock**, um eine Sperre zu löschen.

## Nächste Schritte

- Weitere Informationen zum Arbeiten mit Ressourcensperren finden Sie unter [Sperren Ihrer Azure-Ressourcen](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx).
- Informationen zum logischen Organisieren von Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Ressourcen](resource-group-using-tags.md).
- Informationen dazu, wie Sie die Ressourcengruppe für eine Ressource ändern, finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe](resource-group-move-resources.md).
- Sie können mithilfe benutzerdefinierter Richtlinien Einschränkungen und Konventionen für Ihr Abonnement festlegen. Weitere Informationen finden Sie unter [Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung](resource-manager-policy.md).

<!---HONumber=AcomDC_0128_2016-->