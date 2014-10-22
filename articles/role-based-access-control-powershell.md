<properties pageTitle="Managing Role-Based Access Control with Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Managing role-based access control with Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/de-de/documentation/articles/xplat-cli-rbac.md" title="Plattform&uuml;bergreifende Befehlszeilenschnittstelle">Plattform&uuml;bergreifende Befehlszeilenschnittstelle</a></div>

Mit der rollenbasierten Zugriffssteuerung (RBAC) im Azure-Vorschauportal und der Azure Resource Manager-API können Sie den Zugriff auf Ihr Abonnement differenziert steuern. Mithilfe dieser Funktion lassen sich Zugriffsberechtigungen für Active Directory-Benutzer, -Gruppen oder -Dienstprinzipale festlegen, indem ihnen bestimmte Rollen für einen bestimmten Bereich zugewiesen werden.

In diesem Lernprogramm erfahren Sie, wie Sie mit Windows PowerShell die rollenbasierte Zugriffssteuerung verwalten. Das Lernprogramm beschreibt die Erstellung und Überprüfung von Rollenzuweisungen.

**Geschätzter Zeitaufwand:** 15 Minuten

## Voraussetzungen

Bevor Sie die RBAC mithilfe der Windows PowerShell verwalten können, benötigen Sie Folgendes:

-   Windows PowerShell, Version 3.0 oder 4.0. So finden Sie die Version von Windows PowerShell: Geben Sie `$PSVersionTable` ein, und vergewissern Sie sich, dass der Wert von `PSVersion` 3.0 oder 4.0 ist. Informationen zum Installieren einer kompatiblen Version finden Sie unter [Windows Management Framework 3.0][Windows Management Framework 3.0] oder [Windows Management Framework 4.0][Windows Management Framework 4.0].

-   Azure PowerShell, Version 0.8.8 oder höher. Informationen zum Installieren der aktuellen Version und zum Verknüpfen mit dem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Windows Azure PowerShell][Installieren und Konfigurieren von Windows Azure PowerShell].

Dieses Lernprogramm richtet sich an Windows PowerShell-Anfänger. Es wird aber vorausgesetzt, dass Sie die grundlegenden Konzepte, wie zum Beispiel Module, Cmdlets und Sitzungen, verstehen. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell][Erste Schritte mit Windows PowerShell].

Um detaillierte Hilfe zu einem Cmdlet aus dem Lernprogramm zu erhalten, verwenden Sie das Get-Help-Cmdlet.

    Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise Folgendes ein, um Hilde zum Add-AzureAccount-Cmdlet zu erhalten:

    Get-Help Add-AzureAccount -Detailed

Lesen Sie bitte auch die folgenden Lernprogramme, um sich mit der Einrichtung und Verwendung von Azure Resource Manager in Windows PowerShell vertraut zu machen:

-   [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell]
-   [Verwenden von Windows PowerShell mit dem Ressourcen-Manager][Verwenden von Windows PowerShell mit dem Ressourcen-Manager]

## Dieses Lernprogramm umfasst folgende Punkte

-   [Verbindungsherstellung mit Ihren Abonnements][Verbindungsherstellung mit Ihren Abonnements]
-   [Überprüfen bestehender Rollenzuweisungen][Überprüfen bestehender Rollenzuweisungen]
-   [Erstellen einer Rollenzuweisung][Erstellen einer Rollenzuweisung]
-   [Überprüfen von Berechtigungen][Überprüfen von Berechtigungen]
-   [Nächste Schritte][Nächste Schritte]

## <span id="connect"></span></a>Verbindungsherstellung mit Ihren Abonnements

Da RBAC nur mit Azure Resource Manager funktioniert, müssen Sie zunächst in den Azure Resource Manager-Modus wechseln. Geben Sie dazu Folgendes ein:

    PS C:PS C:\> Switch-AzureMode -Name AzureResourceManagergt; Switch-AzureMode -Name AzureResourceManager

Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager][Verwenden von Windows PowerShell mit dem Ressourcen-Manager].

Um eine Verbindung mit Ihren Azure-Abonnements herzustellen, geben Sie Folgendes ein:

    PS C:PS C:\> Add-AzureAccountgt; Add-AzureAccount

