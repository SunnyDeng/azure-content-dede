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
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Auflisten von RBAC-Rollen
### Auflisten aller verfügbaren Rollen
Zum Auflisten von RBAC-Rollen für die Zuweisung und Überprüfung der Vorgänge, auf die sie Zugriff gewähren, verwenden Sie Folgendes:

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Auflisten der Aktionen einer Rolle
Zum Auflisten der Aktionen für eine bestimmte Rolle verwenden Sie Folgendes:

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Auflisten des Zugriffs
### Auflisten aller Rollenzuweisungen im ausgewählten Abonnement
Zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement, in der angegebenen Ressource oder Ressourcengruppe wirksam sind, verwenden Sie Folgendes:

    Get-AzureRoleAssignment

###	Auflisten der effektiven Rollenzuweisungen für eine Ressourcengruppe
Zum Auflisten der Zugriffszuweisungen für eine Ressourcengruppe verwenden Sie Folgendes:

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Auflisten von Rollenzuweisungen für einen Benutzer, einschließlich derer, die Benutzergruppen zugewiesen sind
Zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie der Gruppen, denen der Benutzer angehört, verwenden Sie Folgendes:

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Auflisten der klassischen Rollenzuweisungen für die Dienstadministrator- und Co-Administrator-Rolle
Zum Auflisten der Zugriffszuweisungen für die klassischen Abonnementadministratoren und -Co-Administratoren verwenden Sie Folgendes:

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## Erteilen von Zugriff
### Suchen nach Objekt-IDs
Wenn Sie die folgenden Befehlssequenzen verwenden möchten, müssen Sie zuerst die Objekt-IDs suchen. Dabei wird vorausgesetzt, dass Sie die Abonnement-ID des verwendeten Abonnements bereits kennen. Andernfalls finden Sie weitere Informationen unter [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) auf MSDN.

#### Suchen der Objekt-ID für eine Azure AD-Gruppe
Zum Abrufen der Objekt-ID für eine Azure AD-Gruppe verwenden Sie Folgendes:

    Get-AzureADGroup -SearchString <group name in quotes>

#### Suchen der Objekt-ID für einen Azure AD-Dienstprinzipal
Zum Abrufen der Objekt-ID für einen Azure AD-Dienstprinzipal verwenden Sie Folgendes: Get-AzureADServicePrincipal -SearchString <service name in quotes>

### Zuweisen einer Rolle zu einer Gruppe im Abonnementkontext
Zum Zuweisen des Zugriffs zu einer Gruppe im Abonnementkontext verwenden Sie Folgendes:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Zuweisen einer Rolle zu einer Anwendung im Abonnementkontext
Zum Zuweisen des Zugriffs zu einer Anwendung im Abonnementkontext verwenden Sie Folgendes:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Zuweisen einer Rolle zu einem Benutzer im Ressourcengruppenkontext
Zum Zuweisen des Zugriffs zu einem Benutzer im Ressourcengruppenkontext verwenden Sie Folgendes:

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Zuweisen einer Rolle zu einer Gruppe im Ressourcenkontext
Zum Zuweisen des Zugriffs zu einer Gruppe im Ressourcenkontext verwenden Sie Folgendes:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Zugriff entfernen
Zum Entfernen des Zugriffs für Benutzer, Gruppen und Anwendungen verwenden Sie Folgendes:

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## RBAC-Themen
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->