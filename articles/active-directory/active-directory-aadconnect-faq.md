<properties
	pageTitle="Häufig gestellte Fragen zu Azure AD Connect | Microsoft Azure"
	description="Auf dieser Seite finden Sie häufig gestellte Fragen zu Azure AD Connect."
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
	ms.date="11/10/2015"
	ms.author="billmath"/>

# Häufig gestellte Fragen zu Azure Active Directory Connect

## Allgemeine Installation
**F: Ist die Installation möglich, wenn der globale Azure AD-Administrator 2FA aktiviert hat?**

Die Installation ist in diesem Fall nicht möglich. Der globale Administrator, der Azure AD Connect installiert, darf MFA nicht aktiviert haben. Wir arbeiten daran, diese Funktion in Zukunft zu unterstützen.

**F: Gibt es eine Möglichkeit, Azure AD Connect unbeaufsichtigt zu installieren?**

Azure AD Connect kann nur mit dem Installationsassistenten installiert werden. Eine unbeaufsichtigte und automatische Installation wird nicht unterstützt.

## Expressinstallation

## Benutzerdefinierte Installation

## Netzwerk
**F: Die maximale Länge für offene Verbindungen in meinem Netzwerk wird durch eine Firewall, ein Netzwerkgerät o. ä. eingeschränkt. Wie hoch sollte der clientseitige Schwellenwert für die Zeitüberschreitung bei der Verwendung von Azure AD Connect sein?**

Für Netzwerksoftware, physische Geräte und alle anderen Komponenten, durch die die maximale Länge von offenen Verbindungen eingeschränkt wird, sollte ein Schwellenwert von mindestens 5 Minuten (300 Sekunden) für die Konnektivität zwischen dem Server, auf dem der Azure AD Connect-Client installiert ist, und Azure Active Directory verwendet werden. Dies gilt auch für alle zuvor veröffentlichten Microsoft Identity-Synchronisierungswerkzeuge.

**F: Was muss ich tun, wenn ich eine E-Mail erhalte, die mich auffordert, mein Office 365-Zertifikat zu erneuern?**

Verwenden Sie die Anleitungen, die in [diesem](active-directory-aadconnect-o365-certs.md) Artikel beschrieben sind, um das Zertifikat zu erneuern.

## Problembehandlung

**F: Wie erhalte ich Hilfe zu Azure AD Connect?**

[Durchsuchen der Microsoft Knowledge Base (KB)](https://www.microsoft.com/de-DE/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Suchen Sie in der Microsoft Knowledge Base (KB) nach technischen Lösungen für häufige Probleme im Zusammenhang mit der Unterstützung von Azure AD Connect.

[Microsoft Azure Active Directory-Foren](https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=WindowsAzureAD)

- Sie können in der Community nach technischen Fragen und Antworten suchen oder eine eigene Frage stellen, indem Sie [hier](https://social.msdn.microsoft.com/Forums/azure/de-DE/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required) klicken.


[Azure AD Connect-Kundensupport](https://manage.windowsazure.com/?getsupport=true)

- Verwenden Sie diesen Link, um Support über das Azure-Portal zu erhalten.

<!---HONumber=Nov15_HO3-->