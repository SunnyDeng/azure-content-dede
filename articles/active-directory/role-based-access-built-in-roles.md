<properties
	pageTitle="RBAC: integrierte Rollen | Microsoft Azure"
	description="Dieses Thema beschreibt die integrierten Rollen der rollenbasierten Zugriffssteuerung (RBAC)."
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

#RBAC: Integrierte Rollen

## Integrierte Rollen

Die rollenbasierte Zugriffssteuerung von Azure umfasst die folgenden integrierten Rollen, die Benutzer, Gruppen und Diensten zugewiesen werden können. Die Definition integrierter Rollen kann nicht geändert werden. In einer zukünftigen Version von Azure RBAC können Sie benutzerdefinierte Rollen definieren. Dazu können Sie aus einer Liste mit verfügbaren Aktionen eine Reihe an Aktionen auswählen, die an Azure-Ressourcen durchgeführt werden dürfen.

Klicken Sie auf den entsprechenden Link, um die **Aktions**- und **Nicht-Aktions**-Eigenschaften einer Rollendefinition anzuzeigen. Die **Aktions**-Eigenschaft gibt die zulässigen Aktionen für Azure-Ressourcen an. Für Aktionszeichenfolgen dürfen Platzhalter verwendet werden. Die **Nicht-Aktions**-Eigenschaft einer Rollendefinition gibt die Aktionen an, die von den zulässigen Aktionen ausgeschlossen werden müssen.


