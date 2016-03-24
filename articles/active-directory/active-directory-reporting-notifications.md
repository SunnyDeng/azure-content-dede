<properties
	pageTitle="Benachrichtigungen zu Azure Active Directory-Berichten"
	description="Verwenden von Benachrichtigungen zu Azure Active Directory-Berichten zu verdächtigen Anmeldungen."
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Benachrichtigungen zu Azure Active Directory-Berichten

## Welche Berichte generieren E-Mail-Benachrichtigungen?

Zu diesem Zeitpunkt werden E-Mail-Benachrichtigungen nur durch den Bericht zu irregulären Anmeldeaktivitäten ausgelöst.

## Was ist eine "irreguläre Anmeldung"?

Irreguläre Anmeldungen sind Anmeldungen, die von Machine Learning-Algorithmen auf der Grundlage einer Bedingung für eine „unmögliche Strecke“ in Kombination mit einem anomalen Standort und Gerät für die Anmeldung identifiziert wurden. Dies kann ein Hinweis sein, dass ein Hacker versucht hat, sich mit diesem Konto anzumelden.

## Wer empfängt die E-Mail-Benachrichtigungen?

Die E-Mail wird an alle globalen Administratoren mit einer Active Directory Premium-Lizenz gesendet. Um die Übermittlung sicherzustellen, wird sie auch an die alternative E-Mail-Adresse der Administratoren gesendet. Administratoren müssen aad-alerts-noreply@mail.windowsazure.com ihrer Liste sicherer Absender hinzufügen, damit sie die e-Mail nicht verpassen.

## Wie oft werden diese E-Mails gesendet?

Die E-Mail wird gesendet, wenn zehn neue irreguläre Anmeldeaktivitäten in den letzten 30 Tagen oder seit der letzten E-Mail aufgetreten sind, je nachdem, welcher Zeitraum kürzer ist.

## Wie greife ich auf den in der E-Mail genannten Bericht zu?

Wenn Sie auf den Link klicken, werden Sie zur Berichtsseite im klassischen Azure-Portal weitergeleitet. Für den Zugriff auf den Bericht müssen Sie die beiden folgenden Rollen bekleiden:

- Administrator oder Co-Administrator Ihres Azure-Abonnements

- Globaler Administrator im Verzeichnis mit Active Directory Premium-Lizenz Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

## Kann ich diese E-Mails deaktivieren?

Ja, zum Deaktivieren von Benachrichtigungen in Zusammenhang mit anomalen Anmeldungen klicken Sie im klassischem Azure-Portal auf **Konfigurieren** und wählen **Deaktiviert** im Abschnitt **Benachrichtigungen** aus.

## Nächste Schritte
- Möchten Sie wissen, welche Sicherheits-, Überwachungs- und Aktivitätsberichte zur Verfügung stehen? Lesen Sie [Sicherheits-, Überwachungs- und Aktivitätsberichte](active-directory-view-access-usage-reports.md).
- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)

<!---HONumber=AcomDC_0309_2016-->