<properties
	pageTitle="Handbuch zur rollenbasierten Zugriffssteuerung für die Azure-Befehlszeilenschnittstelle"
	description="Verwalten der rollenbasierten Access Control mit der Azure-Befehlszeilenschnittstelle"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="kgremban"/>

# Handbuch zur rollenbasierten Zugriffssteuerung für die Azure-Befehlszeilenschnittstelle

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-powershell.md)
- [Azure-Befehlszeilenschnittstelle](role-based-access-control-xplat-cli.md)

Mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) im Azure-Portal und der Azure-Ressourcen-Manager-API können Sie den Zugriff auf Ihr Abonnement und Ihre Ressourcen differenziert steuern. Mithilfe dieser Funktion lassen sich Zugriffsberechtigungen für Active Directory-Benutzer, -Gruppen oder -Dienstprinzipale festlegen, indem ihnen bestimmte Rollen für einen bestimmten Bereich zugewiesen werden.

In diesem Tutorial erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle zum Verwalten der rollenbasierten Zugriffssteuerung (RBAC) verwenden. Das Lernprogramm beschreibt die Erstellung und Überprüfung von Rollenzuweisungen.

**Geschätzter Zeitaufwand**: 15 Minuten.

## Voraussetzungen

Bevor Sie RBAC mithilfe der Azure-Befehlszeilenschnittstelle verwalten können, benötigen Sie Folgendes:

