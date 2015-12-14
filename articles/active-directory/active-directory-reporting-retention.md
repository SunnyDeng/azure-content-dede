<properties
	pageTitle="Aufbewahrungsrichtlinien für Azure Active Directory-Berichte | Microsoft Azure"
	description="Aufbewahrungsrichtlinien für Berichtdaten in Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="11/30/2015"
	ms.author="kenhoff"/>

# Aufbewahrungsrichtlinien für Azure Active Directory-Berichte

## Artikel in der Azure AD Reporting-Dokumentation

 - [Reporting-API](active-directory-reporting-api-getting-started.md)
 - [Überwachungsereignisse](active-directory-reporting-audit-events.md)
 - [Aufbewahrung](active-directory-reporting-retention.md)
 - [Vorschau](active-directory-reporting-previews.md)
 - [Suche](active-directory-reporting-search.md)
 - [Abgleich](active-directory-reporting-backfill.md)
 - [Wartezeiten](active-directory-reporting-latencies.md)
 - [Ereignis „Unbekannter Akteur“](active-directory-reporting-unknown-actor.md)

Azure AD-Berichte werden für die hier angegebene Anzahl von Tagen aufbewahrt.

Bericht | Beschreibung
------------------------------------------------------- | -----------
Anmeldungen aus unbekannten Quellen | 30 Tage
Anmeldungen nach mehreren Fehlern | 30 Tage
Anmeldungen aus mehreren geografischen Regionen | 30 Tage
Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten | 30 Tage
Anmeldungen von möglicherweise infizierten Geräten | 30 Tage
Irreguläre Anmeldeaktivitäten | 30 Tage
Benutzer mit anomalen Anmeldeaktivitäten | 30 Tage
Benutzer mit kompromittierten Anmeldeinformationen | 30 Tage
Überwachungsbericht | 180 Tage
Aktivität "Zurücksetzen des Kennworts" (Azure AD) | 30 Tage
Aktivität "Zurücksetzen des Kennworts" (Identity Manager) | 30 Tage
Aktivität "Registrierung für Zurücksetzen des Kennworts" (Azure AD) | 30 Tage
Aktivität "Registrierung für Zurücksetzen des Kennworts" (Identity Manager) | 30 Tage
Self-Service-Gruppenaktivität (Azure AD) | 30 Tage
Self-Service-Gruppenaktivität (Identity Manager) | 30 Tage
Anwendungsnutzung | 30 Tage
Kontobereitstellungsaktivität | 30 Tage
Status des Kennwortrollovers | 30 Tage
Kontobereitstellungsfehler | 30 Tage
RMS-Nutzung | 30 Tage
Aktivste RMS-Benutzer | 30 Tage
Nutzung von RMS-Geräten | 30 Tage
Nutzung RMS-fähiger Anwendungen | 30 Tage

<!---HONumber=AcomDC_1203_2015-->