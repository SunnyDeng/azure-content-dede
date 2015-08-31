<properties
	pageTitle="Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell"
	description="Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell"
	services="azure-portal"
	documentationCenter="na"
	authors="IHenkel"
	manager="stevenpo"
	editor="mollybos"/>

<tags
	ms.service="azure-portal"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="inhenk"/>

# Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](role-based-access-control-xplat-cli.md)


Mit der rollenbasierten Zugriffssteuerung (RBAC) im Azure-Portal und in der Azure Resource Management-API können Sie den Zugriff auf Ihr Abonnement differenziert steuern. Mithilfe dieser Funktion lassen sich Zugriffsberechtigungen für Active Directory-Benutzer, -Gruppen oder -Dienstprinzipale festlegen, indem ihnen bestimmte Rollen für einen bestimmten Bereich zugewiesen werden.

In diesem Lernprogramm erfahren Sie, wie Sie mit Windows PowerShell die rollenbasierte Zugriffssteuerung verwalten. Das Lernprogramm beschreibt die Erstellung und Überprüfung von Rollenzuweisungen.

**Geschätzter Zeitaufwand**: 15 Minuten.

## Voraussetzungen

Bevor Sie die RBAC mithilfe der Windows PowerShell verwalten können, benötigen Sie Folgendes:

- Windows PowerShell, Version 3.0 oder 4.0. Um die Version von Windows PowerShell zu ermitteln, geben Sie `$PSVersionTable` ein. Vergewissern Sie sich, dass der Wert von `PSVersion` 3.0 oder 4.0 ist. Informationen zum Installieren einer kompatiblen Version finden Sie unter [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- Azure PowerShell, Version 0.8.8 oder höher. Um die neueste Version zu installieren und sie Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

Dieses Lernprogramm richtet sich an Windows PowerShell-Anfänger. Es wird aber vorausgesetzt, dass Sie die grundlegenden Konzepte, wie zum Beispiel Module, Cmdlets und Sitzungen, verstehen. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Um detaillierte Hilfe zu einem Cmdlet aus dem Lernprogramm zu erhalten, verwenden Sie das Get-Help-Cmdlet.

	Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise Folgendes ein, um Hilde zum Add-AzureAccount-Cmdlet zu erhalten:

	Get-Help Add-AzureAccount -Detailed

Lesen Sie bitte auch die folgenden Lernprogramme, um sich mit der Einrichtung und Verwendung von Azure Resource Manager in Windows PowerShell vertraut zu machen:

- [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)
- [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](../powershell-azure-resource-manager.md)


## Verbindungsherstellung mit Ihren Abonnements

Da RBAC nur mit Azure Resource Manager funktioniert, müssen Sie zunächst in den Azure Resource Manager-Modus wechseln. Geben Sie dazu Folgendes ein:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../powershell-azure-resource-manager.md).

Um eine Verbindung mit Ihren Azure-Abonnements herzustellen, geben Sie Folgendes ein:

    PS C:\> Add-AzureAccount

Geben Sie in das Popup-Browsersteuerelement den Benutzernamen und das Kennwort Ihres Azure-Kontos ein. PowerShell ruft alle Abonnements ab, die mit diesem Konto verknüpft sind, und konfiguriert sich selbst so, dass das erste Konto als Standardkonto verwendet wird. Beachten Sie, dass Sie mit der rollenbasierten Zugriffssteuerung nur Abonnements abrufen können, für die Sie entweder als Co-Administrator oder durch Rollenzuweisung Berechtigung haben.

Wenn Sie über mehrere Abonnements verfügen und ein anderes Abonnement abrufen möchten, geben Sie Folgendes ein:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

## Überprüfen bestehender Rollenzuweisungen

Überprüfen wir zunächst, welche Rollenzuweisungen bereits für das Abonnement bestehen. Geben Sie Folgendes ein:

    PS C:\> Get-AzureRoleAssignment

Mit diesem Befehl erhalten Sie alle Rollenzuweisungen für das Abonnement. Beachten Sie die folgenden beiden Punkte:

1. Sie benötigen Lesezugriff auf Abonnementebene.
2. Wenn das Abonnement über eine große Anzahl an Rollenzuweisungen verfügt, kann die Abfrage einige Zeit dauern.

