<properties
	pageTitle="Benachrichtigungen zu Azure Active Directory-Berichten"
	description="Verwenden von Benachrichtigungen zu Azure Active Directory-Berichten zu verdächtigen Anmeldungen."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Benachrichtigungen zu Azure Active Directory-Berichten

## Welche Berichte generieren E-Mail-Benachrichtigungen?

Derzeit wird das E-Mail-Benachrichtigungssystem nur für die Berichte "Anomale Anmeldeaktivitäten" und "Benutzer mit anomalen Anmeldeaktivitäten" genutzt.

## Was löst das Senden von E-Mail-Benachrichtigung aus?

Standardmäßig ist Azure Active Directory auf das automatische Senden von E-Mail-Benachrichtigungen an alle globalen Administratoren festgelegt. Die E-Mail wird unter folgenden Bedingungen für jeden Bericht gesendet.

Für den Bericht "Anomale Anmeldeaktivitäten":

- Unbekannte Quellen: 10 Ereignisse
- Mehrere Fehler: 10 Ereignisse
- IP-Adressen mit verdächtigen Aktivitäten: 10 Ereignisse
- Infizierte Geräte: 10 Ereignisse

Für den Bericht "Benutzer mit anomalen Anmeldeaktivitäten":

- Unbekannte Quellen: 10 Ereignisse
- Mehrere Fehler: 10 Ereignisse
- IP-Adressen mit verdächtigen Aktivitäten: 10 Ereignisse
- Infizierte Geräte: 5 Ereignisse
- Bericht "Anormale Anmeldungen": 15 Ereignisse

Die E-Mail wird gesendet, wenn eine der oben genannten Bedingungen binnen 30 Tagen erfüllt wurde oder wenn das Senden der letzten E-Mail weniger als 30 Tage zurückliegt.

Anormale Anmeldungen sind diejenigen, die von unseren Algorithmen für maschinelles Lernen auf der Basis unerwarteter Anmeldeorte, der Tageszeit und des Standorts oder einer Kombination dieser Faktoren als "anomal" identifiziert wurden. Dies kann ein Hinweis sein, dass ein Hacker versucht hat, sich mit diesem Konto anzumelden. Weitere Informationen zu diesem Bericht finden Sie in der obigen Tabelle.

## Wer empfängt die E-Mail-Benachrichtigungen?

Die E-Mail wird an alle globalen Administratoren mit einer Active Directory Premium-Lizenz gesendet. Um die Übermittlung sicherzustellen, wird sie auch an die alternative E-Mail-Adresse der Administratoren gesendet. Administratoren müssen aad-alerts-noreply@mail.windowsazure.com ihrer Liste sicherer Absender hinzufügen, damit sie die e-Mail nicht verpassen.

## Wie oft werden diese E-Mails gesendet?

Nachdem eine E-Mail gesendet wurde, wird die nächste erst dann gesendet, wenn mindestens 10 weitere neue anomale Anmeldeereignisse binnen 30 Tagen nach Senden dieser E-Mail erfolgt sind. Wie greife ich auf den in der E-Mail genannten Bericht zu?

Wenn Sie auf den Link klicken, werden Sie zur Berichtsseite im Azure-Verwaltungsportal weitergeleitet. Für den Zugriff auf den Bericht müssen Sie die beiden folgenden Rollen bekleiden:

- Administrator oder Co-Administrator Ihres Azure-Abonnements
- Globaler Administrator im Verzeichnis mit Active Directory Premium-Lizenz Weitere Informationen finden Sie unter "Azure Active Directory-Editionen".

## Kann ich diese E-Mails deaktivieren?

Ja, zum Deaktivieren von Benachrichtigungen in Zusammenhang mit anomalen Anmeldungen klicken Sie im Azure-Verwaltungsportal auf **Konfigurieren** und wählen **Deaktiviert** im Abschnitt **Benachrichtigungen** aus.

## Nächste Schritte
- Möchten Sie wissen, welche Sicherheits-, Überwachungs- und Aktivitätsberichte zur Verfügung stehen? Lesen Sie [Sicherheits-, Überwachungs- und Aktivitätsberichte](active-directory-view-access-usage-reports.md).
- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)

<!---HONumber=August15_HO8-->