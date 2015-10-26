<properties
	pageTitle="Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit der Azure-CLI | Microsoft Azure"
	description="Erfahren Sie, wie Sie den rollenbasierten Zugriff (RBAC) mit der Azure-Befehlszeilenschnittstelle verwalten, indem Sie Rollen und Rollenaktionen auflisten und Rollen im Kontext von Abonnements und Anwendungen zuweisen."
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

# Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit der Azure-Befehlszeilenschnittstelle (CLI)
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Auflisten von RBAC-Rollen
###	Auflisten aller verfügbarer Rollen
Um alle verfügbaren Rollen aufzulisten, verwenden Sie Folgendes:

		azure role list

Das folgende Beispiel zeigt die Liste *aller verfügbaren Rollen*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Liste der Aktionen einer Rolle
Um die Aktionen einer Rolle aufzulisten, verwenden Sie Folgendes:

    azure role show <role in quotes>

Das folgende Beispiel zeigt die Aktionen der Rollen *Contributor* und *Virtual Machine Contributor*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Auflisten des Zugriffs
###	Auflisten der effektiven Rollenzuweisungen für eine Ressourcengruppe
Um die effektiven Rollenzuweisungen für eine Ressourcengruppe aufzulisten, verwenden Sie Folgendes:

    azure role assignment list --resource-group <resource group name>

Das folgende Beispiel zeigt die Rollenzuweisungen für die Gruppe *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Auflisten von Rollenzuweisungen für einen Benutzer, einschließlich derer, die den Gruppen des Benutzers zugewiesen sind

Das folgende Beispiel zeigt die Rollenzuweisungen für die Gruppe *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Gewähren von Zugriff
Nachdem Sie die Rolle, die Sie zuweisen möchten, identifiziert haben, verwenden Sie Folgendes, um den Zugriff zu gewähren:

    azure role assignment create

###	Zuweisen einer Rolle zu einer Gruppe im Abonnementkontext
Um einer Gruppe im Abonnementkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

   azure role assignment create --objId <Objekt-ID der Gruppe> --role <name of role> --scope <Abonnement/Abonnement-ID>

Das folgende Beispiel weist die Rolle *Reader* dem *Christine Koch Team* im *Abonnementkontext* zu.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Zuweisen einer Rolle zu einer Anwendung im Abonnementkontext
Um einer Anwendung im Abonnementkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

Das folgende Beispiel gewährt einer *Azure AD*-Anwendung für das ausgewählte Abonnement die Rolle *Contributor*.

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Zuweisen einer Rolle zu einem Benutzer im Ressourcengruppenkontext
Um einem Benutzer im Ressourcengruppenkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

Das folgende Beispiel gewährt dem Benutzer **samert@aaddemo.com* im Kontext der Ressourcengruppe *Pharma-Sales-ProjectForcast* die Rolle *Virtual Machine Contributor*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Zuweisen einer Rolle zu einer Gruppe im Ressourcenkontext
Um einer Gruppe im Ressourcenkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

Das folgende Beispiel gewährt einer *Azure AD*-Gruppe in einem *Subnetz* die Rolle *Virtual Machine Contributor*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Zugriff entfernen
Um eine Rollenzuordnung zu entfernen, verwenden Sie Folgendes:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

Das folgende Beispiel entfernt die Zuweisung der Rolle *Virtual Machine Contributor* von **sammert@aaddemo.com* in der Ressourcengruppe *Pharma-Sales-ProjectForcast*. Anschließend wird die Rollenzuweisung aus einer Gruppe im Abonnement entfernt.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## RBAC-Themen
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->