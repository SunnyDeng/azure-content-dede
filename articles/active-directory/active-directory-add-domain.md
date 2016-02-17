<properties
	pageTitle="Verwenden von benutzerdefinierten Domänennamen zum Vereinfachen des Anmeldevorgangs für Benutzer | Microsoft Azure"
	description="Hier wird erläutert, wie Sie Ihren eigenen Domänennamen und zugehörige Informationen zu Azure Active Directory hinzufügen."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# Verwenden von benutzerdefinierten Domänennamen zum Vereinfachen des Anmeldevorgangs für Benutzer

Sie können Ihre eigenen benutzerdefinierten Domänennamen verwenden, um die Anmeldung und andere Benutzervorgänge in Azure Active Directory zu verbessern und zu vereinfachen. Falls Ihr Unternehmen beispielsweise im Besitz des Domänennamens „contoso.com“ ist, können sich Ihre Benutzer mit den vertrauten Benutzernamen anmelden, z. B. „joe@contoso.com“.

Jedes Verzeichnis in Azure Active Directory verfügt über einen integrierten Domänennamen der Form „contoso.onmicrosoft.com“, den Sie verwenden können, um mit der Nutzung von Azure oder anderen Microsoft-Diensten zu beginnen. [Erfahren Sie mehr über integrierte Domänen](#built-in-domain-names-for-azure-active-directory).

## Verwenden Ihres benutzerdefinierten Domänennamens mit Azure AD

Wenn Ihr Unternehmen im Besitz eines benutzerdefinierten Domänennamens ist, der Ihren Benutzern vertraut ist, ist es am besten, diesen benutzerdefinierten Domänennamen mit Azure Active Directory zu verwenden. Führen Sie folgende Schritte aus, um einen benutzerdefinierten Domänennamen mit Azure Active Directory zu verwenden:

-   [Hinzufügen und Überprüfen Ihres benutzerdefinierten Domänennamens](active-directory-add-domain-add-verify-general.md)

-   [Zuweisen von Benutzern zur benutzerdefinierten Domäne](active-directory-add-domain-add-users.md)

## Verwenden Ihres benutzerdefinierten Domänennamens mit Office 365 und anderen Diensten

Wie andere Ressourcen in Ihrer Azure AD-Umgebung auch, können benutzerdefinierte Domänennamen, die Sie hinzugefügt und überprüft haben, mit Office 365, Intune und anderen Anwendungen verwendet werden, für die Azure AD genutzt wird. Beim Verwenden eines benutzerdefinierten Domänennamens mit Exchange Online können Benutzer beispielsweise unter bekannten E-Mail-Adressen Mails senden und empfangen, z. B. joe@contoso.com. Um die Verwendung benutzerdefinierter Domänen für andere Anwendungen zu ermöglichen, müssen Sie bei der DNS-Registrierungsstelle zusätzliche DNS-Einträge so hinzufügen, wie dies in der Dokumentation der Anwendung angegeben ist.

-   [Verwenden benutzerdefinierter Domänen mit Office 365](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=de-DE&rs=de-DE&ad=US)

-   [Verwenden benutzerdefinierter Domänen mit Intune](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Verwalten von Domänennamen in Azure AD

Für Domänennamen ist im Allgemeinen keine fortlaufende Verwaltung oder Administration in Azure Active Directory erforderlich.

-   [Weitere Informationen finden Sie in der Liste mit den Domänennamen in Azure Active Directory.](active-directory-add-domain-add-users.md)

-   [Vorgehensweise bei Änderung der DNS-Registrierungsstelle für Ihren benutzerdefinierten Domänennamen](active-directory-add-domain-change-registrar.md)

## Löschen eines benutzerdefinierten Domänennamens

Sie können einen benutzerdefinierten Domänennamen aus Azure AD löschen, wenn dieser von Ihrem Unternehmen nicht mehr verwendet wird oder wenn Sie den Domänennamen für eine andere Azure AD-Instanz verwenden möchten.

-   [Löschen eines benutzerdefinierten Domänennamens](#_Deleting_a_custom)

## Integrierte Domänennamen für Azure Active Directory

Sie können integrierte Domänen in Azure Active Directory (Azure AD) und die von Ihnen hinzugefügten Domänen unterscheiden.

-   Integriert: Domäne, die bereits im Azure AD-Verzeichnis enthalten ist und die Form „contoso.onmicrosoft.com“ hat

-   Benutzerdefiniert: Domänenname, der sich bereits im Besitz Ihres Unternehmens befindet, z. B. „contoso.com“

## Verwenden von PowerShell oder der Graph-API zum Verwalten von Domänennamen

Die meisten Verwaltungsaufgaben für Domänennamen in Azure Active Directory können auch mit Microsoft PowerShell oder programmgesteuert mit der Graph-API durchgeführt werden.

-   [Verwenden von PowerShell zum Verwalten von Domänennamen in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Verwenden der Graph-API zum Verwalten von Domänennamen in Azure AD (Vorschau)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## Nächste Schritte

Falls Sie zusätzliche Ressourcen zur Verwendung von Domänennamen in Azure Active Directory benötigen, helfen Ihnen die folgenden Themen weiter:

- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Hinzufügen und Überprüfen eines benutzerdefinierten Domänennamens in Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Zuweisen von Benutzern zu einer benutzerdefinierten Domäne](active-directory-add-domain-add-users.md)
- [Ändern der DNS-Registrierungsstelle für Ihren benutzerdefinierten Domänennamen](active-directory-add-domain-change-registrar.md)
- [Löschen einer benutzerdefinierten Domäne in Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->