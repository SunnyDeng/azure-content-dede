<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Ermitteln der Anforderungen für die Multi-Factor Authentication"
	description="Mit der bedingten Zugriffssteuerung überprüft Azure Active Directory die besonderen Bedingungen, die Sie beim Authentifizieren des Benutzers und vor dem Gewähren des Zugriffs auf die Anwendung auswählen. Nachdem diese Bedingungen erfüllt sind, wird der Benutzer authentifiziert und erhält Zugriff auf die Anwendung."
	documentationCenter=""
	services="active-directory"
	authors="femila"
	manager="billmath"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="billmath"/>

# Ermitteln der Anforderungen an die Multi-Factor Authentication für Ihre Hybrid-Identitätslösung

In dieser Welt mit ihrem hohen Mobilitätsgrad, in der Benutzer mit allen Geräten auf Daten und Anwendungen in der Cloud zugreifen können, ist der Schutz dieser Daten zu einer sehr wichtigen Aufgabe geworden. Jeden Tag kann man neue Artikel über Sicherheitsverletzungen lesen. Es gibt zwar keinen absoluten Schutz vor diesen Sicherheitsverletzungen, aber die Multi-Factor Authentication bietet eine zusätzliche Sicherheitsebene als Schutz vor Verletzungen dieser Art. Beginnen Sie, indem Sie die Anforderungen des Unternehmens in Bezug auf die Multi-Factor Authentication auswerten. Hierbei geht es um die Daten, die vom Unternehmen geschützt werden sollen. Diese Auswertung ist wichtig, um die technischen Anforderungen zum Einrichten und Aktivieren der Unternehmensbenutzer für die Multi-Factor Authentication zu definieren.

>[AZURE.NOTE]Wenn Sie mit MFA und den damit verbundenen Funktionen nicht vertraut sind, empfehlen wir Ihnen dringend, den Artikel [Was ist Azure Multi-Factor Authentication?](multi-factor-authentication.md) zu lesen, bevor Sie mit diesem Abschnitt fortfahren.

Beantworten Sie die folgenden Fragen:

- Sollen in Ihrem Unternehmen Microsoft-Apps geschützt werden? 
- Wie werden diese Apps veröffentlicht?
- Wird von Ihrem Unternehmen der Remotezugriff bereitgestellt, damit die Mitarbeiter auf lokale Apps zugreifen können?

Wenn ja: Welche Art von Remotezugriff? Außerdem müssen Sie auswerten, wo sich die Benutzer befinden sollen, die auf diese Anwendungen zugreifen. Diese Auswertung ist ein weiterer wichtiger Schritt zur Definition der Multi-Factor Authentication-Strategie. Beantworten Sie die folgenden Fragen:

- Wo werden sich die Benutzer befinden?
- Können Sie sich an beliebigen Orten befinden?
- Möchte Ihr Unternehmen Einschränkungen in Bezug auf den Standort des Benutzers festlegen?

Nachdem Sie diese Anforderungen verstanden haben, ist es wichtig, auch die Anforderungen des Benutzers an die Multi-Factor Authentication auszuwerten. Diese Auswertung ist wichtig, weil hierbei die Anforderungen für die Einführung der Multi-Factor Authentication festgelegt werden. Beantworten Sie die folgenden Fragen:

- Sind die Benutzer mit der Multi-Factor Authentication vertraut?
- Ist für einige Verwendungen eine zusätzliche Authentifizierung erforderlich?  
 - Wenn ja: Gilt dies immer, für den Eingang aus externen Netzwerken, beim Zugreifen auf bestimmte Anwendungen oder unter anderen Bedingungen?
- Müssen die Benutzer darin geschult werden, wie die Multi-Factor Authentication eingerichtet und implementiert wird?
- Für welche wichtigen Szenarien möchte Ihr Unternehmen die Multi-Factor Authentication für die Benutzer ermöglichen?

Nach der Beantwortung der obigen Fragen wissen Sie, ob lokal bereits eine Multi-Factor Authentication implementiert wurde. Diese Auswertung ist wichtig, um die technischen Anforderungen zum Einrichten und Aktivieren der Unternehmensbenutzer für die Multi-Factor Authentication zu definieren. Beantworten Sie die folgenden Fragen:

- Müssen in Ihrem Unternehmen privilegierte Konten per MFA geschützt werden?
- Muss Ihr Unternehmen MFA für bestimmte Anwendungen aus Compliance-Gründen aktivieren?
- Muss Ihr Unternehmen MFA für alle betroffenen Benutzer dieser Anwendungen oder nur für Administratoren aktivieren?
- Muss MFA immer aktiviert sein, oder ist dies nur der Fall, wenn die Benutzer von außerhalb Ihres Unternehmensnetzwerks angemeldet sind?


## Nächste Schritte
[Definieren einer Strategie zur Einführung der Hybrid-Identität](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->