Geben Sie in das Popup-Browsersteuerelement den Benutzernamen und das Kennwort Ihres Azure-Kontos ein. PowerShell ruft alle Abonnements ab, die mit diesem Konto verknüpft sind, und konfiguriert sich selbst so, dass das erste Konto als Standardkonto verwendet wird. Beachten Sie, dass Sie mit der rollenbasierten Zugriffssteuerung nur Abonnements abrufen können, für die Sie entweder als Co-Administrator oder durch Rollenzuweisung Berechtigung haben.

Wenn Sie über mehrere Abonnements verfügen und ein anderes Abonnement abrufen möchten, geben Sie Folgendes ein:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

## <span id="check"></span></a>Überprüfen bestehender Rollenzuweisungen

Überprüfen wir zunächst, welche Rollenzuweisungen bereits für das Abonnement bestehen. Geben Sie Folgendes ein:

    PS C:PS C:\> Get-AzureRoleAssignmentgt; Get-AzureRoleAssignment

Mit diesem Befehl erhalten Sie alle Rollenzuweisungen für das Abonnement. Beachten Sie die folgenden beiden Punkte:

1.  Sie benötigen Lesezugriff auf Abonnementebene.
2.  Wenn das Abonnement über eine große Anzahl an Rollenzuweisungen verfügt, kann die Abfrage einige Zeit dauern.

Sie können bestehende Rollenzuweisungen auch für eine bestimmte Rollendefinition, einen bestimmten Bereich oder für einen bestimmten Benutzer überprüfen. Geben Sie Folgendes ein:

    PS C:PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Ownergt; Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Mit diesem Befehl erhalten sie alle Rollenzuweisungen für einen bestimmten Benutzer in Ihrem AD-Mandanten mit der Rollenzuweisung "Besitzer" für die Ressourcengruppe "Gruppe1". Die Rollenzuweisung kann auf zwei Wegen erfolgen:

1.  Als Rollenzuweisung "Besitzer" für den Benutzer der Ressourcengruppe.
2.  Als Rollenzuweisung "Besitzer" für den Benutzer der Ressource, die der Ressourcengruppe übergeordnet ist (in diesem Fall das Abonnement), da eine Berechtigung auf einer Ebene auch für alle untergeordneten Strukturen gilt.

Alle Parameter dieses Cmdlet sind optional. Sie können kombiniert werden, um Rollenzuweisungen mit verschiedenen Filtern zu überprüfen.

## <span id="create"></span></a>Erstellen einer Rollenzuweisung

Um eine Rollenzuweisung zu erstellen, müssen Sie folgende Überlegungen anstellen:

-   Wem soll die Rolle zugewiesen werden: Verwenden Sie die folgenden Azure Active Directory-Cmdlets, um die in Ihrem AD-Mandanten vorhandenen Benutzer, Gruppen und Dienstprinzipale anzuzeigen.

    `PS C:PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADUser PS C:`PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADGroup PS C:`PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADGroupMember PS C:`PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADServicePrincipal</code>

-   Welche Rolle soll zugewiesen werden: Verwenden Sie das folgende Cmdlet, um die unterstützten Rollendefinitionen anzuzeigen.

    `PS C:PS C:\> Get-AzureRoleDefinition`gt; Get-AzureRoleDefinition</code>

-   Welcher Bereich soll zugewiesen werden: Sie können aus den folgenden drei Bereichsebenen auswählen:

    -   Das aktuelle Abonnement
    -   Eine Ressourcengruppe. Um eine Liste der Ressourcengruppen zu erhalten, geben Sie Folgendes ein: `PS C:PS C:\> Get-AzureResourceGroup`gt; Get-AzureResourceGroup</code>
    -   Eine Ressource. Um eine Liste der Ressourcen zu erhalten, geben Sie Folgendes ein: `PS C:PS C:\> Get-AzureResource`gt; Get-AzureResource</code>

Verwenden Sie anschließend den Befehl `New-AzureRoleAssignment`, um eine Rollenzuweisung zu erstellen. Beispiel:

-   Mit dem folgenden Befehl wird eine Rollenzuweisung auf der aktuellen Abonnementebene für einen Benutzer mit Leseberechtigung erstellt.

    `PS C:PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`gt; New-AzureRoleAssignment -Mail \<user's email\> -RoleDefinitionName Reader</code>

-   Mit dem folgenden Befehl wird eine Rollenzuweisung auf Ressourcengruppenebene erstellt:

    `PS C:PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`gt; New-AzureRoleAssignment -Mail \<user's email\> -RoleDefinitionName Contributor -ResourceGroupName group1</code>

