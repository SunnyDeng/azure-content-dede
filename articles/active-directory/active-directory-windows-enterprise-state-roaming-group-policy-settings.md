<properties
	pageTitle="Gruppenrichtlinien- und MDM-Einstellungen | Microsoft Azure"
	description="Enthält Informationen zu Gruppenrichtlinien- und MDM-Einstellungen (Mobile Device Management, Verwaltung mobiler Geräte), die auf unternehmenseigenen Geräten verwendet werden sollen. Diese Richtlinien werden auf das gesamte Gerät eines Benutzers angewendet."
	services="active-directory"
    keywords="Was sind Gruppenrichtlinien- und MDM-Einstellungen für Enterprise State Roaming, Enterprise State Roaming, Windows-Cloud"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Gruppenrichtlinien- und MDM-Einstellungen

Verwenden Sie diese Gruppenrichtlinien- und MDM-Einstellungen (Mobile Device Management, Verwaltung mobiler Geräte) nur für unternehmenseigene Geräte, da diese Richtlinien auf das gesamte Gerät eines Benutzers angewendet werden. Das Anwenden einer MDM-Richtlinie zum Deaktivieren der Einstellungssynchronisierung für ein persönliches, benutzereigenes Gerät hat eine negative Auswirkung auf die Nutzung dieses Geräts. Außerdem werden weitere Benutzerkonten auf dem Gerät von der Richtlinie ebenfalls beeinflusst.

Unternehmen, die das Roaming für persönliche (nicht verwaltete) Geräte verwalten möchten, können das Azure-Portal zum Aktivieren oder Deaktivieren des Roamings verwenden, anstatt die Gruppenrichtlinie oder MDM. In den folgenden Tabellen sind die verfügbaren Richtlinieneinstellungen beschrieben.

## MDM-Einstellungen
Die MDM-Richtlinieneinstellungen gelten sowohl für Windows 10 als auch für Windows 10 Mobile.

| Name | Beschreibung |
|------------------------------------|----------------------------------------------------------------------|
| Microsoft-Konto-Verbindung zulassen | Ermöglicht Benutzern die Authentifizierung mit einem Microsoft-Konto auf dem Gerät. |
| Einstellungen synchronisieren zulassen | Ermöglicht Benutzern das Roaming von Windows-Einstellungen und App-Daten. |
 
## Gruppenrichtlinien-Einstellungen
Die Gruppenrichtlinien-Einstellungen gelten für Windows 10-Geräte, die einer Active Directory-Domäne beigetreten sind. Die Tabelle enthält Legacyeinstellungen, die scheinbar zum Verwalten von Synchronisierungseinstellungen geeignet sind, aber für Enterprise State Roaming für Windows 10 nicht funktionieren.

| Name | Beschreibung |
|-------------------------------------|-------------|
| Konten: Microsoft-Konten blockieren |Diese Richtlinieneinstellung verhindert, dass Benutzer auf diesem Computer neue Microsoft-Konten hinzufügen.|
| Nicht synchronisieren |Ermöglicht Benutzern das Roaming von Windows-Einstellungen und App-Daten.|
| Personalisierung nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Designs“.|
| Browsereinstellungen nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Internet Explorer“.|
| Kennwörter nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Kennwörter“.|
| Weitere Windows-Einstellungen nicht synchronisieren |Deaktiviert die Synchronisierung der Gruppe „Weitere Windows-Einstellungen“.|
| Desktoppersonalisierung nicht synchronisieren |Nicht verwenden, hat keine Auswirkung.|
| Keine Synchronisierung über getaktete Verbindungen |Deaktiviert das Roaming über getaktete Verbindungen, z. B. eine 3G-Mobilfunkverbindung.|
| Apps nicht synchronisieren |Nicht verwenden, hat keine Auswirkung.|
|App-Einstellungen nicht synchronisieren |Deaktiviert das Roaming von App-Daten.|
|Starteinstellungen nicht synchronisieren |Nicht verwenden, hat keine Auswirkung.|


## Verwandte Themen
- [Übersicht über Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Aktivieren von Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Roaming von Einstellungen und Daten – Häufig gestellte Fragen](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Windows 10-Roamingeinstellungen – Referenz](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->