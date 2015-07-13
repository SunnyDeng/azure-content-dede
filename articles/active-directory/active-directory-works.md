<properties
	pageTitle="Funktionsweise von Azure AD"
	description="Azure AD bietet eine Identitätslösung, die Sie in der Cloud verwalten können. Sie kann mit Ihrem lokalen Identitätssystem verbunden oder unabhängig verwendet werden."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="curtand"/>



# Funktionsweise von Azure Active Directory


###Weitere Artikel zu diesem Thema
[Was ist Azure AD?](active-directory-whatis.md)<br> [Wie funktioniert Azure AD?](active-directory-works.md)<br> [Erste Schritte](active-directory-get-started.md)<br> [Nächste Schritte](active-directory-next-steps.md)<br> [Weitere Informationen](active-directory-learn-map.md)


Azure AD bietet eine Identitätslösung, die Sie in der Cloud verwalten können. Sie kann mit Ihrem lokalen Identitätssystem verbunden oder unabhängig verwendet werden.

Stellen Sie sich ein Konto in Azure AD als Führerschein für die Cloud vor. Es handelt sich um Ihre eindeutige ID für den Zugriff auf Onlinedienste. In diesem Sinne funktioniert Azure AD wie Ihre eigene private Registrierungsstelle in der Cloud für diese Führerscheine. Azure AD ermöglicht das Verwenden von Identitäten überall in der Cloud und verbessert die Mobilität für Benutzer, die lokal auf Ressourcen zugreifen.

> [AZURE.NOTE]Um Azure Active Directory verwenden zu können, benötigen Sie ein Azure-Konto. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](http://azure.microsoft.com/pricing/free-trial/).

## Wie unterstützt Azure AD Office 365, Microsoft Intune und andere Azure-Dienste?
Das Azure-Portal, Office 365 Admin Center, Microsoft Intune-Kontoportal und die Cmdlets des Azure AD PowerShell-Moduls lesen und schreiben Daten in eine einzelne freigegebene Instanz von Azure AD, die Ihrem Verzeichnis zugeordnet ist. Portale (oder Cmdlets) fungieren als Front-End-Schnittstelle, die Ihre Verzeichnisinformationen abruft oder ändert. [Erfahren Sie mehr über die Unterstützung für andere Dienste](active-directory-administer.md#what-is-an-azure-ad-tenant)

## Wie unterstützt Azure AD Anwendungen von Drittanbietern?
Azure AD vereinfacht die Authentifizierung für Entwickler durch das Bereitstellen einer Identitätslösung als Dienst sowie von Open-Source-Bibliotheken für unterschiedliche Plattformen, damit Sie schnell in das Programmieren einsteigen können. [Erfahren Sie mehr über Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md).


## Wie erweitert Azure AD mein lokales Verzeichnis?
Azure AD unterstützt mehrere der am häufigsten verwendeten Protokolle zur Authentifizierung und Autorisierung. [Weitere Informationen zu Azure Active Directory-Authentifizierungsprotokollen](active-directory-authentication-scenarios.md).

## Wie unterstützt Azure die Verwaltung von Identitäten?
Wünschen Sie weitere Informationen zum Verwalten von Azure AD? Wie wird ein Verzeichnis eingerichtet? Wie wird ein Verzeichnis gelöscht? Wie werden Verzeichnisdaten verwaltet? Erfahren Sie mehr über das Verwalten des Azure AD-Verzeichnisses. [Erfahren Sie mehr über das Verwalten von Azure AD](active-directory-administer.md).

## Zusätzliche Ressourcen

* [Als Organisation für Azure registrieren](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)
 

<!---HONumber=62-->