Sie können bestehende Rollenzuweisungen auch für eine bestimmte Rollendefinition, einen bestimmten Bereich oder für einen bestimmten Benutzer überprüfen. Geben Sie Folgendes ein:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Mit diesem Befehl erhalten sie alle Rollenzuweisungen für einen bestimmten Benutzer in Ihrem AD-Mandanten mit der Rollenzuweisung "Besitzer" für die Ressourcengruppe "Gruppe1". Die Rollenzuweisung kann auf zwei Wegen erfolgen:

1. Als Rollenzuweisung "Besitzer" für den Benutzer der Ressourcengruppe.
2. Als Rollenzuweisung "Besitzer" für den Benutzer der Ressource, die der Ressourcengruppe übergeordnet ist (in diesem Fall das Abonnement), da eine Berechtigung auf einer Ebene auch für alle untergeordneten Strukturen gilt.

Alle Parameter dieses Cmdlet sind optional. Sie können kombiniert werden, um Rollenzuweisungen mit verschiedenen Filtern zu überprüfen.

## Erstellen einer Rollenzuweisung

Um eine Rollenzuweisung zu erstellen, müssen Sie folgende Überlegungen anstellen:

Wem soll die Rolle zugewiesen werden: Verwenden Sie die folgenden Azure Active Directory-Cmdlets, um die in Ihrem AD-Mandanten vorhandenen Benutzer, Gruppen und Dienstprinzipale anzuzeigen.

    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal

Welche Rolle soll zugewiesen werden: Verwenden Sie das folgende Cmdlet, um die unterstützten Rollendefinitionen anzuzeigen.

    PS C:\> Get-AzureRoleDefinition

Welcher Bereich soll zugewiesen werden: Sie können aus den folgenden drei Bereichsebenen auswählen:

    - The current subscription
    - A resource group, to get a list of resource groups, type `PS C:\> Get-AzureResourceGroup`
    - A resource, to get a list of resources, type `PS C:\> Get-AzureResource`

Verwenden Sie anschließend den Befehl `New-AzureRoleAssignment`, um eine Rollenzuweisung zu erstellen. Beispiel:


Mit dem folgenden Befehl wird eine Rollenzuweisung auf der aktuellen Abonnementebene für einen Benutzer mit Leseberechtigung erstellt:

	 PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

Mit dem folgenden Befehl wird eine Rollenzuweisung auf Ressourcengruppenebene erstellt.

	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

Mit dem folgenden Befehl wird eine Rollenzuweisung für eine Gruppe auf Ressourcengruppenebene erstellt.

	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

Mit dem folgenden Befehl wird eine Rollenzuweisung auf Ressourcenebene erstellt.

	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## Überprüfen von Berechtigungen

Nachdem Sie überprüft haben, ob mit Ihrem Konto Rollenzuweisungen verknüpft sind, können Sie die Berechtigungen der zugewiesenen Rollen anzeigen lassen. Führen Sie dazu folgende Befehle aus:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Mit diesen beiden Cmdlets werden Ressourcengruppen und Ressourcen mit Leseberechtigung zurückgegeben. Zusätzlich werden auch Ihre Berechtigungen angezeigt.

Wenn Sie versuchen, ein anderes Cmdlet wie z. B. `New-AzureResourceGroup` auszuführen und nicht über die erforderlichen Berechtigung verfügen, wird ein Zugriffsverweigerungsfehler ausgegeben.

## Nächste Schritte

In den folgenden Themen und Ressourcen erhalten Sie weitere Informationen zur Verwaltung der rollenbasierten Zugriffssteuerung mit Windows PowerShell:

- [Rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control-configure.md)
- [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409) (in englischer Sprache): Verwenden der Cmdlets im AzureResourceManager-Modul.
- [Using Resource groups to manage your Azure resources](../azure-preview-portal-using-resource-groups.md) (in englischer Sprache): Erstellen und Verwalten von Ressourcengruppe im Azure-Verwaltungsportal.
- [Azure-Blog](http://blogs.msdn.com/windowsazure): Informationen zu neuen Funktionen in Azure.
- [Windows PowerShell-Blog](http://blogs.msdn.com/powershell): Informationen zu neuen Funktionen in Windows PowerShell.
- ["Hey, Scripting Guy!"- Blog](http://blogs.technet.com/b/heyscriptingguy/): Praktische Tipps und Tricks aus der Windows PowerShell-Community.
- [Konfigurieren der rollenbasierten Zugriffssteuerung mit der Azure-Befehlszeilenschnittstelle](role-based-access-control-xplat-cli.md)
- [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung](role-based-access-control-troubleshooting.md)

<!---HONumber=August15_HO8-->