- Azure-Befehlszeilenschnittstelle, Version 0.8.8 oder höher. Um die neueste Version zu installieren und sie Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).
- Lesen Sie sich auch das folgende Tutorial durch, um sich mit der Einrichtung und Verwendung von Azure Resource Manager in der Azure-Befehlszeilenschnittstelle vertraut zu machen: [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

## <a id="connect"></a>Verbindungsherstellung mit Ihren Abonnements

Da RBAC nur mit dem Azure-Ressourcen-Manager funktioniert, müssen Sie zunächst in den Azure-Ressourcen-Manager-Modus wechseln. Geben Sie Folgendes ein:

    azure config mode arm

Um eine Verbindung mit Ihren Azure-Abonnements herzustellen, geben Sie Folgendes ein:

    azure login -u <username>

Geben Sie in die Eingabeaufforderung das Kennwort Ihres Azure-Kontos ein (verwenden Sie nur ein Organisationskonto). Die Azure-Befehlszeilenschnittstelle ruft alle Abonnements ab, die mit diesem Konto verknüpft sind, und konfiguriert sich selbst so, dass das erste Konto als Standardkonto verwendet wird. Beachten Sie, dass Sie mit der rollenbasierten Zugriffssteuerung nur Abonnements abrufen können, für die Sie entweder als Co-Administrator oder durch Rollenzuweisung über Berechtigungen verfügen.

Wenn Sie über mehrere Abonnements verfügen und ein anderes Abonnement abrufen möchten, geben Sie Folgendes ein:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

## <a id="check"></a>Überprüfen bestehender Rollenzuweisungen

Überprüfen wir zunächst, welche Rollenzuweisungen bereits für das Abonnement bestehen. Geben Sie Folgendes ein:

    azure role assignment list

Mit diesem Befehl erhalten Sie alle Rollenzuweisungen für das Abonnement. Beachten Sie die folgenden beiden Punkte:

1. Sie benötigen Lesezugriff auf Abonnementebene.
2. Wenn das Abonnement über eine große Anzahl an Rollenzuweisungen verfügt, kann die Abfrage einige Zeit dauern.

Sie können bestehende Rollenzuweisungen auch für eine bestimmte Rollendefinition, einen bestimmten Bereich oder für einen bestimmten Benutzer überprüfen. Geben Sie Folgendes ein:

    azure role assignment list -g group1 --upn <user email> -o Owner

Mit diesem Befehl erhalten sie alle Rollenzuweisungen für einen bestimmten Benutzer in ihrem Azure AD-Verzeichnis mit der Rollenzuweisung "Besitzer" für die Ressourcengruppe "Gruppe1". Die Rollenzuweisung kann auf zwei Wegen erfolgen:

1. Als Rollenzuweisung "Besitzer" für den Benutzer der Ressourcengruppe.
2. Rollenzuweisung „Besitzer“ an den Benutzer für das übergeordnete Element der Ressourcengruppe (in diesem Fall das Abonnement) Wenn Sie eine Berechtigung auf einer übergeordneten Ebene zuweisen, gilt diese auch für alle untergeordneten Elemente.

Alle Parameter dieses Cmdlet sind optional. Sie können kombiniert werden, um Rollenzuweisungen mit verschiedenen Filtern zu überprüfen.

## <a id="create"></a>Erstellen einer Rollenzuweisung

Um eine Rollenzuweisung zu erstellen, müssen Sie folgende Überlegungen anstellen:

- Wem soll die Rolle zugewiesen werden: Verwenden Sie die folgenden Azure Active Directory-Cmdlets, um die in Ihrem Verzeichnis vorhandenen Benutzer, Gruppen und Dienstprinzipale anzuzeigen.

    ```
    azure ad user list  
    azure ad user show  
    azure ad group list  
    azure ad group show  
    azure ad group member list  
    azure ad sp list  
    azure ad sp show  
    ```

- Welche Rolle soll zugewiesen werden: Verwenden Sie das folgende Cmdlet, um die unterstützten Rollendefinitionen anzuzeigen.

    `azure role list`

- Welcher Bereich soll zugewiesen werden: Sie können aus den folgenden drei Bereichsebenen auswählen:

    - Das aktuelle Abonnement
    - Eine Ressourcengruppe. Um eine Liste der Ressourcengruppen zu erhalten, geben Sie Folgendes ein: `azure group list`
    - Eine Ressource. Um eine Liste der Ressourcen zu erhalten, geben Sie Folgendes ein: `azure resource list`

Verwenden Sie anschließend den Befehl `azure role assignment create`, um eine Rollenzuweisung zu erstellen. Beispiel:

 	#Create a role assignment at the current subscription level for a user as a reader:
    azure role assignment create --upn <user email> -o Reader

	#Create a role assignment at a resource group level:
    PS C:\> azure role assignment create --upn <user email> -o Contributor -g group1

	#Create a role assignment at a resource level:
    azure role assignment create --upn <user email> -o Owner -g group1 -r Microsoft.Web/sites -u site1

## <a id="verify"></a>Überprüfen von Berechtigungen

Nachdem Sie überprüft haben, ob mit Ihrem Konto Rollenzuweisungen verknüpft sind, können Sie die Berechtigungen der zugewiesenen Rollen anzeigen. Führen Sie dazu folgende Befehle aus:

    PS C:\> azure group list
    PS C:\> azure resource list

Mit diesen beiden Cmdlets werden Ressourcengruppen und Ressourcen mit Leseberechtigung zurückgegeben. Zusätzlich werden auch Ihre Berechtigungen angezeigt.

Wenn Sie versuchen, andere Cmdlets wie `azure group create` auszuführen, wird ein Zugriffsverweigerungsfehler ausgegeben, wenn Sie nicht über die Berechtigung verfügen.

## <a id="next"></a>Nächste Schritte

In den folgenden Themen und Ressourcen erhalten Sie weitere Informationen zur Verwaltung der rollenbasierten Zugriffssteuerung mit der Azure-Befehlszeilenschnittstelle:

- [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md)
- [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](../xplat-cli-azure-resource-manager.md)
- [Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen:](../azure-portal/resource-group-portal.md) Erfahren Sie, wie Sie Ressourcengruppen im Azure-Portal erstellen und verwalten.
- [Azure-Blog](http://blogs.msdn.com/windowsazure): Informationen zu neuen Funktionen in Azure.
- [Konfigurieren der rollenbasierten Zugriffssteuerung mit Windows PowerShell](role-based-access-control-powershell.md)
- [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0323_2016-->