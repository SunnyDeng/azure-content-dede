<properties 
   pageTitle="Rollenbasierte Zugriffsteuerung in Azure Automation | Microsoft Azure"
   description="Die rollenbasierte Zugriffssteuerung (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure-Ressourcen. Dieser Artikel beschreibt, wie eine rollenbasierte Zugriffssteuerung in Azure Automation eingerichtet wird."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="sngun"/>

# Rollenbasierte Zugriffssteuerung in Azure Automation

## Rollenbasierte Zugriffssteuerung

Die rollenbasierte Zugriffssteuerung (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure-Ressourcen. Mithilfe der [rollenbasierten Zugriffssteuerung](../active-directory/role-based-access-control-configure.md) können Sie Aufgaben innerhalb Ihres Teams verteilen und Benutzern, Gruppen und Anwendungen nur den Zugriff gewähren, den diese zur Ausführung ihrer Aufgaben benötigen. Den Benutzern kann die rollenbasierte Zugriffssteuerung über das Azure-Portal, Azure Befehlszeilenwerkzeuge oder Azure Management APIs zugewiesen werden.

## Rollenbasierte Zugriffssteuerung in Automation-Konten

Der Zugriff wird in Azure Automation erteilt, indem den Benutzern, Gruppen und Anwendungen im Automation-Konten-Bereich die passende RBAC-Rolle zugewiesen wird. Die folgenden vordefinierten Rollen werden von Automation-Konten unterstützt:

|**Rolle** | **Beschreibung** |
|:--- |:---|
| Besitzer | Die Rolle „Besitzer“ erlaubt den Zugriff auf alle Ressourcen und Aktionen innerhalb des Automation-Kontos, inklusive dem Zugriff auf andere Benutzer, Gruppen und Anwendungen, um das Automation-Konto zu verwalten. |
| Mitwirkender | Die Rolle „Mitwirkender“ erlaubt Ihnen, fast alles zu verwalten. Das Einzige, was Sie nicht können, ist das Ändern der Zugriffserlaubnisse auf ein Automation-Konto anderer Benutzer. |
| Leser | Die Rolle „Leser“ erlaubt Ihnen, alle Ressourcen im Automation-Konto zu betrachten, Änderungen können Sie jedoch nicht vornehmen. |
| Operator für Automation | Die Rolle „Operator für Automation“ erlaubt Ihnen, operative Aufgaben wie das Starten, Anhalten, Unterbrechen, Fortführen und Einplanen von Aufgaben. Diese Rolle ist hilfreich, wenn Sie Ihre Automation-Konten-Ressourcen wie Anmeldeinformationsobjekte und Runbooks vor Einblicken und Änderungen schützen wollen, es Mitgliedern Ihrer Organisation aber dennoch ermöglichen wollen, diese Runbooks auszuführen. [Operator für Automation Aktionen](../active-directory/role-based-access-built-in-roles.md#automation-operator) listet die von der Rolle des Operators für Automation unterstützten Aktionen sowie seine Ressourcen. |
| Benutzerzugriffsadministrator | Die Rolle „Benutzerzugriffsadministrator“ erlaubt Ihnen, den Benutzerzugriff auf Azure Automation-Konten zu verwalten. |

In diesem Artikel zeigen wir Ihnen Schritt für Schritt, wie Sie eine rollenbasierte Zugriffssteuerung in Azure Automation einrichten.

## Konfigurieren einer rollenbasierten Zugriffssteuerung für Automation-Konten mittels Azure-Portal

1.	Melden Sie sich im [Azure-Portal](http://portal.azure.com/) an und öffnen Sie Ihr Automation-Konto aus dem Blatt „Automation-Konten“.  

2.	Klicken Sie auf **Zugriffs**steuerung oben in der rechten Ecke. Dies öffnet das Blatt **Benutzer**, in dem Sie neue Benutzer, Gruppen und Anwendungen hinzufügen können, um Ihr Automation-Konto zu verwalten. Sie können die vorhandenen Rollen sehen, die für das Automation-Konto konfiguriert werden können.

    ![Zugriffsschaltfläche](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]**Abonnement-Administratoren** besteht bereits als Standardnutzer. Die Active Directory-Gruppe der Abonnement-Administratoren umfasst die Dienstadministratoren und Co-Administratoren für Ihr Azure-Abonnement. Der Dienstadministrator ist der Besitzer Ihres Azure-Abonnements und dessen Ressourcen und wird auch für die Automation-Konten die Rolle des Besitzers erben. Dies bedeutet, dass der Zugriff für **Dienstadministratoren und Co-Administratoren** eines Abonnements **geerbt** wird, während er für alle anderen Benutzer **zugewiesen** wird. Klicken Sie auf **Abonnementadministratoren**, um mehr über ihre Rechte zu erfahren.

### Einen neuen Benutzer hinzufügen und eine Rolle zuweisen

1.	Klicken Sie im Benutzerblatt auf **Hinzufügen**, um das **Blatt Zugriff hinzufügen**zu öffnen, in dem Sie einen Benutzer, eine Gruppe oder eine Anwendung hinzufügen und ihnen Rollen zuweisen können.  

    ![Benutzer hinzufügen](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Wählen Sie eine Rolle aus der Liste der verfügbaren Rollen aus. Wir wählen die Rolle **Leser**, Sie können aber jede der verfügbaren, integrierten Rollen, die von Automation-Konten unterstützt werden, oder auch eine eigene, benutzerdefinierte Rolle auswählen.

    ![Rolle wählen](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Klicken Sie auf **Benutzer hinzufügen**, um das Blatt **Benutzer hinzufügen** zu öffnen. Falls Sie bereits Benutzer, Gruppen oder Anwendungen hinzugefügt haben, um Ihr Abonnement zu verwalten, werden diese Benutzer aufgelistet und Sie können sie auswählen um ihnen den Zugriff zu erteilen. Falls keine Benutzer aufgeführt werden, oder falls der Benutzer, den Sie hinzufügen wollen, nicht auf der Liste steht, klicken Sie **Einladen**, um das Blatt **Einen Gast einladen** zu öffnen. Von dort können Sie jeden Benutzer mit einer gültigen Microsoft-Konto-E-Mail-Adresse wie Outlook.com, OneDrive oder Xbox Live Ids einladen. Klicken Sie, sobald Sie die E-Mail-Adresse des Benutzers eingegeben haben, auf **Auswählen**, um den Benutzer hinzuzufügen, und dann auf **OK**.

    ![Hinzufügen von Benutzern](media/automation-role-based-access-control/automation-04-add-users.png)
 
Sie sollten jetzt den Benutzer auf dem Blatt **Benutzer** mit seiner zugewiesenen Rolle als **Leser** sehen.

![Benutzer auflisten](media/automation-role-based-access-control/automation-05-list-users.png)

Sie können dem Benutzer die Rolle auch vom Blatt **Rollen**aus zuweisen. Klicken Sie im Benutzerblatt auf **Rollen**, um das **Rollenblatt** zu öffnen. In diesem Blatt können Sie den Namen der Rolle sowie die Anzahl der Benutzer und Gruppen sehen, denen diese Rolle zugewiesen wurde.

![Rollen vom Benutzerblatt zuweisen](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE]Die rollenbasierte Zugriffssteuerung kann nur auf der Automation-Konto-Ebene eingerichtete werden und nicht bei einer Ressource unter dem Automation-Konto.

Sie können einem Benutzer, einer Gruppe oder einer Anwendung mehr als eine Rolle zuweisen. Falls wir beispielsweise die Rolle **Operator für Automation** zusammen mit der Rolle**Leser** an den Benutzer zuweisen, kann er alle Automation-Ressourcen sehen und alle Runbookjobs ausführen. Sie können das Dropdown-Menü erweitern, um eine Liste mit allen dem Benutzer zugewiesenen Rollen anzeigen zu lassen.

![Mehrere Rollen anzeigen](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Benutzer entfernen

Sie können die Zugriffserlaubnisse eines Benutzers, der selbst nicht das Automation-Konto verwaltet oder nicht mehr für die Organisation arbeitet, entfernen. Folgende Schritte müssen Sie durchführen, um einen Benutzer zu entfernen:

1.	Wählen Sie auf dem Blatt **Benutzer** die Rollenzuweisung aus, die Sie entfernen möchten.

2.	Klicken Sie auf dem Blatt mit den Zuweisungsdetails auf die Schaltfläche **Entfernen**.

3.	Klicken Sie auf **Ja**, um die Entfernung zu bestätigen.

    ![Benutzer entfernen](media/automation-role-based-access-control/automation-08-remove-users.png)

## Benutzer mit zugewiesener Rolle

Wenn Benutzer, denen eine Rolle zugewiesen ist, sich in ihrem Automation-Konto anmelden, können sie jetzt das Konto des Besitzers im **Standardverzeichnis** aufgeführt sehen. Um das Automation-Konto, dem sie hinzugefügt wurden, sehen zu können, müssen sie das Standardverzeichnis auf das Standardverzeichnis des Besitzers ändern.

![Standardverzeichnis](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Die Benutzererfahrung mit der Rolle „Operator für Automation“

Wenn Benutzer, denen die Rolle „Operator für Automation“ zugewiesen wurde, das Automation-Konto, dem sie zugewiesen wurden, betrachten, können sie lediglich die Liste der Runbooks, Runbookaufgaben und Zeitpläne, die im Automation-Konto erstellt wurden, sehen, nicht aber deren Definition. Sie können die Runbookaufgabe starten, anhalten, unterbrechen, fortführen oder einplanen. Der Benutzer wird auf die anderen Automation-Ressourcen wie Konfigurationen, Hybrid-Worker-Gruppen oder DSC-Knoten nicht zugreifen können.

![Kein Zugriff auf Ressourcen](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Wenn der Benutzer das Runbook anklickt, werden ihm die Befehle, die Quelle anzuzeigen oder das Runbook zu bearbeiten, nicht angeboten, da die Rolle „Operator für Automation“ diese Zugriffe nicht erlaubt.

![Kein Änderungszugriff auf Runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

Der Benutzer wird einen Lese- und Erstellungszugriff für die Zeitpläne haben, aber keinen Zugriff auf andere Assettypen.

![Kein Zugriff auf Assets](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Dieser Benutzer hat auch keine Zugriffsrechte, um die einem Runbook zugeordneten Webhooks zu sehen.

![Kein Zugriff auf Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Rollenbasierte Zugriffssteuerung für das Automation-Konto mit Azure PowerShell konfigurieren

Rollenbasierte Zugriffe für ein Automation-Konto können auch mit den folgenden [Azure PowerShell cmdlets](../active-directory/role-based-access-control-manage-access-powershell.md) konfiguriert werden.

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) listet alle im Azure Active Directory verfügbaren rollenbasierten Zugriffssteuerungen auf. Sie können diesen Befehl zusammen mit der **Name**-Eigenschaft verwenden, um alle Benutzer mit einer bestimmten Rolle anzuzeigen. **Beispiel:** ![Beziehe Rollendefinition](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) listet die Rollenzuweisungen durch Azure RBAC für den angegebenen Bereich auf. Ohne einschränkende Parameter gibt dieser Befehl alle Rollenzuweisungen zurück, die in diesem Abonnement erstellt wurden. Verwenden Sie die **ExpandPrincipalGroups**-Parameter, um die Zugriffsweisungen an den bestimmten Benutzer sowie die Gruppen, denen der Benutzer angehört, aufzulisten. **Beispiel:** Verwenden Sie den folgenden Befehl, um alle Benutzer innerhalb eines Automation-Kontos mit ihren Rollen aufzulisten.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Beziehe Rollenzuweisung](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• Verwenden Sie [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx), um Benutzern, Gruppen und Anwendungen für einen bestimmten Bereich den Zugriff zu erteilen. **Beispiel:** Verwenden Sie den folgenden Befehl, um eine neue Rolle „Operator für Automation“ für einen Benutzer im Bereich des Automation-Kontos zu erstellen.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Neue Rollenzuweisung](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

•Verwenden Sie [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx), um den Zugriff eines spezifischen Benutzers, einer spezifischen Gruppe oder Anwendung in einem bestimmten Bereich zu entfernen. **Beispiel:** Verwenden Sie den folgenden Befehl, um eine neue Rolle „Operator für Automation“ für einen Benutzer im Bereich des Automation-Kontos zu erstellen.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Ersetzen Sie in den obigen Cmdlets den Anmeldenamen, die Abonnement-ID, den Namen der Ressourcengruppe und den Namen des Automation-Kontos durch die entsprechenden Werte Ihres Kontos. Wählen Sie **Ja**, wenn Sie gefragt werden, ob Sie mit der Löschung der Rollenzuweisung fortfahren wollen.


## Nächste Schritte
-  Weitere Informationen zu den verschiedenen Arten, rollenbasierte Zugriffsteuerungen in Azure Automation zu konfigurieren, finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Weitere Informationen zu verschiedenen Methoden zum Starten eines Runbooks finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).
- Weitere Informationen zu verschiedenen Typen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md).

<!---HONumber=AcomDC_0121_2016-->