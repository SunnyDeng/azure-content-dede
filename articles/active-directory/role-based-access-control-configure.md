<properties
	pageTitle="Rollenbasierte Zugriffssteuerung in Azure Active Directory | Microsoft Azure"
	description="In diesem Artikel wird die rollenbasierte Zugriffssteuerung in Azure beschrieben."
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

# Rollenbasierte Zugriffssteuerung in Azure Active Directory

## Rollenbasierte Zugriffssteuerung
Die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe von RBAC können Sie Aufgaben in Ihrem DevOps-Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

### Grundlagen zur Zugriffsverwaltung in Azure
Jedes Azure-Abonnement befindet sich in einem Azure Active Directory. Nur Benutzern, Gruppen und Anwendungen aus diesem Verzeichnis kann Verwaltungszugriff auf Ressourcen im zugehörigen Azure-Abonnement gewährt werden. Die Verwaltung kann über das Azure-Verwaltungsportal, Azure-Befehlszeilentools und Azure-Verwaltungs-APIs erfolgen.

Der Zugriff wird gewährt, indem Benutzern, Gruppen und Anwendungen die jeweils geeignete RBAC-Rolle auf der richtigen Ebene zugewiesen wird. Um Zugriff auf das gesamten Abonnement zu gewähren, weisen Sie eine Rolle auf Abonnementebene zu. Um Zugriff auf eine bestimmte Ressourcengruppe innerhalb eines Abonnements zu gewähren, weisen Sie eine Rolle auf Ressourcengruppenebene zu. Sie können Rollen auch für bestimmte Ressourcen zuweisen, wie z. B.für Websites, virtuelle Computer und Subnetze, um nur Zugriff auf eine Ressource zu gewähren.

![](./media/role-based-access-control-configure/overview.png)

Die RBAC-Rolle, die Sie Benutzern, Gruppen und Anwendungen zuweisen, legt fest, welche Ressourcen der Benutzer (bzw. die Gruppe oder Anwendung) auf dieser Ebene verwalten kann.

### Integrierte RBAC-Rollen in Azure
Die rollenbasierte Zugriffssteuerung in Azure verfügt über drei grundlegende Rollen, die für alle Ressourcentypen gelten: Besitzer, Mitwirkender und Leser. Besitzer verfügen über vollständigen Zugriff auf alle Ressourcen, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren. Mitwirkende können alle Arten von Azure-Ressourcen erstellen und verwalten, aber keinen anderen Personen Zugriff gewähren. Leser können nur vorhandene Azure-Ressourcen anzeigen. Die verbleibenden RBAC-Rollen in Azure ermöglichen die Verwaltung von bestimmten Azure-Ressourcen. Beispielsweise ermöglicht die Rolle "Mitwirkender für virtuelle Computer" die Erstellung und Verwaltung von virtuellen Computern, nicht jedoch des virtuellen Netzwerks oder des Subnetzes, mit dem der virtuelle Computer eine Verbindung herstellt.

Unter [Integrierte RBAC-Rollen](role-based-access-built-in-roles.md) werden die in Azure verfügbaren integrierten RBAC-Rollen aufgeführt. Für jede Rolle werden die Vorgänge angegeben, für die eine integrierte Rolle den Zugriff gewährt.

### Ressourcenhierarchie und Zugriffsvererbung in Azure
Jedes Abonnement in Azure gehört zu einem einzigen Verzeichnis, jede Ressourcengruppe gehört zu einem einzigen Abonnement, und jede Ressource gehört zu einer einzigen Ressourcengruppe. Der Zugriff, den Sie auf übergeordneter Ebene gewähren, wird auf untergeordneter Ebene geerbt. Wenn Sie einer Azure AD-Gruppe die Rolle "Leser" auf Abonnementebene zuweisen, können die Mitglieder dieser Rolle alle Ressourcengruppen und alle Ressourcen in diesem Abonnement anzeigen. Wenn Sie einer Anwendung auf Ressourcengruppenebene die Rolle "Mitwirkender" zuweisen, kann diese Anwendung alle Ressourcentypen in dieser Ressourcengruppe verwalten. Sie kann jedoch keine anderen Ressourcengruppen im Abonnement verwalten.

