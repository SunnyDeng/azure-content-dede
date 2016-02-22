    <properties
	pageTitle="Grant user permissions to specific DevTest Lab policies | Microsoft Azure"
	description="Learn how to grant user permissions to specific DevTest Lab policies based on each user's needs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="tarcher"/>

# Gewähren von Benutzerberechtigungen für bestimmte DevTest Lab-Richtlinien

## Übersicht

Dieser Artikel veranschaulicht, wie Sie mit PowerShell Benutzerberechtigungen für eine bestimmte Azure DevTest Lab-Richtlinie gewähren. Auf diese Weise können Berechtigungen basierend auf den Anforderungen der einzelnen Benutzer gewährt werden. Sie möchten beispielsweise einem bestimmten Benutzer die Möglichkeit zum Ändern der Richtlinieneinstellungen der virtuellen Computer gewähren, jedoch nicht für die Kostenrichtlinien.

## Richtlinien als Ressourcen

Wie im Artikel [Rollenbasierte Access Control in Azure](/role-based-access-control-configure.md) verdeutlicht, ermöglicht RBAC eine fein abgestufte Steuerung des Zugriffs auf Azure-Ressourcen. Mithilfe von RBAC können Sie Aufgaben in Ihrem DevOps-Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

In DevTest Lab ist eine Richtlinie ein Ressourcentyp, der die RBAC-Aktion **Microsoft.DevTestLab/labs/policySets/policies/** ermöglicht. Jede DevTest Lab-Richtlinie ist eine Ressource im Richtlinienressourcentyp und kann als Bereich einer RBAC-Rolle zugewiesen werden.

Um Benutzern beispielsweise Lese-/Schreibberechtigungen für die Richtlinie **Zulässige VM-Größen** zu erteilen, erstellen Sie eine benutzerdefinierte Rolle, die mit der Aktion **Microsoft.DevTestLab/labs/policySets/policies/*** arbeitet, und weisen dieser benutzerdefinierten Rolle anschließend im Bereich von **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab** entsprechend Benutzer zu.

Weitere Informationen zu benutzerdefinierten Rollen in RBAC finden Sie im Abschnitt [Benutzerdefinierte Rollen in Azure RBAC](/role-based-access-control-configure.md#custom-roles-in-azure-rbac) im Artikel [Rollenbasierte Access Control in Azure](/role-based-access-control-configure.md).

##Erstellen einer benutzerdefinierten DevTest Lab-Rolle mithilfe von PowerShell
Zu Beginn lesen Sie den folgenden Artikel, der erklärt, wie Sie die Azure PowerShell-Cmdlets installieren und konfigurieren: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Nachdem Sie Azure PowerShell-Cmdlets eingerichtet haben, können Sie die folgenden Aufgaben ausführen:

- Auflisten aller Vorgänge/Aktionen für einen Ressourcenanbieter
- Auflisten der Aktionen in einer bestimmten Rolle
- Erstellen einer benutzerdefinierten Rolle

Im folgenden PowerShell-Skript sind Beispiele für diese Aufgaben veranschaulicht:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##Zuweisen von Benutzerberechtigungen für eine bestimmte Richtlinie mithilfe von benutzerdefinierten Rollen
Nachdem Sie Ihre benutzerdefinierten Rollen definiert haben, können Sie diese Benutzern zuweisen. Um einem Benutzer eine benutzerdefinierte Rolle zuzuweisen, benötigen Sie zunächst die **ObjectId** dieses Benutzers. Verwenden Sie dazu das **Get-AzureRmADUser**-Cmdlet.

Im folgenden Beispiel lautet die **ObjectId** des Benutzers *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Sobald Sie über die **ObjectId** für den Benutzer und einen Namen für die benutzerdefinierte Rolle verfügen, können Sie dem Benutzer diese Rolle per **New-AzureRmRoleAssignment**-Cmdlet zuweisen:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

Im vorherigen Beispiel die **AllowedVmSizesInLab**-Richtlinie verwendet wird. Sie können auch eine der folgenden Richtlinien verwenden:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

## Nächste Schritte

Nachdem Sie Berechtigungen für bestimmte DevTest Lab-Richtlinien gewährt haben, finden Sie im Folgenden Informationen zu den nächsten möglichen Schritten:

- [Absichern des Zugriffs auf ein DevTest Lab](devtest-lab-add-devtest-user.md).

- [Festlegen von Labrichtlinien](devtest-lab-set-lab-policy.md).

- [Erstellen einer Labvorlage](devtest-lab-create-template.md).

- [Erstellen benutzerdefinierter Artefakte für Ihre VMs](devtest-lab-artifact-author.md).

- [Hinzufügen einer VM mit Artefakten zu einem Azure DevTest Lab](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0211_2016-->