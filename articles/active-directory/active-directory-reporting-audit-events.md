<properties
   pageTitle="Azure Active Directory-Überwachungsberichtsereignisse | Microsoft Azure"
   description="Überwachte Ereignisse, die zum Anzeigen und Herunterladen aus Azure Active Directory verfügbar sind"
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
   ms.date="06/18/2015"
   ms.author="kenhoff"/>

# Azure Active Directory-Überwachungsberichtsereignisse
Der Azure Active Directory-Überwachungsbericht hilft Kunden, privilegierte Aktionen zu bestimmen, die in ihrem Azure Active Directory aufgetreten sind. Privilegierte Aktionen umfassen Änderungen zur Rechteerweiterung (z. B. das Erstellen von Rollen oder Zurücksetzen von Kennwörtern), das Ändern von Richtlinienkonfigurationen (z. B. Kennwortrichtlinien) oder Änderungen an der Verzeichniskonfiguration (z. B. Änderungen an Domänenverbundeinstellungen). Die Berichte enthalten den Überwachungsdatensatz für den Ereignisnamen, den Akteur, der die Aktion ausgeführt hat, die von der Änderung betroffene Zielressource sowie Datum und Uhrzeit (in UTC). Kunden können die Liste der Überwachungsereignisse für ihr Azure Active Directory über das [Azure-Verwaltungsportal](https://manage.windowsazure.com/) abrufen, wie beschrieben unter [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md).

## Aufbewahrung von Überwachungsberichten
Ereignisse im Azure AD-Überwachungsbericht werden für 180 Tage beibehalten. Weitere Informationen zur Aufbewahrung von Berichten finden Sie unter [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](active-directory-reporting-retention.md).

Kunden, die ihre Überwachungsereignisse für längere Zeit speichern möchten, können mithilfe der Reporting-API regelmäßig Überwachungsereignisse in einen separaten Datenspeicher abrufen. Ausführliche Informationen finden Sie unter [Erste Schritte mit der Azure AD Reporting-API](active-directory-reporting-api-getting-started.md).

## In jedem Überwachungsereignis enthaltene Eigenschaften

Eigenschaft | Beschreibung
------------- | --------------------------------------------------------------
Datum und Uhrzeit | Datum und Uhrzeit des Auftretens des Überwachungsereignisses
Akteur | Der Benutzer oder Dienstprinzipal, der die Aktion ausgeführt hat
Aktion | Die Aktion, die ausgeführt wurde
Ziel | Der Benutzer oder Dienstprinzipal, auf den die Aktion angewendet wurde

## Liste der Überwachungsberichtsereignisse
<!--- audit event descriptions should be in the past tense --->

Ereignisse | Beschreibung des Ereignisses
------------------------------------ | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Benutzerereignisse** |
Benutzer hinzufügen | Einen Benutzer zum Verzeichnis hinzugefügt.
Benutzer löschen | Einen Benutzer aus dem Verzeichnis gelöscht.
Lizenzeigenschaften festlegen | Lizenzeigenschaften für einen Benutzer im Verzeichnis festgelegt.
Benutzerkennwort zurücksetzen | Kennwort eines Benutzers im Verzeichnis zurückgesetzt.
Benutzerkennwort ändern | Kennwort eines Benutzers im Verzeichnis geändert.
Benutzerlizenz ändern | Die einem Benutzer im Verzeichnis zugewiesene Lizenz geändert.
Benutzer aktualisieren | Einen Benutzer im Verzeichnis aktualisiert.
Änderung des Benutzerkennworts erzwingen | Eigenschaft festgelegt, die einen Benutzer zwingt, sein Kennwort bei der Anmeldung zu ändern.
**Gruppenereignisse** |
Gruppe erstellen | Eine Gruppe wurde im Verzeichnis erstellt.
Gruppe aktualisieren | Eine Gruppe wurde im Verzeichnis aktualisiert.
Gruppe löschen | Eine Gruppe wurde im Verzeichnis gelöscht.
Mitglied zur Gruppe hinzufügen | Einer Gruppe im Verzeichnis wurde ein Mitglied hinzugefügt.
Mitglied aus Gruppe entfernen | Ein Mitglied wurde aus einer Gruppe im Verzeichnis entfernt.
**Anwendungsereignisse** |
Dienstprinzipal hinzufügen | Dienstprinzipal zum Verzeichnis hinzugefügt.
Dienstprinzipal entfernen | Dienstprinzipal aus dem Verzeichnis entfernt.
Anmeldeinformationen für Dienstprinzipal hinzufügen | Anmeldeinformationen für einen Dienstprinzipal hinzugefügt.
Anmeldeinformationen für Dienstprinzipal entfernen | Anmeldeinformationen für einen Dienstprinzipal entfernt.
Delegierungseintrag hinzufügen | Delegierungseintrag zum Verzeichnis hinzugefügt.
Delegierungseintrag festlegen | Delegierungseintrag im Verzeichnis aktualisiert.
Delegierungseintrag entfernen | Delegierungseintrag aus dem Verzeichnis entfernt.
**Rollenereignisse** |
Rollenmitglied zur Rolle hinzufügen | Benutzer einer Verzeichnisrolle hinzugefügt.
Rollenmitglied aus Rolle entfernen | Benutzer aus einer Verzeichnisrolle entfernt.
Kontaktinformationen für das Unternehmen festlegen | Kontakteinstellungen auf Unternehmensebene festgelegt. Dazu gehören E-Mail-Adressen für das Marketing sowie technische Benachrichtigungen zu Microsoft Online Services.
**Verzeichnisereignisse** |
Partner zu Unternehmen hinzufügen | Partner zum Verzeichnis hinzugefügt.
Partner aus Unternehmen entfernen | Partner aus dem Verzeichnis entfernt.
Domäne zu Unternehmen hinzufügen | Domäne zum Verzeichnis hinzugefügt.
Domäne aus Unternehmen entfernen | Domäne aus dem Verzeichnis entfernt.
Domäne aktualisieren | Domäne im Verzeichnis aktualisiert.
Domänenauthentifizierung festlegen | Standarddomäneneinstellung für das Unternehmen geändert.
Verbundeinstellungen für Domäne festlegen | Verbundeinstellungen für eine Domäne aktualisiert.
Domäne überprüfen | Domäne im Verzeichnis überprüft.
Per E-Mail verifizierte Domäne überprüfen | Domäne im Verzeichnis mithilfe der E-Mail-Verifizierung überprüft.
Kennzeichen "DirSyncEnabled" für Unternehmen festlegen | Eigenschaft festgelegt, die ein Verzeichnis für die Azure AD-Synchronisierung aktiviert.
Kennwortrichtlinie festlegen | Längen- und Zeicheneinschränkungen für Benutzerkennwörter festgelegt.
Unternehmensinformationen festlegen | Informationen auf Unternehmensebene aktualisiert. Im Artikel zum PowerShell-Cmdlet [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) finden Sie weitere Details.

<!---

List of events that still need descriptions:

Restore Application
Set String Auth Policy
Promote tenant to partner

--->

### Im Überwachungsereignis "Benutzer aktualisieren" enthaltene Benutzerattribute
Das Überwachungsereignis "Benutzer aktualisieren" enthält Informationen dazu, welche Benutzerattribute aktualisiert wurden. Für jedes Attribut sind der vorherige Wert und der neue Wert enthalten.

Attribut | Beschreibung
------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Der Benutzer darf sich anmelden.
AssignedLicense | Alle Lizenzen, die dem Benutzer zugewiesen wurden.
AssignedPlan | Servicepläne, die sich aus dem Benutzer zugewiesenen Lizenzen ergeben.
LicenseAssignmentDetail | Details zu den dem Benutzer zugewiesenen Lizenzen. Bei einer gruppenbasierten Lizenzierung schließt dies die Gruppe ein, die die Lizenz erteilt hat.
Mobile | Die Mobiltelefonnummer des Benutzers.
OtherMail | Die alternative E-Mail-Adresse des Benutzers.
OtherMobile | Die alternative Mobiltelefonnummer des Benutzers.
StrongAuthenticationMethod | Eine Liste von Überprüfungsmethoden, die vom Benutzer für die Multi-Factor Authentication konfiguriert wurden, z. B. Sprachanruf, SMS oder Verifizierungscode aus einer mobilen App.
StrongAuthenticationRequirement | Wenn die Multi-Factor Authentication erzwungen wird, wird diese Einstellung für den Benutzer aktiviert oder deaktiviert.
StrongAuthenticationUserDetails | Die Telefonnummer des Benutzers, alternative Telefonnummer und E-Mail-Adresse, die für die Multi-Factor Authentication und Überprüfung der Kennwortzurücksetzung verwendet werden.
TelephoneNumber | Die Telefonnummer des Benutzers.

Überwachungsdatensätze sind ein erforderliches Steuerelement in vielen Richtlinien zur Einhaltung von Vorschriften. Kunden, die den Azure Active Directory-Überwachungsbericht zum Erfüllen dieser Vorschriften nutzen, wird empfohlen, der Kopie des exportierten Überwachungsberichts eine Kopie dieses Hilfethemas hinzuzufügen, das die Berichtsdetails erläutert. Wenn der Prüfer die Richtlinien zur Vorschrifteneinhaltung bestimmen möchte, die Azure derzeit erfüllt, leiten Sie ihn zur [Seite "Compliance"](http://azure.microsoft.com/support/trust-center/compliance/) im Microsoft Azure Trust Center.

<!---HONumber=Sept15_HO2-->