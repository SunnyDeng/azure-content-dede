<properties
	pageTitle="Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit Azure PowerShell | Microsoft Azure"
	description="Erläutert das Verwalten von RBAC mit Azure PowerShell einschließlich Auflisten von Rollen, Zuweisen von Rollen und Löschen von Rollenzuweisungen."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="12/04/2015"
	ms.author="inhenk"/>

# Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Auflisten von RBAC-Rollen
### Auflisten aller verfügbaren Rollen
Zum Auflisten von RBAC-Rollen für die Zuweisung und Überprüfung der Vorgänge, auf die sie Zugriff gewähren, verwenden Sie Folgendes:

		Get-AzureRmRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Auflisten der Aktionen einer Rolle
Zum Auflisten der Aktionen für eine bestimmte Rolle verwenden Sie Folgendes:

    Get-AzureRmRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Auflisten des Zugriffs
### Auflisten aller Rollenzuweisungen im ausgewählten Abonnement
Zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement, in der angegebenen Ressource oder Ressourcengruppe wirksam sind, verwenden Sie Folgendes:

    Get-AzureRmRoleAssignment

###	Auflisten der effektiven Rollenzuweisungen für eine Ressourcengruppe
Zum Auflisten der Zugriffszuweisungen für eine Ressourcengruppe verwenden Sie Folgendes:

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Auflisten von Rollenzuweisungen für einen Benutzer, einschließlich derer, die Benutzergruppen zugewiesen sind
Zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie der Gruppen, denen der Benutzer angehört, verwenden Sie Folgendes:

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Auflisten der klassischen Rollenzuweisungen für die Dienstadministrator- und Co-Administrator-Rolle
Zum Auflisten der Zugriffszuweisungen für die klassischen Abonnementadministratoren und -Co-Administratoren verwenden Sie Folgendes:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Erteilen von Zugriff
### Suchen nach Objekt-IDs
Wenn Sie die folgenden Befehlssequenzen verwenden möchten, müssen Sie zuerst die Objekt-IDs suchen. Dabei wird vorausgesetzt, dass Sie die Abonnement-ID des verwendeten Abonnements bereits kennen. Andernfalls finden Sie weitere Informationen unter [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) auf MSDN.

#### Suchen der Objekt-ID für eine Azure AD-Gruppe
Zum Abrufen der Objekt-ID für eine Azure AD-Gruppe verwenden Sie Folgendes:

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Suchen der Objekt-ID für einen Azure AD-Dienstprinzipal
Zum Abrufen der Objekt-ID für einen Azure AD-Dienstprinzipal verwenden Sie Folgendes: Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Zuweisen einer Rolle zu einer Gruppe im Abonnementkontext
Zum Zuweisen des Zugriffs zu einer Gruppe im Abonnementkontext verwenden Sie Folgendes:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Zuweisen einer Rolle zu einer Anwendung im Abonnementkontext
Zum Zuweisen des Zugriffs zu einer Anwendung im Abonnementkontext verwenden Sie Folgendes:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Zuweisen einer Rolle zu einem Benutzer im Ressourcengruppenkontext
Zum Zuweisen des Zugriffs zu einem Benutzer im Ressourcengruppenkontext verwenden Sie Folgendes:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Zuweisen einer Rolle zu einer Gruppe im Ressourcenkontext
Zum Zuweisen des Zugriffs zu einer Gruppe im Ressourcenkontext verwenden Sie Folgendes:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Zugriff entfernen
Zum Entfernen des Zugriffs für Benutzer, Gruppen und Anwendungen verwenden Sie Folgendes:

    Remove-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Erstellen einer benutzerdefinierten Rolle
Verwenden Sie zum Erstellen einer benutzerdefinierten Rolle den Befehl `New-AzureRmRoleDefinition`.

Das folgende Beispiel erstellt eine benutzerdefinierte Rolle namens *Virtual Machine Operator*, die Zugriff auf alle Lesevorgänge der Ressourcenanbieter *Microsoft.Compute*, *Microsoft.Storage* und *Microsoft.Network* und zum Starten, Neustarten und Überwachen virtueller Computer gewährt. Die benutzerdefinierte Rolle kann in zwei Abonnements verwendet werden.

![](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Ändern einer benutzerdefinierten Rolle
Um eine benutzerdefinierte Rolle zunächst zu ändern, verwenden Sie den Befehl `Get-AzureRmRoleDefinition`, um die Rollendefinition abzurufen. Nehmen Sie dann die gewünschten Änderungen an der Rollendefinition vor. Verwenden Sie abschließend den Befehl `Set-AzureRmRoleDefinition`, um die geänderte Rollendefinition zu speichern.

Im folgenden Beispiel wird der Vorgang `Microsoft.Insights/diagnosticSettings/*` zur benutzerdefinierten Rolle *Virtual Machine Operator* hinzugefügt.

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

Im folgenden Beispiel wird ein Azure-Abonnement den zuweisbaren Bereichen der benutzerdefinierten Rolle „Virtual Machine Operator“ hinzugefügt.

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Löschen einer benutzerdefinierten Rolle

Verwenden Sie zum Löschen einer benutzerdefinierten Rolle den Befehl `Remove-AzureRmRoleDefinition`.

Im folgenden Beispiel wird die benutzerdefinierte Rolle *Virtual Machine Operator* entfernt.

![](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Auflisten benutzerdefinierter Rollen
Zum Auflisten der Rollen, die in einem Bereich für die Zuweisung verfügbar sind, wählen Sie den Befehl `Get-AzureRmRoleDefinition`.

Im folgenden Beispiel werden alle Rollen aufgelistet, die für die Zuweisung im ausgewählten Abonnement verfügbar sind.

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

Im folgenden Beispiel ist die benutzerdefinierte Rolle *Virtual Machine Operator* im Abonnement *Production4* nicht verfügbar, da dieses Abonnement nicht in **AssignableScopes** für die Rolle enthalten ist.

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## RBAC-Themen
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_1210_2015-->