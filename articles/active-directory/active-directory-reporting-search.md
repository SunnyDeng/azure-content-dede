<properties
	pageTitle="Suche in Azure Active Directory-Berichten"
	description="Suchen Ihrer Azure Active Directory-Berichte zu Sicherheit, Aktivität und Überwachungsereignissen"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Suche in Azure Active Directory-Berichten

*Diese Dokumentation ist Teil des [Handbuchs für Azure Active Directory Reporting](active-directory-reporting-guide.md).*

Azure Active Directory (Azure AD) bietet Verzeichnisadministratoren die Möglichkeit, in mehreren Berichten nach Benutzersicherheit, Aktivität und Überwachungsereignissen zu suchen.

Den Suchbereich finden Sie, wenn Sie im **klassischen Azure-Portal -> Ihr Azure AD Active Directory -> Berichte** navigieren. Der Bereich befindet sich am Anfang der Liste der Berichte.

Um für einen bestimmten Benutzer nach Aktivitäts- oder Überwachungsereignissen zu suchen, wählen Sie einen Datumsbereich in den Feldern „von“ und „bis“, geben Sie UPN oder Anzeigenamen des Benutzers ein, und wählen Sie das Häkchen („OK“).

## In die Suche einbezogene Berichte

Es werden noch nicht alle Berichte in die Suchergebnisse einbezogen. Diese Tabelle gibt an, welche Berichte enthalten sind.

Bericht | Enthalten
--------------------------------------------------- | --------
Anmeldungen von unbekannten Quellen | Nein
Anmeldungen nach mehreren Fehlern | Nein
Anmeldungen aus mehreren geografischen Regionen | Nein
Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten | Nein
Anmeldungen von möglicherweise infizierten Geräten | Nein
Irreguläre Anmeldeaktivitäten | Nein
Benutzer mit anomalen Anmeldeaktivitäten | Nein
Benutzer mit kompromittierten Anmeldeinformationen | Nein
Überwachungsbericht | Ja
Kennwortzurücksetzungsaktivität | Ja
Aktivität "Registrierung für Zurücksetzen des Kennworts" | Ja
Self-Service-Gruppenaktivität | Ja
Anwendungsnutzung | Nein
Kontobereitstellungsaktivität | Ja
Status des Kennwortrollovers | Nein
Kontobereitstellungsfehler | Nein
RMS-Nutzung | Nein
Aktivste RMS-Benutzer | Nein
Nutzung von RMS-Geräten | Nein


## Weitere Informationen

 - [Azure Active Directory-Berichte](active-directory-view-access-usage-reports.md)
 - [Azure Active Directory-Überwachungsberichtsereignisse](active-directory-reporting-audit-events.md)

<!---HONumber=AcomDC_0309_2016-->