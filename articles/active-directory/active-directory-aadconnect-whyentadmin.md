<properties
	pageTitle="Warum ein Unternehmensadministratorkonto erforderlich ist | Microsoft Azure"
	description="Beschreibung der benutzerdefinierten Einstellungen."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Warum beim Einrichten von Azure AD Connect ein Unternehmensadministratorkonto für die Verbindung mit AD DS erforderlich ist

In der folgenden Tabelle wird erläutert, warum für die Einrichtung von Azure AD Connect ein globales Administratorkonto erforderlich ist.

Unter den folgenden Bedingungen | Beschreibung
------------- | ------------- |
Für Express-Einstellungen und DirSync-Upgrade | <li>Für Express-Einstellungen erstellen wir das lokale Active Directory-Konto, das für die Synchronisierung verwendet wird (auch als Active Directory Connector-Konto bezeichnet) und weisen die entsprechenden Berechtigungen für die Synchronisierung und die Kennwortsynchronisierung zu.</li> <li>Für das DirSync-Upgrade setzen wir das Kennwort für das aktuell konfigurierte Active Directory Connector-Konto zurück und konfigurieren den neuen Azure AD Connect-Dienst für die Verwendung dieses Kontos. </li>



**Zusätzliche Ressourcen**


* [Weitere Informationen zu Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-account-summary.md)
* [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect auf MSDN](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->