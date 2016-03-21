<properties
	pageTitle="Von Azure Active Directory Identity Protection erkannte Sicherheitslücken | Microsoft Azure"
	description="Übersicht über die von Azure Active Directory Identity Protection erkannten Sicherheitslücken"
	services="active-directory"
	keywords="Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="markvi"/>

# Von Azure Active Directory Identity Protection erkannte Sicherheitsrisiken 

Bei Sicherheitsrisiken handelt es sich um Schwachstellen in Ihrer Umgebung, die von einem Angreifer ausgenutzt werden können. Es wird empfohlen, dass Sie diese Sicherheitsrisiken entschärfen, um den Sicherheitsstatus Ihrer Organisation zu verbessern und Angreifer daran zu hindern, diese Schwachstellen auszunutzen. <br><br> ![Sicherheitsrisiken](./media/active-directory-identityprotection-vulnerabilities/101.png "Sicherheitsrisiken") <br>

Die folgenden Abschnitte bieten Ihnen eine Übersicht der Sicherheitsrisiken, die von Identity Protection gemeldet werden.

## Multi-Factor Authentication-Registrierung nicht konfiguriert 

Mit diesem Sicherheitsrisiko können Sie die Bereitstellung der Azure Multi-Factor Authentication in Ihrer Organisation besser steuern.

Azure Multi-Factor Authentication dient als zweite Sicherheitsebene für die Benutzerauthentifizierung. Sie hilft beim Schutz des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Sie bietet leistungsfähige Authentifizierung mittels einiger einfacher Überprüfungsoptionen – Telefonanruf, SMS oder per Benachrichtigung bzw. Überprüfungscode in einer mobilen Anwendung sowie OATH-Tokens von Drittanbietern.

Es wird empfohlen, dass Sie die Azure Multi-Factor Authentication für Anmeldungen von Benutzern obligatorisch machen. Die mehrstufige Authentifizierung spielt eine wichtige Rolle in Bezug auf risikobasierte Richtlinien für bedingten Zugriff, die unter Identity Protection verfügbar sind.

Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md).


## Nicht verwaltete Cloud-Apps

Mit diesem Sicherheitsrisiko können Sie in Ihrer Organisation nicht verwaltete Cloud-Apps identifizieren.
 
In modernen Unternehmen sind den IT-Abteilungen häufig nicht alle Cloudanwendungen bekannt, die die Benutzer der Organisation für ihre Arbeit verwenden. Es ist verständlich, warum Administratoren sich Sorgen um den unberechtigten Zugriff auf Unternehmensdaten, mögliche Datenlecks und andere Sicherheitsrisiken machen.

Es wird empfohlen, dass Sie in Ihrer Organisation die Cloud-App-Ermittlung einsetzen, um nicht verwaltete Cloudanwendungen zu ermitteln und diese Anwendungen mit Azure Active Directory zu verwalten.

Ausführliche Informationen finden Sie unter [Suchen nach nicht verwalteten Cloudanwendungen per Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).



##Sicherheitswarnungen von Privileged Identity Management

Dieses Sicherheitsrisiko hilft Ihnen beim Ermitteln und Bereinigen von Warnungen zu privilegierten Identitäten in Ihrer Organisation.

Damit Benutzer privilegierte Vorgänge ausführen können, müssen Organisationen ihren Benutzern einen vorübergehenden oder dauerhaften privilegierten Zugriff auf Azure AD, Azure- oder Office 365-Ressourcen oder andere SaaS-Apps gewähren. Mit jedem dieser privilegierten Benutzer vergrößert sich die Angriffsfläche Ihrer Organisation. Dieses Sicherheitsrisiko dient Ihnen als Unterstützung beim Identifizieren von Benutzern mit nicht erforderlichem privilegiertem Zugriff und beim Treffen geeigneter Maßnahmen, um das damit verbundene Risiko zu mindern oder zu beseitigen.

Wir empfehlen Ihrer Organisation die Verwendung von Azure AD Privileged Identity Management, um privilegierte Identitäten und deren Zugriff auf Ressourcen in Azure AD und anderen Microsoft-Onlinediensten wie Office 365 oder Microsoft Intune verwalten, steuern und überwachen zu können.

Ausführliche Informationen finden Sie unter [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md).



## Weitere Informationen

 - [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0309_2016-->