<properties pageTitle="Managing Role-Based Access Control with Azure Cross-Platform Command-Line Interface" metaKeywords="ResourceManager, Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli, RBAC" description="Managing role-based access control with cross-platform command-line interface" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Verwalten der rollenbasierten Zugriffssteuerung mit der plattformübergreifenden Befehlszeilenschnittstelle

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/de-de/documentation/articles/xplat-cli-rbac.md" title="Plattform&uuml;bergreifende Befehlszeilenschnittstelle">Plattform&uuml;bergreifende Befehlszeilenschnittstelle</a></div>

Mit der rollenbasierten Zugriffssteuerung (RBAC) im Azure-Vorschauportal und der Azure Resource Manager-API können Sie den Zugriff auf Ihr Abonnement und Ihre Ressourcen differenziert steuern. Mithilfe dieser Funktion lassen sich Zugriffsberechtigungen für Active Directory-Benutzer, -Gruppen oder -Dienstprinzipale festlegen, indem ihnen bestimmte Rollen für einen bestimmten Bereich zugewiesen werden.

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli) die rollenbasierte Zugriffssteuerung verwalten. Das Lernprogramm beschreibt die Erstellung und Überprüfung von Rollenzuweisungen.

**Geschätzter Zeitaufwand:** 15 Minuten

## Voraussetzungen

Bevor Sie die RBAC mithilfe von xplat-cli verwalten können, benötigen Sie Folgendes:

-   Plattformübergreifende Azure-Befehlszeilenschnittstelle der Version 0.8.8 oder höher. Informationen zum Installieren der aktuellen Version und zum Verknüpfen mit Ihrem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle][Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle].
-   Lesen Sie bitte auch die folgenden Lernprogramme, um sich mit der Einrichtung und Verwendung von Azure Resource Manager in der plattformübergreifenden Azure-Befehlszeilenschnittstelle vertraut zu machen: [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager][Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager]

## Dieses Lernprogramm umfasst folgende Punkte

-   [Verbindungsherstellung mit Ihren Abonnements][Verbindungsherstellung mit Ihren Abonnements]
-   [Überprüfen bestehender Rollenzuweisungen][Überprüfen bestehender Rollenzuweisungen]
-   [Erstellen einer Rollenzuweisung][Erstellen einer Rollenzuweisung]
-   [Überprüfen von Berechtigungen][Überprüfen von Berechtigungen]
-   [Nächste Schritte][Nächste Schritte]

## <span id="connect"></span></a>Verbindungsherstellung mit Ihren Abonnements

Da RBAC nur mit Azure Resource Manager funktioniert, müssen Sie zunächst in den Azure Resource Manager-Modus wechseln. Geben Sie Folgendes ein:

    azure config mode arm

Weitere Informationen finden Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Resource Manager][Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager].

Um eine Verbindung mit Ihren Azure-Abonnements herzustellen, geben Sie Folgendes ein:

    azure login -u <username>

Geben Sie in die Eingabeaufforderung das Kennwort Ihres Azure-Kontos ein (verwenden Sie nur ein Organisationskonto). xplat-cli ruft alle Abonnements ab, die mit diesem Konto verknüpft sind, und konfiguriert sich selbst so, dass das erste Konto als Standardkonto verwendet wird. Beachten Sie, dass Sie mit der rollenbasierten Zugriffssteuerung nur Abonnements abrufen können, für die Sie entweder als Co-Administrator oder durch Rollenzuweisung über Berechtigungen verfügen.

Wenn Sie über mehrere Abonnements verfügen und ein anderes Abonnement abrufen möchten, geben Sie Folgendes ein:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Weitere Informationen finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle][Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle].

## <span id="check"></span></a>Überprüfen bestehender Rollenzuweisungen

Überprüfen wir zunächst, welche Rollenzuweisungen bereits für das Abonnement bestehen. Geben Sie Folgendes ein:

    azure role assignment list

Mit diesem Befehl erhalten Sie alle Rollenzuweisungen für das Abonnement. Beachten Sie die folgenden beiden Punkte:

1.  Sie benötigen Lesezugriff auf Abonnementebene.
2.  Wenn das Abonnement über eine große Anzahl an Rollenzuweisungen verfügt, kann die Abfrage einige Zeit dauern.

Sie können bestehende Rollenzuweisungen auch für eine bestimmte Rollendefinition, einen bestimmten Bereich oder für einen bestimmten Benutzer überprüfen. Geben Sie Folgendes ein:

    azure role assignment list -g group1 --upn <user's email> -o Owner

Mit diesem Befehl erhalten sie alle Rollenzuweisungen für einen bestimmten Benutzer in ihrem Azure AD-Verzeichnis mit der Rollenzuweisung "Besitzer" für die Ressourcengruppe "Gruppe1". Die Rollenzuweisung kann auf zwei Wegen erfolgen:

1.  Als Rollenzuweisung "Besitzer" für den Benutzer der Ressourcengruppe.
2.  Als Rollenzuweisung "Besitzer" für den Benutzer der Ressource, die der Ressourcengruppe übergeordnet ist (in diesem Fall das Abonnement), da eine Berechtigung für eine übergeordnete Ressource auch für alle untergeordneten Ressourcen gilt.

