<properties
	pageTitle="Erstellen eines Verlaufsberichts zu Zugriffsänderungen | Microsoft Azure"
	description="Erstellen Sie einen Bericht, in dem alle Änderungen am Zugriff auf Ihre Azure-Abonnements mit rollenbasierter Zugriffssteuerung von Azure in den letzten 90 Tagen aufgeführt sind."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/23/2016"
	ms.author="kgremban"/>

# Erstellen eines Verlaufsberichts zu Zugriffsänderungen

Wenn Sie nicht der alleinige Besitzer der Azure-Abonnements oder der Ressourcen und Ressourcengruppen darin sind, müssen Sie alle Zugriffsänderungen nachverfolgen können. Jedes Mal, wenn ein Benutzer in Ihren Abonnement Zugriff gewährt bzw. widerruft, werden die Änderungen in Azure-Ereignissen protokolliert. Sie können Verlaufsberichte zu Zugriffsänderungen erstellen, um alle Änderungen der letzten 90 Tage anzuzeigen.

## Erstellen eines Berichts mit Azure PowerShell
Verwenden Sie zum Erstellen eines Verlaufsberichts zu Zugriffsänderungen in PowerShell den folgenden Befehl:

```
Get-AzureRMAuthorizationChangeLog
```

Sie können angeben, welche Eigenschaft der Zuweisungen aufgeführt werden soll, u.a.:

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| **Aktion** | Gibt an, ob der Zugriff erteilt oder widerrufen wurde. |
| **Caller** | Der für die Zugriffsänderung verantwortliche Besitzer |
| **Date** | Das Datum und die Uhrzeit, an dem bzw. zu der der Zugriff geändert wurde |
| **DirectoryName** | Das Azure Active Directory-Verzeichnis |
| **PrincipalName** | Der Name des Benutzers, der Gruppe oder der Anwendung |
| **PrincipalType** | Gibt an, ob die Zuordnung für einen Benutzer, eine Gruppe oder eine Anwendung erfolgt ist. |
| **RoleId** | Die GUID der Rolle, die erteilt oder widerrufen wurde |
| **RoleName** | Die Rolle, die erteilt oder widerrufen wurde |
| **ScopeName** | Der Name des Abonnements, der Ressourcengruppe oder der Ressource |
| **ScopeType** | Gibt an, ob die Zuweisung im Abonnement-, Ressourcengruppen- oder Ressourcenbereich erfolgt ist. |
| **SubscriptionId** | Die GUID des Azure-Abonnements |
| **SubscriptionName** | Der Name des Azure-Abonnements |

Mit dem folgenden Beispielbefehl werden alle Zugriffsänderungen im Abonnement für die letzten 7 Tage aufgeführt:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog – Screenshot](./media/role-based-access-control-configure/access-change-history.png)

## Erstellen eines Berichts über die Azure-Befehlszeilenschnittstelle
Verwenden Sie zum Erstellen eines Verlaufsberichts zu Zugriffsänderungen in der Azure-Befehlszeilenschnittstelle den folgenden Befehl:
```
azure authorization changelog
```

## Exportieren in eine Kalkulationstabelle
Wenn Sie den Bericht speichern oder die Daten bearbeiten möchten, exportieren Sie die Zugriffsänderungen in eine CSV-Datei. Sie können dann den Bericht in einer Kalkulationstabelle zur Überprüfung anzeigen.

![Änderungsprotokoll als Arbeitsblatt – Screenshot](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Weitere Informationen
- Erste Schritte mit der [rollenbasierten Zugriffssteuerung von Azure](role-based-access-control-configure.md)
- Arbeiten mit [benutzerdefinierten Rollen in Azure RBAC](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0330_2016-->