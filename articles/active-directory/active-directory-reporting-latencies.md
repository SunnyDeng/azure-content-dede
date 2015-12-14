<properties
   pageTitle="Wartezeiten von Azure Active Directory-Berichten | Microsoft Azure"
   description="Der erforderliche Zeitraum, bis Ereignisse in Ihrem Azure Active Directory-Bericht angezeigt werden."
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

# Wartezeiten von Azure Active Directory-Berichten

## Artikel aus der Reporting-Dokumentation

 - [Reporting-API](active-directory-reporting-api-getting-started.md)
 - [Überwachen von Ereignissen](active-directory-reporting-audit-events.md)
 - [Aufbewahrung](active-directory-reporting-retention.md)
 - [Vorschau](active-directory-reporting-previews.md)
 - [Suchen,](active-directory-reporting-search.md)
 - [Abgleich](active-directory-reporting-backfill.md)
 - [Wartezeiten](active-directory-reporting-latencies.md)
 - [Ereignis "Unbekannter Akteur"](active-directory-reporting-unknown-actor.md)

Bericht | Minimum | Durchschnitt | Maximum
------------------------------------------------------- | -------- | ---------- | ----------
**Sicherheitsberichte** | | |
Irreguläre Anmeldeaktivitäten | 2 Stunden | 4 Stunden | 8 Stunden
Anmeldungen aus unbekannten Quellen | 2 Stunden | 4 Stunden | 8 Stunden
Anmeldungen nach mehreren Fehlern | 2 Stunden | 4 Stunden | 8 Stunden
Anmeldungen aus mehreren geografischen Regionen | 2 Stunden | 4 Stunden | 8 Stunden
Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten | 2 Stunden | 4 Stunden | 8 Stunden
Anmeldungen von möglicherweise infizierten Geräten | 2 Stunden | 4 Stunden | 8 Stunden
Benutzer mit anomalen Anmeldeaktivitäten | 2 Stunden | 4 Stunden | 8 Stunden
Benutzer mit kompromittierten Anmeldeinformationen | 2 Stunden | 4 Stunden | 8 Stunden
**Anwendungsberichte** | | |
Kontobereitstellungsaktivität | 2 Stunden | 4 Stunden | 8 Stunden
Kontobereitstellungsfehler | 2 Stunden | 4 Stunden | 8 Stunden
Anwendungsnutzung | 2 Stunden | 4 Stunden | 8 Stunden
Status des Kennwortrollovers | 2 Stunden | 4 Stunden | 8 Stunden
**Überwachungs- und Aktivitätsberichte** | | |
Überwachungsbericht | 1 Minute | 15 Minuten | 30 Minuten
Aktivität "Zurücksetzen des Kennworts" (Azure AD) | 2 Stunden | 4 Stunden | 8 Stunden
Aktivität "Zurücksetzen des Kennworts" (Identity Manager) | 2 Stunden | 4 Stunden | 8 Stunden
Aktivität "Registrierung für Zurücksetzen des Kennworts" (Azure AD) | 2 Stunden | 4 Stunden | 8 Stunden
Aktivität "Registrierung für Zurücksetzen des Kennworts" (Identity Manager) | 2 Stunden | 4 Stunden | 8 Stunden
Self-Service-Gruppenaktivität (Azure AD) | 2 Stunden | 4 Stunden | 8 Stunden
Self-Service-Gruppenaktivität (Identity Manager) | 2 Stunden | 4 Stunden | 8 Stunden
**RMS-Berichte** | | |
Aktivste RMS-Benutzer | 2 Stunden | 4 Stunden | 8 Stunden
RMS-Nutzung | 2 Stunden | 4 Stunden | 8 Stunden
Nutzung von RMS-Geräten | 2 Stunden | 4 Stunden | 8 Stunden
Nutzung RMS-fähiger Anwendungen | 2 Stunden | 4 Stunden | 8 Stunden
**Private Berichtsvorschau** | | |
Alle Benutzeranmeldeaktivitäten | 2 Stunden | 4 Stunden | 8 Stunden

<!---HONumber=AcomDC_1203_2015-->