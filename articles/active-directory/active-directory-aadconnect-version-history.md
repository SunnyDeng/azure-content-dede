<properties
   pageTitle="Azure AD Connect: Versionsveröffentlichungsverlauf | Microsoft Azure"
	description="In diesem Thema werden alle Versionen von Azure AD Connect und Azure AD Sync aufgeführt."
	services="active-directory"
	documentationCenter=""
	authors="AndKjell"
	manager="stevenpo"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/24/2015"
	ms.author="andkjell"/>

# Azure AD Connect: Versionsveröffentlichungsverlauf

Das Azure Active Directory-Team aktualisiert Azure AD Connect regelmäßig mit neuen Features und Funktionen. Nicht alle Erweiterungen gelten für alle Benutzergruppen.

Dieser Artikel soll Ihnen helfen, die Versionen zu verfolgen, die veröffentlicht wurden, und zu wissen, ob Sie auf die neueste Version aktualisieren müssen oder nicht.

## 1\.0.8667.0
Veröffentlicht im August 2015

**Neue Features:**

- Der Azure AD Connect-Installations-Assistent ist jetzt für alle Windows Server-Sprachen lokalisiert.
- Erweiterte Unterstützung für die Kontoentsperrung bei Verwendung der Azure AD-Kennwortverwaltung.

**Behobene Probleme:**

- Azure AD-Connect-Installations-Assistent stürzt ab, wenn ein anderer Benutzer als die Person, die die Installation gestartet hat, die Installation fortsetzt.
- Wenn eine frühere Deinstallation von Azure AD Connect die Azure AD Connect-Synchronisierung nicht sauber deinstalliert, ist keine Neuinstallation möglich.
- Azure AD Connect kann nicht per Expressinstallation installiert werden, wenn der Benutzer nicht zur Stammdomäne der Gesamtstruktur gehört, oder wenn eine nichtenglische Version von Active Directory verwendet wird.
- Wenn der FQDN des Active Directory-Benutzerkontos nicht aufgelöst werden kann, wird eine irreführende Fehlermeldung "Schreiben des Schemas fehlgeschlagen" angezeigt.
- Wenn das auf dem Active Directory Connector verwendete Konto außerhalb des Assistenten geändert wird, schlägt die Ausführung des Assistenten bei nachfolgenden Versuchen fehl.
- Die Installation von Azure AD Connect auf einem Domänencontroller schlägt manchmal fehl.
- Aktivieren und Deaktivieren des "Stagingmodus" ist nicht möglich, wenn Erweiterungsattribute hinzugefügt wurden.
- Das Rückschreiben von Kennwörtern schlägt in einigen Konfigurationen aufgrund eines falschen Kennworts für den Active Directory Connector fehl.
- DirSync kann nicht aktualisiert werden, wenn DN bei der Attributfilterung verwendet wird.

## 1\.0.8641.0
Veröffentlicht im Juni 2015

**Erste Version von Azure AD Connect.**

Änderung des Namens von Azure AD Sync zu Azure AD Connect.

## 1\.0.494.0501
Veröffentlicht im Mai 2015

**Neue Voraussetzung:**

- Azure AD Sync erfordert jetzt die Installation von .Net Framework-Version 4.5.1.

**Behobene Probleme:**

- Das Rückschreiben von Kennwörtern von Azure AD schlägt mit einem Servicebus-Konnektivitätsfehler fehl.

## 1\.0.491.0413
Veröffentlicht im April 2015

**Behobene Probleme und Verbesserungen:**

- Der Active Directory Connector verarbeitet Löschvorgänge nicht ordnungsgemäß, wenn der Papierkorb aktiviert ist, und mehrere Domänen in der Gesamtstruktur vorhanden sind.
- Die Leistung bei Importvorgängen wurde für den Azure Active Directory Connector verbessert.
- Wenn eine Gruppe die Mitgliedergrenze überschritten hatte (standardmäßig ist der Grenzwert auf 50.000 Objekte festgelegt), wurde die Gruppe in Azure Active Directory gelöscht. Jetzt bleibt die Gruppe erhalten, es wird ein Fehler ausgelöst, und es werden keine neuen Mitgliedschaftsänderungen exportiert.
- Ein neues Objekt kann nicht bereitgestellt werden, wenn eine gestaffelte Löschung mit dem gleichen DN bereits im Connectorbereich vorhanden ist.
- Einige Objekte werden für die Synchronisierung während einer Deltasynchronisierung markiert, obwohl keine Änderung für das Objekt bereitgestellt ist.
- Bei Erzwingen einer Kennwortsynchronisierung wird auch die Liste der bevorzugten Domänencontroller entfernt.
- CSExportAnalyzer hat Probleme mit einigen Objektstatus.