### Azure RBAC im Vergleich zu klassischen Administratoren und Co-Admins für Abonnements
Klassische Administratoren und Co-Admins für Abonnements verfügen über vollständigen Zugriff auf das Azure-Abonnement. Sie können Ressourcen sowohl über das klassische Portal (https://manage.windowsazure.com) und Azure Service Manager-APIs als auch über das neue Verwaltungsportal (https://portal.azure.com) und die neuen Azure-Ressourcen-Manager-APIs verwalten. Im RBAC-Modell wird klassischen Administratoren die Besitzerrolle auf Abonnementebene zugewiesen.

Das präzisere Autorisierungsmodell (Azure RBAC) wird nur vom neuen Verwaltungsportal (https://portal.azure.com) und den Azure-Ressourcen-Manager-APIs unterstützt. Benutzer und Anwendungen, denen eine RBAC-Rolle zugewiesen ist (auf Abonnement-/Ressourcengruppen-/Ressourcenebene), können das klassische Verwaltungsportal (http://manage.windowsazure.com) und die Azure Service Manager-APIs nicht verwenden.

### Autorisierung für Verwaltungsvorgänge im Vergleich zu Datenvorgängen
Das präzisere Autorisierungsmodell (Azure RBAC)wird nur für Verwaltungsvorgänge für die Azure-Ressourcen im Azure-Portal und in den Azure-Ressourcen-Manager-APIs unterstützt. Über RBAC können nicht alle Vorgänge auf Datenebene für Azure-Ressourcen autorisiert werden. Während das Erstellen/Lesen/Aktualisieren/Löschen von Speicherkonten über RBAC gesteuert werden kann, lassen sich dieselben Vorgänge für Blobs oder Tabellen in einem Speicherkonto noch nicht über RBAC steuern. Ebenso kann das Erstellen/Lesen/Aktualisieren/Löschen einer SQL-Datenbank über RBAC gesteuert werden, während sich dieselben Vorgänge noch nicht für SQL-Tabellen in einer Datenbank über RBAC steuern lassen.

## Verwalten des Zugriffs über das Azure-Verwaltungsportal
### Zugriff anzeigen
Wählen Sie die Zugriffseinstellungen auf dem Blatt der Ressourcengruppe im Abschnitt mit den grundlegenden Informationen aus. Auf dem Blatt **Benutzer** werden alle Benutzer, Gruppen und Anwendungen aufgeführt, denen Zugriff auf die Ressourcengruppe gewährt wurde. Der Zugriff wird entweder in der Ressourcengruppe zugewiesen oder aus einer Zuweisung im übergeordneten Abonnement geerbt.

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]Klassische Administratoren und Co-Admins für Abonnements sind im neuen RBAC-Modell de facto Besitzer des Abonnements.

### Zugriff hinzufügen
1. Klicken Sie auf dem Blatt **Benutzer** auf das Symbol **Hinzufügen**. ![](./media/role-based-access-control-configure/grant-access1.png)
2. Wählen Sie die Rolle aus, die Sie zuweisen möchten.
3. Suchen Sie nach dem Benutzer, der Gruppe oder der Anwendung, dem oder der Sie Zugriff gewähren möchten, und wählen Sie das entsprechende Element aus.
4. Durchsuchen Sie das Verzeichnis anhand von Anzeigenamen, E-Mail-Adressen und Objektbezeichnern nach Benutzern, Gruppen und Anwendungen.![](./media/role-based-access-control-configure/grant-access2.png)

### Zugriff entfernen
1. Wählen Sie auf dem Blatt **Benutzer** die Rollenzuweisung aus, die Sie entfernen möchten.
2. Klicken Sie auf dem Blatt mit den Zuweisungsdetails auf das Symbol **Entfernen**.
3. Klicken Sie auf **Ja**, um die Entfernung zu bestätigen.

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]Geerbte Zuweisungen können auf untergeordneter Ebene nicht entfernt werden. Wechseln Sie auf die übergeordnete Ebene, und entfernen Sie die entsprechenden Zuweisungen.


![](./media/role-based-access-control-configure/remove-access2.png)

