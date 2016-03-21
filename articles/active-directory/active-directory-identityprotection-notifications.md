<properties
	pageTitle="Azure Active Directory Identity Protection-Benachrichtigungen | Microsoft Azure"
	description="Erfahren Sie, wie Benachrichtigungen Ihre Untersuchungsaktivitäten unterstützen."
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
	ms.date="03/02/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection Benachrichtigungen 


Von Azure AD Identity Protection werden zwei Arten von automatisierten Benachrichtigungs-E-Mails gesendet, die der Verwaltung von Benutzerrisiko- und Risikoereignissen dienen:

- E-Mail als Warnung vor kompromittiertem Benutzer

- Wöchentliche E-Mail mit Übersicht

## E-Mail als Warnung vor kompromittiertem Benutzer

Eine E-Mail als Warnung vor einem kompromittiertem Benutzer wird generiert, wenn ein Konto von Azure AD Identity Protection als kompromittiert erkannt wird. Die E-Mail enthält einen Link zum Bericht vom Typ „Benutzer mit Risikokennzeichnung“ im Identity Protection Dashboard. Es wird empfohlen, dass Sie sofort Benachrichtigungen zu kompromittierten Benutzern untersuchen.


## Wöchentliche E-Mail mit Übersicht

Die wöchentliche E-Mail mit einer Übersicht enthält eine Zusammenfassung der neuen Risikoereignisse.<br> Sie hat folgenden Inhalt:

- Gefährdete Benutzer
- Verdächtige Aktivitäten
- Erkannte Sicherheitsrisiken
- Links zu verwandten Berichten in Identity Protection


<br> ![Korrektur](./media/active-directory-identityprotection-notifications/400.png "Korrektur") <br>

Sie können das Senden einer wöchentlichen Übersichts-E-Mail deaktivieren. <br><br> ![Benutzerrisiken](./media/active-directory-identityprotection-notifications/62.png "Benutzerrisiken") <br>
 

**Um den entsprechenden Konfigurationsdialog zu öffnen**:

1. Klicken Sie im Hauptblatt von **Azure AD Identity Protection** auf **Einstellungen**. <br><br> ![Richtlinie zum Benutzerrisiko](./media/active-directory-identityprotection-notifications/401.png "Richtlinie zum Benutzerrisiko") <br>.

2. Klicken Sie im Abschnitt **Allgemein** auf **Benachrichtigungen**. <br><br> ![Richtlinie zum Benutzerrisiko](./media/active-directory-identityprotection-notifications/405.png "Richtlinie zum Benutzerrisiko") <br>




## Weitere Informationen

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

<!---HONumber=AcomDC_0309_2016-->