Alle Parameter dieses Cmdlet sind optional. Sie können kombiniert werden, um Rollenzuweisungen mit verschiedenen Filtern zu überprüfen.

## <span id="create"></span></a>Erstellen einer Rollenzuweisung

Um eine Rollenzuweisung zu erstellen, müssen Sie folgende Überlegungen anstellen:

-   Wem soll die Rolle zugewiesen werden: Verwenden Sie die folgenden Azure Active Directory-Cmdlets, um die in Ihrem Verzeichnis vorhandenen Benutzer, Gruppen und Dienstprinzipale anzuzeigen.

    `azure ad user list azure ad user show azure ad group list azure ad group show azure ad group member list azure ad sp list azure ad sp show`

-   Welche Rolle soll zugewiesen werden: Verwenden Sie das folgende Cmdlet, um die unterstützten Rollendefinitionen anzuzeigen.

    `azure role list`

-   Welcher Bereich soll zugewiesen werden: Sie können aus den folgenden drei Bereichsebenen auswählen:

    -   Das aktuelle Abonnement
    -   Eine Ressourcengruppe. Um eine Liste der Ressourcengruppen zu erhalten, geben Sie Folgendes ein: `azure group list`
    -   Eine Ressource. Um eine Liste der Ressourcen zu erhalten, geben Sie Folgendes ein: `azure resource list`

Verwenden Sie anschließend den Befehl `azure role assignment create`, um eine Rollenzuweisung zu erstellen. Beispiel:

-   Mit dem folgenden Befehl wird eine Rollenzuweisung auf der aktuellen Abonnementebene für einen Benutzer mit Leseberechtigung erstellt:

    `azure role assignment create --upn <user's email> -o Reader`

-   Mit dem folgenden Befehl wird eine Rollenzuweisung auf Ressourcengruppenebene erstellt:

    `PS C:PS C:\> azure role assignment create --upn <user's email> -o Contributor -g group1`gt; azure role assignment create --upn \<user's email\> -o Contributor -g group1</code>

-   Mit dem folgenden Befehl wird eine Rollenzuweisung auf Ressourcenebene erstellt:

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <span id="verify"></span></a>Überprüfen von Berechtigungen

Nachdem Sie überprüft haben, ob mit Ihrem Konto Rollenzuweisungen verknüpft sind, können Sie die Berechtigungen der zugewiesenen Rollen anzeigen. Führen Sie dazu folgende Befehle aus:

    PS C:\> azure group list
    PS C:\> azure resource list

Mit diesen beiden Cmdlets werden Ressourcengruppen und Ressourcen mit Leseberechtigung zurückgegeben. Zusätzlich werden auch Ihre Berechtigungen angezeigt.

Wenn sie versuchen, andere Cmdlets wie `azure group create` auszuführen, wird ein Zugriffsverweigerungsfehler ausgegeben, wenn Sie nicht über die Berechtigung verfügen.

## <span id="next"></span></a>Nächste Schritte

In den folgenden Themen und Ressourcen erhalten Sie weitere Informationen zur Verwaltung der rollenbasierten Zugriffssteuerung mit xplat-cli:

-   [Rollenbasierte Zugriffssteuerung in Windows Azure][Rollenbasierte Zugriffssteuerung in Windows Azure]
-   [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle][Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle]
-   [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager][Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager]
-   [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: Erfahren Sie mehr über die Erstellung und Verwaltung von Ressourcengruppen im Azure-Verwaltungsportal.
-   [Azure-Blog][Azure-Blog]: Erhalten Sie Informationen zu neuen Funktionen in Azure.
-   [Konfigurieren der rollenbasierten Zugriffssteuerung mit Windows PowerShell][Konfigurieren der rollenbasierten Zugriffssteuerung mit Windows PowerShell]
-   [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung][Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung]

  [Windows PowerShell]: /de-de/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [Plattformübergreifende Befehlszeilenschnittstelle]: /de-de/documentation/articles/xplat-cli-rbac.md "Plattformübergreifende Befehlszeilenschnittstelle"
  [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle]: http://azure.microsoft.com/de-de/documentation/articles/xplat-cli/
  [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager]: http://azure.microsoft.com/de-de/documentation/articles/xplat-cli-azure-resource-manager/
  [Verbindungsherstellung mit Ihren Abonnements]: #connect
  [Überprüfen bestehender Rollenzuweisungen]: #check
  [Erstellen einer Rollenzuweisung]: #create
  [Überprüfen von Berechtigungen]: #verify
  [Nächste Schritte]: #next
  [Rollenbasierte Zugriffssteuerung in Windows Azure]: http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-configure/
  [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: http://azure.microsoft.com/de-de/documentation/articles/azure-preview-portal-using-resource-groups
  [Azure-Blog]: http://blogs.msdn.com/windowsazure
  [Konfigurieren der rollenbasierten Zugriffssteuerung mit Windows PowerShell]: http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-powershell/
  [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung]: http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-troubleshooting/
