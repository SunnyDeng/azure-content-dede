<properties
   pageTitle="Abgleichsdauer in Azure Active Directory-Berichten | Microsoft Azure"
   description="Der erforderliche Zeitraum, bis frühere Ereignisse in Ihrem Azure Active Directory-Bericht angezeigt werden."
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

Nachdem ein Verzeichnis für Berichte aktiviert wurde, werden die Daten für die nachfolgend angegebene Anzahl von Tagen abgeglichen.

Bericht | Beschreibung
------------------------------------------------------- | -----------
Anmeldungen aus unbekannten Quellen | 0 Tage
Anmeldungen nach mehreren Fehlern | 0 Tage
Anmeldungen aus mehreren geografischen Regionen | 0 Tage
Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten | 0 Tage
Anmeldungen von möglicherweise infizierten Geräten | 0 Tage
Irreguläre Anmeldeaktivitäten | 0 Tage
Benutzer mit anomalen Anmeldeaktivitäten | 0 Tage
Benutzer mit kompromittierten Anmeldeinformationen | 0 Tage
Überwachungsbericht | 30 Tage
Aktivität "Zurücksetzen des Kennworts" (Azure AD) | 0 Tage
Aktivität "Zurücksetzen des Kennworts" (Identity Manager) | 0 Tage
Aktivität "Registrierung für Zurücksetzen des Kennworts" (Azure AD) | 0 Tage
Aktivität "Registrierung für Zurücksetzen des Kennworts" (Identity Manager) | 0 Tage
Self-Service-Gruppenaktivität (Azure AD) | 0 Tage
Self-Service-Gruppenaktivität (Identity Manager) | 0 Tage
Anwendungsnutzung | 0 Tage
Kontobereitstellungsaktivität | 0 Tage
Status des Kennwortrollovers | 0 Tage
Kontobereitstellungsfehler | 0 Tage
RMS-Nutzung | 0 Tage
Aktivste RMS-Benutzer | 0 Tage
Nutzung von RMS-Geräten | 0 Tage
Nutzung RMS-fähiger Anwendungen | 0 Tage

<!---HONumber=AcomDC_1203_2015-->