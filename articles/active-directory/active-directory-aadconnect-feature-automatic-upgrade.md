<properties
   pageTitle="Azure AD Connect: Automatisches Upgrade | Microsoft Azure"
   description="Dieses Thema beschreibt das integrierte automatische Upgradefeature in Azure AD Connect Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: Automatisches Upgrade
Diese Funktion wurde mit Build 1.1.105.0 eingeführt (veröffentlicht im Februar 2016).

## Übersicht
Noch nie konnten Sie so leicht sicherstellen, dass Ihre Azure AD Connect-Installation immer auf dem neuesten Stand ist – mit dem **automatischen Upgradefeature**. Dieses Feature ist für Expressinstallationen standardmäßig aktiviert und stellt sicher, dass Ihre Installation automatisch aktualisiert wird, sobald eine neue Version veröffentlicht wird.

Das automatische Upgrade ist in folgenden Fällen standardmäßig aktiviert:

- Installation mit Express-Einstellungen
- Verwendung von SQL Express LocalDB, wird immer von Expresseinstellungen verwendet
- Das AD-Konto ist das MSOL\_-Standardkonto, das von den Expresseinstellungen erstellt wird
- Weniger als 100.000 Objekte im Metaverse

Der aktuelle Zustand des automatischen Upgrades kann über das PowerShell-Cmdlet `Get-ADSyncAutoUpgrade` angezeigt werden. Folgende Zustände sind möglich:

| Zustand | Kommentar |
| ---- | ---- |
| Aktiviert | Das automatische Upgrade ist aktiviert. |
| Ausgesetzt | Nur vom System festgelegt. Das System ist nicht mehr zum Erhalten automatischer Upgrades berechtigt. |
| Deaktiviert | Das automatische Upgrade ist deaktiviert. |

Mit `Set-ADSyncAutoUpgrade` können Sie zwischen **aktiviert** und **deaktiviert** wechseln. Nur das System sollte den Zustand **Ausgesetzt** festlegen.

Das automatische Upgrade verwendet Azure AD Connect Health als Upgrade-Infrastruktur. Damit das automatische Upgrade ordnungsgemäß funktioniert, stellen Sie sicher, dass Sie die URLs im Proxy für **Azure AD Connect Health** gemäß der Beschreibung unter [Office 365-URLs und IP-Adressbereiche](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) geöffnet haben.

Wenn auf dem Server die **Synchronization Service Manager**-Benutzeroberfläche geöffnet ist, wird das Upgrade angehalten, bis die Benutzeroberfläche geschlossen wird.

## Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->