-   Mit dem folgenden Befehl wird eine Rollenzuweisung auf Ressourcenebene erstellt:

    `PS C:PS C:\> $resources = Get-AzureResource PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`gt; $resources = Get-AzureResource PS C:`PS C:\> $resources = Get-AzureResource PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`gt; New-AzureRoleAssignment -Mail \<user's email\> -RoleDefinitionName Owner -Scope $resources[0].ResourceId</code>

## <span id="verify"></span></a>Überprüfen von Berechtigungen

Nachdem Sie überprüft haben, ob mit Ihrem Konto Rollenzuweisungen verknüpft sind, können Sie die Berechtigungen der zugewiesenen Rollen anzeigen lassen. Führen Sie dazu folgende Befehle aus:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Mit diesen beiden Cmdlets werden Ressourcengruppen und Ressourcen mit Leseberechtigung zurückgegeben. Zusätzlich werden auch Ihre Berechtigungen angezeigt.

Wenn sie versuchen, andere Cmdlets wie `New-AzureResourceGroup` auszuführen, wird ein Zugriffsverweigerungsfehler ausgegeben, wenn Sie nicht über die Berechtigung verfügen.

## <span id="next"></span></a>Nächste Schritte

In den folgenden Themen und Ressourcen erhalten Sie weitere Informationen zur Verwaltung der rollenbasierten Zugriffssteuerung mit Windows PowerShell:

-   [Rollenbasierte Zugriffssteuerung in Windows Azure][Rollenbasierte Zugriffssteuerung in Windows Azure]
-   [Azure Resource Manager Cmdlets][Azure Resource Manager Cmdlets]: Erhalten Sie Informationen zur Verwendung von Cmdlets im AzureResourceManager-Modul.
-   [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: Erfahren Sie mehr über die Erstellung und Verwaltung von Ressourcengruppen im Azure-Verwaltungsportal.
-   [Azure-Blog][Azure-Blog]: Erhalten Sie Informationen zu neuen Funktionen in Azure.
-   [Windows PowerShell-Blog][Windows PowerShell-Blog]: Erhalten Sie Informationen zu neuen Funktionen in Windows PowerShell.
-   ["Hey, Scripting Guy!"- Blog]["Hey, Scripting Guy!"- Blog]: Erhalten Sie praktische Tipps und Tricks aus der Windows PowerShell-Community.
-   [Konfigurieren der rollenbasierten Zugriffssteuerung mit xplat-cli][Konfigurieren der rollenbasierten Zugriffssteuerung mit xplat-cli]
-   [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung][Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung]

  [Windows PowerShell]: /de-de/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [Plattformübergreifende Befehlszeilenschnittstelle]: /de-de/documentation/articles/xplat-cli-rbac.md "Plattformübergreifende Befehlszeilenschnittstelle"
  [Windows Management Framework 3.0]: http://www.microsoft.com/en-us/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/en-us/download/details.aspx?id=40855
  [Installieren und Konfigurieren von Windows Azure PowerShell]: http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell/
  [Erste Schritte mit Windows PowerShell]: http://technet.microsoft.com/en-us/library/hh857337.aspx
  [Installieren und Konfigurieren von Azure PowerShell]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/
  [Verwenden von Windows PowerShell mit dem Ressourcen-Manager]: http://azure.microsoft.com/de-de/documentation/articles/powershell-azure-resource-manager/
  [Verbindungsherstellung mit Ihren Abonnements]: #connect
  [Überprüfen bestehender Rollenzuweisungen]: #check
  [Erstellen einer Rollenzuweisung]: #create
  [Überprüfen von Berechtigungen]: #verify
  [Nächste Schritte]: #next
  [Rollenbasierte Zugriffssteuerung in Windows Azure]: http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-configure/
  [Azure Resource Manager Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: http://azure.microsoft.com/de-de/documentation/articles/azure-preview-portal-using-resource-groups
  [Azure-Blog]: http://blogs.msdn.com/windowsazure
  [Windows PowerShell-Blog]: http://blogs.msdn.com/powershell
  ["Hey, Scripting Guy!"- Blog]: http://blogs.technet.com/b/heyscriptingguy/
  [Konfigurieren der rollenbasierten Zugriffssteuerung mit xplat-cli]: http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-xplat-cli/
  [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung]: http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-troubleshooting/