## Verwalten des Zugriffs mit Azure PowerShell
Der Zugriff kann mithilfe von Azure RBAC-Befehlen in den Azure PowerShell-Tools verwaltet werden.

-	Verwenden Sie `Get-AzureRoleDefinition` zum Auflisten von RBAC-Rollen für die Zuweisung und zum Überprüfen der Vorgänge, auf die sie Zugriff gewähren.

-	Verwenden Sie `Get-AzureRoleAssignment` zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement bzw. in der angegebenen Ressource oder Ressourcengruppe gelten. Verwenden Sie `ExpandPrincipalGroups` zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie zu den Gruppen, denen der Benutzer angehört. Verwenden Sie den `IncludeClassicAdministrators`-Parameter, um auch die klassischen Administratoren und Co-Admins für Abonnements aufzulisten.

-	Verwenden Sie `New-AzureRoleAssignment` zum Gewähren des Zugriffs für Benutzer, Gruppen und Anwendungen.

-	Verwenden Sie `Remove-AzureRoleAssignment` zum Entfernen des Zugriffs.

Detaillierte Beispiele zur Verwaltung des Zugriffs mithilfe von Azure PowerShell finden Sie unter [Verwalten des Zugriffs mit Azure PowerShell](role-based-access-control-manage-access-powershell.md).

## Verwalten des Zugriffs über die Azure-Befehlszeilenschnittstelle
Der Zugriff kann mithilfe von Azure RBAC-Befehlen in der Azure-Befehlszeilenschnittstelle verwaltet werden.

-	Verwenden Sie `azure role list` zum Auflisten der für die Zuweisung verfügbaren RBAC-Rollen. Verwenden Sie "azure role show" zum Überprüfen der Vorgänge, auf welche die Rollen Zugriff gewähren.

-	Verwenden Sie `azure role assignment list` zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement bzw. in der angegebenen Ressource oder Ressourcengruppe gelten. Verwenden Sie die `expandPrincipalGroups`-Option zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie zu den Gruppen, denen der Benutzer angehört. Verwenden Sie den `includeClassicAdministrators`-Parameter, um auch die klassischen Administratoren und Co-Admins für Abonnements aufzulisten.

-	Verwenden Sie `azure role assignment create` zum Gewähren des Zugriffs für Benutzer, Gruppen und Anwendungen.

-	Verwenden Sie `azure role assignment delete` zum Entfernen des Zugriffs.

Detaillierte Beispiele zur Verwaltung des Zugriffs mithilfe der Azure-Befehlszeilenschnittstelle finden Sie unter [Verwalten des Zugriffs über die Azure-Befehlszeilenschnittstelle](role-based-access-control-manage-access-azure-cli.md).

## Verwenden des Verlaufsberichts zu Zugriffsänderungen
Alle Zugriffsänderungen, die in Ihrem Azure-Abonnement vorgenommen werden, werden in Azure-Ereignissen protokolliert.

### Erstellen eines Berichts mit Azure PowerShell
Verwenden Sie folgenden PowerShell-Befehl, um einen Bericht zu erstellen, der darüber informiert, wer welche Art von Zugriff auf welcher Ebene in Ihren Azure-Abonnements gewährt oder widerrufen hat.

    Get-AzureAuthorizationChangeLog

### Erstellen eines Berichts über die Azure-Befehlszeilenschnittstelle
Verwenden Sie folgenden Befehl in der Azure-Befehlszeilenschnittstelle, um einen Bericht zu erstellen, der darüber informiert, wer welche Art von Zugriff auf welcher Ebene in Ihren Azure-Abonnements gewährt oder widerrufen hat.

    azure authorization changelog

> [AZURE.NOTE]Zugriffsänderungen können für die letzten 90 Tage abgefragt werden (in Zeiträumen von jeweils 15 Tagen).

Das folgende Beispiel führt alle Zugriffsänderungen im Abonnement für die letzten 7 Tage auf.

![](./media/role-based-access-control-configure/access-change-history.png)

### Exportieren der Zugriffsänderungen in eine Kalkulationstabelle
Zugriffsänderungen lassen sich zum einfacheren Überprüfen in eine Kalkulationstabelle exportieren.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

<!---HONumber=Oct15_HO3-->