| Rollenname | Beschreibung |
| --------- | ----------- |
| [Mitwirkender des API-Verwaltungsdiensts](#api-management-service-contributor) | Kann Application Insights-Komponenten verwalten |
| [Mitwirkender der Application Insights-Komponente](#application-insights-component-contributor) | Kann Application Insights-Komponenten verwalten |
| [Operator für Automation](#automation-operator) | Kann Aufträge starten, unterbrechen und fortsetzen |
| [Mitwirkender von BizTalk](#biztalk-contributor) | Kann BizTalk-Dienste verwalten |
| [Mitwirkender von ClearDB-MySQL-DB](#cleardb-mysql-db-contributor) | Kann ClearDB MySQL-Datenbanken verwalten |
| [Mitwirkender](#contributor) | Kann alles außer den Zugriff verwalten |
| [Mitwirkender von Data Factory](#data-factory-contributor) | Kann Data Factorys verwalten |
| [DevTest Lab-Benutzer](#devtest-lab-user) | Kann alles anzeigen sowie virtuelle Computer verbinden, starten, neu starten und herunterfahren |
| [Mitwirkender von DocumentDB-Konto](#document-db-account-contributor) | Kann DocumentDB-Konten verwalten |
| [Mitwirkender von Intelligent Systems-Konto](#intelligent-systems-account-contributor) | Kann Intelligent Systems-Konten verwalten |
| [Mitwirkender von virtuellem Netzwerk](#network-contributor) | Kann alle Netzwerkressourcen verwalten |
| [Mitwirkender von NewRelic APM-Konto](#newrelic-apm-account-contributor) | Kann NewRelic Application Performance Management-Konten und -Anwendungen verwalten |
| [Besitzer](#owner) | Besitzer können alles verwalten, einschließlich des Zugriffs. |
| [Leser](#reader) | Leser können alle Dateien anzeigen, jedoch keine Änderungen vornehmen. |
| [Mitwirkender von Redis-Cache](#redis-cache-contributor]) | Kann Redis-Caches verwalten |
| [Mitwirkender von Zeitplanungsauftragssammlung](#scheduler-job-collections-contributor) | Kann Zeitplanungsauftragssammlungen verwalten |
| [Mitwirkender von Suchdienst](#search-service-contributor) | Kann Suchdienste verwalten |
| [Sicherheits-Manager](#security-manager) | Kann Sicherheitskomponenten, Sicherheitsrichtlinien und virtuelle Computer verwalten |
| [Mitwirkender von SQL DB](#sql-db-contributor) | Kann SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien |
| [SQL-Sicherheits-Manager](#sql-security-manager) | Kann die sicherheitsbezogenen Richtlinien von SQL-Servern und SQL-Datenbanken verwalten |
| [Mitwirkender von SQL Server](#sql-server-contributor) | Kann SQL-Server und SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien |
| [Mitwirkender von klassischem Speicherkonto](#classic-storage-account-contributor) | Kann klassische Speicherkonten verwalten |
| [Mitwirkender von Speicherkonto](#storage-account-contributor) | Kann Speicherkonten verwalten |
| [Benutzerzugriffsadministrator](#user-access-administrator) | Kann den Benutzerzugriff auf Azure-Ressourcen verwalten |
| [Mitwirkender von klassischen virtuellen Computern](#classic-virtual-machine-contributor) | Kann klassische virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind |
| [Mitwirkender von virtuellen Computern](#virtual-machine-contributor) | Kann virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind |
| [Mitwirkender von klassischem Netzwerk](#classic-network-contributor) | Kann virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind |
| [Mitwirkender von Webplan](#web-plan-contributor) | Kann Webpläne verwalten |
| [Mitwirkender von Website](#website-contributor) | Kann Websites verwalten, jedoch nicht die Webpläne, mit denen sie verbunden sind |

### Mitwirkender des API-Verwaltungsdienstes
Kann API-Verwaltungsdienste verwalten

| **Aktionen** | |
| ------- | ------ |
| Microsoft.ApiManagement/Services/* | Erstellen und Verwalten von API-Verwaltungsdiensten |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender der Application Insights-Komponente
Kann Application Insights-Komponenten verwalten

| **Aktionen** | |
| ------- | ------ |
| Microsoft.Insights/components/* | Erstellen und Verwalten von Insights-Komponenten |
| Microsoft.Insights/webtests/* | Erstellen und Verwalten von Webtests |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Operator für Automation
Operatoren für Automation können Aufträge starten, unterbrechen und fortsetzen.

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Automation/automationAccounts/read | Lesen von Automation-Konten |
| Microsoft.Automation/automationAccounts/runbooks/read | Lesen von Automation-Runbooks |
| Microsoft.Automation/automationAccounts/schedules/read | Lesen von Zeitplänen für Automation-Konten |
| Microsoft.Automation/automationAccounts/schedules/write | Schreiben von Zeitplänen für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/read | Lesen von Aufträgen für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/write | Schreiben von Aufträgen für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Beenden eines Auftrags für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Unterbrechen eines Auftrags für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Fortsetzen eines Auftrags für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Lesen eines Auftragszeitplans für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Lesen eines Auftragszeitplans für Automation-Konten |

### Mitwirkender von BizTalk
Kann BizTalk-Dienste verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.BizTalkServices/BizTalk/* | Erstellen und Verwalten von BizTalk-Diensten |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von ClearDB-MySQL-DB
Kann ClearDB MySQL-Datenbanken verwalten

| **Aktionen** ||
| ------- | ------ |
| successbricks.cleardb/databases/* | Erstellen und Verwalten von ClearDB MySQL-Datenbanken |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender
Mitwirkende können alles mit Ausnahme des Zugriffs verwalten.

| **Aktionen** ||
| ------- | ------ |
| * | Erstellen und Verwalten von Ressourcen aller Typen |
| ****Nicht-Aktionen | | | Microsoft.Authorization/*/Write | Kann keine Rollen und Rollenzuweisungen erstellen | | Microsoft.Authorization/*/Delete | Kann keine Rollen und Rollenzuweisungen löschen |

### Mitwirkender von Data Factory
Kann Data Factorys verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.DataFactory/dataFactories/* | Erstellen und Verwalten von Data Factorys |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### DevTest Lab-Benutzer
Kann alles anzeigen sowie virtuelle Computer verbinden, starten, neu starten und herunterfahren

| **Aktionen** ||
| ------- | ------ |
| */read | Lesen von Ressourcen aller Typen | | Microsoft.DevTestLab/labs/labStats/action | Lesen von Lab-Statistiken | | Microsoft.DevTestLab/Environments/* | Erstellen und Verwalten von Umgebungen |
| Microsoft.DevTestLab/labs/createEnvironment/action | Erstellen einer Lab-Umgebung |
| Microsoft.Compute/virtualMachines/start/action | Starten virtueller Computer |
| Microsoft.Compute/virtualMachines/restart/action | Neustarten virtueller Computer |
| Microsoft.Compute/virtualMachines/deallocate/action | Aufhebung der Zuordnung virtueller Computer |
| Microsoft.Storage/storageAccounts/listKeys/action | Auflisten von Speicherkontoschlüsseln |
| Microsoft.Network/virtualNetworks/join/action | Verknüpfen von virtuellen Netzwerken |
| Microsoft.Network/loadBalancers/join/action | Verknüpfen von Load Balancern |
| Microsoft.Network/publicIPAddresses/link/action | Verknüpfen mit öffentlichen IP-Adressen |
| Microsoft.Network/networkInterfaces/link/action | Verknüpfen mit Netzwerkschnittstellen |
| Microsoft.Network/networkInterfaces/write | Schreiben in Netzwerkschnittstellen |

### Mitwirkender von DocumentDB-Konto
Kann DocumentDB-Konten verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.DocumentDb/databaseAccounts/* | Erstellen und Verwalten von DocumentDB-Konten |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von Intelligent Systems-Konto
Kann Intelligent Systems-Konten verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.IntelligentSystems/accounts/* | Erstellen und Verwalten von Intelligent Systems-Konten |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von virtuellem Netzwerk
Kann alle Netzwerkressourcen verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Network/* | Erstellen und Verwalten von Netzwerken |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von NewRelic APM-Konto
Kann NewRelic Application Performance Management-Konten und -Anwendungen verwalten

| **Aktionen** ||
| ------- | ------ |
| NewRelic.APM/accounts/* | Erstellen und Verwalten von NewRelic Application Performance Management-Konten |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Besitzer
Kann alles verwalten, einschließlich des Zugriffs

| **Aktionen** ||
| ------- | ------ |
| * | Erstellen und Verwalten von Ressourcen aller Typen |

### Leser
Kann alles anzeigen, jedoch keine Änderungen vornehmen

| **Aktionen** ||
| ------- | ------ |
| **/read | Lesen von Ressourcen aller Typen, mit Ausnahme geheimer Schlüssel |

### Mitwirkender von Redis-Cache
Kann Redis-Caches verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Cache/redis/* | Erstellen und Verwalten von Redis-Caches |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von Zeitplanungsauftragssammlung
Kann Zeitplanungsauftragssammlungen verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Scheduler/jobcollections/* | Erstellen und Verwalten von Auftragssammlungen |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von Suchdienst
Kann Suchdienste verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Search/searchServices/* | Erstellen und Verwalten von Suchdiensten |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Sicherheits-Manager
Kann Sicherheitskomponenten, Sicherheitsrichtlinien und virtuelle Computer verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/*/read | Lesen von Konfigurationsinformationen zu klassischem Netzwerk |
| Microsoft.ClassicCompute/*/read | Lesen von Konfigurationsinformationen zu klassischen virtuellen Compute-Computern |
| Microsoft.ClassicCompute/virtualMachines/*/write | Schreiben der Konfiguration für virtuelle Computer |
| Microsoft.Security/* | Erstellen und Verwalten von Sicherheitskomponenten und -richtlinien |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von SQL DB
Kann SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Lesen von SQL Server |
| Microsoft.Sql/servers/databases/* | Erstellen und Verwalten von SQL-Datenbanken |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
| **Nicht-Aktionen** | |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Kann keine Überwachungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Kann keine Verbindungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Kann keine Datenmaskierungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/databases/securityMetrics/* | Kann keine Sicherheitsmetriken bearbeiten |

### SQL-Sicherheits-Manager
Kann die sicherheitsbezogenen Richtlinien von SQL-Servern und SQL-Datenbanken verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Lesen von SQL Server |
| Microsoft.Sql/servers/auditingPolicies/* | Erstellen und Verwalten von SQL Server-Überwachungsrichtlinien |
| Microsoft.Sql/servers/databases/read | Lesen von SQL-Datenbanken |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Erstellen und Verwalten von Überwachungsrichtlinien von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Erstellen und Verwalten von Verbindungsrichtlinien von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Erstellen und Verwalten von Datenmaskierungsrichtlinien von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/securityMetrics/* | Erstellen und Verwalten von Sicherheitsmetriken von SQL Server-Datenbanken |
| Microsoft.Authorization/*/read | Lesen der Microsoft-Autorisierung |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
| Microsoft.Sql/servers/databases/schemas/read | Lesen von Datenbankschemas von SQL-Servern |
| Microsoft.Sql/servers/databases/schemas/tables/read | Lesen von Datenbanktabellen von SQL-Servern |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Lesen von Spalten der Datenbanktabellen von SQL-Servern |

### Mitwirkender von SQL Server
Kann SQL-Server und SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Sql/servers/* | Erstellen und Verwalten von SQL-Servern |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen|
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
| **Nicht-Aktionen** | |
| Microsoft.Sql/servers/auditingPolicies/* | Kann keine SQL Server-Überwachungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Kann keine Überwachungsrichtlinien von SQL Server-Datenbanken bearbeiten |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Kann keine Verbindungsrichtlinien von SQL Server-Datenbanken bearbeiten |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Kann keine Datenmaskierungsrichtlinien von SQL Server-Datenbanken bearbeiten |
| Microsoft.Sql/servers/databases/securityMetrics/* | Kann keine Sicherheitsmetriken von SQL Server-Datenbanken bearbeiten |

### Mitwirkender von klassischem Speicherkonto
Kann klassische Speicherkonten verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/* | Erstellen und Verwalten von Speicherkonten |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Resources/subscriptions/resources/read | Lesen von Abonnementressourcen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von Speicherkonto
Kann Speicherkonten verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/* | Erstellen und Verwalten von Speicherkonten |
| Microsoft.Authorization/*/read | Lesen aller Autorisierungen |
| Microsoft.Resources/subscriptions/resources/read | Lesen von Abonnementressourcen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Benutzerzugriffsadministrator
Kann den Benutzerzugriff auf Azure-Ressourcen verwalten

| **Aktionen** ||
| ------- | ------ |
| */read | Lesen von Ressourcen aller Typen, mit Ausnahme geheimer Schlüssel | | Microsoft.Authorization/* | Lesen von Autorisierungen |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von klassischen virtuellen Computern
Kann klassische virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind

| **Aktionen** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/read | Lesen von klassischen Speicherkonten |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Auflisten von Speicherkontoschlüsseln |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Lesen von Speicherkontodatenträgern |
| Microsoft.ClassicStorage/storageAccounts/images/read | Lesen von Speicherkontoimages |
| Microsoft.ClassicNetwork/virtualNetworks/read | Lesen von virtuellen Netzwerken |
| Microsoft.ClassicNetwork/reservedIps/read | Lesen von reservierten IP-Adressen |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Verknüpfen von virtuellen Netzwerken |
| Microsoft.ClassicNetwork/reservedIps/link/action | Verknüpfen von reservierten IP-Adressen |
| Microsoft.ClassicCompute/domainNames/* | Erstellen und Verwalten von klassischen Compute-Domänennamen |
| Microsoft.ClassicCompute/virtualMachines/* | Erstellen und Verwalten von virtuellen Computern |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von virtuellen Computern
Kann virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/read | Lesen von Speicherkonten |
| Microsoft.Storage/storageAccounts/listKeys/action | Auflisten von Speicherkontoschlüsseln |
| Microsoft.Network/virtualNetworks/read | Lesen von virtuellen Netzwerken |
| Microsoft.Network/virtualNetworks/subnets/join/action | Verknüpfen von Subnetzen in virtuellen Netzwerken |
| Microsoft.Network/loadBalancers/read | Lesen von Load Balancern |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Verknüpfen von Back-End-Adresspools für Load Balancer |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Verknüpfen von eingehenden NAT-Regeln für Load Balancer |
| Microsoft.Network/publicIPAddresses/read | Lesen von öffentlichen IP-Adressen im Netzwerk |
| Microsoft.Network/publicIPAddresses/join/action | Verknüpfen von öffentlichen IP-Adressen im Netzwerk |
| Microsoft.Network/networkSecurityGroups/read | Lesen von Netzwerksicherheitsgruppen |
| Microsoft.Network/networkSecurityGroups/join/action | Verknüpfen von Netzwerksicherheitsgruppen |
| Microsoft.Network/networkInterfaces/* | Erstellen und Verwalten von Netzwerkschnittstellen |
| Microsoft.Network/locations/* | Erstellen und Verwalten von Netzwerkspeicherorten |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Verknüpfen von Back-End-Adresspools für Netzwerkanwendungsgateway |
| Microsoft.Compute/virtualMachines/* | Erstellen und Verwalten von virtuellen Computern |
| Microsoft.Compute/availabilitySets/* | Erstellen und Verwalten von Compute-Verfügbarkeitsgruppen |
| Microsoft.Compute/locations/* | Erstellen und Verwalten von Compute-Speicherorten |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von klassischem Netzwerk
Kann klassische virtuelle Netzwerke und reservierte IP-Adressen verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/* | Erstellen und Verwalten von klassischen Netzwerken |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von Webplan
Kann Webpläne verwalten

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/* | Erstellen und Verwalten von Serverfarmen |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### Mitwirkender von Website
Kann Websites verwalten, jedoch nicht die Webpläne, mit denen sie verbunden sind

| **Aktionen** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/read | Lesen von Serverfarmen |
| Microsoft.Web/serverFarms/join/action | Verknüpfen von Serverfarmen |
| Microsoft.Web/sites/* | Erstellen und Verwalten von Websites |
| Microsoft.Web/certificates/* | Erstellen und Verwalten von Websitezertifikaten |
| Microsoft.Web/listSitesAssignedToHostName/read | Lesen von Websites, die einem Hostnamen zugewiesen sind |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lesen von Ressourcen für Ressourcengruppen für Abonnements |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen für Abonnements |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
| Microsoft.Insights/components/* | Erstellen und Verwalten von Insights-Komponenten |

## RBAC-Themen
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->