**Neue Features:**

- Mit einer Verknüpfung ist jetzt eine Verbindung zum Objekttyp "ANY" im Metaverse möglich.

## 1\.0.485.0222
Veröffentlicht im Februar 2015

**Verbesserungen:**

- Verbesserte Importleistung.

**Behobene Probleme:**

- Die Kennwortsynchronisierung berücksichtigt das zur Attributfilterung verwendete Attribut cloudFiltered. Gefilterte Objekte fallen nicht mehr in den Bereich der Synchronisierung von Kennwörtern.
- In seltenen Fällen, in denen die Topologie sehr viele Domänencontroller aufweist, funktioniert die Synchronisierung von Kennwörtern nicht.
- "Stopped-server" beim Importieren vom Azure AD-Connector in die Geräteverwaltung wurde in Azure AD/Intune aktiviert.
- Verknüpfen der fremden Sicherheitsprinzipale (FSPs) aus mehreren Domänen in der gleichen Gesamtstruktur verursacht den Fehler mehrdeutiger Verknüpfungen.

## 1\.0.475.1202
Veröffentlicht im Dezember 2014

**Neue Features:**

- Die Synchronisierung von Kennwörtern mit attributbasierter Filterung wird jetzt unterstützt. Weitere Informationen finden Sie unter "Synchronisierung von Kennwörtern mit Filterung".
- Das Attribut msDS-ExternalDirectoryObjectID wird nach AD zurückgeschrieben. Damit wird Unterstützung für Office 365-Anwendungen hinzugefügt, die in einer Hybrid-Exchange-Bereitstellung mithilfe von OAuth2 sowohl auf Online- als auch lokale Postfächer zugreifen.

**Behobene Upgrade-Probleme:**

- Eine neuere Version des Anmelde-Assistenten ist auf dem Server verfügbar.
- Ein benutzerdefinierter Installationspfad wurde verwendet, um Azure AD Sync zu installieren.
- Ein ungültiges benutzerdefiniertes Verknüpfungskriterium blockiert das Upgrade.

**Weitere Problembehebungen:**

- Probleme mit den Vorlagen für Office Pro Plus wurden behoben.
- Installationsprobleme, die durch Benutzernamen verursacht wurden, die mit einem Gedankenstrich beginnen, wurden behoben.
- Das Problem, dass die Einstellung sourceAnchor bei der zweiten Ausführung des Installations-Assistenten verloren geht, wurde behoben.
- Probleme mit der ETW zur Kennwortsynchronisierung wurden behoben.

## 1\.0.470.1023
Veröffentlicht im Oktober 2014

**Neue Features:**

- Synchronisierung von Kennwörtern von mehreren lokalen AD mit Azure AD.
- Lokalisierte Benutzeroberfläche für die Installation für alle Windows Server-Sprachen.

**Upgrade von AADSync 1.0 GA**

Wenn Sie bereits Azure AD Sync installiert haben, müssen Sie einen zusätzlichen Schritt ausführen, falls Sie eine der Out-of-Box-Synchronisierungsregeln geändert haben. Nach dem Upgrade auf die Version 1.0.470.1023 sind die Synchronisierungsregeln, die Sie geändert haben, dupliziert. Führen Sie für jede geänderte Synchronisierungsregel die folgenden Schritte aus:

- Suchen Sie die Synchronisierungsregel, die Sie geändert haben, und notieren Sie sich die Änderungen.
- Löschen Sie die Synchronisierungsregel.
- Suchen Sie die neue, von Azure AD Sync erstellte Synchronisierungsregel, und wenden Sie die Änderungen erneut an.

**Berechtigungen für das AD-Konto**

Dem AD-Konto müssen zusätzliche Berechtigungen gewährt werden, um die Kennworthashes aus AD zu lesen. Die zu gewährenden Berechtigungen heißen "Verzeichnisänderungen replizieren" und "Alle Verzeichnisänderungen replizieren". Beide Berechtigungen sind zum Lesen von Kennworthashes erforderlich.

## 1\.0.419.0911
Veröffentlicht im September 2014

**Erste Version von Azure AD Sync**

## Zusätzliche Ressourcen
[Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)

[Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO9-->